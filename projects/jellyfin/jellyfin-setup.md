# Project: Deploying Jellyfin on a Fedora Homelab Server

## 1. Objective

Deploy a self-hosted media server using Jellyfin, store media on a Fedora Server, and access it from a separate EndeavourOS machine through a web browser.

The project was intended not only to get Jellyfin running, but also to understand the infrastructure behind a self-hosted service.

## 2. Environment and Architecture

- **Server:** Fedora Server
- **Client:** EndeavourOS
- **Media server:** Jellyfin
- **Connection:** Local Area Network (LAN)
- **Web interface:** HTTP on TCP port `8096`

```text
EndeavourOS (Client)
       |
       |  HTTP / TCP 8096
       |
Fedora Server (Jellyfin)
       |
       +---- Media files
```

The media remains on the Fedora server. The client requests it over the network; it does not need to copy the entire media library locally before playback.

## 3. Deployment Workflow

### Step 1 – Install Jellyfin

Jellyfin was installed on Fedora Server using the available Fedora/RPM Fusion package sources.

The important distinction is that Jellyfin runs as a background service rather than as an application that needs to be launched manually in a terminal.

### Step 2 – Manage the service with systemd

The service was managed through `systemd`:

```bash
sudo systemctl enable --now jellyfin
systemctl status jellyfin
```

Useful checks:

```bash
systemctl is-active jellyfin
sudo journalctl -u jellyfin -n 50 --no-pager
```

`enable` configures the service to start at boot, while `--now` starts it immediately.

### Step 3 – Access the web interface

From the EndeavourOS client, open:

```text
http://<SERVER_IP>:8096/web/
```

Replace `<SERVER_IP>` with the Fedora server's LAN IP address.

The `/web/` path opens Jellyfin's web interface. The server's root URL may redirect the browser there.

### Step 4 – Add a media library

In Jellyfin, create a library and select the appropriate content type, such as Movies, Music, or Photos. Then select the media directory on Fedora.

The directory must be readable and traversable by the Linux account running the Jellyfin service.

## 4. Linux Permissions: Giving Jellyfin Access to Media

One of the practical challenges was allowing the `jellyfin` service account to read media stored under:

```text
/home/homelabserver/Pictures
```

Changing permissions to `777` is not a good solution: it grants broad access to every local user and does not override SELinux restrictions.

Instead, Access Control Lists (ACLs) were used to grant the Jellyfin account the specific access it needed.

```bash
sudo setfacl -m u:jellyfin:--x /home/homelabserver
sudo setfacl -R -m u:jellyfin:rX /home/homelabserver/Pictures
```

What these permissions mean:

- `u:jellyfin` targets the `jellyfin` Linux user.
- `--x` on the home directory allows path traversal without granting permission to list the home directory.
- `rX` grants read access and directory traversal. The capital `X` applies execute permission to directories (and to files that already have execute permission).
- `-R` applies the ACL recursively to existing content.

Verify that the service account can list the directory:

```bash
sudo -u jellyfin ls -la /home/homelabserver/Pictures
```

If the command returns `Permission denied`, check every parent directory and the SELinux policy. Traditional Linux permissions and ACLs are not the only access controls on Fedora; SELinux can still deny access even when the Unix permissions appear correct.

Useful inspection commands:

```bash
namei -l /home/homelabserver/Pictures
getfacl /home/homelabserver/Pictures
ls -Zd /home/homelabserver/Pictures
getenforce
```

Do not change SELinux labels or disable SELinux blindly. First inspect the denial and choose a policy-supported label or location for the media.

## 5. Understanding Playback

Jellyfin can handle media in different ways:

- **Direct Play:** The client supports the source media format, so Jellyfin sends the original media without converting it.
- **Remux:** The media container is changed while the audio and video streams are kept, when possible.
- **Transcoding:** Jellyfin converts the media into a format or quality the client can handle. This requires server resources, especially CPU or supported hardware acceleration.

These distinctions matter because they affect server load, network usage, and playback compatibility.

## 6. Verification and Troubleshooting

Check that Jellyfin is running:

```bash
systemctl is-active jellyfin
```

Check that the service is listening on port `8096`:

```bash
sudo ss -lntp | grep 8096
```

Check recent service logs:

```bash
sudo journalctl -u jellyfin -n 50 --no-pager
```

Check whether the service account can read the media directory:

```bash
sudo -u jellyfin ls -la /home/homelabserver/Pictures
```

If the directory appears in Jellyfin but the library is empty, verify the library's content type, confirm that the directory actually contains supported media files, and run a library scan.

## 7. What I Learned

This project provided hands-on practice with:

1. **Client-server architecture:** A server hosts a service while clients access it over a network.
2. **Networking:** IP addresses, LAN connectivity, TCP ports, and HTTP.
3. **Linux service management:** Running and inspecting a persistent service with `systemd`.
4. **Linux access control:** Understanding users, directory traversal, file permissions, and ACLs.
5. **SELinux awareness:** Recognizing that Fedora's mandatory access control can deny access even when ordinary file permissions allow it.
6. **Media delivery:** Understanding Direct Play, remuxing, and transcoding.

## 8. Why Self-Hosting Is Useful

Self-hosting gives you control over where your data is stored and how services are configured. It also creates a practical environment for learning infrastructure concepts: networking, access control, service reliability, monitoring, and troubleshooting.

Jellyfin made these ideas tangible because a working media server depends on all of them—not just on installing an application.

## 9. Possible Next Improvements

- Configure HTTPS for secure access.
- Set up backups for Jellyfin configuration and media metadata.
- Monitor CPU, memory, and network usage during playback.
- Explore hardware-accelerated transcoding if the server hardware supports it.
- Restrict remote access and expose the service only through a secure method.

## 10. Screenshots

Place your screenshots in a `screenshots/` directory. Suggested filenames:

- `screenshots/server-dashboard.png`
- `screenshots/client-library.png`

Before publishing, hide private IP details if desired, usernames, account information, and personal media.

---

**Project status:** Jellyfin deployed and accessed from a separate Linux client over the local network.
