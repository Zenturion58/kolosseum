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

Auch wenn die Funktionen zunächst einfach bleiben, soll das Grundgerüst modular sein. Nicht alles soll in einer einzigen `.mrc`-Datei wachsen. Dabei unterscheiden wir bewusst zwischen **fachlichen `.mrc`-Modulen**, die für ihre jeweiligen Befehle/Ereignisse verantwortlich sind, und **`.als`-Dateien**, in denen die wiederverwendbaren Helfer-Aliase nach Aufgaben gebündelt werden.

### Befehle und Ereignisse gehören in das zuständige Fachmodul

- Charakterbezogene Befehle und Ereignisse werden in `Character.mrc` behandelt, zum Beispiel Charakter erstellen, Passwort ändern, anmelden/abmelden, Beschreibung festlegen, Geschlecht ändern und Charakterinformationen oder Statistiken abrufen.
- Kampf- und Schlachtbefehle werden in `Battle.mrc` behandelt, zum Beispiel `!enter` und die für den Kampf vorgesehenen Spieleraktionen.
- Weitere Fachbereiche erhalten ebenfalls ihre eigenen `.mrc`-Module, wenn sie später hinzukommen.
- Befehle werden **nicht pauschal in einer zentralen `Events.mrc` abgefangen**. Die Zuständigkeit liegt beim jeweiligen Fachmodul. Eine zentrale Ereignisdatei wird für Version 0.1 daher nicht als Voraussetzung eingeplant.
- IRC-Ereignis-Handler wie `on TEXT` oder `on ACTION` dürfen in dem `.mrc`-Modul stehen, das für den betreffenden Befehls- oder Ereignisbereich zuständig ist. Die Fachmodule sollen aber nicht mit allen internen Helfern und Berechnungen überladen werden.

### Wiederverwendbare Helfer liegen in `.als`-Dateien

Die Helfer-Aliase werden nicht wahllos in die jeweilige `.mrc`-Datei geschrieben. Stattdessen werden sie nach Zuständigkeit in eigenständigen `.als`-Dateien gebündelt. Beispiele für die geplante Aufteilung:

- `SystemAliases.als`: gemeinsame Systemhelfer und allgemeine Funktionen.
- `Characters.als`: Helfer für Charakterverwaltung und Charakterdaten, soweit diese als wiederverwendbare Aliase benötigt werden.
- `BattleControl.als`: Helfer für den Ablauf und die Steuerung von Kämpfen.
- `BattleFormula.als`: Berechnung des Schadens und weitere kampfbezogene Formeln.
- Weitere Alias-Dateien können folgen, wenn ein Bereich genügend eigene Helfer benötigt.

Ein `.mrc`-Fachmodul ruft bei Bedarf einen passenden Helfer aus einer `.als`-Datei auf. Beispielsweise kann ein Charakterbefehl in `Character.mrc` einen Charakterstatus-Helfer aus einer Alias-Datei aufrufen; ein Kampfhandler in `Battle.mrc` kann die Zugreihenfolge oder Schadensberechnung über `BattleControl.als` beziehungsweise `BattleFormula.als` anfordern.

### Vorgeschlagene Modulaufteilung

- `Colosseum.mrc`: Hauptdatei, lädt beziehungsweise initialisiert die benötigten Skript- und Alias-Dateien und startet Colosseum.
- `System.mrc`: systembezogene Befehle/Ereignisse und Ablaufsteuerung, etwa Start des Bots und Schlacht-Timer. Wiederverwendbare Systemhelfer liegen in `SystemAliases.als`.
- `Character.mrc`: fachliche Zuständigkeit für Charakterbefehle und -ereignisse: Registrierung, Passwortgenerierung und -änderung, Anmeldung/Abmeldung, Charakterbeschreibung, Geschlecht und Charakterinformationen. Das Modul veranlasst auch das Laden und Speichern der Charakterdateien und ruft dafür gegebenenfalls Helfer aus Alias-Dateien auf.
- `Battle.mrc`: fachliche Zuständigkeit für Schlacht- und Kampfbefehle/Ereignisse, darunter Beitritt, Beginn und Interaktion mit dem laufenden Kampf.
- `Combat.mrc`: optionales Fachmodul für die Koordination kampfbezogener Abläufe, falls es bei der Implementierung sinnvoll ist; enthält keine Sammlung unabhängiger Hilfsaliase, die stattdessen in `.als`-Dateien gehören.
- `Monster.mrc`: zuständig für monsterbezogene Befehle/Ereignisse, falls benötigt, und die fachliche Nutzung der Monsterdaten. Wiederverwendbare Monsterhelfer können in einer separaten Alias-Datei liegen.
- `Rewards.mrc`: zuständig für den fachlichen Abschluss/Belohnungsablauf, soweit ein eigenes Modul in Version 0.1 tatsächlich sinnvoll ist; Hilfsaliase dafür werden getrennt gehalten.
- `Translation.mrc` oder ein passendes zuständiges Modul: lädt/verarbeitet Übersetzungstexte, falls hierfür Ereignisse oder Modulsteuerung nötig sind. Die Helfer zum Abrufen formatierter Texte können separat in einer Alias-Datei liegen.
- `data/Translation.dat`: vom Quellcode getrennte, bearbeitbare Bot-Nachrichten und Textvorlagen.
- `data/monsters/`: Datendateien für die normalen Monster.
- `data/characters/` oder ein separat festgelegter Spieler-Datenordner: dauerhafte Charakterdateien. Die Charakterverwaltung bleibt fachlich in `Character.mrc`; allgemeine wiederverwendbare Dateihelfer können bei Bedarf in einer Alias-Datei liegen.

