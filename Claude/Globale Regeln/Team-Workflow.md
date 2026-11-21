---
tags: [regeln, workflow]
aktualisiert: 2026-11-21
---
# Team-Workflow (Team martech)

**Maßgeblich ist das Team-Repo [[martech-team-claude-rules]]** (`~/git/martech-team-claude-rules`, `github.com/RedaktionsNetzwerk-Deutschland/martech-team-claude-rules`). Claude Code lädt es über `@~/git/martech-team-claude-rules/CLAUDE.md` in `~/.claude/CLAUDE.md` (umgestellt und getestet 2026-11-21). Änderungen nur dort per PR – diese Notiz ist nur der Wegweiser.

## Wo was steht
| Thema | Datei im Team-Repo |
| --- | --- |
| Repo-Arten, Namensschema `martech-team-di/shop/portal/mod-*`, Umbenennen | `rules/repo-namen.md` |
| Standard-Ablauf (Shops, Portale, Module): Ticket → Branch von `main` → PR `staging` → Abnahme → PR `main`; Review, Merge-Art, Build/Rollout, Module, Livestellen | `rules/team-workflow.md` |
| Image-Repos: nur `main`, Build-Matrix | `rules/repos-images.md` |
| Tool-Repos (`aws-services`, `magento-dev`, …) und das Regel-Repo: nur `main`, PR nach `main`, vorerst keine Ticket-Pflicht | `rules/repos-tools.md` |
| Branch-Namen, Commits, PRs (Ticket-ID + Titel, keine KI-Attribution) | `rules/commits-prs.md` |
| Branch-Rollen `main`, `staging`, `development`, `qa-prod` | `rules/branch-modell.md` |
| Branch-Schutz, Merge-Einstellungen je Repo-Art | `rules/github-einstellungen.md` |
| Secrets, Push-Check, keine zerstörenden Aktionen | `rules/sicherheit.md` |
| Anleitung für Menschen | `docs/anleitung.md`, Claude-Doc [Anleitung: Vom Jira-Ticket bis main](https://claude.ai/artifact/54NSfJWqPhSZB1E1mypRwU) |
| Migrationen (RND/OP-Leitlinie) | `docs/migration.md` |

## Kurzfassung (Stand 2026-11-21)
- Standard: Branch `ABO-1234-Titel` von `main` → PR `staging` (Shops/Portale: automatischer Staging-Rollout) → Abnahme → PR `main` (baut Prod-Image, kein Neustart) → Livestellen **händisch** in AWS
- Image- und Tool-Repos: Branch von `main` → PR `main` (Tools: Präfix `martech-team-tool-*`, vorerst keine Ticket-Pflicht – auch nicht im Regel-Repo, bestätigt 2026-11-21)
- Kein Review nötig, nur Merge-Commits, kein Co-Authored-By
- UDPM-Regeln nur in `inf-udpm-*`-Repos

Verwandt: [[Persönliche-Regeln]], [[Magento]], [[Claude-Setup]]
