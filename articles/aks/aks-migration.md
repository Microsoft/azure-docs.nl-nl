---
title: Migreren naar Azure Kubernetes Service (AKS)
description: Migreren naar Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 123f4e5c2442b913a53288602d1c56f199b131a6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872781"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migreren naar Azure Kubernetes Service (AKS)

Deze handleiding bevat informatie over de huidige aanbevolen AKS-configuratie om u te helpen bij het plannen en uitvoeren van een geslaagde migratie naar Azure Kubernetes Service (AKS). Hoewel dit artikel niet elk scenario beschreven, bevat het koppelingen naar meer gedetailleerde informatie voor het plannen van een geslaagde migratie.

Dit document biedt ondersteuning voor de volgende scenario's:

* Bepaalde toepassingen in een container zetten en migreren naar AKS met behulp [van Azure Migrate](../migrate/migrate-services-overview.md).
* Een AKS-cluster migreren met [beschikbaarheidssets naar](../virtual-machines/windows/tutorial-availability-sets.md) [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md).
* Een AKS-cluster migreren om een [Standard SKU-load balancer.](./load-balancer-standard.md)
* Migreren van Azure Container Service (ACS) - buiten bedrijf stellen [op 31 januari 2020](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) naar AKS.
* Migreren van [AKS-engine](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) naar AKS.
* Migreren van niet-Azure Kubernetes-clusters naar AKS.
* Bestaande resources verplaatsen naar een andere regio.

Zorg er tijdens de migratie voor dat uw Kubernetes-doelversie zich binnen het ondersteunde venster voor AKSbevat. Oudere versies zijn mogelijk niet binnen het ondersteunde bereik en vereisen dat een versie-upgrade wordt ondersteund door AKS. Zie Ondersteunde [Kubernetes-versies van AKS voor meer informatie.](./supported-kubernetes-versions.md)

Als u migreert naar een nieuwere versie van Kubernetes, controleert u het ondersteuningsbeleid voor [Kubernetes-versie](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)en versieverschil.

Verschillende opensource-hulpprogramma's kunnen u helpen bij uw migratie, afhankelijk van uw scenario:

* [Velero](https://velero.io/) (vereist Kubernetes 1.7+)
* [Azure Kube CLI-extensie](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

In dit artikel geven we een overzicht van de migratiedetails voor:

> [!div class="checklist"]
> * Toepassingen in een container zetten via Azure Migrate 
> * AKS met Standard Load Balancer en Virtual Machine Scale Sets
> * Bestaande gekoppelde Azure-services
> * Geldige quota garanderen
> * Hoge beschikbaarheid en bedrijfscontinuïteit
> * Overwegingen voor staatloze toepassingen
> * Overwegingen voor stateful toepassingen
> * Implementatie van uw clusterconfiguratie

## <a name="use-azure-migrate-to-migrate-your-applications-to-aks"></a>Gebruik Azure Migrate om uw toepassingen te migreren naar AKS

Azure Migrate biedt een uniform platform voor het evalueren en migreren naar on-premises servers, infrastructuur, toepassingen en gegevens van Azure. Voor AKS kunt u Azure Migrate voor de volgende taken:

* [Containerize ASP.NET toepassingen en migreren naar AKS](../migrate/tutorial-containerize-aspnet-kubernetes.md)
* [Java-webtoepassingen in een container zetten en migreren naar AKS](../migrate/tutorial-containerize-java-kubernetes.md)

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS met Standard Load Balancer en Virtual Machine Scale Sets

AKS is een beheerde service die unieke mogelijkheden biedt met lagere beheeroverhead. Aangezien AKS een beheerde service is, [](./quotas-skus-regions.md) moet u een keuze maken uit een reeks regio's die door AKS worden ondersteund. Mogelijk moet u uw bestaande toepassingen wijzigen om ze in orde te houden op het door AKS beheerde besturingsvlak tijdens de overgang van uw bestaande cluster naar AKS.

We raden u aan AKS-clusters te gebruiken die worden Virtual Machine Scale Sets en [de Azure-Standard Load Balancer](./load-balancer-standard.md) om ervoor te zorgen dat u functies krijgt zoals: [](../virtual-machine-scale-sets/index.yml)
* [Meerdere knooppuntgroepen](./use-multiple-node-pools.md),
* [Beschikbaarheidszones](../availability-zones/az-overview.md),
* [Geautoriseerde IP-adresbereiken](./api-server-authorized-ip-ranges.md),
* [Automatische schaalverdeder voor clusters](./cluster-autoscaler.md),
* [Azure Policy voor AKS](../governance/policy/concepts/policy-for-kubernetes.md), en
* Andere nieuwe functies wanneer ze worden uitgebracht.

AKS-clusters die worden ondersteund [door beschikbaarheidssets voor](../virtual-machines/availability.md#availability-sets) virtuele machines bieden geen ondersteuning voor veel van deze functies.

In het volgende voorbeeld wordt een AKS-cluster gemaakt met één knooppuntgroep die wordt gebruikt door een virtuele-machineschaalset (VM). Het cluster:
* Maakt gebruik van een load balancer. 
* Hiermee schakelt u de automatische schaalverdeder voor clusters in voor de knooppuntgroep voor het cluster.
* Hiermee stelt u minimaal *1* en maximaal *3 knooppunten* in.

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

## <a name="existing-attached-azure-services"></a>Bestaande gekoppelde Azure-services

Bij het migreren van clusters hebt u mogelijk externe Azure-services gekoppeld. Hoewel voor de volgende services geen resourcevernieuwing is vereist, moeten verbindingen van eerdere naar nieuwe clusters worden bijgewerkt om de functionaliteit te behouden.

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* Opslagaccount
* Externe databases

## <a name="ensure-valid-quotas"></a>Geldige quota garanderen

Omdat er tijdens de migratie andere VM's in uw abonnement worden geïmplementeerd, moet u controleren of uw quota en limieten voldoende zijn voor deze resources. Vraag indien nodig een verhoging van [het vCPU-quotum aan.](../azure-portal/supportability/per-vm-quota-requests.md)

Mogelijk moet u een verhoging van de [netwerkquota](../azure-portal/supportability/networking-quota-requests.md) aanvragen om ervoor te zorgen dat u geen IP's gebruikt. Zie Netwerk- en [IP-adresbereiken voor AKS voor meer informatie.](./configure-kubenet.md)

Zie Azure-abonnement en [servicelimieten voor meer informatie.](../azure-resource-manager/management/azure-subscription-service-limits.md) Als u uw huidige quota wilt controleren, Azure Portal u naar de [blade](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)Abonnementen, selecteert u uw abonnement en selecteert u **vervolgens Gebruik + quota.**

## <a name="high-availability-and-business-continuity"></a>Hoge beschikbaarheid en bedrijfscontinuïteit

Als uw toepassing downtime niet kan verwerken, moet u de best practices voor migratiescenario's met hoge beschikbaarheid volgen. Lees meer over Best practices voor complexe planning van bedrijfscontinuïteit, herstel na noodherstel en maximale [uptime in Azure Kubernetes Service (AKS)](./operator-best-practices-multi-region.md).

Voor complexe toepassingen migreert u doorgaans in een periode in plaats van alles tegelijk, wat betekent dat de oude en nieuwe omgevingen mogelijk moeten communiceren via het netwerk. Toepassingen die eerder `ClusterIP` gebruikmaken van services om te communiceren, moeten mogelijk worden blootgesteld als type en op de juiste wijze worden `LoadBalancer` beveiligd.

Als u de migratie wilt voltooien, moet u clients naar de nieuwe services laten wijzen die op AKS worden uitgevoerd. U wordt aangeraden verkeer om te leiden door DNS bij te werken zodat deze verwijst naar de Load Balancer voor uw AKS-cluster.

[Azure Traffic Manager](../traffic-manager/index.yml) klanten naar het gewenste Kubernetes-cluster en toepassings exemplaar leiden. Traffic Manager is een DNS-verkeer dat load balancer netwerkverkeer over regio's kan distribueren. Voor de beste prestaties en redundantie stuurt u al het verkeer van de toepassing via Traffic Manager het naar uw AKS-cluster gaat. 

In een implementatie met meerdere clusters moeten klanten verbinding maken met een Traffic Manager DNS-naam die naar de services op elk AKS-cluster wijst. Definieer deze services met behulp Traffic Manager eindpunten. Elk eindpunt is de *service load balancer IP.* Gebruik deze configuratie om netwerkverkeer van het Traffic Manager in één regio naar het eindpunt in een andere regio te leiden.

![AKS met Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Azure Front Door Service](../frontdoor/front-door-overview.md) is een andere optie voor het routeren van verkeer voor AKS-clusters. Met Azure Front Door Service kunt u de wereldwijde routering voor uw webverkeer definiëren, beheren en bewaken door te optimaliseren voor de beste prestaties en directe wereldwijde failover voor hoge beschikbaarheid. 

### <a name="considerations-for-stateless-applications"></a>Overwegingen voor staatloze toepassingen

Migratie van staatloze toepassingen is het meest eenvoudige geval:
1. Pas uw resourcedefinities (YAML of Helm) toe op het nieuwe cluster.
1. Zorg ervoor dat alles werkt zoals verwacht.
1. Verkeer omleiden om uw nieuwe cluster te activeren.

### <a name="considerations-for-stateful-applications"></a>Overwegingen voor stateful toepassingen

Plan uw migratie van stateful toepassingen zorgvuldig om gegevensverlies of onverwachte downtime te voorkomen.

* Als u Azure Files, kunt u de bestands share als een volume aan het nieuwe cluster toevoegen. Zie [Statische Azure Files als een volume .](./azure-files-volume.md#mount-file-share-as-an-persistent-volume)
* Als u Azure Managed Disks, kunt u de schijf alleen als deze niet is vastgemaakt aan een VM. Zie [Mount Static Azure Disk as a Volume (Statische Azure-schijf als een volume monteren).](./azure-disk-volume.md#mount-disk-as-volume)
* Als geen van deze methoden werkt, kunt u back-up- en herstelopties gebruiken. Zie [Velero op Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md).

#### <a name="azure-files"></a>Azure Files

In tegenstelling tot schijven kunnen Azure Files gelijktijdig aan meerdere hosts worden bevestigd. In uw AKS-cluster verhinderen Azure en Kubernetes niet dat u een pod maakt die nog steeds door uw AKS-cluster wordt gebruikt. Om gegevensverlies en onverwacht gedrag te voorkomen, moet u ervoor zorgen dat de clusters niet tegelijkertijd naar dezelfde bestanden schrijven.

Als uw toepassing meerdere replica's kan hosten die naar dezelfde bestands share wijzen, volgt u de stappen voor staatloze migratie en implementeert u uw YAML-definities in uw nieuwe cluster. 

Zo niet, dan bestaat een mogelijke migratie uit de volgende stappen:

1. Controleer of uw toepassing correct werkt.
1. Uw live verkeer naar uw nieuwe AKS-cluster laten wijzen.
1. Verbreed het oude cluster.

Als u met een lege share wilt beginnen en een kopie van de brongegevens wilt maken, kunt u de opdrachten gebruiken [`az storage file copy`](/cli/azure/storage/file/copy) om uw gegevens te migreren.


#### <a name="migrating-persistent-volumes"></a>Permanente volumes migreren

Als u bestaande permanente volumes migreert naar AKS, volgt u doorgaans deze stappen:

1. Quiesce schrijft naar de toepassing. 
    * Deze stap is optioneel en vereist downtime.
1. Maak momentopnamen van de schijven.
1. Maak nieuwe beheerde schijven op de momentopnamen.
1. Permanente volumes maken in AKS.
1. Werk podspecificaties [bij voor het gebruik van bestaande volumes](./azure-disk-volume.md) in plaats van PersistentVolumeClaims (statische inrichting).
1. Implementeer uw toepassing in AKS.
1. Controleer of uw toepassing correct werkt.
1. Uw live verkeer naar uw nieuwe AKS-cluster laten wijzen.

> [!IMPORTANT]
> Als u ervoor kiest geen schrijf- of schrijfgebruik te doen, moet u gegevens repliceren naar de nieuwe implementatie. Anders mist u de gegevens die zijn geschreven nadat u de momentopnamen van de schijf hebt gemaakt.

Sommige opensource-hulpprogramma's kunnen u helpen bij het maken van beheerde schijven en het migreren van volumes tussen Kubernetes-clusters:

* [Azure CLI Disk Copy-extensie](https://github.com/noelbundick/azure-cli-disk-copy-extension) kopieert en converteert schijven tussen resourcegroepen en Azure-regio's.
* [Met de Azure Kube CLI-extensie](https://github.com/yaron2/azure-kube-cli) worden ACS Kubernetes-volumes opsommen en naar een AKS-cluster gemigreerd.


### <a name="deployment-of-your-cluster-configuration"></a>Implementatie van uw clusterconfiguratie

U wordt aangeraden uw bestaande PIJPLIJN voor continue integratie (CI) en Continuous Deliver (CD) te gebruiken om een bekende goede configuratie in AKS te implementeren. U kunt Azure Pipelines gebruiken om uw [toepassingen te bouwen en te implementeren in AKS.](/azure/devops/pipelines/ecosystems/kubernetes/aks-template) Kloon uw bestaande implementatietaken en zorg ervoor dat `kubeconfig` naar het nieuwe AKS-cluster wijst.

Als dat niet mogelijk is, exporteert u resourcedefinities uit uw bestaande Kubernetes-cluster en pas u deze vervolgens toe op AKS. U kunt gebruiken om `kubectl` objecten te exporteren.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Bestaande resources verplaatsen naar een andere regio

Mogelijk wilt u uw AKS-cluster verplaatsen naar een [andere regio die wordt ondersteund door AKS][region-availability]. U wordt aangeraden een nieuw cluster in de andere regio te maken en vervolgens uw resources en toepassingen in uw nieuwe cluster te implementeren. 

Als er services zoals Azure [Dev Spaces][azure-dev-spaces] worden uitgevoerd op uw AKS-cluster, moet u deze services bovendien installeren en configureren op uw cluster in de nieuwe regio.


In dit artikel hebben we de migratiedetails samengevat voor:

> [!div class="checklist"]
> * AKS met Standard Load Balancer en Virtual Machine Scale Sets
> * Bestaande gekoppelde Azure-services
> * Geldige quota garanderen
> * Hoge beschikbaarheid en bedrijfscontinuïteit
> * Overwegingen voor staatloze toepassingen
> * Overwegingen voor stateful toepassingen
> * Implementatie van uw clusterconfiguratie


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: ../dev-spaces/index.yml
