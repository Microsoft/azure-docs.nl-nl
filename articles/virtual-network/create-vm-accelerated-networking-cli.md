---
title: Een Azure-VM maken met versneld netwerken met behulp van Azure CLI
description: Meer informatie over het maken van een virtuele Linux-machine met versneld netwerken ingeschakeld.
services: virtual-network
documentationcenter: na
author: gsilva5
manager: gedegrac
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: 5b91d6e58f4ae93bbf020f202991f878e7773114
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222952"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking-using-azure-cli"></a>Een virtuele machine met versneld netwerken maken

In deze zelf studie leert u hoe u een virtuele Linux-machine (VM) kunt maken met versneld netwerken. Zie [een Windows-VM maken met versneld](create-vm-accelerated-networking-powershell.md)netwerken voor het maken van een virtuele Windows-machine met versneld netwerken. Versneld netwerken maken gebruik van I/O-virtualisatie met één hoofdmap (SR-IOV) naar een virtuele machine, waardoor de netwerk prestaties aanzienlijk worden verbeterd. Dit pad met hoge prestaties omzeilt de host van de DataPath, vermindert latentie, jitter en CPU-gebruik, voor gebruik met de meest veeleisende netwerk workloads op ondersteunde VM-typen. In de volgende afbeelding ziet u communicatie tussen twee Vm's met en zonder versneld netwerken:

![Vergelijking](./media/create-vm-accelerated-networking/accelerated-networking.png)

Zonder versneld netwerk moeten al het netwerk verkeer van en naar de virtuele machine de host en de Virtual Switch door lopen. De virtuele switch biedt alle beleids afdwinging, zoals netwerk beveiligings groepen, Toegangs beheer lijsten, isolatie en andere gevirtualiseerde netwerk services voor netwerk verkeer. Lees het artikel [Hyper-V-Netwerkvirtualisatie en virtuele-switch](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134230(v=ws.11)) voor meer informatie over virtuele switches.

Met versneld netwerken arriveert het netwerk verkeer op de netwerk interface van de virtuele machine (NIC) en wordt vervolgens doorgestuurd naar de VM. Alle netwerk beleidsregels waarmee de virtuele switch wordt toegepast, worden nu geoffload en toegepast in hardware. Door beleid toe te passen op hardware kan de NIC netwerk verkeer rechtstreeks naar de virtuele machine door sturen, waardoor de host en de virtuele switch worden omzeild, terwijl alle beleids regels die op de host worden toegepast, behouden blijven.

De voor delen van versneld netwerken zijn alleen van toepassing op de virtuele machine waarop deze is ingeschakeld. Voor de beste resultaten is het ideaal om deze functie in te scha kelen op ten minste twee virtuele machines die zijn verbonden met hetzelfde virtuele Azure-netwerk (VNet). Bij de communicatie tussen VNets of on-premises, heeft deze functie een minimale invloed op de algehele latentie.

## <a name="benefits"></a>Voordelen
* **Lagere latentie/hogere pakketten per seconde (PPS):** Als u de virtuele switch uit de DataPath verwijdert, worden de tijd pakketten op de host verwijderd voor beleids verwerking en wordt het aantal pakketten dat kan worden verwerkt in de virtuele machine verhoogd.
* **Gereduceerde jitter:** De verwerking van virtuele switches is afhankelijk van de hoeveelheid beleid die moet worden toegepast en de werk belasting van de CPU die de verwerking uitvoert. Door het beleid afdwingen naar de hardware te verwijderen, verwijdert u die variabiliteit door pakketten rechtstreeks aan de virtuele machine te leveren, waardoor de host wordt verwijderd naar de VM-communicatie en alle software-interrupts en-context switches.
* **Verminderd CPU-gebruik:** Het overs laan van de virtuele switch in de host leidt tot minder CPU-gebruik voor het verwerken van netwerk verkeer.

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen
De volgende distributies worden ondersteund uit het vak van de Azure-galerie: 
* **Ubuntu 14,04 met de Linux-Azure-kernel**
* **Ubuntu 16,04 of hoger** 
* **SLES12 SP3 of hoger** 
* **RHEL 7,4 of hoger**
* **CentOS 7,4 of hoger**
* **CoreOS Linux**
* **Debian "stretch" met backports kernel, Debian "Buster" of hoger**
* **Oracle Linux 7,4 en hoger met Red Hat compatible kernel (RHCK)**
* **Oracle Linux 7,5 en hoger met UEK versie 5**
* **FreeBSD 10,4, 11,1 & 12,0 of hoger**

