### Caddyfile.local
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
