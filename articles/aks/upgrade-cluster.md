---
title: Een AKS-cluster (Azure Kubernetes Service) upgraden
description: Meer informatie over het upgraden van Azure Kubernetes Service AKS-cluster (AKS) voor de nieuwste functies en beveiligingsupdates.
services: container-service
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: d6a5ed468541090d433dba732707a59841e6ff41
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779611"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Een AKS-cluster (Azure Kubernetes Service) upgraden

Een deel van de levenscyclus van het AKS-cluster omvat het uitvoeren van periodieke upgrades naar de nieuwste Kubernetes-versie. Het is belangrijk dat u de nieuwste beveiligingsreleases toe passen of upgradet om de nieuwste functies te krijgen. In dit artikel wordt beschreven hoe u de hoofdonderdelen of één standaardknooppuntgroep in een AKS-cluster bij kunt upgraden.

Zie Een knooppuntgroep upgraden in AKS voor AKS-clusters die gebruikmaken van meerdere knooppuntgroepen of Windows [Server-knooppunten.][nodepool-upgrade]

## <a name="before-you-begin"></a>Voordat u begint

Voor dit artikel moet u Azure CLI versie 2.0.65 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

> [!WARNING]
> Een AKS-clusterupgrade activeert een cordon en drain van uw knooppunten. Als er een laag rekenquotum beschikbaar is, kan de upgrade mislukken. Zie Quota verhogen voor [meer informatie](../azure-portal/supportability/resource-manager-core-quotas-request.md)

## <a name="check-for-available-aks-cluster-upgrades"></a>Controleren op beschikbare AKS-clusterupgrades

Als u wilt controleren welke Kubernetes-releases beschikbaar zijn voor uw cluster, gebruikt u de [opdracht az aks get-upgrades.][az-aks-get-upgrades] In het volgende voorbeeld wordt gecontroleerd op beschikbare upgrades naar *myAKSCluster* in *myResourceGroup:*

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Wanneer u een ondersteund AKS-cluster upgradet, kunnen secundaire Kubernetes-versies niet worden overgeslagen. Alle upgrades moeten opeenvolgend worden uitgevoerd op hoofdversienummer. Upgrades tussen *1.14.x*  ->  *1.15.x* of *1.15.x*  ->  *1.16.x* zijn bijvoorbeeld toegestaan, maar *1.14.x*  ->  *1.16.x* is niet toegestaan. 
> > Het overslaan van meerdere versies kan alleen  worden uitgevoerd wanneer u een upgrade van een niet-ondersteunde versie naar een _ondersteunde versie hebt uitgevoerd._ Een upgrade van een niet-ondersteunde *1.10.x* --> een *ondersteunde 1.15.x* kan bijvoorbeeld worden voltooid.

In de volgende voorbeelduitvoer ziet u dat het cluster kan worden bijgewerkt naar versie *1.19.1* en *1.19.3:*

```console
Name     ResourceGroup    MasterVersion    Upgrades
-------  ---------------  ---------------  --------------
default  myResourceGroup  1.18.10          1.19.1, 1.19.3
```

Als er geen upgrade beschikbaar is, wordt het volgende bericht weergegeven:

