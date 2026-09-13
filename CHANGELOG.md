# Changelog

Alle nennenswerten Änderungen an diesem Projekt werden hier festgehalten.
Format angelehnt an [Keep a Changelog](https://keepachangelog.com/),
Versionierung nach [SemVer](https://semver.org/).

## [1.6.1] — Fixes
### Behoben
- Ansicht-Tauschen (⇄) funktionierte nicht mehr, nachdem das Inset einmal
  manuell verschoben wurde (feste Inline-Position überschrieb die
  CSS-Umschaltregel) — wird beim Tauschen jetzt zurückgesetzt
### Geändert
- Statusleiste von unten nach oben verschoben (direkt unter Timer/Zahnrad),
  dort deutlich besser sichtbar statt am unteren Rand zwischen den
  Bedienelementen

## [1.6.0] — Navigation, Bedienbarkeit, Statusanzeige
### Hinzugefügt
- Karte zoomt während aktiver Navigation automatisch auf "Straße voraus"
  (Zoomstufe 17) statt auf der Gesamtübersicht der Route zu bleiben
- Kleines Inset (Kamera oder Karte, je nach Ansicht) frei verschiebbar
  per Zieh-Griff, sowie minimierbar/wiederherstellbar
- Kompakte, gut lesbare Statusleiste über den Bedienelementen: Aufnahme
  inkl. Timer, Loop-Modus, GPS-Signalstärke, Mikrofonstatus, Verkehr an/aus,
  Anzahl & Größe gespeicherter Clips
### Geändert
- Wake Lock (Bildschirm-wach-halten) wird jetzt erst beim Antippen von
  "Zugriff erlauben & starten" angefordert (zuvor zu früh ohne
  Nutzerinteraktion, wurde von Safari oft stillschweigend abgelehnt)

## [1.5.0] — Suche, Loop-Aufnahme, Bedienung
### Hinzugefügt
- Autoergänzung für Start- und Zielfeld (Live-Vorschläge über Nominatim
  während der Eingabe, Auswahl per Antippen)
- Loop-Aufnahme: Aus / letzte 5 / 10 / 15 / 30 Minuten — ältere Segmente
  werden automatisch verworfen, laufende Aufnahme wird dabei nicht
  unterbrochen
### Geändert
- Start-/Zielfelder blenden sich nach erfolgreicher Routenberechnung
  automatisch aus; "Bearbeiten" in der Routenzusammenfassung blendet sie
  wieder ein

## [1.4.1] — Versionierung & Dokumentation
### Hinzugefügt
- Versionsnummer im Code (`APP_VERSION`) und sichtbar im Kamera-Menü
- README.md und dieses Changelog

## [1.4.0] — Ansicht & Aufnahmequalität
### Hinzugefügt
- ⇄ Knopf: Kamera und Karte tauschen (Karte vollflächig, Kamera als Inset)
- ⚙ Einstellungen: Kamera-Auswahl (mehrere Objektive), Auflösung
  (720p/1080p/maximal), Aufnahmeformat (MP4/H.264 bevorzugt, WebM-Fallback)
- Cover-Fit-Zeichnung der Kamera ins Aufnahme-Canvas (kein Verzerren mehr),
  Aufnahmeauflösung von der Bildschirmgröße entkoppelt

## [1.3.0] — Flexible Start-/Zielsuche
### Hinzugefügt
- Zielfeld akzeptiert direkte Koordinateneingabe (`lat, lon`)
- Neues, optionales Startfeld (Ort/Straße/Koordinaten) — Standard bleibt
  aktueller Standort

## [1.2.0] — Verkehrsdaten-Fixes
### Geändert
- Störungsabfragen auf max. 1x/45s begrenzt (verhindert übermäßigen
  API-Verbrauch durch automatisches Kartennachführen während der Fahrt)
- Verkehrsfluss-Kachelebene wird alle 3 Minuten aktiv neu geladen statt
  auf dem Browser-Cache zu verharren

## [1.1.0] — Live-Verkehr
### Hinzugefügt
- TomTom-Integration (optionaler eigener API-Key, lokal gespeichert):
  Verkehrsfluss-Einfärbung auf der Karte, Störungsmarker mit Popups

## [1.0.0] — Erste Version
### Hinzugefügt
- Kamera-Vollbildaufnahme mit eingebranntem Datum/Zeit/GPS/Geschwindigkeit-
  Overlay
- OpenStreetMap-Karte (Leaflet) mit Live-Standort als Inset, umschaltbar
  größer/kleiner
- Zielsuche über Nominatim, Routenberechnung über OSRM
- Gesprochene Abbiegehinweise (Web Speech API) und Pfeil-Banner
- Aufnahmen als Liste zum Herunterladen (sitzungsgebunden)
