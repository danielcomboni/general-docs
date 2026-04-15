### install caddy if not yet installed
```cli
sudo apt update
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https curl
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
sudo apt update
sudo apt install caddy
```

### check caddy version
```cli
caddy version
```

### run caddy confi
```cli
sudo caddy run --config Caddyfile.local
```

### create Caddyfile.local
>[!NOTE]
> *Caddyfile* is standard nomenclature
```cli

{
    email your-email@example.com
}

lainisha.local {
    header {
        Strict-Transport-Security "max-age=63072000"
        X-Content-Type-Options "nosniff"
        X-XSS-Protection "1; mode=block"
        X-Frame-Options "DENY"
        Referrer-Policy "strict-origin-when-cross-origin"
        -Server
    }

    encode gzip

    @health {
        path /health
    }
    handle @health {
        respond "OK" 200
    }

    handle /api/* {
        reverse_proxy http://localhost:7003
    }

    @websockets {
        header Connection *Upgrade*
        header Upgrade websocket
    }
    handle @websockets {
        reverse_proxy http://localhost:7003
    }

    handle {
        reverse_proxy http://localhost:5173
    }
}

www.lainisha.local {
    redir https://lainisha.local{uri} permanent 308
}
```

### run caddy server
```cli
sudo caddy run --config Caddyfile.local
```
