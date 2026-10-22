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
- Aktueller Arbeitsbranch für die Ausrichtung der Verzeichnisstruktur am Endprodukt: planning/endproduct-folder-layout-2026-10-22.
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
- Allgemeine, sichtbare Botmeldungen werden aus `Translation.dat` im Projekt-Hauptordner geladen. Die Datei hat Abschnitte und eindeutige Schlüssel im Format Schlüssel=Text. Schlüssel links vom Gleichheitszeichen, Platzhalter wie %user% und mIRC-Formatierungsbestandteile wie $chr(3), $chr(15), $chr(2) und $+ bleiben bei Übersetzungen unverändert.
- Texte, die zu einer Angriffs-/Fähigkeitsdefinition gehören, werden getrennt über den vorgesehenen Datenmechanismus verarbeitet, nicht als allgemeine Translation-Schlüssel behandelt.
- Nach erfolgreichem `on CONNECT` führt `$system_default_check` die Startprüfung aus. Fehlt `System.dat`, legt der Helfer die Datei mit den vorgesehenen Standardwerten an; tatsächlich fehlende Einstellungen werden ebenfalls ergänzt. Vorhandene ungültige Werte bleiben unverändert und deaktivieren die davon abhängige Funktion. Gültige Werte werden für die jeweils verfügbaren Funktionen in globale Variablen übernommen. Fehlende Pflichtmodule und ungültige für einen Ablauf benötigte Spieldaten verhindern den Start der jeweiligen Funktion; eine technische Diagnose erscheint im mIRC-Statusfenster.
- Die vollständige Charaktervorlage dient als Grundlage; Version 0.1 liest und verändert nur die benötigten Felder.

## Noch offen

1. Welche konkreten Argon2id-Parameter verwenden wir? Dies muss bei der Implementierung mit mIRC 7.84 getestet werden.
2. Welches genaue Format, welche Zeichencodierung und welche Validierung benötigt Translation.dat? Wie behandeln wir fehlende oder doppelte Schlüssel?
3. Welche endgültigen Argumente und Fehlermeldungen gelten für die Charakterbefehle?
4. Wie lautet die endgültige Schadensformel, und welche Werte erhalten die fünf eigenen Startmonster?
5. Welche Felder der Charaktervorlage werden in Version 0.1 konkret gelesen oder geändert?
6. Wie verhält sich der Bot bei einem Neustart während einer aktiven Kampfinstanz?
7. Wie werden Nickname-Schreibweise und IRC-netzwerk-spezifische Case-Mapping-Regeln konkret behandelt?
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

Nach erfolgreichem `on CONNECT` ruft der Startablauf `$system_default_check` auf. Fehlt `System.dat`, wird sie mit Standardwerten angelegt; fehlende Einstellungen werden ergänzt. Der Dreiminuten-Timer startet nur, wenn die dafür erforderlichen Konfigurationswerte, `Translation.dat`, Pflichtmodule und benötigten Datenbestände (Monster, Waffen, Techniken, Skills usw.) gültig sind. Ein ungültiger Wert, der nur eine andere Funktion betrifft, verhindert nicht automatisch unabhängige Botfunktionen. Bei Erzeugungs-, Schreib- oder Prüfungsfehlern startet kein Timer; Disconnect bereinigt laufende Timer.


## Verbindliche Ablaufregel: Verbindung und erster Timer

Der erste Schlacht-Timer startet erst nach `on CONNECT` und nachdem `$system_default_check` die Konfiguration sichergestellt hat: Eine fehlende `System.dat` wird mit Standardwerten erstellt und fehlende Einträge werden ergänzt. Danach müssen die für den Schlachtzyklus erforderlichen Konfigurationswerte, `Translation.dat`, Pflichtmodule und benötigten Spieldaten gültig sein. Ein vorhandener ungültiger Einzelwert bleibt unverändert und deaktiviert die davon abhängige Funktion; unabhängige Botfunktionen dürfen weiterarbeiten. Die erste Ankündigung folgt drei Minuten später, sofern ihre Voraussetzungen erfüllt sind. Bei Disconnect werden laufende Timer bereinigt; nach Wiederverbindung darf ein einzelner neuer Zyklus beginnen.


## Neue bestätigte Entscheidungen: Systemprüfung und Anmeldung

