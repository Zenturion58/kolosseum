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

**Fehlerbehandlung beim Start:** Bei einer fehlenden oder fehlerhaften Pflichtdatei gibt Colosseum eine technische Diagnose im mIRC-Statusfenster aus und startet keine Schlacht-Timer. Die Diagnose muss auch dann möglich sein, wenn `Translation.dat` selbst fehlt; dafür darf eine minimale technische Statusmeldung direkt im Bootstrap stehen. Das ist keine Spieler-Nachricht im IRC-Kanal.

`System.dat` ist in Version 0.1 noch nicht Bestandteil des Releases. Die Zeiten bleiben fest vorgegeben; daher darf ein fehlendes `System.dat` in Version 0.1 keinen Startfehler auslösen. Sobald `System.dat` in einer späteren Version eingeführt wird, wird es als Pflichtdatei geprüft, bevor Timer gestartet werden. Ein Konfigurations-Check liest und validiert seine Werte und schreibt sie in globale mIRC-Variablen, auf die andere Module zugreifen können.

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
- `System.dat` gehört nicht in v0.1; Zeitwerte bleiben fest vorgegeben. Die Modulstruktur soll die spätere Konfiguration aber nicht behindern.

## 8. Noch gemeinsam festzulegen

1. Exakte Befehlsnamen und Schreibweisen für Registrierung/Login/Logout/Passwortänderung/Profilbefehle.
2. Konkrete Argon2id-Parameter. Die Mindestversion mIRC 7.83 oder neuer ist bestätigt. Der administrative Passwort-Reset ist aus Version 0.1 ausgeklammert und wird erst für eine spätere Version spezifiziert.
3. Technische Details der `Translation.dat`: Zeichencodierung, Abschnitts-/Schlüsselauflösung, kontrollierte Verarbeitung der mIRC-Formatierungsausdrücke und Verhalten bei fehlenden Schlüsseln.
4. Ob und wie das `.chr`- bzw. `.char`-Datenformat aus der Wiki für v0.1 minimal angepasst werden muss.
5. Exakte Schadensformel und Werte der fünf Startmonster.
6. Wie ein Neustart während einer laufenden Instanz behandelt wird.
7. Ob die erste Ankündigung sofort oder nach dem ersten 3-Minuten-Intervall erfolgt (spätere Zyklen starten 3 Minuten nach Kampfende bzw. leerer Beitrittsphase).
8. Validierungsregeln für `Translation.dat`, einschließlich Pflichtschlüsseln und Behandlung doppelter Schlüssel; außerdem welche Startfehler wie detailliert protokolliert werden.
9. Wenn `System.dat` in einer späteren Version eingeführt wird: Pflichtschlüssel, Wertevalidierung und Verhalten bei fehlenden/ungültigen Einstellungen.

## 9. Vorgesehener nächster Schritt

Die Dateinamen, Hauptzuständigkeiten und das Prinzip einer einzigen manuell zu ladenden Einstiegsdatei `Colosseum.mrc` sind bestätigt. Als Nächstes werden wir die Fehlerbehandlung beim Start und die Schnittstellen zwischen den Modulen festlegen. Danach kann das Grundgerüst angelegt werden. Zuerst testen wir, ob Bootstrap und Module zuverlässig geladen werden; erst anschließend folgen Charakterregistrierung und Kampfcode.

## Festgelegte Fehlerbehandlung für die Registrierung

- Wenn der Bot einen Fehler bei der privaten Zustellung des automatisch erzeugten Startpassworts erkennt, zeigt er einen deutschen Hinweis an, bricht die Registrierung ab und entfernt den neu angelegten Charakterdatensatz. Es gibt keine öffentliche Ersatz-Ausgabe des Passworts. IRC garantiert allerdings nicht in allen Fällen eine Zustellbestätigung; behandelt werden nur erkennbare Fehler.

## Bestätigte Registrierungs- und Befehlsorte

- `!registrieren` wird von `Character.mrc` behandelt und darf sowohl im öffentlichen IRC-Kanal als auch per privater Nachricht an den Bot ausgelöst werden. Da dieser Befehl kein Passwort annimmt, wird dabei kein Geheimnis öffentlich übermittelt.
- Das neu erzeugte Startpasswort darf ausschließlich privat an den Spieler gesendet werden. Passwörter dürfen niemals ersatzweise in einem öffentlichen Kanal erscheinen.
- `!anmelden` und `!passwort` sind ausschließlich per privater Nachricht an den Bot zulässig.
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
- Erst nach dem Laden und erfolgreichen Prüfen der Pflichtdateien werden Laufzeitstatus vorbereitet und die Timer gestartet.
- Bei fehlender oder unbrauchbarer `Translation.dat` (oder einem fehlenden Pflichtmodul) wird die Initialisierung kontrolliert abgebrochen; keine Schlacht-Timer starten. Eine minimale technische Fehlerausgabe im mIRC-Statusfenster muss auch dann möglich sein, wenn Übersetzungstexte nicht geladen werden können.
- `System.dat` ist für Version 0.1 nicht vorgesehen. In einer späteren Version soll ein Helfer nach dem benannten Muster `$system_default_check` diese Datei einlesen, validieren und die Werte in globalen Variablen verfügbar machen.

### Sicherheit und offene Punkte

- Geplant ist Passwort-Hashing mit Argon2id über mIRCs `$argon2`, jeweils mit individuellem zufälligem Salt. Mindestversion mIRC 7.83; Parameter werden noch vor der Implementierung getestet.
- Der administrative Passwort-Reset ist für Version 0.1 ausgeschlossen und bleibt ein zukünftiges Feature.
- Das Konto bleibt an den ursprünglichen IRC-Nick gebunden; bei einem Nickwechsel wird nichts automatisch übertragen oder gelöscht. Noch zu klären ist, ob ein späterer Wechsel zurück zum ursprünglichen Nick den Login mit dem bisherigen Passwort wieder zulassen soll.
- Die Projektplanung und Spezifikationsänderungen werden in getrennten GitHub-Branches veröffentlicht und als Pull Request überprüfbar gemacht. Produktive `.mrc`-, `.als`- und `.dat`-Dateien werden erst erstellt, wenn die Planungsgrundlagen ausreichend vollständig sind.
