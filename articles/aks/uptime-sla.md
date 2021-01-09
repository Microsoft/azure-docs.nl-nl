---
title: Azure Kubernetes service (AKS) met SLA voor uptime
description: Meer informatie over de optionele SLA-aanbieding voor uptime voor de API-server van Azure Kubernetes service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 95728ca14b96adf7396ca13888107477d953b2a9
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033333"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>SLA voor uptime van Azure Kubernetes service (AKS)

SLA voor uptime is een optionele functie voor het inschakelen van een financieel ondersteunde, hogere SLA voor een cluster. SLA voor uptime garandeert 99,95% Beschik baarheid van het Kubernetes API-server eindpunt voor clusters die gebruikmaken van [Beschikbaarheidszones][availability-zones] en 99,9% Beschik baarheid voor clusters die geen Beschikbaarheidszones gebruiken. AKS maakt gebruik van hoofd knooppunt replica's in update-en fout domeinen om te controleren of aan de SLA-vereisten wordt voldaan.

Klanten die een SLA nodig hebben om te voldoen aan de nalevings vereisten of een SLA naar hun eind gebruikers moeten uitbreiden, moeten deze functie inschakelen. Klanten met kritieke werk belastingen die profiteren van een hogere SLA, kunnen ook profiteren van de voor delen. Het gebruik van de SLA-functie voor uptime met Beschikbaarheidszones maakt een hogere Beschik baarheid mogelijk voor de uptime van de Kubernetes-API-server.  

Klanten kunnen nog steeds onbeperkte gratis clusters met een serviceniveau doelstelling (SLO) van 99,5% maken en ervoor kiezen de voorkeurs duur van SLO of SLA als nodig te hebben.

> [!Important]
> Zie limiet voor uitgaand [verkeer](limit-egress-traffic.md) voor het openen van de juiste poorten voor clusters met uitgaand vergren delen.

## <a name="region-availability"></a>Beschikbaarheid in regio’s

* SLA voor uptime is beschikbaar in open bare regio's en Azure Government regio's waar [AKS wordt ondersteund](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).
* SLA voor uptime is beschikbaar voor [particuliere AKS-clusters][private-clusters] in alle open bare REGIO'S waar AKS wordt ondersteund.

## <a name="sla-terms-and-conditions"></a>SLA-voor waarden

SLA voor uptime is een betaalde functie en ingeschakeld per cluster. De prijzen voor de SLA voor uptime worden bepaald door het aantal afzonderlijke clusters en niet op basis van de grootte van de afzonderlijke clusters. U kunt de [prijs informatie](https://azure.microsoft.com/pricing/details/kubernetes-service/) voor de sla voor uptime bekijken voor meer informatie.

## <a name="before-you-begin"></a>Voordat u begint

* De [Azure cli](/cli/azure/install-azure-cli) -versie 2.8.0 of hoger installeren

## <a name="creating-a-new-cluster-with-uptime-sla"></a>Een nieuw cluster maken met SLA voor uptime

> [!NOTE]
> Als u de SLA voor uptime inschakelt, kunt u dit op geen enkele manier verwijderen uit een cluster.

Als u een nieuw cluster wilt maken met de SLA voor de uptime, gebruikt u de Azure CLI.

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```
Gebruik de [`az aks create`][az-aks-create] opdracht voor het maken van een AKS-cluster. In het volgende voorbeeld wordt een cluster met de naam *myAKSCluster* gemaakt met één knooppunt. Het volt ooien van deze bewerking duurt enkele minuten:

```azurecli-interactive
# Create an AKS cluster with uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1
```
Na enkele minuten is de opdracht voltooid en retourneert deze informatie over het cluster in JSON-indeling. Het volgende JSON-fragment toont de betaalde laag voor de SKU, wat aangeeft dat uw cluster is ingeschakeld met een SLA voor uptime:

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="modify-an-existing-cluster-to-use-uptime-sla"></a>Een bestaand cluster wijzigen voor het gebruik van de SLA voor uptime

U kunt eventueel uw bestaande clusters bijwerken om de SLA voor uptime te gebruiken.

Als u een AKS-cluster met de vorige stappen hebt gemaakt, verwijdert u de resource groep:

```azurecli-interactive
# Delete the existing cluster by deleting the resource group 
az group delete --name myResourceGroup --yes --no-wait
```

Maak een nieuwe resource groep:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

Een nieuw cluster maken en geen SLA voor uptime gebruiken:

```azurecli-interactive
# Create a new cluster without uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster--node-count 1
```

Gebruik de [`az aks update`][az-aks-nodepool-update] opdracht om het bestaande cluster bij te werken:

```azurecli-interactive
# Update an existing cluster to use Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --uptime-sla
 ```

 Het volgende JSON-fragment toont de betaalde laag voor de SKU, wat aangeeft dat uw cluster is ingeschakeld met een SLA voor uptime:

 ```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  ```

## <a name="clean-up"></a>Opschonen

Opschonen van de resources die u hebt gemaakt om kosten te voor komen. Als u het cluster wilt verwijderen, gebruikt u de [`az group delete`][az-group-delete] opdracht om de resource groep AKS te verwijderen:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Volgende stappen

Gebruik [Beschikbaarheidszones][availability-zones] om maximale Beschik baarheid te verg Roten met uw AKS-cluster werkbelastingen.

Configureer uw cluster om uitgaand [verkeer te beperken](limit-egress-traffic.md).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?#az-aks-nodepool-update
[az-group-delete]: /cli/azure/group#az-group-delete
[private-clusters]: private-clusters.md
