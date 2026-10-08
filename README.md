# Nón Lá – Website

Statische Website für das Restaurant Nón Lá in Basel (8 Seiten, zweisprachig
DE/EN). Die Inhalte (Speisekarten, Wochenbuffet, Öffnungszeiten, Texte) werden
über ein webbasiertes Redaktionssystem (Decap CMS) unter **/admin** gepflegt –
ganz ohne Programmierkenntnisse.

Die Seite wird bei **Netlify** gehostet (kostenlos). Jede Änderung im
Redaktionssystem löst automatisch einen Neuaufbau aus; nach ca. 1 Minute ist
die aktualisierte Seite online.

---

## 1. Einmalige Einrichtung (ca. 20 Minuten)

### Schritt 1: Code zu GitHub hochladen

1. Auf <https://github.com> ein kostenloses Konto erstellen (falls nicht
   vorhanden).
2. Neues Repository anlegen: **New repository** → Name z. B. `nonla-website`
   → Sichtbarkeit *Private* → **Create repository**.
3. Die Dateien dieses Ordners hochladen – am einfachsten mit der
   Kommandozeile im Ordner `nonla-website`:

   ```
   git init
   git add .
   git commit -m "Nón Lá Website"
   git branch -M main
   git remote add origin https://github.com/IHR-BENUTZERNAME/nonla-website.git
   git push -u origin main
   ```

   (Alternativ geht auch **GitHub Desktop** oder das Hochladen über die
   GitHub-Weboberfläche: „uploading an existing file“.)

### Schritt 2: Netlify einrichten

1. Auf <https://app.netlify.com> mit dem GitHub-Konto anmelden
   („Sign up with GitHub“).
2. **Add new site → Import an existing project → GitHub** wählen und das
   Repository `nonla-website` auswählen.
3. Die Build-Einstellungen werden automatisch aus der Datei `netlify.toml`
   gelesen (Build command: `pip install -r requirements.txt && python
   build.py`, Publish directory: `dist`) – einfach **Deploy** klicken.
4. Nach ca. 1 Minute ist die Seite unter einer Adresse wie
   `https://zufaelliger-name.netlify.app` erreichbar.

### Schritt 3: Redaktionssystem (Login) aktivieren

1. In Netlify: **Site configuration → Identity** → **Enable Identity**.
2. Darunter **Services → Git Gateway** → **Enable Git Gateway**.
3. **Identity → Registration** → am besten „Invite only“ einstellen.
4. **Identity → Invite users** → E-Mail-Adresse der Betreiber eintragen.
5. Damit der Einladungs-Link im E-Mail funktioniert, einmalig die
   E-Mail-Vorlagen anpassen: **Site configuration → Identity → Emails**
   → bei *Invitation*, *Confirmation*, *Password recovery* usw. im Text den
   Link `{{ .SiteURL }}/#...` jeweils zu `{{ .SiteURL }}/admin/#...` ändern
   (also `/admin/` vor dem `#` einfügen) und speichern.
6. Die eingeladenen Personen klicken den Link im E-Mail, vergeben ein
   Passwort und landen im Redaktionssystem.

Fertig. Das Redaktionssystem ist danach unter
`https://IHRE-SEITE.netlify.app/admin` erreichbar.

*(Eigene Domain wie www.nonla.ch: in Netlify unter **Domain management**
hinzufügen und beim Domain-Anbieter die DNS-Einträge wie von Netlify
angezeigt setzen.)*

---

## 2. Inhalte bearbeiten (Alltag)

1. `https://IHRE-SEITE.netlify.app/admin` öffnen und einloggen.
2. Links den Bereich wählen:

   | Bereich | Was wird bearbeitet |
   |---|---|
   | **Wochenbuffet (Mo–Fr)** | Buffet-Gerichte pro Wochentag (Mittag Spalenbrunnen) |
   | **Speisekarte Alte Markthalle** | Gerichte, Preise, Texte dieser Karte |
   | **Speisekarte Spalenbrunnen mittags** | Kleine Karte mittags + Seitentexte |
   | **Speisekarte Spalenbrunnen abends** | Abendkarte inkl. Varianten und Notizen |
   | **Speisekarte Klara** | Gerichte, Preise, Texte dieser Karte |
   | **Texte übrige Seiten (DE/EN)** | Navigation, Startseite, Bestellen, Reservieren, Catering, Fusszeile |
   | **Öffnungszeiten** | Zeiten der 3 Standorte, Bestellzeiten, «Jetzt geöffnet»-Automatik |

3. Ändern und auf **Publish** (Veröffentlichen) klicken.
4. Nach ca. 1 Minute ist die Änderung online. (Status in Netlify unter
   **Deploys** sichtbar.)

### Wichtige Regeln

- **Schlüssel/`id`-Felder nie ändern.** Sie verbinden den Text mit der
  Website und den Übersetzungen.
- **Neues Gericht:** in der Liste auf „Add“ klicken und einen **neuen,
  eindeutigen Schlüssel** vergeben (nur Kleinbuchstaben/Zahlen, z. B. `a5`,
  `d8`, `s9` – keiner darf doppelt vorkommen).
- **Gericht löschen:** Eintrag in der Liste entfernen.
- **`<br/>`** = Zeilenumbruch, **`&amp;`** = &-Zeichen – bitte stehen lassen.
- **Bilder:** beim Gericht auf „Choose an image“ → vorhandenes Bild wählen
  oder hochladen (landet im Ordner `images/`).
- Felder mit dem Hinweis „bitte nicht ändern“ (grau/versteckt) sind
  technische Felder – einfach ignorieren.

---

## 3. Für Technik-Interessierte: Aufbau des Projekts

```
content/     ← alle redaktionellen Inhalte (YAML, mit deutscher Anleitung
               im Dateikopf) – das einzige, was das CMS verändert
templates/   ← HTML-/JS-Vorlagen mit @@-Marken (Design-Grundlage)
images/      ← Bilder (auch Upload-Ziel des CMS)
styles-v2.css← Design (unverändert aus dem Original)
admin/       ← Decap CMS (config.yml = Formular-Definition)
original/    ← Originaldateien als Referenz für den Abgleich
build.py     ← baut dist/ aus content/ + templates/
dist/        ← Build-Ergebnis (wird veröffentlicht, nicht einchecken)
```

Lokal bauen und gegen das Original prüfen:

```
pip install -r requirements.txt
python build.py            # baut nach dist/
python build.py --verify   # prüft: dist/ == original/ (byte-identisch)
```

Zum lokalen Anschauen z. B. `python -m http.server -d dist 8000` und
<http://localhost:8000> öffnen.

### Hinweis Vercel

Das Projekt läuft grundsätzlich auch auf Vercel (Build-Kommando wie oben,
Output `dist`). Das Redaktionssystem braucht dann statt Netlify Identity
einen GitHub-OAuth-Login (Backend `github` in `admin/config.yml` plus
OAuth-App) – deutlich mehr Einrichtungsaufwand. **Empfohlen: Netlify.**
