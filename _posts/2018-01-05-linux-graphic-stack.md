---
title: 'linux command - tree / abc'
layout: post
tags:
  - linux
category: linux
---
#### Graphic stack 정리
출처: http://blog.mecheye.net/2012/06/the-linux-graphics-stack/

<br>

##### 3D rendering with OpenGL
1. Your program starts up, using “OpenGL” to draw.
2. A library, “Mesa”, implements the OpenGL API. It uses card-specific drivers to translate the API into a hardware-specific form. If the driver uses Gallium internally, there’s a shared component that turns the OpenGL API into a common intermediate representation, TGSI. The API is passed through Gallium, and all the device-specific driver does is translate from TGSI into hardware commands, 
3. libdrm uses special secret card-specific ioctls to talk to the Linux kernel
4. The Linux kernel, having special permissions, can allocate memory on and for the card.
5. Back out at the Mesa level, Mesa uses DRI2 to talk to Xorg to make sure that buffer flips and window positions, etc. are synchronized.

<br>

##### 2D rendering with cairo
1. Your program starts up, using cairo to draw.
2. You draw some circles using a gradient. cairo decomposes the circles into trapezoids, and sends these trapezoids and gradients to the X server using the XRender extension. In the case where the X server doesn’t support the XRender extension, cairo draws locally using libpixman, and uses some other method to send the rendered pixmap to the X server.
3. The X server acknowledges the XRender request. Xorg can use multiple specialized drivers to do the drawing.
	a. In a software fallback case, or in the case the graphics driver isn’t up to the task, Xorg will use pixman to do the actual drawing, similar to how cairo does it in its case.
	b. In a hardware-accelerated case, the Xorg driver will speak libdrm to the kernel, and send textures and commands to the card in the same way.

<br>

##### X Window System, X11, Xlib, Xorg
X11 isn’t just related to graphics; it has an event delivery system, the concept of properties attached to windows, and more. Lots of other non-graphical things are built on top of it (clipboard, drag and drop). Only listed in here for completeness, and as an introduction. I’ll try and post about the entire X Window System, X11 and all its strange design decisions later.

1. X11
The wire protocol used by the X Window System
2. Xlib
The reference implementation of the client side of the system, and a host of tons of other utilities to manage windows on the X Window System. Used by toolkits with support for X, like GTK+ and Qt. Vanishingly rare to see in applications today.
3. XCB
Sometimes quoted as an alternative to Xlib. It implements a large amount of the X11 protocol. Its API is at a much lower level than Xlib, such that Xlib is actually built on XCB nowadays. Only mentioning it because it’s another acronym.
4. Xorg
The reference implementation of the server side of the system.

<br>

##### OpenGL, Mesa, gallium

Now comes the fun part: modern hardware acceleration. I assume everybody already knows what OpenGL is. It’s not a library, there will never be one set of sources to a libGL.so. Each vendor is supposed to provide its own libGL.so. NVIDIA provides its own implementation of OpenGL and ships its own libGL.so, based on its implementations for Windows and OS X.

If you are running open-source drivers, your libGL.so implementation probably comes from Mesa. Mesa is many things, but one of the major things it provides that it is most famous for is its OpenGL implementation. It is an open-source implementation of the OpenGL API. Mesa itself has multiple backends for which it provides support. It has three CPU-based implementations: swrast (outdated and old, do not use it), softpipe (slow), llvmpipe (potentially fast). Mesa also has hardware-specific drivers. Intel supports Mesa and has built a number of drivers for their chipsets which are shipped inside Mesa. The radeon and nouveau drivers are also supported in Mesa, but are built on a different architecture: gallium.

##### GLES
OpenGL has several profiles for different form factors. GLES is one of them, standing for “GL Embedded System” or “GL Embedded Subset”, depending on who you ask. It’s the latest approach to target the embedded market. The iPhone supports GLES 2.0.

<br>

##### GLX
OpenGL has no concept of platform and window systems on its own. Thus, bindings are needed to translate between the differences of OpenGL and something like X11. For instance, putting an OpenGL scene inside an X11 window. GLX is this glue.

<br>

##### WGL
See above, but replace “X11” with “Windows”, that is, that one Microsoft Operating System.

<br>

##### EGL
EGL and GLES are often confused. EGL is a new platform-agnostic API, developed by Khronos Group (the same group that develops and standardizes OpenGL) that provides facilities to get an OpenGL scene up and running on a platform. Like OpenGL, this is vendor-implemented; it’s an alternative to bindings like WGL/GLX and not just a library on top of them, like GLUT.

<br>

##### fglrx
fglrx is former name for AMD’s proprietary Xorg OpenGL driver, now known as “Catalyst”. It stands for “FireGL and Radeon for X”. Since it is a proprietary driver, it has its own implementation of libGL.so. I do not know if it is based on Mesa. I’m only mentioning it because it’s sometimes confused with generic technology like AIGLX or GLX, due to the appearance of the letters “GL” and “X”.

<br>

##### DIX, DDX
The X graphics parts of Xorg is made up of two major parts, DIX, the “Driver Independent X” subsystem, and DDX, the “Driver Dependent X” subsystem. When we talk about an Xorg driver, the more technically accurate term is a DDX driver.

<br>

##### Xorg Drivers, DRM, DRI
A little while back I mentioned that Xorg has the ability to do accelerated rendering, based on specific pieces of hardware. I’ll also say that this is not implemented by translating from X11 drawing commands into OpenGL calls. If the drivers are implemented in Mesa land, how can this work without making Xorg dependent on Mesa?

