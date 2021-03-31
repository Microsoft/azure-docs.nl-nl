---
title: Spraak assistenten op Windows-Veelgestelde vragen
titleSuffix: Azure Cognitive Services
description: Veelgestelde vragen die vaak worden geleverd tijdens de ontwikkeling van Windows Voice agent.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 3cb4dd16592f3af962b32fe3005d2ad4b241d831
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "84457002"
---
# <a name="samples-and-faqs"></a>Voor beelden en veelgestelde vragen

## <a name="the-uwp-voice-assistant-sample"></a>Het voor beeld van de UWP Voice Assistant

Het UWP Voice Assistant-voor beeld is een spraak assistent in Windows die wordt gebruikt om

- het gebruik van de Windows ConversationalAgent-Api's demonstreren
- Aanbevolen procedures voor een spraak agent in Windows weer geven
- een aanpas bare app en herbruikbare onderdelen voor uw MVP-agent toepassing bieden
- laat zien hoe direct line speech, samen met bot-Framework of aangepaste opdrachten, kunnen worden gebruikt in combi natie met de Windows ConversationalAgent-Api's voor een end-to-end spraak agent

De documentatie die wordt geleverd bij de voor beeld-app, leidt door het code traject van spraak activering en agent beheer, van de vereisten van opstarten via de juiste opschoning.

> [!div class="nextstepaction"]
> [Ga naar de GitHub-opslag plaats voor het UWP-voor beeld](https://aka.ms/MVA/sample)

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="how-do-i-contact-microsoft-for-resources-like-limited-access-feature-tokens-and-keyword-model-files"></a>Hoe kan ik contact opnemen met micro soft voor resources zoals de functie tokens voor beperkte toegang en trefwoord bestanden?

Neem contact winvoiceassistants@microsoft.com op om deze resources aan te vragen.

### <a name="my-app-is-showing-in-a-small-window-when-i-activate-it-by-voice-how-can-i-transition-from-the-compact-view-to-a-full-application-window"></a>Mijn app wordt weer gegeven in een klein venster wanneer ik het via spraak Activeer. Hoe kan ik overstappen van de compacte weer gave naar een volledig toepassings venster?

Wanneer uw toepassing voor het eerst wordt geactiveerd door een spraak, wordt deze in een compacte weer gave gestart. Lees de [ontwerp richtlijnen voor de preview-versie van spraak activering](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) voor meer informatie over de verschillende weer gaven en overgangen ertussen voor spraak assistenten in Windows.

Als u de overgang van de compacte weer gave naar de volledige app-weer gave wilt maken, gebruikt u de appView-API `TryEnterViewModeAsync` :

`var appView = ApplicationView.GetForCurrentView();
 await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);`

### <a name="why-are-voice-assistant-features-on-windows-only-enabled-for-uwp-applications"></a>Waarom zijn de functies van Voice Assistant in Windows alleen ingeschakeld voor UWP-toepassingen?

Gezien de privacy-Risico's die zijn gekoppeld aan functies zoals spraak activering, zijn de functies van het UWP-platform nodig om de functies van de Voice Assistant in Windows voldoende veilig te stellen.

### <a name="the-uwp-voice-assistant-sample-uses-direct-line-speech-do-i-have-to-use-direct-line-speech-for-my-voice-assistant-on-windows"></a>In het voor beeld van de UWP Voice Assistant wordt directe lijn spraak gebruikt. Moet ik directe lijn spraak gebruiken voor mijn Voice Assistant op Windows?

De voorbeeld toepassing UWP is ontwikkeld met behulp van direct line speech en de speech Services SDK als demonstratie van het gebruik van een dialoog service met de Windows-functie voor de conversatie agent. U kunt echter elke service gebruiken voor de verificatie van lokale en Cloud trefwoorden, spraak naar tekst conversie, bot dialoog venster en conversie van tekst naar spraak.