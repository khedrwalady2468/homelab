# My First Homelab

## Overview

I'm building my first homelab using two Linux machines connected over a local network (LAN). My first project is setting up **Syncthing** to synchronize files between them.

## Machines

### Homelab Server

- **Operating system:** Fedora Linux 45 (Server Edition)
- **Hardware:** Presario CQ58 Notebook PC
- **Processor:** AMD E-300 with Radeon HD Graphics
- **Memory:** 5.38 GiB RAM
- **Filesystem:** XFS

### Main Machine

- **Operating system:** EndeavourOS
- **Hardware:** Dell Precision 7730
- **Processor:** Intel Core i5-8400H
- **Graphics:** NVIDIA Quadro P3200 Mobile and Intel UHD Graphics 630
- **Memory:** 15.37 GiB RAM
- **Root filesystem:** Btrfs

## Syncthing Setup

I connected both machines through the LAN and started Syncthing on each one.

I configured the machines as **peer-to-peer devices**, with both sides set to **Send & Receive**. This means either machine can send changes to the other and receive changes from it.

I used the main machine to add shared folders, then accepted and synchronized them on the server.

- **Syncthing version:** 2.1.7
- **Folder type:** Send & Receive
- **Devices:** Main machine and homelab server

## Tests and Results

### 1. Synchronization from the Main Machine to the Server

I added files to the shared folders on the main machine. The files appeared on the server successfully and almost immediately.

**Result:** Successful.

### 2. Synchronization from the Server to the Main Machine

I then tested the opposite direction by adding files to the shared folders on the server. The changes appeared on the main machine as expected.

**Result:** Successful.

### 3. Deletion Synchronization

I deleted a file from a synchronized folder and observed that it was also deleted from the server almost immediately.

**Result:** Deletions are synchronized between the devices too.

### 4. Transfer Speed

File synchronization felt very fast during my tests over the LAN.

**Result:** Good performance in my initial tests. I haven't recorded a measured transfer speed yet.

## Security Notes

Syncthing uses device identities based on cryptographic certificates and protects connections between devices with TLS encryption.

The device ID is used to identify a device; it should not be treated as a password or as a secret that alone protects the files.

## Current Result

The initial setup is working successfully:

- Both machines are connected through the LAN.
- Both devices can send and receive changes.
- Files added on either machine synchronize to the other.
- File deletions also synchronize.
- The shared folders shown in the Syncthing interface are up to date.

This is my first working homelab project, and a practical introduction to running a service across two Linux machines.
