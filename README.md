<h1 align="center">🏠 Personal Homelab</h1>

<p align="center">
  A Linux homelab where I learn system administration by running real services on real machines, and writing down what breaks.
</p>

<p align="center">
  <img alt="Workstation: EndeavourOS" src="https://img.shields.io/badge/workstation-EndeavourOS-7F3FBF?logo=endeavouros&logoColor=white">
  <img alt="Server: Fedora Server" src="https://img.shields.io/badge/server-Fedora%20Server-51A2DA?logo=fedora&logoColor=white">
  <img alt="Syncthing" src="https://img.shields.io/badge/Syncthing-running-0891B2?logo=syncthing&logoColor=white">
  <img alt="Jellyfin" src="https://img.shields.io/badge/Jellyfin-running-00A4DC?logo=jellyfin&logoColor=white">
</p>

Each service has its own folder in [`projects/`](./projects). This README is the big picture.

## Contents

- [Overview](#overview)
- [Goals](#goals)
- [Infrastructure](#infrastructure)
- [Current projects](#current-projects)
- [Remote access (Tailscale)](#remote-access-tailscale)
- [How I approach each project](#how-i-approach-each-project)
- [Roadmap](#roadmap)
- [Repository structure](#repository-structure)

## Overview

Two Linux machines on my home network:

- **Main workstation:** where I study and manage everything.
- **Server:** runs the services.

For every project I set it up, test it, fix what breaks, and document it. Real problems teach faster than tutorials, mistakes are cheap here, and I control my own data.

## Goals

- Learn Linux system administration by doing.
- Understand networking basics (IPs, ports, SSH, HTTP).
- Run and troubleshoot self-hosted services.
- Understand permissions, ACLs, and SELinux.
- Document everything so it can be repeated.

## Infrastructure

| Role | OS | Device | CPU | RAM |
| ---- | -- | ------ | --- | --- |
| Main Workstation | EndeavourOS | Dell Precision 7730 | Intel Core i5-8400H | 15.37 GiB |
| Homelab Server | Fedora Server 45 | Presario CQ58 Notebook | AMD E-300 | 5.38 GiB |

Using two machines keeps things like a real setup: the server runs services, and the workstation is where I manage them.

## Current projects

| Project | What it does | Status | Docs |
| ------- | ------------ | ------ | ---- |
| **Syncthing** | Syncs files between both machines, with no cloud involved | Working | [`projects/syncthing`](./projects/syncthing) |
| **Jellyfin** | Media server on Fedora, watched from the workstation's browser | Working | [`projects/jellyfin`](./projects/jellyfin) |

Each project folder also has a `troubleshooting.md` for problems I hit and how I fixed them.

## Remote access (Tailscale)

To reach the server from outside the home network without opening ports on the router, I use [Tailscale](https://tailscale.com), a mesh VPN built on WireGuard.

1. Install the Tailscale package for the distro.
2. Run `sudo tailscale up` and sign in.
3. Check the connection with `tailscale status`.
4. Connect over the tailnet, for example with SSH.

Access rules (ACLs and tags) are managed in the Tailscale admin console. For safety, this repository never publishes IP addresses, hostnames, or auth keys.

## How I approach each project

1. Set a goal.
2. Install and configure step by step.
3. Test it and see how it breaks.
4. Troubleshoot with logs and commands.
5. Write down the steps, problems, and lessons.

## Roadmap

In the order I plan to do them:

- [ ] 1. Backups, and testing restores
- [ ] 2. Server hardening: SSH keys, firewalld, SELinux
- [ ] 3. HTTPS for Jellyfin
- [ ] 4. Syncthing experiments: conflicts, versioning, network interruptions
- [ ] 5. Monitoring
- [ ] 6. Automation with Bash, then Ansible
- [ ] 7. More services

## Repository structure

```text
homelab/
├── README.md
└── projects/
    ├── syncthing/
    │   └── troubleshooting.md
    └── jellyfin/
        └── troubleshooting.md
```

---

*Personal learning project. Commands may need adjusting for other systems.*
