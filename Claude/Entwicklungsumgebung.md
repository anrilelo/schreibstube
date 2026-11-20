---
tags: [kern, setup]
aktualisiert: 2026-11-20
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
