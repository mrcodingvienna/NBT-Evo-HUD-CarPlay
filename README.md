# NBT Evo CarPlay HUD Bridge – Eigenentwicklung

**Aktueller Gesamtstand: [Projektstand](Projektstand.md)** – was am F30 bestätigt ist, was offen bleibt und wo die Nachweise liegen. Die folgenden Absätze sind die Chronik der Entwicklung.

**Gemeinsamer Aufnahmepfad.** Der unabhängige Rückbau ist im Starter integriert und lokal mit simulierten QNX-Zielen geprüft. Die CLI bietet den Offline-Plan, die lesende Vorprüfung und die Wiederaufnahme vorhandener Protokolle. Fahrzeugnachweise und Prüfgrenzen stehen in der [Kurzanleitung](Aufnahme-Integration.md).

**iDrive-Plugin „MRcoding“:** Eigene Menü-App, gegen die HMI-Bibliotheken von 007.039.008 gebaut; am 10.11.2026 am F30 installiert, geladen und unter *Mein Fahrzeug* (ganz unten) sichtbar. Siehe [iDrive-Plugin](iDrive-Plugin.md).

**F30 330e mit B48.** Der [integrierte ARM-Prüflauf](F30-RGI-und-Motordaten.md) verbindet Google-Navigationsreplay, originale BMW-Ausgaberoutinen und den Import für Öltemperatur und gemeinsame Sportwerte.

**Passiver Nav-Livecheck vorbereitet:** [Startdatei](Start-Nav-Livecheck.cmd) und [Kurzanleitung](Nav-Controller-Livecheck.md). Eigener ARM-Leser mit Firmwareprüfung, begrenzter Prozessbeobachtung und temporärem Rückbau gebaut. BMW-Vorrang, Thread-/Generationsprüfung und Abbruch laufender Leerung bei BMW-Übernahme sind offline vorbereitet. Noch keine residente Nav-Anbindung oder native Anzeigezuteilung; kein vollständiger HUD-Schreibtest. Keine neue ZIP-Paketierung.

**Neu: HUD-Offline-Baustein 0.1.0.** Nativen Ausgabepfad in `NavNBTEvoController` mit `CIcHudComm`/`HUDTerminal` identifiziert. Eigener ARM-Codec für Pfeilgeometrie und Entfernung gebaut; Zeichenstrom durch den originalen BMW-Decoder ausgeführt. Originale Einheitenformatierung und Auswahl der Display-Kommunikatoren geprüft. Lebende Objekt-/Sitzungsanbindung, eigene Terminal-Ressourcen und reale HUD-Ausgabe bleiben offen. Maßgeblich: [HUD-/Kombi-Anbindung offline](HUD-Kombi-Anbindung-Offline.md). Paket: `build/hud-offline-0.1/mrc-hud-offline-0.1.0.zip`; Build über `tools/build_hud_codec.py`, Verifikation über `tools/verify_hud_offline.py`. Weitere Analyse ist ohne Fahrzeug möglich.

**Empfangsseite: Offline-Stand 0.5.0.** Bootstrap-Bibliothek mit Loader-Einstieg, Treiber-Ereignisbrücke, automatischer Stock-Control-SID-Abfrage im Treiber und begrenzter FIFO-Telemetrie gebaut. Eigenständiges ARM-Prüfprogramm für Laden/IPC vorhanden. Realer QNX-Loader, OEM-Timing und iPhone-Annahme bleiben ungeprüft. Maßgeblich: [Bootstrap und IPC 0.5.0](Bootstrap-und-IPC-0.5.0.md). Vollständiges Empfangspaket über `tools/build_bootstrap.py`; Überprüfung über `tools/verify_bootstrap_release.py`. Das neue HUD-Paket ist ein unabhängiger Baustein. Ältere 0.3-/0.4-Angaben unten sind historisch.

**Neu: Offline-Stand 0.4.0.** RGI-Komponenten-/Start-/Stop-Kandidat, explizite Prozessbinder für Connectivity und mm-ipod, begrenzte Ereignisprotokolle und zeigergeprüfter Rückbau sind implementiert und als ARM-Modul gebaut. Reale HU-Ausführung, Bootstrap, Ereignis-/IPC-Anbindung und iPhone-Annahme sind noch offen. Weitere Arbeit ist offline möglich. Maßgeblicher Stand: [RGI-Aushandlung und Prozessanbindung](RGI-Aushandlung-und-Prozessanbindung.md). Frühere 0.3.0-Angaben unten beschreiben den vorherigen Empfangsstand.

