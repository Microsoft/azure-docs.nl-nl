---
title: Spraak assistenten-spraak service
titleSuffix: Azure Cognitive Services
description: Een overzicht van de functies, mogelijkheden en beperkingen voor spraak assistenten met behulp van de speech Software Development Kit (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: aa885b1dcff59ec1b89d9a0bcf4ac5578cb5e8ea
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024905"
---
# <a name="what-is-a-voice-assistant"></a>Wat is een spraakassistent?

Met spraak assistenten die gebruikmaken van de speech-service kunnen ontwikkel aars natuurlijke, menselijke-achtige gespreks interfaces maken voor hun toepassingen en ervaringen.

De Voice Assistant-service biedt een snelle, betrouw bare interactie tussen een apparaat en een assistent-implementatie waarbij gebruik wordt gemaakt van (1) [directe-regel spraak](direct-line-speech.md) (via Azure bot service) voor het toevoegen van spraak mogelijkheden aan uw bots, of, (2) aangepaste opdrachten voor scenario's met spraak opdrachten.

## <a name="choosing-an-assistant-solution"></a>Een assistent-oplossing kiezen

De eerste stap bij het maken van een spraak assistent is bepalen wat het moet doen. De speech-service biedt meerdere, complementaire oplossingen voor het bouwen van uw assistent-interacties. U kunt spraak-en uitspraak mogelijkheden toevoegen aan uw flexibele en veelzijdige bot die is gebouwd met behulp van Azure Bot Service met het [directe lijn spraak](direct-line-speech.md) kanaal of gebruikmaken van de eenvoud van het ontwerpen van een app met [aangepaste opdrachten](custom-commands.md) voor eenvoudige spraak opdrachten.

| Als je wilt... | Bekijk vervolgens... | Bijvoorbeeld... |
|-------------------|------------------|----------------|
|Open-end-conversatie met robuuste integratie van vaardig heden en volledige implementatie beheer | Azure Bot Service bot met [direct line speech](direct-line-speech.md) Channel | <ul><li>"Ik moet naar Seattle gaan"</li><li>"Welk soort pizza kan ik best Ellen?"</li></ul>
|Spraak opdrachten of eenvoudige taak gerichte discussies met vereenvoudigd ontwerpen en hosten | [Aangepaste opdrachten](custom-commands.md) | <ul><li>"Het overhead licht inschakelen"</li><li>"Maak IT 5 graden warmer"</li><li>Andere [beschik bare](https://speech.microsoft.com/customcommands) voor beelden</li></ul>

U wordt aangeraden [regel spraak direct](direct-line-speech.md) als de beste standaard keuze te gebruiken als u nog niet zeker weet wat uw assistent moet verwerken. Het biedt integratie met een uitgebreide set hulpprogram ma's en ontwerp hulpmiddelen, zoals de [Virtual assistent-oplossing en ondernemings sjabloon](/azure/bot-service/bot-builder-enterprise-template-overview) en de [QnA Maker-service](../qnamaker/overview/overview.md) voor het bouwen van algemene patronen en het gebruik van uw bestaande kennis bronnen.

Met [aangepaste opdrachten](custom-commands.md) kunt u eenvoudig geavanceerde spraak opdrachten maken die zijn geoptimaliseerd voor spraak-eerste interactie. Het biedt een uniforme ontwerp-ervaring, een automatisch hosting model en relatief lagere complexiteit, waarmee u zich kunt richten op het bouwen van de beste oplossing voor uw spraak-opdracht scenario's.

   ![Vergelijking van assistent-oplossingen](media/voice-assistants/assistant-solution-comparison.png "Vergelijking van assistent-oplossingen")


## <a name="reference-architecture-for-building-a-voice-assistant-using-the-speech-sdk"></a>Referentie architectuur voor het bouwen van een spraak assistent met behulp van de Speech SDK

   ![Conceptueel diagram van de service stroom van de Orchestrator-indeling voor spraak assistenten](media/voice-assistants/overview.png "De stroom van de Voice Assistant")

## <a name="core-features"></a>Kern functies

Ongeacht of u [direct line speech](direct-line-speech.md) of [aangepaste opdrachten](custom-commands.md) kiest om uw assistent-interacties te maken, kunt u een uitgebreide set aanpassings functies gebruiken om uw assistent aan uw merk, product en persoonlijkheid aan te passen.

| Categorie | Functies |
|----------|----------|
|[Aangepast tref woord](./custom-keyword-basics.md) | Gebruikers kunnen gesp rekken met assistenten starten met een aangepast tref woord zoals ' Hey Contoso '. Dit is een app met een aangepaste trefwoord engine in de Speech SDK, die kan worden geconfigureerd met een aangepast tref woord [dat u hier kunt genereren](./custom-keyword-basics.md). Spraak assistenten kunnen verificatie op service zijde gebruiken om de nauw keurigheid van de activering van het tref woord te verbeteren (alleen voor het apparaat).
|[Spraak naar tekst](speech-to-text.md) | Via spraak [-naar-tekst](speech-to-text.md) van de spraak service kunt u in spraak assistenten real-time audio omzetten in herkende tekst. Deze tekst is beschikbaar, omdat deze is getranscribeerd, naar de implementatie van uw assistent en uw client toepassing.
|[Tekst-naar-spraak](text-to-speech.md) | Tekstuele reacties van uw assistent worden gesynthesizerd met behulp van [tekst naar spraak](text-to-speech.md) vanuit de spraak service. Deze synthese wordt vervolgens beschikbaar gesteld voor uw client toepassing als een audio stroom. Micro soft biedt de mogelijkheid om uw eigen aangepaste Neural TTS-stem van hoge kwaliteit op te bouwen die een stem op uw merk levert. [Neem contact met ons](mailto:mstts@microsoft.com)op voor meer informatie.

## <a name="getting-started-with-voice-assistants"></a>Aan de slag met spraak assistenten

We bieden Quick starts die zijn ontworpen om code in minder dan 10 minuten uit te voeren. Deze tabel bevat een lijst met Quick starts voor de Voice Assistant, geordend op taal.

* [Snelstartgids: een aangepaste spraak-assistent maken met behulp van directe lijn spraak](quickstarts/voice-assistants.md)
* [Snelstartgids: een app voor spraak opdrachten bouwen met aangepaste opdrachten](quickstart-custom-commands-application.md)

## <a name="sample-code-and-tutorials"></a>Voorbeeld code en zelf studies

Voorbeeld code voor het maken van een spraak-assistent is beschikbaar op GitHub. Deze voor beelden beslaan de client toepassing voor het maken van verbinding met uw helper in verschillende populaire programmeer talen.

* [Voor beelden van Voice assistants op GitHub](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)
* [Zelf studie: spraak inschakelen voor uw assistent met behulp van Azure Bot Service met de C# Speech SDK](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Zelf studie: een toepassing voor aangepaste opdrachten maken met eenvoudige spraak opdrachten](how-to-custom-commands-create-application-with-simple-commands.md)

## <a name="customization"></a>Aanpassing

Spraak assistenten die zijn gemaakt met Azure speech Services kunnen gebruikmaken van het volledige aanbod aan aanpassings opties.

* [Aangepaste spraak](how-to-custom-speech.md)
* [Aangepaste stem](how-to-custom-voice.md)
* [Aangepast trefwoord](custom-keyword-overview.md)

> [!NOTE]
> Aanpassings opties variëren per taal/land instelling (Zie [ondersteunde talen](language-support.md)).

## <a name="next-steps"></a>Volgende stappen

* [Verkrijg gratis een spraakserviceabonnementssleutel](overview.md#try-the-speech-service-for-free)
* [Meer informatie over aangepaste opdrachten](custom-commands.md)
* [Meer informatie over direct line speech](direct-line-speech.md)
* [De Speech SDK ophalen](speech-sdk.md)