Die Namen und genaue Anzahl der Dateien sind ein Architekturentwurf. Besonders `Combat.mrc`, `Monster.mrc` und `Rewards.mrc` bleiben davon abhängig, ob sie als eigenständige Fachmodule tatsächlich gebraucht werden. Die verbindlichen Grundsätze sind: Befehle und Ereignisse gehören zum jeweiligen Fachbereich; wiederverwendbare Helfer-Aliase werden getrennt in passenden `.als`-Dateien organisiert; sichtbare Texte liegen in `Translation.dat`.

### Zuständigkeit des Charaktermoduls

- `Character.mrc` besitzt die fachliche Verantwortung für sämtliche charakterbezogenen Vorgänge einschließlich Passwortgenerierung, Anmeldung, Abmeldung, Charakterbefehlen, Beschreibung, Geschlecht sowie dem Laden und Speichern des Charakterdatensatzes.
- Charakterbezogene Befehle/Ereignisse werden im Charaktermodul behandelt, nicht zentral in einer `Events.mrc`.
- `Character.mrc` kann Helfer aus anderen Alias-Dateien aufrufen, etwa für Statusabfragen, gemeinsame Systemfunktionen oder das Übersetzen einer Nachricht. Die Charakterbefehle und die Steuerung des charakterbezogenen Ablaufs bleiben jedoch in `Character.mrc`.
- Eine separate `Storage.mrc` ist für Version 0.1 nicht vorgesehen. Die fachliche Verantwortung für Charakterdateien bleibt in `Character.mrc`; wiederverwendbare, allgemeine Datei- oder Datenhelfer können separat in `.als`-Dateien organisiert werden.

### Weitere Leitlinien

- Die vollständige Charaktervorlage aus der Dokumentation dient als Grundlage; Version 0.1 liest und verarbeitet nur die dafür nötigen Felder.
- Die IRC-Aktionsmethode und der deutsche Angriffs-Befehl müssen dieselbe interne Kampfaktion auslösen; die wiederverwendbare Kampflogik liegt in passenden Alias-Dateien.
- Kampfablauf, Schadensberechnung, Kampfende und Belohnungsvergabe werden in klar abgegrenzte Fachbereiche und Hilfsaliase aufgeteilt.
- Spielerdaten bleiben dauerhaft gespeichert.
- Passwörter dürfen nicht öffentlich ausgegeben oder in allgemein sichtbaren Nachrichten gespeichert werden; eine geeignete sichere Speicherung legen wir vor der Implementierung fest.
- Monster werden aus einer Datenliste ausgewählt, damit neue Monster später ergänzt werden können, ohne die Kampflogik umzuschreiben.
- Sichtbare Meldungen werden über Schlüssel aus `Translation.dat` geladen. Die Datei soll von Anfang an so gestaltet sein, dass spätere weitere Sprachen möglich sind.
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
- Befehle/Ereignisse in den jeweils zuständigen Fachmodulen behandelt werden und wiederverwendbare Helfer-Aliase in aufgabenbezogenen `.als`-Dateien liegen;
- Shop und andere zurückgestellte Systeme nicht für den Basiskampf vorausgesetzt werden.

## 12. Verhältnis zur Wiki

Das GitHub-Wiki beschreibt das vollständige Zielprodukt. Diese Spezifikation beschreibt ausschließlich den freigegebenen Umfang für Version 0.1. Vor jeder späteren Erweiterung prüfen wir die zugehörigen Wiki-Seiten, besprechen nötige Änderungen und ergänzen die Spezifikation für die betreffende Version. Unklare Regeln werden als offen markiert, anstatt stillschweigend geraten zu werden.
