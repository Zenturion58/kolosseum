# Colosseum – Architekturentwurf für Version 0.1

**Status:** Grundstruktur, Dateinamen und Zuständigkeiten sind gemeinsam bestätigt; technische Details und Schnittstellen bleiben noch festzulegen.  
**Ziel:** Kleine, modulare mIRC-Script-Architektur. Fachliche Befehle/Ereignisse gehören in zuständige `.mrc`-Dateien; wiederverwendbare Helfer-Aliases liegen in `.als`-Dateien. Alle sichtbaren Bottexte stammen aus der zentralen `Translation.dat` im Projekt-Hauptordner.

## 1. Repository-Struktur für V0.1 (am Endprodukt ausgerichtet)

Die Verzeichnisstruktur wird bereits in V0.1 an das geplante Endprodukt angelehnt. Die `.mrc`- und `.als`-Dateien liegen im Hauptordner, ebenso die zentralen Konfigurations- und Übersetzungsdateien. Datenverzeichnisse, die erst spätere Versionen nutzen, werden im Layout bereits berücksichtigt, aber in V0.1 noch nicht mit entsprechenden Spielfunktionen befüllt.

```text
kolosseum/                       (Projekt-Hauptordner)
├── Colosseum.mrc                (einziger manuell zu ladender Einstieg)
├── System.mrc
├── Character.mrc
├── Battle.mrc
├── SystemAliases.als
├── Characters.als
├── BattleControl.als
├── BattleFormula.als
├── Monsters.als
├── BattleRewards.als
├── TranslationAliases.als
├── System.dat
├── Translation.dat
├── characters/                  (Spielercharaktere)
├── bosses/                      (spätere Versionen)
├── monsters/                    (Monsterdefinitionen für V0.1 und später)
├── zapped/                      (späteres System)
├── npcs/                        (spätere Versionen)
├── summons/                     (spätere Versionen)
├── help-files/                  (spätere Versionen bzw. spätere Hilfeausweitung)
├── README.md
└── LICENSE
```

Die Ordnernamen folgen den im gezeigten Endversion-Helfer vorgesehenen Standardpfaden (characters, bosses, monsters, zapped, npcs, summons und help-files). Die Ordner werden im Endprodukt nicht erst nachträglich umbenannt oder unter einen neuen data-Ordner verschoben. V0.1 verwendet davon nur die Bereiche, die der Prototyp tatsächlich benötigt, insbesondere characters und monsters; spätere Ordner können zunächst leer bleiben.

Auch die neuen V0.1-Module und Alias-Dateien liegen im Hauptordner, passend zum Ladeprinzip des Endversion-Helfers, der Module per Dateinamen aus dem aktuellen Skriptpfad lädt. Die Namen der V0.1-Dateien bleiben gemäß der bereits bestätigten Modulaufteilung bestehen; die Endversion-Ladeliste wird nicht einfach kopiert.

`System.dat` und `Translation.dat` liegen auf derselben Ebene wie die Einstiegsdatei. Die Übersetzungsdatei wird daher direkt als `Translation.dat` referenziert.

`README.md` und `LICENSE` sind Repository-Dateien, keine Laufzeitmodule. Die Lizenz wird vor der Veröffentlichung separat geklärt.

## 2. Dateiverantwortlichkeiten

