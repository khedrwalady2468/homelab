# Jellyfin – Self-Hosted Media Server

A homelab project deploying **Jellyfin** on a Fedora Server and accessing it from an EndeavourOS client over a local network.

## Project Overview

The goal was to build a personal media server, understand the client-server model, and practice Linux administration and network troubleshooting.

| Component | Role |
|---|---|
| Fedora Server | Hosts Jellyfin and stores media |
| EndeavourOS | Client used to access Jellyfin through a web browser |
| Local network | Connects the server and client |
| Jellyfin | Streams and manages personal media |

## Architecture

```text
                  Local Network
        HTTP – TCP port 8096
+----------------------+       +----------------------+
| EndeavourOS          | <---> | Fedora Server        |
| Client               |       | Jellyfin Server      |
| Web browser          |       | Media storage        |
+----------------------+       +----------------------+
```

Open Jellyfin from a client browser at:

```text
http://<SERVER_IP>:8096/web/
```

Replace `<SERVER_IP>` with the Fedora server's address on your network.

## Main Features Explored

- **Media streaming:** Access media stored on the server from another device.
- **Direct Play:** Play compatible media without converting the original file.
- **Transcoding:** Convert media when the client cannot directly play the source format.
- **Centralized library:** Organize movies, music, and photos in one place.
- **Self-hosting:** Keep control of the server, media, and access to the service.

## What I Practiced

- Client-server architecture.
- LAN communication, IP addressing, and TCP ports.
- Installing and managing a Linux service with `systemd`.
- Linux file permissions and Access Control Lists (ACLs).
- Investigating SELinux-related access restrictions.
- Troubleshooting access between two Linux machines.

## Screenshots

Add your screenshots to the `screenshots/` directory and update the filenames below.

| Server | Client |
|---|---|
| ![Jellyfin server](screenshots/server-dashboard.png) | ![Jellyfin client](screenshots/client-library.png) |

## Documentation

See [jellyfin-setup.md](jellyfin-setup.md) for the project workflow, configuration notes, troubleshooting, and lessons learned.

## Why This Project Matters

Running a service is only one part of system administration. The more valuable work is understanding how clients communicate with servers, how services access files, and how Linux security controls affect real applications.

This project provided hands-on experience with those concepts in a small, practical infrastructure environment.

## Notes

- Jellyfin's default HTTP port is `8096`.
- The example URL uses HTTP on a trusted local network. For access beyond the LAN, use a properly secured remote-access setup, preferably with HTTPS.
- Do not publish private media, passwords, tokens, or sensitive network details in screenshots.

## Technologies

`Fedora Server` · `EndeavourOS` · `Jellyfin` · `Linux` · `systemd` · `ACL` · `SELinux` · `TCP/IP`

