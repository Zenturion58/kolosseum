# Colosseum – Umfang und Entscheidungen für Version 0.1

**Status:** Arbeitsgrundlage mit bestätigten Regeln und offenen Punkten.  
**Leitlinie:** Das GitHub-Wiki beschreibt das angestrebte Endprodukt. Diese Datei grenzt davon den ersten spielbaren Entwicklungsstand ab.

## 1. Verbindliche Leitlinien

- Es gibt noch keinen vorhandenen Quellcode. Die Implementierung wird neu entworfen und selbst geschrieben.
- Der Bot und alle für Spieler sichtbaren Nachrichten, Hilfetexte und Fehlermeldungen sollen standardmäßig auf Deutsch sein.
- Allgemeine Botmeldungen, System-/Profilmeldungen, Schlachtankündigungen und Belohnungstexte werden von Anfang an aus `Translation.dat` geladen; diese Texte werden nicht direkt in die Spiellogik eingebaut. Die Datei nutzt Abschnitte und Schlüssel in der Form `Schlüssel=Textvorlage`. Übersetzer ändern nur den Text rechts vom `=` und lassen Schlüssel, Platzhalter (z. B. `%user%`, `%file`) und mIRC-Formatierungsbestandteile (z. B. `$chr(3)`, `$chr(15)`, `$chr(2)`, `$+`) unverändert. Texte, die zu einer Angriffs-/Fähigkeitsdefinition gehören, werden davon getrennt über den Mechanismus für Angriffs-/Fähigkeitsdaten gelesen.
- Nach erfolgreichem `on CONNECT` ruft das System den Helfer `$system_default_check` auf. Fehlt `System.dat`, legt er sie mit Standardwerten an; fehlende Einstellungen ergänzt er ebenfalls. Danach prüft er die Konfiguration, `Translation.dat`, Pflichtmodule und erforderliche Datenbestände für Monster, Waffen, Techniken, Skills usw. Nur bei erfolgreicher Gesamtprüfung startet der Schlacht-Timer.
- Die sechs besprochenen Laufzeitwerte werden aus `System.dat` in globale Variablen eingelesen und von den Modulen verwendet. Kann `System.dat` nicht angelegt, geschrieben oder validiert werden, startet der Schlacht-Timer nicht.
- Die vier Zeitwerte `TimeBeforeFirstBattle`, `TimeToEnter`, `TimeBetweenBattles` und `TimeForIdle` müssen ganze Zahlen zwischen `1` und `3600` Sekunden einschließlich sein. Damit ist eine Stunde die Obergrenze für alle konfigurierbaren Warte- und Zugzeiten. Dezimalzahlen, nicht numerische Werte und Werte außerhalb des Bereichs gelten als ungültig und werden durch den jeweiligen Standardwert ersetzt; die Korrektur wird im mIRC-Statusfenster gemeldet und in `System.dat` gespeichert.
- `MaxIdleTurns` muss eine ganze Zahl von mindestens `1` sein; `VictoryOrbReward` muss eine ganze Zahl von mindestens `0` sein.
- Unbekannte Schlüssel werden für V0.1 ignoriert, aber nicht gelöscht; der Systemhelfer fasst sie in einer Warnung im mIRC-Statusfenster zusammen. Mehrfach vorkommende bekannte Schlüssel innerhalb von `[system]` gelten als mehrdeutig und werden auf den jeweiligen Standardwert zurückgesetzt; doppelte Vorkommen werden entfernt. Danach muss `System.dat` erneut gespeichert, eingelesen und validiert werden, bevor ein Schlacht-Timer starten darf.
- Die vollständige Charaktervorlage aus der Dokumentation darf als Grundlage verwendet werden. Version 0.1 liest und verarbeitet aber nur die Felder, die sie tatsächlich benötigt.
- Funktionen, die nicht ausdrücklich zum Umfang von Version 0.1 gehören, bleiben für spätere Versionen vorgemerkt. Der administrative Passwort-Reset ist eine solche Zukunftsfunktion.
- Änderungen am Umfang werden gemeinsam besprochen und bestätigt, bevor sie implementiert werden.

## 2. Version 0.1: Charakter und Anmeldung

