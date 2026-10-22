# Colosseum – Architekturentwurf für Version 0.1

**Status:** Gemeinsamer Entwurf zur Prüfung; noch nicht als endgültig freigegeben markiert.  
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
| `data/Translation.dat` | Übersetzbare Meldungen; Deutsch als Standardsprache | mIRC-Code |
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

1. Nutzer lädt `Colosseum.mrc`.
2. Der Bootstrap lädt die `.als`-Dateien, damit alle Helper-Aliase vor der Initialisierung verfügbar sind.
3. Der Bootstrap lädt `System.mrc`, `Character.mrc` und `Battle.mrc` als Remote-Skripte.
4. Erst nach dem vollständigen Laden wird initialisiert: Übersetzung und Datendateien prüfen, Laufzeitstatus setzen und den ersten Schlachtzyklus starten.

Laut offizieller mIRC-Dokumentation lädt `/load -a` eine Alias-Datei und `/load -rs` eine Remote-Skriptdatei. Die konkrete Pfadauflösung und der Bootstrap werden beim Implementieren in der Zielversion von mIRC ausprobiert. Quellen: [mIRC `/load`](https://www.mirc.com/help/html/mirc_commands.html), [mIRC Aliases](https://www.mirc.com/help/html/aliases.html), [mIRC Remote Scripts](https://www.mirc.com/help/html/remote.html).

## 5. Interne Alias-Namenskonvention

Vorgeschlagen ist ein gemeinsames Präfix `col_` für alle eigenen Aliases/Identifier, zum Beispiel konzeptionell `col_system_init`, `col_character_create`, `col_battle_join` und `col_damage`. Die endgültigen Namen und Parameter werden gemeinsam festgelegt, wenn wir die konkreten Schnittstellen definieren.

## 6. Datenformate

- Spielerdateien sollen sich an der dokumentierten `.chr`-Vorlage orientieren. Version 0.1 liest und verändert nur notwendige Felder; nicht benötigte Felder müssen nicht verstanden oder ausgewertet werden.
- Monsterdefinitionen liegen einzeln in `data/monsters/`. Die Wiki beschreibt Monsterdateien mit `.char`; vor Implementierung prüfen wir, welche Felder die eigene v0.1-Monsterdefinition wirklich benötigt und ob das bestehende Format unverändert passt.
- `Translation.dat` enthält alle sichtbaren Nachrichten. Format (Schlüssel, Platzhalter, Zeichencodierung und Umgang mit fehlenden Schlüsseln) wird vor Implementierung festgelegt.
- `System.dat` gehört nicht in v0.1; Zeitwerte bleiben fest vorgegeben. Die Modulstruktur soll die spätere Konfiguration aber nicht behindern.

## 7. Noch gemeinsam festzulegen

1. Exakte Befehlsnamen und Schreibweisen für Registrierung/Login/Logout/Passwortänderung/Profilbefehle.
2. Sichere Passwortspeicherung und Verhalten bei verlorenen Zugangsdaten.
3. Exakte Syntax/Schlüsselstruktur von `Translation.dat`.
4. Ob und wie das `.chr`- bzw. `.char`-Datenformat aus der Wiki für v0.1 minimal angepasst werden muss.
5. Exakte Schadensformel und Werte der fünf Startmonster.
6. Wie ein Neustart während einer laufenden Instanz behandelt wird.
7. Ob die erste Ankündigung sofort oder nach dem ersten 3-Minuten-Intervall erfolgt (spätere Zyklen starten 3 Minuten nach Kampfende bzw. leerer Beitrittsphase).

## 8. Vorgesehener nächster Schritt

Prüfe diese Liste und korrigiere Dateinamen oder Verantwortlichkeiten, wo du etwas anders vorgesehen hast. Erst nach deiner Freigabe legen wir die Dateien im Repository an. Danach schreiben wir das Bootstrap-/Ladesystem und testen zuerst, ob alle Module fehlerfrei geladen werden können – erst anschließend kommt Charakterregistrierung und Kampfcode hinzu.
