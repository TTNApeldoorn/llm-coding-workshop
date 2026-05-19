# Fase 3 — Verbinding maken (LoRa)

> **Doel:** een LoRa-toepassing bouwen waarmee je een persoonlijk bericht uitzendt en berichten van andere deelnemers ontvangt en weergeeft op de seriële monitor.

## Wat ga je doen?

Je breidt je werk uit met de LoRa-radio van de TTGO LoRa32. Aan het einde:

1. Het board zendt elke 30 seconden een korte tekst uit met jouw naam, bijvoorbeeld: `[Remko] hallo iedereen!`.
2. Tegelijkertijd luistert het naar inkomende LoRa-pakketten en print elk ontvangen pakket naar de seriële monitor, samen met RSSI en SNR.

Als alles werkt, zie je in je seriële monitor zowel je eigen uitgezonden bericht als de berichten van de andere deelnemers.

---

## ⚠️ Veiligheid — antenne eerst!

**Sluit altijd eerst de LoRa-antenne aan voordat je het board onder spanning brengt.** Een SX1276 die zonder antenne zendt, kan beschadigen omdat het uitgezonden vermogen terugkaatst in de chip.

Sluit USB pas aan **nadat** de antenne vastzit. Haal je de antenne er tijdens de workshop af, ontkoppel dan eerst de USB.

---

## LoRa in 60 seconden

LoRa is een radio-modulatie ontworpen voor lange afstand bij laag vermogen. Voor deze workshop hoef je niet alles tot in detail te snappen, maar deze begrippen zijn handig:

| Parameter             | Wat het is                                             | Effect                                                  |
|-----------------------|--------------------------------------------------------|---------------------------------------------------------|
| Frequentie            | De carrier in MHz                                      | Bepaalt op welke band je zendt. Wij gebruiken 868 MHz (EU). |
| Bandbreedte (BW)      | Hoe "breed" je signaal in spectrum is                  | Smaller = gevoeliger, breder = sneller.                 |
| Spreading Factor (SF) | Hoeveel chips per symbool                              | Hoger = robuuster en verder, maar trager.               |
| Coding Rate (CR)      | Forward-error-correction-verhouding                    | Hoger = meer overhead, betere foutcorrectie.            |
| Preamble length       | Aantal preamble-symbolen                               | Helpt de ontvanger om te synchroniseren.                |
| Sync word             | Magisch byte dat zenders en ontvangers herkennen       | Onderscheidt jouw "netwerk" van andere.                 |
| TX power              | Zendvermogen in dBm                                    | Max in de EU 868 ISM-band is 14 dBm voor de meeste subbanden. |

---

## Workshop-parameters — gebruik exact deze!

Om elkaar te kunnen horen, moet **iedereen** dezelfde fysieke instellingen gebruiken. Wijk hier niet van af:

| Parameter            | Waarde                                                 |
|----------------------|--------------------------------------------------------|
| Frequentie           | **868.1 MHz** (868100000 Hz)                           |
| Bandbreedte          | **125 kHz**                                            |
| Spreading Factor     | **SF7**                                                |
| Coding Rate          | **4/5**                                                |
| Preamble length      | **8 symbolen**                                         |
| Sync word            | **0x12** (privé-netwerk, géén LoRaWAN)                 |
| TX power             | **14 dBm**                                             |
| Beacon-interval      | **30 seconden**                                        |
| Payload-formaat      | ASCII, korter dan 50 bytes, formaat `[NAAM] tekst`     |

> Met SF7 en BW 125 kHz is je airtime per pakket maar enkele tientallen milliseconden. 30 seconden tussen pakketten zit ruim onder de 1%-duty cycle die de EU voorschrijft. Doe niet sneller — je blokkeert anderen.

> **Sync word 0x12 vs 0x34:** 0x34 is gereserveerd voor LoRaWAN-netwerken. Wij doen géén LoRaWAN — gebruik 0x12 zodat we ons isoleren van publiek LoRaWAN-verkeer.

---

## De V-model aanpak (kort)

Net als in fase 2: linksboven specificatie, rechts testen. Je doorloopt dezelfde stappen, maar nu met meer technische diepte.

---

## Stap 1 — Documentatie verzamelen

Vóór je begint te prompten, verzamel je de relevante documentatie en geef je die mee aan de LLM:

- Pinout van de TTGO LoRa32 v2.1, met name de SPI-pinnen voor de SX1276:
  - SCK, MISO, MOSI, NSS (SS), RST, DIO0.
- API-documentatie van de `arduino-LoRa`-library: zie [`Documentation/arduino-LoRa/API.md`](../Documentation/arduino-LoRa/API.md) (submodule).
- De parameters uit de tabel hierboven.

> **Leerpunt:** de LLM kent `arduino-LoRa` waarschijnlijk wel, maar welke GPIO-pinnen op jóuw board exact gebruikt worden, weet hij niet zeker. Geef de pinout mee — dat scheelt je een uur debuggen.

---

## Stap 2 — Functionele specificatie

### Voorbeeld prompt

> Ik bouw een LoRa-beacon en -monitor op een LilyGO TTGO LoRa32 v2.1 met SX1276. Help me een functionele specificatie schrijven met deze eisen:
>
> 1. Het apparaat zendt elke 30 seconden een ASCII-bericht uit in de vorm `[NAAM] tekst`.
> 2. Tussen uitzendingen luistert het continu naar LoRa-verkeer op dezelfde frequentie.
> 3. Elk ontvangen pakket wordt naar de seriële monitor geprint, samen met RSSI en SNR.
> 4. Bij elke transmit knippert de LED kort als visuele bevestiging.
>
> Geef genummerde requirements en benoem wat buiten scope is.

### Verwachte uitkomst

5–7 requirements, waaronder RSSI/SNR-rapportage, expliciete uitsluiting van LoRaWAN, maximale payload-grootte en het gedrag bij start-up.

---

## Stap 3 — Technische specificatie

Geef in de prompt expliciet álle parameters mee uit de tabel hierboven. Vraag de LLM om:

- `platformio.ini` met `lib_deps` voor `sandeepmistry/LoRa`.
- Pin-mapping (SCK, MISO, MOSI, NSS, RST, DIO0) op basis van de bijgevoegde pinout-documentatie.
- De setup-sequence voor de radio met onze parameters.
- Een hoofdlus zonder blokkerende `delay()` voor de beacon-timing — gebruik `millis()`.

> **Tip:** vraag specifiek *"geef me de exacte `LoRa.setX()`-calls in de juiste volgorde en leg uit waarom die volgorde belangrijk is."* Sommige instellingen moeten vóór `LoRa.begin()`, andere erna.

---

## Stap 4 — Implementatieplan

Vraag een gefaseerd plan, bijvoorbeeld:

- **A:** alleen transmit. Eén keer per 30 seconden zendt het board en print het naar serial wat het verzonden heeft.
- **B:** alleen receive. Het board luistert continu en print elk ontvangen pakket.
- **C:** combineer in een non-blocking design met `millis()` voor de transmit-trigger en `LoRa.parsePacket()` in de loop voor receive.
- **D:** voeg LED-knipper toe bij transmit en RSSI/SNR aan de print-output toe.

Per fase: vooraf duidelijke testcriteria.

---

## Stap 5 — Implementeren

Per fase, zoals in fase 2:

1. Vraag de LLM om alleen díe fase.
2. Lees mee. Stel rubber-duck-vragen: *"Waarom roep je `LoRa.setSyncWord(0x12)` aan en niet 0x34?"*
3. Compileer, flash.
4. Test (zie stap 6).
5. Ga pas door als het werkt.

### Veelvoorkomende valkuilen

- **Verkeerde pinnen.** Symptoom: `LoRa.begin()` geeft `false`, of er gebeurt niets in de seriële monitor. Check SCK/MISO/MOSI/NSS/RST/DIO0 tegen de pinout-documentatie.
- **Sync-word-mismatch.** Symptoom: je zendt netjes uit, maar niemand ontvangt je. Iedereen op 0x12? Anders kunnen jullie elkaar niet horen.
- **Frequentie-mismatch.** Idem — exact 868.1 MHz, exact dezelfde Hz-waarde.
- **Vergeten antenne.** Symptoom: je ontvangt alles van anderen, maar niemand ontvangt jou. Of erger: na een tijd niets meer.

---

## Stap 6 — Testen

- **Fase A:** verschijnt elke 30 seconden een transmit-log op je serial? Kan een buurman jouw bericht horen? (vraag het hardop)
- **Fase B:** ontvang je iemands transmit? Print je RSSI en SNR?
- **Fase C:** lopen zenden en ontvangen tegelijk zonder dat de receive-stream hapert rond een transmit?
- **Fase D:** knippert de LED zichtbaar bij transmit?

### Bonusvraag voor de groep

Vergelijk RSSI-waarden tussen deelnemers. Wie krijgt iedereen luid en duidelijk binnen? Wie zit aan de rand? Wat zegt dat over jullie posities in de ruimte?

---

## Rubber-duck-momenten

In deze fase zit veel verborgen kennis — RF, timing, SPI. Goede vragen aan de LLM om je begrip te toetsen:

- "Wat is het verschil tussen RSSI en SNR? Wanneer is welke het belangrijkst?"
- "Wat gebeurt er als ik ga zenden terwijl er een pakket binnenkomt?"
- "Waarom geeft een hogere Spreading Factor meer bereik maar minder doorvoer?"
- "Welke onderdelen van mijn code zou je veranderen als ik dit op een batterij wilde draaien?"

---

## Reflectie

- Hoeveel iteraties had je nodig per stap? Wat zat er in jouw eerste prompt dat er in je vijfde prompt niet meer in zat?
- Wat heeft de LLM tijdens deze fase **fout gehad**? Hoe merkte je dat?
- Wat zou je een volgende keer anders aanpakken — meer documentatie vooraf? Kleinere stappen? Andere volgorde?

> Bewaar je notities. We sluiten de workshop af met een groepsreflectie.
