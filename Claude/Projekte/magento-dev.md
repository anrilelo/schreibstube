---
repo_remote: github.com/RedaktionsNetzwerk-Deutschland/magento-dev
shops: [Madsack-M2, DuMont]
shopsystem: magento2
tags: [projekt, magento, rollout, docker]
aktualisiert: 2026-11-20
---
# magento-dev

## Zweck
- Lokale Entwicklungsumgebung für die Magento-2-Shops, ein Werkzeug `bin/dev` für alle Shops (Details im `README.md` des Repos)
- Dient auch als Prüfstand vor Rollouts (`image-diff`, `smoke`), siehe Rollout-Historie unten

## Pfade
- Repo lokal: `/Users/rico/Development/magento-dev`
- GitHub: `github.com/RedaktionsNetzwerk-Deutschland/magento-dev`
- Shop-Konfiguration: `shops/<shop>/` (`shop.env` im Repo; `secrets.env`, `modules.conf`, `.state.env` gitignored)
- PHPStorm-Projekt: ? zu klären
- Test-Instanz(en): lokal per Docker (Mac/Apple Silicon und Linux/amd64)

## Architektur
- Gemeinsame Infrastruktur: `infra/compose.yml`; Shop- und Modul-Repos liegen neben dem Repo im Entwicklungsordner
- Wichtige Befehle: `bin/dev <shop> resume|start|link|unlink|smoke|image-diff <alt> [<neu>]`, `bin/dev doctor`, `bin/dev php-status`
- Tool-Plugins im eigenen Marketplace `magento-dev`, siehe [[Claude-Setup]]
- Verwandt: [[aws-services]] (zeigt, welche Images/PHP-Versionen auf den ECS-Clustern laufen), [[Magento]]

## Rollout-Historie

### 2026-11-19 Madsack-Shop (Tag `production24`, PHP 8.2) – erledigt
- Ausgerollt wurde der Stand von `ca-prod24`, Digest `sha256:4e7683039ec4…`
- Vorgänger auf `production24`: `sha256:dbedc937cc13…` vom 12.11.
- Vorab beide Images per `bin/dev madsack image-diff` verglichen, keine Blocker
- Ergebnis laut Enrico am 2026-11-20: alles gut, keine Probleme
- Datatrans-Einmalzahlung (Reservierung, autoSettle false, ABO-1746/1773) nach dem Rollout geprüft, funktioniert (bestätigt von Enrico am 2026-11-20)
- Inhalt:
  - datatranspayment ABO-1746/1773: Nicht-Abo-Bestellungen reservieren jetzt den Reservierungspreis (autoSettle false statt 0 € + true); Abbuchung/Settle erfolgt nicht im Shop
  - odoo-bundle: Report-FTP und VI&VA-Report entfernt
  - molbase: URL-Rewrites über Paket `olegkoval/magento2-regenerate-url-rewrites` 1.12
  - Developer-Mode-Fixes (SQL-identisch)
  - Themes onev2/eshop2one: Topmenü-Script läuft über RequireJS (`menu-toggle.js`) statt inline (CSP-Nonce)
  - madsack-aboshop: odoobundle-Defaults und NWZ-Absenderdaten für die Website `nwz_site`

## Fallstricke
- Siehe `CLAUDE.md` im Repo (Branch-Regeln, nicht pushen, Mails nur über Mailpit)

## Offene Punkte
- Commit 82b1b56 „Create ReadDir on FTP“ in `madsack/module-logistician` fehlt weiterhin (verloren beim Umzug Bitbucket → GitHub)
- ? zu klären: Rollouts Dumont und Hildesheim stehen nur in der Claude-Auto-Memory des Repos, nicht hier
