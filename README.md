# Fahrspur — Dashcam & Navi

Einzelne, selbstständige HTML-Seite, die Kamera-Aufnahme (Dashcam) und
Kartennavigation (OpenStreetMap) in einer Ansicht kombiniert — damit beim
Fahren nicht zwischen zwei Apps gewechselt werden muss (iOS erlaubt sonst
keine Kameraaufnahme im Hintergrund).

**Aktuelle Version:** 1.5.0

## Funktionen

- Kamera-Vollbildaufnahme mit eingebranntem Overlay (Datum, Uhrzeit,
  GPS-Koordinaten, Geschwindigkeit)
- Karte (OpenStreetMap, Leaflet) mit Live-Standort, als Inset oder
  vollflächig umschaltbar
- Zielsuche per Adresse/Ort **oder** Koordinaten (`47.3769, 8.5417`), mit
  Live-Autoergänzung während der Eingabe; optionales freies Startfeld
  (Standard: aktueller Standort)
- Routenführung über OSRM inkl. Pfeil-Hinweisen, Distanzanzeige und
  gesprochenen Abbiegeansagen (Web Speech API, Deutsch)
- Live-Verkehr über TomTom (optional, eigener kostenloser API-Key nötig):
  Verkehrsfluss-Einfärbung + Störungsmeldungen (Stau, Baustelle, Sperrung)
- Kamera-, Auflösungs- (720p / 1080p / max.) und Formatauswahl
  (MP4/H.264 oder WebM, je nach Browser-Unterstützung)
- Loop-Aufnahme (Aus / 5 / 10 / 15 / 30 Minuten) — hält nur die letzten
  N Minuten, ältere Segmente werden automatisch verworfen
- Aufnahmen als Liste in der Sitzung, einzeln herunterladbar

## Voraussetzungen

- **HTTPS-Hosting zwingend nötig** — Kamera- und Standortzugriff
  funktionieren nicht bei lokal geöffneten Dateien (`file://`).
  Geeignet: GitHub Pages, eigener Traefik-Reverse-Proxy o. ä.
- Safari auf iOS, Kamera-/Mikrofon-/Standortfreigabe erforderlich

## Nutzung

1. Seite über HTTPS öffnen, Zugriff erlauben
2. Optional: Start-/Zielfeld ausfüllen, "Los" antippen
3. Optional: 🚦 für Verkehrsdaten (TomTom-Key einmalig hinterlegen),
   ⚙ für Kamera/Auflösung/Format, ⇄ um Kamera/Karte zu tauschen
4. Roten Aufnahme-Knopf antippen, Aufnahme startet
5. Clips über 🎞️ herunterladen, bevor die Seite geschlossen wird —
   sie liegen nur im Speicher des aktuellen Tabs

## Bekannte Grenzen

- Keine echten Google-Maps-Daten (Verkehr/Blitzer/POIs) — Routing und
  Karte basieren auf freien OSM-/OSRM-Diensten
- Keine automatische Neuberechnung bei Stau (Route ist nach Berechnung starr)
- TomTom-Free-Tier ist auf ein Tageskontingent begrenzt
- Aufnahme läuft nur zuverlässig, solange der Tab aktiv im Vordergrund bleibt
  (iOS-Einschränkung, betrifft jede browserbasierte Kameraaufnahme)

## Dateien

- `index.html` — die App selbst (einzelne Datei, keine weiteren Abhängigkeiten
  außer Leaflet/Google Fonts per CDN)
- `CHANGELOG.md` — Versionsverlauf
