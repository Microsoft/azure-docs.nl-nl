---
title: Azure Firewall implementeren en configureren met behulp van Azure CLI
description: In dit artikel vindt u informatie over het implementeren en configureren van Azure Firewall met behulp van de Azure CLI.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 08/29/2019
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 4d611a6d575fbc94a555006882f77e5a31753164
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98051425"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-cli"></a>Azure Firewall implementeren en configureren met behulp van Azure CLI

Het beheren van toegang tot uitgaande netwerken is een belangrijk onderdeel van een algemeen netwerkbeveiligingsabonnement. U kunt bijvoorbeeld de toegang tot websites beperken. U kunt ook de toegang tot uitgaande IP-adressen en poorten beperken.

Een van de manieren waarop u de toegang tot uitgaande netwerken kunt beheren vanaf een Azure-subnet is met Azure Firewall. Met Azure Firewall kunt u het volgende configureren:

* Toepassingsregels die volledig gekwalificeerde domeinnamen (FQDN's) definiëren waartoe toegang kan worden verkregen via een subnet. De FQDN kan ook [SQL-exemplaren bevatten](sql-fqdn-filtering.md).
* Netwerkregels die een bronadres, protocol, doelpoort en doeladres definiëren.

Netwerkverkeer is onderhevig aan de geconfigureerde firewallregels wanneer u het routeert naar de firewall als standaardgateway van het subnet.

Voor dit artikel maakt u een vereenvoudigd single VNet met drie subnetten voor een eenvoudige implementatie. Voor productie-implementaties wordt een [hub-en-spoke-model](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) aanbevolen. De firewall bevindt zich in een eigen VNet. De werkbelastingservers bevinden zich in gepeerde VNets in dezelfde regio met een of meer subnetten.

* **AzureFirewallSubnet** – De firewall bevindt zich in dit subnet.
* **Workload-SN** – De workloadserver bevindt zich in dit subnet. Het netwerkverkeer van dit subnet gaat via de firewall.
* **Jump-SN** – De "jumpserver" bevindt zich in dit subnet. De jumpserver heeft een openbaar IP-adres waarmee u verbinding kunt maken via een extern bureaublad. Van daaruit kunt u vervolgens (via een ander extern bureaublad) verbinding maken met de workloadserver.

![Netwerkinfrastructuur van zelfstudie](media/tutorial-firewall-rules-portal/Tutorial_network.png)

In dit artikel leert u het volgende:

* Een testnetwerkomgeving instellen
* Een firewall implementeren
* Een standaardroute maken
* Een toepassingsregel configureren om toegang tot www.google.com toe te staan
* Een netwerkregel configureren om toegang tot externe DNS-servers toe te staan
* De firewall testen

Als u wilt, kunt u deze procedure volt ooien met behulp van de [Azure Portal](tutorial-firewall-deploy-portal.md) of [Azure PowerShell](deploy-ps.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.4 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="set-up-the-network"></a>Het netwerk instellen

Maak eerst een resourcegroep met de resources die nodig zijn om de firewall te implementeren. Maak vervolgens een VNet, subnetten en testservers.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

De resource groep bevat alle resources voor de implementatie.

```azurecli-interactive
az group create --name Test-FW-RG --location eastus
```

### <a name="create-a-vnet"></a>Een VNet maken

Dit virtuele netwerk heeft drie subnetten.

> [!NOTE]
> De grootte van het subnet AzureFirewallSubnet is /26. Zie [Veelgestelde vragen over Azure Firewall](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size) voor meer informatie over de grootte van het subnet.

```azurecli-interactive
az network vnet create \
  --name Test-FW-VN \
  --resource-group Test-FW-RG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name AzureFirewallSubnet \
  --subnet-prefix 10.0.1.0/26
az network vnet subnet create \
  --name Workload-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.2.0/24
az network vnet subnet create \
  --name Jump-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.3.0/24
```

### <a name="create-virtual-machines"></a>Virtuele machines maken

Maak nu de virtuele jump- en workloadmachines en plaats ze in de toepasselijke subnetten.
Typ een wacht woord voor de virtuele machine wanneer u hierom wordt gevraagd.

Maak de virtuele machine van Srv-Jump.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Jump \
    --location eastus \
    --image win2016datacenter \
    --vnet-name Test-FW-VN \
    --subnet Jump-SN \
    --admin-username azureadmin
az vm open-port --port 3389 --resource-group Test-FW-RG --name Srv-Jump
```



Maak een NIC voor Srv-Work met specifieke IP-adressen van de DNS-server en geen openbaar IP-adres om met te testen.

```azurecli-interactive
az network nic create \
    -g Test-FW-RG \
    -n Srv-Work-NIC \
   --vnet-name Test-FW-VN \
   --subnet Workload-SN \
   --public-ip-address "" \
   --dns-servers 209.244.0.3 209.244.0.4
```

Maak nu de virtuele workload-machine.
Typ een wacht woord voor de virtuele machine wanneer u hierom wordt gevraagd.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Work \
    --location eastus \
    --image win2016datacenter \
    --nics Srv-Work-NIC \
    --admin-username azureadmin
```

## <a name="deploy-the-firewall"></a>De firewall implementeren

Implementeer de firewall nu in het virtuele netwerk.

```azurecli-interactive
az network firewall create \
    --name Test-FW01 \
    --resource-group Test-FW-RG \
    --location eastus
az network public-ip create \
    --name fw-pip \
    --resource-group Test-FW-RG \
    --location eastus \
    --allocation-method static \
    --sku standard
az network firewall ip-config create \
    --firewall-name Test-FW01 \
    --name FW-config \
    --public-ip-address fw-pip \
    --resource-group Test-FW-RG \
    --vnet-name Test-FW-VN
az network firewall update \
    --name Test-FW01 \
    --resource-group Test-FW-RG 
az network public-ip show \
    --name fw-pip \
    --resource-group Test-FW-RG
fwprivaddr="$(az network firewall ip-config list -g Test-FW-RG -f Test-FW01 --query "[?name=='FW-config'].privateIpAddress" --output tsv)"
```

Noteer het privé-IP-adres. U zult het later gebruiken wanneer u de standaardroute maakt.

## <a name="create-a-default-route"></a>Een standaardroute maken

Een tabel maken waarbij BGP-route doorgifte is uitgeschakeld

```azurecli-interactive
az network route-table create \
    --name Firewall-rt-table \
    --resource-group Test-FW-RG \
    --location eastus \
    --disable-bgp-route-propagation true
```

Maak de route.

```azurecli-interactive
az network route-table route create \
  --resource-group Test-FW-RG \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $fwprivaddr
```

De route tabel koppelen aan het subnet

```azurecli-interactive
az network vnet subnet update \
    -n Workload-SN \
    -g Test-FW-RG \
    --vnet-name Test-FW-VN \
    --address-prefixes 10.0.2.0/24 \
    --route-table Firewall-rt-table
```

## <a name="configure-an-application-rule"></a>Een toepassingsregel configureren

De toepassings regel staat uitgaande toegang tot www.google.com toe.

```azurecli-interactive
az network firewall application-rule create \
   --collection-name App-Coll01 \
   --firewall-name Test-FW01 \
   --name Allow-Google \
   --protocols Http=80 Https=443 \
   --resource-group Test-FW-RG \
   --target-fqdns www.google.com \
   --source-addresses 10.0.2.0/24 \
   --priority 200 \
   --action Allow
```

Azure Firewall bevat een ingebouwde regelverzameling voor infrastructuur-FQDN’s die standaard zijn toegestaan. Deze FQDN’s zijn specifiek voor het platform en kunnen niet voor andere doeleinden worden gebruikt. Zie [FQDN's voor infrastructuur](infrastructure-fqdns.md) voor meer informatie.

## <a name="configure-a-network-rule"></a>Een netwerkregel configureren

De netwerk regel staat uitgaande toegang tot twee IP-adressen op poort 53 (DNS) toe.

```azurecli-interactive
az network firewall network-rule create \
   --collection-name Net-Coll01 \
   --destination-addresses 209.244.0.3 209.244.0.4 \
   --destination-ports 53 \
   --firewall-name Test-FW01 \
   --name Allow-DNS \
   --protocols UDP \
   --resource-group Test-FW-RG \
   --priority 200 \
   --source-addresses 10.0.2.0/24 \
   --action Allow
```

## <a name="test-the-firewall"></a>De firewall testen

Test nu de firewall om te controleren of deze werkt zoals verwacht.

1. Noteer het privé-IP-adres voor de virtuele machine **met SRV-werk** :

   ```azurecli-interactive
   az vm list-ip-addresses \
   -g Test-FW-RG \
   -n Srv-Work
   ```

1. Verbind een extern bureaublad met de virtuele machine **Srv-Jump** en meld u aan. Open vanaf daar een verbinding met een extern bureau blad met het privé-IP-adres van **SRV-werk** en meld u aan.

3. Open op **SRV** een Power shell-venster en voer de volgende opdrachten uit:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Beide opdrachten moeten antwoorden retour neren, zodat u kunt zien dat uw DNS-query's via de firewall worden ontvangen.

1. Voer de volgende opdrachten uit:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   De `www.google.com` aanvragen moeten slagen en de `www.microsoft.com` aanvragen moeten mislukken. Dit laat zien dat uw firewall regels werken zoals verwacht.

Nu u hebt geverifieerd dat de firewallregels werken:

* Kunt u DNS-namen omzetten met behulp van de geconfigureerde externe DNS-server.
* Kunt u bladeren naar de enige toegestane FQDN, maar niet naar andere.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt uw firewall bronnen voor de volgende zelf studie houden, of als u deze niet meer nodig hebt, verwijdert u de resource groep **test-FW-RG** om alle firewall-gerelateerde resources te verwijderen:

```azurecli-interactive
az group delete \
  -n Test-FW-RG
```

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Azure Firewall-logboeken bewaken](./firewall-diagnostics.md)