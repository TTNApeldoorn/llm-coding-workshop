# Fase 2 — Hallo Wereld

> **Duur:** ongeveer 1,5 uur
> **Doel:** ervaren hoe je met een LLM een werkende embedded toepassing bouwt door klein te beginnen, goed te prompten en stap voor stap te valideren.

## Wat ga je doen?

Je bouwt met behulp van een LLM (GitHub Copilot Chat) een eenvoudige toepassing op je TTGO LoRa32:

1. Het board print elke seconde `Hallo wereld` over de seriële poort op 115200 baud.
2. Een on-board LED knippert mee in hetzelfde ritme.

Het lijkt simpel. Het ís ook simpel. Maar daar gaat het niet om. Het gaat erom dat je leert om **goed te prompten**, **klein te beginnen** en **iedere stap te valideren**. Dat zijn de vaardigheden waarmee je later complexe firmware kunt bouwen met een LLM aan je zij.

## De olifant en de kleine hapjes

Een LLM werkt het best als je hem opdrachten geeft die hij in één keer kan overzien. Geef je een te grote opdracht in één keer — *"bouw een complete LoRaWAN-node met OLED, deep sleep en OTA-updates"* — dan krijg je code die op het oog werkt maar die je niet kunt narekenen, debuggen of bijsturen.

> *Een olifant eet je niet in één hap. Je eet hem in stukjes.*

In de praktijk betekent dat: deel je doel op in fases, en valideer iedere fase voor je verdergaat.

## De V-model aanpak

We werken in deze workshop met een verkort V-model. Links bouw je op (specificatie → implementatie), rechts test je af (unit → integratie → systeem). Iedere stap aan de linkerkant heeft een corresponderende test rechts.

```
   Functionele specificatie  <----------->  Systeemtest
            \                                 /
       Technische specificatie  <-->  Integratietest
                    \                /
                Implementatieplan <-> Unit-test
                         \         /
                          Implementatie
```

Je doorloopt deze stappen één voor één.

---

## Stap 1 — Verzamel documentatie voor de LLM

Een LLM weet niet alles over jóuw specifieke board. Hij weet veel over ESP32 in het algemeen, maar bijvoorbeeld de exacte pin van de on-board LED op de TTGO LoRa32 v2.1 kan hij gemakkelijk verkeerd raden.

**Wat ga je doen?** Verzamel de relevante documentatie in je project, zodat de LLM die kan lezen.

1. Zoek in de map [`Documentation/`](../Documentation/) van deze repository naar het bestand met de pinout van de TTGO LoRa32 868 MHz.
2. Plaats — of refereer expliciet aan — die documentatie in je werkmap.
3. Vertel Copilot Chat dat hij die documentatie moet gebruiken. (Tip: in Copilot Chat kun je een bestand "attachen" met de `#`-prefix, bijvoorbeeld `#LilyGO_TTGO_LoRa32_868MHz_ESP32.md`.)

> **Leerpunt:** een LLM die de juiste documentatie naast zich heeft, gokt minder. Dat is precies wat je wilt voor embedded werk waar één verkeerde pin het verschil maakt tussen "het werkt" en "ik weet niet wat er gebeurt".

---

## Stap 2 — Functionele specificatie

Schrijf op wat het systeem moet doen, **niet hoe**. Hou het kort en concreet.

### Voorbeeld van een matige prompt

> Schrijf een hello world voor de ESP32.

Te vaag. De LLM gokt het board, gokt de baudrate, en doet geen LED.

### Voorbeeld van een goede prompt

> Ik werk met een LilyGO TTGO LoRa32 v2.1 (868 MHz, ESP32). Help me een functionele specificatie te schrijven voor een eenvoudige toepassing met deze twee eisen:
>
> 1. Het board print elke seconde "Hallo wereld" over de seriële poort op 115200 baud.
> 2. Tegelijkertijd knippert de on-board LED in hetzelfde ritme (1 seconde aan, 1 seconde uit).
>
> Geef de specificatie in genummerde requirements (R1, R2, ...) en benoem expliciet wat er **niet** in scope is.

### Verwachte uitkomst

De LLM geeft 3–5 requirements waarvan er minstens twee corresponderen met je eisen. Bijvoorbeeld:

```
R1: Het systeem zendt elke 1000 ms ± 50 ms de ASCII-string "Hallo wereld\n"
    over UART0 op 115200 8N1.
R2: De on-board LED is aan gedurende de eerste 500 ms van de cyclus
    en uit gedurende de laatste 500 ms.
R3: Initialisatie gebeurt eenmaal bij power-on of reset.

Buiten scope: deep sleep, OTA-updates, knoppen, OLED-display, WiFi.
```

