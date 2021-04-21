---
title: Diagnose van een probleem met netwerkverkeersfilters op een virtuele machine | Microsoft Docs
description: Meer informatie over het diagnosticeren van een probleem met netwerkverkeersfilters op een virtuele machine door de effectieve beveiligingsregels voor een virtuele machine te bekijken.
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
ms.openlocfilehash: d6835d06015923a70301c95370c76efbd0c2163e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776731"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Diagnose van een probleem met netwerkverkeersfilters op een virtuele machine

In dit artikel leert u hoe u een probleem met netwerkverkeersfilters kunt vaststellen door de beveiligingsregels van de netwerkbeveiligingsgroep (NSG) te bekijken die van kracht zijn voor een virtuele machine (VM).

Met NSG's kunt u de typen verkeer bepalen die in en uit een VM stromen. U kunt een NSG koppelen aan een subnet in een virtueel Azure-netwerk, een netwerkinterface die is gekoppeld aan een virtuele machine of beide. De effectieve beveiligingsregels die worden toegepast op een netwerkinterface zijn een aggregatie van de regels die aanwezig zijn in de NSG die is gekoppeld aan een netwerkinterface en het subnet waarin de netwerkinterface zich in zich heeft. Regels in verschillende NSG's kunnen soms met elkaar conflicteren en van invloed zijn op de netwerkverbinding van een VM. U kunt alle effectieve beveiligingsregels weergeven van NSG's die worden toegepast op de netwerkinterfaces van uw VM. Zie Overzicht van virtuele netwerken, Netwerkinterface en Overzicht van [](virtual-networks-overview.md)netwerkbeveiligingsgroepen als u niet bekend bent met de concepten van virtuele netwerken, [](virtual-network-network-interface.md) [netwerkinterfaces of NSG's.](./network-security-groups-overview.md)

## <a name="scenario"></a>Scenario

