---
title: 'Spraak assistenten op Windows: aan de slag'
titleSuffix: Azure Cognitive Services
description: De stappen om te beginnen met het ontwikkelen van een Windows Voice-agent, inclusief een verwijzing naar de Snelstartgids van de voorbeeld code.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: b50b98095cdfe0e6ec19c89b57887ebc4a0f6317
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713045"
---
# <a name="getting-started-with-voice-assistants-on-windows"></a>Aan de slag met spraak assistenten in Windows

In deze hand leiding worden de stappen beschreven voor het ontwikkelen van een telefoon assistent in Windows.

## <a name="set-up-your-development-environment"></a>De ontwikkelomgeving instellen

Als u een spraak assistent voor Windows wilt gaan ontwikkelen, moet u ervoor zorgen dat u de juiste ontwikkel omgeving hebt.

- **Visual Studio:** U moet [micro soft Visual Studio 2017](https://visualstudio.microsoft.com/), de Community Edition of hoger installeren
- **Windows-versie**: een PC met een Windows Insider Fast ring-build van Windows en de Windows Insider-versie van de Windows SDK. Deze voorbeeld code wordt gecontroleerd als werken op Windows Insider release-build 19025.vb_release_analog .191112-1600 met Windows SDK 19018. Alle builds of SDK boven de opgegeven versies moeten compatibel zijn.
- **UWP Development tools**: de universeel Windows-platform development-werk belasting in Visual Studio. Zie de pagina UWP [ophalen instellen](/windows/uwp/get-started/get-set-up) om uw machine gereed te maken voor het ontwikkelen van UWP-toepassingen.
- **Een werkende microfoon en audio-uitvoer**

## <a name="obtain-resources-from-microsoft"></a>Bronnen van micro soft ophalen

Voor sommige resources die nodig zijn voor een volledig aangepaste spraak agent in Windows, zijn resources van micro soft vereist. Het [UWP Voice Assistant](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) -voor beeld bevat een aantal voor beelden van deze bronnen voor de eerste ontwikkeling en tests, waardoor deze sectie niet nodig is voor de eerste ontwikkeling.

- **Trefwoord model:** Voor spraak activering is een trefwoord model van micro soft vereist in de vorm van een bin-bestand. Het. bin-bestand in het voor beeld van de UWP Voice Assistant is getraind op het tref woord *Contoso*.
- **Token voor beperkte toegang:** Omdat de ConversationalAgent-Api's toegang bieden tot de microfoon audio, worden ze beschermd onder beperkingen voor beperkte toegang. Als u een beperkte toegangs functie wilt gebruiken, moet u een token met beperkte toegang verkrijgen dat is verbonden met de pakket-id van uw toepassing van micro soft.

## <a name="establish-a-dialog-service"></a>Een dialoog service maken

Voor een volledige spraak assistent-ervaring heeft de toepassing een dialoog service nodig die

- Een tref woord detecteren in een bepaald audio bestand
- Gebruikers invoer belui Steren en converteren naar tekst
- De tekst voor een bot opgeven
- Het antwoord van de tekst van de bot vertalen naar een audio-uitvoer

Dit zijn de vereisten voor het maken van een Basic-dialoog service met behulp van direct line speech.

- **Abonnement op spraak Services:** Een abonnement voor cognitieve spraak Services voor conversies van spraak naar tekst en tekst naar spraak. Probeer [hier](./overview.md#try-the-speech-service-for-free)gratis spraak Services.
- **Bot-Framework-bot:**  Een bot gemaakt met behulp van bot Framework versie 4,2 of hoger die is geabonneerd op [direct line speech](./direct-line-speech.md) om spraak invoer en uitvoer mogelijk te maken. [Deze hand leiding](./tutorial-voice-enable-your-bot-speech-sdk.md) bevat stapsgewijze instructies voor het maken van een "echo bot" en het abonneren op direct line speech. U kunt [hier](https://blog.botframework.com/2018/05/07/build-a-microsoft-bot-framework-bot-with-the-bot-builder-sdk-v4/) ook terecht voor de stappen voor het maken van een aangepaste bot en vervolgens [dezelfde stappen volgen om u te abonneren](./tutorial-voice-enable-your-bot-speech-sdk.md) op direct line speech, maar met uw nieuwe bot in plaats van met de "echo bot".

## <a name="try-out-the-sample-app"></a>De voor beeld-app uitproberen

Met uw abonnements sleutel voor spraak Services en de bot-ID van de echo bot bent u klaar om het voor beeld van de [UWP Voice Assistant](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)uit te proberen. Volg de instructies in het Leesmij-bestand om de app uit te voeren en uw referenties in te voeren.

## <a name="create-your-own-voice-assistant-for-windows"></a>Uw eigen spraak assistent maken voor Windows

Zodra u het token en bin-bestand van uw beperkte toegangs functie van micro soft hebt ontvangen, kunt u beginnen met uw eigen Voice Assistant in Windows.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [De implementatie handleiding voor spraak assistenten lezen](windows-voice-assistants-implementation-guide.md)