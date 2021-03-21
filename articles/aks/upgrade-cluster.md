---
title: Een AKS-cluster (Azure Kubernetes Service) upgraden
description: Meer informatie over hoe u een AKS-cluster (Azure Kubernetes service) bijwerkt om de nieuwste functies en beveiligings updates te downloaden.
services: container-service
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: 11218fc0cd754e9793067c449fdcb7589688dc2e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176345"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Een AKS-cluster (Azure Kubernetes Service) upgraden

Voor een deel van de levens cyclus van het AKS-cluster moeten periodieke upgrades worden uitgevoerd naar de nieuwste versie van Kubernetes. Het is belang rijk dat u de nieuwste beveiligings releases toepast of een upgrade uitvoert om de nieuwste functies op te halen. In dit artikel wordt beschreven hoe u de hoofd onderdelen of een enkele standaard knooppunt groep in een AKS-cluster bijwerkt.

Zie [een knooppunt groep bijwerken in AKS][nodepool-upgrade]voor AKS-clusters die gebruikmaken van meerdere knooppunt groepen of Windows Server-knoop punten.

## <a name="before-you-begin"></a>Voordat u begint

Voor dit artikel moet u de Azure CLI-versie 2.0.65 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

> [!WARNING]
> Een AKS-cluster upgrade activeert een cordon en afvoer van uw knoop punten. Als er een beperkt reken quotum beschikbaar is, kan de upgrade mislukken. Zie [Quota verhogen](../azure-portal/supportability/resource-manager-core-quotas-request.md) voor meer informatie

## <a name="check-for-available-aks-cluster-upgrades"></a>Controleren op beschik bare AKS-cluster upgrades

Als u wilt controleren welke Kubernetes-releases beschikbaar zijn voor uw cluster, gebruikt u de opdracht [AZ AKS Get-upgrades][az-aks-get-upgrades] . In het volgende voor beeld wordt gecontroleerd op beschik bare upgrades voor *myAKSCluster* in *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Wanneer u een ondersteund AKS-cluster bijwerkt, kunnen secundaire versies van Kubernetes niet worden overgeslagen. Alle upgrades moeten opeenvolgend worden uitgevoerd op basis van het primaire versie nummer. Bijvoorbeeld: upgrades tussen *1.14. x*  ->  *1.15. x* of *1.15. x*  ->  *1.16. x* zijn toegestaan, maar *1.14. x*  ->  *1.16. x* is niet toegestaan. 
> > Meerdere versies overs Laan kan alleen worden uitgevoerd bij een upgrade van een _niet-ondersteunde versie_ naar een _ondersteunde versie_. Bijvoorbeeld een upgrade van een niet-ondersteund *1,10. x* --> een ondersteunde *1.15. x* kan worden voltooid.

In de volgende voorbeeld uitvoer ziet u dat het cluster kan worden bijgewerkt naar versies *1.19.1* en *1.19.3*:

```console
Name     ResourceGroup    MasterVersion    Upgrades
-------  ---------------  ---------------  --------------
default  myResourceGroup  1.18.10          1.19.1, 1.19.3
```

Als er geen upgrade beschikbaar is, wordt het volgende bericht weer gegeven:

