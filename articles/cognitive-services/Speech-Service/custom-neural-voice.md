---
title: Aangepast Neural-spraak overzicht-spraak service
titleSuffix: Azure Cognitive Services
description: Aangepaste Neural Voice is een functie voor tekst naar spraak waarmee u een speciaal aangepaste synthetische stem kunt maken voor uw toepassingen door uw eigen audio gegevens als voor beeld op te geven.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/01/2020
ms.author: trbye
ms.openlocfilehash: 9e230ff4e84eba7b61846540fef2915765fa4f77
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509670"
---
# <a name="what-is-custom-neural-voice"></a>Wat is aangepaste neurale Voice?

Aangepaste Neural Voice is een TTS-functie ( [Text-to-speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) ) waarmee u een aangepaste, gepersonaliseerde stem kunt maken voor uw toepassingen door uw eigen audio gegevens als voor beeld op te geven. Tekst-naar-spraak werkt door tekst naar synthetische spraak te converteren met behulp van een machine learning model dat klinkt als een gekozen stem. Met de [rest API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)kunt u uw apps laten praten met [vooraf gemaakte stemmen](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) of uw eigen [aangepaste spraak](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice-prepare-data) modellen die zijn ontwikkeld met behulp van de aangepaste Neural Voice-functie. Aangepaste Neural Voice is gebaseerd op Neural TTS-technologie die een natuurlijke geluids spraak maakt die vaak niet te onderscheiden is in vergelijking met een menselijke stem.
De realistische en natuurlijke geluids spraak van aangepaste Neural-stem kunnen merken en Personify-machines vertegenwoordigen en gebruikers in staat stellen om op een natuurlijke manier te communiceren met toepassingen.

