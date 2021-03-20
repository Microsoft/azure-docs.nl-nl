---
title: Concepten-API Management
description: Meer informatie over hoe API Management Api's beschermt die worden uitgevoerd op virtuele machines van Azure VMware Solution (Vm's)
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 958cc52c48d1121a69dca2fc901289ad1ed671cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541960"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-azure-vmware-solution-based-vms"></a>API Management voor het publiceren en beveiligen van Api's die worden uitgevoerd op op Azure VMware-oplossingen gebaseerde Vm's

Met Microsoft Azure [API Management](https://azure.microsoft.com/services/api-management/) kunt u veilig publiceren naar interne of externe consumenten.  Alleen de Developer-en Premium-Sku's bieden de integratie van Azure Virtual Network de mogelijkheid om Api's te publiceren die worden uitgevoerd op werk belastingen van de Azure VMware-oplossing.  Beide Sku's bieden de connectiviteit tussen API Management service en de back-end veilig. 

>[!NOTE]
>De SDK voor ontwikkel aars is bedoeld voor ontwikkelings-en test doeleinden terwijl de Premium-SKU voor productie-implementaties is.

De configuratie van de API Management is hetzelfde voor back-end-services die worden uitgevoerd op virtuele machines van Azure VMware Solution (Vm's) en on-premises. Voor beide implementaties API Management configureert u het virtuele IP-adres (VIP) op de load balancer als het back-end-eind punt wanneer de back-endserver wordt geplaatst achter een NSX-Load Balancer in de Azure VMware-oplossing. 


## <a name="external-deployment"></a>Externe implementatie

Een externe implementatie publiceert Api's die worden gebruikt door externe gebruikers met behulp van een openbaar eind punt. Ontwikkel aars en DevOps-technici kunnen Api's beheren via de Azure Portal of Power shell en de API Management ontwikkelaars Portal.

Het externe implementatie diagram toont het hele proces en de betrokken actoren (bovenaan weer gegeven). De Actors zijn:

- **Beheerder (s):** Hiermee wordt het team van de beheerder of het DevOps, waarmee de Azure VMware-oplossing wordt beheerd via de Azure Portal-en automatiserings mechanismen, zoals Power shell of Azure DevOps.

- **Gebruikers:**  Vertegenwoordigt de consumenten van de blootgestelde Api's en vertegenwoordigen zowel gebruikers als services die de Api's gebruiken.

De verkeers stroom loopt API Management exemplaar, waarmee de back-end-services worden abstract, die zijn aangesloten op het virtuele netwerk van de hub. De ExpressRoute-gateway stuurt het verkeer naar de ExpressRoute-Global Reach kanaal en bereikt een NSX Load Balancer het binnenkomende verkeer naar de verschillende exemplaren van de back-end-services te distribueren.

API Management heeft een open bare Azure-API en wordt aanbevolen om de Azure DDOS Protection-Service te activeren. 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="Externe implementatie-API Management voor Azure VMware-oplossing":::


## <a name="internal-deployment"></a>Interne implementatie

Een interne implementatie publiceert Api's die worden gebruikt door interne gebruikers of systemen. DevOps-team en API-Ontwikkel aars gebruiken dezelfde beheer hulpprogramma's en ontwikkelaars portal als in de externe implementatie.

Interne implementaties kunnen [met Azure-toepassing gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) worden uitgevoerd om een openbaar en beveiligd eind punt voor de API te maken.  De mogelijkheden van de gateway worden gebruikt voor het maken van een hybride implementatie die verschillende scenario's mogelijk maakt.  

* Gebruik dezelfde API Management resource voor verbruik door zowel interne als externe consumenten.

* Beschikken over een enkele API Management resource met een subset van Api's die zijn gedefinieerd en beschikbaar zijn voor externe consumenten.

* Bieden een eenvoudige manier om de toegang tot API Management van het open bare Internet te wijzigen in en uit te scha kelen.

In het onderstaande implementatie diagram worden gebruikers weer gegeven die intern of extern kunnen zijn, waarbij elk type toegang tot dezelfde of verschillende Api's heeft.

In een interne implementatie worden Api's blootgesteld aan hetzelfde API Management-exemplaar. Application Gateway wordt voor API Management geïmplementeerd met de functie Azure Web Application firewall (WAF) geactiveerd. Ook geïmplementeerd, een set HTTP-listeners en-regels voor het filteren van het verkeer, waardoor slechts een subset van de back-endservers wordt weer gegeven die wordt uitgevoerd op de Azure VMware-oplossing.


* Interne verkeer stuurt door de ExpressRoute-gateway naar Azure Firewall en vervolgens om rechtstreeks of via verkeers regels te API Management.   

* Extern verkeer voert Azure in via Application Gateway, dat gebruikmaakt van de externe beveiligingslaag voor API Management.


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="Interne implementatie-API Management voor Azure VMware-oplossing" lightbox="media/api-management/internal-deployment.png":::