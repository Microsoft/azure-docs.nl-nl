---
title: Aanbevelingen voor SDK-microfoon matrix voor speech-apparaten
titleSuffix: Azure Cognitive Services
description: Aanbevelingen voor de SDK van speech-apparaten. Deze matrix geometrie wordt aanbevolen voor gebruik met de micro soft audio-stack.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: 698a1d52af6c2472d6c025851ead1a0b6a6dff82
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95015338"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Aanbevelingen voor SDK-microfoon matrix voor speech-apparaten

In dit artikel leert u hoe u een microfoon matrix kunt ontwerpen voor de SDK voor spraak apparaten.

De SDK voor spraak apparaten werkt het beste met een microfoon matrix die is ontworpen volgens de volgende richt lijnen, inclusief de geometrie van de microfoon en de selectie van onderdelen. Richt lijnen worden ook gegeven over integratie-en elektrotechnische overwegingen.

## <a name="microphone-geometry"></a>Geometrie van microfoon

De volgende matrix geometries worden aanbevolen voor gebruik met de micro soft audio-stack. De locatie van de geluids bronnen en de weigering van het lucht geluid is verbeterd met een groter aantal microfoons met afhankelijkheden van specifieke toepassingen, gebruikers scenario's en de vorm factor van het apparaat.

| Mics & geometrie | Cirkel vormige matrix | Cirkel vormige matrix | Lineaire matrix | Lineaire matrix |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \# Mics | 7 | 4 | 4 | 2 |
| Geometrie | 6 buitenste, 1 middelste, RADIUS = 42,5 mm, gelijkmatig gespatieerd | 3 buiten, 1 midden, RADIUS = 42,5 mm, gelijkmatig gespatieerd | Lengte = 120 mm, afstand = 40 mm | Afstand = 40 mm |

Microfoon kanalen moeten worden besteld op basis van de nummering die voor elke bovenstaande matrix wordt weer gegeven, verhoogd van 0. Voor de micro soft audio-stack is een extra referentie stroom van audio afspelen vereist om echo annulering uit te voeren.

## <a name="component-selection"></a>Onderdelen selecteren

Microfoon onderdelen moeten worden geselecteerd om een signaal zonder ruis en vervorming nauw keurig te reproduceren.

De aanbevolen eigenschappen bij het selecteren van een microfoon zijn:

| Parameter | Aanbevolen |
| --------- | ----------- |
| SNR | \>= 65 dB (1 kHz signaal 94 dBSPL, A-gewogen lawaai) |
| Amplitude overeenkomst | ± 1 dB @ 1 kHz |
| Fase overeenkomst | ± 2 ° @ 1 kHz |
| Geluids overbelasting punt (AOP) | \>= 120 dBSPL (THD = 10%) |
| Bitrate | Mini maal 24 bits |
| Samplefrequentie | Mini maal 16 kHz\* |
| Antwoord op frequentie | ± 3 dB, 200-8000 Hz zwevend masker\* |
| Betrouwbaarheid | Opslag temperatuur bereik-40 °C tot 70 °C<br />Bereik van de bedrijfs temperatuur-20 °C tot 55 °C |

\*_Hogere sampling frequenties of ' bredere ' prijsbereiken kunnen nodig zijn voor VoIP-toepassingen (hoogwaardige communicatie)_

De selectie van een goed onderdeel moet worden gekoppeld aan een goede integratie van electroacoustic om te voor komen dat de prestaties van de gebruikte onderdelen worden aangetast. Voor unieke use-cases zijn mogelijk ook extra vereisten vereist (bijvoorbeeld: werk temperatuur bereik).

## <a name="microphone-array-integration"></a>Integratie van microfoon matrix

De prestaties van de microfoon matrix wanneer deze zijn geïntegreerd in een apparaat verschillen van de onderdeel specificatie. Het is belang rijk om ervoor te zorgen dat de microfoons na de integratie goed overeenkomen. De prestaties van het apparaat gemeten na een vast winst-of EQ-systeem moeten daarom voldoen aan de volgende aanbevelingen:

| Parameter          | Aanbevolen                                        |
| ------------------ | -------------------------------------------------- |
| SNR                | \> 63 dB (1 kHz sein 94 dBSPL, A-gewogen lawaai) |
| Uitvoer gevoeligheid | -26 dBFS/pa @ 1 kHz (aanbevolen)                  |
| Amplitude overeenkomst | ± 2 dB, 200-8000 Hz                                |
| THD%\*             | ≤ 1%, 200-8000 Hz, 94 dBSPL, vijfde bestelling             |
| Antwoord op frequentie | ± 6 dB, 200-8000 Hz zwevend masker\*\*              |

