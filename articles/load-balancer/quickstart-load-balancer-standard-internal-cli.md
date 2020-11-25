---
title: 'Quickstart: Een interne load balancer maken - Azure CLI'
titleSuffix: Azure Load Balancer
description: In deze quickstart vindt u informatie over het maken van een interne load balancer via de Azure CLI
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 834b5c3651a7fff085dc53096f66d5e3f4bf27b4
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700406"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Quickstart: Een interne load balancer maken met Azure CLI om taken te verdelen over VM's

Ga aan de slag met Azure Load Balancer via de Azure CLI om een openbare load balancer en drie virtuele machines te maken.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Voor deze quickstart is versie 2.0.28 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resourcegroep maken met [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create):

* met de naam **CreateIntLBQS-rg**. 
* Op de locatie **eastus**.

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**Standaard SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Voor productieworkloads wordt de load balancer uit de Standard SKU aanbevolen. Zie **[Azure Load Balancer-SKU's](skus.md)** voor meer informatie over SKU's.

## <a name="configure-virtual-network"></a>Virtueel netwerk configureren

Voordat u VM's en uw load balancer implementeert, moet u de ondersteunende virtuele-netwerkresources maken.

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een Virtual Network met [az network vnet create](/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt).

* Met de naam **myVNet**.
* Adresvoorvoegsel van **10.1.0.0/16**.
* Subnet met de naam **myBackendSubnet**.
* Subnetvoorvoegsel van **10.1.0.0/24**.
* In de **CreateIntLBQS-rg**-resourcegroep.
* Locatie **VS - Oost**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
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
* In resourcegroep **CreateIntLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Regel voor netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroepregel met behulp van [az network nsg rule create](/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create):

* Met de naam **myNSGRuleHTTP**.
* In de netwerkbeveiligingsgroep die u in de vorige stap hebt gemaakt, **myNSG**.
* In resourcegroep **CreateIntLBQS-rg**.
* Protocol **(*)** .
* Richting **Inkomend**.
* Bron **(*)** .
* Doel **(*)** .
* Doelpoort **Poort 80**.
* Toegang **Toestaan**.
* Prioriteit **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
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

## <a name="create-backend-servers"></a>Back-endservers maken

In deze sectie maakt u:

* Netwerkinterfaces voor de back-endservers.
* Een cloudconfiguratiebestand met de naam **cloud-init.txt** voor de serverconfiguratie.
* Twee virtuele machines die worden gebruikt als back-endservers voor de load balancer.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Netwerkinterfaces maken voor de virtuele machines

Maak twee netwerkinterfaces met de opdracht [az network nic create](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

#### <a name="vm1"></a>VM1

* Met de naam **myNicVM1**.
* In resourcegroep **CreateIntLBQS-rg**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* In netwerkbeveiligingsgroep **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* Met de naam **myNicVM2**.
* In resourcegroep **CreateIntLBQS-rg**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* In netwerkbeveiligingsgroep **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

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
* In resourcegroep **CreateIntLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM1**.
* VM-installatiekopie **UbuntuLTS**.
* Configuratiebestand **cloud-init.txt** dat u in de bovenstaande stap heeft gemaakt.
* In **Zone 1**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 1 \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* Met de naam **myVM2**.
* In resourcegroep **CreateIntLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM2**.
* VM-installatiekopie **UbuntuLTS**.
* Configuratiebestand **cloud-init.txt** dat u in de bovenstaande stap heeft gemaakt.
* In **Zone 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

Het kan enkele minuten duren om de VM's te implementeren.

## <a name="create-standard-load-balancer"></a>Een standaard load balancer maken

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
* Gekoppeld aan het virtuele netwerk **myVNet**.
* Gekoppeld aan het back-endsubnet **myBackendSubnet**.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
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
    --resource-group CreateIntLBQS-rg \
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
    --resource-group CreateIntLBQS-rg \
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
>[!NOTE]
>De virtuele machines in de back-end-pool hebben geen uitgaande internetverbinding met deze configuratie. </br> Voor meer informatie over het bieden van uitgaande connectiviteit raadpleegt u: </br> **[Uitgaande verbindingen in Azure](load-balancer-outbound-connections.md)**</br> Opties voor het bieden van connectiviteit: </br> **[Load balancer-configuratie voor alleen uitgaand verkeer](egress-only.md)** </br> **[Wat is Azure Virtual Network NAT?](../virtual-network/nat-overview.md)**

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Virtuele machines toevoegen aan back-endpool van load balancer

Voeg de virtuele machines aan de back-endpool toe met [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):


#### <a name="vm1"></a>VM1
* In back-endadrespool **myBackEndPool**.
* In resourcegroep **CreateIntLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM1** en **ipconfig1**.
* Gekoppeld aan load balancer **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* In back-endadrespool **myBackEndPool**.
* In resourcegroep **CreateIntLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM2** en **ipconfig1**.
* Gekoppeld aan load balancer **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

# <a name="basic-sku"></a>[**Basis-SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Voor productieworkloads wordt de load balancer uit de Standard SKU aanbevolen. Zie **[Azure Load Balancer-SKU's](skus.md)** voor meer informatie over SKU's.

## <a name="configure-virtual-network"></a>Virtueel netwerk configureren

Voordat u VM's en uw load balancer implementeert, moet u de ondersteunende virtuele-netwerkresources maken.

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een Virtual Network met [az network vnet create](/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt).

* Met de naam **myVNet**.
* Adresvoorvoegsel van **10.1.0.0/16**.
* Subnet met de naam **myBackendSubnet**.
* Subnetvoorvoegsel van **10.1.0.0/24**.
* In de **CreateIntLBQS-rg**-resourcegroep.
* Locatie **VS - Oost**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
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
* In resourcegroep **CreateIntLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Regel voor netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroepregel met behulp van [az network nsg rule create](/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create):

* Met de naam **myNSGRuleHTTP**.
* In de netwerkbeveiligingsgroep die u in de vorige stap hebt gemaakt, **myNSG**.
* In resourcegroep **CreateIntLBQS-rg**.
* Protocol **(*)** .
* Richting **Inkomend**.
* Bron **(*)** .
* Doel **(*)** .
* Doelpoort **Poort 80**.
* Toegang **Toestaan**.
* Prioriteit **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
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

Maak twee netwerkinterfaces met de opdracht [az network nic create](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

#### <a name="vm1"></a>VM1

* Met de naam **myNicVM1**.
* In resourcegroep **CreateIntLBQS-rg**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* In netwerkbeveiligingsgroep **myNSG**.

```azurecli-interactive

  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* Met de naam **myNicVM2**.
* In resourcegroep **CreateIntLBQS-rg**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Back-endservers maken

In deze sectie maakt u:

* Een cloudconfiguratiebestand met de naam **cloud-init.txt** voor de serverconfiguratie. 
* Beschikbaarheidsset voor de virtuele machines
* Twee virtuele machines die worden gebruikt als back-endservers voor de load balancer.

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
* In resourcegroep **CreateIntLBQS-rg**.
* Locatie **VS - Oost**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Virtuele machines maken

Maak de virtuele machines met [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create):

#### <a name="vm1"></a>VM1
* Met de naam **myVM1**.
* In resourcegroep **CreateIntLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM1**.
* VM-installatiekopie **UbuntuLTS**.
* Configuratiebestand **cloud-init.txt** dat u in de bovenstaande stap heeft gemaakt.
* In beschikbaarheidsset **myAvSet**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* Met de naam **myVM2**.
* In resourcegroep **CreateIntLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM2**.
* VM-installatiekopie **UbuntuLTS**.
* Configuratiebestand **cloud-init.txt** dat u in de bovenstaande stap heeft gemaakt.
* In **Zone 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

Het kan enkele minuten duren om de VM's te implementeren.

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
* Gekoppeld aan het virtuele netwerk **myVNet**.
* Gekoppeld aan het back-endsubnet **myBackendSubnet**.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
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
    --resource-group CreateIntLBQS-rg \
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
    --resource-group CreateIntLBQS-rg \
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
* In resourcegroep **CreateIntLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM1** en **ipconfig1**.
* Gekoppeld aan load balancer **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* In back-endadrespool **myBackEndPool**.
* In resourcegroep **CreateIntLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM2** en **ipconfig1**.
* Gekoppeld aan load balancer **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

---

## <a name="test-the-load-balancer"></a>Load balancer testen

### <a name="create-azure-bastion-public-ip"></a>Openbare IP-adressen voor Azure Bastion maken

Gebruik [az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) om een openbaar IP-adres voor de Bastion-host te maken:

* Maak een standaard zoneredundant openbaar IP-adres met de naam **myBastionIP**.
* In **CreateIntLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```

### <a name="create-azure-bastion-subnet"></a>Een subnet voor Azure Bastion maken

Gebruik [az network vnet subnet create](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create) om een subnet te maken:

* met de naam **AzureBastionSubnet**.
* Adresvoorvoegsel van **10.1.1.0/24**.
* In virtueel netwerk **myVnet**.
* In resourcegroep **CreateIntLBQS-rg**.

```azurecli-interactive
  az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-azure-bastion-host"></a>Een Azure Bastion-host maken
Gebruik [az network bastion create](/cli/azure/network/bastion?view=azure-cli-latest#az-network-bastion-create) om een Bastion-host te maken:

* met de naam **myBastionHost**.
* In **CreateIntLBQS-rg**
* Gekoppeld aan het openbare IP-adres **myBastionIP**.
* Gekoppeld aan het virtuele netwerk **myVNet**.
* Op de locatie **eastus**.

```azurecli-interactive
  az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```
Het duurt enkele minuten voordat de Bastion-host is geïmplementeerd.

### <a name="create-test-virtual-machine"></a>Virtuele testmachine maken

Maak de netwerkinterface met de opdracht [az network nic create](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

* met de naam **myNicTestVM**.
* In resourcegroep **CreateIntLBQS-rg**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* In netwerkbeveiligingsgroep **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Maak de virtuele machine met [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create):

* met de naam **myTestVM**.
* In resourcegroep **CreateIntLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicTestVM**.
* Installatiekopie van virtuele machine **Win2019Datacenter**.
* Kies waarden voor **\<adminpass>** en **\<adminuser>** .
  

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username <adminuser> \
    --admin-password <adminpass> \
    --no-wait
```
Het kan enkele minuten duren om de virtuele machine te implementeren.

### <a name="test"></a>Testen

1. [Meld u aan](https://portal.azure.com) bij Azure Portal.

1. Zoek op het scherm **Overzicht** het privé-IP-adres voor de load balancer op. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer**.

2. Noteer of kopieer het adres naast **Privé IP-adres** in het **Overzicht** van **myLoadBalancer**.

3. Selecteer in het linkermenu **Alle services**, selecteer vervolgens **Alle resources** en selecteer daarna in de lijst met resources **myTestVM**, die zich in de resourcegroep **CreateIntLBQS-rg** bevindt.

4. Selecteer op de pagina **Overzicht** de optie **Verbinding maken** en daarna **Bastion**.

6. Voer de gebruikersnaam en het wachtwoord in die zijn ingevoerd tijdens het maken van de VM.

7. Open **Internet Explorer** op **myTestVM**.

8. Voer het IP-adres uit de vorige stap in in de adresbalk van de browser. De standaardpagina van IIS-webserver wordt weergegeven in de browser.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Een interne load balancer van het type Standard maken" border="true":::
   
U kunt de standaardpagina van de IIS-webserver van elke virtuele machine aanpassen en vervolgens uw webbrowser geforceerd vernieuwen vanaf de clientcomputer om te zien hoe de load balancer verkeer verdeelt over alle drie de virtuele machines.

## <a name="clean-up-resources"></a>Resources opschonen

Gebruik de opdracht [az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete) om de resourcegroep, de load balancer en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
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