---
tags: [server, sicherheit]
aktualisiert: 2026-11-20
---
# Server und Sicherheit

## Apache2-Härtung
- Access-Logs analysiert
- fail2ban-Filter gegen verdächtige Query-Strings (`coid=`, `xc638e=`)
- `.htaccess`-Regeln mit `SetEnvIf` / `mod_rewrite`, Ausnahme für `produkt.html?info=*`

## PHP2Shell (CVE-2026-109618/-109619) – ABO-1728 (21.10.2026)
- Kritische PHP-RCE (Sansec „php2shell"); Fix = PHP-Update (8.2.35 / 8.3.36 / 8.4.27 / 8.5.12)
- Stopgap in den Images: `session.upload_progress.enabled=0`, `session.use_strict_mode=1`, als `php_admin_flag` im FPM-Pool; WordPress-Images (Apache/mod_php) via `conf.d/zz-*.ini`
- Details und Branch-Stand: [[di-magento2]]
- ecom-middleware (PHP 8.5, keine Uploads): zusätzlich nginx-Multipart-Sperre (403) und `file_uploads=Off`; Details [[ecom-middleware]]
