---
title: Webverkeer routeren op basis van de URL-Azure CLI
description: In dit artikel wordt beschreven hoe u webverkeer routeert op basis van de URL naar specifieke schaal bare Pools van servers met behulp van de Azure CLI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 08/01/2019
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 8e8fed99fe0b1de52d2e2d0018dfd8867b54b63b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94566517"
---
# <a name="route-web-traffic-based-on-the-url-using-the-azure-cli"></a>Webverkeer routeren op basis van de URL met behulp van de Azure CLI

Als een IT-beheerder die webverkeer beheert, wilt u uw klanten of gebruikers helpen de informatie die ze nodig hebben zo snel mogelijk te verkrijgen. Een manier waarop u hun ervaring kunt optimaliseren is door verschillende soorten webverkeer naar verschillende serverbronnen te routeren. In dit artikel wordt beschreven hoe u de Azure CLI gebruikt om Application Gateway route ring in te stellen en te configureren voor verschillende soorten verkeer vanuit uw toepassing. De routering stuurt het verkeer vervolgens door naar verschillende servergroepen op basis van de URL.

![Voorbeeld van URL-routering](./media/tutorial-url-route-cli/scenario.png)

In dit artikel leert u het volgende:

* Een resourcegroep maken voor de netwerkresources die u nodig hebt
* De netwerkresources maken
* Een toepassingsgateway maken voor het verkeer dat afkomstig is van uw toepassing
* Servergroepen en regels voor doorsturen voor de verschillende soorten verkeer opgeven
* Een schaalset maken voor elke groep, zodat de toepassingen automatisch kunnen worden geschaald
* Een test uitvoeren zodat u kunt controleren of de verschillende soorten verkeer naar de juiste groep gaan

