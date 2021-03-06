---
title: Overzicht van spraak naar tekst-spraak service
titleSuffix: Azure Cognitive Services
description: Met spraak-naar-tekst-software kunt u transcriptie in realtime omzetten in tekst. Uw toepassingen, hulpprogram ma's of apparaten kunnen deze tekst invoer gebruiken, weer geven en actie ondernemen. Dit artikel bevat een overzicht van de voor delen en mogelijkheden van de service voor spraak naar tekst.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: spraak naar tekst, spraak naar tekst software
ms.openlocfilehash: 3450d39729096bfc3077f51e2069f8f102e571a5
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449389"
---
# <a name="what-is-speech-to-text"></a>Wat is spraak-naar-tekst?

In dit overzicht vindt u meer informatie over de voor delen en mogelijkheden van de service voor spraak naar tekst.
Spraak-naar-tekst, ook wel spraak herkenning genoemd, biedt realtime transcriptie van audio-streams in tekst. Uw toepassingen, hulpprogram ma's of apparaten kunnen deze tekst gebruiken, weer geven en actie ondernemen als invoer van de opdracht. Deze service wordt aangestuurd door dezelfde herkennings technologie die door micro soft wordt gebruikt voor Cortana en Office-producten. Het werkt probleemloos met de <a href="./speech-translation.md" target="_blank">Vertaal </a> -en <a href="./text-to-speech.md" target="_blank">tekst-naar-spraak- </a> service aanbiedingen. Zie [ondersteunde talen](language-support.md#speech-to-text)voor een volledige lijst met beschik bare spraak-naar-tekst talen.

De spraak-naar-tekst-service is standaard ingesteld op het gebruik van het universele-taal model. Dit model is getraind met gegevens van micro soft en wordt geïmplementeerd in de Cloud. Het is optimaal voor gespreks-en dicteer scenario's. Wanneer u spraak naar tekst gebruikt voor herkenning en transcriptie in een unieke omgeving, kunt u aangepaste akoestische, taal-en uitspraak modellen maken en trainen. Aanpassing is handig voor het adresseren van omgevings lawaai of branchespecifieke woorden lijsten.

Deze documentatie bevat de volgende artikel typen:

* **Quick** starts zijn aan de slag-instructies die u helpen bij het maken van aanvragen voor de service.
* **Hand leidingen** bevatten instructies voor het gebruik van de service op meer specifieke of aangepaste manieren.
* **Concepten** geven uitgebreide uitleg over de service functionaliteit en-functies.
* **Zelf studies** zijn meer gidsen die laten zien hoe u de service kunt gebruiken als onderdeel in bredere zakelijke oplossingen.

> [!NOTE]
> Bing Speech is uit bedrijf genomen op 15 oktober 2019. Als uw toepassingen, hulpprogram ma's of producten gebruikmaken van de Bing Speech Api's, hebben we gidsen gemaakt om u te helpen migreren naar de spraak service.
> - [Migreren van Bing Speech naar de speech-service](how-to-migrate-from-bing-speech.md)

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="get-started"></a>Aan de slag

Bekijk de [Snelstartgids](get-started-speech-to-text.md) om aan de slag te gaan met spraak naar tekst. De service is beschikbaar via de [Speech SDK](speech-sdk.md), het [rest API](rest-speech-to-text.md#pronunciation-assessment-parameters)en de [Speech cli](spx-overview.md).

## <a name="sample-code"></a>Voorbeeldcode

Voorbeeld code voor de Speech SDK is beschikbaar op GitHub. Deze voorbeelden hebben betrekking op veelvoorkomende scenario's, zoals het lezen van audio van een bestand of stream, continue en eenmalige herkenning en het werken met aangepaste modellen.

- [Voor beelden van spraak naar tekst (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Voorbeelden van batchtranscriptie (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Evaluatie voorbeelden van uitspraak (REST)](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="customization"></a>Aanpassing

Naast het standaard spraak service model kunt u aangepaste modellen maken. Aanpassing helpt de obstakels voor spraak herkenning, zoals spreek stijl, vocabulaire en achtergrond ruis, te overwinnen [Custom speech](./custom-speech-overview.md). Aanpassings opties variëren per taal/land instelling. Zie [ondersteunde talen](./language-support.md) voor het controleren van de ondersteuning.

## <a name="batch-transcription"></a>Batchtranscriptie

Batch-transcriptie is een reeks REST API bewerkingen waarmee u een grote hoeveelheid audio in de opslag kunt transcriberen. U kunt met een SAS-URI (Shared Access Signature) naar audiobestanden verwijzen en de transcriptieresultaten asynchroon ontvangen. Zie de [procedure](batch-transcription.md) voor meer informatie over het gebruik van de batch transcriptie API.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Volgende stappen

- [Verkrijg gratis een spraakserviceabonnementssleutel](overview.md#try-the-speech-service-for-free)
- [De Speech SDK ophalen](speech-sdk.md)