| Datei | Verantwortung | Enthält bewusst nicht |
|---|---|---|
| `Colosseum.mrc` | Einstieg, lädt Module in definierter Reihenfolge und stößt die Initialisierung an | Kampfregeln, Passwort- oder Belohnungslogik |
| `System.mrc` | Systembezogene Befehle/Ereignisse und Lebenszyklus | ausführliche allgemeine Helfer |
| `Character.mrc` | Charakter-/Profilbefehle und zugehörige Ereignisse: Registrierung, Login/Logout, Passwortwechsel, Beschreibung, Geschlecht, Stats | die wiederverwendbaren Detailhelfer jeder einzelnen Aktion |
| `Battle.mrc` | Schlacht-/Kampfbefehle und zugehörige Ereignisse: `!enter`, `!angriff`, IRC-`ACTION`-Angriff | eigenständige Schadensformel-Implementierung in jedem Handler |
| `SystemAliases.als` | Allgemeine Systemhelfer, Initialisierung und Timerkoordination | charakter- oder kampfspezifische Regeln, sofern sie anderswo hingehören |
| `Characters.als` | Helfer für Charaktererstellung, Datensatz lesen/schreiben, Passwortprüfung/-wechsel, Loginzustand und Charakterabfragen | IRC-Handler für Profilbefehle |
| `BattleControl.als` | eine Kampfinstanz, Join-Phase, Kampfstart/-ende, Zugsteuerung, Timeout und Orchestrierung | IRC-Handler für Spielerbefehle |
| `BattleFormula.als` | gemeinsame Schadensberechnung und kampfbezogene Mathematik | eigene IRC-Ereignisse |
| `Monsters.als` | Monsterdefinitionen lesen/prüfen, gültiges Monster auswählen und Werte liefern | Spielerbefehle; für v0.1 kein eigenes `Monster.mrc` nötig, solange es keine monsterbezogenen Befehle/Ereignisse gibt |
| `BattleRewards.als` | Ergebnisregel, 10 rote Kugeln bei Sieg + Überleben und Anforderung der Grundwert-Rücksetzung | IRC-Handler |
| `TranslationAliases.als` | `Translation.dat` im Projekt-Hauptordner laden/nachschlagen und Platzhalter einsetzen | fest codierte Nachrichtentexte in Kampf-/Charakterlogik |
| `Translation.dat` im Projekt-Hauptordner | Übersetzbare allgemeine Botmeldungen; Deutsch als Standardsprache. Werte dürfen definierte mIRC-Formatierungsausdrücke und Platzhalter enthalten. | Schlüssel, Platzhalter und Formatierungsausdrücke verändern oder übersetzen |
| `characters/` | dauerhafte Spielerdatensätze; Format an der Wiki-`.chr`-Vorlage orientiert | Alias- oder Eventlogik |
| `monsters/` | mindestens fünf eigene normale Monster; Monsterformat nach Prüfung der Wiki-Vorlage | Kampfsteuerung |
| `System.dat` im Projekt-Hauptordner | Standardwerte und konfigurierbare Laufzeitwerte; fehlt die Datei, wird sie automatisch angelegt | Übersetzungstexte und Kampfnachrichten |
| `bosses/` | künftige Bossdefinitionen; in V0.1 nicht verwendet | normale Monsterdefinitionen |
| `zapped/` | Endversions-Daten für das entsprechende spätere System; in V0.1 nicht verwendet | Charaktere oder normale Monster |
| `npcs/` | künftige NPC-Daten; in V0.1 nicht verwendet | normale Monsterdefinitionen |
| `summons/` | künftige Beschwörungsdaten; in V0.1 nicht verwendet | normale Monsterdefinitionen |
| `help-files/` | mögliche externe Hilfedateien für spätere Ausbaustufen | allgemeine Bottexte aus `Translation.dat` |

### Grundsatz zu Befehlen und Ereignissen

Es wird **keine zentrale `Events.mrc`** verwendet, die alle Befehle einsammelt und weiterleitet. Jeder Fachbereich hält seine IRC-Handler in seiner eigenen `.mrc`-Datei. So liegt beispielsweise `!enter` in `Battle.mrc`, während Charakterregistrierung und Profilbefehle in `Character.mrc` liegen. Die Handler rufen Helfer-Aliases aus den passenden `.als`-Dateien auf.

mIRC hat technisch keine objektorientierten Klassen. Die Begriffe „Modul“ und „Fachbereich“ beschreiben hier die Verantwortlichkeit einzelner Dateien.

## 3. Vorgeschlagene Abhängigkeitsrichtung

```text
Colosseum.mrc (Bootstrap)
  ├── lädt Alias-Dateien
  ├── lädt System.mrc
  ├── lädt Character.mrc
  ├── lädt Battle.mrc
  └── startet Initialisierung

Character.mrc
  ├── Characters.als
  └── TranslationAliases.als

Battle.mrc
  └── BattleControl.als
       ├── Monsters.als
       ├── BattleFormula.als
       ├── BattleRewards.als
       ├── Characters.als
       └── TranslationAliases.als

System.mrc
  └── SystemAliases.als
       └── startet/koordiniert den Schlachtzyklus über BattleControl.als
```

Die Pfeile beschreiben konzeptionelle Aufrufe, nicht schon implementierte Alias-Signaturen. Wir vermeiden gegenseitige Aufrufschleifen, indem möglichst nur übergeordnete Abläufe ihre untergeordneten Helfer aufrufen.

## 4. Ladefolge

Für Version 0.1 ist verbindlich vorgesehen, dass Nutzer nur `Colosseum.mrc` manuell laden müssen. Diese Hauptdatei übernimmt den Bootstrap und lädt die benötigten Dateien in definierter Reihenfolge.

