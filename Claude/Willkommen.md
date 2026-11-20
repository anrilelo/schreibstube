---
tags: [index]
aktualisiert: 2026-11-20
---
# Willkommen – Claude-Wissensbasis

Zentraler Vault als **Single Point of Truth** für Claude (Chat und Claude Code). Neues Wissen wird sofort hier abgelegt, siehe [[Persönliche-Regeln]].

## Regeln
- [[Persönliche-Regeln]] – Stil, Sprache, Dokumentationsregeln
- [[Team-Workflow]] – unser Ablauf: Jira-Ticket → Branch → PR `staging` → Abnahme → PR `main`
- `Globale Regeln/team/` – Team-Regeln **UDPM**, gelten **nur in `inf-udpm-*`-Repos** (kommt per `bootstrap.sh` / `/sync-team-rules`, nicht manuell bearbeiten außer für Team-PRs)

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
- [[wp-aboplus-karte]] – Übergangs-Redirect-Container (php:8.5-apache) statt altem WordPress-Plugin, ECS
- [[di-php]] – eigenes PHP-Base-Image (thecustomsolutions/php-fpm) je PHP-Version, Base für di-magento2
- [[mm-madsack-datatrans-payment]] – Magento-Zahlungsmodul für Datatrans (Reservierung, autoSettle=false)
- [[inf-udpm-icinga2-agent-docker]] – Icinga2-Monitoring-Agent (Docker), Trivy→Dependency-Track-Feeder
- [[wp-abovorteil]] – WordPress-Abovorteil-Portal (3 Sites), bin/dev-Dev-Umgebung, PHP 8.3
- [[shop-hildesheim-sub]] – Magento-2-Shop Hildesheim (HIAZ), Migration auf 2.4.7 / PHP 8.3 (ABO-739)
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
