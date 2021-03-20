---
title: Uw app met aangepaste opdrachten testen
titleSuffix: Azure Cognitive Services
description: In dit artikel leert u verschillende benaderingen om een toepassing met aangepaste opdrachten te testen.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c0bd21f55fee4d8487826deae23093ede293c8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95021810"
---
# <a name="test-your-custom-commands-application"></a>Uw toepassing voor aangepaste opdrachten testen

In dit artikel leert u verschillende benaderingen om een toepassing met aangepaste opdrachten te testen.

## <a name="test-in-the-portal"></a>Testen in de portal

Testen in de portal is de eenvoudigste en snelste manier om te controleren of uw aangepaste opdracht toepassing naar verwachting werkt. Nadat de app is getraind, klikt u op de `Test` knop om te beginnen met testen.

> [!div class="mx-imgBorder"]
> ![Testen in de portal](media/custom-commands/create-basic-test-chat.png)

## <a name="test-with-windows-voice-assistant-client"></a>Testen met Windows Voice Assistant-client

De Windows Voice Assistant-client is een Windows Presentation Foundation-toepassing (WPF) in C# waarmee u eenvoudig interacties met uw bot kunt testen voordat u een aangepaste client toepassing maakt.

Met het hulp programma kunt u een aangepaste opdracht-ID voor opdrachten accepteren. U kunt hiermee het scenario voor het volt ooien van taken of opdracht-en besturings elementen testen die worden gehost op de service aangepaste opdrachten.

Als u de client wilt instellen, moet u de [Windows Voice Assistant-client](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf)afhandelen.

> [!div class="mx-imgBorder"]
> ![WVAC maken](media/custom-commands/conversation.png)

## <a name="test-with-speech-sdk-enabled-client-applications"></a>Testen met spraak-SDK-client toepassingen 
De speech Software Development Kit (SDK) toont veel van de mogelijkheden van de spraak service, waarmee u toepassingen met spraak functionaliteit kunt ontwikkelen. Het is ook beschikbaar in veel programmeer talen en op alle platforms.

Een Universeel Windows-platform-client toepassing (UWP) met spraak-SDK instellen en deze integreren met uw aangepaste opdracht toepassing:  
- [Procedure: integreren met een client toepassing met behulp van Speech SDK](./how-to-custom-commands-setup-speech-sdk.md)
- [Procedure: activiteit verzenden naar client toepassing](./how-to-custom-commands-send-activity-to-client.md)
- [Procedure: Web-eind punten instellen](./how-to-custom-commands-setup-web-endpoints.md)

Voor andere programmeer talen en platformen:
- [Speech SDK-programmeer talen, platforms, scenario capaciteiten](./speech-sdk.md)
- [Voorbeeld codes voor spraak assistenten](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Bekijk de voor beelden op GitHub](https://aka.ms/speech/cc-samples)