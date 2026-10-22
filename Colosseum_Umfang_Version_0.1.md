# Colosseum – Umfang und Entscheidungen für Version 0.1

**Status:** Arbeitsgrundlage mit bestätigten Regeln und offenen Punkten.  
**Leitlinie:** Das GitHub-Wiki beschreibt das angestrebte Endprodukt. Diese Datei grenzt davon den ersten spielbaren Entwicklungsstand ab.

## 1. Verbindliche Leitlinien

- Es gibt noch keinen vorhandenen Quellcode. Die Implementierung wird neu entworfen und selbst geschrieben.
- Der Bot und alle für Spieler sichtbaren Nachrichten, Hilfetexte und Fehlermeldungen sollen standardmäßig auf Deutsch sein.
- Alle sichtbaren Texte werden von Anfang an aus `Translation.dat` geladen; sie werden nicht direkt in die Spiellogik eingebaut. So können Nutzer später Übersetzungen ändern oder weitere Sprachen hinzufügen, ohne den Quellcode zu bearbeiten.
- Die vollständige Charaktervorlage aus der Dokumentation darf als Grundlage verwendet werden. Version 0.1 liest und verarbeitet aber nur die Felder, die sie tatsächlich benötigt.
- Funktionen, die nicht ausdrücklich zum Umfang von Version 0.1 gehören, bleiben für spätere Versionen vorgemerkt.
- Änderungen am Umfang werden gemeinsam besprochen und bestätigt, bevor sie implementiert werden.

## 2. Version 0.1: Charakter und Anmeldung

- Spieler können einen neuen Charakter erstellen und sich gegenüber dem Bot authentifizieren.
- Bei der Registrierung wird ein zufälliges Passwort erzeugt und dem Spieler privat mitgeteilt.
- Ein Passwortwechsel ist vorgesehen.
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
- Konfiguration über `System.dat`; die Beitrittszeit, Zugzeit, Pause bis zur nächsten Schlacht und andere Werte sind in Version 0.1 fest vorgegeben

Diese Liste bedeutet nicht, dass die Funktionen aus dem Zielprodukt gestrichen werden. Sie werden später ergänzt, nachdem ihre Anforderungen geklärt wurden.

## 9. Architektur-Anforderungen

Auch wenn die Funktionen zunächst einfach bleiben, soll das Grundgerüst modular sein. Nicht alles soll in einer einzigen `.mrc`-Datei wachsen.

### Trennung von Ereignissen und Helfer-Aliasen

- IRC-Ereignis-Handler wie `on TEXT` und `on ACTION` gehören in eine zentrale Ereignis-/Steuerungsschicht, beispielsweise `Events.mrc`.
- Diese Handler sollen eingehende Nachrichten erkennen und an passende Befehls- oder Spielfunktionen weiterleiten.
- Module wie `Combat.mrc`, `Monster.mrc` und `Rewards.mrc` enthalten Helfer-Aliase und Fachlogik, aber keine eigenen `on TEXT`- oder `on ACTION`-Handler. Andere Module können ihre Helfer aufrufen.
- `Battle.mrc` verwaltet den Schlachtablauf über klar definierte Helfer-Aliase; die eigentlichen IRC-Ereignisse bleiben zentral.
- Die Hauptdatei `Colosseum.mrc` dient als Einstiegspunkt und lädt beziehungsweise initialisiert die benötigten Module.

### Vorgeschlagene Modulaufteilung