- Nach erfolgreichem `on CONNECT` wird `$system_default_check` ausgeführt. Falls `System.dat` fehlt, erzeugt der Helfer sie mit Standardwerten; bei fehlenden Einträgen ergänzt er die Standardwerte. Danach prüft er `System.dat`, `Translation.dat`, Pflichtmodule und die erforderlichen Datenbestände (Monster, Waffen, Techniken, Skills usw.). Technische Fehler bei der Erzeugung, beim Schreiben oder beim erneuten Lesen verhindern den Start der betroffenen Timer. Ein vorhandener ungültiger Einzelwert wird gemeldet, nicht überschrieben und deaktiviert nur die davon abhängige Funktion.
- Nach erfolgreicher Validierung liest der Helfer die Einstellungen in globale Variablen, die weitere Module verwenden. Die fünf besprochenen Laufzeitwerte sollen in Version 0.1 dynamisch aus diesen Variablen verwendet werden.
- Die Anmeldesitzung endet bei Netzwerk-Trennung oder Nickname-Änderung. Das Konto wird nicht übertragen; eine Rückkehr zum ursprünglichen Nick erlaubt eine neue Anmeldung mit dem bisherigen Passwort.


## Bestätigte Entscheidung: Laufzeitwerte aus System.dat und globale Variablen

In Version 0.1 werden die bereits besprochenen fünf Laufzeitwerte aus `System.dat` eingelesen und in globalen mIRC-Variablen gespeichert. Die Spiel- und Timerlogik verwendet diese Variablen; die Zahlenwerte werden nicht zusätzlich in den einzelnen Modulen fest codiert. Beispiel aus dem vorhandenen Endversion-Helfer: `TimeToEnter` wird aus der Konfiguration gelesen und in einer globalen Variable wie `%timeToEnter` bereitgestellt.

### Bestätigte Zuordnung von Einstellungen und Variablen



### Initialisierung und Validierung

- Nach erfolgreichem `on CONNECT` lädt `$system_default_check` die Systemeinstellungen und validiert sie, bevor ein Schlacht-Timer gestartet werden darf.
- Bei erfolgreicher Prüfung stellt der Helfer die Werte in globalen Variablen bereit. Die Fachmodule lesen anschließend die Variablen statt eigene feste Laufzeitwerte zu verwenden.
- Fehlt `System.dat`, legt der V0.1-Helfer die Datei mit Standardwerten an. Fehlen einzelne Einträge, werden sie ebenfalls mit den vorgesehenen Standardwerten ergänzt.
- Die Ergänzung von Standardwerten soll sich am bestehenden Endversion-Prinzip mit `SystemDatVersion` orientieren. Die konkrete Versionsmigration wird für V0.1 vereinfacht und vor der Implementierung spezifiziert.
- Bereits vorhandene, aber ungültige Werte bleiben unverändert. Der Systemhelfer meldet den Schlüssel, den vorhandenen Inhalt und den erwarteten Datentyp bzw. Wertebereich im mIRC-Statusfenster. Die vom Wert abhängige Funktion wird deaktiviert, bis der Nutzer den Eintrag korrigiert und die Konfiguration erneut erfolgreich geprüft wurde; unabhängige Botfunktionen dürfen weiterlaufen.
- Kann die Datei nicht angelegt, geschrieben, erneut gelesen oder erfolgreich validiert werden, startet kein Schlacht-Timer.


## Bestätigte Entscheidung: System.dat bei Bedarf automatisch anlegen

Abweichend von der ursprünglichen Planung muss `System.dat` bei einer frischen Installation nicht bereits vorhanden sein. Der Helfer für Version 0.1 legt sie selbst mit den vorgesehenen Standardwerten an. Fehlen einzelne Einstellungen in einer vorhandenen Datei, ergänzt er ebenfalls die dafür vorgesehenen Standardwerte. Anschließend liest und validiert er die resultierende Konfiguration und stellt die Werte in globalen Variablen bereit. Erst nach erfolgreicher Gesamtprüfung startet der Schlacht-Timer.

Diese automatische Anlage gilt nur für `System.dat`. Fehlende Pflichtmodule, `Translation.dat` oder notwendige und ungültige Spieldaten bleiben Fehler der Startprüfung. Bereits vorhandene ungültige Einstellungswerte bleiben unverändert. Der Helfer meldet den konkreten Fehler und deaktiviert die abhängige Funktion, bis der Nutzer den Eintrag korrigiert und die Konfiguration erneut erfolgreich geprüft wurde. Scheitert das Schreiben oder die anschließende Validierung, startet kein Schlacht-Timer.


