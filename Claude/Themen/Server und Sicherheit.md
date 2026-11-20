---
tags: [server, sicherheit]
aktualisiert: 2026-11-20
---
# Server und Sicherheit

## Apache2-Härtung
- Access-Logs analysiert
- fail2ban-Filter gegen verdächtige Query-Strings (`coid=`, `xc638e=`)
- `.htaccess`-Regeln mit `SetEnvIf` / `mod_rewrite`, Ausnahme für `produkt.html?info=*`
