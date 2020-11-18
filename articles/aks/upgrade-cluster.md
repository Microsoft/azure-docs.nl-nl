---
title: Een AKS-cluster (Azure Kubernetes Service) upgraden
description: Meer informatie over hoe u een AKS-cluster (Azure Kubernetes service) bijwerkt om de nieuwste functies en beveiligings updates te downloaden.
services: container-service
ms.topic: article
ms.date: 11/17/2020
ms.openlocfilehash: 262905c9f840850795ba9555912e81eca61369d1
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94683230"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Een AKS-cluster (Azure Kubernetes Service) upgraden

Als onderdeel van de levens cyclus van een AKS-cluster moet u vaak een upgrade uitvoeren naar de nieuwste versie van Kubernetes. Het is belang rijk dat u de meest recente Kubernetes-beveiligings releases toepast of een upgrade uitvoert om de nieuwste functies op te halen. In dit artikel wordt beschreven hoe u de hoofd onderdelen of een enkele standaard knooppunt groep in een AKS-cluster bijwerkt.

Zie [een knooppunt groep bijwerken in AKS][nodepool-upgrade]voor AKS-clusters die gebruikmaken van meerdere knooppunt groepen of Windows Server-knoop punten.

## <a name="before-you-begin"></a>Voordat u begint

Voor dit artikel moet u de Azure CLI-versie 2.0.65 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

> [!WARNING]
> Een AKS-cluster upgrade activeert een cordon en afvoer van uw knoop punten. Als er een beperkt reken quotum beschikbaar is, kan de upgrade mislukken. Zie [quota verg Roten](../azure-portal/supportability/resource-manager-core-quotas-request.md) voor meer informatie.

## <a name="check-for-available-aks-cluster-upgrades"></a>Controleren op beschik bare AKS-cluster upgrades

Als u wilt controleren welke Kubernetes-releases beschikbaar zijn voor uw cluster, gebruikt u de opdracht [AZ AKS Get-upgrades][az-aks-get-upgrades] . In het volgende voor beeld wordt gecontroleerd op beschik bare upgrades voor het cluster met de naam *myAKSCluster* in de resource groep met de naam *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Wanneer u een ondersteund AKS-cluster bijwerkt, kunnen secundaire versies van Kubernetes niet worden overgeslagen. Bijvoorbeeld: upgrades tussen *1.12. x*  ->  *1.13. x* of *1.13. x*  ->  *1.14. x* zijn toegestaan, maar *1.12. x*  ->  *1.14. x* is niet.
>
> Als u een upgrade wilt uitvoeren, van *1.12. x*  ->  *1.14. x*, moet u eerst een upgrade uitvoeren van *1.12. x*  ->  *1.13. x* en vervolgens een upgrade uitvoeren van *1.13. x*  ->  *1.14. x*.
>
> Het overs laan van meerdere versies kan alleen worden uitgevoerd wanneer u een upgrade uitvoert van een niet-ondersteunde versie terug naar een ondersteunde versie. U kunt bijvoorbeeld een upgrade uitvoeren van een niet-ondersteund *1,10. x* --> een ondersteunde *1.15. x* kan worden voltooid.

In de volgende voorbeeld uitvoer ziet u dat het cluster kan worden bijgewerkt naar versies *1.13.9* en *1.13.10*:

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Als er geen upgrade beschikbaar is, krijgt u het volgende:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade"></a>Upgrade van overspanning van knoop punt aanpassen

> [!Important]
> Voor het overschrijden van knoop punten is abonnements quota vereist voor het aangevraagde maximum piek aantal voor elke upgrade bewerking. Een cluster met vijf knooppunt groepen, elk met een aantal van vier knoop punten, heeft bijvoorbeeld een totaal van 20 knoop punten. Als elke knooppunt groep een maximale piek waarde van 50% heeft, is extra reken-en IP-quotum van 10 knoop punten (2 knoop punten * 5 groepen) vereist om de upgrade te volt ooien.
>
> Als u Azure CNI gebruikt, controleert u of er in het subnet beschik bare Ip's zijn en voldoen aan de [IP-vereisten van Azure cni](configure-azure-cni.md).

