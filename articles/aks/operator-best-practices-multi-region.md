---
title: Aanbevolen procedures voor AKS-bedrijfs continuïteit en herstel na nood gevallen
description: Meer informatie over de aanbevolen procedures van een cluster operator voor maximale uptime van uw toepassingen, waardoor u een hoge Beschik baarheid krijgt en herstel na nood geval in azure Kubernetes service (AKS) kunt voorbereiden.
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 5bcd30c22132bc53ff28fdefcb73f686e08e34ea
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105082"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor bedrijfs continuïteit en herstel na nood gevallen in azure Kubernetes service (AKS)

Wanneer u clusters beheert in azure Kubernetes service (AKS), wordt de uptime van toepassingen belang rijker. AKS biedt standaard hoge Beschik baarheid met behulp van meerdere knoop punten in een [virtuele-machine Scale set (VMSS)](../virtual-machine-scale-sets/overview.md). Maar deze meerdere knoop punten beveiligen uw systeem niet vanuit een regionale storing. Plan vooruit om bedrijfs continuïteit te behouden en voor bereidingen voor nood herstel om uw uptime te maximaliseren.

Dit artikel is gericht op het plannen van bedrijfs continuïteit en herstel na nood gevallen in AKS. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Plan voor AKS-clusters in meerdere regio's.
> * Verkeer routeren via meerdere clusters met behulp van Azure Traffic Manager.
> * Gebruik geo-replicatie voor de register installatie kopieën van de container.
> * Plan de toepassings status op meerdere clusters.
> * Opslag repliceren over meerdere regio's.

## <a name="plan-for-multiregion-deployment"></a>Plan voor implementatie met meer regio's

> **Best practice**
>
> Wanneer u meerdere AKS-clusters implementeert, kiest u regio's waar AKS beschikbaar is. Gebruik gekoppelde regio's.

Er wordt een AKS-cluster geïmplementeerd in één regio. Als u uw systeem wilt beveiligen tegen storingen in regio's, implementeert u uw toepassing in meerdere AKS-clusters in verschillende regio's. Overweeg het volgende bij het plannen van de implementatie van uw AKS-cluster:

