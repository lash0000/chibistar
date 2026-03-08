**Note:** When my computer gone it's not server shutdown it's just resting! 

## My current setup:

```
For public access:

    star-philpro-media.space (Hostinger domain)
        │
    Cloudflare Tunnel
        │
      Caddy
        │
    chibisafe

For private access:

    tablet / laptop (Tailscale accessed)
        │
    Tailscale
        │
    ux (Hostname)
        │
    Caddy (Web Server)
        │
    chibisafe (App)
```

## My tutorial as a arch linux user

Install chibisafe 101
Preferred: Docker

1. Create directory (chibisafe or ANY)
2. sudo pacman -S docker docker-compose-bin
(docker-compose seems to be broken during at this time of writing idk...)
3. Prepare docker-compose.yml (from the chibisafe docs) and my Caddyfile
3. docker compose up -> To run
   docker compose down -> To shutdown 
4. Public registration is disabled and so we can request an invite!!!
5. To update: docker pull chibisafe/chibisafe:latest
