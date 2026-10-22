# Colosseum – Abgrenzung des Umfangs von Version 0.1

**Status:** Arbeitsgrundlage – bestätigte Entscheidungen und offene Fragen sind ausdrücklich getrennt.

## 1. Verbindliche Leitlinie

- Das öffentliche GitHub-Wiki beschreibt das angestrebte **Zielprodukt** von Colosseum.
- **Version 0.1** ist eine erste, kleine und spielbare Ausbaustufe – nicht das vollständige Spiel.
- Es gibt noch keinen vorhandenen Quellcode, den wir übernehmen. Die Implementierung wird neu entworfen und geschrieben.
- Die sichtbaren Texte des Bots – Nachrichten, Hilfe, Rückmeldungen und Fehlermeldungen – sollen auf Deutsch sein.
- Funktionen, die nicht ausdrücklich zum Umfang von Version 0.1 gehören, werden nicht stillschweigend aufgenommen. Sie bleiben für spätere Versionen vorgemerkt.
- Eine Änderung am Umfang wird erst übernommen, wenn sie gemeinsam besprochen und bestätigt wurde.

## 2. Für Version 0.1 festgelegt

### 2.1 Charakter und Anmeldung

- Spieler können einen neuen Charakter erstellen.
- Bei der Erstellung wird ein zufälliges Passwort erzeugt und dem Spieler privat mitgeteilt.
- Spieler können sich beim Bot mit ihrem Passwort authentifizieren.
- Ein Passwortwechsel ist vorgesehen.
- Charakterdaten bleiben nach einem Bot-Neustart erhalten.
- Neue Charaktere beginnen mit bloßen Fäusten.

### 2.2 Einfacher rundenbasierter Kampf

- Der Bot kündigt eine offene Schlacht an.
- Spieler erhalten ein begrenztes Zeitfenster, um der Schlacht beizutreten.
- Nach Ende des Beitrittsfensters startet der Kampf mit Gegnern.
- Der Kampf läuft rundenbasiert ab.
- Gegner führen ihre Aktionen automatisch aus.
- In Version 0.1 kann ein Spieler eine einfache Angriffsaktion mit den Fäusten ausführen.
- Reagiert ein Spieler während seines Zuges nicht innerhalb der festgelegten Frist, wird sein Zug übersprungen.
- Der Kampf endet, sobald die noch festzulegenden Sieg- bzw. Niederlagebedingungen erfüllt sind.

### 2.3 Belohnungen

- Die erste Version verwendet **rote Kugeln** als Belohnungswährung.
- Eine Siegesbelohnung gibt es nur nach einem gewonnenen Kampf.
- Sie erhalten ausschließlich Spieler, die am Ende des gewonnenen Kampfes noch am Leben sind.
- Die gutgeschriebenen roten Kugeln werden dauerhaft im Charakterdatensatz gespeichert.
- Schwarze Kugeln sind in Version 0.1 keine Kampfbelohnung.

## 3. Für später vorgemerkt – nicht Teil von Version 0.1

Die folgenden Systeme gehören zur langfristigen Vision, sollen aber nicht in den ersten spielbaren Build aufgenommen werden, sofern sie nicht später ausdrücklich vorgezogen werden:

- Ingame-Shop und Kaufmechaniken
- weitere Waffen und Waffenwechsel über die Fäuste hinaus
- Techniken, Skills, Technikpunkte und Zündungs-/Ignition-Systeme
- Gegenstände und Verbrauchsgegenstände wie Tränke
- Schilde, Rüstungen, Zubehör, Instrumente und Lieder
- Stil-System, Stil-Erfahrung und Stilpunkte als Einfluss auf die Belohnung
- schwarze Kugeln als Belohnung, alliierte Notizen, Odin-Marken und Login-Punkte
- NPC-/Trust-Systeme, besondere NPC-Aktionen und Mechs
- Crafting, Verbesserung/Augments und Reparatur
- Truhen, Auktionshaus und Wetten auf KI-Kämpfe
- Eroberungen, Dragon Balls, Drachenjagd, Dungeons und weitere besondere Spielmodi
- umfangreiche Kampfstatistiken, Kampfserien-Speichern/-Laden und sonstige Meta-Systeme
- erweiterte Statuszustände und spezielle Gegnerverhaltensweisen
- DCC-exklusive Funktionen, sofern die erste Version sie nicht für den Basiskampf benötigt

