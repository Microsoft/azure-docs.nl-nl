---
title: Netwerken configureren voor Azure-ontwikkel ruimten in verschillende netwerk topologieën
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: Hierin worden de netwerk vereisten beschreven voor het uitvoeren van Azure-ontwikkel ruimten in azure Kubernetes Services
keywords: Azure dev Spaces, dev Spaces, docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, CNI, kubenet, SDN, netwerk
ms.openlocfilehash: 09114ab13555cbf9ef42b37c86ffb76a8fe3ab3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91970335"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Netwerken configureren voor Azure-ontwikkel ruimten in verschillende netwerk topologieën

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure dev Spaces worden uitgevoerd op Azure Kubernetes service (AKS)-clusters met de standaard-netwerk configuratie. Als u de netwerk configuratie van uw AKS-cluster wilt wijzigen, bijvoorbeeld door het cluster achter een firewall te plaatsen, netwerk beveiligings groepen te gebruiken of netwerk beleid te gebruiken, moet u aanvullende overwegingen nemen voor het uitvoeren van Azure dev Spaces.

![Configuratie van virtueel netwerk](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>Configuraties van virtuele netwerken of subnetten

Uw AKS-cluster kan een andere configuratie voor het virtuele netwerk of subnet hebben om binnenkomend of uitgaand verkeer voor uw AKS-cluster te beperken. Het is bijvoorbeeld mogelijk dat uw cluster zich achter een firewall bevindt, zoals Azure Firewall, of u kunt netwerk beveiligings groepen of aangepaste rollen gebruiken voor het beperken van netwerk verkeer. Een voor beeld van een netwerk configuratie vindt u in de opslag plaats voor de voor [beelden van Azure dev Spaces op github][sample-repo].

Azure dev Spaces heeft bepaalde vereisten voor binnenkomend *en* uitgaand netwerk verkeer, en *alleen* binnenkomend verkeer. Als u Azure dev Spaces gebruikt in een AKS-cluster met een virtueel netwerk of een subnet-configuratie die verkeer beperkt voor uw AKS-cluster, moet u de volgende ingangs-en ingangs-en uitgangs vereisten volgen om Azure-ontwikkel ruimten goed te laten functioneren.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Netwerk verkeer vereisten voor ingangs-en uitgangs gegevens

Azure dev Spaces heeft ingangs-en uitgangs verkeer nodig voor de volgende FQDN-tekens:

| FQDN                       | Poort       | Gebruik      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Docker-installatie kopieën ophalen voor Azure dev Spaces |
| gcr.io                     | HTTPS: 443 | Helm-installatie kopieën voor Azure-ontwikkel ruimten ophalen |
| storage.googleapis.com     | HTTPS: 443 | Helm-installatie kopieën voor Azure-ontwikkel ruimten ophalen |

Werk uw firewall of beveiligings configuratie bij om netwerk verkeer toe te staan van en naar de bovenstaande FQDN-namen en [Azure dev Spaces-infrastructuur services][service-tags]. Als u bijvoorbeeld een firewall gebruikt om uw netwerk te beveiligen, moeten de bovenstaande FQDN-namen worden toegevoegd aan de toepassings regel van de firewall en moet het Azure dev Spaces-service label ook worden [toegevoegd aan de firewall][firewall-service-tags]. Beide updates voor de firewall zijn vereist voor het toestaan van verkeer naar en van deze domeinen.

### <a name="ingress-only-network-traffic-requirements"></a>Alleen inkomend netwerk verkeer vereisten

Azure dev Spaces biedt Kubernetes-route ring op naam ruimte niveau en open bare toegang tot services met behulp van een eigen FQDN. Als u beide functies wilt gebruiken, werkt u uw firewall of netwerk configuratie bij zodat open bare binnenkomend verkeer naar het externe IP-adres van de controller van Azure dev Spaces kan worden uitgevoerd in uw cluster. U kunt ook een [interne Load Balancer][aks-internal-lb] maken en een NAT-regel in uw firewall toevoegen om het open bare IP-adres van uw firewall om te zetten in het IP-adres van uw interne Load Balancer. U kunt ook [traefik][traefik-ingress] of [NGINX][nginx-ingress] gebruiken om een aangepaste ingangs controller te maken.

## <a name="aks-cluster-network-requirements"></a>AKS-cluster netwerk vereisten

Met AKS kunt u [netwerk beleid][aks-network-policies] gebruiken voor het beheren van binnenkomend en uitgaand verkeer tussen de verschillende clusters op een cluster en het uitkomen van verkeer van een pod. Azure dev Spaces heeft bepaalde vereisten voor binnenkomend *en* uitgaand netwerk verkeer, en *alleen* binnenkomend verkeer. Als u Azure dev Spaces gebruikt in een AKS-cluster met AKS-netwerk beleid, moet u de volgende ingangs-en uitgangs-en uitvoerings vereisten voor verkeer volgen om Azure-ontwikkel ruimten goed te laten functioneren.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Netwerk verkeer vereisten voor ingangs-en uitgangs gegevens

Met Azure dev Spaces kunt u rechtstreeks communiceren met een pod in een dev-ruimte op uw cluster voor fout opsporing. Als u deze functie wilt gebruiken, voegt u een netwerk beleid toe waarmee binnenkomend en uitgaand verkeer wordt toegestaan aan de IP-adressen van de Azure dev Spaces-infra structuur, die [per regio verschillen][service-tags].

### <a name="ingress-only-network-traffic-requirements"></a>Alleen inkomend netwerk verkeer vereisten

Azure dev Spaces biedt route ring tussen de verschillende naam ruimten. Bijvoorbeeld: naam ruimten waarvoor Azure dev Spaces zijn ingeschakeld, kunnen een bovenliggende/onderliggende relatie hebben, waardoor netwerk verkeer tussen de verschillende bovenliggende en onderliggende naam ruimtes kan worden gerouteerd. Met Azure dev Spaces worden service-eind punten ook weer gegeven met behulp van een eigen FQDN. Zie [verschillende eindpunt opties gebruiken][endpoint-options]voor het configureren van verschillende manieren om services weer te geven en te beïnvloeden hoe deze de route ring van het naam ruimte niveau beïnvloeden.

## <a name="using-azure-cni"></a>Azure CNI gebruiken

AKS-clusters zijn standaard geconfigureerd voor het gebruik van [kubenet][aks-kubenet] voor netwerken, die werken met Azure dev Spaces. U kunt uw AKS-cluster ook configureren voor het gebruik van [Azure container Networking interface (cni)][aks-cni]. Als u Azure dev Spaces wilt gebruiken met Azure CNI op uw AKS-cluster, moet u het virtuele netwerk en het subnet adres voorzien van Maxi maal 10 privé-IP-adressen voor de peulen die worden geïmplementeerd door Azure dev Spaces. Meer informatie over het toestaan van privé-IP-adressen is beschikbaar in de [AKS Azure cni-documentatie][aks-cni-ip-planning].

## <a name="using-api-server-authorized-ip-ranges"></a>Met API server geautoriseerde IP-adresbereiken gebruiken

Met AKS-clusters kunt u extra beveiliging configureren waarmee het IP-adres kan communiceren met uw clusters, bijvoorbeeld met behulp van aangepaste virtuele netwerken of [het beveiligen van toegang tot de API-server met behulp van geautoriseerde IP-bereiken][aks-ip-auth-ranges]. Als u Azure dev Spaces wilt gebruiken wanneer u deze extra beveiliging gebruikt tijdens het [maken][aks-ip-auth-range-create] van het cluster, moet u [extra bereiken op basis van uw regio toestaan][service-tags]. U kunt ook een bestaand cluster [bijwerken][aks-ip-auth-range-update] om deze extra bereiken toe te staan. U moet ook het IP-adres van alle ontwikkel computers toestaan die verbinding maken met uw AKS-cluster voor fout opsporing om verbinding te maken met uw API-server.

## <a name="using-aks-private-clusters"></a>AKS persoonlijke clusters gebruiken

Op dit moment wordt Azure dev Spaces niet ondersteund met [persoonlijke AKS-clusters][aks-private-clusters].

## <a name="using-different-endpoint-options"></a>Verschillende eindpunt opties gebruiken

Azure dev Spaces biedt de mogelijkheid om eind punten beschikbaar te stellen voor uw services die worden uitgevoerd op AKS. Wanneer u Azure dev-ruimten in uw cluster inschakelt, hebt u de volgende opties voor het configureren van het eindpunt type voor uw cluster:

* Een *openbaar* eind punt, dat de standaard waarde is, implementeert een ingangs controller met een openbaar IP-adres. Het open bare IP-adres wordt geregistreerd op de DNS van het cluster, waardoor open bare toegang tot uw services mogelijk is via een URL. U kunt deze URL weer geven met `azds list-uris` .
* Een *persoonlijk* eind punt implementeert een ingangs controller met een privé-IP-adres. Met een privé-IP-adres is de load balancer voor uw cluster alleen toegankelijk vanuit het virtuele netwerk van het cluster. Het privé IP-adres van de load balancer is geregistreerd op de DNS van het cluster, zodat de services in het virtuele netwerk van het cluster kunnen worden geopend met behulp van een URL. U kunt deze URL weer geven met `azds list-uris` .
* Als u *geen* instelt voor de eindpunt optie, wordt er geen ingangs controller geïmplementeerd. Als er geen ingangs controller is geïmplementeerd, werken de [routerings mogelijkheden van Azure dev Spaces][dev-spaces-routing] niet. U kunt desgewenst uw eigen ingangs controller implementeren met behulp van [traefik][traefik-ingress] of [NGINX][nginx-ingress], zodat de routerings mogelijkheden weer werken.

Als u uw eindpunt optie wilt configureren, gebruikt u *-e* of *--eind punt* bij het inschakelen van Azure dev-ruimten in uw cluster. Bijvoorbeeld:

> [!NOTE]
> Voor de eindpunt optie moet u Azure CLI versie 2.2.0 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>Clientvereisten

Azure dev Spaces maakt gebruik van Program ma's aan de client zijde, zoals de Azure dev Spaces CLI-extensie, Visual Studio code extension en Visual Studio-extensie, om te communiceren met uw AKS-cluster voor fout opsporing. Als u de Azure dev Spaces-client-side Tool wilt gebruiken, moet u verkeer van de ontwikkel machines naar de [Azure dev Spaces-infra structuur][dev-spaces-allow-infrastructure]toestaan. Als u door [API server geautoriseerde IP-adresbereiken][auth-range-section]gebruikt, moet u ook het IP-adres van alle ontwikkel machines toestaan die verbinding maken met uw AKS-cluster voor fout opsporing om verbinding te maken met uw API-server.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe Azure Dev Spaces werkt.

> [!div class="nextstepaction"]
> [Hoe Azure Dev Spaces werkt](how-dev-spaces-works.md)

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges
[azure-cli-install]: /cli/azure/install-azure-cli
[dev-spaces-allow-infrastructure]: #virtual-network-or-subnet-configurations
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[firewall-service-tags]: ../firewall/service-tags.md
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[sample-repo]: https://github.com/Azure/dev-spaces/tree/master/advanced%20networking
[service-tags]: ../virtual-network/service-tags-overview.md#available-service-tags