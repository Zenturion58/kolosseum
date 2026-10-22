# Colosseum – Umfang und Entscheidungen für Version 0.1

**Status:** Arbeitsgrundlage mit bestätigten Regeln und offenen Punkten.  
**Leitlinie:** Das GitHub-Wiki beschreibt das angestrebte Endprodukt. Diese Datei grenzt davon den ersten spielbaren Entwicklungsstand ab.

## 1. Verbindliche Leitlinien

- Es gibt noch keinen vorhandenen Quellcode. Die Implementierung wird neu entworfen und selbst geschrieben.
- Der Bot und alle für Spieler sichtbaren Nachrichten, Hilfetexte und Fehlermeldungen sollen standardmäßig auf Deutsch sein.
- Allgemeine Botmeldungen, System-/Profilmeldungen, Schlachtankündigungen und Belohnungstexte werden von Anfang an aus `Translation.dat` geladen; diese Texte werden nicht direkt in die Spiellogik eingebaut. Die Datei nutzt Abschnitte und Schlüssel in der Form `Schlüssel=Textvorlage`. Übersetzer ändern nur den Text rechts vom `=` und lassen Schlüssel, Platzhalter (z. B. `%user%`, `%file`) und mIRC-Formatierungsbestandteile (z. B. `$chr(3)`, `$chr(15)`, `$chr(2)`, `$+`) unverändert. Texte, die zu einer Angriffs-/Fähigkeitsdefinition gehören, werden davon getrennt über den Mechanismus für Angriffs-/Fähigkeitsdaten gelesen.
- Nach erfolgreichem `on CONNECT` ruft das System den Helfer `$system_default_check` auf. Er prüft sämtliche für den Betrieb erforderlichen Module und Datenbestände, darunter `System.dat`, `Translation.dat` sowie die Daten/Definitionen für Monster, Waffen, Techniken, Skills und weitere registrierte Spielinhalte. Fehlt etwas oder ist eine Prüfung negativ, wird die Initialisierung abgebrochen und der Schlacht-Timer startet nicht.
- `System.dat` ist bereits beim Start von Version 0.1 eine Pflichtdatei. `$system_default_check` liest und validiert die Einstellungen und stellt die zulässigen Werte in globalen Variablen für andere Module bereit. Welche dieser Einstellungen Version 0.1 tatsächlich dynamisch verwendet und welche Zeitwerte vorerst fest bleiben, muss vor Implementierung abschließend abgegrenzt werden.
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
- `System.dat` wird bereits beim Start geprüft und eingelesen; der genaue Umfang der in Version 0.1 tatsächlich konfigurierbaren Werte wird vor Implementierung festgelegt.

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
- Allgemeine Spielertexte werden aus `data/Translation.dat` bezogen. `TranslationAliases.als` stellt Lade- und Abruffunktionen sowie die kontrollierte Verarbeitung von Platzhaltern und erlaubten Formatierungsausdrücken bereit. Textvorlagen, die unmittelbar zu einer Angriffs-/Fähigkeitsdefinition gehören, werden über den dafür vorgesehenen Datenweg gelesen und sind keine allgemeinen Translation-Schlüssel.
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
- `TranslationAliases.als`: Laden und Nachschlagen der Textschlüssel aus `data/Translation.dat`, einschließlich Platzhaltern für Nicknames, Monster- und Zahlenwerte.

Dateinamen und Alias-Signaturen sind ein Entwurf für Version 0.1 und werden vor der Implementierung gemeinsam freigegeben. Ein Präfix wie `col_` soll für interne Aliasnamen verwendet werden, um Namenskonflikte zu reduzieren.

### Datenordner

- `data/Translation.dat`: externe, bearbeitbare allgemeine Bottexte im Abschnitt-/Schlüssel-Format; Deutsch ist die Standardsprache. Schlüssel, Platzhalter und mIRC-Formatierungssyntax bleiben bei Übersetzungen unverändert. Angriffsfähigkeits-/Angriffsdefinitionstexte werden getrennt behandelt.
- `data/characters/`: gespeicherte Spielercharaktere im dokumentierten `.chr`-Format, vorbehaltlich der abschließenden Formatprüfung.
- `data/monsters/`: mindestens fünf eigene normale Monsterdefinitionen. Die Wiki verwendet für Monster Vorlagen mit der Endung `.char`; wir prüfen vor dem Code, ob wir dieses Format unverändert übernehmen oder für Version 0.1 ein minimales kompatibles Profil definieren.

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
- Allgemeine Spielertexte werden aus `data/Translation.dat` geladen. Angriffstexte, die zu einer Angriffs-/Fähigkeitsdefinition gehören, werden separat über den dafür vorgesehenen Datenmechanismus verarbeitet.
- Der Schlacht-Timer darf ausschließlich nach erfolgreichem `on CONNECT` und erfolgreichem Abschluss von `$system_default_check` starten. Pflichtprüfungen umfassen `System.dat`, `Translation.dat`, benötigte Module und die erforderlichen Datenbestände (unter anderem Monster, Waffen, Techniken und Skills). Jeder Fehler verhindert den Timerstart.
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
- Die Prüfung umfasst `System.dat`, `Translation.dat`, erforderliche Module und die vorhandenen/lesbaren/validen Datenbestände für Monster, Waffen, Techniken, Skills und weitere registrierte Spielinhalte. Welche dieser Bestände als konkrete Dateien bzw. Datenlisten organisiert werden, wird bei der Datenstrukturplanung festgelegt.
- Bei fehlender Datei, ungültigen Einstellungen, fehlenden Pflichtdaten oder einem negativen Prüfergebnis wird eine technische Meldung im mIRC-Statusfenster ausgegeben. Es startet kein Schlacht-Timer.
- Eine Anmeldung bleibt bis zur Trennung vom IRC-Netzwerk oder bis zu einer Nickname-Änderung bestehen. Der jeweilige Vorgang beendet die Sitzung. Kehrt der Spieler später zum ursprünglichen Nickname zurück, kann er sich mit dem bisherigen Passwort erneut anmelden.


## Vorschlag zur Prüfung: Konfiguration über System.dat

Noch nicht endgültig freigegeben ist der Vorschlag, folgende bereits besprochene Zahlenwerte in Version 0.1 über `System.dat` einzustellen:

- Erste Schlachtankündigung nach erfolgreicher Startprüfung: `180` Sekunden.
- Beitrittsfenster: `120` Sekunden.
- Nächste Schlachtankündigung nach leerer Beitrittsphase bzw. Kampfende: `180` Sekunden.
- Spieler-Zeitlimit pro Zug: `180` Sekunden.
- Höchstzahl übersprungener Spielerzüge vor Niederlage: `2`.
- Belohnung für einen Sieg mit überlebendem Spieler: `10` rote Kugeln.

Wenn dieser Vorschlag angenommen wird, sind alle zugehörigen Einträge Pflichtwerte von `System.dat`. Fehlt ein Wert oder ist er nicht eindeutig, nicht numerisch oder außerhalb des erlaubten Bereichs, schlägt die Startprüfung fehl und es startet kein Schlacht-Timer. Es soll keine stillschweigende Rückfalleinstellung geben. Die konkreten Schlüsselnamen, Wertebereiche, das Dateiformat und der Umgang mit zusätzlichen Schlüsseln bleiben offen, bis wir die Konfiguration gemeinsam bestätigen.
