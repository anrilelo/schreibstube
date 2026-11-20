---
repo_remote: github.com/RedaktionsNetzwerk-Deutschland/martech-team-claude-rules
shops: []
shopsystem: keins
tags: [projekt, regeln]
aktualisiert: 2026-11-20
---
# martech-team-claude-rules

## Zweck
Gemeinsame Claude-Code-Regeln des Teams **martech** (4 Personen). Ersetzt für unser Team die UDPM-Regeln; diese gelten nur noch in `inf-udpm-*`-Repos.

## Pfade
- Repo lokal: `/Users/rico/git/martech-team-claude-rules`
- GitHub: `github.com/RedaktionsNetzwerk-Deutschland/martech-team-claude-rules`

## Aufbau (Grundgerüst 2026-11-20, noch nicht committet)
- `CLAUDE.md` – Einstieg, importiert `rules/*.md` per relativem `@rules/…`, Geltungsbereich (UDPM nur in `inf-udpm-*`)
- `rules/team-workflow.md` – Ticket → Branch von `main` → PR `staging` → Abnahme → PR `main`
- `rules/commits-prs.md` – Ticket-ID + Titel, keine Conventional Commits, kein Co-Authored-By
- `rules/branch-modell.md` – `main`, `staging`, `development`, `qa-prod`, `master`-Ausnahme
- `rules/sicherheit.md` – Secrets, Push-Check, keine zerstörenden Aktionen, verifizieren
- `docs/anleitung.md` – Anleitung für Menschen (Markdown-Fassung des Claude-Docs [Anleitung: Vom Jira-Ticket bis main](https://claude.ai/artifact/54NSfJWqPhSZB1E1mypRwU))
- `README.md` – Einrichtung: klonen nach `~/git/martech-team-claude-rules`, in `~/.claude/CLAUDE.md` `@~/git/martech-team-claude-rules/CLAUDE.md`, Update per `git pull`

## Bewusst anders als UDPM
- Kein `sync.sh`/`bootstrap.sh`/Autogen-Block, kein Vault-Sync – nur Klon + ein `@`-Import
- Regeln liegen im Repo, nicht im persönlichen Vault

## Offen
- ? Erster Commit: direkt auf `main` (leeres Repo, PR noch nicht möglich) oder Branch + PR
- ? Review-Pflicht, Merge-Art, Auslösung Staging-Rollout (auch in `rules/team-workflow.md` als offen markiert)
- ? Persönliches Setup danach umstellen: Import `team-workflow.md` aus dem Vault durch Repo-Import ersetzen

## Abhängigkeiten / Verknüpfungen
- [[Team-Workflow]] (Vault-Fassung, Quelle ist künftig das Repo), [[Claude-Setup]], [[Persönliche-Regeln]]
