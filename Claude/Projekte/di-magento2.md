---
repo_remote: github.com/RedaktionsNetzwerk-Deutschland/di-magento2
shops: []
shopsystem: magento2
tags: [projekt, docker, base-image, php]
aktualisiert: 2026-11-21
---
# di-magento2

## Zweck
- **Wird umbenannt in `martech-team-di-magento2`** (entschieden 2026-11-21, noch nicht umgesetzt); danach `repo_remote` anpassen
- Baut das Magento-2.4.7-Base-Image, auf dem alle Shop-Images der Gruppe aufsetzen
- Ergebnis: `thecustomsolutions/magento2:2.4.7-p10-82` (8.2-Linie) bzw. 8.3-Linie; die Shop-Repos bauen darauf ihr eigenes `thecustomsolutions/shop-*`-Image

## Pfade
- Repo lokal: `/Users/rico/Development/di-magento2`
- GitHub: `github.com/RedaktionsNetzwerk-Deutschland/di-magento2` (Org RedaktionsNetzwerk-Deutschland)

## Architektur
- Dockerfile baut von `FROM thecustomsolutions/php-fpm:<php>` (Base-Image aus separatem Repo); installiert nginx + Build-Tools, Composer 2.6.6, n98-magerun2, cron
- Magento-Quellen per `COPY magento2 /var/www`, danach monatliche Security-Patches aus `/m2-patches/*.patch` (`247p10-2026-<MM>-001-CE.patch`) + `calendar_icu.patch`, `fix_addcomment.patch`, `category_tree_lazyload.patch`
- PHP-Config: `.docker/php/php.ini` → `conf.d/99-settings.ini` (memory_limit 2560M [ABO-1589], upload/post 20M, `expose_php Off`, `cgi.fix_pathinfo 0`); `.docker/php/php.conf` → FPM-Pool `www.conf` (Pool `[www]`, `pm=ondemand`, listen 127.0.0.1:9000)
- Sessions in Redis (laut Entrypoint)
- `category_tree_lazyload.patch`: Admin-Kategoriebaum lädt Unterkategorien erst bei Bedarf (wie 2.4.8) gegen „Redis session exceeded concurrent connections" (magento2#38851); beim Update auf 2.4.8 entfernen
- Branch-/Build-Modell: pro Magento-Version + PHP-Variante ein Build-Branch; der Workflow `.github/workflows/docker-image.yml` baut bei Push/PR auf den Branch der jeweiligen Linie:
  - `magento-2.4.7-p10` → PHP 8.3 (aktuelle Haupt-Linie, Stand 2026-11)
  - `magento-2.4.7-p10-82` → PHP 8.2
  - monatliche Patch-Branches `ABO-xxxx-Patch-2.4.7-p10-<Monat>-2026[-83]`
- Prod: Images werden nach AWS ECR gespiegelt (Account 981570960232, eu-central-1) und auf ECS betrieben. Docker-Hub-Tag ≠ ECR-Kopie ≠ laufende Task — Rollout über [[aws-services]] bzw. `php -v` im laufenden Container verifizieren (aus Session 2026-10-21)

## Abhängigkeiten / Verknüpfungen
- Base-Image `thecustomsolutions/php-fpm:<php>` kommt aus [[di-php]] (dort u.a. redis-Extension)
- Alle Shop-Images (`shop-m2-madsack`, `shop-mediengruppe-bayern`, `shop-hildesheim-sub` ([[shop-hildesheim-sub]]), `shop-rnz`, `shop-ovb-abo`, `shop-dumont-v2`, `shop-m2-cz`, `shop-heilbronner-stimme`, …) bauen auf dem hier erzeugten Magento-Image auf
- [[aws-services]] prüft, welche ECR-Images/Tasks mit welcher PHP-Version laufen
- `ecom-middleware` und die WordPress-Images sind eigene Repos (PHP 8.5 bzw. Apache/mod_php), nicht von di-magento2 abgeleitet
- [[Server und Sicherheit]], [[Magento]]

