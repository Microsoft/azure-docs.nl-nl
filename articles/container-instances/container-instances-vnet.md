---
title: Een containergroep implementeren in een virtueel Azure-netwerk
description: Leer hoe u een containergroep implementeert in een nieuw of bestaand virtueel Azure-netwerk met behulp van de Azure-opdrachtregelinterface.
ms.topic: article
ms.date: 07/02/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 44be66957aa745179ffe4cd00db75f1d47237dfc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771043"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Containerinstanties implementeren in een virtueel Azure-netwerk

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) beveiligde privénetwerken voor uw Azure- en on-premises resources. Door containergroepen te implementeren in een virtueel Azure-netwerk, kunnen uw containers veilig communiceren met andere resources in het virtuele netwerk.

In dit artikel wordt beschreven hoe u de [opdracht az container create][az-container-create] in de Azure CLI gebruikt om containergroepen te implementeren in een nieuw virtueel netwerk of een bestaand virtueel netwerk. 

Zie Scenario's en resources voor virtuele netwerken voor [netwerkscenario's en -beperkingen voor Azure Container Instances.](container-instances-virtual-network-concepts.md)

> [!IMPORTANT]
> Implementatie van containergroep in een virtueel netwerk is algemeen beschikbaar voor Linux-containers, in de meeste regio's waar Azure Container Instances beschikbaar is. Zie Regio's en beschikbaarheid [van resources voor meer informatie.][container-regions] 

Voorbeelden in dit artikel zijn opgemaakt voor de Bash-shell. Als u de voorkeur geeft aan een andere shell, zoals PowerShell of opdrachtprompt, past u de tekens voor het vervolg van de regel dienovereenkomstig aan.


## <a name="deploy-to-new-virtual-network"></a>Implementeren in nieuw virtueel netwerk

Als u wilt implementeren in een nieuw virtueel netwerk en Azure de netwerkresources automatisch voor u wilt laten maken, geeft u het volgende op wanneer u [az container create uitvoert:][az-container-create]

* Naam van virtueel netwerk
* Voorvoegsel van virtueel netwerkadres in CIDR-indeling
* Subnetnaam
* Subnetadres voorvoegsel in CIDR-indeling

De adres voorvoegsels van het virtuele netwerk en het subnet geven de adresruimten voor respectievelijk het virtuele netwerk en subnet op. Deze waarden worden weergegeven in cidr-notatie (Classless Inter-Domain Routing), bijvoorbeeld `10.0.0.0/16` . Zie Een subnet van een virtueel netwerk toevoegen, wijzigen of verwijderen voor meer informatie over het werken met [subnetten.](../virtual-network/virtual-network-manage-subnet.md)

Zodra u uw eerste containergroep met deze methode hebt geïmplementeerd, kunt u implementeren naar hetzelfde subnet door de namen van het virtuele netwerk en subnet op te geven, of door het netwerkprofiel op te geven dat automatisch door Azure voor u wordt gemaakt. Omdat Azure het subnet delegeert aan Azure Container Instances, kunt u alleen *containergroepen* implementeren in het subnet.

### <a name="example"></a>Voorbeeld

Met de [volgende opdracht az container create][az-container-create] geeft u instellingen op voor een nieuw virtueel netwerk en subnet. Geef de naam op van een resourcegroep die is gemaakt in een regio waar implementaties van containergroepen in een virtueel netwerk beschikbaar [zijn.](container-instances-region-availability.md) Met deze opdracht wordt de openbare Microsoft [aci-helloworld-container][aci-helloworld] geïmplementeerd die een kleine webserver Node.js een statische webpagina wordt uitgevoerd. In de volgende sectie implementeert u een tweede containergroep in hetzelfde subnet en test u de communicatie tussen de twee container-exemplaren.

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

Wanneer u met deze methode implementeert in een nieuw virtueel netwerk, kan de implementatie enkele minuten duren terwijl de netwerkbronnen worden gemaakt. Na de eerste implementatie worden extra containergroepimplementaties naar hetzelfde subnet sneller voltooid.

## <a name="deploy-to-existing-virtual-network"></a>Implementeren in bestaand virtueel netwerk

Een containergroep implementeren in een bestaand virtueel netwerk:

1. Maak een subnet binnen uw bestaande virtuele netwerk, gebruik een bestaand subnet waarin al een containergroep is geïmplementeerd *of* gebruik een bestaand subnet dat is leeggemaakt van alle andere resources
1. Implementeer een containergroep [met az container create][az-container-create] en geef een van de volgende opties op:
   * Naam van virtueel netwerk en subnetnaam
   * Resource-id van virtueel netwerk en resource-id van subnet, waarmee u een virtueel netwerk uit een andere resourcegroep kunt gebruiken
   * De naam of id van het netwerkprofiel, die u kunt verkrijgen met [az network profile list][az-network-profile-list]

### <a name="example"></a>Voorbeeld

In het volgende voorbeeld wordt een tweede containergroep geïmplementeerd in hetzelfde subnet dat u eerder hebt gemaakt en wordt de communicatie tussen de twee container-exemplaren gecontroleerd.

Haal eerst het IP-adres op van de eerste containergroep die u hebt geïmplementeerd, *de appcontainer*:

```azurecli
az container show --resource-group myResourceGroup \
  --name appcontainer \
  --query ipAddress.ip --output tsv
```

In de uitvoer wordt het IP-adres van de containergroep in het privésubnet weergegeven. Bijvoorbeeld:

```console
10.0.0.4
```

