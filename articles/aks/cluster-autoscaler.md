---
title: De automatische clustering van clusters gebruiken in azure Kubernetes service (AKS)
description: Meer informatie over hoe u de cluster-automatische schaal functie kunt gebruiken om uw cluster automatisch te schalen om te voldoen aan de toepassings vereisten in een Azure Kubernetes service-cluster (AKS).
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: 9caf56545efc6aefae525e28614d39705c00c21e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742565"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Een cluster automatisch schalen om te voldoen aan de toepassingsvraag op Azure Kubernetes Service (AKS)

Als u de vereisten van toepassingen in azure Kubernetes service (AKS) wilt blijven gebruiken, moet u mogelijk het aantal knoop punten aanpassen waarop uw workloads worden uitgevoerd. Het onderdeel voor het automatisch schalen van clusters kan in uw cluster worden bekeken en kan niet worden ingepland vanwege resource beperkingen. Wanneer er problemen worden gedetecteerd, wordt het aantal knoop punten in een knooppunt groep verhoogd om te voldoen aan de vraag van de toepassing. Knoop punten worden ook regel matig gecontroleerd op het ontbreken van een verwerkings hoeveelheid, met het aantal knoop punten dat vervolgens naar behoefte is afgenomen. Met deze mogelijkheid om automatisch het aantal knoop punten in uw AKS-cluster omhoog of omlaag te schalen, kunt u een efficiënt, rendabel cluster uitvoeren.

In dit artikel wordt beschreven hoe u de cluster-automatische schaal functie inschakelt en beheert in een AKS-cluster.

## <a name="before-you-begin"></a>Voordat u begint

Voor dit artikel moet u de Azure CLI-versie 2.0.76 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="about-the-cluster-autoscaler"></a>Over de automatische cluster schaal

Om aan te passen aan de wijziging van de toepassings vereisten, zoals tussen de dag en de avond of in het weekend, moeten clusters vaak een manier hebben om automatisch te schalen. AKS-clusters kunnen op een van de volgende twee manieren worden geschaald:

* De automatische schaal functie van het **cluster** controleert op peulen die niet kunnen worden gepland op knoop punten vanwege resource beperkingen. Het cluster verhoogt het aantal knoop punten vervolgens automatisch.
* De **pod** van de metrische gegevens in een Kubernetes-cluster wordt gebruikt om de resource vraag van Peul te controleren. Als een toepassing meer resources nodig heeft, wordt het aantal peulen automatisch verhoogd om aan de vraag te voldoen.

![De automatische clustering van clusters en de horizontale pod-automatisch schalen kunnen samen worden gebruikt ter ondersteuning van de vereiste toepassings vereisten](media/autoscaler/cluster-autoscaler.png)

Zowel de horizontale pod voor automatisch schalen als cluster automatisch schalen kunnen ook het aantal peulen en knoop punten verlagen als dat nodig is. De automatische schaal aanpassing van het cluster verkleint het aantal knoop punten wanneer er voor een bepaalde tijd ongebruikte capaciteit is. Het Peul op een knoop punt dat door de automatische cluster functie wordt verwijderd, wordt veilig gepland elders in het cluster. De automatische schaal aanpassing van het cluster kan mogelijk niet omlaag worden uitgebreid als er geen peulen kunnen worden verplaatst, zoals in de volgende situaties:

* Een pod wordt rechtstreeks gemaakt en wordt niet ondersteund door een controller object, zoals een implementatie of replicaset.
* Een pod-verstorings budget (PDB) is te beperkend en laat niet toe dat het aantal peulen onder een bepaalde drempel waarde valt.
* Een pod maakt gebruik van knooppunt selecties of een anti-affiniteit die niet kan worden gehonoreerd als deze zijn gepland op een ander knoop punt.

Voor meer informatie over hoe het cluster automatisch schalen niet kan worden geschaald, raadpleegt u [welke typen van de cluster automatisch schalen een knoop punt niet kunnen verwijderen?][autoscaler-scaledown]

