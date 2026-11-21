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
- `qa-prod` (im Repo als `ca-prod` geschrieben, Tippfehler, siehe [[Offene Punkte]]) = Abbild des zum Generierungszeitpunkt aktuellen produktiven Standes; damit werden Änderungen gegen den aktuellen produktiven Stand geprüft und Risiken für den Rollout bewertet
- `qa-prod24` hat dieselbe Bedeutung (in [[magento-dev]] als `ca-prod24`, derselbe Tippfehler, bestätigt); Branches mit Suffix „24“ sind mit dem Umstieg auf Magento 2.4 entstanden
- Nicht dokumentiert: wie und wann `qa-prod` generiert wird (vom User nicht gesagt)
- Feature-Branches gehen per PR nach `staging`, nach Abnahme per PR nach `main` (bestätigt 2026-11-20, siehe [[Team-Workflow]])
- Beispiel: [[shop-hildesheim-sub]] (dort liegen `development`, `staging`, `ca-prod` als Remote-Branches)
- ~~Verhältnis zu [[Branch-Strategie]] / [[BCPR-Workflow]] (Feature-Branch von `main`, PR-Base `main`): kein Widerspruch festgestellt, das Modell ergänzt nur, wofür `staging` dient~~ – überholt (2026-11-20): Das sind UDPM-Regeln, unser Ablauf steht in [[Team-Workflow]]

## Composer-Varianten der Shops (gesehen 2026-11-21)
- Jeder Shop hat `composer/composer.json` (Module `@dev` = Default-Branch → Produktion), `composer_staging.json` (`dev-staging`) und `composer_dev.json` (`dev-development`), plus `auth.json`
- Erklärt, warum Moduländerungen erst nach Merge in `staging`/`main` des Moduls und Shop-Rollout sichtbar werden
- Bitbucket-Anteil der Modul-Repos je Shop (lokaler Stand): madsack-abo 47/79, hildesheim-sub 30/50, stimme 24/40, dumont-abo-v2 20/40, celle-sub 22/39, rnz-v2 20/38, ovb_abo 15/31, mgb-abo 17/30

Siehe auch [[Entwicklungsumgebung]].
