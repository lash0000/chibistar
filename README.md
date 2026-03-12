> [!NOTE]
> When my computer is gone, it's not a server outage — it's just resting.

## My current setup:

```
For public access (Cloudflare DNS):                     For public access (File Service):                           For public access (API Service):                  
  
star-philpro-media.space (Hostinger domain)             chibisafe.tail997971.ts.net (Tailscale domain)              star-api.tail997971.ts.net (Tailscale domain)
            │                                                               │                                                             │
     Cloudflare Tunnel                                              Tailscale + Funnel                                            Tailscale + Funnel
            │                                                               │                                                             │
     Caddy (Web Server)                                                  TSDProxy                                                     TSDProxy
            │                                                               │                                                             │
      chibisafe (App)                                               Caddy (Web Server)                                            Caddy (Web Server)
                                                                            │                                                             │
                                                                     chibisafe (App)                                             Node / Express (App)

```

## My tutorial as a arch linux user

Install chibisafe 101
Preferred: Docker

1. Create directory (chibisafe or ANY)
2. sudo pacman -S docker docker-compose-bin tailscale
(docker-compose seems to be broken during at this time of writing idk...)
3. Prepare docker-compose.yml (from the chibisafe docs) and my Caddyfile
3. docker compose up -> To run
   docker compose down -> To shutdown 
4. Public registration is disabled and so we can request an invite!!!
5. To update: docker pull chibisafe/chibisafe:latest
6. sudo systemctl enable --now tailscaled
7. sudo tailscale up
8. To add more containers with TSDProxy you must ensure the ff:

```
  tsdproxy:
    image: almeidapaulopt/tsdproxy:latest
    container_name: tsdproxy
    restart: unless-stopped
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./tsdproxy-data:/data
    environment:
      - TSDPROXY_AUTHKEY=tskey-auth-xxxxxx
```

> [!NOTE]
> Getting your AUTHKEY will be done via (admin/settings/keys)[https://login.tailscale.com/admin/settings/keys] and set also Reusable and Ephemeral everytime that you boot up your self-hosting machine. 

```
  star-api:
    build: 
      context: ../star
    expose:
      - 3000
    labels:
      - tsdproxy.enable=true
      - tsdproxy.name=star-api
      - tsdproxy.port=3000
      - tsdproxy.funnel=true
```

- You must ensure that the service name is the same name for your tsdproxy.name and also for expose command configuration will be same for tsdproxy.port 

9. tailscale status
10. tailscale serve --bg http://localhost:80

This will give me 
```
https://<hostname>.ts.net (tailnet only)
|-- / proxy http://localhost:80
```
Port 80 is set from Chibisafe's Caddyfile and docker-compose.yml

11. tailscale funnel --bg 80

This will give me 

```
https://<hostname>.ts.net (Funnel on)
|-- / proxy http://127.0.0.1:80
```

Now to shutdown

12. tailscale serve reset 
13. tailscale funnel reset
14. docker compose down

Hurray! welcome to CGNAT life!
