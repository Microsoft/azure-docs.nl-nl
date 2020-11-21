---
title: Speech-apparaten SDK-Speech-Service
titleSuffix: Azure Cognitive Services
description: Aan de slag met de SDK voor spraak apparaten. De speech-service werkt met een groot aantal verschillende apparaten en audio bronnen. De SDK voor spraak apparaten is een vooraf afgestemde bibliotheek die is gekoppeld aan speciaal ontwikkelde, ontwikkel Kits voor microfoon matrices.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: 0309329b37436022dc88f8951853c4bb09a2c080
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015270"
---
# <a name="what-is-the-speech-devices-sdk"></a>Wat is de SDK voor spraak apparaten?

De [Speech-Service](overview.md) werkt met een groot aantal verschillende apparaten en audio bronnen. Nu kunt u uw spraak toepassingen naar een hoger niveau brengen met overeenkomende hardware en software. De SDK voor spraak apparaten is een vooraf afgestemde bibliotheek die is gekoppeld aan speciaal ontwikkelde, ontwikkel Kits voor microfoon matrices.

De SDK voor spraak apparaten kan u helpen bij het volgende:

- Test nieuwe spraak scenario's snel.
- Integreer de spraak service op basis van de Cloud eenvoudiger in uw apparaat.
- Maak een buitengewone gebruikers ervaring voor uw klanten.

De speech-apparaten SDK verbruikt de [spraak-SDK](speech-sdk.md). Met onze geavanceerde algoritmen voor geluids verwerking met de microfoon matrix van het apparaat kunt u de audio naar de [Speech-Service](overview.md)verzenden. Het biedt nauw keurige [spraak herkenning](speech-to-text.md) via ruis, Echo annulering, beamforming en dereverberation.

U kunt ook de speech apparaten SDK gebruiken om omgevings apparaten te bouwen die uw eigen [aangepaste sleutel woord](./custom-keyword-basics.md)hebben. Een aangepast tref woord biedt een hint waarmee een gebruikers interactie wordt gestart die uniek is voor uw merk.

De SDK voor spraak apparaten maakt een groot aantal scenario's met spraak functionaliteit mogelijk, zoals [spraak assistenten](./voice-assistants.md), bestel systemen voor stations, [conversatie transcriptie](./conversation-transcription.md)en slimme sprekers. U kunt reageren op gebruikers met tekst, ze weer in een standaard-of [aangepaste stem](./how-to-custom-voice-create-voice.md)terugspreken, Zoek resultaten opgeven, [vertalen](speech-translation.md) naar andere talen en meer. We kijken ernaar uit om te zien wat u bouwt.

## <a name="get-the-speech-devices-sdk"></a>De Speech Devices SDK ophalen

### <a name="android"></a>Android

De speech-apparaten SDK voor Android ondersteunt de [roobo v1](speech-devices-sdk-roobo-v1.md) -en gelijkwaardige apparaten, voor deze down load de meest recente versie van de [Android speech-apparaten SDK](https://aka.ms/sdsdk-download-android).


Als u een ander Android-apparaat hebt, zoals een telefoon of mobiel, start u met de [Android Speech SDK](speech-sdk.md)


### <a name="windows"></a>Windows

Voor Windows wordt de voorbeeld toepassing meegeleverd als een platformoverschrijdende Java-toepassing. Down load de nieuwste versie van de software voor [jre speech-apparaten](https://aka.ms/sdsdk-download-JRE).
De toepassing wordt gebouwd met het Speech SDK-pakket en de Eclipse Java IDE (v4) op 64-bits Windows. De toepassing wordt uitgevoerd op een 64-bit Java 8 runtime-omgeving (JRE).

### <a name="linux"></a>Linux

Voor Linux wordt de voorbeeld toepassing meegeleverd als een cross-platform Java-toepassing. Down load de nieuwste versie van de software voor [jre speech-apparaten](https://aka.ms/sdsdk-download-JRE).
De toepassing is gebouwd met het Speech SDK-pakket en de eclips Java IDE (v4) op 64-bits Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8). De toepassing wordt uitgevoerd op een 64-bit Java 8 runtime-omgeving (JRE).

Er zijn extra binaire bestanden ter ondersteuning van aanstaande apparaten, [roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2), [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter), [GGEC spreker](https://aka.ms/sdsdk-download-speaker), [Linux ARM32](https://aka.ms/sdsdk-download-linux-arm32)en [Linux ARM64](https://aka.ms/sdsdk-download-linux-arm64).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uw spraak apparaat kiezen](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Verkrijg gratis een spraakserviceabonnementssleutel](overview.md#try-the-speech-service-for-free)