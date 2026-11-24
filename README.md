# 🏠 Personal Homelab

A small homelab where I learn Linux administration by running real services on real machines, and write down what I learn.

Each service has its own folder in [`projects/`](./projects). This README is the big picture.

---

## Project Overview

Two Linux machines on my home network:

- **Main workstation:** where I study and manage everything.
- **Server:** runs the services.

For every project I set it up, test it, fix what breaks, and document it.

---

## Goals & Learning Objectives

- Learn Linux system administration by doing.
- Understand networking basics (IPs, ports, SSH, HTTP).
- Run and troubleshoot self-hosted services.
- Understand permissions, ACLs, and SELinux.
- Document everything so it can be repeated.

---

## Infrastructure

| Role | OS | Device | CPU | RAM |
| ---- | -- | ------ | --- | --- |
| Main Workstation | EndeavourOS | Dell Precision 7730 | Intel Core i5-8400H | 15.37 GiB |
| Homelab Server | Fedora Server 45 | Presario CQ58 Notebook | AMD E-300 | 5.38 GiB |

Using two machines keeps things like a real setup: the server runs services, and the workstation is where I manage them.

---

## Current Projects

| Project | What it does | Status | Docs |
| ------- | ------------ | ------ | ---- |
| **Syncthing** | Syncs files between both machines, with no cloud involved | Working | [`projects/syncthing`](./projects/syncthing) |
| **Jellyfin** | Media server on Fedora, watched from the workstation's browser | Working | [`projects/jellyfin`](./projects/jellyfin) |

---

## How I Approach Each Project

1. Set a goal.
2. Install and configure step by step.
3. Test it and see how it breaks.
4. Troubleshoot with logs and commands.
5. Write down the steps, problems, and lessons.

---

## Why Build a Homelab?

- Real problems teach faster than tutorials.
- Mistakes are cheap here.
- I control my own data and services.
- It's practical experience for a Linux SysAdmin path.

---

## Future Roadmap

- [ ] Backups (and testing restores)
- [ ] HTTPS for Jellyfin
- [ ] Syncthing experiments: conflicts, versioning, network interruptions
- [ ] Server hardening (SSH keys, firewalld, SELinux)
- [ ] Monitoring
- [ ] Automation with Bash, then Ansible
- [ ] More services

---

## Repository Structure

```text
homelab/
├── README.md
└── projects/
    ├── syncthing/
    └── jellyfin/
```

---

*Personal learning project. Commands may need adjusting for other systems.*