Diese Liste ist eine vorläufige Abgrenzung; sie besagt nicht, dass die Features aus dem Zielprodukt gestrichen werden.

## 4. Architektur-Anforderungen, die früh berücksichtigt werden sollen

Auch wenn die zugehörigen Spielfunktionen später kommen, sollte das Grundgerüst Erweiterungen nicht unnötig erschweren:

- Charakterdaten, Kampfzustand, Spielinhalte und sichtbare Texte soweit sinnvoll voneinander trennen.
- Kampfablauf und Belohnungsvergabe in klar abgegrenzte Funktionen aufteilen.
- Spielerdaten so speichern, dass ein Neustart des Bots sie nicht verliert.
- Passwörter nicht im Klartext ausgeben oder in allgemein sichtbaren Nachrichten speichern; die konkrete sichere Speicherung wird vor der Implementierung festgelegt.
- Nutzeranpassungen und mitgelieferte Standarddateien möglichst so organisieren, dass ein Update eigene Konfigurationen nicht unnötig überschreibt.
- Texte zentral und bearbeitbar halten, damit der Bot durchgehend deutschsprachig ist und spätere Übersetzungen möglich bleiben.
- Erweiterte Systeme erst hinzufügen, wenn ihre Anforderungen und Abhängigkeiten geklärt sind.

## 5. Noch zu entscheiden, bevor der Kampf implementiert wird

Diese Punkte sind noch **nicht** als Regeln festgelegt:

1. Welche konkreten Befehle verwendet die erste Version (inklusive Präfix und Schreibweise)?
2. Wie lange dauern Beitrittsfenster und Spielerzüge?
3. Wie viele Spieler sind für eine Schlacht mindestens erforderlich, und kann eine Person allein kämpfen?
4. Welche Gegner und wie viele davon erscheinen in Version 0.1?
5. Welche minimalen Charakterwerte braucht der einfache Kampf, und wie werden Angriff und Schaden berechnet?
6. Wann genau ist der Kampf gewonnen oder verloren, insbesondere wenn mehrere Spieler teilnehmen?
7. Wie hoch ist die rote Kugel-Belohnung in Version 0.1: ein einfacher fester Betrag oder eine bewusst vereinfachte Formel?
8. Wie wird die Schlacht im IRC angekündigt und ausgegeben (Kanal, private Nachricht oder beides)?
9. Welche IRC-/mIRC-Umgebung und welche Art der Datenhaltung werden unterstützt?
10. Welche Administratorbefehle sind nötig, um den ersten Build zu betreiben und Fehler zu beheben?

## 6. Akzeptanzkriterien für Version 0.1

Version 0.1 gilt erst als erster spielbarer Build, wenn:

- ein Spieler einen Charakter anlegen und sich authentifizieren kann;
- der Charakter nach einem Bot-Neustart noch vorhanden ist;
- der Bot eine Schlacht ankündigt und Spielerbeitritte verarbeitet;
- ein Kampf mit mindestens einer Spieleraktion und automatischen Gegneraktionen vollständig ablaufen kann;
- ein untätiger Spielerzug nach der konfigurierten Frist übersprungen wird;
- Sieg und Niederlage zuverlässig erkannt werden;
- nach einem Sieg ausschließlich überlebende Teilnehmer rote Kugeln erhalten;
- die Währung dauerhaft gespeichert wird;
- die für Spieler sichtbaren Nachrichten auf Deutsch sind;
- ein Shop oder andere zurückgestellte Systeme nicht versehentlich vorausgesetzt werden.

## 7. Verhältnis zur Wiki

Die Wiki bleibt die Referenz für die angestrebten vollständigen Funktionen. Bevor eine spätere Funktion implementiert wird, prüfen wir ihre Wiki-Beschreibung, besprechen nötige Änderungen und ergänzen den Umfang der betreffenden Version. Unklare oder widersprüchliche Regeln werden markiert, nicht stillschweigend geraten.
