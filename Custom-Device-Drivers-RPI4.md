# Custom Device Drivers for Raspberry Pi 

### Why Device Drivers ?

Why are drivers even a thing that we have to worry about when we're doing an embedded development? Let's say we want to access a certain GPIO pin with address ox3f2000 in USER space (through OS) directly, it will not allow us to do that because it is the unprivileged version of the processor so if you write code that goes to access this address ox3f2000, you're going to get a serious set of errors that will crash your program and not do the functionality that you want. 

You're trying to do two things that are illegal when you're talking about embedded development

<p align="center">
<img src="tutorial-images/dd-1.PNG">
</p>

The first is you're trying to cross over a memory boundary in user space you are accessing memory that is being virtually translated from a virtual address to a physical address either in ram or a physical interface on the processor when you access this without asking the kernel for permission essentially or mapping it into your processor that creates your first error your second error is when you try to directly access the gpio bus without talking to the hardware abstraction layer your code may work for your processor but the minute you try to translate that to a new kernel or to a new version of the os or a new chip it's not going to work either.

<p align="center">
<img src="tutorial-images/dd-3.PNG">
</p>

It's the job of these things called drivers to create these they look like tunnels in terms of a diagram but they're really are an interface to legally cross this memory
boundary and to legally cross this driver interface to the hardware abstraction layer.

## Basic Hello World Driver for Host PC

Let's first build a basic device driver for our host PC, then we'll shift to RPI drivers. 
  1.  Write basic hello world driver code in C
  ```
  mkdir drivers
  cd drivers/
  gedit
  ```
  Paste the following code in text editor and save file as hello_world.c in ~/drivers DIR
  ```
  #include <linux/module.h>
  
  static int __init hello_world_init (void)
  {
      pr_info("Hello, World!\n");
      return 0;
  }
  
  static void __exit hello_world_exit (void)
  {
      pr_info("Goodbye, World!\n");
  }
  
  module_init(hello_world_init);
  module_exit(hello_world_exit);
  
  MODULE_LICENSE("GPL");
  MODULE_AUTHOR("EE522 (22100172@lums.edu.pk)");
  MODULE_DESCRIPTION("A simple hello world kernel module");
  ```
  
  Here’s a basic breakdown of this driver:

  line 1: we import the linux module header file, because this is a kernel module ; any calls to userland functions (such as printf) make no sense in the kernel.
  
  lines 4-9: the initialization entry point. We have the static keyword because this function should only be defined and used in this file, and __init because it’s good practice (even though this is a dynamic module so the __init directive will have no effect). We then just print a “hello world” message and return 0, like a classic main “hello world” function would.
  
  lines 12-15: the de-initialization entry point. Similarly, static and __exit are used; the latter is needed since dynamic modules can be unloaded/removed (but again it is good practice to always have these directives). Once more, we print a message using kernel functions.
  
  lines 18-19: assignment of the entry points using kernel macros module_init and module_exit.
  
  lines 21-23: various kernel module info (not really necessary in this case but might as well showcase them).

  2.  Create a file named 'Makefile' (NO FILE EXTENSION) in ~/drivers DIR and paste the following in it.
  
  ```
  obj-m += hello_world.o
  ```
  
  3.  Compile the code. 
  In order to compile our code, we first need to know kernel_modules installed in our system. 
  ```
  uname -r
  ```
  <p align="center">
<img src="tutorial-images/dd-4.PNG">
</p>
  
  ```
  sudo make -C /lib/modules/(your-kernel-version)/build M=$PWD modules
  ```
  <p align="center">
<img src="tutorial-images/dd-5.PNG">
</p>
  
  4.  Try to insert your first driver and check using dmesg. 
  ```
  sudo insmod hello_world.ko
  dmesg
  ```
  <p align="center">
<img src="tutorial-images/dd-6.PNG">
</p>
  
  > __Note__
  > dmesg displays kernel-related messages retrieved from the kernel ring buffer. The ring buffer stores information about hardware, device driver initialization, and messages from kernel modules that take place during system startup. It will print a lot other stuff than your "hello_world!"
  5.  Now remove driver and check dmesg again.
  ```
  sudo rmmod hello_world.ko
  dmesg
  ```
  <p align="center">
<img src="tutorial-images/dd-7.PNG">
</p>
  
  > __Note__ 
  > Why would it print both "hello world!" and "goodbye world!" after you do rmmod ? Because dmesg prints all kernel messages _from startup_

## Basic Hello World Driver for Raspberry Pi 

  1.  Install cross-compile toolchain. 
  ```
  sudo apt install crossbuild-essential-arm64
  ```
  2.  Remake kernel config just in case if its broken
  ```
  export PATH=${HOME}/x-tools/aarch64-rpi4-linux-gnu/bin/:$PATH
  export CROSS_COMPILE=aarch64-rpi4-linux-gnu-
  make ARCH=arm64 CROSS_COMPILE=aarch64-rpi4-linux-gnu- bcm2711_defconfig
  make -j$(nproc) ARCH=arm64 CROSS_COMPILE=aarch64-rpi4-linux-gnu-
  ```
  3.  Compile driver code for RPI 4
  ```
  sudo make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- -C ~/linux M=~/drivers modules
  ```
  <p align="center">
<img src="tutorial-images/dd-8.PNG">
</p>

  4.  Copy 'hello_world.ko' file to /home DIR in RPI RootFS (Change Username to yours in the following command)
  ```
  sudo cp hello_world.ko /media/(username)/root/home
  sync
  ```
  5.  Install driver in RPI using the same commands we used for host PC
  ```
  sudo insmod hello_world.ko
  ```
  6.  And remove driver using the following command
  ```
  sudo rmmod hello_world.ko
  ```
  
There you have it, your first basic device driver for Raspberry Pi 4 ✌

## Advanced Device Drivers

Let's now have a look on how we can build device drivers for hardware devices like a single LED and LED matrix.

