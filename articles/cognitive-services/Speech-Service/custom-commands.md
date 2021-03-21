---
title: 'Aangepaste opdrachten: spraak service'
titleSuffix: Azure Cognitive Services
description: Een overzicht van de functies, mogelijkheden en beperkingen voor aangepaste opdrachten, een oplossing voor het maken van spraak toepassingen.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: e4ae8a09a554afe232091b68888af17dfa096771
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426466"
---
# <a name="what-is-custom-commands"></a>Wat zijn aangepaste opdrachten?

Toepassingen zoals [spraak assistenten](voice-assistants.md) worden naar gebruikers geluisterd en ondernemen een actie als reactie, vaak teruggesp roken. Ze gebruiken [spraak-naar-tekst](speech-to-text.md) om de spraak van de gebruiker te transcriberen en vervolgens actie te ondernemen op het memorandum van natuurlijke taal van de tekst. Deze actie omvat vaak gesp roken uitvoer van de assistent die is gegenereerd met [tekst-naar-spraak](text-to-speech.md). Apparaten maken verbinding met assistenten met het object Speech SDK `DialogServiceConnector` .

Met **aangepaste opdrachten** kunt u eenvoudig geavanceerde spraak opdrachten maken die zijn geoptimaliseerd voor spraak-eerste interactie. Het biedt een uniforme ontwerp-ervaring, een automatisch hosting model en relatief lagere complexiteit, waarmee u zich kunt richten op het bouwen van de beste oplossing voor uw spraak-opdracht scenario's.

Aangepaste opdrachten zijn het meest geschikt voor het volt ooien van taken of voor het Toep assen van opdrachten en besturings elementen, met name voor Internet of Things IoT-apparaten (well-to-match), omgevings-en headless apparaten. Voor beelden hiervan zijn oplossingen voor horeca-, retail-en automobiel industrieën, zodat u de beste in de kamer opgeslagen spraak-ervaringen kunt bouwen voor uw gasten, de inventarisatie van uw winkel en de functionaliteit van het beheer binnen de auto kunt beheren terwijl u onderweg bent.

> [!TIP]
> Bekijk onze voorbeeld demo's op onze landings pagina op [https://speech.microsoft.com/customcommands](https://speech.microsoft.com/customcommands) .

Als u geïnteresseerd bent in het bouwen van complexe conversatie-apps, raden we u aan het bot Framework te proberen met behulp van de [Virtual assistent-oplossing](/azure/bot-service/bot-builder-enterprise-template-overview). U kunt spraak toevoegen aan elke bot-Framework-bot met behulp van direct line speech.

Goede kandidaten voor aangepaste opdrachten hebben een vaste vocabulaire met goed gedefinieerde sets variabelen. Home Automation-taken, zoals het beheren van een thermo staat, zijn bijvoorbeeld ideaal.

   ![Voor beelden van scenario's voor het volt ooien van taken](media/voice-assistants/task-completion-examples.png "voor beelden van taak voltooiing")

## <a name="getting-started-with-custom-commands"></a>Aan de slag met aangepaste opdrachten

Het doel van het maken van aangepaste opdrachten is het verminderen van uw cognitieve werk belasting voor meer informatie over de verschillende technologieën en focussen bij het bouwen van uw stem opdracht. Eerste stap voor het gebruik van aangepaste opdrachten voor <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">het maken van een Azure-spraak resource </a>. U kunt uw aangepaste opdrachten-app in het speech Studio ontwerpen en publiceren, waarna een toepassing op het apparaat kan communiceren met de spraak-SDK.

#### <a name="authoring-flow-for-custom-commands"></a>Ontwerp stroom voor aangepaste opdrachten
   ![Ontwerp stroom voor aangepaste opdrachten](media/voice-assistants/custom-commands-flow.png "De aangepaste opdrachten stroom voor ontwerpen")

Volg onze Snelstartgids om uw eerste aangepaste opdrachten voor app-code in minder dan 10 minuten te laten uitvoeren.

* [Een spraakassistent maken met aangepaste opdrachten](quickstart-custom-commands-application.md)

Wanneer u klaar bent met de Snelstartgids, raadpleegt u onze hand leidingen voor gedetailleerde stappen voor het ontwerpen, ontwikkelen, opsporen, implementeren en integreren van een toepassing met aangepaste opdrachten.

## <a name="building-voice-assistants-with-custom-commands"></a>Spraak assistenten bouwen met aangepaste opdrachten
> [!VIDEO https://www.youtube.com/embed/1zr0umHGFyc]

## <a name="next-steps"></a>Volgende stappen

* [Verkrijg gratis een spraakserviceabonnementssleutel](overview.md#try-the-speech-service-for-free)
* [Bekijk onze opslag plaats op GitHub voor voor beelden.](https://aka.ms/speech/cc-samples)
* [Ga naar de speech Studio om aangepaste opdrachten uit te proberen](https://speech.microsoft.com/customcommands)
* [De Speech SDK ophalen](speech-sdk.md)