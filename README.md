# uMIPS_fork
[WIP] My uMIPS fork

## Original from https://dmitry.gr/?r=05.Projects&proj=33.%20LinuxCard  
* LinuxCard2_test_xubuntu202464_failed.tar.gz  
* LinuxCard2.7z  
* disk_can_boot_no_ext4_LinuxCard2.tar.gz  
* (TODO) disk_can_boot_no_ext4_mkdisk.sh.txt  

## toolchain  
* (?) Codescape.GNU.Tools.Package.2020.06-01.for.MIPS.MTI.Linux.CentOS-6.x86_64.tar.gz  

## Ref  
* https://dmitry.gr/?r=05.Projects&proj=33.%20LinuxCard  
* make CPU=pc  
* ./uMIPS ../romboot/loader.bin ../disk.wheezy  
* https://dmitry.gr/images/LinuxCard2.7z?v=220  
* https://mega.nz/file/p9ZWzLrK#saRKVlgBthOFE4Cp-6sb2fMTM7JXtuXMlsYQaaWrEAI  
* https://drive.google.com/file/d/14fhdW4Vdz4-ZKucB-iIP4MLTk8OLB7dI/view?usp=sharing  

## Removed files  
* kernel/vmlinux  
* source/disk  
* source/vmlinux    
* emu/.o  
* emu/uMIPS  
* emu/uMIPS.bin  

## Study in my weibo  
```
其实uMIPS的作者除了给了磁盘镜像和一篇介绍
（dmitry.gr，LinuxCard，
My business card runs Linux），就好像没多少参考的资料了，
我试过跑这些磁盘是不行的，但可以根据MBR分区表的内容
（他提供的bootloader需要某种条件的MBR和磁盘数据才能跑起来）
还原出来，不知道是不是故意造成这样
（也没人提，估计很少人真的去尝试运行这个MIPS模拟器）
```
```
uMIPS研究，有点头绪了，暂时可以把vmlinux跑起来，

但ext4文件系统缺少所以没办法busybox，后续想办法。
这东西的思路是这样，三个分区，第二分区fat16用于加载vmlinux文件，
第三分区则是ext4。如果要跑通比较麻烦，因为无法适应fdisk生成的分区表信息，
必须手工写入mbr分区内容（这和boot loader代码逻辑有关，除非改代码，
我这里是照搬linux.busybox，作者提供的镜像），而且逻辑需要一个id=0xbb
（分区表的type=0xbb）的第一分区（实际不起作用，active=0）。
mbr的分区表在0x1FE地址到内容为0x55AA的字节处之间，最大可以设置4个分区，
可以通过sudo fdisk写入，通过fdisk或file命令读出来，
不过这里只能用编辑器手工写入
```
```
uMIPS启动失败似乎是因为446和512地址之间需要写入一些数据才行
（难道和mbr有关？），
我用分区工具diskgenius和gparted搞了半天都不行，
可能需要找时间修改bootloader源代码去调试。
有种说法是：MBR总共分为三段：引导代码（446字节）、硬盘分区表（64字节）、
MBR标志（2字节，固定是0x55AA）
```
```
我拿到uMIPS的磁盘文件，但仍然跑不起来，原因不明。
其实能跑Linux的MIPS模拟器也不算多，主要还是qemu，
还有几个不太出名的，我以后想办法解决uMIPS的问题，
或者像uARM那样找一些别的轻量级替代品——这方面还是太难了，
当然自己写一个就更难了，但如果能写出来的话意义非凡，
这样可以进一步理解Linux的运行机制，
以及固件如何编译和运行的更详细的过程分析
```
```
umips研究，似乎可以编译出来（只能xubuntu20或以上，但旧的ubuntu不行），
但跑不起来，缺了个磁盘镜像文件（内置vmlinux内核），
还需要研究（如果找不到这个磁盘，有可能研究不出来） 
```
```
如果深究下去，发现gh上有很多类似tvlad1234/linux-ch32v00的项目
（基于mini-rv32ima），例如TinyEMU是另一个比较出名的RISC-V模拟器
（作者就是qemu的作者bellard），又例如ElectroBoy404NotFound/uARM和uMIPS，
也就是说早就有人研究MIPS和ARM的小型模拟器——虽然我还没试验过能否跑起来

```

## TODO  
* How to build vmlinux and disk ?  
