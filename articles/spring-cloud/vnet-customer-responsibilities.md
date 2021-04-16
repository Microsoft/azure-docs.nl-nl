---
title: Verantwoordelijkheden van de klant die Azure Spring Cloud in vnet
description: In dit artikel worden de verantwoordelijkheden van de klant beschreven die Azure Spring Cloud in vnet.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: devx-track-java
ms.openlocfilehash: a6b444092ec4e3588564a3f902b49c4ed3dc5fe5
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376780"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>Verantwoordelijkheden van de klant voor het uitvoeren Azure Spring Cloud in VNET
Dit document bevat specificaties voor het gebruik van Azure Spring Cloud in een virtueel netwerk.

Wanneer Azure Spring Cloud wordt geïmplementeerd in uw virtuele netwerk, zijn er uitgaande afhankelijkheden van services buiten het virtuele netwerk. Voor beheer- en operationele doeleinden moeten Azure Spring Cloud toegang hebben tot bepaalde poorten en FQDN's (Fully Qualified Domain Names). Deze eindpunten zijn vereist om te communiceren met Azure Spring Cloud beheervlak en om kernonderdelen en beveiligingsupdates van Kubernetes-clusters te downloaden en installeren.

Standaard heeft Azure Spring Cloud onbeperkte uitgaande (uitgaande) internettoegang. Met dit niveau van netwerktoegang hebben toepassingen die u wilt uitvoeren toegang tot externe resources. Als u het toegangsverkeer wilt beperken, moet een beperkt aantal poorten en adressen toegankelijk zijn voor onderhoudstaken. De eenvoudigste oplossing voor het beveiligen van uitgaande adressen is het gebruik van een firewallapparaat dat uitgaand verkeer op basis van domeinnamen kan beheren. Azure Firewall kunt bijvoorbeeld uitgaand HTTP- en HTTPS-verkeer beperken op basis van de FQDN van de bestemming. U kunt ook de firewall en beveiligingsregels van uw voorkeur configureren om deze vereiste poorten en adressen toe te staan.

## <a name="azure-spring-cloud-resource-requirements"></a>Azure Spring Cloud resourcevereisten 

Hier volgt een lijst met resourcevereisten voor Azure Spring Cloud services. Als algemene vereiste moet u resourcegroepen die zijn gemaakt door Azure Spring Cloud en de onderliggende netwerkresources niet wijzigen.
- Wijzig geen resourcegroepen die zijn gemaakt en eigendom zijn van Azure Spring Cloud.
  - Standaard worden deze resourcegroepen benoemd als ap-svc-rt_[SERVICE-INSTANCE-NAME]_[REGION]*_ en ap [SERVICE-INSTANCE-NAME]_[REGION]*.
- Wijzig geen subnetten die worden gebruikt door Azure Spring Cloud.
- Maak niet meer dan één Azure Spring Cloud service-exemplaar in hetzelfde subnet.
- Wanneer u een firewall gebruikt om verkeer te *beheren,* moet u het volgende verkeer voor Azure Spring Cloud blokkeren die het service-exemplaar gebruiken, onderhouden en ondersteunen.

## <a name="azure-spring-cloud-network-requirements"></a>Azure Spring Cloud netwerkvereisten

  | Doel-eindpunt | Poort | Gebruik | Notitie |
  |------|------|------|------|
  | *:1194 *of* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:1194 | UDP:1194 | Onderliggend Kubernetes-clusterbeheer. | |
  | *:443 *of* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:443 | TCP:443 | Azure Spring Cloud Service Management. | Informatie over het service-exemplaar 'requiredTraffics' kan bekend zijn in de nettolading van de resource, onder de sectie networkProfile. |
  | *:9000 *of* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:9000 | TCP:9000 | Onderliggend Kubernetes-clusterbeheer. |
  | *:123 *Of* ntp.ubuntu.com:123 | UDP:123 | NTP tijdsynchronisatie op Linux-knooppunten. | |
  | *.azure.io:443 Or  [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureContainerRegistry:443 | TCP:443 | Azure Container Registry. | Kan worden vervangen door het inschakelen *Azure Container Registry* [service-eindpunt in virtueel netwerk](../virtual-network/virtual-network-service-endpoints-overview.md). |
  | *.core.windows.net:443 en *.core.windows.net:445 *Or* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Storage:443 en Storage:445 | TCP:443, TCP:445 | Azure File Storage | Kan worden vervangen door het *inschakelen Azure Storage* [service-eindpunt in virtueel netwerk](../virtual-network/virtual-network-service-endpoints-overview.md). |
  | *.servicebus.windows.net:443 Of  [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - EventHub:443 | TCP:443 | Azure Event Hub. | Kan worden vervangen door het *inschakelen Azure Event Hubs* [service-eindpunt in virtueel netwerk](../virtual-network/virtual-network-service-endpoints-overview.md). |
  

## <a name="azure-spring-cloud-fqdn-requirementsapplication-rules"></a>Azure Spring Cloud FQDN-vereisten/toepassingsregels

Azure Firewall biedt de FQDN-tag **AzureKubernetesService** om de volgende configuraties te vereenvoudigen:

  | Doel-FQDN | Poort | Gebruik |
  |------|------|------|
  | *.azmk8s.io | HTTPS:443 | Onderliggend Kubernetes-clusterbeheer. |
  | <i>mcr.microsoft.com</i> | HTTPS:443 | Microsoft Container Registry (MCR). |
  | *.cdn.mscr.io | HTTPS:443 | MCR-opslag met de Azure CDN. |
  | *.data.mcr.microsoft.com | HTTPS:443 | MCR-opslag met de Azure CDN. |
  | <i>management.azure.com</i> | HTTPS:443 | Onderliggend Kubernetes-clusterbeheer. |
  | <i>*login.microsoftonline.com</i> | HTTPS:443 | Azure Active Directory verificatie. |
  | <i>*login.microsoft.com</i> | HTTPS:443 | Azure Active Directory verificatie. |
  |<i>packages.microsoft.com</i>    | HTTPS:443 | Opslagplaats voor Microsoft-pakketten. |
  | <i>acs-mirror.azureedge.net</i> | HTTPS:443 | Opslagplaats vereist voor het installeren van vereiste binaire bestanden, zoals kubenet en Azure CNI. |
  | *mscrl.microsoft.com* | HTTPS:80 | Vereiste microsoft-certificaatketenpaden. |
  | *crl.microsoft.com* | HTTPS:80 | Vereiste microsoft-certificaatketenpaden. |
  | *crl3.digicert.com* | HTTPS:80 | SSL-certificaatketenpaden van derden. |
  
## <a name="azure-spring-cloud-optional-fqdn-for-third-party-application-performance-management"></a>Azure Spring Cloud FQDN voor prestatiebeheer van toepassingen van derden

Azure Firewall biedt de FQDN-tag **AzureKubernetesService** om de volgende configuraties te vereenvoudigen:

  | Doel-FQDN | Poort | Gebruik                                                          |
  | ---------------- | ---- | ------------------------------------------------------------ |
  | collector*.newrelic.com | TCP:443/80 | Vereiste netwerken van New Relic APM-agents uit de regio VS, zie [ook APM Agents Networks](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents). |
  | collector*.eu01.nr-data.net | TCP:443/80 | Vereiste netwerken van New Relic APM-agents uit de EU-regio, zie [ook APM Agents Networks](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents). |

## <a name="see-also"></a>Zie ook
* [Toegang tot uw toepassing in een particulier netwerk](access-app-virtual-network.md)
* [Apps beschikbaar maken met Application Gateway en Azure Firewall](expose-apps-gateway-azure-firewall.md)
