---
title: Toegang beperken met behulp van een service-eind punt
description: Beperk de toegang tot een Azure container Registry met behulp van een service-eind punt in een virtueel Azure-netwerk. Toegang tot het service-eind punt is een functie van de laag Premium-Service.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: c49595ee4ee79aef264a87dd48bccd03f3d4f5a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773892"
---
# <a name="restrict-access-to-a-container-registry-using-a-service-endpoint-in-an-azure-virtual-network"></a>Toegang tot een container register beperken met behulp van een service-eind punt in een virtueel Azure-netwerk

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) biedt veilige, persoonlijke netwerken voor uw Azure-en on-premises resources. Met een [service-eind punt](../virtual-network/virtual-network-service-endpoints-overview.md) kunt u het open bare IP-adres van het container register beveiligen tot alleen uw virtuele netwerk. Dit eind punt geeft een optimale route naar de resource via het Azure-backbone-netwerk. De identiteiten van het virtuele netwerk en het subnet worden ook met elke aanvraag verzonden.

In dit artikel wordt beschreven hoe u een container register service-eind punt (preview) configureert in een virtueel netwerk. 

> [!IMPORTANT]
> Azure Container Registry biedt nu ondersteuning voor [persoonlijke Azure-koppelingen](container-registry-private-link.md), waardoor persoonlijke eind punten van een virtueel netwerk in een REGI ster kunnen worden geplaatst. Persoonlijke eind punten zijn toegankelijk vanuit het virtuele netwerk met behulp van privé-IP-adressen. U kunt het beste persoonlijke eind punten gebruiken in plaats van service-eind punten in de meeste netwerk scenario's.

Het configureren van een Registry-service-eind punt is beschikbaar in de service tier van het **Premium** -container register. Zie [Azure container Registry service lagen](container-registry-skus.md)voor meer informatie over de service lagen en limieten van het REGI ster.

## <a name="preview-limitations"></a>Preview-beperkingen

* Toekomstige ontwikkeling van service-eind punten voor Azure Container Registry is momenteel niet gepland. Het is raadzaam om in plaats daarvan [persoonlijke eind punten](container-registry-private-link.md) te gebruiken.
* U kunt de Azure Portal niet gebruiken om service-eind punten in een REGI ster te configureren.
* Alleen een [Azure Kubernetes service](../aks/intro-kubernetes.md) -cluster of [virtuele](../virtual-machines/linux/overview.md) Azure-machine kan worden gebruikt als een host voor toegang tot een container register met behulp van een service-eind punt. *Andere Azure-Services, waaronder Azure Container Instances, worden niet ondersteund.*
* Service-eind punten voor Azure Container Registry worden niet ondersteund in de Azure-Cloud voor de Amerikaanse overheid of Azure China.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Vereisten

* Als u de stappen van Azure CLI in dit artikel wilt gebruiken, is Azure CLI-versie 2.0.58 of later vereist. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

* Als u nog geen container register hebt, maakt u er een (vereist een Premium-laag) en pusht u een voor beeld van een installatie kopie, zoals `hello-world` van docker hub. Gebruik bijvoorbeeld de [Azure Portal][quickstart-portal] of de [Azure cli][quickstart-cli] om een REGI ster te maken. 

* Als u de toegang tot het REGI ster wilt beperken met behulp van een service-eind punt in een ander Azure-abonnement, registreert u de resource provider voor Azure Container Registry in dat abonnement. Bijvoorbeeld:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="configure-network-access-for-registry"></a>Netwerk toegang voor het REGI ster configureren

In deze sectie configureert u het container register zodanig dat toegang is toegestaan vanuit een subnet in een virtueel Azure-netwerk. De stappen worden beschreven met behulp van de Azure CLI.

### <a name="add-a-service-endpoint-to-a-subnet"></a>Een service-eind punt toevoegen aan een subnet

Wanneer u een virtuele machine maakt, maakt Azure standaard een virtueel netwerk in dezelfde resource groep. De naam van het virtuele netwerk is gebaseerd op de naam van de virtuele machine. Als u bijvoorbeeld een *myDockerVM* naam voor de virtuele machine hebt, is de standaard naam van het virtuele netwerk *myDockerVMVNET*, met een subnet met de naam *myDockerVMSubnet*. Controleer dit met behulp van de opdracht [AZ Network vnet List][az-network-vnet-list] :

