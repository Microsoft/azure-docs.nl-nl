---
title: Toegang beperken met behulp van een service-eindpunt
description: Beperk de toegang tot een Azure-containerregister met behulp van een service-eindpunt in een virtueel Azure-netwerk. Toegang tot service-eindpunten is een functie van de Premium-servicelaag.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 8a67a011c75a192df9ad3460458fd766b5ec1ec1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773459"
---
# <a name="restrict-access-to-a-container-registry-using-a-service-endpoint-in-an-azure-virtual-network"></a>De toegang tot een containerregister beperken met behulp van een service-eindpunt in een virtueel Azure-netwerk

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) biedt veilige privénetwerken voor uw Azure- en on-premises resources. Met [een service-eindpunt](../virtual-network/virtual-network-service-endpoints-overview.md) kunt u het openbare IP-adres van uw containerregister alleen beveiligen naar uw virtuele netwerk. Dit eindpunt biedt verkeer een optimale route naar de resource via het Backbone-netwerk van Azure. De identiteiten van het virtuele netwerk en het subnet worden ook bij elke aanvraag verzonden.

In dit artikel wordt beschreven hoe u een service-eindpunt voor een containerregister (preview) configureert in een virtueel netwerk. 

> [!IMPORTANT]
> Azure Container Registry ondersteunt nu [Azure Private Link,](container-registry-private-link.md)zodat privé-eindpunten vanuit een virtueel netwerk in een register kunnen worden geplaatst. Privé-eindpunten zijn toegankelijk vanuit het virtuele netwerk met behulp van privé-IP-adressen. In de meeste netwerkscenario's raden we u aan om privé-eindpunten te gebruiken in plaats van service-eindpunten.

Het configureren van een service-eindpunt voor het register is beschikbaar in de **servicelaag** Premium-containerregister. Zie Azure Container Registry servicelagen voor meer informatie [over registerservicelagen en -limieten.](container-registry-skus.md)

## <a name="preview-limitations"></a>Preview-beperkingen

* Toekomstige ontwikkeling van service-eindpunten voor Azure Container Registry is momenteel niet gepland. We raden u aan [om in plaats daarvan privé-eindpunten te](container-registry-private-link.md) gebruiken.
* U kunt de Azure Portal gebruiken om service-eindpunten in een register te configureren.
* Alleen een [Azure Kubernetes Service](../aks/intro-kubernetes.md) of virtuele [Azure-machine](../virtual-machines/linux/overview.md) kan worden gebruikt als host voor toegang tot een containerregister met behulp van een service-eindpunt. *Andere Azure-services, Azure Container Instances worden niet ondersteund.*
* Service-eindpunten voor Azure Container Registry worden niet ondersteund in de Cloud voor De Amerikaanse overheid of de Azure China-cloud.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Vereisten

* Als u de Azure CLI-stappen in dit artikel wilt gebruiken, is Azure CLI versie 2.0.58 of hoger vereist. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

* Als u nog geen containerregister hebt, maakt u er een (Premium-laag vereist) en pusht u een voorbeeldafbeelding, `hello-world` zoals vanuit Docker Hub. Gebruik bijvoorbeeld de [Azure Portal][quickstart-portal] of [de Azure CLI om][quickstart-cli] een register te maken. 

* Als u de registertoegang wilt beperken met behulp van een service-eindpunt in een ander Azure-abonnement, registreert u de resourceprovider voor Azure Container Registry in dat abonnement. Bijvoorbeeld:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="configure-network-access-for-registry"></a>Netwerktoegang voor register configureren

In deze sectie configureert u het containerregister om toegang toe te staan vanuit een subnet in een virtueel Azure-netwerk. Stappen worden gegeven met behulp van de Azure CLI.

### <a name="add-a-service-endpoint-to-a-subnet"></a>Een service-eindpunt toevoegen aan een subnet

