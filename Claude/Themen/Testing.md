---
tags: [testing]
aktualisiert: 2026-11-20
---
# Testing

## Codeception 5 + Allure in Docker
- Acceptance-Tests mit Headless Chromium (`--no-sandbox`, `--disable-dev-shm-usage`)
- Adapter `allure-framework/allure-codeception`, Allure CLI für HTML-Reports
- Stolpersteine:
  - Java fehlte → `default-jre-headless`
  - Leere Reports → falscher Result-Pfad oder Report per `file://` statt HTTP geöffnet

Siehe auch [[Entwicklungsumgebung]].
