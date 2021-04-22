---
title: Openbare IP-adressen beheren | Microsoft Docs
titleSuffix: Azure Virtual Network
description: Openbare IP-adressen beheren.  Lees ook hoe een openbaar IP-adres een resource is met eigen configureerbare instellingen.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: 3277d5836d85f1071b7aa169cc83896934a2f63d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872403"
---
# <a name="manage-public-ip-addresses"></a>Twee openbare IP-adresse beheren

Meer informatie over een openbaar IP-adres en het maken, wijzigen en verwijderen van een ip-adres. Een openbaar IP-adres is een resource met eigen configureerbare instellingen. Als u een openbaar IP-adres toewijst aan een Azure-resource die openbare IP-adressen ondersteunt, kunt u het volgende doen:
- Binnenkomende communicatie van internet naar de resource, zoals Azure Virtual Machines (VM), Azure-toepassing Gateways, Azure Load Balancers, Azure VPN Gateways en andere. U kunt nog steeds via internet communiceren met bepaalde resources, zoals VM's, als er geen openbaar IP-adres aan een VM is toegewezen, zolang de VM deel uitmaakt van een back-endpool van een load balancer en de load balancer een openbaar IP-adres krijgt toegewezen. Zie de documentatie voor de service om te bepalen of aan een resource voor een specifieke Azure-service een openbaar IP-adres kan worden toegewezen of dat er mee kan worden gecommuniceerd via het openbare IP-adres van een andere Azure-resource.
- Uitgaande connectiviteit met internet met behulp van een voorspelbaar IP-adres. Een virtuele machine kan bijvoorbeeld uitgaand communiceren met internet zonder dat er een openbaar IP-adres aan is toegewezen, maar het adres is standaard het netwerkadres dat door Azure wordt omgezet in een onvoorspelbaar openbaar adres. Als u een openbaar IP-adres toewijst aan een resource, weet u welk IP-adres wordt gebruikt voor de uitgaande verbinding. Hoewel dit voorspelbaar is, kan het adres worden gewijzigd, afhankelijk van de gekozen toewijzingsmethode. Zie Een openbaar [IP-adres maken voor meer informatie.](#create-a-public-ip-address) Zie Informatie over uitgaande verbindingen voor meer informatie over uitgaande verbindingen [vanuit Azure-resources.](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voltooi de volgende taken voordat u de stappen in een van de secties van dit artikel uitvoert:

- Als u nog geen Azure-account hebt, kunt u zich aanmelden voor een [gratis proefaccount.](https://azure.microsoft.com/free)
- Als u de portal gebruikt, opent https://portal.azure.com u en meld u zich aan met uw Azure-account.
- Als u PowerShell-opdrachten gebruikt om taken in dit artikel uit te voeren, voert u de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/powershell)of door PowerShell op uw computer uit te voeren. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Voor deze zelfstudie is Azure PowerShell moduleversie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.
- Als u cli-opdrachten (Opdrachtregelinterface) van Azure gebruikt om taken in dit artikel uit te voeren, voert u de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/bash)of door de CLI op uw computer uit te voeren. Voor deze zelfstudie is azure CLI versie 2.0.31 of hoger vereist. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Als u de Azure CLI lokaal gebruikt, moet u ook uitvoeren om `az login` een verbinding met Azure te maken.

