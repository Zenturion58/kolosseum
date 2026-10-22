# Colosseum – Architekturentwurf für Version 0.1

**Status:** Grundstruktur, Dateinamen und Zuständigkeiten sind gemeinsam bestätigt; technische Details und Schnittstellen bleiben noch festzulegen.  
**Ziel:** Kleine, modulare mIRC-Script-Architektur. Fachliche Befehle/Ereignisse gehören in zuständige `.mrc`-Dateien; wiederverwendbare Helfer-Aliases liegen in `.als`-Dateien. Alle sichtbaren Bottexte stammen aus `data/Translation.dat`.

## 1. Vorgeschlagene Repository-Struktur

```text
Colosseum/
├── Colosseum.mrc
├── scripts/
│   ├── System.mrc
│   ├── Character.mrc
│   ├── Battle.mrc
│   └── aliases/
│       ├── SystemAliases.als
│       ├── Characters.als
│       ├── BattleControl.als
│       ├── BattleFormula.als
│       ├── Monsters.als
│       ├── BattleRewards.als
│       └── TranslationAliases.als
├── data/
│   ├── Translation.dat
│   ├── characters/
│   └── monsters/
├── README.md
└── LICENSE
```

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
| `TranslationAliases.als` | Translation.dat laden/nachschlagen und Platzhalter einsetzen | fest codierte Nachrichtentexte in Kampf-/Charakterlogik |
| `data/Translation.dat` | Übersetzbare allgemeine Botmeldungen; Deutsch als Standardsprache. Werte dürfen definierte mIRC-Formatierungsausdrücke und Platzhalter enthalten. | Schlüssel, Platzhalter und Formatierungsausdrücke verändern oder übersetzen |
| `data/characters/` | dauerhafte Spielerdatensätze; Format an der Wiki-`.chr`-Vorlage orientiert | Alias- oder Eventlogik |
| `data/monsters/` | mindestens fünf eigene normale Monster; Monsterformat nach Prüfung der Wiki-Vorlage | Kampfsteuerung |

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
4. Erst nach dem vollständigen Laden beginnt die Startprüfung. Für Version 0.1 ist `data/Translation.dat` erforderlich; wird sie vermisst oder kann sie nicht gelesen bzw. vorbereitet werden, wird der Start kontrolliert abgebrochen.
5. Der Schlachtzyklus wird erst gestartet, wenn alle für diese Version erforderlichen Module und Daten erfolgreich geprüft und initialisiert wurden.

**Fehlerbehandlung beim Start:** Bei einer fehlenden oder fehlerhaften Pflichtdatei startet Colosseum keine Schlacht-Timer und gibt eine technische Diagnose im mIRC-Statusfenster aus. `System.dat` ist die Ausnahme beim Fehlen: Diese Datei wird mit Standardwerten automatisch angelegt. Kann das nicht gelingen oder bleibt die Konfiguration ungültig, wird der Start abgebrochen. Die Diagnose muss auch dann möglich sein, wenn `Translation.dat` selbst fehlt; dafür darf eine minimale technische Statusmeldung direkt im Bootstrap stehen. Das ist keine Spieler-Nachricht im IRC-Kanal.

In Version 0.1 muss `System.dat` nach erfolgreicher Verbindung durch `$system_default_check` verfügbar gemacht werden. Fehlt die Datei, legt der Helfer sie mit den vorgesehenen Standardwerten an; fehlen einzelne Einstellungen, ergänzt er diese ebenfalls. Nach Validierung stellt er die Werte in globalen mIRC-Variablen für andere Module bereit. Kann die Datei nicht angelegt, geschrieben, erneut gelesen oder geprüft werden, startet kein Schlacht-Timer.

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
- Monsterdefinitionen liegen einzeln in `data/monsters/`. Die Wiki beschreibt Monsterdateien mit `.char`; vor Implementierung prüfen wir, welche Felder die eigene v0.1-Monsterdefinition wirklich benötigt und ob das bestehende Format unverändert passt.
- `Translation.dat` enthält allgemeine System-, Charakter-, Schlacht- und Belohnungsmeldungen in Sektionen wie `[system]`, `[character]`, `[Battle]` und `[Rewards]`. Jede Zeile hat die Form `Schlüssel=Textvorlage`; beim Übersetzen wird nur der Teil rechts vom `=` verändert.
- Schlüssel links vom `=`, Platzhalter wie `%user%` oder `%file` sowie mIRC-Formatierungsbestandteile wie `$chr(3)`, `$chr(15)`, `$chr(2)` und `$+` müssen exakt erhalten bleiben.
- Texte, die zu einer Angriffsfähigkeit bzw. Angriffsdefinition gehören, werden nicht als allgemeiner Translation-Schlüssel behandelt, sondern über den dafür vorgesehenen Mechanismus für Angriffs-/Fähigkeitsdaten gelesen. Für den Faustangriff in v0.1 ist dieser konkrete Datenweg noch festzulegen.
- Da die Werte mIRC-Ausdrücke enthalten können, muss der Übersetzungshelfer diese gezielt und kontrolliert verarbeiten; wir evaluieren nicht blind beliebigen Dateiinhalt als mIRC-Code.
- Doppelte Schlüssel müssen vor Implementierung aufgelöst werden. Im vorgelegten Beispiel ist `ALREADY_OCCUPIED` zweimal vorhanden; wir behalten nur einen Eintrag oder vergeben für eine tatsächlich andere Meldung einen eigenen eindeutigen Schlüssel.
- `System.dat` wird bereits in v0.1 geprüft und eingelesen. Ob einzelne Timerwerte in v0.1 schon dynamisch aus der Datei übernommen oder noch fest vorgegeben werden, wird vor Implementierung ausdrücklich festgelegt.