## <a name="limitations-and-constraints"></a>Beperkingen en beperkingen

### <a name="supported-vm-instances"></a>Ondersteunde VM-exemplaren
Versnelde netwerken worden ondersteund in de meeste algemene doel stellingen en met Compute geoptimaliseerde exemplaar grootten met twee of meer Vcpu's.  Deze ondersteunde reeksen zijn: D/DSv2 en F/FS

Op instanties die HyperThreading ondersteunen, wordt versneld netwerken ondersteund op VM-exemplaren met vier of meer Vcpu's. Ondersteunde reeksen zijn: D/Dsv3, D/Dsv4, DD/Ddv4, da/Dasv4, E/Esv3, E/Esv4, Ed/Edsv4, EA/Easv4, Fsv2, Lsv2, MS/MMS en MS/Mmsv2.

Zie [Linux VM-grootten](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over VM-exemplaren.

### <a name="custom-images"></a>Aangepaste installatiekopieën
Als u een aangepaste installatie kopie gebruikt en uw installatie kopie versneld netwerken ondersteunt, moet u ervoor zorgen dat de vereiste Stuur Programma's worden gebruikt om te werken met de Mellanox Connectx-3-en Connectx-4 LX-Nic's in Azure.

### <a name="regions"></a>Regio's
Beschikbaar in alle open bare Azure-regio's en Azure Government Clouds.

<!-- ### Network interface creation 
Accelerated networking can only be enabled for a new NIC. It cannot be enabled for an existing NIC.
removed per issue https://github.com/MicrosoftDocs/azure-docs/issues/9772 -->
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Versnelde netwerken inschakelen op een actieve virtuele machine
Voor een ondersteunde VM-grootte zonder versneld netwerken is ingeschakeld, kan de functie alleen worden ingeschakeld wanneer deze wordt gestopt en de toewijzing ongedaan wordt gemaakt.  
### <a name="deployment-through-azure-resource-manager"></a>Implementatie via Azure Resource Manager
Virtuele machines (klassiek) kunnen niet worden geïmplementeerd met versneld netwerken.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Een virtuele Linux-machine maken met een versneld Azure-netwerk
## <a name="portal-creation"></a>Portal maken
Hoewel dit artikel stappen bevat voor het maken van een virtuele machine met versneld netwerken met behulp van de Azure CLI, kunt u ook [een virtuele machine maken met versneld netwerken met behulp van de Azure Portal](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Wanneer u een virtuele machine in de portal maakt, kiest u op de Blade **een virtuele machine maken** het tabblad **netwerk** .  Op dit tabblad is er een optie voor **versneld netwerken**.  Als u een [ondersteund besturings systeem](#supported-operating-systems) en een [VM-grootte](#supported-vm-instances)hebt gekozen, wordt deze optie automatisch ingevuld op ' aan '.  Als dat niet het geval is, wordt de optie ' uit ' gevuld voor versneld netwerken en krijgt de gebruiker een reden waarom deze niet is ingeschakeld.   

* *Opmerking:* Alleen ondersteunde besturings systemen kunnen worden ingeschakeld via de portal.  Als u een aangepaste installatie kopie gebruikt en uw installatie kopie versneld netwerken ondersteunt, maakt u uw virtuele machine met CLI of Power shell. 

Nadat de virtuele machine is gemaakt, kunt u de versnelde netwerken bevestigen door de instructies te volgen in de [bevestigen dat versneld netwerken zijn ingeschakeld](#confirm-that-accelerated-networking-is-enabled).

## <a name="cli-creation"></a>CLI maken
### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Installeer de nieuwste [Azure cli](/cli/azure/install-azure-cli) en meld u aan bij een Azure-account met de opdracht [AZ login](/cli/azure/reference-index). Vervang in de volgende voor beelden voorbeeld parameter namen door uw eigen waarden. Voor beelden van parameter namen zijn *myResourceGroup*, *myNic* en *myVm*.

Maak een resourcegroep maken met [az group create](/cli/azure/group). In het volgende voor beeld wordt een resource groep met de naam *myResourceGroup* gemaakt op de locatie *middenus* :

```azurecli
az group create --name myResourceGroup --location centralus
```

Selecteer een ondersteunde Linux-regio die wordt weer gegeven in [Linux-versneld netwerken](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Maak een virtueel netwerk met [az network vnet create](/cli/azure/network/vnet). In het volgende voor beeld wordt een virtueel netwerk met de naam *myVnet* gemaakt met één subnet:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken
Maak een netwerk beveiligings groep met [AZ Network NSG Create](/cli/azure/network/nsg). In het volgende voorbeeld wordt een netwerkbeveiligingsgroep met de naam *myNetworkSecurityGroup* gemaakt:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

De netwerk beveiligings groep bevat verschillende standaard regels, waarvan een van de alle binnenkomende toegang van Internet uitschakelt. Open een poort om SSH-toegang tot de virtuele machine toe te staan met [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule):

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Een netwerk interface met versneld netwerken maken

Maak een openbaar IP-adres met [az network public-ip create](/cli/azure/network/public-ip). Een openbaar IP-adres is niet vereist als u niet van plan bent om toegang te krijgen tot de virtuele machine via internet, maar dit is vereist om de stappen in dit artikel uit te voeren.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Maak een netwerk interface met [AZ Network NIC Create](/cli/azure/network/nic) with versneld netwerken ingeschakeld. In het volgende voor beeld wordt een netwerk interface met de naam *myNic* in het *mySubnet* -subnet van het virtuele netwerk *MyVnet* gemaakt en wordt de *myNetworkSecurityGroup* -netwerk beveiligings groep gekoppeld aan de netwerk interface:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

### <a name="create-a-vm-and-attach-the-nic"></a>Een virtuele machine maken en de NIC koppelen
Wanneer u de virtuele machine maakt, geeft u de NIC op die u hebt gemaakt met `--nics` . Selecteer een grootte en distributie die worden vermeld in [Linux versneld netwerken](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Maak een VM met [az vm create](/cli/azure/vm). In het volgende voor beeld wordt een VM gemaakt met de naam *myVM* met de UbuntuLTS-installatie kopie en een grootte die ondersteuning biedt voor versneld netwerken (*Standard_DS4_v2*):

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

Zie [Linux VM-grootten](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor een lijst met alle VM-grootten en-kenmerken.

Zodra de VM is gemaakt, wordt de uitvoer vergelijkbaar met de volgende voorbeeld uitvoer geretourneerd. Let op het **openbare IP-adres**. Dit adres wordt gebruikt voor toegang tot de virtuele machine in volgende stappen.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Controleren of versneld netwerken zijn ingeschakeld

Gebruik de volgende opdracht om voor de VM een SSH-sessie te maken. Vervang door `<your-public-ip-address>` het open bare IP-adres dat is toegewezen aan de virtuele machine die u hebt gemaakt en vervang *azureuser* als u een andere waarde hebt gebruikt voor `--admin-username` het maken van de VM.

```bash
ssh azureuser@<your-public-ip-address>
```

Voer vanuit de bash-shell in `uname -r` en bevestig dat de versie van de kernel een van de volgende versies is, of hoger:

* **Ubuntu 16,04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Bevestig dat het Mellanox VF-apparaat wordt blootgesteld aan de virtuele machine met de `lspci` opdracht. De geretourneerde uitvoer is vergelijkbaar met de volgende uitvoer:

```output
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Controleer op activiteit op de VF (virtuele functie) met de `ethtool -S eth0 | grep vf_` opdracht. Als er uitvoer wordt weer gegeven die vergelijkbaar is met de volgende voorbeeld uitvoer, wordt versneld netwerken ingeschakeld en werken.

```output
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Versneld netwerken zijn nu ingeschakeld voor uw VM.

## <a name="handle-dynamic-binding-and-revocation-of-virtual-function"></a>Dynamische binding en het intrekken van de virtuele functie verwerken 
Toepassingen moeten worden uitgevoerd via de synthetische NIC die wordt weer gegeven in de VM. Als de toepassing rechtstreeks wordt uitgevoerd via de VF-NIC, worden niet **alle** pakketten ontvangen die bestemd zijn voor de virtuele machine, omdat sommige pakketten via de synthetische interface worden weer gegeven.
Als u een toepassing uitvoert via de synthetische NIC, zorgt dit ervoor dat de toepassing **alle** pakketten ontvangt die ernaar zijn bestemd. Het zorgt er ook voor dat de toepassing blijft werken, zelfs als het VF wordt ingetrokken wanneer de host wordt verwerkt. Toepassingen die zijn gebonden aan de synthetische NIC, zijn **verplicht** voor alle toepassingen die gebruikmaken van **versneld netwerken**.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Versneld netwerken op bestaande Vm's inschakelen
Als u een VM hebt gemaakt zonder versneld netwerk, is het mogelijk om deze functie in te scha kelen op een bestaande virtuele machine.  De virtuele machine moet versneld netwerken ondersteunen door aan de volgende vereisten te voldoen die ook hierboven worden beschreven:

* De virtuele machine moet een ondersteunde grootte voor versneld netwerken zijn
* De virtuele machine moet een ondersteunde installatie kopie van Azure Gallery zijn (en de kernel-versie voor Linux)
* Alle virtuele machines in een beschikbaarheidsset of VMSS moeten worden gestopt/vrijgegeven voordat versnelde netwerken op een NIC worden ingeschakeld

### <a name="individual-vms--vms-in-an-availability-set"></a>Afzonderlijke Vm's & Vm's in een beschikbaarheidsset
Eerst stoppen/toewijzing van de virtuele machine ongedaan maken of, als u een Beschikbaarheidsset hebt, alle virtuele machines in de set:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Belang rijk: als uw virtuele machine afzonderlijk is gemaakt, zonder dat er een beschikbaarheidsset beschikbaar is, hoeft u de afzonderlijke virtuele machine alleen te stoppen/detoewijzen om versnelde netwerken mogelijk te maken.  Als uw virtuele machine is gemaakt met een beschikbaarheidsset, moeten alle virtuele machines in de beschikbaarheidsset worden gestopt/vrijgegeven voordat versneld netwerken op een van de Nic's worden ingeschakeld. 

Als u bent gestopt, schakelt u versneld netwerken in op de NIC van uw VM:

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Start de virtuele machine opnieuw op of, in een Beschikbaarheidsset, alle virtuele machines in de set en controleer of versneld netwerken zijn ingeschakeld: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
VMSS is iets anders, maar volgt dezelfde werk stroom.  Eerst stopt u de Vm's:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

Zodra de Vm's zijn gestopt, werkt u de eigenschap versneld netwerk bij onder de netwerk interface:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Houd er rekening mee dat een VMSS VM-upgrades heeft die updates Toep assen met behulp van drie verschillende instellingen, automatische, Rolling en hand matig.  In deze instructies wordt het beleid ingesteld op automatisch, zodat de VMSS de wijzigingen direct na het opnieuw opstarten ophaalt.  Stel deze in op automatisch, zodat de wijzigingen onmiddellijk worden opgehaald: 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

Ten slotte start u de VMSS opnieuw:

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

Nadat u de computer opnieuw hebt opgestart, wacht u totdat de upgrade is voltooid, wordt het VF weer gegeven in de virtuele machine.  (Zorg ervoor dat u een ondersteund besturings systeem en VM-grootte gebruikt.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Het formaat wijzigen van bestaande Vm's met versneld netwerken

Vm's waarvoor versneld netwerken zijn ingeschakeld, kunnen alleen worden gewijzigd in de grootte van virtuele machines die ondersteuning bieden voor versneld netwerken.  

Het formaat van een virtuele machine met versneld netwerken kan niet worden gewijzigd in een VM-exemplaar dat geen ondersteuning biedt voor versnelde netwerken met behulp van de bewerking voor het wijzigen van de grootte.  In plaats daarvan wijzigt u het formaat van een van deze Vm's: 

* Stop/Hef de toewijzing van de virtuele machine op of hef de toewijzing van de virtuele machines in de set-VMSS op en sluit deze toe.
* Versnelde netwerken moeten worden uitgeschakeld op de NIC van de virtuele machine of in een beschikbaarheidsset/VMSS alle virtuele machines in de set/VMSS.
* Zodra versneld netwerken zijn uitgeschakeld, kunnen de set/VMSS van de virtuele machine en de beschik baarheid worden verplaatst naar een nieuwe grootte die geen versneld netwerken ondersteunt en opnieuw wordt gestart.