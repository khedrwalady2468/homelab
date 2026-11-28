# Docker Basics: Starting Uptime Kuma

Running my first container: **Uptime Kuma**, a dashboard that checks my homelab services every minute and shows which ones are up or down.

| | |
|---|---|
| **Status** | Running, tests still to do |
| **Host** | Fedora Server (`192.168.0.103`) |
| **Dashboard** | `http://192.168.0.103:3001` |
| **Full guide** | [docker-basics-setup.md](docker-basics-setup.md) |

---

## Why this matters

- Most self-hosted software is shared as a Docker image, so knowing Docker lets me run almost any project.
- The one command below describes the whole setup, so I can rebuild it anywhere.
- Uptime Kuma tells me when Jellyfin or Pi-hole stops working, instead of me finding out later.

---

## Quick start

On the server:

```bash
# 1. Is Docker running?
sudo systemctl status docker
sudo docker run hello-world

# 2. Start Uptime Kuma
sudo docker run -d --restart=unless-stopped -p 192.168.0.103:3001:3001 -v uptime-kuma:/app/data --name uptime-kuma louislam/uptime-kuma:1

# 3. Check it
sudo docker ps
sudo docker logs uptime-kuma
```

Then open `http://192.168.0.103:3001` from the workstation and create the admin account.

### What the command means

| Part | Meaning |
|---|---|
| `-d` | Run in the background |
| `--restart=unless-stopped` | Start again after a crash or a reboot, unless I stopped it on purpose |
| `-p 192.168.0.103:3001:3001` | Listen on the server's LAN address, port 3001, and forward to port 3001 inside the container |
| `-v uptime-kuma:/app/data` | Keep the data in a Docker volume so it survives if the container is deleted |
| `--name uptime-kuma` | Give the container a name |
| `louislam/uptime-kuma:1` | The image to run (pin the version, no `latest`) |

---

## Monitors I added

Use the server's LAN address, never `127.0.0.1`: inside the container that means the container itself.

| Name | Type | Target |
|---|---|---|
| Jellyfin | HTTP(s) | `http://192.168.0.103:8096/web/` |
| Pi-hole | HTTP(s) or DNS | `http://192.168.0.103/admin` |

---

## Problems I hit

| Symptom | Cause | Fix |
|---|---|---|
| `failed to connect to the docker API at unix:///var/run/docker.sock` | The Docker engine was not running (or not installed) | Install it or start it: `sudo systemctl enable --now docker` |
| `docker: invalid reference format` | The command had `–-restart` with a long dash copied from a web page | Retype the options with two normal hyphens (`--`) |
| Jellyfin monitor red, but Jellyfin opens in the browser | Monitor used `https://`, and Jellyfin on 8096 serves plain `http://` | Change the monitor URL to `http://192.168.0.103:8096/web/` |

---

## Still to do

- [ ] Break test: stop Jellyfin and see how long Uptime Kuma takes to notice
- [ ] Reboot test: does the container come back by itself?
- [ ] Add a notification (Telegram or email) and test it
- [ ] Add SSH and router monitors
- [ ] Back up the `uptime-kuma` volume

See [docker-basics-setup.md](docker-basics-setup.md) for the details.