## Bestätigte Entscheidung: vorhandene ungültige System.dat-Werte nicht verändern

Wenn ein vorhandener Eintrag in `System.dat` einen ungültigen Wert enthält, bleibt dieser Eintrag unverändert. Der V0.1-Helfer meldet den Schlüsselnamen, den gefundenen Inhalt und den erwarteten Datentyp bzw. Wertebereich im mIRC-Statusfenster. Die jeweilige Funktion bleibt deaktiviert, bis der Nutzer den Wert selbst korrigiert und die Konfiguration erneut erfolgreich geprüft wurde. Unabhängige Botfunktionen dürfen weiterarbeiten. Ein Schlacht-Timer oder Kampfablauf startet nur, wenn die dafür erforderlichen Einstellungen gültig sind.


## Bestätigte Abgrenzung: Endversion-Helfer und V0.1-Helfer

Der vom Nutzer gezeigte `system_defaults_check` ist der Helfer für die angestrebte Endversion und dient bei der Planung als Referenz für das Prinzip: Einstellungen mit Standardwerten ergänzen, prüfen und in globalen Variablen bereitstellen. Er wird nicht unverändert in den Prototyp kopiert.

Für V0.1 wird ein eigener, reduzierter Systemhelfer entworfen. Er bearbeitet nur Einstellungen, Module, Verzeichnisse und Datenbestände, die der kleine Prototyp tatsächlich benötigt. Endversionspezifische Funktionen (beispielsweise Bosse, Mechs, NPCs, Shops, Auktionen, Dungeons, zusätzliche Events und alte Datenbereinigungen) werden nicht allein deshalb übernommen, weil sie im Endversion-Helfer stehen.

Nächster Planungsschritt ist eine vollständige V0.1-Standardwertliste: jeden benötigten Eintrag festlegen, vorhandene Schlüssel aus dem Endversion-Helfer wiederverwenden, wo passend, neue Schlüssel nur bei Bedarf ergänzen und alle übrigen Endversion-Einträge explizit aus dem V0.1-Umfang ausschließen. Es werden weiterhin keine produktiven Skript- oder Datendateien angelegt.


## Bestätigte Standardwerte für V0.1: sechs Laufzeit-Einstellungen

Die folgende Liste ist nicht mehr nur ein Vorschlag. Alle fünf Einstellungen werden in Version 0.1 aus `System.dat` gelesen, validiert und in globalen mIRC-Variablen gespeichert. Die Timer- und Kampfmodule nutzen diese Variablen statt eigener fest codierter Laufzeitwerte.



## Bestätigte Entscheidung: Ordnerstruktur schon in V0.1 am Endprodukt ausrichten

Die Verzeichnisstruktur wird nicht nur auf den Prototyp zugeschnitten. V0.1 verwendet von Anfang an das geplante Endprodukt-Layout, damit später keine Datenordner verschoben oder umbenannt werden müssen. Die Einstiegsdatei `Colosseum.mrc`, die Fachmodule, die Alias-Dateien sowie `System.dat` und `Translation.dat` liegen im Projekt-Hauptordner. Ein separater `scripts/`- oder `data/`-Ordner wird für diese Dateien nicht eingeführt.

Auf der obersten Projektebene sind die Verzeichnisse `characters/`, `bosses/`, `monsters/`, `zapped/`, `npcs/`, `summons/` und `help-files/` vorgesehen, angelehnt an die Standardpfade aus dem Endversion-Helfer. V0.1 benötigt insbesondere `characters/` und `monsters/`; die übrigen Ordner stehen für spätere Funktionen und werden in V0.1 noch nicht mit diesen Funktionen implementiert. Die Übersetzungsdatei heißt weiterhin `Translation.dat`, liegt aber im Projekt-Hauptordner statt unter `data/Translation.dat`.

## Bestätigte Entscheidung: Wertebereiche für System.dat


Dezimalzahlen, nicht numerische Werte sowie Zeitwerte außerhalb des zulässigen Bereichs gelten als ungültig. Der Systemhelfer verändert den vorhandenen Eintrag nicht. Er meldet den gefundenen Wert, den gültigen Wertebereich und welche Funktion deshalb deaktiviert bleibt. Der Nutzer korrigiert die Datei selbst; erst nach einer erneuten erfolgreichen Prüfung wird die abhängige Funktion wieder aktiviert.



## Bestätigte Entscheidung: System.dat bleibt eine Klartextdatei

