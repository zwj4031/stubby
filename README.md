The Stubby UEFI Bootloader
===============================================================================
Contact: pmoore2@cisco.com or paul@paul-moore.com

The stubby bootloader is a simple UEFI stub that can be combined with a Linux
Kernel, initrd, and kernel command line to create a single UEFI application
that can be booted directly from UEFI or from the UEFI shim bootloader when
running on a UEFI Secure Boot system.

The initial version of stubby was extracted from systemd under the LGPL v2.1+
license.  The systemd source repository can be found at URL below.

* https://github.com/systemd/systemd

## Building and Using Stubby

As stubby is intentionally a small and simple tool it has a very limited number
of dependencies.  Beyond a functional compiler and make tool, you will need
the gnu-efi tools/libraries installed on your build system; thankfully most
Linux distributions package the gnu-efi project, but if you need to install it
you can find it at the URL below.

* https://sourceforge.net/projects/gnu-efi

If all of the dependencies are installed, you should be able to build stubby
simply by running `make`.  The build process can be configured via the
"make.conf" file.

```
% make
cc -Wall ... -c -o util.o util.c
cc -Wall ... -c -o disk.o disk.c
cc -Wall ... -c -o pe.o pe.c
cc -Wall ... -c -o linux.o linux.c
cc -Wall ... -c -o stub.o stub.c
ld ... -o stubby.so -lefi -lgnuefi
objcopy -j .text -j .sdata -j .data -j .dynamic \
        -j .dynsym  -j .rel -j .rela -j .reloc \
        --target=efi-app-x86_64 stubby.so stubby.efi
% ls -l stubby.efi
-rwxr-xr-x 1 user users 56K Oct 30 13:48 stubby.efi*
```

Once you have successfully built "stubby.efi" you can combine it with your
Linux Kernel, initrd, and command line using the `stubby-smash` script.

```
% ./stubby-smash 
usage: stubby-smash <kernel> <initrd> <cmdline> <output>

  Combine the <kernel>, <initrd>, and <cmdline> files into a
  single bootable EFI application in <output>.
```

The resulting output artifact is suitable for booting directly from UEFI and
can optionally be signed using `sbsign` or `pesign` for use on a UEFI Secure
Boot system.

## Bug and Vulnerability Reporting

Problems with the project can be reported using the GitHub issue tracking
system.  Those who wish to privately report potential vulnerabilities should
follow the directions in the SECURITY file.

