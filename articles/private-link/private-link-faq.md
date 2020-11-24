---
title: Veelgestelde vragen over persoonlijke koppelingen van Azure
description: Meer informatie over persoonlijke Azure-koppelingen.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: 2e5edb0ccf20ab0dba14864487f2b2ddef7d57e7
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544204"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Veelgestelde vragen over persoonlijke koppelingen van Azure

## <a name="private-link"></a>Private Link

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Wat is Azure private endpoint en Azure Private Link service?

- **[Persoonlijk Azure-eind punt](private-endpoint-overview.md)**: Azure persoonlijk eind punt is een netwerk interface waarmee u privé en veilig kunt verbinden met een service die wordt aangestuurd door een persoonlijke Azure-koppeling. U kunt privé-eind punten gebruiken om verbinding te maken met een Azure PaaS-service die een persoonlijke koppeling of een persoonlijke koppelings service ondersteunt.
- **[Persoonlijke Azure-koppelings service](private-link-service-overview.md)**: Azure Private Link service is een service die is gemaakt door een service provider. Op dit moment kan een persoonlijke koppelings service worden gekoppeld aan de front-end-IP-configuratie van een Standard Load Balancer. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Hoe wordt het verkeer verzonden wanneer een persoonlijke koppeling wordt gebruikt?
Verkeer wordt privé verzonden met micro soft backbone. Er wordt geen verbinding met Internet verzonden. Persoonlijke Azure-koppeling slaat geen klant gegevens op.
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>Wat is het verschil tussen een service-eind punt en een persoonlijk eind punt?
- Privé-eind punten verlenen netwerk toegang tot specifieke bronnen achter een bepaalde service die gedetailleerde segmentatie levert. Verkeer kan de service bron van on-premises bereiken zonder gebruik te maken van open bare eind punten.
- Een service-eind punt blijft een openbaar routeerbaar IP-adres.  Een persoonlijk eind punt is een privé-IP in de adres ruimte van het virtuele netwerk waar het persoonlijke eind punt is geconfigureerd.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Wat is de relatie tussen de service voor persoonlijke koppelingen en het persoonlijke eind punt?
Meerdere bron typen voor persoonlijke koppelingen bieden ondersteuning voor toegang via een persoonlijk eind punt. Resources zijn Azure PaaS Services en uw eigen persoonlijke koppelings service. Het is een een-op-veel-relatie. 

Een persoonlijke koppelings service ontvangt verbindingen van meerdere persoonlijke eind punten. Een persoonlijk eind punt maakt verbinding met één privé koppelings service.    

## <a name="private-endpoint"></a>Privé-eindpunt 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Kan ik meerdere persoonlijke eind punten maken in hetzelfde VNet? Kunnen ze verbinding maken met verschillende services? 
Ja. U kunt meerdere persoonlijke eind punten hebben in hetzelfde VNet of subnet. Ze kunnen verbinding maken met verschillende services.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Is er een toegewezen subnet vereist voor privé-eind punten? 
Nee. U hebt geen toegewezen subnet voor privé-eind punten nodig. U kunt een privé-eindpunt-IP-adres kiezen uit elk subnet van het VNet waar uw service is geïmplementeerd.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>Kan een persoonlijk eind punt verbinding maken met de service voor persoonlijke koppelingen tussen Azure Active Directory tenants? 
Ja. Privé-eind punten kunnen verbinding maken met persoonlijke koppelings Services of Azure PaaS over AD-tenants.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>Kan een persoonlijk eind punt verbinding maken met Azure PaaS-resources in azure-regio's?
Ja. Privé-eind punten kunnen verbinding maken met Azure PaaS-resources in azure-regio's.

### <a name="can-i-modify-my-private-endpoint-network-interface-nic-"></a>Kan ik mijn netwerk interface voor het privé-eind punt (NIC) wijzigen?
Wanneer een persoonlijk eind punt wordt gemaakt, wordt er een alleen-lezen NIC toegewezen. Dit kan niet worden gewijzigd en blijft voor de levens cyclus van het persoonlijke eind punt.

## <a name="private-link-service"></a>Private Link-service
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Wat zijn de vereisten voor het maken van een privé koppelings service? 
Uw service back-ends moeten zich in een virtueel netwerk en achter een Standard Load Balancer bevindt.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Hoe kan ik een persoonlijke koppelings service schalen? 
U kunt uw persoonlijke koppelings service op een aantal verschillende manieren schalen: 
- Back-end-Vm's toevoegen aan de groep achter uw Standard Load Balancer 
- Voeg een IP-adres toe aan de persoonlijke koppelings service. We hebben Maxi maal 8 Ip's per privé koppelings service toegestaan.  
- Voeg een nieuwe persoonlijke koppelings service toe aan Standard Load Balancer. We hebben Maxi maal acht Services voor persoonlijke koppelingen per load balancer toegestaan.   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Kan ik mijn Service koppelen aan meerdere persoonlijke eind punten?
Ja. Een service voor persoonlijke koppelingen kan verbindingen van meerdere persoonlijke eind punten ontvangen. Een persoonlijk eind punt kan echter alleen verbinding maken met één privé koppelings service.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Hoe kan ik de bloot stelling van mijn persoonlijke koppelings service controleren?
U kunt de bloot stelling regelen met behulp van de zichtbaarheids configuratie voor de service private link. De zicht baarheid ondersteunt drie instellingen:

- **Geen** : alleen abonnementen met toegang tot Azure RBAC kunnen de service vinden. 
- Alleen **beperkte** abonnementen die zijn goedgekeurd en met toegang tot Azure RBAC, kunnen de service vinden. 
- **Alle** -iedereen kan de service vinden. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Kan ik een persoonlijke koppelings service maken met basis load balancer? 
Nee. Een persoonlijke koppelings service via een basis load balancer wordt niet ondersteund.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Is een toegewezen subnet vereist voor de service voor privé-koppeling? 
Nee. Voor de service voor persoonlijke koppelingen is geen toegewezen subnet vereist. U kunt elk subnet in uw VNet kiezen waar uw service wordt geïmplementeerd.   

### <a name="im-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Ik ben een service provider die gebruikmaakt van een persoonlijke Azure-koppeling. Moet ik ervoor zorgen dat alle klanten een unieke IP-ruimte hebben en elkaar niet overlappen met mijn IP-ruimte? 
Nee. Persoonlijke Azure-koppeling biedt deze functionaliteit voor u. U hoeft geen niet-overlappende adres ruimte te hebben met de adres ruimte van uw klant. 

##  <a name="next-steps"></a>Volgende stappen

- Meer informatie over [persoonlijke Azure-koppelingen](private-link-overview.md)
