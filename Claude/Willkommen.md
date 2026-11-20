---
tags: [index]
aktualisiert: 2026-11-20
---
# Willkommen – Claude-Wissensbasis

Zentraler Vault als **Single Point of Truth** für Claude (Chat und Claude Code). Neues Wissen wird sofort hier abgelegt, siehe [[Persönliche-Regeln]].

## Regeln
- [[Persönliche-Regeln]] – Stil, Sprache, Dokumentationsregeln
- `Globale Regeln/team/` – Team-Regeln UDPM (kommt per `bootstrap.sh` / `/sync-team-rules`, nicht manuell bearbeiten außer für Team-PRs)

## Über mich
- [[Profil]]
- [[Entwicklungsumgebung]]
- [[Claude-Setup]]
- Agents: [[heinrich|Heinrich]] (Chronist), [[hildegard|Hildegard]] (Archivarin), [[oscar|Oscar]] (Projekt-Brief), [[otto|Otto]] (Web-Recherche), [[philipp|Philipp]] (PHP-Stand-Prüfung)

## Projekte
Projektnotizen liegen unter `Projekte/`, Zuordnung zum Repo über `repo_remote` im Frontmatter. Vorlage: [[Vorlage Projekt]].
- [[aws-services]] – ECS-Tasks und PHP-Versionen der laufenden Images (mol-cluster-production, prod-shops)
- [[di-magento2]] – Magento-2.4.7-Base-Image für alle Shops (Docker, PHP 8.2/8.3)
- [[ecom-middleware]] – Symfony-Middleware (Shopify-Webhooks), PHP 8.5, ECS mol-cluster-production

- [[magento-dev]] – lokale Magento-Dev-Umgebung (`bin/dev`), Rollout-Historie Madsack production24

## Themen
- [[Magento]]
- [[Server und Sicherheit]]
- [[Testing]]
- [[Joomla]]

## Offen
- [[Offene Punkte]]


```dataview
TABLE shopsystem, shops, aktualisiert
FROM "Claude/Projekte"
SORT aktualisiert DESC
```
