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
- Aktueller Arbeitsbranch für die vorgeschlagenen `System.dat`-Einstellungen: planning/systemdat-settings-2026-10-22.
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
- `$system_default_check` prüft nach `on CONNECT` die `System.dat`, `Translation.dat`, Pflichtmodule sowie vorhandene/gültige Datenbestände für Monster, Waffen, Techniken, Skills usw. Jeder Fehler verhindert den Schlacht-Timerstart; eine technische Diagnose erscheint im mIRC-Statusfenster.
- `System.dat` und `$system_default_check` sind bereits für die Startprüfung von Version 0.1 vorgesehen. Der Helfer liest gültige Werte in globale Variablen. Noch festzulegen ist, welche Einstellungen Version 0.1 aktiv nutzt und welche Zeitwerte vorerst fest bleiben.
- Die vollständige Charaktervorlage dient als Grundlage; Version 0.1 liest und verändert nur die benötigten Felder.

## Noch offen

1. Welche konkreten Argon2id-Parameter verwenden wir? Dies muss bei der Implementierung mit mIRC 7.84 getestet werden.
2. Welches genaue Format, welche Zeichencodierung und welche Validierung benötigt Translation.dat? Wie behandeln wir fehlende oder doppelte Schlüssel?
3. Welche endgültigen Argumente und Fehlermeldungen gelten für die Charakterbefehle?
4. Wie lautet die endgültige Schadensformel, und welche Werte erhalten die fünf eigenen Startmonster?
5. Welche Felder der Charaktervorlage werden in Version 0.1 konkret gelesen oder geändert?
6. Wie verhält sich der Bot bei einem Neustart während einer aktiven Kampfinstanz?
7. Wie werden Nickname-Schreibweise und IRC-netzwerk-spezifische Case-Mapping-Regeln konkret behandelt?
8. Den Vorschlag für `System.dat`-Schlüssel und Werte prüfen: Welche davon sollen in Version 0.1 tatsächlich konfigurierbar und verpflichtend sein?
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

Nach erfolgreichem `on CONNECT` ruft der Startablauf `$system_default_check` auf. Nur bei erfolgreicher Prüfung von `System.dat`, `Translation.dat`, Pflichtmodulen und benötigten Datenbeständen (Monster, Waffen, Techniken, Skills usw.) startet der Dreiminuten-Timer. Bei Fehlern startet kein Timer; Disconnect bereinigt laufende Timer.


## Verbindliche Ablaufregel: Verbindung und erster Timer

Der erste Schlacht-Timer startet erst nach `on CONNECT` und nachdem `$system_default_check` alle Pflichtprüfungen (einschließlich `System.dat`, `Translation.dat` und benötigter Spieldaten) erfolgreich abgeschlossen hat. Die erste Ankündigung folgt drei Minuten später. Bei Disconnect werden laufende Timer bereinigt; nach Wiederverbindung darf ein einzelner neuer Zyklus beginnen.


## Neue bestätigte Entscheidungen: Systemprüfung und Anmeldung

- Nach erfolgreichem `on CONNECT` wird `$system_default_check` ausgeführt. Er prüft `System.dat`, `Translation.dat`, Pflichtmodule und die erforderlichen Datenbestände (Monster, Waffen, Techniken, Skills usw.). Jeder fehlgeschlagene Check verhindert den Start des Schlacht-Timers.
- Bei erfolgreicher Prüfung liest der Helfer gültige Systemwerte aus `System.dat` in globale Variablen, die weitere Module verwenden können. Die konkrete Liste der Schlüssel und welche Werte in Version 0.1 tatsächlich dynamisch angewendet werden, ist noch festzulegen.
- Die Anmeldesitzung endet bei Netzwerk-Trennung oder Nickname-Änderung. Das Konto wird nicht übertragen; eine Rückkehr zum ursprünglichen Nick erlaubt eine neue Anmeldung mit dem bisherigen Passwort.


## Vorschlag zur Prüfung: System.dat-Schlüssel für Version 0.1

Die folgenden Werte sind ein **Vorschlag zur gemeinsamen Prüfung**, noch keine endgültig freigegebene Konfigurationsschnittstelle. Die Zahlen entsprechen bereits besprochenen Spielregeln; neu ist nur der Vorschlag, sie über `System.dat` steuerbar zu machen.

| Vorgeschlagener Schlüssel | Vorgeschlagener Wert | Verwendung |
|---|---:|---|
| `battle.first_announcement_delay_seconds` | `180` | Wartezeit nach erfolgreicher Verbindung und abgeschlossener Startprüfung bis zur ersten Schlachtankündigung |
| `battle.join_window_seconds` | `120` | Beitrittsfenster einer angekündigten Schlacht |
| `battle.next_announcement_delay_seconds` | `180` | Wartezeit bis zur nächsten Ankündigung nach leerer Beitrittsphase oder Kampfende |
| `battle.player_turn_timeout_seconds` | `180` | Zeitlimit für einen Spielerzug |
| `battle.max_skipped_player_turns` | `2` | Anzahl übersprungener Spielerzüge, nach der der Kampf als Niederlage endet |
| `battle.victory_red_orbs` | `10` | Belohnung für einen Sieg mit überlebendem Spieler |

Die Schlüsselbezeichnungen sind vorläufig; auch das genaue Syntax- und Dateiformat von `System.dat` ist noch nicht festgelegt.

### Vorgeschlagene Prüfregeln

- Wenn dieser Satz von Einstellungen freigegeben wird, muss jeder Schlüssel in Version 0.1 genau einmal vorhanden sein und einen gültigen ganzzahligen Wert enthalten.
- Zeitwerte und die Anzahl erlaubter übersprungener Züge müssen positiv sein; die Belohnung darf nicht negativ sein.
- Eine fehlende, doppelte, nicht numerische oder ungültige Pflichtangabe lässt die Startprüfung fehlschlagen. Für diese Pflichtwerte wird nicht stillschweigend ein Ersatzwert eingesetzt und der Schlacht-Timer startet nicht.
- Die Prüfung von Konfigurationseinstellungen bleibt getrennt von der Prüfung, ob erforderliche Module und Spieldaten tatsächlich vorhanden und verwendbar sind.
- Die sechs Einträge sind ein erster Vorschlag. Vor Implementierung sind Freigabe, genaue Grenzen und der Umgang mit zusätzlichen unbekannten Schlüsseln zu klären.
