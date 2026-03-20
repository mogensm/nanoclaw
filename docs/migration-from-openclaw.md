# Migration from OpenClaw to NanoClaw — CJK Secretary

## Pre-migration: What stays
- **Skyteliste webapp** (`repo-skyteliste-webapp-1`) — independent container, untouched
- **Nextcloud stack** (`nextcloud-app`, `nextcloud-db`, `nextcloud-redis`, `onlyoffice-documentserver`) — untouched
- **Reverse proxy** (`hopeful_neumann`) — untouched, but remove OpenClaw port 8443 block after migration
- **Other containers** (`oauth2-proxy`, `proserv-web`) — untouched
- **Club documents** (`/volume1/SportsClub/`) — untouched
- **CJK SQLite database** — moves from OpenClaw workspace to NanoClaw data dir

## Phase 1: Deploy NanoClaw alongside OpenClaw

1. Copy forked NanoClaw repo to NAS: `/volume1/docker/nanoclaw/`
2. Build container image: `cd container && bash build.sh`
3. Build host process: `npm run build`
4. Configure `.env` with all credentials
5. Configure mount allowlist, sender allowlist
6. Migrate skills (SKILL.md files) into `container/skills/`
7. Copy agent persona (SOUL.md, AGENTS.md, USER.md) into group CLAUDE.md
8. Test via Telegram (use different bot token or same bot, different chat)
9. Verify all skills work: email send/read, Spond queries, skyteliste, OCR, database

## Phase 2: Cut over

1. Stop OpenClaw: `sudo docker stop repo-openclaw-gateway-1`
2. Point Telegram bot to NanoClaw (if using same bot token)
3. Start NanoClaw host process (systemd service or screen/tmux)
4. Verify Telegram works end-to-end
5. Test scheduled tasks

## Phase 3: Remove OpenClaw completely

### Containers to remove
```bash
sudo /usr/local/bin/docker stop repo-openclaw-gateway-1
sudo /usr/local/bin/docker rm repo-openclaw-gateway-1
sudo /usr/local/bin/docker rmi openclaw:custom
```

### Files to remove from NAS
```bash
# OpenClaw repo and build artifacts
sudo rm -rf /volume1/docker/openclaw/repo/

# OpenClaw config (keep backup first)
sudo cp /volume1/docker/openclaw/config/openclaw.json /volume1/docker/openclaw/config/openclaw.json.bak-pre-removal
sudo rm -rf /volume1/docker/openclaw/config/

# OpenClaw workspace (skills are copied to NanoClaw, DB is migrated)
# CRITICAL: verify DB and skills are migrated before removing
sudo rm -rf /volume1/docker/openclaw/workspace/

# Docker compose files (no longer needed)
# The docker-compose.yml, docker-compose.extra.yml, docker-compose.override.yml
# are inside /volume1/docker/openclaw/repo/ which is already removed above
```

### Files to keep
```bash
# Keep these until NanoClaw is fully verified:
/volume1/docker/openclaw/restore-nginx.sh  # update for NanoClaw or remove
/volume1/docker/openclaw/backup-db.sh      # update path to NanoClaw DB
```

### Nginx cleanup
Remove port 8443 server block from `/volume1/docker/reverse-proxy.conf` (container nginx).
Add NanoClaw port if needed (or use Telegram-only access).

### Cron cleanup
Remove or update any cron entries referencing `repo-openclaw-gateway-1`.

### Docker cleanup
```bash
# Remove dangling images
sudo /usr/local/bin/docker image prune -f

# Remove unused volumes
sudo /usr/local/bin/docker volume prune -f

# Verify no OpenClaw traces
sudo /usr/local/bin/docker ps -a | grep -i openclaw
sudo /usr/local/bin/docker images | grep -i openclaw
```

### DNS/hosts cleanup
No changes needed — OpenClaw used `mogensm.synology.me:8443` which was just a reverse proxy port.

### Verification checklist
- [ ] NanoClaw responds to Telegram messages
- [ ] Email skill works (send and read)
- [ ] Spond skill works (members, events, attendance)
- [ ] Skyteliste skill works (generate, show, export)
- [ ] OCR skill works (if used via Telegram)
- [ ] Database queries work
- [ ] Brev/document generation works
- [ ] Scheduled tasks fire correctly
- [ ] Club documents accessible (read and write)
- [ ] No OpenClaw containers running (`docker ps`)
- [ ] No OpenClaw images stored (`docker images`)
- [ ] No OpenClaw files on disk (`find /volume1/docker/openclaw -type f | wc -l` = 0)
- [ ] Backup script updated for NanoClaw DB path
- [ ] restore-nginx.sh updated (remove OpenClaw references)
