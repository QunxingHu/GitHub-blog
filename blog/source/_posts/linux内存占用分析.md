---
title: Linux 内存占用分析
date: 2017-3-31
categories:
- 术业专攻
tags: 
- Linux
---
这篇博客主要介绍 linux 环境下，查看内存占用的两种方式：使用 `ps,top`等命令；查看`/proc/[pid]/`下的文件。文章简要介绍了命令的使用方法与一些参数意义，同时对`/proc/[pid]/`下的文件内容进行了一些详细的介绍。文章内容来自google和自我总结，如有不当之处，欢迎批评指正。
<!-- more -->

# 查看 linux 中内存占用的方法

linux 下面查看内存有多种渠道，比如通过命令 ps ,top,free， pmap 等，或者通过/proc系统。一般情况下，`ps,top,pmap,free`可以满足要求，如果需要比较详细和精确地知道整机内存或者某个进程内存的使用情况，可以通过/proc 系统。

## 使用命令

**free **： 显示系统可用内存以及已经使用的内存的信息
**ps：** 查看进程信息，静态，即当前状态
**top： ** 查看进程信息，动态
**pstree：** 查看进程树
**pmap：** 根据进程ID查看进程信息

### ps vs top
1. ps命令–提供系统过去信息的**一次性快照**,也就是说ps命令能够查看刚刚系统的进程信息。
2. top命令反应的是**系统进程动态信息**，默认10s更新一次。
3. ps和top都是从/proc目录下读取进程的状态信息，内核把当前系统进程的各种有用信息都放在这个伪目录下。

**常见ps命令：**
`ps -aux`: 查看系统所有进程
`ps -l`: 进查看自己的bash相关进程

**top 命令详解**，请参考http://www.jb51.net/article/40807.htm
**基本命令：**
- 按键盘数字“1”，可监控每个逻辑CPU的状况：
- 键盘“b”（打开/关闭加亮效果）
- 键盘“x”（打开/关闭排序列的加亮效果）
- ”shift + >”或”shift + <”可以向右或左改变排序列
- 敲击“f”键，编排基本视图中的显示字段
- s，改变画面更新频率
- l，关闭或开启第一部分第一行 top 信息的表示
- t，关闭或开启第一部分第二行 Tasks 和第三行 Cpus 信息的表示
- m，关闭或开启第一部分第四行 Mem 和 第五行 Swap 信息的表示

**具体使用方法，可以使用 `man [cmd]` 查看。**

### 相关参数说明
**VSZ & VIRT**
1. 进程使用的虚拟内存值总量，包括所有代码，数据，共享库已经被swapped out的。VIRT = SWAP + RES。
2. VSZ来自`ps`命令， VIRT来自`top`命令，二者均表示进程占用的虚拟内存大小。
3. 假如进程申请100m的内存，但实际只使用了10m，那么它会增长100m，而不是实际的使用量

**RES & RSS**
1. 进程当前使用的内存大小，但不包括swap out. RES = CODE +DATA。
2. 包含其他进程的共享
3. RES 来自 `top` 命令， RSS 来自 `ps` 命令，两者在表示意义上没有区别，都是从 `/cat/proc/[pid]/stat` 文件中读取的信息。
4. 如果申请100m的内存，实际使用10m，它只增长10m，与VIRT相反
5. 关于库占用内存的情况，它只统计加载的库文件所占内存大小

**CODE**
可执行代码占用的物理内存大小

**DATA**
1. 物理内存中存放数据的大小，在程序运行中需要用到
2. 如果`top`命令没有显示， 按`f`键显示

**SHR**
1. 共享内存大小
2. 除了自身进程的共享内存，也包含其他进程的共享内存
3. **计算某个进程所占用物理内存的大小： RES - SHR**
4. swap out后，该值会下降。

### 查看一个进程的内存信息步骤

1. 获取进程PID

```shell
$ ps -aux | grep /usr/sbin/NetworkManager
root 845 0.0 0.0 387084 13332 ? Ssl 3月28 0:00 /usr/sbin/NetworkManager --no-daemon

```