- `Colosseum.mrc`: Hauptdatei und Initialisierung.
- `Events.mrc`: zentrale mIRC-Ereignis-Handler und Weiterleitung eingehender Ereignisse.
- `Commands.mrc`: Erkennung und Weiterleitung unterstützter Bot-Befehle.
- `System.mrc`: Systemstart, globaler Zustand, Timer und zunächst fest vorgegebene Einstellungen.
- `Character.mrc`: Registrierungs-, Anmelde- und Charakter-Helfer.
- `Storage.mrc`: Laden und Speichern von Charakterdaten.
- `Battle.mrc`: Schlachtzyklus, Beitrittsfenster und Verwaltung der einzelnen Kampfinstanz.
- `Combat.mrc`: reine Kampffunktionen wie Zugreihenfolge, Angriff und Schadensberechnung; keine IRC-Ereignis-Handler.
- `Monster.mrc`: Monsterdaten laden, zufällig auswählen und Monsteraktionen ausführen; keine IRC-Ereignis-Handler.
- `Rewards.mrc`: Berechnung und Vergabe der Kampfbelohnung.
- `Translation.mrc`: Helfer zum Laden und Abrufen übersetzbarer Texte.
- `data/Translation.dat`: vom Quellcode getrennte, bearbeitbare Nachrichten und Textvorlagen.
- `data/monsters/`: Datendateien für die normalen Monster.
- `data/characters/` oder ein separat festgelegter Spieler-Datenordner: dauerhafte Charakterdateien.

Die Namen und genaue Anzahl der Dateien sind ein Architekturvorschlag und können bei der konkreten Implementierung noch angepasst werden. Die verbindlichen Grundsätze sind die Trennung der Zuständigkeiten, zentrale Ereignisverarbeitung und externe Übersetzungstexte.

### Weitere Leitlinien

- Die vollständige Charaktervorlage aus der Dokumentation dient als Grundlage; Version 0.1 liest und verarbeitet nur die dafür nötigen Felder.
- Die Angriffsverarbeitung für IRC-Aktion und `!angriff` muss dieselbe interne Kampfaktion aufrufen.
- Kampfablauf, Schadensberechnung, Kampfende und Belohnungsvergabe werden in klar abgegrenzte Funktionen aufgeteilt.
- Spielerdaten bleiben dauerhaft gespeichert.
- Passwörter dürfen nicht öffentlich ausgegeben oder in allgemein sichtbaren Nachrichten gespeichert werden; eine geeignete sichere Speicherung legen wir vor der Implementierung fest.
- Monster werden aus einer Datenliste ausgewählt, damit neue Monster später ergänzt werden können, ohne die Kampflogik umzuschreiben.
- Sichtbare Meldungen werden über Schlüssel aus `Translation.dat` geladen. Die Datei soll von Anfang an so gestaltet werden, dass spätere weitere Sprachen möglich sind.
- Spätere Erweiterungen sollen nicht durch unnötige Annahmen in der ersten Implementierung erschwert werden.

## 10. Noch offen vor der Implementierung

1. Die genaue Schadensformel und konkrete Schadenswerte.
2. Auswahl und Werte der mindestens fünf Startmonster aus der Dokumentation.
3. Exakte Schreibweise und Aliase aller Registrierungs-, Login-, Passwort- und Statusbefehle.
4. Einzelheiten der Charaktervorlage, die für Version 0.1 tatsächlich gelesen oder verändert werden müssen.
5. Technische Zielumgebung (mIRC-Version/IRC-Verhalten) und konkretes Speicherformat der Charakterdateien.
6. Exakter Wortlaut und Kanal-/Privatnachrichten-Verteilung der deutschen Botmeldungen.
7. Ein genauer Testplan für Grenzfälle, beispielsweise gleichzeitige Befehle, Bot-Neustart während eines Kampfes und Monster-/Spieler-HP bei 0.

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
- IRC-Ereignis-Handler zentral in der Ereignis-/Steuerungsschicht liegen und die Fachmodule nur aufrufbare Helfer-Aliase enthalten;
- Shop und andere zurückgestellte Systeme nicht für den Basiskampf vorausgesetzt werden.

## 12. Verhältnis zur Wiki

Das GitHub-Wiki beschreibt das vollständige Zielprodukt. Diese Spezifikation beschreibt ausschließlich den freigegebenen Umfang für Version 0.1. Vor jeder späteren Erweiterung prüfen wir die zugehörigen Wiki-Seiten, besprechen nötige Änderungen und ergänzen die Spezifikation für die betreffende Version. Unklare Regeln werden als offen markiert, anstatt stillschweigend geraten zu werden.
