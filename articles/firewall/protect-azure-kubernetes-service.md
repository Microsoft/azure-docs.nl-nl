---
title: Azure Firewall gebruiken om implementaties van Azure Kubernetes service (AKS) te beveiligen
description: Meer informatie over het gebruik van Azure Firewall voor het beveiligen van de implementaties van Azure Kubernetes service (AKS)
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 01/11/2021
ms.author: victorh
ms.openlocfilehash: 51af9ff4972f5edef02426a6e81e8582123c9a7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98107851"
---
# <a name="use-azure-firewall-to-protect-azure-kubernetes-service-aks-deployments"></a>Azure Firewall gebruiken om implementaties van Azure Kubernetes service (AKS) te beveiligen

Azure Kubernetes service (AKS) biedt een beheerd Kubernetes-cluster in Azure. Het vermindert de complexiteit en operationele overhead van het beheer van Kubernetes door een groot deel van die verantwoordelijkheid te offloaden naar Azure. AKS verwerkt kritieke taken, zoals status controle en onderhoud voor u en levert een hoogwaardige en veilige cluster met vereenvoudigd beheer.

Kubernetes organiseert clusters van virtuele machines en plant containers voor uitvoering op die virtuele machines op basis van hun beschik bare reken bronnen en de resource vereisten van elke container. Containers zijn gegroepeerd in peul, de basis operationele eenheid voor Kubernetes en de gewenste grootte.

Voor beheer-en operationele doel einden moeten knoop punten in een AKS-cluster toegang hebben tot bepaalde poorten en FQDN-namen (Fully Qualified Domain Name). Deze acties zijn mogelijk te communiceren met de API-server of voor het downloaden en installeren van kern Kubernetes-cluster onderdelen en beveiligings updates voor knoop punten. Azure Firewall kunt u helpen uw omgeving te vergren delen en uitgaand verkeer te filteren.

Volg de richt lijnen in dit artikel om extra beveiliging te bieden voor uw Azure Kubernetes-cluster met behulp van Azure Firewall.

## <a name="prerequisites"></a>Vereisten

- Een geïmplementeerd Azure Kubernetes-cluster met actieve toepassing.

   Zie [zelf studie: een AKS-cluster (Azure Kubernetes service) implementeren](../aks/tutorial-kubernetes-deploy-cluster.md) en [zelf studie: toepassingen uitvoeren in azure KUBERNETES service (AKS)](../aks/tutorial-kubernetes-deploy-application.md)voor meer informatie.


## <a name="securing-aks"></a>AKS beveiligen

Azure Firewall biedt een AKS FQDN-code om de configuratie te vereenvoudigen. Voer de volgende stappen uit om uitgaand AKS-platform verkeer toe te staan:

- Wanneer u Azure Firewall gebruikt om uitgaand verkeer te beperken en een door de gebruiker gedefinieerde route (UDR) te maken om al het uitgaande verkeer te leiden, moet u ervoor zorgen dat u in de firewall een geschikte DNAT-regel maakt om inkomend verkeer correct toe te staan. 

   Als Azure Firewall wordt gebruikt met een UDR, wordt de binnenkomende installatie afgebroken vanwege asymmetrische route ring. Het probleem treedt op als het AKS-subnet een standaard route heeft die naar het privé-IP-adres van de firewall gaat, maar u een open bare load balancer gebruikt. Bijvoorbeeld een inkomende of Kubernetes service van het type *Load Balancer*.

   In dit geval wordt het binnenkomende load balancer verkeer ontvangen via het open bare IP-adres, maar het retour pad gaat via het privé-IP-adres van de firewall. Omdat de firewall stateful is, wordt het terugkerende pakket neergezet omdat de firewall niet op de hoogte is van een vastgestelde sessie. Zie [Azure firewall integreren met Azure Standard Load Balancer](integrate-lb.md)voor meer informatie over het integreren van Azure Firewall met uw ingangs-of service Load Balancer.
- Maak een toepassings regel verzameling en voeg een regel toe om de *AzureKubernetesService* FQDN-tag in te scha kelen. Het bron-IP-adres bereik is het virtuele netwerk van de hostgroep, het protocol is https en de doel locatie is AzureKubernetesService.
- De volgende uitgaande poorten/netwerk regels zijn vereist voor een AKS-cluster:

   - TCP-poort 443
   - TCP [*IPAddrOfYourAPIServer*]: 443 is vereist als u een app hebt die moet communiceren met de API-server. Deze wijziging kan worden ingesteld nadat het cluster is gemaakt.
   - TCP-poort 9000 en UDP-poort 1194 voor de tunnel-front-pod om te communiceren met het tunnel einde op de API-server.

      Zie de adressen in de volgende tabel voor meer specifieke informatie:

   | Doel eindpunt                                                             | Protocol | Poort    | Gebruik  |
   |----------------------------------------------------------------------------------|----------|---------|------|
   | **`*:1194`** <br/> *Of* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Of* <br/> [Regionale CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Of* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Voor beveiligde communicatie via een tunnel tussen de knoop punten en het besturings vlak. |
   | **`*:9000`** <br/> *Of* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Of* <br/> [Regionale CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Of* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Voor beveiligde communicatie via een tunnel tussen de knoop punten en het besturings vlak. |

   - UDP-poort 123 voor NTP (Network Time Protocol) tijd synchronisatie (Linux-knoop punten).
   - UDP-poort 53 voor DNS is ook vereist als u een rechtstreekse toegang hebt tot de API-server.

   Zie uitgaand [verkeer controleren voor cluster knooppunten in azure Kubernetes service (AKS)](../aks/limit-egress-traffic.md)voor meer informatie.
- AzureMonitor-en Storage service-Tags configureren. Azure Monitor ontvangt log Analytics-gegevens.

   U kunt ook uw werk ruimte-URL afzonderlijk toestaan: `<worksapceguid>.ods.opinsights.azure.com` en `<worksapceguid>.oms.opinsights.azure.com` . U kunt dit op een van de volgende manieren aanpakken:

    - Sta https-toegang vanuit het subnet van uw hostgroep toe aan `*. ods.opinsights.azure.com` en `*.oms. opinsights.azure.com` . Deze FQDN-joker tekens bieden de vereiste toegang, maar zijn minder beperkend.
    - Gebruik de volgende log Analytics-query om de exacte vereiste FQDN-namen weer te geven en sta ze expliciet toe in uw firewall toepassings regels:
   ```
   AzureDiagnostics 
   | where Category == "AzureFirewallApplicationRule" 
   | search "Allow" 
   | search "*. ods.opinsights.azure.com" or "*.oms. opinsights.azure.com"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" * 
   | project TimeGenerated,Protocol,FQDN 
   ```


## <a name="next-steps"></a>Volgende stappen

- Zie [Kubernetes core-concepten voor Azure Kubernetes service (AKS) voor](../aks/concepts-clusters-workloads.md)meer informatie over de Azure Kubernetes-service.
