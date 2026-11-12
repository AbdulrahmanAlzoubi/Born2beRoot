*This project has been created as part of the 42 curriculum by abalzoub.*

# Born2beRoot

## Description

Born2beRoot is a system administration project from the 42 curriculum. Its goal is to introduce virtualization, Linux server administration, and fundamental security practices by building and configuring a secure Linux server inside a virtual machine.

The project focuses on creating a minimal server environment and applying security measures such as encrypted storage, user and permission management, SSH hardening, firewall configuration, password policies, sudo auditing, and automated system monitoring.

### Operating System Choice: Debian

Debian 13 (Trixie) was selected as the operating system for this project.

**Advantages of Debian:**
- Stable and reliable for server environments.
- Large software repository and strong package management.
- Extensive documentation and community support.
- Lightweight installation suitable for minimal servers.
- Uses APT for straightforward package management.

**Disadvantages of Debian:**
- Software versions in stable repositories may be older than those in rolling-release distributions.
- Some newer technologies may require additional repositories or manual configuration.

### Debian vs Rocky Linux

| Debian | Rocky Linux |
|---|---|
| Community-developed Linux distribution. | Enterprise-oriented Linux distribution compatible with RHEL. |
| Uses APT and dpkg. | Uses DNF and RPM. |
| Uses AppArmor by default. | Uses SELinux by default. |
| Common in web hosting and general-purpose servers. | Common in enterprise environments requiring RHEL compatibility. |

Debian was selected because of its stability, straightforward installation, and beginner-friendly administration.

### Virtual Machine Configuration

| Component | Configuration |
|---|---|
| Operating System | Debian 13 (Trixie) |
| Architecture | x86_64 |
| Hostname | abalzoub42 |
| Virtualization | VirtualBox |
| Time Zone | Asia/Amman |
| Network | NAT |
| SSH Port | 4242 |

### Storage and Partitioning

The virtual disk uses LUKS encryption with LVM to provide encrypted storage and flexible logical volume management.

The storage layout includes:

- `/boot` — Boot partition.
- `sda5_crypt` — Encrypted partition using LUKS.
- `root` — Root filesystem.
- `var` — Variable system data and logs.
- `srv` — Service data.
- `swap_1` — Swap space.

This configuration protects stored data through disk encryption and allows logical volumes to be managed independently.

### Security Configuration

#### 1. AppArmor

AppArmor is enabled and configured to start at boot.

It is a Mandatory Access Control (MAC) security system that restricts application capabilities according to security profiles.

#### 2. SSH

The SSH service is configured with the following security settings:

- Listening port: `4242`.
- Root login: Disabled.
- Remote administration through a regular user account.

#### 3. Firewall (UFW)

UFW is enabled with restrictive default policies.

| Rule | Configuration |
|---|---|
| Incoming traffic | Denied by default |
| Outgoing traffic | Allowed by default |
| Allowed incoming port | `4242/tcp` |

#### 4. Password Policy

A strong password policy is configured using PAM and `libpam-pwquality`.

The policy includes:

- Minimum password length of 10 characters.
- At least one uppercase letter.
- At least one lowercase letter.
- At least one digit.
- Maximum of 3 consecutive identical characters.
- User name checking.
- Password expiration after 30 days.
- Minimum password age of 2 days.
- Expiration warning 7 days in advance.
- At least 7 characters different from the previous password for regular users.

The root password is also subject to the required password complexity and expiration policy.

#### 5. Sudo Configuration

Sudo is configured to improve security and maintain an audit trail.

The configuration includes:

- Maximum of 3 authentication attempts.
- Custom incorrect-password message.
- Pseudo-terminal allocation using `use_pty`.
- Restricted `secure_path`.
- Input and output logging.
- Command logging in `/var/log/sudo/sudo.log`.
- I/O session logs stored in `/var/log/sudo`.

The sudo log directory is restricted to root access.

### User and Group Management

The main user account is `abalzoub`.

The account belongs to the following groups:

- `sudo` — Allows administrative commands through sudo.
- `user42` — Required project group.

Direct root access through SSH is disabled. Administrative tasks are performed through sudo.

### Monitoring Script

A Bash monitoring script is installed at:

`/usr/local/bin/monitoring.sh`

The script collects and displays:

