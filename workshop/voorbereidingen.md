# Voorbereidingen voor de workshop

> Lees dit document **vóór** de workshop door en voer de installatie thuis uit. Lukt dat niet, dan kun je deze stappen ook aan het begin van de workshop doen — maar dan blijft er minder tijd over voor de oefeningen zelf.

## Waarom voorbereiden?

Tijdens de workshop ga je met een Large Language Model (LLM) — bijvoorbeeld GitHub Copilot — software ontwikkelen voor een ESP32 met LoRa-radio. Zonder werkende installatie kun je niet starten met prompten. Door de installatie thuis te doen, win je tijd én voorkom je dat je achterloopt op de groep.

Aan het einde van deze voorbereidingen kun je:

- Een PlatformIO-project compileren en flashen naar de TTGO LoRa32.
- Code laten genereren door een LLM-assistent in VSCode.
- De seriële monitor lezen van je board.

---

## Wat heb je nodig?

### Hardware

| Item                                           | Door wie?  | Opmerking                                                       |
|------------------------------------------------|------------|-----------------------------------------------------------------|
| LilyGO TTGO LoRa32 868 MHz ESP32               | Workshop   | Het display kan kapot zijn — geen probleem voor de oefeningen.  |
| LoRa-antenne (868 MHz)                         | Workshop   | **Altijd vóór het inschakelen aansluiten** — anders kan de zender beschadigen. |
| Micro-USB-kabel (data, geen "alleen-laden")    | Zelf       | Veel goedkope kabels kunnen alleen laden. Test thuis dat je hem aan kunt sluiten en data kunt sturen. |
| Laptop met vrije USB-poort                     | Zelf       | Windows, macOS en Linux zijn allemaal prima.                    |

### Software

| Tool                              | Versie         | Doel                                                          |
|-----------------------------------|----------------|---------------------------------------------------------------|
| Visual Studio Code                | Laatste stable | IDE waarin je werkt.                                          |
| PlatformIO IDE (VSCode-extensie)  | Laatste        | Build- en flash-omgeving voor de ESP32.                       |
| GitHub Copilot (VSCode-extensie)  | Laatste        | LLM-assistent. **Copilot Free is voldoende** voor deze workshop. |
| USB-seriële driver (CP210x of CH340) | n.v.t.      | Zonder driver ziet je laptop het board niet als COM-poort. Vaak al ingebouwd. |

### Accounts

- **GitHub-account** (gratis). Heb je er nog geen? Ga naar [github.com](https://github.com) en maak er een aan.
- **GitHub Copilot Free**. Sinds eind 2024 biedt GitHub een gratis tier van Copilot. Activeer deze via je GitHub-accountinstellingen onder **Settings → Copilot**.

---

## Twee installatieroutes

### Route A — Snelle route (al VSCode + PlatformIO)

Heb je al VSCode én PlatformIO werkend met een ander ESP32-board? Dan hoef je alleen het volgende te doen:

1. **GitHub Copilot extensie** installeren via de VSCode Extensions-tab (zoek op "GitHub Copilot"). Installeer zowel `GitHub Copilot` als `GitHub Copilot Chat`.
2. **Aanmelden** bij GitHub vanuit VSCode (er verschijnt rechtsonder een prompt).
3. **Copilot Free activeren** in je GitHub-instellingen als dat nog niet is gebeurd.
4. **Copilot Chat openen** in VSCode (Ctrl+Alt+I of via de zijbalk).
5. **Het bordprofiel** `ttgo-lora32-v21` toevoegen aan een test-project en compileren (zie [Controle](#controle-vóór-de-workshop) hieronder).

Klaar? Ga door naar de controlestap.

### Route B — Volledige installatie

#### 1. Visual Studio Code

Download via [code.visualstudio.com](https://code.visualstudio.com) en installeer met de standaardopties.

#### 2. PlatformIO IDE extensie

In VSCode:

1. Open de Extensions-tab (Ctrl+Shift+X).
2. Zoek op `PlatformIO IDE`.
3. Klik **Install**. Wacht tot PlatformIO klaar is met setup (kan enkele minuten duren — er worden toolchains op de achtergrond geïnstalleerd).
4. Herstart VSCode wanneer dat gevraagd wordt.

#### 3. GitHub Copilot extensies

Nog steeds in de Extensions-tab:

1. Zoek op `GitHub Copilot`.
2. Installeer **zowel** `GitHub Copilot` **als** `GitHub Copilot Chat`.
3. VSCode vraagt je in te loggen op GitHub — volg de prompt.

#### 4. GitHub Copilot Free activeren

1. Ga naar [github.com](https://github.com) en log in.
2. Klik op je avatar rechtsboven → **Settings**.
3. Kies in het zijmenu **Copilot**.
4. Volg de stappen om **Copilot Free** te activeren (geen creditcard nodig).

> Heb je toegang tot **GitHub Education** via een opleiding? Dan kun je in plaats van Free ook een gratis Pro-account aanvragen.

#### 5. USB-seriële driver

Sluit het board aan met je micro-USB-kabel. Verschijnt er een nieuw apparaat?

- **Windows:** open Apparaatbeheer.
- **macOS:** Systeeminformatie → USB.
- **Linux:** `lsusb` en `dmesg | tail` in een terminal.

Zie je het board? Mooi — je driver werkt. Zo niet: de TTGO LoRa32 gebruikt meestal de CP2102-chip. Installeer de **CP210x VCP driver** van [Silicon Labs](https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers). Op sommige varianten zit een CH340 — installeer dan de CH340-driver.

#### 6. Testproject (aanbevolen)

Maak in VSCode een nieuw PlatformIO-project:

- Naam: `test`
- Bord: `TTGO LoRa32-OLED V2.1.6` (interne ID: `ttgo-lora32-v21`)
- Framework: `Arduino`

Open `src/main.cpp` en plak:

```cpp
void setup() {
  Serial.begin(115200);
}

void loop() {
  Serial.println("test");
  delay(1000);
}
```

Klik op het **Build**-icoon (✓) onderin VSCode. Als het zonder errors compileert: top.

Klik daarna op **Upload** (→). De flash duurt 10–30 seconden.

Open de **Serial Monitor** (stekker-icoon) en stel hem in op 115200 baud. Zie je elke seconde `test` voorbijkomen? Dan is je toolchain compleet.

---

## Controle vóór de workshop

Vink af voordat je naar de workshop komt:

- [ ] VSCode is geïnstalleerd.
- [ ] PlatformIO IDE is geïnstalleerd in VSCode.
- [ ] GitHub Copilot **en** GitHub Copilot Chat zijn geïnstalleerd, en ik kan met Copilot Chat praten.
- [ ] GitHub Copilot Free is geactiveerd op mijn GitHub-account.
- [ ] De USB-driver werkt — mijn laptop ziet het board (lukt dit niet thuis, dan helpen we je op de workshop, maar je verliest tijd).
- [ ] Ik heb een werkende micro-USB-**data**kabel.
- [ ] (Optioneel maar aanbevolen) Ik kan een hello-world flashen naar het board.

Loop je vast? Maak een aantekening van waar je vastliep en welke foutmelding je zag, en neem die mee. We helpen je er aan het begin van de workshop doorheen.