- Spieler können einen neuen Charakter erstellen und sich gegenüber dem Bot authentifizieren.
- Die Spielerbefehle sind deutschsprachig. Der derzeitige Befehlsentwurf umfasst `!registrieren`, `!anmelden <Passwort>`, `!abmelden`, `!passwort <altesPasswort> <neuesPasswort>` und `!werte`. Die genaue Syntax und die zulässigen Ausführungskontexte werden vor Implementierung noch einmal verbindlich geprüft.
- Bei der Registrierung wird ein zufälliges Passwort erzeugt und dem Spieler privat mitgeteilt.
- Wenn der Bot einen Fehler bei der privaten Zustellung des Startpassworts erkennt, informiert er den Spieler mit einer deutschen Meldung, dass private Nachrichten vom Bot möglich sein müssen. Die Registrierung wird in diesem Fall abgebrochen und der neu angelegte Charakterdatensatz zurückgerollt/entfernt. Passwort, Hash und andere sensible Daten dürfen niemals ersatzweise öffentlich ausgegeben werden. IRC garantiert nicht in allen Fällen eine Zustellbestätigung; behandelt werden können nur tatsächlich erkennbare Zustellfehler.
- Passwörter dürfen nicht öffentlich im IRC-Kanal eingegeben oder ausgegeben werden; Anmeldung und Passwortänderung erfolgen privat mit dem Bot.
- Passwörter werden niemals im Klartext in Charakterdateien gespeichert. Vorgesehen ist Argon2id mit individuellem zufälligem Salt pro Charakter.
- Als Passwort-Hashverfahren ist Argon2id über mIRCs `$argon2` vorgesehen. Colosseum 0.1 setzt mIRC 7.83 oder neuer voraus; die konkreten Parameter und die Speicherung der Hash-Metadaten werden vor der Implementierung festgelegt.
- Ein Passwortwechsel ist vorgesehen.
- Vergessene Passwörter können nicht wiederhergestellt oder angezeigt werden. **Ein administrativer Passwort-Reset ist für Version 0.1 ausdrücklich zurückgestellt** und wird für eine spätere Version vorgemerkt. Wenn wir ihn ergänzen, darf auch danach nur ein neuer Hash mit individuellem Salt gespeichert werden; Ablauf und private Zustellung werden dann separat festgelegt.
- `!werte` zeigt dem Spieler seine aktuellen HP und TP sowie die vier Kernattribute Stärke, Verteidigung, Intelligenz und Schnelligkeit. Die Ausgabe erfolgt privat.
- Charakterdaten bleiben nach einem Bot-Neustart erhalten.
- Die Charaktervorlage orientiert sich an der Dokumentation; nicht benötigte Felder dürfen zunächst ungenutzt bleiben.
- Neue Charaktere beginnen mit bloßen Fäusten.
- Neue Charaktere starten mit **0 roten Kugeln**. Da es in Version 0.1 keinen Shop gibt, ist kein Startguthaben erforderlich.

## 3. Version 0.1: Schlachtzyklus und Beitritt

- Es gibt zu jedem Zeitpunkt höchstens **eine aktive Kampfinstanz**.
- Beim Start des Bots wird der automatische Schlachtzyklus aktiviert.
- Der Bot kündigt im IRC-Kanal eine bevorstehende Schlacht an.
- Nach der Ankündigung ist das Beitrittsfenster **120 Sekunden (2 Minuten)** geöffnet.
- Mit `!enter` kann sich ein Spieler für die angekündigte Schlacht anmelden.
- In Version 0.1 wird pro Schlacht höchstens **ein Spieler** aufgenommen. Weitere Beitrittsversuche werden abgewiesen.
- Nach Ablauf des Beitrittsfensters wird – sofern ein Spieler beigetreten ist – ein zufälliges normales Monster ausgewählt und der Kampf beginnt.
- Wenn niemand beigetreten ist, wird keine Kampfinstanz gestartet. Der Bot meldet dies und kündigt die nächste Schlacht in **3 Minuten** an.
- Nach dem Ende eines Kampfes verarbeitet der Bot zuerst Ergebnis und Belohnung, meldet danach, dass die nächste Schlacht in **3 Minuten** beginnt, und startet den Countdown für die nächste Ankündigung.
- Es wird kein neuer Kampf gestartet, während der aktuelle Kampf noch läuft.

## 4. Version 0.1: Monster und Kampfrunden

- Es stehen mindestens **fünf verschiedene normale Monster** für die Zufallsauswahl bereit.
- Bosse sind nicht enthalten.
- Pro Kampf wird genau ein zufälliges Monster erzeugt.
- Die Zugreihenfolge richtet sich nach dem Speed-Wert des Spielers und des Monsters.
- Bei höherem Speed ist die jeweilige Figur zuerst an der Reihe.
- Bei identischen Speed-Werten wird zufällig entschieden, wer zuerst handelt.
- Die Monster-KI führt bei jedem eigenen Zug nur einen normalen Nahkampfangriff aus.
- Der Spieler greift mit bloßen Fäusten an.
- Beide Angriffsmethoden werden unterstützt: eine IRC-Aktion wie `/me attacks Monster` und ein deutscher Befehl wie `!angriff`. Beide Methoden müssen dieselbe interne Angriffslogik verwenden.
- Angriffe sind nur zulässig, wenn der Spieler an der Reihe ist.
- Erreicht das Monster 0 HP, gewinnt der Spieler. Erreicht der Spieler 0 HP, verliert er den Kampf.

## 5. Version 0.1: Zeitlimit bei Inaktivität

- Der Spieler hat **180 Sekunden (3 Minuten)** Zeit für seinen Zug.
- Läuft das Zeitlimit ab, wird der Zug übersprungen.
- Nach zwei übersprungenen Spielerzügen wird der Spieler aus dem Kampf entfernt.
- Der Kampf endet dann sofort als Niederlage; der Spieler erhält keine Siegesbelohnung.
- Diese Regeln verhindern, dass eine Kampfinstanz durch einen inaktiven Spieler unbegrenzt blockiert wird.

## 6. Version 0.1: Schaden und Kampfwerte

- Die Schadensberechnung ist zunächst fest und enthält keinen Zufallsfaktor: Gleiche Ausgangswerte führen zu gleichem Schaden.
- Die Stärke des Angreifers bestimmt die Ausgangskraft des Angriffs.
- Die Verteidigung des Ziels reduziert den erlittenen Schaden.
- Die eigene Verteidigung des Angreifers reduziert nicht seinen ausgeteilten Schaden; sie schützt ihn vor gegnerischen Angriffen.
- Ein erfolgreicher normaler Angriff verursacht mindestens **1 Schadenspunkt**.
- Zusätzliche Modifikatoren wie Rüstung, Augments, Ignition Boosts, Skills, Techniken und Spezialfähigkeiten werden in Version 0.1 noch nicht berücksichtigt.
- Die genaue mathematische Schadensformel und ihre Zahlenwerte sind noch offen und werden vor der Implementierung gemeinsam festgelegt.

