---
title: Verantwoordelijkheden van klanten die Azure lente Cloud in vnet uitvoeren
description: In dit artikel worden de verantwoordelijkheden beschreven van klanten die Azure lente Cloud in vnet uitvoeren.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 0c73d0394486472c2c3c92450aab6a1a0d329cf7
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878195"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>Verantwoordelijkheden van klanten voor het uitvoeren van Azure lente Cloud in VNET
Dit document bevat specificaties voor het gebruik van Azure lente-Cloud in een virtueel netwerk.

Als Azure lente-Cloud in uw virtuele netwerk wordt geïmplementeerd, heeft dit uitgaande afhankelijkheden voor services buiten het virtuele netwerk. Voor beheer-en operationele doeleinden moet Azure lente-Cloud toegang hebben tot bepaalde poorten en FQDN-namen (Fully Qualified Domain Name). Deze eind punten zijn vereist om te communiceren met het Azure lente-Cloud beheer vlak en om essentiële Kubernetes-cluster onderdelen en beveiligings updates te downloaden en te installeren.

Azure lente-Cloud heeft standaard onbeperkte uitgaande (uitgaand) Internet toegang. Dit niveau van netwerk toegang maakt het mogelijk dat toepassingen die u uitvoert, toegang krijgen tot externe bronnen. Als u uitgaand verkeer wilt beperken, moet een beperkt aantal poorten en adressen toegankelijk zijn voor onderhouds taken. De eenvoudigste oplossing voor het beveiligen van uitgaande adressen is het gebruik van een firewall apparaat dat uitgaand verkeer op basis van domein namen kan beheren. Azure Firewall kan bijvoorbeeld het uitgaande HTTP-en HTTPS-verkeer beperken op basis van de FQDN van de bestemming. U kunt ook uw voorkeurs firewall en beveiligings regels configureren om deze vereiste poorten en adressen toe te staan.

## <a name="azure-spring-cloud-resource-requirements"></a>Azure lente Cloud resource vereisten 

Hier volgt een lijst met resource vereisten voor Azure lente-Cloud Services. Als algemene vereiste moet u de resource groepen die zijn gemaakt door de Azure lente-Cloud en de onderliggende netwerk bronnen niet wijzigen.
- Wijzig geen resource groepen die zijn gemaakt en eigendom zijn van Azure lente Cloud.
  - Deze resource groepen worden standaard aangeduid als AP-SVC-rt_ [SERVICE-INSTANCE-NAME]_[regio] * en AP_[Service-instance-name] _ [regio] *.
- Wijzig geen subnetten die worden gebruikt door Azure lente Cloud.
- Maak niet meer dan één Azure veer-Cloud service-exemplaar in hetzelfde subnet.
- Als u een firewall gebruikt om verkeer te beheren, blokkeert u *niet* het volgende uitgaand verkeer naar Azure lente-Cloud onderdelen die het service-exemplaar gebruiken, onderhouden en ondersteunen.

## <a name="azure-spring-cloud-network-requirements"></a>Azure lente-Cloud netwerk vereisten

  | Doel eindpunt | Poort | Gebruik | Notitie |
  |------|------|------|
  | *: 1194 *of* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -Cloud: 1194 | UDP: 1194 | Onderliggend Kubernetes-Cluster beheer. | |
  | *: 443 *of* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -Cloud: 443 | TCP: 443 | Azure lente-Cloud Service beheer. | Informatie over het service-exemplaar ' requiredTraffics ' kan bekend zijn bij resource Payload, onder ' networkProfile '. |
  | *: 9000 *of* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -Cloud: 9000 | TCP: 9000 | Onderliggend Kubernetes-Cluster beheer. |
  | *: 123 *of* ntp.Ubuntu.com:123 | UDP: 123 | NTP-tijd synchronisatie op Linux-knoop punten. | |
  | *. azure.io:443 *of* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureContainerRegistry: 443 | TCP: 443 | Azure Container Registry. | Kan worden vervangen door *Azure container Registry* [service-eind punt in](../virtual-network/virtual-network-service-endpoints-overview.md)te scha kelen in het virtuele netwerk. |
  | *. core.windows.net:443 en *. core.windows.net:445 *of* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -Storage: 443 en Storage: 445 | TCP: 443, TCP: 445 | Azure File Storage | Kan worden vervangen door *Azure Storage* [service-eind punt in](../virtual-network/virtual-network-service-endpoints-overview.md)te scha kelen in het virtuele netwerk. |
  | *. servicebus.windows.net:443 *of* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -EventHub: 443 | TCP: 443 | Azure Event hub. | Kan worden vervangen door *Azure Event hubs* [service-eind punt in](../virtual-network/virtual-network-service-endpoints-overview.md)te scha kelen in het virtuele netwerk. |
  

## <a name="azure-spring-cloud-fqdn-requirements--application-rules"></a>FQDN-vereisten/toepassings regels voor Azure lente Cloud

Azure Firewall biedt een Fully Qualified Domain Name (FQDN)-tag **AzureKubernetesService** om de volgende configuraties te vereenvoudigen.

  | Doel-FQDN | Poort | Gebruik |
  |------|------|------|
  | *. azmk8s.io | HTTPS: 443 | Onderliggend Kubernetes-Cluster beheer. |
  | <i>mcr.microsoft.com</i> | HTTPS: 443 | Micro soft Container Registry (MCR). |
  | *. cdn.mscr.io | HTTPS: 443 | MCR-opslag ondersteund door de Azure CDN. |
  | *. data.mcr.microsoft.com | HTTPS: 443 | MCR-opslag ondersteund door de Azure CDN. |
  | <i>management.azure.com</i> | HTTPS: 443 | Onderliggend Kubernetes-Cluster beheer. |
  | <i>login.microsoftonline.com</i> | HTTPS: 443 | Azure Active Directory-verificatie. |
  |<i>packages.microsoft.com</i>    | HTTPS: 443 | Micro soft packages-opslag plaats. |
  | <i>acs-mirror.azureedge.net</i> | HTTPS: 443 | De opslag plaats is vereist om de vereiste binaire bestanden te installeren, zoals kubenet en Azure CNI. |
  | *mscrl.microsoft.com* | HTTPS: 80 | Vereiste micro soft-certificaat keten paden. |
  | *crl.microsoft.com* | HTTPS: 80 | Vereiste micro soft-certificaat keten paden. |
  | *crl3.digicert.com* | HTTPS: 80 | SSL-certificaat keten paden van derden. |

## <a name="see-also"></a>Zie ook
* [Toegang tot uw toepassing in een privé netwerk](spring-cloud-access-app-virtual-network.md)
* [Apps beschikbaar maken met Application Gateway en Azure Firewall](spring-cloud-expose-apps-gateway-azure-firewall.md)