#Ubuntu 14.04 add a new system_call :


0.install libncurses5-dev

    apt-get install gcc make libncurses5-dev



##1.download ubuntu kernel source code

	sudo apt-get install linux-source





##2.add a new systemCall source in the kernel 


###a. arch/x86/syscalls/syscall_64.tbl 

	add a new line

	318     common  mysyscall               sys_mysyscall



###b. gedit include/uapi/asm-generic/unistd.h

	add  __SYSCALL(__NR_mysyscall, sys_mysyscall)


	change #define __NR_syscalls 278 => #define __NR_syscalls 279




###c. add systemCall source in kernel/sys.c 


    SYSCALL_DEFINE0(mysyscall)
    {
	    printk("mysyscall test\n");
	    return 0;
    }





##3.compile the kernel source and install the new kernel

	cd /usr/src/kernel-directory
	cp /boot/config-xxx   ./config

    make menuconfig
    make bzImage 
    make modules 
    make modules_install 
    make install



    GRUB_DEFAULT=1 
    GRUB_HIDDEN_TIMEOUT=5


    update-grub 






##4.test the new system on the new kernel


	#include <unistd.h>
	#include <sys/syscall.h>
	#include <errno.h>
	#include <stdio.h>
	
	int main(void)
	{
	        syscall(318) ;
	        printf("errno = %d\n", errno) ;
	        return  0;
	}







[YOUTUBE vedio Here](https://www.youtube.com/playlist?list=PL6d10Xug8jOwrtp21josB5kxNEggaBu04)