```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade"></a>Upgrade van overspanning van knoop punt aanpassen

> [!Important]
> Voor het overschrijden van knoop punten is abonnements quota vereist voor het aangevraagde maximum piek aantal voor elke upgrade bewerking. Een cluster met vijf knooppunt groepen, elk met een aantal van vier knoop punten, heeft bijvoorbeeld een totaal van 20 knoop punten. Als elke knooppunt groep een maximale piek waarde van 50% heeft, is extra reken-en IP-quotum van 10 knoop punten (2 knoop punten * 5 groepen) vereist om de upgrade te volt ooien.
>
> Als u Azure CNI gebruikt, controleert u of er in het subnet beschik bare Ip's zijn en voldoen aan de [IP-vereisten van Azure cni](configure-azure-cni.md).

AKS configureert standaard upgrades om met één extra knoop punt te overspanning. Met een standaard waarde van 1 voor de maximale piek instellingen kan AKS de onderbreking van de werk belasting tot een minimum beperken door een extra knoop punt te maken vóór de Cordon/afvoer van bestaande toepassingen om een ouder versie knooppunt te vervangen. De maximale piek waarde kan worden aangepast per knooppunt groep om een afweging tussen upgrade snelheid en upgrade onderbreking mogelijk te maken. Door de maximale piek waarde te verhogen, wordt het upgrade proces sneller uitgevoerd, maar het instellen van een grote waarde voor maximale piek spanning kan onderbrekingen veroorzaken tijdens het upgrade proces. 

Een maximale piek waarde van 100% levert bijvoorbeeld het snelst mogelijke upgrade proces (dubbel het aantal knoop punten), maar zorgt er ook voor dat alle knoop punt in de knooppunt groep gelijktijdig wordt leeg gesteld. U kunt een hogere waarde gebruiken, bijvoorbeeld voor het testen van omgevingen. Voor productie knooppunt groepen wordt een max_surge instelling van 33% aanbevolen.

AKS accepteert zowel gehele waarden als een percentage voor de maximale piek waarde. Een geheel getal, zoals ' 5 ', geeft aan dat er vijf extra knoop punten pieken. Een waarde van "50%" duidt op een piek waarde van de helft van het huidige aantal knoop punten in de pool. Het maximum aantal piek waarden kan mini maal 1% en Maxi maal 100% zijn. Een percentage waarde wordt naar boven afgerond op het dichtstbijzijnde aantal knoop punten. Als de maximale piek waarde lager is dan het huidige aantal knoop punten op het moment van de upgrade, wordt het huidige aantal knoop punten gebruikt voor de maximale piek waarde.

Tijdens een upgrade kan de maximale piek waarde 1 en een maximum waarde gelijk zijn aan het aantal knoop punten in de knooppunt groep. U kunt grotere waarden instellen, maar het maximum aantal knoop punten dat voor de maximale piek wordt gebruikt, is niet groter dan het aantal knoop punten in de pool op het moment van de upgrade.

> [!Important]
> De maximale piek instelling voor een knooppunt groep is permanent.  Bij volgende Kubernetes-upgrades of knooppunt versie-upgrades wordt deze instelling gebruikt. U kunt de maximale piek waarde voor uw knooppunt groepen op elk gewenst moment wijzigen. Voor productie knooppunt groepen wordt een maximale piek waarde van 33% aangeraden.

Gebruik de volgende opdrachten om maximale piek waarden in te stellen voor nieuwe of bestaande knooppunt groepen.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>Een AKS-cluster upgraden

Met een lijst met beschik bare versies voor uw AKS-cluster gebruikt u de opdracht [AZ AKS upgrade][az-aks-upgrade] . Tijdens het upgrade proces zal AKS: 
- Voeg een nieuw buffer knooppunt (of zoveel knoop punten toe als geconfigureerd in [maximale piek](#customize-node-surge-upgrade)) toe aan het cluster waarop de opgegeven Kubernetes-versie wordt uitgevoerd. 
- [Cordon en afvoer][kubernetes-drain] een van de oude knoop punten om de onderbreking van het uitvoeren van toepassingen tot een minimum te beperken (als u de maximale piek gebruikt, wordt de [Cordon en het water afvoer][kubernetes-drain] net zo veel knoop punten als het aantal opgegeven buffer knooppunten). 
- Wanneer het oude knoop punt volledig is ontslagen, wordt de installatie kopie van de nieuwe versie geschaald en wordt het knoop punt de buffer voor het volgende knoop punt wordt bijgewerkt. 
- Dit proces wordt herhaald totdat alle knoop punten in het cluster zijn bijgewerkt. 
- Aan het einde van het proces wordt het laatste buffer knooppunt verwijderd, waarbij het bestaande aantal agent knooppunten en de zone balans behouden blijft.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

Het duurt enkele minuten om het cluster bij te werken, afhankelijk van het aantal knoop punten dat u hebt.

> [!IMPORTANT]
> Zorg ervoor dat elke `PodDisruptionBudgets` (PDBs) ten minste één pod replica mag worden verplaatst per keer, anders mislukt de bewerking voor afvoer/verwijdering.
> Als de afvoer bewerking mislukt, zal de upgrade bewerking mislukken door het ontwerp om te controleren of de toepassingen niet worden onderbroken. Corrigeer wat heeft geleid tot het stoppen van de bewerking (onjuiste PDBs, gebrek aan quota, enzovoort) en voer de bewerking opnieuw uit.

Als u wilt controleren of de upgrade is voltooid, gebruikt u de opdracht [AZ AKS show][az-aks-show] :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

In de volgende voorbeeld uitvoer ziet u dat het cluster nu *1.18.10* uitvoert:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.18.10              Succeeded            myakscluster-dns-379cbbb9.hcp.eastus.azmk8s.io
```

