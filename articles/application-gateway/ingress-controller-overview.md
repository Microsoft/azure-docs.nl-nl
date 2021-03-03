---
title: Wat is Azure-toepassing gateway ingangs controller?
description: Dit artikel bevat een inleiding tot de Application Gateway ingangs controller.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: 2564fd38056241fd48f58f5f6039bf64f92b6741
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714405"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Wat is Application Gateway ingangs controller?
De Application Gateway ingangs controller (AGIC) is een Kubernetes-toepassing, waardoor [Azure Kubernetes service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) -klanten de systeem eigen [Application Gateway](https://azure.microsoft.com/services/application-gateway/) N7-Load Balancer van Azure kunnen gebruiken om cloud software op internet beschikbaar te stellen. AGIC bewaakt het Kubernetes-cluster dat wordt gehost op een Application Gateway, zodat de geselecteerde services worden blootgesteld aan Internet.

De ingangs controller wordt uitgevoerd in een eigen pod op de AKS van de klant. AGIC bewaakt een subset van Kubernetes-resources op wijzigingen. De status van het AKS-cluster wordt omgezet naar Application Gateway specifieke configuratie en toegepast op de [Azure Resource Manager (arm)](../azure-resource-manager/management/overview.md).

## <a name="benefits-of-application-gateway-ingress-controller"></a>Voor delen van Application Gateway ingangs controller
AGIC helpt te voor komen dat er een ander load balancer/openbaar IP-adres voor het AKS-cluster nodig is en dat er meerdere hops in uw DataPath worden gemaakt voordat aanvragen het AKS-cluster bereiken. Application Gateway gespeert naar een doel dat rechtstreeks gebruikmaakt van het privé-IP-adres en geen NodePort-of KubeProxy-Services vereist. Dit brengt ook betere prestaties voor uw implementaties.

Ingangs controller wordt alleen ondersteund door Standard_v2 en WAF_v2 Sku's, waarmee u uw voor delen automatisch kunt schalen. Application Gateway kan reageren op een toename of afname in de belasting van het verkeer en de schaal dienovereenkomstig, zonder dat er resources van uw AKS-cluster worden gebruikt.

Het gebruik van Application Gateway naast AGIC biedt ook bescherming voor uw AKS-cluster door TLS-beleid en functionaliteit voor Web Application firewall (WAF) te bieden.

![Azure-toepassing gateway + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC wordt geconfigureerd via de [ingangs bron](https://kubernetes.io/docs/user-guide/ingress/)van Kubernetes, samen met Service en implementaties/peul. Het biedt een aantal functies, met Azure native Application Gateway N7 load balancer. Een paar van de volgende namen:
  - URL-routering
  - Affiniteit op basis van cookies
  - TLS-beëindiging
  - End-to-end TLS
  - Ondersteuning voor open bare, persoonlijke en hybride websites
  - Geïntegreerde Web Application Firewall

## <a name="difference-between-helm-deployment-and-aks-add-on"></a>Verschil tussen helm-implementatie en AKS-Add-On
Er zijn twee manieren om AGIC te implementeren voor uw AKS-cluster. De eerste manier is via helm. de tweede is door AKS als een invoeg toepassing. Het belangrijkste voor deel van het implementeren van AGIC als een AKS-invoeg toepassing is dat het veel eenvoudiger is dan implementatie via helm. Voor een nieuwe installatie kunt u een nieuwe Application Gateway en een nieuw AKS-cluster implementeren met AGIC ingeschakeld als een invoeg toepassing op één regel in azure CLI. De invoeg toepassing is ook een volledig beheerde service die extra voor delen biedt, zoals automatische updates en verbeterde ondersteuning. Beide manieren waarop u AGIC (helm en AKS-invoeg toepassing) implementeert, worden volledig ondersteund door micro soft. Daarnaast biedt de invoeg toepassing een betere integratie met AKS als een eerste klasse-invoeg toepassing.

De AGIC-invoeg toepassing wordt nog steeds geïmplementeerd als een pod in het AKS-cluster van de klant. er zijn echter enkele verschillen tussen de implementatie versie van helm en de invoeg toepassing van AGIC. Hieronder vindt u een lijst met verschillen tussen de twee versies: 
  - Helm implementatie waarden kunnen niet worden gewijzigd in de AKS-invoeg toepassing:
    - `verbosityLevel` wordt standaard ingesteld op 5
    - `usePrivateIp` wordt standaard ingesteld op ONWAAR. Dit kan worden overschreven door het [gebruik van de aantekening-Private-IP](ingress-controller-annotations.md#use-private-ip)
    - `shared` wordt niet ondersteund in de invoeg toepassing 
    - `reconcilePeriodSeconds` wordt niet ondersteund in de invoeg toepassing
    - `armAuth.type` wordt niet ondersteund in de invoeg toepassing
  - AGIC geïmplementeerd via helm ondersteunt ProhibitedTargets, wat betekent dat AGIC de Application Gateway voor AKS-clusters kan configureren zonder dat dit van invloed is op andere bestaande back-endservers. AGIC-invoeg toepassing biedt momenteel geen ondersteuning voor dit. 
  - Omdat de AGIC-invoeg toepassing een beheerde service is, worden klanten automatisch bijgewerkt naar de nieuwste versie van AGIC-invoeg toepassing, in tegens telling tot AGIC geïmplementeerd via helm, waarbij de klant AGIC hand matig moet bijwerken. 

> [!NOTE]
> Klanten kunnen slechts één AGIC-invoeg toepassing per AKS-cluster implementeren en elke AGIC-invoeg toepassing op dit moment kan slechts één Application Gateway als doel hebben. Voor implementaties die meer dan één AGIC per cluster of meerdere AGICs hebben die zijn gericht op een Application Gateway, kunt u AGIC gebruiken die via helm is geïmplementeerd. 

## <a name="next-steps"></a>Volgende stappen
- [**AKS Add-On ontwikkel-implementatie**](tutorial-ingress-controller-add-on-new.md): instructies voor het installeren van AGIC add-on, AKS en Application Gateway op een lege infra structuur.
- [**AKS Add-On Brownfield-implementatie**](tutorial-ingress-controller-add-on-existing.md): Installeer AGIC-invoeg toepassing op een AKS-cluster met een bestaande Application Gateway.
- [**Helm ontwikkel-implementatie**](ingress-controller-install-new.md): Installeer AGIC via helm, nieuw AKS cluster en nieuwe Application Gateway op een lege infra structuur.
- [**Helm Brownfield-implementatie**](ingress-controller-install-existing.md): implementeer AGIC via helm op een bestaande AKS-cluster en Application Gateway.