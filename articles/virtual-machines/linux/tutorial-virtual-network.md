---
title: "Zelfstudie: virtuele Azure-netwerken maken en beheren voor Linux-VM's"
description: In deze zelfstudie leert u hoe u Azure CLI gebruikt voor het maken en beheren van virtuele Azure-netwerken voor virtuele Linux-machines
services: virtual-machines
author: cynthn
ms.collection: linux
ms.subservice: networking
ms.service: virtual-machines
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cfc76ffd05b6348c9a585a64e3f2868f28f5e5cd
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811702"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-linux-virtual-machines-with-the-azure-cli"></a>Zelfstudie: Virtuele Azure-netwerken voor virtuele Linux-machines maken en beheren met de Azure CLI

Virtuele Azure-machines maken gebruik van Azure-netwerken voor interne en externe communicatie. In deze zelfstudie wordt uitgelegd hoe u twee virtuele machines implementeert en Azure-netwerken configureert voor deze virtuele machines. In de voorbeelden in deze zelfstudie wordt ervan uitgegaan dat de virtuele machines een webtoepassing met een databaseback-end hosten, maar er wordt geen toepassing geïmplementeerd in de zelfstudie. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtueel netwerk en een subnet maken
> * Een openbaar IP-adres maken
> * Een front-end virtuele machine maken
> * Netwerkverkeer beveiligen
> * Een back-end virtuele machine maken

In deze zelfstudie wordt gebruikgemaakt van de CLI in de [Azure Cloud Shell](../../cloud-shell/overview.md), die voortdurend wordt bijgewerkt naar de nieuwste versie. Als u de Cloud Shell wilt openen, selecteert u **Probeer het** bovenaan een willekeurig codeblok.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.0.30 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="vm-networking-overview"></a>Overzicht van VM-netwerken

Virtuele Azure-netwerken maken beveiligde netwerkverbindingen mogelijk tussen virtuele machines, internet en andere Azure-services zoals Azure SQL Database. Virtuele netwerken zijn onderverdeeld in logische segmenten die subnetten worden genoemd. Subnetten worden gebruikt om de netwerkstroom te beheersen, en als een beveiligingsgrens. Wanneer een virtuele machine wordt geïmplementeerd, omvat deze doorgaans een virtuele netwerkinterface, die is gekoppeld aan een subnet.

Wanneer u de zelfstudie voltooit, worden de volgende virtuele-netwerkbronnen gemaakt:

