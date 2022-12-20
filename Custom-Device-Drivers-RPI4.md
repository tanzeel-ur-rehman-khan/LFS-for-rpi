# Custom Device Drivers for Raspberry Pi 

### Why Device Drivers ?

Why are drivers even a thing that we have to worry about when we're doing an embedded development? Let's say we want to access a certain GPIO pin with address ox3f2000 in USER space (through OS) directly, it will not allow us to do that because it is the unprivileged version of the processor so if you write code that goes to access this address ox3f2000, you're going to get a serious set of errors that will crash your program and not do the functionality that you want. 

You're trying to do two things that are illegal when you're talking about embedded development

<p align="center">
<img src="tutorial-images/dd-1.PNG">
</p>

The first is you're trying to cross over a memory boundary in user space you are accessing memory that is being virtually translated from a virtual address to a physical address either in ram or a physical interface on the processor when you access this without asking the kernel for permission essentially or mapping it into your processor that creates your first error your second error is when you try to directly access the gpio bus without talking to the hardware abstraction layer your code may work for your processor but the minute you try to translate that to a new kernel or to a new version of the os or a new chip it's not going to work either.

<p align="center">
<img src="tutorial-images/dd-2.PNG">
</p>

It's the job of these things called drivers to create these they look like tunnels in terms of a diagram but they're really are an interface to legally cross this memory
boundary and to legally cross this driver interface to the hardware abstraction layer.
