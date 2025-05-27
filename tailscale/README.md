# Tailscale

This document is a shortened form of the original `Tailscale` [documentation](https://tailscale.com/kb/1017/install).

## Content

- [Installation](#installation)
  - [1. Download](#1-download)
  - [2. IP forwarding](#2-ip-forwarding)
  - [3. Start](#3-start)
  - [4. Route settings](#4-route-settings)
  - [5. Disable key expiry](#5-disable-key-expiry)
  - [6. Enable on boot](#6-enable-on-boot)
  - [7. Check status](#7-check-status)

## Installation

For the full installation guide for `subnet router` go [here](https://tailscale.com/kb/1406/quick-guide-subnets).

### 1. Download

```sh
curl -fsSL https://tailscale.com/install.sh | sh
```

### 2. IP forwarding

```sh
echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
sudo sysctl -p /etc/sysctl.d/99-tailscale.conf
```

### 3. Start

```sh
sudo tailscale up --advertise-routes=10.0.0.0/24
```

### 4. Route settings

Go to the `Machines` page of the admin console. For the added machine open the menu `Edit route setting` > `Subnet routes` and check `10.0.0.0/24` and save.

### 5. Disable key expiry

Go to the `Machines` page of the admin console. For the added machine open the menu and `Disable key expiry`.

### 6. Enable on boot

```sh
sudo systemctl enable tailscaled
sudo systemctl start tailscaled
```

### 7. Check status

```sh
sudo tailscale status
sudo systemctl status tailscaled
```