```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade"></a>Upgrade van pieken van knooppunt aanpassen

> [!Important]
> Voor knooppuntpieken is een abonnementsquotum vereist voor het aangevraagde maximum aantal pieken voor elke upgradebewerking. Zo heeft een cluster met 5 knooppuntgroepen, elk met een telling van 4 knooppunten, in totaal 20 knooppunten. Als elke knooppuntgroep een maximale piekwaarde van 50% heeft, zijn extra reken- en IP-quota van 10 knooppunten (2 knooppunten * 5 pools) vereist om de upgrade te voltooien.
>
> Als u Azure CNI, controleert u of er ip-adressen beschikbaar zijn in het subnet om te voldoen aan [de IP-vereisten van Azure CNI](configure-azure-cni.md).

Standaard configureert AKS upgrades om pieken te maken met één extra knooppunt. Met een standaardwaarde van één voor de maximale piekinstellingen kan AKS workloadonderbrekingen minimaliseren door een extra knooppunt te maken vóór de cordon/drain van bestaande toepassingen ter vervanging van een ouder knooppunt met versie. De maximale piekwaarde kan per knooppuntgroep worden aangepast om een afweging te maken tussen upgradesnelheid en upgradeonderbreking. Door de maximale piekwaarde te verhogen, wordt het upgradeproces sneller voltooid, maar het instellen van een grote waarde voor de maximale piek kan leiden tot onderbrekingen tijdens het upgradeproces. 

Een maximale piekwaarde van 100% biedt bijvoorbeeld het snelst mogelijke upgradeproces (het aantal knooppunten verdubbeld), maar zorgt er ook voor dat alle knooppunten in de knooppuntgroep tegelijkertijd worden leeggestroomd. Mogelijk wilt u een hogere waarde zoals deze gebruiken voor testomgevingen. Voor productie-knooppuntgroepen wordt een max_surge instelling van 33% aanbevolen.

AKS accepteert zowel gehele getallen als een percentagewaarde voor de maximale piek. Een geheel getal, zoals '5', geeft aan dat er vijf extra knooppunten moeten pieken. Een waarde van '50%' geeft een piekwaarde aan van de helft van het huidige aantal knooppunt in de pool. Het maximum piek percentage kan minimaal 1% en maximaal 100% zijn. Een procentwaarde wordt naar boven afgerond op het dichtstbijzijnde aantal knooppunt. Als de maximale piekwaarde lager is dan het huidige aantal knooppunt op het moment van de upgrade, wordt het huidige aantal knooppunt gebruikt voor de maximale piekwaarde.

Tijdens een upgrade kan de maximale piekwaarde minimaal 1 zijn en een maximumwaarde die gelijk is aan het aantal knooppunten in uw knooppuntgroep. U kunt grotere waarden instellen, maar het maximum aantal knooppunten dat wordt gebruikt voor de maximale piek is niet hoger dan het aantal knooppunten in de pool op het moment van de upgrade.

> [!Important]
> De maximale piekinstelling voor een knooppuntgroep is permanent.  Volgende Kubernetes-upgrades of knooppuntversie-upgrades maken gebruik van deze instelling. U kunt de maximale piekwaarde voor uw knooppuntgroepen op elk moment wijzigen. Voor productie-knooppuntgroepen wordt een maximale piekinstelling van 33% aanbevolen.

Gebruik de volgende opdrachten om maximale piekwaarden in te stellen voor nieuwe of bestaande knooppuntgroepen.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>Een AKS-cluster upgraden

Gebruik de opdracht [az aks upgrade][az-aks-upgrade] om een upgrade uit te voeren met een lijst met beschikbare versies voor uw AKS-cluster. Tijdens het upgradeproces zal AKS het volgende doen: 
- voeg een nieuw bufferknooppunt (of net zoveel knooppunten als geconfigureerd in [de maximale](#customize-node-surge-upgrade)piek) toe aan het cluster met de opgegeven Kubernetes-versie. 
- [een][kubernetes-drain] van de oude knooppunten aan- en verwijderen om onderbreking van lopende [][kubernetes-drain] toepassingen te minimaliseren (als u een maximale piek gebruikt, worden er net zoveel knooppunten aan- en verwijderd op hetzelfde moment als het aantal bufferknooppunten dat is opgegeven). 
- Wanneer het oude knooppunt volledig leeg is, wordt er een nieuwe versie van gemaakt en wordt het het buffer-knooppunt voor het volgende knooppunt dat moet worden bijgewerkt. 
- Dit proces wordt herhaald totdat alle knooppunten in het cluster zijn bijgewerkt. 
- Aan het einde van het proces wordt het laatste buffer-knooppunt verwijderd, met behoud van het bestaande aantal agent-knooppunt en de zone balance.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

Het duurt enkele minuten om het cluster bij te upgraden, afhankelijk van het aantal knooppunten dat u hebt.

> [!IMPORTANT]
> Zorg ervoor dat een (PDB) toestaat dat ten minste 1 podreplica tegelijk wordt verplaatst, anders mislukt de bewerking voor het `PodDisruptionBudgets` leeglaten/uitputten.
> Als de bewerking voor het leeglaten mislukt, mislukt de upgradebewerking zonder ontwerp om ervoor te zorgen dat de toepassingen niet worden onderbroken. Corrigeert wat de bewerking heeft veroorzaakt (onjuiste PDF's, onvoldoende quotum, bijvoorbeeld ) en probeer de bewerking opnieuw.

Gebruik de opdracht [az aks show][az-aks-show] om te controleren of de upgrade is geslaagd:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

In de volgende voorbeelduitvoer ziet u dat het cluster *nu 1.18.10 wordt uitgevoerd:*

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.18.10              Succeeded            myakscluster-dns-379cbbb9.hcp.eastus.azmk8s.io
```