## 7. Version 0.1: Kampfende, Zurücksetzen und Belohnungen

- Nach jedem beendeten Kampf werden die Kampfwerte auf die jeweiligen Grundwerte des Charakters zurückgesetzt – unabhängig davon, ob er gewonnen, verloren oder wegen Inaktivität entfernt wurde.
- Eine feste Siegesbelohnung beträgt **10 rote Kugeln**.
- Rote Kugeln werden nur vergeben, wenn der Spieler das Monster besiegt und den Kampf überlebt.
- Bei einer Niederlage, bei 0 HP des Spielers oder bei Ausschluss wegen Inaktivität gibt es keine Belohnung.
- Bei einer leeren Beitrittsphase findet kein Kampf statt und es gibt keine Belohnung.
- Die roten Kugeln werden dauerhaft im Charakterdatensatz gespeichert.
- Schwarze Kugeln sind in Version 0.1 keine Kampfbelohnung.
- Der spätere Modus „Players must die“, bei dem Kampfwerte nach einem Kampf nicht automatisch aufgefüllt werden, gehört nicht zu Version 0.1.

## 8. Für spätere Versionen vorgemerkt

Diese Funktionen gehören zur langfristigen Vision und sind nicht Teil von Version 0.1:

- Mehrere Spieler in einer gemeinsamen Kampfinstanz
- Shop und Kaufmechaniken
- weitere Waffen, Schilde, Rüstung, Zubehör und Waffenwechsel
- Techniken, Skills, Technikpunkte und Ignition-System
- Gegenstände und Verbrauchsgegenstände wie Tränke
- Stil-System, Stil-Erfahrung und stilbasierte Belohnungen
- dynamische Belohnungsformeln und schwarze Kugeln als Belohnung
- alliierte Notizen, Odin-Marken und Login-Punkte
- NPC-/Trust-Systeme, Mechs und besondere NPC-Aktionen
- Crafting, Verbesserungen/Augments und Reparatur
- Truhen, Auktionshaus und Wetten auf KI-Kämpfe
- Eroberungen, Dragon Balls, Drachenjagd, Dungeons und weitere Spielmodi
- Bosskämpfe, erweiterte Statuszustände und besondere Gegner-KI
- der Modus „Players must die“
- umfangreiche Kampfstatistiken, Kampfserien speichern/laden und weitere Meta-Systeme
- DCC-exklusive Funktionen, sofern sie nicht für den ersten Basiskampf erforderlich sind
- `System.dat` wird bei fehlender Datei automatisch mit Standardwerten angelegt; fehlende Einträge werden ergänzt, bevor Werte geprüft und in globale Variablen eingelesen werden.

Diese Liste bedeutet nicht, dass die Funktionen aus dem Zielprodukt gestrichen werden. Sie werden später ergänzt, nachdem ihre Anforderungen geklärt wurden.

## 9. Architektur-Anforderungen

Die Skriptstruktur trennt **fachliche Module (`.mrc`)** von **wiederverwendbaren Helfer-Aliases (`.als`)**. mIRC arbeitet nicht mit Klassen im objektorientierten Sinn; wir verwenden „Modul“ als Bezeichnung für eine thematisch zuständige Datei bzw. Dateigruppe.

### Grundprinzipien

- `Colosseum.mrc` ist die einzige Datei, die Nutzer in Version 0.1 manuell laden müssen. Sie lädt die benötigten `.als`-Aliasdateien und fachlichen `.mrc`-Module in definierter Reihenfolge und stößt die Initialisierung an. In späteren Versionen kann sie zusätzliche Aufgaben übernehmen, bleibt aber frei von umfangreicher Spiellogik.
- Befehle und IRC-Ereignisse liegen in dem fachlich zuständigen `.mrc`-Modul, nicht in einer zentralen `Events.mrc`.
- `Character.mrc` behandelt Charakter-/Profilbefehle und charakterbezogene Ereignisse.
- `Battle.mrc` behandelt Schlacht- und Kampfbefehle sowie relevante `TEXT`-/`ACTION`-Ereignisse.
- Wiederverwendbare Helfer-Aliases liegen in eigenen `.als`-Dateien, gruppiert nach Aufgabe.
- Die `.mrc`-Module orchestrieren den fachlichen Ablauf und rufen Helfer aus den `.als`-Dateien auf. Hilfsberechnungen werden nicht wahllos in Ereignishandler kopiert.
- Allgemeine Spielertexte werden aus `Translation.dat` im Projekt-Hauptordner bezogen. `TranslationAliases.als` stellt Lade- und Abruffunktionen sowie die kontrollierte Verarbeitung von Platzhaltern und erlaubten Formatierungsausdrücken bereit. Textvorlagen, die unmittelbar zu einer Angriffs-/Fähigkeitsdefinition gehören, werden über den dafür vorgesehenen Datenweg gelesen und sind keine allgemeinen Translation-Schlüssel.
- Die Charakterverwaltung bleibt fachlich in `Character.mrc`; eine separate `Storage.mrc` wird für Version 0.1 nicht benötigt. Charakterbezogene Lade-/Speicher-Helfer gehören in `Characters.als`.
- Eine `Combat.mrc` oder `Monster.mrc` wird in Version 0.1 nur angelegt, falls dort tatsächlich fachliche Befehle/Ereignisse unterzubringen sind. Reine Kampf- und Monster-Helfer gehören in `.als`-Dateien.

