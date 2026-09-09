# Context
Routing rules change frequently as new containers are deployed or rolled back.

# Decision
We use Traefik's Docker Provider to dynamically discover routing rules via Docker container labels.

# Consequences
- **Positive:** Zero downtime when updating routes. No proxy reloads required.
- **Negative:** The proxy requires access to the Docker socket (`/var/run/docker.sock`), which is a security risk if the proxy is compromised. We mitigate this by mounting it as read-only (`:ro`).
