---
tags: [regeln]
aktualisiert: 2026-11-20
---
# Persönliche Regeln

Ergänzen die Team-Regeln unter `Globale Regeln/team/`, überschreiben sie aber nicht.

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
- **Max** – kommt aus dem Team-Plugin `max@udpm`, Pflicht vor jedem `git push` und `gh pr create` (per Hook erzwungen).

## repo_remote-Schlüssel
origin-URL normalisieren: Protokoll und `.git` entfernen, `:` nach dem Host → `/`.
- `git@github.com:Org/repo.git` → `github.com/Org/repo`
- `https://github.com/Org/repo.git` → `github.com/Org/repo`

