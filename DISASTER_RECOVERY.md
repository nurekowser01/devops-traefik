# Disaster Recovery

## Scenario: acme.json Corruption or Loss
If the `acme.json` file is deleted, Traefik will attempt to re-request all certificates from Let's Encrypt simultaneously upon restart.
**Risk:** Hitting Let's Encrypt rate limits (e.g., 50 certificates per week), causing the site to be inaccessible over HTTPS for days.

### Recovery Procedure
1. Halt Traefik: `docker compose down`
2. Retrieve the latest `acme.json.gz` backup from S3/R2 (see Backup Phase).
3. Extract and overwrite the local file: `gzip -d -c backup.json.gz > acme.json`
4. Ensure correct permissions: `chmod 600 acme.json`
5. Restart Traefik: `docker compose up -d`