### Vorgesehene `.mrc`-Module

- `Colosseum.mrc`: Bootstrap/Hauptdatei. Wird vom Nutzer geladen, lädt die benötigten Alias-Dateien und anschließend die Fachmodule, prüft den Startvorgang und stößt die Initialisierung an. Keine umfangreiche Spiellogik.
- `System.mrc`: systembezogene Befehle und Ereignisse. Der eigentliche wiederverwendbare Systemcode und allgemeine Aliases liegen in `SystemAliases.als`.
- `Character.mrc`: Charaktererstellung, Passwortgenerierung/-änderung, Anmeldung/Abmeldung, Beschreibung, Geschlecht, Charakterinformationen und andere Charakter-/Profilbefehle. Ruft dafür passende Helfer aus `Characters.als` sowie Übersetzungshelfer auf.
- `Battle.mrc`: Beitrittsbefehl, deutscher Angriffs-Befehl, IRC-Aktionsangriff (`on ACTION`, etwa für `/me attacks ...`) und andere Schlacht-/Kampfereignisse. Ruft die Kampfsteuerungs- und Berechnungshelfer auf.

### Vorgesehene `.als`-Dateien

- `SystemAliases.als`: allgemeine Systemhelfer und zentrale Initialisierungs-/Timer-Helfer, soweit sie nicht zu einem spezifischeren Modul gehören.
- `Characters.als`: Helfer für Charakterdateien, Charakterdaten lesen/schreiben, Charakteranlage, Passwortverarbeitung, Login-Zustand und Abfragen von Charakterwerten.
- `BattleControl.als`: Status der einzelnen Kampfinstanz, Beitrittsfenster, Beginn/Ende des Kampfes, Zugverwaltung, Zeitlimits, Inaktivitätszählung und Koordination zwischen den Kampfhelfern.
- `BattleFormula.als`: Berechnung des Schadens und weitere Kampfmathematik. Die genaue Formel wird separat festgelegt.
- `Monsters.als`: Laden/Validieren der Monsterdefinitionen, zufällige Auswahl eines gültigen normalen Monsters und Bereitstellung seiner Werte.
- `BattleRewards.als`: Prüfung der Kampfresultate, Vergabe der zehn roten Kugeln bei einem Sieg mit überlebendem Spieler sowie Anforderung der Rücksetzung der Kampfwerte.
- `TranslationAliases.als`: Laden und Nachschlagen der Textschlüssel aus `Translation.dat` im Projekt-Hauptordner, einschließlich Platzhaltern für Nicknames, Monster- und Zahlenwerte.

Dateinamen und Alias-Signaturen sind ein Entwurf für Version 0.1 und werden vor der Implementierung gemeinsam freigegeben. Ein Präfix wie `col_` soll für interne Aliasnamen verwendet werden, um Namenskonflikte zu reduzieren.

### Datenordner

- `Translation.dat` im Projekt-Hauptordner: externe, bearbeitbare allgemeine Bottexte im Abschnitt-/Schlüssel-Format; Deutsch ist die Standardsprache. Schlüssel, Platzhalter und mIRC-Formatierungssyntax bleiben bei Übersetzungen unverändert. Angriffsfähigkeits-/Angriffsdefinitionstexte werden getrennt behandelt.
- `characters/`: gespeicherte Spielercharaktere im dokumentierten `.chr`-Format, vorbehaltlich der abschließenden Formatprüfung.
- `monsters/`: mindestens fünf eigene normale Monsterdefinitionen. Die Wiki verwendet für Monster Vorlagen mit der Endung `.char`; wir prüfen vor dem Code, ob wir dieses Format unverändert übernehmen oder für Version 0.1 ein minimales kompatibles Profil definieren.

### Vorgeschlagene Abhängigkeiten

- `Character.mrc` → `Characters.als`, `TranslationAliases.als`
- `Battle.mrc` → `BattleControl.als`, `BattleFormula.als`, `Monsters.als`, `BattleRewards.als`, `TranslationAliases.als`
- `BattleControl.als` → `Monsters.als`, `BattleFormula.als`, `BattleRewards.als`, `Characters.als`, `TranslationAliases.als`
- `BattleRewards.als` → `Characters.als`, `TranslationAliases.als`
- `System.mrc` → `SystemAliases.als`, `TranslationAliases.als`
- `SystemAliases.als` koordiniert die Initialisierung und ruft den Schlachtzyklus in `BattleControl.als` an, ohne die Kampfregeln selbst zu implementieren.

Die Abhängigkeiten sind ein Vorschlag. Die Implementierung soll möglichst eine klare Aufrufrichtung haben und zyklische Abhängigkeiten vermeiden.

### Geplante Ladelogik

1. Der Nutzer lädt `Colosseum.mrc` als Einstiegsskript.
2. Die Bootstrap-Logik lädt zuerst die benötigten `.als`-Dateien (damit deren Helfer bei der Initialisierung verfügbar sind).
3. Anschließend lädt sie die Fachmodule `System.mrc`, `Character.mrc` und `Battle.mrc` als Remote-Skripte.
4. Erst wenn die Module geladen sind, wird die Initialisierung ausgelöst: Übersetzungsdatei und Daten prüfen, Laufzeitstatus vorbereiten und den Schlachtzyklus starten.