Wanneer u een virtuele machine maakt, maakt Azure standaard een virtueel netwerk in dezelfde resourcegroep. De naam van het virtuele netwerk is gebaseerd op de naam van de virtuele machine. Als u bijvoorbeeld de virtuele machine *myDockerVM* noemt, is de standaardnaam van het virtuele netwerk *myDockerVMVNET,* met een subnet met de naam *myDockerVMSubnet.* Controleer dit met behulp van [de opdracht az network vnet list:][az-network-vnet-list]

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
    "Subnet": "myDockerVMSubnet"
  }
]
```

Gebruik de [opdracht az network vnet subnet update om][az-network-vnet-subnet-update] een **Microsoft.ContainerRegistry-service-eindpunt** toe te voegen aan uw subnet. Vervang de namen van uw virtuele netwerk en subnet in de volgende opdracht:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Gebruik de [opdracht az network vnet subnet show om][az-network-vnet-subnet-show] de resource-id van het subnet op te halen. U hebt deze in een latere stap nodig om een netwerktoegangsregel te configureren.

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

### <a name="change-default-network-access-to-registry"></a>Standaardnetwerktoegang tot register wijzigen

Standaard staat een Azure-containerregister verbindingen van hosts in elk netwerk toe. Als u de toegang tot een geselecteerd netwerk wilt beperken, wijzigt u de standaardactie om de toegang te weigeren. Vervang de naam van het register in de volgende [az acr update-opdracht:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Netwerkregel toevoegen aan register

Gebruik de [opdracht az acr network-rule add][az-acr-network-rule-add] om een netwerkregel toe te voegen aan uw register die toegang toestaat vanuit het subnet van de virtuele machine. Vervang in de volgende opdracht de naam van het containerregister en de resource-id van het subnet: 

 ```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --subnet <subnet-resource-id>
```

## <a name="verify-access-to-the-registry"></a>Toegang tot het register controleren

Nadat u enkele minuten hebt gewacht totdat de configuratie is bijgewerkt, controleert u of de VM toegang heeft tot het containerregister. Maak een SSH-verbinding met uw VM en voer de [opdracht az acr login][az-acr-login] uit om u aan te melden bij uw register. 

```bash
az acr login --name mycontainerregistry
```

U kunt registerbewerkingen uitvoeren zoals uitvoeren om `docker pull` een voorbeeldafbeelding uit het register op te halen. Vervang een afbeelding en tagwaarde die geschikt zijn voor uw register, voorafgaat door de naam van de aanmeldingsserver voor het register (in kleine letters):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker haalt de afbeelding op naar de VM.

In dit voorbeeld ziet u dat u toegang hebt tot het privécontainerregister via de netwerktoegangsregel. Het register is echter niet toegankelijk vanaf een aanmeldingshost waar geen netwerktoegangsregel is geconfigureerd. Als u zich probeert aan te melden vanaf een andere host met behulp van de opdracht of opdracht, ziet de uitvoer `az acr login` er ongeveer als volgt `docker login` uit:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Standaardregistertoegang herstellen

Als u het register wilt herstellen om standaard toegang toe te staan, verwijdert u eventuele geconfigureerde netwerkregels. Stel vervolgens de standaardactie in om toegang toe te staan. 

### <a name="remove-network-rules"></a>Netwerkregels verwijderen

Voer de volgende opdracht [az acr network-rule list][az-acr-network-rule-list] uit om een lijst met netwerkregels weer te geven die zijn geconfigureerd voor uw register:

```azurecli
az acr network-rule list --name mycontainerregistry 
```

Voer voor elke geconfigureerde regel de [opdracht az acr network-rule remove][az-acr-network-rule-remove] uit om deze te verwijderen. Bijvoorbeeld:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="allow-access"></a>Toegang toestaan

Vervang de naam van het register in de volgende [az acr update-opdracht:][az-acr-update]
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle Azure-resources in dezelfde resourcegroep hebt gemaakt en deze niet meer nodig hebt, kunt u de resources eventueel verwijderen met één [opdracht az group delete:](/cli/azure/group)

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

* Zie Configure Azure Private Link [for an Azure container registry](container-registry-private-link.md)(Toegang tot een register beperken met behulp van een privé-eindpunt in een virtueel netwerk).
* Zie Regels configureren voor toegang tot een Azure-containerregister achter een firewall als u registertoegangsregels wilt [instellen achter een clientfirewall.](container-registry-firewall-access-rules.md)


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
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_repository_list
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az_acr_network_rule_add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az_acr_network_rule_remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az_acr_network_rule_list
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-list]: /cli/azure/network/vnet/#az_network_vnet_list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