## 8. Noch gemeinsam festzulegen

1. Exakte Befehlsnamen und Schreibweisen für Registrierung/Login/Logout/Passwortänderung/Profilbefehle.
2. Konkrete Argon2id-Parameter. Die Mindestversion mIRC 7.83 oder neuer ist bestätigt. Der administrative Passwort-Reset ist aus Version 0.1 ausgeklammert und wird erst für eine spätere Version spezifiziert.
3. Technische Details der `Translation.dat`: Zeichencodierung, Abschnitts-/Schlüsselauflösung, kontrollierte Verarbeitung der mIRC-Formatierungsausdrücke und Verhalten bei fehlenden Schlüsseln.
4. Ob und wie das `.chr`- bzw. `.char`-Datenformat aus der Wiki für v0.1 minimal angepasst werden muss.
5. Exakte Schadensformel und Werte der fünf Startmonster.
6. Wie ein Neustart während einer laufenden Instanz behandelt wird.
7. Start des Schlacht-Timers: bestätigt ist, dass er erst nach erfolgreichem IRC-Verbindungsereignis (`on CONNECT`) und bestandener Pflichtdateiprüfung startet. Die erste Ankündigung erfolgt drei Minuten danach; bei Disconnect werden aktive Timer bereinigt und nach Wiederverbindung ohne Duplikate neu gestartet.
8. Validierungsregeln für `Translation.dat`, einschließlich Pflichtschlüsseln und Behandlung doppelter Schlüssel; außerdem welche Startfehler wie detailliert protokolliert werden.
9. Welche konkreten Einstellungen aus der bereits in v0.1 geprüften `System.dat` werden in dieser Version aktiv verwendet, und welche Zeitwerte bleiben vorerst fest?

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
- `TranslationAliases.als` lädt und verarbeitet allgemeine Nachrichten aus `data/Translation.dat`. Die Datendatei nutzt Abschnittsüberschriften und eindeutige Schlüssel im Format `Schlüssel=Text`. Schlüssel, Platzhalter und mIRC-Formatierungsausdrücke müssen beim Übersetzen unverändert bleiben. Es darf nicht blind beliebiger Dateiinhalt als mIRC-Code ausgewertet werden.

### Bootstrap und Konfiguration

- Zuerst werden die erforderlichen Alias-Dateien geladen; anschließend die Fachmodule `System.mrc`, `Character.mrc` und `Battle.mrc`.
- Nach erfolgreichem `on CONNECT` ruft der Systemstart `$system_default_check` auf. Erst nach erfolgreicher Prüfung aller Pflichtmodule und Datenbestände werden Laufzeitstatus vorbereitet und Schlacht-Timer gestartet.
- Fehlt `System.dat`, erzeugt `$system_default_check` die Datei mit Standardwerten und ergänzt fehlende Einträge. Kann die Datei nicht geschrieben oder anschließend nicht erfolgreich validiert werden, wird die Initialisierung abgebrochen. Fehlende oder unbrauchbare `Translation.dat`, fehlende Pflichtmodule oder unvollständige/ungültige benötigte Datenbestände (unter anderem Monster, Waffen, Techniken und Skills) führen ebenfalls zum kontrollierten Abbruch; keine Schlacht-Timer starten. Eine minimale technische Fehlerausgabe im mIRC-Statusfenster muss auch dann möglich sein, wenn Übersetzungstexte nicht geladen werden können.
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