AKS configureert standaard upgrades om met één extra knoop punt te overspanning. Met een standaard waarde van één voor de instelling Maximum pieken kan AKS de onderbreking van de werk belasting tot een minimum beperken door een extra knoop punt te maken vóór de Cordon/afvoer van bestaande toepassingen om een ouder versie knooppunt te vervangen. De maximale piek waarde kan worden aangepast per knooppunt groep om een afweging tussen upgrade snelheid en upgrade onderbreking mogelijk te maken. Door de maximale piek waarde te verhogen, wordt het upgrade proces sneller uitgevoerd, maar het instellen van een grote waarde voor maximale piek spanning kan onderbrekingen veroorzaken tijdens het upgrade proces. 

Een maximale piek waarde van 100% levert bijvoorbeeld het snelst mogelijke upgrade proces (dubbel het aantal knoop punten), maar zorgt er ook voor dat alle knoop punt in de knooppunt groep gelijktijdig wordt leeg gesteld. U kunt een hogere waarde gebruiken, bijvoorbeeld voor het testen van omgevingen. Voor productie knooppunt groepen wordt een max_surge instelling van 33% aanbevolen.

AKS accepteert zowel gehele waarden als een percentage voor de maximale piek waarde. Een geheel getal, zoals ' 5 ', geeft aan dat er vijf extra knoop punten pieken. Een waarde van "50%" duidt op een piek waarde van de helft van het huidige aantal knoop punten in de pool. Het maximum aantal piek waarden kan mini maal 1% en Maxi maal 100% zijn. Een percentage waarde wordt naar boven afgerond op het dichtstbijzijnde aantal knoop punten. Als de maximale piek waarde lager is dan het huidige aantal knoop punten op het moment van de upgrade, wordt het huidige aantal knoop punten gebruikt voor de maximale piek waarde.

Tijdens een upgrade kan de maximale piek waarde 1 en een maximum waarde gelijk zijn aan het aantal knoop punten in de knooppunt groep. U kunt grotere waarden instellen, maar het maximum aantal knoop punten dat voor de maximale piek wordt gebruikt, is niet groter dan het aantal knoop punten in de pool op het moment van de upgrade.

Tot CLI-versie 2.16.0 + hebt u de CLI *-extensie AKS-preview* nodig voor het gebruik van maximale piek spanning. Gebruik de opdracht [AZ extension add][az-extension-add] en controleer vervolgens of er beschik bare updates zijn met behulp van de opdracht [AZ extension update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

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

Met een lijst met beschik bare versies voor uw AKS-cluster gebruikt u de opdracht [AZ AKS upgrade][az-aks-upgrade] . Tijdens het upgrade proces voegt AKS een nieuw buffer knooppunt toe (of net als veel knoop punten die zijn geconfigureerd voor [maximale piek spanning](#customize-node-surge-upgrade)) aan het cluster waarop de opgegeven Kubernetes-versie wordt uitgevoerd. Vervolgens wordt een van de oude knoop punten [Cordon en][kubernetes-drain] verwerkt om de onderbreking van het uitvoeren van toepassingen tot een minimum te beperken (als u de maximale piek gebruikt, wordt de [Cordon en][kubernetes-drain] het verwerkings Stop net zo veel knoop punten als het aantal opgegeven buffer knooppunten). Wanneer het oude knoop punt volledig is ontslagen, wordt de installatie kopie van de nieuwe versie geschaald en wordt het knoop punt de buffer voor het volgende knoop punt wordt bijgewerkt. Dit proces wordt herhaald totdat alle knoop punten in het cluster zijn bijgewerkt. Aan het einde van het proces wordt het laatste geleegde knoop punt verwijderd, waarbij het bestaande aantal agent knooppunten behouden blijft.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

Het duurt enkele minuten om het cluster bij te werken, afhankelijk van het aantal knoop punten dat u hebt.

> [!NOTE]
> Er is een totale toegestane tijd voor het volt ooien van een cluster upgrade. Deze tijd wordt berekend door het product van te nemen `10 minutes * total number of nodes in the cluster` . Voor beeld: in een cluster met 20 knoop punten moeten upgrade bewerkingen in 200 minuten slagen of AKS de bewerking mislukken om een onherstelbare cluster status te voor komen. Als u het probleem wilt herstellen tijdens de upgrade, voert u de upgrade bewerking opnieuw uit nadat de time-out is bereikt.

Als u wilt controleren of de upgrade is voltooid, gebruikt u de opdracht [AZ AKS show][az-aks-show] :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

In de volgende voorbeeld uitvoer ziet u dat het cluster nu *1.13.10* uitvoert:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
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
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
