---
title: Clusterconfiguratie in Azure Kubernetes Services (AKS)
description: Meer informatie over het configureren van een cluster in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/09/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 5740c1c299e8a6a2e8874bd13aae76b0353cc6a2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775866"
---
# <a name="configure-an-aks-cluster"></a>Een AKS-cluster configureren

Als onderdeel van het maken van een AKS-cluster moet u mogelijk uw clusterconfiguratie aanpassen aan uw behoeften. In dit artikel worden enkele opties voor het aanpassen van uw AKS-cluster beschreven.

## <a name="os-configuration"></a>Besturingssysteemconfiguratie

AKS ondersteunt nu Ubuntu 18.04 als het standaardbesturingssysteem voor knooppunt (OS) in algemene beschikbaarheid voor clusters in Kubernetes-versies die hoger zijn dan 1.18 Voor versies lager dan 1.18 is AKS Ubuntu 16.04 nog steeds de standaardbasisinstallatier. Vanaf kubernetes v1.18 en hoger is de standaardbasis AKS Ubuntu 18.04.

> [!IMPORTANT]
> Knooppuntgroepen die zijn gemaakt op Kubernetes v1.18 of hoger, zijn standaard ingesteld op `AKS Ubuntu 18.04` de knooppuntafbeelding. Knooppuntgroepen op een ondersteunde Kubernetes-versie van minder dan 1.18 ontvangen als de knooppuntafbeelding, maar worden bijgewerkt naar zodra de Kubernetes-versie van de knooppuntgroep is bijgewerkt naar `AKS Ubuntu 16.04` `AKS Ubuntu 18.04` v1.18 of hoger.
> 
> Het wordt ten zeerste aanbevolen om uw workloads te testen op AKS Ubuntu 18.04-knooppuntgroepen voordat u clusters met 1,18 of hoger gebruikt.


### <a name="use-aks-ubuntu-1804-ga-on-new-clusters"></a>AKS Ubuntu 18.04 (GA) gebruiken in nieuwe clusters

Clusters die zijn gemaakt op Kubernetes v1.18 of hoger, zijn standaard ingesteld `AKS Ubuntu 18.04` op een knooppuntafbeelding. Knooppuntgroepen op een ondersteunde Kubernetes-versie lager dan 1.18 ontvangen nog steeds als de knooppuntafbeelding, maar worden bijgewerkt naar zodra de Kubernetes-versie van het cluster of de knooppuntgroep is bijgewerkt naar `AKS Ubuntu 16.04` `AKS Ubuntu 18.04` v1.18 of hoger.

Het wordt ten zeerste aanbevolen om uw workloads te testen op AKS Ubuntu 18.04-knooppuntgroepen voordat u clusters met 1,18 of hoger gebruikt.

Als u een cluster wilt maken met behulp van een knooppuntafbeelding, maakt u gewoon een cluster met `AKS Ubuntu 18.04` kubernetes v1.18 of hoger, zoals hieronder wordt weergegeven

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="use-aks-ubuntu-1804-ga-on-existing-clusters"></a>AKS Ubuntu 18.04 (GA) gebruiken op bestaande clusters

Clusters die zijn gemaakt op Kubernetes v1.18 of hoger, zijn standaard ingesteld `AKS Ubuntu 18.04` op een knooppuntafbeelding. Knooppuntgroepen op een ondersteunde Kubernetes-versie lager dan 1.18 ontvangen nog steeds als de knooppuntafbeelding, maar worden bijgewerkt naar zodra de Kubernetes-versie van het cluster of de knooppuntgroep is bijgewerkt naar `AKS Ubuntu 16.04` `AKS Ubuntu 18.04` v1.18 of hoger.

Het wordt ten zeerste aanbevolen om uw workloads op AKS Ubuntu 18.04-knooppuntgroepen te testen voordat u clusters met 1,18 of hoger gebruikt.

Als uw clusters of knooppuntgroepen gereed zijn voor een knooppuntafbeelding, kunt u ze gewoon upgraden naar `AKS Ubuntu 18.04` v1.18 of hoger, zoals hieronder wordt beschreven.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

