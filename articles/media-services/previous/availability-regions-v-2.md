---
title: Regionale Beschik baarheid van Azure Media Services | Microsoft Docs
description: Dit artikel bevat een overzicht van Microsoft Azure Media Services functies en regionale Beschik baarheid van de service.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: references_regions
ms.openlocfilehash: 125f2be205760618ab6ffa3175f719f5e91e8c05
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012255"
---
# <a name="media-services-regional-availability"></a>Regionale Beschik baarheid Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. Bekijk de nieuwste versie [Media Services v3](../latest/media-services-overview.md). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Met Microsoft Azure Media Services (AMS) kunt u veilig video- of audio-inhoud uploaden, opslaan, coderen en verpakken, zowel voor levering on-demand als levering via livestreaming aan verschillende clients (bijvoorbeeld tv, pc en mobiele apparaten).

AMS werkt in meerdere regio's over de hele wereld, waardoor u flexibiliteit hebt bij het kiezen waar u uw toepassingen wilt bouwen. Dit artikel bevat een overzicht van Microsoft Azure Media Services functies en regionale Beschik baarheid van de service.

Zie [Azure-geografi](https://azure.microsoft.com/global-infrastructure/geographies/)(Engelstalig) voor meer informatie over de volledige algemene Azure-infra structuur.

## <a name="ams-accounts"></a>AMS-accounts

Gebruik [Azure-producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=media-services&regions=all) om te bepalen of Media Services beschikbaar is in een bepaalde regio.

## <a name="streaming-endpoints"></a>Streaming-eindpunten

Media Services-klanten kunnen een **Standard**-streaming-eindpunt of een Premium-**streaming**-eindpunt kiezen.

|Name|Status|Region
|---|---|---|
|Standard|Algemene beschikbaarheid|Alles|
|Premium|Algemene beschikbaarheid|Alles|

## <a name="live-encoding"></a>Live Encoding

Beschikbaar in alle regio's behalve: Duitsland, Brazilië-zuid, India-West, India-Zuid en India centraal.

## <a name="encoding-media-processors"></a>Mediaprocessors coderen

AMS biedt twee coderingsprogramma's die op basis van behoefte kunnen worden gebruikt: **Media Encoder Standard** en **Media Encoder Premium Workflow**. Zie [Overzicht en een vergelijking van Azure-mediacoderingsprogramma's die op basis van behoefte kunnen worden gebruikt](media-services-encode-asset.md) voor meer informatie.

|Naam van mediaprocessor|Status|Regio's
|---|---|---|
|Media Encoder Standard|Algemene beschikbaarheid|Alles|
|Media Encoder Premium Workflow|Algemene beschikbaarheid|Overal behalve China|

## <a name="analytics-media-processors"></a>Mediaprocessors voor analyse

Media Analytics is een verzameling spraakonderdelen en visuele onderdelen waarmee organisaties en bedrijven gemakkelijker inzichten kunnen verkrijgen op basis van hun video's, waarvoor een actie kan worden uitgevoerd. Zie [Overzicht van Azure Media Services Analytics](./legacy-components.md) voor meer informatie.

> [!NOTE]
> Sommige analyse media-processors worden buiten gebruik gesteld. Zie het onderwerp [oudere onderdelen](legacy-components.md) voor de pensioen datums.

|Naam van mediaprocessor|Status|Region
|---|---|---|
|Azure Media Face Detector|Preview|Alles|
|Azure Media Indexer|Algemene beschikbaarheid|Alles|
|Azure Media Motion Detector|Preview|Alles|
|Azure Media OCR|Preview|Alles|
|Azure Media Redactor|Algemene beschikbaarheid|Alles|
|Azure Media Video Thumbnails|Preview|Alles|

## <a name="protection"></a>Beveiliging

Met Microsoft Azure Media Services kunt u uw media beveiligen vanaf het moment dat deze uw computer verlaten en ze worden opgeslagen, verwerkt en afgeleverd. Zie [AMS-inhoud beveiligen](media-services-content-protection-overview.md) voor meer informatie.

|Versleuteling|Status|Regio's|
|---|---|---| 
|Storage|Algemene beschikbaarheid|Alles|
|AES-128-sleutels|Algemene beschikbaarheid|Alles|
|FairPlay|Algemene beschikbaarheid|Alles|
|PlayReady|Algemene beschikbaarheid|Alles|
|Widevine|Algemene beschikbaarheid|Alle behalve Duitsland, federale overheid en China.

> [!NOTE]
> Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="reserved-units-rus"></a>Gereserveerde eenheden (RU's)

Op basis van het aantal ingerichte, gereserveerde eenheden wordt bepaald hoeveel mediataken tegelijk kunnen worden verwerkt voor een bepaald account.

Beschikbaar in alle regio's.

## <a name="reserved-unit-ru-type"></a>Gereserveerde-eenheidstype (RU)

Een Media Services-account is gekoppeld aan een gereserveerd eenheids type waarmee de snelheid waarmee de media verwerkings taken worden voltooid, wordt bepaald. U kunt kiezen uit de volgende gereserveerde-eenheids typen: S1, S2 of S3.

|RU-typenaam|Status|Regio's
|---|---|---|
|S1|Algemene beschikbaarheid|Alles|
|S2|Algemene beschikbaarheid|Overal behalve in BR - zuid en India - west|
|S3|Algemene beschikbaarheid|Overal behalve in India - west|

## <a name="next-steps"></a>Volgende stappen

[Migreren naar Media Services v3](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>Feedback geven

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]