# Colosseum – aktueller Projektstand

**Stand:** 22. Oktober 2026  
**Repository:** [Zenturion58/kolosseum](https://github.com/Zenturion58/kolosseum)  
**Status:** Bestandsaufnahme und Architekturplanung. Noch keine produktive mIRC-Implementierung.

Diese Datei ist als dauerhafte Übergabe für neue Chat-Unterhaltungen gedacht. Sie fasst bestätigte Entscheidungen, offene Fragen und die nächsten Schritte zusammen. Bei Widersprüchen sind die jüngsten bestätigten Entscheidungen in den beiden Spezifikationen maßgeblich.

## Zentrale Dokumente

- [Bestandsaufnahme](Colosseum_Bestandsaufnahme.md)
- [Bestandsaufnahme – technische Grundlagen](Colosseum_Bestandsaufnahme_Technische_Grundlagen.md)
- [Umfang Version 0.1](Colosseum_Umfang_Version_0.1.md)
- [Architektur Version 0.1](Colosseum_Architektur_Version_0.1.md)
- [Ursprüngliches GitHub-Wiki](https://github.com/Zenturion58/kolosseum/wiki)

## Projektziel

Colosseum ist ein geplanter deutschsprachiger, rundenbasierter mIRC-IRC-Kampfsimulator. Das Wiki beschreibt das angestrebte Endprodukt. Version 0.1 ist bewusst kleiner und soll Registrierung, dauerhafte Speicherung und einen normalen Monsterkampf erstmals funktionsfähig umsetzen. Der Quellcode wird neu geschrieben; vorhandener Quellcode liegt nicht vor.

## Arbeitsregeln

- Wir befinden uns weiterhin in der Bestandsaufnahme und Architekturplanung.
- Noch keine produktiven MRC-, ALS- oder DAT-Dateien anlegen.
- Wichtige Entscheidungen werden in den Planungsdokumenten festgehalten und in einem separaten GitHub-Branch als Pull Request zur Prüfung vorbereitet. So kann eine neue Unterhaltung anhand dieses Dokuments und der Spezifikationen weiterarbeiten.
- Der Hauptbranch main wird nicht ohne Prüfung und Freigabe geändert.
- Der frühere Verbindungstest wurde aufgeräumt: Die Testdatei ist entfernt und der Test-Branch wurde vom Nutzer gelöscht.
- Aktueller Arbeitsbranch für die Standardwert-Erzeugung von `System.dat`: planning/systemdat-default-generation-2026-10-22.
- Dieser Branch enthält nur Planungsdokumentation und Statusnotizen, keinen produktiven Spielcode.

## Bestätigte Anforderungen für Version 0.1

### Charakterkonten und Anmeldung

- Pro IRC-Nickname ist genau ein Charakterkonto erlaubt.
- Das Konto bleibt an den bei der Registrierung verwendeten Nickname gebunden. Ein Nickwechsel überträgt es nicht auf den neuen Nick; die Charakterdatei wird durch den Nickwechsel nicht automatisch gelöscht.
- Eine Anmeldesitzung endet bei Netzwerk-Trennung oder Nickname-Änderung. Bei Rückkehr zum ursprünglichen Nick kann der Spieler sich erneut mit seinem bisherigen Passwort anmelden.
- Befehle: !registrieren, !anmelden mit Passwort, !abmelden, !passwort mit altem und neuem Passwort sowie !werte.
- !registrieren funktioniert im Kanal oder per privater Nachricht; der Befehl enthält kein Passwort.
- Das automatisch erzeugte Startpasswort wird nur privat gesendet. Bei einem erkennbaren Zustellfehler wird der Spieler informiert, die Registrierung abgebrochen und der Datensatz entfernt. Das Passwort darf niemals öffentlich ausgegeben werden. IRC liefert nicht für jede Nichtzustellung ein verlässliches Fehlerereignis.
- Anmeldung und Passwortänderung erfolgen ausschließlich per privater Nachricht. Persönliche Profilfunktionen erfordern vorherige Anmeldung.
- !werte zeigt privat HP, TP und die vier Kernattribute Stärke, Verteidigung, Intelligenz und Schnelligkeit.
- Passwörter werden nie im Klartext gespeichert. Vorgesehen ist Argon2id über mIRCs $argon2 mit individuellem zufälligem Salt. Mindestversion ist mIRC 7.83; die Entwicklungsumgebung des Nutzers ist mIRC 7.84.
- Der administrative Passwort-Reset ist für Version 0.1 zurückgestellt.

### Kampfablauf

- Es gibt höchstens eine aktive Kampfinstanz. Version 0.1 nimmt pro Kampf höchstens einen Spieler auf; Mehrspieler-Unterstützung ist für später geplant.
- Nach erfolgreichem IRC-Verbindungsereignis (`on CONNECT`) wird `$system_default_check` aufgerufen. Der Schlacht-Timer startet nur, wenn alle Pflichtprüfungen erfolgreich sind; die erste Ankündigung folgt drei Minuten danach.
- Der automatische Schlachtzyklus kündigt eine Schlacht an; die Beitrittsfrist beträgt 120 Sekunden.
- Nach leerer Beitrittsphase oder nach Kampfende kündigt der Bot die nächste Schlacht in drei Minuten an.
- Pro Kampf wird genau ein zufälliges normales Monster ausgewählt. Mindestens fünf eigene Monster sind vorgesehen; Bosse gehören nicht zu Version 0.1.
- Die Zugreihenfolge richtet sich nach Speed. Bei gleichem Speed entscheidet der Zufall.
- Der Spieler kämpft mit bloßen Fäusten. Monster nutzen zunächst nur einen normalen Nahkampfangriff.
- Beide Angriffsmethoden sollen vorhanden sein: die IRC-Aktion /me attacks Monster und der deutsche Befehl !angriff. Beide verwenden dieselbe interne Angriffslogik.
- Ein Spielerzug hat ein Zeitlimit von 180 Sekunden. Nach zwei übersprungenen Spielerzügen endet der Kampf als Niederlage.
- Die Schadensberechnung ist fest und zunächst ohne Zufallsfaktor. Sie berücksichtigt die Stärke des Angreifers und die Verteidigung des Ziels; erfolgreicher Angriff verursacht mindestens 1 Schaden. Die genaue Formel ist noch offen.
- Nach jedem Kampf werden die Kampfwerte auf die Grundwerte zurückgesetzt.
- Ein überlebender Sieger erhält zehn rote Kugeln. Bei Niederlage oder Inaktivitätsausschluss gibt es keine Belohnung. Neue Charaktere starten mit null roten Kugeln. Ein Shop ist nicht Bestandteil von Version 0.1.

### Architektur und Daten

- Programmiersprache: mIRC Script; Mindestversion 7.83.
- Colosseum.mrc ist die einzige manuell zu ladende Einstiegsdatei.
- Fachbefehle und zugehörige IRC-Handler liegen in den zuständigen MRC-Fachmodulen. Es gibt keine zentrale Events.mrc.
- Wiederverwendbare Helfer liegen in aufgabenbezogenen ALS-Dateien: SystemAliases.als, Characters.als, BattleControl.als, BattleFormula.als, Monsters.als, BattleRewards.als und TranslationAliases.als.
- Character.mrc verantwortet Charakterfunktionen einschließlich charakterbezogener Speicherung; Characters.als enthält zugehörige wiederverwendbare Helfer. Eine separate Storage.mrc ist nicht vorgesehen.
- Allgemeine, sichtbare Botmeldungen werden aus data/Translation.dat geladen. Die Datei hat Abschnitte und eindeutige Schlüssel im Format Schlüssel=Text. Schlüssel links vom Gleichheitszeichen, Platzhalter wie %user% und mIRC-Formatierungsbestandteile wie $chr(3), $chr(15), $chr(2) und $+ bleiben bei Übersetzungen unverändert.
- Texte, die zu einer Angriffs-/Fähigkeitsdefinition gehören, werden getrennt über den vorgesehenen Datenmechanismus verarbeitet, nicht als allgemeine Translation-Schlüssel behandelt.
- Nach erfolgreichem `on CONNECT` führt `$system_default_check` die Startprüfung aus. Fehlt `System.dat`, legt der Helfer die Datei mit den vorgesehenen Standardwerten an; fehlende Einstellungen werden ebenfalls ergänzt. Anschließend prüft er die Werte und speichert sie in globalen Variablen. Fehlende Pflichtmodule, fehlerhafte Konfigurationswerte oder ungültige erforderliche Spieldaten verhindern den Schlacht-Timerstart; eine technische Diagnose erscheint im mIRC-Statusfenster.
- Die vollständige Charaktervorlage dient als Grundlage; Version 0.1 liest und verändert nur die benötigten Felder.

## Noch offen

1. Welche konkreten Argon2id-Parameter verwenden wir? Dies muss bei der Implementierung mit mIRC 7.84 getestet werden.
2. Welches genaue Format, welche Zeichencodierung und welche Validierung benötigt Translation.dat? Wie behandeln wir fehlende oder doppelte Schlüssel?
3. Welche endgültigen Argumente und Fehlermeldungen gelten für die Charakterbefehle?
4. Wie lautet die endgültige Schadensformel, und welche Werte erhalten die fünf eigenen Startmonster?
5. Welche Felder der Charaktervorlage werden in Version 0.1 konkret gelesen oder geändert?
6. Wie verhält sich der Bot bei einem Neustart während einer aktiven Kampfinstanz?
7. Wie werden Nickname-Schreibweise und IRC-netzwerk-spezifische Case-Mapping-Regeln konkret behandelt?
8. Endgültige Schlüsselnamen und Wertebereiche sowie die Behandlung bereits vorhandener ungültiger Werte festlegen; außerdem die Abweichung `TimeBetweenBattles` (Endversion 120 Sekunden, v0.1 bislang 180 Sekunden) auflösen.
9. Lizenz und Herkunft vorhandener Dokumentationsinhalte prüfen, bevor das daraus abgeleitete Projekt öffentlich veröffentlicht wird.

## Nächste Arbeitsschritte

1. Den Charakterregistrierungsablauf und die Befehlsdetails weiter konkretisieren.
2. Translation.dat-Format und die Startprüfung spezifizieren.
3. Modulschnittstellen und Ladelogik finalisieren.
4. Die aktualisierte Planung über den Pull Request prüfen.
5. Erst nach ausreichender Planungsfreigabe produktive Dateien anlegen.
6. Bootstrap und Registrierung testen, danach einen vollständigen einfachen Monsterkampf implementieren.


## Neue bestätigte Entscheidung: Nickname-Rückkehr

Ein Konto wird nicht auf einen geänderten Nickname übertragen und bei einem Nickwechsel nicht automatisch gelöscht. Kehrt der Nutzer zum ursprünglichen Nickname zurück, kann er sich mit dem bisherigen Passwort anmelden. Die genaue IRC-Case-Mapping-Behandlung bleibt vor der Implementierung zu prüfen.


## Neue bestätigte Entscheidung: Timer-Start

Nach erfolgreichem `on CONNECT` ruft der Startablauf `$system_default_check` auf. Fehlt `System.dat`, wird sie mit Standardwerten angelegt; fehlende Einstellungen werden ergänzt. Nur wenn danach die Konfiguration, `Translation.dat`, Pflichtmodule und benötigten Datenbestände (Monster, Waffen, Techniken, Skills usw.) erfolgreich geprüft sind, startet der Dreiminuten-Timer. Bei Erzeugungs-, Schreib- oder Prüfungsfehlern startet kein Timer; Disconnect bereinigt laufende Timer.


## Verbindliche Ablaufregel: Verbindung und erster Timer

Der erste Schlacht-Timer startet erst nach `on CONNECT` und nachdem `$system_default_check` die Konfiguration sichergestellt hat: Eine fehlende `System.dat` wird mit Standardwerten erstellt und fehlende Einträge werden ergänzt. Danach müssen die validierte Konfiguration, `Translation.dat`, Pflichtmodule und benötigte Spieldaten erfolgreich geprüft sein. Die erste Ankündigung folgt drei Minuten später. Bei Disconnect werden laufende Timer bereinigt; nach Wiederverbindung darf ein einzelner neuer Zyklus beginnen.


## Neue bestätigte Entscheidungen: Systemprüfung und Anmeldung

- Nach erfolgreichem `on CONNECT` wird `$system_default_check` ausgeführt. Falls `System.dat` fehlt, erzeugt der Helfer sie mit Standardwerten; bei fehlenden Einträgen ergänzt er die Standardwerte. Danach prüft er `System.dat`, `Translation.dat`, Pflichtmodule und die erforderlichen Datenbestände (Monster, Waffen, Techniken, Skills usw.). Fehler bei der Erzeugung, beim Schreiben oder bei der Prüfung verhindern den Start des Schlacht-Timers.
- Nach erfolgreicher Validierung liest der Helfer die Einstellungen in globale Variablen, die weitere Module verwenden. Die sechs besprochenen Laufzeitwerte sollen in Version 0.1 dynamisch aus diesen Variablen verwendet werden.
- Die Anmeldesitzung endet bei Netzwerk-Trennung oder Nickname-Änderung. Das Konto wird nicht übertragen; eine Rückkehr zum ursprünglichen Nick erlaubt eine neue Anmeldung mit dem bisherigen Passwort.


## Bestätigte Entscheidung: Laufzeitwerte aus System.dat und globale Variablen

In Version 0.1 werden die bereits besprochenen sechs Laufzeitwerte aus `System.dat` eingelesen und in globalen mIRC-Variablen gespeichert. Die Spiel- und Timerlogik verwendet diese Variablen; die Zahlenwerte werden nicht zusätzlich in den einzelnen Modulen fest codiert. Beispiel aus dem vorhandenen Endversion-Helfer: `TimeToEnter` wird aus der Konfiguration gelesen und in einer globalen Variable wie `%timeToEnter` bereitgestellt.

### Vorläufige Zuordnung von Einstellungen und Variablen

| Zweck | Schlüssel in `System.dat` (Arbeitsname) | Wert für Version 0.1 | Globale Variable (Arbeitsname) |
|---|---|---:|---|
| Erste Schlachtankündigung nach erfolgreicher Verbindung und Prüfung | `TimeBeforeFirstBattle` (neu vorgeschlagen) | `180` Sekunden | `%timeBeforeFirstBattle` |
| Beitrittsfenster | `TimeToEnter` (bereits im Endversion-Helfer vorhanden) | `120` Sekunden | `%timeToEnter` |
| Abstand bis zur nächsten Ankündigung nach leerer Beitrittsphase oder Kampfende | `TimeBetweenBattles` (bereits vorhanden; Wert abzugleichen) | laut bisheriger v0.1-Planung `180` Sekunden | `%timeBetweenBattles` |
| Zeitlimit eines Spielerzugs | `TimeForIdle` (bereits im Endversion-Helfer vorhanden) | `180` Sekunden | `%timeForIdle` |
| Übersprungene Spielerzüge bis zur Niederlage | `MaxIdleTurns` (bereits im Endversion-Helfer vorhanden) | `2` | `%maxIdleTurns` |
| Belohnung für einen Sieg mit überlebendem Spieler | `VictoryOrbReward` (neu vorgeschlagen) | `10` rote Kugeln | `%victoryOrbReward` |

Die genaue Benennung der neu vorgeschlagenen Schlüssel und Variablen muss vor der Implementierung noch bestätigt werden. Insbesondere enthält der übergebene Endversion-Helfer für `TimeBetweenBattles` den Standardwert `120`, während die bisher beschlossene v0.1-Regel eine nächste Ankündigung nach `180` Sekunden vorsieht. Diese Abweichung darf bei der Anpassung für v0.1 nicht unbemerkt übernommen werden.

### Initialisierung und Validierung

- Nach erfolgreichem `on CONNECT` lädt `$system_default_check` die Systemeinstellungen und validiert sie, bevor ein Schlacht-Timer gestartet werden darf.
- Bei erfolgreicher Prüfung stellt der Helfer die Werte in globalen Variablen bereit. Die Fachmodule lesen anschließend die Variablen statt eigene feste Laufzeitwerte zu verwenden.
- Fehlt `System.dat`, legt der V0.1-Helfer die Datei mit Standardwerten an. Fehlen einzelne Einträge, werden sie ebenfalls mit den vorgesehenen Standardwerten ergänzt.
- Die Ergänzung von Standardwerten soll sich am bestehenden Endversion-Prinzip mit `SystemDatVersion` orientieren. Die konkrete Versionsmigration wird für V0.1 vereinfacht und vor der Implementierung spezifiziert.
- Bereits vorhandene, aber ungültige Werte dürfen nicht stillschweigend ungeprüft verwendet werden. Ob sie automatisch auf den Standardwert zurückgesetzt werden oder die Startprüfung fehlschlägt, bleibt noch zu entscheiden.
- Kann die Datei nicht angelegt, geschrieben, erneut gelesen oder erfolgreich validiert werden, startet kein Schlacht-Timer.


## Bestätigte Entscheidung: System.dat bei Bedarf automatisch anlegen

Abweichend von der ursprünglichen Planung muss `System.dat` bei einer frischen Installation nicht bereits vorhanden sein. Der Helfer für Version 0.1 legt sie selbst mit den vorgesehenen Standardwerten an. Fehlen einzelne Einstellungen in einer vorhandenen Datei, ergänzt er ebenfalls die dafür vorgesehenen Standardwerte. Anschließend liest und validiert er die resultierende Konfiguration und stellt die Werte in globalen Variablen bereit. Erst nach erfolgreicher Gesamtprüfung startet der Schlacht-Timer.

Diese automatische Anlage gilt nur für `System.dat`. Fehlende Pflichtmodule, `Translation.dat` oder notwendige und ungültige Spieldaten bleiben Fehler der Startprüfung. Bereits vorhandene, aber ungültige Einstellungswerte werden nicht ungeprüft übernommen; ob sie auf Standardwerte zurückgesetzt werden oder den Start abbrechen, ist noch offen.