## <a name="set-auto-upgrade-channel"></a>Het kanaal voor automatische upgrades instellen

Naast het hand matig bijwerken van een cluster, kunt u een kanaal voor automatische upgrades instellen in uw cluster. De volgende upgrade kanalen zijn beschikbaar:

|Kanaal| Bewerking | Voorbeeld
|---|---|---|
| `none`| Hiermee schakelt u automatische upgrades uit en houdt u het cluster op de huidige versie van Kubernetes| Standaard instelling indien niet gewijzigd|
| `patch`| het cluster automatisch upgraden naar de meest recente ondersteunde patch versie wanneer deze beschikbaar wordt wanneer de secundaire versie hetzelfde blijft.| Als er bijvoorbeeld een cluster is waarop versie *1.17.7* en versies *1.17.9*, *1.18.4*, *1.18.6* en *1.19.1* beschikbaar zijn, wordt uw cluster bijgewerkt naar *1.17.9*|
| `stable`| het cluster automatisch upgraden naar de meest recente ondersteunde patch release op de secundaire versie *n-1*, waarbij *N* de meest recente ondersteunde secundaire versie is.| Als er bijvoorbeeld een cluster is waarop versie *1.17.7* en versies *1.17.9*, *1.18.4*, *1.18.6* en *1.19.1* beschikbaar zijn, wordt uw cluster bijgewerkt naar *1.18.6*.
| `rapid`| het cluster automatisch upgraden naar de meest recente ondersteunde patch release voor de meest recente ondersteunde secundaire versie.| Als het cluster zich in een versie van Kubernetes bevindt die een *n-2-* secundaire versie is, waarbij *n* de meest recente ondersteunde secundaire versie is, wordt het cluster eerst bijgewerkt naar de meest recente ondersteunde patch versie op een secundaire versie van *N-1* . Als op een cluster bijvoorbeeld versie *1.17.7* en versies *1.17.9*, *1.18.4*, *1.18.6* en *1.19.1* beschikbaar zijn, wordt het cluster eerst geüpgraded naar *1.18.6*, waarna het wordt bijgewerkt naar *1.19.1*.

> [!NOTE]
> Automatische upgrade van het cluster werkt alleen bij aan de GA-versies van Kubernetes en wordt niet bijgewerkt naar Preview-versies.

Het automatisch upgraden van een cluster volgt hetzelfde proces als het hand matig upgraden van een cluster. Zie [een AKS-cluster upgraden][upgrade-cluster]voor meer informatie.

De automatische cluster upgrade voor AKS-clusters is een preview-functie.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Registreer de `AutoUpgradePreview` functie vlag met behulp van de opdracht [AZ feature REGI ster][az-feature-register] , zoals wordt weer gegeven in het volgende voor beeld:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n AutoUpgradePreview
```

Het duurt enkele minuten voordat de status is *geregistreerd*. Controleer de registratie status met behulp van de opdracht [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AutoUpgradePreview')].{Name:name,State:properties.state}"
```

Als u klaar bent, vernieuwt u de registratie van de resource provider *micro soft. container service* met de opdracht [AZ provider REGI ster][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Als u het kanaal voor automatische upgrades wilt instellen bij het maken van een cluster, gebruikt u de para meter *automatisch bijwerken-kanaal* , zoals in het volgende voor beeld.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable --generate-ssh-keys
```

Als u het kanaal voor automatische upgrades op een bestaand cluster wilt instellen, werkt u de para meter *automatisch bijwerken-kanaal* bij, zoals in het volgende voor beeld.

```azurecli-interactive
az aks update --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u een bestaand AKS-cluster bijwerkt. Zie de set zelf studies voor meer informatie over het implementeren en beheren van AKS-clusters.

> [!div class="nextstepaction"]
> [Zelf studies voor AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-provider-register]: /cli/azure/provider#az-provider-register
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[upgrade-cluster]:  #upgrade-an-aks-cluster
