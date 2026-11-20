---
tags: [kern, claude]
aktualisiert: 2026-11-20
---
# Claude-Setup

## Vault
- Neuer Vault: `/Users/rico/claude-obsidian/`, Wissen unter `Claude/` (Index: [[Willkommen]])
- Alter Vault `/Users/rico/obsidian/claude` (2026-11-19) wurde hierher übernommen
- Backup geplant: Time Machine oder Git (bei Git `.obsidian/workspace.json` und `Claude/Globale Regeln/team/` ignorieren)
- Graph: Filter `-path:...` zum Ausblenden, Groups zum Einfärben

## Claude Code
- `~/.claude/CLAUDE.md` ist ein Symlink auf `Globale Regeln/Claude Code Setup/CLAUDE.md` im Vault (seit 2026-11-20, wie beim Kollegen) → in Obsidian bearbeitbar und mit dem Vault gesichert. Inhalt: Vault-Anweisungen + `@`-Imports der Regeln
- `settings.json` bewusst **nicht** verlinkt: Claude Code schreibt sie selbst und könnte den Symlink durch eine echte Datei ersetzen
- Der Claude-Chat (Desktop-App-Ordnerzugriff) darf `CLAUDE.md` im Vault nicht lesen – geschützte Datei; Prüfen/Bearbeiten nur in Claude Code, Obsidian oder Terminal
- `~/.claude/settings.json`: `permissions.additionalDirectories` = `/Users/rico/claude-obsidian`
- Stolperstein: `permissions`-Block mit zusätzlichen `{}` eingefügt → ungültiges JSON. Prüfen mit `python3 -m json.tool ~/.claude/settings.json`
- Plugins: phpstorm-plugin, max@udpm (Team, Pre-Push-Check), eigener Marketplace `magento-dev` (`/Users/rico/Development/magento-dev`): shop-status, folge-schritt, lange-laeufe, fix-status
- Verlauf wird per `cleanupPeriodDays` aufgeräumt – Vault-Dateien bleiben davon unberührt

## Team-Regeln UDPM
- Repo `RedaktionsNetzwerk-Deutschland/inf-udpm-team-claude-rules`, Klon unter `~/git/inf-udpm-team-claude-rules`
- Enthält Konventionen, Workflow-Regeln, Plugin **max** (Pre-Push-Check per Hook, `/max:bcpr`), `bootstrap.sh`, `scripts/sync.sh`, `/sync-team-rules`
- Sync legt Regeln unter `Claude/Globale Regeln/team/` ab; `TEAM_RULES_VAULT=/Users/rico/claude-obsidian/Claude` dauerhaft in `~/.zshrc`
- Kernregeln: kein `Co-Authored-By`, keine KI-Attribution, Conventional Commits (Commits + Branches), Max vor jedem Push, Vault sofort pflegen
- Persönliche Agents des Kollegen (Heinrich, Hildegard, Otto, Oscar) sind **nicht** im Team-Repo; am 2026-11-20 übernommen und angepasst (Pfade, Oscar ohne `find-project-note.sh`, Heinrich ohne Obsidian-MCP-Tools). Max nicht als eigener Agent, kommt aus dem Plugin. Definitionen in `Agents/`, Symlinks nach `~/.claude/agents/`

### Fallstricke unter macOS
- `sync.sh` braucht Bash 4+ (`mapfile`) und GNU find (`find -printf`) → `brew install bash findutils jq`, `gnubin` vor macOS-Tools in den PATH
- Mit BSD-find werden neue Vault-Dateien still nicht in den Klon übernommen
- GNU find liegt unter `/opt/homebrew/opt/findutils/libexec/gnubin` – Zeile am Ende der `~/.zshrc`

### Fallstrick: Erst-Sync mit leerem Vault (Exit 3)
- Beim allerersten Lauf gibt es noch keinen Sync-Stand (`.git/team-rules-sync-state`); der leere Vault-Ordner wird als „alles gelöscht“ gewertet → Abbruch mit Exit 3
- **Niemals** `TEAM_RULES_ALLOW_DELETIONS=1` nutzen – das würde alle Regeln im Klon löschen
- Lösung: Inhalt einmalig per `rsync` aus dem Klon nach `Globale Regeln/team/` kopieren (gleiche Excludes wie `sync.sh` **plus `--exclude .idea`**, sonst landet der PHPStorm-Ordner im Vault), dann `bootstrap.sh` erneut → „Kein Diff“
- Fix vorbereitet (2026-11-20) im Klon `~/git/inf-udpm-team-claude-rules`: `sync.sh` prüft Bash 4+ und GNU-find beim Start und behandelt Erst-Sync mit leerem Vault als Repo→Vault; README um macOS-Abschnitt ergänzt. In Wegwerf-Kopie getestet (Erst-Sync, Kein Diff, Vault→Klon, BSD-find-Abbruch)

### Max im Einsatz (Test 2026-11-20)
- Ablauf funktioniert: Hook sperrt `git push`, Max läuft (als Hintergrund-Agent, ~10 s), Urteil OK, Push mit `MAX_OK=1`
- Beobachtung: Während Max lief, wurde ein `git push` versucht und vom Hook gesperrt – der Hook greift also auch, wenn zu früh gepusht wird
- `git push origin --delete <branch>` läuft ebenfalls durch den Hook (Max nötig)

### Fallstrick: `@`-Imports mit Leerzeichen werden nicht geladen (bestätigt 2026-11-20)
- `@`-Import bricht den Pfad am ersten Leerzeichen ab → alle Imports unter `Globale Regeln/…` (und `Commit- und PR-Regeln.md`) wurden still **nicht** geladen
- `/memory` zeigt nur die `CLAUDE.md` selbst, nicht die Imports – Prüfung nur per Nachfrage in einer neuen Sitzung
- Lösung: Symlinks ohne Leerzeichen/Umlaute unter `~/.claude/vault-rules/` auf die Vault-Dateien, Imports als `@~/.claude/vault-rules/<name>.md`; nach `/sync-team-rules` greift automatisch der neue Stand
- Beim Kollegen (Linux) funktionieren die Imports mit Leerzeichen laut seiner Aussage → Ursache evtl. abhängig von OS (macOS) oder Client (Desktop-App vs. CLI) bzw. Version; ? zu klären
- Test 2026-11-20 nach Umstellung: alle 5 Imports geladen. Per `[[Wikilink]]` verlinkte Notizen werden **nicht** mitgeladen, sondern bei Bedarf gelesen (gewollt)
- Team-Regeln verweisen auf `[[Superpowers Execution-Strategie]]` – existiert in diesem Vault nicht (Notiz des Kollegen)
