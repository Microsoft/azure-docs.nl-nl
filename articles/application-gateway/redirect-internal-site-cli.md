---
title: Interne omleiding met CLI
titleSuffix: Azure Application Gateway
description: Meer informatie over het maken van een toepassings gateway die intern webverkeer omleidt naar de juiste pool met behulp van de Azure CLI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: b443fa7c2d6c644fc1173295f89813c18657d160
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94566721"
---
# <a name="create-an-application-gateway-with-internal-redirection-using-the-azure-cli"></a>Een toepassings gateway met interne omleiding maken met behulp van Azure CLI

U kunt de Azure CLI gebruiken voor het configureren van [omleiding van webverkeer](multiple-site-overview.md) wanneer u een [toepassings gateway](overview.md)maakt. In deze zelf studie definieert u een back-end-pool met behulp van een schaalset voor virtuele machines. Vervolgens configureert u listeners en regels op basis van domeinen waarvan u de eigenaar bent om ervoor te zorgen dat webverkeer bij de juiste groep arriveert. In deze zelf studie wordt ervan uitgegaan dat u beschikt over meerdere domeinen en gebruikmaakt van voor beelden van *www- \. contoso.com* en *www- \. contoso.org*.

In dit artikel leert u het volgende:

* Het netwerk instellen
* Een toepassingsgateway maken
* Listeners en omleidings regel toevoegen
* Een schaalset voor virtuele machines maken met de back-end-groep
* Een CNAME-record in uw domein maken

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

Maak het virtuele netwerk *myVNet* en het subnet *myAGSubnet* met [az network vnet create](/cli/azure/network/vnet). U kunt vervolgens het subnet met de naam *myBackendSubnet* toevoegen dat nodig is voor de back-end-groep van servers met [AZ Network vnet subnet Create](/cli/azure/network/vnet/subnet). Maak het openbare IP-adres *myAGPublicIPAddress* met [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create).

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
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

U kunt [az network application-gateway create](/cli/azure/network/application-gateway) gebruiken om de toepassingsgateway *myAppGateway* te maken. Als u met de Azure CLI een toepassingsgateway maakt, geeft u configuratiegegevens op, zoals capaciteit, SKU en HTTP-instellingen. De toepassingsgateway wordt toegewezen aan *myAGSubnet* en *myAGPublicIPAddress*, die u eerder hebt gemaakt. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

Het kan enkele minuten duren voordat de toepassingsgateway is gemaakt. Nadat de toepassingsgateway is gemaakt, kunt u de volgende nieuwe functies ervan zien:

- *appGatewayBackendPool*: een toepassingsgateway moet ten minste één back-endadresgroep hebben.
- *appGatewayBackendHttpSettings*: hiermee wordt aangegeven dat voor de communicatie poort 80 en een HTTP-protocol worden gebruikt.
- *appGatewayHttpListener*: de standaard-listener die aan *appGatewayBackendPool* is gekoppeld.
- *appGatewayFrontendIP*: hiermee wordt *myAGPublicIPAddress* aan *appGatewayHttpListener* toegewezen.
- *rule1*: de standaardrouteringsregel die aan *appGatewayHttpListener* is gekoppeld.


## <a name="add-listeners-and-rules"></a>Listeners en regels toevoegen 

Een listener is vereist om de toepassingsgateway in te schakelen om het verkeer op de juiste manier naar de back-endpool door te sturen. In deze zelfstudie maakt u twee listeners voor de twee domeinen. In dit voor beeld worden listeners gemaakt voor de domeinen www- *\. contoso.com* en *www- \. contoso.org*.

Voeg de back-endlisteners, die voor het omleiden van verkeer nodig zijn, toe met [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener#az-network-application-gateway-http-listener-create).

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoComListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name contosoOrgListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.org   
  ```

### <a name="add-the-redirection-configuration"></a>De configuratie van de omleiding toevoegen

Voeg de omleidings configuratie toe die verkeer van *www- \. consoto.org* naar de listener verzendt voor *www- \. contoso.com* in de toepassings gateway met [AZ Network Application-Gateway redirect-config Create](/cli/azure/network/application-gateway/redirect-config#az-network-application-gateway-redirect-config-create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name orgToCom \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener contosoListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-routing-rules"></a>Routeringsregels toevoegen

Regels worden verwerkt in de volg orde waarin ze worden gemaakt en verkeer wordt omgeleid met behulp van de eerste regel die overeenkomt met de URL die wordt verzonden naar de toepassings gateway. Als u bijvoorbeeld een regel hebt die van een basislistener gebruikmaakt en een regel die via dezelfde poort van een listener voor meerdere sites gebruikmaakt, moet de regel voor de listener voor meerdere sites vermeld worden vóór de regel met de basislistener, opdat de regel voor meerdere sites kan functioneren zoals het hoort. 

In dit voor beeld maakt u twee nieuwe regels en verwijdert u de standaard regel die is gemaakt.  U kunt de regel toevoegen met [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az-network-application-gateway-rule-create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoComRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoComListener \
  --rule-type Basic \
  --address-pool appGatewayBackendPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoOrgRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoOrgListener \
  --rule-type Basic \
  --redirect-config orgToCom
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Virtuele-machineschaalset maken

In dit voor beeld maakt u een schaalset voor virtuele machines die ondersteuning biedt voor de back-end-groep die u hebt gemaakt. De schaalset die u maakt, heeft de naam *myvmss* en bevat twee exemplaren van virtuele machines waarop u NGINX installeert.

```azurecli-interactive
az vmss create \
  --name myvmss \
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
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>NGINX installeren

Voer deze opdracht uit in het shell-venster:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-cname-record-in-your-domain"></a>CNAME-record in uw domein maken

Als de toepassingsgateway met het bijbehorende openbare IP-adres is gemaakt, kunt u het DNS-adres ophalen en dit gebruiken om een CNAME-record in uw domein te maken. Gebruik [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) om het DNS-adres van de toepassingsgateway op te halen. Kopieer de waarde *fqdn* van DNSSettings en gebruik deze als de waarde van de CNAME-record die u maakt. Het gebruik van A-records wordt niet aanbevolen, omdat de VIP kan veranderen wanneer de toepassingsgateway opnieuw wordt gestart.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

Voer uw domeinnaam in de adresbalk van de browser in. Zoals http: \/ /www.contoso.com.

![Contoso-site testen in toepassingsgateway](./media/redirect-internal-site-cli/application-gateway-nginxtest.png)

Wijzig het adres in uw andere domein, bijvoorbeeld http: \/ /www.contoso.org, en u ziet dat het verkeer is omgeleid naar de listener voor www- \. contoso.com.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> * Het netwerk instellen
> * Een toepassingsgateway maken
> * Listeners en omleidings regel toevoegen
> * Een schaalset voor virtuele machines maken met de back-end-groep
> * Een CNAME-record in uw domein maken
