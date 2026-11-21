---
tags: [kern, setup]
aktualisiert: 2026-11-21
---
# Entwicklungsumgebung

## System
- macOS (Apple Silicon)
- Lokale Magento-Instanzen in Docker, eigenes `docker compose` (kein Warden/DDEV)
- Module werden **nicht gemountet**, sondern zum Testen in die Container kopiert (Docker-Mount-Einschränkungen unter macOS)
- Viele Module parallel, je nach Projekt in unterschiedlichen lokalen Magento-Instanzen getestet
- Shop-Projekt: `/Users/rico/Development/madsack-m2-shop/magento2/www`

## IDE
- PHPStorm: jedes Modul eigenes Projekt, Magento-Instanz als separates Projekt
- Claude Code als JetBrains-Plugin und in der macOS-App (eine Umgebung pro Projekt, ein Chat pro Aufgabe)

Siehe auch [[Claude-Setup]], [[Magento]], [[Testing]].

## Ordnerstruktur ~/Development (seit 2026-11-21)
- Aktive GitHub-Repos **flach** direkt unter `~/Development` – `magento-dev` erwartet die Shop-Repos dort (`DEV_ROOT`, `SRC_DIR=shop-…/magento2/www`), Plugin-Marketplace auf `~/Development/magento-dev`
- Nach Umbenennung auf `martech-team-<art>-*` sortieren sie sich von selbst nach Art
- `_archiv/` – Ordner ohne Git, `*_old`, Dubletten, Loses (nur verschoben, nichts gelöscht)
- `_bitbucket/` – Bitbucket-Vorgänger heutiger GitHub-Repos (Bitbucket wird aufgelöst)
- `_extern/` – Fremd-Repos (z. B. `burp-payloads`)
- Noch nicht migrierte Bitbucket-Repos (`module-kdballdie`, `module-evolver-sso`, `module-externalorder`, `module-static-bundle`, `codeception-madsack-m2-shop`) und kleine lokale Tools ohne Git (`compare`, `nwz_plz`) liegen flach
