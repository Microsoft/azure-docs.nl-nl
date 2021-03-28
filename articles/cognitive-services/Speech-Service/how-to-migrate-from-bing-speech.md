---
title: Migreren van Bing Speech naar de speech-service
titleSuffix: Azure Cognitive Services
description: Meer informatie over hoe u vanuit een bestaand Bing Speech abonnement migreert naar de spraak service vanuit Azure Cognitive Services.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: nitinme
ms.openlocfilehash: d6b7b298e7e4d18e68985ec3cf536c90ef773608
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644392"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migreren van Bing Speech naar de speech-service

Gebruik dit artikel om uw toepassingen van de Bing Speech-API naar de speech-service te migreren.

In dit artikel vindt u een overzicht van de verschillen tussen de Bing Speech Api's en de speech-service, en suggesties voor strategieën voor het migreren van uw toepassingen. Uw Bing Speech-API-abonnements sleutel werkt niet met de spraak service; u hebt een nieuw abonnement voor de spraak service nodig.

Een service-abonnements sleutel met één spraak verleent toegang tot de volgende functies. Elk wordt afzonderlijk gemeten, zodat u alleen betaalt voor de functies die u gebruikt.

* [Spraak naar tekst](speech-to-text.md)
* [Aangepaste spraak-naar-tekst](/azure/cognitive-services/speech-service/custom-speech-overview)
* [Tekst-naar-spraak](text-to-speech.md)
* [Aangepaste tekst naar spraak](./how-to-custom-voice-create-voice.md)
* [Spraakomzetting](speech-translation.md) (bevat geen [tekstomzetting](../translator/translator-info-overview.md))

De [Speech SDK](speech-sdk.md) is een functionele vervanging voor de Bing speech-client Bibliotheken, maar gebruikt een andere API.

## <a name="comparison-of-features"></a>Vergelijking van functies

De spraak service is grotendeels vergelijkbaar met Bing Speech, met de volgende verschillen.

| Functie | Bing Speech | Speech Service | Details |
|--|--|--|--|
| C# SDK | :heavy_check_mark: | :heavy_check_mark: | Speech Service ondersteunt Windows 10, Universeel Windows-platform (UWP) en .NET Standard 2,0. |
| C++ SDK | : heavy_minus_sign: | :heavy_check_mark: | De speech-service biedt ondersteuning voor Windows en Linux. |
| Java-SDK | :heavy_check_mark: | :heavy_check_mark: | Spraak service ondersteunt Android-en spraak apparaten. |
| Continue spraak herkenning | 10 minuten | Onbeperkt | De Speech SDK ondersteunt onbeperkte doorlopende herkenning en maakt automatisch opnieuw verbinding met de time-out of de verbinding wordt verbroken. |
| Gedeeltelijke of tussentijdse resultaten | :heavy_check_mark: | :heavy_check_mark: | Ondersteund met de spraak-SDK. |
| Aangepaste spraak modellen | :heavy_check_mark: | :heavy_check_mark: | Voor Bing Speech is een afzonderlijk Custom Speech-abonnement vereist. |
| Aangepaste spraak lettertypen | :heavy_check_mark: | :heavy_check_mark: | Voor Bing Speech is een apart aangepast spraak abonnement vereist. |
| 24-kHz stemmen | : heavy_minus_sign: | :heavy_check_mark: |
| Herkenning van spraak intentie | Vereist een afzonderlijke LUIS-API-aanroep | Geïntegreerd (met SDK) | U kunt een LUIS-sleutel gebruiken met de spraak service. |
| Eenvoudige intentie herkenning | : heavy_minus_sign: | :heavy_check_mark: |
| Batch-transcriptie met lange audio bestanden | : heavy_minus_sign: | :heavy_check_mark: |
| Herkennings-modus | Hand matig via eind punt-URI | Automatisch | Herkennings modus is niet beschikbaar in de speech-service. |
| Locatie van eind punt | Globaal | Regionaal | Regionale eind punten verbeteren de latentie. |
| REST-API’s | :heavy_check_mark: | :heavy_check_mark: | De REST-Api's van de speech-service zijn compatibel met Bing Speech (verschillende eind punten). REST-Api's bieden ondersteuning voor tekst-naar-spraak-en beperkte spraak-naar-tekst functionaliteit. |
| Protocollen voor websockets | :heavy_check_mark: | : heavy_minus_sign: | De Speech SDK abstracten Web socket-verbindingen voor functionaliteit waarvoor een constante verbinding met de service is vereist, zodat u zich niet meer kunt abonneren op de apps. |
| Service-naar-Service-API-aanroepen | :heavy_check_mark: | : heavy_minus_sign: | In Bing Speech via de service bibliotheek van C#. |
| Open-Source-SDK | :heavy_check_mark: | : heavy_minus_sign: |

