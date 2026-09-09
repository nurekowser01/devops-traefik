# Traefik Dynamic Reverse Proxy (devops-traefik)

## Overview
This repository provides a production-ready configuration for **Traefik v3**, acting as a dynamic edge router and reverse proxy for containerized workloads.

## Architecture & Traffic Flow

`mermaid
graph TD
    Client[Client Request] -->|Port 80/443| Traefik[Traefik Proxy]
    
    subgraph Traefik Middlewares
        Traefik -->|HTTP to HTTPS Redirect| TLS[TLS Termination]
        TLS -->|Rate Limiting| Rate[RateLimit Middleware]
        Rate -->|Security Headers| Headers[Security Headers Middleware]
        Headers -->|BasicAuth| Auth[Dashboard BasicAuth]
    end
    
    subgraph Container Workloads
        Headers --> App1[App Container 1]
        Headers --> App2[App Container 2]
    end
`

## Implementation Features

1. **Dynamic Docker Provider:** Automatically discovers new containers via Docker labels without requiring a proxy restart or config reload.
2. **Automated TLS (Let's Encrypt):** HTTP-01 challenge configured to automatically fetch and renew SSL certificates for newly spun-up containers.
3. **Global HTTP to HTTPS Redirection:** Enforces secure connections for all incoming traffic.
4. **Security Middlewares:** 
   - Strict-Transport-Security (HSTS)
   - X-Content-Type-Options
   - X-Frame-Options
   - Rate limiting to protect against brute-force attacks.
5. **Dashboard Protection:** Traefik's internal management dashboard is exposed but protected by Basic Authentication middleware.

## Repository Structure
- \docker-compose.yml\: The main orchestration file spinning up the Traefik daemon.
- \	raefik.yml\: Static configuration (entrypoints, providers, certificate resolvers).
- \dynamic.yml\: Dynamic configuration for globally applied middlewares.
- \cme.json\: (Ignored in Git) The persistent storage file for SSL certificates.

## Deployment

1. Create a secure password for the dashboard:
   \\\ash
   htpasswd -nb admin secure_password | sed -e s/\\$/\\$\\$/g > .htpasswd
   \\\
2. Touch the ACME certificate file and secure it:
   \\\ash
   touch acme.json && chmod 600 acme.json
   \\\
3. Deploy the proxy:
   \\\ash
   docker compose up -d
   \\\

## Security
- Traefik accesses the Docker socket as read-only (/var/run/docker.sock:ro).
- Certificates are backed up using the architecture defined in the \devops-dokploy\ / \devops-backup-recovery\ phases.
