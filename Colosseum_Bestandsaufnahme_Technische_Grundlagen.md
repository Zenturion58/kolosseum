# Colosseum – erste Bestandsaufnahme

**Stand:** 22. Oktober 2026  
**Grundlage:** öffentliches GitHub-Wiki unter <https://github.com/Zenturion58/kolosseum/wiki>  
**Zweck dieses Dokuments:** Die vorhandenen Themen und ihre Abhängigkeiten sichtbar machen, bevor Regeln festgelegt oder Quellcode geschrieben werden.

> **Wichtige Abgrenzung:** Dies ist eine erste, strukturierte Bestandsaufnahme anhand der Wiki-Übersicht und einer Auswahl zentraler Seiten. Es ist noch keine vollständige inhaltliche Prüfung aller 145 Wiki-Seiten. Die Dokumentation beschreibt Funktionen und teilweise Dateivorlagen des dokumentierten Systems; ein ausführbarer Quellcode für unser Projekt liegt damit nicht vor. Wir werden die neue Implementierung selbst entwickeln und die Regeln gemeinsam mit dem Projektverantwortlichen klären.

## 1. Zusammenfassung

Das Wiki weist 145 Seiten aus und gruppiert sie in 14 Themenbereiche. Der Umfang geht deutlich über einen einfachen Kampfbot hinaus: Colosseum umfasst Charakterverwaltung, automatische rundenbasierte Schlachten, Gegenstände und Ausrüstung, mehrere Fortschritts- und Währungssysteme, Shops und NPCs, zusätzliche Spielmodi sowie konfigurierbare Inhalte und Vorlagen.

Das Kernprinzip laut Übersicht:

1. Spieler legen einen Charakter an und melden sich beim Bot an.
2. Der Bot kündigt eine offene Schlacht an.
3. Spieler treten der Schlacht bei; anschließend werden Gegner erzeugt und der Kampf beginnt.
4. Monster und Bosse handeln automatisch. Spieler wählen bei ihrem Zug Aktionen wie Waffenangriff, Technik, Fähigkeit oder Gegenstand.
5. Bei zu langem Leerlauf wird ein Spielerzug übersprungen, damit die Schlacht nicht endlos blockiert.
6. Belohnungen und Währungen dienen der weiteren Charakterentwicklung.

