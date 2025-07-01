# Ultimatte Ethernet Protocol  
**Ultimatte 12 – September 2022**  
**Developer Information**

---

## Controlling Ultimatte using Telnet

Das Ultimatte 12 Ethernet-Protokoll erlaubt es, über Telnet eigene Softwarelösungen zu erstellen, um ein Ultimatte-Gerät per Netzwerk zu steuern.

### Verbindung herstellen:

1. Verbinde Ultimatte per Ethernet mit deinem Computer (direkt oder im selben Netzwerk).
2. Bei direkter Verbindung:
   - Setze eine manuelle statische IP-Adresse auf dem Computer.
   - Die ersten drei Blöcke der IP-Adresse müssen übereinstimmen.
   - Subnetzmaske: `255.255.255.0`
   - Gateway kann leer bleiben.

### Telnet-Steuerung:

Beispiel:
```bash
telnet 192.168.10.220 9998
```

Danach:
```
control:
backing color: blue
```
Antwort: `ack` (Bestätigung)

Weitere Beispiele:
- Werkseinstellungen wiederherstellen:
  ```
  control:
  factory defaults: yes
  ```
- Matte Density ändern:
  ```
  control:
  matte density: 273
  ```

### Telnet beenden:
Drücke `Ctrl + ]`, dann: `quit`

---

## Protokoll-Übersicht

- Textbasiertes Protokoll über TCP-Port `9998`
- Informationen werden in **Blöcken** gesendet, mit Header in Großbuchstaben und `:` (z. B. `IDENTITY:`)
- Zeilen enden mit Newline `\n`
- Blöcke enden mit einer Leerzeile

### Beim Verbinden gesendete Blöcke:

1. **PROTOCOL PREAMBLE**  
   ```
   PROTOCOL PREAMBLE:
   Version: 2.0
   ```

2. **IDENTITY:**  
   ```
   Model: Ultimatte 12 8K
   Label: Ultimatte 12 8K
   Unique ID: 12345678
   ```

3. **NETWORK:**  
   Allgemeine Netzwerkinformationen

4. **NETWORK INTERFACE 0:**  
   Details zur Netzwerkschnittstelle (MAC, IP, etc.)

5. **VERSION:**  
   Produkt- und Softwareinformationen

6. **DEVICE:**  
   Videoformat und Status der Eingänge (Locked/Unlocked)

7. **VIDEO FORMATS:**  
   Unterstützte Formate (NTSC, PAL, 720p60, 1080p60, etc.)

8. **CONTROL:**  
   Aktuelle Steuerparameter (Matte Density, Red Density, etc.)

9. **CONTROL DEFAULT:**  
   Standardwerte der Steuerparameter

10. **CURRENT FILE:**  
    Aktuell geladene Datei

11. **FILE LIST:**  
    Liste gespeicherter Presets

12. **GPI LIST:**  
    Liste von GPI-Ereignissen (nur bei unterstützten Modellen)

13. **IMAGE LIST:**  
    Vorhandene Bilder im Medienspeicher

14. **FRAME BUFFER:**  
    Status der Frame Buffer (aktiv, zugewiesen, Dauer etc.)

15. **END PRELUDE:**  
    Abschließende Kennzeichnung des Status-Dumps

---

## Statusänderungen

- Änderungen werden durch `CONTROL:`-Blöcke an alle verbundenen Clients gesendet
- Beispiel bei Änderung:
  ```
  CONTROL:
  Matte Density: 0
  ```

---

## Änderungen anfordern

Um z. B. Matte Density zu setzen:
```
CONTROL:
Matte Density: 100
```

Antwort: `ACK`

Auch relative Änderungen möglich:
```
CONTROL:
Offset Matte Density: 10
```

---

## Status-Dump anfordern:

Um aktuellen CONTROL-Block zu erhalten:
```
CONTROL:
```

Antwort:
```
ACK
CONTROL:
...
```

---

## Dateioperationen:

- Datei laden:
  ```
  FILE:
  Load: <filename>
  ```

- Datei speichern/löschen/umbenennen:
  ```
  FILE:
  Save/Delete/Rename: <filename>
  ```

---

## GPI-Ereignisse:

- Event einfügen:
  ```
  GPI:
  ID: 1
  Insert: <filename>
  At: -1
  ```

- Event entfernen:
  ```
  GPI:
  ID: 1
  Remove: 0
  ```

---

## Frame Buffer:

Zuweisung eines Bildes:
```
FRAME BUFFER:
BG 1 Frame Buffer Index: 1
BG 1 Frame Buffer Enable: on
```

---

## Kamera-Steuerung:

Nur Ultimatte 12 HD Mini:
```
CAMERACONTROL:
Camera Id: 1
```

---

## Controls (Auszug – alle im Bereich 0–10000 außer anders angegeben):

- **Matte Density**, **Red/Green/Blue Density**
- **Shadow Level**, **Cursor X/Y**
- **Cleanup Level/Strength**
- **Correction Level**, **Noise Level**
- **Flare Level**, **Skin Tone**, **Light/Dark Warm**
- **FG/BG/LY Saturation, Contrast, Black/White Levels**
- **Lighting Levels**, **Window Position/Softness/Skew**
- **Transition Rate (1–120)**
- **Enable/Disable-Funktionen** (On/Off)
- **GP Out Level**, **Color Space (Rec.709/2020)**
- **Video Format Auswahl (z. B. 1080p59.94, 4320p60)**
- **Factory Defaults: Yes**
- **Reset-Funktionen: Yes**

---

## Glossar:

- **FG** = Foreground  
- **BG** = Background  
- **LY** = Layer  
- **GM** = Garbage Matte  
- **BM** = Background Matte  
- **HM** = Holdout Matte  
- **LM** = Layer Matte
