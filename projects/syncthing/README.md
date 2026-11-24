# 🏠 Personal Homelab

A hands-on homelab project built to learn and practice Linux system administration, networking, self-hosted services, and infrastructure management.

The goal is to build a practical environment, experiment with real-world technologies, document what I learn, and understand how systems work rather than simply following tutorials.

## 🖥️ Infrastructure

The lab currently consists of two Linux machines connected over a local network.

### Main Workstation

| Component       | Specification       |
| --------------- | ------------------- |
| OS              | EndeavourOS         |
| Device          | Dell Precision 7730 |
| CPU             | Intel Core i5-8400H |
| RAM             | 15.37 GiB           |
| Root filesystem | Btrfs               |
| LAN IP          | `192.168.1.25`      |

### Homelab Server

| Component  | Specification    |
| ---------- | ---------------- |
| OS         | Fedora Server 45 |
| CPU        | AMD E-300        |
| RAM        | 5.38 GiB         |
| Filesystem | XFS              |
| LAN IP     | `10.42.0.109`    |

## 🧩 Current Projects

### 1. Private File Synchronization — Syncthing

**Status:** Working

Using [Syncthing](https://syncthing.net/) to synchronize files between the main workstation and the Fedora server without relying on third-party cloud storage.

**Version:** 2.1.7

#### Architecture

- Peer-to-peer communication between both machines.
- Both devices configured as Send & Receive.
- Shared folders configured on both devices.
- Local network discovery and device pairing.
- Files synchronized in both directions.

#### Experiments and Results

| Experiment                                    | Result     |
| --------------------------------------------- | ---------- |
| Synchronize a file from workstation to server | Successful |
| Synchronize a file from server to workstation | Successful |
| Modify files and observe synchronization      | Successful |
| Delete a file and observe propagation         | Successful |

#### Important Lessons

- Synchronization is not the same as backup.
- Deletions can propagate between synchronized devices.
- Device IDs identify Syncthing devices; they are not passwords or secrets.
- TLS protects device-to-device communication.
- Syncthing can transfer data directly over the local network.

#### Future Experiments

- [ ] Test simultaneous modifications on both devices.
- [ ] Investigate conflict resolution and conflict files.
- [ ] Test synchronization after a network interruption.
- [ ] Explore file versioning and recovery.
- [ ] Investigate Send Only and Receive Only folder modes.
- [ ] Monitor connections and synchronization status through the CLI.

## 🛠️ Technologies

- **Operating Systems:** EndeavourOS, Fedora Server
- **File Synchronization:** Syncthing
- **Version Control:** Git and GitHub
- **Networking:** TCP/IP, LAN, SSH
- **Linux Administration:** systemd, command-line tools

## 🎯 Learning Objectives

This homelab is an ongoing learning environment. My main objectives are:

1. Develop practical Linux system administration skills.
2. Understand networking and communication between machines.
3. Deploy and manage self-hosted services.
4. Learn service management, monitoring, and troubleshooting.
5. Practice security principles and infrastructure hardening.
6. Document experiments, failures, solutions, and lessons learned.

## 📚 Documentation

Each project and experiment will be documented with:

- Objective and expected outcome.
- System architecture and configuration.
- Commands and implementation steps.
- Tests and observations.
- Problems encountered and their solutions.
- Lessons learned.

## ⚠️ Disclaimer

This repository documents a personal learning environment. Configurations and commands are provided for educational purposes and may need adjustment for other systems.

---

**Status:** Actively building and experimenting.

**Maintainer:** Mohamed Emad
