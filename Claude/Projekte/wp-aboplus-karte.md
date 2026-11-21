---
repo_remote: github.com/RedaktionsNetzwerk-Deutschland/wp-aboplus-karte
shops: []
shopsystem: wordpress
tags: [projekt, docker, apache, php, redirect, aws]
aktualisiert: 2026-11-21
---
# wp-aboplus-karte

## Zweck
- **Läuft aktuell und wird weiter gebraucht** (bestätigt 2026-11-21), obwohl das alte Portal AboPlus-Karte abgeschaltet ist und nicht reaktiviert wird; alter Bitbucket-Klon `aboplus-karte` lokal gelöscht
- Übergangs-Redirect-Container, der das alte WordPress-Plugin `madsack-sub-check` ersetzt (altes WP unter `/Users/rico/Development/aboplus-karte`, wird nicht mehr gebraucht)
- Produktiv unter `www.aboplus-karte.de`, lokal `aboplus.local`
- Leitet alte Ticket-Links auf das neue Ziel (Eventim / tickets.madsack.de) weiter

## Pfade
- Repo lokal: `/Users/rico/Development/wp-aboplus-karte`
- GitHub: `github.com/RedaktionsNetzwerk-Deutschland/wp-aboplus-karte` (Org RedaktionsNetzwerk-Deutschland), neues Repo (erster Commit f6efb02), Arbeit direkt auf `main`
- Image-Quellen unter `redirect/`; lokaler Dev-Stack: `docker-compose.yml` (Port via `HTTP_PORT`, Default 80)

## Architektur
- Base-Image **`php:8.5-apache`** (bewusst nicht `php:8.5-fpm`: FPM spricht nur FastCGI, bräuchte nginx-Sidecar; Apache liefert HTTP auf Port 80 direkt und loggt nach stdout/stderr)
- `redirect/public/index.php` — Redirect-Logik, nachgebaut aus `madsackSubCheckFollowRedirect()` / `generateRedirectUrl()` des alten Plugins (Template 1:1 `madsack_sub_check_redirect.php`):
  - GET-Param `msc` → `base64_decode` (nicht-strikt, URL-safe + `+`→Leerzeichen-Reparatur) → bei gültiger http/https-URL Redirect, sonst `DEFAULT_URL`
  - User-Agent enthält „firefox" → Meta-Refresh 30 s, sonst 0 s
  - Pfad egal (alter WP-Hook lief bei jeder URL); `/health` → 200 (ALB/ECS); `/favicon.ico`, `/apple-touch-icon*.png` → 204; echte `robots.txt` (`Disallow: /`) + Header `X-Robots-Tag: noindex, nofollow`
- `ENV DEFAULT_URL="https://tickets.madsack.de"`, `ALLOWED_HOSTS="eventim.de,madsack.de"` (per ECS-Task-Def überschreibbar)
- `redirect/apache/redirect.conf`: `ServerName www.aboplus-karte.de`, Alias `aboplus-karte.de aboplus.local`
- Logging nach Vorbild `di-wordpress` (`apache/logFormat.conf`): `mod_remoteip` (nur private Netze als Trusted Proxy), LogFormat `ecs` mit `X-Forwarded-For` vorn (echte Nutzer-IP), Access→stdout, Error→stderr, `/health` + Favicons nicht geloggt
- Deployment: AWS ECS (Fargate) hinter ALB + CloudFront; HTTPS/Zertifikat am ALB, Container nur HTTP:80. Cluster `mol-cluster-production`

## CI/Deploy (`.github/workflows/update-main.yml`)
- Nach Vorbild [[ecom-middleware]]: Secrets `MARTECH_DOCKER_HUB_USER/PASS`, `MARTECH_MOL_CLUSTER_AWS_KEY/SECRET/REGION`, `MARTECH_MOL_CLUSTER_NAME`; Variablen `IMAGE_NAME` (Docker Hub), `AWS_IMAGE` (ECR), `ECS_SERVICE`
- Trigger: Push auf `main`, manuell, **täglich** `cron '0 3 * * *'`
- Build nur amd64, `--pull --no-cache` + `apt-get upgrade` im Dockerfile → immer frisches Base-Image + Debian-Sicherheitsupdates
- Statt `composer audit` ein **Smoke-Test** (Container-Start, `/health`, Redirect für Browser 0 s und Firefox 30 s); schlägt er fehl, wird nichts gepusht/ausgerollt
- Push Docker Hub + ECR (Tags `production`, `production_<run>`), danach `aws ecs update-service --force-new-deployment`
- **Fingerprint-Gate:** geplante Läufe rollen nur bei echter Änderung aus. Fingerabdruck aus Base-Image-Digest + Paketliste (`dpkg -l`) + Git-Commit, als ECR-Tag `fp-<hash>`; ist er am aktuellen `production`-Image schon vorhanden → kein Rollout. Push/manuell rollen immer aus

## Abhängigkeiten / Verknüpfungen
- Läuft auf ECS-Cluster `mol-cluster-production` → [[aws-services]]
- Workflow- und Deploy-Konventionen aus [[ecom-middleware]]
- Logging-Setup aus Repo `di-wordpress` übernommen (kein eigener Vault-Eintrag)
- [[Server und Sicherheit]]

## Fallstricke
- Open-Redirect-Absicherung über `ALLOWED_HOSTS`: subdomain-genau, `eventim.de.evil.example` wird abgelehnt; nur http/https, kein `javascript:`, Ziel-URL im HTML escaped. In Prod unbedingt gesetzt lassen
- Ziel-URL wird escaped ausgegeben (Abweichung vom alten Plugin, das roh ausgab) — gegen XSS; Browser landet trotzdem auf exakt derselben URL
- Erst versuchte CloudFront-IP-Liste (`cloudfront-ranges.php`) wieder verworfen; stattdessen der in ECS bewährte di-wordpress-Ansatz. Ohne CloudFront-Berücksichtigung würde die CloudFront-IP statt der Nutzer-IP geloggt
- Mac ist arm64, Fargate standardmäßig x86_64 → Build `--platform linux/amd64` (der Workflow baut ohnehin amd64)
- Täglicher Rollout erzeugt in ECR viele `production_<run>`-Tags → ECR Lifecycle Policy empfohlen

## Offene Punkte
- AWS-seitig (nicht im Code): ECS Deployment Circuit Breaker + Rollback aktivieren, `minimumHealthyPercent=100`
- ECR Lifecycle Policy (z. B. nur letzte 30 Images) wegen täglichem Rollout
- IAM-User hinter `MARTECH_MOL_CLUSTER_AWS_KEY` braucht `ecr:DescribeImages` (für den Fingerprint-Vergleich)
- **?** GitHub-Repo-Voraussetzungen (Variablen `IMAGE_NAME`/`AWS_IMAGE`/`ECS_SERVICE`, `MARTECH_*`-Secrets freigegeben, ECR-Repo existiert) — Stand nicht verifiziert
