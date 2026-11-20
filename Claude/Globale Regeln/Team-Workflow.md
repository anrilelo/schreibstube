---
tags: [regeln, workflow]
aktualisiert: 2026-11-20
---
# Team-Workflow (eigenes Team)

Vom User beschrieben am 2026-11-20. Gilt für **unser** Team – **nicht** die UDPM-Regeln unter `Globale Regeln/team/` (die stammen aus dem Team-Repo eines Kollegen, `inf-udpm-team-claude-rules`, und gelten für uns nicht bzw. nur eingeschränkt; Umgang damit noch offen, siehe [[Offene Punkte]]).

## Ablauf: vom Ticket bis `main`
1. **Jira-Ticket** mit der Aufgabenbeschreibung kommt rein
2. Aus dem Ticket heraus wird ein **Branch im jeweiligen Repo** erzeugt – neue Features **immer von `main`** abzweigen (bestätigt 2026-11-20)
3. Feature entwickeln, auf dem Branch **committen**
4. Ist der Stand testbar: **Pull Request nach `staging`**
5. Version wird in der **Staging-Umgebung** des jeweiligen Projekts ausgerollt
6. Nach Test und **Abnahme**: **Pull Request nach `main`**, dann mergen

**`main` und `staging` immer nur per Pull Request** – nie direkt committen oder pushen (bestätigt 2026-11-20)

## Branch-Namen und Commit-Messages (bestätigt 2026-11-20)
- Schema: **Jira-Ticket-ID + Ticket-Titel** – **keine** Conventional Commits (anders als UDPM)
- Beispiele aus den Repos: Branch `ABO-1728-Critical-PHP`, `ABO-1739-Hildesheim-2.4.7-8.3`; Commit „ABO-739 Hildesheim PHP 8.3 mit magento 2.4.7“ (siehe [[shop-hildesheim-sub]], [[di-magento2]])
- **Kein `Co-Authored-By`-Trailer, keine KI-Attribution** („Generated with Claude Code“ o. Ä.) in Commits und PRs – bleibt wie bisher (bestätigt 2026-11-20, deckt sich hier mit UDPM)

## Sonstiges aus den Repos
- Ausnahme Default-Branch: [[wp-abovorteil]] hat `master` statt `main`

## Offen (? zu klären)
- Review-Pflicht für PRs (wer gibt frei?), Merge-Art (Merge-Commit, Squash)

Branch-Rollen (`main`, `staging`, `development`, `qa-prod`): [[Magento]]
