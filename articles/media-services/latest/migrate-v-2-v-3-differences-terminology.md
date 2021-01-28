---
title: Media Services v3-terminologie en entiteits wijzigingen
description: In dit artikel worden de terminologie verschillen tussen Azure Media Services v2 en V3 beschreven.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: a2348e0578b60c59fd7205037bd42d7bb1e84fae
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953696"
---
# <a name="terminology-and-entity-changes-between-media-services-v2-and-v3"></a>Terminologie en entiteits wijzigingen tussen Media Services v2 en v3

![logo van de migratie handleiding](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![migratie stappen 2](./media/migration-guide/steps-2.svg)

In dit artikel worden de terminologie verschillen tussen Azure Media Services v2 en V3 beschreven.

## <a name="naming-conventions"></a>Naamconventies

Bekijk de naamgevings regels die worden toegepast op Media Services v3-resources. Lees ook [naamgevings-blobs](assets-concept.md#naming)

## <a name="terminology-changes"></a>Terminologiewijzigingen

- Een *Locator* wordt nu een *streaming-Locator* genoemd.
- Een *kanaal* wordt nu een *live gebeurtenis* genoemd.
- Een *programma* wordt nu een *Live uitvoer* genoemd.
- Een *taak* wordt nu een *JobOutput* genoemd, die deel uitmaakt van een taak.

## <a name="entity-changes"></a>Entiteits wijzigingen
| **V2-entiteit**<!-- row --> | **V3-entiteit** | **Hulp** | **Toegankelijk voor v3** | **Bijgewerkt door v3** |
|--|--|--|--|--|
| `AccessPolicy`<!-- row --> | <!-- empty --> |  De entiteit `AccessPolicies` bestaat niet in v3. | Nee | Nee |
| `Asset`<!-- row --> | `Asset` | <!-- empty --> | Ja | Ja |
| `AssetDeliveryPolicy`<!-- row --> | `StreamingPolicy` | <!-- empty --> | Ja | Nee |
| `AssetFile`<!-- row --> | <!-- empty --> |De entiteit `AssetFiles` bestaat niet in v3. Hoewel bestanden (opslag-blobs) die u uploadt, nog steeds als bestanden worden beschouwd.<br/><br/> Gebruik de Azure Storage Api's om in plaats daarvan de blobs in een container op te sommen. Er zijn twee manieren om een trans formatie toe te passen op de bestanden met een taak:<br/><br/>Bestanden die al zijn geüpload naar opslag: de URI bevat de Asset-ID voor taken die moeten worden uitgevoerd op assets in een opslag account.<br/><br/>Bestanden die moeten worden geüpload tijdens de trans formatie en het taak proces: de Asset wordt gemaakt in de opslag, een SAS-URL wordt geretourneerd, bestanden worden geüpload naar opslag en vervolgens wordt de trans formatie toegepast op de bestanden. | Nee | Nee |
| `Channel`<!-- row --> | `LiveEvent` | Live Events vervangt kanalen van de v2 API. Ze beschikken over de meeste functies en hebben meer nieuwe functies, zoals Live transcripties, een stand-by-modus en ondersteuning voor RTMP-opname. <br/><br/>Bekijk [Live-gebeurtenissen in scenario op basis van live streamen](migrate-v-2-v-3-migration-scenario-based-live-streaming.md) | Nee | Nee |
| `ContentKey`<!-- row --> | <!-- empty --> | `ContentKeys` is geen entiteit meer, het is nu een eigenschap van een streaming-Locator.<br/><br/>  In v3 zijn de gegevens van de inhouds sleutel gekoppeld aan de `StreamingLocator` (voor uitvoer versleuteling) of de Asset zelf (voor opslag versleuteling aan de client zijde). | Ja | Nee |
| `ContentKeyAuthorizationPolicy`<!-- row --> | `ContentKeyPolicy` | <!-- empty --> | Ja | Nee |
| `ContentKeyAuthorizationPolicyOption` <!-- row --> | <!-- empty --> |  `ContentKeyPolicyOptions` zijn opgenomen in de `ContentKeyPolicy` . | Ja | Nee |
| `IngestManifest`<!-- row --> | <!-- empty --> | De entiteit `IngestManifests` bestaat niet in v3. Het uploaden van bestanden in v3 omvat de Azure Storage-API. Assets worden eerst gemaakt en vervolgens worden de bestanden geüpload naar de gekoppelde opslag container. Er zijn veel manieren om gegevens op te halen in een Azure Storage-container die in plaats daarvan kan worden gebruikt. `JobInputHttp` biedt ook een manier om een taak invoer van een bepaalde URL te downloaden, indien gewenst. | Nee | Nee |
| `IngestManifestAsset`<!-- row --> | <!-- empty --> | Er zijn veel manieren om gegevens op te halen in een Azure Storage-container die in plaats daarvan kan worden gebruikt. `JobInputHttp` biedt ook een manier om een taak invoer van een bepaalde URL te downloaden, indien gewenst. | Nee | Nee |
| `IngestManifestFile`<!-- row --> | <!-- empty --> | Er zijn veel manieren om gegevens op te halen in een Azure Storage-container die in plaats daarvan kan worden gebruikt. `JobInputHttp` biedt ook een manier om een taak invoer van een bepaalde URL te downloaden, indien gewenst. | Nee | Nee |
| `Job`<!-- row --> | `Job` | Maak een `Transform` voordat u een maakt `Job` . | Nee | Nee |
| `JobTemplate`<!-- row --> | `Transform` | Gebruik `Transform` in plaats daarvan een. Een trans formatie is een afzonderlijke entiteit van een taak en kan worden hergebruikt. | Nee | Nee |
| `Locator`<!-- row --> | `StreamingLocator` | <!--empty --> | Ja | Nee |
| `MediaProcessor`<!-- row --> | <!-- empty --> | In plaats van te zoeken `MediaProcessor` op naam, gebruikt u de gewenste voor instelling wanneer u een trans formatie definieert. De voor instelling die wordt gebruikt, bepaalt de media processor die wordt gebruikt door het taak systeem. Zie de onderwerpen over het coderen van [op scenario's gebaseerde code ring](migrate-v-2-v-3-migration-scenario-based-encoding.md). <!--Probably needs a link to its own article so customers know Indexerv1 maps to AudioAnalyzerPreset in basic mode, etc.--> | No | N.V.T. (alleen-lezen in v2) |
| `NotificationEndPoint`<!-- row --> | <!--empty --> | Meldingen in v3 worden verwerkt via Azure Event Grid. De `NotificationEndpoint` wordt vervangen door de registratie van het event grid-abonnement, waarmee ook de configuratie wordt ingekapseld voor de typen meldingen die moeten worden ontvangen (die in v2 zijn verwerkt door de `JobNotificationSubscription` van de taak, het `TaskNotificationSubscription` van de taken en de telemetrie `ComponentMonitoringSetting` ). De versie van de v2-telemetrie is gesplitst tussen Azure Event Grid en Azure Monitor om in de verbeteringen van het grotere Azure-ecosysteem te passen. | Nee | Nee |
| `Program`<!-- row --> | `LiveOutput` | Live outputs vervangt nu Program Ma's in de V3 API.  | Nee | Nee |
| `StreamingEndpoint`<!-- row --> | `StreamingEndpoint` | Streaming-eind punten blijven voornamelijk hetzelfde. Ze worden gebruikt voor dynamische verpakking, versleuteling en levering van HLS-en streepje-inhoud voor zowel live als on-demand streaming rechtstreeks van oorsprong, hetzij via het CDN. Nieuwe functies zijn onder meer ondersteuning voor betere Azure Monitor integratie en grafieken. |  Ja | Ja |
| `Task`<!-- row --> | `JobOutput` | Vervangen door `JobOutput` (is geen afzonderlijke entiteit meer in de API).  Zie de onderwerpen over het coderen van [op scenario's gebaseerde code ring](migrate-v-2-v-3-migration-scenario-based-encoding.md). | Nee | Nee |
| `TaskTemplate`<!-- row --> | `TransformOutput` | Vervangen door `TransformOutput` (is geen afzonderlijke entiteit meer in de API). Zie de onderwerpen over het coderen van [op scenario's gebaseerde code ring](migrate-v-2-v-3-migration-scenario-based-encoding.md). | Nee | Nee |
| `Inputs`<!-- row --> | `Inputs` | Invoer en uitvoer zijn nu op taak niveau. Zie de onderwerpen over het coderen van [op scenario's gebaseerde code ring](migrate-v-2-v-3-migration-scenario-based-encoding.md) | Nee | Nee |
| `Outputs`<!-- row --> | `Outputs` | Invoer en uitvoer zijn nu op taak niveau.  In v3 wordt de meta gegevens indeling gewijzigd van XML naar JSON.  Live-uitvoer starten zodra ze zijn gemaakt en stoppen wanneer ze worden verwijderd. Zie de onderwerpen over het coderen van [op scenario's gebaseerde code ring](migrate-v-2-v-3-migration-scenario-based-encoding.md) | Nee | Nee |


| **Andere wijzigingen** | **Offload**  | **V3** |
|---|---|---|
| **Storage** <!--new row --> |||
| Storage <!--new row --> | | De V3 Sdk's zijn nu losgekoppeld van de opslag-SDK, waarmee u meer controle hebt over de versie van de opslag-SDK die u wilt gebruiken en voor komt versie problemen.                      |
| **Codering** <!--new row --> |||
| Bitsnelheid voor versleuteling <!--new row --> | bitsnelheden gemeten in kbps: 128 (kbps)| bits per seconde, bijvoorbeeld: 128000 (bits/seconde)|
| DRM-FairPlay coderen <!--new row --> | In Media Services v2 kunt u de initialisatie vector (IV) opgeven. | In Media Services v3 kan de FairPlay IV niet worden opgegeven.|
| Premium encoder <!--new row --> | Premium encoder en verouderde Indexer| De [Premium encoder](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset) en de verouderde [Media Analytics-processors](https://docs.microsoft.com/azure/media-services/previous/legacy-components) (Azure Media Services indexer 2, Face Redactor, enzovoort) zijn niet toegankelijk via v3. Er is ondersteuning toegevoegd voor audio kanaal toewijzing aan de standaard encoder.  Zie [Audio in de Media Services code ring Swagger-documentatie](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json).  | Zie de onderwerpen over het coderen van [op scenario's gebaseerde code ring](migrate-v-2-v-3-migration-scenario-based-encoding.md) |
| **Transformaties en taken** <!--new row -->|||
| Op taak gebaseerde verwerking HTTPS <!--new row --> |<!-- empty -->| Voor taak verwerking op basis van een bestand kunt u een HTTPS-URL gebruiken als invoer. U hoeft inhoud niet al in azure te hebben opgeslagen en u hoeft geen assets te maken. |
| ARM-sjablonen voor taken <!--new row --> | Er zijn geen ARM-sjablonen aanwezig in v2. | Een trans formatie kan worden gebruikt om herbruikbare configuraties te bouwen, Azure Resource Manager-sjablonen te maken en verwerkings instellingen te isoleren tussen meerdere klanten of tenants. |
| **Live-gebeurtenissen** <!--new row --> |||
| Streaming-eindpunt <!--new row --> | Een streaming-eind punt vertegenwoordigt een streaming-service die inhoud rechtstreeks kan leveren aan een client speler of een Content Delivery Network (CDN) voor verdere distributie. | Streaming-eind punten blijven voornamelijk hetzelfde. Ze worden gebruikt voor dynamische verpakking, versleuteling en levering van HLS-en streepje-inhoud voor zowel live als on-demand streaming rechtstreeks van oorsprong, hetzij via het CDN.  Nieuwe functies zijn onder meer ondersteuning voor betere Azure Monitor integratie en grafieken. |
| Live Event channels <!--new row --> | Kanalen zijn verantwoordelijk voor het verwerken van live streaming-inhoud. Een kanaal biedt een invoer eindpunt (opname-URL) dat u vervolgens verstrekt aan een live-transcodeer. Het kanaal ontvangt Live-invoer stromen van de Live-transcodeer en maakt deze beschikbaar voor streaming via een of meer streaming-eind punten. Kanalen bieden ook een preview-eind punt (Preview-URL) die u gebruikt om uw stroom te bekijken en te valideren vóór verdere verwerking en levering.| Live Events vervangt kanalen van de v2 API. Ze beschikken over de meeste functies en hebben meer nieuwe functies, zoals Live transcripties, een stand-by-modus en ondersteuning voor RTMP-opname. |
| Program ma's voor Live-gebeurtenissen <!--new row --> | Met een kanaal kunt u het publiceren en opslaan van segmenten in een livestream beheren. Kanalen beheren programma's. De kanaal-en programma relatie is vergelijkbaar met traditionele media, waarbij een kanaal een constante stroom inhoud heeft en een programma een bepaalde time-outgebeurtenis op dat kanaal heeft. U kunt het aantal uren opgeven dat u de opgenomen inhoud voor het programma wilt laten blijven door de eigenschap in te stellen `ArchiveWindowLength` . Deze waarde kan worden ingesteld van minimaal 5 minuten tot maximaal 25 uur.| Live outputs vervangt nu Program Ma's in de V3 API. |
| Lengte van live-gebeurtenis <!--new row --> |<!-- empty -->| U kunt live-gebeurtenissen 24/7 streamen wanneer u Media Services gebruikt voor het coderen van een enkele bitrate bijdrage-feed in een uitvoer stroom met meerdere bitrates.|
| Latentie van live gebeurtenis <!--new row --> |<!-- empty -->| Nieuwe ondersteuning voor live streaming met lage latentie voor Live-gebeurtenissen. |
| Preview van Live Event <!--new row --> |<!-- empty -->| Preview van Live Event ondersteunt dynamische pakketten en dynamische versleuteling. Hiermee wordt de beveiliging van inhoud op de preview-versie en de verstreep-en HLS-verpakking ingeschakeld. |
| RTMP-live gebeurtenis <!--new row --> |<!-- empty-->| Verbeterde RTMP-ondersteuning met verbeterde stabiliteit en meer ondersteuning voor broncode encoder. |
| RTMP-beveiligde opname van live gebeurtenis <!--new row --> | | Wanneer u een live gebeurtenis maakt, krijgt u vier opname-Url's. De 4 opname-Url's zijn bijna identiek, hebben hetzelfde streaming `AppId` -token. alleen het poort nummer deel verschilt. Twee van de Url's zijn primaire en back-ups voor RTMP.| 
| Live Event transcriptie <!--new row --> |<!-- empty--> | De Azure media-service levert video, audio en tekst in verschillende protocollen. Wanneer u uw Live Stream publiceert met MPEG-DASH of HLS/CMAF en vervolgens video en audio, levert onze service de getranscribeerde tekst in IMSC 1.1 compatibele TTML.|
| Modus stand-by Live Event <!--new row --> | Er is geen standby-modus voor v2. | De stand-by-modus is een nieuwe v3-functie die u helpt bij het beheren van warme Pools met Live-gebeurtenissen. Klanten kunnen nu een live gebeurtenis in de stand-by-modus met lagere kosten starten voordat ze overstappen naar de actieve status. Dit verbetert de start tijd van het kanaal en verlaagt de kosten van het gebruik van Hot-Pluggers voor snellere start-ups. |
| Facturering van Live-gebeurtenissen <!--new row --> | <!-- empty-->| Facturering van Live-gebeurtenissen is gebaseerd op Live Channel meters. |
| Live uitvoer <!--new row --> | Program ma's moest worden gestart na het maken. | Live-uitvoer starten zodra ze zijn gemaakt en stoppen wanneer ze worden verwijderd. |

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
