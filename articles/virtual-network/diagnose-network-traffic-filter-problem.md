---
title: Een probleem met het netwerk verkeer van een virtuele machine diagnosticeren | Microsoft Docs
description: Meer informatie over het vaststellen van een probleem met het netwerk verkeer van een virtuele machine door de juiste beveiligings regels voor een virtuele machine weer te geven.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: kumud
ms.openlocfilehash: 73562d8d32f265fa43ca80d2f8d4f84b1b631ec6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98223666"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Een probleem met het netwerk verkeer van een virtuele machine vaststellen

In dit artikel leert u hoe u een probleem met een netwerk verkeer filter kunt vaststellen door de beveiligings regels voor de netwerk beveiligings groep (NSG) weer te geven die van kracht zijn voor een virtuele machine (VM).

Met Nsg's kunt u de typen verkeer bepalen die in en uit een virtuele machine stromen. U kunt een NSG koppelen aan een subnet in een virtueel Azure-netwerk, een netwerk interface die is gekoppeld aan een virtuele machine of beide. De geldende beveiligings regels die worden toegepast op een netwerk interface zijn een aggregatie van de regels die bestaan in de NSG die zijn gekoppeld aan een netwerk interface en het subnet waarin de netwerk interface zich bevindt. Regels in verschillende Nsg's kunnen soms conflicteren met elkaar en invloed hebben op de netwerk connectiviteit van een virtuele machine. U kunt alle effectief beveiligings regels van Nsg's weer geven die worden toegepast op de netwerk interfaces van uw VM. Zie overzicht van [virtueel netwerk](virtual-networks-overview.md), [netwerk interface](virtual-network-network-interface.md)en [netwerk beveiligings groepen](./network-security-groups-overview.md)voor meer informatie over het gebruik van virtuele netwerken, netwerk interfaces en NSG-concepten.

## <a name="scenario"></a>Scenario

