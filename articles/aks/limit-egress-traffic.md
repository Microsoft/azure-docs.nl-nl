---
title: Beperk het verkeer voor Azure Kubernetes Service (AKS)
description: Meer informatie over welke poorten en adressen vereist zijn voor het beheer van Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.author: jpalma
ms.date: 01/12/2021
author: palma21
ms.openlocfilehash: 39c0877b96a3e8c6c716c1ab9ae7ba11575990a0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765589"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Beheer het verkeer voor het verkeer voor clusterknooppunten in Azure Kubernetes Service (AKS)

Dit artikel bevat de benodigde gegevens waarmee u uitgaand verkeer van uw Azure Kubernetes Service (AKS) kunt beveiligen. Het bevat de clustervereisten voor een basis-AKS-implementatie en aanvullende vereisten voor optionele invoegfuncties en functies. [Er wordt een voorbeeld gegeven aan het einde van het configureren van](#restrict-egress-traffic-using-azure-firewall)deze vereisten met Azure Firewall . U kunt deze informatie echter toepassen op elke uitgaande beperkingsmethode of elk apparaat.

## <a name="background"></a>Achtergrond

AKS-clusters worden geïmplementeerd in een virtueel netwerk. Dit netwerk kan worden beheerd (gemaakt door AKS) of aangepast (vooraf geconfigureerd door de gebruiker). In beide gevallen heeft het cluster **uitgaande** afhankelijkheden van services buiten dat virtuele netwerk (de service heeft geen binnenkomende afhankelijkheden).

Voor beheer- en operationele doeleinden moeten knooppunten in een AKS-cluster toegang hebben tot bepaalde poorten en FQDN's (Fully Qualified Domain Names). Deze eindpunten zijn vereist om de knooppunten te laten communiceren met de API-server of om kernonderdelen van kubernetes-clusters en beveiligingsupdates voor knooppunten te downloaden en installeren. Het cluster moet bijvoorbeeld containerinstallatie installatie van het basissysteem uit Microsoft Container Registry (MCR) halen.

De uitgaande AKS-afhankelijkheden worden bijna volledig gedefinieerd met FQDN's, die geen statische adressen achter zich hebben. Het ontbreken van statische adressen betekent dat netwerkbeveiligingsgroepen niet kunnen worden gebruikt om het uitgaande verkeer van een AKS-cluster te vergrendelen. 

Standaard hebben AKS-clusters onbeperkte uitgaande (uitgaande) internettoegang. Met dit niveau van netwerktoegang kunnen knooppunten en services die u hebt uitgevoerd, zo nodig toegang krijgen tot externe resources. Als u het toegangsverkeer wilt beperken, moet een beperkt aantal poorten en adressen toegankelijk zijn om goed te kunnen blijven werken met clusteronderhoudstaken. De eenvoudigste oplossing voor het beveiligen van uitgaande adressen is het gebruik van een firewallapparaat dat uitgaand verkeer kan beheren op basis van domeinnamen. Azure Firewall kan bijvoorbeeld uitgaand HTTP- en HTTPS-verkeer beperken op basis van de FQDN van de bestemming. U kunt ook de firewall en beveiligingsregels van uw voorkeur configureren om deze vereiste poorten en adressen toe te staan.

> [!IMPORTANT]
> Dit document bevat alleen informatie over het vergrendelen van het verkeer dat het AKS-subnet verlaat. AKS heeft standaard geen vereisten voor ingress.  Het **blokkeren van intern subnetverkeer** met behulp van netwerkbeveiligingsgroepen (NSG's) en firewalls wordt niet ondersteund. Gebruik Netwerkbeleid om het verkeer binnen het cluster te controleren [**_en te blokkeren._**][network-policy]

## <a name="required-outbound-network-rules-and-fqdns-for-aks-clusters"></a>Vereiste uitgaande netwerkregels en FQDN's voor AKS-clusters

De volgende netwerk- en FQDN-/toepassingsregels zijn vereist voor een AKS-cluster. U kunt deze gebruiken als u een andere oplossing dan een Azure Firewall.

* IP-adresafhankelijkheden zijn voor niet-HTTP/S-verkeer (zowel TCP- als UDP-verkeer)
* FQDN HTTP/HTTPS-eindpunten kunnen op uw firewallapparaat worden geplaatst.
* HTTP/HTTPS-eindpunten met jokertekens zijn afhankelijkheden die met uw AKS-cluster kunnen variëren op basis van een aantal kwalificaties.
* AKS maakt gebruik van een toegangscontroller om de FQDN als een omgevingsvariabele te injecteren in alle implementaties onder kube-system en gatekeeper-system, die ervoor zorgt dat alle systeemcommunicatie tussen knooppunten en API-server gebruikmaakt van de API-server-FQDN en niet het IP-adres van de API-server. 
* Als u een app of oplossing hebt die moet communiceren  met de API-server, moet u een extra netwerkregel toevoegen om TCP-communicatie toe te staan naar poort 443 van het IP-adres van uw *API-server.*
* In zeldzame gevallen kan het IP-adres van uw API-server worden gewijzigd als er een onderhoudsbewerking wordt uitgevoerd. Geplande onderhoudsbewerkingen die het IP-adres van de API-server kunnen wijzigen, worden altijd vooraf gecommuniceerd.


### <a name="azure-global-required-network-rules"></a>Wereldwijd vereiste netwerkregels voor Azure

De vereiste netwerkregels en IP-adresafhankelijkheden zijn:

| Doel-eindpunt                                                             | Protocol | Poort    | Gebruik  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Of* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Of* <br/> [Regionale CIDR's](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Of* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Voor beveiligde communicatie via tunneling tussen de knooppunten en het besturingsvlak. Dit is niet vereist voor [privéclusters](private-clusters.md)|
| **`*:9000`** <br/> *Of* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Of* <br/> [Regionale CIDR's](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Of* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Voor beveiligde communicatie via tunneling tussen de knooppunten en het besturingsvlak. Dit is niet vereist voor [privéclusters](private-clusters.md) |
| **`*:123`** of **`ntp.ubuntu.com:123`** (als u Azure Firewall netwerkregels)  | UDP      | 123     | Vereist voor NTP-tijdsynchronisatie (Network Time Protocol) op Linux-knooppunten.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Als u aangepaste DNS-servers gebruikt, moet u ervoor zorgen dat ze toegankelijk zijn voor de clusterknooppunten. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Vereist bij het uitvoeren van pods/implementaties die toegang hebben tot de API-server, gebruiken deze pods/implementaties het API-IP-adres. Dit is niet vereist voor [privéclusters](private-clusters.md)  |

### <a name="azure-global-required-fqdn--application-rules"></a>Vereiste FQDN-/toepassingsregels voor Azure Global 

De volgende FQDN-/toepassingsregels zijn vereist:

| Doel-FQDN                 | Poort            | Gebruik      |
|----------------------------------|-----------------|----------|
| **`*.hcp.<location>.azmk8s.io`** | **`HTTPS:443`** | Vereist voor communicatie tussen knooppunt<-> API-server. Vervang *\<location\>* door de regio waarin uw AKS-cluster is geïmplementeerd. |
| **`mcr.microsoft.com`**          | **`HTTPS:443`** | Vereist voor toegang tot afbeeldingen in Microsoft Container Registry (MCR). Dit register bevat eigen afbeeldingen/grafieken (bijvoorbeeld coreDNS, enzovoort). Deze afbeeldingen zijn vereist voor het correct maken en functioneren van het cluster, inclusief schaal- en upgradebewerkingen.  |
| **`*.data.mcr.microsoft.com`**   | **`HTTPS:443`** | Vereist voor MCR-opslag met ondersteuning van het Azure CDN (Content Delivery Network). |
| **`management.azure.com`**       | **`HTTPS:443`** | Vereist voor Kubernetes-bewerkingen met de Azure-API. |
| **`login.microsoftonline.com`**  | **`HTTPS:443`** | Vereist voor Azure Active Directory verificatie. |
| **`packages.microsoft.com`**     | **`HTTPS:443`** | Dit adres is de opslagplaats voor Microsoft-pakketten die wordt gebruikt voor *apt-get-bewerkingen* in de cache.  Voorbeeldpakketten zijn Moby, PowerShell en Azure CLI. |
| **`acs-mirror.azureedge.net`**   | **`HTTPS:443`** | Dit adres is voor de opslagplaats die vereist is voor het downloaden en installeren van vereiste binaire bestanden, zoals kubenet en Azure CNI. |

### <a name="azure-china-21vianet-required-network-rules"></a>Azure China 21Vianet netwerkregels maken

De vereiste netwerkregels en IP-adresafhankelijkheden zijn:

| Doel-eindpunt                                                             | Protocol | Poort    | Gebruik  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Of* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.Region:1194`** <br/> *Of* <br/> [Regionale CIDR's](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Of* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Voor beveiligde communicatie via tunneling tussen de knooppunten en het besturingsvlak. |
| **`*:9000`** <br/> *Of* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Of* <br/> [Regionale CIDR's](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Of* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Voor beveiligde communicatie via tunneling tussen de knooppunten en het besturingsvlak. |
| **`*:22`** <br/> *Of* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:22`** <br/> *Of* <br/> [Regionale CIDR's](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:22`** <br/> *Of* <br/> **`APIServerPublicIP:22`** `(only known after cluster creation)`  | TCP           | 22      | Voor beveiligde communicatie via tunneling tussen de knooppunten en het besturingsvlak. |
| **`*:123`** of **`ntp.ubuntu.com:123`** (als u Azure Firewall netwerkregels)  | UDP      | 123     | Vereist voor NTP-tijdsynchronisatie (Network Time Protocol) op Linux-knooppunten.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Als u aangepaste DNS-servers gebruikt, moet u ervoor zorgen dat ze toegankelijk zijn voor de clusterknooppunten. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Vereist als pods/implementaties worden uitgevoerd die toegang hebben tot de API-server, zouden deze pods/implementaties gebruikmaken van het API-IP-adres.  |

### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure China 21Vianet FQDN/toepassingsregels maken

De volgende FQDN-/toepassingsregels zijn vereist:

| Doel-FQDN                               | Poort            | Gebruik      |
|------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Vereist voor communicatie tussen knooppunt<->-API-server. Vervang *\<location\>* door de regio waarin uw AKS-cluster is geïmplementeerd. |
| **`*.tun.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Vereist voor communicatie tussen knooppunt<-> API-server. Vervang *\<location\>* door de regio waarin uw AKS-cluster is geïmplementeerd. |
| **`mcr.microsoft.com`**                        | **`HTTPS:443`** | Vereist voor toegang tot afbeeldingen in Microsoft Container Registry (MCR). Dit register bevat eigen afbeeldingen/grafieken (bijvoorbeeld coreDNS, enzovoort). Deze afbeeldingen zijn vereist voor het juiste maken en functioneren van het cluster, inclusief schaal- en upgradebewerkingen. |
| **`.data.mcr.microsoft.com`**                  | **`HTTPS:443`** | Vereist voor MCR-opslag met ondersteuning van de Azure Content Delivery Network (CDN). |
| **`management.chinacloudapi.cn`**              | **`HTTPS:443`** | Vereist voor Kubernetes-bewerkingen met de Azure-API. |
| **`login.chinacloudapi.cn`**                   | **`HTTPS:443`** | Vereist voor Azure Active Directory verificatie. |
| **`packages.microsoft.com`**                   | **`HTTPS:443`** | Dit adres is de opslagplaats voor Microsoft-pakketten die wordt gebruikt voor *apt-get-bewerkingen* in de cache.  Voorbeeldpakketten zijn Moby, PowerShell en Azure CLI. |
| **`*.azk8s.cn`**                               | **`HTTPS:443`** | Dit adres is voor de opslagplaats die vereist is voor het downloaden en installeren van vereiste binaire bestanden, zoals kubenet en Azure CNI. |

### <a name="azure-us-government-required-network-rules"></a>Vereiste netwerkregels voor Azure voor de Amerikaanse overheid

De vereiste netwerkregels en IP-adresafhankelijkheden zijn:

| Doel-eindpunt                                                             | Protocol | Poort    | Gebruik  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Of* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Of* <br/> [Regionale CIDR's](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Of* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Voor beveiligde communicatie via tunneling tussen de knooppunten en het besturingsvlak. |
| **`*:9000`** <br/> *Of* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Of* <br/> [Regionale CIDR's](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Of* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Voor beveiligde communicatie via tunneling tussen de knooppunten en het besturingsvlak. |
| **`*:123`** of **`ntp.ubuntu.com:123`** (als u Azure Firewall netwerkregels)  | UDP      | 123     | Vereist voor NTP-tijdsynchronisatie (Network Time Protocol) op Linux-knooppunten.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Als u aangepaste DNS-servers gebruikt, moet u ervoor zorgen dat ze toegankelijk zijn voor de clusterknooppunten. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Vereist als pods/implementaties worden uitgevoerd die toegang hebben tot de API-server, gebruiken deze pods/implementaties het API-IP-adres.  |

### <a name="azure-us-government-required-fqdn--application-rules"></a>Vereiste FQDN-/toepassingsregels voor Azure voor de Amerikaanse overheid 

De volgende FQDN-/toepassingsregels zijn vereist:

| Doel-FQDN                                        | Poort            | Gebruik      |
|---------------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.aks.containerservice.azure.us`** | **`HTTPS:443`** | Vereist voor communicatie tussen knooppunt<-> API-server. Vervang *\<location\>* door de regio waarin uw AKS-cluster is geïmplementeerd.|
| **`mcr.microsoft.com`**                                 | **`HTTPS:443`** | Vereist voor toegang tot afbeeldingen in Microsoft Container Registry (MCR). Dit register bevat eigen afbeeldingen/grafieken (bijvoorbeeld coreDNS, enzovoort). Deze afbeeldingen zijn vereist voor het juiste maken en functioneren van het cluster, inclusief schaal- en upgradebewerkingen. |
| **`*.data.mcr.microsoft.com`**                          | **`HTTPS:443`** | Vereist voor MCR-opslag met ondersteuning van het Azure CDN (Content Delivery Network). |
| **`management.usgovcloudapi.net`**                      | **`HTTPS:443`** | Vereist voor Kubernetes-bewerkingen met de Azure-API. |
| **`login.microsoftonline.us`**                          | **`HTTPS:443`** | Vereist voor Azure Active Directory verificatie. |
| **`packages.microsoft.com`**                            | **`HTTPS:443`** | Dit adres is de opslagplaats voor Microsoft-pakketten die wordt gebruikt voor *apt-get-bewerkingen* in de cache.  Voorbeeldpakketten zijn Moby, PowerShell en Azure CLI. |
| **`acs-mirror.azureedge.net`**                          | **`HTTPS:443`** | Dit adres is voor de opslagplaats die vereist is voor het installeren van vereiste binaire bestanden, zoals kubenet en Azure CNI. |

## <a name="optional-recommended-fqdn--application-rules-for-aks-clusters"></a>Optionele aanbevolen FQDN/toepassingsregels voor AKS-clusters

De volgende FQDN-/toepassingsregels zijn optioneel, maar worden aanbevolen voor AKS-clusters:

| Doel-FQDN                                                               | Poort          | Gebruik      |
|--------------------------------------------------------------------------------|---------------|----------|
| **`security.ubuntu.com`, `azure.archive.ubuntu.com`, `changelogs.ubuntu.com`** | **`HTTP:80`** | Met dit adres kunnen de Linux-clusterknooppunten de vereiste beveiligingspatches en updates downloaden. |

Als u ervoor kiest om deze FQDN's te blokkeren of [](node-image-upgrade.md) niet toe te staan, ontvangen de knooppunten alleen updates van het besturingssysteem wanneer u een upgrade van de knooppuntafbeelding of [clusterupgrade hebt uitgevoerd.](upgrade-cluster.md)

## <a name="gpu-enabled-aks-clusters"></a>AKS-clusters met GPU

### <a name="required-fqdn--application-rules"></a>Vereiste FQDN-/toepassingsregels

De volgende FQDN-/toepassingsregels zijn vereist voor AKS-clusters met GPU ingeschakeld:

| Doel-FQDN                        | Poort      | Gebruik      |
|-----------------------------------------|-----------|----------|
| **`nvidia.github.io`**                  | **`HTTPS:443`** | Dit adres wordt gebruikt voor de juiste installatie en bewerking van stuurprogramma's op GPU-knooppunten. |
| **`us.download.nvidia.com`**            | **`HTTPS:443`** | Dit adres wordt gebruikt voor de juiste installatie en bewerking van stuurprogramma's op GPU-knooppunten. |
| **`apt.dockerproject.org`**             | **`HTTPS:443`** | Dit adres wordt gebruikt voor de juiste installatie en bewerking van stuurprogramma's op GPU-knooppunten. |

## <a name="windows-server-based-node-pools"></a>Op Windows Server gebaseerde knooppuntgroepen 

### <a name="required-fqdn--application-rules"></a>Vereiste FQDN/toepassingsregels

De volgende FQDN-/toepassingsregels zijn vereist voor het gebruik van op Windows Server gebaseerde knooppuntgroepen:

| Doel-FQDN                                                           | Poort      | Gebruik      |
|----------------------------------------------------------------------------|-----------|----------|
| **`onegetcdn.azureedge.net, go.microsoft.com`**                            | **`HTTPS:443`** | Windows-gerelateerde binaire bestanden installeren |
| **`*.mp.microsoft.com, www.msftconnecttest.com, ctldl.windowsupdate.com`** | **`HTTP:80`**   | Windows-gerelateerde binaire bestanden installeren |

## <a name="aks-addons-and-integrations"></a>AKS-addons en integraties

### <a name="azure-monitor-for-containers"></a>Azure Monitor voor containers

Er zijn twee opties om toegang te bieden tot Azure Monitor voor containers. U kunt de Azure Monitor [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags)  toestaan of toegang verlenen tot de vereiste FQDN-/toepassingsregels.

#### <a name="required-network-rules"></a>Vereiste netwerkregels

De volgende FQDN-/toepassingsregels zijn vereist:

| Doel-eindpunt                                                             | Protocol | Poort    | Gebruik  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | Dit eindpunt wordt gebruikt voor het verzenden van metrische gegevens en logboeken naar Azure Monitor en Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Vereiste FQDN/toepassingsregels

De volgende FQDN-/toepassingsregels zijn vereist voor AKS-clusters Azure Monitor voor containers zijn ingeschakeld:

| FQDN                                    | Poort      | Gebruik      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | **`HTTPS:443`**    | Dit eindpunt wordt gebruikt voor metrische gegevens en het bewaken van telemetriegegevens met behulp Azure Monitor. |
| *.ods.opinsights.azure.com    | **`HTTPS:443`**    | Dit eindpunt wordt gebruikt door Azure Monitor voor het opnemen van logboekanalysegegevens. |
| *.oms.opinsights.azure.com | **`HTTPS:443`** | Dit eindpunt wordt gebruikt door omsagent, dat wordt gebruikt om de Log Analytics-service te verifiëren. |
| *.monitoring.azure.com | **`HTTPS:443`** | Dit eindpunt wordt gebruikt voor het verzenden van metrische gegevens naar Azure Monitor. |

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

Werk uw firewall of beveiligingsconfiguratie bij om netwerkverkeer van en naar alle onderstaande FQDN's en [Azure Dev Spaces-infrastructuurservices toe te staan.][dev-spaces-service-tags]

#### <a name="required-network-rules"></a>Vereiste netwerkregels

| Doel-eindpunt                                                             | Protocol | Poort    | Gebruik  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureDevSpaces`**  | TCP           | 443      | Dit eindpunt wordt gebruikt voor het verzenden van metrische gegevens en logboeken naar Azure Monitor en Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Vereiste FQDN-/toepassingsregels 

De volgende FQDN-/toepassingsregels zijn vereist voor AKS-clusters waarin Azure Dev Spaces is ingeschakeld:

| FQDN                                    | Poort      | Gebruik      |
|-----------------------------------------|-----------|----------|
| `cloudflare.docker.com` | **`HTTPS:443`** | Dit adres wordt gebruikt om alpine linux- en andere Azure Dev Spaces-afbeeldingen op te halen |
| `gcr.io` | **`HTTPS:443`** | Dit adres wordt gebruikt om Helm-/Tiller-afbeeldingen op te halen |
| `storage.googleapis.com` | **`HTTPS:443`** | Dit adres wordt gebruikt om Helm-/Tiller-afbeeldingen op te halen |


### <a name="azure-policy"></a>Azure Policy

#### <a name="required-fqdn--application-rules"></a>Vereiste FQDN-/toepassingsregels 

De volgende FQDN-/toepassingsregels zijn vereist voor AKS-clusters met Azure Policy ingeschakeld.

| FQDN                                          | Poort      | Gebruik      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.core.windows.net`** | **`HTTPS:443`** | Dit adres wordt gebruikt om het Kubernetes-beleid op te halen en om de nalevingsstatus van het cluster te rapporteren aan de beleidsservice. |
| **`store.policy.core.windows.net`** | **`HTTPS:443`** | Dit adres wordt gebruikt om de Gatekeeper-artefacten van ingebouwde beleidsregels op te halen. |
| **`gov-prod-policy-data.trafficmanager.net`** | **`HTTPS:443`** | Dit adres wordt gebruikt voor de juiste werking van Azure Policy.  |
| **`raw.githubusercontent.com`**               | **`HTTPS:443`** | Dit adres wordt gebruikt om het ingebouwde beleid op te halen uit GitHub om de juiste werking van de Azure Policy. |
| **`dc.services.visualstudio.com`**            | **`HTTPS:443`** | Azure Policy-invoegtoepassingen die telemetriegegevens naar het eindpunt van Applications Insights verzendt. |

#### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure China 21Vianet FQDN/toepassingsregels maken 

De volgende FQDN-/toepassingsregels zijn vereist voor AKS-clusters die de Azure Policy ingeschakeld.

| FQDN                                          | Poort      | Gebruik      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.cn`** | **`HTTPS:443`** | Dit adres wordt gebruikt om het Kubernetes-beleid op te halen en om de nalevingsstatus van het cluster te rapporteren aan de beleidsservice. |
| **`store.policy.azure.cn`** | **`HTTPS:443`** | Dit adres wordt gebruikt om de Gatekeeper-artefacten van ingebouwde beleidsregels op te halen. |

#### <a name="azure-us-government-required-fqdn--application-rules"></a>Vereiste FQDN/toepassingsregels voor Azure US Government

De volgende FQDN-/toepassingsregels zijn vereist voor AKS-clusters die de Azure Policy ingeschakeld.

| FQDN                                          | Poort      | Gebruik      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.us`** | **`HTTPS:443`** | Dit adres wordt gebruikt om het Kubernetes-beleid op te halen en om de nalevingsstatus van het cluster te rapporteren aan de beleidsservice. |
| **`store.policy.azure.us`** | **`HTTPS:443`** | Dit adres wordt gebruikt om de Gatekeeper-artefacten van ingebouwde beleidsregels op te halen. |

## <a name="restrict-egress-traffic-using-azure-firewall"></a>Verkeer voor verkeer dat uit de cloud komt beperken met behulp van Azure Firewall

Azure Firewall biedt een Azure Kubernetes Service ( `AzureKubernetesService` ) FQDN-tag om deze configuratie te vereenvoudigen. 

> [!NOTE]
> De FQDN-tag bevat alle hierboven vermelde FQDN's en wordt automatisch up-to-date gehouden.
>
> We raden u aan minimaal 20 front-end-IP's op de Azure Firewall voor productiescenario's te gebruiken om te voorkomen dat er problemen met SNAT-poortuitputting zijn.

Hieronder vindt u een voorbeeldarchitectuur van de implementatie:

![Vergrendelde topologie](media/limit-egress-traffic/aks-azure-firewall-egress.png)

* Openbaar toegangsgress wordt gedwongen om door firewallfilters te stromen
  * AKS-agentknooppunten worden geïsoleerd in een toegewezen subnet.
  * [Azure Firewall](../firewall/overview.md) wordt geïmplementeerd in een eigen subnet.
  * Met een DNAT-regel wordt het openbare IP-adres van de fw omgezet in het IP-adres van de LB-front-end.
* Uitgaande aanvragen starten vanaf agentknooppunten naar het interne IP Azure Firewall met behulp van een [door de gebruiker gedefinieerde route](egress-outboundtype.md)
  * Aanvragen van AKS-agentknooppunten volgen een UDR die is geplaatst op het subnet waar het AKS-cluster in is geïmplementeerd.
  * Azure Firewall het virtuele netwerk uit vanaf een openbare IP-front-out
  * Toegang tot het openbare internet of andere Azure-services loopt van en naar het FRONT-adres van de firewall-front-en-over
  * Optioneel wordt toegang tot het AKS-besturingsvlak beveiligd door de [API-server](./api-server-authorized-ip-ranges.md)Geautoriseerde IP-bereiken, waaronder het openbare front-end-IP-adres van de firewall.
* Intern verkeer
  * U kunt in plaats van of naast een openbare Load Balancer ook een [interne](internal-lb.md) [Load Balancer](load-balancer-standard.md) gebruiken voor intern verkeer, dat u ook op een eigen subnet kunt isoleren.


In de onderstaande stappen wordt gebruik gemaakt van de FQDN-tag van Azure Firewall om het uitgaande verkeer van het AKS-cluster te beperken en een voorbeeld te geven van het configureren van openbaar inkomende verkeer via de `AzureKubernetesService` firewall.


### <a name="set-configuration-via-environment-variables"></a>Configuratie instellen via omgevingsvariabelen

Definieer een set omgevingsvariabelen die moeten worden gebruikt bij het maken van resources.

```bash
PREFIX="aks-egress"
RG="${PREFIX}-rg"
LOC="eastus"
PLUGIN=azure
AKSNAME="${PREFIX}"
VNET_NAME="${PREFIX}-vnet"
AKSSUBNET_NAME="aks-subnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}-fw"
FWPUBLICIP_NAME="${PREFIX}-fwpublicip"
FWIPCONFIG_NAME="${PREFIX}-fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}-fwrt"
FWROUTE_NAME="${PREFIX}-fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}-fwinternet"
```

### <a name="create-a-virtual-network-with-multiple-subnets"></a>Een virtueel netwerk met meerdere subnetten maken

Een virtueel netwerk inrichten met twee afzonderlijke subnetten, één voor het cluster, één voor de firewall. Desgewenst kunt u er ook een maken voor intern service-ingress.

![Lege netwerktopologie](media/limit-egress-traffic/empty-network.png)

Maak een resourcegroep voor alle resources.

```azurecli
# Create Resource Group

az group create --name $RG --location $LOC
```

Maak een virtueel netwerk met twee subnetten voor het hosten van het AKS-cluster en de Azure Firewall. Elk heeft een eigen subnet. Laten we beginnen met het AKS-netwerk.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --location $LOC \
    --address-prefixes 10.42.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 10.42.1.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 10.42.2.0/24
```

### <a name="create-and-set-up-an-azure-firewall-with-a-udr"></a>Een Azure Firewall maken en instellen met een UDR

Azure Firewall inkomende en uitgaande regels moeten worden geconfigureerd. Het belangrijkste doel van de firewall is om organisaties in staat te stellen gedetailleerde regels voor in- en uitverkeer naar en uit het AKS-cluster te configureren.

![Firewall en UDR](media/limit-egress-traffic/firewall-udr.png)


> [!IMPORTANT]
> Als uw cluster of toepassing een groot aantal uitgaande verbindingen maakt die zijn omgeleid naar dezelfde of een kleine subset van bestemmingen, hebt u mogelijk meer front-end-IP-adressen voor de firewall nodig om te voorkomen dat de poorten per front-end-IP worden uitbesteed.
> Meer informatie over het maken van een Azure-firewall met meerdere IP's vindt u [ **hier**](../firewall/quick-create-multiple-ip-template.md)

Maak een openbare IP-resource van de standaard-SKU die wordt gebruikt als het Azure Firewall front-Azure Firewall adres.

```azurecli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Registreer de preview-cli-extensie om een nieuwe Azure Firewall.
```azurecli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC --enable-dns-proxy true
```
Het IP-adres dat u eerder hebt gemaakt, kan nu worden toegewezen aan de firewall-front-end.

> [!NOTE]
> Het instellen van het openbare IP-adres voor Azure Firewall kan enkele minuten duren.
> Als u FQDN wilt gebruiken voor netwerkregels, moet de DNS-proxy zijn ingeschakeld. Wanneer deze is ingeschakeld, luistert de firewall op poort 53 en worden DNS-aanvragen doorgestuurd naar de DNS-server die hierboven is opgegeven. Hierdoor kan de firewall die FQDN automatisch vertalen.

```azurecli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Wanneer de vorige opdracht is geslaagd, moet u het front-end-IP-adres van de firewall later opslaan voor configuratie.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

> [!NOTE]
> Als u beveiligde toegang tot de [](./api-server-authorized-ip-ranges.md)AKS API-server met geautoriseerde IP-adresbereiken gebruikt, moet u het openbare IP-adres van de firewall toevoegen aan het geautoriseerde IP-bereik.

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Een UDR maken met een hop naar Azure Firewall

Azure routeert automatisch verkeer tussen Azure-subnetten, virtuele netwerken en on-premises netwerken. Als u de standaardroutering van Azure wilt wijzigen, doet u dit door een routetabel te maken.

Maak een lege routetabel die moet worden gekoppeld aan een bepaald subnet. De routetabel definieert de volgende hop als de Azure Firewall hierboven is gemaakt. Elk subnet kan zijn gekoppeld aan geen enkele of één routetabel.

```azurecli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG -l $LOC --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Zie [documentatie over routetabel voor](../virtual-network/virtual-networks-udr-overview.md#user-defined) virtuele netwerken over hoe u de standaardsysteemroutes van Azure kunt overschrijven of aanvullende routes kunt toevoegen aan de routetabel van een subnet.

### <a name="adding-firewall-rules"></a>Firewallregels toevoegen

Hieronder vindt u drie netwerkregels die u kunt gebruiken om te configureren op uw firewall. Mogelijk moet u deze regels aanpassen op basis van uw implementatie. Met de eerste regel is toegang tot poort 9000 via TCP mogelijk. Met de tweede regel hebt u toegang tot poort 1194 en 123 via UDP (als u implementeert in Azure China 21Vianet, hebt u mogelijk meer [nodig).](#azure-china-21vianet-required-network-rules) Met beide regels is alleen verkeer toegestaan dat is bestemd voor de AZURE-regio-CIDR die we gebruiken, in dit geval VS - oost. Ten slotte voegen we een derde netwerkregel toe die poort 123 opent naar FQDN via UDP (het toevoegen van een FQDN als een netwerkregel is een van de specifieke functies van Azure Firewall en u moet deze aanpassen wanneer u uw eigen opties `ntp.ubuntu.com` gebruikt).

Na het instellen van de netwerkregels voegen we ook een toepassingsregel toe met behulp van de die betrekking heeft op alle benodigde FQDN's die toegankelijk zijn `AzureKubernetesService` via TCP-poort 443 en poort 80.

```
# Add FW Network Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apiudp' --protocols 'UDP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 1194 --action allow --priority 100
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apitcp' --protocols 'TCP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 9000
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'time' --protocols 'UDP' --source-addresses '*' --destination-fqdns 'ntp.ubuntu.com' --destination-ports 123

# Add FW Application Rules

az network firewall application-rule create -g $RG -f $FWNAME --collection-name 'aksfwar' -n 'fqdn' --source-addresses '*' --protocols 'http=80' 'https=443' --fqdn-tags "AzureKubernetesService" --action allow --priority 100
```

Zie [Azure Firewall documentatie](../firewall/overview.md) voor meer informatie over de Azure Firewall service.

### <a name="associate-the-route-table-to-aks"></a>De routetabel koppelen aan AKS

Als u het cluster wilt koppelen aan de firewall, moet het toegewezen subnet voor het subnet van het cluster verwijzen naar de hierboven gemaakte routetabel. U kunt een associatie tot stand brengen door een opdracht uit te geven aan het virtuele netwerk met zowel het cluster als de firewall om de routetabel van het subnet van het cluster bij te werken.

```azurecli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

### <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>AKS met uitgaand type UDR implementeren in het bestaande netwerk

Nu kan een AKS-cluster worden geïmplementeerd in het bestaande virtuele netwerk. We gebruiken ook uitgaand [type `userDefinedRouting` ](egress-outboundtype.md). Deze functie zorgt ervoor dat uitgaand verkeer wordt geforceerd via de firewall en dat er geen andere uitgaande paden bestaan (standaard kan het Load Balancer uitgaand type worden gebruikt).

![aks-deploy](media/limit-egress-traffic/aks-udr-fw.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Een service-principal maken met toegang voor inrichting binnen het bestaande virtuele netwerk

Een clusteridentiteit (beheerde identiteit of service-principal) wordt door AKS gebruikt om clusterbronnen te maken. Een service-principal die tijdens het maken wordt doorgegeven, wordt gebruikt om onderliggende AKS-resources te maken, zoals Opslagbronnen, IP's en Load Balancers die worden gebruikt door AKS (u kunt in plaats daarvan ook een beheerde identiteit [gebruiken).](use-managed-identity.md) Als u niet de juiste machtigingen hieronder krijgt, kunt u het AKS-cluster niet inrichten.

```azurecli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Vervang nu de en hieronder door de appid van de service-principal en het wachtwoord van de service-principal die automatisch zijn `APPID` gegenereerd door de vorige `PASSWORD` opdrachtuitvoer. We verwijzen naar de resource-id van het VNET om de machtigingen aan de service-principal te verlenen, zodat AKS er resources in kan implementeren.

```azurecli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role "Network Contributor"
```

U kunt hier de gedetailleerde machtigingen controleren die vereist [zijn.](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)

> [!NOTE]
> Als u de invoegvoegsel kubenet network gebruikt, moet u de AKS-service-principal of beheerde identiteit machtigingen geven voor de vooraf gemaakte routetabel, omdat kubenet een routetabel vereist om regels voor doorsturen toe te voegen. 
> ```azurecli-interactive
> RTID=$(az network route-table show -g $RG -n $FWROUTE_TABLE_NAME --query id -o tsv)
> az role assignment create --assignee $APPID --scope $RTID --role "Network Contributor"
> ```

### <a name="deploy-aks"></a>AKS implementeren

Ten slotte kan het AKS-cluster worden geïmplementeerd in het bestaande subnet dat we voor het cluster hebben toegewezen. Het doelsubnet waarin moet worden geïmplementeerd, wordt gedefinieerd met de omgevingsvariabele , `$SUBNETID` . In de vorige stappen hebben we `$SUBNETID` de variabele niet definiëren. Als u de waarde voor de subnet-id wilt instellen, kunt u de volgende opdracht gebruiken:

```azurecli
SUBNETID=$(az network vnet subnet show -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --query id -o tsv)
```

U definieert het uitgaande type om de UDR te gebruiken die al in het subnet bestaat. Met deze configuratie kan AKS de installatie en IP-inrichting voor de load balancer.

> [!IMPORTANT]
> Zie uitgaand uitgaand type UDR voor meer informatie over uitgaande type [**UDR,**](egress-outboundtype.md#limitations)inclusief beperkingen.


> [!TIP]
> Er kunnen extra functies worden toegevoegd aan de clusterimplementatie, zoals [**Privécluster.**](private-clusters.md) 
>
> De AKS-functie voor geautoriseerde IP-bereiken van de [**API-server**](api-server-authorized-ip-ranges.md) kan worden toegevoegd om de API-servertoegang te beperken tot alleen het openbare eindpunt van de firewall. De functie geautoriseerde IP-adresbereiken wordt in het diagram aangeduid als optioneel. Wanneer u de functie voor geautoriseerd IP-bereik inschakelen om de toegang tot de API-server te beperken, moeten uw ontwikkelhulpprogramma's een jumpbox gebruiken vanuit het virtuele netwerk van de firewall of moet u alle eindpunten voor ontwikkelaars toevoegen aan het geautoriseerde IP-bereik.

```azurecli
az aks create -g $RG -n $AKSNAME -l $LOC \
  --node-count 3 --generate-ssh-keys \
  --network-plugin $PLUGIN \
  --outbound-type userDefinedRouting \
  --service-cidr 10.41.0.0/16 \
  --dns-service-ip 10.41.0.10 \
  --docker-bridge-address 172.17.0.1/16 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
```

### <a name="enable-developer-access-to-the-api-server"></a>Toegang van ontwikkelaars tot de API-server inschakelen

Als u in de vorige stap geautoriseerde IP-bereiken voor het cluster hebt gebruikt, moet u de IP-adressen van uw ontwikkelhulpprogramma toevoegen aan de AKS-clusterlijst met goedgekeurde IP-bereiken om van hieruit toegang te krijgen tot de API-server. Een andere optie is om een jumpbox te configureren met de benodigde hulpprogramma's binnen een afzonderlijk subnet in het virtuele netwerk van de firewall.

Voeg nog een IP-adres toe aan de goedgekeurde reeksen met de volgende opdracht

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Gebruik de opdracht [az aks get-credentials][az-aks-get-credentials] om te configureren dat verbinding wordt gemaakt met uw zojuist gemaakte `kubectl` Kubernetes-cluster. 

 ```azurecli
 az aks get-credentials -g $RG -n $AKSNAME
 ```

### <a name="deploy-a-public-service"></a>Een openbare service implementeren
U kunt nu beginnen met het blootstellen van services en het implementeren van toepassingen in dit cluster. In dit voorbeeld geven we een openbare service weer, maar u kunt er ook voor kiezen om een interne service beschikbaar te maken via [interne load balancer](internal-lb.md).

![DNAT van openbare service](media/limit-egress-traffic/aks-create-svc.png)

Implementeer de Azure-stem-app door de onderstaande yaml te kopiëren naar een bestand met de naam `example.yaml` .

```yaml
# voting-storage-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-storage
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-storage
  template:
    metadata:
      labels:
        app: voting-storage
    spec:
      containers:
      - name: voting-storage
        image: mcr.microsoft.com/aks/samples/voting/storage:2.0
        args: ["--ignore-db-dir=lost+found"]
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
---
# voting-storage-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: voting-storage-secret
type: Opaque
data:
  MYSQL_USER: ZGJ1c2Vy
  MYSQL_PASSWORD: UGFzc3dvcmQxMg==
  MYSQL_DATABASE: YXp1cmV2b3Rl
  MYSQL_ROOT_PASSWORD: UGFzc3dvcmQxMg==
---
# voting-storage-pv-claim.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
# voting-storage-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-storage
  labels: 
    app: voting-storage
spec:
  ports:
  - port: 3306
    name: mysql
  selector:
    app: voting-storage
---
# voting-app-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-app
  template:
    metadata:
      labels:
        app: voting-app
    spec:
      containers:
      - name: voting-app
        image: mcr.microsoft.com/aks/samples/voting/app:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
        - name: ANALYTICS_HOST
          value: "voting-analytics"
---
# voting-app-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-app
  labels: 
    app: voting-app
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8080
    name: http
  selector:
    app: voting-app
---
# voting-analytics-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-analytics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-analytics
      version: "2.0"
  template:
    metadata:
      labels:
        app: voting-analytics
        version: "2.0"
    spec:
      containers:
      - name: voting-analytics
        image: mcr.microsoft.com/aks/samples/voting/analytics:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
---
# voting-analytics-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-analytics
  labels: 
    app: voting-analytics
spec:
  ports:
  - port: 8080
    name: http
  selector:
    app: voting-analytics
```

Implementeer de service door het volgende uit te uitvoeren:

```bash
kubectl apply -f example.yaml
```

### <a name="add-a-dnat-rule-to-azure-firewall"></a>Een DNAT-regel toevoegen aan Azure Firewall

> [!IMPORTANT]
> Wanneer u Azure Firewall gebruikt om het verkeer te beperken en een door de gebruiker gedefinieerde route (UDR) maakt om al het verkeer af te dwingen, moet u een geschikte DNAT-regel in Firewall maken om het toegangsverkeer op de juiste manier toe te staan. Als Azure Firewall met een UDR gebruikt, wordt de instelling van het ingress-verkeer door asymmetrische routering door een onderbreking veroorzaakt. (Het probleem treedt op als het AKS-subnet een standaardroute heeft die naar het privé-IP-adres van de firewall gaat, maar u een openbare load balancer gebruikt- toegangs- of Kubernetes-service van het type: LoadBalancer). In dit geval wordt het binnenkomende load balancer ontvangen via het openbare IP-adres, maar loopt het retourpad via het privé-IP-adres van de firewall. Omdat de firewall stateful is, wordt het retourpakket uit de firewall daalt omdat de firewall niet op de hoogte is van een tot stand gebrachte sessie. Zie Integrate Azure Firewall with Azure Standard Load Balancer (Integratie van Azure Firewall met Azure Standard Load Balancer) voor meer informatie over het integreren van Azure Firewall met uw load balancer voor [Standard Load Balancer.](../firewall/integrate-lb.md)


Als u binnenkomende connectiviteit wilt configureren, moet er een DNAT-regel naar de Azure Firewall. Om de connectiviteit met uw cluster te testen, wordt er een regel gedefinieerd voor het openbare IP-adres van de front-end van de firewall om te worden gerouteerd naar het interne IP-adres dat wordt blootgesteld door de interne service.

Het doeladres kan worden aangepast omdat dit de poort op de firewall is die moet worden gebruikt. Het vertaalde adres moet het IP-adres van de interne load balancer. De vertaalde poort moet de ontsluisde poort voor uw Kubernetes-service zijn.

U moet het interne IP-adres opgeven dat is toegewezen aan de load balancer gemaakt door de Kubernetes-service. Haal het adres op door het volgende uit te uitvoeren:

```bash
kubectl get services
```

Het benodigde IP-adres wordt weergegeven in de kolom EXTERNAL-IP, vergelijkbaar met het volgende.

```bash
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP      10.41.0.1       <none>        443/TCP        10h
voting-analytics   ClusterIP      10.41.88.129    <none>        8080/TCP       9m
voting-app         LoadBalancer   10.41.185.82    20.39.18.6    80:32718/TCP   9m
voting-storage     ClusterIP      10.41.221.201   <none>        3306/TCP       9m
```

Haal het IP-adres van de service op door het volgende uit te uitvoeren:
```bash
SERVICE_IP=$(kubectl get svc voting-app -o jsonpath='{.status.loadBalancer.ingress[*].ip}')
```

Voeg de NAT-regel toe door het volgende uit te uitvoeren:
```azurecli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address $SERVICE_IP
```

### <a name="validate-connectivity"></a>Connectiviteit valideren

Navigeer naar Azure Firewall front-end-IP-adres in een browser om de connectiviteit te valideren.

U ziet nu de AKS-stem-app. In dit voorbeeld was het openbare IP-adres van de firewall `52.253.228.132` .


![Schermopname van de A K S Voting-app met knoppen voor katten, honden en opnieuw instellen, en totalen.](media/limit-egress-traffic/aks-vote.png)


### <a name="clean-up-resources"></a>Resources opschonen

Verwijder de AKS-resourcegroep om Azure-resources op te schonen.

```azurecli
az group delete -g $RG
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd welke poorten en adressen moeten worden toegestaan als u het toegangsverkeer voor het cluster wilt beperken. U hebt ook gezien hoe u uw uitgaande verkeer kunt beveiligen met behulp Azure Firewall. 

Indien nodig kunt u de bovenstaande stappen generaliseren om het verkeer door testuren naar de uitgaande oplossing van uw [ `userDefinedRoute` voorkeur,](egress-outboundtype.md)volgens de documentatie voor uitgaand type .

Zie Verkeer tussen pods beveiligen met behulp van netwerkbeleid in AKS als u de communicatie tussen pods tussen zichzelf en de East-West binnen het cluster [wilt beperken.][network-policy]

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[dev-spaces-service-tags]: ../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations
