---
title: Container groep implementeren in een virtueel Azure-netwerk
description: Meer informatie over hoe u een container groep implementeert in een nieuw of bestaand virtueel Azure-netwerk met behulp van de Azure-opdracht regel interface.
ms.topic: article
ms.date: 07/02/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: b791d3f37809c2eca53f5a3cd34f7c44dd11ce40
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98028876"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Containerinstanties implementeren in een virtueel Azure-netwerk

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) biedt veilige, persoonlijke netwerken voor uw Azure-en on-premises resources. Door container groepen te implementeren in een virtueel Azure-netwerk, kunnen uw containers veilig communiceren met andere resources in het virtuele netwerk.

In dit artikel wordt beschreven hoe u de opdracht [AZ container Create][az-container-create] in de Azure cli gebruikt om container groepen te implementeren naar een nieuw virtueel netwerk of een bestaand virtueel netwerk. 

Zie [scenario's en resources voor het virtuele netwerk voor Azure container instances](container-instances-virtual-network-concepts.md)voor netwerk scenario's en-beperkingen.

> [!IMPORTANT]
> Implementatie van container groep naar een virtueel netwerk is algemeen beschikbaar voor Linux-containers, in de meeste regio's waar Azure Container Instances beschikbaar is. Zie [regio's en resources Beschik baarheid][container-regions]voor meer informatie. 

Voor beelden in dit artikel zijn ingedeeld voor de bash-shell. Als u de voor keur geeft aan een andere shell, zoals Power shell of opdracht prompt, past u de regel vervolg tekens dienovereenkomstig aan.


## <a name="deploy-to-new-virtual-network"></a>Implementeren naar nieuw virtueel netwerk

Als u wilt implementeren in een nieuw virtueel netwerk en Azure de netwerk resources automatisch voor u wilt maken, geeft u het volgende op wanneer u [AZ container Create][az-container-create]uitvoert:

* Naam van virtueel netwerk
* Adres voorvoegsel van het virtuele netwerk in CIDR-indeling
* Subnetnaam
* Adres voorvoegsel van het subnet in CIDR-indeling

Met de voor voegsels voor het virtuele netwerk en het subnet adres worden de adres ruimten voor het virtuele netwerk en het subnet opgegeven. Deze waarden worden weer gegeven in de CIDR-notatie (Classable Inter-Domain Routing), bijvoorbeeld `10.0.0.0/16` . Zie [een subnet van een virtueel netwerk toevoegen, wijzigen of verwijderen](../virtual-network/virtual-network-manage-subnet.md)voor meer informatie over het werken met subnetten.

Wanneer u uw eerste container groep met deze methode hebt geïmplementeerd, kunt u in hetzelfde subnet implementeren door het virtuele netwerk en de subnetnaam op te geven, of het netwerk profiel dat door Azure automatisch voor u wordt gemaakt. Omdat Azure het subnet delegeert naar Azure Container Instances, kunt u *alleen* container groepen implementeren naar het subnet.

### <a name="example"></a>Voorbeeld

Met de volgende opdracht [AZ container Create][az-container-create] worden de instellingen voor een nieuw virtueel netwerk en subnet opgegeven. Geef de naam op van een resource groep die is gemaakt in een regio waarin implementaties van container groepen in een virtueel netwerk [beschikbaar](container-instances-region-availability.md)zijn. Met deze opdracht wordt de open bare container micro soft [ACI-HelloWorld][aci-helloworld] met een kleine Node.js webserver met een statische webpagina geïmplementeerd. In de volgende sectie implementeert u een tweede container groep naar hetzelfde subnet en test u de communicatie tussen de twee container exemplaren.

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

Wanneer u deze methode gebruikt voor het implementeren van een nieuw virtueel netwerk, kan het enkele minuten duren voordat de implementatie van de netwerk bronnen is gemaakt. Na de eerste implementatie worden extra container groeps implementaties naar hetzelfde subnet sneller uitgevoerd.

## <a name="deploy-to-existing-virtual-network"></a>Implementeren in een bestaand virtueel netwerk

Een container groep implementeren in een bestaand virtueel netwerk:

1. Maak een subnet in uw bestaande virtuele netwerk, gebruik een bestaand subnet waarin een container groep al is geïmplementeerd, of gebruik een bestaand subnet dat *alle* andere resources heeft geleegd
1. Implementeer een container groep met [AZ container Create][az-container-create] en geef een van de volgende opties op:
   * Naam van het virtuele netwerk en het subnet
   * Bron-ID van het virtuele netwerk en de resource-ID van het subnet, waarmee een virtueel netwerk van een andere resource groep kan worden gebruikt
   * Naam of ID van het netwerk profiel, dat u kunt verkrijgen met de [lijst AZ Network profile][az-network-profile-list]

### <a name="example"></a>Voorbeeld

In het volgende voor beeld wordt een tweede container groep geïmplementeerd op hetzelfde subnet dat eerder is gemaakt en wordt de communicatie tussen de twee container instanties geverifieerd.

Haal eerst het IP-adres op van de eerste container groep die u hebt geïmplementeerd, de *appcontainer*:

