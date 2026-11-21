---
repo_remote: github.com/RedaktionsNetzwerk-Deutschland/martech-team-claude-rules
shops: []
shopsystem: keins
tags: [projekt, regeln]
aktualisiert: 2026-11-21
---
# martech-team-claude-rules

## Zweck
Gemeinsame Claude-Code-Regeln des Teams **martech** (4 Personen). Ersetzt für unser Team die UDPM-Regeln; diese gelten nur noch in `inf-udpm-*`-Repos.

## Pfade
- Repo lokal: `/Users/rico/git/martech-team-claude-rules`
- GitHub: `github.com/RedaktionsNetzwerk-Deutschland/martech-team-claude-rules`

## Aufbau (Grundgerüst 2026-11-20, Commit e2e0fb8 auf main gepusht)
- `CLAUDE.md` – Einstieg, importiert `rules/*.md` per relativem `@rules/…`, Geltungsbereich (UDPM nur in `inf-udpm-*`)
- `rules/team-workflow.md` – Ticket → Branch von `main` → PR `staging` → Abnahme → PR `main`
- `rules/commits-prs.md` – Ticket-ID + Titel, keine Conventional Commits, kein Co-Authored-By
- `rules/branch-modell.md` – `main`, `staging`, `development`, `qa-prod`, `master`-Ausnahme
- `rules/sicherheit.md` – Secrets, Push-Check, keine zerstörenden Aktionen, verifizieren
- `docs/anleitung.md` – Anleitung für Menschen (Markdown-Fassung des Claude-Docs [Anleitung: Vom Jira-Ticket bis main](https://claude.ai/artifact/54NSfJWqPhSZB1E1mypRwU))
- `README.md` – Einrichtung: klonen nach `~/git/martech-team-claude-rules`, in `~/.claude/CLAUDE.md` `@~/git/martech-team-claude-rules/CLAUDE.md`, Update per `git pull`

- `docs/empfehlungen.md` (2026-11-20) – 6 Vorschläge aus UDPM, **nicht beschlossen, nicht importiert**: Branch-Schutz `main`+`staging`, Merge-Einstellungen (PR_TITLE, delete_branch_on_merge), PR-Status vor Commit, Änderungen aufteilen, Branch-Cleanup mit `LC_ALL=C`, Konflikt-Prüfung bei neuen Regeln
- `docs/migration.md` – Verweis auf RND/OP-Leitlinie „Claude Code Leitlinien Devs“ (Confluence OP, Seite 5251629059, Autor Martin Kautz, Stand 2026-04-09) + Abweichungen (Commit-Schema, kein `git add .`, staging→main)

- PR #1 (Empfehlungen + Migration) gemergt (21dc7f2)
- Branch `regeln-pro-repo-art` (2026-11-21, noch nicht committet): `rules/repo-namen.md` (Repo-Art per Präfix `martech-team-di/shop/portal/mod-*`, schrittweises Umbenennen), `rules/repos-images.md` (nur `main`, Build-Matrix, kein `staging`), `team-workflow.md` als Standard für alle übrigen Repos

- Test 2026-11-21 in `di-php` bestanden: Claude Code erkennt Image-Repo, PR für MRT-107 nach `main` (nicht `staging`), Branch von aktuellem `main`, Archivieren nur mit Zustimmung

## Bewusst anders als UDPM
- Kein `sync.sh`/`bootstrap.sh`/Autogen-Block, kein Vault-Sync – nur Klon + ein `@`-Import
- Regeln liegen im Repo, nicht im persönlichen Vault

## Offen
- Erster Commit direkt auf `main` (e2e0fb8, leeres Repo); ab jetzt nur per PR
- ? Review-Pflicht, Merge-Art, Auslösung Staging-Rollout (auch in `rules/team-workflow.md` als offen markiert)
- ? Persönliches Setup danach umstellen: Import `team-workflow.md` aus dem Vault durch Repo-Import ersetzen

## Abhängigkeiten / Verknüpfungen
- [[Team-Workflow]] (Vault-Fassung, Quelle ist künftig das Repo), [[Claude-Setup]], [[Persönliche-Regeln]]
