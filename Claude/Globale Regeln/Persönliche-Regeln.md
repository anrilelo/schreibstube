---
tags: [regeln]
aktualisiert: 2026-11-20
---
# Persönliche Regeln

## Geltungsbereich der Regeln (entschieden 2026-11-20)
- **Unser Team:** [[Team-Workflow]] (Jira → Branch von `main` → PR `staging` → Abnahme → PR `main`; Ticket-ID + Titel statt Conventional Commits)
- **UDPM-Regeln** unter `Globale Regeln/team/` gelten **nur bei Arbeit in `inf-udpm-*`-Repos** (bzw. Repos des UDPM-Teams). Dort haben sie Vorrang; überall sonst gelten sie nicht
- Widersprechen sich beide außerhalb von UDPM-Repos, gewinnt [[Team-Workflow]]

## Allgemeine Regeln (aus den UDPM-Regeln übernommen, gelten überall)
- **Keine Secrets** (Tokens, Passwörter, API-Keys) in Vault, Commits, PRs oder Chat-Antworten; liegen welche im Repo: warnen, nur schematisch (`<PLACEHOLDER>`) dokumentieren
- Kein `git add` von `.env`, `credentials.json`, `*.pem`, `*.key` ohne ausdrückliche Bestätigung
- **Vor jedem `git push`** staged + ungepushte Commits auf Secrets, ungewollte Dateien und große Binaries prüfen (übernimmt Max)
- **Keine zerstörenden Aktionen** (`push --force`, `reset --hard`, `rm -rf`, Branch löschen) ohne ausdrückliche Zustimmung
- **Verifizieren statt annehmen:** Dateiinhalt/Repo-Stand vor Behauptungen prüfen; vor jedem Commit prüfen, ob der Branch zu einem schon gemergten PR gehört
- Kein `Co-Authored-By`, keine KI-Attribution (siehe [[Team-Workflow]])

## Kommunikation
- Antworten auf Deutsch
- Shopsystem ist Magento 2

## Vault-Pflege
- Vault: `/Users/rico/claude-obsidian/Claude/`, Index: [[Willkommen]]
- **Sofort dokumentieren** über den Agent **Heinrich** (`Agents/heinrich.md`), nicht erst am Session-Ende. Heinrich sieht das Gespräch nicht: Erkenntnis vollständig samt Kontext (Repo-Pfad, origin-URL, Dateien) übergeben. Anlässe: Feedback/Korrektur, neue Regel/Präferenz, Architektur-Entscheidung, neuer Projekt-Fakt oder Fallstrick, dauerhaftes TODO, Referenz auf externe Systeme (Jira, Dashboards, URLs)
- Nicht dokumentieren: Syntaxfragen, einmalige Q&A, laufendes Debugging, Build-Output
- Bestehende Notiz ergänzen statt neue anlegen; neue Notizen in [[Willkommen]] verlinken
- `[[Wikilinks]]`, Frontmatter mit `tags` und `aktualisiert`
- Keine Zugangsdaten, Tokens oder Kundendaten

## Projektnotizen
- Ein Modul/Repo = eine Notiz unter `Projekte/`, nach [[Vorlage Projekt]]
- Zuordnung über Frontmatter `repo_remote: github.com/<org>/<repo>`, bei Repos ohne Remote `repo_uuid`
- Feld `shops:` – in welchen Shop-Umgebungen das Modul eingesetzt wird

## Agents
Definitionen liegen in `Agents/` im Vault, verlinkt nach `~/.claude/agents/`. In der Kommunikation als Personen ansprechen.
- **Heinrich** (Sonnet) – der Chronist. Schreibt/aktualisiert Vault-Notizen. **Immer sofort** aufrufen bei Feedback, Korrektur, neuer Regel, Entscheidung, Projekt-Fakt, Fallstrick, dauerhaftem TODO, externer Referenz. Vollen Kontext mitgeben, bei Projekten den `repo_remote`-Schlüssel.
- **Hildegard** (Haiku) – die Archivarin. Durchsucht den Vault bei projektübergreifenden Fragen („was weiß ich über X", „welche Module hängen mit Y zusammen").
- **Oscar** (Haiku) – der Kofferträger. Proaktiv beim Einstieg in ein (ungewohntes) Repo: Kurzbrief aus Vault-Notiz + Repo-Stand.
- **Otto** (Haiku) – der Bote. Stumpfe Web-Recherche (Doku, Changelogs, Fehlermeldungen). Kein Architektur-Rat.
- **Philipp** (Haiku) – der Heizungsableser. Prüft per `bin/dev php-status` (Repo magento-dev) die PHP-Version aller Shop-/WordPress-/Basis-Images gegen php.net; meldet „Update fehlt", „EOL" und nicht prüfbare Images. Nur lesen. Läuft automatisch als geplante Aufgabe, siehe [[magento-dev]] / [[philipp]].
- **Max** – kommt aus dem Team-Plugin `max@udpm`, Pflicht vor jedem `git push` und `gh pr create` (per Hook erzwungen). Bleibt global als Secret-Check vor dem Push; ? prüfen, ob Max auch UDPM-Konventionen (Conventional Commits) anmahnt – das wäre außerhalb von UDPM-Repos falsch.

## repo_remote-Schlüssel
origin-URL normalisieren: Protokoll und `.git` entfernen, `:` nach dem Host → `/`.
- `git@github.com:Org/repo.git` → `github.com/Org/repo`
- `https://github.com/Org/repo.git` → `github.com/Org/repo`