mIRC unterscheidet technisch zwischen Alias-Dateien und Remote-Skriptdateien; die offizielle Dokumentation beschreibt `/load -a` zum Laden von Alias-Dateien und `/load -rs` zum Laden von Remote-Skripten. Die konkreten Pfade und der Bootstrap-Ablauf werden bei der Implementierung gegen die Zielversion von mIRC getestet.

### Sicherheits- und Wartbarkeitsregeln

- Passwörter werden nicht öffentlich ausgegeben. Eine sichere Speicher-/Prüfmethode wird vor der Implementierung festgelegt.
- Die beiden Angriffswege müssen dieselbe interne Kampffunktion verwenden.
- Der Kampfzustand muss zentral in der Kampfinstanz verwaltet werden, damit keine zweite Instanz startet.
- Sichtbare Texte gehören in `Translation.dat`; Debug- und Fehlermeldungen an Spieler sollen nicht fest in die Kampflogik eingebaut werden.
- Die Modulgrenzen sollen spätere Systeme (Shop, weitere Waffen/Techniken, Rüstung, Augments, Ignition und Mehrspieler) ermöglichen, ohne alle bestehenden Dateien neu zu schreiben.

## 10. Noch offen vor der Implementierung

1. Die genaue Schadensformel und konkrete Schadenswerte.
2. Auswahl und Werte der mindestens fünf Startmonster aus der Dokumentation.
3. Endgültige Schreibweise, Argumente und zulässige Ausführungskontexte der deutschen Registrierungs-, Login-, Logout-, Passwort- und Wertebefehle.
4. Einzelheiten der Charaktervorlage, die für Version 0.1 tatsächlich gelesen oder verändert werden müssen.
5. Technische Zielumgebung (mIRC-Version/IRC-Verhalten) und konkretes Speicherformat der Charakterdateien.
6. Exakter Wortlaut und Kanal-/Privatnachrichten-Verteilung der deutschen Botmeldungen. Meldungen, die ein Passwort enthalten (z. B. `%newpass`), dürfen ausschließlich privat an den betreffenden Spieler gesendet werden.
7. Für den einfachen Faustangriff: genauer Speicherort bzw. Lesemechanismus für die an eine Angriffs-/Fähigkeitsdefinition gebundene Meldung, getrennt von `Translation.dat`.
8. Ein genauer Testplan für Grenzfälle, beispielsweise gleichzeitige Befehle, Bot-Neustart während eines Kampfes und Monster-/Spieler-HP bei 0.

## 11. Akzeptanzkriterien für Version 0.1

Version 0.1 ist erst spielbar, wenn:

- ein Spieler einen Charakter anlegen und sich authentifizieren kann;
- Charakterdaten und rote Kugeln Neustarts überstehen;
- der automatische Schlachtzyklus mit 3-Minuten-Pause funktioniert;
- ein Beitrittsfenster von 120 Sekunden startet und höchstens einen Spieler zulässt;
- ohne Beitritt kein Kampf erzeugt wird;
- bei Beitritt genau ein zufälliges Monster aus mindestens fünf normalen Monstern ausgewählt wird;
- Zugreihenfolge nach Speed bestimmt wird und Speed-Gleichstand zufällig entschieden wird;
- beide Angriffswege dieselbe Kampfaktion auslösen;
- Monster bei jedem Zug automatisch normal angreifen;
- der Schaden nach der festgelegten Formel berechnet wird und mindestens 1 beträgt;
- ein Spielerzug nach 180 Sekunden übersprungen wird und der Kampf nach zwei übersprungenen Zügen als Niederlage endet;
- Sieg, Niederlage und Belohnung korrekt verarbeitet werden;
- ausschließlich ein überlebender Sieger 10 rote Kugeln erhält;
- Kampfwerte nach jedem beendeten Kampf auf Grundwerte zurückgesetzt werden;
- alle sichtbaren Bottexte über `Translation.dat` bereitgestellt werden und standardmäßig auf Deutsch erscheinen;
- Befehle/Ereignisse in den jeweils zuständigen Fachmodulen behandelt werden und wiederverwendbare Helfer-Aliase in aufgabenbezogenen `.als`-Dateien liegen;
- Shop und andere zurückgestellte Systeme nicht für den Basiskampf vorausgesetzt werden.

## 12. Verhältnis zur Wiki

Das GitHub-Wiki beschreibt das vollständige Zielprodukt. Diese Spezifikation beschreibt ausschließlich den freigegebenen Umfang für Version 0.1. Vor jeder späteren Erweiterung prüfen wir die zugehörigen Wiki-Seiten, besprechen nötige Änderungen und ergänzen die Spezifikation für die betreffende Version. Unklare Regeln werden als offen markiert, anstatt stillschweigend geraten zu werden.

## Bereits entschieden

- Wenn die private Zustellung des generierten Startpassworts mit einem erkennbaren Fehler scheitert, wird der Spieler informiert und die Registrierung zurückgerollt. Es bleibt kein Charakterdatensatz ohne zugestelltes Startpasswort zurück. Da IRC nicht in allen Fällen eine Zustellbestätigung liefert, kann nur auf tatsächlich erkennbare Fehler reagiert werden.

## Registrierungs- und Befehlsorte (bestätigt)

