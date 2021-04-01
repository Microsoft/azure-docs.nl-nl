---
title: 'Quickstart: Webverkeer omleiden met behulp van CLI'
titleSuffix: Azure Application Gateway
description: In deze quickstart leest u hoe u Azure CLI gebruikt om een Azure Application Gateway te maken die webverkeer doorstuurt naar virtuele machines in een back-endpool.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 01/19/2021
ms.author: victorh
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 1a691d8ab212dd436b0dc9f7aafbc19a406b12b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98601785"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Quickstart: Webverkeer omleiden met Azure Application Gateway - Azure CLI

In deze quickstart gebruikt u Azure CLI om een toepassingsgateway te maken. Vervolgens test u de toepassing om te controleren of alles correct werkt. 

De toepassingsgateway stuurt webverkeer van toepassingen naar specifieke resources in een back-endpool. U wijst listeners toe aan poorten, maakt regels en voegt resources toe aan een back-endpool. Dit artikel gebruikt een eenvoudige configuratie met een openbaar IP-adres voor de front-end, een eenvoudige listener om een enkele site op de toepassingsgateway te hosten, een eenvoudige regel voor doorsturen van aanvragen en twee virtuele machines in de back-endpool.

U kunt deze quickstart ook uitvoeren met [Azure PowerShell](quick-create-powershell.md) of [Azure Portal](quick-create-portal.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.4 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-resource-group"></a>Een resourcegroep maken

In Azure kunt u verwante resources toewijzen aan een resourcegroep. Maak een resourcegroep met behulp van `az group create`. 

In het volgende voorbeeld wordt de resourcegroep *myResourceGroupAG* gemaakt op de locatie *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Netwerkbronnen maken 

Er is een virtueel netwerk nodig voor communicatie tussen de resources die u maakt.  Het subnet van de toepassingsgateway kan alleen bestaan uit toepassingsgateways. Andere resources zijn niet toegestaan.  U kunt een nieuw subnet maken voor Application Gateway of een bestaand subnet gebruiken. In dit voorbeeld maakt u twee subnetten: één voor de toepassingsgateway en één voor de back-endservers. Afhankelijk van uw toepassing kunt u het IP-adres voor de front-end van de toepassingsgateway als openbaar of privé configureren. In dit voorbeeld kiest u voor een openbaar IP-adres voor de front-end.

Gebruik `az network vnet create` om het virtuele netwerk en subnet te maken. Voer `az network public-ip create` uit om het openbare IP-adres te maken.

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
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-the-backend-servers"></a>De back-endservers maken

Een back-end kan NIC's, virtuele-machineschaalsets, openbare IP-adressen, interne IP-adressen, FQDN's (Fully Qualified Domain Name) en multitenant back-ends, zoals Azure App Service, bevatten. In dit voorbeeld maakt u twee virtuele machines die worden gebruikt als back-endservers voor de toepassingsgateway. U installeert ook IIS op de virtuele machines om de toepassingsgateway te testen.

#### <a name="create-two-virtual-machines"></a>Twee virtuele machines maken

Installeer de NGINX-webserver op de virtuele machines om te controleren of de toepassingsgateway is gemaakt. U kunt een cloud-init-configuratiebestand maken om NGINX te installeren en een 'Hallo wereld' Node.js-app uit te voeren op een virtuele Linux-machine. Zie [Cloud-init-ondersteuning voor virtuele machines in Azure](../virtual-machines/linux/using-cloud-init.md) voor meer informatie over cloud-init.

Kopieer in uw Azure Cloud Shell de volgende configuratie en plak deze in een bestand met de naam *cloud-init.txt*. Voer *editor cloud-init.txt* in om het bestand te maken.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Maak de netwerkinterfaces met `az network nic create`. Voor het maken van de virtuele machines gebruikt u `az vm create`.

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>De toepassingsgateway maken

Maak een toepassingsgateway met `az network application-gateway create`. Als u met de Azure CLI een toepassingsgateway maakt, geeft u configuratiegegevens op, zoals capaciteit, SKU en HTTP-instellingen. Azure voegt dan de privé-IP-adressen van de netwerkinterfaces toe als servers in de back-endpool van de toepassingsgateway.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Het kan tot 30 minuten duren om de toepassingsgateway te maken in Azure. Wanneer deze is gemaakt, kunt u de volgende instellingen bekijken in het gedeelte **Instellingen** van de pagina **Toepassingsgateway**:

- **appGatewayBackendPool**: Staat op de pagina **back-endpools**. Hier is te zien wat de vereiste back-endpool is.
- **appGatewayBackendHttpSettings**: Staat op de pagina **HTTP-instellingen**. Hier wordt aangegeven dat de toepassingsgateway voor communicatie gebruikmaakt van poort 80 en het HTTP-protocol.
- **appGatewayHttpListener**: Staat op de **pagina Listeners**. Hier staat wat de standaard-listener is die aan **appGatewayBackendPool** is gekoppeld.
- **appGatewayFrontendIP**: Staat op de pagina **Front-end-IP-configuraties**. Hiermee wordt *myAGPublicIPAddress* aan **appGatewayHttpListener** toegewezen.
- **rule1**: Staat op de pagina **Regels**. Hier wordt aangegeven welke standaardrouteringsregel aan **appGatewayHttpListener** is gekoppeld.

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

Het is in Azure niet nodig een NGINX-webserver te installeren om de toepassingsgateway te maken, maar u hebt de server in deze quickstart geïnstalleerd om te controleren of het maken van de toepassingsgateway in Azure is geslaagd. Gebruik `az network public-ip show` om het openbare IP-adres van de nieuwe toepassingsgateway op te halen. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Kopieer het openbare IP-adres en plak het in de adresbalk van de browser.
    
![Toepassingsgateway testen](./media/quick-create-cli/application-gateway-nginxtest.png)

Als u de browser vernieuwt, ziet u de naam van de tweede VM. Dit geeft aan dat de toepassingsgateway is gemaakt en dat deze verbinding kan maken met de back-end.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources die u bij de toepassingsgateway hebt gemaakt niet meer nodig hebt, gebruikt u de opdracht `az group delete` om de resourcegroep te verwijderen. Wanneer u de resourcegroep verwijdert, verwijdert u ook de toepassingsgateway en alle resources die hieraan zijn gerelateerd.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Webverkeer met een toepassingsgateway beheren met behulp van Azure CLI](./tutorial-manage-web-traffic-cli.md)

