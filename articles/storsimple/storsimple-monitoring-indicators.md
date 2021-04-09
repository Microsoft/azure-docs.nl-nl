---
title: StorSimple bewakings indicatoren | Microsoft Docs
description: Beschrijft de lichtgestuurde dioden (Led's) en geluids signalen die worden gebruikt voor het bewaken van de status van het StorSimple-apparaat.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 59dee7b9-ca6d-4fd9-96e6-a0071e8d248e
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 97209dca7d30de037dbd21f5cc145b2941060e70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015397"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>StorSimple-bewakings indicatoren gebruiken om uw apparaat te beheren


## <a name="overview"></a>Overzicht
Uw StorSimple-apparaat bevat Led's (Light-zenders) en waarschuwingen die u kunt gebruiken om de modules en de algehele status van het StorSimple-apparaat te bewaken. De bewakings indicatoren zijn te vinden op de hardwareonderdelen van de primaire behuizing van het apparaat en de EBOD-behuizing. De bewakings indicatoren kunnen Led's of hoorbare waarschuwingen zijn.

Er zijn drie LED-staten die worden gebruikt om de status van een module aan te geven: groen, licht groen naar rood oranje of rood oranje.  

* Groene Led's vertegenwoordigen een gezonde operationele status.  
* Het licht groen naar rood oranje Led's duidt op de aanwezigheid van niet-kritieke omstandigheden waarvoor tussen komst van de gebruiker nood zakelijk is.  
* Rood-oranje Led's geven aan dat er een kritieke fout is opgetreden in de module.  

In de rest van dit artikel worden de verschillende bewakings indicator-Led's beschreven, hun locaties op het StorSimple-apparaat, de Apparaatstatus op basis van de LED-status en eventuele geluids signalen die eraan zijn gekoppeld.

## <a name="front-panel-indicator-leds"></a>Led's voor het front paneel
Het voor paneel, ook wel het paneel *bewerkingen* of het *deel venster OPS*, geeft de algemene status weer van alle modules in het systeem. Het voor paneel is identiek aan de StorSimple primaire en de EBOD-behuizing en wordt hieronder geïllustreerd.  

   ![Voor paneel van Device][1]

Het voor paneel bevat de volgende indica toren:  

1. Knop Dempen
2. LED voor voedings indicator (groen/rood-geel)
3. LED voor module-fout indicator (op rood-geel/uit)
4. LED voor logische fout indicator (op rood-geel/uit
5. Eenheid-ID weer geven  

Het belangrijkste verschil tussen de Led's voor het voor paneel voor het apparaat en die voor de EBOD behuizing is het **identificatie nummer van de systeem eenheid** die wordt weer gegeven op de LED-weer gave. De standaard eenheid-ID die wordt weer gegeven op het apparaat is **00**, terwijl de standaard eenheid-id die wordt weer gegeven in de EBOD Enclosure **01** is. Zo kunt u snel onderscheid maken tussen het apparaat en de EBOD-behuizing wanneer het apparaat is ingeschakeld. Als uw apparaat is uitgeschakeld, gebruikt u de informatie in [inschakelen op een nieuw apparaat](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) om onderscheid te maken tussen het apparaat en de EBOD-behuizing.  

## <a name="front-panel-led-status"></a>LED-status voor paneel
Gebruik de volgende tabel om de status te identificeren die wordt aangegeven door de Led's op het voor paneel voor het apparaat of de EBOD-behuizing.  

| Systeem energie | Module fout | Logische fout | Waarschuwingen | Status |
| --- | --- | --- | --- | --- |
| Rood-geel |UIT |UIT |N.v.t. |Netstroom is verloren, werkt op een back-upstroom of op netstroom en de controller modules zijn verwijderd. |
| Green |AAN |AAN |N.v.t. |Test status van het OPS-paneel (5s) |
| Green |UIT |UIT |N.v.t. |Inschakelen, alle functies goed |
| Green |AAN |N.v.t. |PCM-fout-led's, ventilator fout-Led's |Een PCM-fout, een ventilator fout, meer dan of onder een Tempe ratuur |
| Green |AAN |N.v.t. |Led's voor de I/O-module |Er is een fout opgetreden in de controller module |
| Green |AAN |N.v.t. |N.v.t. |Logische fout in behuizing |
| Green |Flits |N.v.t. |Status van module-LED voor controller module. PCM-fout-led's, ventilator fout-Led's |Onbekende controller module type geïnstalleerd, I2C-bus fout, fout in de configuratie van vitale product gegevens (VPD) van de controller module |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Led's voor de indicator energie-koel module (PCM)
De Led's van de indicator voor elektrische koeling module (PCM) zijn te vinden op de achtergrond van de primaire behuizing of EBOD behuizing van elke PCM-module. In dit onderwerp wordt beschreven hoe u de volgende Led's kunt gebruiken om de status van uw StorSimple-apparaat te controleren.  

* PCM-Led's voor de primaire behuizing
* PCM-Led's voor de EBOD-behuizing

## <a name="pcm-leds-for-the-primary-enclosure"></a>PCM-Led's voor de primaire behuizing
Het StorSimple-apparaat heeft een 764W PCM-module met een extra batterij. In de volgende afbeelding ziet u het LED-paneel voor het apparaat.  

   ![PCM-Led's op de primaire behuizing][2]

LED-legenda:

1. Netstroom storing
2. Fout met ventilator
3. Accu fout
4. PCM OK
5. DC-fout
6. Accu-goed  

De status van de PCM wordt aangegeven in het LED-paneel. Het LED-paneel van Device PCM heeft zes Led's. Vier van deze Led's geven de status van de voeding en de ventilator weer. De overige twee Led's geven de status aan van de module back-upbatterij in de PCM. U kunt de volgende tabellen gebruiken om de status van de PCM te bepalen.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>PCM-indicator-Led's voor voedings toevoer en ventilator
| Status | PCM OK (groen) | AC mislukt (geel) | Defecte ventilatoren (geel) | DC mislukt (geel) |
| --- | --- | --- | --- | --- |
| Geen AC-stroom (naar behuizing) |UIT |UIT |UIT |UIT |
| Geen netstroom (alleen deze PCM) |UIT |AAN |UIT |AAN |
| AC is ON-OK geïnstalleerd |AAN |UIT |UIT |UIT |
| PCM mislukt (fout met ventilator) |UIT |UIT |AAN |N.v.t. |
| PCM-fout (via amp, over spanning, over de huidige) |UIT |AAN |AAN |AAN |
| PCM (buitenste of tolerantie voor ventilator) |AAN |UIT |UIT |AAN |
| Modus stand-by |Knipperend |UIT |UIT |UIT |
| PCM-firmware downloaden |UIT |Knipperend |Knipperend |Knipperend |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>PCM-indicator-Led's voor de back-upbatterij
| Status | Accu-goed (groen) | Accu fout (geel) |
| --- | --- | --- |
| Geen accu aanwezig |UIT |UIT |
| Batterij aanwezig en in rekening gebracht |AAN |UIT |
| Batterij wordt geloosd of onderhouds werkzaamheden |Knipperend |UIT |
| Energie ' zachte ' fout (herstelbaar) |UIT |Knipperend |
| Accu "hard"-fout (niet-herstelbaar) |UIT |AAN |
| Batterij ontwapend |Knipperend |UIT |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>PCM-Led's voor de EBOD-behuizing
De EBOD Enclosure heeft een 580W PCM en geen extra accu. Het PCM-paneel voor de EBOD Enclosure heeft alleen indicator-Led's voor de voedings eenheden en de ventilator. In de volgende afbeelding ziet u deze Led's.

   ![PCM-Led's op de EBOD-behuizing][3] 

U kunt de volgende tabel gebruiken om de status van de PCM te bepalen.  

| Status | PCM OK (groen) | AC mislukt (geel) | Defecte ventilatoren (geel) | DC mislukt (geel) |
| --- | --- | --- | --- | --- |
| Geen AC-stroom (naar behuizing) |UIT |UIT |UIT |UIT |
| Geen netstroom (alleen deze PCM) |UIT |AAN |UIT |AAN |
| AC beschikbaar op – OK |AAN |UIT |UIT |UIT |
| PCM mislukt (fout met ventilator) |UIT |UIT |AAN |X |
| PCM-fout (via het amp, via de spanning, via de huidige |UIT |AAN |AAN |AAN |
| PCM (buitenste of tolerantie voor ventilator) |AAN |UIT |UIT |AAN |
| Stand-by model |Knipperend |UIT |UIT |UIT |
| PCM-firmware downloaden |UIT |Knipperend |Knipperend |Knipperend |

## <a name="controller-module-indicator-leds"></a>Indicator-Led's voor controller module
Het StorSimple-apparaat bevat Led's voor de primaire controller en de EBOD-controller modules.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Bewaking van Led's voor de primaire controller
De volgende afbeelding helpt u bij het identificeren van de Led's op de primaire controller. (Alle onderdelen worden weer gegeven om u te helpen bij de afdruk stand.)  

   ![Bewakings Led's-primaire controller][4]

Gebruik de volgende tabel om te bepalen of de controller module goed werkt.  

### <a name="controller-indicator-leds"></a>Led's voor controller indicator
| GELEID | Description |
| --- | --- |
| LED voor ID (blauw) |Geeft aan dat de module wordt geïdentificeerd. Als de blauwe LED knippert op een actieve controller, is de controller de actieve controller en de andere is de standby-controller. Zie [de actieve controller op het apparaat identificeren](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)voor meer informatie. |
| LED voor fout (geel) |Duidt op een fout in de controller. |
| LED voor OK (groen) |Een constante groen geeft aan dat de controller OK is. Flashing groen duidt op een configuratie fout van een controller-VPD. |
| SAS-activiteit-Led's (groen) |Een gelijkmatig groen geeft aan dat er een verbinding is zonder huidige activiteit. Flashing groen geeft aan dat de verbinding doorlopende activiteiten heeft. |
| Ethernet-status-Led's |Rechter zijde geeft aan dat koppeling/netwerk activiteit: (constante groene) koppeling actief, (knipperend groen) netwerk activiteit. Aan de linkerkant wordt de netwerk snelheid aangegeven: (geel) 1000 MB/s, (groen) 100 MB/s en (uit) 10 MB/s. Afhankelijk van het onderdeel model kan dit licht knip peren zelfs als de netwerk interface niet is ingeschakeld. |
| BERICHT-Led's |Hiermee wordt de voortgang van het opstarten aangegeven wanneer de controller is ingeschakeld. Als het StorSimple-apparaat niet kan worden opgestart, helpt deze LED bij het Microsoft Ondersteuning identificeren van het punt in het opstart proces waarop de fout is opgetreden. |

> [!IMPORTANT]
> Als er een probleem is met de oorzaak van de fout, kan de controller module problemen oplossen door de controller opnieuw te starten. Neem contact op met Microsoft Ondersteuning als dit probleem niet wordt opgelost door de controller opnieuw op te starten.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Bewaking van Led's voor de EBOD (EBOD-behuizing)
Elk van de 6 GB/s SAS EBOD-controllers heeft Led's die de status aangeven, zoals wordt weer gegeven in de volgende afbeelding.  

  ![Bewakings Led's-EBOD Enclosure][5]

Gebruik de volgende tabel om te bepalen of de EBOD-controller module normaal werkt.  

### <a name="ebod-controller-module-indicator-leds"></a>Indicator-Led's van de EBOD-controller module
| Status | I/O-module OK (groen) | Fout in de I/O-module (geel) | Poort activiteit host (groen) |
| --- | --- | --- | --- |
| Controller module OK |AAN |UIT |- |
| Fout in controller module |UIT |AAN |- |
| Geen poort verbinding voor externe host |- |- |UIT |
| Poort verbinding voor externe host: geen activiteit |- |- |AAN |
| Poort verbinding voor externe host-activiteit |- |- |Knipperend |
| Meta gegevens fout van controller module |Knipperend |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Indicator-Led's voor het schijf station voor de primaire behuizing en EBOD behuizing
Het StorSimple-apparaat heeft schijf stations in zowel de primaire behuizing als de EBOD-behuizing. Elke schijf station bevat Led's voor bewakings indicatoren, zoals beschreven in deze sectie. 

Voor de schijf stations wordt de status van het station aangeduid met een groene LED en een rode, oranje LED gekoppeld aan de voor zijde van elke vervoerders module. In de volgende afbeelding ziet u deze Led's.

  ![Led's voor het schijf station][6]

Gebruik de volgende tabel om de status van elk schijf station te bepalen, wat op zijn beurt van invloed is op de status van het algemene front paneel LED.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Indicator-Led's voor het schijf station voor de EBOD-behuizing
| Status | LED voor activiteit OK (groen) | LED voor fout (rood-geel) | LED voor gekoppeld OPS-paneel |
| --- | --- | --- | --- |
| Er is geen station geïnstalleerd |UIT |UIT |Geen |
| Station geïnstalleerd en operationeel |Flashen aan/uit met activiteit |X |Geen |
| Apparaat-id van SCSI Enclosure Services (SES) |AAN |Flitsen 1 seconde op/1 seconde |Geen |
| Bit voor SES-apparaatfout |AAN |AAN |Logische fout (rood) |
| Storing in Energiebeheer circuit |UIT |AAN |Module fout (rood) |

## <a name="audible-alarms"></a>Hoorbare alarm signalen
Een StorSimple-apparaat bevat hoorbare waarschuwingen die zijn gekoppeld aan de primaire behuizing en de EBOD-behuizing. Er bevindt zich een hoorbaar alarm op het front paneel (ook wel het deel venster OPS genoemd) van beide behuizingen. De hoorbare waarschuwing geeft aan wanneer een fout voorwaarde aanwezig is. De waarschuwing wordt door de volgende voor waarden geactiveerd:  

* Fout in ventilator of fout
* De spanning valt buiten het bereik
* Boven of onder temperatuur voorwaarde
* Thermische overloop
* Systeem fout
* Logische fout
* Fout in voeding
* Verwijderen van een module voor energie-koeling (PCM)  

In de volgende tabel worden de verschillende alarm Staten beschreven.  

### <a name="alarm-states"></a>Alarm statussen
| Waarschuwings status | Bewerking | Actie met dempen knop ingedrukt |
| --- | --- | --- |
| S0 |Normale modus: stil |Piep pieptoon twee keer |
| S1 |Fout modus: 1 seconde op/1 seconde uit |Overgang naar S2 of S3 (zie opmerkingen) |
| S2 |Modus voor herinneren: geluid loopt af en toe |Geen |
| S3 |Modus gedempt: stil |Geen |
| S4 |Kritieke fout modus: doorlopend alarm |Niet beschikbaar: Dempen niet actief |

> [!NOTE]
> * Als u in alarm status S1 niet binnen twee minuten op dempen drukt, wordt de status automatisch overgezet naar S2 of S3.  
> * Alarm status S1 tot S4 keer terug naar S0 nadat de fout voorwaarde is gewist.  
> * Kritieke fout status S4 kan worden ingevoerd vanuit een andere status.  


U kunt het hoorbare alarm dempen door op de knop Dempen in het deel venster ops te drukken. Automatische demping vindt plaats na twee minuten als de demp schakelaar niet hand matig wordt gebruikt. Wanneer de wekker is gedempt, blijft er geluid met korte, tijdelijke geluids signalen om aan te geven dat er nog een probleem bestaat. Het alarm wordt op de achtergrond weer wanneer alle problemen zijn opgelost.

In de volgende tabel worden de verschillende alarm omstandigheden beschreven.

### <a name="alarm-conditions"></a>Alarm omstandigheden
| Status | Ernst | Waarschuwingen | LED voor het OPS-paneel |
| --- | --- | --- | --- |
| PCM-waarschuwing: het verlies van gelijkstroom voeding van één PCM |Fout – geen verlies van redundantie |S1 |Module fout |
| PCM-waarschuwing: het verlies van gelijkstroom voeding van één PCM |Fout – verlies van redundantie |S1 |Module fout |
| PCM-ventilator mislukt |Fout – verlies van redundantie |S1 |Module fout |
| PCM-fout in SBB-module gedetecteerd |Slat |S1 |Module fout |
| PCM verwijderd |Configuratiefout |Geen |Module fout |
| Fout in de configuratie van de behuizing |Fout – kritiek |S1 |Module fout |
| Waarschuwing voor een lage waarschuwings temperatuur |Waarschuwing |S1 |Module fout |
| Waarschuwing voor een hoge waarschuwings temperatuur |Waarschuwing |S1 |Module fout |
| Via temperatuur wekker |Fout – kritiek |S1 |Module fout |
| Fout met I2C-bus |Fout – verlies van redundantie |S1 |Module fout |
| Communicatie fout (I2C) van het OPS-paneel |Fout – kritiek |S1 |Module fout |
| Controller fout |Fout – kritiek |S1 |Module fout |
| Fout in module SBB-interface |Fout – kritiek |S1 |Module fout |
| Fout in module SBB-Interface: er resteren geen werkende modules |Fout – kritiek |S4 |Module fout |
| Module SBB-interface verwijderd |Waarschuwing |Geen |Module fout |
| Energie beheer fout |Waarschuwing: de kracht van het station is niet verloren gegaan |S1 |Module fout |
| Energie beheer fout |Fout – kritiek; verlies van de kracht van het station |S1 |Module fout |
| Station verwijderd |Waarschuwing |Geen |Module fout |
| Onvoldoende stroom beschikbaar |Waarschuwing |geen |Module fout |

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [StorSimple en status](storsimple-8000-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


