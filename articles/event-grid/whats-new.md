---
title: Wat is nieuw? Releaseopmerkingen voor Azure Event Grid
description: Ontdek wat er nieuw is in Azure Event Grid, zoals de laatste opmerkingen bij de release, bekende problemen, opgeloste problemen, verminderde functionaliteit en aankomende wijzigingen.
ms.topic: overview
ms.date: 07/23/2020
ms.openlocfilehash: da0b26e4f163f428e6955a37636ceb19bb34abc5
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107530"
---
# <a name="whats-new-in-azure-event-grid"></a>Wat is er nieuw in Azure Event Grid?

>U kunt een melding krijgen wanneer deze pagina is bijgewerkt door de URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Event+Grid%22&locale=en-us` te kopiëren en plakken in uw ![pictogram van RSS-feedlezer](./media/whats-new/feed-icon-16x16.png) feedlezer.

Azure Event Grid wordt doorlopend verder verbeterd. Om u op de hoogte te houden van de nieuwste ontwikkelingen, biedt dit artikel u informatie over:

- De nieuwste releases
- Bekende problemen
- Opgeloste fouten
- Afgeschafte functionaliteit
- Plannen voor wijzigingen

## <a name="600-2020-06"></a>6.0.0 (2020-06)
- Ondersteuning toevoegen voor nieuwe, algemeen beschikbare (GA) versie van service-API 2020-06-01.
- De nieuwe functies die nu in GA zijn:
    - [Invoertoewijzingen](input-mappings.md)
    - [Aangepast invoerschema](input-mappings.md)
    - [Cloudgebeurtenisschema V10](cloud-event-schema.md)
    - [Service Bus-onderwerp als bestemming](handler-service-bus.md)
    - [Azure-functie als bestemming](handler-functions.md)
    - [Webhookbatches](./edge/delivery-output-batching.md)
    - [Beveiligde webhook (Azure Active Directory-ondersteuning)](secure-webhook-delivery.md)
    - [Filteren op IP](configure-firewall.md)
    - [Ondersteuning Private Link-service](configure-private-endpoints.md)
    - [Schema voor levering van gebeurtenissen](event-schema.md)

## <a name="532-preview-2020-05"></a>5.3.2-preview (2020-05)
- Deze release bevat aanvullende bugfixes om de kwaliteit te verbeteren.
- Als versie 5.3.1-preview komt deze release overeen met de API-versie 2020-04-01-preview, die de volgende nieuwe functionaliteiten bevat: 
    - [Ondersteuning voor IP-filtering bij het publiceren van gebeurtenissen naar domeinen en onderwerpen](configure-firewall.md)
    - [Partneronderwerpen](./partner-events-overview.md)
    - [Systeemonderwerpen als bijgehouden resources in de Azure-portal](system-topics.md)
    - [Levering van gebeurtenissen met beheerde service-identiteit](managed-service-identity.md) 
    - [Ondersteuning Private Link-service](configure-private-endpoints.md)

## <a name="531-preview-2020-04"></a>5.3.1-preview (2020-04)
- Deze release bevat verschillende bugfixes om de kwaliteit te verbeteren.
- Als versie 5.3.0-preview komt deze release overeen met de API-versie 2020-04-01-preview, die de volgende nieuwe functionaliteiten bevat: 
    - [Ondersteuning voor IP-filtering bij het publiceren van gebeurtenissen naar domeinen en onderwerpen](configure-firewall.md)
    - [Partneronderwerpen](./partner-events-overview.md)
    - [Systeemonderwerpen als bijgehouden resources in de Azure-portal](system-topics.md)
    - [Levering van gebeurtenissen met beheerde service-identiteit](managed-service-identity.md) 
    - [Ondersteuning Private Link-service](configure-private-endpoints.md)

## <a name="530-preview-2020-03"></a>5.3.0-preview (2020-03)
- We introduceren nieuwe functies naast de functies die al zijn toegevoegd in versie 5.2.0-preview. 
- Als versie 5.2.0-preview komt deze release overeen met de API-versie 2020-04-01-preview.
- De release voegt ondersteuning toe voor de volgende nieuwe functies: 
    - [Ondersteuning voor IP-filtering bij het publiceren van gebeurtenissen naar domeinen en onderwerpen](configure-firewall.md)
    - [Partneronderwerpen](./partner-events-overview.md)
    - [Systeemonderwerpen als bijgehouden resources in de Azure-portal](system-topics.md)
    - [Levering van gebeurtenissen met beheerde service-identiteit](managed-service-identity.md) 
    - [Ondersteuning Private Link-service](configure-private-endpoints.md)

## <a name="520-preview-2020-01"></a>5.2.0-preview (2020-01)
- Deze release komt overeen met de API-versie 2020-04-01-preview.
- De release voegt ondersteuning toe voor de volgende nieuwe functionaliteit:
    - [Ondersteuning voor IP-filtering bij het publiceren van gebeurtenissen naar domeinen en onderwerpen](configure-firewall.md)

## <a name="500-2019-05"></a>5.0.0 (2019-05)
- Deze release komt overeen met de API-versie `2019-06-01`.
- De release voegt ondersteuning toe voor de volgende nieuwe functies:
    * [Domeinen](event-domains.md)
    * Paginering en zoekfilter voor bewerkingen op resourcelijsten. Zie [Onderwerpen: op abonnement weergeven](/rest/api/eventgrid/version2020-10-15-preview/partnernamespaces/listbysubscription) voor een voorbeeld.
    * [Service Bus-wachtrij als bestemming](handler-service-bus.md)
    * [Geavanceerd filteren](event-filtering.md#advanced-filtering)

## <a name="410-preview-2019-03"></a>4.1.0-preview (2019-03)
- Deze release komt overeen met de API-versie 2019-02-01-preview.
- De release voegt ondersteuning toe voor de volgende nieuwe functies:
    * Paginering en zoekfilter voor bewerkingen op resourcelijsten. Zie [Onderwerpen: op abonnement weergeven](/rest/api/eventgrid/version2020-10-15-preview/partnernamespaces/listbysubscription) voor een voorbeeld.
    * [Domeinonderwerpen handmatig maken/verwijderen](how-to-event-domains.md)
    * [Service Bus-wachtrij als bestemming](handler-service-bus.md)

## <a name="400-2018-12"></a>4.0.0 (2018-12)
- Deze release komt overeen met de stabiele API-versie `2019-01-01`.
- De release ondersteunt GA (algemene beschikbaarheid) van de volgende functies met betrekking tot gebeurtenisabonnementen:
    * [Bestemming voor onbestelbare berichten](manage-event-delivery.md)
    * [Azure Storage-wachtrij als bestemming](handler-storage-queues.md)
    * [Azure Relay: hybride verbinding als bestemming](handler-relay-hybrid-connections.md)
    * [Handmatig validatie van handshake](webhook-event-delivery.md)
    * [Ondersteuning voor beleid voor opnieuw proberen](delivery-and-retry.md)
- Functies die nog in preview zijn (zoals [Event Grid-domeinen](event-domains.md) of [ondersteuning voor geavanceerde filters](event-filtering.md#advanced-filtering) zijn nog steeds toegankelijk via de 3.0.1-preview-versie van de SDK.

## <a name="301-preview-2018-10"></a>3.0.1-preview (2018-10)
- Is afhankelijk van [10.0.3-versie van Newton Soft NuGet-pakket](https://www.nuget.org/packages/Newtonsoft.Json/10.0.3).

## <a name="300-preview-2018-10"></a>3.0.0-preview (2018-10)
- Deze release is een preview-SDK voor de nieuwe functies die zijn geïntroduceerd in de API-versie 2018-09-15-preview. - Deze release bevat ondersteuning voor:
    - [Domeinen en domeinonderwerpen](event-domains.md)
    - Introductie van [verloopdatum voor gebeurtenisabonnement](concepts.md#event-subscription-expiration)
    - Introductie van [geavanceerd filteren](event-filtering.md#advanced-filtering) voor gebeurtenisabonnementen
    - De stabiele versie van de SDK die is gericht op de API-versie van `2018-01-01` blijft bestaan als versie 1.3.0

## <a name="next-steps"></a>Volgende stappen