The answer was to create a new piece of infrastructure to be shared between Mesa and Xorg. Mesa implements the OpenGL parts, Xorg implements the X11 drawing parts, and they both convert to a set of card-specific commands. These commands are then uploaded to the kernel, using something called the “Direct Rendering Manager”, or DRM. libdrm uses a set of generic, private ioctls with the kernel to allocate things on the card, and stuffs the commands and textures and things it needs in there. This ioctl interface comes in two forms: Intel’s GEM, and Tungsten Graphics’s TTM. There is no good distinction between them; they both do the same thing, they’re just different competing implementation. Historically, GEM was designed and proudly announced to be a simpler alternative to TTM, but over time, it has quietly grown to about the same complexity as TTM. Welp.

This means that when you run something like glxgears, it loads Mesa. Mesa itself loads libdrm, and that talks to the kernel driver directly using GEM/TTM. Yes, glxgears talks to the kernel driver directly to show you some spinning gears, and sternly remind you of the benchmarking contents of said utility.

If you poke in ls /usr/lib64/libdrm_*, you’ll note that there are hardware-specific drivers. For cases when GEM/TTM aren’t enough, the Mesa and X server drivers will have a set of private ioctls to talk to the kernel, which are encapsulated in here. libdrm itself doesn’t actually load these.

The X server needs to know what’s happening here, though, so it can do things like synchronization. This synchronization between your glxgears, the kernel, and the X server is called DRI, or more accurately, DRI2. “DRI” stands for “Direct Rendering Infrastructure”, but it’s sort of a strange acronym. “DRI” refers to both the project that glued Mesa and Xorg together (introducing DRM and a bunch of the things I talk about in this article), as well as the DRI protocol and library. DRI 1 wasn’t really that good, so we threw it out and replaced it with DRI 2.

<br>

##### KMS

As a sort of aside, let’s say you’re working on a new X server, or maybe you want to show graphics on a VT without using an X server. How do you do it? You have to configure the actual hardware to be able to put up graphics. Inside of libdrm and the kernel, there’s a special subsystem that does exactly that, called KMS, which stands for “Kernel Mode Setting”. Again, through a set of ioctls, it’s possible to set up a graphics mode, map a framebuffer, and so on, to display directly on a TTY. Before, there were (and still are) hardware-specific ioctls, so a shared library called libkms was created to give a shared API. Eventually, there was a new API at the kernel level, literally called the “dumb ioctls”. With the new dumb ioctls in place, it is recommended to use those and not libkms.

While it’s very low-level, it’s entirely possible to do. Plymouth, the boot splash screen integrated into modern distributions, is a good example of a very simple application that does this to set up graphics without relying on an X server.

<br>

##### Wayland

As you can see, we’ve split out quite a large bit of the original infrastructure from X’s initial monolithic behavior. This isn’t the only place where we’ve tore down the monolithic parts of X: a lot of the input device handling has moved into the kernel with evdev, and things like device hotplug support has been moved back into udev.

A large reason that the X Window System stuck around for now was just because it was a lot of effort to replace it. With Xorg stripped down from what it initially was, and with a large amount of functionality required for a modern desktop environment provided solely by extensions, well, how can I say this, X is overdue.

Enter Wayland. Wayland reuses a lot of existing infrastructure that we’ve built up. One of the most controversial things about it is that it lacks any sort of network transparency or drawing protocol. X’s network transparency falls flat in modern times. A large amount of Linux features are hosted in places like DBus, not X, and it’s a shame to see things like Drag and Drop and Clipboard support being by and large hacks with the X Window System solely for network support.

Wayland can use almost the entire stack as detailed above to get a framebuffer on your monitor up and running. Wayland still has a protocol, but it’s based around UNIX sockets and local resources. The biggest drastic change is that there is no /usr/bin/wayland binary running like there is a /usr/bin/Xorg. Instead, Wayland follows the modern desktop’s advice and moves all of this into the window manager process instead. These window managers, more accurately called “compositors” in Wayland terms, are actually in charge of pulling events from the kernel with a system like evdev, setting up a frame buffer using KMS and DRM, and displaying windows on the screen with whatever drawing stack they want, including OpenGL. While this may sound like a lot of code, since these subsystems have moved elsewhere, code to do all of these things would probably be on the order of 2000-3000 SLOC. Consider that the portion of mutter just to implement a sane window focus and stacking policy and synchronize it with the X server is around 4000-5000 SLOC, and maybe you’ll understand my fascination a bit more.

While Wayland does have a library that implementations of both clients and compositors probably should use, it’s simply a reference implementation of the specified Wayland protocol. Somebody could write a Wayland compositor entirely in Python or Ruby and implement the protocol in pure Python, without the help of a libwayland.

Wayland clients talk to the compositor and request a buffer. The compositor will hand them back a buffer that they can draw into, using OpenGL, cairo, or whatever. The compositor is at the discretion to do whatever it wants with that buffer – display it normally because it’s awesome, set it on fire because the application is being annoying, or spin it on a cube because we need more YouTube videos of Linux cube spinning.

The compositor is also in control of input and event handling. If you tried out the thing with setting the scale of the windows in GNOME Shell above, you may have been confused at first, and then figured out that your mouse corresponded to the untransformed window. This is because we weren’t *actually* affecting the X11 window itself, just changing how it gets displayed. The X server keeps track of where windows are, and it’s up to the compositing window manager to display them where the X server thinks it is, otherwise, confusion happens.

Since a Wayland compositor is in charge of reading from evdev and giving windows events, it probably has a much better idea of where a window is, and can do the transformations internally, meaning that not only can we spin windows on a cube temporarily, we will be able to interact with windows on a cube.