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
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: edf893f1f6ba0691da5764420017282d7a8bde84
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562808"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Quickstart: Een interne load balancer maken met Azure CLI om taken te verdelen over VM's

Ga aan de slag met Azure Load Balancer via Azure CLI om een interne load balancer en drie virtuele machines te maken.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Voor deze quickstart is versie 2.0.28 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resourcegroep maken met [az group create](/cli/azure/group#az_group_create):

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

In deze sectie maakt u een load balancer die taken van virtuele machines verdeelt. 

Wanneer u een interne load balancer maakt, wordt een virtueel netwerk geconfigureerd als netwerk voor de load balancer. 

Het volgende diagram bevat de resources die u in deze quickstart hebt gemaakt:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Resources voor de Standard-load balancer die worden gemaakt voor de quickstart." border="false":::

## <a name="configure-virtual-network---standard"></a>Virtuele netwerken configureren - Standard

Voordat u VM's en uw load balancer implementeert, moet u de ondersteunende virtuele-netwerkresources maken.

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een Virtual Network met [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create).

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

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Gebruik [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) om een openbaar IP-adres voor de Bastion-host te maken:

* Maak een standaard zoneredundant openbaar IP-adres met de naam **myBastionIP**.
* In **CreateIntLBQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Een bastion-subnet maken

Gebruik [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) om een bastionsubnet te maken:

* met de naam **AzureBastionSubnet**.
* Adresvoorvoegsel van **10.1.1.0/24**.
* In virtueel netwerk **myVnet**.
* In resourcegroep **CreateIntLBQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Bastion-host maken

Gebruik [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) om een Bastion-host te maken:

* met de naam **myBastionHost**.
* In **CreateIntLBQS-rg**.
* Gekoppeld aan het openbare IP-adres **myBastionIP**.
* Gekoppeld aan het virtuele netwerk **myVNet**.
* Op de locatie **eastus**.

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

De Azure Bastion kan een paar minuten nodig hebben om te implementeren.


### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Voor Standard Load Balancer moeten de VM's in het back-endadres netwerkinterfaces bevatten die tot een netwerkbeveiligingsgroep behoren. 

Maak een netwerkbeveiligingsgroep met [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create):

* Met de naam **myNSG**.
* In resourcegroep **CreateIntLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Regel voor netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroepregel met behulp van [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create):

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

## <a name="create-backend-servers---standard"></a>Back-endservers maken - Standard

In deze sectie maakt u:

* Drie netwerkinterfaces voor de virtuele machines.
* Drie virtuele machines die worden gebruikt als back-endservers voor de load balancer.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Netwerkinterfaces maken voor de virtuele machines

Maak drie netwerkinterfaces met de opdracht [az network nic create](/cli/azure/network/nic#az-network-nic-create):

* met de naam **myNicVM1**, **myNicVM2** en **myNicVM3**.
* In resourcegroep **CreateIntLBQS-rg**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* In netwerkbeveiligingsgroep **myNSG**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-virtual-machines"></a>Virtuele machines maken

Maak de virtuele machines met [az vm create](/cli/azure/vm#az-vm-create):

* Met de namen **myVM1**, **myVM2** en **myVM3**.
* In resourcegroep **CreateIntLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM1**, **myNicVM2** en **myNicVM3**.
* Installatiekopie van virtuele machine **win2019datacenter**.
* In **Zone 1**, **Zone 2** en **Zone 3**.

```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone $n \
    --no-wait
  done
```

Het kan enkele minuten duren om de VM's te implementeren.

## <a name="create-standard-load-balancer"></a>Een standaard load balancer maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de load balancer kunt maken en configureren:

  * Een front-end-IP-pool die het binnenkomende netwerkverkeer op de load balancer ontvangt.
  * Een back-end-IP-pools waar de front-endpool het netwerkverkeer naartoe stuurt dat door de load balancer is verdeeld.
  * Een statustest die de status van de back-end-VM-instanties vaststelt.
  * Een load balancer-regel die bepaalt hoe het verkeer over de VM's wordt verdeeld.

### <a name="create-the-load-balancer-resource"></a>De load balancer-resource maken

Maak een openbare load balancer met [az network lb create](/cli/azure/network/lb#az-network-lb-create):

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

Gebruik een statustest met [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create):

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

Gebruik [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) om een load balancer-regel te maken:

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
    --idle-timeout 15 \
    --enable-tcp-reset true
```

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Virtuele machines toevoegen aan back-endpool van load balancer

Voeg de virtuele machines aan de back-endpool toe met [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add):

* In back-endadrespool **myBackEndPool**.
* In resourcegroep **CreateIntLBQS-rg**.
* Gekoppeld aan netwerkinterface **myNicVM1**, **myNicVM2** en **myNicVM3**.
* Gekoppeld aan load balancer **myLoadBalancer**.

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```

# <a name="basic-sku"></a>[**Basis-SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Voor productieworkloads wordt de load balancer uit de Standard SKU aanbevolen. Zie **[Azure Load Balancer-SKU's](skus.md)** voor meer informatie over SKU's.

In deze sectie maakt u een load balancer die taken van virtuele machines verdeelt. 

Wanneer u een interne load balancer maakt, wordt een virtueel netwerk geconfigureerd als netwerk voor de load balancer. 

Het volgende diagram bevat de resources die u in deze quickstart hebt gemaakt:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Resources voor de Basic-load balancer die worden gemaakt in de quickstart." border="false":::

## <a name="configure-virtual-network---basic"></a>Virtueel netwerk configureren - Basic

Voordat u VM's en uw load balancer implementeert, moet u de ondersteunende virtuele-netwerkresources maken.

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een Virtual Network met [az network vnet create](/cli/azure/network/vnet#az-network-vnet-createt).

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

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Gebruik [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) om een openbaar IP-adres voor de Bastion-host te maken:

* Maak een standaard zoneredundant openbaar IP-adres met de naam **myBastionIP**.
* In **CreateIntLBQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Een bastion-subnet maken

Gebruik [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) om een bastionsubnet te maken:

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

### <a name="create-bastion-host"></a>Bastion-host maken

Gebruik [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) om een Bastion-host te maken:

* met de naam **myBastionHost**.
* In **CreateIntLBQS-rg**.
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

De Azure Bastion kan een paar minuten nodig hebben om te implementeren.

### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Voor Standard Load Balancer moeten de VM's in het back-endadres netwerkinterfaces bevatten die tot een netwerkbeveiligingsgroep behoren. 

Maak een netwerkbeveiligingsgroep met [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create):

* Met de naam **myNSG**.
* In resourcegroep **CreateIntLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Regel voor netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroepregel met behulp van [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create):

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

## <a name="create-backend-servers---basic"></a>Back-endservers maken - Basic

In deze sectie maakt u:

* Drie netwerkinterfaces voor de virtuele machines.
* Beschikbaarheidsset voor de virtuele machines
* Drie virtuele machines die worden gebruikt als back-endservers voor de load balancer.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Netwerkinterfaces maken voor de virtuele machines

Maak drie netwerkinterfaces met de opdracht [az network nic create](/cli/azure/network/nic#az-network-nic-create):

* met de naam **myNicVM1**, **myNicVM2** en **myNicVM3**.
* In resourcegroep **CreateIntLBQS-rg**.
* In virtueel netwerk **myVnet**.
* In subnet **myBackendSubnet**.
* In netwerkbeveiligingsgroep **myNSG**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-availability-set-for-virtual-machines"></a>Maak een beschikbaarheidsset voor de virtuele machines

Maak de beschikbaarheidsset met behulp van [az vm availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create):

* Met de naam **myAvailabilitySet**.
* In resourcegroep **CreateIntLBQS-rg**.
* Locatie **VS - Oost**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Virtuele machines maken

Maak de virtuele machines met [az vm create](/cli/azure/vm#az-vm-create):

* Met de namen **myVM1**, **myVM2** en **myVM3**.
* In resourcegroep **CreateIntLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicVM1**, **myNicVM2** en **myNicVM3**.
* Installatiekopie van virtuele machine **win2019datacenter**.
* In **myAvailabilitySet**.


```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvailabilitySet \
    --no-wait
  done
```
Het kan enkele minuten duren om de VM's te implementeren.

## <a name="create-basic-load-balancer"></a>Een eenvoudige load balancer maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de load balancer kunt maken en configureren:

  * Een front-end-IP-pool die het binnenkomende netwerkverkeer op de load balancer ontvangt.
  * Een back-end-IP-pools waar de front-endpool het netwerkverkeer naartoe stuurt dat door de load balancer is verdeeld.
  * Een statustest die de status van de back-end-VM-instanties vaststelt.
  * Een load balancer-regel die bepaalt hoe het verkeer over de VM's wordt verdeeld.

### <a name="create-the-load-balancer-resource"></a>De load balancer-resource maken

Maak een openbare load balancer met [az network lb create](/cli/azure/network/lb#az-network-lb-create):

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

Gebruik een statustest met [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create):

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

Gebruik [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) om een load balancer-regel te maken:

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

Voeg de virtuele machines aan de back-endpool toe met [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add):

* In back-endadrespool **myBackEndPool**.
* In resourcegroep **CreateIntLBQS-rg**.
* Gekoppeld aan netwerkinterface **myNicVM1**, **myNicVM2** en **myNicVM3**.
* Gekoppeld aan load balancer **myLoadBalancer**.

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```
---

## <a name="test-the-load-balancer"></a>Load balancer testen

### <a name="create-test-virtual-machine"></a>Virtuele testmachine maken

Maak de netwerkinterface met de opdracht [az network nic create](/cli/azure/network/nic#az-network-nic-create):

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
Maak de virtuele machine met [az vm create](/cli/azure/vm#az-vm-create):

* met de naam **myTestVM**.
* In resourcegroep **CreateIntLBQS-rg**.
* Gekoppeld aan de netwerkinterface **myNicTestVM**.
* Installatiekopie van virtuele machine **Win2019Datacenter**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
Het kan enkele minuten duren om de virtuele machine te implementeren.

## <a name="install-iis"></a>IIS installeren

Gebruik [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) om IIS op de virtuele machines te installeren en de standaardwebsite op de computernaam in te stellen.

```azurecli-interactive
  array=(myVM1 myVM2 myVM3)
    for vm in "${array[@]}"
    do
     az vm extension set \
       --publisher Microsoft.Compute \
       --version 1.8 \
       --name CustomScriptExtension \
       --vm-name $vm \
       --resource-group CreateIntLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

### <a name="test"></a>Testen

1. [Meld u aan](https://portal.azure.com) bij Azure Portal.

2. Zoek op het scherm **Overzicht** het privé-IP-adres voor de load balancer op. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myLoadBalancer**.

3. Noteer of kopieer het adres naast **Privé IP-adres** in het **Overzicht** van **myLoadBalancer**.

4. Selecteer in het linkermenu **Alle services**, selecteer vervolgens **Alle resources** en selecteer daarna in de lijst met resources **myTestVM**, die zich in de resourcegroep **CreateIntLBQS-rg** bevindt.

5. Selecteer op de pagina **Overzicht** de optie **Verbinding maken** en daarna **Bastion**.

6. Voer de gebruikersnaam en het wachtwoord in die zijn ingevoerd tijdens het maken van de VM.

7. Open **Internet Explorer** op **myTestVM**.

8. Voer het IP-adres uit de vorige stap in in de adresbalk van de browser. De standaardpagina van IIS-webserver wordt weergegeven in de browser.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Een interne load balancer van het type Standard maken" border="true":::
   
U kunt de standaardpagina van de IIS-webserver van elke virtuele machine aanpassen en vervolgens uw webbrowser geforceerd vernieuwen vanaf de clientcomputer om te zien hoe de load balancer verkeer verdeelt over alle drie de virtuele machines.

## <a name="clean-up-resources"></a>Resources opschonen

Gebruik de opdracht [az group delete](/cli/azure/group#az-group-delete) om de resourcegroep, de load balancer en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Volgende stappen

Voor deze snelstart geldt het volgende:

* Een standaardversie van een openbare load balancer gemaakt
* Virtuele machines gekoppeld. 
* De load balancer-verkeersregel en de statustest geconfigureerd.
* De load balancer getest.

Als u meer informatie wilt over Azure Load Balancer, gaat u naar:
> [!div class="nextstepaction"]
> [Wat is Azure Load Balancer?](load-balancer-overview.md)