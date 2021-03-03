---
title: 'Quickstart: Een interne load balancer maken - Azure CLI'
titleSuffix: Azure Load Balancer
description: In deze Quick start ziet u hoe u een interne load balancer maakt met behulp van de Azure CLI.
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
ms.openlocfilehash: 10ac477bed97d2a48344aa8ef9b570d2b6203345
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "101702604"
---
# <a name="quickstart-create-an-internal-load-balancer-by-using-the-azure-cli"></a>Snelstartgids: een interne load balancer maken met behulp van de Azure CLI

Ga aan de slag met Azure Load Balancer met behulp van de Azure CLI om een interne load balancer en drie virtuele machines te maken.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

Voor deze quickstart is versie 2.0.28 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

>[!NOTE]
>Azure Load Balancer Standard is de aanbevolen keuze voor productie werkbelastingen. Dit artikel bevat informatie over Azure Load Balancer Standard, en Azure Load Balancer Basic. Zie [Azure Load Balancer sku's](skus.md)voor meer informatie over sku's.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resource groep is een logische container waarin u uw Azure-resources implementeert en beheert.

Maak een resourcegroep maken met [az group create](/cli/azure/group#az_group_create). Noem de resource groep **CreateIntLBQS-RG** en geef de locatie op als **Oost-Timor**.

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```

## <a name="azure-load-balancer-standard"></a>Azure Load Balancer standaard

In deze sectie maakt u een load balancer die taken van virtuele machines verdeelt. Wanneer u een interne load balancer maakt, wordt een virtueel netwerk geconfigureerd als netwerk voor de load balancer. Het volgende diagram bevat de resources die u in deze quickstart hebt gemaakt:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Resources voor de Standard-load balancer die worden gemaakt voor de quickstart." border="false":::

### <a name="configure-the-virtual-network"></a>Het virtuele netwerk configureren

Voordat u VM's en uw load balancer implementeert, moet u de ondersteunende virtuele-netwerkresources maken.

#### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met [AZ Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create). Geef het volgende op:

* Met de naam **myVNet**
* Adres voorvoegsel van **10.1.0.0/16**
* Subnet met de naam **myBackendSubnet**
* Subnetvoorvoegsel van **10.1.0.0/24**
* In de resource groep **CreateIntLBQS-RG**
* Locatie van **Oost** -US

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

Gebruik [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) om een openbaar IP-adres te maken voor de Azure bastion-host. Geef het volgende op:

* Maak een standaard zone-redundant openbaar IP-adres met de naam **myBastionIP**
* In **CreateIntLBQS-rg**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Een Azure Bastion-subnet maken

Gebruik [AZ Network vnet subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) om een subnet te maken. Geef het volgende op:

* Met de naam **AzureBastionSubnet**
* Adres voorvoegsel van **10.1.1.0/24**
* In virtueel netwerk **myVNet**
* In resource groep **CreateIntLBQS-RG**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Een Azure Bastion-host maken

Gebruik [AZ Network Bastion Create](/cli/azure/network/bastion#az-network-bastion-create) om een host te maken. Geef het volgende op:

* met de naam **myBastionHost**.
* In **CreateIntLBQS-rg**
* Gekoppeld aan open bare IP- **myBastionIP**
* Gekoppeld aan virtueel netwerk **myVNet**
* Locatie van **Oost**

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

Zorg ervoor dat uw virtuele machines een netwerk interface hebben die deel uitmaakt van een netwerk beveiligings groep voor een standaard load balancer. Maak een netwerk beveiligings groep met behulp van [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create). Geef het volgende op:

* Met de naam **mijnnbg**
* In resource groep **CreateIntLBQS-RG**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Regel voor netwerkbeveiligingsgroep maken

Maak een regel voor de netwerk beveiligings groep met behulp van [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create). Geef het volgende op:

* Met de naam **myNSGRuleHTTP**
* In de netwerk beveiligings groep die u in de vorige stap hebt gemaakt, **mijnnbg**
* In resource groep **CreateIntLBQS-RG**
* Protocol **(*)**
* Richting **Inkomend**
* Bron **(*)**
* Doel **(*)**
* Doel poort **poort 80**
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

Maak drie netwerk interfaces met [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create). Geef het volgende op:

* Met de naam **myNicVM1**, **myNicVM2** en **myNicVM3**
* In resource groep **CreateIntLBQS-RG**
* In virtueel netwerk **myVNet**
* In subnet **myBackendSubnet**
* In netwerk beveiligings groep **mijnnbg**

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

Maak de virtuele machines met [az vm create](/cli/azure/vm#az-vm-create). Geef het volgende op:

* Met de naam **myVM1**, **myVM2** en **myVM3**
* In resource groep **CreateIntLBQS-RG**
* Gekoppeld aan de netwerk interface **myNicVM1**, **myNicVM2** en **myNicVM3**
* Installatie kopie van virtuele machine **win2019datacenter**
* In **zone 1**, **zone 2** en **zone 3**

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

Het kan een paar minuten duren voordat de Vm's zijn geïmplementeerd.

### <a name="create-the-load-balancer"></a>Load balancer maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de load balancer kunt maken en configureren:

* Een IP-adres groep die het binnenkomende netwerk verkeer op de load balancer ontvangt.
* Een tweede IP-adres groep, waarbij de eerste pool het netwerk verkeer met gelijke taak verdeling verzendt.
* Een status test die de status van de VM-exemplaren bepaalt.
* Een load balancer-regel die bepaalt hoe het verkeer over de VM's wordt verdeeld.

#### <a name="create-the-load-balancer-resource"></a>De load balancer-resource maken

Maak een open bare load balancer met [AZ Network lb Create](/cli/azure/network/lb#az-network-lb-create). Geef het volgende op:

* Met de naam **myLoadBalancer**
* Een groep met de naam **myFrontEnd**
* Een groep met de naam **myBackEndPool**
* Gekoppeld aan de **myVNet** van het virtuele netwerk
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

Maak een status test met [AZ Network lb probe Create](/cli/azure/network/lb/probe#az-network-lb-probe-create). Geef het volgende op:

* Bewaakt de status van de virtuele machines
* Met de naam **myHealthProbe**
* Protocol **TCP**
* **Poort 80** controleren

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
* De IP-adres groep voor het ontvangen van het verkeer.
* De vereiste bron- en doelpoort. 

Gebruik [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) om een load balancer-regel te maken. Geef het volgende op:

* Naam: **myHTTPRule**
* Luis teren op **poort 80** in de groep **myFrontEnd**
* Netwerk verkeer met taak verdeling naar de adres groep **myBackEndPool** verzenden met behulp van **poort 80** 
* **MyHealthProbe** voor Health probe gebruiken
* Protocol **TCP**
* Time-out voor niet-actief van **15 minuten**
* TCP Reset inschakelen

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

#### <a name="add-vms-to-the-load-balancer-pool"></a>Vm's toevoegen aan de load balancer pool

Voeg de virtuele machines toe aan de back-end-groep met [AZ Network NIC IP-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add). Geef het volgende op:

* In adres groep **myBackEndPool**
* In resource groep **CreateIntLBQS-RG**
* Gekoppeld aan de netwerk interface **myNicVM1**, **myNicVM2** en **myNicVM3**
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

## <a name="azure-load-balancer-basic"></a>Azure Load Balancer Basic

In deze sectie maakt u een load balancer die taken van virtuele machines verdeelt. Wanneer u een interne load balancer maakt, wordt een virtueel netwerk geconfigureerd als netwerk voor de load balancer. Het volgende diagram bevat de resources die u in deze quickstart hebt gemaakt:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Basis load balancer resources die zijn gemaakt voor Quick Start." border="false":::

### <a name="configure-the-virtual-network"></a>Het virtuele netwerk configureren

Voordat u VM's en uw load balancer implementeert, moet u de ondersteunende virtuele-netwerkresources maken.

#### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met [AZ Network vnet Create](/cli/azure/network/vnet#az-network-vnet-createt). Geef het volgende op:

* Met de naam **myVNet**
* Adres voorvoegsel van **10.1.0.0/16**
* Subnet met de naam **myBackendSubnet**
* Subnetvoorvoegsel van **10.1.0.0/24**
* In de resource groep **CreateIntLBQS-RG**
* Locatie van **Oost** -US

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

Gebruik [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) om een openbaar IP-adres te maken voor de Azure bastion-host. Geef het volgende op:

* Maak een standaard zone-redundant openbaar IP-adres met de naam **myBastionIP**
* In **CreateIntLBQS-rg**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Een Azure Bastion-subnet maken

Gebruik [AZ Network vnet subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) om een subnet te maken. Geef het volgende op:

* Met de naam **AzureBastionSubnet**
* Adres voorvoegsel van **10.1.1.0/24**
* In virtueel netwerk **myVNet**
* In resource groep **CreateIntLBQS-RG**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Een Azure Bastion-host maken

Gebruik [AZ Network Bastion Create](/cli/azure/network/bastion#az-network-bastion-create) om een host te maken. Geef het volgende op:

* met de naam **myBastionHost**.
* In **CreateIntLBQS-rg**
* Gekoppeld aan open bare IP- **myBastionIP**
* Gekoppeld aan virtueel netwerk **myVNet**
* Locatie van **Oost**

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

Zorg ervoor dat uw virtuele machines een netwerk interface hebben die deel uitmaakt van een netwerk beveiligings groep voor een standaard load balancer. Maak een netwerk beveiligings groep met behulp van [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create). Geef het volgende op:

* Met de naam **mijnnbg**
* In resource groep **CreateIntLBQS-RG**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Regel voor netwerkbeveiligingsgroep maken

Maak een regel voor de netwerk beveiligings groep met behulp van [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create). Geef het volgende op:

* Met de naam **myNSGRuleHTTP**
* In de netwerk beveiligings groep die u in de vorige stap hebt gemaakt, **mijnnbg**
* In resource groep **CreateIntLBQS-RG**
* Protocol **(*)**
* Richting **Inkomend**
* Bron **(*)**
* Doel **(*)**
* Doel poort **poort 80**
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

Maak drie netwerk interfaces met [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create). Geef het volgende op:

* Met de naam **myNicVM1**, **myNicVM2** en **myNicVM3**
* In resource groep **CreateIntLBQS-RG**
* In virtueel netwerk **myVNet**
* In subnet **myBackendSubnet**
* In netwerk beveiligings groep **mijnnbg**

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

Maak de beschikbaarheidsset met [AZ VM Availability-set Create](/cli/azure/vm/availability-set#az-vm-availability-set-create). Geef het volgende op:

* Met de naam **myAvailabilitySet**
* In resource groep **CreateIntLBQS-RG**
* Locatie **oostus**

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

#### <a name="create-the-virtual-machines"></a>De virtuele machines maken

Maak de virtuele machines met [az vm create](/cli/azure/vm#az-vm-create). Geef het volgende op:

* Met de naam **myVM1**, **myVM2** en **myVM3**
* In resource groep **CreateIntLBQS-RG**
* Gekoppeld aan de netwerk interface **myNicVM1**, **myNicVM2** en **myNicVM3**
* Installatie kopie van virtuele machine **win2019datacenter**
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
Het kan een paar minuten duren voordat de Vm's zijn geïmplementeerd.

### <a name="create-the-load-balancer"></a>Load balancer maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de load balancer kunt maken en configureren:

* Een IP-adres groep die het binnenkomende netwerk verkeer op de load balancer ontvangt.
* Een tweede IP-adres groep, waarbij de eerste pool het netwerk verkeer met gelijke taak verdeling verzendt.
* Een status test die de status van de VM-exemplaren bepaalt.
* Een load balancer-regel die bepaalt hoe het verkeer over de VM's wordt verdeeld.

#### <a name="create-the-load-balancer-resource"></a>De load balancer-resource maken

Maak een open bare load balancer met [AZ Network lb Create](/cli/azure/network/lb#az-network-lb-create). Geef het volgende op:

* Met de naam **myLoadBalancer**
* Een groep met de naam **myFrontEnd**
* Een groep met de naam **myBackEndPool**
* Gekoppeld aan de **myVNet** van het virtuele netwerk
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

Maak een status test met [AZ Network lb probe Create](/cli/azure/network/lb/probe#az-network-lb-probe-create). Geef het volgende op:

* Bewaakt de status van de virtuele machines
* Met de naam **myHealthProbe**
* Protocol **TCP**
* **Poort 80** controleren

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
* De IP-adres groep voor het ontvangen van het verkeer.
* De vereiste bron- en doelpoort. 

Gebruik [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) om een load balancer-regel te maken. Geef het volgende op:

* Naam: **myHTTPRule**
* Luis teren op **poort 80** in de groep **myFrontEnd**
* Netwerk verkeer met taak verdeling naar de adres groep **myBackEndPool** verzenden met behulp van **poort 80** 
* **MyHealthProbe** voor Health probe gebruiken
* Protocol **TCP**
* Time-out voor niet-actief van **15 minuten**

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
#### <a name="add-vms-to-the-load-balancer-pool"></a>Vm's toevoegen aan de load balancer pool

Voeg de virtuele machines toe aan de back-end-groep met [AZ Network NIC IP-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add). Geef het volgende op:

* In adres groep **myBackEndPool**
* In resource groep **CreateIntLBQS-RG**
* Gekoppeld aan de netwerk interface **myNicVM1**, **myNicVM2** en **myNicVM3**
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

## <a name="test-the-load-balancer"></a>Load balancer testen

Maak de netwerk interface met [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create). Geef het volgende op:

* Met de naam **myNicTestVM**
* In resource groep **CreateIntLBQS-RG**
* In virtueel netwerk **myVNet**
* In subnet **myBackendSubnet**
* In netwerk beveiligings groep **mijnnbg**

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Maak de virtuele machine met [az vm create](/cli/azure/vm#az-vm-create). Geef het volgende op:

* Met de naam **myTestVM**
* In resource groep **CreateIntLBQS-RG**
* Gekoppeld aan de netwerk interface **myNicTestVM**
* Installatie kopie van virtuele machine **Win2019Datacenter**

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
Mogelijk moet u enkele minuten wachten totdat de virtuele machine is geïmplementeerd.

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

2. Zoek op de pagina **overzicht** het privé-IP-adres voor de Load Balancer. Selecteer in het menu aan de linkerkant **alle services**  >  **alle resources**  >  **myLoadBalancer**.

3. Kopieer in het overzicht van **myLoadBalancer** het adres naast het **privé-IP-adres**.

4. Selecteer **alle services** alle resources in het menu aan de linkerkant  >  . Selecteer in de lijst met resources in de resource groep **CreateIntLBQS-RG** **myTestVM**.

5. Selecteer op de pagina **overzicht** de optie **verbinding maken met**  >  **Bastion**.

6. Voer de gebruikers naam en het wacht woord in die u hebt ingevoerd tijdens het maken van de virtuele machine.

7. Open **Internet Explorer** op **myTestVM**.

8. Voer het IP-adres uit de vorige stap in in de adresbalk van de browser. De standaard pagina van de IIS-webserver wordt weer gegeven in de browser.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Scherm opname van het IP-adres in de adres balk van de browser." border="true":::
   
U kunt de standaard pagina van de IIS-webserver van elke virtuele machine aanpassen om de load balancer distributie van verkeer over alle drie Vm's te bekijken. Vernieuw vervolgens de webbrowser hand matig van de client computer.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer uw resources niet meer nodig zijn, gebruikt u de opdracht [AZ Group delete](/cli/azure/group#az-group-delete) om de resource groep, Load Balancer en alle gerelateerde resources te verwijderen.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Volgende stappen

Bekijk een overzicht van Azure Load Balancer.
> [!div class="nextstepaction"]
> [Wat is Azure Load Balancer?](load-balancer-overview.md)