Ziel: eigene, kommerziell anbietbare Erweiterung für MRcodingVienn e.U.  
Entwicklungsträger: BMW F30, NBT Evo ID5, `NBTevo_Y21432J`, CarPlay über Feature Installer aktiviert, HUD mit funktionierender BMW-Zielführung.  
Aktueller Zugang: SSH-Anmeldung an `hu-omap` bereits nachgewiesen; Fahrzeug derzeit getrennt. Installierte Analyse-Dateien und passende Stock-Firmware lokal vorhanden. Ein eigener C-Prototyp wurde jetzt ohne QNX-SDK für ARM gelinkt; der echte HU-Start ist noch offen. Stand: 01.11.2026.

**Arbeitsreihenfolge nach erneuter Prüfung:** Zunächst offline weiterentwickeln. Für die noch ausstehende RGI-Komponentenbeschreibung, Start-/Stop-Logik sowie Lade-/Anbindungs- und Rückbaulogik reichen die vorhandenen Dateien als Entwicklungsbasis. Ein isolierter HU-Selbsttest ist kein zwingender Vorabblocker. Ein Fahrzeugtermin ist erst sinnvoll, wenn ein konkreter, vorab geprüfter Integrationsversuch mit Protokollierung und Rückbau vorbereitet ist. Die tatsächliche HU-Ausführung und echte iPhone-RGI-Daten werden dadurch nicht ersetzt.

**Empfangsanbindung 0.3.0 offline vorbereitet:** Eigene ARM-Empfangspipeline, C-Proxy am nachgewiesenen WLAN-/iAP2-Anbindungspunkt und begrenzte Rohframe-Queue gebaut. Der Stock-Code unterstützt das Ergänzen der Nachrichtenlisten über `[ident] sendmsgs/recvmsgs`; ein Entwurf liegt vor. 62 lokale Softwareprüfungen bestanden, darunter Ausführung eigener und ausgewählter Stock-ARM-Instruktionen mit simulierten OS-/Hilfsaufrufen. Echte HU-Einbindung, vollständige RGI-Aushandlung und iPhone-Rohframes bleiben offen. Details: [CarPlay-Empfang offline](CarPlay-Empfang-Offline.md).

**SDK-freier Build:** `build/qnx-free/mrc-rgi-prototype` ist nun ein 64.328-Byte-ARM-Executable mit QNX-Interpreter und ausschließlich `libc.so.3` als Abhängigkeit. `libmrc_iap2_rx.so` enthält den importfreien Empfangsadapter. Eigener C-Start und Clang/LLD; keine QNX-CRT-Quellen übernommen. Echter QNX-Loader und HU-Ausführung bleiben ungeprüft. Grundlage: [SDK-freier Build](QNX-SDK-freier-Build.md). Der [offizielle SDK-Buildweg](QNX-6.5-ARM-Buildweg.md) bleibt eine zusätzliche Option.

**Entwicklungsfortschritt:** Statische iAP2-Registrierung rekonstruiert: Nachrichtengruppe `0x52` leer. Identifikations-Formatter und Rohsendeweg lokalisiert; eigener Offline-RGI-Decoder implementiert und mit synthetischen Fixtures geprüft. Noch kein nativer HU-Empfänger und keine HUD-Einspeisung. Siehe [Route-Guidance-Entwicklungsstand](RouteGuidance-Entwicklungsstand.md).

**Laufzeitbefund:** 13 von 14 installierten Dateien sind hashidentisch mit Stock, darunter die CarPlay- und Navigationsmodule. `NBTCarHU` enthält eine kleine Abweichung. CarPlay ohne und mit aktiver Apple-Karten-Route wurde erfasst; in den untersuchten PPS-Statusobjekten ist noch kein Manöverstrom nachgewiesen. Siehe [Laufzeitanalyse](Laufzeitanalyse-2026-11-01.md).

