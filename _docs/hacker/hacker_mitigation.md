# **科普 | 你必须了解的漏洞利用缓解及对抗技术**

随着软件系统越来越复杂，软件漏洞变得无法避免。业界逐渐推出了让漏洞无法利用或利用难度提高的方法，简称漏洞缓解技术。我们简单介绍下Android和iOS中广泛使用的一些漏洞缓解及可能的绕过技术。当然这里也包含一些相关联的安全限制，而非真正意义的缓解技术。

## **缓解及绕过技术点**

**User Permission**

每个app有自己uid，selinux_context，只有申请并且用户允许才有权限做它想做的事。要突破这些限制，可以考虑通过每个app合理的权限相互结合后产生的不合理性来入手。或者App之间交互的漏洞，如Android的FileProvider，先拿下此app等。

**SELinux、MAC、Sandbox**

SELinux是Security Enhanced Linux缩写，可解释为“安全加固型Linux内核”，MAC是Mandatory Access Control的缩写，意为强制访问控制。Sandbox即沙盒。它们含了一套很复杂的权限管理策略。基本采用白名单模式，默认禁止任意进程的绝大部分的行为，限制文件访问，限制系统调用及调用参数。即限制了每个app的行为，也减少了被攻击面。要突破这些限制，比较可行的方法是攻击如内核关闭等。

**PIE、ASLR、KALSR**

PIE是Position Independent Executable的缩写，与PIC，Position Independent Code一样。ALSR是Address Layout Space Randomization增强内存地址空间分配的随机度。可执行文件和动态加载的库之间的间隔，前后顺序均不同，更没有规律。准确地址未知的情况下攻击者几乎不可能完成攻击，相当于战场上目标都没有找到。KALSR中的K是kernel的缩写，保证每次设备启动内核的虚拟地址是不同的。要突破这些限制，可采用堆喷射等喷射加各种类型的滑板，提高利用成功几率。信息泄漏漏洞，如泄漏内存，泄漏文件，获取内存地址。

**DEP、PXN**

DEP是Data Execution Protection的缩写数据不可执行，意味着攻击者不能直接执行自己的代码，使攻击难度变高。PXN是Privileged Execute Never的缩写，内核态无法运行用户态可执行数据。要突破这些限制，可利用ROP（Return Orient Program），JOP（Jump Orient Program），stack pivot技术，用程序自己的代码做攻击者想做的事。原理是利用现在ABI（Application Binary Interface）的特点，改写程序的栈，控制多个函数返回地址从而形成链，将原有程序自己的代码片段连起来做攻击者想做的事。类似生物上的病毒。浏览器的话可以改写JIT（Just In Time）编译器所用的内存。Android用户态mprotect，mmap系统调用，把内存改成可执行。

**Trust Zone**

可信空间，完成指纹、支付、解锁、DRM和其他认证最保险的一步。即使是操作系统内核也无法访问其内存。它完成签名，加密等工作。要突破这些限制，先考虑拿下有权限访问的service进程drmservie，gatekeeper。通过Fuzz接口找漏洞。如果拿到代码执行的权限的话，就可以跳过指纹验证悄悄的扣款了。

**平滑升级**

App自动更新，系统自动下载并提醒开关机时候升级，保证及时修复bug，我们也将此列入到漏洞缓解中。要突破这些限制，可以考虑使用Google的app之间彼此信任，先拿下Google的一个app。让这个app向Google Play发送安装请求。

**Code Sign**

对代码进行签名，保证代码从商店到用户手机上不会有变化，防止被恶意植入代码，也防止执行未被苹果公司审核的代码。绕过方法这里就不阐述了。

**Secure Boot、Verifying Boot**

[Secure Boot](https://www.theiphonewiki.com/wiki/Bootchain)是iOS中的、[Verifying Boot](https://source.android.com/security/verifiedboot/verified-boot.html)是Android中的，它们保证系统代码不被修改，保证完美越狱、完美root无法实现。还有一些手机厂商自定义的缓释措施，如Android的system分区强制只能只读挂载，需要修改部分驱动中的数据才能实现对system分区的修改

## **示例初探**

在之前发布的[《PXN防护技术的研究与绕过》](http://www.10tiao.com/html/176/201508/209974457/1.html?spm=a313e.7916648.0.0.AnvAlt)这篇文章，里面详细解释了如何绕过DEP、PXN这两种缓释措施，用的基本方法就是JOP和ROP技术。我们将在此基础上解释root工具中绕过其他几种防护的方法。

**文章提到将thread_info中的addr_limit改为0xffffffffffffffff：**

![img](assets/hacker_mitigation/1460000007877776.png)

addr_limit是用于限制一个线程虚拟地址访问范围，为0至addr_limit，改成0xffffffffffffffff表示全部64位地址全部可访问。Google至今没有在Android上启用KALSR，arm64内核一般都固定在0xffffffc000000000开始的虚拟地址上，并没有随机化也就是说可以随意读写内核了。虽然Android没有KALSR但内核堆的地址依旧是不可预知的相当于一种随机化，想要利用CVE-2015-3636（pingpong）这类UAF类型漏洞必须要用到喷射或者ret2dir这类内存掩盖技术。

![img](assets/hacker_mitigation/1460000007877777.png)

一般是将uid、gid修改为0，0是root的uid，即此线程拥有了root权限，capabilities修改为0xffffffffffffffff掩盖所有比特位，表示拥有所有capability。至此user permissions缓释就被绕过了，也可以把自己伪造成其他任意用户。

将“selinux_enforcing”这个内核中的全局变量设为0后，selinux相当于被关闭了。此举可以绕过SElinux的缓释。

MAC、Sandbox是iOS中的缓释措施，作用相当于Android中的SELinux。苹果公司的Code Sign要求除了开发所用程序，所有可执行代码必须要苹果公司签名才能在iPhone运行。Android虽然要对APK签名但APK依旧可以任意加载可执行程序。

在此次阿里聚安全攻防挑战赛中便可以体验一把**如何突破ASLR、DEP等漏洞缓解技术。该题是由蚂蚁金服巴斯光年实验室(AFLSLab) 曲和、超六、此彼三位同学完成设计**，将模拟应用场景准备一些包含bug的程序，并侧重于PWN形式，服务端PWN需要选手具备二进制程序漏洞挖掘和利用能力，通过逆向服务端程序，找出服务端程序的各类问题，然后编写利用代码，在同服务端程序的交互中取得服务端程序的shell权限，进而拿到服务器上的敏感信息。另外在Android应用PWN能力方面，需要选手具有远程获得任意代码执行和arm64平台反汇编理解逻辑的能力，寻找能够突破DEP、ASLR等防护，进而控制目标APP执行自己的代码。

这次参与出题的蚂蚁金服巴斯光年实验室(AFLSLab)是蚂蚁金服安全中心（俗称蚂蚁神盾局）旗下近期刚成立不久的移动安全实验室，除护航支付宝钱包及蚂蚁金服相关产品的安全外，也同时为守护外部厂商、商户、生态伙伴终端基础安全。虽然成立时间很短，但已经为google、三星、华为等公司上报多个安全漏洞。实验室技术负责人曲和表示，期望通过此次比赛吸引更多应用及系统漏洞挖掘和漏洞利用的选手进行交流学习，共同为互联网安全新生态而努力。

[原文](https://segmentfault.com/a/1190000007877773)