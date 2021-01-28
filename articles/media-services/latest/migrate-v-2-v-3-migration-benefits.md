---
title: De voor delen van migreren naar Media Services API v3
description: In dit artikel vindt u een overzicht van de voor delen van het migreren van Media Services v2 naar v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 70f64813546c66c0f9e3533e09de192315f75600
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955068"
---
# <a name="step-1---understand-the-benefits-of-migrating-to-media-services-api-v3"></a>Stap 1: inzicht krijgen in de voor delen van migreren naar Media Services API v3

![logo van de migratie handleiding](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![migratie stappen 2](./media/migration-guide/steps-1.svg)

We raden u aan om versie 2020-05-01 van de Azure Media Services v3 API nu te gebruiken om de voor delen te verkrijgen, omdat nieuwe functies, functionaliteit en prestatie optimalisaties zijn alleen beschikbaar in de huidige v3 API.

U kunt de API-versie wijzigen in de portal, de nieuwste Sdk's, de meest recente CLI en REST API met de juiste versie teken reeks.

Er zijn belang rijke verbeteringen aangebracht in Media Services met v3.  

## <a name="benefits-of-media-services-v3"></a>Voor delen van Media Services v3

| **V3-functie** | **Voordeel** |
| --- | --- |
| **Azure-portal** | |
| Azure Portal updates | Het Azure Portal is bijgewerkt met het beheer van v3 API-entiteiten. Klanten kunnen de portal gebruiken om live streamen te starten, v3-transformatie taken te verzenden, beleids regels voor inhouds beveiliging te beheren, streaming-eind punten, API-toegang verkrijgen, gekoppelde opslag accounts beheren en bewakings taken uitvoeren. |
| **Accounts en opslag** | |
| Op rollen gebaseerd toegangs beheer (RBAC) van Azure | Klanten kunnen nu hun eigen rollen definiëren en toegang tot elke entiteit beheren in de Media Services ARM-API. Zo kunt u de toegang tot resources beheren via AAD-accounts. |
| Beheerde identiteiten | Beheerde identiteiten voor komen dat ontwikkel aars referenties kunnen beheren door een identiteit op te geven voor de Azure-resource in azure AD. Zie [hier](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)voor meer informatie over beheerde identiteiten. |
| Ondersteuning voor persoonlijke koppelingen | Klanten hebben toegang tot Media Services-eind punten voor key delivery, LiveEvents en StreamingEndpoints via een PrivateEndpoint op het VNet. |
| [Klant-beheerd sleutels](concept-use-customer-managed-keys-byok.md) of uw eigen Key (BYOK)-ondersteuning | Klanten kunnen de gegevens in hun Media Services account versleutelen met behulp van een sleutel in hun Azure Key Vault. |
| **Assets** | |
| Een Asset kan meerdere [Stream-Locators](streaming-locators-concept.md) hebben elk met verschillende [dynamische pakketten](dynamic-packaging-overview.md) en dynamische versleutelings instellingen. | Er is een limiet van 100 streaming-Locators toegestaan op elke Asset. Klanten kunnen één exemplaar van de media-inhoud opslaan in de Asset, maar verschillende streaming-Url's delen met verschillende streaming-beleids regels of beveiligings beleid voor inhoud die is gebaseerd op een doel groep.
| **Taak verwerking** ||
| V3 introduceert het concept van [trans formaties](transforms-jobs-concept.md)   voor taak verwerking op basis van bestanden. | Een trans formatie kan worden gebruikt om herbruikbare configuraties te bouwen, Azure Resource Manager-sjablonen te maken en verwerkings instellingen te isoleren tussen meerdere klanten of tenants. |
| Voor taak verwerking op basis van een bestand kunt u een HTTP/S-URL gebruiken als invoer. | U hoeft inhoud niet al in azure te hebben opgeslagen en u hoeft geen invoer assets te maken. |
| **Live-gebeurtenissen** ||
| Premium 1080p Live-gebeurtenissen | Met de nieuwe SKU voor Live Event kunnen klanten Cloud codering ontvangen met de uitvoer naar 1080p in oplossing. |
| Nieuwe ondersteuning voor live streaming met [lage latentie](live-event-latency.md) voor Live-gebeurtenissen. | Hiermee kunnen gebruikers live gebeurtenissen dichter in realtime bekijken dan als deze instelling niet is ingeschakeld. |
| Preview van Live Event ondersteunt [dynamische pakketten](dynamic-packaging-overview.md)   en dynamische versleuteling. | Hiermee wordt Content Protection ingeschakeld voor preview-en DASH-en HLS-pakketten. |
| Live-uitvoer, Program Ma's vervangen | Live output is eenvoudiger te gebruiken dan de entiteit van het programma in de v2-Api's. |
| RTMP-opname voor Live-gebeurtenissen is verbeterd, met ondersteuning voor meer encoders | Verbetert de stabiliteit en biedt de flexibiliteit van de bron encoder. |
| Live-gebeurtenissen kunnen 24x7 streamen | U kunt een live gebeurtenis hosten en uw publiek gedurende langere Peri Oden laten presen. |
| Live transcriptie op live evenementen | Met Live transcriptie kunnen klanten automatisch gesp roken taal in realtime naar tekst overzetten tijdens het verzenden van Live-gebeurtenissen. Dit verbetert de toegankelijkheid van Live-gebeurtenissen aanzienlijk. |
| [Stand-by-modus](live-events-outputs-concept.md#standby-mode) voor Live gebeurtenissen | Live-gebeurtenissen met de status stand-by zijn minder kostbaar dan het uitvoeren van Live-gebeurtenissen. Hierdoor kunnen klanten een set Live-gebeurtenissen onderhouden die klaar zijn om binnen enkele seconden aan de slag te gaan met lagere kosten dan het onderhouden van een set actieve Live-gebeurtenissen. Voor de meeste regio's geldt een gereduceerde prijs voor Live-gebeurtenissen in februari 2021, met de rest om te volgen in april 2021.
|**Inhouds beveiliging** ||
| [Inhouds beveiliging](content-key-policy-concept.md)   ondersteunt functies met meerdere sleutels. | Klanten kunnen nu meerdere inhouds versleutelings sleutels gebruiken op hun streaming-Locators. |
| **Controle** | |
| Ondersteuning voor [Azure EventGrid](reacting-to-media-services-events.md) -meldingen | EventGrid-meldingen zijn uitgebreidere functies. Er zijn meer typen meldingen, bredere SDK-ondersteuning voor het ontvangen van meldingen in uw eigen toepassing en meer bestaande Azure-Services die als gebeurtenis-handlers kunnen fungeren. |
| [Azure Monitor ondersteuning en integratie in het Azure Portal](monitor-events-portal-how-to.md) | Hierdoor kunnen klanten Media Services account quotum gebruik, realtime statistieken van streaming-eind punten en opname-en archief statistieken voor Live-gebeurtenissen visualiseren. Klanten kunnen nu waarschuwingen instellen en de benodigde acties uitvoeren op basis van real-time metrische gegevens. |

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