**Aktueller Befund:** Die bereitgestellte SVT ergibt `HU_NBT2`, HWEL `00002FC3`, BTLD/SWFL `007_039_008`. 30 passende Firmwaredateien gefunden, 43 Analyse-Dateien extrahiert und 18 ARM32-ELFs untersucht. Siehe [Firmwareanalyse](Firmware-Analyse-007_039_008.md). Der folgende Einstieg ist damit bis zur ersten Offline-Inventarisierung umgesetzt.

## Technische Entscheidung

Native Bridge auf der Headunit als Entwicklungsziel:

`iPhone / CarPlay → Navigationsmetadaten → eigener Zustandsautomat → BMW-Navigationsschnittstelle → vorhandene HUD-Ausgabe`

Erste Ausbaustufe: Manöverpfeil und Entfernung aus Apple Karten im HUD. Straßenname, Google Maps, Kombiinstrument und Installationsprodukt folgen nach dem echten Funktionsnachweis. Eine weitere CarPlay-Aktivierung ist nicht das Entwicklungsziel.

**Noch nicht implementiert:** Live-Anbindung im Connectivity-Prozess, vollständige RGI-Aushandlung, BMW-HUD-Ausgabe, laufender QNX-Dienst und Installer. Es gibt noch keinen fahrzeugfähigen HUD-Patch.

## Was belegt ist – und was untersucht werden muss

| Punkt | Status |
|---|---|
| NBT Evo mit QNX 6.5 und separaten Connectivity-/Navigation-Domains | Am Entwicklungsträger über SSH und installierte ELFs bestätigt; siehe Laufzeitanalyse. [1] liefert ergänzenden Hintergrund. |
| CarPlay-Manöverdaten für HUD nutzbar | Öffentliche Implementierungen auf anderen Plattformen. Keine BMW-kompatible API daraus ableiten. [2] |
| BMW-HUD-Anzeige funktioniert am Entwicklungsträger | Vom Halter bestätigt; nutzbarer OEM-Ausgabepfad vorhanden. |
| CarPlay-Metadatenzugriff auf Y21432J | Offen: Prozess, Libraries, Aushandlung und Datenformat ermitteln. |
| HUD-Einspeisung auf Y21432J | Zeichenformat, ausgewählte native Aufrufverträge und Displayauswahl offline bestätigt. Laufende Objekt-/Sitzungsanbindung und Anzeigehoheit offen; siehe neuer HUD-Bericht. |
| Feature-Installer-Auswirkung | Offen: Stock-Firmware und tatsächlich installierte Dateien können abweichen. |
| Wayglass-Unterstützung | Nachweis eines angebotenen Drittprodukts, keine Kompatibilitätsfreigabe unserer Software. [3] |

## Sofortiger Einstieg ohne SSH

1. In E-Sys den **aktuellen SVT über „Read ECU“ / „Lesen ECU“** auslesen und als `SVT_ist.xml` speichern. Benötigt werden die HU-Einträge für HWEL, BTLD, SWFL und CAFD. Keine TAL-Berechnung oder Programmierung für diesen Schritt.
2. Mit unserem Offlinewerkzeug die exakten SGBM-IDs und Versionen extrahieren. Es unterstützt XML-Namespaces und liest nur die gewählte HU aus `standardSVK`; TAL-Dateien werden zurückgewiesen.
3. Wenn passende PSdZData vorhanden sind, die **genau passenden** HU-Softwaredateien lokalisieren. Das Werkzeug erstellt dafür eine Suchliste und optional einen lokalen Dateiabgleich. Es lädt und kopiert keine Firmware.
4. Die gefundenen Firmwarecontainer offline untersuchen. Öffentlich dokumentierte NBT-Unpacker und SWE-Extraktoren sind Recherchehinweise; ihre Eignung für die konkreten Container ist noch nicht bestätigt. [4, 5]
5. Anhand der Originalsoftware CarPlay-Eingang und HUD-Ausgang identifizieren. Anschließend einen zur HU passenden Ausführungszugang herstellen und die tatsächlich installierten Dateihashes vergleichen. Der vorhandene CarPlay-Patch beweist keinen offenen SSH-Zugang.

Ein SVT enthält Softwarekennungen, keine ausführbaren HU-Dateien. Für Codeanalyse brauchen wir danach passende Firmwaredateien oder einen Dump. Stock-Dateien aus PSdZData ersetzen nicht den Abgleich mit der durch Feature Installer veränderten HU.

## Eigenes Werkzeug

