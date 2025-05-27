# Cloudflared

This document is a shortend form of the original `cloudflared` [documentation](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/do-more-with-tunnels/local-management/).

## Content

- [Installation](#installation)
  - [1. Download](#1-download)
  - [2. Authenticate](#2-authenticate-cloudflared)
  - [3. Create tunnel](#3-create-tunnel)
  - [4. Create config](#4-create-config)
  - [5. DNS entry](#5-create-dns-entry)
  - [6. Run as service](#6-run-as-service)

## Installation

For the full installation guide go [here](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/do-more-with-tunnels/local-management/create-local-tunnel/).

### 1. Download

#### 1. Add Cloudflare's signing key

```sh
sudo mkdir -p --mode=0755 /usr/share/keyrings
curl -fsSL https://pkg.cloudflare.com/cloudflare-main.gpg | sudo tee /usr/share/keyrings/cloudflare-main.gpg >/dev/null
```

#### 2. Add Cloudflare's repo

```sh
echo "deb [signed-by=/usr/share/keyrings/cloudflare-main.gpg] https://pkg.cloudflare.com/cloudflared any main" | sudo tee /etc/apt/sources.list.d/cloudflared.list
```

#### 3. Update and Install

```sh
sudo apt-get update && sudo apt-get install cloudflared -y
```

### 2. Authenticate `cloudflared`

After login in via the profided link, `cloudflared` will save a `cert.pem` file at `~/.cloudflared/`.

```sh
cloudflared tunnel login
```

### 3. Create tunnel

This command will not start a connection, but instead provide a UUID and a `credential-file` (`<Tunnel-UUID>.json`) at `~/.cloudflared/` for the tunnel.

```sh
cloudflared tunnel create <NAME>
```

All tunnels can be listed via:

```sh
cloudflared tunnel list
```

### 4. Create Config

In your `~/.cloudflared/` directory, create a `config.yml` file. This is only one of the many examples form the [official docs](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/do-more-with-tunnels/local-management/configuration-file/). This `config.yml` file can be found [here](/infrastructure/templates/cloudflared/config.yml).

```yml
tunnel: <Tunnel-UUID>
credentials-file: /etc/cloudflared/<Tunnel-UUID>.json

ingress:
  - hostname: <example.com>
    service: http://localhost:80
  - service: http_status:404
```

### 5. Create DNS Entry

Create a `CNAME` record with this prarmeters.

| Field | Value |
| ----- | ----- |
| Name  | `<example.com>` |
| Target | `<Tunnel-UUID>.cfargotunnel.com` |
| Proxy | `true` |

### 6. Run as service

#### 1. Move config

```sh
sudo mv .cloudflared /etc/cloudflared
```

#### 2. Install `cloudflared` service

```sh
sudo cloudflared service install
```

#### 3. Start the service

```sh
sudo systemctl start cloudflared 
```

#### 4. Enable on boot

```sh
sudo systemctl enable cloudflared
```

#### 5. Check the service

```sh
systemctl status cloudflared
```
