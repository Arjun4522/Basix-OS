# Basix-OS
![Basix](https://github.com/Arjun4522/Basix-OS/assets/94633408/530a7911-53f7-4040-be4e-99cd2a9bf486)

Basix-OS is a lightweight, KVM-based minimal Linux operating system designed for learning and experimentation, targeting resource-constrained environments like IoT devices (e.g., Raspberry Pi). Built from scratch using Linux kernel 5.18.11 and BusyBox 1.36.1, it features a stripped-down filesystem, optimized boot process, and GRUB 2.04 bootloader. Developed on Ubuntu, Basix-OS is a testament to low-level systems programming, leveraging C and Bash to deliver a functional and secure OS with a minimal memory footprint.

## Table of Contents

- [Project Overview](#project-overview)
- [Features](#features)
- [System Architecture](#system-architecture)
- [Directory Structure](#directory-structure)
- [Installation](#installation)
- [Usage](#usage)
- [Building Basix-OS](#building-basix-os)
- [Contributing](#contributing)
- [License](#license)
- [Acknowledgments](#acknowledgments)

## Project Overview

Basix-OS was created as a personal project to explore the intricacies of operating system design, focusing on kernel programming, filesystem management, and virtualization. The goal was to craft a minimal yet functional Linux-based OS that could run efficiently on low-power devices, making it ideal for IoT applications. By integrating a custom-configured Linux kernel with BusyBox utilities and a GRUB bootloader, Basix-OS achieves a bootable system with essential functionality, all while maintaining simplicity and security.

## Features

- **Minimal Footprint**: Uses BusyBox 1.36.1 to provide core utilities (e.g., sh, ls, mount, mknod) in a compact filesystem.
- **Fast Boot**: Configured with GRUB 2.04 and a root.cpio.gz initramfs, achieving boot times of a few seconds on devices like Raspberry Pi.
- **KVM Virtualization**: Tested using QEMU/KVM to simulate IoT hardware, ensuring compatibility with constrained environments.
- **Custom Kernel**: Built on Linux kernel 5.18.11 with trimmed modules for efficiency and security.
- **Extensible Filesystem**: Supports essential filesystems (/proc, /sys) via a custom init script.
- **Open-Source**: Hosted on GitHub, encouraging community contributions and feedback.

## System Architecture

Basix-OS follows a minimalist design, comprising the following core components:

### Linux Kernel (5.18.11)

- Compiled as bzImage (located in boot/bzImage).
- Configured with minimal modules to support basic process scheduling, memory management, and device handling.
- Optimized for IoT devices by disabling unnecessary features.

### BusyBox (1.36.1)

- Provides a suite of lightweight utilities in /bin, /sbin, /usr/bin, and /usr/sbin.
- Key utilities include sh (default shell), mount, mknod, ls, cat, and networking tools like ifconfig and ping.
- Reduces disk and memory usage compared to full-featured alternatives.

### GRUB Bootloader (2.04)

- Configured via boot/grub/grub.cfg to load the kernel (bzImage) and initramfs (root.cpio.gz).
- Supports multiple filesystem types (e.g., ext2, iso9660) via GRUB modules in boot/grub/i386-pc.
- Includes graphical and text-based boot options.

### Initramfs (root.cpio.gz)

- Contains the initial filesystem, including the init script (init).
- The init script mounts /proc and /sys and launches a BusyBox shell (/bin/sh).
- Minimal device nodes (e.g., /dev/ttyS0) can be created dynamically using mknod.

### ISO Image (Basix.iso)

- A bootable ISO image generated with boot.catalog and [BOOT]/Boot-NoEmul.img.
- Designed for use with QEMU or physical hardware.

## Directory Structure

```
arjun4522-basix-os/
├── README.md              # Project documentation
├── Basix.iso             # Bootable ISO image
├── boot.catalog          # CD boot catalog
├── init                  # Init script for mounting filesystems and starting shell
├── linuxrc               # Empty linuxrc script (optional)
├── root.cpio.gz          # Compressed initramfs
├── [BOOT]/               # Boot directory for ISO
│   └── Boot-NoEmul.img   # No-emulation boot image
├── bin/                  # BusyBox user utilities (e.g., ls, cat, sh)
├── sbin/                 # BusyBox system utilities (e.g., init, ifconfig)
├── usr/                  # Additional BusyBox utilities
│   ├── bin/              # User utilities (e.g., awk, find)
│   └── sbin/             # System utilities (e.g., adduser, crond)
└── boot/                 # Bootloader and kernel files
    ├── bzImage           # Compiled Linux kernel
    ├── root.cpio.gz      # Copy of initramfs
    └── grub/             # GRUB bootloader configuration
        ├── grub.cfg      # GRUB configuration
        ├── fonts/        # GRUB font (unicode.pf2)
        └── i386-pc/      # GRUB modules for i386-pc platform
```

## Installation

### Prerequisites

- Ubuntu (or a compatible Linux distribution)
- QEMU (qemu-system-x86_64)
- Git

### Steps

1. **Update your package list:**
   ```bash
   sudo apt update
   ```

2. **Install QEMU:**
   ```bash
   sudo apt install qemu-system
   ```

3. **Clone the Basix-OS repository:**
   ```bash
   git clone https://github.com/Arjun4522/Basix-OS/
   cd Basix-OS
   ```

4. **Ensure the ISO is executable:**
   ```bash
   chmod +x Basix.iso
   ```

## Usage

**Run Basix-OS using QEMU:**
```bash
qemu-system-x86_64 -cdrom Basix.iso
```

This command boots the ISO in QEMU, loading the GRUB menu, followed by the Linux kernel and initramfs. The init script mounts essential filesystems and starts a BusyBox shell (/bin/sh). From the shell, you can use utilities like ls, mount, or ifconfig to interact with the system.

**To test on physical hardware (e.g., Raspberry Pi):**

1. Write Basix.iso to a USB drive using dd or a tool like Etcher.
2. Boot the device with the USB drive inserted, ensuring the BIOS/UEFI is configured to boot from USB.

## Building Basix-OS

To rebuild Basix-OS from source, follow these steps:

### Download Dependencies

- Linux kernel 5.18.11: [kernel.org](https://kernel.org)
- BusyBox 1.36.1: [busybox.net](https://busybox.net)
- GRUB 2.04: [gnu.org](https://gnu.org)

### Configure and Compile the Kernel

```bash
tar -xvf linux-5.18.11.tar.xz
cd linux-5.18.11
make menuconfig  # Disable unnecessary modules
make bzImage
cp arch/x86/boot/bzImage ../Basix-OS/boot/bzImage
```

### Build BusyBox

```bash
tar -xvf busybox-1.36.1.tar.bz2
cd busybox-1.36.1
make menuconfig  # Select desired utilities
make
make install CONFIG_PREFIX=../Basix-OS
```

### Create Initramfs

1. Copy the init script to the root of the filesystem.
2. Generate root.cpio.gz:
   ```bash
   cd Basix-OS
   find . -print0 | cpio --null -ov --format=newc | gzip -9 > root.cpio.gz
   cp root.cpio.gz boot/root.cpio.gz
   ```

### Configure GRUB

- Edit boot/grub/grub.cfg to point to bzImage and root.cpio.gz.
- Ensure GRUB modules in boot/grub/i386-pc support required filesystems.

### Generate ISO

```bash
grub-mkrescue -o Basix.iso .
```

## Contributing

Contributions are welcome! To contribute:

1. Fork the repository.
2. Create a feature branch (`git checkout -b feature/your-feature`).
3. Commit changes (`git commit -m "Add your feature"`).
4. Push to the branch (`git push origin feature/your-feature`).
5. Open a pull request.

Please include tests and documentation for new features. Report issues via the GitHub Issues page.

## License

Basix-OS is licensed under the MIT License. The Linux kernel, BusyBox, and GRUB are licensed under their respective licenses (GPLv2, GPLv2, and GPLv3).

## Acknowledgments

- **Linux Kernel Community**: For the robust 5.18.11 kernel.
- **BusyBox Project**: For providing lightweight utilities.
- **GRUB Team**: For the versatile 2.04 bootloader.
- **Open-Source Community**: For invaluable resources and feedback.