`System.dat` bleibt eine normale, unverschlüsselte Textdatei. Es wird kein verschlüsseltes Dateiformat und kein spezieller automatischer Bereinigungsmechanismus für manuell eingefügte doppelte Schlüsselzeilen eingeführt. Der Nutzer kann die Datei bearbeiten, muss dabei jedoch den dokumentierten Aufbau, die Abschnitts- und Schlüsselnamen sowie die zulässigen Wertebereiche beachten.

Der Systemhelfer prüft weiterhin die fünf festgelegten V0.1-Einstellungen. Vorhandene ungültige Werte werden nicht automatisch ersetzt oder in der Datei geändert. Die betroffene Funktion wird deaktiviert und eine Diagnose erklärt den Fehler sowie den erwarteten Wert bzw. Wertebereich. Erst nach einer manuellen Korrektur und erneuten erfolgreichen Prüfung wird die Funktion wieder aktiviert. Unbekannte Schlüssel werden von V0.1 nicht verwendet und nicht automatisch gelöscht.

Doppelte Schlüssel, falsch geschriebene Abschnitte oder andere manuelle Strukturänderungen werden nicht durch eine eigene Reparaturroutine bereinigt. Solche Änderungen können bewirken, dass die Konfiguration fehlerhaft ist oder das Skript nicht wie vorgesehen funktioniert. Eine ausführliche Dokumentation wird später erklären, wie `System.dat` korrekt bearbeitet wird und dass manuelle Änderungen in der Verantwortung des Nutzers liegen.


## Bestätigte Entscheidung: ungültige Laufzeitwerte lassen und betroffene Funktion deaktivieren

Enthält ein vorhandener Eintrag in `System.dat` einen ungültigen Wert, verändert Colosseum diesen Eintrag nicht. Stattdessen meldet der Systemhelfer im mIRC-Statusfenster den Schlüsselnamen, den gefundenen Inhalt und den erwarteten Datentyp bzw. zulässigen Wertebereich. Die Funktion, die diesen Wert benötigt, bleibt deaktiviert, bis der Nutzer die Datei selbst korrigiert und die Konfiguration erneut erfolgreich geprüft wurde. Unabhängige Botfunktionen dürfen weiterlaufen.

Die bereits beschlossene automatische Anlage einer fehlenden `System.dat` und das Ergänzen tatsächlich fehlender Einträge mit Standardwerten bleiben bestehen. Davon klar getrennt ist ein vorhandener, aber ungültiger Wert: Dieser wird niemals stillschweigend überschrieben. Die automatische Bereinigung manuell eingefügter doppelter Schlüsselzeilen ist weiterhin nicht vorgesehen.

Für den Schlachtzyklus gilt: Ohne gültige Werte für die jeweils benötigten Ablauf- und Zugzeiten dürfen die abhängigen Timer bzw. Kampfabläufe nicht starten. Die konkrete erneute Prüfung nach einer manuellen Änderung während einer laufenden Verbindung wird vor der Implementierung festgelegt.

## Verbindliche System.dat-Einstellungen für Version 0.1

Die folgenden fünf Einstellungen sind für Version 0.1 festgelegt. Es werden ausschließlich offizielle Wiki-Schlüsselnamen verwendet. Die Endversion-Wiki beschreibt weiterhin das angestrebte Endprodukt; die hier genannten Wertebereiche und die prototypspezifische Verwendung gelten für Version 0.1.


### Inaktivität im Kampf

In Version 0.1 wird die Inaktivität über den gesamten Kampf gezählt. Nach dem ersten abgelaufenen Zuglimit wird der Spielerzug übersprungen und das Monster kann erneut angreifen. Erst wenn der Spieler zum zweiten Mal (bei `MaxIdleTurns=2`) keine Aktion ausführt, scheidet er aus. Ein zwischenzeitlich ausgeführter Angriff setzt den Zähler verpasster Züge nicht zurück. Da V0.1 nur einen Spieler pro Kampf zulässt, endet der Kampf dann als Niederlage ohne Belohnung.

### Belohnung und Wiki-Bedeutung

Das offizielle Wiki beschreibt `MaxOrbReward` als Obergrenze der möglichen Kugelbelohnung vor späteren Modifikatoren wie Skills und Accessoires. Version 0.1 verwendet denselben offiziellen Schlüssel prototypspezifisch für eine feste Belohnung von 10 roten Kugeln bei einem Sieg, sofern der Spieler überlebt. Skills, Styles, Accessoires und weitere Belohnungsmodifikatoren sind in V0.1 nicht vorhanden. Bei Niederlage oder Ausscheiden wegen Inaktivität gibt es keine Belohnung.

