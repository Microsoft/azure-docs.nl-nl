---
title: Overzicht van spraak omzetting-spraak service
titleSuffix: Azure Cognitive Services
description: Met spraak omzetting kunt u een end-to-end, realtime vertaling van spraak naar uw toepassingen, hulpprogram ma's en apparaten toevoegen. Dezelfde API kan worden gebruikt voor omzetting van zowel spraak-naar-spraak als spraak-naar-tekst. Dit artikel bevat een overzicht van de voor delen en mogelijkheden van de service voor spraak omzetting.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: erhopf
ms.custom: devx-track-csharp, cog-serv-seo-aug-2020
keywords: spraak omzetting
ms.openlocfilehash: 94ddd06068513261b5b73b313877e273c7251d62
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954957"
---
# <a name="what-is-speech-translation"></a>Wat is spraakomzetting?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

In dit overzicht vindt u meer informatie over de voor delen en mogelijkheden van de service voor spraak omzetting, waarmee u audio gegevensstromen in realtime, [meertalige spraak naar spraak](language-support.md#speech-translation) en spraak naar tekst kunt omzetten. Met de spraak-SDK hebben uw toepassingen, hulpprogram ma's en apparaten toegang tot de bron-transcripties en de Vertaal uitvoer voor de audio. Tussenliggende transcriptie-en vertaal resultaten worden geretourneerd als spraak wordt gedetecteerd en de uiteindelijke resultaten kunnen worden omgezet in gesynthesizerde spraak.

## <a name="core-features"></a>Kern functies

* Conversie van spraak naar tekst met herkennings resultaten.
* Conversie van spraak naar spraak.
* Ondersteuning voor vertaling naar meerdere doel talen.
* Tussentijdse herkenning en omzettings resultaten.

## <a name="get-started"></a>Aan de slag 

Raadpleeg de [Snelstartgids](get-started-speech-translation.md) om aan de slag te gaan met spraak omzetting. De service voor spraak omzetting is beschikbaar via de [Speech SDK](speech-sdk.md) en de [Speech cli](spx-overview.md).

## <a name="sample-code"></a>Voorbeeldcode

Voorbeeld code voor de Speech SDK is beschikbaar op GitHub. Deze voor beelden hebben betrekking op veelvoorkomende scenario's, zoals het lezen van audio van een bestand of stream, continue en eenmalige herkenning/omzetting en werken met aangepaste modellen.

* [Voor beelden van spraak naar tekst en vertaling (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Migratiehandleidingen

Als uw toepassingen, hulpprogram ma's of producten de [Translator Speech-API](./how-to-migrate-from-translator-speech-api.md)gebruiken, hebben we gidsen gemaakt om u te helpen migreren naar de spraak service.

* [Migreren van de Translator Speech-API naar de speech-service](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Naslagdocumentatie

* [Speech-SDK](./speech-sdk.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Spraak-naar-tekst](rest-speech-to-text.md)
* [REST API: Tekst-naar-spraak](rest-text-to-speech.md)
* [REST API: Batchtranscriptie en aanpassing](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)

## <a name="next-steps"></a>Volgende stappen

* De [Snelstartgids](get-started-speech-translation.md) voor spraak omzetting volt ooien
* [Verkrijg gratis een spraakserviceabonnementssleutel](overview.md#try-the-speech-service-for-free)
* [De Speech SDK ophalen](speech-sdk.md)