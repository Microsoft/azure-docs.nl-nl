---
title: 'Quickstart: Een interne load balancer maken - Azure CLI'
titleSuffix: Azure Load Balancer
description: In deze quickstart ziet u hoe u een interne load balancer met behulp van de Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2db30024b26352bcc038d606bcdc760b6350d413
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788831"
---
# <a name="quickstart-create-an-internal-load-balancer-by-using-the-azure-cli"></a>Quickstart: Een intern load balancer met behulp van de Azure CLI

Ga aan de slag Azure Load Balancer met behulp van de Azure CLI om een intern load balancer en drie virtuele machines te maken.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

Voor deze quickstart is versie 2.0.28 of hoger van Azure CLI vereist. Als u een Azure Cloud Shell, is de meest recente versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin u uw Azure-resources implementeert en beheert.

Maak een resourcegroep maken met [az group create](/cli/azure/group#az_group_create). Geef de resourcegroep **de naam CreateIntLBIRS-rg** en geef de locatie op als **eastus**.

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```

---
# <a name="standard-sku"></a>[**Standaard SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Voor productieworkloads wordt de load balancer uit de Standard SKU aanbevolen. Zie **[Azure Load Balancer-SKU's](skus.md)** voor meer informatie over SKU's.

In deze sectie maakt u een load balancer die taken van virtuele machines verdeelt. Wanneer u een interne load balancer maakt, wordt een virtueel netwerk geconfigureerd als netwerk voor de load balancer. Het volgende diagram bevat de resources die u in deze quickstart hebt gemaakt:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Resources voor de Standard-load balancer die worden gemaakt voor de quickstart." border="false":::

### <a name="configure-the-virtual-network"></a>Het virtuele netwerk configureren

Voordat u VM's en uw load balancer implementeert, moet u de ondersteunende virtuele-netwerkresources maken.

#### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met [az network vnet create.](/cli/azure/network/vnet#az_network_vnet_create) Geef het volgende op:

* Met de **naam myVNet**
* Adres **voorvoegsel van 10.1.0.0/16**
* Subnet met de **naam myBackendSubnet**
* Subnet-voorvoegsel **van 10.1.0.0/24**
* In de **resourcegroep CreateIntLBIRS-rg**
* Locatie van **eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Gebruik [az network public-ip create om](/cli/azure/network/public-ip#az_network_public_ip_create) een openbaar IP-adres te maken voor de Azure Bastion host. Geef het volgende op:

* Maak een standaard zone-redundant openbaar IP-adres met de **naam myBastionIP**
* In **CreateIntLBQS-rg**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Een Azure Bastion maken

Gebruik [az network vnet subnet create om](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) een subnet te maken. Geef het volgende op:

* Met **de naam AzureBastionSubnet**
* Adres voorvoegsel **van 10.1.1.0/24**
* In virtueel netwerk **myVNet**
* In resourcegroep **CreateIntLBQS-rg**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Een Azure Bastion-host maken

Gebruik [az network bastion create om](/cli/azure/network/bastion#az_network_bastion_create) een host te maken. Geef het volgende op:

* met de naam **myBastionHost**.
* In **CreateIntLBQS-rg**
* Gekoppeld aan openbare IP **myBastionIP**
* Gekoppeld aan virtueel netwerk **myVNet**
* Op **locatie eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

De Azure Bastion kan een paar minuten nodig hebben om te implementeren.

#### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Zorg er voor load balancer dat uw VM's netwerkinterfaces hebben die deel uitmaken van een netwerkbeveiligingsgroep. Maak een netwerkbeveiligingsgroep met [az network nsg create.](/cli/azure/network/nsg#az_network_nsg_create) Geef het volgende op:

* Met de **naam myNSG**
* In resourcegroep **CreateIntLBQS-rg**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Regel voor netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroepsregel met [az network nsg rule create.](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) Geef het volgende op:

* Met de **naam myNSGRuleHTTP**
* In de netwerkbeveiligingsgroep die u in de vorige stap hebt gemaakt, **myNSG**
* In resourcegroep **CreateIntLBQS-rg**
* Protocol **(*)**
* Richting **inkomende richting**
* Bron **(*)**
* Doel **(*)**
* Doelpoort **poort 80**
* Toegang **toestaan**
* Prioriteit **200**

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

### <a name="create-back-end-servers"></a>Back-endservers maken

In deze sectie maakt u:

* Drie netwerkinterfaces voor de virtuele machines.
* Drie virtuele machines die moeten worden gebruikt als servers voor de load balancer.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Netwerkinterfaces maken voor de virtuele machines

Maak drie netwerkinterfaces [met az network nic create](/cli/azure/network/nic#az_network_nic_create). Geef het volgende op:

* **MyNicVM1,** **myNicVM2** en **myNicVM3 genoemd**
* In resourcegroep **CreateIntLBIRS-rg**
* In virtueel netwerk **myVNet**
* In subnet **myBackendSubnet**
* In netwerkbeveiligingsgroep **myNSG**

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

#### <a name="create-the-virtual-machines"></a>De virtuele machines maken

Maak de virtuele machines met [az vm create](/cli/azure/vm#az_vm_create). Geef het volgende op:

* **MyVM1,** **myVM2** en **myVM3 genoemd**
* In resourcegroep **CreateIntLBIRS-rg**
* Gekoppeld aan de netwerkinterface **myNicVM1,** **myNicVM2** en **myNicVM3**
* Virtuele-machine-afbeelding **win2019datacenter**
* In **Zone 1**, **Zone 2** en **Zone 3**

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

Het kan enkele minuten duren voordat de VM's zijn geïmplementeerd.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]


### <a name="create-the-load-balancer"></a>Load balancer maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de load balancer kunt maken en configureren:

* Een IP-adresgroep die het binnenkomende netwerkverkeer op de load balancer.
* Een tweede IP-adresgroep, waarbij de eerste groep het netwerkverkeer met load balanced verzendt.
* Een statustest die de status van de VM-exemplaren bepaalt.
* Een load balancer-regel die bepaalt hoe het verkeer over de VM's wordt verdeeld.

#### <a name="create-the-load-balancer-resource"></a>De load balancer-resource maken

Maak een openbare load balancer met [az network lb create](/cli/azure/network/lb#az_network_lb_create). Geef het volgende op:

* Met de naam **myLoadBalancer**
* Een pool met de **naam myFrontEnd**
* Een pool met de **naam myBackEndPool**
* Gekoppeld aan het virtuele netwerk **myVNet**
* Gekoppeld aan het subnet **myBackendSubnet**

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

#### <a name="create-the-health-probe"></a>Statustest maken

Met een statustest worden alle VM-instanties gecontroleerd om na te gaan of ze netwerkverkeer kunnen verzenden. Een virtuele machine met een mislukte test wordt verwijderd uit de load balancer. De virtuele machine wordt weer toegevoegd aan de load balancer wanneer de fout is opgelost.

Maak een statustest met [az network lb probe create.](/cli/azure/network/lb/probe#az_network_lb_probe_create) Geef het volgende op:

* Controleert de status van de virtuele machines
* Met **de naam myHealthProbe**
* **Protocol-TCP**
* Poort **80 bewaken**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Met een load balancer-regel wordt het volgende gedefinieerd:

* De IP-configuratie voor het binnenkomende verkeer.
* De IP-adresgroep voor het ontvangen van het verkeer.
* De vereiste bron- en doelpoort. 

Gebruik [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) om een load balancer-regel te maken. Geef het volgende op:

* Naam: **myHTTPRule**
* Luisteren op **poort 80** in de pool **myFrontEnd**
* Netwerkverkeer met load balanced verzenden naar de adresgroep **myBackEndPool** met behulp van **poort 80** 
* Statustest **myHealthProbe gebruiken**
* **Protocol-TCP**
* Time-out voor inactieve **periode van 15 minuten**
* TCP opnieuw instellen inschakelen

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

#### <a name="add-vms-to-the-load-balancer-pool"></a>VM's toevoegen aan de load balancer groep

Voeg de virtuele machines toe aan de back-endpool [met az network nic ip-config address-pool add.](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add) Geef het volgende op:

* In adresgroep **myBackEndPool**
* In resourcegroep **CreateIntLBQS-rg**
* Gekoppeld aan de **netwerkinterface myNicVM1,** **myNicVM2** en **myNicVM3**
* Gekoppeld aan load balancer **myLoadBalancer**

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

In deze sectie maakt u een load balancer die taken van virtuele machines verdeelt. Wanneer u een interne load balancer maakt, wordt een virtueel netwerk geconfigureerd als netwerk voor de load balancer. Het volgende diagram bevat de resources die u in deze quickstart hebt gemaakt:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Basisresources load balancer gemaakt voor quickstart." border="false":::

### <a name="configure-the-virtual-network"></a>Het virtuele netwerk configureren

Voordat u VM's en uw load balancer implementeert, moet u de ondersteunende virtuele-netwerkresources maken.

#### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met [az network vnet create.](/cli/azure/network/vnet#az_network_vnet_createt) Geef het volgende op:

* Met de **naam myVNet**
* Adres **voorvoegsel van 10.1.0.0/16**
* Subnet met de **naam myBackendSubnet**
* Subnet-voorvoegsel **van 10.1.0.0/24**
* In de **resourcegroep CreateIntLBIRS-rg**
* Locatie van **eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Gebruik [az network public-ip create om](/cli/azure/network/public-ip#az_network_public_ip_create) een openbaar IP-adres te maken voor de Azure Bastion host. Geef het volgende op:

* Maak een standaard zone-redundant openbaar IP-adres met de **naam myBastionIP**
* In **CreateIntLBQS-rg**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Een Azure Bastion maken

Gebruik [az network vnet subnet create om](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) een subnet te maken. Geef het volgende op:

* Met **de naam AzureBastionSubnet**
* Adres voorvoegsel **van 10.1.1.0/24**
* In virtueel netwerk **myVNet**
* In resourcegroep **CreateIntLBIRS-rg**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Een Azure Bastion-host maken

Gebruik [az network bastion create om](/cli/azure/network/bastion#az_network_bastion_create) een host te maken. Geef het volgende op:

* met de naam **myBastionHost**.
* In **CreateIntLBQS-rg**
* Gekoppeld aan openbare IP **myBastionIP**
* Gekoppeld aan virtueel netwerk **myVNet**
* Op **locatie eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

De Azure Bastion kan een paar minuten nodig hebben om te implementeren.

#### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Voor een standaard load balancer moet u ervoor zorgen dat uw VM's netwerkinterfaces hebben die deel uitmaken van een netwerkbeveiligingsgroep. Maak een netwerkbeveiligingsgroep met [az network nsg create.](/cli/azure/network/nsg#az_network_nsg_create) Geef het volgende op:

* Met de **naam myNSG**
* In resourcegroep **CreateIntLBQS-rg**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Regel voor netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroepsregel met [az network nsg rule create.](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) Geef het volgende op:

* Met de **naam myNSGRuleHTTP**
* In de netwerkbeveiligingsgroep die u in de vorige stap hebt gemaakt, **myNSG**
* In resourcegroep **CreateIntLBQS-rg**
* Protocol **(*)**
* Richting **inkomende richting**
* Bron **(*)**
* Doel **(*)**
* Doelpoort **poort 80**
* Toegang **toestaan**
* Prioriteit **200**

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

### <a name="create-back-end-servers"></a>Back-endservers maken

In deze sectie maakt u:

* Drie netwerkinterfaces voor de virtuele machines.
* De beschikbaarheidsset voor de virtuele machines.
* Drie virtuele machines die moeten worden gebruikt als servers voor de load balancer.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Netwerkinterfaces maken voor de virtuele machines

Maak drie netwerkinterfaces [met az network nic create.](/cli/azure/network/nic#az_network_nic_create) Geef het volgende op:

* **MyNicVM1,** **myNicVM2** en **myNicVM3 genoemd**
* In resourcegroep **CreateIntLBIRS-rg**
* In virtueel netwerk **myVNet**
* In subnet **myBackendSubnet**
* In netwerkbeveiligingsgroep **myNSG**

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

#### <a name="create-the-availability-set-for-the-virtual-machines"></a>De beschikbaarheidsset voor de virtuele machines maken

Maak de beschikbaarheidsset [met az vm availability-set create.](/cli/azure/vm/availability-set#az_vm_availability_set_create) Geef het volgende op:

* Met **de naam myAvailabilitySet**
* In resourcegroep **CreateIntLBIRS-rg**
* Locatie **eastus**

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

#### <a name="create-the-virtual-machines"></a>De virtuele machines maken

Maak de virtuele machines met [az vm create](/cli/azure/vm#az_vm_create). Geef het volgende op:

* **MyVM1,** **myVM2** en **myVM3 genoemd**
* In resourcegroep **CreateIntLBIRS-rg**
* Gekoppeld aan de netwerkinterface **myNicVM1,** **myNicVM2** en **myNicVM3**
* Virtuele-machine-afbeelding **win2019datacenter**
* In **myAvailabilitySet**


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
Het kan enkele minuten duren voordat de VM's zijn geïmplementeerd.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]


### <a name="create-the-load-balancer"></a>Load balancer maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de load balancer kunt maken en configureren:

* Een IP-adresgroep die het binnenkomende netwerkverkeer op de load balancer.
* Een tweede IP-adresgroep, waarbij de eerste groep het netwerkverkeer met load balanced verzendt.
* Een statustest die de status van de VM-exemplaren bepaalt.
* Een load balancer-regel die bepaalt hoe het verkeer over de VM's wordt verdeeld.

#### <a name="create-the-load-balancer-resource"></a>De load balancer-resource maken

Maak een openbare load balancer met [az network lb create](/cli/azure/network/lb#az_network_lb_create). Geef het volgende op:

* Met de naam **myLoadBalancer**
* Een pool met de **naam myFrontEnd**
* Een pool met de **naam myBackEndPool**
* Gekoppeld aan het virtuele netwerk **myVNet**
* Gekoppeld aan het subnet **myBackendSubnet**

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

#### <a name="create-the-health-probe"></a>Statustest maken

Met een statustest worden alle VM-instanties gecontroleerd om na te gaan of ze netwerkverkeer kunnen verzenden. Een virtuele machine met een mislukte test wordt verwijderd uit de load balancer. De virtuele machine wordt weer toegevoegd aan de load balancer wanneer de fout is opgelost.

Maak een statustest met [az network lb probe create.](/cli/azure/network/lb/probe#az_network_lb_probe_create) Geef het volgende op:

* Controleert de status van de virtuele machines
* Met **de naam myHealthProbe**
* **Protocol-TCP**
* Poort **80 bewaken**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Met een load balancer-regel wordt het volgende gedefinieerd:

* De IP-configuratie voor het binnenkomende verkeer.
* De IP-adresgroep voor het ontvangen van het verkeer.
* De vereiste bron- en doelpoort. 

Gebruik [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) om een load balancer-regel te maken. Geef het volgende op:

* Naam: **myHTTPRule**
* Luisteren op **poort 80** in de pool **myFrontEnd**
* Netwerkverkeer met load balanced verzenden naar de adresgroep **myBackEndPool** met behulp van **poort 80** 
* Statustest **myHealthProbe gebruiken**
* **Protocol-TCP**
* Time-out voor inactieve **periode van 15 minuten**

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
#### <a name="add-vms-to-the-load-balancer-pool"></a>VM's toevoegen aan de load balancer groep

Voeg de virtuele machines toe aan de back-endpool [met az network nic ip-config address-pool add.](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add) Geef het volgende op:

* In adresgroep **myBackEndPool**
* In resourcegroep **CreateIntLBQS-rg**
* Gekoppeld aan de **netwerkinterface myNicVM1,** **myNicVM2** en **myNicVM3**
* Gekoppeld aan load balancer **myLoadBalancer**

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

Maak de netwerkinterface [met az network nic create](/cli/azure/network/nic#az_network_nic_create). Geef het volgende op:

* Met de **naam myNicTestVM**
* In resourcegroep **CreateIntLBIRS-rg**
* In virtueel netwerk **myVNet**
* In subnet **myBackendSubnet**
* In netwerkbeveiligingsgroep **myNSG**

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Maak de virtuele machine met [az vm create](/cli/azure/vm#az_vm_create). Geef het volgende op:

* Met de **naam myTestVM**
* In resourcegroep **CreateIntLBIRS-rg**
* Gekoppeld aan de netwerkinterface **myNicTestVM**
* Virtuele-machine-afbeelding **Win2019Datacenter**

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
Mogelijk moet u enkele minuten wachten tot de virtuele machine is geïmplementeerd.

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

2. Zoek op **de** pagina Overzicht het privé-IP-adres voor de load balancer. Selecteer in het menu aan de linkerkant **Alle services**  >  **Alle resources**  >  **myLoadBalancer.**

3. In het overzicht van **myLoadBalancer kopieert** u het adres naast **Privé-IP-adres**.

4. Selecteer in het menu aan de linkerkant **Alle services**  >  **Alle resources.** Selecteer in de lijst met resources in de resourcegroep **CreateIntLBIRS-rg** de optie **myTestVM.**

5. Selecteer op **de pagina** Overzicht de optie **Verbinding maken** met  >  **Bastion**.

6. Voer de gebruikersnaam en het wachtwoord in die u hebt ingevoerd tijdens het maken van de VM.

7. Open **op myTestVM** **de Internet Explorer**.

8. Voer het IP-adres uit de vorige stap in in de adresbalk van de browser. De standaardpagina van de IIS-webserver wordt weergegeven in de browser.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Schermopname van het IP-adres in de adresbalk van de browser." border="true":::
   
Als u de load balancer verkeer wilt verdelen over alle drie de VM's, kunt u de standaardpagina van de IIS-webserver van elke VM aanpassen. Vernieuw vervolgens handmatig uw webbrowser vanaf de clientmachine.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer uw resources niet meer nodig zijn, gebruikt u de [opdracht az group delete](/cli/azure/group#az_group_delete) om de resourcegroep, load balancer en alle gerelateerde resources te verwijderen.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Volgende stappen

Bekijk een overzicht van Azure Load Balancer.
> [!div class="nextstepaction"]
> [Wat is Azure Load Balancer?](load-balancer-overview.md)
