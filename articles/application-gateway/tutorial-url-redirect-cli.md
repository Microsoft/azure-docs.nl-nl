---
title: 'Zelfstudie: Omleiding op basis van URL-pad met behulp van CLI'
titleSuffix: Azure Application Gateway
description: In deze zelfstudie leert u hoe u een toepassingsgateway kunt maken met verkeer dat is omgeleid op basis van een URL-pad, met behulp van Azure CLI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/05/2021
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 042cb42d16fdc5394940b62709b5d25187775014
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772645"
---
# <a name="tutorial-create-an-application-gateway-with-url-path-based-redirection-using-the-azure-cli"></a>Zelfstudie: Een toepassingsgateway maken met een omleiding op basis van een URL-pad met behulp van Azure CLI

U kunt Azure CLI gebruiken om [op een URL-pad gebaseerde routeringsregels](tutorial-url-route-cli.md) te configureren als u een [toepassingsgateway maakt](./overview.md). In deze zelfstudie maakt u back-endpools met behulp van [schaalsets voor virtuele machines](../virtual-machine-scale-sets/overview.md). Vervolgens maakt u URL-routeringsregels waardoor webverkeer wordt omgeleid naar de juiste back-endpool.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Het netwerk instellen
> * Een toepassingsgateway maken
> * Listeners en routeringsregels toevoegen
> * Schaalsets voor virtuele machines voor back-endpools maken

Het volgende voorbeeld toont siteverkeer afkomstig van de poorten 8080 en 8081 en dat wordt doorgestuurd naar dezelfde back-endpools:

![Voorbeeld van URL-routering](./media/tutorial-url-redirect-cli/scenario.png)

