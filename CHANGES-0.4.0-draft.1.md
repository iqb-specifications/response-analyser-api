# API-Entwurf 0.4.0-draft.1

Dieser Entwurf passt API 0.3.0 an die erste Ausbaustufe des internen ResponseAnalysers an. Die folgenden Änderungen sind inkompatibel mit bisherigen Clients.

## Workspace-Endpunkte

- PUT `/` erstellt einen namenlosen Workspace ohne Request-Body und liefert dessen ID.
- Die bisherigen `/ws-admin`-Präfixe entfallen. Die Auswertung liegt unter POST `/{workspaceId}/evaluate`.
- PATCH zum Umbenennen und das Workspace-Feld `name` entfallen.
- GET/PUT `/{workspaceId}/configuration` verwalten die effektive Konfiguration einschließlich Defaults.
- POST `/{workspaceId}/reset` und DELETE `/{workspaceId}` ergänzen den Lebenszyklus.
- Es gibt keinen Workspace-State. Das Portal koordiniert Imports und laufende Auswertungen; einzelne Änderungen sind transaktional.

## Konfiguration

`codingParameters` übernimmt die Struktur aus dem AP-Index-Commit `4342b0dcfb2840eab51d3b54165877a64a874909`, einschließlich der fünf dort konfigurierbaren Statusnamen. Die interne Referenz auf itemValueType wurde für OpenAPI angepasst. Ein separates Missing-Map-Feld wird nicht eingeführt.

Die automatische Itembenennung wird mit `separator`, `omitPrefixIfVariableIdLongerThan` und `useUnitAliasIfSingleCodingCompleteVariable` konfiguriert. Defaults: leere Zeichenfolge, null, false. Explizite Itemlisten haben Vorrang.

## Auswertung

- Berechnete Items und Skalen gewinnen gegenüber zusätzlich gelieferten Einträgen gleicher ID, auch bei berechneten Fehlerresultaten.
- Messzeitpunkte sind kein Bestandteil des Datenmodells; unterschiedliche fachliche Skalen verwenden unterschiedliche IDs.
- Skalen ohne relevante Daten werden ausgelassen. Alle relevanten Skalen erhalten einen expliziten Status.
- VALID verlangt `value`; Fehlerstatus schließen `value` aus. Zu wenige verwendbare Items oder Quellskalen führen zu INSUFFICIENT_SOURCE.
- Die erste Stufe verwendet ausdrücklich `calculationProfile: mean-v1`: alle Basismethoden mitteln Scores ungewichtet; Derived SUM/MEAN mitteln die benötigten Quellen; MAP verwendet ausschließlich die erste Quelle.
- Externe Skalenformate bleiben erhalten. Eine als WLE deklarierte Skala liefert in diesem Profil noch keine WLE-Schätzung.
- Veraltete unitId-Beispiele wurden auf unitAlias korrigiert.

## Noch abzustimmende Details

Feldnamen und Defaults der Itembenennung, Ersatzcodes, Fallbacks für nicht konfigurierbare Antwortstatus, Fehlerpriorität sowie mehrdeutige subform-Referenzen sind als Vorschläge beziehungsweise offene Punkte gekennzeichnet. Die Auslagerung von codingParameters in ein gemeinsames eigenständiges Schema ist nicht Bestandteil dieses Entwurfs.

## Validierung

43 lokale Spezifikationsprüfungen bestanden: OpenAPI 3.1, aufgelöste Schemas, Defaults, Beispiele, positive/negative Datenfälle und Strukturvergleich der übernommenen codingParameters. Zusätzlich wurden vereinfachte Endpunktpfade, namenlose Workspaces und das Fehlen eines Workspace-State geprüft. Die Swagger-Darstellung wurde im Browser kontrolliert. Keine Anwendung implementiert oder zur fachlichen Laufzeitberechnung getestet.
