---
title: Beleid voor inhouds sleutels in Media Services-Azure
description: Dit artikel geeft een uitleg van de beleids regels voor inhouds sleutels en hoe deze worden gebruikt door Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 1333bbcc7e2ea9219d28fcc1e63f21a833c2bc6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709560"
---
# <a name="content-key-policies"></a>Beleid voor inhoudssleutels

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Met Media Services kunt u uw live en on-demand inhoud dynamisch versleutelen met Advanced Encryption Standard (AES-128) of een van de drie belangrijkste Digital Rights Management-systemen (DRM): Microsoft PlayReady, Google Widevine en Apple FairPlay. Media Services biedt ook een service voor het leveren van AES-sleutels en DRM-licenties (PlayReady, Widevine en FairPlay) aan geautoriseerde klanten. 

Als u versleutelings opties wilt opgeven voor uw stream, moet u een [streaming-beleid](streaming-policy-concept.md) maken en dit koppelen aan uw [streaming-Locator](streaming-locators-concept.md). U maakt het [beleid voor inhouds sleutels](/rest/api/media/contentkeypolicies) om te configureren hoe de inhouds sleutel (die beveiligde toegang tot uw [assets](assets-concept.md)biedt) wordt geleverd aan de eind clients. U moet de vereisten (beperkingen) instellen voor het beleid voor de inhouds sleutel waaraan moet worden voldaan om sleutels met de opgegeven configuratie aan clients te kunnen leveren. Het beleid voor inhoudssleutels is niet nodig voor het wissen van streams of downloaden. 

Normaal gesp roken koppelt u het beleid voor inhouds sleutels aan uw [streaming-Locator](streaming-locators-concept.md). U kunt ook het beleid voor de inhouds sleutel binnen een [streaming-beleid](streaming-policy-concept.md) opgeven (bij het maken van een aangepast streaming-beleid voor geavanceerde scenario's). 

## <a name="best-practices-and-considerations"></a>Aanbevolen procedures en overwegingen

> [!IMPORTANT]
> Controleer de volgende aanbevelingen.

* U dient een beperkt aantal beleids regels te ontwerpen voor uw media service-account en deze opnieuw te gebruiken voor uw streaming-Locators wanneer dezelfde opties nodig zijn. Zie [quota's en limieten](limits-quotas-constraints.md)voor meer informatie.
* Beleid voor inhouds sleutels kan worden bijgewerkt. Het kan tot vijf tien minuten duren voordat de sleutel bezorgings caches zijn bijgewerkt en het bijgewerkte beleid wordt opgehaald. 

   Door het beleid bij te werken, wordt uw bestaande CDN-cache overschreven, waardoor er problemen kunnen ontstaan met het afspelen van klanten die inhoud in de cache gebruiken.  
* We raden u aan geen nieuw beleid voor inhouds sleutels voor elke Asset te maken. De belangrijkste voor delen van het delen van hetzelfde beleid voor inhouds sleutels tussen activa die dezelfde beleids opties nodig hebben, zijn:
   
   * Het is eenvoudiger om een klein aantal beleids regels te beheren.
   * Als u updates moet maken voor het beleid voor inhouds sleutels, worden de wijzigingen bijna direct van kracht op alle nieuwe licentie aanvragen.
* Als u een nieuw beleid moet maken, moet u een nieuwe streaming-Locator voor de Asset maken.
* Het wordt aanbevolen Media Services automatisch de inhouds sleutel te genereren. 

   Normaal gesp roken gebruikt u een langdurige sleutel en controleert u of het beleid voor de inhouds sleutel met [Get](/rest/api/media/contentkeypolicies/get). Als u de sleutel wilt ophalen, moet u een afzonderlijke actie methode aanroepen om geheimen of referenties op te halen. Zie het volgende voor beeld.

## <a name="example"></a>Voorbeeld

Als u de sleutel wilt weer geven, gebruikt u `GetPolicyPropertiesWithSecretsAsync` , zoals wordt weer gegeven in het voor beeld [een handtekening sleutel van het bestaande beleid ophalen](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets) .

## <a name="filtering-ordering-paging"></a>Filteren, ordenen, paginering

Zie [filteren, ordenen, pagineren van Media Services entiteiten](entities-overview.md).

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Eigenschappen van het beleid voor de inhouds sleutel van het `Datetime` type zijn altijd in UTC-indeling.
* Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="next-steps"></a>Volgende stappen

* [Gebruik AES-128 dynamische versleuteling en de key delivery service](protect-with-aes128.md)
* [De Digital Rights Management-service gebruiken voor dynamische versleuteling en licentielevering](protect-with-drm.md)
* [Voorbeeld code van basis-AES Clear Key-versleuteling en streaming](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)
