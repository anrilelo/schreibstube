---
tags: [magento]
aktualisiert: 2026-11-20
---
# Magento

## bootstrap.php überschreibt error_reporting
- `app/bootstrap.php` setzt `error_reporting(E_ALL)` – wichtig für Modul-Templates
- Lösungswege: direkte Änderung, Composer-Patch, Override nach dem Bootstrap
- PHP 8.4: `E_STRICT` entfällt → Forward-Compatibility beachten

## Checkout-Konfiguration (Magento 2.4.7)
- Minifizierte UI-Component-JSON-Configs (haz.txt, cz.txt, dev.txt) pretty-printed und analysiert
- Inhalte: Checkout-Steps, Zahlarten (PayPal, KDB-Debit, Madsack, Unzer)
- Zahlart Datatrans: eigenes Modul [[mm-madsack-datatrans-payment]] (autoSettle=false, Buchung durch Verlagssystem)

## Branch-Modell der Shop-/Magento-Repos
Vom User bestätigt (2026-11-20): In (fast) allen Repos (Shop-Repos, Magento) gibt es drei Branches `main`, `staging`, `development`.
- `main` = immer der aktuelle produktive Stand
- `staging` = neue Features, die schon getestet werden können und von den Fachabteilungen freigegeben werden
- Sind Features freigegeben, kommt der entsprechende (Feature-)Branch in `main` und geht mit dem nächsten Rollout produktiv
- `development` = neue Features in der ersten Entwicklungsphase, die meist lokal getestet werden können; wird in der Praxis kaum genutzt (Erfahrung aus der Vergangenheit)
- ? zu klären: Rolle von `ca-prod` (= `qa-prod`, Tippfehler, siehe [[Offene Punkte]]) im Modell
- ? zu klären: ob `ca-prod24` in [[magento-dev]] derselbe Tippfehler ist (Vermutung, unbestätigt)
- ? zu klären: ob Feature-Branches zum Testen in `staging` gemergt werden
- Beispiel: [[shop-hildesheim-sub]] (dort liegen `development`, `staging`, `ca-prod` als Remote-Branches)
- Verhältnis zu [[Branch-Strategie]] / [[BCPR-Workflow]] (Feature-Branch von `main`, PR-Base `main`): kein Widerspruch festgestellt, das Modell ergänzt nur, wofür `staging` dient

Siehe auch [[Entwicklungsumgebung]].
