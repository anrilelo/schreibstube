---
repo_remote: github.com/RedaktionsNetzwerk-Deutschland/wp-abovorteil
shops: []
shopsystem: wordpress
tags: [projekt, wordpress, docker, aws]
aktualisiert: 2026-11-20
---
# wp-abovorteil

## Zweck
- WordPress-Projekt „Abovorteil" mit drei Sites/Domains: `abovorteil`, `kn-abovorteil`, `sz-abovorteil` (lokal `*.local`)
- Vorteils-/Abocard-Portal mit Abonnent-Check; Jira-Projekt **MRT**

## Pfade
- Repo lokal: `/Users/rico/Development/wp-abovorteil`
- GitHub: `github.com/RedaktionsNetzwerk-Deutschland/wp-abovorteil`; Default-Branch **`master`** (es gibt kein `main`), zusätzlich `staging`
- Dev-Stack unter `docker-wp-abovorteil/` (gitignored: dumps, uploads, `.env`)

## Architektur
- Eigene Plugins: `kortpress`, `kortpress-xpresso`, `xpresso-viva`, `abocard-coupon`; Theme `mamaso`. SOAP-Aufrufe (WSDL-Portal-Login) in `kortpress-xpresso` und `xpresso-viva`
- Fremd-Plugins: ACF Pro 6.8.11, Limit Login Attempts 3.4.3, Remove Dashboard Access 1.3.1
- Dockerfile mehrstufig: `FROM thecustomsolutions/wordpress:8.3 AS base` (+ `soap`) → `dev` (Xdebug, nur `--target dev`) → `production` (**muss letzte Stage bleiben**; `docker build .` ohne `--target` baut Prod ohne Xdebug). Base-Image nur amd64
- CI (GitHub Actions, Push nach Docker Hub + ECR):
  - `docker-image_staging.yml` „WP-Abovorteil Staging": `ref: staging`, Cron `14 2 * * *`
  - `docker-image-main.yml` „WP-Abovorteil Prod": baut aus **`master`**, bei Push auf `master` + wöchentlich Dienstag 04:14 UTC
- Branch-Strategie: Feature → PR nach `staging` → Test auf Staging → PR nach `master` (= Prod)

## Lokale Dev-Umgebung (MRT-106)
- `bin/dev` (nach Vorbild [[magento-dev]]): `start [--build]`, `stop`, `rebuild [--pull]`, `db-fetch [stag|prod]`, `db-import`, `localize`, `sync`, `wp`, `shell`, `db`, `log(s)`, `status`, `admin`, `xdebug on|off`, `destroy`
- DB-Dumps: `scp mol1_admin:/efs-admin/db_dumps/wpAbovorteil{Stag,Prod}-latest.sql.gz.crypt` + `gpg --decrypt`; Prod-Dump fragt nach (Personendaten). DB auf `127.0.0.1:3308` (3307 belegt magento-dev)
- Plugins/Themes sind **nicht gemountet** (wie Prod aus dem Image) → nach Änderung `bin/dev sync` oder `rebuild`. `abocard-coupon` steht nicht im Dockerfile → `sync` ohne Namen kopiert es nicht

## Fallstricke
- Neue Dumps von mariadb-dump 11.8 beginnen mit einer „sandbox mode"-Zeile, an der der MySQL-8-Client abbricht → `db-import` filtert sie heraus
- `localize` lässt `guid`-Spalten (WordPress-Empfehlung) und ACF-Lizenzdaten bewusst auf den Staging-Domains stehen
- `error-logging.ini` im Image unterdrückt Deprecations/Notices ohne `WORDPRESS_DEBUG=1` — auch auf Staging/Prod; unter PHP 8.3 bricht dadurch nichts
- Base-Image nur amd64 → auf Apple Silicon emuliert, ~6 s/Seite

## Entscheidungen / Historie
- **Prod-Workflow-Bug behoben (inzwischen in `master`):** `docker-image-main.yml` checkte früher `ref: staging` aus → Prod wurde immer aus `staging` gebaut, ungetestete Staging-Stände gingen live (u.a. über den Dienstags-Cron). Fix auf `ref: master`. Reihenfolge war kritisch: `master` musste erst den Staging-Stand (MRT-82, MRT-105) bekommen, sonst hätte der nächste Prod-Build Features zurückgerollt
- **PHP 8.3 (MRT-105):** `base` auf `thecustomsolutions/wordpress:8.3`; eigene Plugins/Theme unter 8.3 geprüft (`php -l`, PHPCompatibility 8.2–8.4, WP_DEBUG-Laufzeittest) — sauber. SOAP-Aufrufe nur statisch geprüft, nicht zur Laufzeit. Stand 2026-11-20: in `master`, Prod auf 8.3

## Abhängigkeiten / Verknüpfungen
- `bin/dev` und DB-Dump-Weg (`mol1_admin:/efs-admin/db_dumps`, gpg) analog [[magento-dev]]
- Base-Image `thecustomsolutions/wordpress` (RND-WordPress-Image-Familie; auch bei PHP2Shell geprüft, siehe [[Server und Sicherheit]])

## Offene Punkte
- Keine aus diesen Sessions — Workflow-Fix und PHP 8.3 sind in `master`, Prod läuft auf 8.3