* [**Beschik baarheid van AKS-regio**](./quotas-skus-regions.md#region-availability)
    * Kies regio's sluiten voor uw gebruikers. 
    * AKS wordt doorlopend uitgebreid naar nieuwe regio's.
* [**Gekoppelde Azure-regio's**](../best-practices-availability-paired-regions.md)
    * Kies voor uw geografische gebied twee regio's die aan elkaar zijn gekoppeld.
    * Gekoppelde regio's coördineren platform updates en bepalen waar nodig herstel taken.
* **Service beschikbaarheid**
    * Bepaal of de gekoppelde regio's warm/hot, Hot/warme of warme/koud moeten zijn.
    * Wilt u beide regio's tegelijk uitvoeren, met een regio die *gereed* is voor het leveren van verkeer? Of
    * Wilt u een regio tijd geven om het verkeer te kunnen verwerken?

De beschik baarheid en gekoppelde regio's van de AKS-regio zijn een gezamenlijke overweging. Implementeer uw AKS-clusters in gekoppelde regio's, ontworpen voor het samen stellen van de regionale nood herstel. AKS is bijvoorbeeld beschikbaar in VS-Oost en VS-West. Deze regio's zijn gekoppeld. Kies deze twee regio's wanneer u een AKS BC/DR-strategie maakt.

Wanneer u uw toepassing implementeert, voegt u nog een stap toe aan uw CI/CD-pijp lijn om te implementeren op deze meerdere AKS-clusters. Als u uw implementatie pijplijnen bijwerkt, voor komt u dat toepassingen in slechts één van uw regio's en AKS-clusters worden geïmplementeerd. In dat scenario ontvangen klanten verkeer dat naar een secundaire regio wordt gestuurd, niet de nieuwste code-updates.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Azure Traffic Manager gebruiken om verkeer te routeren

> **Best practice**
>
> U kunt met Azure Traffic Manager u naar uw dichtstbijzijnde AKS-cluster en-toepassings exemplaar sturen. Voor de beste prestaties en redundantie moet u alle toepassings verkeer via Traffic Manager door sturen voordat het naar uw AKS-cluster gaat.

Als u meerdere AKS-clusters in verschillende regio's hebt, gebruikt u Traffic Manager om de verkeers stroom te beheren voor de toepassingen die in elk cluster worden uitgevoerd. [Azure Traffic Manager](../traffic-manager/index.yml) is een op DNS gebaseerd verkeer Load Balancer dat netwerk verkeer kan distribueren tussen regio's. Gebruik Traffic Manager om gebruikers te routeren op basis van de reactie tijd van een cluster of op basis van geografie.

![AKS met Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Als u één AKS-cluster hebt, kunt u doorgaans verbinding maken met de service-IP of DNS-naam van een bepaalde toepassing. In een implementatie met meerdere clusters moet u verbinding maken met een Traffic Manager DNS-naam die verwijst naar de services op elk AKS-cluster. Definieer deze services met behulp van Traffic Manager-eind punten. Elk eind punt is de *service load BALANCER IP*. Gebruik deze configuratie om netwerk verkeer van het Traffic Manager-eind punt in de ene regio naar het eind punt in een andere regio te sturen.

![Geografische route ring via Traffic Manager](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager voert DNS-zoek acties uit en retourneert het meest geschikte eind punt. Geneste profielen kunnen een prioriteit geven aan een primaire locatie. U moet bijvoorbeeld verbinding maken met de dichtstbijzijnde geografische regio. Als deze regio een probleem heeft, stuurt Traffic Manager u naar een secundaire regio. Deze aanpak zorgt ervoor dat u verbinding kunt maken met een toepassings exemplaar, zelfs als uw dichtstbijzijnde geografische regio niet beschikbaar is.

Voor informatie over het instellen van eind punten en route ring, Zie [de methode voor geografische verkeers routering configureren met behulp van Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md).

### <a name="application-routing-with-azure-front-door-service"></a>Toepassings routering met Azure front-deur service

Met behulp van een gesplitste op TCP gebaseerd anycast-protocol verbindt de [Azure front-deur service](../frontdoor/front-door-overview.md) de eind gebruikers onmiddellijk met de dichtstbijzijnde pop voor de voor deur (punt van aanwezigheid). Meer functies van de Azure front-deur-service:
* TLS-beëindiging
* Aangepast domein
* Web Application Firewall
* URL opnieuw genereren
* Sessieaffiniteit 

Bekijk de vereisten van uw toepassings verkeer om te begrijpen welke oplossing het meest geschikt is.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>Interconnect-regio's met globale virtuele-netwerk peering

Verbind beide virtuele netwerken met elkaar via [Virtual Network-peering](../virtual-network/virtual-network-peering-overview.md) om communicatie tussen clusters mogelijk te maken. Peering van virtuele netwerken interconnecteert Virtual Networks en biedt hoge band breedte in het backbone-netwerk van micro soft, zelfs in verschillende geografische regio's.

Gebruik voor de peering van virtuele netwerken met AKS-clusters de standaard Load Balancer in uw AKS-cluster. Dit vereiste zorgt ervoor dat Kubernetes services bereikbaar zijn via de peering van het virtuele netwerk.

## <a name="enable-geo-replication-for-container-images"></a>Geo-replicatie inschakelen voor container installatie kopieën

> **Best practice**
> 
> Sla de container installatie kopieën op in Azure Container Registry en geo-repliceer het REGI ster naar elke AKS-regio.

Als u uw toepassingen wilt implementeren en uitvoeren in AKS, hebt u een manier nodig om de container installatie kopieën op te slaan en te halen. Container Registry kan worden geïntegreerd met AKS, zodat de container installatie kopieën of helm-grafieken veilig kunnen worden opgeslagen. Container Registry ondersteunt geo-replicatie met meerdere masters om uw installatie kopieën automatisch te repliceren naar Azure-regio's over de hele wereld. 

Prestaties en beschik baarheid verbeteren:
1. Gebruik Container Registry geo-replicatie voor het maken van een REGI ster in elke regio waar u een AKS-cluster hebt. 
1. Elk AKS-cluster haalt vervolgens container installatie kopieën uit het lokale container register in dezelfde regio:

![Geo-replicatie Container Registry voor container installatie kopieën](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Wanneer u Container Registry geo-replicatie gebruikt voor het ophalen van installatie kopieën uit dezelfde regio, zijn de volgende resultaten:

* **Sneller**: pull-installatie kopieën van netwerk verbindingen met hoge snelheid en lage latentie binnen dezelfde Azure-regio.
* **Betrouwbaarder**: als een regio niet beschikbaar is, haalt uw AKS-cluster de installatie kopieën op uit een beschikbaar container register.
* **Goed koper**: er worden geen kosten in rekening gebracht voor het netwerk tussen data centers.

Geo-replicatie is een *Premium* SKU-container register functie. Zie [container Registry geo-replicatie](../container-registry/container-registry-geo-replication.md)voor meer informatie over het configureren van geo-replicatie.

## <a name="remove-service-state-from-inside-containers"></a>Service status van binnen containers verwijderen

> **Best practice**
> 
> Sla de service status niet op in de container. Gebruik in plaats daarvan een Azure-platform als een service (PaaS) die ondersteuning biedt voor replicatie in meerdere regio's.

De *service status* verwijst naar de gegevens in het geheugen of op schijf die vereist zijn voor het functioneren van een service. De status bevat de gegevens structuren en lidvariabelen die de service leest en schrijft. Afhankelijk van hoe de service is ontworpen, kan de status ook bestanden of andere bronnen die op de schijf zijn opgeslagen, bevatten. De status kan bijvoorbeeld de bestanden bevatten die een data base gebruikt om gegevens en transactie logboeken op te slaan.

De status kan extern of gezamenlijk worden gevonden met de code die de status bewerkt. Normaal gesp roken Externalize u de status met behulp van een Data Base of een ander gegevens archief dat wordt uitgevoerd op verschillende computers via het netwerk of die op dezelfde computer wordt uitgevoerd.

Containers en micro Services zijn het meest flexibel wanneer de processen die in hun worden uitgevoerd, niet de status behouden hebben. Omdat toepassingen bijna altijd een bepaalde status bevatten, kunt u een PaaS-oplossing gebruiken, zoals:
* Azure Cosmos DB
* Azure Database for PostgreSQL
* Azure Database for MySQL
* Azure SQL Database

Als u draag bare toepassingen wilt bouwen, raadpleegt u de volgende richt lijnen:

* [De methodologie van de 12-factor-app](https://12factor.net/)
* [Een webtoepassing uitvoeren in meerdere Azure-regio's](/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Een opslag migratie plan maken

> **Best practice**
>
> Als u Azure Storage gebruikt, bereidt en test u hoe u uw opslag van de primaire regio naar de back-upregio kunt migreren.

Uw toepassingen kunnen Azure Storage gebruiken voor hun gegevens. Als dat het geval is, worden uw toepassingen verdeeld over meerdere AKS-clusters in verschillende regio's. U moet de opslag gesynchroniseerd laten worden. Hier volgen twee veelvoorkomende manieren om opslag te repliceren:

* Asynchrone replicatie op basis van een infra structuur
* Asynchrone replicatie op basis van toepassingen

### <a name="infrastructure-based-asynchronous-replication"></a>Asynchrone replicatie op basis van een infra structuur

Voor uw toepassingen is mogelijk permanente opslag vereist, zelfs nadat een Pod is verwijderd. In Kubernetes kunt u permanente volumes gebruiken om gegevens opslag te behouden. Permanente volumes worden gekoppeld aan een VM-knoop punt en vervolgens weer blootgesteld aan het gehele. Permanente volumes volgen een Peul, zelfs als het Peul wordt verplaatst naar een ander knoop punt binnen hetzelfde cluster.

De replicatie strategie die u gebruikt, is afhankelijk van uw opslag oplossing. De volgende algemene opslag oplossingen bieden hun eigen richt lijnen voor herstel na nood gevallen en replicatie:
* [Gluster](https://docs.gluster.org/en/latest/Administrator-Guide/Geo-Replication/)
* [Ceph](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/)
* [Rook](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)
* [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) 

Normaal gesp roken geeft u een gemeen schappelijk opslag punt op waarin de gegevens van toepassingen worden geschreven. Deze gegevens worden vervolgens gerepliceerd tussen regio's en lokaal geopend.

![Asynchrone replicatie op basis van een infra structuur](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Als u Azure Managed Disks gebruikt, kunt u [Velero op Azure][velero] en [kasten][kasten] gebruiken voor het afhandelen van replicatie en herstel na nood gevallen. Deze opties zijn back-ups van oplossingen die standaard zijn, maar niet worden ondersteund door Kubernetes.

### <a name="application-based-asynchronous-replication"></a>Asynchrone replicatie op basis van toepassingen

Kubernetes biedt momenteel geen systeem eigen implementatie voor asynchrone replicatie op basis van toepassingen. Omdat containers en Kubernetes soepel zijn gekoppeld, zou elke traditionele toepassing of taal benadering moeten werken. Normaal gesp roken repliceren de toepassingen de opslag aanvragen, die vervolgens naar de onderliggende gegevens opslag van elk cluster worden geschreven.

![Asynchrone replicatie op basis van toepassingen](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Volgende stappen

Dit artikel richt zich op bedrijfs continuïteit en herstel na nood gevallen voor AKS-clusters. Meer informatie over cluster bewerkingen in AKS vindt u in de volgende artikelen over aanbevolen procedures:

* [Multitenancy en cluster isolatie][aks-best-practices-cluster-isolation]
* [Functies van de Basic Kubernetes scheduler][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md

[velero]: https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md
[kasten]: https://www.kasten.io/