---
title: De automatische schaalverdeder voor clusters in Azure Kubernetes Service (AKS) gebruiken
description: Meer informatie over het gebruik van automatische schaalvergroting van clusters om uw cluster automatisch te schalen om te voldoen aan de toepassingseisen in een Azure Kubernetes Service (AKS)-cluster.
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: f02b91f73320786716e356639d8134280325dc19
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775993"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Een cluster automatisch schalen om te voldoen aan de toepassingsvraag op Azure Kubernetes Service (AKS)

Als u wilt voldoen aan de toepassingseisen in Azure Kubernetes Service (AKS), moet u mogelijk het aantal knooppunten aanpassen dat uw workloads uitvoeren. Het onderdeel voor automatische schaal aanpassen van clusters kan pods in uw cluster bekijken die niet kunnen worden gepland vanwege resourcebeperkingen. Wanneer er problemen worden gedetecteerd, wordt het aantal knooppunten in een knooppuntgroep verhoogd om aan de vraag van de toepassing te voldoen. Knooppunten worden ook regelmatig gecontroleerd op een gebrek aan draaiende pods, met het aantal knooppunten en vervolgens naar behoefte afgenomen. Dankzij deze mogelijkheid om het aantal knooppunten in uw AKS-cluster automatisch omhoog of omlaag te schalen, kunt u een efficiënt, rendabel cluster uitvoeren.

In dit artikel wordt beschreven hoe u de automatische schaalverdeder voor clusters in een AKS-cluster kunt inschakelen en beheren.

## <a name="before-you-begin"></a>Voordat u begint

Voor dit artikel moet u Azure CLI versie 2.0.76 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="about-the-cluster-autoscaler"></a>Over de automatische schaalverdeder van clusters

Clusters moeten vaak automatisch worden geschaald om zich aan te passen aan veranderende toepassingseisen, zoals tussen de werkdag en 's avonds of in het weekend. AKS-clusters kunnen op twee manieren worden geschaald:

* De **automatische schaalverdeder van** clusters kijkt naar pods die niet kunnen worden gepland op knooppunten vanwege resourcebeperkingen. Het cluster verhoogt vervolgens automatisch het aantal knooppunten.
* De **horizontale automatische schaalverlener voor** pods maakt gebruik van de Metrics Server in een Kubernetes-cluster om de resourcevraag van pods te bewaken. Als een toepassing meer resources nodig heeft, wordt het aantal pods automatisch verhoogd om aan de vraag te voldoen.

![De automatische schaalverdeder voor clusters en horizontale automatische schaalverlening van pods werken vaak samen om de vereiste toepassingseisen te ondersteunen](media/autoscaler/cluster-autoscaler.png)

Zowel de horizontale automatische schaalverlener voor pods als de automatische schaalverdeder voor clusters kan naar behoefte ook het aantal pods en knooppunten verlagen. De automatische schaalverdeder voor clusters vermindert het aantal knooppunten wanneer er een bepaalde tijd ongebruikte capaciteit is. Pods op een knooppunt dat door de automatische schaalverdedering van het cluster moet worden verwijderd, worden veilig elders in het cluster gepland. De automatische schaalvergroting van clusters kan mogelijk niet omlaag worden geschaald als pods niet kunnen worden verplaatst, zoals in de volgende situaties:

* Een pod wordt rechtstreeks gemaakt en wordt niet gebacked door een controllerobject, zoals een implementatie- of replicaset.
* Een PDB (Pod Disruption Budget) is te beperkend en staat niet toe dat het aantal pods onder een bepaalde drempelwaarde valt.
* Een pod maakt gebruik van knooppunt selectors of anti-affiniteit die niet kunnen worden gehonoreerd als deze is gepland op een ander knooppunt.

Zie Welke typen pods kunnen voorkomen dat een knooppunt wordt verwijderd door de automatische schaalvergroting van clusters? voor meer informatie over hoe automatische schaalvergroting van clusters mogelijk niet kan [worden omlaag geschaald.][autoscaler-scaledown]