Stel nu in `CONTAINER_GROUP_IP` op het IP-adres dat u hebt opgehaald met de `az container show` opdracht en voer de volgende opdracht `az container create` uit. Deze tweede container, *commchecker,* voert een Alpine Linux-gebaseerde afbeelding uit en wordt uitgevoerd op basis van het IP-adres van het privé-subnet van de eerste `wget` containergroep.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-address>

az container create \
  --resource-group myResourceGroup \
  --name commchecker \
  --image alpine:3.5 \
  --command-line "wget $CONTAINER_GROUP_IP" \
  --restart-policy never \
  --vnet aci-vnet \
  --subnet aci-subnet
```

Nadat deze tweede containerimplementatie is voltooid, haalt u de logboeken op, zodat u de uitvoer kunt zien van de `wget` opdracht die is uitgevoerd:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Als de tweede container met de eerste is gecommuniceerd, is de uitvoer vergelijkbaar met:

```console
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

De logboekuitvoer moet laten zien dat het indexbestand verbinding heeft kunnen maken en downloaden vanuit de eerste container met behulp van het `wget` privé-IP-adres op het lokale subnet. Netwerkverkeer tussen de twee containergroepen blijft binnen het virtuele netwerk.

### <a name="example---yaml"></a>Voorbeeld - YAML

U kunt ook een containergroep implementeren in een bestaand virtueel netwerk met behulp van een YAML-bestand, een [Resource Manager-sjabloon of](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)een andere programmatische methode, zoals met de Python SDK. 

Wanneer u bijvoorbeeld een YAML-bestand gebruikt, kunt u implementeren in een virtueel netwerk met een subnet dat is gedelegeerd Azure Container Instances. Geef de volgende eigenschappen op:

* `ipAddress`: De instellingen voor het privé-IP-adres voor de containergroep.
  * `ports`: de poorten die moeten worden geopend, indien aanwezig.
  * `protocol`: Het protocol (TCP of UDP) voor de geopende poort.
* `networkProfile`: Netwerkinstellingen voor het virtuele netwerk en subnet.
  * `id`: De volledige Resource Manager resource-id van de `networkProfile` .

Voer de opdracht [az network profile list][az-network-profile-list] uit om de id van het netwerkprofiel op te halen, en geef de naam op van de resourcegroep die uw virtuele netwerk en gedelegeerd subnet bevat.

``` azurecli
az network profile list --resource-group myResourceGroup \
  --query [0].id --output tsv
```

Voorbeelduitvoer:

```console
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Zodra u de netwerkprofiel-id hebt, kopieert u de volgende YAML naar een nieuw bestand met de naam *vnet-deploy-aci.yaml.* Vervang `networkProfile` onder de waarde door de id die u zojuist hebt opgehaald en sla het bestand `id` op. Deze YAML maakt een containergroep met de *naam appcontaineryaml* in uw virtuele netwerk.

```YAML
apiVersion: '2019-12-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Implementeer de containergroep met de [opdracht az container create,][az-container-create] met de naam van het YAML-bestand voor de `--file` parameter :

```azurecli
az container create --resource-group myResourceGroup \
  --file vnet-deploy-aci.yaml
```

Zodra de implementatie is voltooid, kunt u de [opdracht az container show][az-container-show] uitvoeren om de status ervan weer te geven. Voorbeelduitvoer:

```console
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Resources opschonen

### <a name="delete-container-instances"></a>Container instances verwijderen

Wanneer u klaar bent met het werken met de container-exemplaren die u hebt gemaakt, verwijdert u deze met de volgende opdrachten:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Netwerkbronnen verwijderen

Voor deze functie zijn momenteel verschillende aanvullende opdrachten vereist om de netwerkresources te verwijderen die u eerder hebt gemaakt. Als u de voorbeeldopdrachten in de vorige secties van dit artikel hebt gebruikt om uw virtuele netwerk en subnet te maken, kunt u het volgende script gebruiken om deze netwerkbronnen te verwijderen. In het script wordt ervan uit gegaan dat uw resourcegroep één virtueel netwerk met één netwerkprofiel bevat.

Voordat u het script gaat uitvoeren, stelt u de variabele in op de naam van de resourcegroep die het virtuele netwerk en subnet bevat dat `RES_GROUP` moet worden verwijderd. Werk de naam van het virtuele netwerk bij als u de eerder voorgestelde `aci-vnet` naam niet hebt gebruikt. Het script is opgemaakt voor de Bash-shell. Als u de voorkeur geeft aan een andere shell, zoals PowerShell of de opdrachtprompt, moet u de variabeletoewijzing en accessoires dienovereenkomstig aanpassen.

> [!WARNING]
> Met dit script worden resources verwijderd. Hiermee verwijdert u het virtuele netwerk en alle subnetten die het bevat. Zorg ervoor dat u  geen resources meer nodig hebt in het virtuele netwerk, inclusief eventuele subnetten die het bevat, voordat u dit script gaat uitvoeren. Zodra deze resources zijn **verwijderd, kunnen ze niet meer worden teruggehaald.**

```azurecli
# Replace <my-resource-group> with the name of your resource group
# Assumes one virtual network in resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
# Assumes one profile in virtual network
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Volgende stappen

Zie Een [Azure-containergroep](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)maken met VNet als u een nieuw virtueel netwerk, subnet, netwerkprofiel en containergroep wilt implementeren met behulp van een Resource Manager-sjabloon.

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-network-vnet-create]: /cli/azure/network/vnet#az_network_vnet_create
[az-network-profile-list]: /cli/azure/network/profile#az_network_profile_list
[container-regions]: container-instances-region-availability.md
