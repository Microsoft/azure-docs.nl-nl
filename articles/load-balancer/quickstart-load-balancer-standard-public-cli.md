---
title: 'Quickstart: Een openbare load balancer maken - Azure CLI'
titleSuffix: Azure Load Balancer
description: In deze snelstart vindt u meer informatie over het maken van een openbare load balancer met Azure CLI
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: c66ecceea770ec32e907a9bdc21fff29cf6aa453
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698504"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>Quickstart: Een openbare load balancer maken om taken van VM's te verdelen met behulp van Azure CLI

Ga aan de slag met Azure Load Balancer via de Azure CLI om een openbare load balancer en drie virtuele machines te maken.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor deze quickstart is versie 2.0.28 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resourcegroep maken met [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create):

* met de naam **CreatePubLBQS-rg**. 
* Op de locatie **eastus**.

```azurecli-interactive
  az group create \
    --name CreatePubLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**Standaard SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Voor productieworkloads wordt de load balancer uit de Standard SKU aanbevolen. Zie **[Azure Load Balancer-SKU's](skus.md)** voor meer informatie over SKU's.

## <a name="configure-virtual-network"></a>Virtueel netwerk configureren

Voordat u VM's implementeert en uw load balancer test, moet u de ondersteunende virtuele-netwerkresources maken.

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een Virtual Network met [az network vnet create](/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt).

* Met de naam **myVNet**.
* Adresvoorvoegsel van **10.1.0.0/16**.
* Subnet met de naam **myBackendSubnet**.
* Subnetvoorvoegsel van **10.1.0.0/24**.
* In de **CreatePubLBQS-rg**-resourcegroep.
* Locatie **VS - Oost**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Voor Standard Load Balancer moeten de VM's in het back-endadres netwerkinterfaces bevatten die tot een netwerkbeveiligingsgroep behoren. 

Maak een netwerkbeveiligingsgroep met [az network nsg create](/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create):

* Met de naam **myNSG**.
* In resourcegroep **CreatePubLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Regel voor netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroepregel met behulp van [az network nsg rule create](/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create):

* Met de naam **myNSGRuleHTTP**.
* In de netwerkbeveiligingsgroep die u in de vorige stap hebt gemaakt, **myNSG**.
* In resourcegroep **CreatePubLBQS-rg**.
* Protocol **(*)** .
* Richting **Inkomend**.
* Bron **(*)** .
* Doel **(*)** .
* Doelpoort **Poort 80**.
* Toegang **Toestaan**.
* Prioriteit **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Netwerkinterfaces maken voor de virtuele machines

Maak drie netwerkinterfaces met de opdracht [az network nic create](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

#### <a name="vm1"></a>VM1

* Met de naam **myNicVM1**.
* In resourcegroep **CreatePubLBQS-rg**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* In netwerkbeveiligingsgroep **myNSG**.

```azurecli-interactive

  az network nic create \
    --resource-group CreatePubLBQS-rg \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* Met de naam **myNicVM2**.
* In resourcegroep **CreatePubLBQS-rg**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.

```azurecli-interactive
  az network nic create \
    --resource-group CreatePubLBQS-rg \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm3"></a>VM3

* Met de naam **myNicVM3**.
* In resourcegroep **CreatePubLBQS-rg**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* In netwerkbeveiligingsgroep **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreatePubLBQS-rg \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Back-endservers maken

In deze sectie maakt u:

* Een cloudconfiguratiebestand met de naam **cloud-init.txt** voor de serverconfiguratie.
* Drie virtuele machines die worden gebruikt als back-endservers voor de load balancer.

### <a name="create-cloud-init-configuration-file"></a>Een cloud-init-configuratiebestand maken

Gebruik een cloud-init-configuratiebestand om NGINX te installeren en een 'Hallo wereld' Node.js-app uit te voeren op een virtuele Linux-machine. 

Maak in uw huidige shell een bestand met de naam cloud-init.txt. Kopieer en plak de volgende configuratie van de shell. Zorg ervoor dat u het hele cloud-init-bestand correct kopieert, met name de eerste regel:

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
### <a name="create-virtual-machines"></a>Virtuele machines maken

Maak de virtuele machines met [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create):

#### <a name="vm1"></a>VM1
* Met de naam **myVM1**.
* In resourcegroep **CreatePubLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM1**.
* VM-installatiekopie **UbuntuLTS**.
* Configuratiebestand **cloud-init.txt** dat u in de bovenstaande stap heeft gemaakt.
* In **Zone 1**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 1 \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* Met de naam **myVM2**.
* In resourcegroep **CreatePubLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM2**.
* VM-installatiekopie **UbuntuLTS**.
* Configuratiebestand **cloud-init.txt** dat u in de bovenstaande stap heeft gemaakt.
* In **Zone 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

#### <a name="vm3"></a>VM3
* Met de naam **myVM3**.
* In resourcegroep **CreatePubLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM3**.
* VM-installatiekopie **UbuntuLTS**.
* Configuratiebestand **cloud-init.txt** dat u in de bovenstaande stap heeft gemaakt.
* In **Zone 3**.

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 3 \
    --no-wait
```
Het kan enkele minuten duren om de VM's te implementeren.