De automatische schaalvergroting van clusters maakt gebruik van opstartparameters voor zaken zoals tijdsintervallen tussen schaalgebeurtenissen en resourcedrempels. Zie Using [the autoscaler profile](#using-the-autoscaler-profile)(Het profiel voor automatisch schalen gebruiken) voor meer informatie over de parameters die door de automatische schaalverdeder voor clusters worden gebruikt.

De automatische schaalverdesters voor clusters en horizontale pods kunnen samenwerken en worden vaak beide geïmplementeerd in een cluster. In combinatie is de horizontale automatische schaalverdeder voor pods gericht op het uitvoeren van het aantal pods dat nodig is om aan de vraag van de toepassing te voldoen. De automatische schaalverdedering van clusters is gericht op het uitvoeren van het aantal knooppunten dat is vereist voor de ondersteuning van de geplande pods.

> [!NOTE]
> Handmatig schalen is uitgeschakeld wanneer u de automatische schaalvergroting van clusters gebruikt. Laat de automatische schaalverdeder van clusters het vereiste aantal knooppunten bepalen. Als u het cluster handmatig wilt schalen, schakelt [u de automatische schaalvergroting voor clusters uit.](#disable-the-cluster-autoscaler)

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Een AKS-cluster maken en de automatische schaalverdeder voor clusters inschakelen

Als u een AKS-cluster wilt maken, gebruikt u [de opdracht az aks create.][az-aks-create] Als u de automatische schaalverdeder voor clusters in de knooppuntgroep voor het cluster wilt inschakelen en configureren, gebruikt u de `--enable-cluster-autoscaler` parameter en geeft u een knooppunt en `--min-count` `--max-count` op.

> [!IMPORTANT]
> De automatische schaalverdedering van clusters is een Kubernetes-onderdeel. Hoewel het AKS-cluster gebruikmaakt van een virtuele-machineschaalset voor de knooppunten, moet u instellingen voor automatische schaalsets niet handmatig inschakelen of bewerken in de Azure Portal of met behulp van de Azure CLI. Laat de kubernetes-clusterautoscaler de vereiste schaalinstellingen beheren. Zie Kan ik de [AKS-resources in de][aks-faq-node-resource-group] knooppuntresourcegroep wijzigen voor meer informatie.

In het volgende voorbeeld wordt een AKS-cluster gemaakt met één knooppuntgroep die wordt gebruikt door een virtuele-machineschaalset. Ook wordt de automatische schaalverdedering van clusters op de knooppuntgroep voor het cluster mogelijk en worden minimaal *1* en maximaal *3* knooppunten geconfigureerd:

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

Het duurt enkele minuten om het cluster te maken en de instellingen voor automatische schaal aanpassen van clusters te configureren.

## <a name="update-an-existing-aks-cluster-to-enable-the-cluster-autoscaler"></a>Een bestaand AKS-cluster bijwerken om automatische schaalverdedering van clusters in te kunnenschakelen

Gebruik de [opdracht az aks update][az-aks-update] om de automatische schaalverdeling van clusters in de knooppuntgroep in te stellen en te configureren voor het bestaande cluster. Gebruik de `--enable-cluster-autoscaler` parameter en geef een knooppunt en `--min-count` `--max-count` op.

> [!IMPORTANT]
> De automatische schaalverdedering van clusters is een Kubernetes-onderdeel. Hoewel het AKS-cluster gebruikmaakt van een virtuele-machineschaalset voor de knooppunten, moet u instellingen voor automatische schaalsets niet handmatig inschakelen of bewerken in de Azure Portal of met behulp van de Azure CLI. Laat de kubernetes-clusterautoscaler de vereiste schaalinstellingen beheren. Zie Kan ik de [AKS-resources in de][aks-faq-node-resource-group] knooppuntresourcegroep wijzigen voor meer informatie.

In het volgende voorbeeld wordt een bestaand AKS-cluster bijgewerkt om de automatische schaalverdedering van clusters op de knooppuntgroep voor het cluster in te stellen en worden minimaal *1* en maximaal *3* knooppunten geconfigureerd:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Het duurt enkele minuten om het cluster bij te werken en de instellingen voor automatische schaal aanpassen van clusters te configureren.

## <a name="change-the-cluster-autoscaler-settings"></a>De instellingen voor automatische schaalverdedering van clusters wijzigen

> [!IMPORTANT]
> Als u meerdere knooppuntgroepen in uw AKS-cluster hebt, gaat u naar de sectie Automatisch schalen [met meerdere agentpools.](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled) Clusters met meerdere agentpools vereisen het gebruik van de opdrachtset om de specifieke eigenschappen `az aks nodepool` van knooppuntgroepen te wijzigen in plaats van `az aks` .

In de vorige stap om een AKS-cluster te maken of een bestaande knooppuntgroep bij te werken, is het minimale aantal knooppunt voor automatische schaalsets van clusters ingesteld op *1* en is het maximum aantal knooppunt ingesteld *op 3.* Als de vraag van uw toepassing verandert, moet u mogelijk het aantal knooppunt voor automatische schaalwijziging van clusters aanpassen.

Als u het aantal knooppunt wilt wijzigen, gebruikt u [de opdracht az aks update.][az-aks-update]

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

In het bovenstaande voorbeeld wordt automatisch schalen van clusters op de pool met één knooppunt in *myAKSCluster* bijgewerkt naar minimaal *1* en maximaal *5 knooppunten.*

> [!NOTE]
> De automatische schaalvergroting van clusters neemt beslissingen over schaalbaarheid op basis van het minimum- en maximumaantal dat is ingesteld voor elke knooppuntgroep, maar dwingt deze niet af na het bijwerken van het minimum- of maximumaantal. Als u bijvoorbeeld een minimum aantal van 5 instelt wanneer het huidige aantal knooppunt 3 is, wordt de pool niet onmiddellijk omhoog geschaald naar 5. Als het minimumaantal voor de knooppuntgroep een waarde heeft die hoger is dan het huidige aantal knooppunten, worden de nieuwe minimale of maximale instellingen in acht genomen wanneer er voldoende niet-te schalen pods aanwezig zijn die 2 nieuwe extra knooppunten vereisen en een gebeurtenis voor automatische schaalverdediging activeren. Na de schaalgebeurtenis worden de nieuwe limieten voor het aantal in acht genomen.

Controleer de prestaties van uw toepassingen en services en pas het aantal knooppunt voor automatische schaalverlening van clusters aan aan de vereiste prestaties.

## <a name="using-the-autoscaler-profile"></a>Het profiel voor automatisch schalen gebruiken

U kunt ook gedetailleerdere details van de automatische schaalverdedering voor clusters configureren door de standaardwaarden te wijzigen in het profiel voor automatische schaalschalen voor het hele cluster. Een gebeurtenis voor omlaag schalen vindt bijvoorbeeld plaats nadat knooppunten na 10 minuten te veel worden gebruikt. Als u elke 15 minuten workloads hebt die worden toegepast, kunt u het profiel voor automatische schaalvergroting wijzigen om na 15 of 20 minuten omlaag te schalen onder gebruikte knooppunten. Wanneer u automatische schaal aanpassen van clusters inschakelen, wordt een standaardprofiel gebruikt, tenzij u verschillende instellingen opgeeft. Het profiel voor automatische schaal aanpassen van clusters heeft de volgende instellingen die u kunt bijwerken:

| Instelling                          | Beschrijving                                                                              | Standaardwaarde |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| scaninterval                    | Hoe vaak het cluster opnieuw wordt geëvalueerd voor omhoog of omlaag schalen                                    | 10 seconden    |
| scale-down-delay-after-add       | Hoe lang na omhoog schalen de evaluatie van omlaag schalen wordt hervat                               | 10 minuten    |
| scale-down-delay-after-delete    | Hoe lang na het verwijderen van een knooppunt de evaluatie van omlaag schalen wordt hervat                          | scaninterval |
| scale-down-delay-after-failure   | Hoe lang na een storing bij omlaag schalen de evaluatie van omlaag schalen wordt hervat                     | 3 minuten     |
| omlaag schalen -onnodige tijd         | Hoe lang een knooppunt onnodig moet zijn voordat het in aanmerking komt voor omlaag schalen                  | 10 minuten    |
| scale-down-unready-time          | Hoe lang een ongelezen knooppunt onnodig moet zijn voordat het in aanmerking komt voor omlaag schalen         | 20 minuten    |
| scale-down-utilization-threshold | Knooppuntgebruiksniveau, gedefinieerd als som van aangevraagde resources gedeeld door capaciteit, waaronder een knooppunt kan worden overwogen om omlaag te schalen | 0,5 |
| max-graceful-termination-sec     | Maximum aantal seconden dat de automatische schaalvergroting van het cluster wacht op beëindiging van pods bij het omlaag schalen van een knooppunt | 600 seconden   |
| balance-similar-node-groups      | Detecteert vergelijkbare knooppuntgroepen en balancert het aantal knooppunten ertussen                 | onjuist         |
| Expander                         | Type knooppuntgroep [expander](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-expanders) dat moet worden gebruikt bij omhoog schalen. Mogelijke waarden: `most-pods` , `random` , , `least-waste` , `priority` | willekeurig | 
| skip-nodes-with-local-storage    | Als automatische schaalverdeling van het cluster waar is, worden knooppunten met pods met lokale opslag, bijvoorbeeld EmptyDir of HostPath, nooit verwijderd | true |
| skip-nodes-with-system-pods      | Als automatisch schalen van clusters waar is, worden knooppunten met pods van kube-system nooit verwijderd (met uitzondering van DaemonSet of mirror-pods) | true | 
| max-empty-bulk-delete            | Maximum aantal lege knooppunten dat tegelijkertijd kan worden verwijderd                       | 10 knooppunten      |
| new-pod-scale-up-delay           | Voor scenario's zoals burst/batch-schaal waarbij u niet wilt dat CA actie moet ondernemen voordat de kubernetes-scheduler alle pods kan plannen, kunt u ca vertellen dat niet-geplande pods moeten worden genegeerd voordat ze een bepaalde leeftijd hebben.                                                                                                                | 0 seconden    |
| max-total-unready-percentage     | Maximumpercentage ongelezen knooppunten in het cluster. Nadat dit percentage is overschreden, stopt de CA-bewerkingen | 45% |
| max-node-provision-time          | Maximale tijd dat de automatische schaalverdeder wacht tot een knooppunt is ingericht                           | 15 minuten    |   
| ok-total-unready-count           | Aantal toegestane ongelezen knooppunten, ongeacht max-total-unready-percentage            | 3 knooppunten       |

> [!IMPORTANT]
> Het profiel voor automatische schaalverdedering van clusters is van invloed op alle knooppuntgroepen die gebruikmaken van de automatische schaalverdeder voor clusters. U kunt geen profiel voor automatisch schalen per knooppuntgroep instellen.
>
> Voor het profiel voor automatische schaalschalen van clusters is versie *2.11.1* of hoger van de Azure CLI vereist. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>Het profiel voor automatische schaalset van clusters instellen op een bestaand AKS-cluster

Gebruik de [opdracht az aks update][az-aks-update-preview] met de parameter *cluster-autoscaler-profile* om het profiel voor automatische schaalset van clusters in te stellen op uw cluster. In het volgende voorbeeld wordt de instelling voor het scaninterval geconfigureerd als 30-seconden in het profiel.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

Wanneer u de automatische schaalverdeder voor clusters in het cluster inschakelen, gebruiken deze clusters ook het profiel voor automatische schaalverdedering van clusters. Bijvoorbeeld:

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!IMPORTANT]
> Wanneer u het profiel voor automatische schaalschalen voor clusters instelt, worden alle bestaande knooppuntgroepen met automatische schaalset voor clusters onmiddellijk gebruikt.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>Het profiel voor automatisch schalen van clusters instellen bij het maken van een AKS-cluster

U kunt ook de *parameter cluster-autoscaler-profile* gebruiken wanneer u uw cluster maakt. Bijvoorbeeld:

```azurecli-interactive
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3 \
  --cluster-autoscaler-profile scan-interval=30s
```

Met de bovenstaande opdracht maakt u een AKS-cluster en definieert u het scaninterval als 30 seconden voor het profiel voor automatisch schalen voor het hele cluster. Met de opdracht wordt ook de automatische schaalset van clusters in de initiële knooppuntgroep in staat stelt het minimale aantal knooppunt in op 1 en het maximumaantal knooppunt op 3.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>Automatisch schalen van clusters opnieuw instellen op standaardwaarden

Gebruik de [opdracht az aks update om][az-aks-update-preview] het profiel voor automatische schaalset van clusters in uw cluster opnieuw in te stellen.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>De automatische schaalverdeder voor clusters uitschakelen

Als u de automatische schaalverdeling voor clusters niet meer wilt gebruiken, kunt u deze uitschakelen met behulp van de [opdracht az aks update][az-aks-update-preview] en de parameter op te `--disable-cluster-autoscaler` geven. Knooppunten worden niet verwijderd wanneer de automatische schaalverdeder voor clusters is uitgeschakeld.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

U kunt het cluster handmatig schalen nadat u de automatische schaalvergroting van het cluster hebt uitgeschakeld met behulp van [de opdracht az aks scale.][az-aks-scale] Als u de horizontale automatische schaalfunctie voor pods gebruikt, blijft die functie worden uitgevoerd met de automatische schaalverdeder voor clusters uitgeschakeld, maar kunnen pods mogelijk niet worden gepland als alle knooppuntresources in gebruik zijn.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Een uitgeschakelde automatische schaalverdeder voor clusters opnieuw inschakelen

Als u de automatische schaalverdeling voor clusters opnieuw wilt inschakelen op een bestaand cluster, kunt u deze opnieuw inschakelen met de [opdracht az aks update,][az-aks-update-preview] waarin u de `--enable-cluster-autoscaler` parameters , en `--min-count` `--max-count` opgeeft.

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Logboeken en status van automatische schaalverdedering van clusters ophalen

Voor het diagnosticeren en opsporen van gebeurtenissen van automatische schaalverschalen, kunnen logboeken en status worden opgehaald uit de invoegvoegs voor automatische schaalverschalen.

AKS beheert de automatische schaalverdeder voor clusters namens u en voert deze uit in het beheerde besturingsvlak. U kunt het besturingsvlak-knooppunt inschakelen om de logboeken en bewerkingen van de CA weer te geven.

Volg deze stappen om logboeken te configureren die vanuit de automatische schaalverdeder voor clusters naar Log Analytics moeten worden ge pushen.

1. Stel een regel in voor resourcelogboeken om logboeken voor automatisch schalen van clusters naar Log Analytics te pushen. [Instructies worden hier beschreven.][aks-view-master-logs]Zorg ervoor dat u het selectievakje voor incheckt `cluster-autoscaler` bij het selecteren van opties voor 'Logboeken'.
1. Selecteer de sectie Logboeken in uw cluster via de Azure Portal.
1. Voer de volgende voorbeeldquery in Log Analytics in:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

Als het goed is, ziet u logboeken die vergelijkbaar zijn met het volgende voorbeeld, zolang er logboeken zijn om op te halen.

![Log Analytics-logboeken](media/autoscaler/autoscaler-logs.png)

De automatische schaalverdeder voor clusters schrijft ook de status uit naar een `configmap` met de naam `cluster-autoscaler-status` . Voer de volgende opdracht uit om deze logboeken op te `kubectl` halen. Er wordt een status gerapporteerd voor elke knooppuntgroep die is geconfigureerd met de automatische schaalverdeder voor clusters.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Lees de Veelgestelde vragen over het [GitHub-project Kubernetes/autoscaler][kubernetes-faq]voor meer informatie over wat er wordt geregistreerd vanuit de automatische schaalverdeder.

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>De automatische schaalverdeder van clusters gebruiken met meerdere knooppuntgroepen ingeschakeld

De automatische schaalverdeder voor clusters kan worden gebruikt samen [met meerdere knooppuntgroepen][aks-multiple-node-pools] ingeschakeld. Volg dit document voor meer informatie over het inschakelen van meerdere knooppuntgroepen en het toevoegen van extra knooppuntgroepen aan een bestaand cluster. Wanneer u beide functies samen gebruikt, kunt u de automatische schaalverdeling voor clusters inschakelen voor elke afzonderlijke knooppuntgroep in het cluster en unieke regels voor automatisch schalen aan elke groep doorgeven.

De onderstaande opdracht gaat [](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) ervan uit dat u de eerste instructies eerder in dit document hebt gevolgd en dat u het maximum aantal van een bestaande knooppuntgroep wilt bijwerken van *3* naar *5*. Gebruik de [opdracht az aks nodepool update om][az-aks-nodepool-update] de instellingen van een bestaande knooppuntgroep bij te werken.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

De automatische schaalverdeling voor clusters kan worden uitgeschakeld [met az aks nodepool update en het][az-aks-nodepool-update] doorgeven van de parameter `--disable-cluster-autoscaler` .

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Als u automatisch schalen van clusters opnieuw wilt inschakelen op een bestaand cluster, kunt u deze opnieuw inschakelen met behulp van de opdracht [az aks nodepool update,][az-aks-nodepool-update] waarmee u de `--enable-cluster-autoscaler` parameters , en `--min-count` `--max-count` opgeeft.

> [!NOTE]
> Als u van plan bent om de automatische schaalverplaatsing van clusters te gebruiken met knooppuntpools die meerdere zones bespannen en gebruikmaken van planningsfuncties met betrekking tot zones, zoals volumetopologische planning, is de aanbeveling om één knooppuntgroep per zone te hebben en de in teschakelen via het profiel voor automatische schaalverkennen. `--balance-similar-node-groups` Dit zorgt ervoor dat de automatische schaalvergroting met succes omhoog wordt geschaald en dat de grootte van de knooppuntpools in balans wordt gehouden.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u kunnen lezen hoe u het aantal AKS-knooppunten automatisch kunt schalen. U kunt ook de automatische horizontale schaalverdeder voor pods gebruiken om automatisch het aantal pods aan te passen dat uw toepassing wordt uitgevoerd. Zie Toepassingen schalen in AKS voor stappen voor het gebruik van de horizontale automatische [schaalvergroting voor pods.][aks-scale-apps]

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-control-plane-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register

<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why