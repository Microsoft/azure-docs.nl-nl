---
title: Statisch IP-adres voor containergroep
description: Maak een containergroep in een virtueel netwerk en gebruik een Azure-toepassingsgateway om een statisch front-end-IP-adres beschikbaar te maken voor een in een container geplaatste web-app
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: de9e06b457a9ea5485fe268bd2b7cf206f0a6c0e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790937"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>Een statisch IP-adres voor een containergroep blootstellen

In dit artikel wordt één manier beschreven om een statisch, openbaar IP-adres voor een [containergroep](container-instances-container-groups.md) beschikbaar te maken met behulp van een [Azure-toepassingsgateway.](../application-gateway/overview.md) Volg deze stappen wanneer u een statisch toegangspunt nodig hebt voor een extern gerichte container-app die wordt uitgevoerd in Azure Container Instances. 

In dit artikel gebruikt u de Azure CLI om de resources voor dit scenario te maken:

* Een virtueel Azure-netwerk
* Een containergroep die is geïmplementeerd [in het virtuele netwerk dat](container-instances-vnet.md) als host voor een kleine web-app wordt gebruikt
* Een toepassingsgateway met een openbaar front-end-IP-adres, een listener voor het hosten van een website op de gateway en een route naar de back-endcontainergroep

Zolang de toepassingsgateway wordt uitgevoerd en de containergroep een stabiel privé-IP-adres beschikbaar maakt in het gedelegeerde subnet van het netwerk, is de containergroep toegankelijk op dit openbare IP-adres.

> [!NOTE]
> Azure brengt kosten in rekening voor een toepassingsgateway op basis van de hoeveelheid tijd die de gateway is ingericht en beschikbaar is, evenals de hoeveelheid gegevens die wordt verwerkt. Zie [prijzen.](https://azure.microsoft.com/pricing/details/application-gateway/)

## <a name="create-virtual-network"></a>Virtueel netwerk maken

In veel gevallen hebt u mogelijk al een virtueel Azure-netwerk. Als u er nog geen hebt, maakt u er een zoals wordt weergegeven met de volgende voorbeeldopdrachten. Het virtuele netwerk heeft afzonderlijke subnetten nodig voor de toepassingsgateway en de containergroep.

Als u er een nodig hebt, maakt u een Azure-resourcegroep. Bijvoorbeeld:

```azureci
az group create --name myResourceGroup --location eastus
```

Maak een virtueel netwerk met de [opdracht az network vnet create.][az-network-vnet-create] Met deze opdracht maakt u *het subnet myAGSubnet* in het netwerk.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

Gebruik de [opdracht az network vnet subnet create][az-network-vnet-subnet-create] om een subnet voor de back-endcontainergroep te maken. Hier heeft deze de *naam my CAPACITIESubnet.*

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

Gebruik de [opdracht az network public-ip create om][az-network-public-ip-create] een statische openbare IP-resource te maken. In een latere stap wordt dit adres geconfigureerd als de front-end van de toepassingsgateway.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>Containergroep maken

Voer de volgende [az container create uit om][az-container-create] een containergroep te maken in het virtuele netwerk dat u in de vorige stap hebt geconfigureerd. 

De groep wordt geïmplementeerd in het *subnet my CAPACITIESubnet* en bevat één exemplaar met de naam *appcontainer* dat de afbeelding `aci-helloworld` oppikt. Zoals u in andere artikelen in de documentatie kunt zien, verpakt deze afbeelding een kleine web-app die is geschreven in Node.js een statische HTML-pagina. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

Wanneer de containergroep is geïmplementeerd, wordt een privé-IP-adres toegewezen in het virtuele netwerk. Voer bijvoorbeeld de volgende opdracht [az container show][az-container-show] uit om het IP-adres van de groep op te halen:

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

De uitvoer is vergelijkbaar met: `10.0.2.4`.

Sla voor gebruik in een latere stap het IP-adres op in een omgevingsvariabele:

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

> [!IMPORTANT]
> Als de containergroep is gestopt, gestart of opnieuw is opgestart, kan het privé-IP-adres van de containergroep worden gewijzigd. Als dit gebeurt, moet u de configuratie van de toepassingsgateway bijwerken.

## <a name="create-application-gateway"></a>Een toepassingsgateway maken

Maak een toepassingsgateway in het virtuele netwerk door de stappen in de quickstart voor [de toepassingsgateway te volgen.](../application-gateway/quick-create-cli.md) Met de [volgende opdracht az network application-gateway create][az-network-application-gateway-create] maakt u een gateway met een openbaar front-end-IP-adres en een route naar de back-endcontainergroep. Zie de [Application Gateway voor](../application-gateway/index.yml) meer informatie over de gatewayinstellingen.

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


Het kan tot 15 minuten duren voordat Azure de toepassingsgateway heeft maken. 

## <a name="test-public-ip-address"></a>Openbaar IP-adres testen
  
U kunt nu de toegang testen tot de web-app die wordt uitgevoerd in de containergroep achter de toepassingsgateway.

Voer de [opdracht az network public-ip show][az-network-public-ip-show] uit om het openbare IP-adres van de front-end van de gateway op te halen:

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

Uitvoer is een openbaar IP-adres, vergelijkbaar met: `52.142.18.133` .

Als u de web-app wilt weergeven wanneer deze is geconfigureerd, gaat u naar het openbare IP-adres van de gateway in uw browser. Geslaagde toegang is vergelijkbaar met:

![Schermafbeelding van browser met toepassing die wordt uitgevoerd in een exemplaar van een Azure-container](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>Volgende stappen

* Zie een [quickstart-sjabloon voor](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) het maken van een containergroep met een WordPress-containerin instance als een back-endserver achter een toepassingsgateway.
* U kunt ook een toepassingsgateway configureren met een certificaat voor SSL-beëindiging. Zie het [overzicht en](../application-gateway/ssl-overview.md) de [zelfstudie](../application-gateway/create-ssl-portal.md).
* Afhankelijk van uw scenario kunt u andere Azure-taakverdelingsoplossingen gebruiken met Azure Container Instances. Gebruik bijvoorbeeld [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) om verkeer te verdelen over meerdere container-exemplaren en over meerdere regio's. Zie dit [blogbericht](https://aaronmsft.com/posts/azure-container-instances/).

[az-network-vnet-create]:  /cli/azure/network/vnet#az_network_vnet_create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az_network_public_ip_show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az_network-application-gateway-create
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