> **Leerpunt:** door de LLM eerst een specificatie te laten opschrijven, dwing je hem (en jezelf) om eerst te denken. Dat scheelt verderop debugtijd.

---

## Stap 3 — Technische specificatie

Nu vraag je de LLM hoe het opgelost gaat worden — welke libraries, welke pinnen, welke calls.

### Voorbeeld prompt

> Op basis van de functionele specificatie hierboven, schrijf een technische specificatie. Gebruik:
>
> - Het Arduino-framework op PlatformIO met board `ttgo-lora32-v21`.
> - De standaard `Serial`-library voor UART.
> - `pinMode` en `digitalWrite` voor de LED. De LED-pin staat in de bijgevoegde documentatie — zoek hem op en vermeld het GPIO-nummer expliciet.
> - Geen extra libraries. Geen interrupts. Voor de timing: `millis()` of `delay()`.

### Verwachte uitkomst

Een document met:

- `platformio.ini`-configuratie.
- LED-pin uit de pinout (laat de LLM het nummer hardop benoemen, dan kun je controleren of hij hetzelfde kiest als de documentatie).
- Pseudocode voor `setup()` en `loop()`.

---

## Stap 4 — Implementatieplan

Hak het op in kleine stukjes. Stel de LLM voor:

> Maak een implementatieplan in drie fases. Elke fase moet onafhankelijk compileerbaar en testbaar zijn:
>
> - **Fase A:** alleen seriële output. Nog geen LED.
> - **Fase B:** voeg LED-knipperen toe, zonder de seriële output uit te zetten.
> - **Fase C:** refactor zodat de timing van seriële print en LED gegarandeerd synchroon loopt.
>
> Per fase: wat is de input, wat is de output, hoe test je het?

---

## Stap 5 — Implementeren (in kleine stappen)

Per fase:

1. Vraag de LLM om de code voor **alleen díe fase**.
2. Lees de code. Snap je wat er staat? Zo niet: vraag uitleg vóór je het draait. Dit is **rubber ducking** — je gebruikt de LLM als slimme medesoftwareontwikkelaar om je eigen begrip te toetsen.
3. Compileer en flash.
4. Test (zie stap 6).
5. Ga pas verder naar de volgende fase als deze werkt.

### Goede vragen tijdens rubber ducking

- "Leg uit waarom je `delay(500)` gebruikt en niet `delay(1000)`."
- "Wat gebeurt er als de seriële poort niet open is?"
- "Wat zijn de risico's van deze aanpak?"
- "Hoe zou je dit anders schrijven als ik geen `delay()` mag gebruiken?"

Je leert vaak meer van het gesprek dan van de eindcode.

---

## Stap 6 — Testen

Per fase een test:

- **Fase A — unit-test (handmatig):** open de seriële monitor. Zie je `Hallo wereld` precies elke seconde?
- **Fase B — unit-test (handmatig):** knippert de LED zichtbaar? Tel met een stopwatch — is de cyclus ongeveer 1 seconde?
- **Fase C — integratietest:** komt de regel exact gelijktijdig met de LED-overgang? Een filmpje op slow-motion van je telefoon is een prima check.

### Bonus

Vraag de LLM om voor je code een **testcase-tabel** te schrijven: per requirement één rij, met "hoe test je dit?" als kolom. Dit dwingt je om te toetsen of je requirements **testbaar** zijn.

---

## Tips voor effectief prompten

1. **Vertel wie je bent en wat je hebt.** "Ik werk met bord X, framework Y, versie Z."
2. **Geef expliciet aan wat binnen en buiten scope is.** Anders gaat de LLM "behulpzaam" doen en bouwt hij dingen die je niet vroeg.
3. **Vraag om uitleg, niet alleen om code.** *"Leg uit waarom"* is je beste vriend.
4. **Werk in kleine stappen.** Eén concept tegelijk. Lukt het niet in drie pogingen? Stap terug en specificeer kleiner.
5. **Confronteer fouten letterlijk.** Plak de echte foutmelding in je volgende prompt — niet je samenvatting van de fout.
6. **Hou jezelf de baas.** De LLM heeft geen oordeel. Klopt iets niet, zeg dat dan en vraag opnieuw.

---

## Reflectievragen

Tijdens of na deze fase, bespreek met je buurman:

- Welke prompts werkten goed? Welke niet? Waar denk je dat dat aan ligt?
- Hoeveel tijd kostte het schrijven van de specificatie? Hoeveel de code? Welke verhouding voelt logisch?
- Op welk moment wist je zeker dat het ging werken — vóór of na de eerste flash?

> Bewaar je antwoorden. We pakken ze terug in fase 3.