\*\*_Er is een lage vervormings spreker vereist om THD te meten (bijvoorbeeld Neumann KH120)_

\*\*_Er kunnen ruimere frequentie bereiken nodig zijn voor VoIP-toepassingen (hoogwaardige communicatie)_

## <a name="speaker-integration-recommendations"></a>Aanbevelingen voor de integratie van de spreker

Omdat Echo annulering nood zakelijk is voor apparaten voor spraak herkenning die luid sprekers bevatten, worden extra aanbevelingen gegeven voor selectie en integratie van de spreker.

| Parameter | Aanbevolen |
| --------- | ----------- |
| Overwegingen voor lineariteit | Geen niet-lineaire verwerking na de referentie van de spreker, anders is een hardwarematige loop back-referentie stroom vereist |
| Loop back van de spreker | Verschaft via WASAPI, persoonlijke Api's, aangepaste ALSA-invoeg toepassingen (Linux) of via een firmware kanaal |
| THD% | 3e octave-banden mini maal vijfde order, 70 dBA afspelen @ 0,8 m ≤ 6,3%, 315-500 Hz ≤ 5%, 630-5000 Hz |
| ECHO koppeling op microfoons | \> -10 dB-TCLw met behulp van ITU-T G. 122 bijlage B. 4 methode, genormaliseerd op het niveau van de microfoon<br />TCLw = TCLwmeasured \+ (gemeten niveau doel uitvoer gevoeligheid)<br />TCLw = TCLwmeasured \+ (gemeten niveau-(-26)) |

## <a name="integration-design-architecture"></a>Ontwerp architectuur voor integratie

De volgende richt lijnen voor architectuur zijn nodig bij het integreren van micro telefoons in een apparaat:

| Parameter | Aanbeveling |
| --------- | -------------- |
| Gelijkwaardige microfoon poort | Alle microfoon poorten hebben dezelfde lengte in de matrix |
| Microfoon poort dimensies | Poort grootte Ø 0,8-1,0 mm. Poort lengte/poort diameter \< 2 |
| Mic verzegelen         | Gaskets verzegelen uniform geïmplementeerd in de stack-up. \>70% compressie ratio voor schuim Gaskets aanbevelen |
| Betrouw baarheid van Mic     | Net moet worden gebruikt om te voor komen dat stof en inkomend verkeer (tussen PCB'S voor de onderste, getransporteerde microfoons en verzegelde GASKET/top-dekking) |
| Microfoon isolatie       | Rubber Gaskets en trillingen ontkoppelen via structuur, met name voor het isoleren van trillings paden vanwege geïntegreerde sprekers |
| Sampling-klok      | De audio van het apparaat mag geen jitter-en drop-outs met lage drift hebben |
| Record mogelijkheid   | Het apparaat moet tegelijkertijd onbewerkte individuele kanaal gegevens kunnen vastleggen |
| USB                 | Alle USB-audio-invoer apparaten moeten descriptors instellen volgens de [USB-audio apparaten Rev3 spec](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| Geometrie van microfoon | Stuur Programma's moeten [geometrie-descriptors](/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) op de juiste wijze implementeren |
| Detectie     | Apparaten mogen geen niet-opgemaakte of niet-bewerkte hardware, firmware of op software gebaseerde niet-lineaire, niet-gestuurde algoritmen voor audio verwerking van het apparaat hebben |
| Capture-indeling      | Vastleg indelingen moeten een mini maal sampling frequentie van 16 kHz en een aanbevolen 24-bits diepte gebruiken |

## <a name="electrical-architecture-considerations"></a>Overwegingen voor de elektriciteits architectuur

Waar van toepassing kunnen matrices worden aangesloten op een USB-host (zoals een SoC die de micro soft audio-stack uitvoert) en de interfaces voor spraak Services of andere toepassingen.

Hardwareonderdelen, zoals PDM-naar-TDM-conversie, moeten ervoor zorgen dat het dynamische bereik en de SNR van de microfoons binnen resamplings blijven.

De High-Speed USB-audio klasse 2,0 moet worden ondersteund in alle audio-Sphere om de benodigde band breedte te bieden voor Maxi maal zeven kanalen met een hogere steek proef frequentie en bitdiepte.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over de SDK voor spraak apparaten](speech-devices-sdk.md)