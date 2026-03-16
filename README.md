# gluetun-airvpn-edition

Lightweight VPN container for AirVPN — stripped-down fork of [gluetun](https://github.com/qdm12/gluetun).

## What is this?

A minimal Docker container that routes traffic through AirVPN using WireGuard. Forked from gluetun with everything except AirVPN + WireGuard removed — no OpenVPN, no 20+ other providers, no DNS-over-TLS, no HTTP proxy, no shadowsocks, no AmneziaWG.

## Why fork?

gluetun is excellent but supports 60+ providers with a lot of code paths most users don't need. This fork:

- Removes all providers except AirVPN
- Removes OpenVPN (WireGuard only)
- Removes unused features: shadowsocks, HTTP proxy, PMTUD, AmneziaWG, boringpoll
- Removes DNS-over-TLS and DNS block lists
- Smaller image, fewer dependencies, less attack surface
- Focused maintenance for one provider

## Quick Start

```yaml
services:
  gluetun:
    image: ghcr.io/wolffcatskyy/gluetun-airvpn-edition
    cap_add:
      - NET_ADMIN
    devices:
      - /dev/net/tun:/dev/net/tun
    environment:
      - VPN_SERVICE_PROVIDER=airvpn
      - VPN_TYPE=wireguard
      - WIREGUARD_PRIVATE_KEY=your_private_key_here
      - WIREGUARD_PRESHARED_KEY=your_preshared_key_here
      - WIREGUARD_ADDRESSES=10.x.x.x/32
      - SERVER_COUNTRIES=Netherlands
      - TZ=America/New_York
```

To route another container through the VPN, use `network_mode: service:gluetun` on that container.

## Environment Variables

Only AirVPN + WireGuard variables are supported.

| Variable | Description |
|----------|-------------|
| `VPN_SERVICE_PROVIDER` | Must be `airvpn` |
| `VPN_TYPE` | Must be `wireguard` |
| `WIREGUARD_PRIVATE_KEY` | Your WireGuard private key from AirVPN |
| `WIREGUARD_PRESHARED_KEY` | Your WireGuard preshared key from AirVPN |
| `WIREGUARD_ADDRESSES` | Your assigned VPN IP address, e.g. `10.x.x.x/32` |
| `SERVER_COUNTRIES` | Filter by country, e.g. `Netherlands` |
| `SERVER_CITIES` | Filter by city, e.g. `Amsterdam` |
| `SERVER_NAMES` | Filter by server name, e.g. `Adhara` |
| `FIREWALL_VPN_INPUT_PORTS` | Comma-separated ports for AirVPN port forwarding |
| `TZ` | Timezone for log timestamps, e.g. `America/New_York` |
| `LOG_LEVEL` | Log verbosity: `debug`, `info`, `warning`, `error` |

To get your WireGuard keys, generate a configuration in the [AirVPN client area](https://airvpn.org/generator/).

## Getting WireGuard Keys from AirVPN

1. Log in to [airvpn.org](https://airvpn.org)
2. Go to Client Area > Config Generator
3. Select WireGuard protocol
4. Choose your server(s)
5. Download the config — your `PrivateKey`, `PresharedKey`, and `Address` are in the `[Interface]` section

## Port Forwarding

AirVPN supports static port forwarding. After configuring a forwarded port in your AirVPN account:

```yaml
environment:
  - FIREWALL_VPN_INPUT_PORTS=12345
```

## Credits

Forked from [qdm12/gluetun](https://github.com/qdm12/gluetun) — an excellent multi-provider VPN container. This fork strips it to AirVPN-only for users who want a minimal, focused setup.

## License

MIT (inherited from upstream gluetun).