```azurecli
az network vnet list \
  --resource-group myResourceGroup \
  --query "[].{Name: name, Subnet: subnets[0].name}"
```

Uitvoer:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet&quot;: &quot;myDockerVMSubnet"
  }
]
```

Gebruik de opdracht [AZ Network vnet subnet update][az-network-vnet-subnet-update] om een service-eind punt van **micro soft. ContainerRegistry** toe te voegen aan uw subnet. Vervang de namen van uw virtuele netwerk en subnet door de volgende opdracht:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Gebruik de opdracht [AZ Network vnet subnet show][az-network-vnet-subnet-show] om de resource-id van het subnet op te halen. U hebt dit in een latere stap nodig om een netwerk toegangs regel te configureren.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

Uitvoer:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="change-default-network-access-to-registry"></a>De standaard netwerk toegang wijzigen in het REGI ster

Een Azure container Registry staat standaard verbindingen toe van hosts op elk netwerk. Als u de toegang tot een geselecteerd netwerk wilt beperken, wijzigt u de standaard actie om de toegang te weigeren. Vervang de naam van het REGI ster in de volgende [AZ ACR update][az-acr-update] -opdracht:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Netwerk regel toevoegen aan REGI ster

Gebruik de opdracht [AZ ACR Network-Rule add][az-acr-network-rule-add] om een netwerk regel toe te voegen aan het REGI ster waarmee toegang vanuit het subnet van de virtuele machine wordt toegestaan. Vervang de naam van het container register en de bron-ID van het subnet in de volgende opdracht: 

 ```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --subnet <subnet-resource-id>
```

## <a name="verify-access-to-the-registry"></a>Toegang tot het REGI ster controleren

Nadat u een paar minuten hebt gewacht voordat de configuratie is bijgewerkt, controleert u of de virtuele machine toegang heeft tot het container register. Maak een SSH-verbinding met uw virtuele machine en voer de opdracht [AZ ACR login][az-acr-login] uit om u aan te melden bij het REGI ster. 

```bash
az acr login --name mycontainerregistry
```

U kunt register bewerkingen uitvoeren zoals uitvoeren `docker pull` om een voorbeeld installatie kopie uit het REGI ster te halen. Vervang een installatie kopie en label waarde die geschikt is voor uw REGI ster, voorafgegaan door de naam van de aanmeldings server van het REGI ster (alle kleine letters):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker haalt de installatie kopie op naar de virtuele machine.

In dit voor beeld wordt gedemonstreerd dat u toegang hebt tot het persoonlijke container register via de netwerk toegangs regel. Het REGI ster kan echter niet worden geopend vanaf een aanmeldings-host waarvoor geen netwerk toegangs regel is geconfigureerd. Als u zich probeert aan te melden bij een andere host met behulp van de `az acr login` opdracht of `docker login` opdracht, is de uitvoer vergelijkbaar met het volgende:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Standaard toegang tot het REGI ster herstellen

Als u het REGI ster wilt herstellen om standaard toegang toe te staan, verwijdert u de netwerk regels die zijn geconfigureerd. Stel vervolgens de standaard actie in om toegang toe te staan. 

### <a name="remove-network-rules"></a>Netwerk regels verwijderen

Als u een lijst wilt weer geven met netwerk regels die voor het REGI ster zijn geconfigureerd, voert u de volgende opdracht [AZ ACR Network-Rule List][az-acr-network-rule-list] uit:

```azurecli
az acr network-rule list --name mycontainerregistry 
```

Voer voor elke geconfigureerde regel de opdracht [AZ ACR Network-regel Remove][az-acr-network-rule-remove] uit om deze te verwijderen. Bijvoorbeeld:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="allow-access"></a>Toegang toestaan

Vervang de naam van het REGI ster in de volgende [AZ ACR update][az-acr-update] -opdracht:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle Azure-resources in dezelfde resource groep hebt gemaakt en deze niet meer nodig hebt, kunt u de resources eventueel verwijderen met behulp van één opdracht [AZ Group delete](/cli/azure/group) :

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

* Als u de toegang tot een REGI ster wilt beperken met behulp van een persoonlijk eind punt in een virtueel netwerk, raadpleegt u [Azure private link configureren voor een Azure container Registry](container-registry-private-link.md).
* Zie [regels configureren voor toegang tot een Azure container Registry achter een firewall](container-registry-firewall-access-rules.md)als u toegangs regels voor het REGI ster wilt instellen van achter een firewall van een client.


<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
