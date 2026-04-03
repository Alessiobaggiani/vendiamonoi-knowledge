# 12 — Deployment Production & Scalabilità

> Guida tecnica completa al deployment in produzione di OpenClaw: Docker, Kubernetes, reverse proxy, SSL/TLS, scaling orizzontale, monitoring, backup, disaster recovery e infrastruttura enterprise per Vendiamonoi.it

---

## Indice

1. [Panoramica Deployment](#1-panoramica-deployment)
2. [Docker: Setup Base](#2-docker-setup-base)
3. [Docker Compose: Production Stack](#3-docker-compose-production-stack)
4. [Reverse Proxy e SSL/TLS](#4-reverse-proxy-e-ssltls)
5. [Kubernetes Deployment](#5-kubernetes-deployment)
6. [Scaling Orizzontale](#6-scaling-orizzontale)
7. [State Management e Persistenza](#7-state-management-e-persistenza)
8. [Monitoring e Observability](#8-monitoring-e-observability)
9. [Logging e Alerting](#9-logging-e-alerting)
10. [Backup e Disaster Recovery](#10-backup-e-disaster-recovery)
11. [Update Strategy](#11-update-strategy)
12. [Sicurezza Production](#12-sicurezza-production)
13. [Costi Infrastruttura](#13-costi-infrastruttura)
14. [Configurazione Vendiamonoi](#14-configurazione-vendiamonoi)

---

## 1. Panoramica Deployment

### Opzioni di deployment

```
┌─────────────────────────────────────────────────────┐
│              OPZIONI DEPLOYMENT OPENCLAW             │
├─────────────────────────────────────────────────────┤
│                                                     │
│  1. Local (Dev)          Installazione diretta      │
│     npm install          Nessun container           │
│     Solo sviluppo        Non per produzione         │
│                                                     │
│  2. Docker Compose       Container isolati          │
│     docker-compose up    Proxy + SSL                │
│     PMI / Single server  Fino a ~50 agenti          │
│                                                     │
│  3. Kubernetes           Orchestrazione avanzata    │
│     kubectl apply        Auto-scaling               │
│     Enterprise           Centinaia di agenti        │
│                                                     │
│  4. Managed Hosting      OpenClaw.rocks / Cloud     │
│     Zero ops             Pay-per-use                │
│     Per chi non vuole    gestire infrastruttura     │
│                                                     │
└─────────────────────────────────────────────────────┘
```

### Quale scegliere?

| Criterio | Docker Compose | Kubernetes | Managed |
|---|---|---|---|
| **Complessità setup** | Bassa | Alta | Zero |
| **Costo infra** | €5-30/mese | €50-200/mese | €30-100/mese |
| **Scalabilità** | Fino a ~50 agenti | Illimitata | Dipende dal piano |
| **Controllo** | Totale | Totale | Limitato |
| **Manutenzione** | Media | Alta | Zero |
| **Per Vendiamonoi** | ✅ Fase iniziale | 🔜 Fase avanzata | ❌ No controllo |

---

## 2. Docker: Setup Base

### Prerequisiti

| Componente | Minimo | Raccomandato |
|---|---|---|
| **CPU** | 2 core | 4+ core |
| **RAM** | 2 GB | 4+ GB |
| **Disco** | 20 GB SSD | 50+ GB SSD |
| **Docker** | 24.0+ | Latest |
| **Docker Compose** | v2.20+ | Latest |
| **OS** | Ubuntu 22.04 LTS | Ubuntu 24.04 LTS |

> **IMPORTANTE**: Host con meno di 2GB RAM crashano con exit code 137 (OOM kill).

### Installazione Docker base

```bash
# 1. Clona il repository
git clone https://github.com/openclaw/openclaw.git
cd openclaw

# 2. Usa lo script di setup automatico
chmod +x docker-setup.sh
./docker-setup.sh

# 3. Oppure build manuale
docker build -t openclaw:latest .

# 4. Run base (solo per test, NON produzione)
docker run -d \
  --name openclaw \
  -p 18789:18789 \
  -v openclaw-config:/root/.openclaw \
  -v openclaw-workspace:/workspace \
  -e GATEWAY_TOKEN=your-secret-token \
  openclaw:latest
```

### Struttura directory container

```
/root/.openclaw/          ← Configurazione
  ├── openclaw.json       ← Config principale
  ├── agents/             ← Configurazione agenti
  ├── skills/             ← Skill installate
  └── sessions/           ← Sessioni attive

/workspace/               ← Area di lavoro agente
  ├── projects/           ← Progetti
  └── memory/             ← MEMORY.md, daily notes
```

---

## 3. Docker Compose: Production Stack

### docker-compose.yml completo

```yaml
version: "3.9"

services:
  # ─── OpenClaw Gateway ───
  openclaw:
    image: openclaw/openclaw:latest
    container_name: openclaw-gateway
    restart: unless-stopped
    ports:
      - "127.0.0.1:18789:18789"   # Solo localhost (proxy davanti)
    volumes:
      - openclaw-config:/root/.openclaw
      - openclaw-workspace:/workspace
      - /etc/localtime:/etc/localtime:ro
    environment:
      - GATEWAY_TOKEN=${GATEWAY_TOKEN}
      - NODE_ENV=production
      - LOG_LEVEL=info
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 2G
        reservations:
          cpus: '1'
          memory: 1G
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:18789/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 30s
    logging:
      driver: "json-file"
      options:
        max-size: "50m"
        max-file: "5"

  # ─── Nginx Reverse Proxy ───
  nginx:
    image: nginx:1.27-alpine
    container_name: openclaw-proxy
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/conf.d:/etc/nginx/conf.d:ro
      - ./nginx/ssl:/etc/nginx/ssl:ro
      - certbot-webroot:/var/www/certbot:ro
    depends_on:
      openclaw:
        condition: service_healthy

  # ─── Certbot (SSL automatico) ───
  certbot:
    image: certbot/certbot
    container_name: openclaw-certbot
    volumes:
      - ./nginx/ssl:/etc/letsencrypt
      - certbot-webroot:/var/www/certbot
    entrypoint: "/bin/sh -c 'trap exit TERM; while :; do certbot renew; sleep 12h & wait $${!}; done;'"

  # ─── Redis (Session Store per scaling) ───
  redis:
    image: redis:7-alpine
    container_name: openclaw-redis
    restart: unless-stopped
    command: redis-server --requirepass ${REDIS_PASSWORD} --maxmemory 256mb --maxmemory-policy allkeys-lru
    volumes:
      - redis-data:/data
    deploy:
      resources:
        limits:
          memory: 512M

volumes:
  openclaw-config:
  openclaw-workspace:
  redis-data:
  certbot-webroot:
```

### File .env (NON committare!)

```env
# Gateway
GATEWAY_TOKEN=your-very-long-random-token-here

# Redis
REDIS_PASSWORD=another-strong-password

# API Keys (montati come Docker secrets in produzione)
ANTHROPIC_API_KEY=sk-ant-...
OPENAI_API_KEY=sk-...
```

---

## 4. Reverse Proxy e SSL/TLS

### Nginx configuration

```nginx
# /nginx/conf.d/openclaw.conf

# Redirect HTTP → HTTPS
server {
    listen 80;
    server_name openclaw.vendiamonoi.it;
    
    location /.well-known/acme-challenge/ {
        root /var/www/certbot;
    }
    
    location / {
        return 301 https://$host$request_uri;
    }
}

# HTTPS + WebSocket
server {
    listen 443 ssl http2;
    server_name openclaw.vendiamonoi.it;
    
    # SSL Certificates (Let's Encrypt)
    ssl_certificate     /etc/nginx/ssl/live/openclaw.vendiamonoi.it/fullchain.pem;
    ssl_certificate_key /etc/nginx/ssl/live/openclaw.vendiamonoi.it/privkey.pem;
    
    # SSL Security
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256;
    ssl_prefer_server_ciphers off;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 1d;
    
    # HSTS
    add_header Strict-Transport-Security "max-age=63072000; includeSubDomains" always;
    
    # Security Headers
    add_header X-Content-Type-Options nosniff;
    add_header X-Frame-Options DENY;
    add_header X-XSS-Protection "1; mode=block";
    
    # Rate Limiting
    limit_req zone=openclaw burst=20 nodelay;
    
    # Proxy → OpenClaw Gateway
    location / {
        proxy_pass http://openclaw:18789;
        proxy_http_version 1.1;
        
        # WebSocket support (CRITICO per OpenClaw)
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        
        # Headers
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # Timeout lunghi per sessioni agente
        proxy_read_timeout 3600s;
        proxy_send_timeout 3600s;
        proxy_connect_timeout 60s;
    }
}

# Rate Limit Zone
limit_req_zone $binary_remote_addr zone=openclaw:10m rate=10r/s;
```

### Alternativa: Caddy (auto-SSL)

```Caddyfile
# /Caddyfile — più semplice di Nginx, SSL automatico
openclaw.vendiamonoi.it {
    reverse_proxy openclaw:18789 {
        # WebSocket
        header_up Connection {>Connection}
        header_up Upgrade {>Upgrade}
    }
    
    # Timeout lunghi per agent sessions
    timeouts {
        read_body 0
        write 3600s
        idle 3600s
    }
}
```

---

## 5. Kubernetes Deployment

### Architettura K8s

```
┌─────────────────────────────────────────────────────┐
│                   KUBERNETES CLUSTER                │
│                                                     │
│  ┌─────────────┐    ┌─────────────────────────┐     │
│  │  Ingress    │───►│  Service (ClusterIP)    │     │
│  │  (nginx)    │    │  openclaw-gateway       │     │
│  │  TLS term.  │    └───────────┬─────────────┘     │
│  └─────────────┘                │                   │
│                    ┌────────────┼────────────┐      │
│               ┌────▼───┐  ┌────▼───┐  ┌─────▼──┐   │
│               │ Pod 1  │  │ Pod 2  │  │ Pod 3  │   │
│               │OpenClaw│  │OpenClaw│  │OpenClaw│   │
│               └────┬───┘  └────┬───┘  └────┬───┘   │
│                    │           │           │        │
│               ┌────▼───────────▼───────────▼───┐    │
│               │    Redis (Session Store)       │    │
│               │    + PVC (Persistent Volume)   │    │
│               └────────────────────────────────┘    │
└─────────────────────────────────────────────────────┘
```

### Deployment manifest

```yaml
# openclaw-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: openclaw-gateway
  labels:
    app: openclaw
spec:
  replicas: 2
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 0
      maxSurge: 1
  selector:
    matchLabels:
      app: openclaw
  template:
    metadata:
      labels:
        app: openclaw
    spec:
      containers:
        - name: openclaw
          image: openclaw/openclaw:latest
          ports:
            - containerPort: 18789
          env:
            - name: GATEWAY_TOKEN
              valueFrom:
                secretKeyRef:
                  name: openclaw-secrets
                  key: gateway-token
            - name: REDIS_URL
              value: "redis://:$(REDIS_PASSWORD)@redis-svc:6379"
            - name: NODE_ENV
              value: "production"
          resources:
            requests:
              cpu: "500m"
              memory: "1Gi"
            limits:
              cpu: "2000m"
              memory: "2Gi"
          readinessProbe:
            httpGet:
              path: /health
              port: 18789
            initialDelaySeconds: 15
            periodSeconds: 10
          livenessProbe:
            httpGet:
              path: /health
              port: 18789
            initialDelaySeconds: 30
            periodSeconds: 30
          volumeMounts:
            - name: config
              mountPath: /root/.openclaw
            - name: workspace
              mountPath: /workspace
      volumes:
        - name: config
          persistentVolumeClaim:
            claimName: openclaw-config-pvc
        - name: workspace
          persistentVolumeClaim:
            claimName: openclaw-workspace-pvc

---
# Service
apiVersion: v1
kind: Service
metadata:
  name: openclaw-gateway
spec:
  selector:
    app: openclaw
  ports:
    - port: 18789
      targetPort: 18789
  type: ClusterIP

---
# HPA (Horizontal Pod Autoscaler)
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: openclaw-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: openclaw-gateway
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
    - type: Resource
      resource:
        name: memory
        target:
          type: Utilization
          averageUtilization: 80
```

### Kubernetes Operator

Il progetto `openclaw-operator` (openclaw-rocks) offre un operator Kubernetes per:
- Deploy e gestione lifecycle automatico
- Security production-grade
- Observability integrata
- Gestione CRD (Custom Resource Definition)

```yaml
# CRD esempio
apiVersion: openclaw.rocks/v1
kind: OpenClawInstance
metadata:
  name: vendiamonoi-agent
spec:
  replicas: 2
  model: "anthropic/claude-sonnet-4"
  resources:
    memory: "2Gi"
    cpu: "2"
  security:
    sandbox: true
    networkPolicy: restricted
```

---

## 6. Scaling Orizzontale

### Architettura multi-instance

```
                 ┌──────────────────┐
                 │   Load Balancer  │
                 │  (Nginx / HAProxy│
                 │   / Cloud LB)   │
                 └────────┬─────────┘
                          │
            ┌─────────────┼─────────────┐
            │             │             │
    ┌───────▼──────┐ ┌───▼────────┐ ┌──▼───────────┐
    │  Gateway #1  │ │ Gateway #2 │ │  Gateway #3  │
    │  (Instance)  │ │ (Instance) │ │  (Instance)  │
    └──────┬───────┘ └─────┬──────┘ └──────┬───────┘
           │               │               │
           └───────────────┼───────────────┘
                           │
                  ┌────────▼────────┐
                  │   Redis Cluster │
                  │  (Session State)│
                  └────────┬────────┘
                           │
                  ┌────────▼────────┐
                  │  Shared Storage │
                  │  (NFS / EFS)   │
                  └─────────────────┘
```

### State sharing: due approcci

| Approccio | Pro | Contro | Quando usarlo |
|---|---|---|---|
| **Shared Filesystem** (NFS/EFS) | Semplice, nessun codice | Latenza I/O, lock contention | Fino a 5 istanze |
| **Redis Session Store** | Veloce, scalabile, atomic | Setup aggiuntivo, costo | 5+ istanze |

### Configurazione Redis per session state

```json
{
  "gateway": {
    "sessionStore": {
      "type": "redis",
      "url": "redis://:password@redis-host:6379",
      "prefix": "openclaw:session:",
      "ttl": 3600
    }
  }
}
```

### Performance benchmark

| Setup | Latenza P95 | Throughput | Costo/mese |
|---|---|---|---|
| 1 istanza (4 CPU, 4GB) | ~350ms | ~20 req/s | €20 |
| 2 istanze + Redis | ~200ms | ~40 req/s | €50 |
| 5 istanze + Redis cluster | ~150ms | ~100 req/s | €120 |
| 10 istanze + K8s + HPA | ~120ms | ~200+ req/s | €250+ |

---

## 7. State Management e Persistenza

### Cosa persistere

| Dato | Percorso | Critico? | Backup frequenza |
|---|---|---|---|
| **Config** | `~/.openclaw/openclaw.json` | 🔴 Critico | Ogni modifica |
| **Agents config** | `~/.openclaw/agents/` | 🔴 Critico | Ogni modifica |
| **Skills** | `~/.openclaw/skills/` | 🟡 Medio | Settimanale |
| **Sessions** | `~/.openclaw/sessions/` | 🟡 Medio | Giornaliero |
| **Workspace** | `/workspace/` | 🔴 Critico | Giornaliero |
| **MEMORY.md** | `/workspace/memory/` | 🔴 Critico | Giornaliero |
| **Cron jobs** | `~/.openclaw/jobs.json` | 🟡 Medio | Settimanale |
| **Credentials** | `~/.openclaw/.env` | 🔴 Critico | Ogni modifica |

### Docker volumes strategy

```yaml
volumes:
  # Named volumes per persistenza
  openclaw-config:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: /opt/openclaw/config

  openclaw-workspace:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: /opt/openclaw/workspace
```

---

## 8. Monitoring e Observability

### Stack di monitoring raccomandato

```
┌─────────────────────────────────────────────────┐
│             OBSERVABILITY STACK                  │
│                                                 │
│  ┌───────────┐    ┌──────────────┐              │
│  │ OpenClaw  │───►│ OTel         │              │
│  │ (OTel     │    │ Collector    │              │
│  │  built-in)│    └──────┬───────┘              │
│  └───────────┘           │                      │
│                 ┌────────┼────────┐             │
│            ┌────▼───┐ ┌──▼─────┐ ┌▼──────────┐ │
│            │Promethe│ │ Loki   │ │  Tempo    │ │
│            │us      │ │(Logs)  │ │ (Traces)  │ │
│            └────┬───┘ └───┬────┘ └─────┬─────┘ │
│                 │         │            │        │
│            ┌────▼─────────▼────────────▼───┐    │
│            │          Grafana              │    │
│            │    (Dashboard + Alerting)     │    │
│            └───────────────────────────────┘    │
└─────────────────────────────────────────────────┘
```

### Metriche chiave da monitorare

| Categoria | Metrica | Soglia alert |
|---|---|---|
| **System** | CPU usage | > 80% per 5 min |
| **System** | Memory usage | > 85% |
| **System** | Disk usage | > 90% |
| **Gateway** | Active sessions | > max_sessions * 0.8 |
| **Gateway** | WebSocket connections | Anomalia +50% |
| **Agent** | Token usage/ora | > budget_limit |
| **Agent** | Costo giornaliero | > $X/giorno |
| **Agent** | Error rate | > 5% |
| **Agent** | Response latency P95 | > 5s |
| **Model** | API error rate | > 2% |
| **Model** | Rate limit hits | > 10/min |
| **Cron** | Job failure rate | > 0 |
| **Queue** | Queue depth | > 100 |

### OpenTelemetry built-in

OpenClaw esporta nativamente telemetria via OpenTelemetry:

- **Traces**: model usage, webhook processing, message flow
- **Metrics**: token counters, cost histograms, context size, run duration, session state
- **Logs**: strutturati, con correlation ID

```json
{
  "telemetry": {
    "enabled": true,
    "exporter": "otlp",
    "endpoint": "http://otel-collector:4317",
    "serviceName": "openclaw-vendiamonoi",
    "sampleRate": 1.0
  }
}
```

### Grafana dashboard essenziali

1. **Agent Overview**: sessioni attive, token/ora, costo cumulativo
2. **Performance**: latenza P50/P95/P99, throughput, error rate
3. **Cost Tracker**: costo per agente, per modello, per giorno
4. **Infrastructure**: CPU, RAM, disco, network per nodo
5. **Alerts**: pannello con alert attivi e storico

---

## 9. Logging e Alerting

### Logging strutturato

```json
{
  "logging": {
    "level": "info",
    "format": "json",
    "output": "stdout",
    "fields": {
      "service": "openclaw",
      "environment": "production",
      "instance": "gateway-1"
    }
  }
}
```

### Livelli di log

| Livello | Quando | Esempio |
|---|---|---|
| **error** | Errori che richiedono azione | API key scaduta, crash agente |
| **warn** | Situazioni anomale | Rate limit vicino, retry |
| **info** | Operazioni normali | Sessione creata, task completato |
| **debug** | Solo per troubleshooting | Payload completi, state changes |

### Alerting tiers

```
Tier 1 (CRITICAL) ──► PagerDuty / SMS / Chiamata
  - Gateway down
  - Tutti gli agenti in errore
  - Costo > soglia giornaliera
  - Disco > 95%

Tier 2 (WARNING) ──► Slack / Telegram
  - Error rate > 5%
  - Latenza P95 > 10s
  - Agent session stuck > 30 min
  - Rate limit hit

Tier 3 (INFO) ──► Dashboard / Log
  - Nuova sessione creata
  - Update completato
  - Backup completato
```

---

## 10. Backup e Disaster Recovery

### Strategia due livelli

```
Livello 1: Backup incrementali frequenti
─────────────────────────────────────────
Frequenza: ogni 6 ore
Cosa: config, agents, workspace, sessions
Metodo: tar + rsync a storage remoto
Retention: 30 giorni

Livello 2: Snapshot completi
─────────────────────────────────────────
Frequenza: giornaliero (notte)
Cosa: intero host / volume Docker
Metodo: snapshot provider (Hetzner, AWS)
Retention: 90 giorni
```

### Script backup automatico

```bash
#!/bin/bash
# /opt/openclaw/scripts/backup.sh

TIMESTAMP=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/opt/backups/openclaw"
REMOTE="s3://vendiamonoi-backups/openclaw"

# 1. Backup config (critico)
tar czf ${BACKUP_DIR}/config_${TIMESTAMP}.tar.gz \
  /opt/openclaw/config/

# 2. Backup workspace (critico)
tar czf ${BACKUP_DIR}/workspace_${TIMESTAMP}.tar.gz \
  /opt/openclaw/workspace/

# 3. Backup SQLite (zero-downtime con .backup)
sqlite3 /opt/openclaw/config/sessions.db ".backup '${BACKUP_DIR}/sessions_${TIMESTAMP}.db'"

# 4. Sync a remote storage
aws s3 sync ${BACKUP_DIR}/ ${REMOTE}/ --delete

# 5. Cleanup locale (mantieni 7 giorni)
find ${BACKUP_DIR} -type f -mtime +7 -delete

# 6. Log
echo "[$(date)] Backup completato: config + workspace + sessions" >> /var/log/openclaw/backup.log
```

### Cron per backup automatico

```bash
# /etc/cron.d/openclaw-backup
0 */6 * * * root /opt/openclaw/scripts/backup.sh
0 3 * * * root /opt/openclaw/scripts/full-snapshot.sh
```

### RTO / RPO targets

| Metrica | Target Vendiamonoi | Come |
|---|---|---|
| **RPO** (max data loss) | 6 ore | Backup incrementali ogni 6h |
| **RTO** (max downtime) | 30 minuti | Docker compose up + restore |
| **Recovery test** | Mensile | Drill di restore su ambiente test |

### Procedura di restore

```bash
# 1. Stop servizi
docker compose down

# 2. Restore config
tar xzf /opt/backups/openclaw/config_LATEST.tar.gz -C /

# 3. Restore workspace
tar xzf /opt/backups/openclaw/workspace_LATEST.tar.gz -C /

# 4. Restore sessions DB
cp /opt/backups/openclaw/sessions_LATEST.db /opt/openclaw/config/sessions.db

# 5. Restart
docker compose up -d

# 6. Verifica health
curl -f http://localhost:18789/health
```

---

## 11. Update Strategy

### Rolling update (zero downtime)

```
Stato iniziale: [Gateway v1.0] [Gateway v1.0]
                      ▲               ▲
                      └───── LB ──────┘

Step 1: Scale up     [Gateway v1.0] [Gateway v1.0] [Gateway v1.1]
Step 2: Drain old    [Gateway v1.0] [draining...]  [Gateway v1.1]
Step 3: Replace      [Gateway v1.0] [Gateway v1.1] [Gateway v1.1]
Step 4: Drain old    [draining...]  [Gateway v1.1] [Gateway v1.1]
Step 5: Completato   [Gateway v1.1] [Gateway v1.1]
```

### Procedura update sicuro

```bash
#!/bin/bash
# /opt/openclaw/scripts/update.sh

# 1. Pre-update backup
/opt/openclaw/scripts/backup.sh

# 2. Pull nuova immagine
docker compose pull openclaw

# 3. Health check pre-update
curl -f http://localhost:18789/health || { echo "Gateway unhealthy, abort"; exit 1; }

# 4. Rolling restart
docker compose up -d --no-deps openclaw

# 5. Wait for healthy
sleep 10
curl -f http://localhost:18789/health || {
    echo "Post-update health check failed, rolling back"
    docker compose down
    # Restore da backup
    /opt/openclaw/scripts/restore.sh
    docker compose up -d
    exit 1
}

# 6. Cleanup
docker image prune -f

echo "[$(date)] Update completato con successo"
```

### Canary deployment (K8s)

```yaml
# Aggiornamento graduale: 10% traffico → 50% → 100%
apiVersion: apps/v1
kind: Deployment
metadata:
  name: openclaw-canary
spec:
  replicas: 1  # 1 pod su nuova versione
  template:
    spec:
      containers:
        - name: openclaw
          image: openclaw/openclaw:v1.1  # nuova versione
```

---

## 12. Sicurezza Production

### Checklist sicurezza deployment

| Area | Azione | Priorità |
|---|---|---|
| **Network** | Bind Gateway solo su localhost (127.0.0.1) | 🔴 Critica |
| **Network** | TLS 1.2+ su tutte le connessioni esterne | 🔴 Critica |
| **Network** | Firewall: solo porte 80, 443 aperte | 🔴 Critica |
| **Auth** | Gateway token forte (64+ char random) | 🔴 Critica |
| **Auth** | Rotazione token periodica (90 giorni) | 🟡 Media |
| **Secrets** | API keys in Docker secrets / Vault | 🔴 Critica |
| **Secrets** | .env MAI nel repository | 🔴 Critica |
| **Container** | Non eseguire come root | 🟡 Media |
| **Container** | Read-only filesystem dove possibile | 🟡 Media |
| **Container** | Resource limits (CPU, RAM) | 🔴 Critica |
| **Updates** | Aggiornamenti sicurezza settimanali | 🟡 Media |
| **Audit** | Logging di tutte le azioni agente | 🟡 Media |
| **Backup** | Backup crittografati | 🟡 Media |
| **HSTS** | Header Strict-Transport-Security | 🟡 Media |

### Docker secrets

```yaml
# docker-compose.yml con secrets
services:
  openclaw:
    secrets:
      - gateway_token
      - anthropic_api_key
    environment:
      - GATEWAY_TOKEN_FILE=/run/secrets/gateway_token
      - ANTHROPIC_API_KEY_FILE=/run/secrets/anthropic_api_key

secrets:
  gateway_token:
    file: ./secrets/gateway_token.txt
  anthropic_api_key:
    file: ./secrets/anthropic_api_key.txt
```

### Network policy (K8s)

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: openclaw-network-policy
spec:
  podSelector:
    matchLabels:
      app: openclaw
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: nginx-ingress
      ports:
        - port: 18789
  egress:
    - to: []  # Consenti traffico in uscita (API calls)
      ports:
        - port: 443  # HTTPS per API Anthropic/OpenAI
        - port: 6379  # Redis
```

---

## 13. Costi Infrastruttura

### Costi per scenario

| Scenario | Provider | Spec | Costo/mese |
|---|---|---|---|
| **Starter** | Hetzner CX31 | 4 CPU, 8GB, 80GB SSD | €8 |
| **Growth** | Hetzner CX41 | 8 CPU, 16GB, 160GB SSD | €15 |
| **Pro** | Hetzner CX51 + Redis | 16 CPU, 32GB, 240GB | €35 |
| **Enterprise** | K8s cluster (3 nodi) | 24+ CPU, 48+ GB | €100-200 |

### Costi totali stimati Vendiamonoi (tutto incluso)

| Componente | Costo/mese |
|---|---|
| **Server** (Hetzner CX41) | €15 |
| **Dominio** (openclaw.vendiamonoi.it) | €1 |
| **SSL** (Let's Encrypt) | €0 |
| **Backup storage** (S3/Hetzner Storage Box) | €3-5 |
| **Redis** (incluso nel server) | €0 |
| **Monitoring** (self-hosted Grafana) | €0 |
| **API AI** (vedi settore 4 + 11) | €50-106 |
| **Totale infrastruttura** | **€19-21/mese** |
| **Totale con API AI** | **€69-127/mese** |

### Confronto con alternative

| Soluzione | Costo/mese | Pro | Contro |
|---|---|---|---|
| **Self-hosted (Docker)** | €19-21 + API | Controllo totale, economico | Manutenzione |
| **OpenClaw.rocks (Managed)** | €30-100 + API | Zero ops | Meno controllo |
| **AWS / GCP** | €50-200 + API | Enterprise, SLA | Costo alto |
| **DigitalOcean App Platform** | €25-60 + API | Facile, scaling | Lock-in |

---

## 14. Configurazione Vendiamonoi

### Architettura production raccomandata

```
┌─────────────────────────────────────────────────────┐
│           VENDIAMONOI PRODUCTION STACK              │
│                                                     │
│  Internet ──► Cloudflare (DNS + DDoS) ──►           │
│                                                     │
│  ┌────────────────────────────────────────────┐     │
│  │  Hetzner CX41 (8 CPU, 16GB RAM)           │     │
│  │                                            │     │
│  │  ┌──────────────────────────────────────┐  │     │
│  │  │  Docker Compose                      │  │     │
│  │  │                                      │  │     │
│  │  │  ┌──────────┐  ┌──────────────────┐  │  │     │
│  │  │  │  Nginx   │──│  OpenClaw GW     │  │  │     │
│  │  │  │  :443    │  │  :18789 (local)  │  │  │     │
│  │  │  │  SSL/TLS │  │  2 CPU, 2GB      │  │  │     │
│  │  │  └──────────┘  └──────────────────┘  │  │     │
│  │  │                                      │  │     │
│  │  │  ┌──────────┐  ┌──────────────────┐  │  │     │
│  │  │  │  Redis   │  │  OTel Collector  │  │  │     │
│  │  │  │  :6379   │  │  :4317           │  │  │     │
│  │  │  │  256MB   │  │                  │  │  │     │
│  │  │  └──────────┘  └──────────────────┘  │  │     │
│  │  │                                      │  │     │
│  │  │  ┌──────────┐  ┌──────────────────┐  │  │     │
│  │  │  │Prometheus│  │  Grafana         │  │  │     │
│  │  │  │  :9090   │  │  :3000           │  │  │     │
│  │  │  └──────────┘  └──────────────────┘  │  │     │
│  │  └──────────────────────────────────────┘  │     │
│  │                                            │     │
│  │  Backup: ogni 6h → Hetzner Storage Box     │     │
│  └────────────────────────────────────────────┘     │
│                                                     │
│  Integrazioni:                                      │
│  OpenClaw ──► Make/n8n (automazioni)                │
│  OpenClaw ──► Shopify API                           │
│  OpenClaw ──► ChannelEngine API                     │
│  OpenClaw ──► Telegram Bot (notifiche)              │
│  OpenClaw ──► Notion API (knowledge base)           │
└─────────────────────────────────────────────────────┘
```

### Costi totali progetto OpenClaw Vendiamonoi

| Voce | Costo/mese | Note |
|---|---|---|
| Infrastruttura (server + backup) | ~€20 | Hetzner CX41 + Storage Box |
| API AI — Fase 1 (single agent) | ~€50 | Opus + Haiku subagent |
| API AI — Fase 4 (full team) | ~€106 | 7 agenti, piramide modelli |
| MCP infra (da settore 7) | ~€0-25 | Server MCP self-hosted |
| Voice (da settore 10) | ~€0-32 | Se attivato |
| Automazioni (da settore 8) | ~€17-37 | Cron + webhooks + n8n |
| **Totale Fase 1** | **~€87/mese** | Single agent + infra base |
| **Totale Fase 4 (full)** | **~€200-220/mese** | Full team + tutto attivo |

### Roadmap deployment

| Fase | Settimana | Azione | Risultato |
|---|---|---|---|
| **0** | S1 | Setup server Hetzner + Docker | Infra pronta |
| **1** | S1-2 | Deploy OpenClaw base + Nginx + SSL | Gateway operativo |
| **2** | S2 | Configurazione agente principale (Opus) | Primo agente |
| **3** | S3 | Setup monitoring (Prometheus + Grafana) | Observability |
| **4** | S3-4 | Backup automatico + test restore | DR pronto |
| **5** | S4+ | Aggiunta worker agent (settore 11) | Multi-agent |
| **6** | S6+ | Integrazioni Make/n8n + marketplace | Produzione piena |

### Priorità implementazione

1. **CRITICA** — Server + Docker + Nginx + SSL + Gateway token
2. **ALTA** — Backup automatico (6h + giornaliero)
3. **ALTA** — Monitoring base (Prometheus + Grafana)
4. **MEDIA** — Redis per session state (prepara scaling)
5. **MEDIA** — Alerting (Telegram per warning, email per critical)
6. **BASSA** — Kubernetes migration (solo quando > 50 agenti)
7. **BASSA** — Canary deployment (solo quando update frequenti)

---

## Fonti

- [OpenClaw Docs — Kubernetes](https://docs.openclaw.ai/install/kubernetes)
- [OpenClaw Docs — Gateway Architecture](https://docs.openclaw.ai/concepts/architecture)
- [OpenClaw Docs — Trusted Proxy Auth](https://docs.openclaw.ai/gateway/trusted-proxy-auth)
- [OpenClaw Docs — CLI Backup](https://docs.openclaw.ai/cli/backup)
- [Deploy OpenClaw with Docker (CyberNews)](https://cybernews.com/best-web-hosting/how-to-deploy-openclaw-with-docker/)
- [OpenClaw Docker & Kubernetes Production (Yotta Labs)](https://www.yottalabs.ai/post/how-to-deploy-openclaw-in-production-docker-kubernetes-and-gpu-infrastructure)
- [Running OpenClaw at Scale: HA Clustering (LumaDock)](https://lumadock.com/tutorials/openclaw-high-availability-clustering)
- [OpenClaw Multi-Instance Deployment](https://oepnclaw.com/en/tutorials/openclaw-multi-instance.html)
- [Unified Observability for OpenClaw (UBOS)](https://ubos.tech/unified-observability-for-openclaw-on-ubos-combining-opentelemetry-tracing-prometheus-metrics-and-grafana-dashboards/)
- [OpenClaw Backup & Disaster Recovery (DEV)](https://dev.to/clawsetup/openclaw-backup-disaster-recovery-on-hetzner-rporto-restore-drills-and-practical-failover-for-1c4e)
- [OpenClaw Update Without Downtime](https://www.openclawexperts.io/guides/setup/how-to-update-openclaw-without-downtime)
- [Deploying OpenClaw on AWS (Medium)](https://jeevabyte.medium.com/deploying-openclaw-ai-on-aws-a-complete-infrastructure-guide-6b7efc6c6c55)
- [OpenClaw Security: Architecture & Hardening (Nebius)](https://nebius.com/blog/posts/openclaw-security)
- [DigitalOcean — Run OpenClaw](https://www.digitalocean.com/community/tutorials/how-to-run-openclaw)
