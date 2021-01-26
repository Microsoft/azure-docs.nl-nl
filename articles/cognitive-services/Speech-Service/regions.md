---
title: Regio's-spraak service
titleSuffix: Azure Cognitive Services
description: Een lijst met beschik bare regio's en eind punten voor de spraak service, inclusief spraak naar tekst, tekst naar spraak en spraak omzetting.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.custom: seodec18,references_regions
ms.openlocfilehash: 646d29e72b91cd6afcde8e70ad8fd8715442b88e
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98786788"
---
# <a name="speech-service-supported-regions"></a>Ondersteunde regio's voor spraak Services

Met de speech-service kan uw toepassing audio naar tekst omzetten, spraak omzetting uitvoeren en tekst naar spraak converteren. De service is beschikbaar in meerdere regio's met unieke eind punten voor de Speech SDK en REST Api's.

De spraak Portal om aangepaste configuraties uit te voeren op uw spraak ervaring voor alle regio's is hier beschikbaar: https://speech.microsoft.com

Houd rekening met de volgende punten bij het overwegen van regio's:

* Als uw toepassing gebruikmaakt van een [spraak-SDK](speech-sdk.md), geeft u de regio-id op, zoals bij het maken van `westus` een spraak configuratie.
* Als uw toepassing gebruikmaakt van een van de rest- [api's](./overview.md#reference-docs)van de speech-service, maakt de regio deel uit van de EINDPUNT-URI die u gebruikt bij het maken van aanvragen.
* Sleutels die zijn gemaakt voor een regio, zijn alleen geldig in die regio. Als u deze probeert te gebruiken met andere regio's, worden verificatie fouten veroorzaakt.

## <a name="speech-sdk"></a>Speech-SDK

In de [Speech SDK](speech-sdk.md)worden regio's opgegeven als een teken reeks (bijvoorbeeld als een para meter voor `SpeechConfig.FromSubscription` in de Speech SDK voor C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Spraak naar tekst, tekst-naar-spraak en omzetting

De portal voor spraak aanpassing is hier beschikbaar: https://speech.microsoft.com

De speech-service is beschikbaar in deze regio's voor **spraak herkenning**, **tekst naar spraak** en **vertaling**:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Als u de [spraak-SDK](speech-sdk.md)gebruikt, worden regio's opgegeven met de **regio-id** (bijvoorbeeld als een para meter aan `SpeechConfig.FromSubscription` ). Zorg ervoor dat de regio overeenkomt met de regio van uw abonnement.

Als u van plan bent een aangepast model met audio gegevens te trainen, gebruikt u een van de [regio's met speciale hardware](custom-speech-overview.md#set-up-your-azure-account) voor een snellere training. U kunt de [rest API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) gebruiken om het volledig getrainde model later te kopiëren naar een andere regio.

### <a name="intent-recognition"></a>Intentieherkenning

Beschik bare regio's voor **intentie herkenning** via de Speech SDK zijn de volgende:

| Wereld wijde regio | Regio           | Regio-id |
| ------------- | ---------------- | -------------------- |
| Azië          | Azië - oost        | `eastasia`           |
| Azië          | Azië - zuidoost   | `southeastasia`      |
| Australië     | Australië - oost   | `australiaeast`      |
| Europa        | Europa - noord     | `northeurope`        |
| Europa        | Europa -west      | `westeurope`         |
| Noord-Amerika | VS - oost          | `eastus`             |
| Noord-Amerika | VS - oost 2        | `eastus2`            |
| Noord-Amerika | VS - zuid-centraal | `southcentralus`     |
| Noord-Amerika | VS - west-centraal  | `westcentralus`      |
| Noord-Amerika | VS - west          | `westus`             |
| Noord-Amerika | VS - west 2        | `westus2`            |
| Zuid-Amerika | Brazilië - zuid     | `brazilsouth`        |

Dit is een subset van de publicatie regio's die worden ondersteund door de [Language Understanding-service (Luis)](../luis/luis-reference-regions.md).

### <a name="voice-assistants"></a>Spraakassistenten

De [spraak-SDK](speech-sdk.md) biedt ondersteuning voor **spraak assistent** -mogelijkheden via [directe line spraak](./direct-line-speech.md) in deze regio's:

| Wereld wijde regio | Regio           | Regio-id    |
| ------------- | ---------------- | -------------------- |
| Noord-Amerika | VS - west          | `westus`             |
| Noord-Amerika | VS - west 2        | `westus2`            |
| Noord-Amerika | VS - oost          | `eastus`             |
| Noord-Amerika | VS - oost 2        | `eastus2`            |
| Noord-Amerika | VS - west-centraal  | `westcentralus`      |
| Noord-Amerika | VS - zuid-centraal | `southcentralus`     |
| Europa        | Europa -west      | `westeurope`         |
| Europa        | Europa - noord     | `northeurope`        |
| Azië          | Azië - oost        | `eastasia`           |
| Azië          | Azië - zuidoost   | `southeastasia`      |
| India         | India - centraal    | `centralindia`       |

### <a name="speaker-recognition"></a>Speaker Recognition

Speaker Recognition is momenteel alleen beschikbaar in de `westus` regio.

## <a name="rest-apis"></a>REST-API’s

De spraak service stelt ook REST-eind punten beschikbaar voor aanvragen voor spraak naar tekst en tekst naar spraak.

### <a name="speech-to-text"></a>Spraak naar tekst

Zie [spraak-naar-tekst-rest API](rest-speech-to-text.md)voor naslag informatie over spraak naar tekst.

Het eind punt voor de REST API heeft de volgende indeling:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Vervang door `<REGION_IDENTIFIER>` de id die overeenkomt met de regio van uw abonnement uit deze tabel:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> De para meter language moet worden toegevoegd aan de URL om te voor komen dat er een 4xx HTTP-fout wordt ontvangen. Bijvoorbeeld, de taal die is ingesteld op Amerikaans-Engels met het eind punt vs West is: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

### <a name="text-to-speech"></a>Tekst naar spraak

Zie [tekst-naar-spraak-rest API](rest-text-to-speech.md)voor naslag informatie over tekst naar spraak.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]