![Virtueel netwerk met twee subnetten](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet*: het virtuele netwerk dat de virtuele machines gebruiken om met elkaar en met internet te communiceren.
- *myFrontendSubnet*: het subnet in *myVNet* dat door de front-endresources wordt gebruikt.
- *myPublicIPAddress*: het openbare IP-adres dat wordt gebruikt voor toegang tot *myFrontendVM* via internet.
- *myFrontentNic*: de netwerkinterface die wordt gebruikt door *myFrontendVM* om te communiceren met *myBackendVM*.
- *myFrontendVM*: de VM die wordt gebruikt voor communicatie tussen internet en *myBackendVM*.
- *myBackendNSG*: de netwerkbeveiligingsgroep waardoor de communicatie tussen de *myFrontendVM* en *myBackendVM* wordt bepaald.
- *myBackendSubnet*: het subnet dat is gekoppeld aan *myBackendNSG* en door de back-endresources wordt gebruikt.
- *myBackendNic*: de netwerkinterface die wordt gebruikt door *myBackendVM* om te communiceren met *myFrontendVM*.
- *myBackendVM*: de virtuele machine die gebruikmaakt van poort 22 en 3306 om te communiceren met *myFrontendVM*.

## <a name="create-a-virtual-network-and-subnet"></a>Een virtueel netwerk en een subnet maken

Voor deze zelfstudie wordt één virtueel netwerk met twee subnetten gemaakt. Een front-endsubnet voor het hosten van een webtoepassing en een back-endsubnet voor het hosten van een databaseserver.

Voordat u een virtueel netwerk kunt maken, moet u eerst een resourcegroep maken met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myRGNetwork* gemaakt op de locatie VS Oost.

```azurecli-interactive 
az group create --name myRGNetwork --location eastus
```

### <a name="create-virtual-network"></a>Virtueel netwerk maken

Gebruik de opdracht [az network vnet create](/cli/azure/network/vnet) om een virtueel netwerk te maken. In dit voorbeeld krijgt het netwerk de naam *mvVNet* en een adresvoorvoegsel van *10.0.0.0/16*. Er wordt ook een subnet gemaakt met de naam *myFrontendSubnet* en het voorvoegsel *10.0.1.0/24*. Verderop in deze zelfstudie wordt een front-end VM verbonden met dit subnet. 

```azurecli-interactive 
az network vnet create \
  --resource-group myRGNetwork \
  --name myVNet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myFrontendSubnet \
  --subnet-prefix 10.0.1.0/24
```

### <a name="create-subnet"></a>Subnet maken

Er wordt een nieuw subnet toegevoegd aan het virtuele netwerk met de opdracht [az network vnet subnet create](/cli/azure/network/vnet/subnet). In dit voorbeeld heeft het subnet de naam *myBackendSubnet* en een adresvoorvoegsel van *10.0.2.0/24*. Dit subnet wordt gebruikt met alle back-endservices.

```azurecli-interactive 
az network vnet subnet create \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --address-prefix 10.0.2.0/24
```

Op dit moment is er een netwerk gemaakt en gesegmenteerd in twee subnetten, één voor de front-endservices en een andere voor back-endservices. In de volgende sectie worden de virtuele machines gemaakt en verbonden met deze subnetten.

## <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Met een openbaar IP-adres kunnen Azure-resources toegankelijk zijn via internet. De toewijzingsmethode van het openbare IP-adres kan worden geconfigureerd als dynamisch of statisch. Een openbaar IP-adres wordt standaard dynamisch toegewezen. Dynamische IP-adressen worden vrijgegeven wanneer de toewijzing van een virtuele machine ongedaan wordt gemaakt. Dit gedrag zorgt ervoor dat het IP-adres wijzigt tijdens een bewerking waarbij de toewijzing van een virtuele machine ongedaan wordt gemaakt.

De toewijzingsmethode kan worden ingesteld op statisch. Dit zorgt ervoor dat het IP-adres aan een virtuele machine toegewezen blijft, zelfs bij een status waarin de toewijzing ongedaan is gemaakt. Wanneer u een statisch toegewezen IP-adres gebruikt, kan het IP-adres zelf niet worden opgegeven. Dit wordt toegewezen uit een groep met beschikbare adressen.

```azurecli-interactive
az network public-ip create --resource-group myRGNetwork --name myPublicIPAddress
```

Wanneer een virtuele machine wordt gemaakt met de opdracht [az vm create](/cli/azure/vm), worden openbare IP-adressen standaard dynamisch toegewezen. Wanneer een virtuele machine wordt gemaakt met de opdracht [az vm create](/cli/azure/vm), neemt u het argument `--public-ip-address-allocation static` op om een statisch openbaar IP-adres toe te wijzen. Deze bewerking wordt niet geïllustreerd in deze zelfstudie, maar in de volgende sectie wordt een dynamisch toegewezen IP-adres gewijzigd in een statisch toegewezen adres. 

### <a name="change-allocation-method"></a>Toewijzingsmethode wijzigen

De toewijzingsmethode voor het IP-adres kan worden gewijzigd met de opdracht [az network public-ip update](/cli/azure/network/public-ip). In dit voorbeeld wordt de toewijzingsmethode voor het IP-adres van de front-end VM gewijzigd in statisch.

Maak eerst de toewijzing van de virtuele machine ongedaan.

```azurecli-interactive 
az vm deallocate --resource-group myRGNetwork --name myFrontendVM
```

Gebruik de opdracht [az network public-ip update](/cli/azure/network/public-ip) om de toewijzingsmethode bij te werken. In dit geval wordt de `--allocation-method` ingesteld op *statisch*.

```azurecli-interactive 
az network public-ip update --resource-group myRGNetwork --name myPublicIPAddress --allocation-method static
```

Start de virtuele machine.

```azurecli-interactive 
az vm start --resource-group myRGNetwork --name myFrontendVM --no-wait
```

### <a name="no-public-ip-address"></a>Geen openbaar IP-adres

Een virtuele machine hoeft vaak niet toegankelijk zijn via internet. Gebruik voor het maken van een virtuele machine zonder een openbaar IP-adres het argument `--public-ip-address ""` met een lege set dubbele aanhalingstekens. Deze configuratie wordt later in deze zelfstudie geïllustreerd.

## <a name="create-a-front-end-vm"></a>Een front-end virtuele machine maken

Gebruik de opdracht [az vm create](/cli/azure/vm) om de virtuele machine met de naam *myFrontendVM* te maken met *myPublicIPAddress*.

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --vnet-name myVNet \
  --subnet myFrontendSubnet \
  --nsg myFrontendNSG \
  --public-ip-address myPublicIPAddress \
  --image UbuntuLTS \
  --generate-ssh-keys
```

## <a name="secure-network-traffic"></a>Netwerkverkeer beveiligen

Een netwerkbeveiligingsgroep (Network Security Group, NSG) bevat een lijst met beveiligingsregels waarmee netwerkverkeer naar resources die zijn verbonden met virtuele Azure-netwerken (VNet) wordt toegestaan of geweigerd. Netwerkbeveiligingsgroepen kunnen worden gekoppeld aan subnetten of afzonderlijke netwerkinterfaces. Wanneer een netwerkbeveiligingsgroep is gekoppeld aan een netwerkinterface, is deze alleen van toepassing op de gekoppelde virtuele machine. Wanneer een NSG is gekoppeld aan een subnet, zijn de regels van toepassing op alle resources die zijn verbonden met het subnet. 

### <a name="network-security-group-rules"></a>Regels voor netwerkbeveiligingsgroepen

Met regels voor netwerkbeveiligingsgroepen worden netwerkpoorten gedefinieerd waarover verkeer wordt toegestaan of geweigerd. De regels kunnen bron- en doel-IP-adresbereiken bevatten, zodat verkeer wordt geregeld tussen specifieke systemen of subnetten. Regels voor netwerkbeveiligingsgroepen bevatten ook een prioriteit (tussen 1- en 4096). Regels worden in volgorde van prioriteit gecontroleerd. Een regel met een prioriteit van 100 wordt geëvalueerd vóór een regel met een prioriteit van 200.

Alle NSG's bevatten een set met standaardregels. De standaardregels kunnen niet worden verwijderd, maar omdat ze de laagste prioriteit hebben, kunnen ze worden overschreven door de regels die u maakt.

De standaardregels voor netwerkbeveiligingsgroepen zijn:

- **Virtueel netwerk**: verkeer dat afkomstig is van en eindigt in een virtueel netwerk wordt toegestaan in zowel binnenkomende als uitgaande richting.
- **Internet**: uitgaand verkeer is toegestaan, maar binnenkomend verkeer wordt geblokkeerd.
- **Load balancer:** : toestaan dat de load balancer van Azure de status van uw VM's en rolexemplaren controleert. Als u geen set met taakverdeling gebruikt, kunt u deze regel onderdrukken.

### <a name="create-network-security-groups"></a>Netwerkbeveiligingsgroepen maken

Een netwerkbeveiligingsgroep kan op hetzelfde moment als een virtuele machine worden gemaakt met de opdracht [az vm create](/cli/azure/vm). Wanneer u dit doet, wordt de netwerkbeveiligingsgroep gekoppeld aan de netwerkinterface van de virtuele machine en wordt automatisch een regel voor de netwerkbeveiligingsgroep gemaakt waarmee verkeer op poort *22* uit elke bron wordt toegestaan. Eerder in deze zelfstudie is de front-endnetwerkbeveiligingsgroep automatisch gemaakt met de front-end VM. Er is ook automatisch een netwerkbeveiligingsgroepregel gemaakt voor poort 22. 

In sommige gevallen kan het handig zijn vooraf een netwerkbeveiligingsgroep te maken, bijvoorbeeld wanneer er geen SSH-standaardregels kunnen worden gemaakt of wanneer de netwerkbeveiligingsgroep moet worden gekoppeld aan een subnet. 

Gebruik de opdracht [az network nsg create](/cli/azure/network/nsg) om een netwerkbeveiligingsgroep te maken.

```azurecli-interactive 
az network nsg create --resource-group myRGNetwork --name myBackendNSG
```

In plaats van de netwerkbeveiligingsgroep aan een netwerkinterface koppelen, wordt deze aan een subnet gekoppeld. In deze configuratie neemt elke virtuele machine die is gekoppeld aan het subnet de netwerkbeveiligingsgroepregels over.

Werk het bestaande subnet met de naam *myBackendSubnet* bij met de nieuwe netwerkbeveiligingsgroep.

```azurecli-interactive 
az network vnet subnet update \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --network-security-group myBackendNSG
```

### <a name="secure-incoming-traffic"></a>Binnenkomend verkeer beveiligen

Wanneer de front-end VM is gemaakt, is een regel voor de netwerkbeveiligingsgroep gemaakt voor het toestaan van binnenkomend verkeer op poort 22. Met deze regel worden SSH-verbindingen met de virtuele machine toegestaan. Voor dit voorbeeld moet ook verkeer op poort *80* worden toegestaan. Met deze configuratie kan een webtoepassing worden geopend op de virtuele machine.

Gebruik de opdracht [az network nsg rule create](/cli/azure/network/nsg/rule) om een regel te maken voor poort *80*.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myFrontendNSG \
  --name http \
  --access allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 80
```

De front-end VM is alleen toegankelijk op poort *22* en poort *80*. Al het andere binnenkomende verkeer wordt geblokkeerd bij de netwerkbeveiligingsgroep. Het kan handig zijn de configuratie van de netwerkbeveiligingsgroepregels te visualiseren. Haal de configuratie van de netwerkbeveiligingsgroepregels op met de opdracht [az network rule list](/cli/azure/network/nsg/rule). 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myFrontendNSG --output table
```

### <a name="secure-vm-to-vm-traffic"></a>Verkeer tussen virtuele machines beveiligen

Netwerkbeveiligingsgroepsregels kunnen ook van toepassing zijn tussen virtuele machines. In dit voorbeeld moet de front-end VM communiceren met de back-end-VM op poort *22* en *3306*. Deze configuratie staat SSH-verbindingen van de front-end virtuele machine toe, en staat ook toe dat een toepassing op de front-end VM communiceert met een MySQL-back-enddatabase. Al het andere verkeer moet worden geblokkeerd tussen de front-end en back-end virtuele machines.

Gebruik de opdracht [az network nsg rule create](/cli/azure/network/nsg/rule) om een regel te maken voor poort 22. U ziet dat het argument `--source-address-prefix` een waarde specificeert van *10.0.1.0/24*. Deze configuratie zorgt ervoor dat alleen verkeer vanuit het front-endsubnet wordt toegestaan door de netwerkbeveiligingsgroep.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name SSH \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "22"
```

Voeg nu een regel toe voor MySQL-verkeer op poort 3306.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name MySQL \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "3306"
```

Ten slotte, omdat netwerkbeveiligingsgroepen al het verkeer toestaan tussen virtuele machines in hetzelfde VNet, kan een regel worden gemaakt voor de back-end netwerkbeveiligingsgroepen om alle verkeer te blokkeren. Zoals u ziet, krijgt de `--priority` hier een waarde van *300*, die lager is dan zowel de netwerkbeveiligingsgroep- als de MySQL-regels. Met deze configuratie wordt SSH- en MySQL-verkeer nog steeds door de netwerkbeveiligingsgroep toegestaan.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name denyAll \
  --access Deny \
  --protocol Tcp \
  --direction Inbound \
  --priority 300 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "*"
```

## <a name="create-back-end-vm"></a>Een back-end virtuele machine maken

Maak nu een virtuele machine, die is gekoppeld aan de *myBackendSubnet*. U ziet dat het argument `--nsg` een waarde heeft van lege dubbele aanhalingstekens. Er hoeft geen netwerkbeveiligingsgroep te worden gemaakt voor de virtuele machine. De virtuele machine is gekoppeld aan het back-endsubnet, dat is beveiligd met de vooraf gemaakte back-endnetwerkbeveiligingsgroep. Deze netwerkbeveiligingsgroep is van toepassing op de virtuele machine. U ziet hier ook dat het argument `--public-ip-address` een waarde van lege dubbele aanhalingstekens heeft. Met deze configuratie wordt een virtuele machine zonder een openbaar IP-adres gemaakt. 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --public-ip-address "" \
  --nsg "" \
  --image UbuntuLTS \
  --generate-ssh-keys
```

De back-end VM is alleen toegankelijk op poort *22* en poort *3306* via het front-endsubnet. Al het andere binnenkomende verkeer wordt geblokkeerd bij de netwerkbeveiligingsgroep. Het kan handig zijn de configuratie van de netwerkbeveiligingsgroepregels te visualiseren. Haal de configuratie van de netwerkbeveiligingsgroepregels op met de opdracht [az network rule list](/cli/azure/network/nsg/rule). 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myBackendNSG --output table
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Azure-netwerken met betrekking tot virtuele machines gemaakt en beveiligd. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een virtueel netwerk en een subnet maken
> * Een openbaar IP-adres maken
> * Een front-end virtuele machine maken
> * Netwerkverkeer beveiligen
> * Een back-end virtuele machine maken

Zie Back-up en herstel na noodherstel voor schijven voor meer informatie over het beveiligen [van uw VM-schijven.](../backup-and-disaster-recovery-for-azure-iaas-disks.md)