## <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Om toegang te krijgen tot uw web-app op internet, hebt u een openbaar IP-adres nodig voor de load balancer. 

Gebruik [az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) om:

* Maak een standaard zoneredundant openbaar IP-adres met de naam **myPublicIP**.
* In **CreatePubLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard
```

Als u een zonegebonden redundant openbaar IP-adres in Zone 1 wilt maken:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard \
    --zone 1
```

## <a name="create-standard-load-balancer"></a>Een Standard Load Balancer maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de load balancer kunt maken en configureren:

  * Een front-end-IP-pool die het binnenkomende netwerkverkeer op de load balancer ontvangt.
  * Een back-end-IP-pools waar de front-endpool het netwerkverkeer naartoe stuurt dat door de load balancer is verdeeld.
  * Een statustest die de status van de back-end-VM-instanties vaststelt.
  * Een load balancer-regel die bepaalt hoe het verkeer over de VM's wordt verdeeld.

### <a name="create-the-load-balancer-resource"></a>De load balancer-resource maken

Maak een openbare load balancer met [az network lb create](/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create):

* Genaamd **myLoadBalancer**.
* Een front-endgroep met de naam **myFrontEnd**.
* Een back-endgroep met de naam MyBackendPool.
* Gekoppeld met het openbare IP-adres **myPublicIP** dat u in de vorige stap hebt gemaakt. 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Statustest maken

Met een statustest worden alle VM-instanties gecontroleerd om na te gaan of ze netwerkverkeer kunnen verzenden. 

Een virtuele machine met een mislukte test wordt verwijderd uit de load balancer. De virtuele machine wordt weer toegevoegd aan de load balancer wanneer de fout is opgelost.

Gebruik een statustest met [az network lb probe create](/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create):

* Bewaakt de status van de virtuele machines.
* Met de naam **myHealthProbe**.
* Protocol: **TCP**.
* Controleert **poort 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Load balancer-regel maken

Met een load balancer-regel wordt het volgende gedefinieerd:

* De front-end-IP-configuratie voor het binnenkomende verkeer.
* De back-end-IP-adresgroep voor het ontvangen van verkeer.
* De vereiste bron- en doelpoort. 

Gebruik [az network lb rule create](/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) om een load balancer-regel te maken:

* Naam: **myHTTPRule**
* Luistert aan **poort 80** in de front-endpool **myFrontEnd**.
* Verzendt netwerkverkeer volgens taakverdeling naar de back-endadresgroep **myBackEndPool** via **poort 80**. 
* Gebruikt statustest **myHealthProbe**.
* Protocol: **TCP**.
* Time-out voor inactiviteit: **15 minuten**.
* Schakel TCP opnieuw instellen in.


```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true

```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Virtuele machines toevoegen aan back-endpool van load balancer

Voeg de virtuele machines aan de back-endpool toe met [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):

#### <a name="vm1"></a>VM1
* In back-endadrespool **myBackEndPool**.
* In resourcegroep **CreatePubLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM1** en **ipconfig1**.
* Gekoppeld aan load balancer **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreatePubLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* In back-endadrespool **myBackEndPool**.
* In resourcegroep **CreatePubLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM2** en **ipconfig1**.
* Gekoppeld aan load balancer **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreatePubLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* In back-endadrespool **myBackEndPool**.
* In resourcegroep **CreatePubLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM3** en **ipconfig1**.
* Gekoppeld aan load balancer **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group CreatePubLBQS-rg \
   --lb-name myLoadBalancer
```

## <a name="create-outbound-rule-configuration"></a>Configuratie voor uitgaande regel maken
Uitgaande regels van load balancers configureren uitgaande SNAT voor virtuele machines in de back-endgroep. 

Zie [Uitgaande verbindingen in Azure](load-balancer-outbound-connections.md) voor meer informatie over uitgaande verbindingen.

### <a name="create-outbound-public-ip-address-or-public-ip-prefix"></a>Maak een uitgaand openbaar IP-adres of een voorvoegsel van een uitgaand openbaar IP-adres.

Gebruik [az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) om één IP-adres voor de uitgaande verbinding te maken.  

Gebruik [az network public-ip prefix create](/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create) om een voorvoegsel van een openbaar IP-adres voor de uitgaande verbinding te maken.

Zie [Uitgaande NAT schalen met meerdere IP-adressen](load-balancer-outbound-connections.md) voor meer informatie over het schalen van uitgaande NAT en uitgaande verbindingen.

#### <a name="public-ip"></a>Openbare IP

* Met de naam **myPublicIPOutbound**.
* In **CreatePubLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard
```

Als u een zonegebonden redundant openbaar IP-adres in Zone 1 wilt maken:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```
#### <a name="public-ip-prefix"></a>Voorvoegsel voor het openbare IP-adres

* Met de naam **myPublicIPPrefixOutbound**.
* In **CreatePubLBQS-rg**.
* Voorvoegsellengte van **28**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28
```
Als u een zonegebonden redundant openbaar IP-voorvoegsel in Zone 1 wilt maken:

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

### <a name="create-outbound-frontend-ip-configuration"></a>Uitgaande front-end-IP-configuratie maken

Een nieuwe front-end-IP-configuratie maken met [az network lb frontend-ip create ](/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create):

Selecteer de opdrachten voor het openbare IP-adres of het openbare IP-voorvoegsel op basis van de beslissing in de vorige stap.

#### <a name="public-ip"></a>Openbare IP

* Met de naam **myFrontEndOutbound**.
* In resourcegroep **CreatePubLBQS-rg**.
* Gekoppeld aan openbaar IP-adres **myPublicIPOutbound**.
* Gekoppeld aan load balancer **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>Voorvoegsel voor het openbare IP-adres

* Met de naam **myFrontEndOutbound**.
* In resourcegroep **CreatePubLBQS-rg**.
* Gekoppeld aan het openbare IP-voorvoegsel **myPublicIPPrefixOutbound**.
* Gekoppeld aan load balancer **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>Uitgaande groep maken

Maak een nieuwe uitgaande groep met [az network lb address-pool create](/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create):

* Met de naam **myBackEndPoolOutbound**.
* In resourcegroep **CreatePubLBQS-rg**.
* Gekoppeld aan load balancer **myLoadBalancer**.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>Uitgaande regel maken

Maak een nieuwe uitgaande regel voor de uitgaande back-endgroep met [az network lb outbound-rule create](/cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-create):

* Met de naam **myOutboundRule**.
* In resourcegroep **CreatePubLBQS-rg**.
* Gekoppeld aan load balancer **myLoadBalancer**
* Gekoppeld aan front-end **myFrontEndOutbound**.
* Protocol: **Alle**.
* Time-out voor inactiviteit: **15**.
* **10.000** uitgaande poorten.
* Gekoppeld aan back-endgroep **myBackEndPoolOutbound**.

```azurecli-interactive
  az network lb outbound-rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myOutboundRule \
    --frontend-ip-configs myFrontEndOutbound \
    --protocol All \
    --idle-timeout 15 \
    --outbound-ports 10000 \
    --address-pool myBackEndPoolOutbound
```
### <a name="add-virtual-machines-to-outbound-pool"></a>Virtuele machines toevoegen aan uitgaande groep

Voeg de virtuele machines aan de uitgaande groep toe met [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):


#### <a name="vm1"></a>VM1
* In back-endadresgroep **myBackEndPoolOutbound**.
* In resourcegroep **CreatePubLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM1** en **ipconfig1**.
* Gekoppeld aan load balancer **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreatePubLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* In back-endadresgroep **myBackEndPoolOutbound**.
* In resourcegroep **CreatePubLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM2** en **ipconfig1**.
* Gekoppeld aan load balancer **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreatePubLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* In back-endadresgroep **myBackEndPoolOutbound**.
* In resourcegroep **CreatePubLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM3** en **ipconfig1**.
* Gekoppeld aan load balancer **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group CreatePubLBQS-rg \
   --lb-name myLoadBalancer
