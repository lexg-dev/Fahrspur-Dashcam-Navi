# Fahrspur — Dashcam & Navi

Selbstgebautes Single-Page-HTML-Tool für iPhone, das Dashcam-Aufnahme und OpenStreetMap-Navigation in einer einzigen Browser-Seite kombiniert. Grund für die Kombination: iOS erlaubt keine Kameraaufnahme im Hintergrund, sobald eine andere App (z.B. eine separate Navi-App) in den Vordergrund wechselt — Kamera und Karte müssen deshalb in derselben Seite laufen.

## Kernfunktionen

- **Dashcam-Aufnahme**: Kamerabild + eingebranntes HUD (Datum/Zeit, GPS-Koordinaten, Geschwindigkeit) wird per Canvas gezeichnet und über `MediaRecorder` aufgenommen.
- **Loop-Aufnahme**: automatische Segmentierung in mehrere Clips, älteste werden bei Erreichen des eingestellten Zeitfensters verworfen.
- **Navigation**: Routing über OSRM, Kartendarstellung über MapLibre GL JS (WebGL-Vektorkacheln) mit dem kostenlosen [OpenFreeMap](https://openfreemap.org/) "Liberty"-Style.
- **Verkehr (optional)**: TomTom Traffic Flow (Rasterkacheln) + Incidents, erfordert eigenen TomTom-API-Key.
- **Tempolimit-Anzeige**: über die Overpass-API (OpenStreetMap) anhand der aktuellen Position, seit 4.5.0 zusätzlich per Fahrtrichtung (Bearing) disambiguiert.
- **GPS-Simulation & Debug-HUD**: zum Testen ohne echte Fahrt — Route abfahren simulieren, dazu ein eigenes Debug-Overlay über Karte/Kamera mit Standort-/GPS-Kennzahlen (Berechtigungsstatus, letzter Fix, Fehler).

## Technischer Aufbau

Eine einzige HTML-Datei (`fahrspur-dashcam-navi.html`), kein Build-Schritt, kein Server nötig — muss aber über **HTTPS** ausgeliefert werden (Kamera-/Standortzugriff funktioniert sonst nicht).

Externe Dienste:
| Zweck | Dienst | API-Key nötig? |
|---|---|---|
| Kartenkacheln | OpenFreeMap ("Liberty"-Style) | Nein |
| Routing | OSRM (öffentlicher Demo-Server) | Nein |
| Adress-/Ortssuche | Nominatim (OpenStreetMap) | Nein |
| Tempolimits | Overpass API | Nein |
| Verkehr | TomTom Traffic API | Ja, eigener Key in den Einstellungen |

Wichtige Bibliotheken: MapLibre GL JS (via cdnjs, Version siehe `<head>`).

## Architektur-Hinweise & Fallstricke

Diese Sektion sammelt wiederkehrende Konventionen und Stolperfallen im Code, die schon mehrfach zu echten Bugs geführt haben. Seit 4.5.1 wurden lange, historische Bugfix-Erzählungen aus den Inline-Kommentaren entfernt (Details dazu jeweils im `CHANGELOG.md`) — die dauerhaft relevanten Invarianten stehen stattdessen hier, statt über Dutzende Einzelkommentare verstreut zu sein.

### OSRM-Schritt-Indexierung: `nextStepIndex` vs. `nextStepIndex - 1`

`routeSteps[nextStepIndex]` ist immer der **kommende, noch nicht erreichte** Schritt — seine `.geometry` beginnt erst am anstehenden Manöverpunkt und beschreibt die Strecke, die NACH diesem Manöver befahren wird. Die Strecke, auf der man **aktuell tatsächlich fährt**, ist `routeSteps[nextStepIndex - 1]` — deren Geometrie endet exakt am anstehenden Manöverpunkt.

Jeder Code, der "die aktuelle Strasse", "die aktuelle Position im Streckenverlauf" oder Ähnliches braucht, muss also `nextStepIndex - 1` verwenden (mit Bounds-Check `>= 0`), niemals `nextStepIndex` direkt. Referenzimplementierung: `rebuildGeomForCurrentSegment()`.

**Das ist kein theoretisches Risiko** — dieselbe Verwechslung hat bereits zweimal einen echten Bug verursacht:
- 3.9.15: Restdistanz-Anzeige fror ein, weil an einer Stelle `nextStepIndex` statt `nextStepIndex - 1` verwendet wurde.
- 4.5.0: Die Autobahn-Vorfilterung (`onMotorway`) für die Tempolimit-Erkennung prüfte `routeSteps[nextStepIndex].ref` statt `routeSteps[nextStepIndex - 1].ref` — am gravierendsten ausgerechnet bei Autobahnausfahrten, dem eigentlichen Zielszenario der Funktion. Fix in 4.5.1.

Bei jeder neuen Stelle, die mit `routeSteps` und `nextStepIndex` arbeitet: diese Konvention zuerst prüfen.

### Geschützte Performance-Konstanten — nie ohne Rückfrage ändern

Folgende Konstanten drosseln bewusst CPU-/GPU-/Netzwerklast (Akku- und Wärmeschonung auf dem iPhone während langer Fahrten) und dürfen **nicht ohne explizite Anweisung** geändert werden, auch nicht im Rahmen von "Aufräumen" oder Refactoring:
- `NAV_CAMERA_UPDATE_MS`
- GPS `maximumAge` (bei `getCurrentPosition`/`watchPosition`)
- alle weiteren Throttling-/Rate-Limit-Konstanten für Netzwerk-Calls (z.B. Tempolimit-/Verkehrs-Abfragen) und Rendering (z.B. `CANVAS_FPS`, `pixelRatio`-Begrenzung der Karte)

Wenn im Rahmen einer Aufgabe unklar ist, ob eine Konstante in diese Kategorie fällt: nachfragen statt anpassen.

### Namenskollisionen bei Funktionsdeklarationen

JavaScript löst mehrere `function`-Deklarationen mit demselben Namen im selben Scope stillschweigend zur textlich **letzten** Definition auf — es gibt keine Fehlermeldung, keine Warnung, alle früheren Aufrufstellen rufen ab sofort einfach die falsche Funktion. Genau das ist der `bearingDeg`-Fall: eine 4-Parameter-Funktion für Tunnel-Bearing-Berechnungen (`bearingDeg(lat1, lon1, lat2, lon2)`) wurde von einer später deklarierten 2-Parameter-Simulations-Funktion `bearingDeg(a, b)` überschrieben — dadurch lieferte die Tunnel-Dunkelmodus-Bearing-Prüfung seit 3.8.0 stillschweigend `NaN`. Fix: Umbenennung der 4-Parameter-Variante zu `bearingDegLL`.

Konsequenz für künftige Änderungen: vor dem Hinzufügen einer neuen Funktion kurz prüfen (z.B. `grep -n "function <name>("`), ob der Name im Datei-Scope bereits vergeben ist — besonders bei generischen Namen wie `bearingDeg`, `distance`, `format*`.

## Bekannte Einschränkungen

- Nur getestet auf iOS Safari (Zielplattform: iPhone). Andere Browser/Plattformen ungetestet.
- Ohne eigenen TomTom-Key bleibt die Verkehrsanzeige deaktiviert.

## Versionierung

Siehe `CHANGELOG.md`. Die aktuelle Version steht als `APP_VERSION`-Konstante im Script und wird unten rechts im HUD angezeigt.
