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

Siehe auch [[Entwicklungsumgebung]].
