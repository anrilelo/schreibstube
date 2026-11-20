---
repo_remote: github.com/RedaktionsNetzwerk-Deutschland/aws-services
shops: [Madsack-M2, DuMont]
shopsystem: magento2
tags: [projekt, aws, ecs, php]
aktualisiert: 2026-11-20
---
# aws-services

## Zweck
- Zeigt, welche Tasks auf welchen EC2-Instanzen der ECS-Cluster laufen und mit welcher PHP-Version ihr Image gebaut ist
- Ausgabe als Terminal-Text oder als Panel in Claude Code
- Rein lesend, ändert nichts in AWS
- Zweck der PHP-Prüfung: sehen, ob PHP-Sicherheitsupdates schon ausgerollt sind (maßgeblich ist das tatsächlich gestartete Image, nicht das Registry-Tag)

## Pfade
- Repo lokal: `/Users/rico/Development/aws-services`
- GitHub: `github.com/RedaktionsNetzwerk-Deutschland/aws-services` (Org RedaktionsNetzwerk-Deutschland), Default-Branch `main`, Arbeit per PRs (bisher #1–#8)
- PHPStorm-Projekt (vom User am 2026-11-20 bestätigt): das Repo selbst, `/Users/rico/Development/aws-services`; dort liegt `.idea/` mit `aws-services.iml`, die `.idea/`-Dateien sind im Repo eingecheckt
- Test-Instanz(en): keine (liest direkt gegen AWS)

## Architektur
- Tech: PHP-CLI, AWS CLI v2 (`aws` im PATH), curl
- Kernklasse `GetAwsEcsServices.php`: Konstruktor (Cluster-Name, AWS-Profil, optional `true` = Debug-Ausgabe der aws-Befehle), Methode `listServices()`
- Cluster und Einstiegsskripte:
  - `mol-cluster-production` (AWS-Standardprofil): `molServices.sh` / `molServices.php`
  - `prod-shops` (AWS-Profil `dumont`): `dumontServices.sh` / `dumontServices.php`
- Ablauf: unabhängige aws-Aufrufe laufen parallel in Runden: erst Container-Instanzen, Name-Tags, Task-ARNs; dann Details; danach je Image Manifest, Download-URL, Config. php.net wird parallel zur ersten Image-Runde abgefragt. Laufzeit ca. 4–8 s.
- Instanzname = EC2-`Name`-Tag; ohne Tag (z.B. Auto-Scaling) wird die Instanz-ID angezeigt
- PHP-Version: Digest aus `ecs describe-tasks` (`containers[].imageDigest`) -> Manifest aus ECR (bei Multi-Arch `linux/amd64`) -> Image-Config -> Env `PHP_VERSION` (offizielle PHP-Images) -> Vergleich mit `https://www.php.net/releases/active.php`. Anzeige: aktuell / „veraltet, aktuell X" / „EOL" / „?"
- Benötigte IAM-Leserechte: `ecs:ListContainerInstances`, `ecs:DescribeContainerInstances`, `ecs:ListTasks`, `ecs:DescribeTasks`, `ec2:DescribeTags`; für PHP-Version zusätzlich `ecr:BatchGetImage`, `ecr:GetDownloadUrlForLayer` (fehlen sie: Anzeige `PHP ?` + Hinweis auf stderr)
- Weiteren Cluster ergänzen: neue PHP-Datei nach Muster `dumontServices.php` mit `new GetAwsEcsServices('cluster-name', 'aws-profil')`
- Claude-Code-Mod `.claude/skills/ecs-tasks/`:
  - Panel „ECS Tasks", lädt automatisch in jeder Session des Projekts
  - fragt beim Sessionstart und stündlich beide Cluster ab
  - Buttons „Aktualisieren" je Cluster (Tasten `m`/`d`); Befehle `/ecs-tasks`, `/ecs-tasks mol`, `/ecs-tasks dumont`
  - PHP-Zusammenfassung je Cluster (rot/grün), Fehler/Hinweise je Cluster
  - Prüfen: `claude plugin validate .claude/skills/ecs-tasks` und `claude plugin test .claude/skills/ecs-tasks`

## Abhängigkeiten / Verknüpfungen
- Betroffene Umgebungen (vom User am 2026-11-20 bestätigt): Madsack-M2 -> Cluster `mol-cluster-production` (betreibt u.a. den Service „Madsack-M2-Shop-Prod", Magento 2); DuMont -> Cluster `prod-shops` (Profil `dumont`, DuMont-Shops). Siehe [[Magento]]
- Die ECS-Nodes und ihre Images werden von [[inf-udpm-icinga2-agent-docker]] überwacht (Trivy → Dependency-Track)

## Fallstricke
- Der Mod parst die Textausgabe von `listServices()`. Ändert sich das Ausgabeformat, muss `parse()` in `.claude/skills/ecs-tasks/hooks/register.tsx` angepasst werden
- Der Mod sucht `php`/`aws` nur unter `/opt/homebrew/bin`, `/usr/local/bin`, `/usr/bin`, `/bin`
- Fehlende ECR-Rechte führen nur zu `PHP ?`, nicht zum Abbruch

## Historie
- init -> Claude-Optimierungen (services_v2, #1) -> ECS-Tasks-Mod-Panel (#2) -> `.sh` ausführbar (#3) -> Einzel-Cluster-Aktualisierung im Mod (#4) -> AWS-Aufrufe parallel (#5) -> README (#6) -> Instanzen ohne Name-Tag mit Instanz-ID (#7) -> PHP-Version laufender Images vs. php.net (#8)

## Offene Punkte
- Keine
