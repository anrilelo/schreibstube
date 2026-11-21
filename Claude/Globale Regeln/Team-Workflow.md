---
tags: [regeln, workflow]
aktualisiert: 2026-11-20
---
# Team-Workflow (eigenes Team)

> **Maßgeblich ist künftig das Team-Repo [[martech-team-claude-rules]]** (`rules/team-workflow.md`, `rules/commits-prs.md`). Änderungen dort per PR; diese Notiz ist die persönliche Fassung.

Vom User beschrieben am 2026-11-20. Gilt für **unser** Team in allen Repos außer `inf-udpm-*`. Die UDPM-Regeln unter `Globale Regeln/team/` (Team-Repo `inf-udpm-team-claude-rules`) gelten **nur in `inf-udpm-*`-Repos**; bei Widerspruch gewinnt außerhalb davon diese Notiz. Allgemeine Regeln (Secrets, Push-Check, Git-Vorsicht): [[Persönliche-Regeln]].

## Geltung nach Repo-Art (bestätigt 2026-11-21)
- **Shops, andere Portale (z. B. WordPress) und Magento-Module** arbeiten alle gleich: Feature von `main`, PR nach `staging`, final PR nach `main` (Ablauf unten)
- **Image-Repos** (`di-php`, `di-wordpress`, `di-magento2`, künftig `martech-team-di-*`): nur `main`, Ticket-Branch → PR nach `main`, kein `staging` (siehe [[di-php]], MRT-107)

- **Namensschema** (2026-11-21): `martech-team-di-*`, `martech-team-shop-*`, `martech-team-portal-*`, `martech-team-mod-*`; Umbenennung Schritt für Schritt. Der Ablauf unten ist der **Standard** für alle Repos ohne Sonder-Präfix

## Ablauf: vom Ticket bis `main`
1. **Jira-Ticket** mit der Aufgabenbeschreibung kommt rein
2. Aus dem Ticket heraus wird ein **Branch im jeweiligen Repo** erzeugt – neue Features **immer von `main`** abzweigen (bestätigt 2026-11-20)
3. Feature entwickeln, auf dem Branch **committen**
4. Ist der Stand testbar: **Pull Request nach `staging`**
5. Version wird in der **Staging-Umgebung** des jeweiligen Projekts ausgerollt
6. Nach Test und **Abnahme**: **Pull Request nach `main`**, dann mergen

**`main` und `staging` immer nur per Pull Request** – nie direkt committen oder pushen (bestätigt 2026-11-20)

## Branch-Namen und Commit-Messages (bestätigt 2026-11-20)
- Ticket-Titel per Atlassian-Connector aus Jira holen (in Claude Code integriert), nicht raten
- Schema: **Jira-Ticket-ID + Ticket-Titel** – **keine** Conventional Commits (anders als UDPM)
- Beispiele aus den Repos: Branch `ABO-1728-Critical-PHP`, `ABO-1739-Hildesheim-2.4.7-8.3`; Commit „ABO-739 Hildesheim PHP 8.3 mit magento 2.4.7“ (siehe [[shop-hildesheim-sub]], [[di-magento2]])
- **Kein `Co-Authored-By`-Trailer, keine KI-Attribution** („Generated with Claude Code“ o. Ä.) in Commits und PRs – bleibt wie bisher (bestätigt 2026-11-20, deckt sich hier mit UDPM)

## Sonstiges aus den Repos
- Ausnahme Default-Branch: [[wp-abovorteil]] hat `master` statt `main`

## Offen (? zu klären)

## Build und Rollout (bestätigt 2026-11-21)
- **Shops + Portale:** Merge nach `staging` stößt Rollout an; zusätzlich nächtlicher automatischer Build; alles auch manuell per GitHub Action
- **Magento-Module:** kein Rollout; Merge nach `staging` oder `main` löst nichts aus
- Ablauf Shops/Portale: GitHub Actions baut Docker-Image → Push Docker Hub + AWS ECR → bei `staging` zusätzlich ECS-Service-Neustart; bei `main` identisch, aber **ohne** Neustart
- **Livestellen Produktion = immer händisch** in AWS: Task-Anzahl des ECS-Service auf 1 reduzieren → Service neu starten → Anzahl wieder erhöhen (bestätigt 2026-11-21); Claude Code macht das nie selbst

## Review (bestätigt 2026-11-21)
- PRs brauchen **aktuell keinen Review**
- Gemergt wird mit **normalem Merge-Commit**, kein Squash/Zusammenfassen (bestätigt 2026-11-21)

Branch-Rollen (`main`, `staging`, `development`, `qa-prod`): [[Magento]]

Anleitung für Kolleg:innen (Claude-Doc, teilbar): [Anleitung: Vom Jira-Ticket bis main](https://claude.ai/artifact/54NSfJWqPhSZB1E1mypRwU)