De automatische schaal functie van het cluster maakt gebruik van opstart parameters voor dingen zoals tijds intervallen tussen schaal gebeurtenissen en resource drempels. Zie [het profiel voor automatisch schalen gebruiken](#using-the-autoscaler-profile)voor meer informatie over de para meters die door de cluster-automatische schaal functie worden gebruikt.

Het cluster en de horizontale pod kunnen samen worden gebruikt en worden vaak geïmplementeerd in een cluster. In combi natie met de horizontale Pod is de automatische schaal aanpassing gericht op het uitvoeren van het aantal vereiste aantallen dat nodig is om te voldoen aan de vraag van de toepassing. De cluster automatisch schalen is gericht op het uitvoeren van het aantal knoop punten dat vereist is voor de ondersteuning van de geplande peulen.

> [!NOTE]
> Hand matig schalen is uitgeschakeld wanneer u de automatische schaal functie van het cluster gebruikt. Laat de cluster automatisch schalen het vereiste aantal knoop punten bepalen. Als u de schaal van het cluster hand matig wilt aanpassen, [schakelt u de automatische clustering van clusters uit](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Een AKS-cluster maken en de cluster automatisch schalen inschakelen

Als u een AKS-cluster moet maken, gebruikt u de opdracht [AZ AKS Create][az-aks-create] . Voor het inschakelen en configureren van de cluster-automatische schaal functie voor de knooppunt groep voor het cluster, gebruikt u de `--enable-cluster-autoscaler` para meter en geeft u een knoop punt `--min-count` en op `--max-count` .

> [!IMPORTANT]
> De automatische schaal functie van het cluster is een Kubernetes-onderdeel. Hoewel het AKS-cluster gebruikmaakt van een schaalset voor virtuele machines voor de knoop punten, kunt u de instellingen voor het automatisch schalen van schaal sets niet hand matig inschakelen of bewerken in de Azure Portal of de Azure CLI gebruiken. Laat de Kubernetes voor cluster automatisch schalen de vereiste schaal instellingen beheren. Zie [kan ik de AKS-resources in de knooppunt resource groep wijzigen?][aks-faq-node-resource-group] voor meer informatie.

In het volgende voor beeld wordt een AKS-cluster gemaakt met één knooppunt groep die wordt ondersteund door een virtuele-machine schaalset. Ook wordt de cluster-automatische schaal functie voor de knooppunt groep voor het cluster ingeschakeld en worden mini maal *1* en Maxi maal *drie* knoop punten ingesteld:

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

Het duurt enkele minuten om het cluster te maken en de instellingen voor het automatisch schalen van clusters te configureren.

## <a name="update-an-existing-aks-cluster-to-enable-the-cluster-autoscaler"></a>Een bestaand AKS-cluster bijwerken om de automatische cluster schaalr in te scha kelen

Gebruik de opdracht [AZ AKS update][az-aks-update] om de cluster auto scaleer in te scha kelen voor de knooppunt groep voor het bestaande cluster. Gebruik de `--enable-cluster-autoscaler` para meter en geef een knoop punt `--min-count` en op `--max-count` .

> [!IMPORTANT]
> De automatische schaal functie van het cluster is een Kubernetes-onderdeel. Hoewel het AKS-cluster gebruikmaakt van een schaalset voor virtuele machines voor de knoop punten, kunt u de instellingen voor het automatisch schalen van schaal sets niet hand matig inschakelen of bewerken in de Azure Portal of de Azure CLI gebruiken. Laat de Kubernetes voor cluster automatisch schalen de vereiste schaal instellingen beheren. Zie [kan ik de AKS-resources in de knooppunt resource groep wijzigen?][aks-faq-node-resource-group] voor meer informatie.

In het volgende voor beeld wordt een bestaand AKS-cluster bijgewerkt om de automatische cluster functie in te scha kelen voor de knooppunt groep voor het cluster en worden er mini maal *1* en Maxi maal *3* knoop punten ingesteld:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Het duurt enkele minuten om het cluster bij te werken en de instellingen voor het automatisch schalen van clusters te configureren.

## <a name="change-the-cluster-autoscaler-settings"></a>De instellingen voor het automatisch schalen van clusters wijzigen

> [!IMPORTANT]
> Als u meerdere knooppunt groepen in uw AKS-cluster hebt, gaat u naar de [sectie automatisch schalen met meerdere agent groepen](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled). Voor clusters met meerdere agent Pools moet de `az aks nodepool` opdracht set worden gebruikt om specifieke eigenschappen van de knooppunten groep te wijzigen in plaats van `az aks` .

In de vorige stap om een AKS-cluster te maken of een bestaande knooppunt groep bij te werken, is het minimum aantal knoop punten van het cluster automatisch ingesteld op *1* en is het maximum aantal knoop punten ingesteld op *3*. Als uw toepassing wordt gewijzigd, moet u mogelijk het aantal knoop punten van de cluster automatisch schalen aanpassen.

Als u het aantal knoop punten wilt wijzigen, gebruikt u de opdracht [AZ AKS update][az-aks-update] .

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

In het bovenstaande voor beeld wordt de cluster-automatische schaal functie van de groep met één knoop punt in *myAKSCluster* ingesteld op mini maal *1* en Maxi maal *5* knoop punten.

> [!NOTE]
> De automatische schaal aanpassing van het cluster maakt beslissingen op basis van het minimum-en maximum aantal dat is ingesteld voor elke knooppunt groep, maar dwingt deze niet af na het bijwerken van de minimum-of maximum aantallen. Als u bijvoorbeeld een minimum aantal van 5 instelt wanneer het huidige aantal knoop punten 3 is, wordt de pool niet onmiddellijk geschaald naar 5. Als het minimum aantal voor de knooppunt groep een waarde heeft die hoger is dan het huidige aantal knoop punten, worden de nieuwe minimum-of maximum instellingen in acht genomen wanneer voldoende unschedulablee peulen aanwezig zijn die 2 nieuwe extra knoop punten vereisen en een gebeurtenis voor een automatische schaalr activeren. Na de gebeurtenis Scale worden de limieten voor het nieuwe aantal in acht genomen.

Bewaak de prestaties van uw toepassingen en services en pas het aantal knoop punten van de cluster automatisch aan, zodat dit overeenkomt met de vereiste prestaties.

## <a name="using-the-autoscaler-profile"></a>Het profiel voor automatisch schalen gebruiken

U kunt ook gedetailleerdere Details van de cluster automatisch schalen configureren door de standaard waarden te wijzigen in het profiel voor het automatisch schalen van het cluster. Een gebeurtenis met een omlaag schalen vindt bijvoorbeeld plaats nadat de knoop punten na 10 minuten zijn gebruikt. Als u werk belastingen hebt die om de 15 minuten worden uitgevoerd, wilt u mogelijk het profiel voor automatisch schalen wijzigen om na 15 of 20 minuten omlaag te schalen onder gebruikte knoop punten. Wanneer u de cluster-automatische schaal functie inschakelt, wordt er een standaard profiel gebruikt, tenzij u andere instellingen opgeeft. Het profiel voor het automatisch schalen van clusters heeft de volgende instellingen die u kunt bijwerken:

| Instelling                          | Beschrijving                                                                              | Standaardwaarde |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| Scan-interval                    | Hoe vaak het cluster opnieuw wordt geëvalueerd voor omhoog of omlaag schalen                                    | 10 seconden    |
| omlaag schalen na toevoegen       | Hoe lang na omhoog schalen de evaluatie wordt hervat                               | 10 minuten    |
| omlaag schalen na verwijderen    | Hoe lang na het verwijderen van het knoop punt de evaluatie wordt hervat                          | Scan-interval |
| omlaag schalen-omlaag-na-mislukt   | Hoe lang na uitval van de omlaag geschaalde fout dat het omlaag schalen van evaluatie versies wordt hervat                     | 3 minuten     |
| omlaag schalen-overbodige tijdstippen         | Hoe lang een knoop punt niet nodig moet zijn voordat deze in aanmerking komt voor omlaag omlaag schalen                  | 10 minuten    |
| omlaag schalen-niet-lees bare tijd          | Hoe lang een ongelezeny-knoop punt niet nodig moet zijn voordat het in aanmerking komt voor omlaag schalen         | 20 minuten    |
| schaal-down-gebruik-drempel waarde | Het niveau van het knooppunt gebruik, gedefinieerd als de som van aangevraagde resources gedeeld door capaciteit, waaronder een knoop punt kan worden overwogen voor omlaag schalen | 0,5 |
| Max-correct beëindigen-SEC     | Het maximum aantal seconden dat de cluster-automatische schaal bewerking wacht op beëindiging van pod wanneer u een knoop punt omlaag wilt schalen | 600 seconden   |
| saldo-vergelijkbaar-node-groups      | Detecteert vergelijk bare knooppunt Pools en balanceert het aantal knoop punten ertussen                 | onjuist         |
| uitbreiden                         | Type [uitbreidings](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-expanders) module voor de knooppunt groep die moet worden gebruikt voor omhoog schalen. Mogelijke waarden: `most-pods` , `random` , `least-waste` , `priority` | willekeurig | 
| overs Laan: knoop punten-met-Local-Storage    | Als de echte automatische schaal functie van het cluster nooit knoop punten met de lokale opslag, bijvoorbeeld EmptyDir of HostPath, verwijdert. | true |
| overs Laan: knoop punten-met-systeem-peul      | Als de echte cluster-functie voor automatisch schalen geen knoop punten met een van de uitvoeren-systemen (met uitzonde ring van Daemonset of spie gelen) verwijdert | true | 
| Max-leeg-bulk-Delete            | Maximum aantal lege knoop punten dat tegelijkertijd kan worden verwijderd                       | 10 knoop punten      |
| New-pod-upscale-up-vertraging           | Voor scenario's zoals burst/batch Scale, waar u niet wilt dat certificerings instantie van de kubernetes de gehele leeftijd kan plannen, kunt u ervoor zorgen dat certificerings instantie niet-geplande peulingen negeert voordat ze een bepaalde ouderdom hebben.                                                                                                                | 0 seconden    |
| Max-totaal-onleesbaar percentage     | Maximum percentage van ongelezeny-knoop punten in het cluster. Nadat dit percentage is overschreden, stopt de CA bewerkingen | 45% |
| Max-node-inrichtings tijd          | Maximum tijd dat de automatische schaalr wacht totdat een knoop punt is ingericht                           | 15 minuten    |   
| OK-totaal-onleesbaar aantal           | Aantal toegestane ongelezen knoop punten, ongeacht het Max-totaal-onleesbaar-percentage            | 3 knoop punten       |

> [!IMPORTANT]
> Het profiel van de cluster automatisch schalen is van invloed op alle knooppunt groepen die gebruikmaken van de automatische cluster schaal. U kunt geen profiel voor automatische schaal aanpassing instellen per knooppunt groep.
>
> Voor het profiel voor automatisch schalen van cluster is versie *2.11.1* of hoger van de Azure-cli vereist. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>Het profiel van de cluster automatisch schalen instellen op een bestaand AKS-cluster

Gebruik de opdracht [AZ AKS update][az-aks-update-preview] met de para meter *cluster-auto scaleer-profile* voor het instellen van het profiel voor het automatisch schalen van clusters in uw cluster. In het volgende voor beeld wordt de instelling voor het Scan interval ingesteld als 30s in het profiel.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

Wanneer u de automatische clustering-schaal functie inschakelt op knooppunt groepen in het cluster, gebruiken die clusters ook het profiel van het automatisch schalen van het cluster. Bijvoorbeeld:

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
> Wanneer u het profiel voor de automatische schaal van het cluster instelt, wordt het profiel direct met alle bestaande knooppunt groepen met de automatische schaal functie van het cluster ingeschakeld.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>Het profiel van de cluster automatisch schalen instellen bij het maken van een AKS-cluster

U kunt ook de para meter *cluster-automatisch schalen-profiel* gebruiken wanneer u uw cluster maakt. Bijvoorbeeld:

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

Met de bovenstaande opdracht maakt u een AKS-cluster en definieert u het Scan interval van 30 seconden voor het profiel voor de automatische schaal bewerking voor het hele cluster. Met deze opdracht wordt ook de automatische cluster schaalr ingeschakeld op de eerste knooppunt groep, wordt het minimum aantal knoop punten ingesteld op 1 en het maximum aantal knoop punten op 3.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>Het profiel voor automatisch schalen van de cluster opnieuw instellen op de standaard waarden

Gebruik de opdracht [AZ AKS update][az-aks-update-preview] om het profiel voor het automatisch schalen van clusters op uw cluster opnieuw in te stellen.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>De automatische schaal functie van het cluster uitschakelen

Als u de cluster automatisch schalen niet meer wilt gebruiken, kunt u dit uitschakelen met de opdracht [AZ AKS update][az-aks-update-preview] , waarbij u de `--disable-cluster-autoscaler` para meter opgeeft. Knoop punten worden niet verwijderd wanneer de automatische schaal functie van het cluster is uitgeschakeld.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

U kunt het cluster hand matig schalen na het uitschakelen van de automatische cluster schaal met behulp van de opdracht [AZ AKS Scale][az-aks-scale] . Als u de horizontale pod autoscaleer gebruikt, blijft die functie actief met de automatische cluster schaalr, maar kan de omvang van het hele knoop punt uiteindelijk niet worden gepland als alle knooppunt bronnen in gebruik zijn.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Een uitgeschakelde cluster automatisch schalen opnieuw inschakelen

Als u de automatisch schalen van het cluster opnieuw wilt inschakelen op een bestaand cluster, kunt u het opnieuw inschakelen met behulp van de opdracht [AZ AKS update][az-aks-update-preview] en de `--enable-cluster-autoscaler` `--min-count` `--max-count` para meters, en opgeven.

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Logboeken en status van automatische cluster-uitschalen ophalen

Voor het diagnosticeren en opsporen van fouten in de functie voor automatisch schalen, kunnen Logboeken en statussen worden opgehaald uit de invoeg toepassing voor automatisch schalen.

AKS beheert de automatisch schalen van het cluster namens u en voert dit uit in het beheerde besturings vlak. U kunt Control node-knoop punt inschakelen om de logboeken en bewerkingen van de CA te bekijken.

Voer de volgende stappen uit om logboeken te configureren die moeten worden gepusht van de automatische clustering van clusters naar Log Analytics.

1. Stel een regel in voor bron Logboeken om de logboeken van het automatisch schalen van het cluster te Log Analytics. [Instructies worden hier beschreven][aks-view-master-logs], zodat u het selectie vakje inschakelt `cluster-autoscaler` bij het selecteren van opties voor Logboeken.
1. Selecteer de sectie ' logs ' in het cluster via de Azure Portal.
1. Voer de volgende voorbeeld query in op Log Analytics:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

Er worden logboeken weer gegeven die vergelijkbaar zijn met het volgende voor beeld, zolang er logboeken zijn die moeten worden opgehaald.

![Log Analytics logboeken](media/autoscaler/autoscaler-logs.png)

De automatische schaal functie van het cluster kan ook de integriteits status naar een `configmap` benoemde naam schrijven `cluster-autoscaler-status` . Als u deze logboeken wilt ophalen, voert u de volgende `kubectl` opdracht uit. Er wordt een integriteits status gerapporteerd voor elke knooppunt groep die is geconfigureerd met de cluster-automatische schaal functie.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Lees de veelgestelde vragen over het [github-project van Kubernetes/autoscaler][kubernetes-faq]voor meer informatie over wat er wordt vastgelegd van de automatische schaal bewerking.

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>De automatische clustering van clusters gebruiken met meerdere knooppunt Pools ingeschakeld

De cluster-automatische schaal functie kan worden gebruikt in combi natie met [meerdere knooppunt Pools][aks-multiple-node-pools] ingeschakeld. Volg dat document voor meer informatie over het inschakelen van meerdere knooppunt groepen en toevoegen van extra knooppunt groepen aan een bestaand cluster. Wanneer beide functies samen worden gebruikt, schakelt u de automatische schaal functie van het cluster in op elke afzonderlijke knooppunt groep in het cluster en kunt u hiervoor unieke regels voor automatisch schalen door geven.

In de onderstaande opdracht wordt ervan uitgegaan dat u de [eerste instructies](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) eerder in dit document hebt gevolgd en dat u het maximum aantal van een bestaande groep van een knoop punt wilt bijwerken van *3* naar *5*. Gebruik de opdracht [AZ AKS nodepool update][az-aks-nodepool-update] om de instellingen van een bestaande groep knoop punten bij te werken.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

De cluster automatisch schalen kan worden uitgeschakeld met [AZ AKS nodepool update][az-aks-nodepool-update] en de `--disable-cluster-autoscaler` para meter door gegeven.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Als u de automatisch schalen van het cluster opnieuw wilt inschakelen op een bestaand cluster, kunt u het opnieuw inschakelen met behulp van de opdracht [AZ AKS nodepool update][az-aks-nodepool-update] , waarbij u de `--enable-cluster-autoscaler` `--min-count` `--max-count` para meters, en opgeeft.

> [!NOTE]
> Als u van plan bent de automatische schaal functie van het cluster uit te voeren met nodepools die meerdere zones omvatten en gebruikmaken van plannings functies die betrekking hebben op zones zoals de topologische planning van een volume, is het raadzaam één nodepool per zone te hebben en de `--balance-similar-node-groups` via het profiel voor automatisch schalen in te scha kelen. Zo zorgt u ervoor dat de automatische schaal baarheid met succes wordt geschaald en de grootte van de nodepools evenwichtig blijft.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u het aantal AKS-knoop punten automatisch kunt schalen. U kunt ook de horizontale pod autoschaalr gebruiken om automatisch het aantal peulen te verhogen waarmee uw toepassing wordt uitgevoerd. Zie [toepassingen schalen in AKS][aks-scale-apps]voor meer informatie over het gebruik van de pod.

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
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why