1. Nutzer lädt `Colosseum.mrc`.
2. Der Bootstrap lädt die `.als`-Dateien, damit alle Helper-Aliase vor der Initialisierung verfügbar sind.
3. Der Bootstrap lädt `System.mrc`, `Character.mrc` und `Battle.mrc` als Remote-Skripte.
4. Erst nach dem vollständigen Laden beginnt die Startprüfung. Für Version 0.1 ist `Translation.dat` erforderlich; wird sie vermisst oder kann sie nicht gelesen bzw. vorbereitet werden, wird der Start kontrolliert abgebrochen.
5. Der Schlachtzyklus wird erst gestartet, wenn alle für diese Version erforderlichen Module und Daten erfolgreich geprüft und initialisiert wurden.

**Fehlerbehandlung beim Start:** Bei einer fehlenden oder fehlerhaften Pflichtdatei startet Colosseum keine Schlacht-Timer und gibt eine technische Diagnose im mIRC-Statusfenster aus. `System.dat` ist die Ausnahme beim Fehlen: Diese Datei wird mit Standardwerten automatisch angelegt. Kann `System.dat` nicht angelegt, geschrieben oder erneut gelesen werden, startet kein Schlacht-Timer. Enthält eine vorhandene Datei einen ungültigen Wert, bleibt dieser Eintrag unverändert; die betroffene Funktion wird mit einer verständlichen Diagnose deaktiviert. Unabhängige Botfunktionen dürfen weiterarbeiten. Schlacht-Timer und Kampfabläufe starten nur, wenn alle dafür erforderlichen Einstellungen gültig sind. Die Diagnose muss auch dann möglich sein, wenn `Translation.dat` selbst fehlt; dafür darf eine minimale technische Statusmeldung direkt im Bootstrap stehen. Das ist keine Spieler-Nachricht im IRC-Kanal.

In Version 0.1 muss `System.dat` nach erfolgreicher Verbindung durch `$system_default_check` verfügbar gemacht werden. Fehlt die Datei, legt der Helfer sie mit den vorgesehenen Standardwerten an; fehlen einzelne Schlüssel tatsächlich, ergänzt er diese ebenfalls. Gültige Werte werden in globale mIRC-Variablen bereitgestellt. Vorhandene ungültige Werte bleiben unverändert; die jeweilige abhängige Funktion wird deaktiviert. Kann die Datei technisch nicht angelegt, geschrieben oder erneut gelesen werden, starten keine von ihr abhängigen Schlacht-Timer.

Die Hauptdatei kann in späteren Versionen zusätzliche Aufgaben erhalten. Für Version 0.1 bleibt ihre Hauptaufgabe jedoch das zuverlässige Laden und Initialisieren des Systems; umfangreiche Spiellogik gehört nicht hinein.

