---
repo_remote: github.com/RedaktionsNetzwerk-Deutschland/ecom-middleware
shops: []
shopsystem: shopify
tags:
  - projekt
  - middleware
  - symfony
  - docker
  - php
aktualisiert: 2026-11-20
---
# ecom-middleware

## Zweck
- Symfony-basierte Middleware mit EasyAdmin-Oberfläche; verarbeitet u.a. Shopify-Webhooks (JSON)
- **?** genauer Datenfluss/Integrationen aus dieser Session nicht vollständig belegt — bei Import der weiteren ecom-middleware-Sessions ergänzen

## Pfade
- Repo lokal: `/Users/rico/Development/ecom-middleware`
- GitHub: `github.com/RedaktionsNetzwerk-Deutschland/ecom-middleware` (Org RedaktionsNetzwerk-Deutschland), Arbeit per PRs (zuletzt #125)
- Lokaler Dev-Stack: `docker-middleware/` (docker-compose + DB-Dumps `dump_*.sql`)

## Architektur
- Symfony-App (EasyAdmin-Bundle), PHP 8.5, `FROM php:8.5-fpm-trixie`
- nginx davor (`.docker/webserver/001_default.conf`); php.ini → `conf.d/zz-app.ini`; FPM-Pool `.docker/php/php.conf` → `www.conf`; Sessions in Redis
- PHP-Extensions: intl, pdo, pdo_mysql, soap, redis
- redis-Extension: phpredis 6.3.0 aus GitHub-Release-Tarball (`ARG PHPREDIS_VERSION`/`PHPREDIS_SHA256`, `docker-php-ext-install`) statt `pecl install redis` — weil pecl.php.net bei weltweiten Massen-Rebuilds 504/500 liefert (Commit 8b2ac00 auf main, 2026-10-21)
- CI/Deploy (Images `thecustomsolutions/ecom-middleware:staging` / `:production`, zusätzlich AWS ECR):
  - `.github/workflows/update-staging.yml` „Push on Staging": Cron 03:14 + `workflow_dispatch`, Build, `composer audit`, Push Docker Hub + ECR, dann `aws ecs update-service --force-new-deployment` (Cluster `MARTECH_MOL_CLUSTER_NAME` = `mol-cluster-production`)
  - `.github/workflows/update-main.yml` „Push on Production": `workflow_dispatch`, Push Docker Hub + ECR

## Abhängigkeiten / Verknüpfungen
- Läuft auf AWS ECS, Cluster `mol-cluster-production` → [[aws-services]]
- Eigenes Base-Image, nicht von [[di-magento2]] abgeleitet (PHP 8.5, Symfony), aber dieselbe PHP2Shell-Härtungsstrategie
- [[Server und Sicherheit]]

## Fallstricke
- Build hängt nicht mehr an pecl.php.net (redis via GitHub-Tarball). Bei neuer phpredis-Version `ARG`-Version **und** SHA256 im Dockerfile anpassen, sonst bricht die Prüfsumme den Build
- Base-Image-Tag `php:8.5-fpm-trixie` ist kein fester Patch-Stand; nach einem PHP-Release zieht Docker Hub den Tag erst mit Stunden Verzug nach → nach Patch per `workflow_dispatch` neu bauen (nicht den Cron abwarten), danach `php -v` prüfen. Ein Pin auf `php:8.5.x-fpm-trixie` schlägt fehl, solange das Tag-Image noch nicht für amd64 existiert
- `--no-cache` hilft **nicht** gegen ein veraltetes Base-Image: Runner ist `ubuntu-latest` (frische VM, kein Layer-Cache), das Base wird ohnehin frisch gezogen; das Problem ist der Tag selbst
- Härtungswerte in CLI und FPM getrennt prüfen — maßgeblich ist FPM: `docker exec <c> php-fpm -i | grep -E 'file_uploads|upload_progress|use_strict_mode'`

## Sicherheit: PHP2Shell / ABO-1728 (21.10.2026)
- php.ini `file_uploads = Off`, `session.upload_progress.enabled = 0`, `session.use_strict_mode = 1` (Commit 1fd926a, Branch `php-Security`) — stärker gehärtet als die Shops, weil die App keine Uploads braucht; `use_strict_mode` greift bei Redis-Sessions allerdings nicht
- FPM-Pool: dieselben drei Werte zusätzlich als `php_admin_flag` (PR `ABO-1728-PHP2Shell-php-admin-flag`, Commit 910b74a, gemergt)
- nginx: `if ($http_content_type ~* "multipart") { return 403; }` in `001_default.conf`
- Staging/Prod laufen seit 21.10. auf gepatchtem PHP 8.5.12

## Verlauf: composer audit / Dependency-Security-Fixes
- Neben `main` gibt es einen Branch `staging`. Dependency-/Security-Fixes gehen per PR vom selben Branch **sowohl nach `staging` als auch nach `main`** (zwei PRs). `composer audit` läuft seit ABO-1675 in der CI und bricht den Build bei neuen Advisories
- Früheres gleichartiges Muster: ABO-1781 (composer audit: Symfony 7.4.23, phpunit 13.4.6, PR #132)
- **2026-11-19 – easycorp/easyadmin-bundle 5.6.1 → 5.10.2:** Am 17.11.2026 erschienen 12 neue Advisories für easyadmin-bundle < 5.10.2 (u.a. `access_control`-Umgehung über `routeName`, Stored XSS, Rechte-Umgehung über `setPermissions()`), dadurch scheiterte `composer audit` im Build. Fix: Commit 60cc5e6 „composer audit: easycorp/easyadmin-bundle 5.10.2“ auf Branch `Update-Easyadmin-Bundle`, nur EasyAdmin in `composer/composer.lock` aktualisiert, keine weiteren Pakete. Gemergt als PR #137 → `staging` (2026-11-19 16:08 UTC) und PR #138 → `main` (16:17 UTC). Der Branch ist erledigt (nicht weiter darauf committen, lokal beim nächsten Touch aufräumen)

## Offene Punkte
- **?** nginx-Multipart-Sperre: Code-Kommentar sagt „Remove once the image runs a patched PHP". PHP läuft inzwischen auf 8.5.12, die Sperre steht aber noch — entfernen oder bewusst als Defense-in-Depth behalten?
- AWS-WAF-Managed-Rules für die CloudFront-Distribution prüfen (aus ABO-1675, noch offen)