Als u wilt, kunt u deze procedure volt ooien met behulp van [Azure PowerShell](tutorial-url-route-powershell.md) of de [Azure Portal](create-url-route-portal.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze zelfstudie is versie 2.0.4 of hoger van de Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Maak een resourcegroep met behulp van `az group create`.

In het volgende voorbeeld wordt de resourcegroep *myResourceGroupAG* gemaakt op de locatie *eastus*.

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Netwerkbronnen maken

Maak het virtuele netwerk *myVNet* en het subnet *myAGSubnet* met `az network vnet create`. Vervolgens voegt u het subnet *myBackendSubnet*, dat voor de back-endservers vereist is, toe met `az network vnet subnet create`. Maak het openbare IP-adres *myAGPublicIPAddress* met `az network public-ip create`.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-the-app-gateway-with-a-url-map"></a>De app-gateway maken met een URL-toewijzing

Gebruik `az network application-gateway create` om een toepassingsgateway met de naam *myAppGateway* te maken. Als u met de Azure CLI een toepassingsgateway maakt, geeft u configuratiegegevens op, zoals capaciteit, SKU en HTTP-instellingen. De toepassingsgateway wordt toegewezen aan *myAGSubnet* en *myAGPublicIPAddress*.

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 Het kan enkele minuten duren om de toepassingsgateway te maken. Nadat de toepassingsgateway is gemaakt, ziet u de volgende nieuwe functies:


|Functie  |Beschrijving  |
|---------|---------|
|appGatewayBackendPool     |Een toepassingsgateway moet ten minste één back-endadresgroep hebben.|
|appGatewayBackendHttpSettings     |Hiermee wordt aangegeven dat voor de communicatie poort 80 en een HTTP-protocol worden gebruikt.|
|appGatewayHttpListener     |De standaard-listener die aan appGatewayBackendPool is gekoppeld|
|appGatewayFrontendIP     |Hiermee wordt myAGPublicIPAddress aan appGatewayHttpListener toegewezen.|
|rule1     |De standaardregel voor doorsturen die aan appGatewayHttpListener is gekoppeld.|

### <a name="add-image-and-video-backend-pools-and-a-port"></a>Back-endpools en een poort voor afbeeldingen en video toevoegen

Voeg de back-endpools *imagesBackendPool* en *videoBackendPool* toe aan de toepassingsgateway met `az network application-gateway address-pool create`. U voegt de front-endpoort toe met `az network application-gateway frontend-port create`.

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name imagesBackendPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name videoBackendPool

az network application-gateway frontend-port create \
  --port 8080 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name port8080
```

### <a name="add-a-backend-listener"></a>Een back-endlistener toevoegen

Voeg de back-endlistener met de naam *backendListener* die nodig is om verkeer te routeren toe met behulp van `az network application-gateway http-listener create`.


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-a-url-path-map"></a>Toewijzing van een URL-pad toevoegen

URL-padtoewijzingen zorgen ervoor dat specifieke URL's naar specifieke back-endpools worden omgeleid. Maak URL-padtoewijzingen met de naam *imagePathRule* en *videoPathRule* met behulp van `az network application-gateway url-path-map create` en `az network application-gateway url-path-map rule create`.

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name myPathMap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --address-pool imagesBackendPool \
  --default-address-pool appGatewayBackendPool \
  --default-http-settings appGatewayBackendHttpSettings \
  --http-settings appGatewayBackendHttpSettings \
  --rule-name imagePathRule

az network application-gateway url-path-map rule create \
  --gateway-name myAppGateway \
  --name videoPathRule \
  --resource-group myResourceGroupAG \
  --path-map-name myPathMap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-a-routing-rule"></a>Een routeringsregel toevoegen

De routeringsregel koppelt de URL-toewijzingen aan de listener die u hebt gemaakt. Voeg een regel met de naam *rule2* toe met behulp van `az network application-gateway rule create`.

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map myPathMap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Virtuele-machineschaalset maken

In dit artikel maakt u drie virtuele-machine schaal sets die ondersteuning bieden voor de drie back-endservers die u hebt gemaakt. U maakt schaalsets met de namen *myvmss1*, *myvmss2* en *myvmss3*. Elke schaalset bevat twee exemplaren van virtuele machines waarop u NGINX installeert.

```azurecli-interactive
for i in `seq 1 3`; do

  if [ $i -eq 1 ]
  then
    poolName="appGatewayBackendPool" 
  fi

  if [ $i -eq 2 ]
  then
    poolName="imagesBackendPool"
  fi

  if [ $i -eq 3 ]
  then
    poolName="videoBackendPool"
  fi

  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>NGINX installeren

```azurecli-interactive
for i in `seq 1 3`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

Gebruik az network public-ip show om het openbare IP-adres van de toepassingsgateway op te halen. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. Zoals,, `http://40.121.222.19` `http://40.121.222.19:8080/images/test.htm` of `http://40.121.222.19:8080/video/test.htm` .

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Basis-URL testen in de toepassingsgateway](./media/tutorial-url-route-cli/application-gateway-nginx.png)

Wijzig de URL in http://&lt;IP-adres&gt;:8080/images/test.html, waarbij u &lt;ip-adres&gt; vervangt door uw eigen IP-adres. U krijgt nu iets te zien zoals in het volgende voorbeeld:

![Afbeeldingen-URL in toepassingsgateway testen](./media/tutorial-url-route-cli/application-gateway-nginx-images.png)

Wijzig de URL in http:// &lt; IP-adres &gt; : 8080/video/test.html en vervang uw IP-adres voor &lt; IP-adres &gt; en u ziet iets als in het volgende voor beeld.

![Video-URL testen in de toepassingsgateway](./media/tutorial-url-route-cli/application-gateway-nginx-video.png)

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep, de toepassingsgateway en alle gerelateerde resources verwijderen als u deze niet meer nodig hebt.

```azurecli-interactive
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Volgende stappen

[Een toepassingsgateway maken met een omleiding op basis van een URL-pad](./tutorial-url-redirect-cli.md)