- Operating system architecture and kernel version.
- Physical CPU and virtual CPU information.
- RAM usage and available memory.
- Disk usage and available storage.
- Current CPU utilization.
- Last system boot time.
- LVM status.
- Active TCP connections.
- Number of logged-in users.
- IPv4 address and MAC address.
- Number of sudo commands recorded in the sudo log.

The report is broadcast to logged-in terminals using `wall`.

#### Automation with Cron

Cron is configured to execute the monitoring script:

- At system startup.
- Every 10 minutes.

The script runs with root privileges to access the required system information.

## Instructions

### Virtual Machine

The project is configured inside a Debian 13 virtual machine using VirtualBox.

To run the server:

1. Open Oracle VirtualBox.
2. Select the configured Debian virtual machine.
3. Start the virtual machine.
4. Log in using the configured user account.

### SSH Access

SSH is configured to listen on port `4242`.

To connect from the host machine, use:

```bash
ssh -p 4242 abalzoub@127.0.0.1
```

### Useful Verification Commands

| Purpose | Command |
|---|---|
| System information | `uname -a` |
| CPU information | `lscpu` |
| Memory usage | `free -h` |
| Disk usage | `df -h` |
| Disk and LVM layout | `lsblk` |
| AppArmor status | `sudo aa-status` |
| SSH service status | `sudo systemctl status ssh` |
| SSH configuration | `sudo sshd -t` |
| Firewall status | `sudo ufw status verbose` |
| Password expiration | `sudo chage -l abalzoub` |
| Validate sudoers | `sudo visudo -c` |
| Sudo configuration | `sudo -l` |
| Monitoring script | `sudo /usr/local/bin/monitoring.sh` |
| Cron jobs | `sudo crontab -l` |

## Comparisons

### AppArmor vs SELinux

| AppArmor | SELinux |
|---|---|
| Uses file paths to define security rules. | Uses security labels and policies. |
| Generally simpler to configure. | Provides more detailed and granular policy control. |
| Commonly used in Debian and Ubuntu. | Commonly used in RHEL-based distributions. |

Both implement Mandatory Access Control to restrict processes and improve system security.

### UFW vs firewalld

| UFW | firewalld |
|---|---|
| Simple firewall management interface. | Dynamic firewall management solution. |
| Commonly used in Debian and Ubuntu. | Commonly used in Rocky Linux and RHEL. |
| Uses straightforward rules for ports and services. | Supports zones and runtime configuration. |

Both manage firewall rules and control incoming and outgoing network traffic.

### VirtualBox vs UTM

| VirtualBox | UTM |
|---|---|
| Cross-platform virtualization software. | Virtualization and emulation application mainly used on Apple platforms. |
| Supports Windows, Linux, and macOS hosts. | Designed primarily for macOS and iOS environments. |
| Supports a wide range of guest operating systems. | Uses Apple's virtualization framework and QEMU. |

VirtualBox was selected because it is available on the Linux host and provides the virtualization features required for this project.

## Resources

The following resources were useful for understanding and configuring the system:

- Debian Documentation: https://www.debian.org/doc/
- Debian Administrator's Handbook: https://debian-handbook.info/
- Linux Manual Pages: https://man7.org/linux/man-pages/
- OpenSSH Manual: https://www.openssh.com/manual.html
- AppArmor Documentation: https://apparmor.net/
- UFW Documentation: https://help.ubuntu.com/community/UFW
- LVM Administration Guide: https://sourceware.org/lvm2/
- Oracle VirtualBox Documentation: https://www.virtualbox.org/manual/
- GNU Bash Manual: https://www.gnu.org/software/bash/manual/

### AI Usage

AI tools were used as supplementary learning and troubleshooting resources throughout the project.

They helped with:
- Understanding Linux system administration concepts and security mechanisms.
- Explaining commands and configuration files.
- Troubleshooting configuration issues related to networking, SSH, firewall rules, and system services.
- Reviewing documentation and improving the clarity of technical explanations.

AI-generated suggestions were reviewed and tested against the actual virtual machine configuration. AI was used as a learning aid, not as a replacement for understanding or verifying the system.

## Conclusion

Born2beRoot provided practical experience in Linux system administration, virtualization, and server security.

The project demonstrates the importance of encrypted storage, access control, secure remote administration, firewall management, auditing, and automated system monitoring.
