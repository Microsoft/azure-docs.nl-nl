---
title: Azure front deur Standard/Premium | Microsoft Docs
description: In dit artikel vindt u een overzicht van de voor deur standaard/Premium van Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: overview
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 574340825567dcd512a5da1b311c57fe12954e34
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030542"
---
# <a name="what-is-azure-front-door-standardpremium-preview"></a>Wat is Azure front deur Standard/Premium (preview)?

> [!IMPORTANT]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Documenten voor de front-deur van Azure](../front-door-overview.md)weer geven.

Azure front deur Standard/Premium is een snelle, betrouw bare en veilige moderne CDN voor Clouds die gebruikmaakt van het wereld wijde Edge-netwerk van micro soft en dat kan worden geïntegreerd met intelligente beveiliging tegen bedreigingen. Het combineert de mogelijkheden van Azure front-deur, Azure Content Delivery Network (CDN) Standard en Azure Web Application firewall (WAF) in één veilig cloud CDN-platform.

Met Azure front deur Standard/Premium kunt u uw wereld wijde consumenten-en bedrijfs toepassingen omzetten in veilige en hoogwaardige, persoonlijke moderne toepassingen met inhoud die een wereld wijd publiek in de buurt van de rand van het netwerk bereikt. Ook kan uw toepassing worden uitgeschaald zonder te worden opgewarmd tijdens het profiteert van de globale HTTP-taak verdeling met directe failover.

   :::image type="content" source="../media/overview/front-door-overview.png" alt-text="Azure front deur Standard/Premium-architectuur" lightbox="../media/overview/front-door-overview-expanded.png":::

Azure front deur Standard/Premium werkt op laag 7 (HTTP/HTTPS-laag) met behulp van het anycast-protocol met Split TCP en het wereld wijde netwerk van micro soft om de wereld wijde connectiviteit te verbeteren. Op basis van uw aangepaste routerings methode met behulp van de ingestelde regels, kunt u ervoor zorgen dat de Azure-front-deur uw client aanvragen doorstuurt naar de snelste en meest beschik bare oorsprong. Een oorsprong van een toepassing is een Internet gerichte service die binnen of buiten Azure wordt gehost. Azure front deur Standard/Premium biedt een reeks methoden voor het routeren van verkeer en de status bewakings opties voor de oorsprong voor verschillende toepassings behoeften en automatische failover-scenario's. Front Door is vergelijkbaar met Traffic Manager en bestand tegen storingen, waaronder het uitvallen van een hele Azure-regio.

Azure front-deur beveiligt ook uw app aan de rand met geïntegreerde firewall beveiliging van webtoepassingen, bot-beveiliging en ingebouwde set DDoS-beveiliging (Distributed Denial of service) van 3 of laag 4. Ook wordt uw persoonlijke back-ends beveiligd met een privé koppelings service. Azure front-deur biedt u de best practice-beveiliging van micro soft op wereld wijde schaal.  

>[!NOTE]
> Azure biedt een pakket volledig beheerde oplossingen voor taakverdeling voor uw scenario's.
>
> * Als u op zoek bent naar een internationale routering op basis van DNS en u voldoet **niet** aan de vereisten voor beëindiging van het TLS-protocol (Transport Layer Security), ('SSL-offload') of aanvragen per HTTP/HTTPS-aanvraag, verwerking via de toepassingslaag, raadpleegt u [Traffic Manager](../../traffic-manager/traffic-manager-overview.md).
> * Als u de taken wilt verdelen tussen uw servers in een regio op de toepassingslaag, raadpleegt u [Application Gateway](../../application-gateway/overview.md)
> * Raadpleeg [Load Balancer](../../load-balancer/load-balancer-overview.md) voor taakverdeling in de netwerklaag.
>
> Uw end-to-end scenario 's kunnen eventueel profiteren van een combinatie van deze oplossingen.
> Zie [Opties voor taakverdeling in Azure](/azure/architecture/guide/technology-choices/load-balancing-overview) voor een vergelijking van de opties voor taakverdeling van Azure.

> [!IMPORTANT]
> Azure front deur Standard/Premium is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="why-use-azure-front-door-standardpremium-preview"></a>Waarom Azure front deur Standard/Premium (preview) gebruiken?

Azure front deur Standard/Premium biedt één uniform platform dat voldoet aan zowel dynamische als statische versnelling met ingebouwde kant-en-klare beveiligings integratie, en een eenvoudig en voorspelbaar prijs model. Met de voor deur kunt u ook de wereld wijde route ring voor uw app definiëren, beheren en bewaken.

Belangrijkste functies die zijn opgenomen in azure front deur Standard/Premium (preview):

- Versnelde toepassings prestaties met behulp van een **[gesplitste op TCP gebaseerd anycast-](../front-door-routing-architecture.md#splittcp)** protocol.

- Intelligent **[Health probe](concept-health-probes.md)** monitoring en taak verdeling tussen **[oorsprong](concept-origin.md)**.

- Definieer uw eigen **[aangepaste domein](how-to-add-custom-domain.md)** met flexibele domein validatie.

- Toepassings beveiliging met geïntegreerde **[Web Application firewall (WAF)](../../web-application-firewall/afds/afds-overview.md)**.

- SSL-offload en geïntegreerd **[certificaat beheer](how-to-configure-https-custom-domain.md)**.

- Beveilig uw oorsprong met **[persoonlijke koppeling](concept-private-link.md)**.  

- Aanpas bare verkeers Routering en optimalisaties via de **[regel reeks](concept-rule-set.md)**.

- **[Ingebouwde rapporten](how-to-reports.md)** met alles-in-één dash board voor zowel de voor deur-als beveiligings patronen.

- **[Real-time bewaking](how-to-monitor-metrics.md)** en waarschuwingen die worden geïntegreerd met Azure-bewaking.

- **[Logboek registratie](how-to-logs.md)** voor elke front-deur aanvraag en mislukte status controles.

- Systeem eigen ondersteuning van end-to-end IPv6-connectiviteit en HTTP/2-protocol.

## <a name="pricing"></a>Prijzen

Azure front deur Standard/Premium heeft twee Sku's, Standard en Premium. Zie [laag vergelijking](tier-comparison.md). Zie [Prijzen van Front Door](https://azure.microsoft.com/pricing/details/frontdoor/) voor informatie over de prijzen. 

## <a name="whats-new"></a>Wat is nieuw?

Abonneer u op de RSS-feed en bekijk de nieuwste updates voor Azure Front Door-onderdelen op de pagina [Azure-updates](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door).

## <a name="next-steps"></a>Volgende stappen

* Lees hoe u [een Front Door maakt](create-front-door-portal.md).
