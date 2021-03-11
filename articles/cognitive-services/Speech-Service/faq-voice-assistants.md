---
title: Veelgestelde vragen over spraak assistenten
titleSuffix: Azure Cognitive Services
description: Krijg antwoorden op de meest populaire vragen over spraak assistenten met aangepaste opdrachten of het directe lijn spraak kanaal.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 511eb12df511fd037fc0b5bec701c0cc5c29bad2
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102617774"
---
# <a name="voice-assistants-frequently-asked-questions"></a>Veelgestelde vragen over spraak assistenten

Als u geen antwoorden op uw vragen in dit document kunt vinden, Bekijk dan [Andere ondersteunings opties](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext).

## <a name="general"></a>Algemeen

**V: wat is een spraak assistent?**

**A:** Net als Cortana is een spraak assistent een oplossing die luistert naar de gesp roken uitingen van een gebruiker, analyseert de inhoud van deze uitingen voor betekenis, voert een of meer acties uit als reactie op het doel van de utterance, en geeft vervolgens een reactie op de gebruiker die vaak een gesp roken onderdeel bevat. Het is een ' spraak-in-en uitspraak-ervaring voor interactie met een systeem. auteurs van de Voice Assistant maken een toepassing op het apparaat met behulp `DialogServiceConnector` van de in de Speech SDK om te communiceren met een assistent die is gemaakt met [aangepaste opdrachten](custom-commands.md) of het [directe lijn spraak](direct-line-speech.md) kanaal van het bot-Framework. Deze assistenten kunnen aangepaste tref woorden, aangepaste spraak en aangepaste spraak gebruiken om een ervaring te bieden die is afgestemd op uw merk of product.

**V: moet ik aangepaste opdrachten of direct line speech gebruiken? Wat is het verschil?**

**A:** [aangepaste opdrachten](custom-commands.md) zijn een set hulpprogram ma's waarmee u eenvoudig een assistent kunt maken en hosten die geschikt is voor scenario's voor het volt ooien van taken. [Direct line speech](direct-line-speech.md) biedt geavanceerde, geavanceerdere mogelijkheden waarmee robuuste gespreks scenario's kunnen worden ingeschakeld. Zie de [vergelijking van assistent-oplossingen](voice-assistants.md#choosing-an-assistant-solution) voor meer informatie.

**V: Hoe kan ik aan de slag?**

**A:** De beste manier om te beginnen met het maken van een aangepaste opdrachten (preview)-toepassing of basis-bot-raam werk.

- [Een aangepaste opdrachten (preview)-toepassing maken](./quickstart-custom-commands-application.md)
- [Een eenvoudige bot-Framework-bot maken](/azure/bot-service/bot-builder-tutorial-basic-deploy)
- [Een bot aansluiten op het directe lijn spraak kanaal](/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Foutopsporing

**V: waar is mijn kanaal geheim?**

**A:** Als u de preview-versie van direct line speech hebt gebruikt of als u gerelateerde documentatie leest, wordt u mogelijk verwacht een geheime sleutel te vinden op de registratie pagina van het directe lijn spraak kanaal. Voor de methode v 1.7 `DialogServiceConfig` Factory `FromBotSecret` in de Speech SDK wordt deze waarde ook verwacht.

De nieuwste versie van direct line speech vereenvoudigt het proces van het maken van contact met uw bot vanaf een apparaat. Op de pagina kanaal registratie wordt de vervolg keuzelijst aan de bovenkant gekoppeld aan de registratie van uw directe lijn spraak kanaal met een spraak bron. Na de koppeling bevat de v 1.8 Speech SDK een `BotFrameworkConfig::FromSubscription` fabrieks methode waarmee een `DialogServiceConnector` verbinding wordt gemaakt met de bot die u hebt gekoppeld aan uw abonnement.

Als u nog steeds uw client toepassing van v 1.7 naar v 1.8 migreert, `DialogServiceConfig::FromBotSecret` kan het blijven werken met een niet-lege, niet-null-waarde voor de geheime para meter van het kanaal, bijvoorbeeld het vorige geheime geheim dat u hebt gebruikt. Het wordt gewoon genegeerd wanneer u een spraak abonnement gebruikt dat is gekoppeld aan een nieuwe kanaal registratie. Houd er rekening mee dat de waarde _moet_ niet-null en niet-leeg zijn, aangezien deze worden gecontroleerd op het apparaat voordat de koppeling aan de service zijde relevant is.

Raadpleeg de [sectie zelf studie](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel) voor informatie over kanaal registratie voor een gedetailleerde hand leiding.

**V: er wordt een 401-fout weer geven bij het maken van verbinding en niets werkt. Ik weet dat mijn spraak abonnement sleutel geldig is. Wat gebeurt er?**

**A:** Wanneer u uw abonnement op de Azure Portal beheert, moet u ervoor zorgen dat u de **spraak** resource (micro soft. CognitiveServicesSpeechServices, Speech) gebruikt en _niet_ de **Cognitive Services** resource (micro soft. CognitiveServicesAllInOne, "alle Cognitive Services"). Controleer ook de [ondersteuning voor spraak-service regio's voor spraak assistenten](regions.md#voice-assistants).

![juiste abonnement voor direct line speech](media/voice-assistants/faq-supported-subscription.png "voor beeld van een compatibel spraak abonnement")

**V: Ik vind herkennings tekst terug van mijn `DialogServiceConnector` , maar ik zie een ' 1011 '-fout en niets van mijn bot. Waarom?**

**A:** Deze fout wijst op een communicatie probleem tussen uw assistent en de Voice Assistant-service.

- Voor aangepaste opdrachten moet u ervoor zorgen dat uw toepassing voor aangepaste opdrachten wordt gepubliceerd
- Zorg ervoor dat u [uw bot op het directe lijn spraak kanaal hebt aangesloten](/azure/bot-service/bot-service-channel-connect-directlinespeech), het [Streaming-Protocol ondersteuning](/azure/bot-service/directline-speech-bot) voor uw bot hebt toegevoegd (met de gerelateerde ondersteuning voor websockets) en controleer vervolgens of uw bot reageert op inkomende aanvragen van het kanaal.

**V: deze code werkt nog steeds niet en/of ik krijg een andere fout bij het gebruik van een `DialogServiceConnector` . Wat moet ik doen?**

**A:** Logboek registratie op basis van bestanden biedt aanzienlijk meer details en kan helpen bij het versnellen van ondersteunings aanvragen. Zie [logboek registratie gebruiken](how-to-use-logging.md)om deze functie in te scha kelen.

## <a name="next-steps"></a>Volgende stappen

- [Problemen oplossen](troubleshooting.md)
- [Releaseopmerkingen](releasenotes.md)