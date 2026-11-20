---
tags: [todo]
aktualisiert: 2026-11-20
---
# Offene Punkte

- [x] UI-Bug auf abo-dev.haz.de (Option „USB-Adapter“, gemeldet 2026-07-28) – am 2026-11-20 geprüft: nicht mehr reproduzierbar. Option heißt jetzt „USB-C Power Netzteil (20 W)“, Anhaken ohne Konsolenfehler, Preis einmalig 49,00 → 58,90 €, Artikel landet korrekt in der Kasse
- [x] Team-Repo unter `~/git/` geklont, `bootstrap.sh` gelaufen (2026-11-20): Regeln unter `Globale Regeln/team/`, `@`-Imports in `~/.claude/CLAUDE.md`, `/sync-team-rules` installiert
- [x] Alten Klon unter `~/Development/inf-udpm-team-claude-rules` entfernt (2026-11-20)
- [x] `.idea/` aus `Globale Regeln/team/` entfernt
- [x] Max-Plugin installiert (2026-11-20, `max@udpm` in settings.json aktiv); Praxistest bestanden: Hook sperrt Push, Max urteilt, Push mit `MAX_OK=1`
- [x] `~/.claude/CLAUDE.md` und `settings.json` auf neuen Vault-Pfad umgestellt (2026-11-20)
- [x] Alle Regel-Imports auf Symlinks unter `~/.claude/vault-rules/` umgestellt (2026-11-20) – Test bestanden, alle 5 geladen
- [ ] ? klären, warum `@`-Imports mit Leerzeichen beim Kollegen (Linux) funktionieren, bei mir (macOS) nicht
- [x] Agent Heinrich angelegt (`Agents/heinrich.md`, Symlink nach `~/.claude/agents/`), Test mit [[aws-services]] erfolgreich (2026-11-20)
- [ ] Weitere Modulnotizen unter `Projekte/` anlegen (erste: [[aws-services]])
- [x] PR für `inf-udpm-team-claude-rules` erstellt (2026-11-20): macOS-Prüfung + Erst-Sync-Fix. Nach dem Merge: im Klon `main` auschecken + pullen, dann `/sync-team-rules`
- [x] Symlinks für hildegard, oscar, otto nach `~/.claude/agents/` angelegt (2026-11-20) – alle Agents verfügbar, Oscar-Test erfolgreich (`/agents`-Befehl gibt es nicht mehr; prüfen per Frage „Welche Subagents stehen dir zur Verfügung?“)
- [x] Session-Import: di-magento2 erledigt (2026-11-20)
- [ ] ? di-magento2: PHP2Shell-Stopgap-Härtung in der 8.3-Linie übernehmen oder bewusst weglassen? (siehe [[di-magento2]])
- [x] Session-Import: docker-middleware (ecom-middleware) erledigt (2026-11-20)
- [ ] ? ecom-middleware: nginx-Multipart-Sperre nach PHP-Patch (8.5.12) entfernen oder als Defense-in-Depth behalten? (siehe [[ecom-middleware]])
- [ ] ecom-middleware: AWS-WAF-Managed-Rules für CloudFront prüfen (ABO-1675, siehe [[ecom-middleware]])
- [x] Session-Import: wp-aboplus-karte erledigt (2026-11-20)
- [ ] wp-aboplus-karte (AWS-seitig): ECS Circuit Breaker + Rollback, minimumHealthyPercent=100, ECR Lifecycle Policy, ecr:DescribeImages für MARTECH_MOL_CLUSTER_AWS_KEY (siehe [[wp-aboplus-karte]])
- [x] Session-Import: di-php erledigt (2026-11-20)
- [ ] ? di-php: GitHub-Default-Branch auf main umstellen, sonst läuft der Nightly-Rebuild (nightly.yml) nicht (siehe [[di-php]])
- [x] Session-Import: mm-madsack-datatrans-payment erledigt (2026-11-20)
- [x] Session-Import: inf-udpm-icinga2-agent-docker erledigt (2026-11-20)
- [x] Session-Import: wp-abovorteil erledigt (2026-11-20)
- [ ] Shop-Repos: Branch `ca-prod` heißt eigentlich `qa-prod` – Tippfehler, zieht sich durch alle Shop-Repos. Irgendwann umbenennen (ca-prod → qa-prod über alle Shop-Repos), nicht terminiert; welche Deploy-Pipelines auf den Branch zeigen, ist ungeprüft (? zu klären). Beispiel: [[shop-hildesheim-sub]]; ähnlicher Name `ca-prod24` in [[magento-dev]] (? gleicher Tippfehler, unbestätigt)
