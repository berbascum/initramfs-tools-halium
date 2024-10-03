# initramfs-tools-halium

Hooks and configuration to build a Halium initramfs

## fork info
This branch is a droidian upstream temporal fork by Berbascum.

There are two reasons for forking it:

### Fork reason
Analizing the current situation

1-Building initram: from a droidian device running snapshot 99.
Some packages are required: unl0kr

2- Plymouth issue: Upstream currently is building a stretch based initram due to compatibility with some legacy devices. Since stretch is not compatible with some droidian stuff, a mixed environment (bookworm and stretch) was implemented as droidian minienv.
But there is an issue with plymouth: Since reverted to stretch and implemented the minienv, the print messages on plymouth are not showed anymore.

3- Build issue: Currently cannot debug it properly, since the build script is not creating a full initram image (the resulting image has 7MB instead 15MB). Not aparent errors, so debug is needed.
TODO: Try building deb package to use build deps and rules?

4- Bookworm based: Using a Droidian bookworm based initram (builded on 2023 February) and applying the missing halium script patches from upstream gets working the plymouth messages again.

5- Adding hooks: Because the build issue (3), to add new packages from a initram-tools hook, i'm adding the hook in build-initram.sh and building. Since the resulting image is not bootable (because the build issue 3), i'm extracting the image and getting the new files added using git diff.

TODO: Determine if the plymouth issue is a stretch plymouth version or a minienv problem.

## Build an initramfs image

Building your own initramfs image wtih the tools in this repository is simple.

Requirements:

* Any OS with `debootstrap`
* `sudo` rights on the machine, to create the chroot

1. Clone this repository into your home folder
1. Install the prerequisites: `sudo apt install debootstrap qemu-user-static binfmt-support dpkg-dev`
1. `cd` into the repository
1. Run `sudo ./build-initrd.sh -a [ARCH]`

The initrd will be saved as `./out/initrd.img-touch-$ARCH` by default.

## Command-line / Environment options

`-a|--arch / ARCH=` The architecture to build an initrd for. Can be any architecture supported by Debian. Default `armhf`.

`-m|--mirror / MIRROR=` Mirror to pass to debootstrap. Default `http://deb.debian.org/debian`.

`RELEASE=` Debian release to use for building this initrd. Default `stable`.

`ROOT=` Location to place build chroot. Default `./build/$ARCH`.

`OUT=` Location to copy finished initrd to. Default `./out`.

`INCHROOTPACKAGES=` Packages to install in the chroot. These are installed in addition to the `minbase` packages specified by debootstrap. Default `initramfs-tools dctrl-tools e2fsprogs libc6-dev zlib1g-dev libssl-dev busybox-static`

## FAQ

*I'm getting a strange error when I try to build*

Try deleting your chroots (normally in the `build/` directory) and building again.

*I can't delete my chroots! They say that something is busy!*

Just run `umount build/*/*` to unmount anything that's mounted. If that doesn't work, reboot your computer. The mounts should be gone after that. Then you can delete the chroots.