U probeert verbinding te maken met een virtuele machine via poort 80 van Internet, maar de verbinding is mislukt. Om te bepalen waarom u geen toegang hebt tot poort 80 vanaf internet, kunt u de effectief beveiligings regels voor een netwerk interface weer geven met behulp van de Azure- [Portal](#diagnose-using-azure-portal), [Power shell](#diagnose-using-powershell)of de [Azure cli](#diagnose-using-azure-cli).

Bij de volgende stappen wordt ervan uitgegaan dat u een bestaande virtuele machine hebt om de juiste beveiligings regels voor te bekijken. Als u geen bestaande VM hebt, implementeert u eerst een [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -of [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -VM om de taken in dit artikel uit te voeren met. De voor beelden in dit artikel zijn voor een virtuele machine met de naam *myVM* met een netwerk interface met de naam *myVMVMNic*. De virtuele machine en netwerk interface bevinden zich in een resource groep met de naam *myResourceGroup* en bevinden zich in de regio *VS-Oost* . Wijzig de waarden in de stappen, indien van toepassing, voor de virtuele machine waarvoor u het probleem wilt vaststellen.

## <a name="diagnose-using-azure-portal"></a>Problemen vaststellen met behulp van Azure Portal

1. Meld u aan bij Azure [Portal](https://portal.azure.com) met een Azure-account dat over de [benodigde machtigingen](virtual-network-network-interface.md#permissions)beschikt.
2. Voer boven aan de Azure Portal de naam van de virtuele machine in het zoekvak in. Wanneer de naam van de virtuele machine wordt weer gegeven in de zoek resultaten, selecteert u deze.
3. Onder **instellingen** selecteert u **netwerken**, zoals wordt weer gegeven in de volgende afbeelding:

   ![Scherm afbeelding toont de Azure Portal met netwerk instellingen voor mijn V M V M-NIC.](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   De regels die in de vorige afbeelding worden weer gegeven, zijn voor een netwerk interface met de naam **myVMVMNic**. U ziet dat er **Binnenkomende poort regels** zijn voor de netwerk interface van twee verschillende netwerk beveiligings groepen:
   
   - **mySubnetNSG**: gekoppeld aan het subnet waarin de netwerk interface zich bevindt.
   - **myVMNSG**: gekoppeld aan de netwerk interface in de virtuele machine met de naam **myVMVMNic**.

   De regel met de naam **DenyAllInBound** is wat voor komt dat binnenkomende communicatie met de virtuele machine via poort 80, via internet, wordt voor komen, zoals beschreven in het [scenario](#scenario). De regel bevat *0.0.0.0/0* voor de **bron**, inclusief Internet. Voor geen enkele andere regel met een hogere prioriteit (lager nummer) is poort 80 inkomend toegestaan. Zie [een probleem oplossen](#resolve-a-problem)om poort 80 van inkomend verkeer naar de virtuele machine via Internet toe te staan. Zie [netwerk beveiligings groepen](./network-security-groups-overview.md)voor meer informatie over beveiligings regels en hoe Azure deze toepast.

   Onder aan de afbeelding ziet u ook **uitgaande poort regels**. Onder dit zijn de regels voor uitgaande poorten voor de netwerk interface. Hoewel in de afbeelding slechts vier regels voor binnenkomende verbindingen voor elk NSG worden weer gegeven, heeft uw Nsg's mogelijk veel meer dan vier regels. In de afbeelding ziet u **VirtualNetwork** onder **bron** en **doel** en **AzureLoadBalancer** onder **bron**. **VirtualNetwork** en **AzureLoadBalancer** zijn [service Tags](./network-security-groups-overview.md#service-tags). Service Tags vertegenwoordigen een groep IP-adres voorvoegsels om de complexiteit voor het maken van beveiligings regels te minimaliseren.

4. Zorg ervoor dat de virtuele machine wordt uitgevoerd en selecteer vervolgens de **juiste beveiligings regels**, zoals wordt weer gegeven in de vorige afbeelding, om de juiste beveiligings regels te zien, zoals wordt weer gegeven in de volgende afbeelding:

   ![Scherm afbeelding toont het deel venster effectief beveiligings regels waarvoor down load geselecteerd en binnenkomende regel AllowAzureLoadBalancerInbound geselecteerd.](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   De regels die worden weer gegeven, zijn hetzelfde als u in stap 3 hebt gezien, maar er zijn verschillende tabbladen voor de NSG die zijn gekoppeld aan de netwerk interface en het subnet. Zoals u in de afbeelding kunt zien, worden alleen de eerste 50 regels weer gegeven. Als u een CSV-bestand wilt downloaden dat alle regels bevat, selecteert u **downloaden**.

   Als u wilt zien welke voor voegsels elke servicetag vertegenwoordigt, selecteert u een regel, bijvoorbeeld de regel met de naam **AllowAzureLoadBalancerInbound**. In de volgende afbeelding ziet u de voor voegsels voor de **AzureLoadBalancer** -servicetag:

   ![Scherm opname toont adres voorvoegsels voor AllowAzureLoadBalancerInbound.](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   Hoewel de servicetag **AzureLoadBalancer** alleen één voor voegsel vertegenwoordigt, vertegenwoordigen andere service Tags verschillende voor voegsels.

5. In de vorige stappen zijn de beveiligings regels voor een netwerk interface met de naam **myVMVMNic** weer gegeven, maar u hebt ook een netwerk interface met de naam **myVMVMNic2** in enkele van de vorige afbeeldingen gezien. Er zijn twee netwerk interfaces gekoppeld aan de virtuele machine in dit voor beeld. De juiste beveiligings regels kunnen verschillen voor elke netwerk interface.

   Als u de regels voor de **myVMVMNic2** -netwerk interface wilt weer geven, selecteert u deze. Zoals in de volgende afbeelding wordt weer gegeven, heeft de netwerk interface dezelfde regels als de netwerk interface van het **myVMVMNic** , omdat beide netwerk interfaces zich in hetzelfde subnet bevinden. Wanneer u een NSG aan een subnet koppelt, worden de bijbehorende regels toegepast op alle netwerk interfaces in het subnet.

   ![Scherm afbeelding toont de Azure Portal met netwerk instellingen voor mijn V M V M NIC 2.](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   In tegens telling tot de **myVMVMNic** -netwerk interface is er geen netwerk beveiligings groep gekoppeld aan de **myVMVMNic2** -netwerk interface. Elke netwerk interface en elk subnet kunnen een of meer NSG hebben. De NSG die is gekoppeld aan elke netwerk interface of elk subnet kan hetzelfde zijn of een andere. U kunt dezelfde netwerk beveiligings groep koppelen aan net zoveel netwerk interfaces en subnetten als u kiest.

Hoewel effectief beveiligings regels zijn bekeken via de virtuele machine, kunt u ook de meest efficiënte beveiligings regels bekijken via een individu:
- **Netwerk interface**: meer informatie over het [weer geven van een netwerk interface](virtual-network-network-interface.md#view-network-interface-settings).
- **NSG**: meer informatie over het [weer geven van een NSG](manage-network-security-group.md#view-details-of-a-network-security-group).

## <a name="diagnose-using-powershell"></a>Problemen vaststellen met Power shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt de opdrachten uitvoeren die volgen in de [Azure Cloud shell](https://shell.azure.com/powershell), of door Power shell uit te voeren vanaf uw computer. De Azure Cloud Shell is een gratis interactieve shell. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Als u Power shell vanaf uw computer uitvoert, hebt u de Azure PowerShell module versie 1.0.0 of hoger nodig. Voer uit `Get-Module -ListAvailable Az` op uw computer om de geïnstalleerde versie te vinden. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u Power shell lokaal uitvoert, moet u ook uitvoeren `Connect-AzAccount` om u aan te melden bij Azure met een account met de [vereiste machtigingen](virtual-network-network-interface.md#permissions)].

Haal de juiste beveiligings regels op voor een netwerk interface met [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup). In het volgende voor beeld worden de effectief beveiligings regels opgehaald voor een netwerk interface met de naam *myVMVMNic*, die zich in een resource groep met de naam *myResourceGroup* bevindt:

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup
```

Uitvoer wordt geretourneerd in JSON-indeling. Zie [interpretatie van opdracht uitvoer](#interpret-command-output)voor meer informatie over de uitvoer.
De uitvoer wordt alleen geretourneerd als een NSG is gekoppeld aan de netwerk interface, het subnet waarin de netwerk interface zich bevindt of beide. De virtuele machine moet de status actief hebben. Een virtuele machine kan meerdere netwerk interfaces hebben waarop verschillende Nsg's zijn toegepast. Bij het oplossen van problemen voert u de opdracht uit voor elke netwerk interface.

Als u nog steeds een probleem met de verbinding hebt, raadpleegt u [aanvullende diagnose](#additional-diagnosis) en [overwegingen](#considerations).

Als u de naam van een netwerk interface niet kent, maar wel de naam weet van de VM waaraan de netwerk interface is gekoppeld, retour neren de volgende opdrachten de Id's van alle netwerk interfaces die zijn gekoppeld aan een virtuele machine:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

U ontvangt uitvoer die er ongeveer zo uitziet als in het volgende voor beeld:

```output
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

In de vorige uitvoer is de naam van de netwerk interface *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnoses uitvoeren met Azure CLI

Als u Azure-opdracht regel interface opdrachten gebruikt om taken in dit artikel te volt ooien, moet u de opdrachten uitvoeren in de [Azure Cloud shell](https://shell.azure.com/bash)of door de CLI vanaf uw computer uit te voeren. Voor dit artikel is de Azure CLI-versie 2.0.32 of hoger vereist. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Als u de Azure CLI lokaal uitvoert, moet u ook uitvoeren en u `az login` Aanmelden bij Azure met een account dat over de [benodigde machtigingen](virtual-network-network-interface.md#permissions)beschikt.

Haal de juiste beveiligings regels op voor een netwerk interface met [AZ Network NIC List-effectief-NSG](/cli/azure/network/nic#az-network-nic-list-effective-nsg). In het volgende voor beeld worden de effectief beveiligings regels opgehaald voor een netwerk interface met de naam *myVMVMNic* die zich in een resource groep met de naam *myResourceGroup* bevindt:

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Uitvoer wordt geretourneerd in JSON-indeling. Zie [interpretatie van opdracht uitvoer](#interpret-command-output)voor meer informatie over de uitvoer.
De uitvoer wordt alleen geretourneerd als een NSG is gekoppeld aan de netwerk interface, het subnet waarin de netwerk interface zich bevindt of beide. De virtuele machine moet de status actief hebben. Een virtuele machine kan meerdere netwerk interfaces hebben waarop verschillende Nsg's zijn toegepast. Bij het oplossen van problemen voert u de opdracht uit voor elke netwerk interface.

Als u nog steeds een probleem met de verbinding hebt, raadpleegt u [aanvullende diagnose](#additional-diagnosis) en [overwegingen](#considerations).

Als u de naam van een netwerk interface niet kent, maar wel de naam weet van de VM waaraan de netwerk interface is gekoppeld, retour neren de volgende opdrachten de Id's van alle netwerk interfaces die zijn gekoppeld aan een virtuele machine:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

Binnen de geretourneerde uitvoer ziet u informatie die lijkt op het volgende voor beeld:

```output
"networkProfile": {
    "additionalProperties": {},
    "networkInterfaces": [
      {
        "additionalProperties": {},
        "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
        "primary": true,
        "resourceGroup": "myResourceGroup"
      },
```

In de vorige uitvoer is de naam van de netwerk interface *myVMVMNic-interface*.

## <a name="interpret-command-output"></a>Opdracht uitvoer interpreteren

Ongeacht of u de [Power shell](#diagnose-using-powershell)of de [Azure cli](#diagnose-using-azure-cli) hebt gebruikt om de oorzaak van het probleem vast te stellen, ontvangt u uitvoer die de volgende gegevens bevat:

- **NetworkSecurityGroup**: de id van de netwerk beveiligings groep.
- **Koppeling**: of de netwerk beveiligings groep is gekoppeld aan een *Network Interface* of *subnet*. Als een NSG is gekoppeld aan beide, wordt er voor elke NSG een uitvoer geretourneerd met **NetworkSecurityGroup**, **Association** en **EffectiveSecurityRules**. Als de NSG direct is gekoppeld of ontstaat voordat de opdracht wordt uitgevoerd om de juiste beveiligings regels weer te geven, moet u mogelijk enkele seconden wachten voordat de wijziging in de uitvoer van de opdracht voor komt.
- **EffectiveSecurityRules**: een uitleg van elke eigenschap wordt beschreven in [een beveiligings regel maken](manage-network-security-group.md#create-a-security-rule). Regel namen die zijn voorafgegaan door *defaultSecurityRules/* zijn standaard beveiligings regels die aanwezig zijn in elke NSG. Regel namen die zijn voorafgegaan door *securityRules/* zijn regels die u hebt gemaakt. Regels [die een servicetag](./network-security-groups-overview.md#service-tags)opgeven, zoals **Internet**, **VirtualNetwork** en **AzureLoadBalancer** voor de eigenschappen **destinationAddressPrefix** of **sourceAddressPrefix** , hebben ook waarden voor de eigenschap **expandedDestinationAddressPrefix** . De eigenschap **expandedDestinationAddressPrefix** geeft een lijst van alle adres voorvoegsels die door de servicetag worden vertegenwoordigd.

Als er dubbele regels worden weer gegeven in de uitvoer, komt dit doordat er een NSG is gekoppeld aan de netwerk interface en het subnet. Beide Nsg's hebben dezelfde standaard regels en kunnen extra dubbele regels hebben als u uw eigen regels hebt gemaakt die in beide Nsg's hetzelfde zijn.

De regel met de naam **defaultSecurityRules/DenyAllInBound** is wat het voor komen van binnenkomende communicatie met de virtuele machine via poort 80, via internet, zoals beschreven in het [scenario](#scenario). Geen andere regel met een hogere prioriteit (lager nummer) staat poort 80 inkomende van Internet toe.

## <a name="resolve-a-problem"></a>Een probleem oplossen

Of u de Azure- [Portal](#diagnose-using-azure-portal), [Power shell](#diagnose-using-powershell)of de [Azure cli](#diagnose-using-azure-cli) gebruikt om vast te stellen welk probleem in het [scenario](#scenario) in dit artikel wordt weer gegeven, is de oplossing om een netwerk beveiligings regel te maken met de volgende eigenschappen:

| Eigenschap                | Waarde                                                                              |
|---------                |---------                                                                           |
| Bron                  | Elk                                                                                |
| Poortbereiken van bron      | Alle                                                                                |
| Doel             | Het IP-adres van de virtuele machine, een bereik van IP-adressen of alle adressen in het subnet. |
| Poortbereiken van doel | 80                                                                                 |
| Protocol                | TCP                                                                                |
| Bewerking                  | Toestaan                                                                              |
| Prioriteit                | 100                                                                                |
| Naam                    | Allow-HTTP-all                                                                     |

Nadat u de regel hebt gemaakt, wordt poort 80 inkomende van Internet toegestaan, omdat de prioriteit van de regel hoger is dan de standaard beveiligings regel met de naam *DenyAllInBound*, die het verkeer weigert. Meer informatie over het [maken van een beveiligings regel](manage-network-security-group.md#create-a-security-rule). Als verschillende Nsg's zijn gekoppeld aan de netwerk interface en het subnet, moet u dezelfde regel maken in beide Nsg's.

Wanneer het inkomende verkeer door Azure wordt verwerkt, worden de regels in de NSG die zijn gekoppeld aan het subnet (als er een gekoppelde NSG is) verwerkt. vervolgens worden de regels verwerkt in de NSG die aan de netwerk interface is gekoppeld. Als er een NSG is gekoppeld aan de netwerk interface en het subnet, moet de poort zijn geopend in beide Nsg's, zodat het verkeer de virtuele machine kan bereiken. Voor het vereenvoudigen van beheer-en communicatie problemen raden wij aan dat u een NSG aan een subnet koppelt in plaats van afzonderlijke netwerk interfaces. Als Vm's binnen een subnet verschillende beveiligings regels nodig hebben, kunt u de netwerk interfaces lid maken van een toepassings beveiligings groep (ASG) en een ASG opgeven als de bron en het doel van een beveiligings regel. Meer informatie over [toepassings beveiligings groepen](./network-security-groups-overview.md#application-security-groups).

Als u nog steeds communicatie problemen ondervindt, raadpleegt u [overwegingen](#considerations) en aanvullende diagnose.

## <a name="considerations"></a>Overwegingen

Houd rekening met de volgende punten bij het oplossen van verbindings problemen:

* Standaard beveiligings regels blok keren binnenkomende toegang vanaf het internet en geven alleen binnenkomend verkeer van het virtuele netwerk toe. Als u inkomend verkeer van het Internet wilt toestaan, voegt u beveiligings regels toe met een hogere prioriteit dan standaard regels. Meer informatie over [standaard beveiligings regels](./network-security-groups-overview.md#default-security-rules)of over het [toevoegen van een beveiligings regel](manage-network-security-group.md#create-a-security-rule).
* Als u gepeerde virtuele netwerken hebt, wordt de **VIRTUAL_NETWORK** service-tag standaard automatisch uitgebreid met voor voegsels voor gekoppelde virtuele netwerken. U kunt de voor voegsels in de lijst **ExpandedAddressPrefix** bekijken om problemen met de peering van het virtuele netwerk op te lossen. Meer informatie over [peering](virtual-network-peering-overview.md) en [service Tags](./network-security-groups-overview.md#service-tags)voor virtuele netwerken.
* Effectief beveiligings regels worden alleen weer gegeven voor een netwerk interface als er een NSG is gekoppeld aan de netwerk interface van de VM en, of subnet, en als de virtuele machine de status actief heeft.
* Als er geen Nsg's is gekoppeld aan de netwerk interface of het subnet en u een [openbaar IP-adres](virtual-network-public-ip-address.md) hebt toegewezen aan een virtuele machine, zijn alle poorten geopend voor inkomende toegang vanaf en uitgaande toegang tot elke locatie. Als de virtuele machine een openbaar IP-adres heeft, kunt u het beste een NSG Toep assen op het subnet van de netwerk interface.

## <a name="additional-diagnosis"></a>Aanvullende diagnose

* Als u een snelle test wilt uitvoeren om te bepalen of verkeer is toegestaan van of naar een virtuele machine, gebruikt u de [IP-stroom controle](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) van Azure Network Watcher. Met IP-stroom controle wordt aangegeven of verkeer wordt toegestaan of geweigerd. Als u deze weigert, geeft u aan welke beveiligings regel het verkeer weigert.
* Als er geen beveiligings regels zijn die de netwerk connectiviteit van een virtuele machine laten mislukken, kan dit probleem worden veroorzaakt door:
  * Firewall software die wordt uitgevoerd binnen het besturings systeem van de virtuele machine
  * Routes die zijn geconfigureerd voor virtuele apparaten of on-premises verkeer. Internet verkeer kan worden omgeleid naar uw on-premises netwerk via [geforceerde tunneling](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Als u het Internet verkeer van de tunnel naar een virtueel apparaat of on-premises afdwingt, kunt u mogelijk geen verbinding maken met de virtuele machine via internet. Zie [een probleem met een netwerk verkeer van een virtuele machine diagnosticeren](diagnose-network-routing-problem.md)voor meer informatie over het vaststellen van route problemen die de stroom van verkeer van de VM kunnen belemmeren.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over alle taken, eigenschappen en instellingen voor een [netwerk beveiligings groep](manage-network-security-group.md#work-with-network-security-groups) en [beveiligings regels](manage-network-security-group.md#work-with-security-rules).
- Meer informatie over [standaard beveiligings regels](./network-security-groups-overview.md#default-security-rules), [service Tags](./network-security-groups-overview.md#service-tags)en [hoe Azure beveiligings regels voor binnenkomend en uitgaand verkeer](./network-security-groups-overview.md#network-security-groups) voor een virtuele machine verwerkt.