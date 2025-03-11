

代办分析：

1. 


================ mm ===============
mm同内存条的交互逻辑？？调度程序怎么走到CPU内部的？？ 软件和硬件之间的一层具体是什么？

内存碎片的管理机制感觉很多，如何兼容？？

Hugemem内核通过打入4G/4G补丁，将内核和用户空间的虚拟地址空间都扩大到4GB，从而增加了低端内存（可直接映射的内存）的容量。

interval tree？？区间树？

filemap，iomap，mmap差异。区别，底层机理？？

madvise说明内存也是有随机顺序的访问方式的，随机顺序对性能的影响，处罚条件，评估方式？？

内核swap机制？
由于内存和磁盘的读写性能差异较大，Linux会在内存充裕时将空闲内存用于缓存磁盘数据，以提高I/O性能。
相对的在内存紧张时Linux会将这些缓存回收，将脏页回写到磁盘中。而在进程的地址空间中，如heap，stack等匿名页，在磁盘上并没有对应的文件，但同样有回收到磁盘上以释放出空闲内存的需求。swap机制通过在磁盘上开辟专用的swap分区作为匿名页的backing storage，满足了这一需求


内核页表隔离（Kernel page-table isolation，缩写KPTI，也简称PTI，旧称KAISER）是Linux内核中的一种强化技术，旨在更好地隔离用户空间与内核空间的内存来提高安全性，缓解现代x86 CPU中的“熔毁”硬件安全缺陷。
pti，即Page Table Isolation，previously known as KAISER。

其主要思想：让内核态和用户态使用独立的页表。确切的说，每个进程拥有两张页表：用户态页表和内核态页表，当用户态和内核态切换时，同时切换相应页表。

如此，由于使用独立页表，那么内核态和用户态地址则分别处于不同的TLB context中，如此，用户态再无法通过CPU的预测执行来访问内核态地址了，即可规避掉Meltdown漏洞。


================ ext4 ===============

1.superblock的位置各不相同，linux如何做到识别各文件系统   type驱动注册， magic校验
2.分区，格式化后的磁盘分区，系统启动后如何自动挂载   
3.依赖配置文件，fstab，rootfs还没有挂在起来，这个配置文件怎么读？rootfs的挂载也要通过fstab来实现
4.

引导程序的配置文件（如 GRUB 的 /boot/grub/grub.cfg）中包含了启动时需要加载的内核和 initramfs 的路径，以及根文件系统的位置信息。
这些信息通常在系统安装或手动配置时写入配置文件中。

1. 引导程序传递的内核参数
在启动过程中，引导程序（如 GRUB）会将内核参数传递给内核。这些参数中通常包含根文件系统的位置信息，例如：
root=：指定根文件系统所在的设备，如 root=/dev/sda1。
root=UUID=：通过 UUID 指定根文件系统，如 root=UUID=12345678-1234-1234-1234-123456789012。
2. 内核解析内核参数
内核启动时会解析这些参数，并将根文件系统的位置信息传递给 initramfs。initramfs 中的初始化脚本（通常是 /init）会读取这些参数。
3. initramfs 中的 /init 脚本
initramfs 中的 /init 脚本负责执行以下任务：
加载必要的驱动程序：根据内核参数或配置文件加载必要的驱动程序，以便能够访问根文件系统所在的设备。
挂载根文件系统：使用 mount 命令将根文件系统挂载到一个临时目录（如 /root 或 /sysroot）。
切换根文件系统：使用 pivot_root 或 switch_root 系统调用，将当前根文件系统切换到真正的根文件系统。


在安装过程中，安装程序通常会提供选择引导程序的选项。常见的引导程序包括：
GRUB（GRand Unified Bootloader）：目前最常用的引导程序，支持多种文件系统和启动方式。
LILO（Linux Loader）：较早期的引导程序，功能相对简单。
Syslinux：常用于 Live CD 和 USB 启动盘。

　stage1：运行Boot Loader主程序，这个程序必须要安装在启动区，即MBR中。因为MBR空间有限，因此在MBR当中仅安装Boot Loader的最小程序，并没有安装Boot Loader的相关配置文件
　　stage1_5：MBR之后的扇区，让stage1中的BootLoader能识别stage2所在的分区上的文件系统(相当于文件系统的驱动)
　　stage2：boot目录所在的磁盘分区(/boot/grub)
　　配置文件：/etc/grub.conf软链接到/boot/grub/grub.conf
       /boot/grub/menu.lst软链接到/boot/grub/grub.conf



ext4_allocation_request



=====================================




open流程:
SYSCALL_DEFINE3(open, const char __user *, filename, int, flags, umode_t, mode)
{
	if (force_o_largefile())
		flags |= O_LARGEFILE;
	return do_sys_open(AT_FDCWD, filename, flags, mode);
}

static long do_sys_openat2(int dfd, const char __user *filename,
			   struct open_how *how)

struct file *do_filp_open(int dfd, struct filename *pathname,
		const struct open_flags *op)

static struct file *path_openat(struct nameidata *nd,
    const struct open_flags *op, unsigned flags)

static int do_open(struct nameidata *nd,
		   struct file *file, const struct open_flags *op)

