# Maintenance Notes

## Fork Scope

This is a stripped-down fork of [qdm12/gluetun](https://github.com/qdm12/gluetun) targeting AirVPN + WireGuard only.

### What was removed (phase 1)

- All VPN providers except AirVPN
- OpenVPN support (WireGuard only)
- AmneziaWG
- Shadowsocks proxy
- HTTP proxy
- DNS-over-TLS and DNS block lists
- PMTUD (Path MTU Discovery tooling)
- boringpoll
- Server list updater (AirVPN servers are fetched at runtime)
- CLI subcommands: `genkey`, `openvpnconfig`, `formatservers`, `clientkey`, `update`, `ci`

### What remains

- WireGuard (kernelspace and userspace)
- AirVPN provider + server selection (country, city, name)
- Port forwarding (`FIREWALL_VPN_INPUT_PORTS`)
- Kill switch / firewall
- Health check endpoint
- Public IP reporting
- `network_mode: service:gluetun` container routing

## Syncing from Upstream

The fork tracks `qdm12/gluetun` master. To pull upstream fixes:

```bash
git fetch upstream
git rebase upstream/master
# resolve conflicts — most will be in files we deleted or providers.go
```

Files that will always conflict:
- `internal/constants/providers/providers.go` — we keep only `airvpn`
- `internal/provider/providers.go` — we keep only the AirVPN case
- `internal/configuration/settings/settings.go` — removed features
- `cmd/gluetun/main.go` — removed feature wiring

## Release Process

1. Test locally with a real AirVPN WireGuard config
2. Bump version tag: `git tag vX.Y.Z`
3. Push tag — GitHub Actions builds and pushes `ghcr.io/wolffcatskyy/gluetun-airvpn-edition`

## Known Differences from Upstream

- No `VPN_TYPE=openvpn` support — will error
- No provider other than `airvpn` supported — will error
- No `HTTP_PROXY=on` — removed
- No `SHADOWSOCKS=on` — removed
- No `DNS_OVER_TLS` — removed; uses system DNS
- No server list auto-update (`UPDATER_PERIOD`) — removed
