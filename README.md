# homeproxy

Caddy in rootless Docker, giving LAN-only services real HTTPS names under
kertinge.dk. Lives in `/data/projects/homeproxy` on the home server.

## Setup (once)

1. **Move the DNS to Cloudflare.** In the Cloudflare dashboard choose
   *Add a domain*, enter `kertinge.dk`, pick the Free plan and continue past
   the record scan. Cloudflare then shows two nameservers
   (`<name>.ns.cloudflare.com`).
2. **Point the domain at them.** At the registrar (dns.services), open
   kertinge.dk, turn off DNSSEC if it is on, and replace the nameservers
   with Cloudflare's two. When Cloudflare shows the domain as *Active*
   (minutes to a few hours), continue.
3. **Add the record.** Cloudflare > kertinge.dk > DNS > Records > Add
   record: type `A`, name `dash`, IPv4 `192.168.50.249`, proxy status
   **DNS only** (grey cloud).
4. **Create the token.** My Profile > API Tokens > Create Token > template
   *Edit zone DNS*; under Zone Resources pick *Specific zone* `kertinge.dk`;
   create and copy it (shown once). On the server, copy `.env.example` to
   `.env` and paste it after `CLOUDFLARE_API_TOKEN=`.
5. Let rootless Docker bind 443 (needs sudo, once):
   `echo net.ipv4.ip_unprivileged_port_start=443 | sudo tee /etc/sysctl.d/90-unprivileged-443.conf && sudo sysctl --system`
6. Open 443/tcp in the firewall for the LAN.
7. `docker compose up -d --build`

Then https://dash.kertinge.dk opens the dashboard.

## Adding a service

Add a block to `Caddyfile`, an A record for its name, and
`docker compose exec caddy caddy reload --config /etc/caddy/Caddyfile`.