```

# <a name="basic-sku"></a>[**Basis-SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Voor productieworkloads wordt de load balancer uit de Standard SKU aanbevolen. Zie **[Azure Load Balancer-SKU's](skus.md)** voor meer informatie over SKU's.

## <a name="configure-virtual-network"></a>Virtueel netwerk configureren

Voordat u VM's implementeert en uw load balancer test, moet u de ondersteunende virtuele-netwerkresources maken.

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een Virtual Network met [az network vnet create](/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt).

* Met de naam **myVNet**.
* Adresvoorvoegsel van **10.1.0.0/16**.
* Subnet met de naam **myBackendSubnet**.
* Subnetvoorvoegsel van **10.1.0.0/24**.
* In de **CreatePubLBQS-rg**-resourcegroep.
* Locatie **VS - Oost**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Voor Standard Load Balancer moeten de VM's in het back-endadres netwerkinterfaces bevatten die tot een netwerkbeveiligingsgroep behoren. 

Maak een netwerkbeveiligingsgroep met [az network nsg create](/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create):

* Met de naam **myNSG**.
* In resourcegroep **CreatePubLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Regel voor netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroepregel met behulp van [az network nsg rule create](/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create):

* Met de naam **myNSGRuleHTTP**.
* In de netwerkbeveiligingsgroep die u in de vorige stap hebt gemaakt, **myNSG**.
* In resourcegroep **CreatePubLBQS-rg**.
* Protocol **(*)** .
* Richting **Inkomend**.
* Bron **(*)** .
* Doel **(*)** .
* Doelpoort **Poort 80**.
* Toegang **Toestaan**.
* Prioriteit **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Netwerkinterfaces maken voor de virtuele machines

Maak drie netwerkinterfaces met de opdracht [az network nic create](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

#### <a name="vm1"></a>VM1

* Met de naam **myNicVM1**.
* In resourcegroep **CreatePubLBQS-rg**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* In netwerkbeveiligingsgroep **myNSG**.

```azurecli-interactive

  az network nic create \
    --resource-group CreatePubLBQS-rg \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* Met de naam **myNicVM2**.
* In resourcegroep **CreatePubLBQS-rg**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* In netwerkbeveiligingsgroep **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreatePubLBQS-rg \
    --name myNicVM2 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm3"></a>VM3

* Met de naam **myNicVM3**.
* In resourcegroep **CreatePubLBQS-rg**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* In netwerkbeveiligingsgroep **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreatePubLBQS-rg \
    --name myNicVM3 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Back-endservers maken

In deze sectie maakt u:

* Een cloudconfiguratiebestand met de naam **cloud-init.txt** voor de serverconfiguratie. 
* Beschikbaarheidsset voor de virtuele machines
* Drie virtuele machines die worden gebruikt als back-endservers voor de load balancer.


Installeer NGINX op de virtuele machines om te controleren of de load balancer is gemaakt.

### <a name="create-cloud-init-configuration-file"></a>Een cloud-init-configuratiebestand maken

Gebruik een cloud-init-configuratiebestand om NGINX te installeren en een 'Hallo wereld' Node.js-app uit te voeren op een virtuele Linux-machine. 

Maak in uw huidige shell een bestand met de naam cloud-init.txt. Kopieer en plak de volgende configuratie van de shell. Zorg ervoor dat u het hele cloud-init-bestand correct kopieert, met name de eerste regel:

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
### <a name="create-availability-set-for-virtual-machines"></a>Maak een beschikbaarheidsset voor de virtuele machines