## Fallstricke
- Default-Branch `master` ist der **Nightly-Orchestrator** (geprüft 2026-11-21): enthält nur `create_project.sh` (Magento per `composer create-project` von repo.magento.com) und je Linie `.github/workflows/build-magento-<version>.yml` mit `schedule` (2.4.6-p15-81 02:17, 2.4.6-p15 02:27, 2.4.7-p10-82 03:06, 2.4.7-p10 03:17 UTC), die den Versions-Branch auschecken, bauen und **ohne Test** direkt pushen. → 2.4.6-p15 (EOL) wird weiterhin jede Nacht gebaut. Aktiv sind die Versions-Branches; dazu viele Alt-Branches (2.2.x, 2.3.x, 2.4.6-p10/p15, 2.4.7-p9) und erledigte Ticket-Branches (Stand 2026-11-21)
- Build nur `linux/amd64`; auf Apple Silicon emuliert und langsam. Häufiger Abbruch ist nicht das Dockerfile, sondern die volle Docker-VM (`E: You don't have enough free space in /var/cache/apt/archives/`) → `docker builder prune` / `docker image prune -a` (aus Session 2026-10-21)
- redis-Extension kam früher per `pecl install redis`; PECL liefert bei weltweiten Massen-Rebuilds zeitweise HTTP 500. Robuster: phpredis-Tarball von GitHub + `docker-php-ext-install redis`. Gilt fürs php-fpm-Base-Image; im aktuellen di-magento2-Dockerfile keine eigene redis-Zeile (aus Session 2026-10-21)
- Nach PHP-Neubau `docker build --pull`, sonst nimmt Docker das alte Base-Image aus dem Cache
- IAM-User `S3CfLogs` darf kein `ecr:DescribeImages`; ECS Exec nicht aktiviert → Remote-Abfrage der laufenden PHP-Version eingeschränkt (Weg: siehe [[aws-services]])

## Sicherheit: PHP2Shell / ABO-1728 (21.10.2026)
- CVE-2026-109618 / -109619 (Sansec „php2shell"), kritische RCE über PHP-Memory/Upload-Progress. Eigentlicher Fix = PHP-Update; erste gepatchte Releases 8.2.35 / 8.3.36 / 8.4.27 / 8.5.12
- Stopgap-Härtung: php.ini `session.upload_progress.enabled = 0`, `session.use_strict_mode = 1`; zusätzlich als `php_admin_flag` im FPM-Pool, damit nicht per `ini_set()` reaktivierbar
- Liegt auf Branch `ABO-1728-Critical-PHP` (Commit e0bd8b6) / 8.2-Linie. **?** Im aktuellen Haupt-Branch `magento-2.4.7-p10` (8.3) nicht in php.ini/php.conf — unklar, ob bewusst weggelassen (8.3.x ohnehin gepatcht) oder nachzuziehen
- Alle produktiven Shop-Images am 21.10. neu gebaut und am laufenden Container (u.a. Madsack-Prod) als 8.2.35 verifiziert

## Offene Punkte
- **MRT-108** (angelegt 2026-11-21, Backlog): Versions-Branches durch `main` mit Build-Matrix ersetzen (nach Pilot MRT-107); vorab `master` → `main` umbenennen (Admin)
- **Entscheidung 2026-11-21:** `entrypoint.sh`, `env.dist.php` (→ `app/etc/env.php`) und `magento2-prepare.sh` gehören ins Magento-Basis-Image, **für beide PHP-Versionen**; Shops überschreiben nur bei Bedarf, bei identischem Inhalt nicht nötig. Prüfung lokaler Shop-Repos (2026-11-21): alle Shops auf `:2.4.7-p10-82` (u. a. `shop-dumont-abo-v2`, `shop-rnz-v2`, `shop-madsack-abo`, `shop-stimme`, `shop-mgb-abo`, `shop_ovb_abo`, `shop-celle-sub`, `shop-magento-demo`) haben eigenes `ENTRYPOINT /entrypoint.sh` + eigene `env.php` → Änderung am 8.2-Basis-Image wirkt dort nicht; Shop-eigene Kopien könnten später entfallen, wenn identisch
- Magento **2.4.6-p15 wird nicht mehr benötigt** (EOL, bestätigt 2026-11-21) → Branches `magento-2.4.6-p15`, `magento-2.4.6-p15-81` und ältere nur noch archivieren
- **?** PHP2Shell-Stopgap-Härtung in der 8.3-Linie bewusst nicht übernommen oder nachzuziehen? – Befund 2026-11-21 (lokaler Stand, Fetch 2026-11-09): sieht nach **Drift** aus, nicht nach Absicht. `magento-2.4.7-p10-82` hat die Härtung in `php.ini`/`php.conf`, `magento-2.4.7-p10` nicht; umgekehrt hat nur die 8.3-Linie `entrypoint.sh`, `env.dist.php` und `magento2-prepare.sh` im Image. Sonst unterscheiden sich die Linien nur in `FROM php-fpm:8.2/8.3` und dem Image-Tag
- ecom-middleware: php_admin_flag-PR (ABO-1728-PHP2Shell-php-admin-flag, 910b74a) gemergt; eigene Notiz: [[ecom-middleware]]
