# Home Assistant & Matter Server Docker Stack

This repository configures and manages a home automation stack running **Home Assistant Core** and **Matter Server** using Docker Compose. Both services are configured with host networking for device discovery (such as mDNS, Thread, and Chromecast).

## Services

* **Home Assistant** ([docker-compose.yml](docker-compose.yml))
  * **Image**: `ghcr.io/home-assistant/home-assistant:2026.5.4`
  * **Description**: The core home automation platform.
  * **Volume Mounts**:
    * `./config` mapped to `/config` (stores Home Assistant configuration, databases, and logs).
    * `/etc/localtime` (read-only, syncs time with the host).
    * `/run/dbus` (read-only, required for Bluetooth/mDNS integration).
  * **Privileges**: Runs in `privileged: true` mode to allow direct hardware access (e.g., Bluetooth, Zigbee/Z-Wave USB dongles).

* **Matter Server** ([docker-compose.yml](docker-compose.yml))
  * **Image**: `ghcr.io/home-assistant-libs/python-matter-server:8.1.0`
  * **Description**: Python Matter Server to support Matter-compatible devices and Thread commissioning.
  * **Volume Mounts**:
    * `./matter-data` mapped to `/data` (stores Matter fabric administration details and persistent node data).
    * `/run/dbus` (read-only, required for mDNS discovery and Thread communication).
  * **Security**: Configured with `apparmor:unconfined` to permit necessary low-level networking and hardware communications.

---

## Directory Structure

* [docker-compose.yml](docker-compose.yml): The configuration file defining the services, images, network, and storage.
* `config/`: Direct directory mapping containing Home Assistant's configuration files.
* `matter-data/`: Direct directory mapping containing Matter network/fabric files.

---

## Management Commands

All commands should be run from the root of this project directory.

### Start the Stack
Start the containers in the background (detached mode):
```bash
docker compose up -d
```

### Stop the Stack
Stop the running containers:
```bash
docker compose down
```

### Pull Latest Images
To check for and download any updated images specified in [docker-compose.yml](docker-compose.yml):
```bash
docker compose pull
```

### View Logs
To view logs for both services:
```bash
docker compose logs -f
```

To tail logs for a specific service (e.g., `homeassistant` or `matter-server`):
```bash
docker compose logs -f homeassistant
docker compose logs -f matter-server
```

### Check Container Status
Verify which containers are running and their statuses:
```bash
docker compose ps
```
