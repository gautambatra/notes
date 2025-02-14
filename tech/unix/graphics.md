# Unix/Linux Graphics (with Wayland as example)
## Kernel
* Talks to the hardware, provides an abstraction over it so that it can be accessed by the userspace.
* DRM - *direct rendering manager*: tasks the GPU with work from the userspace
    * KMS - *kernel mode setting*: substystem of DRM. Enumerates displays, sets properteis like resolution.
    * Interfaces are available to userpace by files in /dev/dri e.g.,  card0 for primary node, renderD128 for render node.
* Input devices are abstracted through the *evdev* interface
    * evdev device nodes are /dev/input/event*


## Userspace
* Applications work via the device nodes provided by the kernel (such as the ones in /dev/dri, /dev/input, etc.)

### libdrm
* Userspace portion of the DRM subsystem
* Used by Wayland compositors (See notes [[windowing-system#Wayland]]) for modsetting and other DRM operations
* Not used by Wayland clients directly

### Mesa
* Provides vendor optimised implementations of OpenGL and Vulkan for Linux, and the Generic Buffer Management (GBM) library.
    * GBM is an abstraction on top of libdrm for allocating buffers on the GPU
* Used by most Wayland compositors and clients

### libinput
* Userspace abstraction of evdev
* Receives input events from kernel from various input devices, decodes them into usable form and passes them to the Wayland compositor.
    * Wayland clients don't have the special permissions to used the evdev files, so must receive the inputs through the compositor.

### udev/eudev
* Tracks appearance of new devices, configures permissions for resulting device nodes in /dev, and notifies applications about these changes.
* Used by the Wayland compositor to receive notification about new input devices and GPUs.

### xkbcommon
* Does keyboard handling
* "X keyboard" - was originally part of the Xorg server, now an independent library
* Libinput delivers keyboard events in the form of *scancodes*, xkbcommon translates them into meaningful symbols.

### pixman
* Performs pixel manipulation tasks

### libwayland
* Most commmonly used implementation of the Wayland protocol
* Written in C

# References
* https://wayland-book.com/
