---
title: Directe lijn spraak-spraak service
titleSuffix: Azure Cognitive Services
description: Een overzicht van de functies, mogelijkheden en beperkingen voor spraak assistenten met behulp van directe lijn spraak met de speech Software Development Kit (SDK).
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 6eb689c2e93b5a9cac932e428245410ba35e77d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102611790"
---
# <a name="what-is-direct-line-speech"></a>Wat is Direct Line-spraak?

**Direct line speech** is een robuuste, end-to-end oplossing voor het maken van een flexibele, uitbreid bare spraak assistent. Het wordt mogelijk gemaakt door het bot-Framework en het directe-lijn spraak kanaal dat is geoptimaliseerd voor spraak-in-en spraak-interactie met bots.

[Spraak assistenten](voice-assistants.md) worden naar gebruikers geluisterd en nemen een actie in antwoord, vaak gesp roken terug. Ze gebruiken [spraak-naar-tekst](speech-to-text.md) om de spraak van de gebruiker te transcriberen en vervolgens actie te ondernemen op het memorandum van natuurlijke taal van de tekst. Deze actie omvat vaak gesp roken uitvoer van de assistent die is gegenereerd met [tekst-naar-spraak](text-to-speech.md).

Direct line speech biedt de hoogste mate van aanpassing en verfijning voor spraak assistenten. Het is ontworpen voor gespreks scenario's die zijn geopend, natuurlijke of hybriden van de twee met taak voltooiing of opdracht-en controle gebruik. Deze hoge mate van flexibiliteit wordt geleverd met een grotere complexiteit en scenario's die zijn afgestemd op goed gedefinieerde taken met behulp van invoer in natuurlijke taal, is het mogelijk om te denken aan [aangepaste opdrachten](custom-commands.md) voor een gestroomlijnde oplossing.

## <a name="getting-started-with-direct-line-speech"></a>Aan de slag met direct line speech

De eerste stappen voor het maken van een spraak-assistent met behulp van directe lijn spraak zijn het [verkrijgen van een sleutel voor een spraak abonnement](overview.md#try-the-speech-service-for-free), het maken van een nieuwe bot die is gekoppeld aan dat abonnement, en het verbinden van de bot met het directe lijn spraak kanaal.

   ![Conceptueel diagram van de service stroom van de directe lijn spraak indeling](media/voice-assistants/overview-directlinespeech.png "De stroom van het spraak kanaal")

Voor een volledige stapsgewijze hand leiding voor het maken van een eenvoudige spraak assistent met behulp van directe lijn spraak raadpleegt u [de zelf studie voor spraak herkenning met de spraak-SDK en het direct lijn spraak kanaal](tutorial-voice-enable-your-bot-speech-sdk.md).

We bieden ook Quick starts die zijn ontworpen om u te laten werken met code en snel de Api's te leren. Deze tabel bevat een lijst met Quick starts voor de Voice Assistant, geordend op taal en platform.

| Snelstart | Platform | API-verwijzing |
|------------|----------|---------------|
| C#, UWP | Windows | [Bladeren](/dotnet/api/microsoft.cognitiveservices.speech) |
| Java | Windows, macOS, Linux | [Bladeren](/java/api/com.microsoft.cognitiveservices.speech) |
| Java | Android | [Bladeren](/java/api/com.microsoft.cognitiveservices.speech) |

## <a name="sample-code"></a>Voorbeeldcode

Voorbeeld code voor het maken van een spraak-assistent is beschikbaar op GitHub. Deze voor beelden beslaan de client toepassing voor het maken van verbinding met uw helper in verschillende populaire programmeer talen.

* [Voorbeelden van spraakassistenten (SDK)](https://aka.ms/csspeech/samples/#voice-assistants-quickstarts)
* [Zelf studie: spraak inschakelen voor uw assistent met de spraak-SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Aanpassing

Spraak assistenten die zijn gemaakt met Speech Service kunnen gebruikmaken van het volledige aanbod aan aanpassings opties voor [spraak naar tekst](speech-to-text.md), [tekst naar spraak](text-to-speech.md)en [aangepaste trefwoord selectie](./custom-keyword-basics.md).

> [!NOTE]
> Aanpassings opties variëren per taal/land instelling (Zie [ondersteunde talen](./language-support.md)).

Direct line speech en de bijbehorende functionaliteit voor spraak assistenten zijn een ideale aanvulling op de [Virtual assistent-oplossing en de ondernemings sjabloon](/azure/bot-service/bot-builder-enterprise-template-overview). Hoewel directe lijn spraak kan worden gebruikt met een compatibele bot, bieden deze resources een herbruikbare basis lijn voor gesp roken ervaringen van hoge kwaliteit, evenals algemene ondersteunende vaardig heden en modellen om snel aan de slag te gaan.

## <a name="reference-docs"></a>Naslagdocumentatie

* [Speech-SDK](./speech-sdk.md)
* [Azure Bot Service](/azure/bot-service/)

## <a name="next-steps"></a>Volgende stappen

* [Verkrijg gratis een spraakserviceabonnementssleutel](overview.md#try-the-speech-service-for-free)
* [De Speech SDK ophalen](speech-sdk.md)
* [Een basisbot maken en implementeren](/azure/bot-service/bot-builder-tutorial-basic-deploy)
* [De Virtual assistent-oplossing en ondernemings sjabloon ophalen](https://github.com/Microsoft/AI)