Quelle: [Overview](https://github.com/Zenturion58/kolosseum/wiki/overview), [Battle Info & Commands](https://github.com/Zenturion58/kolosseum/wiki/battle-commands).

## 2. Die 14 Themenbereiche des Wikis

### 2.1 Einstieg und Charakterverwaltung

Dokumentierte Seiten: *Overview*, *Starting Out and Character Commands*, *Daily Login*.

Enthaltene Themen:
- Charakterregistrierung und Erzeugung eines Passworts.
- Authentifizierung beim Bot und Passwortänderung.
- Charakterstatus und Informationsbefehle.
- Grundwerte wie HP, TP, Stärke, Verteidigung, Intelligenz und Geschwindigkeit.
- Besitz und Ausrüstung von Waffen, Schilden, Rüstung, Zubehör, Techniken, Fähigkeiten, Liedern und Gegenständen.
- Charakterbeschreibung, Geschlechtsangabe, Ausrüstungsanzeige und Kampfstatistiken.
- Login-Punkte als wiederkehrende Fortschrittsbelohnung.

Im Gespräch bereits festgehalten: Neue Figuren beginnen mit bloßen Fäusten. Die erste Charaktererstellung soll abhängig vom vorhandenen Spielniveau Start-Rotkugeln gewähren; das Kennwort wird zufällig erstellt und kann vom Spieler geändert werden. Ein Administrator soll ein vergessenes Passwort zurücksetzen können.

Quellen: [Starting Out](https://github.com/Zenturion58/kolosseum/wiki/starting-out), [Detailed Look at new_chr.char](https://github.com/Zenturion58/kolosseum/wiki/new-char).

### 2.2 Shop und besondere NPCs

Dokumentierte Seiten: *The Shop*, *Potion Effects Shop*, *Special NPCs*, *Trust NPCs*.

Enthaltene Themen:
- Shop-Kategorien für Gegenstände, Techniken, Fähigkeiten, Werte, Waffen, Stile, Ignitions, Orbs, Portale, sonstige Inhalte, Mechs und Mech-Gegenstände.
- Kauf- und Listenbefehle.
- Ein Shop-Level, das sich bei den meisten Käufen erhöht und Preise beeinflusst; in der Referenz sind neue Waffen und Gegenstände davon ausgenommen.
- Spezial-NPCs, die bei erfüllten Bedingungen erscheinen und weitere Shop-Inhalte oder Minispiele freischalten.
- Trank-/Potion-Effekte, die beim nächsten Kampf oder dessen Abschluss wirken und nach Vorgaben aus Beutematerialien erworben werden.
- Trust-NPCs, die als Verbündete in Kämpfen beschworen werden können; ihre Gegenstände werden in der Referenz mit Login-Punkten gekauft.

Wichtige Abhängigkeit: Shop-Funktionen brauchen Charakterinventar, Währungsbuchungen, Preisregeln, Inhaltstabellen und NPC-Freischaltbedingungen.

Quellen: [The Shop](https://github.com/Zenturion58/kolosseum/wiki/the-shop), [Potion Effects Shop](https://github.com/Zenturion58/kolosseum/wiki/potion-effects-shop), [Special NPCs](https://github.com/Zenturion58/kolosseum/wiki/special-npcs), [Trust NPCs](https://github.com/Zenturion58/kolosseum/wiki/trust-npcs).

### 2.3 Kampfsystem

Dokumentierte Seiten: *Monsters, Bosses, and NPCs*, *Battle Info & Commands*, *Status Effects*, *Elemental Strength and Weakness*, *Renkei System*, *Battlefields*, *Dungeons*, *Dragon Hunting*, *Torment Battles*, *Cosmic Battles*, *Besieged Battles*, *Limit Breaks*, *Monster Fair*.

Enthaltene Themen:
- Zeitgesteuerte Schlachtankündigung und Beitritt der Spieler.
- Automatische Gegnerzüge und zeitlich begrenzte Spielerzüge.
- Monster als reguläre Gegner sowie stärkere Bosse mit besonderen Belohnungen.
- Waffenangriffe, Techniken, Fähigkeiten und Gegenstände als Kampfbefehle.
- Statuszustände, Elementarresistenzen und Schwächen.
- Renkei-/Skillchain-System zum Verbinden aufeinanderfolgender Techniken.
- Schlachtfelder mit Wetter- und Tageszeitinformationen.
- Spezielle Kampftypen und Abenteuer: Dungeons, Drachenjagd, Torment-, Cosmic- und Belagerungsschlachten sowie Limit Breaks und Monster-Fair.

Dies ist der zentrale Laufzeitbereich des Spiels. Andere Systeme (Ausrüstung, Statuswerte, Fähigkeiten, Verbrauchsgegenstände, Trusts, Belohnungen und Statistiken) müssen mit ihm zusammenarbeiten.

Quellen: [Battle Info & Commands](https://github.com/Zenturion58/kolosseum/wiki/battle-commands), [Monsters, Bosses, and NPCs](https://github.com/Zenturion58/kolosseum/wiki/monsters-bosses-npcs), [Status Effects](https://github.com/Zenturion58/kolosseum/wiki/status-effects), [Elemental Strengths](https://github.com/Zenturion58/kolosseum/wiki/elemental-strengths), [Renkei System](https://github.com/Zenturion58/kolosseum/wiki/renkei-system), [Dungeons](https://github.com/Zenturion58/kolosseum/wiki/dungeons).

### 2.4 Inventar und Ausrüstung

Dokumentierte Seiten: *Weapons*, *Armor*, *Shields*, *Accessories*, *Mythic Weapons*.

Enthaltene Themen:
- Waffen und ihre gebundenen Techniken.
- Rüstung, Schilde und Zubehör.
- Ausrüstungswechsel und Anzeige der getragenen Gegenstände.
- Besondere bzw. mythische Waffen.
- Werte und Eigenschaften von Ausrüstung, die Treffer, Schaden, Element, Kosten und Spezialverhalten beeinflussen können.

Die Inventarlogik muss zwischen **Besitz**, **ausgerüstetem Zustand** und **im Kampf wirksamen Eigenschaften** unterscheiden. Konsumierbare Items benötigen zusätzlich Mengenverwaltung und Verbrauchsregeln.

Quellen: [Weapons](https://github.com/Zenturion58/kolosseum/wiki/weapons), [Armor](https://github.com/Zenturion58/kolosseum/wiki/armor), [Shields](https://github.com/Zenturion58/kolosseum/wiki/shields), [Accessories](https://github.com/Zenturion58/kolosseum/wiki/accessories).

### 2.5 Charakterverbesserungen

Dokumentierte Seiten: *Skills*, *Augments*, *Ignitions*, *Mechs*.

Enthaltene Themen:
- Passive und aktive Fähigkeiten, Widerstände sowie Schadenserhöhungen gegen bestimmte Monsterfamilien.
- Augments als zusätzliche Modifikatoren.
- Ignitions bzw. ein aufladbarer Sonderzustand, der mit einer Anzeige/Gauge verbunden ist.
- Mechs als gesonderte kauf- oder nutzbare Ausrüstung/Spielinhalte.

Diese Systeme hängen an Charakterwerten, Shop, Ressourcenverbrauch und der Auflösung von Kampfaktionen. Die genauen Regeln, Kosten und Wechselwirkungen sind anhand der einzelnen Seiten noch zu verifizieren.

Quellen: [Skills](https://github.com/Zenturion58/kolosseum/wiki/skills), [Augments](https://github.com/Zenturion58/kolosseum/wiki/augments), [Ignitions](https://github.com/Zenturion58/kolosseum/wiki/ignitions), [Mechs](https://github.com/Zenturion58/kolosseum/wiki/mechs).

### 2.6 Crafting und Schatztruhen

Dokumentierte Seiten: *Crafting Info*, *Treasure Chests* sowie mehrere Seiten für Truhenarten und Rezepte.

Enthaltene Themen:
- Rezepte und Materialien.
- Fähigkeiten zur Herstellung bestimmter Gegenstände.
- Beutematerialien, die auch von anderen Systemen wie Potion-Effekten verwendet werden können.
- Schatztruhen mit unterschiedlichen Arten von Belohnungen.

Abhängigkeiten: Item-Datenbank, Rezeptdefinitionen, Inventar, Materialverbrauch, Zufall/Erfolgschancen und Belohnungslogik.

Quellen: [Crafting](https://github.com/Zenturion58/kolosseum/wiki/crafting), [Treasure Chests](https://github.com/Zenturion58/kolosseum/wiki/treasure-chests), [Recipes](https://github.com/Zenturion58/kolosseum/wiki/recipes).

### 2.7 Stile und Stilpunkte

Dokumentierte Seiten: *Style Points*, *Player Styles*.

Enthaltene Themen:
- Verschiedene Kampf-Stile, die unterschiedliche Aktionen oder Vorteile ermöglichen.
- Stil-Erfahrung bzw. Stilpunkte.
- Eine Verbindung zwischen stilvollem Kämpfen und Belohnungen, insbesondere Rotkugeln, laut Referenzbeschreibung.

Abhängigkeiten: Kampfaktionsereignisse, Belohnungsberechnung, Charakterfortschritt und Shop.

Quellen: [Style Points](https://github.com/Zenturion58/kolosseum/wiki/style-points), [Player Styles](https://github.com/Zenturion58/kolosseum/wiki/player-styles).

### 2.8 Auktionshaus und Hauptquartier-Garten

Dokumentierte Seiten: *Auction House*, *Allied Forces HQ Garden* sowie *Garden.dat*.

Enthaltene Themen:
- Automatisiertes Auktionshaus mit regelmäßig wechselndem Angebot und Geboten in Alliierten-Notizen.
- Ein Garten-Minispiel im Hauptquartier mit eigenem Zustand bzw. eigener Datenhaltung.

Abhängigkeiten: Zeitsteuerung, Inventar/Beute, Währungen, Gebotsverwaltung und dauerhafte Speicherung.

Quellen: [Auction House](https://github.com/Zenturion58/kolosseum/wiki/auction-house), [Garden.dat](https://github.com/Zenturion58/kolosseum/wiki/garden-dat).

### 2.9 Listen und Nachschlagetabellen

Dokumentierte Listen: Gegenstände, Rezepte, Waffen/Techniken, Portal-Kampf-Gegenstände, Trust-NPCs und Achievements.

Diese Seiten sind nicht alle eigenständige Laufzeitmodule. Sie dienen zum Auffinden und Nachschlagen der registrierten Inhalte. Eine wichtige technische Frage für unsere Neuentwicklung ist, ob der Bot solche Listen aus zentralen Datenquellen automatisch erzeugt, statt sie doppelt von Hand zu pflegen.

Quellen: [Colosseum Topics](https://github.com/Zenturion58/kolosseum/wiki/Colosseum-Topics), [`.lst` Files](https://github.com/Zenturion58/kolosseum/wiki/lst-files).

### 2.10 Weitere Spielmodi und Informationen

Dokumentierte Seiten: *AI vs AI Battles and Betting*, *Misc Info*.

Zusätzlich werden in anderen Bereichen besondere Modi wie Portal-, Cosmic-, Torment-, Belagerungs- und Dungeon-Kämpfe beschrieben. Ein KI-gegen-KI-Modus mit Wettsystem ist ebenfalls vorgesehen. In der Besprechung wurde außerdem Conquest mit Eroberungsperioden und -punkten sowie eine spätere Dragon-Ball-Suche erwähnt.

Diese Modi sollten nicht alle gleichzeitig als erste Entwicklungsstufe beginnen. Wir müssen jeweils klären, ob sie zum gewünschten Umfang der ersten Version gehören oder erst später dazukommen.

Quellen: [AI Battles](https://github.com/Zenturion58/kolosseum/wiki/ai-battles), [Misc Info](https://github.com/Zenturion58/kolosseum/wiki/misc-info), [Dragon Hunts](https://github.com/Zenturion58/kolosseum/wiki/dragon-hunts).

### 2.11 Administrationsfunktionen

Dokumentierte Seite: *Bot Admin Commands*.

Der Administratorbereich umfasst laut Dokumentation Befehle, die normalen Spielern nicht zur Verfügung stehen, z. B. Schlachten manuell zu starten oder zu steuern. Für unsere Umsetzung braucht es klare Rollen und Berechtigungen sowie Schutz davor, dass Spielbefehle mit administrativen Befehlen verwechselt werden.

Quelle: [Bot Admin Commands](https://github.com/Zenturion58/kolosseum/wiki/bot-admin-cmnds).

### 2.12 Erweiterungs- und Datenvorlagen

Dokumentierte Vorlagen: Waffen, Techniken, Monster, Bosse, NPCs und Beschwörungen; ergänzend Flags für Waffen und Techniken sowie Anleitungen zum Erstellen neuer Inhalte.

Beispiele für in der Dokumentation beschriebene Felder sind bei Waffen unter anderem Kosten, Typ, Element, Trefferzahl, Grundstärke, Upgrade, Energieverbrauch, Schildreflexion und Mindestlevel. Technik-Vorlagen können z. B. Typ, Kosten/TP, Element, Trefferzahl, Stärke, Statuswirkungen, Renkei-Verknüpfung und Ansagetexte enthalten. Boss-Vorlagen enthalten u. a. Grundwerte, Kampfwerte, Rage-Zustand, Beschreibungen und Beute.

Diese Vorlagen zeigen, dass das Spiel auf erweiterbare Daten ausgelegt ist. Wir sollten eine verständliche und validierbare Form für neue Inhalte vorsehen, aber nicht automatisch dieselbe Dateistruktur übernehmen, bevor wir die Anforderungen geprüft haben.

Quellen: [Weapon Flags](https://github.com/Zenturion58/kolosseum/wiki/weapon-flags), [Tech Flags](https://github.com/Zenturion58/kolosseum/wiki/tech-flags), [Monster Template](https://github.com/Zenturion58/kolosseum/wiki/mon-template), [Boss Template](https://github.com/Zenturion58/kolosseum/wiki/boss-template), [Colosseum Topics](https://github.com/Zenturion58/kolosseum/wiki/Colosseum-Topics).

### 2.13 Betriebsmodi

Dokumentierte Seiten: *DCC Mode* und *Twitch Mode*.

Das Wiki beschreibt diese als alternative Betriebsarten zum normalen IRC-Kanalbetrieb. Bei der Bestandsaufnahme ist zu unterscheiden, ob sie lediglich dokumentierte Ideen, teilweise ausgearbeitete Spezifikationen oder verbindliche Anforderungen für unser Projekt darstellen. Sie müssen nicht zwingend Teil der ersten Version sein.

Quellen: [DCC Mode](https://github.com/Zenturion58/kolosseum/wiki/DCC-mode), [Twitch Mode](https://github.com/Zenturion58/kolosseum/wiki/Twitch-Mode).

### 2.14 Technische Informationen und Dateiformate

Dokumentierte Seiten: `System.dat`, `Battlestats.dat`, `Shopnpcs.dat`, `Garden.dat`, `Battlefields.dat`, `.lst`-Dateien sowie eine detaillierte Beschreibung von `new_chr.char`.

Die Referenz beschreibt überwiegend editierbare Textdateien bzw. INI-ähnliche Dateien. Beispiele:
- `System.dat`: Botname, Kanal, Besitzer, Verbindungseinstellungen und weitere Konfiguration.
- `Battlestats.dat`: Gesamtstatistiken, Sieg-/Niederlagenserien sowie Informationen zu besonderen Modi.
- `Shopnpcs.dat`: Informationen über Shop-NPCs.
- `Garden.dat`: Zustand des Garten-Minispiels.
- `Battlefields.dat`: Schlachtfelder, Wetter und Tageszeit.
- `.lst`-Dateien: Namenslisten, die u. a. für die Anzeige von Inventar und Inhaltsübersichten genutzt werden.
- `new_chr.char`: Vorlage mit Grund- und Kampfwerten sowie weiteren Charakterinformationen.

Für unsere Neuimplementierung müssen wir entscheiden, welche Informationen als Konfiguration, welche als Spieldaten und welche als dauerhaft veränderlicher Spielstand gespeichert werden. Diese Trennung ist wichtig für Sicherungen, Updates, Bearbeitbarkeit und Datenintegrität.

Quellen: [System.dat](https://github.com/Zenturion58/kolosseum/wiki/system-dat), [Battlestats.dat](https://github.com/Zenturion58/kolosseum/wiki/battle-stats-dat), [Shopnpcs.dat](https://github.com/Zenturion58/kolosseum/wiki/shop-npcs-dat), [Garden.dat](https://github.com/Zenturion58/kolosseum/wiki/garden-dat), [Battlefields.dat](https://github.com/Zenturion58/kolosseum/wiki/battlefields-dat), [`.lst` Files](https://github.com/Zenturion58/kolosseum/wiki/lst-files), [new_chr.char](https://github.com/Zenturion58/kolosseum/wiki/new-char).

## 3. Währungen und Fortschritt – vorläufige Übersicht

Die bisherigen Informationen sprechen von mehreren getrennten Belohnungs- und Fortschrittssystemen:

| Ressource / Wert | Beschriebener Zweck | Status dieser Bestandsaufnahme |
|---|---|---|
| Rote Kugeln | Viele Shop-Käufe, Gegenstände, Techniken und sonstige Verbesserungen | Aus Übersicht und Besprechung bekannt; genaue Preisformeln noch zu prüfen |
| Schwarze Kugeln | Besonders für Waffen; werden im Zusammenhang mit Boss-Siegen vergeben | Aus Übersicht und Besprechung bekannt |
| Alliierte Notizen | Bestimmte Shop-Angebote, Mechs und Auktionsgebote; Portal-Kämpfe werden als häufige Quelle genannt | Wiki und Besprechung geben erste Hinweise |
| Login-Punkte | Trust-NPC-Gegenstände | In der Referenz beschrieben |
| Technikpunkte (TP) | Ausführen von Techniken | Aus Besprechung bekannt |
| Ignition Gauge (IG) | Fortschritts-/Ladeanzeige für Ignition-Funktionen | Aus Besprechung bekannt; genaue Aktivierung noch zu prüfen |
| Stilpunkte/-XP | Fortschritt im ausgerüsteten Stil | Im Wiki als eigener Bereich vorhanden |
| Eroberungspunkte, Odin-Marken, Dragon Balls | Zusätzliche Langzeit-/Sondersysteme, teilweise in der Besprechung genannt | Regeln noch gemeinsam zu prüfen |

Die Tabelle ist keine vollständige ökonomische Spezifikation. Vor der Implementierung müssen alle Belohnungsquellen, Kosten, Grenzen und Sonderfälle zusammengetragen werden.

## 4. Erkannte Abhängigkeiten

Ein erster Abhängigkeitsentwurf sieht so aus:

1. **IRC-Eingang und Befehlsparser** → erkennt Nachricht, Befehl, Argumente und Kontext (Kanal/Privatnachricht/DCC).
2. **Identität und Charakterverwaltung** → Anmeldung, Passwortprüfung, Charakterdaten und Berechtigungen.
3. **Speicherung** → lädt und sichert Charaktere, Konfiguration, Inhalte und Spielzustand.
4. **Inhaltskataloge** → Waffen, Techniken, Items, Monster, Bosse, NPCs, Rezepte und Stile.
5. **Kampf-Engine** → Schlachtzustand, Zugfolge, Spieleraktionen, Gegner-KI, Zeitlimits, Status und Schadensberechnung.
6. **Inventar und Ausrüstung** → Besitz, Anlegen, Verbrauch, Ressourcen und Ausrüstungsboni.
7. **Belohnung und Fortschritt** → Kugeln, Materialien, Statistiken, Stil-XP, Erfolge und Freischaltungen.
8. **Shop und NPC-Systeme** → Einkäufe, Preisregeln, Freischaltbedingungen und spezielle Interaktionen.
9. **Zusatzmodi** → Dungeons, Drachenjagd, Auktionshaus, Garten, KI-Wetten und weitere Kampftypen.
10. **Lokalisierung und Meldungen** → sämtliche sichtbaren Bot-Texte auf Deutsch, unabhängig von der internen Spiellogik.
11. **Administration und Diagnose** → sichere Adminbefehle, Protokollierung, Fehlerbehandlung und Testmöglichkeiten.

Das ist ein Vorschlag zur Planung, noch keine endgültige Architektur.

## 5. Empfohlene Reihenfolge der Umsetzung

### Stufe A – Grundlagen
- Anforderungen und Befehlsliste verifizieren.
- Programmierumgebung und Zielversion von mIRC festlegen.
- IRC-Verbindung, Konfiguration, Logging und deutschen Meldungskatalog vorbereiten.
- Sichere Charakterregistrierung und Anmeldung.
- Laden und Speichern von Charakterdaten.

### Stufe B – spielbarer Kern
- Charakterwerte, Fäuste als Startangriff, Inventar und Ausrüstung.
- Schlachtankündigung, Beitritt, Zugverwaltung und AFK-Zeitlimit.
- Einfache Monster-KI und Kampfaktionen.
- HP/TP, Status, Schaden und Kampfabschluss.
- Belohnungen und Speicherung der Ergebnisse.

### Stufe C – Progression
- Shop und Währungen.
- Waffen und Techniken mit Datenvorlagen.
- Items, Rüstung, Schilde, Zubehör und Fähigkeiten.
- Bosse, Drops, Shop-Level und Preisregeln.

### Stufe D – Erweiterungen
- Trust-NPCs, Spezial-NPCs, Ignitions, Augments, Stile und Mechs.
- Crafting, Schatztruhen, Potion-Effekte und Auktionshaus.
- Dungeons, Drachenjagd, Eroberung, Garten, KI-Wetten und weitere Sondermodi.
- Optional DCC- und Twitch-Betrieb, sofern gewünscht.

### Stufe E – Härtung und Veröffentlichung
- Balancing, Grenzfälle und Tests mit mehreren Spielern.
- Backup- und Update-Konzept für Spielstände und nutzerbearbeitbare Daten.
- Dokumentation und Installationsanleitung auf Deutsch.
- Lizenz- und Herkunftsprüfung der Inhalte, bevor Bestandteile fremder Dokumentation, Figuren, Namen oder andere geschützte Inhalte öffentlich übernommen werden.

Diese Reihenfolge ist eine Empfehlung. Umfang und Prioritäten müssen gemeinsam beschlossen werden.

## 6. Offene Fragen, bevor wir Code entwerfen

1. **Übernahme oder Neuinterpretation:** Welche dokumentierten Funktionen sollen genau übernommen werden, welche dürfen vereinfacht oder geändert werden?
2. **Erste Version:** Was muss in Version 0.1 unbedingt spielbar sein, und welche Sondermodi dürfen später folgen?
3. **Zielumgebung:** Welche mIRC-Version soll unterstützt werden? Läuft der Bot auf einem Windows-Rechner dauerhaft, und soll er nur auf einem IRC-Kanal oder auch in DCC funktionieren?
4. **Befehlsstil:** Sollen die vorhandenen englischen Befehle beibehalten werden (z. B. `!shop`, `!enter`) oder möchtest du deutsche Befehle? Unabhängig davon werden sichtbare Antworten auf Deutsch gestaltet.
5. **Dateien:** Sollen Besitzer Spieldaten weiterhin manuell mit einem Texteditor bearbeiten können? Welche Dateien müssen vor Updates geschützt bleiben?
6. **Charakter-Sicherheit:** Wie sollen Passwort-Hashing bzw. Schutz, Reset durch Admins und Mehrfachanmeldungen umgesetzt werden?
7. **Spielbalance:** Welche Kernformeln sind verbindlich und welche werden für das neue Projekt neu entworfen?
8. **Öffentliche Veröffentlichung:** Unter welcher Lizenz soll unser selbst geschriebener Code veröffentlicht werden, und welche Rechte bestehen an den übernommenen Dokumentationsinhalten und Spiel-Referenzen?

## 7. Was diese Bestandsaufnahme noch nicht leistet

- Nicht alle 145 Wiki-Seiten wurden in dieser ersten Runde vollständig Zeile für Zeile geprüft.
- Die exakten Schadens-, Heilungs-, Status-, Kosten- und Dropformeln sind noch nicht vollständig erfasst.
- Seltene Sonderregeln und Wechselwirkungen zwischen Waffen, Techniken, Skills, Augments und Zuständen müssen bei einer gezielten Prüfung geklärt werden.
- Es wurde kein Quellcode geschrieben oder getestet.
- Die Regeln in diesem Dokument sind keine endgültigen Designentscheidungen; der Projektverantwortliche entscheidet gemeinsam mit der Entwicklung über das tatsächliche Verhalten von Colosseum.

## Quellenübersicht

- [Wiki-Startseite](https://github.com/Zenturion58/kolosseum/wiki)
- [Colosseum Topics / Themenübersicht](https://github.com/Zenturion58/kolosseum/wiki/Colosseum-Topics)
- [Overview](https://github.com/Zenturion58/kolosseum/wiki/overview)
- [Starting Out and Character Commands](https://github.com/Zenturion58/kolosseum/wiki/starting-out)
- [The Shop](https://github.com/Zenturion58/kolosseum/wiki/the-shop)
- [Battle Info & Commands](https://github.com/Zenturion58/kolosseum/wiki/battle-commands)
- [Monsters, Bosses, and NPCs](https://github.com/Zenturion58/kolosseum/wiki/monsters-bosses-npcs)
- [System.dat](https://github.com/Zenturion58/kolosseum/wiki/system-dat)
- [Battlestats.dat](https://github.com/Zenturion58/kolosseum/wiki/battle-stats-dat)
- [`.lst` Files](https://github.com/Zenturion58/kolosseum/wiki/lst-files)

---

## 7. Vertiefung: technische Grundlagen anhand ausgewählter Wiki-Seiten

Diese Vertiefung prüft vier zentrale Themen näher. Sie unterscheidet zwischen dokumentiertem Verhalten des Referenzsystems und Entscheidungen, die wir für die neue deutsche Colosseum-Implementierung erst treffen müssen.

### 7.1 Charakterdatei und Spielerdaten

Die Seite [`new char`](https://github.com/Zenturion58/kolosseum/wiki/new-char) beschreibt eine Charaktervorlage im INI-ähnlichen Klartextformat. Die Abschnitte umfassen unter anderem:

- `[BaseStats]`: Ausgangs- bzw. Ruhewerte für HP, TP und die vier Kernattribute.
- `[Battle]`: veränderliche Kampfwerte und Kampfstatus. Laut Referenz werden diese nach dem Kampf aus den Basiswerten zurückgesetzt.
- `[Stuff]`: Währungen, Shop-Level sowie diverse Zähler und Fortschrittsstatistiken.
- `[Info]`: Geschlechtsangaben, Passwort und Zeitstempel/Metadaten.
- `[Status]`: Status-Effekte und teilweise deren Timer.
- `[Weapons]`, `[Skills]`, `[Techniques]`, `[Item_Amount]` und `[Styles]`: Waffenbesitz und ausgerüstete Waffe, erlernte Fähigkeiten und Techniken, Item-Anzahlen sowie Stil-Level und Erfahrung.

Die Beispielvorlage der Referenz nennt außerdem als Startausstattung unter anderem Fäuste auf Stufe 1, eine Technik auf Stufe 1, einen Trank sowie drei grundlegende Stile. **Das sind dokumentierte Werte des Referenzsystems, keine bereits beschlossenen Startregeln für unser Colosseum.** Wir entscheiden gemeinsam, welche davon übernommen werden.

**Technische Erkenntnis:** Charakterdaten sind nicht nur aktuelle HP und ein Inventar. Es gibt mindestens vier fachlich unterschiedliche Datenarten:

1. dauerhafte Charakterentwicklung (Kernattribute, Waffen-Level, erlernte Techniken, Währungen),
2. aktueller Ausrüstungs- und Besitzstand,
3. vorübergehender Kampfzustand (Kampfwerte, Status, Timer),
4. Statistiken und Historie.

Für unsere Implementierung sollten diese Unterscheidungen erhalten bleiben, auch wenn wir nicht exakt dieselbe Dateistruktur übernehmen.

**Offene Entscheidung:** Bleiben Charakterdaten als einzelne, von Hand editierbare Textdateien organisiert, oder nutzen wir ein anderes Speicherformat? Die Anforderungen an Bearbeitbarkeit, Datensicherheit, Backup und Update-Kompatibilität müssen wir zusammen abwägen.

### 7.2 Konfiguration des Bot-Betriebs

Die Seite [`system dat`](https://github.com/Zenturion58/kolosseum/wiki/system-dat) beschreibt globale Einstellungen: Bot- und Kanalinformationen, Besitzer, Betriebsmodus, Ausgabeformatierung, Aktivierung des automatischen Kampfsystems bzw. der Monster-KI, Belohnungsbasiswerte, Startwährung, Zeitabstände und Obergrenzen.

Besonders relevant für das Grundgerüst sind dokumentierte Zeitsteuerungen:

- Zeit zwischen Kämpfen,
- Zeitfenster für den Beitritt zu einer Schlacht,
- maximale Zeit für einen Spielerzug,
- Anzahl übersprungener Leerlaufzüge, bevor jemand aus dem Kampf entfernt wird,
- Zeitfenster für den Dungeon-Beitritt,
- Obergrenzen für HP, TP und Ignition Gauge.

In der Referenz stehen für einzelne Werte Standardbeispiele wie 180 Sekunden für einen Spielerzug, zwei Leerlaufzüge und 120 Sekunden für den Schlachtbeitritt. Diese Werte dienen hier nur als Referenz und sind noch nicht für unsere Version beschlossen.

**Technische Erkenntnis:** Konfigurierbare Regeln sollten von der eigentlichen Kampflogik getrennt werden. So kann ein Serverbetreiber zum Beispiel Wartezeiten und Ausgabeoptionen anpassen, ohne die Schadensberechnung ändern zu müssen.

**Offene Entscheidungen:** Welche Einstellungen sollen Administratoren bearbeiten dürfen? Welche Grenzwerte schützen vor unspielbaren Konfigurationen? Welche Betriebsmodi (IRC, DCC) gehören zur ersten Version? Twitch-Unterstützung wird vorerst nicht als Voraussetzung behandelt.

### 7.3 Kampfrunde und Aktionen

Die Seite [`battle commands`](https://github.com/Zenturion58/kolosseum/wiki/battle-commands) beschreibt folgenden Referenzablauf:

1. Der Bot kündigt eine kommende Schlacht an.
2. Spieler treten während eines begrenzten Zeitfensters bei.
3. Wenn niemand teilnimmt, endet der Vorgang ohne Kampf.
4. Der Bot bestimmt einen Kampftyp und die Kampfumgebung, darunter Schlachtfeld, Wetter, Mondphase und Tageszeit.
5. Gegner werden erzeugt und die Reihenfolge der Züge wird bekannt gegeben.
6. Gegner handeln automatisch über eine KI; Spieler führen eine gültige Aktion aus.
7. Wenn die Kampfbedingungen erfüllt sind, wird das Ergebnis festgestellt und die Belohnung berechnet.

Die Referenz nennt als Aktionsgruppen unter anderem Waffenangriffe, Techniken, Waffen-Boosts bzw. Ignitions, Rückkehr aus einem Ignition-Zustand, Lieder, Item-Einsatz, Verspotten und bestimmte Fertigkeitsbefehle. Waffen und Techniken können beispielsweise Trefferzahl, Elemente, TP-Kosten und Statuswirkungen beeinflussen.

Die Referenz verknüpft außerdem Monsterstärke und verursachten Schaden teilweise mit der aktuellen Siegesserie und dem Levelunterschied. Das ist ein wichtiger Zusammenhang fürs Balancing, aber noch keine verbindliche Formel für unsere Umsetzung.

**Technische Erkenntnis:** Der Kampf sollte als eigener Zustandsautomat modelliert werden, etwa mit Zuständen wie `Ankündigung`, `Beitrittsfenster`, `Kampfvorbereitung`, `Spielerzug`, `Gegnerzug`, `Kampfende`. Das ist ein Architekturvorschlag und kein aus dem Wiki übernommener Code.

**Offene Entscheidungen:** Reihenfolge und Gleichstände bei Initiative, Gültigkeitsprüfung der Befehle, genaue Timeout-Behandlung, Tod/KO, Abbruchbedingungen, Schadens- und Heilungsformeln, Statusdauer, Belohnungsformeln und Verhalten bei Bot-Neustart während eines Kampfes.

### 7.4 Datenvorlagen für Monster und Bosse

Die Seiten [`mon template`](https://github.com/Zenturion58/kolosseum/wiki/mon-template) und [`boss template`](https://github.com/Zenturion58/kolosseum/wiki/boss-template) zeigen umfangreiche Textvorlagen. Neben Basis- und Kampfwerten enthalten sie Abschnitte für Beschreibungen, Belohnungen, Statuszustände, Waffen, Skills, Techniken, Augments, Ausrüstung, Stile und elementare bzw. waffentypbezogene Modifikatoren. Boss-Vorlagen enthalten zusätzlich unter anderem Rage-bezogene Werte. Optional aktivierbare Felder steuern besondere Verhaltensweisen.

Die dokumentierte Monster-KI hat unter anderem Typen wie Verteidiger, Berserker, zahlungsabhängiger NPC, Beschwörer/Portal und Heiler. Damit sind Gegner nicht bloß Statistiken, sondern können unterschiedliche Entscheidungslogik besitzen.

**Technische Erkenntnis:** Inhalt und Engine sollten getrennt sein: Die Engine interpretiert allgemeine Felder und Flags; individuelle Monster- oder Boss-Dateien liefern Daten und erlauben kontrollierte Abweichungen.

**Offene Entscheidung:** Wie strikt müssen Vorlagen geprüft werden, wenn Felder fehlen oder fehlerhafte Werte enthalten? Wir sollten einen Datenvalidator und verständliche deutsche Fehlerhinweise vorsehen, damit eine fehlerhafte selbst erstellte Waffe nicht den gesamten Bot lahmlegt.

### 7.5 Globale Statistiken sind von Charakterstatistiken zu unterscheiden

Die Seite [`battle stats dat`](https://github.com/Zenturion58/kolosseum/wiki/battle-stats-dat) beschreibt globale Zustände, darunter die Anzahl der Kämpfe, Siege/Niederlagen/Unentschieden, aktuelle Serien, Eroberungspunkte, Dragon-Ball-Fortschritt, KI-Kämpfe und ein aktuelles Kopfgeld.

Das ist von den individuellen Charakterstatistiken zu trennen. Für die Umsetzung werden wir daher mindestens unterscheiden müssen zwischen:

- Daten eines einzelnen Charakters,
- globalem Bot-/Weltzustand,
- aktuellem vorübergehendem Kampfzustand,
- Daten eines optionalen Subsystems (z. B. Auktionshaus oder Garten).

Diese Trennung ist wichtig, damit ein Reset einzelner Statistiken nicht versehentlich Inventar, Charakterfortschritt oder andere Spielsysteme zurücksetzt.

### 7.6 Sicherheit und Datenschutz als Prüfpunkte

Die Beispiel-Charaktervorlage zeigt ein Passwortfeld und eine IP-bezogene Metadaten-Spalte. Das dokumentiert, wie das Referenzsystem Informationen ablegt; es ist keine Empfehlung, dies ungeprüft zu übernehmen.

Vor einer Implementierung sollten wir festlegen:

- wie Charaktere einem IRC-Nick zugeordnet und authentifiziert werden,
- wie Kennwörter geschützt gespeichert oder alternativ sicher gehandhabt werden,
- wie Passwort-Resets durch Administratoren abgesichert sind,
- welche Nutzungsmetadaten wirklich nötig sind und wie lange sie aufbewahrt werden,
- wie häufig und in welchem Format Backups erstellt werden.

Wir werden keine Klartext-Passwortspeicherung oder unnötige personenbezogene Protokollierung einfach aus der Vorlage übernehmen, ohne diese Punkte zuvor zu bewerten.

## 8. Vorläufige technische Reihenfolge

Aus den Abhängigkeiten ergibt sich dieser Vorschlag für spätere Entwicklungsphasen:

1. **Lauffähiges mIRC-Grundgerüst:** Verbindung, Konfiguration, deutsche Meldungen, zentrale Fehlerbehandlung.
2. **Befehlsverarbeitung und Identität:** Parser, private Nachrichten, Charaktererstellung und Anmeldung.
3. **Speicherung und Inhaltsladen:** Charakterdaten, globale Daten, Konfiguration und Vorlagen; inklusive Prüfung fehlerhafter Daten.
4. **Minimaler Kampf:** Join-Fenster, festgelegte Zugreihenfolge, Fäuste, ein Testgegner, HP/KO, Timeout und Kampfergebnis.
5. **Grundlegende Belohnungen:** rote und schwarze Kugeln nach gemeinsam vereinbarten Regeln, einfache Status- und Kampfstatistiken.
6. **Inventar und Shop:** Item-Anzahlen, Käufe, Währungen, Waffenbesitz und Ausrüsten.
7. **Techniken, Fähigkeiten und mehr Gegnerverhalten:** TP, Effekte, Elementarregeln, Gegner-KI und Bosse.
8. **Weitere Systeme:** Stile, NPCs/Trusts, Crafting, Truhen, Auktionshaus, Conquest, Dragon Balls und Spezialmodi.
9. **Robustheit und Veröffentlichung:** Backup/Restore, Update-Regeln, Dokumentation, Tests, Lizenzprüfung und deutsche Hilfetexte.

Die Reihenfolge kann sich ändern, wenn die gemeinsame Regelklärung etwas anderes nahelegt. Sie soll vor allem vermeiden, dass Shop, Inventar und Sondermodi auf ungetesteten Kampf- oder Speichergrundlagen entstehen.

## 9. Konkrete offene Punkte für die gemeinsame Regelklärung

Noch keine dieser Fragen muss jetzt einzeln beantwortet werden. Sie bilden die Arbeitsliste für spätere Gespräche:

- Welche Startausrüstung und Startgegenstände sollen Charaktere tatsächlich erhalten? Die Referenz nennt mehr als nur Fäuste.
- Welche Teile des Systems sind Pflicht für die erste veröffentlichte Version und welche kommen später?
- Welche Daten dürfen Serverbetreiber per Texteditor ändern, und welche Zustände müssen vor versehentlicher Beschädigung geschützt werden?
- Wie werden Spielstände bei Updates migriert, statt dass angepasste Dateien überschrieben werden?
- Welche Befehle und internen Schlüssel behalten englische Namen, und welche sichtbaren Meldungen sind deutsch?
- Wie sollen Schaden, Level, Siegesserien, Timeout, Belohnungen, Währungen und Shop-Level genau berechnet werden?
- Welche Lizenz und Nutzungsrechte gelten für Inhalte, die aus dem bestehenden Wiki übernommen oder daran angelehnt werden?

**Status:** Die erste technische Vertiefung ist abgeschlossen. Sie reicht, um die Grundbausteine zu erkennen, aber noch nicht, um den Kampf oder die Wirtschaft vollständig zu implementieren. Der nächste sinnvolle Gesprächsschritt ist, die grundlegenden Regeln und den gewünschten Umfang der ersten Version gemeinsam zu klären.