## <a name="set-auto-upgrade-channel"></a>Kanaal voor automatisch upgraden instellen

Naast het handmatig bijwerken van een cluster, kunt u een kanaal voor automatische upgrade instellen op uw cluster. De volgende upgradekanalen zijn beschikbaar:

|Kanaal| Bewerking | Voorbeeld
|---|---|---|
| `none`| schakelt automatische upgrades uit en behoudt de huidige versie van Kubernetes van het cluster| Standaardinstelling als deze ongewijzigd blijft|
| `patch`| upgrade het cluster automatisch naar de nieuwste ondersteunde patchversie wanneer deze beschikbaar komt terwijl de secundaire versie hetzelfde blijft.| Als op een cluster bijvoorbeeld versie *1.17.7* en versies *1.17.9,* *1.18.4,* *1.18.6* en *1.19.1* beschikbaar zijn, wordt uw cluster bijgewerkt naar *1.17.9*|
| `stable`| upgrade het cluster automatisch naar de meest recente ondersteunde patchre release op secundaire versie *N-1,* waarbij *N* de meest recente ondersteunde secundaire versie is.| Als op een cluster bijvoorbeeld versie *1.17.7* en versies *1.17.9,* *1.18.4,* *1.18.6* en *1.19.1* beschikbaar zijn, wordt uw cluster bijgewerkt naar *1.18.6.*
| `rapid`| het cluster automatisch upgraden naar de nieuwste ondersteunde patchre release op de meest recente ondersteunde secundaire versie.| In gevallen waarin het cluster een versie van Kubernetes heeft met *een secundaire N-2-versie* waarbij *N* de meest recente ondersteunde secundaire versie is, wordt het cluster eerst geupgraded naar de nieuwste ondersteunde patchversie op een secundaire versie van *N-1.* Als op een cluster bijvoorbeeld versie *1.17.7* en versies *1.17.9,* *1.18.4,* *1.18.6* en *1.19.1* beschikbaar zijn, wordt uw cluster eerst bijgewerkt naar *1.18.6* en vervolgens bijgewerkt naar *1.19.1.*

> [!NOTE]
> Updates voor de automatische upgrade van clusters naar GA-versies van Kubernetes worden niet bijgewerkt naar preview-versies.

Het automatisch upgraden van een cluster volgt hetzelfde proces als het handmatig bijwerken van een cluster. Zie Een [AKS-cluster upgraden voor meer informatie.][upgrade-cluster]

De automatische upgrade van het cluster voor AKS-clusters is een preview-functie.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Registreer de `AutoUpgradePreview` functievlag met behulp van [de opdracht az feature register,][az-feature-register] zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n AutoUpgradePreview
```

Het duurt enkele minuten voordat de status Geregistreerd *we weergeven.* Controleer de registratiestatus met behulp van [de opdracht az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AutoUpgradePreview')].{Name:name,State:properties.state}"
```

Wanneer u klaar bent, vernieuwt u de registratie van de resourceprovider *Microsoft.ContainerService* met behulp van [de opdracht az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Als u het kanaal voor automatische upgrade wilt instellen bij het maken van een cluster, gebruikt u de parameter *auto-upgrade-channel,* vergelijkbaar met het volgende voorbeeld.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable --generate-ssh-keys
```

Als u het kanaal voor automatische upgrade wilt instellen op een bestaand cluster, moet u de parameter *auto-upgrade-channel* bijwerken, vergelijkbaar met het volgende voorbeeld.

```azurecli-interactive
az aks update --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u kunnen lezen hoe u een bestaand AKS-cluster kunt upgraden. Zie de reeks zelfstudies voor meer informatie over het implementeren en beheren van AKS-clusters.

> [!div class="nextstepaction"]
> [AKS-zelfstudies][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-provider-register]: /cli/azure/provider#az_provider_register
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[upgrade-cluster]:  #upgrade-an-aks-cluster
