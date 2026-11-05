*This project has been created as part of the 42 curriculum by Lucas-pin.*

# Born2beroot

## Description

Born2beroot is a 42 project focused on system administration, virtualization and server security. The goal is to create and configure a secure Debian virtual machine, apply strict user and password policies, configure remote access and a firewall, and automate system monitoring.

The project develops practical knowledge of Linux administration: partitioning, privilege management, SSH hardening, scheduled tasks, service configuration and security auditing.

## Project description

### Operating system choice: Debian

I chose **Debian** because it is stable, lightweight and well documented. Its package management through `apt` is mature, predictable and widely used in server environments. Debian also has a large community and makes it easy to reproduce the installation and administration steps.

The main disadvantage is that Debian often provides older, more conservative package versions than a rolling or enterprise-oriented distribution. Some configuration choices also require more manual work than a distribution with stronger default automation.

**Rocky Linux** would be a good alternative when compatibility with the Red Hat Enterprise Linux ecosystem, enterprise tooling and long-term commercial support are priorities. Its disadvantages for this project are a somewhat heavier default environment and a package-management workflow based on `dnf`/RPM that differs from the Debian ecosystem.

### Main setup decisions

- **Virtual machine:** the server is installed in VirtualBox, using a dynamically allocated virtual disk and a minimal Debian installation.
- **Partitioning:** the disk is organized with encrypted LVM, separating the system areas and swap so that storage can be managed safely and resized when necessary.
- **Security:** SSH is configured on the required non-default port, root remote login is disabled, and only authorized users can administer the machine.
- **Password policy:** password length, expiration, history and failed-login protections are enforced with PAM and `libpam-pwquality`/`libpam-faillock`-style controls available in the selected Debian setup.
- **User management:** the root account is protected, a regular user is created for daily work, and administrative access is granted through the `sudo` group. The required 42 user/group conventions are preserved.
- **Services:** OpenSSH provides remote administration, `cron`/`crond` runs scheduled monitoring, and UFW controls network access. Unnecessary services are not enabled.
- **Monitoring:** a scheduled shell script reports CPU load, memory, disk usage, processor information, last boot, LVM status, active connections, users, network addresses and sudo commands.

### Security technology comparisons

| Choice | Selected option | Alternative | Reason for the selected option |
|---|---|---|---|
| Mandatory access control | **AppArmor** | SELinux | AppArmor uses readable path-based profiles and integrates naturally with Debian, making it easier to learn and audit in this project. SELinux provides more granular label-based controls but has a steeper learning curve and more complex policy administration. |
| Firewall | **UFW** | firewalld | UFW offers a simple command-line interface over `iptables`/`nftables`, which is appropriate for a single Debian server. firewalld is more dynamic and powerful for zones and enterprise environments, but adds concepts that are unnecessary for this setup. |
| Virtualization | **VirtualBox** | UTM | VirtualBox is cross-platform, widely documented and integrates well with the 42 evaluation workflow. UTM is an excellent option on macOS, especially for Apple Silicon and emulation, but its workflow and performance characteristics depend more strongly on the host architecture. |

## Instructions

### Requirements

- VirtualBox
- A Debian ISO compatible with the host architecture
- At least 8 GB of free disk space and enough RAM for the virtual machine
- A terminal and an SSH client on the host

### Installation and execution

1. Create a new VirtualBox virtual machine and install Debian without a graphical desktop environment.
2. Configure encrypted LVM partitions during installation.
3. Set the hostname, create the regular user and configure the required groups.
4. Install and configure `sudo`, `openssh-server`, `ufw`, `apparmor`, `apparmor-utils`, `cron` and the password-policy packages.
5. Apply the SSH, PAM, sudo and firewall rules described in the project requirements.
6. Enable the monitoring script through `cron`.
7. Start the virtual machine and connect from the host with:

```bash
ssh <username>@<vm-ip> -p 4242
```

To inspect the main configuration and validate the machine:

```bash
sudo ufw status verbose
sudo aa-status
sudo systemctl status ssh
sudo systemctl status cron
sudo crontab -u root -l
lsblk
sudo vgs
sudo lvs
```

The exact IP address can be found inside the VM with `hostname -I` or `ip addr`.

## Evaluation checklist

- The VM boots without a graphical environment.
- SSH is available on port `4242` and root login over SSH is disabled.
- UFW is active and only the required port is open.
- AppArmor is enabled and profiles are loaded.
- The password and sudo policies are applied.
- The regular user belongs to the required administrative groups.
- The monitoring script runs automatically through cron.
- LVM and disk encryption are correctly configured.

## Resources

- [Debian Administrator's Handbook](https://www.debian.org/doc/manuals/debian-handbook/)
- [Debian Reference](https://www.debian.org/doc/manuals/debian-reference/)
- [OpenSSH manual](https://man.openbsd.org/ssh)
- [UFW documentation](https://help.ubuntu.com/community/UFW)
- [AppArmor documentation](https://apparmor.net/)
- [Linux PAM documentation](https://www.linux-pam.org/)
- [VirtualBox User Manual](https://www.virtualbox.org/manual/UserManual.html)
- [42 Born2beroot subject](https://cdn.intra.42.fr/pdf/pdf/108528/en.subject.pdf)

### Use of AI

AI was used as a documentation and learning assistant: to organize this README, compare Debian with Rocky Linux, summarize AppArmor/SELinux, UFW/firewalld and VirtualBox/UTM, and improve the clarity of installation and validation instructions. The VM configuration, commands, security decisions and final verification were reviewed and carried out by the project author; AI was not used as a replacement for understanding or validating the system.

## Author

- [Lucas-pin](https://github.com/Lucas-pin)