De speech-service gebruikt een op tijd gebaseerd prijs model (in plaats van een model op basis van een trans actie). Zie de [prijzen voor spraak Services](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) voor meer informatie.

## <a name="migration-strategies"></a>Migratiestrategieën

Als u of uw organisatie toepassingen in ontwikkeling of productie heeft die gebruikmaken van een Bing Speech-API, moet u ze zo snel mogelijk bijwerken om de spraak service te gebruiken. Raadpleeg de [documentatie](index.yml) van de speech-service voor beschik bare sdk's, code voorbeelden en zelf studies.

De rest- [api's](./overview.md#reference-docs) van de speech-service zijn compatibel met de Bing speech-api's. Als u momenteel de Bing Speech REST-Api's gebruikt, hoeft u alleen het REST-eind punt te wijzigen en over te scha kelen naar een sleutel voor een spraak service-abonnement.

Als u een Bing Speech-client bibliotheek gebruikt voor een specifieke programmeer taal, moet u voor de migratie naar de [Speech SDK](speech-sdk.md) wijzigingen aanbrengen in uw toepassing, omdat de API anders is. De Speech SDK kan uw code eenvoudiger maken, en u hebt ook toegang tot nieuwe functies. De Speech SDK is beschikbaar in een groot aantal programmeer talen. Api's op alle platforms zijn vergelijkbaar en versnellen de ontwikkeling van meerdere platforms.

De speech-service biedt geen globaal eind punt. Bepaal of uw toepassing efficiënt werkt wanneer er één regionaal eind punt wordt gebruikt voor al het verkeer. Als dat niet het geval is, gebruikt u geolocatie om het meest efficiënte eind punt te bepalen. U hebt een apart abonnement voor spraak services nodig in elke regio die u gebruikt.

Om aan de slag te gaan met de Speech SDK:

1. Down load de [spraak-SDK](speech-sdk.md).
1. Werk met de Quick Start- [hand leidingen](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) en [zelf studies](how-to-recognize-intents-from-speech-csharp.md)voor de spraakherkennings service. Bekijk ook de [code voorbeelden](./speech-sdk.md#sample-source-code) om ervaring te krijgen met de nieuwe api's.
1. Werk uw toepassing bij om de speech-service te gebruiken.

## <a name="support"></a>Ondersteuning

Bing Speech klanten contact opnemen met klant ondersteuning door een [ondersteunings ticket](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)te openen. U kunt ook contact met ons opnemen als voor uw ondersteunings [abonnement een technisch ondersteunings plan](https://azure.microsoft.com/support/plans/)nodig is.

Ga naar de [ondersteunings pagina](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)voor spraak Services voor spraak service, SDK en API-ondersteuning.

## <a name="next-steps"></a>Volgende stappen

* [De spraak service gratis uitproberen](overview.md#try-the-speech-service-for-free)
* [Aan de slag met spraak-naar-tekst](get-started-speech-to-text.md)
* [Aan de slag met tekst-naar-spraak](get-started-text-to-speech.md)

## <a name="see-also"></a>Zie ook

* [Release opmerkingen bij de spraak service](releasenotes.md)
* [Wat is de speech-service](overview.md)
* [Documentatie voor speech-service en spraak SDK](speech-sdk.md#get-the-speech-sdk)