2. 查看进程的所有线程
```shell
$ ps mp 845 -o THREAD,tid
USER %CPU PRI SCNT WCHAN USER SYSTEM TID
root 0.0 - - - - - -
root 0.0 19 - - - - 845
root 0.0 19 - - - - 1025
root 0.0 19 - - - - 1027

```
3. 查看所有子进程
```shell
$ pstree -p 845
NetworkManager(845)─┬─dhclient(30278)
├─dnsmasq(1123)
├─{gdbus}(1027)
└─{gmain}(1025)
```
## 使用 /proc 下文件
`/proc/[pid]/` 下面与进程内存相关的文件主要有`maps , smaps, status`。
**maps：** 文件可以查看某个进程的代码段、栈区、堆区、动态库、内核区对应的虚拟地址
**smaps:** 显示每个分区更详细的内存占用数据
**status:** 包含了所有CPU活跃的信息，该文件中的所有值都是从系统启动开始累计到当前时刻

**有名与匿名：**
一个文件可以映射到进程的一段内存区域中，映射的文件描述符保存在vm_area_struct->vm_file域中，这种内存区域叫做有名内存区域，相反，属于匿名映射内存区域。

### maps 文件分析
`Proc/[pid]/maps` 显示进程映射了的内存区域和访问权限。对应内核中的操作集为 proc_pid_maps_op，具体的导出函数为 show_map 。内核中进程的一段地址空间用一个vm_area_struct结构体表示，所有地址空间存储在task->mm->mmap链表中。

截取一行内容如下：
```
7f4e3f5ca000-7f4e3f674000 r-xp 00000000 08:02 525202 /usr/lib/x86_64-linux-gnu/NetworkManager/libnm-device-plugin-wifi.so

```
Vm_area_struct每项对应解析如下表所示：

| vm_area_struct项 | maps项 | 含义 |
|:----------------:|:---------------------------:|:-----------------------------------|
| vm_start | “-”前一列，如7f4e3f5ca000 | 此段虚拟地址空间起始地址 |
| vm_end | “-”后一列，如7f4e3f674000 | 此段虚拟地址空间结束地址 |
| vm_flags | 第三列，如r-xp | 此段虚拟地址空间的属性。r:读，w:写，x:执行，p和s共用一个字段，互斥关系，p表示私有段，s表示共享段，如果没有相应权限，则用’-’代替|
| vm_pgoff | 第四列，如00000000 | 对有名映射，表示此段虚拟内存起始地址在文件中以页为单位的偏移。对匿名映射，它等于0或者vm_start/PAGE_SIZE
| vm_file->f_dentry->d_inode->i_sb->s_dev | 第五列，如08:02 | 映射文件所属设备号。对匿名映射l来说，因为没有文件在磁盘上，所以没有设备号，始终为00:00。对有名映射来说，是映射的文件所在设备的设备号|
| vm_file->f_dentry->d_inode->i_ino | 第六列，如525202 | 映射文件所属节点号。对匿名映射来说，因为没有文件在磁盘上，所以没有节点号，始终为00:00。对有名映射来说，是映射的文件的节点号 |
| | 第七列，如/lib/ld-2.5.so |对有名来说，是映射的文件名。对匿名映射来说，是此段虚拟内存在进程中的角色。[stack]表示在进程中作为栈使用，[heap]表示堆。其余情况则无显示|


**maps文件只能显示简单的分区，smap文件可以显示每个分区的更详细的内存占用数据。**

### smap 文件分析

截取一段文件，各字段解析如下：
```
7f148b2fa000-7f148b2fb000 rw-p 00026000 08:02 2883675 /lib/x86_64-linux-gnu/ld-2.23.so
Size: 4 kB 虚拟内存大小
Rss: 4 kB 实际使用物理内存大小 RSS = Shared_Clean+Shared_Dirty+Private_Clean+Private_Dirty
Pss: 4 kB RSS中私有的内存页面
Shared_Clean: 0 kB RSS中共享内存，没有被改写的页面
Shared_Dirty: 0 kB RSS中共享内存，被改写的页面
Private_Clean: 0 kB RSS中私有内存，未被改写
Private_Dirty: 4 kB RSS中私有内存，被改写
Referenced: 4 kB
Anonymous: 4 kB
AnonHugePages: 0 kB
Shared_Hugetlb: 0 kB
Private_Hugetlb: 0 kB
Swap: 0 kB 处于交换区的页面大小
SwapPss: 0 kB
KernelPageSize: 4 kB 操作系统一个页面大小
MMUPageSize: 4 kB 体系结构MMU一个页面大小
Locked: 0 kB
VmFlags: rd wr mr mw me dw ac sd

```
Dirty页面如果没有交换机制的情况下，应该是不能回收的。