Nach erfolgreichem `on CONNECT` ruft der Initialisierungsablauf `$system_default_check` auf. Fehlt `System.dat`, legt der Helfer sie mit Standardwerten an; fehlende Einträge werden ergänzt. Danach prüft er die Konfiguration, `Translation.dat`, alle benötigten Module und die vorhandenen sowie gültigen Datenbestände für Monster, Waffen, Techniken, Skills und weitere erforderliche Spielinhalte. Nach erfolgreicher Validierung stellt er die Einstellungen in globalen Variablen bereit. Nur wenn die gesamte Prüfung erfolgreich ist, wird der Dreiminuten-Timer für die erste Schlachtankündigung gestartet. Bei Erzeugungs-, Schreib- oder Prüfungsfehlern wird der Schlacht-Timer unterlassen und eine technische Diagnose im mIRC-Statusfenster ausgegeben.

## Bestätigte Ablaufregel: Anmeldesitzung

Die Anmeldesitzung endet, sobald die Verbindung zum IRC-Netzwerk abbricht oder der Nickname geändert wird. Das Konto selbst bleibt an den ursprünglichen Nick gebunden und wird bei einem Nickwechsel nicht übertragen oder gelöscht. Nach der Rückkehr zum ursprünglichen Nick kann der Spieler sich mit dem bisherigen Passwort neu anmelden.


## Bestätigte Entscheidung: System.dat als Quelle globaler Laufzeitwerte

In Version 0.1 dienen die Einstellungen in `System.dat` als Quelle der konfigurierbaren Laufzeitwerte. Beim Initialisieren werden sie geprüft und in globale mIRC-Variablen geschrieben. Die Fachmodule verwenden anschließend die Variablen, nicht selbst fest codierte Zahlenwerte. Dieses Muster lehnt sich an den vorhandenen Helfer der Endversion an, etwa `TimeToEnter` → `%timeToEnter`.

| Zweck | Schlüssel in `System.dat` | Standardwert V0.1 | Globale Variable |
|---|---|---:|---|
| Erste Schlachtankündigung nach erfolgreicher Startprüfung | `TimeBeforeFirstBattle` | `180` Sekunden | `%timeBeforeFirstBattle` |
| Beitrittsfenster | `TimeToEnter` | `120` Sekunden | `%timeToEnter` |
| Abstand bis zur nächsten Ankündigung nach leerer Beitrittsphase oder Kampfende | `TimeBetweenBattles` | `180` Sekunden | `%timeBetweenBattles` |
| Zeitlimit für den Spielerzug | `TimeForIdle` | `180` Sekunden | `%timeForIdle` |
| Maximal übersprungene Spielerzüge | `MaxIdleTurns` | `2` | `%maxIdleTurns` |
| Rote Kugeln als Siegesbelohnung | `VictoryOrbReward` | `10` | `%victoryOrbReward` |

Die sechs Schlüssel, globalen Variablen und Standardwerte in der Tabelle sind für V0.1 bestätigt. Der Endversion-Helfer setzt `TimeBetweenBattles` derzeit auf `120`; für V0.1 ist ausdrücklich `180` Sekunden vorgesehen. Dieser Unterschied ist beabsichtigt, da der Helfer der Endversion nicht unverändert kopiert wird.

### Lade-, Prüf- und Standardwertverhalten

- Nach erfolgreichem `on CONNECT` liest und prüft `$system_default_check` die Pflichtkonfiguration, bevor Timer aktiviert werden.
- Bei Erfolg schreibt der Helfer jeden validierten Wert in eine globale Variable. Andere Module verwenden diese Werte als zentrale Quelle.
- Eine fehlende `System.dat` wird automatisch mit Standardwerten erzeugt. Fehlende Schlüssel werden ebenfalls mit ihren vorgesehenen Standardwerten ergänzt; dabei soll das bewährte Versionsprinzip (`SystemDatVersion`) sinngemäß berücksichtigt werden.
- Bereits vorhandene, ungültige Einstellungswerte werden durch die vorgesehenen Standardwerte ersetzt. Die Korrektur wird im mIRC-Statusfenster gemeldet und in `System.dat` gespeichert; anschließend wird die Startprüfung fortgesetzt.
- Kann die Datei nicht angelegt, geschrieben, erneut gelesen oder erfolgreich validiert werden, startet kein Schlacht-Timer. Wertebereiche und die genaue V0.1-Versionsmigration werden vor Implementierung dokumentiert.


## Bestätigte Entscheidung: automatische Standardwert-Erzeugung für System.dat

Für Version 0.1 ist keine vorab vorhandene `System.dat` erforderlich. Wenn die Datei fehlt, legt `$system_default_check` sie mit den vorgesehenen Standardwerten an. In einer vorhandenen Datei fehlende Einstellungen ergänzt der Helfer ebenfalls mit Standardwerten. Danach liest und validiert er die endgültigen Einstellungen und stellt sie als globale mIRC-Variablen bereit.

