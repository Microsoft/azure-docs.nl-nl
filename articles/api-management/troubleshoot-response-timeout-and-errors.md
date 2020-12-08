---
title: Problemen met de time-out en fouten van client antwoorden oplossen met API Management
description: Problemen oplossen met onregelmatige verbindings fouten en verwante latentie problemen in API Management
author: vladvino
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: apimpm
ms.service: api-management
ms.openlocfilehash: 770a8191b1b07a7ebc779b84f443ae96d66d1c97
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841427"
---
# <a name="troubleshooting-client-response-timeouts-and-errors-with-api-management"></a>Problemen met de time-out en fouten van client antwoorden oplossen met API Management

Dit artikel helpt u bij het oplossen van onregelmatige verbindings fouten en verwante latentie problemen in [Azure API Management](./api-management-key-concepts.md). Dit artikel bevat met name informatie en probleem oplossing voor de uitputting van de SNAT-poorten (bron adres netwerk Translation). Als u meer hulp nodig hebt, neemt u contact op met de Azure-experts bij [Azure Community-ondersteuning](https://azure.microsoft.com/support/community/) of een ondersteunings aanvraag indienen bij [Azure-ondersteuning](https://azure.microsoft.com/support/options/).

## <a name="symptoms"></a>Symptomen

Client toepassingen die Api's aanroepen via uw API Management-service (APIM) kunnen een of meer van de volgende symptomen ondervinden:

* Terugkerende HTTP 500-fouten
* Time-outfout berichten

Deze symptomen kunnen zich voordoen als instanties van `BackendConnectionFailure` in uw [Azure monitor-bron logboeken](../azure-monitor/platform/resource-logs.md).

## <a name="cause"></a>Oorzaak

Dit patroon van de symptomen treedt vaak op als gevolg van de limieten voor Network Address Translation (SNAT) bij uw APIM-service.

Wanneer een client een van uw APIM-Api's aanroept, opent Azure API Management service een SNAT-poort voor toegang tot uw back-end-API. Zoals beschreven in [uitgaande verbindingen in azure](../load-balancer/load-balancer-outbound-connections.md), gebruikt azure bron Network Address Translation (SNAT) en een Load Balancer (niet zichtbaar voor klanten) om te communiceren met eind punten buiten Azure in de open bare IP-adres ruimte, evenals de eind punten intern naar Azure die geen [Virtual Network Service-eind](../virtual-network/virtual-network-service-endpoints-overview.md)punten gebruiken. Deze situatie is alleen van toepassing op back-end-Api's die beschikbaar zijn op open bare Ip's.

Elk exemplaar van API Management service krijgt in eerste instantie een vooraf toegewezen aantal SNAT-poorten. Deze limiet is van invloed op het openen van verbindingen met dezelfde combi natie van host en poort. Er worden SNAT-poorten gebruikt wanneer u herhaaldelijk aanroepen naar dezelfde combi natie van adres en poort hebt. Zodra een SNAT-poort is vrijgegeven, is de poort beschikbaar voor hergebruik als dat nodig is. In het Azure-netwerk load balancer worden alleen SNAT-poorten van gesloten verbindingen hersteld nadat u vier minuten hebt gewacht.

Een snelle opeenvolging van client aanvragen voor uw Api's kan de vooraf toegewezen quota van SNAT-poorten uitgeput raken als deze poorten niet worden gesloten en snel genoeg worden gerecycled, zodat uw APIM-service niet tijdig client aanvragen verwerkt.

## <a name="mitigations-and-solutions"></a>Beperkingen en oplossingen

Voor het oplossen van het probleem van de SNAT-poort ontstaat, moet u eerst de prestaties van uw back-end-services vaststellen en optimaliseren.

Algemene strategieën voor het beperken van de uitputting van de SNAT-poort worden besproken in fouten bij het [oplossen van problemen met uitgaande verbindingen](../load-balancer/troubleshoot-outbound-connection.md) van *Azure Load Balancer* documentatie. Van deze strategieën zijn de volgende van toepassing op API Management.

### <a name="scale-your-apim-instance"></a>Uw APIM-instantie schalen

Aan elk API Management-exemplaar is een aantal SNAT-poorten toegewezen, op basis van APIM-eenheden. U kunt extra SNAT-poorten toewijzen door uw API Management-exemplaar te schalen met extra eenheden. Zie [uw API Management-service schalen](upgrade-and-scale.md#scale-your-api-management-service) voor meer informatie

> [!NOTE]
> Het SNAT-poort gebruik is momenteel niet beschikbaar als meet waarde voor het automatisch schalen van API Management eenheden.

### <a name="use-multiple-ips-for-your-backend-urls"></a>Meerdere IP-adressen gebruiken voor uw back-end-Url's

Elke verbinding van uw APIM-exemplaar met dezelfde doel-IP en doel poort van uw back-end-service gebruikt een SNAT-poort, om een afzonderlijke verkeers stroom te kunnen onderhouden. Zonder verschillende SNAT-poorten voor het retour verkeer van uw achtergrond service, zou APIM geen enkele manier hebben om een reactie van een andere te scheiden.

Omdat de SNAT-poorten opnieuw kunnen worden gebruikt als het doel-IP-of doel poort verschilt, is een andere manier om te voor komen dat de SNAT-poort ontstaat door meerdere IP-adressen te gebruiken voor uw back-end-service-Url's.

Zie voor meer informatie [uitgaande proxy Azure Load Balancer](../load-balancer/load-balancer-outbound-connections.md).

### <a name="place-your-apim-and-backend-service-in-the-same-vnet"></a>Plaats uw APIM-en back-end-service in hetzelfde VNet

Als uw back-end-API wordt gehost op een Azure-service die *service-eind punten* ondersteunt, zoals app service, kunt u storingen in de SNAT-poort voor komen door uw APIM-exemplaar en de back-end-service in hetzelfde virtuele netwerk te plaatsen en deze weer te geven via [service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md) of [persoonlijke eind punten](../private-link/private-endpoint-overview.md). Wanneer u een gemeen schappelijke VNet gebruikt en service-eind punten in het integratie subnet plaatst, wordt uitgaand verkeer van uw APIM-exemplaar naar die services omzeild met het Internet, waardoor er geen SNAT-poort beperkingen gelden. Als u een VNet-en persoonlijk eind punt gebruikt, hebt u ook geen uitgaande SNAT-poort problemen voor die bestemming.

Zie [azure API Management gebruiken met virtuele netwerken](api-management-using-with-vnet.md) en [app service integreren met een virtueel Azure-netwerk](../app-service/web-sites-integrate-with-vnet.md)voor meer informatie.

### <a name="place-your-apim-in-a-virtual-network-and-route-outbound-calls-to-azure-firewall"></a>Uw APIM in een virtueel netwerk plaatsen en uitgaande oproepen naar Azure Firewall door sturen

Net als bij het plaatsen van uw APIM-en back-end-services in een virtueel netwerk, kunt u Azure Firewall in een VNet met uw APIM-service gebruiken en vervolgens uitgaande APIM-aanroepen naar Azure Firewall routeren. Er zijn geen SNAT-poorten vereist tussen APIM en Azure Firewall (die zich in hetzelfde VNet bevinden). Voor SNAT-verbindingen met uw back-end-services heeft Azure Firewall 64.000 beschik bare poorten, een veel hoger bedrag dan toegewezen aan APIM-instanties.

Raadpleeg de documentatie van [Azure firewall](../firewall/overview.md) voor meer informatie.

### <a name="consider-response-caching-and-other-backend-performance-tuning"></a>Antwoorden in de cache opslaan en andere back-upprestaties afstemmen

Een andere mogelijke oplossing waarmee u rekening moet houden, is de verwerkings tijd voor uw back-end-Api's verbeteren. Een manier om dit te doen is door bepaalde Api's te configureren met antwoord cache om de latentie te verminderen tussen client toepassingen die uw API aanroepen en uw APIM-back-end te laden.

Zie [cache toevoegen om de prestaties in Azure API management te verbeteren](api-management-howto-cache.md)voor meer informatie.

### <a name="consider-implementing-access-restriction-policies"></a>Overweeg het implementeren van het beleid voor toegangs restricties

Als het zinvol is voor uw bedrijfs scenario, kunt u toegangs beperkings beleid implementeren voor uw API Management-product. Het `rate-limit-by-key` beleid kan bijvoorbeeld worden gebruikt om het pingen van het API-gebruik per sleutel te voor komen door de oproep frequentie per opgegeven periode te beperken.

Zie [API Management restrictie beleid voor toegang](api-management-access-restriction-policies.md) voor meer informatie.

## <a name="see-also"></a>Zie ook

* [Azure Load Balancer: mislukte problemen met uitgaande verbindingen](../load-balancer/troubleshoot-outbound-connection.md)
* [Azure App Service: oplossen van problemen met terugkerende uitgaande verbindings fouten](../app-service/troubleshoot-intermittent-outbound-connection-errors.md)
