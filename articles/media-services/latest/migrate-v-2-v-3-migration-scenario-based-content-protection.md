---
title: Richt lijnen voor migratie van inhouds beveiliging
description: In dit artikel vindt u informatie over het scenario op basis van inhouds beveiliging die u helpt bij het migreren van Azure Media Services v2 naar v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 04/05/2021
ms.author: inhenkel
ms.openlocfilehash: a481759da3f1e7d67accdca7b4322db53abbcb0c
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490944"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Richt lijnen voor migratie op basis van inhouds beveiligings scenario's

![logo van de migratie handleiding](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![migratie stappen 2](./media/migration-guide/steps-4.svg)

In dit artikel vindt u informatie en richt lijnen over de migratie van gebruiks voorbeelden van inhouds beveiliging van de v2 API naar de nieuwe Azure Media Services v3 API.

## <a name="protect-content-in-v3-api"></a>Inhoud in v3 API beveiligen

Gebruik de ondersteuning voor functies met [meerdere sleutels](architecture-design-multi-drm-system.md) in de nieuwe v3 API.

Zie concepten voor inhouds beveiliging, zelf studies en hand leidingen aan het einde van dit artikel voor specifieke stappen.

> [!NOTE]
> In de rest van dit artikel wordt beschreven hoe u uw v2-inhouds beveiliging kunt migreren naar V3 met .NET.  Als u instructies of voorbeeld code voor een andere taal of methode nodig hebt, kunt u een GitHub-probleem voor deze pagina maken.

## <a name="v3-visibility-of-v2-assets-streaminglocators-and-properties"></a>V3-zicht baarheid van v2-assets, StreamingLocators en eigenschappen

In de v2-API,, `Assets` `StreamingLocators` en `ContentKeys` zijn gebruikt om de inhoud van uw stream te beveiligen. Wanneer u migreert naar de V3 API, wordt uw v2 API `Assets` , `StreamingLocators` en `ContentKeys` worden alle gegevens automatisch weer gegeven in de V3 API, en zijn alle data die er beschikbaar zijn voor u toegankelijk.

U kunt de eigenschappen van v2-entiteiten echter niet *bijwerken* via de V3 API die in v2 is gemaakt.

Als u inhoud die is opgeslagen op v2-entiteiten wilt bijwerken, wijzigen of wijzigen, moet u deze bijwerken met de v2 API of nieuwe v3 API-entiteiten maken om ze te migreren.

## <a name="asset-identifier-differences"></a>Verschillen activa-id's

Als u wilt migreren, hebt u toegang tot eigenschappen of inhouds sleutels vanuit uw v2-assets.  Het is belang rijk om te begrijpen dat de v2-API gebruikmaakt `AssetId` van de as the primary Identification Key, maar de nieuwe v3 API maakt gebruik van de *Azure resource management-naam* van de entiteit als de primaire id.  (De v2 `Asset.Name` -eigenschap wordt niet gebruikt als een unieke id.) Met de V3 API wordt de naam van uw v2-Asset nu weer gegeven als `Asset.Description` .

Als u bijvoorbeeld eerder een v2-activum hebt met de ID van `nb:cid:UUID:8cb39104-122c-496e-9ac5-7f9e2c2547b8` , is de id nu aan het einde van de GUID `8cb39104-122c-496e-9ac5-7f9e2c2547b8` . Dit wordt weer gegeven wanneer u uw v2-assets via de V3 API aanbiedt.

Activa die zijn gemaakt en gepubliceerd met behulp van de v2 API, hebben zowel a `ContentKeyPolicy` als a `ContentKey` in de V3 API in plaats van een standaard beleid voor de inhouds sleutel op de `StreamingPolicy` .

Zie de documentatie voor het [inhouds sleutel beleid](https://docs.microsoft.com/azure/media-services/latest/drm-content-key-policy-concept) en de documentatie van het [streaming-beleid](https://docs.microsoft.com/azure/media-services/latest/stream-streaming-policy-concept) voor meer informatie.

## <a name="use-azure-media-services-explorer-amse-v2-and-amse-v3-tools-side-by-side"></a>Azure Media Services Explorer (AMSE) v2-en AMSE v3-hulpprogram ma's naast elkaar gebruiken

Gebruik het [hulp programma v2 Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0) samen met het [hulp programma v3 Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) om de gegevens naast elkaar te vergelijken voor een Asset dat is gemaakt en gepubliceerd via v2-api's. De eigenschappen moeten allemaal zichtbaar zijn, maar op verschillende locaties.

## <a name="use-the-net-content-protection-migration-sample"></a>Het voor beeld voor de migratie van .NET Content Protection gebruiken

U kunt een code voorbeeld vinden om de verschillen in Asset-id's te vergelijken met behulp van de [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration) onder ContentProtection in de Media Services code voorbeelden.

## <a name="list-the-streaming-locators"></a>De streaming-Locators weer geven

U kunt een query uitvoeren `StreamingLocators` op de activa die zijn gemaakt in de v2 API met behulp van de nieuwe v3-methode [ListStreamingLocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators) op de Asset-entiteit.  Raadpleeg ook de .NET client SDK-versie van [ListStreamingLocatorsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?view=azure-dotnet&preserve-view=true)

De resultaten van de- `ListStreamingLocators` methode bieden u de `Name` en `StreamingLocatorId` van de Locator samen met de `StreamingPolicyName` .

## <a name="find-the-content-keys"></a>De inhouds sleutels zoeken

`ContentKeys` `StreamingLocators` U kunt de [StreamingLocator. ListContentKeysAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?view=azure-dotnet&preserve-view=true) -methode aanroepen om de gebruikt met uw te vinden.  

Voor meer informatie over de beveiliging van inhoud in de V3 API raadpleegt [u het artikel Beveilig uw inhoud met Media Services dynamische versleuteling.](https://docs.microsoft.com/azure/media-services/latest/drm-content-protection-concept)

## <a name="change-the-v2-contentkeypolicy-keeping-the-same-contentkey"></a>De v2-ContentKeyPolicy die dezelfde ContentKey houdt, wijzigen

U moet eerst de publicatie ongedaan maken (alle stream-Locators verwijderen) op het activum via de v2-SDK. U doet dit als volgt:

1. De Locator verwijderen.
1. Koppel de `ContentKeyAuthorizationPolicy` .
1. Koppel de `AssetDeliveryPolicy` .
1. Koppel de `ContentKey` .
1. Verwijder de `ContentKey` .
1. Maak een nieuw `StreamingLocator` in v3 met behulp van een v3 `StreamingPolicy` en `ContentKeyPolicy` , waarbij u de specifieke inhouds sleutel-id en sleutel waarde opgeeft die nodig zijn.

> [!NOTE]
> Het is mogelijk om de v2-Locator te verwijderen met behulp van de V3 API, maar Hiermee wordt de inhouds sleutel of het beleid voor de inhouds sleutel niet verwijderd als deze zijn gemaakt in de v2-API.

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Concepten voor inhouds beveiliging, zelf studies en hand leidingen

### <a name="concepts"></a>Concepten

- [Uw inhoud beveiligen met Media Services dynamische versleuteling](drm-content-protection-concept.md)
- [Ontwerp van een inhoudsbeveiligingssysteem van een multi-DRM met toegangsbeheer](architecture-design-multi-drm-system.md)
- [Media Services V3 met PlayReady-licentie sjabloon](drm-playready-license-template-concept.md)
- [Overzicht van Media Services V3 met Widevine-licentie sjabloon](drm-widevine-license-template-concept.md)
- [Vereisten voor en configuratie van Apple FairPlay-licenties](drm-fairplay-license-overview.md)
- [Streaming-beleid](stream-streaming-policy-concept.md)
- [Beleid voor inhouds sleutels](drm-content-key-policy-concept.md)

### <a name="tutorials"></a>Zelfstudies

[Quickstart: De portal gebruiken om inhoud te versleutelen](drm-encrypt-content-how-to.md)

### <a name="how-to-guides"></a>Handleidingen

- [Een ondertekeningssleutel ophalen uit het bestaand beleid](drm-get-content-key-policy-dotnet-how-to.md)
- [Offline FairPlay streaming voor iOS met Media Services v3](drm-offline-fairplay-for-ios-concept.md)
- [Offline Widevine streaming voor Android met Media Services v3](drm-offline-widevine-for-android.md)
- [Offline PlayReady streaming voor Windows 10 met Media Services v3](drm-offline-playready-streaming-for-windows-10.md)

## <a name="samples"></a>Voorbeelden

- [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration)
- U kunt ook [de v2-en V3-code vergelijken in de code voorbeelden](migrate-v-2-v-3-migration-samples.md).

## <a name="tools"></a>Hulpprogramma's

- [hulp programma v3 Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer)
- [v2 Azure Media Services Explorer-hulp programma](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)