> [!NOTE]
> De aangepaste Neural Voice-functie vereist registratie en de toegang tot deze optie is beperkt op basis van de geschiktheids-en gebruiks criteria van micro soft. Klanten die deze functie willen gebruiken, zijn verplicht hun use-cases te registreren via het [formulier innemen](https://aka.ms/customneural).

## <a name="the-basics-of-custom-neural-voice"></a>De basis beginselen van aangepaste Neural Voice

De onderliggende Neural TTS-technologie die wordt gebruikt voor aangepaste Neural-stem bestaat uit drie belang rijke onderdelen: tekst analyse, Neural akoestische model en Neural vocoder. Voor het genereren van natuurlijke synthetische spraak vanuit tekst, wordt tekst eerst ingevoerd in tekst analyse, die uitvoer in de vorm van foneem-reeks bevat. Een foneem is een eenvoudige geluids eenheid die het ene woord van een andere in een bepaalde taal onderscheidt. Een reeks fonemen definieert de uitspraak van de woorden die in de tekst zijn opgenomen. 

Vervolgens wordt de foneem-reeks in het Neural akoestische model geplaatst om geluids functies te voors pellen waarmee spraak signalen worden gedefinieerd, zoals de timbre, de spreek stijl, snelheid, intonations en stress patronen. Ten slotte zet de Neural Vocoder de akoestische functies om in hoorbare golven, zodat synthetische spraak wordt gegenereerd.

![Introductie installatie kopie voor aangepaste Neural-stem.](./media/custom-voice/cnv-intro.png)

Neural TTS Voice-modellen worden getraind met behulp van diepe Neural-netwerken op basis van de opname voorbeelden van menselijke stemmen. In deze [blog](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-extends-support-to-15-more-languages-with/ba-p/1505911)beschrijven we hoe Neural TTS werkt met geavanceerde Neural-spraakherkennings modellen. De blog legt ook uit hoe een universeel basis model kan worden aangepast met minder dan 2 uur aan spraak gegevens (of minder dan 2.000 vastgelegde uitingen) van een doel spreker, en leren om te spreken in de stem van de doel spreker. Zie het [blog bericht](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860)voor meer informatie over hoe een Neural-Vocoder wordt getraind.

Met de aanpassings mogelijkheid van aangepaste Neural Voice kunt u de Neural TTS-engine aanpassen zodat deze beter aansluit bij uw gebruikers scenario's. Als u een aangepaste Neural-stem wilt maken, gebruikt u [Speech Studio](https://speech.microsoft.com/customvoice) om de opgenomen audio en bijbehorende scripts te uploaden, het model te trainen en de stem op een aangepast eind punt te implementeren. Afhankelijk van de use-aanvraag kan aangepaste Neural-stem worden gebruikt om tekst in realtime te converteren (bijvoorbeeld gebruikt in een Smart virtuele assistent) of om audio-inhoud offline te maken (bijvoorbeeld gebruikt in audio boek of instructies in e-learning toepassingen) met de tekst invoer van de gebruiker. Dit wordt beschikbaar gesteld via de [rest API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech), de [spraak-SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started-text-to-speech?tabs=script%2Cwindowsinstall&pivots=programming-language-csharp)of een [webportal](https://speech.microsoft.com/audiocontentcreation).

## <a name="terms-and-definitions"></a>Termen en definities

| **Termijn**      | **Definition**                                                                                                                                                                                                                                                                                                                                                                                       |
|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Spraak model   | Een model voor tekst naar spraak dat de unieke kenmerken van de vocaal van een doel spreker kan nabootsen. Een *spraak model* wordt ook wel een *spraak lettertype* of *synthetische stem* genoemd. Een spraak model is een set para meters in een binaire indeling die niet door de mens kan worden gelezen en die geen audio-opnames bevat. Er kan geen reverse-engineering worden toegepast om de audio van een menselijke stem af te leiden of te maken. |
| Stem talen  | Personen of doel luidsprekers waarvan de stemmen worden vastgelegd en worden gebruikt voor het maken van spraak modellen die zijn bedoeld om te klinken zoals de stem van het stem talend is.                                                                                                                                                                                                                                                   |
| Standaard TTS  | De standaard-of ' traditionele ' methode van TTS die gesp roken taal opsplitst in fonetische fragmenten zodat ze kunnen worden Remixed en vergeleken met klassieke programmeer-of statistische methoden.                                                                                                                                                                                                    |
| Neural TTS    | Neural TTS combineert spraak met diepe Neural-netwerken die "geleerd" zijn op de manier waarop fonetiek in natuurlijke menselijke spraak worden gecombineerd, in plaats van procedurele programmering of statistische methoden te gebruiken. Naast de opnamen van een doel spraak-talen gebruikt Neural TTS een bron bibliotheek/base-model dat is gemaakt met spraak opnamen van verschillende sprekers.          |
| Trainingsgegevens | Een aangepaste Neural voor spraak training met daarin de audio-opnames van het stem talen, en de bijbehorende tekst transcripties.                                                                                                                                                                                                                                                               |
| Persona       | Een persoon die u wilt dat deze stem wordt beschreven. Met een goede persona-ontwerp wordt het maken van alle spraak op de hoogte gesteld, of het nu gaat om een beschikbaar spraak model dat al is gemaakt, of helemaal vanaf het begin door een nieuwe stem talen te casten en te registreren.                                                                                                |
| Script        | Een script is een tekst bestand dat de uitingen bevat die door uw stem talen moet worden gesp roken. (De term '*uitingen*' omvat zowel volledige zinnen als korte zinnen.)                                                                                                                                                                                                                               |

## <a name="responsible-use-of-ai"></a>Verantwoordelijk gebruik van AI

Zie de [Opmerking over transparantie](https://docs.microsoft.com/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice)als u wilt weten hoe u aangepaste Neural-Voice-degelijken kunt gebruiken. De transparantie notities van micro soft zijn bedoeld om u te helpen begrijpen hoe onze AI-technologie werkt, de keuzes van systeem eigen aren kunnen de prestaties en het gedrag van het systeem beïnvloeden en het belang van het hele systeem, met inbegrip van de technologie, de personen en de omgeving.

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met Custom Voice](how-to-custom-voice.md)
* [Een aangepast spraak eindpunt maken en gebruiken](how-to-custom-voice-create-voice.md)