Der Schlacht-Timer startet nur, wenn die automatische Anlage/Ergänzung, die Reparatur ungültiger Einstellungen und die gesamte Startprüfung erfolgreich sind. Ungültige Einstellungswerte werden durch Standardwerte ersetzt; die Korrektur wird im mIRC-Statusfenster gemeldet und in der Datei gespeichert, bevor die Prüfung fortgesetzt wird. Die automatische Anlage von `System.dat` ersetzt keine anderen Pflichtdateien: Eine fehlende `Translation.dat`, ein fehlendes Pflichtmodul oder unbrauchbare notwendige Spieldaten verhindern den Start. Scheitern Schreiben oder Validierung, startet ebenfalls kein Schlacht-Timer.


## Bestätigte Entscheidung: ungültige System.dat-Werte korrigieren

Ein vorhandener, aber ungültiger Konfigurationswert wird nicht ungeprüft übernommen und löst nicht allein einen sofortigen Startabbruch aus. `$system_default_check` ersetzt ihn durch den für diesen Schlüssel vorgesehenen Standardwert, meldet die Korrektur im mIRC-Statusfenster und schreibt den korrigierten Wert nach `System.dat`. Anschließend wird die Konfiguration erneut validiert und die Startprüfung fortgesetzt. Wenn Schreiben, erneutes Einlesen oder Validierung fehlschlagen oder eine andere Pflichtprüfung negativ ausfällt, startet kein Schlacht-Timer.


## Bestätigte Abgrenzung: eigener, reduzierter Systemhelfer für V0.1

Der vorhandene `system_defaults_check` gehört zur Endversion. Er ist eine Referenz für das allgemeine Muster zur Anlage/Ergänzung von Standardeinstellungen, Validierung, Versionsprüfung und Bereitstellung globaler Variablen; er wird nicht als vollständiger Helfer in V0.1 kopiert.

Für den Prototyp wird ein eigener, kleiner `$system_default_check` spezifiziert. Er verwaltet nur die Einstellungen und Prüfungen, die Colosseum V0.1 tatsächlich benötigt. Endversionspezifische Verzeichnisse, Module, Datenmigrationen und Bereinigungen für Bosse, Mechs, NPCs, Shops, Auktionen, Dungeons, Sonderereignisse oder weitere spätere Systeme gehören nicht automatisch hinein.

Die sechs Schlüssel, globalen Variablen und Standardwerte in der oben dokumentierten V0.1-Liste sind bestätigt. Für die Implementierung werden zusätzlich zulässige Datentypen/Wertebereiche und die genaue Zuordnung zu den verwendenden Modulen festgelegt. Der Endversion-Helfer setzt `TimeBetweenBattles` auf `120`; für V0.1 ist ausdrücklich `180` Sekunden festgelegt, sodass der Endversion-Wert nicht übernommen wird.


## Bestätigte V0.1-Konfigurationsschnittstelle: sechs Laufzeitwerte

Die folgenden sechs Einträge sind für V0.1 freigegeben. `$system_default_check` stellt die validierten Werte in den jeweils genannten globalen Variablen bereit. Fachmodule dürfen diese Laufzeitwerte nicht zusätzlich mit abweichenden fest codierten Zahlenwerten versehen.

| `System.dat`-Schlüssel | Globale Variable | Standardwert | Modul-/Zweckzuordnung |
|---|---|---:|---|
| `TimeBeforeFirstBattle` | `%timeBeforeFirstBattle` | `180` Sekunden | erster Countdown nach erfolgreicher Verbindung und Startprüfung |
| `TimeToEnter` | `%timeToEnter` | `120` Sekunden | Beitrittsfenster |
| `TimeBetweenBattles` | `%timeBetweenBattles` | `180` Sekunden | Countdown zur nächsten Ankündigung nach leerer Beitrittsphase oder Kampfende |
| `TimeForIdle` | `%timeForIdle` | `180` Sekunden | Zeitlimit eines Spielerzugs |
| `MaxIdleTurns` | `%maxIdleTurns` | `2` | übersprungene Spielerzüge bis zur Niederlage |
| `VictoryOrbReward` | `%victoryOrbReward` | `10` rote Kugeln | Siegesbelohnung bei Überleben des Spielers |

Die getrennten Einstellungen `TimeBeforeFirstBattle` und `TimeBetweenBattles` bleiben bewusst eigenständig, obwohl beide zunächst `180` Sekunden betragen. Sie regeln unterschiedliche Phasen des Zyklus. Der Wert `TimeBetweenBattles = 180` ist eine ausdrückliche Abweichung vom Endversion-Helfer, der aktuell `120` verwendet.