U kunt deze zelfstudie desgewenst volgen met behulp van [Azure PowerShell](tutorial-url-redirect-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze zelfstudie is versie 2.0.4 of hoger van de Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Maak een resourcegroep met de opdracht [az group create](/cli/azure/group).

In het volgende voorbeeld wordt de resourcegroep *myResourceGroupAG* gemaakt op de locatie *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Netwerkbronnen maken 

Maak het virtuele netwerk *myVNet* en het subnet *myAGSubnet* met [az network vnet create](/cli/azure/network/vnet). Vervolgens kunt u het subnet *myBackendSubnet*, dat voor de back-endservers vereist is, toevoegen met [az network vnet subnet create](/cli/azure/network/vnet/subnet). Maak het openbare IP-adres *myAGPublicIPAddress* met [az network public-ip create](/cli/azure/network/public-ip).

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

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

Gebruik [az network application-gateway create](/cli/azure/network/application-gateway) om de toepassingsgateway myAppGateway te maken. Als u met de Azure CLI een toepassingsgateway maakt, geeft u configuratiegegevens op, zoals capaciteit, SKU en HTTP-instellingen. De toepassingsgateway wordt toegewezen aan *myAGSubnet* en *myPublicIPAddress*, die u zojuist hebt gemaakt.

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

 Het kan enkele minuten duren voordat de toepassingsgateway is gemaakt. Nadat de toepassingsgateway is gemaakt, ziet u de volgende nieuwe functies:

- *appGatewayBackendPool*: een toepassingsgateway moet ten minste één back-endadresgroep hebben.
- *appGatewayBackendHttpSettings*: hiermee wordt aangegeven dat voor de communicatie poort 80 en een HTTP-protocol worden gebruikt.
- *appGatewayHttpListener*: de standaard-listener die aan *appGatewayBackendPool* is gekoppeld.
- *appGatewayFrontendIP*: hiermee wordt *myAGPublicIPAddress* aan *appGatewayHttpListener* toegewezen.
- *rule1*: de standaardrouteringsregel die aan *appGatewayHttpListener* is gekoppeld.


### <a name="add-backend-pools-and-ports"></a>Back-endpools en back-endpoorten toevoegen

U kunt de back-endadresgroepen *imagesBackendPool* en *videoBackendPool* toevoegen aan de toepassingsgateway met [az network application-gateway address-pool create](/cli/azure/network/application-gateway/address-pool). U voegt de front-endpoorten voor de pools toe met [az network application-gateway frontend-port create](/cli/azure/network/application-gateway/frontend-port). 

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
  --name bport

az network application-gateway frontend-port create \
  --port 8081 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name rport
```

## <a name="add-listeners-and-rules"></a>Listeners en regels toevoegen

### <a name="add-listeners"></a>Listeners toevoegen

Voeg back-endlisteners *backendListener* en *redirectedListener*, die voor het omleiden van verkeer nodig zijn, toe met [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener).


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port bport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway

az network application-gateway http-listener create \
  --name redirectedListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port rport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-default-url-path-map"></a>De standaardtoewijzing voor het URL-pad toevoegen

URL-padtoewijzingen zorgen ervoor dat specifieke URL's naar specifieke back-endpools worden omgeleid. U kunt de URL-padtoewijzingen *imagePathRule* en *videoPathRule* maken met [az network application-gateway url-path-map create](/cli/azure/network/application-gateway/url-path-map) en [az network application-gateway url-path-map rule create](/cli/azure/network/application-gateway/url-path-map/rule)

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name urlpathmap \
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
  --path-map-name urlpathmap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-redirection-configuration"></a>Omleidingsconfiguratie toevoegen

U kunt omleiding voor de listener configureren met [az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config).

```azurecli-interactive
az network application-gateway redirect-config create \
  --gateway-name myAppGateway \
  --name redirectConfig \
  --resource-group myResourceGroupAG \
  --type Found \
  --include-path true \
  --include-query-string true \
  --target-listener backendListener
```

### <a name="add-the-redirection-url-path-map"></a>Toewijzing voor het omleidings-URL-pad toevoegen

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name redirectpathmap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --redirect-config redirectConfig \
  --rule-name redirectPathRule
```

### <a name="add-routing-rules"></a>Routeringsregels toevoegen

De routeringsregels koppelen de URL-padtoewijzingen aan de listeners die u hebt gemaakt. U kunt regels *defaultRule* en *redirectedRule* toevoegen met [az network application-gateway rule create](/cli/azure/network/application-gateway/rule).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name defaultRule \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map urlpathmap \
  --address-pool appGatewayBackendPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectedRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectedListener \
  --rule-type PathBasedRouting \
  --url-path-map redirectpathmap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Virtuele-machineschaalset maken

In dit voorbeeld maakt u drie virtuele-machineschaalsets die ondersteuning bieden voor de drie back-endpools die u hebt gemaakt. De schaalsets die u maakt, hebben de namen *myvmss1*, *myvmss2* en *myvmss3*. Elke schaalset bevat twee exemplaren van virtuele machines waarop u NGINX installeert.

Vervang \<azure-user> en door een gebruikersnaam en wachtwoord van uw \<password> keuze.

```azurecli
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
    --admin-username <azure-user> \
    --admin-password <password> \
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

Gebruik [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) om het openbare IP-adres van de toepassingsgateway op te halen. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. Zoals `http://40.121.222.19`, `http://40.121.222.19:8080/images/test.htm`, `http://40.121.222.19:8080/video/test.htm` of `http://40.121.222.19:8081/images/test.htm`.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Basis-URL testen in de toepassingsgateway](./media/tutorial-url-redirect-cli/application-gateway-nginx.png)

Wijzig de URL in http://&lt;IP-adres&gt;:8080/images/test.html, waarbij u &lt;ip-adres&gt; vervangt door uw eigen IP-adres. U krijgt nu iets te zien zoals in het volgende voorbeeld:

![Afbeeldingen-URL in toepassingsgateway testen](./media/tutorial-url-redirect-cli/application-gateway-nginx-images.png)

Wijzig de URL in http://&lt;IP-adres&gt;:8080/video/test.html, waarbij u &lt;ip-adres&gt; vervangt door uw eigen IP-adres. U krijgt nu iets te zien zoals in het volgende voorbeeld:

![Video-URL testen in de toepassingsgateway](./media/tutorial-url-redirect-cli/application-gateway-nginx-video.png)

Wijzig nu de URL in http://&lt;IP-adres&gt;:8081/images/test.htm, waarbij u &lt;IP-adres&gt; vervangt door uw eigen IP-adres. U ziet dat het verkeer terug wordt omgeleid naar de back-endpool met afbeeldingen op http://&lt;IP-adres&gt;:8080/images.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep, de toepassingsgateway en alle gerelateerde resources verwijderen als u deze niet meer nodig hebt.

```azurecli-interactive
az group delete --name myResourceGroupAG
```
## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over wat u kunt doen met de toepassingsgateway](./overview.md)