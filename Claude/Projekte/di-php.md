---
repo_remote: github.com/RedaktionsNetzwerk-Deutschland/di-php
shops: []
shopsystem: magento2
tags: [projekt, docker, base-image, php]
aktualisiert: 2026-11-20
---
# di-php

## Zweck
- Baut das eigene PHP-Base-Image der Gruppe (`php-fpm`) für mehrere PHP-Versionen, je eine pro Branch
- Ersetzt das alte Repo `php-fpm` (vorher Bitbucket Pipelines; Vorlage war dessen Branch `php8.2` unter `/Users/rico/Development/php-fpm`)
- Zielort sehr wahrscheinlich `thecustomsolutions/php-fpm` (Beleg: [[di-magento2]] baut `FROM thecustomsolutions/php-fpm:8.3`); der genaue Wert steckt in der Repo-Variable `IMAGE_NAME`, nicht direkt einsehbar

## Pfade
- Repo lokal: `/Users/rico/Development/di-php`
- GitHub: `github.com/RedaktionsNetzwerk-Deutschland/di-php` (Org RedaktionsNetzwerk-Deutschland)

## Architektur
- Branch-Modell: ein Branch je PHP-Version (`php8.2`, `php8.3`, …); `main` trägt README + Nightly-Workflow
- `Dockerfile` je php*-Branch: `FROM php:<version>-fpm` (offizielles Image), `LABEL maintainer`, `ARG DEBIAN_FRONTEND`, Composer via `COPY --from=composer:2`, `apt-get dist-upgrade`
  - Laufzeit-Pakete nur `git`, `unzip`, `imagemagick`; Build-Abhängigkeiten (`$PHPIZE_DEPS`, `-dev`-Pakete) werden danach wieder entfernt
  - Extensions in einem Aufruf: `docker-php-ext-install -j$(nproc) bcmath gd intl pdo_mysql soap sockets xsl exif zip`; zusätzlich `pecl install redis imagick` + `docker-php-ext-enable`
  - `imagick` wegen Magento (Backend-Image-Adapter „ImageMagick")
- CI je php*-Branch: `.github/workflows/docker-publish.yml` — bei Push baut GitHub Actions, Tag wird aus dem Branch-Namen abgeleitet (`php8.2` → `:8.2`), `pull: true`, Login über `MARTECH_DOCKER_HUB_USER`/`PASS`, dann Push zu Docker Hub. Smoke-Test vor dem Push: `php -v`, alle 12 Extensions geladen (bcmath, ctype, gd, intl, pdo_mysql, soap, sockets, xsl, exif, zip, redis, imagick), `composer --version`
- Nightly auf `main`: `.github/workflows/nightly.yml` — `cron '0 3 * * *'` + `workflow_dispatch`, sucht per `gh api` alle `php*`-Branches und startet auf jedem `docker-publish.yml` (künftige Branches wie `php8.4` automatisch dabei). `pull: true` + `dist-upgrade` → jeder Nachtbuild zieht frisches Base-Image + Sicherheitsupdates

## Abhängigkeiten / Verknüpfungen
- Liefert das Base-Image, auf dem [[di-magento2]] aufsetzt (`FROM thecustomsolutions/php-fpm:<php>`)
- Docker-Hub-Secrets `MARTECH_DOCKER_HUB_*` wie bei [[ecom-middleware]] und [[wp-aboplus-karte]]
- [[Server und Sicherheit]]

## Fallstricke
- Image-Größe: Ausgangs-Image war 1,26 GB, nach Abspecken 886 MB (Base `php:8.2-fpm` allein 698 MB). Abspecken über `--no-install-recommends`, Entfernen der Build-Deps, Extensions in einem `docker-php-ext-install`, Composer per `COPY --from=composer:2`, `DEBIAN_FRONTEND` als `ARG` statt `ENV`
- redis kommt hier per `pecl install redis` (ohne Versionspin, aktuell 6.3.0) — der PECL-Ausfall vom 21.10.2026 (504/500 bei Massen-Rebuilds, siehe [[di-magento2]]) war vorübergehend; der GitHub-Tarball-Umweg ist hier nicht nötig
- Übernahme vom alten Repo: `MAINTAINER` → `LABEL`, überzähliges `\` in letzter Zeile entfernt, `-y` bei `autoremove`, nginx-Zeile gestrichen (in diesem Image kein nginx)

## Offene Punkte
- **?** GitHub-Default-Branch ist laut `origin/HEAD` `php8.2`, nicht `main`. Zeitgesteuerte Workflows laufen nur auf dem Default-Branch → der Nightly-Rebuild (`nightly.yml` auf `main`) feuert so vermutlich nicht. Default-Branch auf `main` umstellen
- GitHub-Repo-Voraussetzungen: Secrets `MARTECH_DOCKER_HUB_USER`/`PASS` + Variable `IMAGE_NAME` im Repo oder in der Org vorhanden? (sonst bricht der Build sofort ab) — Stand nicht verifiziert
