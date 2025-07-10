# Testing lab

UAC is a forensic tool designed to automate the collection of key system artifacts across a wide range of Unix-like operating systems. It is built with portability and broad compatibility in mind, making it suitable for use in environments ranging from modern enterprise systems to legacy infrastructure.

To ensure reliability, UAC is extensively tested in our lab across:

- Diverse Unix-like operating systems.

- A wide range of system versions, from legacy releases to the latest stable builds.

- Various hardware architectures, such as i386, x86_64, ARM, MIPS, PowerPC, and others.

This comprehensive testing approach ensures that UAC functions consistently across different environments, including outdated or niche systems often encountered in forensic investigations and enterprise environments.

The table below lists all OSes, versions, and architectures currently used in our testing pipeline.

⚠️ **Important Note**: The operating systems and versions listed on this page represent those actively used in our testing lab. However, UAC is not limited to these platforms. The tool is designed to run on a wide array of Unix-like systems, even if a particular version or architecture is not explicitly listed. The testing matrix is provided for transparency and quality assurance purposes only.

## aix

|Operating System|Architecture|Comments|
|---|---|---|
|AIX 7.3|powerpc||

## esxi

|Operating System|Architecture|Comments|
|---|---|---|
|ESXi 6.7|x86_64||
|ESXi 8.0|x86_64||

## freebsd

|Operating System|Architecture|Comments|
|---|---|---|
|FreeBSD 7.4|i386||
|FreeBSD 14.1|amd64||
|pfsense 2.6|amd64||

## linux

|Operating System|Architecture|Comments|
|---|---|---|
|AlmaLinux 9|x86_64||
|Alpine Linux 3.21|x86_64||
|Amazon Linux|x86_64||
|Armbian Linux|aarch64|Running on a Orange Pi 3 LTS|
|CentOS 3|i386||
|CentOS 5|i386||
|Clear Linux|x86_64||
|Debian 12|aarch64||
|Debian 12|x86_64|Running containers (Docker)|
|Debian 6|i686||
|Fedora 39|x86_64|Running Gnome Shell|
|Gentoo|x86_64||
|Kali Linux|x86_64||
|Kubuntu 24.04|x86_64|Running KDE Plasma|
|Linux 2.6.15-sigma|mips|Very limited Linux running on a set-up box using Busybox v1.00|
|Manjaro|x86_64||
|OpenWRT 23|i686||
|Oracle Linux 9|x86_64||
|Proxmox 7|x86_64|Running VMs|
|Proxmox 8|x86_64|Running containers (LXC)|
|QNAP QTS 5|x86_64||
|Red Hat 6|x86_64||
|Red Hat 9|x86_64|Running containers (Podman)|
|Slackware 15|x86_64||
|Suse Tumbleweed|x86_64||
|Synology DSM 7|x86_64||
|Ubuntu Server 20.04|x86_64|Using BusyBox v1.30.1|

## macos

|Operating System|Architecture|Comments|
|---|---|---|
|High Sierra|x86_64||
|Sonoma|arm64|Apple Silicon|
|Ventura|x86_64||

## netbsd

|Operating System|Architecture|Comments|
|---|---|---|
|NetBSD 6|i386||
|NetBSD 9.3|amd64||
|NetBSD 10.0|amd64||

## netscaler

|Operating System|Architecture|Comments|
|---|---|---|
|NetScaler 10.5|amd64||
|NetScaler 13.0|amd64||

## openbsd

|Operating System|Architecture|Comments|
|---|---|---|
|OpenBSD 7.3|amd64||

## solaris

|Operating System|Architecture|Comments|
|---|---|---|
|Solaris 10|i386||
|Solaris 11|i386||