```azurecli
az container show --resource-group myResourceGroup \
  --name appcontainer \
  --query ipAddress.ip --output tsv
```

In de uitvoer wordt het IP-adres van de container groep in het privé-subnet weer gegeven. Bijvoorbeeld:

```console
10.0.0.4
```

Stel nu `CONTAINER_GROUP_IP` in op het IP-adres dat u hebt opgehaald met de `az container show` opdracht en voer de volgende `az container create` opdracht uit. Deze tweede container, *commchecker*, voert een alpine Linux-installatie kopie uit en voert een uitvoer uit `wget` op basis van het IP-adres van het particuliere subnet van de container groep.

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

Nadat deze tweede container implementatie is voltooid, haalt u de logboeken op zodat u de uitvoer van de `wget` opdracht die deze heeft uitgevoerd, kunt zien:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Als de tweede container met de eerste keer is gecommuniceerd, is de uitvoer vergelijkbaar met:

```console
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

De logboek uitvoer geeft aan dat er `wget` verbinding kan worden gemaakt met het index bestand van de eerste container met behulp van het bijbehorende privé-IP-adres op het lokale subnet. Het netwerk verkeer tussen de twee container groepen bleef binnen het virtuele netwerk.

### <a name="example---yaml"></a>Voor beeld-YAML

U kunt ook een container groep implementeren in een bestaand virtueel netwerk met behulp van een YAML-bestand, een [Resource Manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)of een andere programmatische methode, zoals met de PYTHON-SDK. 

Wanneer u bijvoorbeeld een YAML-bestand gebruikt, kunt u implementeren in een virtueel netwerk met een subnet dat is overgedragen aan Azure Container Instances. Geef de volgende eigenschappen op:

* `ipAddress`: De instellingen voor het privé IP-adres van de container groep.
  * `ports`: De poorten die moeten worden geopend, indien van toepassing.
  * `protocol`: Het Protocol (TCP of UDP) voor de geopende poort.
* `networkProfile`: Netwerk instellingen voor het virtuele netwerk en subnet.
  * `id`: De volledige Resource Manager-Resource-ID van de `networkProfile` .

Als u de ID van het netwerk profiel wilt ophalen, voert u de opdracht [AZ Network profile list][az-network-profile-list] uit en geeft u de naam op van de resource groep die het virtuele netwerk en het overgedragen subnet bevat.

``` azurecli
az network profile list --resource-group myResourceGroup \
  --query [0].id --output tsv
```

Voorbeelduitvoer:

```console
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Wanneer u de netwerk profiel-ID hebt, kopieert u de volgende YAML naar een nieuw bestand met de naam *vnet-Deploy-ACI. yaml*. `networkProfile`Vervang onder de waarde door de `id` id die u zojuist hebt opgehaald en sla het bestand op. Met deze YAML maakt u een container groep met de naam *appcontaineryaml* in uw virtuele netwerk.

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

Implementeer de container groep met de opdracht [AZ container Create][az-container-create] en geef de naam van het yaml-bestand op voor de `--file` para meter:

```azurecli
az container create --resource-group myResourceGroup \
  --file vnet-deploy-aci.yaml
```

Zodra de implementatie is voltooid, voert u de opdracht [AZ container show][az-container-show] uit om de status weer te geven. Voorbeelduitvoer:

```console
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Resources opschonen

### <a name="delete-container-instances"></a>Container instanties verwijderen

Wanneer u klaar bent met de container instanties die u hebt gemaakt, verwijdert u deze met de volgende opdrachten:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Netwerk bronnen verwijderen

Voor deze functie zijn momenteel verschillende extra opdrachten vereist voor het verwijderen van de netwerk resources die u eerder hebt gemaakt. Als u de voorbeeld opdrachten in vorige secties van dit artikel hebt gebruikt om uw virtuele netwerk en subnet te maken, kunt u het volgende script gebruiken om die netwerk resources te verwijderen. In het script wordt ervan uitgegaan dat uw resource groep één virtueel netwerk met één netwerk profiel bevat.

Voordat u het script uitvoert, stelt `RES_GROUP` u de variabele in op de naam van de resource groep met het virtuele netwerk en het subnet dat moet worden verwijderd. Werk de naam van het virtuele netwerk bij als u de naam die u eerder hebt voorgesteld niet hebt gebruikt `aci-vnet` . Het script is geformatteerd voor de bash-shell. Als u de voor keur geeft aan een andere shell, zoals Power shell of opdracht prompt, moet u de toewijzings-en toegangs rechten van de variabele dienovereenkomstig aanpassen.

> [!WARNING]
> Met dit script worden resources verwijderd! Hiermee verwijdert u het virtuele netwerk en alle subnetten die het bevat. Zorg ervoor dat u *een* van de resources in het virtuele netwerk niet meer nodig hebt, met inbegrip van de subnetten die het bevat, voordat u dit script uitvoert. Nadat **deze bronnen zijn verwijderd, kunnen ze onherstelbaar zijn**.

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

Zie [een Azure-container groep met VNet maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)voor het implementeren van een nieuw virtueel netwerk, subnet, netwerk profiel en container groep met behulp van een resource manager-sjabloon.

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
[container-regions]: container-instances-region-availability.md