- `!registrieren` darf sowohl im öffentlichen IRC-Kanal als auch per privater Nachricht an den Bot verwendet werden. Der Befehl enthält kein Passwort.
- Das automatisch erzeugte Startpasswort wird ausschließlich privat an den Spieler gesendet; Passwörter werden niemals ersatzweise öffentlich ausgegeben.
- `!anmelden` und `!passwort` sind nur per privater Nachricht an den Bot zulässig, damit Zugangsdaten nicht im Kanal erscheinen.
- Wenn die private Zustellung des Startpassworts mit einem erkennbaren Fehler scheitert, informiert der Bot den Spieler, bricht die Registrierung ab und entfernt den neu angelegten Charakterdatensatz. IRC garantiert nicht in allen Fällen eine Zustellbestätigung; behandelbar sind nur erkennbare Fehler.


---

## Projektstand-Nachtrag vom 22.10.2026

Dieser Nachtrag hält Entscheidungen fest, die nach der bisherigen Fassung gemeinsam konkretisiert wurden.

### Charakterkonten und Befehle

- Pro IRC-Nickname ist genau ein Charakterkonto erlaubt.
- Das Konto bleibt an den bei der Registrierung verwendeten Nickname gebunden. Ein Nickwechsel überträgt das Konto nicht auf den neuen Nick; die Charakterdatei wird durch den Nickwechsel nicht automatisch gelöscht.
- Wenn der Nutzer später zu seinem ursprünglichen IRC-Nickname zurückkehrt, kann er sich mit dem bisherigen Passwort wieder anmelden. Das Konto wird nicht übertragen; der zwischenzeitliche Nickwechsel löscht die Charakterdatei nicht.
- Bestätigte Befehle für Version 0.1: `!registrieren`, `!anmelden <Passwort>`, `!abmelden`, `!passwort <altesPasswort> <neuesPasswort>` und `!werte`.
- `!registrieren` darf im Kanal oder per Privatnachricht ausgelöst werden. Das automatisch generierte Startpasswort wird ausschließlich privat gesendet.
- `!anmelden` und `!passwort` sind nur per Privatnachricht zulässig. Persönliche Profilfunktionen wie `!werte` setzen eine erfolgreiche Anmeldung voraus.
- Eine erfolgreiche Anmeldung bleibt bestehen, bis der Spieler die Verbindung zum IRC-Netzwerk verliert oder seinen Nickname ändert. In beiden Fällen wird die Sitzung beendet. Bei Rückkehr zum ursprünglichen Nickname kann er sich erneut mit seinem Passwort anmelden.
- `!werte` zeigt HP, TP sowie Stärke, Verteidigung, Intelligenz und Schnelligkeit privat an.
- Wenn bei der Zustellung des Startpassworts ein Fehler erkennbar ist, informiert der Bot den Spieler, bricht die Registrierung ab und entfernt den neu angelegten Datensatz. IRC liefert nicht für jede Nichtzustellung ein verlässliches Fehlerereignis; die Implementierung darf keine erfolgreiche Zustellung vortäuschen.

### Passwortsicherheit

- Geplant ist Argon2id über mIRCs `$argon2` mit individuellem zufälligem Salt. Mindestversion ist mIRC 7.83; die Entwicklungsumgebung des Nutzers ist mIRC 7.84.
- Hash-Parameter und konkrete Speicherung der nötigen Metadaten müssen vor dem Implementieren getestet und festgelegt werden.
- Der administrative Passwort-Reset ist nicht Teil von Version 0.1 und bleibt für eine spätere Version vorgemerkt.

### Architektur und Arbeitsablauf

- Die Hauptdatei `Colosseum.mrc` ist der einzige Einstiegspunkt, den Nutzer manuell laden sollen.
- Fachbefehle und zugehörige IRC-Handler liegen in der jeweils zuständigen `.mrc`-Datei. Es ist keine zentrale `Events.mrc` vorgesehen.
- Wiederverwendbare Helfer liegen in aufgabenbezogenen `.als`-Dateien. Charakterbezogene Speicherung bleibt fachlich bei `Character.mrc` und den Helfern in `Characters.als`; eine separate `Storage.mrc` ist nicht vorgesehen.
- Allgemeine Spielertexte werden aus `Translation.dat` geladen. Angriffstexte, die zu einer Angriffs-/Fähigkeitsdefinition gehören, werden separat über den dafür vorgesehenen Datenmechanismus verarbeitet.
- Der Schlacht-Timer darf ausschließlich nach erfolgreichem `on CONNECT` und erfolgreichem Abschluss von `$system_default_check` starten. Eine fehlende `System.dat` wird automatisch mit Standardwerten angelegt; fehlende Einträge werden ergänzt. Danach werden die Konfiguration, `Translation.dat`, benötigte Module und erforderliche Datenbestände (unter anderem Monster, Waffen, Techniken und Skills) geprüft. Jeder Erzeugungs-, Schreib- oder Prüfungsfehler verhindert den Timerstart.
- Änderungen an Planung und Spezifikation werden während der Entwicklung in einem separaten GitHub-Branch vorbereitet und über einen Pull Request zur Prüfung vorgelegt. `main` wird nicht ohne Freigabe geändert.


## Nickname-Bindung – Entscheidung ergänzt

Bei einem zwischenzeitlichen Nickwechsel wird das Konto nicht auf den neuen Nick übertragen, und die gespeicherte Charakterdatei wird nicht automatisch gelöscht. Kehrt der Nutzer zu genau dem ursprünglichen Nickname zurück, darf er sich mit dem bisherigen Passwort wieder anmelden. Die Nickname-Schreibweise und die IRC-netzwerkspezifische Vergleichslogik müssen bei der Implementierung berücksichtigt und getestet werden.


