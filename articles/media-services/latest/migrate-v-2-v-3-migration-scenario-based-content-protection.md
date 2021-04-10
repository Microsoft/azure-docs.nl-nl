---
title: Richt lijnen voor migratie van inhouds beveiliging
description: In dit artikel vindt u informatie over het scenario op basis van inhouds beveiliging die u helpt bij het migreren van Azure Media Services v2 naar v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/26/2021
ms.author: inhenkel
ms.openlocfilehash: 9141fb025cb2c7976f88d894768972b10ea3a3d3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729402"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Richt lijnen voor migratie op basis van inhouds beveiligings scenario's

![logo van de migratie handleiding](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![migratie stappen 2](./media/migration-guide/steps-4.svg)

In dit artikel vindt u informatie en richt lijnen over de migratie van gebruiks voorbeelden van inhouds beveiliging van de v2 API naar de nieuwe Azure Media Services v3 API.

## <a name="protect-content-in-v3-api"></a>Inhoud in v3 API beveiligen

Gebruik de ondersteuning voor functies met [meerdere sleutels](design-multi-drm-system-with-access-control.md) in de nieuwe v3 API.

Zie concepten voor inhouds beveiliging, zelf studies en hand leidingen hieronder voor specifieke stappen.

## <a name="visibility-of-v2-assets-streaminglocators-and-properties-in-the-v3-api-for-content-protection-scenarios"></a>Zicht baarheid van v2-assets, StreamingLocators en eigenschappen in de V3 API voor scenario's voor inhouds beveiliging

Tijdens de migratie naar de V3 API zult u zien dat u bepaalde eigenschappen of inhouds sleutels uit uw v2-assets moet openen. Een belang rijk verschil is dat de v2 API het **AssetId** zou gebruiken als de primaire identificatie sleutel en de nieuwe v3 API de Azure resource management-naam van de entiteit als de primaire id gebruikt.  De eigenschap v2 **Asset.name** wordt doorgaans niet gebruikt als een unieke id, dus wanneer u naar v3 migreert, zult u zien dat uw v2-Asset-namen nu worden weer gegeven in het veld **activa. Beschrijving** .

Als u bijvoorbeeld eerder een v2-activum met de ID **' NB: Cid: uuid: 8cb39104-122c-496e-9ac5-7f9e2c2547b8 '** had, dan vindt u de oude v2-assets via de V3 API. de naam is nu het GUID-deel aan het einde (in dit geval **' 8cb39104-122c-496e-9ac5-7f9e2c2547b8 '**.)

U kunt een query uitvoeren voor de **StreamingLocators** die zijn gekoppeld aan de assets die zijn gemaakt in de v2 API met behulp van de nieuwe v3-methode [ListStreamingLocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators) op de Asset-entiteit.  Raadpleeg ook de .NET client SDK-versie van [ListStreamingLocatorsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?view=azure-dotnet&preserve-view=true)

De resultaten van de **ListStreamingLocators** -methode geven u de **naam** en **StreamingLocatorId** van de Locator samen met de **StreamingPolicyName**.

Als u de **ContentKeys** wilt vinden die wordt gebruikt in uw **StreamingLocators** voor inhouds beveiliging, kunt u de methode [StreamingLocator. ListContentKeysAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?view=azure-dotnet&preserve-view=true) aanroepen.  

**Activa** die zijn gemaakt en gepubliceerd met behulp van de v2 API, hebben zowel een beleid voor de [inhouds sleutel](https://docs.microsoft.com/azure/media-services/latest/content-key-policy-concept) als een inhouds sleutel die is gedefinieerd in de V3 API, in plaats van een standaard beleid voor de inhouds sleutel in het [streaming-beleid](https://docs.microsoft.com/azure/media-services/latest/streaming-policy-concept)te gebruiken.

Voor meer informatie over de beveiliging van inhoud in de V3 API raadpleegt [u het artikel Beveilig uw inhoud met Media Services dynamische versleuteling.](https://docs.microsoft.com/azure/media-services/latest/content-protection-overview)

## <a name="how-to-list-your-v2-assets-and-content-protection-settings-using-the-v3-api"></a>Uw v2-assets en instellingen voor inhouds beveiliging vermelden met behulp van de V3 API

In de v2 API gebruikt u doorgaans **assets**, **StreamingLocators** en **ContentKeys** om uw streaming-inhoud te beschermen.
Wanneer u migreert naar de V3 API, worden uw v2 API-assets, StreamingLocators en ContentKeys automatisch alle weer gegeven in de V3 API en zijn alle gegevens erop beschikbaar voor toegang.

## <a name="can-i-update-v2-properties-using-the-v3-api"></a>Kan ik v2-eigenschappen bijwerken met behulp van de V3 API?

Nee, u kunt geen eigenschappen van v2-entiteiten bijwerken via de V3 API die is gemaakt met behulp van StreamingLocators, StreamingPolicies, beleids regels voor inhouds sleutels en inhouds sleutels in v2.
Als u inhoud die is opgeslagen op v2-entiteiten moet bijwerken, wijzigen of wijzigen, moet u deze bijwerken via de v2 API of nieuwe v3 API-entiteiten maken om ze vooruit te migreren.

## <a name="how-do-i-change-the-contentkeypolicy-used-for-a-v2-asset-that-is-published-and-keep-the-same-content-key"></a>Hoe kan ik Wijzig de ContentKeyPolicy die wordt gebruikt voor een v2-Asset die wordt gepubliceerd en behoud dezelfde inhouds sleutel?

In dit geval moet u eerst de publicatie ongedaan maken (alle stream-Locators verwijderen) op het activum via de v2 SDK (de Locator verwijderen, het autorisatie beleid voor de inhouds sleutel ontkoppelen, het beleid voor de levering van assets ontkoppelen, de inhouds sleutel verwijderen, de inhouds sleutel ontkoppelen) en vervolgens een nieuwe **[StreamingLocator](https://docs.microsoft.com/azure/media-services/latest/streaming-locators-concept)** in v3 maken met behulp van een v3 [StreamingPolicy](https://docs.microsoft.com/azure/media-services/latest/streaming-policy-concept) en [ContentKeyPolicy](https://docs.microsoft.com/azure/media-services/latest/content-key-policy-concept)

U moet de specifieke inhouds sleutel-id en sleutel waarde opgeven die nodig zijn wanneer u de **[StreamingLocator](https://docs.microsoft.com/azure/media-services/latest/streaming-locators-concept)** maakt.

Houd er rekening mee dat het mogelijk is om de v2-Locator te verwijderen met de V3 API, maar Hiermee wordt de inhouds sleutel of het beleid voor de inhouds sleutel die wordt gebruikt als ze zijn gemaakt in de v2 API, niet verwijderd.  

## <a name="using-amse-v2-and-amse-v3-side-by-side"></a>AMSE v2 en AMSE v3 naast elkaar gebruiken

Wanneer u inhoud migreert van v2 naar v3, wordt u geadviseerd om het [v2 Azure Media Services Explorer-hulp programma](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0) samen met het [hulp programma v3 Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) te installeren, zodat u de gegevens kunt vergelijken die naast elkaar worden weer gegeven voor een Asset die wordt gemaakt en gepubliceerd via v2-api's. De eigenschappen moeten allemaal zichtbaar zijn, maar op iets andere locaties nu.  


## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Concepten voor inhouds beveiliging, zelf studies en hand leidingen

### <a name="concepts"></a>Concepten

- [Uw inhoud beveiligen met Media Services dynamische versleuteling](content-protection-overview.md)
- [Ontwerp van een inhoudsbeveiligingssysteem van een multi-DRM met toegangsbeheer](design-multi-drm-system-with-access-control.md)
- [Media Services V3 met PlayReady-licentie sjabloon](playready-license-template-overview.md)
- [Overzicht van Media Services V3 met Widevine-licentie sjabloon](widevine-license-template-overview.md)
- [Vereisten voor en configuratie van Apple FairPlay-licenties](fairplay-license-overview.md)
- [Streaming-beleid](streaming-policy-concept.md)
- [Beleid voor inhouds sleutels](content-key-policy-concept.md)

### <a name="tutorials"></a>Zelfstudies

[Quickstart: De portal gebruiken om inhoud te versleutelen](encrypt-content-quickstart.md)

### <a name="how-to-guides"></a>Handleidingen

- [Een ondertekeningssleutel ophalen uit het bestaand beleid](get-content-key-policy-dotnet-howto.md)
- [Offline FairPlay streaming voor iOS met Media Services v3](offline-fairplay-for-ios.md)
- [Offline Widevine streaming voor Android met Media Services v3](offline-widevine-for-android.md)
- [Offline PlayReady streaming voor Windows 10 met Media Services v3](offline-plaready-streaming-for-windows-10.md)

## <a name="samples"></a>Voorbeelden

U kunt ook [de v2-en V3-code vergelijken in de code voorbeelden](migrate-v-2-v-3-migration-samples.md).

## <a name="tools"></a>Hulpprogramma's

- [hulp programma v3 Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer)
- [v2 Azure Media Services Explorer-hulp programma](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)