int vfs_open(const struct path *path, struct file *file)


/* Alas, no aliases. Too much hassle with bringing module.h everywhere */
#define fops_get(fops) ({						\
	const struct file_operations *_fops = (fops);			\
	(((_fops) && try_module_get((_fops)->owner) ? (_fops) : NULL));	\
})

块设备的 inode->i_fop 赋值过程主要发生在文件系统初始化或设备节点创建时

块设备的 inode->i_fop 赋值过程如下：
在设备节点创建时，通过 init_special_inode 函数将 def_blk_fops 赋值给 inode->i_fop。
在文件打开时，do_dentry_open 函数会调用 fops_get 将 inode->i_fop 赋值给 file 结构体的 f_op 字段。
fops_get 函数会增加文件操作结构体的引用计数

const struct file_operations def_blk_fops = {
	.open		= blkdev_open,
	.release	= blkdev_release,
	.llseek		= blkdev_llseek,
	.read_iter	= blkdev_read_iter,
	.write_iter	= blkdev_write_iter,
	.iopoll		= iocb_bio_iopoll,
	.mmap		= blkdev_mmap,
	.fsync		= blkdev_fsync,
	.unlocked_ioctl	= blkdev_ioctl,
#ifdef CONFIG_COMPAT
	.compat_ioctl	= compat_blkdev_ioctl,
#endif
	.splice_read	= filemap_splice_read,
	.splice_write	= iter_file_splice_write,
	.fallocate	= blkdev_fallocate,
	.uring_cmd	= blkdev_uring_cmd,
	.fop_flags	= FOP_BUFFER_RASYNC,
};


类似的问题有很多，比如softraid，glibc中syscall的实现和内核版本的对应，
mkfs.ext4需要和内核中的ext4版本保持一致，
那是否可能会存在该问题：做好的产品后续遇到内核版本升级，导致无法兼容原来已经格式化好的ext4

IO完整链路：
APP->系统调用->VFS->pagecache->通用block层->IO调度->设备驱动层-> 设备cache->设备disk

无论是DirectIO还是BufferIO，
最后都会通过submit_bio()将IO请求提交到通用块层，
通过generic_make_request转发bio ，
generic_make_request函数主要是获取请求队列，然后调用make_request_fn方法处理bio


==========
通用块层的代码链路，scsi层的实现逻辑
块分配逻辑及算法；

每日代办的框架，理论和实践分开，
理论中包含技术（看是一方面，主要是提出问题，不断的加强网络）+ 思维（系统，管理，价值，文章，等）； 
实践按照项目走，项目最好有预估的时间；



通用块层的价值，意义在哪里？
1. 提供统一接口
由于不同块设备（如磁盘、SSD 等）有着不同的设备驱动程序，通用块设备层提供了一个统一的接口，使得文件系统可以无差别地访问这些设备。这样，文件系统实现者无需关心不同设备驱动程序的差异，从而简化了文件系统的开发。
2. 提高 I/O 效率
通用块设备层负责对文件系统的 I/O 请求进行排队和调度，通过重新排序、请求合并等方式，提高磁盘读写的效率。它还支持多种 I/O 调度算法，如 NOOP、CFQ、Deadline 等，这些算法可以根据不同的应用场景和需求进行选择。
3. 支持高级特性
通用块设备层支持多种高级特性，如零拷贝（zero-copy）、大磁盘缓存、增强的 DMA 功能等。这些特性可以显著提高数据传输的效率，减少 CPU 的负担。
4. 管理逻辑卷
通用块设备层还支持逻辑卷管理（LVM）和 RAID 等技术，使得用户可以灵活地管理磁盘空间。
5. 适配现代存储设备
随着高速 SSD 的出现，传统的块设备层逐渐成为系统 I/O 性能的瓶颈。为了适配现代存储设备高 IOPS、低延迟的 I/O 特征，Linux 内核引入了多队列框架（Block multi-queue，blk-mq），极大地提高了 Block Layer 并发处理 I/O 的能力。
6. 缓冲区和缓存管理
通用块设备层还负责管理缓冲区和缓存，如页缓存、索引节点缓存和目录项缓存等，这些缓存机制用于减少对块设备的直接访问，提高对文件数据的读取和写入效率。
总之，通用块设备层是 Linux 内核中不可或缺的一部分，它通过提供统一接口、优化 I/O 调度、支持高级特性等方式，极大地提高了存储系统的性能和可靠性。


bio结构体代表的是I/O操作，它可以包括内存中的一个或多个页；
而另一方面， buffer_head结构体代表的是一个缓冲区，它描述的仅仅是磁盘中的一个块


 IO调度器（IOScheduler）是操作系统用来决定块设备上IO操作提交顺序的方法
 存在的目的有两个，一是提高IO吞吐量，二是降低IO响应时间。
 然而IO吞吐量和IO响应时间往往是矛盾的，为了尽量平衡这两者，IO调度器提供了多种调度算法来适应不同的IO请求场景。

IO调度算法，为何很多都依赖红黑树，好处及价值是什么？

NOOP：电梯调度算法；

deadline算法；

anticipatory算法：

CFQ算法：默认的调度算法：