## Start des Schlacht-Timers – Entscheidung vom 22.10.2026

- Der Schlacht-Timer startet nicht bereits beim Laden/Starten des mIRC-Skripts.
- Der Timer wird erst durch das erfolgreiche IRC-Verbindungsereignis (`on CONNECT`) gestartet, nachdem die Pflichtmodule und `Translation.dat` erfolgreich geprüft wurden.
- Die erste Schlachtankündigung erfolgt nach Ablauf der ersten drei Minuten ab diesem Verbindungsereignis.
- Die folgenden Zyklen bleiben unverändert: Ankündigung, 120 Sekunden Beitrittsfrist, danach entweder kein Kampf bei null Teilnehmern oder ein Einzelkampf; nach leerer Beitrittsphase bzw. nach Kampfende wird die nächste Ankündigung für drei Minuten später angesetzt.
- Bei einem Disconnect darf kein aktiver Schlacht-Timer weiterlaufen. Nach erfolgreicher Wiederverbindung wird der Zyklus sauber neu gestartet; doppelte parallele Timer sind zu verhindern.


## Bestätigte Entscheidung: Systemprüfung und Anmeldesitzung

- Nach erfolgreichem IRC-Verbindungsereignis (`on CONNECT`) wird `$system_default_check` aufgerufen. Nur wenn sämtliche Systemprüfungen positiv sind, darf der Schlacht-Timer gestartet werden.
- Der Helfer erzeugt `System.dat` bei Bedarf mit Standardwerten und ergänzt fehlende Einstellungen. Danach prüft er die Konfiguration, `Translation.dat`, erforderliche Module und vorhandene/lesbare/gültige Datenbestände für Monster, Waffen, Techniken, Skills und weitere Spielinhalte. Welche Datenbestände als Dateien bzw. Datenlisten organisiert werden, wird bei der Datenstrukturplanung festgelegt.
- Kann `System.dat` nicht angelegt, geschrieben, erneut gelesen oder validiert werden, oder fehlen andere Pflichtdateien/-daten, wird eine technische Meldung im mIRC-Statusfenster ausgegeben. Es startet kein Schlacht-Timer.
- Eine Anmeldung bleibt bis zur Trennung vom IRC-Netzwerk oder bis zu einer Nickname-Änderung bestehen. Der jeweilige Vorgang beendet die Sitzung. Kehrt der Spieler später zum ursprünglichen Nickname zurück, kann er sich mit dem bisherigen Passwort erneut anmelden.


## Bestätigte Entscheidung: Konfigurierbare Werte aus System.dat

Die sechs Laufzeitwerte werden bereits in Version 0.1 aus `System.dat` gelesen und in globalen mIRC-Variablen gespeichert. Die Module nutzen diese Variablen; die eigentlichen Laufzeitwerte werden nicht separat in der Kampf- oder Timerlogik fest codiert. Das Vorbild dafür ist der Endversion-Helfer, beispielsweise `TimeToEnter` → `%timeToEnter`.

Bestätigte Zuordnung:

- Erste Ankündigung nach erfolgreicher Startprüfung: `180` Sekunden; Schlüssel `TimeBeforeFirstBattle`, globale Variable `%timeBeforeFirstBattle`.
- Beitrittsfrist: `120` Sekunden; vorhandener Schlüssel `TimeToEnter`, globale Variable `%timeToEnter`.
- Abstand bis zur nächsten Ankündigung nach leerer Beitrittsphase oder Kampfende: bisher vereinbarte `180` Sekunden; vorhandener Schlüssel `TimeBetweenBattles`, globale Variable `%timeBetweenBattles`.
- Spieler-Zeitlimit: `180` Sekunden; vorhandener Schlüssel `TimeForIdle`, globale Variable `%timeForIdle`.
- Höchstzahl übersprungener Spielerzüge: `2`; vorhandener Schlüssel `MaxIdleTurns`, globale Variable `%maxIdleTurns`.
- Siegesbelohnung: `10` rote Kugeln; Schlüssel `VictoryOrbReward`, globale Variable `%victoryOrbReward`.

Die sechs Schlüssel, globalen Variablen und Standardwerte der Tabelle sind bestätigt. Im vorgelegten Endversion-Helfer hat `TimeBetweenBattles` den Standardwert `120`; für V0.1 ist ausdrücklich `180` Sekunden festgelegt.

Fehlt `System.dat`, legt der V0.1-Helfer die Datei selbst mit Standardwerten an. Fehlen einzelne Einstellungen, werden sie ebenfalls ergänzt, bevor die Konfiguration validiert und in globale Variablen übernommen wird. Das Vorgehen soll sich am versionsabhängigen Standardwertprinzip des Endversion-Helfers (`SystemDatVersion`) orientieren, aber für V0.1 vereinfacht werden. Bereits vorhandene ungültige Werte werden durch die vorgesehenen Standardwerte ersetzt. Die Korrektur wird im mIRC-Statusfenster gemeldet und in `System.dat` gespeichert; danach setzt der Helfer die Startprüfung fort. Wenn Erzeugung, Schreiben, erneutes Einlesen oder Validierung scheitern, startet kein Schlacht-Timer.


## Bestätigte Entscheidung: System.dat bei Neuinstallation automatisch anlegen