### Prüfung der Werte

Zeitwerte müssen ganze Zahlen innerhalb der für den jeweiligen Schlüssel angegebenen Grenzen sein. `MaxIdleTurns` muss eine ganze Zahl zwischen 1 und 10 sein. In Version 0.1 ist `MaxOrbReward` auf 10 festgelegt. Vorhandene ungültige Werte werden nicht automatisch überschrieben: Die Diagnose nennt Schlüssel, gefundenen Wert und erwarteten Bereich; die abhängige Funktion bleibt deaktiviert, bis der Nutzer den Wert korrigiert und die Konfiguration erneut erfolgreich geprüft wurde. Fehlende Einstellungen dürfen mit den festgelegten Standardwerten ergänzt werden.

## Verbindliche System.dat-Einstellungen für Version 0.1

Die folgenden fünf Einstellungen sind für Version 0.1 festgelegt. Es werden ausschließlich offizielle Wiki-Schlüsselnamen verwendet. Die Endversion-Wiki beschreibt weiterhin das angestrebte Endprodukt; die hier genannten Wertebereiche und die prototypspezifische Verwendung gelten für Version 0.1.

| Schlüssel | Standardwert für V0.1 | Zulässiger Bereich | Einheit / Bedeutung |
|---|---:|---|---|
| `TimeBetweenBattles` | `3` | `1–240` | Minuten; Wartezeit bis zur nächsten Schlachtankündigung im Leerlauf nach leerem Beitrittsfenster oder Kampfende. Bei der Übergabe an einen mIRC-Timer wird der Minutenwert in Sekunden umgerechnet. |
| `TimeToEnter` | `120` | `30–300` | Sekunden; Dauer des Beitrittsfensters. Der Wert wird nicht erneut umgerechnet. |
| `TimeForIdle` | `180` | `30–600` | Sekunden; Zeitlimit für einen Spielerzug. |
| `MaxIdleTurns` | `2` | `1–10` | Ganze Zahl; Anzahl verpasster Spielerzüge, nach der der Spieler aus dem Kampf ausscheidet. |
| `MaxOrbReward` | `10` | Für V0.1 fest auf `10` gesetzt | Rote Kugeln; im Prototyp als feste Siegesbelohnung verwendet. |

### Inaktivität im Kampf

In Version 0.1 wird die Inaktivität über den gesamten Kampf gezählt. Nach dem ersten abgelaufenen Zuglimit wird der Spielerzug übersprungen und das Monster kann erneut angreifen. Erst wenn der Spieler zum zweiten Mal (bei `MaxIdleTurns=2`) keine Aktion ausführt, scheidet er aus. Ein zwischenzeitlich ausgeführter Angriff setzt den Zähler verpasster Züge nicht zurück. Da V0.1 nur einen Spieler pro Kampf zulässt, endet der Kampf dann als Niederlage ohne Belohnung.

### Belohnung und Wiki-Bedeutung

Das offizielle Wiki beschreibt `MaxOrbReward` als Obergrenze der möglichen Kugelbelohnung vor späteren Modifikatoren wie Skills und Accessoires. Version 0.1 verwendet denselben offiziellen Schlüssel prototypspezifisch für eine feste Belohnung von 10 roten Kugeln bei einem Sieg, sofern der Spieler überlebt. Skills, Styles, Accessoires und weitere Belohnungsmodifikatoren sind in V0.1 nicht vorhanden. Bei Niederlage oder Ausscheiden wegen Inaktivität gibt es keine Belohnung.

### Prüfung der Werte

Zeitwerte müssen ganze Zahlen innerhalb der für den jeweiligen Schlüssel angegebenen Grenzen sein. `MaxIdleTurns` muss eine ganze Zahl zwischen 1 und 10 sein. In Version 0.1 ist `MaxOrbReward` auf 10 festgelegt. Vorhandene ungültige Werte werden nicht automatisch überschrieben: Die Diagnose nennt Schlüssel, gefundenen Wert und erwarteten Bereich; die abhängige Funktion bleibt deaktiviert, bis der Nutzer den Wert korrigiert und die Konfiguration erneut erfolgreich geprüft wurde. Fehlende Einstellungen dürfen mit den festgelegten Standardwerten ergänzt werden.

