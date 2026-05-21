# LLM Coding Workshop — IoT Apeldoorn

Workshopmaterialen voor de **LLM Coding Workshop** op de IoT-Apeldoorn meetup. In deze workshop ervaar je hands-on hoe je Large Language Models (LLM's) zoals ChatGPT en GitHub Copilot inzet als coding-assistent bij het ontwikkelen van embedded software.

---

## Beschrijving van de workshop

In deze workshop oefen je met het gebruik van een LLM-coding-assistent tijdens het ontwikkelen van embedded software. We leren je géén specifieke technologie — we leren je een **vaardigheid**: hoe je effectief communiceert met een LLM om bruikbare, werkende code te krijgen.

Je ontdekt al doende dat de kwaliteit en bewoording van je prompt direct bepalen wat je terugkrijgt: hetzelfde doel, anders verwoord, levert andere code op. Daarnaast oefen je met **klein-en-getest werken**: de meest betrouwbare route van idee naar werkende firmware.

De doelgroep is beginnend met een kleine groep gevorderden. Alles wordt in het Nederlands aangeboden.

---

## Leerdoelen

Na de workshop kun je:

1. Een LLM inzetten als coding-assistent bij embedded ontwikkeling.
2. Je werkomgeving en repository voorbereiden met relevante documentatie, zodat de LLM context heeft.
3. Werken in kleine, testbare stappen: *een olifant eet je in kleine hapjes*.
4. Effectief prompten en je prompts stap voor stap aanscherpen tot je het beoogde resultaat dicht benadert.
5. Met de LLM "rubber ducken" — gebruik hem als slimme gesprekspartner om je eigen begrip te toetsen of om te testen en debuggen.
6. Het V-model toepassen: specificatie → implementatie ↔ unit-, integratie- en systeemtest.

---

## Wat krijg je mee, wat breng je mee?

| Door wie?  | Wat                                                                                            |
|------------|------------------------------------------------------------------------------------------------|
| Workshop   | Een LilyGO TTGO LoRa32 868 MHz ESP32 (display mogelijk defect — geen probleem voor de oefeningen). |
| Workshop   | Een LoRa-antenne voor 868 MHz.                                                                  |
| Zelf       | Een micro-USB-**data**kabel.                                                                    |
| Zelf       | Een laptop met VSCode, PlatformIO en GitHub Copilot geïnstalleerd.                              |

**Lees vóór de workshop** [`voorbereidingen.md`](workshop/voorbereidingen.md). Daar staat precies hoe je je laptop klaarzet. Door dit thuis te doen win je tijd voor de oefeningen zelf.

---

## Opbouw van de workshop

De workshop bestaat uit drie fases:

### Fase 1 — Voorbereidingen
Installatie van VSCode, PlatformIO en GitHub Copilot, en het aanmaken van de benodigde accounts. **Bij voorkeur thuis te doen.** Zie [`voorbereidingen.md`](workshop/voorbereidingen.md).

### Fase 2 — Hallo Wereld (±1,5 uur)
Je bouwt met behulp van een LLM een toepassing die `Hallo wereld` over serial print en een on-board LED laat knipperen. Daarbij oefen je met documentatie verzamelen, functionele en technische specificaties, een gefaseerd implementatieplan en testen volgens het V-model. Zie [`hallo-wereld.md`](workshop/hallo-wereld.md).

### Fase 3 — Verbinding maken
Je activeert de LoRa-radio van het board, zendt een persoonlijk bericht uit en ontvangt berichten van de andere deelnemers. Je leert nadenken over de fysieke parameters (frequentie, SF, BW, CR, preamble, sync word) en oefent verder met prompten en rubber ducking. Zie [`verbinding-maken.md`](workshop/verbinding-maken.md).

Aan het eind sluiten we af met een gezamenlijke reflectie.

---

## Hoe gebruik je deze repository?

- Lees [`voorbereidingen.md`](workshop/voorbereidingen.md) **vóór** de workshop.
- Werk tijdens de workshop de bestanden [`hallo-wereld.md`](workshop/hallo-wereld.md) en [`verbinding-maken.md`](workshop/verbinding-maken.md) per fase door.
- Achtergrondmateriaal over de TTGO LoRa32, ESP32 en LoRa staat in de map [`Documentation/`](Documentation/).
- Een eenvoudig PlatformIO-startproject staat in [`software/helloWorld/`](software/helloWorld/).
- Feedback of verbetervoorstellen? Open een issue of pull request.

---

## Licentie

De workshopmaterialen in deze repository zijn gelicentieerd onder de Creative Commons Attribution-NonCommercial-NoDerivatives 4.0 International License (CC BY-NC-ND 4.0) door IoT Apeldoorn (https://iotapeldoorn.nl/).

<a rel="license" href="https://creativecommons.org/licenses/by-nc-nd/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-nd/4.0/88x31.png" /></a><br />Dit werk valt onder de <a rel="license" href="https://creativecommons.org/licenses/by-nc-nd/4.0/">Creative Commons Attribution-NonCommercial-NoDerivatives 4.0 International License</a>.

---

## Disclaimer

Dit project wordt gedeeld in de hoop dat het nuttig is, maar ZONDER ENIGE GARANTIE; ook niet de impliciete garantie van VERKOOPBAARHEID of GESCHIKTHEID VOOR EEN BEPAALD DOEL.
