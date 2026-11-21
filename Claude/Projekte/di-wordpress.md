---
repo_remote: github.com/RedaktionsNetzwerk-Deutschland/di-wordpress
shops: []
shopsystem: wordpress
tags: [projekt, docker, base-image, php]
aktualisiert: 2026-11-21
---
# di-wordpress

## Zweck
- **Wird umbenannt in `martech-team-di-wordpress`** (entschieden 2026-11-21, noch nicht umgesetzt); danach `repo_remote` anpassen
- Baut das WordPress-Base-Image `thecustomsolutions/wordpress:<php>` (z. B. `:8.2`, `:8.3`) auf Basis des offiziellen `wordpress:php<version>-apache`
- Gehört zur Repo-Art „Image-Repos“ (mit [[di-php]] und [[di-magento2]])

## Pfade
- Repo lokal: `/Users/rico/Development/di-wordpress`
- GitHub: `github.com/RedaktionsNetzwerk-Deutschland/di-wordpress`

## Architektur (lokaler Stand, letzter Fetch 2026-10-21)
- Default-Branch `master` (letzter Commit 2026-09-17, Merge PR #9 `moreVersions`), Versions-Branches `wordpress-php-82`, `wordpress-php-83` (je 2026-11-17)
- Je Versions-Branch `.github/workflows/docker-image.yml`: Build bei Push + `schedule`-Cron (8.2: 01:11, 8.3: 02:11 UTC), Login `MARTECH_DOCKER_HUB_*`
- Inhalt: `Dockerfile`, `apache/`, `composer/` (wpackagist-Plugins, ACF Pro), `external_plugins/` (u. a. `rnd-cmp-helper.zip`), `php/zz-cve-2026-109618.ini` (PHP2Shell-Stopgap, in beiden Versions-Branches), `deployment_templates/`, `scripts/`, Altlast `bitbucket-pipelines.yml`

## Fallstricke
- **Nightly-Cron feuert vermutlich nicht:** `schedule` läuft bei GitHub nur auf dem Default-Branch (`master`), die Crons liegen aber in den Versions-Branches (gleiches Problem wie bei [[di-php]])
- **Drift zwischen Versions-Branches** (2026-11-21 gesehen): 8.2 hat `limit-login-attempts-reloaded` 3.4.2, 8.3 schon 3.4.3; `wp-optimize` `^4` vs `^4.7`

## Abhängigkeiten / Verknüpfungen
- Basis für WordPress-Projekte wie [[wp-abovorteil]] (? genaue Abhängigkeit nicht geprüft)
