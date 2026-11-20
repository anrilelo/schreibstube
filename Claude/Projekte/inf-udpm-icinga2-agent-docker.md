---
repo_remote: github.com/RedaktionsNetzwerk-Deutschland/inf-udpm-icinga2-agent-docker
shops: []
shopsystem: "-"
tags: [projekt, infra, monitoring, icinga2, docker, aws]
aktualisiert: 2026-11-20
---
# inf-udpm-icinga2-agent-docker

## Projektregel
- **In diesem Projekt keine Commits/Pushes/PRs ohne ausdrückliche Ansage des Entwicklers.** Änderungen bleiben im Working Tree (User-Regel, 2026-10-30)

## Zweck
- Docker-Image für den Icinga2-Agent auf RND-Hosts (ca. 22); ersetzt mittelfristig das NRPE-Setup (`inf-icinga2-config`), Migration auf Icinga2-Native-Cluster-Protocol (mTLS, Push-Mode)
- Enthält die Check-Plugins zentral versioniert

## Pfade
- Repo lokal: `/Users/rico/Development/inf-udpm-icinga2-agent-docker`
- GitHub: `github.com/RedaktionsNetzwerk-Deutschland/inf-udpm-icinga2-agent-docker`
- Image: `registry.rndtech.de/icinga2-agent:latest` (private Registry rndtech.de)
- Host-Config: `/opt/docker/icinga2-agent/.env`

## Architektur
- Dockerfile mehrstufig: `FROM docker:cli` + `FROM icinga/icinga2:latest`; Build über **Jenkins** (`Jenkinsfile`, `docker buildx --platform linux/amd64,linux/arm64 --push` → registry.rndtech.de)
- Checks unter `checks/` (viele: `check_docker_*`, `check_haproxy*`, `check_dt_cve.sh`, `check_selenium_login.sh`, `check_cachet_epaper_drift.sh`, …), Hilfslib `checks/lib/http_ua.sh`, Tests unter `tests/`
- `.env` zweifach genutzt: `env_file` injiziert die `ICINGA_*`-Variablen; zusätzlich read-only als `/etc/dt-feeder.env` gemountet (`docker-compose.yml`), weil **Icinga2 die Container-Env nicht an die Check-Skripte vererbt**. `DOCKER_GID` aus der `.env` (Socket-Zugriff)

## Trivy → Dependency-Track Feeder
- `checks/trivy_dt_feeder.sh`: liest laufende Images, erzeugt mit **Trivy** (`aquasec/trivy:0.70.0`, CycloneDX 1.6) eine SBOM und lädt sie nach **Dependency-Track** (`DT_URL` z.B. `https://dt.rndtech.de`, `DT_API_KEY`). Knobs: `MAX_SCANS_PER_RUN` (10), `TRIVY_TIMEOUT` (30m), `TRIVY_IMAGE`, Cache-Volume `trivy-cache`
- `trivy_dt_reconcile.sh` deaktiviert verwaiste Projekte; `check_dt_cve.sh` meldet CVEs
- **ECS-Variante `checks/trivy_dt_feeder_aws_ecs.sh`** (Branch `trivy-AWS-ECS`, Commit 64cd593): nötig, weil das Standard-Skript über `docker ps --format '{{.Image}}'` + `docker image inspect <tag>` auf ECS mit „No such image" scheitert (ECS pullt per Digest / bereinigt / verschiebt Tags). Die ECS-Variante geht über die **Container** (`docker ps -q | docker inspect '{{.Image}}\t{{.Config.Image}}'`): die Image-ID des Containers existiert immer; Projektname aus `.Config.Image`, wobei ECS `repo:tag@sha256:…` setzt → `@digest` abschneiden, reiner Digest ohne Tag → `:latest`

## Fallstricke
- **Trivy „unable to get uncompressed layer" bei großen Images** (z.B. `madsack-m2-shop`) = Root-Volume `/var/lib/docker` der Node voll. Trivy schreibt das Image per `docker save` als Tar aufs Root-Volume. Lösung: EBS vergrößern **oder** Trivy `--image-src remote` (liest Layer direkt aus ECR über die EC2-Instanz-IAM-Rolle; IMDSv2 muss erreichbar sein, ggf. `--network host`) — robuster für sehr große Images
- EBS im Betrieb vergrößern (Elastic Volumes): `modify-volume` → `growpart /dev/nvme0n1 1` → `xfs_growfs -d /`. **Auch die Launch-Vorlage/ASG anpassen**, sonst startet eine ersetzte Node wieder mit der alten Größe. Max. eine Volume-Änderung pro 6 h, Verkleinern unmöglich
- Icinga-Check-Timeout (Default 60 s) ist für Trivy-Erst-Scans zu kurz (DB-Download 1–3 min + pro Image 30 s–Minuten). ~40–60 min Timeout, 1–4 h Intervall — steht in der Icinga-Master-Config, nicht in diesem Repo
- Fehlergründe stehen pro Image unter der Statuszeile; `fail_reason` entfernt Farbcodes, ersetzt `|` durch `/` (sonst Perfdata) und kürzt (ECS-Variante 1000 Zeichen, da Trivy die Ursachen verkettet und die wichtigste zuletzt steht)
- **Bekannter Bug:** der `pruned`-Zähler steigt auch, wenn das Entfernen eines Tags (PATCH) fehlschlägt, weil `api` immer Erfolg meldet

## Abhängigkeiten / Verknüpfungen
- Überwacht u.a. die ECS-Nodes aus [[aws-services]] (madsack-m2-shop, rnd-commerce-hub)
- Dependency-Track (`dt.rndtech.de`), private Registry `registry.rndtech.de`
- [[Server und Sicherheit]]

## Offene Punkte
- **?** `.env`-Pfad-Konsolidierung: Feeder liest Config aus `/etc/dt-feeder.env` (= gemountete Host-`.env`); Wunsch, das zu vereinheitlichen — Zielbild beim Session-Ende offen
- `pruned`-Zähler-Bug (s.o.) nicht behoben
