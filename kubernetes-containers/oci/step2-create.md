A container image is a TAR of TAR files with some metadata. ([Tartar sauce](https://en.wikipedia.org/wiki/Tartar_sauce)?) Therefore, an empty image can be created using the `tar` command below.

`tar cv --files-from /dev/null | docker import - empty`{{execute}}

When importing the TAR file, the docker tool will add the required metadata.
The command creates the image and reports the image ID, and is now in the images list.

`docker images`{{execute}}

The container image is ready, but as the container doesn't contain anything, it can't start a process.

`docker run empty`{{execute}}

The container runtime appropriately reports:

> `No command specified.`

## Create Image without Dockerfile

The previous idea of importing a TAR file can be extended to create an entire image from scratch.

<img align="right" src="./assets/busybox.png" width="100">
Next, we'll use BusyBox as the base to create a functional container by just using this `tar` and `import` technique. BusyBox combines tiny versions of many common UNIX utilities into a single small executable. Install BusyBox locally.

`apt install busybox-static`{{execute}}

Docker provides [a script](https://github.com/moby/moby/blob/a575b0b1384b2ba89b79cbd7e770fbeb616758b3/contrib/mkimage/busybox-static) to download the BusyBox [rootfs](https://www.kernel.org/doc/Documentation/filesystems/ramfs-rootfs-initramfs.txt).

`curl -LO https://raw.githubusercontent.com/moby/moby/a575b0b1384b2ba89b79cbd7e770fbeb616758b3/contrib/mkimage/busybox-static && chmod +x busybox-static`{{execute}}

`./busybox-static busybox`{{execute}}

Running the script will download the rootfs and the main binaries.

`ls -lha busybox`{{execute}}

The default Busybox rootfs doesn't include any version information, so let's create a file.

`echo KatacodaPrivateBuild > busybox/release`{{execute}}

As before, the directory can be converted into a TAR and automatically imported into Docker as an image.

`tar -C busybox -c . | docker import - busybox`{{execute}}

You have created a container image.

`docker images`{{execute}}

Finally, your new container image can be used to launch your custom BusyBox container.

`docker run busybox cat /release`{{execute}}

All those [BusyBox commands](https://boxmatrix.info/wiki/BusyBox-Commands) are also available.

`docker run busybox /bin/sh -c "uname -a; env"`{{execute}}