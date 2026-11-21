---
repo_remote: github.com/RedaktionsNetzwerk-Deutschland/magento-dev
shops: [Madsack-M2, DuMont]
shopsystem: magento2
tags: [projekt, magento, rollout, docker]
aktualisiert: 2026-11-20
---
# magento-dev

## Zweck
- **Repo-Art Tool** (nur `main`, PR nach `main`); wird umbenannt in `martech-team-tool-magento-dev` (entschieden 2026-11-21, noch nicht umgesetzt)
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

## PHP-Stand-Prüfung (Philipp)
- Agent [[philipp]] (Haiku, Tools Bash/Read) ruft `bin/dev php-status` auf (Skript `tools/php-status.py`, App-Liste `php-apps.conf`) und prüft die PHP-Version aller Shop-, WordPress- und Basis-Images gegen php.net. Meldet „Update fehlt", „EOL" und nicht prüfbare Images (z. B. fehlendes docker login). Nur lesen, kein eigener Nachrichtenversand.
- Automatik: geplante Aufgabe der Claude-Desktop-App `php-stand-pruefen` („PHP-Stand prüfen (Philipp)"), werktags zweimal täglich gegen 08:30 und 14:30 Uhr (cron `30 8,14 * * 1-5`; vom User bestätigt: „1–2 am Tag reicht"; der zweite Lauf erwischt PHP-Releases, die über Tag erscheinen, die Images werden nachts neu gebaut, häufiger prüfen bringt nichts), Auto-Modus, Datei `~/.claude/scheduled-tasks/php-stand-pruefen/SKILL.md`. Bei OK keine Benachrichtigung, bei veraltetem PHP oder nicht prüfbaren Images Desktop-/Push-Benachrichtigung (PushNotification). Läuft nur bei geöffneter App (sonst Nachholen beim nächsten Start).
- Kein Slack-Versand durch die Aufgabe; dafür manuell `bin/dev php-status --slack`.
- Fallstrick: neue Agenten in `~/.claude/agents` werden erst in neuen Sitzungen geladen.

## Rollout-Historie

### 2026-11-19 Madsack-Shop (Tag `production24`, PHP 8.2) – erledigt
- Ausgerollt wurde der Stand von `ca-prod24`, Digest `sha256:4e7683039ec4…` (gemeint: `qa-prod24`, Tippfehler wie bei `ca-prod`, vom User bestätigt; Abbild des produktiven Standes, siehe [[Magento]], TODO in [[Offene Punkte]], Beispiel [[shop-hildesheim-sub]])
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
- ? zu klären: Rollout Dumont steht nur in der Claude-Auto-Memory des Repos, nicht hier. Hildesheim hat jetzt eine eigene Projektnotiz ([[shop-hildesheim-sub]]); der Rollout-Verlauf im Detail ist dort noch nicht erfasst