**分析脚本：**
自己写了个简单的分析脚本，如下，可以根据需要进行修改。
```shell
#! /bin/bash

awk 'BEGIN{
        total = 0;
        printf("SIZE\tRSS\tSHARED_CLEAN\tSHARED_DIRTY\tPRIVATE_CLEAN\tPRIVATE_DIRTY\n")
    }{
        if(NF >3){
            if($2 ~ /[r-][w-][x-][ps]/){
                if($6 =="")
                    name = $1;
                else
                    name = $6;
         }
         }
        while(getline)
        {
            if(NF >3){
                if($2 ~ /[r-][w-][x-][ps]/){
                    if($6 =="")
                        name = $1;
                    else
                        name = $6;
                }
            }

            if($1 ~ /^Size/){
                size = $2;
                total += $2;
            }  
            
            if($1 ~ /Rss/){
                rss = $2;
            }
            
            if($1 ~ /Shared_Clean/){
                shared_clean = $2;
            }

            if($1 ~ /Shared_Dirty/){
                shared_dirty = $2;
            }
        
            if($1 ~ /Private_Clean/){
                private_clean = $2;
            }
            
            if($1 ~ /Private_Dirty/){
                private_dirty = $2;
            }

            if($1 ~ /VmFlags/){
                printf("%d\t%d\t%d\t%d\t%d\t%d\t%s\n",size,rss,shared_clean,shared_dirty,private_clean,private_dirty,name);
                size = 0;
                name = "";
                rss = 0;
                shared_clean = 0;
                shared_dirty = 0;
                private_clean = 0;
                private_dirty = 0;
                continue;
            } 
        }
    }END{
        printf("====total: %d\n", total);
    }' $1
```

**关于匿名映射**
smaps 中可能会存在大量的匿名区域，它们是使用 mmap 机制生成的，但是没有关联到任何一个文件。通常情况下，它们主要用于处理一些琐碎的任务，比如处理没有在堆中申请的共享内存或者缓冲区。比如 pthread 使用匿名映射区作为新线程的栈，在linux 下，pthread 为 新线程申请 8M 空间作为栈空间 已经一个小小片内存（如4Kb）用于检测内存溢出。所以在每个pthread创建时，会分配一个映射到节点0的8Mb内存，和一个映射到节点0的4Kb区域。



### status 文件分析

截取文件，解析个字段如下：
```
Develop>cat /proc/24475/status
Name: netio 可执行程序的名字
State: R (running) 任务状态，运行/睡眠/僵死
Tgid: 24475 线程组号
Pid: 24475 进程id
PPid: 19635 父进程id
TracerPid: 0
Uid: 0 0 0 0
Gid: 0 0 0 0
FDSize: 256 该进程最大文件描述符个数
Groups: 0
VmPeak: 6330708 kB 内存使用峰值
VmSize: 268876 kB 进程虚拟地址空间大小
VmLck: 0 kB 进程锁住的物理内存大小，锁住的物理内存无法交换到硬盘
VmHWM: 16656 kB
VmRSS: 11420 kB 进程正在使用的物理内存大小
VmData: 230844 kB 进程数据段大小
VmStk: 136 kB 进程用户态栈大小
VmExe: 760 kB 进程代码段大小
VmLib: 7772 kB 进程使用的库映射到虚拟内存空间的大小
VmPTE: 120 kB 进程页表大小
VmSwap: 0 kB
Threads: 5 共享使用该信号描述符的任务的个数，在POSIX多线程序应用程序中，线程组中的所有线程使用同一个信号描述符。
SigQ: 0/63346 待处理信号的个数
SigPnd: 0000000000000000 屏蔽位，存储了该线程的待处理信号
ShdPnd: 0000000000000000 屏蔽位，存储了该线程组的待处理信号
SigBlk: 0000000000000000 存放被阻塞的信号
SigIgn: 0000000001000000 存放被忽略的信号
SigCgt: 0000000180000000 存放被俘获到的信号
CapInh: 0000000000000000 能被当前进程执行的程序的继承的能力
CapPrm: ffffffffffffffff 进程能够使用的能力，可以包含CapEff中没有的能力，这些能力是被进程自己临时放弃的，CapEff是CapPrm的一个子集，进程放弃没有必要的能力有利于提高安全性
CapEff: ffffffffffffffff 进程的有效能力
CapBnd: ffffffffffffffff
Cpus_allowed: 01
Cpus_allowed_list: 0
Mems_allowed: 01
Mems_allowed_list: 0
voluntary_ctxt_switches: 201
nonvoluntary_ctxt_switches: 909
```


