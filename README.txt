
KANONAS.de – Statisches Site-Paket (v2)
=======================================

Dateien
-------
- index.html          → Landingpage (DE)
- regeln.html         → Weiterleitung zu /index.html#rules (QR-Ziel)
- impressum.html      → Impressum (Platzhalter: bitte Angaben ergänzen)
- datenschutz.html    → Datenschutz (Platzhalter: juristisch prüfen)
- /assets/logo.svg    → SVG-Logo

Kein Webspace? Drei schnelle Optionen
-------------------------------------
A) GitHub Pages (kostenlos, dauerhaft möglich)
   1. GitHub-Account anlegen, Repo erstellen (z. B. kanonas.de-site).
   2. Dateien hochladen.
   3. In den Repo-Einstellungen "Pages" aktivieren (Deploy from branch, root).
   4. Optional: Datei 'CNAME' mit Inhalt 'kanonas.de' anlegen (für Custom Domain).
   5. Bei IONOS in den DNS-Einstellungen 'www' als CNAME auf deine GitHub-Pages-URL setzen
      und die Root-Domain auf 'www' umleiten (URL-Weiterleitung), bis A/ALIAS eingerichtet ist.

B) Netlify Drop (extrem schnell)
   - ZIP hier hochladen → Netlify-Subdomain erhalten.
   - In IONOS 'www' als CNAME auf die Netlify-Subdomain zeigen lassen.
   - Root → www umleiten.

C) IONOS Domain-Weiterleitung (temporär)
   - Solange du keinen Webspace hast, leite 'kanonas.de' auf eine temporäre URL (z. B. Netlify/GitHub) um.

QR-Code-Ziel
------------
- Ziel-URL: https://kanonas.de/regeln
- regeln.html leitet automatisch zu /index.html#rules weiter.
- Du kannst die Inhalte später tauschen, ohne den QR-Code neu zu drucken.

Kontaktformular
---------------
- Formspree-Action in index.html ersetzen oder auf Mailserver umstellen – ich liefere dir die Variante, die du bevorzugst.
