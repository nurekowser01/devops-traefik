# Security & Hardening

## Docker Socket Protection
Traefik needs access to the Docker API to read container labels. We mount the socket strictly as read-only: `- /var/run/docker.sock:/var/run/docker.sock:ro`.

## Middlewares
We apply global middlewares to all ingress traffic:
1. **Rate Limiting:** Protects against brute-force and basic DDoS.
2. **Security Headers:** Enforces HSTS, X-Content-Type-Options, and Frame-Options.
3. **BasicAuth:** Protects the Traefik dashboard.