Maak de beschikbaarheidsset met behulp van [az vm availability-set create](/cli/azure/vm/availability-set?view=azure-cli-latest#az-vm-availability-set-create):

* Met de naam **myAvSet**.
* In resourcegroep **CreatePubLBQS-rg**.
* Locatie **VS - Oost**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreatePubLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Virtuele machines maken

Maak de virtuele machines met [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create):

#### <a name="vm1"></a>VM1
* Met de naam **myVM1**.
* In resourcegroep **CreatePubLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM1**.
* VM-installatiekopie **UbuntuLTS**.
* Configuratiebestand **cloud-init.txt** dat u in de bovenstaande stap heeft gemaakt.
* In beschikbaarheidsset **myAvSet**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet \
    --no-wait 
```
#### <a name="vm2"></a>VM2
* Met de naam **myVM2**.
* In resourcegroep **CreatePubLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM2**.
* VM-installatiekopie **UbuntuLTS**.
* Configuratiebestand **cloud-init.txt** dat u in de bovenstaande stap heeft gemaakt.
* In **Zone 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

#### <a name="vm3"></a>VM3
* Met de naam **myVM3**.
* In resourcegroep **CreatePubLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM3**.
* VM-installatiekopie **UbuntuLTS**.
* Configuratiebestand **cloud-init.txt** dat u in de bovenstaande stap heeft gemaakt.
* In **Zone 3**.

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```
Het kan enkele minuten duren om de VM's te implementeren.


## <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Om toegang te krijgen tot uw web-app op internet, hebt u een openbaar IP-adres nodig voor de load balancer. 

Gebruik [az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) om:

* Maak een standaard zoneredundant openbaar IP-adres met de naam **myPublicIP**.
* In **CreatePubLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Basic
```

## <a name="create-basic-load-balancer"></a>Een eenvoudige load balancer maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de load balancer kunt maken en configureren:

  * Een front-end-IP-pool die het binnenkomende netwerkverkeer op de load balancer ontvangt.
  * Een back-end-IP-pools waar de front-endpool het netwerkverkeer naartoe stuurt dat door de load balancer is verdeeld.
  * Een statustest die de status van de back-end-VM-instanties vaststelt.
  * Een load balancer-regel die bepaalt hoe het verkeer over de VM's wordt verdeeld.

### <a name="create-the-load-balancer-resource"></a>De load balancer-resource maken

Maak een openbare load balancer met [az network lb create](/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create):

* Genaamd **myLoadBalancer**.
* Een front-endgroep met de naam **myFrontEnd**.
* Een back-endgroep met de naam MyBackendPool.
* Gekoppeld met het openbare IP-adres **myPublicIP** dat u in de vorige stap hebt gemaakt. 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Statustest maken

Met een statustest worden alle VM-instanties gecontroleerd om na te gaan of ze netwerkverkeer kunnen verzenden. 

Een virtuele machine met een mislukte test wordt verwijderd uit de load balancer. De virtuele machine wordt weer toegevoegd aan de load balancer wanneer de fout is opgelost.

Gebruik een statustest met [az network lb probe create](/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create):

* Bewaakt de status van de virtuele machines.
* Met de naam **myHealthProbe**.
* Protocol: **TCP**.
* Controleert **poort 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Load balancer-regel maken

Met een load balancer-regel wordt het volgende gedefinieerd:

* De front-end-IP-configuratie voor het binnenkomende verkeer.
* De back-end-IP-adresgroep voor het ontvangen van verkeer.
* De vereiste bron- en doelpoort. 

Gebruik [az network lb rule create](/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) om een load balancer-regel te maken:

* Naam: **myHTTPRule**
* Luistert aan **poort 80** in de front-endpool **myFrontEnd**.
* Verzendt netwerkverkeer volgens taakverdeling naar de back-endadresgroep **myBackEndPool** via **poort 80**. 
* Gebruikt statustest **myHealthProbe**.
* Protocol: **TCP**.
* Time-out voor inactiviteit: **15 minuten**.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15
```

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Virtuele machines toevoegen aan back-endpool van load balancer

Voeg de virtuele machines aan de back-endpool toe met [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):


#### <a name="vm1"></a>VM1
* In back-endadrespool **myBackEndPool**.
* In resourcegroep **CreatePubLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM1** en **ipconfig1**.
* Gekoppeld aan load balancer **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreatePubLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* In back-endadrespool **myBackEndPool**.
* In resourcegroep **CreatePubLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM2** en **ipconfig1**.
* Gekoppeld aan load balancer **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreatePubLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* In back-endadrespool **myBackEndPool**.
* In resourcegroep **CreatePubLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM3** en **ipconfig1**.
* Gekoppeld aan load balancer **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group CreatePubLBQS-rg \
   --lb-name myLoadBalancer
```
---

## <a name="test-the-load-balancer"></a>Load balancer testen

Gebruik [az network public-ip show](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show) om het openbare IP-adres van de load balancer op te halen. 

Kopieer het openbare IP-adres en plak het in de adresbalk van de browser.

```azurecli-interactive
  az network public-ip show \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```
:::image type="content" source="./media/load-balancer-standard-public-cli/running-nodejs-app.png" alt-text="Test de load balancer" border="true":::

## <a name="clean-up-resources"></a>Resources opschonen

Gebruik de opdracht [az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete) om de resourcegroep, de load balancer en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt.

```azurecli-interactive
  az group delete \
    --name CreatePubLBQS-rg
```

## <a name="next-steps"></a>Volgende stappen
In deze quickstart hebt u

* Een standaardversie van een openbare load balancer gemaakt
* Virtuele machines gekoppeld. 
* De load balancer-verkeersregel en de statustest geconfigureerd.
* De load balancer getest.

Als u meer informatie wilt over Azure Load Balancer, gaat u naar
> [!div class="nextstepaction"]
> [Wat is Azure Load Balancer?](load-balancer-overview.md)