Het account bij wie u zich aanmeldt of verbinding [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) maakt met Azure, [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) moet worden toegewezen aan de rol Netwerkbijdrager of aan een aangepaste rol die de juiste acties krijgt toegewezen die worden vermeld in [Machtigingen.](#permissions)

Openbare IP-adressen hebben een nominaal bedrag. Als u de prijzen wilt bekijken, leest u de [pagina met prijzen voor IP-adressen.](https://azure.microsoft.com/pricing/details/ip-addresses)

## <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Raadpleeg de volgende pagina's voor instructies over het maken van openbare IP-adressen met behulp van de portal, PowerShell of CLI:

 * [Openbaar IP-adressen maken - Portal](./create-public-ip-portal.md?tabs=option-create-public-ip-standard-zones)
 * [Openbaar IP-adressen maken - PowerShell](./create-public-ip-powershell.md?tabs=option-create-public-ip-standard-zones)
 * [Openbaar IP-adressen maken - Azure CLI](./create-public-ip-cli.md?tabs=option-create-public-ip-standard-zones)

>[!NOTE]
>Hoewel de portal de mogelijkheid biedt om twee openbare IP-adresresources (één IPv4 en één IPv6) te maken, maken de PowerShell- en CLI-opdrachten één resource met een adres voor de ene of de andere IP-versie. Als u twee openbare IP-adresresources wilt, één voor elke IP-versie, moet u de opdracht twee keer uitvoeren, met verschillende namen en IP-versies voor de openbare IP-adresresources.

Zie de onderstaande tabel voor meer informatie over de specifieke kenmerken van een openbaar IP-adres tijdens het maken.

   |Instelling|Vereist?|Details|
   |---|---|---|
   |IP-versie|Yes| Selecteer IPv4, IPv6 of Beide. Als u Beide selecteert, worden 2 openbare IP-adressen gemaakt: 1 IPv4-adres en 1 IPv6-adres. Meer informatie over [IPv6 in Azure VNETs](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
   |SKU|Yes|Alle openbare IP-adressen die zijn gemaakt  vóór de introductie van SKU's zijn openbare IP-adressen van basic-SKU's. U kunt de SKU niet wijzigen nadat het openbare IP-adres is gemaakt. Een zelfstandige virtuele machine, virtuele machines binnen een beschikbaarheidsset of virtuele-machineschaalsets kunnen gebruikmaken van Basic- of Standard-SKU's. Het combineren van SKU's tussen virtuele machines binnen beschikbaarheidssets, schaalsets of zelfstandige VM's is niet toegestaan. **Basic** SKU: Als u een openbaar IP-adres maakt in  een regio die beschikbaarheidszones ondersteunt, is de instelling Beschikbaarheidszone *standaard* ingesteld op Geen. Openbare BASIC-IP's bieden geen ondersteuning voor beschikbaarheidszones. **Standard** SKU: Een openbaar IP-adres van een Standard-SKU kan worden gekoppeld aan een virtuele machine of een load balancer front-end. Als u een openbaar IP-adres maakt in een  regio die beschikbaarheidszones ondersteunt, is de instelling Beschikbaarheidszone standaard ingesteld op *Zone-redundant.* Zie de instelling Beschikbaarheidszone voor meer informatie over **beschikbaarheidszones.** De standaard-SKU is vereist als u het adres aan een Standard-load balancer. Zie Azure load balancer Standard SKU voor meer informatie over standard [load balancers.](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Als u een openbaar IP-adres van een standaard-SKU toewijst aan een netwerkinterface van een virtuele machine, moet u het bedoelde verkeer expliciet toestaan met een [netwerkbeveiligingsgroep](./network-security-groups-overview.md#network-security-groups). Communicatie met de resource mislukt totdat u een netwerkbeveiligingsgroep maakt en koppelt en het gewenste verkeer expliciet toestaat.|
   |Laag|Yes|Geeft aan of het IP-adres is gekoppeld aan een regio **(regionaal**) of dat het 'anycast' is van meerdere regio's **(globaal).** Houd er rekening mee dat een IP-adres voor globale lagen preview-functionaliteit is voor Standard IP-adressen en momenteel alleen wordt gebruikt voor de *regio-overschrijdende Load Balancer.*|
   |Name|Yes|De naam moet uniek zijn binnen de resourcegroep die u selecteert.|
   |IP-adrestoewijzing|Yes|**Dynamisch:** Dynamische adressen worden pas toegewezen nadat een openbaar IP-adres is gekoppeld aan een Azure-resource en de resource voor de eerste keer wordt gestart. Dynamische adressen kunnen worden gewijzigd als ze zijn toegewezen aan een resource, zoals een virtuele machine, en de virtuele machine wordt gestopt (toewijzing wordt in de toewijzing van de virtuele machine in de weg gezet) en vervolgens opnieuw wordt gestart. Het adres blijft hetzelfde als een virtuele machine opnieuw wordt opgestart of gestopt (maar de toewijzing niet wordt toegewezen). Dynamische adressen worden vrijgegeven wanneer een openbare IP-adresresource wordt loskoppeld van een resource waar deze aan is gekoppeld. **Statisch:** Statische adressen worden toegewezen wanneer een openbaar IP-adres wordt gemaakt. Statische adressen worden pas vrijgegeven wanneer een openbare IP-adresresource wordt verwijderd. Als het adres niet is gekoppeld aan een resource, kunt u de toewijzingsmethode wijzigen nadat het adres is gemaakt. Als het adres is gekoppeld aan een resource, kunt u de toewijzingsmethode mogelijk niet wijzigen. Als u *IPv6 als IP-versie* **selecteert,** moet de toewijzingsmethode *Dynamisch zijn* voor de Basic-SKU.  Standaard-SKU-adressen zijn *statisch* voor zowel IPv4 als IPv6. |
   |Time-out voor inactiviteit (minuten)|No|Het aantal minuten dat een TCP- of HTTP-verbinding geopend moet blijven zonder dat clients keep-alive-berichten moeten verzenden. Als u IPv6 selecteert als **IP-versie,** kan deze waarde niet worden gewijzigd. |
   |DNS-naamlabel|No|Moet uniek zijn binnen de Azure-locatie waarin u de naam maakt (voor alle abonnementen en alle klanten). Azure registreert automatisch de naam en het IP-adres in de DNS, zodat u met de naam verbinding kunt maken met een resource. Azure voegen een standaardsubnet, zoals *location.cloudapp.azure.com* (waarbij locatie de locatie is die u selecteert) toe aan de naam die u op geeft, om de volledig gekwalificeerde DNS-naam te maken. Als u ervoor kiest om beide adresversies te maken, wordt dezelfde DNS-naam toegewezen aan zowel de IPv4- als IPv6-adressen. De standaard-DNS van Azure bevat zowel IPv4 A- als IPv6 AAAA-naamrecords en reageert met beide records wanneer de DNS-naam wordt op gezocht. De client kiest met welk adres (IPv4 of IPv6) moet worden gecommuniceert. In plaats van of naast het gebruik van het DNS-naamlabel met het standaardachtervoegsel, kunt u de Azure DNS-service gebruiken om een DNS-naam met een aangepast achtervoegsel te configureren dat wordt omgezet naar het openbare IP-adres. Zie [Azure DNS gebruiken met een openbaar IP-adres van Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) voor meer informatie.|
   |Naam (alleen zichtbaar als u IP-versie van beide **selecteert)**|Ja, als u IP-versie van beide **selecteert**|De naam moet anders zijn dan de naam die u voor de voornaam **in** deze lijst op invoeren. Als u ervoor kiest om zowel een IPv4- als een IPv6-adres te maken, maakt de portal twee afzonderlijke openbare IP-adresresources, één met elke IP-adresversie die aan het adres is toegewezen.|
   |IP-adrestoewijzing (alleen zichtbaar als u IP-versie van beide **selecteert)**|Ja, als u IP-versie van beide **selecteert**|Dezelfde beperkingen als hierboven voor IP-adrestoewijzing|
   |Abonnement|Yes|Moet bestaan in hetzelfde [abonnement als](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) de resource waaraan u de openbare IP-adressen koppelt.|
   |Resourcegroep|Yes|Kan bestaan in dezelfde of een andere [resourcegroep](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) als de resource waaraan u de openbare IP-adressen koppelt.|
   |Locatie|Ja|Moet zich op dezelfde locatie [bevinden,](https://azure.microsoft.com/regions)ook wel regio genoemd, als de resource waaraan u de openbare IP-adressen koppelt.|
   |Beschikbaarheidszone| No | Deze instelling wordt alleen weergegeven als u een ondersteunde locatie selecteert. Zie Overzicht van beschikbaarheidszones voor een lijst [met ondersteunde locaties.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Als u de **Basis-SKU** *hebt* geselecteerd, wordt Geen automatisch voor u geselecteerd. Als u liever een specifieke zone garandeert, kunt u een specifieke zone selecteren. Beide keuzen zijn niet zone-redundant. Als u de **Standaard-SKU** hebt geselecteerd: Zone-redundant wordt automatisch voor u geselecteerd en maakt uw gegevenspad bestand tegen zonestoringen. Als u liever een specifieke zone garandeert die niet bestand is tegen zonestoringen, kunt u een specifieke zone selecteren.

## <a name="view-modify-settings-for-or-delete-a-public-ip-address"></a>Instellingen voor een openbaar IP-adres weergeven, wijzigen of verwijderen

   - **Weergave/lijst:** als u instellingen voor een openbaar IP-adres wilt controleren, met inbegrip van de SKU, het adres, eventuele toepasselijke verbanden (bijvoorbeeld virtuele-machine-NIC, Load Balancer front-Load Balancer).
   - **Wijzigen:** als u instellingen wilt wijzigen met behulp van de informatie in stap 4 van een openbaar [IP-adres](#create-a-public-ip-address)maken, zoals de time-out voor inactief, het DNS-naamlabel of de toewijzingsmethode.  (Zie Openbare [IP-adressen](./virtual-network-public-ip-address-upgrade.md)van Azure upgraden voor het volledige proces van het upgraden van een openbare IP-SKU van Basic naar Standard.)
   >[!WARNING]
   >Als u de toewijzing voor een openbaar IP-adres wilt wijzigen van statisch in dynamisch, moet u eerst het adres loskoppelen van alle toepasselijke IP-configuraties (zie **de sectie** Verwijderen).  Houd er ook rekening mee dat wanneer u de toewijzingsmethode wijzigt van statisch in dynamisch, u het IP-adres verliest dat is toegewezen aan het openbare IP-adres. Hoewel de openbare DNS-servers van Azure een toewijzing onderhouden tussen statische of dynamische adressen en een DNS-naamlabel (als u er een hebt gedefinieerd), kan een dynamisch IP-adres worden gewijzigd wanneer de virtuele machine wordt gestart nadat deze de status Gestopt heeft (toewijzing is verwijderd). Wijs een statisch IP-adres toe om te voorkomen dat het adres wordt veranderd.
   
|Bewerking|Azure Portal|Azure PowerShell|Azure CLI|
|---|---|---|---|
|Weergave | In de **sectie Overzicht** van een openbaar IP-adres |[Get-AzPublicIpAddress om](/powershell/module/az.network/get-azpublicipaddress) een openbaar IP-adresobject op te halen en de instellingen ervan weer te geven| [az network public-ip show om instellingen](/cli/azure/network/public-ip#az_network_public_ip_show) weer te geven|
|Lijst | Onder de **categorie Openbare IP-adressen** |[Get-AzPublicIpAddress om](/powershell/module/az.network/get-azpublicipaddress) een of meer openbare IP-adresobjecten op te halen en de instellingen ervan weer te geven|[az network public-ip list](/cli/azure/network/public-ip#az_network_public_ip_list) to list public IP addresses|
|Wijzigen | Voor een IP-adres dat is  loskoppeld, selecteert u Configuratie om de time-out voor inactieve gegevens, het DNS-naamlabel of de toewijzing van basic-IP te wijzigen van Statisch naar Dynamisch  |[Set-AzPublicIpAddress om](/powershell/module/az.network/set-azpublicipaddress) instellingen bij te werken |[az network public-ip update](/cli/azure/network/public-ip#az_network_public_ip_update) to update |

   - **Verwijderen:** voor het verwijderen van openbare IP-adressen is vereist dat het openbare IP-object niet is gekoppeld aan een IP-configuratie of virtuele-machine-NIC. Zie de onderstaande tabel voor meer informatie.

|Resource|Azure Portal|Azure PowerShell|Azure CLI|
|---|---|---|---|
|[Virtuele machine](./remove-public-ip-address-vm.md)|Selecteer **Loskoppelen om** het IP-adres te ontkoppelen van de NIC-configuratie en selecteer **vervolgens Verwijderen.**|[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) om het IP-adres te ontkoppelen van de NIC-configuratie; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) verwijderen|[az network public-ip update --remove om](/cli/azure/network/public-ip#az_network_public_ip_update) het IP-adres te ontkoppelen van de NIC-configuratie; [az network public-ip delete](/cli/azure/network/public-ip#az_network_public_ip_delete) to delete |
|Load Balancer Front-end | Navigeer naar een niet-gebruikt openbaar IP-adres, selecteer Koppelen en kies de Load Balancer door de relevante front-end-IP-configuratie om dit te vervangen (het oude IP-adres kan vervolgens worden verwijderd met dezelfde methode als voor de VM)   | [Set-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig) om nieuwe front-end-IP-configuratie te koppelen aan openbare Load Balancer; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) om te verwijderen; kan ook [Remove-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/remove-azloadbalancerfrontendipconfig) gebruiken om front-end-IP-configuratie te verwijderen als er meer dan één ip-adres is |[az network lb frontend-ip update](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_update) to associate new Frontend IP config with Public Load Balancer; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) om te verwijderen; kan ook [az network lb frontend-ip delete gebruiken om front-end-IP-configuratie](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_delete) te verwijderen als er meer dan één ip-adres is|
|Firewall|N.v.t.| [De toewijzing van de firewall verwijderen () om](../firewall/firewall-faq.yml#how-can-i-stop-and-start-azure-firewall) de toewijzing van de firewall te verwijderen en alle IP-configuraties te verwijderen | [az network firewall ip-config delete to remove](/cli/azure/network/firewall/ip-config#az_network_firewall_ip_config_delete) IP (but must use PowerShell to deallocate first)|

## <a name="virtual-machine-scale-sets"></a>Virtuele-machineschaalsets

Wanneer u een virtuele-machineschaalset met openbare IP-adressen gebruikt, zijn er geen afzonderlijke openbare IP-objecten gekoppeld aan de afzonderlijke exemplaren van de virtuele machine. Er kan echter een openbaar IP-voorvoegselobject [worden gebruikt om de IP-adressen van het exemplaar te genereren.](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/)

Als u de openbare IP-adressen wilt op een virtuele-machineschaalset, kunt u PowerShell ([Get-AzPublicIpAddress -VirtualMachineScaleSetName)](/powershell/module/az.network/get-azpublicipaddress)of CLI[(az vmss list-instance-public-ips)](/cli/azure/vmss#az_vmss_list_instance_public_ips)gebruiken.

Zie voor meer informatie [Netwerken voor schaalsets voor virtuele Azure-machines](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine).

## <a name="assign-a-public-ip-address"></a>Een openbaar IP-adres toewijzen

Meer informatie over het toewijzen van een openbaar IP-adres aan de volgende resources:

- Een [virtuele Windows-](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [Linux-machine](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (bij het maken) of op een bestaande [virtuele machine](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Openbare Load Balancer](../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway](../application-gateway/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Site-naar-site-verbinding met behulp van een VPN Gateway](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Virtuele-machineschaalset](../virtual-machine-scale-sets/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Machtigingen

Als u taken wilt uitvoeren op openbare IP-adressen, moet uw [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) account zijn toegewezen aan de rol Netwerkbijdrager of aan een aangepaste rol die de juiste acties krijgt toegewezen die in de volgende tabel worden vermeld: [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)

| Bewerking                                                             | Name                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Een openbaar IP-adres lezen                                          |
| Microsoft.Network/publicIPAddresses/write                          | Een openbaar IP-adres maken of bijwerken                           |
| Microsoft.Network/publicIPAddresses/delete                         | Een openbaar IP-adres verwijderen                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Een openbaar IP-adres aan een resource koppelen                    |

## <a name="next-steps"></a>Volgende stappen

- Een openbaar IP-adres maken met [behulp van PowerShell-](powershell-samples.md) of [Azure CLI-voorbeeldscripts](cli-samples.md) of met Behulp van Azure [Resource Manager sjablonen](template-samples.md)
- Definities voor [Azure Policy ip-adressen](./policy-reference.md) maken en toewijzen
