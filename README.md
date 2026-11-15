# Kali Linux Attack–Defense Lab

## Overview
This project documents an isolated cybersecurity lab built with Kali Linux and Ubuntu Server in VirtualBox. Kali acted as the authorized testing machine; Ubuntu acted as the server being tested and hardened.

The lab demonstrates how weak SSH password authentication can be identified, monitored, and mitigated through layered defensive controls.

## Lab Architecture

| Machine | NAT Interface | Host-only Interface |
|---|---|---|
| Kali Linux | `eth0` — `10.0.2.15` | `eth1` — `192.168.56.102` |
| Ubuntu Server | `enp0s3` — `10.0.2.15` | `enp0s8` — `192.168.56.101` |

The tests used the isolated host-only network (`192.168.56.0/24`).

## Objectives
- Verify connectivity between the two virtual machines.
- Identify exposed services with Nmap.
- Demonstrate the risk of weak SSH password authentication in a controlled lab.
- Observe authentication events in Ubuntu logs.
- Configure and validate Fail2ban.
- Configure SSH public-key authentication.
- Disable SSH password authentication and verify that the tested password-based path is blocked.

## Tools
- Kali Linux and Ubuntu Server
- Oracle VirtualBox
- Nmap
- Medusa
- OpenSSH
- Fail2ban
- Linux system logs (`journalctl`)

## Method and Results

1. **Connectivity check:** Used `ping -c 4 192.168.56.101` to confirm basic reachability.
2. **Service discovery:** Used `nmap -sV 192.168.56.101` to identify services, including HTTP on TCP port 80 and SSH on TCP port 22 when enabled.
3. **SSH access:** Connected to the Ubuntu lab account using SSH.
4. **Log monitoring:** Followed SSH service logs with `sudo journalctl -u ssh -f` while generating intentional failed login attempts.
5. **Controlled password audit:** Used Medusa with a small lab-only wordlist against the disposable lab account to demonstrate the risk of weak password-based SSH.
6. **Fail2ban:** Configured the SSH jail and verified its status and temporary ban of the Kali host-only address after repeated failures.
7. **Key-based authentication:** Generated an ED25519 key pair on Kali, installed the public key on Ubuntu, and verified key-based login.
8. **SSH hardening:** Set `PasswordAuthentication no`, validated the SSH configuration, restarted the service, and checked the effective setting.
9. **Final validation:** Repeated the password-audit test; Medusa reported that no supported authentication methods were located for that password-based test.

## Defensive Controls Demonstrated

| Control | Security Contribution |
|---|---|
| SSH logs | Visibility and investigation of authentication events |
| Fail2ban | Reactive temporary blocking after repeated matching failures |
| SSH public-key authentication | Stronger authentication based on possession of a private key |
| `PasswordAuthentication no` | Prevents the tested password-authentication route |

## Evidence

Screenshots are stored in the `evidence/` folder. The study guide is in the `docs/` folder.

## Important Security Notes
- All testing was performed only against the author's isolated VirtualBox lab machines.
- The private SSH key must never be committed to GitHub.
- The final Medusa result demonstrates that the tested password-based path was unavailable; it does not prove immunity to every possible attack.
- An open port indicates a listening service, not automatically a vulnerability.

## Learning Outcomes

This lab provided practical experience with network reconnaissance, SSH authentication, Linux log monitoring, defensive response, public-key authentication, and verification of security hardening.
