###Linux头文件

Tags: Linux

1.内核编程使用头文件
 
>The base files are located in the include/ directory in the root of the kernel source tree. For 
example, the header file <linux/inotify.h> is located at include/linux/inotify.h in the kernel source tree.
 
>A set of architecture-specific header files are located in arch/architecture/include/asm 
in the kernel source tree. For example, if compiling for the x86 architecture, your architecture-specific headers are in arch/x86/include/asm. Source code includes these headers via just the asm/ prefix, for example asm/ioctl.h
 
--摘自《Linux Kernel Development》英文版第三版第17页
 
2.应用程序编程使用头文件
 
>Linux system programming revolves around a handful of headers. Both the kernel itself
and  glibc provide the headers used in system-level programming. These headers include
the standard C fare (for example, string.h), and the usual Unix offerings (say, unistd.h)
 
--摘自《Linux System Programming》英文版第二版第21页
 
3.内核头文件的使用参考 [1](http://blog.chinaunix.net/uid-20543672-id-3162485.html) [2](http://kernelnewbies.org/KernelHeaders)




