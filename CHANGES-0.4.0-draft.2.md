# API 0.4.0-draft.2 – Item Matrix Rules und Bulk-Upload

Lokaler Folgeentwurf zu Merge 3d005217b47eecdfdc0064b5a38de9e1b33ccbaf. Noch nicht veröffentlicht.

## Gemeinsame Konfiguration

WorkspaceConfiguration.itemMatrixRules referenziert https://w3id.org/iqb/spec/item-matrix-rules/0.1. Das veröffentlichte Schema entspricht Commit 0c2791f1b8edce5876ae3fa6b3cb8cdd4a2c5a85. Die bisherigen Felder codingParameters und itemNaming auf Workspace-Ebene entfallen im vorgeschlagenen Vertrag. Die externe Spezifikation wird nicht verändert.

Dies ist keine reine Feldumbenennung: useUnitAliasIfSingleVariable zählt alle Einträge der gespeicherten codingScheme.variableCodings statt der Antworten eines einzelnen Requests. Dazu zählen abgeleitete und manuelle Variablen. Doppelte Definitions-IDs werden beim Upload abgewiesen. Die Zuordnung der einzigen Variable erfolgt über alias, falls nicht leer, sonst id; nur deren Item erhält den Unit-Alias. Teilantworten verändern so die Benennung nicht. Die Längenschwelle verwendet 0 statt null zum Deaktivieren; eine alte Schwelle 0 lässt sich nicht direkt übernehmen. Nicht fertig kodierte Antworten erhalten beide Werte aus der passenden Regel oder missingElse. Defaults sind code=0 und score=-99. Ein vorhandener Antwortcode wird dabei ersetzt. CODING_COMPLETE bleibt unverändert, soweit Code und Score vollständig und ganzzahlig sind.

Das neue API-Feld missingScores reserviert explizit die aus der Mittelwertbildung ausgeschlossenen Item-Scores; Default [-99]. Andere negative Scores bleiben gültig. Für zusätzliche Missing-Werte wie -98 muss die Liste entsprechend erweitert werden. [] deaktiviert den Ausschluss. Die Liste gilt für berechnete und zusätzliche Items; sie gehört nicht zum externen Item-Matrix-Schema. Negative Skalenwerte bleiben gültige Quellen. Das externe Schema erzwingt weder nichtnegative Benennungsschwellen noch eindeutige Statuszuordnungen; diese Bedingungen werden ergänzend semantisch geprüft. Die vollständige Ablösung und diese Fachregeln sind ausdrücklich noch zu bestätigen.

## Bulk-Upload

POST /{workspaceId}/upload-content nimmt units, baseScales, derivedScales und optional configuration gemeinsam an. Angegebene Listen sind nicht leer. Es wird atomar ergänzt/ersetzt; nicht angegebene Definitionen und eine ausgelassene Konfiguration bleiben unverändert. Eine angegebene Konfiguration ersetzt die bisherige vollständig. Einzeluploads bleiben erhalten und teilen das Unit-Schema mit Bulk.

Doppelte IDs, Alias-Konflikte im Endbestand und Wechsel zwischen Basis-/Derived-Typ bei bestehender ID werden abgewiesen. Letzteres gilt ebenfalls für Einzeluploads. Fachliche Vorwärtsreferenzen bleiben erlaubt. 200 enthält Anzahlen und configurationUpdated; jeder Fehler bedeutet keine Teilübernahme. Bei Größenüberschreitung 413; das konkrete Limit legt der Betrieb fest. Keine asynchronen Jobs, keine Personendaten-Bulk-Auswertung.

## Prüfung

39 Schema- und Beispieldatenprüfungen bestanden, einschließlich eines gemeinsamen Upload-Beispiels mit Unit, Basis- und abgeleiteter Skala sowie Konfiguration. Externe Referenzen wurden zur Prüfung lokal aufgelöst. Laufzeitverhalten, Rollback, Rechenregeln und Größenlimits sind noch nicht implementiert oder getestet. Swagger ist als offline lesbare Dokumentation aktualisiert; die Browserdarstellung wurde nicht erneut visuell geprüft.

## Korrekturen nach Review

Die pauschale Vorzeichenregel wurde durch explizite Missing-Scores ersetzt. Die Itembenennung verwendet die feste Variablenmenge der gespeicherten Kodierdefinition. Das Bulk-Erfolgsbeispiel meldet entsprechend dem Anfragebeispiel jeweils eine Unit, Basis- und abgeleitete Skala. Die API enthält Referenzfälle für gültige negative Scores, mehrere Missing-Werte und stabile Itemnamen bei Teilantworten; ihre Laufzeitprüfung ist Aufgabe der späteren Implementierung.

Die vollständige Ablösung von codingParameters bleibt eine offene Vertragsentscheidung. Alternativen sind ein versionierter Schnittstellenwechsel, zwei gegenseitig ausgeschlossene Konfigurationsformate mit einem Adapter oder eine gemeinsame Weiterentwicklung der Spezifikationen mit klar getrennten Verantwortlichkeiten. Zwei gleichzeitig wirksame Statusmappings werden nicht empfohlen.
