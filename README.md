# Basix-OS
Basix is a KVM based minimal linux operating system, built on linux kernel version 5.18.11 and BusyBox version 1.36.1. 

![Basix](https://github.com/Arjun4522/Basix-OS/assets/94633408/530a7911-53f7-4040-be4e-99cd2a9bf486)

## Usage 
1. `sudo apt update`
2. `sudo apt install qemu-system`
3. `git clone https://github.com/Arjun4522/Basix-OS/`
4. `cd Basix-OS`
5. `gzip -d Basix.iso.gz`
6. `chmod +x Basix.iso`
7. `qemu-system-x86_64 -cdrom Basix.iso`