Voraussetzung: Python 3.10 oder neuer, keine Zusatzpakete.

```powershell
python .\tools\hu_inventory.py .\SVT_ist.xml --out .\evidence\hu-inventory.json
```

Optional mit vorhandenem lokalen Datenbestand:

```powershell
python .\tools\hu_inventory.py .\SVT_ist.xml --psdz-root D:\PSdZData --out .\evidence\hu-inventory.json
```

Standard-Zielvarianten: `HU_NBT2` und `HU_NBT_EVO`. Andere XML-Struktur oder Basisvariante: Originaldatei prüfen und Parser gezielt erweitern. Ein Treffer ist ein Dateinamensabgleich, keine Inhalts- oder Signaturprüfung. Der Parser kann nicht feststellen, ob eine eingelesene SVT wirklich aus dem Ist-Zustand stammt; deshalb den unveränderten aktuellen ECU-Read verwenden. VIN und übrige Fahrzeugsteuergeräte werden nicht in den Bericht übernommen.

## Erster echter Entwicklungsnachweis

Es reichen zunächst zwei technische Nachweise auf **Y21432J**:

- **Eingang:** echte Manöver und Entfernung einer laufenden CarPlay-Route empfangen; Route-Ende und Sitzungswechsel erkennen. RGI/iAP2 ist ein zu prüfender Ansatz, keine bereits bestätigte BMW-Schnittstelle. Mazda-Offsets, Nachrichtenlängen und Konfigurationsschalter werden nicht auf BMW übertragen.
- **Ausgang:** einen eindeutig als Entwicklungsausgabe erkennbaren Manöverdatensatz über die identifizierte BMW-Schnittstelle im HUD darstellen und die Anzeigehoheit anschließend vollständig zurückgeben.

Danach beide Wege verbinden. Der Dienst muss veraltete Hinweise verwerfen, bei Verbindungsende leeren und BMW-Zielführung Vorrang geben. Erst danach folgt die Vorbereitung für Kundenfahrzeuge: eindeutige Firmware-/Dateihashprofile, Installationspaket, Wiederherstellung und Versionspflege.

## Quellen und Wiederverwendung

Keine fremden Projektdateien wurden in unseren Code übernommen.

- [1 – NBT-Evo-Boot-/Starter-Analyse](https://www.hakstuff.net/blog/dissecting-the-bmw-nbt-hu-evo-infotainment-unit-boot-process): technische Referenz für QNX und Prozessaufteilung; kein Nachweis für die identischen Dateien auf Y21432J.
- [2 – Mazda-CarPlay-HUD: Datenpfad](https://github.com/KidMixer/mazda-carplay-hud/blob/main/docs/how-it-works.md): Beispiel für Metadaten-Bridge, Plattformcode unter AGPL-3.0; nicht als BMW-Code kopiert.
- [3 – Wayglass](https://wayglass.info/hud): Markt-/Machbarkeitsreferenz, keine Quellcodebasis.
- [4 – BMW_NBT_Tools](https://github.com/bsalvador/BMW_NBT_Tools): öffentliches NBT-/NBT-Evo-Unpacker-Projekt; Format und Nutzungsbedingungen vor Übernahme separat prüfen.
- [5 – BMW-SWE-extractor](https://github.com/speidy/BMW-SWE-extractor): weiterer Offline-Extraktionsansatz, kein bestätigter NBT-Evo-Parser für unsere Dateien.
- [Supra-iDrive6-Android-Auto](https://github.com/Sq00ky/Supra-iDrive6-Android-Auto): Projekt nennt **PolyForm Noncommercial 1.0.0** und untersagt Verkauf/Bundling. Nicht als Basis unseres Verkaufsprodukts übernommen.
- [BMW iDrive-7-Apps](https://github.com/robertnisipeanu/idrive-apps): MGU/iDrive 7, nicht direkt auf unser NBT Evo übertragbar.
- [Audi-RGI-Projekt: komponentenweise Herkunft](https://github.com/edzenis/mib2high-carplay-altscreen-rgi/blob/main/LICENSE): keine pauschal geklärte Wiederverwendungsbasis.

Der frühere `F30-CarPlay-HUD-Test.md` beschreibt die Erprobung eines Drittprodukts und ist für die Eigenentwicklung durch dieses Dokument abgelöst.