Laut offizieller mIRC-Dokumentation lädt `/load -a` eine Alias-Datei und `/load -rs` eine Remote-Skriptdatei. Die konkrete Pfadauflösung und der Bootstrap werden beim Implementieren in der Zielversion von mIRC ausprobiert. Quellen: [mIRC `/load`](https://www.mirc.com/help/html/mirc_commands.html), [mIRC Aliases](https://www.mirc.com/help/html/aliases.html), [mIRC Remote Scripts](https://www.mirc.com/help/html/remote.html).

## 5. Interne Alias-Namenskonvention

Vorgeschlagen ist ein gemeinsames Präfix `col_` für alle eigenen Aliases/Identifier, zum Beispiel konzeptionell `col_system_init`, `col_character_create`, `col_battle_join` und `col_damage`. Die endgültigen Namen und Parameter werden gemeinsam festgelegt, wenn wir die konkreten Schnittstellen definieren.


## 6. Passwortspeicherung und Anmeldung

- Passwörter werden niemals im Klartext in `.chr`-Dateien gespeichert. Das automatisch erzeugte Startpasswort wird einmalig per privater Nachricht an den Spieler gesendet.
- Wenn eine fehlgeschlagene Passwort-PM erkennbar ist, weist eine deutsche Meldung darauf hin, dass der Spieler private Nachrichten des Bots ermöglichen muss. Die Registrierung wird abgebrochen und der neu angelegte Datensatz zurückgerollt/entfernt. Passwörter oder andere Geheimnisse werden niemals ersatzweise öffentlich ausgegeben. Da IRC für `PRIVMSG` nicht generell eine Zustellbestätigung liefert, kann die Implementierung nur Fehler behandeln, die der Client/Server tatsächlich meldet.
- Vorgesehen ist ein moderner Passwort-Hash mit individuellem zufälligem Salt pro Charakter. Beim Login wird der eingegebene Passwortkandidat mit den gespeicherten Parametern geprüft; der Klartext wird nicht gespeichert.
- **Vorgesehene Hashfunktion:** Argon2id über mIRCs `$argon2`-Identifier. Für Colosseum 0.1 wird mIRC 7.83 oder neuer als Mindestversion vorausgesetzt; der Nutzer verwendet mIRC 7.84. Die geeigneten Parameter müssen wir bei der Implementierung testen und so wählen, dass sie sicher sind und keine übermäßig lange Blockierung des Clients verursachen. Quellen: [mIRC-Versionen und Neuigkeiten](https://www.mirc.com/news.html), [OWASP Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html).
- Vergessene Passwörter können nicht aus dem gespeicherten Hash wiederhergestellt oder angezeigt werden. **Ein administrativer Passwort-Reset gehört nicht zu Version 0.1** und ist für eine spätere Version vorgemerkt. Bei einer späteren Umsetzung werden Ablauf, Berechtigungsprüfung und private Zustellung festgelegt; auch dann wird ausschließlich ein neuer Hash mit individuellem Salt gespeichert.
- Ein schneller Einzelhash wie ein alleiniger SHA-256-Aufruf ist nicht als Passwortspeicherverfahren vorgesehen.

## 7. Datenformate

- Spielerdateien sollen sich an der dokumentierten `.chr`-Vorlage orientieren. Version 0.1 liest und verändert nur notwendige Felder; nicht benötigte Felder müssen nicht verstanden oder ausgewertet werden.
- Monsterdefinitionen liegen einzeln in `monsters/`. Die Wiki beschreibt Monsterdateien mit `.char`; vor Implementierung prüfen wir, welche Felder die eigene v0.1-Monsterdefinition wirklich benötigt und ob das bestehende Format unverändert passt.
- `Translation.dat` enthält allgemeine System-, Charakter-, Schlacht- und Belohnungsmeldungen in Sektionen wie `[system]`, `[character]`, `[Battle]` und `[Rewards]`. Jede Zeile hat die Form `Schlüssel=Textvorlage`; beim Übersetzen wird nur der Teil rechts vom `=` verändert.
- Schlüssel links vom `=`, Platzhalter wie `%user%` oder `%file` sowie mIRC-Formatierungsbestandteile wie `$chr(3)`, `$chr(15)`, `$chr(2)` und `$+` müssen exakt erhalten bleiben.
- Texte, die zu einer Angriffsfähigkeit bzw. Angriffsdefinition gehören, werden nicht als allgemeiner Translation-Schlüssel behandelt, sondern über den dafür vorgesehenen Mechanismus für Angriffs-/Fähigkeitsdaten gelesen. Für den Faustangriff in v0.1 ist dieser konkrete Datenweg noch festzulegen.
- Da die Werte mIRC-Ausdrücke enthalten können, muss der Übersetzungshelfer diese gezielt und kontrolliert verarbeiten; wir evaluieren nicht blind beliebigen Dateiinhalt als mIRC-Code.
- Doppelte Schlüssel müssen vor Implementierung aufgelöst werden. Im vorgelegten Beispiel ist `ALREADY_OCCUPIED` zweimal vorhanden; wir behalten nur einen Eintrag oder vergeben für eine tatsächlich andere Meldung einen eigenen eindeutigen Schlüssel.
- `System.dat` wird in V0.1 bei Bedarf mit Standardwerten angelegt; fehlende Einstellungen werden ergänzt. Vorhandene ungültige Werte werden nicht überschrieben: Die betroffene Funktion wird deaktiviert und eine Diagnose nennt den Schlüssel, den gefundenen Wert und den erwarteten Wert bzw. Wertebereich. Die fünf Laufzeitwerte werden nur dann als gültige Laufzeitwerte bereitgestellt, wenn sie erfolgreich geprüft wurden.

## 8. Noch gemeinsam festzulegen

1. Exakte Befehlsnamen und Schreibweisen für Registrierung/Login/Logout/Passwortänderung/Profilbefehle.
2. Konkrete Argon2id-Parameter. Die Mindestversion mIRC 7.83 oder neuer ist bestätigt. Der administrative Passwort-Reset ist aus Version 0.1 ausgeklammert und wird erst für eine spätere Version spezifiziert.
3. Technische Details der `Translation.dat`: Zeichencodierung, Abschnitts-/Schlüsselauflösung, kontrollierte Verarbeitung der mIRC-Formatierungsausdrücke und Verhalten bei fehlenden Schlüsseln.
4. Ob und wie das `.chr`- bzw. `.char`-Datenformat aus der Wiki für v0.1 minimal angepasst werden muss.
5. Exakte Schadensformel und Werte der fünf Startmonster.
6. Wie ein Neustart während einer laufenden Instanz behandelt wird.
7. Start des Schlacht-Timers: bestätigt ist, dass er erst nach erfolgreichem IRC-Verbindungsereignis (`on CONNECT`) und bestandener Pflichtdateiprüfung startet. Die erste Ankündigung erfolgt drei Minuten danach; bei Disconnect werden aktive Timer bereinigt und nach Wiederverbindung ohne Duplikate neu gestartet.
8. Validierungsregeln für `Translation.dat`, einschließlich Pflichtschlüsseln und Behandlung doppelter Schlüssel; außerdem welche Startfehler wie detailliert protokolliert werden.
9. Das vereinfachte Versionsmigrationsprinzip für `System.dat`.

## 9. Vorgesehener nächster Schritt

Die Dateinamen, Hauptzuständigkeiten und das Prinzip einer einzigen manuell zu ladenden Einstiegsdatei `Colosseum.mrc` sind bestätigt. Als Nächstes werden wir die Fehlerbehandlung beim Start und die Schnittstellen zwischen den Modulen festlegen. Danach kann das Grundgerüst angelegt werden. Zuerst testen wir, ob Bootstrap und Module zuverlässig geladen werden; erst anschließend folgen Charakterregistrierung und Kampfcode.

## Festgelegte Fehlerbehandlung für die Registrierung

- Wenn der Bot einen Fehler bei der privaten Zustellung des automatisch erzeugten Startpassworts erkennt, zeigt er einen deutschen Hinweis an, bricht die Registrierung ab und entfernt den neu angelegten Charakterdatensatz. Es gibt keine öffentliche Ersatz-Ausgabe des Passworts. IRC garantiert allerdings nicht in allen Fällen eine Zustellbestätigung; behandelt werden nur erkennbare Fehler.

## Bestätigte Registrierungs- und Befehlsorte

- `!registrieren` wird von `Character.mrc` behandelt und darf sowohl im öffentlichen IRC-Kanal als auch per privater Nachricht an den Bot ausgelöst werden. Da dieser Befehl kein Passwort annimmt, wird dabei kein Geheimnis öffentlich übermittelt.
- Das neu erzeugte Startpasswort darf ausschließlich privat an den Spieler gesendet werden. Passwörter dürfen niemals ersatzweise in einem öffentlichen Kanal erscheinen.
- `!anmelden` und `!passwort` sind ausschließlich per privater Nachricht an den Bot zulässig.
- Eine Anmeldung bleibt bestehen, bis der Spieler die IRC-Verbindung verliert oder seinen Nickname ändert; dann wird die Sitzung beendet. Bei Rückkehr zum ursprünglichen Nick kann er sich mit dem bisherigen Passwort erneut anmelden.
- Bei einem erkennbaren Fehler bei der privaten Zustellung des Startpassworts wird die Registrierung abgebrochen und der neue Charakterdatensatz entfernt; der Bot informiert den Spieler, dass private Nachrichten vom Bot ermöglicht werden müssen. IRC garantiert nicht in jedem Fall eine Zustellbestätigung, daher kann die Logik nur tatsächlich erkennbare Fehler behandeln.


---

## Projektstand-Nachtrag vom 22.10.2026

### Verantwortlichkeiten bestätigt

- `Colosseum.mrc` ist die einzige manuell zu ladende Einstiegsdatei. Sie lädt die erforderlichen Helfer und Fachmodule, prüft den Startzustand und startet den Schlachtzyklus erst nach erfolgreicher Initialisierung.
- `Character.mrc` enthält Charakterbefehle und Charakterereignisse, Registrierung, Passwortgenerierung und -änderung, Anmeldung/Abmeldung, Profilfunktionen sowie den Ablauf für charakterbezogenes Laden und Speichern. Wiederverwendbare Charakterhelfer liegen in `Characters.als`.
- `Battle.mrc` enthält Kampf-/Schlachtbefehle und zugehörige Ereignisse. Die Kampfabwicklung wird von Helfern in `BattleControl.als`, `BattleFormula.als`, `Monsters.als` und `BattleRewards.als übernommen.
- `System.mrc` enthält systembezogene Befehle/Ereignisse; allgemeine Systemhelfer liegen in `SystemAliases.als`.
- Eine zentrale `Events.mrc` und eine separate `Storage.mrc` sind nicht vorgesehen.
- `TranslationAliases.als` lädt und verarbeitet allgemeine Nachrichten aus `Translation.dat` im Projekt-Hauptordner. Die Datendatei nutzt Abschnittsüberschriften und eindeutige Schlüssel im Format `Schlüssel=Text`. Schlüssel, Platzhalter und mIRC-Formatierungsausdrücke müssen beim Übersetzen unverändert bleiben. Es darf nicht blind beliebiger Dateiinhalt als mIRC-Code ausgewertet werden.

### Bootstrap und Konfiguration

- Zuerst werden die erforderlichen Alias-Dateien geladen; anschließend die Fachmodule `System.mrc`, `Character.mrc` und `Battle.mrc`.
- Nach erfolgreichem `on CONNECT` ruft der Systemstart `$system_default_check` auf. Erst nach erfolgreicher Prüfung aller Pflichtmodule und Datenbestände werden Laufzeitstatus vorbereitet und Schlacht-Timer gestartet.
- Fehlt `System.dat`, erzeugt `$system_default_check` die Datei mit Standardwerten und ergänzt tatsächlich fehlende Einträge. Kann die Datei technisch nicht geschrieben oder erneut gelesen werden, wird die betroffene Konfigurationsinitialisierung abgebrochen. Ein vorhandener ungültiger Einzelwert gilt dagegen als Fehler der von ihm abhängigen Funktion: Der Wert bleibt unangetastet und die übrigen unabhängigen Botfunktionen dürfen weiter initialisiert werden. Fehlende oder unbrauchbare `Translation.dat`, fehlende Pflichtmodule oder ungültige für einen konkreten Kampf erforderliche Datenbestände führen zum kontrollierten Abbruch der jeweils abhängigen Funktion. Eine minimale technische Fehlerausgabe im mIRC-Statusfenster muss auch dann möglich sein, wenn Übersetzungstexte nicht geladen werden können.
- `$system_default_check` wird nach erfolgreichem `on CONNECT` aufgerufen. Nach automatischer Anlage bzw. Ergänzung prüft er `System.dat`, `Translation.dat`, erforderliche Module sowie die Pflicht-Datenbestände (Monster, Waffen, Techniken, Skills usw.). Nach erfolgreicher Validierung schreibt er die Systemwerte in globale Variablen.

### Sicherheit und offene Punkte

- Geplant ist Passwort-Hashing mit Argon2id über mIRCs `$argon2`, jeweils mit individuellem zufälligem Salt. Mindestversion mIRC 7.83; Parameter werden noch vor der Implementierung getestet.
- Der administrative Passwort-Reset ist für Version 0.1 ausgeschlossen und bleibt ein zukünftiges Feature.
- Das Konto bleibt an den ursprünglichen IRC-Nick gebunden. Bei einem Nickwechsel wird es nicht übertragen und nicht automatisch gelöscht; kehrt der Nutzer zum ursprünglichen Nick zurück, kann er sich mit dem bisherigen Passwort erneut anmelden. Die IRC-netzwerkspezifische Nickname-Vergleichslogik wird bei der Implementierung berücksichtigt und getestet.
- Die Projektplanung und Spezifikationsänderungen werden in getrennten GitHub-Branches veröffentlicht und als Pull Request überprüfbar gemacht. Produktive `.mrc`-, `.als`- und `.dat`-Dateien werden erst erstellt, wenn die Planungsgrundlagen ausreichend vollständig sind.


## Ergänzte Entscheidung: Rückkehr zum ursprünglichen Nickname

Nach einem zwischenzeitlichen Nickwechsel bleibt der Charakterdatensatz erhalten und an den ursprünglichen Nick gebunden. Bei Rückkehr zu diesem ursprünglichen Nick kann der Nutzer sich mit dem bisherigen Passwort wieder anmelden. Ein Accounttransfer auf den geänderten Nick findet nicht statt.


## Bestätigte Entscheidung: Timer-Start bei IRC-Verbindung

Der Timer für den Schlachtzyklus wird nicht beim Laden oder Starten des Skripts gestartet. Erst wenn mIRC das erfolgreiche Verbindungsereignis (`on CONNECT`) auslöst und die Initialisierung einschließlich Pflichtmodulen und `Translation.dat` erfolgreich ist, beginnt der erste Dreiminuten-Countdown. Danach erscheint die erste Schlachtankündigung.

Bei einem Disconnect werden zum Schlachtzyklus gehörende Timer gestoppt bzw. bereinigt. Nach einer erfolgreichen Wiederverbindung startet der Zyklus sauber neu; parallele doppelte Timer dürfen nicht entstehen.


## Bestätigte Ablaufregel: Systemprüfung nach IRC-Verbindung

Nach erfolgreichem `on CONNECT` ruft der Initialisierungsablauf `$system_default_check` auf. Fehlt `System.dat`, legt der Helfer sie mit Standardwerten an; tatsächlich fehlende Einträge werden ergänzt. Danach prüft er die Konfiguration, `Translation.dat`, erforderliche Module und Spieldaten. Gültige Einstellungen werden in globale Variablen übernommen. Ein vorhandener ungültiger Wert bleibt unverändert; die davon abhängige Funktion wird mit einer Diagnose deaktiviert, während unabhängige Botfunktionen weiterarbeiten dürfen. Der Timer für die erste Schlachtankündigung startet nur, wenn seine eigenen Voraussetzungen und die übrigen für den Schlachtzyklus zwingend erforderlichen Prüfungen erfolgreich sind. Technische Lese-, Schreib- oder Initialisierungsfehler verhindern den Start der betroffenen Funktion und werden im mIRC-Statusfenster ausgegeben.

## Bestätigte Ablaufregel: Anmeldesitzung

Die Anmeldesitzung endet, sobald die Verbindung zum IRC-Netzwerk abbricht oder der Nickname geändert wird. Das Konto selbst bleibt an den ursprünglichen Nick gebunden und wird bei einem Nickwechsel nicht übertragen oder gelöscht. Nach der Rückkehr zum ursprünglichen Nick kann der Spieler sich mit dem bisherigen Passwort neu anmelden.


## Bestätigte Entscheidung: System.dat als Quelle globaler Laufzeitwerte

In Version 0.1 dienen die Einstellungen in `System.dat` als Quelle der konfigurierbaren Laufzeitwerte. Beim Initialisieren werden sie geprüft und in globale mIRC-Variablen geschrieben. Die Fachmodule verwenden anschließend die Variablen, nicht selbst fest codierte Zahlenwerte. Dieses Muster lehnt sich an den vorhandenen Helfer der Endversion an, etwa `TimeToEnter` → `%timeToEnter`.



### Verbindliche Wertebereiche für `System.dat`

- `MaxIdleTurns` muss eine ganze Zahl von mindestens `1` sein.
- Dezimalzahlen, nicht numerische Inhalte sowie Werte außerhalb des jeweiligen Bereichs gelten als ungültig. Ein vorhandener ungültiger Wert wird nicht automatisch verändert. Der Systemhelfer meldet den Schlüssel, den vorhandenen Inhalt und den erwarteten Datentyp bzw. Wertebereich im mIRC-Statusfenster und deaktiviert die davon abhängige Funktion, bis die Datei korrigiert und erneut erfolgreich geprüft wurde.
- Der Schlacht-Timer darf nur starten, wenn die korrigierte Konfiguration erfolgreich gespeichert, erneut eingelesen und validiert wurde.

### Umgang mit manuellen Änderungen an `System.dat`

`System.dat` bleibt eine normale Klartextdatei. Sie wird weder verschlüsselt noch mit einem besonderen Reparaturverfahren gegen manuelle Änderungen geschützt. Nutzer dürfen die Datei bearbeiten, müssen dabei aber die dokumentierte Struktur, Abschnittsnamen, Schlüsselnamen und zulässigen Werte beachten.

Der Systemhelfer prüft die fünf für V0.1 vorgesehenen Einstellungen und behandelt ungültige Werte nach den festgelegten Regeln. Er übernimmt jedoch keine allgemeine Reparatur beliebiger manueller Änderungen und entfernt insbesondere keine doppelt eingefügten Textzeilen automatisch. Unbekannte Schlüssel werden von V0.1 nicht verwendet und nicht automatisch gelöscht.

Wenn Nutzer Schlüssel doppelt eintragen, Abschnitte falsch schreiben oder die Dateistruktur anderweitig verändern, kann die Konfiguration fehlerhaft sein und das Skript möglicherweise nicht wie vorgesehen starten oder funktionieren. Die spätere Dokumentation muss klar erklären, wie die Datei aufgebaut ist, welche Werte zulässig sind und dass manuelle Änderungen auf eigene Verantwortung erfolgen.

### Lade-, Prüf- und Standardwertverhalten

- Nach erfolgreichem `on CONNECT` liest und prüft `$system_default_check` die Pflichtkonfiguration, bevor Timer aktiviert werden.
- Bei Erfolg schreibt der Helfer jeden validierten Wert in eine globale Variable. Andere Module verwenden diese Werte als zentrale Quelle.
- Eine fehlende `System.dat` wird automatisch mit Standardwerten erzeugt. Fehlende Schlüssel werden ebenfalls mit ihren vorgesehenen Standardwerten ergänzt; dabei soll das bewährte Versionsprinzip (`SystemDatVersion`) sinngemäß berücksichtigt werden.
- Bereits vorhandene, ungültige Einstellungswerte bleiben unverändert. Der Systemhelfer meldet den konkreten Fehler und den erwarteten Wert bzw. Wertebereich; die abhängige Funktion bleibt deaktiviert. Andere unabhängige Funktionen dürfen weiter initialisiert werden.
- Kann die Datei nicht angelegt, geschrieben, erneut gelesen oder erfolgreich validiert werden, startet kein Schlacht-Timer. Die genaue V0.1-Versionsmigration wird vor der Implementierung dokumentiert.


## Bestätigte Entscheidung: automatische Standardwert-Erzeugung für System.dat

Für Version 0.1 ist keine vorab vorhandene `System.dat` erforderlich. Wenn die Datei fehlt, legt `$system_default_check` sie mit den vorgesehenen Standardwerten an. In einer vorhandenen Datei fehlende Einstellungen ergänzt der Helfer ebenfalls mit Standardwerten. Danach liest und validiert er die endgültigen Einstellungen und stellt sie als globale mIRC-Variablen bereit.

Der Schlacht-Timer startet nur, wenn die automatische Anlage/Ergänzung sowie alle für den Schlachtzyklus erforderlichen Prüfungen erfolgreich sind. Vorhandene ungültige Einstellungswerte werden nicht repariert und nicht in der Datei verändert. Die jeweilige Funktion bleibt deaktiviert; Funktionen, die von den ungültigen Werten abhängen, starten nicht. Die automatische Anlage von `System.dat` ersetzt keine anderen Pflichtdateien: Eine fehlende `Translation.dat`, ein fehlendes Pflichtmodul oder unbrauchbare notwendige Spieldaten verhindern den Start. Scheitern Schreiben oder Validierung, startet ebenfalls kein Schlacht-Timer.


## Bestätigte Entscheidung: ungültige System.dat-Werte unverändert lassen

Ein vorhandener, aber ungültiger Konfigurationswert wird weder ungeprüft verwendet noch automatisch durch einen Standardwert ersetzt. Die Datei bleibt unverändert. `$system_default_check` meldet im mIRC-Statusfenster den betroffenen Schlüssel, den gefundenen Wert und den zulässigen Datentyp bzw. Wertebereich. Die von diesem Wert abhängige Funktion wird deaktiviert, bis der Nutzer den Eintrag korrigiert und die Konfiguration erneut erfolgreich geprüft wurde. Unabhängige Botfunktionen dürfen weiterlaufen. Ein Schlacht-Timer oder Kampfablauf startet nur dann, wenn die dafür benötigten Einstellungen gültig sind.


## Bestätigte Abgrenzung: eigener, reduzierter Systemhelfer für V0.1

Der vorhandene `system_defaults_check` gehört zur Endversion. Er ist eine Referenz für das allgemeine Muster zur Anlage/Ergänzung von Standardeinstellungen, Validierung, Versionsprüfung und Bereitstellung globaler Variablen; er wird nicht als vollständiger Helfer in V0.1 kopiert.

Für den Prototyp wird ein eigener, kleiner `$system_default_check` spezifiziert. Er verwaltet nur die Einstellungen und Prüfungen, die Colosseum V0.1 tatsächlich benötigt. Endversionspezifische Verzeichnisse, Module, Datenmigrationen und Bereinigungen für Bosse, Mechs, NPCs, Shops, Auktionen, Dungeons, Sonderereignisse oder weitere spätere Systeme gehören nicht automatisch hinein.



## Bestätigte V0.1-Konfigurationsschnittstelle: fünf Laufzeitwerte

Die folgenden fünf Einträge sind für V0.1 freigegeben. `$system_default_check` stellt die validierten Werte in den jeweils genannten globalen Variablen bereit. Fachmodule dürfen diese Laufzeitwerte nicht zusätzlich mit abweichenden fest codierten Zahlenwerten versehen.



## Bestätigte Entscheidung: Ordnerstruktur am Endprodukt ausrichten

Die Ordnerstruktur für V0.1 wird von Beginn an an das vorgesehene Endprodukt angelehnt. Es gibt keinen separaten `scripts/`- oder `data/`-Ordner für die V0.1-Module bzw. die hier beschriebenen Daten. Die Einstiegsdatei, Fachmodule, Alias-Dateien, `System.dat` und `Translation.dat` liegen im Projekt-Hauptordner. Die Datenordner `characters/`, `bosses/`, `monsters/`, `zapped/`, `npcs/`, `summons/` und `help-files/` werden auf der obersten Projektebene vorgesehen, entsprechend den im Endversion-Helfer gezeigten Pfadnamen.

V0.1 nutzt insbesondere `characters/` und `monsters/`. Die übrigen Ordner dürfen für spätere Funktionen vorhanden bzw. im endgültigen Layout vorgesehen sein, ohne dass ihre Endversion-Funktionen bereits implementiert werden. Dadurch sollen spätere Datei- und Ordnerumzüge vermieden werden.

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

