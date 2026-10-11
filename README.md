# homeproxy

Caddy in rootless Docker, giving LAN-only services real HTTPS names under
kertinge.dk. Lives in `/data/projects/homeproxy` on the home server.

## Setup (once)

1. Move kertinge.dk's DNS to Cloudflare (free plan).
2. In Cloudflare add an A record `dash` -> `192.168.50.249`, proxy status
   **DNS only** (grey cloud).
3. Create an API token: Zone > DNS > Edit, limited to kertinge.dk. Copy
   `.env.example` to `.env` and put it there.
4. Let rootless Docker bind 443 (needs sudo, once):
   `echo net.ipv4.ip_unprivileged_port_start=443 | sudo tee /etc/sysctl.d/90-unprivileged-443.conf && sudo sysctl --system`
5. Open 443/tcp in the firewall for the LAN.
6. `docker compose up -d --build`

Then https://dash.kertinge.dk opens the dashboard.

## Adding a service

Add a block to `Caddyfile`, an A record for its name, and
`docker compose exec caddy caddy reload --config /etc/caddy/Caddyfile`.