### meminfo 文件分析

整机内存使用情况的文件`/proc/meminfo`，摘取分析如下
```
Develop>cat /proc/meminfo
MemTotal: 8112280 kB 所有可用RAM大小 （即物理内存减去一些预留位和内核的二进制代码大小）
MemFree: 4188636 kB LowFree与HighFree的总和，被系统留着未使用的内存
Buffers: 34728 kB 用来给文件做缓冲大小
Cached: 289740 kB 被高速缓冲存储器（cache memory）用的内存的大小
（等于 diskcache minus SwapCache ）
SwapCached: 0 kB 被高速缓冲存储器（cache memory）用的交换空间的大小
已经被交换出来的内存，但仍然被存放在swapfile中。
用来在需要的时候很快的被替换而不需要再次打开I/O端口
Active: 435240 kB 在活跃使用中的缓冲或高速缓冲存储器页面文件的大小，
除非非常必要否则不会被移作他用
Inactive: 231512 kB 在不经常使用中的缓冲或高速缓冲存储器页面文件的大小，可能被用于其他途径.
Active(anon): 361252 kB
Inactive(anon): 120688 kB
Active(file): 73988 kB
Inactive(file): 110824 kB
Unevictable: 0 kB
Mlocked: 0 kB
SwapTotal: 0 kB 交换空间的总大小
SwapFree: 0 kB 未被使用交换空间的大小
Dirty: 0 kB 等待被写回到磁盘的内存大小
Writeback: 0 kB 正在被写回到磁盘的内存大小
AnonPages: 348408 kB 未映射页的内存大小
Mapped: 33600 kB 已经被设备和文件等映射的大小
Shmem: 133536 kB
Slab: 55984 kB 内核数据结构缓存的大小，可以减少申请和释放内存带来的消耗
SReclaimable: 25028 kB 可收回Slab的大小
SUnreclaim: 30956 kB 不可收回Slab的大小（SUnreclaim+SReclaimable＝Slab）
KernelStack: 1896 kB 内核栈区大小
PageTables: 8156 kB 管理内存分页页面的索引表的大小
NFS_Unstable: 0 kB 不稳定页表的大小
Bounce: 0 kB
WritebackTmp: 0 kB
CommitLimit: 2483276 kB
Committed_AS: 1804104 kB
VmallocTotal: 34359738367 kB 可以vmalloc虚拟内存大小
VmallocUsed: 565680 kB 已经被使用的虚拟内存大小
VmallocChunk: 34359162876 kB
HardwareCorrupted: 0 kB
HugePages_Total: 1536 大页面数目
HugePages_Free: 0 空闲大页面数目
HugePages_Rsvd: 0
HugePages_Surp: 0
Hugepagesize: 2048 kB 大页面一页大小
DirectMap4k: 10240 kB
DirectMap2M: 8302592 kB
```

## 总结
linux下内存占用是一个比较复杂的概念，不能简单通过一个单一指标就判断某个程序“内存消耗”大小，因为进程所申请的内存不一定真正会被用到（malloc或mmap的实现）而且真正用到了的内存也不一定是只有该进程自己在用 (比如动态共享库)。我们应该根据具体需求选择合适的方式去分析内存占用。
