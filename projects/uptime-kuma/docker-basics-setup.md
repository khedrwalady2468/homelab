# Guide: Starting with Docker and Uptime Kuma

## 1. Goal

1. Check that Docker works on the Fedora server.
2. Learn the handful of Docker commands I use every day.
3. Start Uptime Kuma in a container and monitor the other homelab services.

## 2. Environment

| Item | Value |
|---|---|
| Server | Fedora Server, `192.168.0.103` (reserved on the router) |
| Workstation | EndeavourOS, used for the browser and SSH |
| Port used | `3001` (Uptime Kuma) |
| Other services on the server | Pi-hole (80, 53), Jellyfin (8096) |

## 3. Check Docker

```bash
sudo systemctl status docker
sudo docker version
sudo docker run hello-world
```

- If `status` says the unit isn't found, Docker isn't installed. If it says `inactive`, start it:
  ```bash
  sudo systemctl enable --now docker
  ```
- Docker's documentation lists Fedora 43 and 44 as supported. Check my Fedora version with `cat /etc/fedora-release` and read https://docs.docker.com/engine/install/fedora/ before installing. If my version isn't listed, I don't force it: I use Fedora's own packages or Podman instead.
- If the service fails and the log mentions iptables: `sudo alternatives --set iptables /usr/bin/iptables-nft`, then `sudo systemctl restart docker`.

My install method and `docker version` output: TODO paste.

I use `sudo docker ...` for now and do not add my user to the `docker` group, because membership is effectively root access.

## 4. Docker commands I use

| Command | What it does |
|---|---|
| `sudo docker ps` | Running containers (`-a` shows stopped ones too) |
| `sudo docker logs uptime-kuma` | Show the container's output (`-f` follows it) |
| `sudo docker stop uptime-kuma` | Stop it |
| `sudo docker start uptime-kuma` | Start it again |
| `sudo docker restart uptime-kuma` | Restart it |
| `sudo docker exec -it uptime-kuma sh` | Open a shell inside it |
| `sudo docker rm uptime-kuma` | Delete the container (the volume stays) |
| `sudo docker images` | List downloaded images |
| `sudo docker volume ls` | List volumes |
| `sudo docker system df` | Disk used by Docker |

## 5. Start Uptime Kuma

Check that the image tag is current in the project's documentation, pin it, then run:

```bash
sudo docker run -d --restart=unless-stopped -p 192.168.0.103:3001:3001 -v uptime-kuma:/app/data --name uptime-kuma louislam/uptime-kuma:1
```

Type the `--` options by hand if the command doesn't run: pasted text can turn them into long dashes, and Docker then fails with `invalid reference format`.

### The port option

```text
-p  192.168.0.103 : 3001 : 3001
        |            |      |
        |            |      +-- port inside the container
        |            +--------- port on the server
        +---------------------- which server address listens
```

Without the address (`-p 3001:3001`) Docker listens on every interface. Docker also manages its own firewall rules for published ports, so I choose the address in the command instead of relying only on firewalld (I should confirm this on my server).

### Verify

```bash
sudo docker ps
sudo docker logs uptime-kuma
sudo ss -lntp | grep 3001
```

`ss` should show `192.168.0.103:3001`, not `0.0.0.0:3001`. From the workstation open `http://192.168.0.103:3001`, create the admin account, and use a strong password.

Output: TODO paste.

Note: because it listens only on `192.168.0.103`, `curl http://localhost:3001` on the server fails on purpose. Use the LAN address.

## 6. Add monitors

Click **Add New Monitor**. Always use the server's LAN address, because inside the container `127.0.0.1` means the container itself:

```text
Uptime Kuma (container)  --->  127.0.0.1:8096       = the container itself   (wrong)
Uptime Kuma (container)  --->  192.168.0.103:8096   = the server             (right)
```

| Name | Type | Settings |
|---|---|---|
| Jellyfin | HTTP(s) | URL `http://192.168.0.103:8096/web/`, interval 60, retries 3 |
| Pi-hole dashboard | HTTP(s) | URL `http://192.168.0.103/admin` |
| Pi-hole DNS | DNS | Resolver `192.168.0.103`, name `google.com`, record `A`, port `53` |
| SSH | TCP Port | Host `192.168.0.103`, port `22` |
| Router | Ping | `192.168.0.1` |

Retries matter: Jellyfin took about 85 seconds to finish starting after a restart (its log said "Startup complete 0:01:24"), so with no retries a normal restart looks like an outage.

Add a notification under **Settings, Notifications**, use the **Test** button, and enable "Default enabled".

## 7. Tests

- [ ] **Break test:** `sudo systemctl stop jellyfin`, watch the monitor turn red, then `sudo systemctl start jellyfin`. Time to detect: TODO.
- [ ] **Reboot test:** `sudo reboot`, then `sudo docker ps`. Did Uptime Kuma come back by itself? TODO.
- [ ] **Persistence test:** `sudo docker stop uptime-kuma && sudo docker rm uptime-kuma`, run the same `docker run` command again, and check that the account and monitors are still there. TODO.

## 8. Everyday operation

**Update to a new version.** Read the release notes first, then:

```bash
sudo docker stop uptime-kuma
sudo docker rm uptime-kuma
sudo docker pull louislam/uptime-kuma:<new-tag>
# run the same docker run command with the new tag
```

The data stays in the `uptime-kuma` volume.

**Back up the volume** (to the current folder):

```bash
sudo docker run --rm -v uptime-kuma:/data -v "$PWD":/backup alpine tar czf /backup/uptime-kuma-backup.tgz -C /data .
```

**Clean up** old images carefully: `sudo docker system df`, then `sudo docker system prune` (read the prompt first).

## 9. Problems and fixes

| Symptom | Cause | Fix |
|---|---|---|
| `failed to connect to the docker API at unix:///var/run/docker.sock` | The Docker engine was not running (or not installed) | Install or start it: `sudo systemctl enable --now docker` |
| `docker: invalid reference format` | Long dashes (`–-restart`) copied into the command | Retype the options with two normal hyphens |
| Jellyfin monitor red, Jellyfin works in the browser | Monitor URL used `https://`, but port 8096 serves `http://` | Use `http://192.168.0.103:8096/web/` |

Which cause applied to the first row on my server: TODO confirm.

## 10. Security notes

- No port forwarding on the router for this project: Uptime Kuma stays on the LAN.
- Strong admin password (and two-factor authentication if the settings offer it).
- Ports are published on the LAN address only.
- Image version is pinned, and updates happen on purpose.

## 11. What I learned

TODO: five lines in my own words, including one thing I copied without understanding and then looked up.

## 12. Next

- Add SSH and router monitors, and a notification.
- Move Uptime Kuma to a `compose.yaml` file (same setup in one file).
- Include the `uptime-kuma` volume in the restic backups.
