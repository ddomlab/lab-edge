# lab-edge

Caddy reverse proxy: the single public entry point for the lab server.
HTTPS certificates are obtained and renewed automatically, so no certbot on the host.

## Routes

| Address | Goes to |
|---|---|
| `pth.ddomlab.org` | `pth` container (PTH sensor API + dashboard) |
| `eln.ddomlab.org` | eLabFTW *(added at cutover)* |
| `eln.ddomlab.org:5000` | `pth` - compatibility for the sensor and MEDUSA *(added at cutover, removed once they are repointed)* |

Apps publish no ports of their own; only this project is exposed.

## Deploy

```bash
sudo docker network create lab-net          # once per server
git clone https://github.com/ddomlab/lab-edge.git /opt/edge
cd /opt/edge && sudo docker compose up -d
sudo docker compose logs caddy | tail -20   # expect "certificate obtained successfully"
```

Update a route: edit `Caddyfile` via a PR, then on the server `git pull && sudo docker compose restart caddy`.

## Requirements

- DNS record for each hostname pointing at this server
- Ports 80 and 443 open (DigitalOcean Cloud Firewall; note Docker bypasses ufw)
- The `caddy_data` volume must persist: it holds the certificates (Let's Encrypt has rate limits)
