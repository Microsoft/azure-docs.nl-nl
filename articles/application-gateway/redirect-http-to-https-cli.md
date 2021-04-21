---
title: HTTP-naar-HTTPS-omleiding met CLI
titleSuffix: Azure Application Gateway
description: Meer informatie over het maken van een HTTP-naar-HTTPS-omleiding en het toevoegen van een certificaat voor TLS-beëindiging met behulp van de Azure CLI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 09/24/2020
ms.author: victorh
ms.openlocfilehash: e66eca305433a89496f72aac667512efd418a369
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784763"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-cli"></a>Een toepassingsgateway maken met HTTP-naar-HTTPS-omleiding met behulp van de Azure CLI

U kunt de Azure CLI gebruiken om een [toepassingsgateway te](overview.md) maken met een certificaat voor TLS/SSL-beëindiging. Een regel voor doorsturen wordt gebruikt om HTTP-verkeer om te leiden naar de HTTPS-poort in uw toepassingsgateway. In dit voorbeeld maakt u ook een [virtuele-machineschaalset](../virtual-machine-scale-sets/overview.md) voor de back-endpool van de toepassingsgateway die twee exemplaren van virtuele machines bevat.

In dit artikel leert u het volgende:

* Een zelfondertekend certificaat maken
* Een netwerk instellen
* Een toepassingsgateway maken met behulp van het certificaat
* Een listener en omleidingsregel toevoegen
* Een virtuele-machineschaalset maken met de standaard back-endgroep

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze zelfstudie is versie 2.0.4 of hoger van de Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken

Voor productiegebruik moet u een geldig certificaat importeren dat is ondertekend door een vertrouwde provider. U maakt voor deze zelfstudie een zelfondertekend certificaat en pfx-bestand via de openssl-opdracht.

```console
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out appgwcert.crt
```

Voer waarden in die voor uw certificaat van belang zijn. U kunt de standaardwaarden accepteren.

```console
openssl pkcs12 -export -out appgwcert.pfx -inkey privateKey.key -in appgwcert.crt
```

Voer het wachtwoord voor het certificaat in. In dit voorbeeld wordt *Azure123456!* gebruikt.

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
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway"></a>De toepassingsgateway maken

U kunt [az network application-gateway create](/cli/azure/network/application-gateway#az_network_application_gateway_create) gebruiken om de toepassingsgateway *myAppGateway* te maken. Als u met de Azure CLI een toepassingsgateway maakt, geeft u configuratiegegevens op, zoals capaciteit, SKU en HTTP-instellingen. 

De toepassingsgateway wordt toegewezen aan *myAGSubnet* en *myAGPublicIPAddress*, die u eerder hebt gemaakt. In dit voorbeeld koppelt u het certificaat dat u hebt gemaakt aan het wachtwoord als u de toepassingsgateway maakt. 

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
  --frontend-port 443 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress \
  --cert-file appgwcert.pfx \
  --cert-password "Azure123456!"

```

 Het kan enkele minuten duren voordat de toepassingsgateway is gemaakt. Nadat de toepassingsgateway is gemaakt, kunt u de volgende nieuwe functies ervan zien:

- *appGatewayBackendPool*: een toepassingsgateway moet ten minste één back-endadresgroep hebben.
- *appGatewayBackendHttpSettings*: hiermee wordt aangegeven dat voor de communicatie poort 80 en een HTTP-protocol worden gebruikt.
- *appGatewayHttpListener*: de standaard-listener die aan *appGatewayBackendPool* is gekoppeld.
- *appGatewayFrontendIP*: hiermee wordt *myAGPublicIPAddress* aan *appGatewayHttpListener* toegewezen.
- *rule1*: de standaardrouteringsregel die aan *appGatewayHttpListener* is gekoppeld.

## <a name="add-a-listener-and-redirection-rule"></a>Een listener en omleidingsregel toevoegen

### <a name="add-the-http-port"></a>De HTTP-poort toevoegen

U kunt [az network application-gateway frontend-port create](/cli/azure/network/application-gateway/frontend-port#az_network-application_gateway_frontend_port_create) gebruiken om de HTTP-poort toe te voegen aan de toepassingsgateway.

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name httpPort
```

### <a name="add-the-http-listener"></a>De HTTP-listener toevoegen

U kunt [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener#az_network_application_gateway_http_listener_create) gebruiken om de listener met de naam *myListener* toe te voegen aan de toepassingsgateway.

```azurecli-interactive
az network application-gateway http-listener create \
  --name myListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port httpPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-redirection-configuration"></a>De omleidingsconfiguratie toevoegen

Voeg de configuratie van de HTTP-naar-HTTPS-omleiding toe aan de toepassingsgateway [met az network application-gateway redirect-config create.](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create)

```azurecli-interactive
az network application-gateway redirect-config create \
  --name httpToHttps \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener appGatewayHttpListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-the-routing-rule"></a>De routeringsregel toevoegen

Voeg de regel voor doorsturen *met de naam rule2* met de omleidingsconfiguratie toe aan de toepassingsgateway met [az network application-gateway rule create.](/cli/azure/network/application-gateway/rule#az_network_application_gateway_rule_create)

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener myListener \
  --rule-type Basic \
  --redirect-config httpToHttps
```

## <a name="create-a-virtual-machine-scale-set"></a>Een virtuele-machineschaalset maken

In dit voorbeeld maakt u een virtuele-machineschaalset met de naam *myvmss* die servers levert voor de back-endpool in de toepassingsgateway. De virtuele machines in de schaalset worden gekoppeld aan *myBackendSubnet* en *appGatewayBackendPool*. U kunt [az vmss create](/cli/azure/vmss#az_vmss_create) gebruiken om de schaalset te maken.

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

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

Gebruik [az network public-ip show](/cli/azure/network/public-ip) om het openbare IP-adres van de toepassingsgateway op te halen. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Beveiligingswaarschuwing](./media/redirect-http-to-https-cli/application-gateway-secure.png)

Als u de beveiligingswaarschuwing wilt accepteren als u een zelf-ondertekend certificaat hebt gebruikt, selecteert u **Details** en vervolgens **Ga naar de webpagina.** Uw beveiligde NGINX-site wordt vervolgens weergegeven zoals in het volgende voorbeeld:

![Basis-URL testen in de toepassingsgateway](./media/redirect-http-to-https-cli/application-gateway-nginxtest.png)

## <a name="next-steps"></a>Volgende stappen

- [Een toepassingsgateway met interne omleiding maken met behulp van de Azure CLI](redirect-internal-site-cli.md)