# Context
We need to securely terminate TLS for multiple containerized web applications.

# Decision
Traefik will act as the single edge TLS terminator. It will automatically negotiate Let's Encrypt certificates using HTTP-01 or DNS-01 challenges.

# Consequences
- **Positive:** Application containers do not need to manage certificates or handle HTTPS natively. They listen on port 80 internally.
- **Negative:** Traefik becomes a single point of failure (SPOF) for incoming traffic.
