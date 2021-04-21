---
title: Azure Kubernetes Service (AKS) met SLA voor uptime
description: Meer informatie over de optionele SLA-aanbieding voor uptime voor de Azure Kubernetes Service (AKS) API Server.
services: container-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 846446b4c19c066afe789bf636d68ad37b20709e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779557"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Azure Kubernetes Service (AKS) Uptime SLA

Sla voor uptime is een optionele functie voor het inschakelen van een sla met een hogere sla voor een cluster met een financiële back-up. De SLA voor uptime garandeert een beschikbaarheid van 99,95% van het Kubernetes API-server-eindpunt voor clusters die gebruikmaken van [Beschikbaarheidszones][availability-zones] en 99,9% van de beschikbaarheid voor clusters die geen gebruik maken van Beschikbaarheidszones. AKS maakt gebruik van hoofd-knooppuntreplica's in update- en foutdomeinen om ervoor te zorgen dat aan de SLA-vereisten wordt voldaan.

Klanten die een SLA nodig hebben om te voldoen aan nalevingsvereisten of een SLA willen uitbreiden naar hun eindgebruikers, moeten deze functie inschakelen. Klanten met kritieke workloads die profiteren van een sla met een hogere uptime kunnen er ook van profiteren. Door de SLA-functie Uptime te gebruiken Beschikbaarheidszones een hogere beschikbaarheid voor de uptime van de Kubernetes-API-server.  

Klanten kunnen nog steeds onbeperkte gratis clusters maken met een serviceniveaudoelstelling (SLO) van 99,5% en indien nodig kiezen voor de voorkeurs-SLO of SLA Uptime.

> [!Important]
> Zie Voor clusters met egress lockdown, [beperk het verkeer](limit-egress-traffic.md) om de juiste poorten te openen.

## <a name="region-availability"></a>Beschikbaarheid in regio’s

* Sla voor uptime is beschikbaar in openbare regio's en Azure Government regio's waar [AKS wordt ondersteund.](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)
* Sla voor uptime is beschikbaar voor [persoonlijke AKS-clusters][private-clusters] in alle openbare regio's waar AKS wordt ondersteund.

## <a name="sla-terms-and-conditions"></a>SLA-voorwaarden

Sla voor uptime is een betaalde functie die per cluster is ingeschakeld. De prijzen van de SLA voor uptime worden bepaald door het aantal afzonderlijke clusters en niet door de grootte van de afzonderlijke clusters. U kunt de [prijsgegevens van de SLA voor uptime bekijken](https://azure.microsoft.com/pricing/details/kubernetes-service/) voor meer informatie.

## <a name="before-you-begin"></a>Voordat u begint

* Azure [CLI versie](/cli/azure/install-azure-cli) 2.8.0 of hoger installeren

## <a name="creating-a-new-cluster-with-uptime-sla"></a>Een nieuw cluster maken met een SLA voor uptime

Als u een nieuw cluster wilt maken met de SLA voor uptime, gebruikt u de Azure CLI.

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```
Gebruik de [`az aks create`][az-aks-create] opdracht om een AKS-cluster te maken. In het volgende voorbeeld wordt een cluster met de naam *myAKSCluster* gemaakt met één knooppunt. Het uitvoeren van deze bewerking duurt enkele minuten:

```azurecli-interactive
# Create an AKS cluster with uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1
```
Na enkele minuten is de opdracht voltooid en retourneert deze informatie over het cluster in JSON-indeling. Het volgende JSON-fragment toont de betaalde laag voor de SKU, waarmee wordt aangegeven dat uw cluster is ingeschakeld met sla voor uptime:

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="modify-an-existing-cluster-to-use-uptime-sla"></a>Een bestaand cluster wijzigen voor het gebruik van de SLA voor uptime

U kunt eventueel uw bestaande clusters bijwerken om de SLA voor uptime te gebruiken.

Als u in de vorige stappen een AKS-cluster hebt gemaakt, verwijdert u de resourcegroep:

```azurecli-interactive
# Delete the existing cluster by deleting the resource group 
az group delete --name myResourceGroup --yes --no-wait
```

Maak een nieuwe resourcegroep:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

Maak een nieuw cluster en gebruik geen SLA voor uptime:

```azurecli-interactive
# Create a new cluster without uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster--node-count 1
```

Gebruik de [`az aks update`][az-aks-update] opdracht om het bestaande cluster bij te werken:

```azurecli-interactive
# Update an existing cluster to use Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --uptime-sla
 ```

 Het volgende JSON-fragment toont de betaalde laag voor de SKU, waarmee wordt aangegeven dat uw cluster is ingeschakeld met sla voor uptime:

 ```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  ```

## <a name="opt-out-of-uptime-sla"></a>Sla voor opt-out van uptime

U kunt uw cluster bijwerken om over te gaan naar de gratis laag en u af te zien van de SLA voor uptime.

```azurecli-interactive
# Update an existing cluster to opt out of Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --no-uptime-sla
 ```

## <a name="clean-up"></a>Opschonen

Om kosten te voorkomen, schoont u alle resources op die u hebt gemaakt. Als u het cluster wilt verwijderen, gebruikt u [`az group delete`][az-group-delete] de opdracht om de AKS-resourcegroep te verwijderen:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Volgende stappen

Gebruik [Beschikbaarheidszones][availability-zones] om hoge beschikbaarheid te verhogen met uw AKS-clusterworkloads.

Configureer uw cluster om [het toegangsverkeer te beperken.](limit-egress-traffic.md)

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?#az_aks_create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-group-delete]: /cli/azure/group#az_group_delete
[private-clusters]: private-clusters.md
