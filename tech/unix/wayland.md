# Wayland
* `sudo apt-get install libwayland-dev`
* wayland.xml: defines the wayland protocols
* wayland-scanner: tool to process the xml file and generate code
* libwayland-client, libwayland-server : implementation of the protocol


## Wire protocol
* Stream of 32-bit *messages*
* Each message is an *event* (server to client) or a *request* (client to servert)
* Each message acts on an *object* which has an *object id*. Has a header, containing the size of the message and the opcode.
* Each object has an *interface* which defines what events and requests are possible, and their signatures.
* Interfaces supported by a Wayland client or server are defined in wayland.xml
* Object ID 1 is pre-allocated as the Wayland display singleton
* Client allocates IDs in the range of `[1, 0xFEFFFFFF]`, and the server allocates IDs in the range of `[0xFF000000, 0xFFFFFFFF]`. 
* Object ID of 0 signifies null or no object

## Wayland protocol
* Most Wayland implementations use Unix sockets as the transport layer
* For details see [this page](https://wayland-book.com/protocol-design/wire-protocol.html)
* Atomicity
    * Interfaces allow updation of state through several requests specifying changes to different properties of an object.
    * These updates are applied to a *pending state* 
    * On a *commit* request, these updates are merged into the *current state*, allowing atomic updates of all the properties in a single frame, without any partial updates/glitches.
* Wayland is a fully asynchronous protocol.

## References
* https://wayland-book.com/protocol-design/design-patterns.html
