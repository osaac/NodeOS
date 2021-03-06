[![Build Status](https://semaphoreapp.com/api/v1/projects/71d72807-779a-40d3-a8d4-523cd0a52eb3/356164/shields_badge.svg)](https://semaphoreapp.com/nodeos/nodeos)
[![Stories in Ready](https://badge.waffle.io/NodeOS/NodeOS.png?label=ready&title=Ready)](https://waffle.io/NodeOS/NodeOS)
# NodeOS

Lightweight operating system using [Node.js](http://nodejs.org) as userspace.

NodeOS is an operating system build entirely in Javascript and managed by
[npm](https://www.npmjs.com). Any package in npm is a NodeOS package, which at
last count was 206,609 packages. The goal of NodeOS is to provide just enough to
let npm provide the rest. Since anyone can contribute to npm, anyone can create
NodeOS packages.

This project won the spanish [9th National Free Software Championship](https://www.concursosoftwarelibre.org/1415)
and it's a current participant of its [10th edition](https://www.concursosoftwarelibre.org/1516)
and the Granada University [2nd Free Projects prize](http://osl.ugr.es/bases-de-los-premios-a-proyectos-libres-de-la-ugr)

[Roadmap](https://github.com/NodeOS/NodeOS/issues/37)

[![Join the Discussion](http://i.imgur.com/hUjSLXt.png)](https://github.com/NodeOS/NodeOS/issues)

## find us

- Web: please use [github issues](https://github.com/NodeOS/NodeOS/issues) for
  discussion
- IRC: join `#node-os` on Freenode

## introduction

NodeOS is a Node.js based operating system, built-off of the Linux kernel.
The eventual goal of NodeOS is to produce images that can be run on

- **real hardware** like desktop PCs, laptops, servers or Raspberry Pi
- **cloud providers** like Joyent, Amazon or Rackspace
- **virtual machines** like QEmu, VirtualBox, VMWare and KVM
- **PaaS providers** like Heroku or Joyent's Manta
- **container providers** like Docker or vagga

Core development is being done in layers. There could be some differences to
adjust better to each target platform, but the general structure is:

- *barebones* custom Linux kernel with an initramfs that boots to a Node.js REPL
- *initramfs* Initram environment to mount the users partition & boot the system
- *rootfs*    Read-only partition image to host Linux kernel and initramfs files
- *usersfs*   multi-user environment with the same behaviour of traditional OSes

### Booting process

All the layers are bootable, leading *barebones* to a raw naked Node.js
[REPL](http://nodejs.org/api/repl.html) prompt as PID 1. *initramfs* (and by
extension *rootfs*) lead to a Node.js REPL where `/dev` and `/proc` filesystems
are mounted. In all the cases, it will be used an initramfs as root filesystem
and all the changes will be lost when powered-off.

If a users partition is being set at boot time, it will be mounted and the
system will considerate each one of the folders there as the home folder for a
valid user on the system and will execute a `init` file in the root of each of
them. If found, `root` user will be the first to be considerated and will have
access to all the home directories, but by design it will not be possible to
elevate permissions once the system has booted.

### Hacking

If you are hacking on NodeOS as a somewhat production server, you are likely
building *usersfs* images since each user is isolated of others, but you can be
able to customize all layers. For example, you could be able to modify
*initramfs* to login the users and mount their home folders from a cloud service
or craft a system without global services (no `root` user) or also dedicate a
full NodeOS instance to a single Node.js application.


# Pre-build images

Ready to use [pre-build images](https://github.com/NodeOS/NodeOS/releases) are
automatically generated after each commit in master branch that sucessfully
[pass the tests](https://semaphoreapp.com/nodeos/nodeos). To exec them, you'll
need to have [QEmu](http://wiki.qemu.org/Main_Page) installed on your system.

The *iso* can be written to a CD-R or flashed to an USB pendrive, but will only
provide the read-only rootfs and the changes will be done in memory loosing them
after reboot, so you'll need to set manually a read-write usersfs partition if
you want to persist them. On the other hand, if you want to flash it to an USB
pendrive, it's recomended to so it by using `bin/installUSB` command so it will
create automatically a read-write usersfs partition to fill the remaining space
so your changes will persist.

# Build NodeOS in three steps

NodeOS require to have first installed some build tools, on a Ubuntu based
system you can install them by executing the file `bin/install-dependencies`

1. Download the project source code and build NodeOS for QEmu:
   ```bash
   git clone git@github.com:NodeOS/NodeOS.git
   cd NodeOS
   npm install
   ```
   By default it generate some files that can be used with QEmu, compiled for
   your current architecture. You can be able to configure the build process by
   passing some environment variables. For example, to force to build for 32
   bits, use `PLATFORM=qemu_32 npm install` instead.
2. Pick some microwave pop-corn and go to see a movie. No, really, do it.
3. Exec your fresh compiled NodeOS image:
   ```bash
   npm start
   ```
   It will automatically detect what CPU architecture will need to be used on
   QEmu.

...and profit! :-D

If you encounter an error when building NodeOS, take a look at
[the wiki](https://github.com/NodeOS/NodeOS/wiki/Fixing-NodeOS-Build-Errors) or
open an [issue](https://github.com/NodeOS/NodeOS/issues).

# NodeOS on LXC containers (Docker and vagga)

Currently LXC containers support is unmaintained due to imposibility of mount
filesystems from inside them. There are some NodeOS images on Docker Hub, but
they are totally outdated. If you are interested on help or testing, you can
build them from source code.

## Quick Start

1. [Install Docker](http://docs.docker.io/en/latest/installation/)
2. One Liner
   ```bash
   sudo docker run -t -i nodeos/nodeos
   ```
   or learn how to make a [Custom Build](http://node-os.com/blog/get-involved/)

## Build from Source

*Warning*: the build process is hairy, it probably won't work the first time.
I'm working on that.

```bash
git clone git@github.com:NodeOS/NodeOS.git
cd NodeOS
PLATFORM=docker npm install
```
