---
title: Uitgaand verkeer beperken in azure Kubernetes service (AKS)
description: Meer informatie over welke poorten en adressen vereist zijn voor het beheren van uitgaand verkeer in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.author: jpalma
ms.date: 01/12/2021
author: palma21
ms.openlocfilehash: 9e65e2736578ce04dfa79d5a7827e190d47fb312
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103573826"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Uitgaand verkeer beheren voor cluster knooppunten in azure Kubernetes service (AKS)

Dit artikel bevat de benodigde details waarmee u uitgaand verkeer van uw Azure Kubernetes-service (AKS) kunt beveiligen. Het bevat de cluster vereisten voor de implementatie van een basis AKS en aanvullende vereisten voor optionele addons en functies. [Er wordt een voor beeld weer gegeven aan het einde van de configuratie van deze vereisten met Azure firewall](#restrict-egress-traffic-using-azure-firewall). U kunt deze informatie echter Toep assen op elke uitgaande beperkings methode of apparaat.

## <a name="background"></a>Achtergrond

AKS-clusters worden geïmplementeerd in een virtueel netwerk. Dit netwerk kan worden beheerd (gemaakt door AKS) of aangepast (vooraf geconfigureerd door de gebruiker). In beide gevallen heeft het cluster **uitgaande** afhankelijkheden voor services buiten dat virtuele netwerk (de service heeft geen inkomende afhankelijkheden).

Voor beheer-en operationele doel einden moeten knoop punten in een AKS-cluster toegang hebben tot bepaalde poorten en FQDN-namen (Fully Qualified Domain Name). Deze eind punten zijn vereist voor de communicatie tussen de knoop punten en de API-server, of voor het downloaden en installeren van kern Kubernetes-cluster onderdelen en beveiligings updates voor knoop punten. Het cluster moet bijvoorbeeld basis installatie kopieën van het-systeem container ophalen van micro soft Container Registry (MCR).

De uitgaande afhankelijkheden voor AKS zijn bijna volledig gedefinieerd met FQDN-adressen. Het ontbreken van statische adressen betekent dat netwerk beveiligings groepen niet kunnen worden gebruikt voor het vergren delen van het uitgaande verkeer van een AKS-cluster. 

Standaard hebben AKS-clusters onbeperkte uitgaande (uitgaand) Internet toegang. Met dit niveau van netwerk toegang kunnen knoop punten en services die u uitvoert om toegang te krijgen tot externe bronnen als dat nodig is. Als u uitgaand verkeer wilt beperken, moet een beperkt aantal poorten en adressen toegankelijk zijn voor het onderhouden van goede cluster onderhouds taken. De eenvoudigste oplossing voor het beveiligen van uitgaande adressen berust op het gebruik van een firewall apparaat waarmee het uitgaande verkeer op basis van domein namen kan worden beheerd. Azure Firewall kan bijvoorbeeld het uitgaande HTTP-en HTTPS-verkeer beperken op basis van de FQDN van de bestemming. U kunt ook uw voorkeurs firewall en beveiligings regels configureren om deze vereiste poorten en adressen toe te staan.

> [!IMPORTANT]
> In dit document wordt alleen beschreven hoe u het verkeer vergrendelt dat het AKS-subnet verlaat. AKS heeft standaard geen ingangs vereisten.  Het blok keren van het **verkeer van interne subnetten** met netwerk beveiligings groepen (nsg's) en firewalls wordt niet ondersteund. Gebruik [**_netwerk beleid_**][network-policy]om het verkeer binnen het cluster te beheren en te blok keren.

## <a name="required-outbound-network-rules-and-fqdns-for-aks-clusters"></a>Vereiste uitgaande netwerk regels en FQDN voor AKS-clusters

Het volgende netwerk en de FQDN/toepassings regels zijn vereist voor een AKS-cluster. u kunt deze gebruiken als u een andere oplossing dan Azure Firewall wilt configureren.

* Afhankelijkheden van IP-adressen zijn voor niet-HTTP/S-verkeer (TCP-en UDP-verkeer)
* FQDN HTTP/HTTPS-eind punten kunnen worden geplaatst op het apparaat van de firewall.
* Joker tekens voor HTTP/HTTPS-eind punten zijn afhankelijkheden die kunnen variëren met uw AKS-cluster op basis van een aantal kwalificaties.
* AKS maakt gebruik van een toegangs controller om de FQDN als een omgevings variabele in te voeren voor alle implementaties onder uitvoeren-System en gate keeper-System, waardoor alle systeem communicatie tussen knoop punten en API server de FQDN van de API-server en niet het API-server-IP-adres gebruikt. 
* Als u een app of oplossing hebt die moet communiceren met de API-server, moet u een **extra** netwerk regel toevoegen om *TCP-communicatie toe te staan aan poort 443 van het IP-adres van uw API-server*.
* Als er sprake is van een onderhouds bewerking kan uw API-server-IP in zeldzame gevallen worden gewijzigd. Geplande onderhouds bewerkingen die het API server-IP-adres kunnen wijzigen, worden altijd vooraf gecommuniceerd.


### <a name="azure-global-required-network-rules"></a>Algemene Azure-netwerk regels vereist

De vereiste netwerk regels en afhankelijkheden voor IP-adressen zijn:

| Doel eindpunt                                                             | Protocol | Poort    | Gebruik  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Of* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Of* <br/> [Regionale CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Of* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Voor beveiligde communicatie via een tunnel tussen de knoop punten en het besturings vlak. Dit is niet vereist voor [particuliere clusters](private-clusters.md)|
| **`*:9000`** <br/> *Of* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Of* <br/> [Regionale CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Of* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Voor beveiligde communicatie via een tunnel tussen de knoop punten en het besturings vlak. Dit is niet vereist voor [particuliere clusters](private-clusters.md) |
| **`*:123`** of **`ntp.ubuntu.com:123`** (als u Azure firewall netwerk regels gebruikt)  | UDP      | 123     | Vereist voor NTP-tijd synchronisatie (Network Time Protocol) op Linux-knoop punten.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Als u aangepaste DNS-servers gebruikt, moet u ervoor zorgen dat deze toegankelijk zijn voor de cluster knooppunten. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Vereist voor het uitvoeren van een Peule/implementaties die toegang hebben tot de API-server, wordt het API-IP-adres gebruikt. Dit is niet vereist voor [particuliere clusters](private-clusters.md)  |

### <a name="azure-global-required-fqdn--application-rules"></a>Azure Global vereist FQDN/toepassings regels 

De volgende FQDN/toepassings regels zijn vereist:

| Doel-FQDN                 | Poort            | Gebruik      |
|----------------------------------|-----------------|----------|
| **`*.hcp.<location>.azmk8s.io`** | **`HTTPS:443`** | Vereist voor de > API-server communicatie van het knoop punt <. Vervang door *\<location\>* de regio waarin uw AKS-cluster is geïmplementeerd. |
| **`mcr.microsoft.com`**          | **`HTTPS:443`** | Vereist voor toegang tot afbeeldingen in micro soft Container Registry (MCR). Dit REGI ster bevat afbeeldingen/grafieken van de eerste partij (bijvoorbeeld coreDNS, enzovoort). Deze installatie kopieën zijn vereist voor het correct maken en functioneren van het cluster, inclusief schaal-en upgrade bewerkingen.  |
| **`*.data.mcr.microsoft.com`**   | **`HTTPS:443`** | Vereist voor MCR-opslag die wordt ondersteund door het Azure Content Delivery Network (CDN). |
| **`management.azure.com`**       | **`HTTPS:443`** | Vereist voor Kubernetes-bewerkingen voor de Azure-API. |
| **`login.microsoftonline.com`**  | **`HTTPS:443`** | Vereist voor Azure Active Directory-verificatie. |
| **`packages.microsoft.com`**     | **`HTTPS:443`** | Dit adres is de micro soft packages-opslag plaats die wordt gebruikt voor *apt-get-* bewerkingen in de cache.  Voor beelden van pakketten zijn Moby, Power shell en Azure CLI. |
| **`acs-mirror.azureedge.net`**   | **`HTTPS:443`** | Dit adres is voor de opslag plaats die vereist is om vereiste binaire bestanden te downloaden en te installeren, zoals kubenet en Azure CNI. |

### <a name="azure-china-21vianet-required-network-rules"></a>Azure China 21Vianet vereiste netwerk regels

De vereiste netwerk regels en afhankelijkheden voor IP-adressen zijn:

| Doel eindpunt                                                             | Protocol | Poort    | Gebruik  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Of* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.Region:1194`** <br/> *Of* <br/> [Regionale CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Of* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Voor beveiligde communicatie via een tunnel tussen de knoop punten en het besturings vlak. |
| **`*:9000`** <br/> *Of* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Of* <br/> [Regionale CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Of* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Voor beveiligde communicatie via een tunnel tussen de knoop punten en het besturings vlak. |
| **`*:22`** <br/> *Of* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:22`** <br/> *Of* <br/> [Regionale CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:22`** <br/> *Of* <br/> **`APIServerPublicIP:22`** `(only known after cluster creation)`  | TCP           | 22      | Voor beveiligde communicatie via een tunnel tussen de knoop punten en het besturings vlak. |
| **`*:123`** of **`ntp.ubuntu.com:123`** (als u Azure firewall netwerk regels gebruikt)  | UDP      | 123     | Vereist voor NTP-tijd synchronisatie (Network Time Protocol) op Linux-knoop punten.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Als u aangepaste DNS-servers gebruikt, moet u ervoor zorgen dat deze toegankelijk zijn voor de cluster knooppunten. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Vereist voor het uitvoeren van een Peule/implementaties die toegang hebben tot de API-server, zou deze pod/implementaties het API-IP-adres gebruiken.  |

### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure China 21Vianet vereiste FQDN/toepassings regels

De volgende FQDN/toepassings regels zijn vereist:

| Doel-FQDN                               | Poort            | Gebruik      |
|------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Vereist voor de > API-server communicatie van het knoop punt <. Vervang door *\<location\>* de regio waarin uw AKS-cluster is geïmplementeerd. |
| **`*.tun.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Vereist voor de > API-server communicatie van het knoop punt <. Vervang door *\<location\>* de regio waarin uw AKS-cluster is geïmplementeerd. |
| **`mcr.microsoft.com`**                        | **`HTTPS:443`** | Vereist voor toegang tot afbeeldingen in micro soft Container Registry (MCR). Dit REGI ster bevat afbeeldingen/grafieken van de eerste partij (bijvoorbeeld coreDNS, enzovoort). Deze installatie kopieën zijn vereist voor het correct maken en functioneren van het cluster, inclusief schaal-en upgrade bewerkingen. |
| **`.data.mcr.microsoft.com`**                  | **`HTTPS:443`** | Vereist voor MCR-opslag die wordt ondersteund door de Azure Content Delivery Network (CDN). |
| **`management.chinacloudapi.cn`**              | **`HTTPS:443`** | Vereist voor Kubernetes-bewerkingen voor de Azure-API. |
| **`login.chinacloudapi.cn`**                   | **`HTTPS:443`** | Vereist voor Azure Active Directory-verificatie. |
| **`packages.microsoft.com`**                   | **`HTTPS:443`** | Dit adres is de micro soft packages-opslag plaats die wordt gebruikt voor *apt-get-* bewerkingen in de cache.  Voor beelden van pakketten zijn Moby, Power shell en Azure CLI. |
| **`*.azk8s.cn`**                               | **`HTTPS:443`** | Dit adres is voor de opslag plaats die vereist is om vereiste binaire bestanden te downloaden en te installeren, zoals kubenet en Azure CNI. |

### <a name="azure-us-government-required-network-rules"></a>Azure U.S. Government vereist netwerk regels

De vereiste netwerk regels en afhankelijkheden voor IP-adressen zijn:

| Doel eindpunt                                                             | Protocol | Poort    | Gebruik  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Of* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Of* <br/> [Regionale CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Of* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Voor beveiligde communicatie via een tunnel tussen de knoop punten en het besturings vlak. |
| **`*:9000`** <br/> *Of* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Of* <br/> [Regionale CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Of* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Voor beveiligde communicatie via een tunnel tussen de knoop punten en het besturings vlak. |
| **`*:123`** of **`ntp.ubuntu.com:123`** (als u Azure firewall netwerk regels gebruikt)  | UDP      | 123     | Vereist voor NTP-tijd synchronisatie (Network Time Protocol) op Linux-knoop punten.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Als u aangepaste DNS-servers gebruikt, moet u ervoor zorgen dat deze toegankelijk zijn voor de cluster knooppunten. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Vereist voor het uitvoeren van een Peule/implementaties die toegang hebben tot de API-server, wordt het API-IP-adres gebruikt.  |

### <a name="azure-us-government-required-fqdn--application-rules"></a>Azure Amerikaanse overheid vereist FQDN/toepassings regels 

De volgende FQDN/toepassings regels zijn vereist:

| Doel-FQDN                                        | Poort            | Gebruik      |
|---------------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.aks.containerservice.azure.us`** | **`HTTPS:443`** | Vereist voor de > API-server communicatie van het knoop punt <. Vervang door *\<location\>* de regio waarin uw AKS-cluster is geïmplementeerd.|
| **`mcr.microsoft.com`**                                 | **`HTTPS:443`** | Vereist voor toegang tot afbeeldingen in micro soft Container Registry (MCR). Dit REGI ster bevat afbeeldingen/grafieken van de eerste partij (bijvoorbeeld coreDNS, enzovoort). Deze installatie kopieën zijn vereist voor het correct maken en functioneren van het cluster, inclusief schaal-en upgrade bewerkingen. |
| **`*.data.mcr.microsoft.com`**                          | **`HTTPS:443`** | Vereist voor MCR-opslag die wordt ondersteund door het Azure Content Delivery Network (CDN). |
| **`management.usgovcloudapi.net`**                      | **`HTTPS:443`** | Vereist voor Kubernetes-bewerkingen voor de Azure-API. |
| **`login.microsoftonline.us`**                          | **`HTTPS:443`** | Vereist voor Azure Active Directory-verificatie. |
| **`packages.microsoft.com`**                            | **`HTTPS:443`** | Dit adres is de micro soft packages-opslag plaats die wordt gebruikt voor *apt-get-* bewerkingen in de cache.  Voor beelden van pakketten zijn Moby, Power shell en Azure CLI. |
| **`acs-mirror.azureedge.net`**                          | **`HTTPS:443`** | Dit adres is voor de opslag plaats die vereist is voor het installeren van vereiste binaire bestanden, zoals kubenet en Azure CNI. |

## <a name="optional-recommended-fqdn--application-rules-for-aks-clusters"></a>Optionele aanbevolen FQDN/toepassings regels voor AKS-clusters

De volgende FQDN/toepassings regels zijn optioneel, maar aanbevolen voor AKS-clusters:

| Doel-FQDN                                                               | Poort          | Gebruik      |
|--------------------------------------------------------------------------------|---------------|----------|
| **`security.ubuntu.com`, `azure.archive.ubuntu.com`, `changelogs.ubuntu.com`** | **`HTTP:80`** | Met dit adres kunnen Linux-cluster knooppunten de vereiste beveiligings patches en updates downloaden. |

Als u ervoor kiest deze FQDN-naam te blok keren/niet toe te staan, ontvangen de knoop punten alleen updates van het besturings systeem wanneer u een [knooppunt installatie kopie](node-image-upgrade.md) of [cluster upgrade](upgrade-cluster.md)uitvoert.

## <a name="gpu-enabled-aks-clusters"></a>Met GPU ingeschakelde AKS-clusters

### <a name="required-fqdn--application-rules"></a>Vereiste FQDN/toepassings regels

De volgende FQDN/toepassings regels zijn vereist voor AKS-clusters waarop GPU is ingeschakeld:

| Doel-FQDN                        | Poort      | Gebruik      |
|-----------------------------------------|-----------|----------|
| **`nvidia.github.io`**                  | **`HTTPS:443`** | Dit adres wordt gebruikt voor juiste installatie van Stuur Programma's en bewerkingen op op GPU gebaseerde knoop punten. |
| **`us.download.nvidia.com`**            | **`HTTPS:443`** | Dit adres wordt gebruikt voor juiste installatie van Stuur Programma's en bewerkingen op op GPU gebaseerde knoop punten. |
| **`apt.dockerproject.org`**             | **`HTTPS:443`** | Dit adres wordt gebruikt voor juiste installatie van Stuur Programma's en bewerkingen op op GPU gebaseerde knoop punten. |

## <a name="windows-server-based-node-pools"></a>Op Windows Server gebaseerde knooppunt groepen 

### <a name="required-fqdn--application-rules"></a>Vereiste FQDN/toepassings regels

De volgende FQDN/toepassings regels zijn vereist voor het gebruik van Windows Server-knooppunt groepen:

| Doel-FQDN                                                           | Poort      | Gebruik      |
|----------------------------------------------------------------------------|-----------|----------|
| **`onegetcdn.azureedge.net, go.microsoft.com`**                            | **`HTTPS:443`** | Windows-gerelateerde binaire bestanden installeren |
| **`*.mp.microsoft.com, www.msftconnecttest.com, ctldl.windowsupdate.com`** | **`HTTP:80`**   | Windows-gerelateerde binaire bestanden installeren |

## <a name="aks-addons-and-integrations"></a>AKS en integraties

### <a name="azure-monitor-for-containers"></a>Azure Monitor voor containers

Er zijn twee opties om toegang te bieden tot Azure Monitor voor containers. u kunt de Azure Monitor [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) toestaan **of** toegang bieden tot de vereiste FQDN/toepassings regels.

#### <a name="required-network-rules"></a>Vereiste netwerk regels

De volgende FQDN/toepassings regels zijn vereist:

| Doel eindpunt                                                             | Protocol | Poort    | Gebruik  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | Dit eind punt wordt gebruikt voor het verzenden van metrische gegevens en logboeken naar Azure Monitor en Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Vereiste FQDN/toepassings regels

De volgende FQDN/toepassings regels zijn vereist voor AKS-clusters waarvoor de Azure Monitor voor containers is ingeschakeld:

| FQDN                                    | Poort      | Gebruik      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | **`HTTPS:443`**    | Dit eind punt wordt gebruikt voor metrische gegevens en het bewaken van telemetrie met behulp van Azure Monitor. |
| *.ods.opinsights.azure.com    | **`HTTPS:443`**    | Dit eind punt wordt gebruikt door Azure Monitor voor opname van log Analytics-gegevens. |
| *.oms.opinsights.azure.com | **`HTTPS:443`** | Dit eind punt wordt gebruikt door omsagent, dat wordt gebruikt om de log Analytics-service te verifiëren. |
| *. monitoring.azure.com | **`HTTPS:443`** | Dit eind punt wordt gebruikt om metrische gegevens naar Azure Monitor te verzenden. |

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

Werk uw firewall of beveiligings configuratie bij om netwerk verkeer toe te staan van en naar de onderstaande FQDN-namen en [Azure dev Spaces-infrastructuur services][dev-spaces-service-tags].

#### <a name="required-network-rules"></a>Vereiste netwerk regels

| Doel eindpunt                                                             | Protocol | Poort    | Gebruik  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureDevSpaces`**  | TCP           | 443      | Dit eind punt wordt gebruikt voor het verzenden van metrische gegevens en logboeken naar Azure Monitor en Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Vereiste FQDN/toepassings regels 

De volgende FQDN/toepassings regels zijn vereist voor AKS-clusters waarvoor de Azure dev Spaces zijn ingeschakeld:

| FQDN                                    | Poort      | Gebruik      |
|-----------------------------------------|-----------|----------|
| `cloudflare.docker.com` | **`HTTPS:443`** | Dit adres wordt gebruikt voor het ophalen van images voor Linux alpine en andere Azure dev Spaces |
| `gcr.io` | **`HTTPS:443`** | Dit adres wordt gebruikt voor het ophalen van helm/Tiller-installatie kopieën |
| `storage.googleapis.com` | **`HTTPS:443`** | Dit adres wordt gebruikt voor het ophalen van helm/Tiller-installatie kopieën |


### <a name="azure-policy"></a>Azure Policy

#### <a name="required-fqdn--application-rules"></a>Vereiste FQDN/toepassings regels 

De volgende FQDN/toepassings regels zijn vereist voor AKS-clusters waarvoor de Azure Policy ingeschakeld.

| FQDN                                          | Poort      | Gebruik      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.core.windows.net`** | **`HTTPS:443`** | Dit adres wordt gebruikt om het Kubernetes-beleid op te halen en de status van de cluster naleving te rapporteren aan de beleids service. |
| **`store.policy.core.windows.net`** | **`HTTPS:443`** | Dit adres wordt gebruikt voor het ophalen van de gate keeper artefacten van ingebouwde beleids regels. |
| **`gov-prod-policy-data.trafficmanager.net`** | **`HTTPS:443`** | Dit adres wordt gebruikt voor een juiste werking van Azure Policy.  |
| **`raw.githubusercontent.com`**               | **`HTTPS:443`** | Dit adres wordt gebruikt om het ingebouwde beleid van GitHub te halen om te zorgen voor een juiste werking van Azure Policy. |
| **`dc.services.visualstudio.com`**            | **`HTTPS:443`** | Azure Policy-invoeg toepassing waarmee telemetriegegevens worden verzonden naar het apps Insights-eind punt. |

#### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure China 21Vianet vereiste FQDN/toepassings regels 

De volgende FQDN/toepassings regels zijn vereist voor AKS-clusters waarvoor de Azure Policy ingeschakeld.

| FQDN                                          | Poort      | Gebruik      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.cn`** | **`HTTPS:443`** | Dit adres wordt gebruikt om het Kubernetes-beleid op te halen en de status van de cluster naleving te rapporteren aan de beleids service. |
| **`store.policy.azure.cn`** | **`HTTPS:443`** | Dit adres wordt gebruikt voor het ophalen van de gate keeper artefacten van ingebouwde beleids regels. |

#### <a name="azure-us-government-required-fqdn--application-rules"></a>Azure Amerikaanse overheid vereist FQDN/toepassings regels

De volgende FQDN/toepassings regels zijn vereist voor AKS-clusters waarvoor de Azure Policy ingeschakeld.

| FQDN                                          | Poort      | Gebruik      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.us`** | **`HTTPS:443`** | Dit adres wordt gebruikt om het Kubernetes-beleid op te halen en de status van de cluster naleving te rapporteren aan de beleids service. |
| **`store.policy.azure.us`** | **`HTTPS:443`** | Dit adres wordt gebruikt voor het ophalen van de gate keeper artefacten van ingebouwde beleids regels. |

## <a name="restrict-egress-traffic-using-azure-firewall"></a>Uitgaand verkeer beperken met behulp van Azure firewall

Azure Firewall biedt een FQDN-label van Azure Kubernetes service ( `AzureKubernetesService` ) om deze configuratie te vereenvoudigen. 

> [!NOTE]
> De FQDN-code bevat alle FQDN-codes die hierboven worden vermeld en die automatisch up-to-date blijven.
>
> We raden u aan mini maal 20 frontend-Ip's te hebben op de Azure Firewall voor productie scenario's om te voor komen dat er geen SNAT-poort ontstaats problemen ontstaan.

Hieronder ziet u een voor beeld van de architectuur van de implementatie:

![Vergrendelde topologie](media/limit-egress-traffic/aks-azure-firewall-egress.png)

* Open bare inkomend verkeer wordt geforceerd door Firewall filters
  * AKS-agent knooppunten worden geïsoleerd in een toegewezen subnet.
  * [Azure firewall](../firewall/overview.md) wordt geïmplementeerd in een eigen subnet.
  * Met een DNAT-regel wordt het open bare IP-adres van de FW omgezet in het IP-adres van de LB-frontend.
* Uitgaande aanvragen worden gestart vanaf agent knooppunten naar het Azure Firewall interne IP-adres met behulp van een door de [gebruiker gedefinieerde route](egress-outboundtype.md)
  * Aanvragen van AKS-agent knooppunten volgen een UDR die is geplaatst op het subnet waarin het AKS-cluster is geïmplementeerd.
  * Azure Firewall egresses buiten het virtuele netwerk vanaf een openbaar IP-front-end
  * Toegang tot het open bare Internet of andere Azure-Services stromen van en naar het IP-adres van de firewall-frontend
  * De toegang tot het AKS-besturings vlak wordt optioneel beschermd door de door de [API server geautoriseerde IP-adresbereiken](./api-server-authorized-ip-ranges.md), waaronder het open bare IP-adres van de firewall.
* Intern verkeer
  * U kunt in plaats daarvan ook of naast een [open bare Load Balancer](load-balancer-standard.md) een [intern Load Balancer](internal-lb.md) gebruiken voor intern verkeer, dat u ook op eigen subnet zou kunnen isoleren.


De onderstaande stappen maken gebruik van de FQDN-code van Azure Firewall `AzureKubernetesService` om het uitgaande verkeer van het AKS-cluster te beperken en bieden een voor beeld van het configureren van openbaar binnenkomend verkeer via de firewall.


### <a name="set-configuration-via-environment-variables"></a>Configuratie via omgevings variabelen instellen

Definieer een set omgevings variabelen die moeten worden gebruikt voor het maken van resources.

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

Richt een virtueel netwerk in met twee afzonderlijke subnetten, één voor het cluster, één voor de firewall. U kunt eventueel ook een voor interne service-ingang maken.

![Lege netwerk topologie](media/limit-egress-traffic/empty-network.png)

Maak een resource groep om alle resources te bewaren.

```azurecli
# Create Resource Group

az group create --name $RG --location $LOC
```

Maak een virtueel netwerk met twee subnetten om het AKS-cluster en het Azure Firewall te hosten. Elk heeft een eigen subnet. Laten we beginnen met het AKS-netwerk.

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

### <a name="create-and-set-up-an-azure-firewall-with-a-udr"></a>Een Azure Firewall met een UDR maken en instellen

Azure Firewall binnenkomende en uitgaande regels moeten worden geconfigureerd. Het belangrijkste doel van de firewall is om organisaties in staat te stellen gedetailleerde inkomende en uitgaande verkeers regels in en uit het AKS-cluster te configureren.

![Firewall-en UDR](media/limit-egress-traffic/firewall-udr.png)


> [!IMPORTANT]
> Als uw cluster of toepassing een groot aantal uitgaande verbindingen maakt dat is omgeleid naar dezelfde of een kleine subset van bestemmingen, hebt u mogelijk meer frontend-Ip's van de firewall nodig om te voor komen dat de poorten per frontend-IP-adres worden maxing.
> Zie hier voor meer informatie over het maken van een Azure-Firewall met meerdere IP-adressen. [ ](../firewall/quick-create-multiple-ip-template.md)

Maak een open bare IP-resource van een standaard-SKU die wordt gebruikt als het front-Azure Firewall front-end-adres.

```azurecli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Registreer de preview cli-extensie om een Azure Firewall te maken.
```azurecli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC --enable-dns-proxy true
```
Het eerder gemaakte IP-adres kan nu worden toegewezen aan de firewall-frontend.

> [!NOTE]
> Het kan enkele minuten duren voordat het open bare IP-adres is ingesteld op de Azure Firewall.
> Als u gebruik wilt maken van FQDN op netwerk regels waarvoor een DNS-proxy is ingeschakeld, wordt de firewall ingeschakeld op poort 53 en worden DNS-aanvragen doorgestuurd naar de hierboven opgegeven DNS-server. Hierdoor kan de firewall die FQDN automatisch omzetten.

```azurecli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Wanneer de vorige opdracht is geslaagd, slaat u het IP-adres van de firewall voor de configuratie later op.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

> [!NOTE]
> Als u gebruikmaakt van beveiligde toegang tot de AKS API-server met [geautoriseerde IP-](./api-server-authorized-ip-ranges.md)adresbereiken, moet u het open bare IP-adres van de firewall toevoegen aan het toegestane IP-bereik.

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Een UDR maken met een hop naar Azure Firewall

Azure routeert automatisch verkeer tussen Azure-subnetten, virtuele netwerken en on-premises netwerken. Als u de standaard routering van Azure wilt wijzigen, doet u dat door een route tabel te maken.

Maak een lege route tabel die moet worden gekoppeld aan een bepaald subnet. In de route tabel wordt de volgende hop gedefinieerd als de hierboven gemaakte Azure Firewall. Elk subnet kan zijn gekoppeld aan geen enkele of één routetabel.

```azurecli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG -l $LOC --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Raadpleeg de documentatie over de [route tabel van het virtuele netwerk](../virtual-network/virtual-networks-udr-overview.md#user-defined) over hoe u de standaard systeem routes van Azure kunt overschrijven of extra routes toevoegt aan de route tabel van een subnet.

### <a name="adding-firewall-rules"></a>Firewall regels toevoegen

Hieronder vindt u drie netwerk regels die u kunt gebruiken om op uw firewall te configureren. u moet deze regels mogelijk aanpassen op basis van uw implementatie. Met de eerste regel is toegang tot poort 9000 via TCP toegestaan. Met de tweede regel krijgt u toegang tot poort 1194 en 123 via UDP (als u implementeert in azure China 21Vianet, hebt u mogelijk [meer](#azure-china-21vianet-required-network-rules)nodig). Beide regels staan alleen toe dat verkeer dat bestemd is voor de Azure-regio CIDR die we gebruiken, in dit geval VS-Oost. Tot slot voegt u een derde netwerk regel voor het openen van poort 123 met `ntp.ubuntu.com` FQDN via UDP toe (het toevoegen van een FQDN als netwerk regel is een van de specifieke functies van Azure firewall, en u moet deze aanpassen wanneer u uw eigen opties gebruikt).

Nadat u de netwerk regels hebt ingesteld, wordt er ook een toepassings regel toegevoegd met behulp van de `AzureKubernetesService` betreffende FQDN-waarden die toegankelijk zijn via TCP-poort 443 en poort 80.

```
# Add FW Network Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apiudp' --protocols 'UDP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 1194 --action allow --priority 100
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apitcp' --protocols 'TCP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 9000
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'time' --protocols 'UDP' --source-addresses '*' --destination-fqdns 'ntp.ubuntu.com' --destination-ports 123

# Add FW Application Rules

az network firewall application-rule create -g $RG -f $FWNAME --collection-name 'aksfwar' -n 'fqdn' --source-addresses '*' --protocols 'http=80' 'https=443' --fqdn-tags "AzureKubernetesService" --action allow --priority 100
```

Raadpleeg de [documentatie van Azure firewall](../firewall/overview.md) voor meer informatie over de Azure Firewall-service.

### <a name="associate-the-route-table-to-aks"></a>De route tabel aan AKS koppelen

Als u het cluster wilt koppelen aan de firewall, moet het toegewezen subnet voor het subnet van het cluster verwijzen naar de route tabel die hierboven is gemaakt. U kunt de koppeling doen door een opdracht uit te geven aan het virtuele netwerk dat zowel het cluster als de firewall heeft om de route tabel van het subnet van het cluster bij te werken.

```azurecli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

### <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>AKS met uitgaand type UDR implementeren in het bestaande netwerk

Nu kan een AKS-cluster worden geïmplementeerd in het bestaande virtuele netwerk. We gebruiken ook het [uitgaande type `userDefinedRouting` ](egress-outboundtype.md). deze functie zorgt ervoor dat uitgaand verkeer wordt afgedwongen via de firewall en er geen andere uitgangs paden bestaan (standaard kan het Load Balancer uitgaand type worden gebruikt).

![AKS-implementeren](media/limit-egress-traffic/aks-udr-fw.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Een service-principal met toegang maken om in het bestaande virtuele netwerk in te richten

Een cluster identiteit (beheerde identiteit of Service-Principal) wordt door AKS gebruikt om cluster bronnen te maken. Een service-principal die tijdens het maken wordt door gegeven, wordt gebruikt voor het maken van onderliggende AKS-resources, zoals opslag resources, Ip's en load balancers die door AKS worden gebruikt (u kunt in plaats daarvan ook een [beheerde identiteit](use-managed-identity.md) gebruiken). Als u hieronder niet de juiste machtigingen hebt verleend, kunt u het AKS-cluster niet inrichten.

```azurecli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Vervang nu de `APPID` en `PASSWORD` onderstaande door de Service Principal AppID en het Service Principal-wacht woord dat automatisch wordt gegenereerd door de vorige opdracht uitvoer. We verwijzen naar de VNET-Resource-ID om machtigingen toe te kennen aan de Service-Principal zodat AKS hierin resources kan implementeren.

```azurecli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role "Network Contributor"
```

U kunt de gedetailleerde machtigingen controleren die [hier](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)nodig zijn.

> [!NOTE]
> Als u de kubenet-netwerk-invoeg toepassing gebruikt, moet u de AKS Service Principal of beheerde identiteits machtigingen verlenen aan de vooraf gemaakte route tabel, omdat kubenet een route tabel vereist om nodig-routerings regels toe te voegen. 
> ```azurecli-interactive
> RTID=$(az network route-table show -g $RG -n $FWROUTE_TABLE_NAME --query id -o tsv)
> az role assignment create --assignee $APPID --scope $RTID --role "Network Contributor"
> ```

### <a name="deploy-aks"></a>AKS implementeren

Ten slotte kan het AKS-cluster worden geïmplementeerd in het bestaande subnet dat voor het cluster is toegewezen. Het doel-subnet waarnaar moet worden geïmplementeerd, is gedefinieerd met de omgevings variabele `$SUBNETID` . De variabele is niet gedefinieerd `$SUBNETID` in de vorige stappen. Als u de waarde voor de subnet-ID wilt instellen, kunt u de volgende opdracht gebruiken:

```azurecli
SUBNETID=$(az network vnet subnet show -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --query id -o tsv)
```

U definieert het uitgaande type voor het gebruik van de UDR die al in het subnet bestaat. Deze configuratie zorgt ervoor dat AKS de installatie en IP-inrichting voor de load balancer overs laat.

> [!IMPORTANT]
> Zie voor meer informatie over uitgaand type UDR, inclusief beperkingen, het [**uitgaand uitgaande type UDR**](egress-outboundtype.md#limitations).


> [!TIP]
> Extra functies kunnen worden toegevoegd aan de cluster implementatie, zoals een [**persoonlijk cluster**](private-clusters.md). 
>
> De AKS-functie voor door de [**API server geautoriseerde IP-adresbereiken**](api-server-authorized-ip-ranges.md) kan worden toegevoegd om de API-server toegang alleen te beperken tot het open bare eind punt van de firewall. De functie voor geautoriseerde IP-bereiken wordt in het diagram als optioneel aangeduid. Wanneer u de functie geautoriseerd IP-bereik inschakelt om de toegang tot de API-server te beperken, moeten uw ontwikkel hulpprogramma's gebruikmaken van een JumpBox in het virtuele netwerk van de firewall of moet u alle ontwikkel eindpunten toevoegen aan het geautoriseerde IP-bereik.

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

### <a name="enable-developer-access-to-the-api-server"></a>Ontwikkel aars toegang tot de API-server inschakelen

Als u in de vorige stap geautoriseerde IP-adresbereiken voor het cluster hebt gebruikt, moet u de IP-adressen van ontwikkel aars toevoegen aan de AKS-cluster lijst met goedgekeurde IP-bereiken om vanaf daar toegang te krijgen tot de API-server. Een andere optie is het configureren van een JumpBox met het benodigde hulp programma binnen een afzonderlijk subnet in het virtuele netwerk van de firewall.

Voeg een ander IP-adres toe aan de goedgekeurde bereiken met de volgende opdracht

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Gebruik de opdracht [AZ AKS Get-credentials] [AZ-AKS-Get-credentials] om te configureren `kubectl` om verbinding te maken met het zojuist gemaakte Kubernetes-cluster. 

 ```azurecli
 az aks get-credentials -g $RG -n $AKSNAME
 ```

### <a name="deploy-a-public-service"></a>Een open bare service implementeren
U kunt nu Services gaan weer geven en toepassingen implementeren op dit cluster. In dit voor beeld wordt een open bare service beschikbaar gemaakt, maar u kunt er ook voor kiezen om een interne service beschikbaar te stellen via [interne Load Balancer](internal-lb.md).

![Open bare service DNAT](media/limit-egress-traffic/aks-create-svc.png)

Implementeer de Azure stem-app-toepassing door de yaml hieronder te kopiëren naar een bestand met de naam `example.yaml` .

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

Implementeer de service door uit te voeren:

```bash
kubectl apply -f example.yaml
```

### <a name="add-a-dnat-rule-to-azure-firewall"></a>Een DNAT-regel toevoegen aan Azure Firewall

> [!IMPORTANT]
> Wanneer u Azure Firewall gebruikt om uitgaand verkeer te beperken en een door de gebruiker gedefinieerde route (UDR) te maken om al het uitgaande verkeer af te dwingen, moet u een geschikte DNAT-regel in de firewall maken om binnenkomend verkeer op de juiste wijze toe te staan. Als Azure Firewall wordt gebruikt met een UDR, wordt de ingangs instellingen voor asymmetrische route ring onderbroken. (Het probleem treedt op als het AKS-subnet een standaard route heeft die naar het privé-IP-adres van de firewall gaat, maar u een open bare load balancer-binnenkomend of Kubernetes service van het type: LoadBalancer) gebruikt. In dit geval wordt het binnenkomende load balancer verkeer ontvangen via het open bare IP-adres, maar het retour pad gaat via het privé-IP-adres van de firewall. Omdat de firewall stateful is, wordt het terugkerende pakket neergezet omdat de firewall niet op de hoogte is van een vastgestelde sessie. Zie [Azure firewall integreren met Azure Standard Load Balancer](../firewall/integrate-lb.md)voor meer informatie over het integreren van Azure Firewall met uw ingangs-of service Load Balancer.


Als u binnenkomende connectiviteit wilt configureren, moet er een DNAT-regel naar de Azure Firewall geschreven. Als u de verbinding met uw cluster wilt testen, wordt er een regel gedefinieerd voor het open bare IP-adres van de firewall-frontend om te routeren naar de interne IP die wordt weer gegeven door de interne service.

Het doel adres kan worden aangepast wanneer het de poort op de firewall is die moet worden geopend. Het vertaalde adres moet het IP-adres van de interne load balancer zijn. De vertaalde poort moet de weer gegeven poort voor uw Kubernetes-service zijn.

U moet het interne IP-adres opgeven dat is toegewezen aan de load balancer gemaakt door de Kubernetes-service. Het adres ophalen door het volgende uit te voeren:

```bash
kubectl get services
```

Het IP-adres dat nodig is, wordt weer gegeven in de kolom extern-IP, vergelijkbaar met het volgende.

```bash
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP      10.41.0.1       <none>        443/TCP        10h
voting-analytics   ClusterIP      10.41.88.129    <none>        8080/TCP       9m
voting-app         LoadBalancer   10.41.185.82    20.39.18.6    80:32718/TCP   9m
voting-storage     ClusterIP      10.41.221.201   <none>        3306/TCP       9m
```

De service-IP ophalen door uit te voeren:
```bash
SERVICE_IP=$(kubectl get svc voting-app -o jsonpath='{.status.loadBalancer.ingress[*].ip}')
```

Voeg de NAT-regel toe door uit te voeren:
```azurecli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address $SERVICE_IP
```

### <a name="validate-connectivity"></a>Connectiviteit valideren

Ga in een browser naar het Azure Firewall frontend-IP-adres om de connectiviteit te valideren.

U ziet de AKS stem-app. In dit voor beeld was de firewall het open bare IP-adres `52.253.228.132` .


![Scherm afbeelding toont de A K S-stem-app met knoppen voor katten, honden en resetten en totalen.](media/limit-egress-traffic/aks-vote.png)


### <a name="clean-up-resources"></a>Resources opschonen

Als u Azure-resources wilt opschonen, verwijdert u de resource groep AKS.

```azurecli
az group delete -g $RG
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd welke poorten en adressen zijn toegestaan als u uitgaand verkeer voor het cluster wilt beperken. U hebt ook gezien hoe u uw uitgaande verkeer kunt beveiligen met behulp van Azure Firewall. 

Als dat nodig is, kunt u de bovenstaande stappen voor het door sturen van het verkeer naar uw voorkeurs uitgangs oplossing generaliseren door de [uitgaande type `userDefinedRoute` documentatie](egress-outboundtype.md)te volgen.

Als u wilt beperken hoe peulen tussen zichzelf en East-West verkeers beperkingen binnen het cluster worden gecommuniceerd, raadpleegt u het [beveiligde verkeer tussen peulen met netwerk beleid in AKS][network-policy].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[dev-spaces-service-tags]: ../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations
