![Overview](./resources/Overview.jpg)

## The setup

The Digital Addressable Lighting Interface (DALI) is a communication protocol used in lighting control systems. It allows for digital communication between lighting fixtures, sensors, and control devices, enabling the creation of flexible and dynamic lighting systems. DALI is a two-way protocol, which means that control devices can both send and receive data to and from the lighting fixtures. This allows for individual control of each fixture, such as turning them on and off, dimming, and adjusting color temperature.

DALI is an open standard protocol, meaning that it is not owned by any single company and can be used by any lighting manufacturer. It has gained popularity in recent years due to its ability to reduce energy consumption and provide enhanced lighting control and automation in buildings.

The DALI protocol's data link layer is based on asynchronous serial communication. It consists of a start bit, 8 data bits, and a stop bit. It uses a differential Manchester encoding scheme and the data is transmitted using a two-wire bus, with each device connected in series.

The data link layer also includes mechanisms for addressing and controlling individual devices on the bus. Each DALI device has a unique 16-bit address that can be used to send commands to it. The devices are grouped into 16 logical channels, with up to 64 devices per channel. This allows for control of up to 1,024 devices in a single DALI network.

> Beckhoff Application Film: [Intelligent DALI lighting control system for highest energy efficiency](https://www.youtube.com/watch?v=pvF_gx5tHSY&list=PLO_MYxzSFFnYWlXqCy7Ek5_9-lSvw86cJ&index=13)

> BeckHoff Application Film: [Beckhoff manages 60K data points in Tower 185 at Frankfurt, consisting of DaLI and EnOcean communication.](https://www.youtube.com/watch?v=8wU_nKmCpQ0)

## Hardware

- DALI devices
  - Integratech SR-2303DIN, universele DALI dimmer
  - TRIDONIC DALI-XC, input controller (art. no. 28001716)
  - TRIDONIC DALI MSensor 02 5DPI 41w (art. no. 28000896)
  - TRIDONIC DALI USB 24138923

- PLC terminal: 
  - KL6811 DALI master terminal

## Preperations

- Lees de [documentatie van de dimmer](https://integratech.be/nl/producten/voedingen-en-sturingen/led-strip-sturing-dali/controller-dali-naar-pwm-monocctrgb-w-4x5a-din).
- Lees de [documentatie van de MSensor](https://www.tridonic.com/en/int/product/17380?tab=0).
- Lees de [documentatie van de schakelaars](https://www.tridonic.com/en/int/product/17786?tab=0).
- Lees de documentatie van de DALI configuratie [tool](https://www.tridonic.com/en/int/product/24138923?tab=0) en download [de software](https://www.tridonic.com/en/int/services/software/masterconfigurator)
- Raadpleeg de handleiding van de Beckhoff [KL6811 communication interface](https://download.beckhoff.com/download/Document/io/bus-terminals/kl6811en.pdf). Lees zeker de pagina's:
  - Product overview => Introduction
  - Product overview => Technical data
  - Product overview => Basic function principles
  - Mounting and wiring => LED indicators
  - Configuration software KS2000
  - Appendix => DALI commands
- Lees volgende pagina's over de DALI bibliotheek [Tc2_DALI](https://infosys.beckhoff.com/english.php?content=../content/1033/tcplclib_tc2_dali/index.html&id=)
  - Introduction
  - DALI
  - Programming => POU's => Low-Level commands => Base => FB_KL6811Communication and FB_DALIV2xSendDALICommand
  - Programming => POU's => High-Level commands => Part 102 => Power control => FB_DALIV2Dimmer2Switch
  - Programming => POU's => High-Level commands => Part 102 => Light control
  - Programming => POU's => Third-party function blocks => Tridonic => FB_DALIV2SmartSPOT
  - Programming => DUTs => Structures => ST_KL6811InData, ST_KL6811OutData en ST_DALIV2CommandBuffer

## Goals

1.  Leg in je eigen woorden DALI uit. Hoe is het opgebouwd en wat kan je ermee?
2.  Beschrijf de aansluitingen van de KL6811.
3.  Beschrijf de 'FB_KL6811Communication' functieblok. Welke zijn de in- en uitgangen en waarvoor wordt deze gebruikt?
4.  Beschrijf de 'ST_KL6811InData' structure. Wat is de inhoud en waarvoor wordt deze gebruikt?
5.  Beschrijf de 'ST_KL6811OutData' structure. Wat is de inhoud en waarvoor wordt deze gebruikt?
6.  Beschrijf de 'ST_DALIV2CommandBuffer' structure. Wat is de inhoud en waarvoor wordt deze gebruikt?
7.  Beschrijf de aansluitingen en de werking van de Integratech universele DALI dimmer
8.  Beschrijf de aansluitingen en de werking van de TRIDONIC dali XC input controller en het doel van de TRIDONIC DALI USB
9.  Beschrijf de aansluitingen en de werking van de TRIDONIC DALI MSensor 02

## Exercises

Programmeer de PLC zodat de DALI schakelaar en dimmer volledig te besturen zijn. Voor DALI te programmeren dien je de bibliotheek Tc2_DALI toe te voegen bij je References.


### Programmatie (minimaal vereist om 60% te behalen)

#### 1. Communicatie en device scanning (8%)

Maak gebruik van de 'FB_KL6811Communication' functieblok voor de communicatie met de KL6811. Koppel de juiste variabelen met de KL6811. **Voer een DALI scan uit** om alle devices te detecteren en toon hun adressen en types in de visualisatie. Ga na het vinden van twee devices automatisch over naar het uitlezen (zie volgende opdrachten) maar laat de status van de scan FB zien in de visualisatie voor snelle foutopsporing.

#### 2. Sensor uitlezen (12%)

Lees de DALI sensor uit met de FB_DALIV2SmartSPOT en toon alle beschikbare waarden in de visualisatie:
- Lichtintensiteit (lux)
- Aanwezigheidsstatus (bewegingsdetectie)

#### 3. Dimmer aansturen met uitgebreide bediening (15%)

Stuur de DALI dimmer aan met de FB_DALIV2Dimmer2Switch vanuit de visualisatie. Implementeer de volgende bediening:
- ON/OFF toggle functie
- Dimwaarde instelbaar via slider (0-100%)
- Fade time instelbaar (0-10 seconden) - hoe snel het licht moet in/uitfaden
- Minimum level instelbaar (laagste dim-waarde die gebruikt mag worden)
- Maximum level instelbaar (hoogste dim-waarde die gebruikt mag worden)
- Fade rate instelbaar (snelheid van dim-veranderingen)

**Let op:** Dit zijn zes verschillende parameters die je moet kunnen instellen en naar de dimmer moet kunnen sturen.

#### 4. Automatische lichtregeling (15%)

Implementeer een gesloten regelkring voor automatische lichtregeling:
- Meet de huidige lichtintensiteit van de MSensor
- Vergelijk met een target lichtintensiteit (instelbaar via visualisatie, bv. 500 lux)
- Stuur de dimmer bij om de target te bereiken
- Gebruik een eenvoudige regelaar (bv. proportioneel) om overshoot en oscillatie te vermijden
- Toon in de visualisatie:
  - Gemeten lichtintensiteit
  - Target lichtintensiteit
  - Huidige dimmer output (%)
  - Regelfout (verschil tussen target en gemeten waarde)
- Schakelbaar ON/OFF via visualisatie

> [!TIP]
> Zoek in de Tc2_DALI library naar FB's die te maken hebben met "Light Control" of "Constant Light". Let op dat je een zachte regelactie implementeert, niet een aan/uit schakelaar.

#### 5. Aanwezigheidsdetectie met timer (10%)

Implementeer intelligente aanwezigheidsdetectie:
- Bij detectie van aanwezigheid: licht naar instelbaar niveau (bv. 80%)
- Bij geen aanwezigheid gedurende X seconden (instelbaar via visualisatie): 
  - Licht dimmen naar laag niveau (bv. 20%) OF volledig uitschakelen
  - Keuze tussen dimmen of uitschakelen via visualisatie
- Toon in visualisatie:
  - Aanwezigheidsstatus (Aanwezig / Niet aanwezig)
  - Countdown timer (hoeveel seconden nog tot actie)
  - Huidige modus (Actief / Dimmed / Uit)

**Let op:** De timer moet resetten zodra opnieuw beweging gedetecteerd wordt.

---

### Bijkomende programmatie (Percentage telt enkel indien minimale programmatie bereikt is)

#### 6. Uitgebreide CSV logging met energie-analyse (20%)

Schrijf een geavanceerd logging systeem naar CSV file:

**Te loggen data:**
- Timestamp in formaat `YYYY-MM-DD HH:MM:SS`
- Gemeten lichtintensiteit (lux)
- Dimmer output waarde (%)
- Aanwezigheidsstatus (0 of 1)
- Energie-besparing statistiek: bereken het verschil tussen "altijd aan op 100%" en het daadwerkelijke verbruik

**Functionaliteit:**
- Sample rate instelbaar via visualisatie (1s - 60s)
- Bestandsnaam instelbaar via visualisatie
- Start/Stop functie via druktoets
- Toon aantal geschreven regels in visualisatie

**Energie-besparing berekening:**
Als de dimmer op 50% staat, verbruikt hij minder energie dan op 100%. Bereken voor elke log-regel:
- Theoretisch verbruik als dimmer altijd 100% zou zijn: `tijd × 100%`
- Werkelijk verbruik: `tijd × huidige_dim_waarde%`
- Besparing: `theoretisch - werkelijk`
- Toon cumulatieve besparing in visualisatie

**Let op:** Dit is een complexe opdracht die goed begrip vereist van file I/O, timestamps, en real-time berekeningen.

#### 7. Geavanceerd scene-systeem met automatisering (20%)

Implementeer een professioneel scene-beheer systeem:

**Scene definitie (minimaal 4 scenes):**
- Scene 1: "Dag" (100% brightness, fade time 2s)
- Scene 2: "Avond" (60% brightness, fade time 5s)
- Scene 3: "Film" (20% brightness, fade time 10s)
- Scene 4: "Weg" (0% brightness, fade time 1s)

**Functionaliteit:**
- Elke scene heeft:
  - Eigen dim-waarde (instelbaar)
  - Eigen fade time (instelbaar)
  - Eigen naam
- Scenes opslaan en terugroepen via knoppen in visualisatie
- Automatische scene-wisseling op basis van tijdschema:
  - Minimaal 2 instelbare tijden (bv. 08:00 → Scene "Dag", 20:00 → Scene "Avond")
  - Tijden instelbaar via visualisatie
- Handmatige override:
  - Als gebruiker handmatig een scene selecteert, wordt automatische wisseling tijdelijk uitgeschakeld
  - Via druktoets kan automatische modus weer geactiveerd worden
- Toon in visualisatie:
  - Huidige actieve scene (naam + parameters)
  - Status: "Automatisch" of "Handmatig"
  - Volgende geplande scene-wissel + tijd

**Tip:** Gebruik de system tijd van de PLC om de automatische scene-wisseling te triggeren. Denk aan edge-detection om scenes maar één keer te activeren.

---

## Troubleshooting met masterCONFIGURATOR

Als je DALI communicatie problemen hebt met de PLC, kun je de TRIDONIC DALI USB gebruiken voor diagnostiek:

1. **Disconnect de KL6811** van de DALI bus (belangrijk! Twee masters op één bus geeft conflicten)
2. Sluit de DALI USB aan op je PC en DALI bus
3. Open masterCONFIGURATOR en scan voor devices
4. Verifieer:
   - Worden alle devices gevonden?
   - Zijn de adressen correct?
   - Reageren devices op Quick Test commando's?
5. Als alles werkt in masterCONFIGURATOR maar niet via de PLC:
   - Check je TwinCAT code
   - Verifieer de I/O mappings
   - Check de FB_KL6811Communication parameters

**Let op:** MasterCONFIGURATOR en de PLC kunnen NIET tegelijkertijd op de DALI bus! DALI ondersteunt geen multi-master zonder arbitratie.

---

## Questions

For questions and support please place a comment in the pull request titled **Feedback**.

## Remarks

Take care of the setup. Afterwards return the setup to its original state and place it on top of the lab table in the same place. If you notice a defect, you must immediately inform the teacher.