U probeert via internet verbinding te maken met een VM via poort 80, maar de verbinding mislukt. Als u wilt bepalen waarom u geen toegang hebt tot poort 80 via internet, kunt u de effectieve beveiligingsregels voor een netwerkinterface bekijken met behulp van Azure [Portal,](#diagnose-using-azure-portal) [PowerShell](#diagnose-using-powershell)of [de Azure CLI.](#diagnose-using-azure-cli)

Bij de volgende stappen wordt ervan uitgenomen dat u een bestaande VM hebt om de effectieve beveiligingsregels voor te bekijken. Als u geen bestaande VM hebt, implementeert u eerst een [Linux-](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [Windows-VM](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) om de taken in dit artikel mee uit te voeren. De voorbeelden in dit artikel zijn voor een VM met de naam *myVM met* een netwerkinterface met de *naam myVMVMNic.* De VM en netwerkinterface maken deel uit van een resourcegroep met de naam *myResourceGroup* en zijn in de regio *VS -* oost. Wijzig de waarden in de stappen, waar van toepassing, voor de VM voor wie u het probleem wilt diagnosticeren.

## <a name="diagnose-using-azure-portal"></a>Diagnoses stellen met Azure Portal

1. Meld u aan bij Azure [Portal](https://portal.azure.com) met een Azure-account met [de benodigde machtigingen.](virtual-network-network-interface.md#permissions)
2. Voer bovenaan de Azure Portal de naam van de VM in het zoekvak in. Wanneer de naam van de VM wordt weergegeven in de zoekresultaten, selecteert u deze.
3. Selecteer **onder INSTELLINGEN** de optie **Netwerken,** zoals wordt weergegeven in de volgende afbeelding:

   ![Schermopname van de Azure Portal met netwerkinstellingen voor mijn V M V M Nic.](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   De regels die u in de vorige afbeelding ziet, zijn voor een netwerkinterface met de **naam myVMVMNic.** U ziet dat er REGELS **VOOR BINNENKOMENDE POORT zijn** voor de netwerkinterface van twee verschillende netwerkbeveiligingsgroepen:
   
   - **mySubnetNSG: gekoppeld** aan het subnet waarin de netwerkinterface zich in.
   - **myVMNSG: gekoppeld** aan de netwerkinterface in de VM met de **naam myVMVMNic.**

   De regel **DenyAllInBound** verhindert inkomende communicatie naar de VM via poort 80, van internet, zoals beschreven in het [scenario](#scenario). De regel *vermeldt 0.0.0.0/0* voor **SOURCE,** dat internet bevat. Geen enkele andere regel met een hogere prioriteit (lager nummer) staat binnenkomende poort 80 toe. Zie Een probleem oplossen als u wilt toestaan dat poort 80 van internet naar de VM [binnenkomende poort 80 wordt gebruikt.](#resolve-a-problem) Zie Netwerkbeveiligingsgroepen voor meer informatie over beveiligingsregels en hoe Deze worden [toegepast in Azure.](./network-security-groups-overview.md)

   Onderaan de afbeelding ziet u ook **REGELS VOOR UITGAANDE POORT.** Onder staan de regels voor uitgaande poort voor de netwerkinterface. Hoewel de afbeelding slechts vier regels voor binnenkomende verkeer voor elke NSG toont, kunnen uw NSG's veel meer dan vier regels hebben. In de afbeelding ziet u **VirtualNetwork** onder **SOURCE** en **DESTINATION** en **AzureLoadBalancer** onder **SOURCE**. **VirtualNetwork** en **AzureLoadBalancer zijn** [servicetags.](./network-security-groups-overview.md#service-tags) Servicetags vertegenwoordigen een groep IP-adres voorvoegsels om de complexiteit voor het maken van beveiligingsregel te minimaliseren.

4. Zorg ervoor dat de VM actief is en selecteer vervolgens Effectieve beveiligingsregels **,** zoals weergegeven in de vorige afbeelding, om de effectieve beveiligingsregels te zien, zoals wordt weergegeven in de volgende afbeelding:

   ![Schermopname van het deelvenster Effectieve beveiligingsregels met Downloaden geselecteerd en Regel voor binnenkomende binnenkomende toegang ToestaanAzureLoadBalancer geselecteerd.](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   De vermelde regels zijn dezelfde als u in stap 3 hebt gezien, maar er zijn verschillende tabbladen voor de NSG die zijn gekoppeld aan de netwerkinterface en het subnet. Zoals u in de afbeelding kunt zien, worden alleen de eerste 50 regels weergegeven. Als u een CSV-bestand wilt downloaden dat alle regels bevat, selecteert u **Downloaden.**

   Als u wilt zien welke voorvoegsels elke servicetag vertegenwoordigt, selecteert u een regel, zoals de regel met de **naam AllowAzureLoadBalancerInbound.** In de volgende afbeelding ziet u de voorvoegsels voor de servicetag **AzureLoadBalancer:**

   ![Schermopname toont Adres voorvoegsels voor AllowAzureLoadBalancerInbound ingevoerd.](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   Hoewel de **servicetag AzureLoadBalancer** slechts één voorvoegsel vertegenwoordigt, vertegenwoordigen andere servicetags verschillende voorvoegsels.

5. In de vorige stappen zijn de beveiligingsregels voor een netwerkinterface met de naam **myVMVMNic** getoond, maar u hebt ook een netwerkinterface met de naam **myVMVMNic2** gezien in enkele van de vorige afbeeldingen. Aan de VM in dit voorbeeld zijn twee netwerkinterfaces gekoppeld. De effectieve beveiligingsregels kunnen per netwerkinterface verschillen.

   Als u de regels voor de **netwerkinterface myVMVMNic2** wilt zien, selecteert u deze. Zoals weergegeven in de afbeelding die volgt, heeft de netwerkinterface dezelfde regels die zijn gekoppeld aan het subnet als de **netwerkinterface myVMVMNic,** omdat beide netwerkinterfaces zich in hetzelfde subnet. Wanneer u een NSG aan een subnet koppelt, worden de regels toegepast op alle netwerkinterfaces in het subnet.

   ![Schermopname van de Azure Portal met netwerkinstellingen voor mijn V M V M Nic 2.](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   In tegenstelling **tot de netwerkinterface myVMVMNic** heeft de netwerkinterface **myVMVMNic2** geen netwerkbeveiligingsgroep. Aan elke netwerkinterface en elk subnet kan nul of één NSG zijn gekoppeld. De NSG die aan elke netwerkinterface of elk subnet is gekoppeld, kan hetzelfde of anders zijn. U kunt dezelfde netwerkbeveiligingsgroep koppelen aan zoveel netwerkinterfaces en subnetten als u wilt.

Hoewel effectieve beveiligingsregels zijn bekeken via de VM, kunt u ook effectieve beveiligingsregels weergeven via een individu:
- **Netwerkinterface:** informatie over het [weergeven van een netwerkinterface.](virtual-network-network-interface.md#view-network-interface-settings)
- **NSG:** meer informatie over het [weergeven van een NSG.](manage-network-security-group.md#view-details-of-a-network-security-group)

## <a name="diagnose-using-powershell"></a>Diagnoses stellen met behulp van PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt de opdrachten uitvoeren die volgen in de [Azure Cloud Shell](https://shell.azure.com/powershell)of door PowerShell op uw computer uit te voeren. De Azure Cloud Shell is een gratis interactieve shell. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Als u PowerShell op uw computer hebt uitgevoerd, hebt u de Azure PowerShell module versie 1.0.0 of hoger nodig. Voer `Get-Module -ListAvailable Az` uit op uw computer om de geïnstalleerde versie te vinden. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal gebruikt, moet u ook uitvoeren om u aan te melden bij Azure met een account met `Connect-AzAccount` [de benodigde machtigingen](virtual-network-network-interface.md#permissions).

Haal de effectieve beveiligingsregels voor een netwerkinterface op [met Get-AzEffectiveNetworkSecurityGroup.](/powershell/module/az.network/get-azeffectivenetworksecuritygroup) In het volgende voorbeeld worden de effectieve beveiligingsregels voor een netwerkinterface met de naam *myVMVMNic,* die zich in een resourcegroep met de *naam myResourceGroup.*

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup
```

De uitvoer wordt geretourneerd in json-indeling. Zie Opdrachtuitvoer interpreteren voor [meer informatie over de uitvoer.](#interpret-command-output)
Uitvoer wordt alleen geretourneerd als een NSG is gekoppeld aan de netwerkinterface, het subnet waarin de netwerkinterface zich of beide. De VM moet de status Actief hebben. Op een VM kunnen meerdere netwerkinterfaces met verschillende NSG's zijn toegepast. Voer bij het oplossen van problemen de opdracht uit voor elke netwerkinterface.

Als u nog steeds een verbindingsprobleem hebt, bekijkt u [aanvullende diagnose](#additional-diagnosis) en [overwegingen.](#considerations)

Als u de naam van een netwerkinterface niet weet, maar wel de naam weet van de VM waar de netwerkinterface aan is gekoppeld, retourneren de volgende opdrachten de ID's van alle netwerkinterfaces die zijn gekoppeld aan een VM:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

U ontvangt uitvoer die vergelijkbaar is met het volgende voorbeeld:

```output
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

In de vorige uitvoer is de naam van de netwerkinterface *myVMVMNic.*

## <a name="diagnose-using-azure-cli"></a>Diagnose uitvoeren met behulp van Azure CLI

Als u azure CLI-opdrachten (Opdrachtregelinterface) gebruikt om taken in dit artikel uit te voeren, voert u de opdrachten uit in [de Azure Cloud Shell](https://shell.azure.com/bash)of door de CLI uit te voeren vanaf uw computer. Voor dit artikel is Versie 2.0.32 of hoger van Azure CLI vereist. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Als u de Azure CLI lokaal gebruikt, moet u ook uitvoeren en u aanmelden bij Azure met een account met `az login` [de benodigde machtigingen.](virtual-network-network-interface.md#permissions)

Haal de effectieve beveiligingsregels voor een netwerkinterface op [met az network nic list-effective-nsg](/cli/azure/network/nic#az_network_nic_list_effective_nsg). In het volgende voorbeeld worden de effectieve beveiligingsregels voor een netwerkinterface met de naam *myVMVMNic,* die zich in een resourcegroep met de *naam myResourceGroup.*

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Uitvoer wordt geretourneerd in json-indeling. Zie Opdrachtuitvoer interpreteren voor [meer informatie over de uitvoer.](#interpret-command-output)
Uitvoer wordt alleen geretourneerd als een NSG is gekoppeld aan de netwerkinterface, het subnet waarin de netwerkinterface zich of beide. De VM moet de status Actief hebben. Op een VM kunnen meerdere netwerkinterfaces met verschillende NSG's zijn toegepast. Voer bij het oplossen van problemen de opdracht uit voor elke netwerkinterface.

Als u nog steeds een verbindingsprobleem hebt, bekijkt u [aanvullende diagnose](#additional-diagnosis) en [overwegingen.](#considerations)

Als u de naam van een netwerkinterface niet weet, maar wel de naam weet van de VM waar de netwerkinterface aan is gekoppeld, retourneren de volgende opdrachten de ID's van alle netwerkinterfaces die zijn gekoppeld aan een VM:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

In de geretourneerde uitvoer ziet u informatie die lijkt op het volgende voorbeeld:

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

In de vorige uitvoer is de naam van de netwerkinterface *myVMVMNic-interface*.

## <a name="interpret-command-output"></a>Opdrachtuitvoer interpreteren

Ongeacht of u [PowerShell](#diagnose-using-powershell)of de [Azure CLI](#diagnose-using-azure-cli) hebt gebruikt om het probleem vast te stellen, ontvangt u uitvoer met de volgende informatie:

- **NetworkSecurityGroup:** de id van de netwerkbeveiligingsgroep.
- **Associatie:** hiermee wordt bepaald of de netwerkbeveiligingsgroep is gekoppeld *aan een NetworkInterface* of *Subnet.* Als een NSG aan beide is gekoppeld, wordt de uitvoer geretourneerd met **NetworkSecurityGroup,** **Association** en **EffectiveSecurityRules** voor elke NSG. Als de NSG is gekoppeld of onmiddellijk wordt losgekoppeld voordat de opdracht wordt uitgevoerd om de effectieve beveiligingsregels weer te geven, moet u mogelijk een paar seconden wachten totdat de wijziging wordt weergegeven in de uitvoer van de opdracht.
- **EffectiveSecurityRules:** Een uitleg van elke eigenschap wordt beschreven in [Een beveiligingsregel maken.](manage-network-security-group.md#create-a-security-rule) Regelnamen die voorafgaan *aan defaultSecurityRules/* zijn standaardbeveiligingsregels die in elke NSG bestaan. Regelnamen voorafgaand aan *securityRules/* zijn regels die u hebt gemaakt. Regels die een [servicetag](./network-security-groups-overview.md#service-tags)opgeven, zoals **Internet**, **VirtualNetwork** en **AzureLoadBalancer** voor de eigenschappen **destinationAddressPrefix** of **sourceAddressPrefix,** hebben ook waarden voor de eigenschap **expandedDestinationAddressPrefix.** De **eigenschap expandedDestinationAddressPrefix** bevat een lijst met alle adresvoorvoegsels die worden vertegenwoordigd door de servicetag.

Als er dubbele regels worden vermeld in de uitvoer, komt dit doordat een NSG is gekoppeld aan zowel de netwerkinterface als het subnet. Beide NSG's hebben dezelfde standaardregels en hebben mogelijk extra dubbele regels als u uw eigen regels hebt gemaakt die in beide NSG's hetzelfde zijn.

De regel met de naam **defaultSecurityRules/DenyAllInBound** verhindert inkomende communicatie naar de VM via poort 80, vanaf internet, zoals beschreven in [het scenario](#scenario). Geen enkele andere regel met een hogere prioriteit (lager nummer) staat binnenkomende poort 80 van internet toe.

## <a name="resolve-a-problem"></a>Een probleem oplossen

Of u nu Azure [Portal,](#diagnose-using-azure-portal) [PowerShell](#diagnose-using-powershell)of [de Azure CLI](#diagnose-using-azure-cli) gebruikt om het probleem te diagnosticeren dat wordt beschreven in het [scenario](#scenario) in dit artikel, de oplossing is het maken van een netwerkbeveiligingsregel met de volgende eigenschappen:

| Eigenschap                | Waarde                                                                              |
|---------                |---------                                                                           |
| Bron                  | Elk                                                                                |
| Poortbereiken van bron      | Alle                                                                                |
| Doel             | Het IP-adres van de virtuele machine, een bereik van IP-adressen of alle adressen in het subnet. |
| Poortbereiken van doel | 80                                                                                 |
| Protocol                | TCP                                                                                |
| Bewerking                  | Toestaan                                                                              |
| Prioriteit                | 100                                                                                |
| Naam                    | Allow-HTTP-All                                                                     |

Nadat u de regel hebt gemaakt, is binnenkomende toegang tot poort 80 toegestaan vanaf internet, omdat de prioriteit van de regel hoger is dan de standaardbeveiligingsregel met de naam *DenyAllInBound,* waardoor het verkeer wordt weigert. Meer informatie over het [maken van een beveiligingsregel.](manage-network-security-group.md#create-a-security-rule) Als er verschillende NSG's zijn gekoppeld aan zowel de netwerkinterface als het subnet, moet u dezelfde regel in beide NSG's maken.

Wanneer Inkomende verkeer door Azure wordt verwerkt, worden regels verwerkt in de NSG die is gekoppeld aan het subnet (als er een gekoppelde NSG is) en worden vervolgens de regels verwerkt in de NSG die is gekoppeld aan de netwerkinterface. Als er een NSG is gekoppeld aan de netwerkinterface en het subnet, moet de poort in beide NSG's zijn geopend om het verkeer de virtuele machine te laten bereiken. Om problemen met beheer en communicatie te gemaken, raden we u aan een NSG aan een subnet te koppelen in plaats van aan afzonderlijke netwerkinterfaces. Als VM's binnen een subnet verschillende beveiligingsregels nodig hebben, kunt u de netwerkinterfaces lid maken van een toepassingsbeveiligingsgroep (ASG) en een ASG opgeven als bron en bestemming van een beveiligingsregel. Meer informatie over [toepassingsbeveiligingsgroepen.](./network-security-groups-overview.md#application-security-groups)

Zie Overwegingen en Aanvullende diagnose [](#considerations) als u nog steeds communicatieproblemen hebt.

## <a name="considerations"></a>Overwegingen

Houd rekening met de volgende punten bij het oplossen van verbindingsproblemen:

* Standaardbeveiligingsregels blokkeren binnenkomende toegang vanaf internet en staan alleen inkomende verkeer van het virtuele netwerk toe. Als u inkomende verkeer van internet wilt toestaan, voegt u beveiligingsregels toe met een hogere prioriteit dan standaardregels. Meer informatie over [standaardbeveiligingsregels](./network-security-groups-overview.md#default-security-rules)of het toevoegen [van een beveiligingsregel.](manage-network-security-group.md#create-a-security-rule)
* Als u virtuele netwerken met peering hebt, wordt de servicetag **VIRTUAL_NETWORK** standaard automatisch uitgebreid met voorvoegsels voor virtuele peernetwerken. Als u problemen met betrekking tot peering voor virtuele netwerken wilt oplossen, kunt u de voorvoegsels bekijken in de **lijst ExpandedAddressPrefix.** Meer informatie over [peering voor virtuele netwerken](virtual-network-peering-overview.md) en [servicetags.](./network-security-groups-overview.md#service-tags)
* Effectieve beveiligingsregels worden alleen weergegeven voor een netwerkinterface als er een NSG is gekoppeld aan de netwerkinterface en, of, subnet van de VM, en als de virtuele machine actief is.
* Als er geen NSG's zijn gekoppeld aan de netwerkinterface of het subnet en u een openbaar [IP-adres](virtual-network-public-ip-address.md) hebt toegewezen aan een VM, zijn alle poorten geopend voor binnenkomende toegang van en uitgaande toegang tot elke locatie. Als de virtuele machine een openbaar IP-adres heeft, raden we u aan een NSG toe te passen op het subnet van de netwerkinterface.

## <a name="additional-diagnosis"></a>Aanvullende diagnose

* Als u een snelle test wilt uitvoeren om te bepalen of verkeer van of naar een VM is toegestaan, gebruikt u de [functie IP-stroom](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) controleren van Azure Network Watcher. Ip-stroom controleren geeft aan of verkeer is toegestaan of geweigerd. Als dit wordt geweigerd, wordt met IP-stroom controleren bepaald welke beveiligingsregel het verkeer weigert.
* Als er geen beveiligingsregels zijn die ervoor zorgen dat de netwerkverbinding van een VM mislukt, kan het probleem worden veroorzaakt door:
  * Firewallsoftware die wordt uitgevoerd binnen het besturingssysteem van de VM
  * Routes die zijn geconfigureerd voor virtuele apparaten of on-premises verkeer. Internetverkeer kan worden omgeleid naar uw on-premises netwerk via [geforceerd tunnelen.](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Als u geforceerd internetverkeer naar een virtueel apparaat of on-premises tunnelt, kunt u mogelijk geen verbinding maken met de virtuele machine via internet. Zie Diagnose a virtual machine network traffic routing problem (Diagnose van een routeringsprobleem met netwerkverkeer op een virtuele machine) voor meer informatie over het diagnosticeren van routeproblemen die de verkeersstroom van de VM [kunnen bemoeilijken.](diagnose-network-routing-problem.md)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over alle taken, eigenschappen en instellingen voor een [netwerkbeveiligingsgroep en](manage-network-security-group.md#work-with-network-security-groups) [beveiligingsregels.](manage-network-security-group.md#work-with-security-rules)
- Meer informatie [over standaardbeveiligingsregels,](./network-security-groups-overview.md#default-security-rules) [servicetags](./network-security-groups-overview.md#service-tags)en hoe Azure beveiligingsregels verwerkt voor [inkomende](./network-security-groups-overview.md#network-security-groups) en uitgaande verkeer voor een VM.