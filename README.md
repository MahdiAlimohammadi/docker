# Docker Engine Install Script

This repository contains a modified Docker Engine installer script (install.sh) that supports proxy and Docker network configuration.

Quick summary
- Script: install.sh (no rename required)
- Path: ./install.sh
- Purpose: install Docker Engine on Linux with optional proxy, APT/YUM proxy configuration, Docker daemon network settings (/etc/docker/daemon.json), and systemd Docker service proxy config.

Prerequisites
- Linux VM (Ubuntu/Debian/CentOS/Fedora/RHEL supported)
- sudo or root
- curl

Make the script executable and run
```sh
chmod +x install.sh
sudo ./install.sh
```

Common examples
- Dry run (no changes):
```sh
sudo ./install.sh --dry-run
```
- Disable proxy at runtime:
```sh
sudo ./install.sh --disable-proxy
```
- Enable network config only:
```sh
sudo ./install.sh --enable-network-config
```
- Install using a mirror:
```sh
sudo ./install.sh --mirror Aliyun
```
- Pin a Docker version:
```sh
sudo ./install.sh --version 24.0.2
```

Top-of-file configuration (edit before running)
- ENABLE_PROXY=1 or 0
- HTTP_PROXY_HOST / HTTP_PROXY_PORT
- HTTPS_PROXY_HOST / HTTPS_PROXY_PORT
- NO_PROXY_LIST
- CONFIGURE_NETWORK=1 or 0
- DOCKER_BIP (docker0 CIDR)
- POOL_BASE and POOL_SIZE (default-address-pools)
- LOG_DRIVER, LOG_MAX_SIZE, LOG_MAX_FILE

What the script changes
- Writes /etc/docker/daemon.json (network + logging settings)
- Creates systemd override at /etc/systemd/system/docker.service.d/http-proxy.conf when proxy is enabled
- Writes APT proxy file /etc/apt/apt.conf.d/95proxies for Debian/Ubuntu when proxy enabled
- Adds proxy setting to /etc/yum.conf for CentOS/RHEL when proxy enabled
- Registers Docker repository and installs packages via apt/dnf/yum depending on distro

Flags and useful options
- --enable-proxy / --disable-proxy — toggle proxy (also available by editing the file)
- --enable-network-config / --disable-network-config — toggle writing /etc/docker/daemon.json
- --dry-run — show actions without applying
- --setup-repo / --repo-only — configure repos only
- --no-autostart — install without enabling/starting docker.service
- --channel stable|test — repository channel
- --mirror Aliyun|AzureChinaCloud — use alternative Docker mirror

Safety notes
- If Docker is already installed the script warns and pauses (20s) to allow aborting.
- Running this script overwrites daemon.json and repo files; back up custom config before running.
- Requires network access to the chosen download URL and curl.

Rename
- No rename necessary. To rename locally:
```sh
git mv install.sh setup.sh
chmod +x setup.sh
```

Support
- Inspect the script header for current default values and available runtime flags.