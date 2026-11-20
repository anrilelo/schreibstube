---
repo_remote: github.com/RedaktionsNetzwerk-Deutschland/shop-hildesheim-sub
shops: [abo.hildesheimer-allgemeine.de (HIAZ), abo.alfelder-zeitung.de]
shopsystem: magento2
tags: [projekt, shop, docker, magento]
aktualisiert: 2026-11-20
---
# shop-hildesheim-sub

## Zweck
- Abo-Shop des Verlags Gebrüder Gerstenberg GmbH & Co. KG (Magento 2), Kürzel HIAZ in Commits/Branches
- 2 Untershops, beides produktive Domains:
  - https://abo.hildesheimer-allgemeine.de (Hildesheimer Allgemeine Zeitung, HIAZ)
  - https://abo.alfelder-zeitung.de (Alfelder Zeitung)
- ? zu klären: Abbildung der Untershops in Magento (Store Views oder Websites) nicht bestätigt
- Baut das Shop-Image auf dem Magento-Base-Image aus [[di-magento2]] auf

## Pfade
- Repo lokal: `/Users/rico/Development/shop-hildesheim-sub`
- GitHub: `github.com/RedaktionsNetzwerk-Deutschland/shop-hildesheim-sub`
- Top-Level: `Dockerfile`, `README.md`, `composer`, `deployment-scripts`, `docker-hiaz`, `local_install`, `magento2`, `sql`

## Architektur
- Docker-Image auf Basis des Magento-Images aus [[di-magento2]]
- Weitere Details (Module, Deployment) noch nicht erfasst, nur Belegtes aus Git-Historie

## Aktueller Stand (2026-11-20)
- Migration auf Magento 2.4.7 mit PHP 8.3, Ticket ABO-739, Branch `ABO-1739-Hildesheim-2.4.7-8.3`; PRs #220, #229, #230 alle gemergt
- Commit `83365e3` „ABO-739 Hildesheim PHP 8.3 mit magento 2.4.7" ist via PR #229 in `main` gemergt und liegt auch auf den Remote-Branches `development`, `staging`, `ca-prod` (gemeint: `qa-prod`, siehe Offene Punkte; geprüft per `git fetch` am 2026-11-20)
- Neuester bekannter Stand `main`: PR #234 ABO-1801 Modul `trackcheckoutabort` nach GH (HIAZ), SMTP-Update; davor PR #228 ABO-1789 HIAZ neuer Logistiker
- Frühere Änderungen: ABO-1682 Braintree aus HIAZ entfernt (PR #211), Versandpreise nach GH verschoben; ABO-1637 Modul gettosession auf HIAZ (PR #208)

## Abhängigkeiten / Verknüpfungen
- [[di-magento2]] (Base-Image), [[Magento]], [[magento-dev]] (lokale Dev-Umgebung, Rollout-Historie)
- Test der Claude-CLI im PHPStorm-Terminal in diesem Repo: [[Claude-Setup]]

## Fallstricke

## Offene Punkte
- `ca-prod` ist ein Tippfehler, gemeint ist `qa-prod` (vom User bestätigt, zieht sich durch alle Shop-Repos). TODO projektübergreifend: Umbenennung ca-prod → qa-prod in allen Shop-Repos, nicht terminiert, siehe [[Offene Punkte]]
- Branch-Modell (`main` = produktiv, `staging` = testbare, von Fachabteilungen freizugebende Features): siehe [[Magento]], Abschnitt „Branch-Modell der Shop-/Magento-Repos"
- `development` = frühe Entwicklungsphase, meist lokal getestet, kaum genutzt (siehe [[Magento]])
- `ca-prod` (= `qa-prod`) = Abbild des zum Generierungszeitpunkt aktuellen produktiven Standes, dient dem Prüfen von Änderungen und der Risikobewertung für den Rollout (siehe [[Magento]])
- Feature-Branches per PR nach `staging`, nach Abnahme nach `main` (siehe [[Team-Workflow]])
- ? Rollout-Verlauf 2.4.7 / PHP 8.3 (Staging-/Prod-Termine, Probleme, Reste) noch nicht erfasst