V0.1 setzt nicht voraus, dass `System.dat` schon mitgeliefert oder vor dem Start von Hand erstellt wurde. Der Helfer legt die Datei bei Bedarf mit den vorgesehenen Standardwerten an und ergänzt fehlende Einträge in vorhandenen Dateien. Die Werte werden anschließend validiert und in globalen mIRC-Variablen bereitgestellt. Nur nach erfolgreicher Gesamtprüfung beginnt der Schlachtzyklus.

Diese Ausnahme gilt nur für `System.dat`. Fehlende `Translation.dat`, Pflichtmodule oder erforderliche Spieldaten verhindern weiterhin den Start. Bereits vorhandene ungültige Konfigurationswerte werden durch ihre vorgesehenen Standardwerte ersetzt. Der Helfer meldet die Korrektur im mIRC-Statusfenster, schreibt die aktualisierte `System.dat` und setzt die Prüfung fort. Wenn das Schreiben oder die anschließende Validierung scheitert, startet kein Schlacht-Timer.


## Bestätigte Entscheidung: ungültige System.dat-Einstellungen auf Standardwerte zurücksetzen

Enthält eine vorhandene `System.dat` eine Einstellung mit einem ungültigen Wert, ersetzt der V0.1-Helfer den Wert durch den für diese Einstellung vorgesehenen Standardwert. Er meldet die Korrektur im mIRC-Statusfenster und speichert die Änderung in `System.dat`. Anschließend wird die korrigierte Konfiguration erneut geprüft und die restliche Startprüfung fortgesetzt. Der Schlacht-Timer startet nur dann, wenn das Speichern, die erneute Validierung und sämtliche anderen Pflichtprüfungen erfolgreich sind.


## Bestätigte Abgrenzung: System-Defaults für den Prototyp statt Endversion

Der gezeigte `system_defaults_check` ist für die Endversion vorgesehen und wird in V0.1 nicht unverändert übernommen. Er dient als Vorlage für das Grundprinzip: fehlende Einstellungen ergänzen, ungültige Werte anhand definierter Standardwerte korrigieren, Änderungen protokollieren, Konfiguration validieren und gültige Werte in globale Variablen übernehmen.

Der V0.1-Helfer bleibt bewusst klein. Er enthält nur solche Einstellungen, Pfade und Startprüfungen, die für den Prototyp erforderlich sind. Funktionen, Module, Ordner und Alt-Daten-Bereinigungen für Endversionsysteme wie Bosse, Mechs, NPCs, Shops, Auktionen, Dungeons und zusätzliche Ereignisse werden nicht in den V0.1-Umfang gezogen.

Vor der Implementierung wird eine vollständige Standardwertliste für V0.1 beschlossen. Sie enthält für jeden benötigten Eintrag Schlüssel, Standardwert, Datentyp/Wertebereich, globale Variable und Verwendungszweck. Bestehende Schlüssel werden nach Möglichkeit beibehalten, sofern ihre Bedeutung zum Prototyp passt; Unterschiede zwischen der Endversion-Vorgabe und den bestätigten V0.1-Regeln sind explizit festzulegen.


## Bestätigte System.dat-Standardwerte für V0.1

Die folgenden sechs Einstellungen werden getrennt voneinander in `System.dat` geführt, beim Start validiert und in die globalen Variablen übernommen:

| Schlüssel | Globale Variable | Standardwert |
|---|---|---:|
| `TimeBeforeFirstBattle` | `%timeBeforeFirstBattle` | `180` Sekunden |
| `TimeToEnter` | `%timeToEnter` | `120` Sekunden |
| `TimeBetweenBattles` | `%timeBetweenBattles` | `180` Sekunden |
| `TimeForIdle` | `%timeForIdle` | `180` Sekunden |
| `MaxIdleTurns` | `%maxIdleTurns` | `2` |
| `VictoryOrbReward` | `%victoryOrbReward` | `10` rote Kugeln |

`TimeBeforeFirstBattle` und `TimeBetweenBattles` bleiben zwei getrennte Einstellungen. Der erste Wert steuert die Wartezeit nach erfolgreicher Verbindung und abgeschlossener Startprüfung; der zweite steuert die Pause nach einer leeren Beitrittsphase oder nach einem Kampf. Beide haben für V0.1 zunächst den Standardwert `180` Sekunden. Die Endversions-Vorgabe `TimeBetweenBattles=120` wird in V0.1 nicht übernommen.

## Bestätigte Entscheidung: Ordnerstruktur des Endprodukts bereits in V0.1 verwenden

Die vollständige Grundstruktur orientiert sich von Beginn an am geplanten Endprodukt. Die `.mrc`- und `.als`-Dateien, `System.dat` und `Translation.dat` liegen im Projekt-Hauptordner. Spielercharaktere werden in `characters/` und Monsterdefinitionen in `monsters/` gespeichert. Weitere Verzeichnisse aus dem Endversion-Helfer (`bosses/`, `zapped/`, `npcs/`, `summons/` und `help-files/`) gehören bereits zum vorgesehenen Hauptordner-Layout, auch wenn V0.1 ihre jeweiligen Funktionen nicht implementiert oder die Verzeichnisse noch nicht befüllt.

Es wird kein zusätzlicher `scripts/`- oder `data/`-Ordner eingeführt, der beim Ausbau zur Endversion wieder verlassen werden müsste. Damit sollen Datei- und Ordnerumzüge bei späteren Versionsschritten vermieden werden. Nur die Funktionen, Module und Daten, die V0.1 benötigt, werden tatsächlich umgesetzt.