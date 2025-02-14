# Hypervisor
* Thin software layer that allows multiple VMs run simultaneously and share the same physical resources
* Assigns each VM its own portion of the underlying computing power, memory and storage

# Benefits of Virtualization
* Better use of resources - if running a single app/OS, when it is not running compute sits idle
* Reduced downtime, better resiliency - Entire physical server need not be stopped/rebooted/replaced to debug or fix a problem
* Easy to maintain - less physical servers to maintain, easier to apply updates/changes to all VM's running in one physical server
* Can be used as test environments before applying changes in production
* Reduced carbon footprint

# LXC Container
* Linux Container
* Closely mimics a full virtual machine
* Requires less resources/overhead than a full-blown virtual machine
* Unlike VM, doesn't require its own kernel. Uses the host OS's kernel.
* Does not emulate hardware
* Has its own filesystem


# Docker Container
* Lightwait, portable, easy to manage
* Contains all the application prerequisites, dependencies, and requirements
* Automation scripts can handle creation, maintenance, configuration of docker containers


# LXC vs Docker Containers
* LXC - mimics VM (OS level virtualization), DC - designed to run specific applications (app level virtualization)
* LXC - can run different Linux distros, DC - typically runs same OS as host
* LXC uses slightly more resources than DC
* DC has stronger isolation and is less dependent on host kernel for security
* DC has an ecosystem - Docker Hub (repository service), Docker Engine
* DC is more portable
* LXC - suitable for running multiple services in one container, DC - suitable for running microservices, CI/CD piplelines, rapid deployments
* LXC - more complex to configure
* DC - compatible with Kubernetes, user Docker Swarm for orchestration


# References
* [LXC vs Docker: Pros and Cons Explained](https://www.virtualizationhowto.com/2024/01/lxc-vs-docker-pros-and-cons-explained/)
* [5 Benefits of Virtualization](https://www.ibm.com/think/insights/virtualization-benefits)