Als u slechts één knooppuntgroep wilt upgraden:

```azurecli
az aks nodepool upgrade -name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="test-aks-ubuntu-1804-ga-on-existing-clusters"></a>AKS Ubuntu 18.04 (GA) testen op bestaande clusters

Knooppuntgroepen die zijn gemaakt op Kubernetes v1.18 of hoger, zijn standaard ingesteld `AKS Ubuntu 18.04` op de knooppuntafbeelding. Knooppuntgroepen op een ondersteunde Kubernetes-versie lager dan 1.18 ontvangen nog steeds als de knooppuntafbeelding, maar worden bijgewerkt naar zodra de Kubernetes-versie van de knooppuntgroep is bijgewerkt naar `AKS Ubuntu 16.04` `AKS Ubuntu 18.04` v1.18 of hoger.

Het wordt ten zeerste aanbevolen om uw workloads op AKS Ubuntu 18.04-knooppuntgroepen te testen voordat u uw productie-knooppuntgroepen bijwerkt.

Als u een knooppuntgroep wilt maken met behulp van een knooppuntafbeelding, maakt u gewoon een knooppuntgroep met `AKS Ubuntu 18.04` kubernetes v1.18 of hoger. Uw clusterbesturingsvlak moet ten minste v1.18 of hoger hebben, maar uw andere knooppuntgroepen kunnen een oudere kubernetes-versie hebben.
Hieronder upgraden we eerst het besturingsvlak en maken we vervolgens een nieuwe knooppuntgroep met v1.18 die de nieuwe versie van het besturingssysteem van de knooppuntafbeelding ontvangt.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14 --control-plane-only

az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

## <a name="container-runtime-configuration"></a>Configuratie van containerruntime

Een containerruntime is software die containers uitvoert en containerafbeeldingen op een knooppunt beheert. De runtime helpt bij het abstraheeren van sys-aanroepen of besturingssysteemspecifieke functionaliteit voor het uitvoeren van containers in Linux of Windows. AKS-clusters die gebruikmaken van Kubernetes-knooppuntgroepen van versie 1.19 en groter gebruik `containerd` als containerruntime. AKS-clusters met Kubernetes vóór v1.19 voor knooppuntgroepen gebruiken [Moby](https://mobyproject.org/) (upstream docker) als containerruntime.

![Docker CRI 1](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/) is een [OCI-compatibele](https://opencontainers.org/) kerncontainerruntime (Open Container Initiative) die de minimale set vereiste functionaliteit biedt voor het uitvoeren van containers en het beheren van afbeeldingen op een knooppunt. Het is [in maart](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/) 2017 aan de Cloud Native Compute Foundation (CNCF) doneerd. De huidige Moby-versie die AKS gebruikt, maakt al gebruik van en is gebaseerd op `containerd` , zoals hierboven wordt weergegeven.

Met een knooppunt- en knooppuntgroep op basis van , in plaats van te communiceren met de , zal de kubelet rechtstreeks communiceren met via de CRI-invoeg-invoeging (container runtime interface), waardoor extra hops in de stroom worden verwijderd in vergelijking met de `containerd` `dockershim` `containerd` Docker CRI-implementatie. Als zodanig ziet u een betere opstartlatentie voor pods en minder resourcegebruik (CPU en geheugen).

Door voor `containerd` AKS-knooppunten te gebruiken, verbetert de opstartlatentie van pods en neemt het resourceverbruik van knooppunten door de containerruntime af. Deze verbeteringen worden mogelijk gemaakt door deze nieuwe architectuur, waarbij kubelet rechtstreeks met de CRI-invoegvoeging praat terwijl `containerd` in moby-/docker-architectuur kubelet zou communiceren met de docker-engine en voordat deze bereikt, waardoor er extra hops in de stroom `dockershim` `containerd` zijn.

![Docker CRI 2](media/cluster-configuration/containerd-cri.png)

`Containerd` werkt op elke GA-versie van Kubernetes in AKS en in elke upstream kubernetes-versie boven v1.19 en ondersteunt alle Kubernetes- en AKS-functies.

> [!IMPORTANT]
> Clusters met knooppuntgroepen die zijn gemaakt op Kubernetes v1.19 of hoger, zijn standaard ingesteld op `containerd` voor de containerruntime. Clusters met knooppuntgroepen op een ondersteunde Kubernetes-versie van minder dan 1.19 ontvangen voor de containerruntime, maar worden bijgewerkt naar zodra de Kubernetes-versie van de knooppuntgroep is bijgewerkt naar `Moby` `ContainerD` v1.19 of hoger. U kunt nog steeds `Moby` knooppuntgroepen en clusters in oudere ondersteunde versies gebruiken totdat deze uitvallen.
> 
> Het wordt ten zeerste aanbevolen om uw workloads op AKS-knooppuntgroepen te testen met vóór het gebruik van clusters met `containerD` 1,19 of hoger.

### <a name="containerd-limitationsdifferences"></a>`Containerd` beperkingen/verschillen

* Als u `containerd` wilt gebruiken als de container-runtime, moet u AKS Ubuntu 18.04 gebruiken als basis-os-installatiebestand.
* Hoewel de docker-toolset nog steeds aanwezig is op de knooppunten, gebruikt Kubernetes `containerd` als de containerruntime. Omdat Moby/Docker de door Kubernetes gemaakte containers op de knooppunten niet beheert, kunt u uw containers dus niet weergeven of gebruiken met behulp van Docker-opdrachten (zoals ) of de `docker ps` Docker-API.
* Voor wordt u aangeraden om te gebruiken als een vervangende CLI in plaats van de Docker-CLI voor het oplossen van problemen met pods, containers en containerafbeeldingen op `containerd` [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) Kubernetes-knooppunten (bijvoorbeeld  `crictl ps` ). 
   * Het biedt niet de volledige functionaliteit van de docker-CLI. Deze is alleen bedoeld voor probleemoplossing.
   * `crictl` biedt een meer kubernetes-vriendelijke weergave van containers, met concepten zoals pods, enzovoort.
* `Containerd` stelt logboekregistratie in met behulp van de gestandaardiseerde indeling voor logboekregistratie (dit verschilt van wat u momenteel krijgt van het `cri` JSON-stuurprogramma van Docker). Uw logboekregistratieoplossing moet ondersteuning bieden voor de `cri` logboekindeling [(zoals Azure Monitor for Containers](../azure-monitor/containers/container-insights-enable-new-cluster.md))
* U hebt geen toegang meer tot de docker-engine, , of u kunt `/var/run/docker.sock` Docker-in-Docker (Wiltd) gebruiken.
  * Als u momenteel toepassingslogboeken of bewakingsgegevens uit de Docker-engine haalt, gebruikt u in plaats daarvan iets als [Azure Monitor voor containers.](../azure-monitor/containers/container-insights-enable-new-cluster.md) Bovendien biedt AKS geen ondersteuning voor het uitvoeren van out-of-band-opdrachten op de agentknooppunten die instabiliteit kunnen veroorzaken.
  * Zelfs wanneer u Moby/docker gebruikt, wordt het sterk afgeraden om afbeeldingen te bouwen en rechtstreeks gebruik te maken van de docker-engine via de bovenstaande methoden. Kubernetes is niet volledig op de hoogte van de verbruikte resources. Deze benaderingen geven een groot aantal problemen die hier en hier [worden](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/)beschreven, bijvoorbeeld. [](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/)
* Afbeeldingen bouwen: u kunt uw huidige docker-buildwerkstroom gewoon blijven gebruiken, tenzij u afbeeldingen in uw AKS-cluster bouwt. In dit geval kunt u overwegen over te schakelen naar de aanbevolen methode voor het bouwen van afbeeldingen met behulp van [ACR-taken](../container-registry/container-registry-quickstart-task-cli.md)of een veiligere optie in het cluster, zoals [docker buildx](https://github.com/docker/buildx).

## <a name="generation-2-virtual-machines"></a>Virtuele machines van Generatie 2

Azure ondersteunt [virtuele machines van generatie 2 (Gen2) (VM's).](../virtual-machines/generation-2.md) Virtuele VM's van de tweede generatie ondersteunen belangrijke functies die niet worden ondersteund in VM's van de eerste generatie (Gen1). Deze functies omvatten meer geheugen, Intel Software Guard Extensions (Intel SGX) en gevirtualiseerd permanent geheugen (vPMEM).

VM's van de tweede generatie maken gebruik van de nieuwe op UEFI gebaseerde opstartarchitectuur in plaats van de BIOS-architectuur die wordt gebruikt door virtuele VM's van de eerste generatie.
Alleen specifieke SKU's en grootten ondersteunen Gen2-VM's. Controleer de [lijst met ondersteunde grootten](../virtual-machines/generation-2.md#generation-2-vm-sizes)om te zien of uw SKU Gen2 ondersteunt of vereist.

Bovendien ondersteunen niet alle VM-installatie afbeeldingen Gen2, op AKS Gen2-VM's wordt de nieuwe [AKS Ubuntu 18.04-installatie afbeelding gebruikt.](#os-configuration) Deze afbeelding ondersteunt alle Gen2-SKU's en -grootten.

## <a name="ephemeral-os"></a>Kortstondig besturingssysteem

Standaard repliceert Azure automatisch de besturingssysteemschijf voor een virtuele machine naar Azure Storage om gegevensverlies te voorkomen als de VM naar een andere host moet worden verplaatst. Omdat containers echter niet zijn ontworpen om de lokale status persistent te maken, biedt dit gedrag beperkte waarde en biedt het tegelijkertijd enkele nadelen, waaronder tragere node-inrichting en hogere lees-/schrijflatentie.

Tijdelijke besturingssysteemschijven worden daarentegen alleen op de hostmachine opgeslagen, net als een tijdelijke schijf. Dit biedt een lagere lees-/schrijflatentie, samen met sneller schalen van knooppunt en clusterupgrades.

Net als bij de tijdelijke schijf is een tijdelijke besturingssysteemschijf opgenomen in de prijs van de virtuele machine, zodat u geen extra opslagkosten maakt.

> [!IMPORTANT]
>Wanneer een gebruiker niet expliciet beheerde schijven voor het besturingssysteem aanvraagt, wordt AKS standaard ingesteld op kortstondig besturingssysteem, indien mogelijk voor een bepaalde configuratie van de knooppuntpool.

Wanneer u een kortstondig besturingssysteem gebruikt, moet de besturingssysteemschijf in de VM-cache passen. De grootten voor de VM-cache zijn beschikbaar in de [Azure-documentatie](../virtual-machines/dv3-dsv3-series.md) tussen haakjes naast I/O-doorvoer ('cachegrootte in GiB').

Met behulp van de standaardgrootte van de AKS-VM Standard_DS2_v2 met de standaardschijfgrootte van het besturingssysteem van 100 GB als voorbeeld, ondersteunt deze VM-grootte kortstondig besturingssysteem, maar heeft deze slechts 86 GB cachegrootte. Deze configuratie wordt standaard ingesteld op beheerde schijven als de gebruiker deze niet expliciet opgeeft. Als een gebruiker expliciet een kortstondig besturingssysteem heeft aangevraagd, ontvangt deze een validatiefout.

Als een gebruiker dezelfde Standard_DS2_v2 met een besturingssysteemschijf van 60 GB aanvraagt, wordt deze configuratie standaard ingesteld op een kortstondig besturingssysteem: de aangevraagde grootte van 60 GB is kleiner dan de maximale cachegrootte van 86 GB.

Met Standard_D8s_v3 besturingssysteemschijf van 100 GB ondersteunt deze VM-grootte kortstondig besturingssysteem en heeft 200 GB cacheruimte. Als een gebruiker het type besturingssysteemschijf niet opgeeft, ontvangt de nodepool standaard een kortstondig besturingssysteem. 

Voor kortstondig besturingssysteem is ten minste versie 2.15.0 van de Azure CLI vereist.

### <a name="use-ephemeral-os-on-new-clusters"></a>Kortstondig besturingssysteem gebruiken in nieuwe clusters

Configureer het cluster voor het gebruik van kortstondige besturingssysteemschijven wanneer het cluster wordt gemaakt. Gebruik de vlag om kortstondig besturingssysteem in `--node-osdisk-type` te stellen als het type besturingssysteemschijf voor het nieuwe cluster.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

Als u een normaal cluster wilt maken met behulp van besturingssysteemschijven die zijn gekoppeld aan het netwerk, kunt u dit doen door op te `--node-osdisk-type=Managed` geven. U kunt er ook voor kiezen om meer kortstondige os-knooppuntgroepen toe te voegen, zoals hieronder wordt weergegeven.

### <a name="use-ephemeral-os-on-existing-clusters"></a>Kortstondig besturingssysteem gebruiken in bestaande clusters
Configureer een nieuwe knooppuntgroep voor het gebruik van kortstondige besturingssysteemschijven. Gebruik de vlag om in te stellen als het type besturingssysteemschijf als het type `--node-osdisk-type` besturingssysteemschijf voor die knooppuntgroep.

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> Met een kortstondig besturingssysteem kunt u VM- en exemplaarafbeeldingen implementeren tot de grootte van de VM-cache. In het geval van AKS maakt de standaardconfiguratie van de besturingssysteemschijf van het knooppunt gebruik van 128 GB. Dit betekent dat u een VM-grootte nodig hebt met een cache die groter is dan 128 GB. De standaardwaarde Standard_DS2_v2 een cachegrootte van 86 GB, die niet groot genoeg is. De Standard_DS3_v2 heeft een cachegrootte van 172 GB, die groot genoeg is. U kunt ook de standaardgrootte van de besturingssysteemschijf beperken met behulp van `--node-osdisk-size` . De minimale grootte voor AKS-afbeeldingen is 30 GB. 

Als u knooppuntgroepen wilt maken met besturingssysteemschijven die aan het netwerk zijn gekoppeld, kunt u dit doen door op te `--node-osdisk-type Managed` geven.

## <a name="custom-resource-group-name"></a>Naam van aangepaste resourcegroep

Wanneer u een cluster Azure Kubernetes Service in Azure implementeert, wordt er een tweede resourcegroep gemaakt voor de werkknooppunten. Standaard geeft AKS de knooppuntresourcegroep een naam, maar `MC_resourcegroupname_clustername_location` u kunt ook uw eigen naam geven.

Als u de naam van uw eigen resourcegroep wilt opgeven, installeert u de Azure CLI-extensie aks-preview versie 0.3.2 of hoger. Gebruik de Azure CLI met de parameter van de opdracht om een aangepaste naam voor de `--node-resource-group` `az aks create` resourcegroep op te geven. Als u een Azure Resource Manager gebruikt om een AKS-cluster te implementeren, kunt u de naam van de resourcegroep definiëren met behulp van de `nodeResourceGroup` eigenschap .

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

De secundaire resourcegroep wordt automatisch gemaakt door de Azure-resourceprovider in uw eigen abonnement. U kunt alleen de naam van de aangepaste resourcegroep opgeven wanneer het cluster wordt gemaakt. 

Wanneer u met de knooppuntresourcegroep werkt, moet u er rekening mee houden dat u het volgende niet kunt doen:

- Geef een bestaande resourcegroep op voor de knooppuntresourcegroep.
- Geef een ander abonnement op voor de knooppuntresourcegroep.
- Wijzig de naam van de knooppuntresourcegroep nadat het cluster is gemaakt.
- Geef namen op voor de beheerde resources in de knooppuntresourcegroep.
- Door Azure gemaakte tags van beheerde resources in de knooppuntresourcegroep wijzigen of verwijderen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het upgraden van de knooppuntafbeeldingen](node-image-upgrade.md) in uw cluster.
- Zie [Een AKS Azure Kubernetes Service cluster upgraden](upgrade-cluster.md) voor meer informatie over het upgraden van uw cluster naar de nieuwste versie van Kubernetes.
- Meer informatie over [ `containerd` en Kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)
- Zie de lijst met [veelgestelde vragen over AKS](faq.md) voor antwoorden op enkele veelgestelde vragen over AKS.
- Lees meer over [kortstondige besturingssysteemschijven.](../virtual-machines/ephemeral-os-disks.md)


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
