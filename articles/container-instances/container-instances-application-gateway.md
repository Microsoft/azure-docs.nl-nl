---
title: Statisch IP-adres voor container groep
description: Een container groep maken in een virtueel netwerk en een Azure Application gateway gebruiken om een statisch frontend-IP-adres beschikbaar te stellen voor een web-app met container
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 0131780fdb04a71837d5ae9bf5498bf2bd499f8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98035050"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>Een statisch IP-adres voor een container groep beschikbaar maken

In dit artikel wordt één manier getoond om een statisch, openbaar IP-adres voor een [container groep](container-instances-container-groups.md) beschikbaar te maken met behulp van een Azure [Application Gateway](../application-gateway/overview.md). Volg deze stappen als u een statisch toegangs punt nodig hebt voor een extern gerichte container-app die wordt uitgevoerd in Azure Container Instances. 

In dit artikel gebruikt u de Azure CLI om de resources voor dit scenario te maken:

* Een virtueel Azure-netwerk
* Een container groep die [in het virtuele netwerk](container-instances-vnet.md) is geïmplementeerd en die als host fungeert voor een kleine web-app
* Een toepassings gateway met een openbaar front-end-IP-adres, een listener voor het hosten van een website op de gateway en een route naar de back-end-container groep

Zolang de Application Gateway wordt uitgevoerd en de container groep een stabiel privé IP-adres in het overgedragen subnet van het netwerk beschikbaar maakt, is de container groep toegankelijk op dit open bare IP-adres.

> [!NOTE]
> Azure-kosten voor een toepassings gateway op basis van de hoeveelheid tijd die de gateway is ingericht en beschikbaar, evenals de hoeveelheid gegevens die wordt verwerkt. Zie [prijzen](https://azure.microsoft.com/pricing/details/application-gateway/).

## <a name="create-virtual-network"></a>Virtueel netwerk maken

In de meeste gevallen hebt u mogelijk al een virtueel Azure-netwerk. Als u er nog geen hebt, kunt u er een maken zoals wordt weer gegeven met de volgende voorbeeld opdrachten. Het virtuele netwerk heeft afzonderlijke subnetten nodig voor de toepassings gateway en de container groep.

Als u een Azure-resource groep nodig hebt, moet u er een maken. Bijvoorbeeld:

```azureci
az group create --name myResourceGroup --location eastus
```

Maak een virtueel netwerk met de opdracht [AZ Network vnet Create][az-network-vnet-create] . Met deze opdracht maakt u het *myAGSubnet* -subnet in het netwerk.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

Gebruik de opdracht [AZ Network vnet subnet Create][az-network-vnet-subnet-create] om een subnet te maken voor de back-end-container groep. Hier ziet u de naam *myACISubnet*.

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

Gebruik de opdracht [AZ Network Public-IP Create][az-network-public-ip-create] om een statische open bare IP-resource te maken. In een latere stap wordt dit adres geconfigureerd als de front-end van de toepassings gateway.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>Containergroep maken

Voer de volgende [AZ-container Create][az-container-create] uit om een container groep te maken in het virtuele netwerk dat u in de vorige stap hebt geconfigureerd. 

De groep wordt geïmplementeerd in het *myACISubnet* -subnet en bevat één exemplaar met de naam *appcontainer* die de `aci-helloworld` installatie kopie ophaalt. Zoals in andere artikelen in de documentatie wordt weer gegeven, verpakt deze afbeelding een kleine web-app die is geschreven in Node.js die een statische HTML-pagina vormt. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

Wanneer de implementatie is voltooid, wordt aan de container groep een privé-IP-adres in het virtuele netwerk toegewezen. Voer bijvoorbeeld de volgende opdracht [AZ container show][az-container-show] uit om het IP-adres van de groep op te halen:

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

De uitvoer is vergelijkbaar met: `10.0.2.4`.

Voor gebruik in een latere stap slaat u het IP-adres op in een omgevings variabele:

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

> [!IMPORTANT]
> Als de container groep is gestopt, gestart of opnieuw wordt gestart, is het privé-IP-adres van de container groep onderhevig aan wijzigingen. Als dit het geval is, moet u de configuratie van de toepassings gateway bijwerken.

## <a name="create-application-gateway"></a>Een toepassingsgateway maken

Maak een toepassings gateway in het virtuele netwerk volgens de stappen in de [Snelstartgids voor Application Gateway](../application-gateway/quick-create-cli.md). De volgende [AZ Network Application-Gateway Create][az-network-application-gateway-create] opdracht maakt een gateway met een openbaar frontend-IP-adres en een route naar de back-end-container groep. Raadpleeg de [Application Gateway-documentatie](../application-gateway/index.yml) voor meer informatie over de gateway-instellingen.

```azurecli
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroup \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-protocol http \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$ACI_IP" 
```


Het kan tot vijf tien minuten duren voordat Azure de toepassings gateway maakt. 

## <a name="test-public-ip-address"></a>Openbaar IP-adres testen
  
Nu kunt u de toegang tot de web-app die wordt uitgevoerd in de container groep achter de toepassings gateway testen.

Voer de opdracht [AZ Network public-ip show][az-network-public-ip-show] uit om het open bare frontend-IP-adres van de gateway op te halen:

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

Output is een openbaar IP-adres, vergelijkbaar met: `52.142.18.133` .

Als u de actieve web-app wilt weer geven wanneer deze is geconfigureerd, gaat u naar het open bare IP-adres van de gateway in uw browser. Geslaagde toegang is vergelijkbaar met:

![Schermafbeelding van browser met toepassing die wordt uitgevoerd in een exemplaar van een Azure-container](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>Volgende stappen

* Bekijk een [Quick](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) start-sjabloon voor het maken van een container groep met een WordPress-container exemplaar als back-endserver achter een toepassings gateway.
* U kunt ook een toepassings gateway configureren met een certificaat voor SSL-beëindiging. Zie het [overzicht](../application-gateway/ssl-overview.md) en de [zelf studie](../application-gateway/create-ssl-portal.md).
* Afhankelijk van uw scenario kunt u gebruikmaken van andere Azure-oplossingen voor taak verdeling met Azure Container Instances. Gebruik bijvoorbeeld [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) om het verkeer over meerdere container instanties en meerdere regio's te distribueren. Zie dit [blog bericht](https://aaronmsft.com/posts/azure-container-instances/).

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
