
<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [1. 开源社区介绍](#1-开源社区介绍)
  - [1.1. Linux开源社区](#11-linux开源社区)
  - [1.2. KVM开源社区](#12-kvm开源社区)
  - [1.3. QEMU开源社区](#13-qemu开源社区)
  - [1.4. 其他开源社区](#14-其他开源社区)
- [2. 代码结构简介](#2-代码结构简介)
  - [2.1. KVM代码](#21-kvm代码)
    - [2.1.1. KVM框架的核心代码](#211-kvm框架的核心代码)
    - [2.1.2. 与硬件架构相关的代码](#212-与硬件架构相关的代码)
    - [2.1.3. KVM相关的头文件](#213-kvm相关的头文件)
  - [2.2. QEMU代码](#22-qemu代码)
  - [2.3. KVM单元测试代码](#23-kvm单元测试代码)
    - [2.3.1. 介绍](#231-介绍)
    - [2.3.2. 代码目录](#232-代码目录)
    - [2.3.3. 基本原理](#233-基本原理)
    - [2.3.4. 编译运行](#234-编译运行)
    - [2.3.5. 添加测试](#235-添加测试)
- [3. 向开源社区贡献代码](#3-向开源社区贡献代码)
  - [3.1. 开发者邮件列表](#31-开发者邮件列表)
  - [3.2. 代码风格](#32-代码风格)
    - [3.2.1. KVM内核部分的代码风格](#321-kvm内核部分的代码风格)
    - [3.2.2. QEMU的代码风格](#322-qemu的代码风格)
  - [3.3. 生成patch](#33-生成patch)
    - [3.3.1. 使用diff工具生成patch](#331-使用diff工具生成patch)
    - [3.3.2. 使用Git工具生成patch](#332-使用git工具生成patch)
  - [3.4. 检查patch](#34-检查patch)
  - [3.5. 提交patch](#35-提交patch)
- [4. 提交KVM相关的bug](#4-提交kvm相关的bug)

<!-- /code_chunk_output -->

# 1. 开源社区介绍

“开源即开放源代码（Open Source），保证任何人都可以根据“自由许可证”获得软件的源代码，并且允许任何人对其代码进行修改并重新发布。

在英文中，Free可以有两个意思：一个是免费，一个是自由。当提及“Free Software”（自由软件）时，应该将“Free”理解为“自由的演讲”（Free Speech）中的自由，而不是“免费的啤酒”（Free Beer）中的免费。

一般来说，每个开源软件都有一个对应的开源社区.

## 1.1. Linux开源社区

KVM是Linux内核的一个模块，因此KVM社区也与Linux内核社区非常类似，也可以算作Linux内核社区的一部分。这里介绍的Linux开源社区主要是指Linux内核的社区而不是Linux系统的用户工具和发行版社区。

**Linus**并不会亲自审查每一个新的补丁、每一行新 的代码，而是将**大部分的工作**都交给各个**子系统的维护者**去处理。

各个维护者下面可能还有一些**驱动程序或几个特殊文件的维护者**，Linus自己则主要把握一些大的方向及合并(merge)各个子系统维护者的分支到Linux主干树。

如图所示，**普通的Linux内核开发者**一般都是向驱动程序或子系统的维护者提交代码，提交的代码经过**维护者**和开发社区中的**其他人审核**之后，才能进入**子系统！！！维护者**的**开发代码仓库！！！**，进入每个Linux内核的**合并窗口(merge window**)之后才能进入真正的Linux upstream代码仓库中。

注: upstream也被翻译为“上游”，是指在一些开源软件的开发过程中**最主干**的且**一直向前发展的代码树**。upstream中包含了**最新的代码**，一些新增的功能特性和修复bug的代码一般都先进入upstream中，然后才会到某一个具体的发型版本中。对于Linux内核来说， Linux upstream就是指最新的Linux内核代码树，而一些Linux发行版(如RHEL、Fedora、 Ubuntu等)的内核都是基于upstream中的某个版本再加上一部分patch来制作的。

在Linux内核中有许多的**子系统**(如:内存管理、PCI总线、网络子系统、ext4文件系 统、SCSI子系统、USB子系统、KVM模块等)，也分为更细的**许多驱动**(如:Intel的以 太网卡驱动、USB XHCI驱动、Intel的DRM驱动等)。各个**子系统**和**驱动**分别由相应的开发者来维护，如:PCI子系统的维护者是来自Google的Bjorn Helgaas，Intel以太网卡驱动 (如igb、ixgbe)的维护者是来自Intel的Jeff Kirsher。关于Linux内核中维护者相关的信 息，可以查看内核代码中名为“MAINTAINERS”的文件。

目前，Linux内核版本的发布周期一般是2~3个月，在这段时间中，一般会发布七八个RC[2]版本。

注: RC即**Release Candidate**，与Alpha、Beta等版本类似，RC也是一种**正式产品发布前**的一种**测试版本**。RC是**发布前的候选版本**，比Alpha/Beta更加成熟。一般来说，RC版本是前面已经经过测试并修复了大部分bug的，一般比较稳定、接近正式发布的产品。

![2019-11-27-16-17-43.png](./images/2019-11-27-16-17-43.png)

## 1.2. KVM开源社区

KVM(Kernel Virtual Machine)是Linux内核中原生的虚拟机技术，KVM内核部分的 代码全部都在Linux内核中，是Linux内核的一部分(相当于一个内核子系统)。

KVM项目的官方主页是 https://www.linux-kvm.org 。

除了平时在KVM邮件列表中讨论之外，KVM Forum是KVM开发者、用户等相互交流的一个重要会议。KVM Forum一般每年举行一届，其内容主要涉及KVM的一些最新开发 的功能、未来的发展思路，以及一些管理工具的开发。关于KVM Forum，可以参考其官方网页[3]，该网页提供了各届KVM Forum的议程和演讲文档，供用户下载。

KVM Forum官网: http://www.linux-kvm.org/page/KVM_Forum

## 1.3. QEMU开源社区

QEMU(Quick EMUlator)是一个实现**硬件虚拟化**的开源软件，它通过**动态二进制转换**实现了对中央处理单元(CPU)的模拟，并且提供了**一整套模拟的设备模型**，从而可以使未经修改的各种操作系统得以在QEMU上运行。

QEMU自身就是一个**独立的、完整的虚拟机模拟器**，可以独立**模拟CPU**和其他一些**基本外设**。除此之外，QEMU还可以为KVM、Xen等流行的虚拟化技术提供设备模拟功能。

在用普通QEMU来配合KVM使用时，在QEMU命令行启动客户机时要加上“\-enable\-kvm”参 数来使用KVM加速，而较老的qemu-kvm命令行默认开启了使用KVM加速的选项。

QEMU社区的代码仓库网址是 http://git.qemu.org ，其中qemu.git就是QEMU upstream的主干代码树。可以使用GIT工具，通过两个URL( git://git.qemu.org/qemu.git 和 http://git.qemu.org/git/qemu.git )中的任意一个来下载QEMU的最新源代码。

从QEMU的源代码中的“MAINTAINERS”文件可知：QEMU的维护者是Peter May-dell，QEMU中与KVM相关部分代码的维护者就是KVM的维护者Paolo Bonzini，QEMU中与Xen相关部分代码的维护者是Stefano Stabellini。

## 1.4. 其他开源社区

1)Libvirt，一个著名的虚拟化API项目。

https://libvirt.org

2)OpenStack，一个扩展性良好的开源云操作系统。 

www.openstack.org

3)CloudStack，一个可提供高可用性、高扩展性的开源云计算平台。 

http://cloudstack.apache.org 

4)ZStack，一个简单、强壮、可扩展性强的云计算管理平台。 

http://www.zstack.io

5)Xen，一个功能强大的虚拟化软件。

http://xen.org 

6)Ubuntu，一个免费开源的、外观漂亮的、非常流行的Linux操作系统。 

www.ubuntu.com

7)Fedora，一个免费的、开源的Linux操作系统。

fedoraproject.org 

8)CentOS，一个基于RHEL的开源代码进行重新编译构建的开源Linux操作系统。 

http://www.centos.org 

9)openSUSE，一个开源社区驱动的、开放式开发的Linux操作系统。 

www.opensuse.org

# 2. 代码结构简介

## 2.1. KVM代码

由于**Linux内核**过于庞大，因此**KVM开发者的代码**一般先进入**KVM社区的kvm.git代码仓库**，再由**KVM维护者**定期地将代码提**供给Linux 内核的维护者(即Linus Torvalds**)，并由其添加到**Linux内核代码仓库**中。可以分别查看如下两个网页来了解最新的KVM开发和Linux内核的代码仓库的状态。

```
https://git.kernel.org/cgit/virt/kvm/kvm.git/ 
https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/
```

KVM内核部分的代码主要由3部分组成:KVM框架的核心代码、与硬件架构相关的代码和KVM相关的头文件。在Linux 4.14.12版本中，KVM相关的代码行数大约是17多万行。基于Linux 4.14.12版本的Linux内核为例，对KVM代码结构进行简单的介绍。

### 2.1.1. KVM框架的核心代码

与具体硬件架构无关的代码，位于virt/kvm/目录中，有22 135行代码。

```
# ls virt/kvm/
arm  async_pf.c  async_pf.h  coalesced_mmio.c  coalesced_mmio.h  eventfd.c  irqchip.c  Kconfig  kvm_main.c  vfio.c  vfio.h
```

其中，`kvm_main.c`文件是KVM代码中最核心的文件之一，`kvm_main.c`中的`kvm_init`函数是与**硬件架构无关**的**KVM初始化入口**。

### 2.1.2. 与硬件架构相关的代码

不同的处理器架构有对应的KVM代码，位于arch/$ARCH/kvm/目录中。KVM目前支 持的架构包括:X86、ARM、ARM64、PowerPC、S390、MIPS等。与处理器硬件架构相关的KVM代码目录如下:

```
# ls -d arch/*/kvm
arch/arm64/kvm  arch/arm/kvm  arch/mips/kvm  arch/powerpc/kvm  arch/s390/kvm  arch/x86/kvm
```

在KVM支持的架构中，Intel和AMD的x86架构上的功能是最完善和成熟的。x86架构 相关的代码位于arch/x86/kvm/目录中，有51 911行代码。其代码结构如下:

```
# ls arch/x86/kvm/
cpuid.c  cpuid.h  debugfs.c  emulate.c  hyperv.c  hyperv.h  i8254.c  i8254.h  i8259.c  ioapic.c  ioapic.h  irq.c  irq_comm.c  irq.h  Kconfig  kvm_cache_regs.h  lapic.c  lapic.h  Makefile  mmu  mmu_audit.c  mmu.h  mmutrace.h  mtrr.c  pmu_amd.c  pmu.c  pmu.h  svm.c  trace.h  tss.h  vmx  x86.c  x86.h
```

其中，`vmx.c`和`svm.c`分别是Intel和AMD CPU的架构相关模块`kvm-intel`和`kvm-amd`的主要代码。

以`vmx.c`为例，其中`vmx_init()`函数是`kvm-intel`模块加载时执行的初始化函数，而 `vmx_exit()`函数是`kvm-intel`模块卸载时执行的函数。

### 2.1.3. KVM相关的头文件

在前面提及的代码中，一般都会引用一些相关的头文件，包括与各个处理器架构相关 的头文件(位于`arch/*/include/asm/kvm*`)和其他的头文件。KVM相关的头文件的结构如下:

```
# ls arch/*/include/asm/kvm*
arch/x86/include/asm/kvm_emulate.h
arch/x86/include/asm/kvm_host.h
arch/x86/include/asm/kvm_page_track.h
arch/x86/include/asm/kvm_para.h
arch/x86/include/asm/kvm_vcpu_regs.h
arch/x86/include/asm/kvmclock.h

.......
```

另外, 在Linux内核代码中关于KVM的文档, 主要位于`Documentation/virtual/kvm/*`和`Documentation/*kvm/*.txt`, 如下:

```
# ls Documentation/virt/kvm/*
Documentation/virt/kvm/amd-memory-encryption.rst  Documentation/virt/kvm/halt-polling.txt  Documentation/virt/kvm/locking.txt  Documentation/virt/kvm/nested-vmx.txt        Documentation/virt/kvm/s390-diag.txt
Documentation/virt/kvm/api.txt                    Documentation/virt/kvm/hypercalls.txt    Documentation/virt/kvm/mmu.txt      Documentation/virt/kvm/ppc-pv.txt            Documentation/virt/kvm/timekeeping.txt
Documentation/virt/kvm/cpuid.rst                  Documentation/virt/kvm/index.rst         Documentation/virt/kvm/msr.txt      Documentation/virt/kvm/review-checklist.txt  Documentation/virt/kvm/vcpu-requests.rst

Documentation/virt/kvm/arm:
hyp-abi.txt  psci.txt  pvtime.rst

Documentation/virt/kvm/devices:
arm-vgic-its.txt  arm-vgic.txt  arm-vgic-v3.txt  mpic.txt  README  s390_flic.txt  vcpu.txt  vfio.txt  vm.txt  xics.txt  xive.txt
```

## 2.2. QEMU代码

QEMU 1.3.0版本已将qemu-kvm中的代码全部合并到纯QEMU中了。

QEMU代码仓库如下:

```
http://git.qemu.org/?p=qemu.git;a=summary
```

......

## 2.3. KVM单元测试代码

### 2.3.1. 介绍

KVM相关的代码除了KVM和QEMU自身的代码之外，还包括本节介绍的KVM单元测 试代码和下一节将介绍的KVM Autotest代码。KVM单元测试代码用于测试一些细粒度的 系统底层的特性(如:客户机CPU中的MSR的值)，在一些重要的特性被加入KVM时， KVM维护者也会要求代码作者在KVM单元测试代码中添加对应的测试用例。

https://www.linux-kvm.org/page/KVM-unit-tests

KVM单元测试的代码仓库也存放在Linux内核社区的官方站点，参考如下的网页链接: 

https://git.kernel.org/cgit/virt/kvm/kvm-unit-tests.git

### 2.3.2. 代码目录

KVM单元测试的代码目录结构如下:

```
# ls
api  arm  configure  COPYRIGHT  errata.txt  lib  MAINTAINERS  Makefile  powerpc  README  README.md  run_tests.sh  s390x  scripts  x86
```

其中，

* lib目录下是一些公共的库文件，包括系统参数、打印、系统崩溃的处理，以及lib/x86/下面的x86架构相关的一些基本功能函数的文件(如apic.c、msr.h、io.c等); 

* x86目录下是关于x86架构下的一些具体测试代码(如msr.c、apic.c、vmexit.c等)。

### 2.3.3. 基本原理

KVM单元测试的**基本工作原理**是:

将编译好的**轻量级的测试内核镜像**(*.flat文件) 作为支持**多重启动**的QEMU的**客户机内核镜像！！！** 来启动，测试使用了一个**通过客户机BIOS来调用**的基础结构，该基础结构将主要**初始化客户机系统**(包括**CPU**等)，然后**切换到长模式**(x86\_64 CPU架构的一种运行模式)，并调**用各个具体测试用例的主函数**从而执行测试，在测试完成后QEMU进程自动退出。

### 2.3.4. 编译运行

编译KVM单元测试代码是很容易的，直接运行make命令即可。

```
$ ./configure
$ make
```

在编译完成后，在**x86**目录下会生成很多具体测试用例需要的内核镜像文件(*.flat)。

执行测试时，运行msr这个测试的命令行示例如下:

```
qemu-system-x86_64 -enable-kvm -device pc-testdev -serial stdio -device isa-debug-exit,iobase=0xf4,iosize=0x4 -kernel ./x86/msr.flat -vnc none
```

其中，\-kernel选项后的./**x86/msr.flat**文件即为被测试的**内核镜像**。测试结果会默认打印在当前执行测试的终端上。

KVM单元测试代码中还提供了一些脚本，以便让单元测试的执行更容易，如`x86-run`脚本可以方便地执行一个具体的测试。

```
# ls ./x86/*.flat
./x86/access.flat              ./x86/hyperv_stimer.flat  ./x86/pcid.flat        ./x86/sieve.flat                ./x86/umip.flat
./x86/apic.flat                ./x86/hyperv_synic.flat   ./x86/pku.flat         ./x86/smap.flat                 ./x86/vmexit.flat
./x86/asyncpf.flat             ./x86/idt_test.flat       ./x86/pmu.flat         ./x86/smptest.flat              ./x86/vmware_backdoors.flat
./x86/debug.flat               ./x86/init.flat           ./x86/port80.flat      ./x86/svm.flat                  ./x86/vmx.flat
./x86/emulator.flat            ./x86/intel-iommu.flat    ./x86/rdpru.flat       ./x86/syscall.flat              ./x86/xsave.flat
./x86/eventinj.flat            ./x86/ioapic.flat         ./x86/realmode.flat    ./x86/tsc.flat
./x86/hypercall.flat           ./x86/kvmclock_test.flat  ./x86/rmap_chain.flat  ./x86/tsc_adjust.flat
./x86/hyperv_clock.flat        ./x86/memory.flat         ./x86/s3.flat          ./x86/tscdeadline_latency.flat
./x86/hyperv_connections.flat  ./x86/msr.flat            ./x86/setjmp.flat      ./x86/tsx-ctrl.flat
```

执行msr测试的示例如下:

```
# ./x86-run ./x86/msr.flat
/usr/local/bin/qemu-system-x86_64 -nodefaults -device pc-testdev -device isa-debug-exit,iobase=0xf4,iosize=0x4 -vnc none -serial stdio -device pci-testdev -machine accel=kvm -kernel ./x86/msr.flat # -initrd /tmp/tmp.Pd6jyCi0dG
[2019-11-28 17:10:40.421] /root/rpmbuild/BUILD/qemu-kvm-2.6.0_65/cpus.c:1067: vcpu 0 thread is 369518
[2019-11-28 17:10:40.448] /root/rpmbuild/BUILD/qemu-kvm-2.6.0_65/cpus.c:1355: enter resume_all_vcpus
[2019-11-28 17:10:40.448] vl.c:842: vm_start function cost 56
[2019-11-28 17:10:40.448] vl.c:844: vm_start: cost 56
enabling apic
PASS: IA32_SYSENTER_CS
PASS: MSR_IA32_SYSENTER_ESP
PASS: IA32_SYSENTER_EIP
PASS: MSR_IA32_MISC_ENABLE
PASS: MSR_IA32_CR_PAT
PASS: MSR_FS_BASE
PASS: MSR_GS_BASE
PASS: MSR_KERNEL_GS_BASE
PASS: MSR_EFER
PASS: MSR_LSTAR
PASS: MSR_CSTAR
PASS: MSR_SYSCALL_MASK
SUMMARY: 12 tests
```

`run_tests.sh`脚本可以默认运行`x86/unittests.cfg`文件中配置的所有测试。

```
# ./run_tests.sh
FAIL apic-split (timeout; duration=90s)
PASS ioapic-split (19 tests)
FAIL apic (timeout; duration=30)
FAIL ioapic (26 tests, 1 unexpected failures)
PASS smptest (1 tests)
PASS smptest3 (1 tests)
PASS vmexit_cpuid
PASS vmexit_vmcall
PASS vmexit_mov_from_cr8
PASS vmexit_mov_to_cr8
PASS vmexit_inl_pmtimer
PASS vmexit_ipi
PASS vmexit_ipi_halt
PASS vmexit_ple_round_robin
PASS vmexit_tscdeadline
PASS vmexit_tscdeadline_immed
PASS access
PASS smap (18 tests)
SKIP pku (0 tests)
PASS asyncpf (1 tests)
PASS emulator (125 tests, 2 skipped)
PASS eventinj (13 tests)
PASS hypercall (2 tests)
PASS idt_test (4 tests)
PASS memory (8 tests)
PASS msr (12 tests)
SKIP pmu (/proc/sys/kernel/nmi_watchdog not equal to 0)
FAIL vmware_backdoors (11 tests, 8 unexpected failures)
PASS port80
PASS realmode
PASS s3
PASS sieve
FAIL syscall (2 tests, 1 unexpected failures)
PASS tsc (3 tests)
PASS tsc_adjust (5 tests)
PASS xsave (17 tests)
PASS rmap_chain
SKIP svm (0 tests)
SKIP taskswitch (i386 only)
SKIP taskswitch2 (i386 only)
PASS kvmclock_test
PASS pcid (3 tests)
PASS rdpru (1 tests)
SKIP umip (qemu-system-x86_64: CPU feature umip not found)
SKIP vmx (0 tests)
SKIP ept (0 tests)
SKIP vmx_eoi_bitmap_ioapic_scan (0 tests)
SKIP vmx_hlt_with_rvi_test (0 tests)
SKIP vmx_apicv_test (0 tests)
SKIP vmx_apic_passthrough_thread (0 tests)
SKIP vmx_init_signal_test (0 tests)
SKIP vmx_apic_passthrough_tpr_threshold_test (0 tests)
SKIP vmx_vmcs_shadow_test (0 tests)
FAIL debug
SKIP hyperv_synic (qemu-system-x86_64: can't apply global kvm64-x86_64-cpu.hv-vpindex=on: Property '.hv-vpindex' not found)
SKIP hyperv_connections (qemu-system-x86_64: can't apply global kvm64-x86_64-cpu.hv-vpindex=on: Property '.hv-vpindex' not found)
SKIP hyperv_stimer (qemu-system-x86_64: can't apply global kvm64-x86_64-cpu.hv-vpindex=on: Property '.hv-vpindex' not found)
PASS hyperv_clock
FAIL intel_iommu
PASS tsx-ctrl
```

查看`x86/unittests.cfg`, 下面是其中一节. 这个用例将测试`apic.flat`在`x86_64`架构下30秒以内.

```conf
[apic]
file = apic.flat
smp = 2
extra_params = -cpu qemu64,+x2apic,+tsc-deadline
arch = x86_64
timeout = 30
```

每个用例都是通过`scripts/runtime.sh`打印到屏幕

```
PASS() { echo -ne "\e[32mPASS\e[0m"; }
SKIP() { echo -ne "\e[33mSKIP\e[0m"; }
FAIL() { echo -ne "\e[31mFAIL\e[0m"; }
```

在`logs/`目录下有更多测试结果信息.

### 2.3.5. 添加测试

参照: https://www.linux-kvm.org/page/KVM-unit-tests

# 3. 向开源社区贡献代码

## 3.1. 开发者邮件列表

开源社区的沟通交流方式有很多种，如电子邮件列表、IRC\[5]、wiki、博客、论坛等。一般来说，开发者的交流使用邮件列表比较多，普通用户则使用邮件列表、论坛、 IRC等多种方式。关于KVM和QEMU开发相关的讨论主要都依赖邮件列表。

邮件列表有两种基本形式:公告型(邮件列表)，通常由一个管理者向小组中的所有成员发送信息，如 电子杂志、新闻邮件等;讨论型(讨论组)，所有的成员都可以向组内的其他成员发送信 息，其操作过程简单来说就是发一个邮箱到小组的公共电子邮箱，通过系统处理后，将这 封邮件分发给组内所有成员。KVM和QEMU等开发社区使用的邮件列表是属于讨论型的邮件列表，任何人都可以向该列表中的成员发送电子邮件。

## 3.2. 代码风格

### 3.2.1. KVM内核部分的代码风格

### 3.2.2. QEMU的代码风格

## 3.3. 生成patch

patch是**GNU diff工具**生成的**输出内容**，这种输出格式能够被patch工具正确读取并添加到相应的代码仓库中。

当然，patch文件还可以由一些**源代码版本控制工具**来生成，如：SVN、CSV、Mercurial、Git等。

在Linux内核社区中，所有的开发代码都以patch的形式发送到开发者邮件列表中，然后经过讨论、审核、测试等步骤之后才会被项目（或子项目）维护者加入代码仓库中。QEMU/KVM的功能开发和修复bug的代码也都是以patch的形式发送出去的。

在目前的KVM社区中，生成KVM内核部分的patch要基于kvm.git代码仓库的next或master分支来进行开发，而QEMU部分的patch要基于qemu.git代码仓库的master分支来开发。在动手修改代码做自己的patch之前，需要先将自己的工作目录切换到对应的开发分支，并更新到最新的代码中。示例如下：

```
[root@kvm-host kvm.git]# git checkout next￼
Branch next set up to track remote branch next from origin.￼
Switched to a new branch 'next'￼
[root@kvm-host kvm.git]# git branch￼
  master￼
* next￼
[root@kvm-host kvm.git]# git pull￼
￼
[root@kvm-host qemu.git]# git branch￼
*  master￼
[root@kvm-host qemu.git]# git pull
```

下面以kvm.git代码仓库为例，分别介绍使用diff工具和使用Git工具生成patch的方法。生成QEMU的patch的方法和KVM内核中是完全一样的，只是修改各自代码时注意遵循各自并不完全相同的代码编写风格。

### 3.3.1. 使用diff工具生成patch

最简单的生成patch的方法是准备两个代码仓库，其中一个是未经任何修改的代码仓库，另一个就是经过自己修改后的代码仓库。

假设kvm-my.git是经过修改后的代码仓库，kvm.git是修改前的原生代码仓库，可以使用如下的命令来生成patch：


### 3.3.2. 使用Git工具生成patch

由于kvm.git和qemu.git代码仓库都是使用Git进行源代码版本管理的，所以在KVM的开发中也通常使用Git工具来生成patch。

在Git代码仓库中，用Git工具生成patch只需要简单的两个步骤：

* 第1步是使用`git commit`命令在本地仓库中提交修改内容；(可以用`git commit -s`)

* 第2步是使用`git format-patch`命令生成所需的patch文件。

如下的命令演示了使用Git生成一个patch并继续修改再生成另外一个patch的过程。

```
#假设前面已经对virt/kvm/kvm_main.c进行了修改￼
# 将修改的文件添加到git管理的索引中￼
[root@kvm-host kvm.git]# git add virt/kvm/kvm_main.c￼
# 提交修改到本地仓库￼
[root@kvm-host kvm.git]# git commit -m "just a demo"￼
[next 86abe87] just a demo￼
    1 files changed, 2 insertions(+), 0 deletions(-)￼
# 根据前面的修改，生成对应的patch￼
[root@kvm-host kvm.git]# git format-patch -1￼
0001-just-a-demo.patch￼
￼
# 做了另一些其他的修改之后，提交本次修改内容￼
[root@kvm-host kvm.git]# git commit -m "just another demo"￼
[next 486236a] just another demo￼
    1 files changed, 1 insertions(+), 0 deletions(-)￼
# 分别生成两次提交的patch￼
[root@kvm-host kvm.git]# git format-patch -2￼
0001-just-a-demo.patch￼
0002-just-another-demo.patch
```

其中，

`git commit`命令中的-m参数表示添加对本次提交的描述信息；

`git format-patch -N`命令表示从本地最新的提交开始往前根据最新的N次提交信息生成对应的patch；

而`git format-patch origin`命令则可以生成**所有的本地提交**对应的patch文件（在原来的代码仓库中已存在的信息则不会生成patch）。

生成的两个patch中的第1个为`0001-just-a-demo.patch`，内容如下：

```patch
From 86abe871f15004faa9a950f445dd710ec70f97bc Mon Sep 17 00:00:00 2001￼
From: Jay <smile665@gmail.com>￼
Date: Sun, 26 May 2013 17:56:49 +0800￼
Subject: [PATCH 1/2] just a demo￼
￼
---￼
    virt/kvm/kvm_main.c |    2 ++￼
    1 files changed, 2 insertions(+), 0 deletions(-)￼
￼
diff --git a/virt/kvm/kvm_main.c b/virt/kvm/kvm_main.c￼
index 302681c..f944735 100644￼
--- a/virt/kvm/kvm_main.c￼
+++ b/virt/kvm/kvm_main.c￼
@@ -3105,6 +3105,8 @@ int kvm_init(void *opaque, unsigned vcpu_size, unsigned vcpu_align,￼
        int r;￼
        int cpu;￼
￼
+       printk(KERN_INFO "Hey, KVM is initializing.\n");￼
+￼
        r = kvm_arch_init(opaque);￼
        if (r)￼
                goto out_fail;￼
--￼
1.7.1
```

## 3.4. 检查patch

在前面代码风格中介绍了KVM内核和QEMU的代码规范，而且开源社区对代码规范的执行也比较严格，如果你发送的patch不符合代码风格，维护者是不会接受的，他们会觉得你太不专业从而鄙视你。

所以，在将patch正式发送出去之前，非常有必要进行检查，至少用它们项目源代码仓库提供的自动检查脚本进行patch检查。

使用脚本自动检查patch可以发现大多数的代码风格的问题，对于脚本检查发现的问题（包括错误和警告），原则上都应该全部解决（尽管偶尔也有可能遇到实际并不需要改正的警告信息）。

Linux内核与QEMU分别提供了检查patch的脚本，它们的位置分别如下：

```
[root@kvm-host kvm.git]# ls scripts/checkpatch.pl￼
scripts/checkpatch.pl￼
￼
[root@kvm-host qemu.git]# ls scripts/checkpatch.pl￼
scripts/checkpatch.pl
```

检查前面3.3节生成的patch，示例如下：

```
[root@kvm-host kvm.git]# scripts/checkpatch.pl 0001-just-a-demo.patch￼
WARNING: Prefer netdev_info(netdev, ... then dev_info(dev, ... then pr_info(...  to printk(KERN_INFO ...￼
#18: FILE: virt/kvm/kvm_main.c:3108:￼
+       printk(KERN_INFO "Hey, KVM is initializing.\n");￼
￼
ERROR: Missing Signed-off-by: line(s)￼
￼
total: 1 errors, 1 warnings, 8 lines checked￼
￼
0001-just-a-demo.patch has style problems, please review.￼
￼
If any of these errors are false positives, please report￼
them to the maintainer, see CHECKPATCH in MAINTAINERS.
```

发现了一个错误和一个警告，错误是缺少“Signed-off-by：”这样的行，警告是printk(KERN_INFO...)这样的写法是不推荐的（最好用pr_info()函数来代替）。

所以需要再编辑patch，在其中添加`Signed-off-by：Jay<smile665@gmail.com>`这样的作者信息行，当然有**多个作者**可以用多行“Signed-off-by：”。

另外，有时还可以**根据需要**添加其他的**多行信息**，如下：

```
“Reviewed-by:” 当有人做过审查代码时（一般是社区中资深人士）￼
“Acked-by:” 当有人表示响应和同意时（一般是社区中资深人士）￼
“Reported-by:” 当某人报告了一个问题，本patch就修复那个问题时￼
“Tested-by:” 当某人测试了本patch时
```

另外，可以在**检查脚本**中加`--no-signoff`参数来忽略对“Signed-off-by：”的检查，示例命令为：`scripts/checkpatch.pl --no-signoff my.patch`。

当然，如果是用`git format-patch`命令来生成patch的，则可以在**生成patch时**就添加`-s`或`--signoff`参数，以便在**生成patch文件**时就添加上“Signed-off-by：”的信息行。

对于那个警告，使用pr_info()来替换printk(KERN_INFO...)函数即可。最后，用“git format-patch-s origin”命令生成0001-just-a-demo.patch。示例如下：

```
From 2c2118137eaa86bdce3c85016819dff336ca61f7 Mon Sep 17 00:00:00 2001￼
From: Jay <smile665@gmail.com>￼
Date: Sun, 26 May 2013 22:19:19 +0800￼
Subject: [PATCH] just a demo￼
￼
￼
Signed-off-by: Jay <smile665@gmail.com>￼
---￼
    virt/kvm/kvm_main.c |    2 ++￼
    1 files changed, 2 insertions(+), 0 deletions(-)￼
￼
diff --git a/virt/kvm/kvm_main.c b/virt/kvm/kvm_main.c￼
index 302681c..e0bbfe6 100644￼
--- a/virt/kvm/kvm_main.c￼
+++ b/virt/kvm/kvm_main.c￼
@@ -3105,6 +3105,8 @@ int kvm_init(void *opaque, unsigned vcpu_size, unsigned vcpu_align,￼
        int r;￼
        int cpu;￼
￼
+       pr_info("Hey, KVM is initializing.\n");￼
+￼
        r = kvm_arch_init(opaque);￼
        if (r)￼
                goto out_fail;￼
--￼
1.7.1
```

在修正错误和警告后，用checkpatch.pl脚本重新检查生成的patch，命令如下：

```
[root@kvm-host kvm.git]# scripts/checkpatch.pl 0001-just-a-demo.patch￼
total: 0 errors, 0 warnings, 8 lines checked￼
￼
0001-just-a-demo.patch has no obvious style problems and is ready for submission.
```

可见，本次检查没有发现任何错误和警告，即没有明显的代码风格问题，可以向开源社区提交这个patch了。

## 3.5. 提交patch

准备好patch了后，最后要做的事情当然是提交patch了。KVM和QEMU的patch提交都是通过发送到邮件列表来实现的，KVM开发者邮件列表是kvm@vger.kernel.org，QEMU开发者邮件列表是qemu-devel@nongnu.org。QEMU代码中针对KVM相关修改的patch，需要发送到KVM邮件列表，并且抄送QEMU邮件列表。
除了邮件列表之外，一般收件人或抄送人中还包含该项目或子模块的维护者，以便让维护者将patch添加到upstream中。那么如何才能找到patch相关的维护者呢？首先，可以根据Linux内核和QEMU项目源代码中的“MAINTAINERS”文件查看维护者的信息以及他们负责的模块，然后根据patch中的改动及其影响找到影响的维护者。其次，Linux内核和QEMU代码仓库中都提供了一个根据patch查找到维护者的脚本，其位置都在源代码的“scripts/get_maintainer.pl”中。根据patch获得维护者信息的脚本的示例如下：

[root@kvm-host kvm.git]# scripts/get_maintainer.pl 0001-just-a-demo.patch￼ Paolo Bonzini <pbonzini@redhat.com> (supporter:KERNEL VIRTUAL MACHINE (KVM))￼ "Radim Kr?má?" <rkrcmar@redhat.com> (supporter:KERNEL VIRTUAL MACHINE (KVM))￼ kvm@vger.kernel.org (open list:KERNEL VIRTUAL MACHINE (KVM))￼ linux-kernel@vger.kernel.org (open list)￼ ￼ [root@kvm-host qemu.git]# scripts/get_maintainer.pl 0001-hello.patch￼ Paolo Bonzini <pbonzini@redhat.com> (maintainer:X86)

一般来说，发送patch邮件时需要将维护者放在“抄送人”这一栏。不过目前没有非常严格地要求将维护者放在抄送人一栏：有的人发patch时，将邮件列表作为收件人，将维护者作为抄送人；有的人将维护者作为收件人，将KVM或QEMU邮件列表作为抄送人；也有的人将维护者和邮件列表都作为收件人。另外，也可以将任何与这个patch相关的人员（如部门同事、经理、测试人员）加到你发送patch的收件人或抄送人列表中。
在向QEMU/KVM的邮件组发送patch时，有不少朋友在初次使用Linux开发相关邮件列表时都可能会犯一些与邮件格式相关的小错误。笔者根据经验总结了如下几个注意事项：
1）邮件内容使用纯文本格式，而不要使用HTML格式修饰得很复杂和漂亮（因为一些Linux开发者使用纯文本模式的邮件收发工具来处理邮件）。发往KVM相关邮件列表的邮件都应该使用英文来作为相互交流的语言，因为开发者来自世界各地（不要使用中文等非英语语言，否则会因不专业而受到批评）。
2）将patch的内容直接粘贴在邮件正文中（尽量不要使用附件，除非你的邮件客户端不方便编辑纯文本的邮件正文），因为一些维护者是直接使用邮件来生成patch的（这样就不需要复制和粘贴patch内容的过程）。
3）使用“[PATCH]”字符作为主题的开头来表明邮件是patch，以引起维护者的关注。一般来说，还要在主题的开始部分表明该patch属于QEMU/KVM中的哪个模块。
4）如果实现某一个功能的patch的代码量比较大，则尽量将一个大patch拆成多个相对独立的小patch。一个大的patch信息量较大，如果逻辑不是很清晰，则很容易引入bug，别人也不是很容易看懂，维护者当然也不会很快同意将此patch加入upstream中。另外将大patch拆分的过程也可以让作者自己重新理一下思路，从而减少一些错误。在将一个相关功能拆分成m个patch后，在发送其中第n个patch时，应将邮件主题标注为“[PATCH n/m]”，这样可以让看的人明白当前patch在这一系列patch中的位置。而且一般还会首先发一个[PATCH 0/m]作为第1个patch，这个数字编号0的patch通常用于书写本系列patch的概况信息。
5）根据社区的意见修改patch后，发送后续版本时，需要加上当前patch的版本号。比如，通过主题中类似“[PATCH v6 05/12]”这样关键字（可以用git format-patch命令的-v、-n参数直接生成）。特别是当patch修改的是比较核心的功能或添加一个较为重要的特性时，社区中可能有不少人会与你讨论，并给出对patch的意见，这时就需要根据一些意见来修改自己的patch，然后发出更新后的版本。笔者就在KVM邮件列表中见到过patch发送了超过10个版本才最终被维护者接受的案例，所以开发者不能一下就做出完美的patch，需要耐心地参加讨论，并持续更新patch的版本，直到被社区中的维护者和其他大牛们都接受为止。
关于发送patch时的其他问题，可咨询有经验人士或者仔细阅读Linux内核代码仓库中的文档Documentation/process/submitting-patches.rst。
一般来说，可以使用Outlook、Foxmail、Thunderbird等客户端，或使用在线Gmail等邮箱来发送patch和接收邮件列表的邮件。另外，如果你对Git工具比较熟悉，还可以使用git-email安装包中的“git send-email”命令行工具来发送patch。
最后，在发送了patch之后，就耐心地等待社区中开发者的回复吧。如果收到一些批评的意见，不要感到被打击了，一方面要检查自己是否的确可以将patch做得更完美，另一方面，如果你不同意别人给的批评意见，你也可以直接与之进行技术讨论，必要时甚至可以请社区中一些“德高望重”的大牛们来评判。收到别人的回复总比没人理你要好，因为一般来说，一个patch不会在没有任何人讨论或回复的情况下就被加入upstream中。偶尔也会在发送patch后几天都没有任何回复，这时你可以检查一下是否邮件格式、patch内容、收件人等方面有问题，确认这些都没问题后可以发邮件提醒维护者或其他相关人员对你的patch给出评价。一般来说，当你收到维护者发给你的带有“applied”字样的回复时，恭喜你，你的patch就可以顺利进入upstream了。你的patch可能进入QEMU中，也可能进入KVM内核中，这样在下一个Linux内核发布版本中就很可能有你贡献的代码了。

# 4. 提交KVM相关的bug

有句话是这样说的，没有任何软件没有bug。当然，QEMU/KVM也不例外，在使用它们的过程中也可能遇到一些bug，有一些严重的bug可能会导致客户机甚至是宿主机系统崩溃，有一些比较轻微的bug可能会导致在特殊的（通常是老旧的）硬件平台上某个版本客户机的某个小功能不可用。KVM和QEMU作为开源软件，有着强大的开源社区的支持，在遇到bug时，就会提交出去让大家一起讨论。对于不会修复bug的新手，很可能遇到热心的开发者帮着一起解决bug。本节将介绍如何在开源社区中提交QEMU/KVM相关的bug和用git bisect命令来定位bug。
B.4.1　通过邮件列表提交bug
在目前的QEMU/KVM开源社区中，比较简单、直接并可以很快得到反馈的提交bug的方式是使用开发者邮件列表来反映问题。邮件列表的详情可参考B.3.1节中的介绍。
对于KVM内核部分的Bug，要发送邮件到kvm@vger.kernel.org邮件列表；对于QEMU部分的bug，要发送邮件到qemu-devel@nongnu.org（与KVM相关的，也同时发送到KVM的邮件列表）。如果认为可能是某个人引入了该bug，或者知道某个人是这方面的专家，也可以在发往邮件列表的bug邮件中抄送相关的人员，请求他们协助解决。
在向邮件列表提交bug时，要注意将问题尽可能地描述清楚。这样做至少有两个原因：一是清晰的描述可以让其他开发者明白所遇到的问题，从而快速地帮你解决问题；二是详细的描述是请教的真诚和技术的专业性的表现。如果只有“can抰boot a KVM guest（不能启动一个KVM客户机）”这样的内容而没有任何其他详细描述的内容，会被认为很不专业也没有诚意，也许很多人就不理会这个邮件。
下面是笔者根据经验总结的清晰地描述一个KVM相关bug的几个方面。
1.测试环境
通常包括遇到bug时的硬件环境和软件环境。硬件环境主要包括：CPU架构（Intel的x86/x86-64还是ARM等，有时还需要提供具体的型号）、磁盘类型（如IDE、SATA、SAS、SSD等）、网卡型号（如Intel的E1000E类型的网卡或82599等10G网卡）、显卡类型（Intel的核心集成显卡还是Nvidia的独立显卡）等。当然，根据实际遇到的问题只需要选择性地提供其中的一部分硬件信息，不过一般最好把CPU架构介绍一下（如果别人不能从上文中看出来的话）。软件环境首先包括KVM内核的体系架构（x86、ARM、PowerPC等，当然和硬件CPU架构有关联）；然后是KVM内核的版本、QEMU的版本、客户机操作系统的版本（如RHEL7.4、Ubuntu16.04、Windows 10等，它们是32位系统还是64位系统）；最后是其他可能相关的一些特定软件（如客户机中因运行某个软件而发现了虚拟机的bug）。如果是在使用kvm.git和qemu.git代码仓库编译的KVM和QEMU时遇到的bug，需要提供准确的在Git工具的“git log”命令显示的commit ID。
2.现象描述
当然，反映一个问题，需要将问题描述清楚。一般需要说明做了什么操作，得到什么结果，有时也可以加上所期望的结果是什么样的。另外，尽可能多做一点对比的实验，以便其他开发者可以方便快速定位问题。比如：使用某个i版本QEMU遇到了问题而j版本的QEMU是正常的；使用某个网卡遇到了问题但用另一个网卡却正常工作；Windows客户机不能启动但Linux客户机能启动，等等。最后，如果在使用kvm.git和qemu.git时遇到问题，而且刚好有一个可以正常工作和一个不正常工作的版本，可以考虑多做一些实验，通过二分法找到引入bug的点（详见B.4.3节中的介绍）。
3.详细日志
对于很多bug仅仅通过前面的现象描述还是不能将问题讲清楚，此时一般需要提供尽可能详细的日志信息来辅助描述bug。日志信息主要包括：宿主机KVM内核的信息、QEMU命令行的错误、libvirt日志（如果使用的是libvirt）、客户机内核的信息、客户机中某个引发bug的应用软件的日志等。根据实际bug的情况不同，需要提供的日志信息差别也很大。如果bug导致宿主机或客户机内核都在打印一些错误了，那么它们的内核的日志是非常重要的，在Linux上可以通过dmesg命令来获取。如果bug导致宿主机或客户机系统直接崩溃，那么可以将它们的串口重定向到另外的地方，以便获取其崩溃之时打印的函数调用、堆栈、寄存器的信息。如果某个PCI设备不可用，那么可能需要内核信息中关于PCI设备的部分，也需要这个PCI设备的一些详情，比如可以通过“lspci-vvv$BDF”命令来获取PCI设备的一些信息。总之，对于bug分析，可能有用的日志信息都应该尽可能地提交到bug中，即使有一些信息是没用到的，社区开发者忽略便是。
4.重现步骤
俗话说，如果能稳定重现一个bug，那么这个bug就算修复了一半。KVM虚拟化中的一些系统性的bug更是这样，一旦某个大牛能够稳定重现你的bug，那么bug被修复也就指日可待了。所以，重现bug的操作步骤也是提交的bug中至关重要的信息。重现步骤要写得条理清晰且信息丰富，一般可分为1、2、3这样的步骤来书写，而且其中每个操作步骤涉及的命令也要完整地写出来（特别是QEMU命令行中启动客户机的命令是至关重要的）。可能有一些开发者会回复你说“不能重现你报的bug”，这时你需要耐心地将你的测试环境和重现步骤更详细地告诉他们，让别人也能重现bug，从而使问题得到快速解决。
提交bug的内容看起来比较复杂，注意事项也比较多，不过也不要被复杂吓退了，一个简单的方法就是：订阅邮件列表，然后关注里面关于提交bug相关的邮件，然后作为自己提交bug时的参考。
B.4.2　使用bug管理系统提交bug
有时，在邮件列表中提交的一个bug并不能在几天时间内一下被解决，过了几个月基本上就没人会记得你报过的bug，更别提修复bug了。所以除了通过邮件提交bug之外，还可以通过KVM和QEMU各自的bug跟踪系统来提交bug。在bug跟踪系统中提交的bug更加便于长期的管理和今后的bug数据分析。KVM作为Linux内核的一部分，使用Linux内核社区的Bugzilla系统来跟踪bug。Bugzilla是一个非常流行的、开源的、基于Web的bug管理系统，包括Linux内核、Xen、GNOME、Mozilla、Apache、Redhat、Novell等很多著名的项目或组织都在使用它。QEMU使用的bug跟踪系统是在Launchpad网站上的一个bug管理系统。下面分别介绍KVM和QEMU的bug管理系统及其使用的注意事项。
KVM的bug管理系统网址是https://bugzilla.kernel.org，在提交bug时需要选择“Vir-tualization”（虚拟化），然后在bug的具体描述中选择“KVM”这个组件为对象来提交bug。在搜索KVM内核相关的bug时，也需要在搜索条件中选择“Virtualization”作为产品，选择“KVM”作为组件。
QEMU的bug管理系统网址是https://bugs.launchpad.net/qemu，在这里可以查看或搜索到目前哪些bug正在修复或讨论中。在网页的右边有“Report a bug”（报告一个bug）链接供提交bug之用。尽管QEMU不是与KVM一样使用Bugzilla系统，不过Launchpad上的QEMU bug系统也是非常易于使用的。
在bug跟踪系统中提交的bug的基本要素与前一节中提到的清晰描述一个bug的注意事项是完全一样的，所以就不重复介绍了。笔者对KVM和QEMU两个项目都提交过不少的bug，下面分别提供两个bug的网络地址，供大家提交bug时参考。
KVM bug例子：https://bugzilla.kernel.org/show_bug.cgi?id=43328和https://bugzilla.kernel.org/show_bug.cgi?id=45931。
QEMU bug例子：https://bugs.launchpad.net/qemu/+bug/1013467和https://bugs.launchpad.net/qemu/+bug/1096814。
B.4.3　使用二分法定位bug
通常，一旦能够定位到bug是在某个准确的点引入的，那么修复这个bug一般都会比较容易了。如果定位到在Linux内核3.10.2版本中存在某个bug，而在3.10.1版本中不存在该bug，那么要修复这个bug就不难了：要么revert这两个版本之间的某些patch，要么精确地找到哪里的代码错误并进行修正。
当然，并不是总能够很容易地找到一个可以正常工作和一个存在bug的代码版本。一般来说，在遇到bug时，应当清楚当前这个版本是有bug的版本。那么如何找到之前可以正常工作的版本呢？也没有特别好的方法，可以考虑找到几个月前的版本来试试，如果它依然是有这个bug的，那么只能再往前找（比如找一两年前的版本）。一般情况下，除非遇到的bug确实很偏门或者有非常新的特性（从来就没正常工作过），否则总可以找到一个版本会出现某个bug，也能找到老的可以没有某bug的版本。
在找到的可以工作和有bug的版本之间发布的时间相隔较长、差异较大时，可以使用二分法来进一步定位引入bug的具体版本。假设已知3.10.8版本是有某个bug的版本，而3.10.0版本是可以正常工作的，使用二分法来查找的示例方法为：先测试它们的中间版本3.10.4版本，如果可以正常工作的，则在3.10.4和3.10.8之间再次做二分法，应该继续测试3.10.6版本；如果3.10.6版本是有bug的，则查找3.10.4和3.10.6之间的版本（自然就是3.10.5版本了）；如果3.10.5是可以正常工作，那么就可以确认bug是由3.10.5和3.10.6版本之间的patch引入的。
对KVM和QEMU开源社区来说，它们的源代码仓库都是使用Git工具来做代码控制管理的，而Git工具提供了便捷的命令工具“git bisect”来支持通过二分法查找引入bug的代码修改。当使用kvm.git和qemu.git这样的Git管理的代码仓库编译遇到问题时，使用“git bisect”工具定位bug是非常方便的。下面对其进行简单的介绍。
“git bisect”的基本原理是：开始执行二分法查找后，需要先告诉Git一个有bug的版本（标记为bad）和一个正常工作的版本（标记为good），然后Git会自动切换当前代码库到二者中间的版本；接着经过测试后告诉Git当前版本是正常的还是有bug的；Git根据它得到的信息再次切换到相应的一个中间版本，如此循环，直到Git发现一个版本是有bug的而它的前一个版本是没有bug的，这时Git就会报告找到了第1个引入bug的版本。
下面的命令行示例是使用“git bisect”工具来进行二分法查找引入某个bug的第1个版本的过程。

[4] 在Xen中使用QEMU upstream的信息，请参考xen.org的官方wiki上的文章: http://wiki.xen.org/wiki/QEMU_Upstream。
[5] IRC(Internet Relay Chat的缩写，意思是因特网中继聊天)是一种通过网络进行即时聊 天的方式。其主要用于群体聊天，但同样也可以用于个人对个人的聊天。IRC是一个分布 式的客户端/服务器结构，通过连接到一个IRC服务器，我们可以访问这个服务器及它所连 接的其他服务器上的频道。要使用IRC，必须先登录一个IRC服务器，如:一个常见的服 务器为irc.freenode.net。
[6] Google对开源项目代码风格的指导文档见https://github.com/google/styleguide。
[7] Linux内核代码风格的中英文版本分别为 https://www.kernel.org/doc/html/latest/translations/zh_CN/codinstyle.html， https://www.kernel.org/doc/html/latest/process/coding-style.html。
[8] Brian Kernighan和Dennis Ritchie共同编写C语言中最经典的书籍《C programming language》(也称为K&R C)，Linux内核中关于大括号的使用风格就是来自于K&R C中 的规范。他们是UNIX操作系统的最主要的开发者中的两位，Brian Kernighan是AWK编程 语言的联合作者，Dennis Ritchie发明了C语言(是“C语言之父”)。
[9] 匈牙利命名法是Microsoft公司推荐的命名方法，可以参考如下网页: http://en.wikipedia.org/wiki/Hungarian_notation。