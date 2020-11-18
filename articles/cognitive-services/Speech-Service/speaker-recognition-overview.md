---
title: 'Overzicht van Speaker Recognition: spraak service'
titleSuffix: Azure Cognitive Services
description: Speaker Recognition biedt algoritmen waarmee luid sprekers worden geverifieerd en geïdentificeerd aan de hand van hun unieke spraak kenmerken met behulp van Voice biometrie. Speaker Recognition wordt gebruikt voor het beantwoorden van de vraag 'Wie spreekt er?'. Dit artikel bevat een overzicht van de voor delen en mogelijkheden van de Speaker Recognition service.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: sprekerherkenning, biometrische spraak
ms.openlocfilehash: 5001ac166b37f3a3852bc14261a5b5d199d778cd
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844559"
---
# <a name="what-is-speaker-recognition-preview"></a>Wat is Speaker Recognition (preview)?

De Speaker Recognition-service biedt algoritmen waarmee luid sprekers worden geverifieerd en geïdentificeerd aan de hand van hun unieke spraak kenmerken met behulp van Voice biometrie. Speaker Recognition wordt gebruikt voor het beantwoorden van de vraag 'Wie spreekt er?'. U geeft audio training gegevens voor één spreker op, waarmee een inschrijvings profiel wordt gemaakt op basis van de unieke kenmerken van de stem van de spreker. U kunt vervolgens Audio-Voice-voor beelden op dit profiel controleren om te controleren of de spreker dezelfde persoon is (verificatie van de spreker), of audio spraak voorbeelden op een *groep* Inge schreven luidspreker profielen controleren om te zien of deze overeenkomen met een profiel in de groep (speaker-id). Met de Diarization van de [spreker](batch-transcription.md#speaker-separation-diarization) worden daarentegen segmenten van audio door de spreker gegroepeerd in een batch-bewerking.

## <a name="speaker-verification"></a>Sprekercontrole

Speaker Verification stroomlijnt het proces van het controleren van de identiteit van een Inge schreven spreker met wachtwoordzins of vrije-vorm spraak invoer. De IT-afdeling kan worden gebruikt om personen te verifiëren voor veilige, frictieloze klant afspraken in een breed scala aan oplossingen, van de verificatie van de klant identiteit in Call Centers om toegang te krijgen tot een beperktere faciliteit.

### <a name="how-does-speaker-verification-work"></a>Hoe werkt Speaker Verification?

:::image type="content" source="media/speaker-recognition/speaker-rec.png" alt-text="Speaker Verification stroom diagram.":::

De luidspreker verificatie kan tekst afhankelijke of tekst onafhankelijk zijn. **Tekst afhankelijke** verificatie betekent dat de sprekers dezelfde wachtwoordzin moeten kiezen voor gebruik tijdens de inschrijvings-en verificatie fases. **Tekst onafhankelijke** verificatie betekent dat sprekers in de alledaagse taal in de registratie-en verificatie zinnen kunnen spreken.

Voor verificatie met een **tekst afhankelijke** wordt de stem van de spreker Inge schreven door een wachtwoordzin te zeggen uit een reeks vooraf gedefinieerde woord groepen. Spraak functies worden geëxtraheerd uit de audio-opname om een unieke spraak handtekening te vormen, terwijl de gekozen wachtwoordzin ook wordt herkend. De spraak handtekening en de wachtwoordzin worden samen gebruikt om de spreker te controleren. 

**Tekst onafhankelijke** verificatie heeft geen beperkingen ten aanzien van wat de spreker tijdens de registratie of in het audio voorbeeld moet worden geverifieerd, omdat hiermee alleen spraak functies worden uitgepakt voor het vergelijken van de score. 

De Api's zijn niet bedoeld om te bepalen of de audio afkomstig is van een live persoon of een imitatie/opname van een Inge schreven spreker. 

## <a name="speaker-identification"></a>Sprekeridentificatie

Speaker Identification wordt gebruikt om de identiteit van een onbekende spreker binnen een groep Inge schreven luid sprekers te bepalen. Met Speaker Identification kunt u een spraak op de afzonderlijke sprekers maken, en de waarde van scenario's met meerdere sprekers ontgrendelen, zoals:

* Ondersteunings oplossingen voor externe productiviteit van de vergadering 
* Personalisatie van apparaten op meerdere gebruikers bouwen

### <a name="how-does-speaker-identification-work"></a>Hoe werkt Speaker Identification?

De inschrijving voor Speaker-identificatie is **onafhankelijk van tekst**, wat betekent dat er geen beperkingen zijn voor wat de spreker in de audio vertelt. Net als bij Speaker Verification, in de inschrijvings fase wordt de stem van de spreker vastgelegd en Voice-functies worden uitgepakt om een unieke spraak handtekening te vormen. In de id-fase wordt het voor beeld van de invoer in vergelijking met een opgegeven lijst met geregistreerde stemmen (Maxi maal 50 in elke aanvraag).

## <a name="data-security-and-privacy"></a>Gegevens beveiliging en privacy

Inschrijvings gegevens voor de spreker worden opgeslagen in een beveiligd systeem, met inbegrip van de spraak audio voor inschrijving en de functies van de spraak handtekening. De spraak audio voor inschrijving wordt alleen gebruikt wanneer het algoritme wordt geüpgraded en de functies moeten opnieuw worden geëxtraheerd. De service behoudt de spraak opname of de uitgepakte spraak functies die tijdens de herkennings fase naar de service worden verzonden. 

U bepaalt hoe lang de gegevens bewaard moeten blijven. U kunt inschrijvings gegevens voor afzonderlijke sprekers maken, bijwerken en verwijderen via API-aanroepen. Wanneer het abonnement wordt verwijderd, worden alle gegevens over de registratie van de sprekernotities die zijn gekoppeld aan het abonnement, ook verwijderd. 

Net als bij alle Cognitive Services resources moeten ontwikkel aars die gebruikmaken van de Speaker Recognition-service op de hoogte zijn van het beleid van micro soft op klant gegevens. Zorg ervoor dat u de juiste machtigingen hebt ontvangen van de gebruikers voor Speaker Recognition. Zie de [pagina Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)   in het micro soft vertrouwens centrum voor meer informatie. 

## <a name="common-questions-and-solutions"></a>Veelgestelde vragen en oplossingen

| Vraag | Oplossing |
|---------|----------|
| Welke scenario's kunnen Speaker Recognition worden gebruikt voor? | Call Center klantverificatie, op spraak gebaseerde patiënten check-in, Meeting Transcriptie, personalisatie van apparaten op meerdere gebruikers|
| Wat is het verschil tussen identificatie en verificatie? | Identificatie is het proces van het detecteren van welk lid van een groep luid sprekers spreekt. Verificatie is het bevestigen dat een spreker overeenkomt met een bekende of **Inge schreven** stem.|
| Wat is het verschil tussen tekst afhankelijke en tekst onafhankelijke verificatie? | Voor een tekst afhankelijke verificatie is een specifieke wachtwoordzin vereist voor zowel de inschrijving als de herkenning. Tekst onafhankelijke verificatie vereist een langere stem voorbeeld voor de inschrijving, maar alles kan worden gesp roken, inclusief tijdens de herkenning.|
| Welke talen worden ondersteund? | Engels, Frans, Spaans, Chinees, Duits, Italiaans, Japans en Portugees |
| Welke Azure-regio's worden ondersteund? | Speaker Recognition is een preview-service en is momenteel alleen beschikbaar in de regio vs-West.|
| Welke audio-indelingen worden ondersteund? | Mono 16-bits, 16kHz PCM-encoded WAV |
| **Accepteren** en **weigeren** van antwoorden is niet nauw keurig, hoe kunt u de drempel waarde afstemmen? | Omdat de optimale drempel sterk varieert met scenario's, bepaalt de API of ' geaccepteerd ' of ' afwijzen ' op basis van een standaard drempelwaarde van 0,5. Ervaren gebruikers worden geadviseerd om de standaard beslissing te negeren en het resultaat te verfijnen op basis van uw eigen scenario. |
| Kunt u één spreker meerdere keren inschrijven? | Ja, voor een tekst afhankelijke verificatie kunt u een spreker van Maxi maal 50 keer inschrijven. Voor tekst onafhankelijke verificatie of Speaker-identificatie kunt u inschrijven met Maxi maal 300 seconden aan audio. |
| Welke gegevens worden opgeslagen in azure? | Inschrijvings audio wordt opgeslagen in de service totdat het spraak profiel wordt [verwijderd](speaker-recognition-basics.md#deleting-voice-profile-enrollments). Audio-voor beelden voor herkenning blijven niet behouden of worden opgeslagen. |

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> * Voltooi het Speaker Recognition [basis-artikel](speaker-recognition-basics.md) voor een uitvoering van veelvoorkomende ontwerp patronen die u in uw toepassingen kunt gebruiken.
> * Raadpleeg de [video zelf studie](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/) voor tekst onafhankelijke luidspreker verificatie.
