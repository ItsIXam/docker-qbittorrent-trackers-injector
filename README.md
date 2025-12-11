# qBittorrent Trackers Updater – Dockerized Service

![GitHub Workflow Status](https://img.shields.io/github/actions/workflow/status/greatnewhope/docker-qbittorrent-trackers-injector/publish.yml?branch=main&label=build&style=flat-square)
![GHCR](https://img.shields.io/badge/GHCR-qbittorrent--trackers--updater-blue?logo=github&style=flat-square)
![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=flat-square)

🚀 This container runs [`AddqBittorrentTrackers.sh`](https://github.com/Jorman/Scripts) on a schedule to ensure **all your public torrents** in qBittorrent are updated with the **latest public trackers** from [`ngosang/trackerslist`](https://github.com/ngosang/trackerslist).

📌 This tool is different from [claabs/qbittorrent-tracker-updater](https://github.com/claabs/qbittorrent-tracker-updater), which only updates qBittorrent's default **settings** (used for *new torrents*).  
This container **actively updates existing torrents** using the qBittorrent API — hands-free and fully automated.

✨ Based on [Jorman's script](https://github.com/Jorman/Scripts), this project wraps it into a **production-ready Docker service**.

---

## ✅ Features

- 🧠 **Automatically updates existing torrents** with new trackers
- 🌍 Supports **multiple qBittorrent instances** via `QBT_HOSTS` and `QBT_PORTS`
- ⚙️ (Optional) **Also updates** qBittorrent's default setting for appending trackers to new downloads
- 🐳 Fully compatible with `linuxserver/qbittorrent`
- 🔒 Supports both **authenticated** and **auth-bypassed** WebUI setups
- 🔎 Skips **private torrents**
- 🧩 Works out of the box with `gluetun` and VPN setups
- 🪶 Lightweight, scheduled, and self-healing (`restart: unless-stopped`)

---

## 📦 Docker Compose Example

If you're using a VPN container like `gluetun`, run the updater in the same network namespace:

```yaml
services:
  qbittorrent_trackers_updater:
    image: ghcr.io/greatnewhope/qbittorrent-trackers-updater:latest
    container_name: qbittorrent_trackers_updater
    env_file:
      - .env.qbittorrent_trackers_updater
    network_mode: "service:gluetun"
    restart: unless-stopped
```

If you're **not** using a VPN container:

```yaml
services:
  qbittorrent_trackers_updater:
    image: ghcr.io/greatnewhope/qbittorrent-trackers-updater:latest
    container_name: qbittorrent_trackers_updater
    env_file:
      - .env.qbittorrent_trackers_updater
    networks:
      - net
    restart: unless-stopped

networks:
  net:
    external: true
```

---

## ⚙️ Environment Variables

Create a `.env.qbittorrent_trackers_updater` like this:

```dotenv
# List of qBittorrent hosts and ports (comma-separated)
QBT_HOSTS=http://localhost,http://192.168.1.42
QBT_PORTS=8080,8081

# Optional: if not using bypass auth
QBT_USERNAME=admin
QBT_PASSWORD=adminadmin

# Set to true if using qBittorrent's "Bypass authentication for localhost"
QBT_AUTH_BYPASS=false

# Interval in seconds between runs (default is 7200 = 2 hours)
INTERVAL_SECONDS=7200

# Optional: also update qBittorrent's "add_trackers" config with latest public trackers
UPDATE_DEFAULT_TRACKERS=true

# Optional: enable debug logs
DEBUG=true

# optional: define a custom tracker_list url. Must be a url to a tracker list.
TRACKER_LIST=https://newtrackon.com/api/stable,https://trackerslist.com/best.txt,https://trackerslist.com/http.txt,https://raw.githubusercontent.com/ngosang/trackerslist/master/trackers_best.txt
```

> 🔁 The script loops through each qBittorrent instance and injects updated trackers.
> ✍️ If `UPDATE_DEFAULT_TRACKERS=true`, the `"Automatically add these trackers to new downloads"` setting will be updated using the same list.

---

## 🛡️ Authentication Tips

If you're using the `linuxserver/qbittorrent` container, and you've enabled:

> ⚙️ Settings → Web UI → “Bypass authentication for clients on localhost”

Then:

- Set `QBT_USERNAME=` and `QBT_PASSWORD=`
- And set `QBT_AUTH_BYPASS=true`
- The script will skip the login step and go straight to tracker injection

---

## 🧊 Future Plans

- Keep improving the service from user feedback.

---

## 🙏 Credits

- Script by [Jorman](https://github.com/Jorman/Scripts)
- Tracker lists by [ngosang](https://github.com/ngosang/trackerslist)
- Original Docker wrapper idea from [`claabs/qbittorrent-tracker-updater`](https://github.com/claabs/qbittorrent-tracker-updater)

---

Feel free to open an issue or contribute if you want to help make it better!
