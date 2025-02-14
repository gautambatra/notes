# Windowing System
* A GUI that implements the WIMP (windows, icons, menus, pointer) paradigm. E.g., Xorg. 
* It is the low-level software suite that allows GUI elements like windows to exist and draws them on the screen. 
* Enables several applications to run at the same time, each in its own window.
* Provides an abstraction of the graphics hardware for use by higher-level elements such as the window manager.
* Handles low level events e.g., interacting with mouse, drawing and moving windows, etc.
* Draws windows as per the programmer's specification.
* Actual UI is handled by individual programs, the windowing system just provides the framework.

## Components (bottom up)
* Display server protocol: 
    * The communication protocol used between the display server and client. 
    * May be network transparent. 
    * E.g., X11 display server protocol, Wayland display server protocol
    * To check which one you're using: 
        * `echo $XDG_SESSION_TYPE`
        * `loginctl show-session $(loginctl show-user $(whoami) -p Display --value) -p Type --value`
* Display server/window server: 
    * Main component of the windowing system. 
    * Its clients are applications that run their GUI in windows. 
    * Coordinates the input and output of its clients to and from the rest of the OS, the hardware, and each other.
    * Display servers that implement the wayland protocol are called *Wayland compositors* because they also perform the task of a compositing window manager.
    * X11 Examples: X.Org Server, XFree86, XQuartx, Cygwin/X
    * Wayland Examples: Weston, Muttor, KWin, Enlightenment
* Display manager/login manager: 
    * Manages the display server and handles graphical user logins. 
    * Starts session on the display server and then displays the login screen. 
    * E.g., gdm, kdm, xdm, slim, qingy. 
    * To check which one you're using: 
        * `systemctl --property=Id,Description show display-manager.service`
        * `cat /etc/systemd/system/display-manager.service`
* Widget/GUI toolkit/framework/library: Provides buttons, scrollbars, edit boxes, etc. E.g., GTK+, QT
* Window manager: 
     * Controls placement, decoration, sizing of windows. Some are standalone, other depend on the desktop environment. 
     * E.g., WindowMaker, sawfish, fvwm, metacity, twm
     * To check which one you're using: `wmctrl -m` (may need to install wmctrl)
* Desktop Environment: 
    * Suites of applications that provide a consistent experience. 
    * Allows different aplications to work well together. 
    * E.g., XFCE, KDE, GNOME.
    * To check which one you're using: `echo $XDG_CURRENT_DESKTOP`

## X Window System
* aka X11
* Released for Unix by MIT in 1984
* No major release after version 11 (1987). Minor releases have been there.
* Default for many decades
* Managed by the X protocol (client/server model)
* X Server implementations: XFree86 (common default till 2004), X.org (current most common default)
* Client libraries implementing the X11 display server protocol: Xlib and XCB.
* The jobs of drawing of windows and interface elements is shared between the X server and the Window Manager. That code isn't part of the client applciation.
* Window manager is a special type of client.
* X server manages resources, executes drawing requests, and sends events like key presses and mouse clicks to the server. It also acts as the broker between the Window Manager and the applications.


## Wayland
* Server and window manager are merged into one application
* Code for functions now performed by the kernel is removed. Kernel functionality is leveraged.
* Compositing: The process of bringing together all application windows for display on an output.
* Compositor and client application communicate directly.
    * Compositor: keeps track of which windows are where, and brokers keyboard, mouse, and window change events and sends them to applications that own those windows.
    * Rendering is performed inside the applications according to the events received from the compositor. Linked Wayland libraries are used for this.
    * The application updates the buffer used to hold the window, or creates a new one and discards the old one. It then sends the notification to the compositor telling it to update the window mapping and use the new or updated buffer.
* Reduced messaging as compared to X11, helping performance.
* XWayland: a compatibility layer (middleware) that allows X11 applications to run under Wayland. Majority of X11 applications haven't been ported to native Wayland.


## References
* [Wikipedia - Windowing system](https://en.wikipedia.org/wiki/Windowing_system)
* [archlinux wiki - Wayland](https://wiki.archlinux.org/title/Wayland#Xwayland)
* [How-To Geek - What Is Wayland on Linux, and How Is It Different From X?](https://www.howtogeek.com/900698/what-is-wayland-on-linux-and-how-is-it-different-from-x/)
* [StackExchange - Windows Managers vs Login Managers Vs Display Managers Vs Desktop Environment](https://unix.stackexchange.com/questions/20385/windows-managers-vs-login-managers-vs-display-managers-vs-desktop-environment)
