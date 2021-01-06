---
title: Open bare IP-adressen beheren | Microsoft Docs
titleSuffix: Azure Virtual Network
description: Open bare IP-adressen beheren.  Lees ook hoe een openbaar IP-adres een bron is met een eigen Configureer bare instellingen.
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
ms.openlocfilehash: 1d7ba163f05e827e9262215fd604fa175efa6fc8
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955600"
---
# <a name="manage-public-ip-addresses"></a>Twee openbare IP-adresse beheren

Meer informatie over een openbaar IP-adres en hoe u er een kunt maken, wijzigen en verwijderen. Een openbaar IP-adres is een bron met eigen Configureer bare instellingen. U kunt als volgt een openbaar IP-adres toewijzen aan een Azure-resource die open bare IP-adressen ondersteunt:
- Inkomende communicatie van het internet naar de resource, zoals Azure Virtual Machines (VM), Azure-toepassing gateways, Azure load balancers, Azure VPN-gateways en anderen. U kunt nog steeds communiceren met sommige resources, zoals Vm's, van het Internet, als aan een virtuele machine geen openbaar IP-adres is toegewezen, mits de virtuele machine deel uitmaakt van een load balancer back-end-pool en aan de load balancer een openbaar IP-adres is toegewezen. Raadpleeg de documentatie voor de service om te bepalen of er een openbaar IP-adres kan worden toegewezen aan een resource voor een specifieke Azure-service of dat deze kan worden gecommuniceerd via het open bare IP-adres van een andere Azure-resource.
- Uitgaande verbinding met internet met behulp van een voorspelbaar IP-adres. Een virtuele machine kan bijvoorbeeld uitgaand verkeer naar Internet communiceren zonder dat hieraan een openbaar IP-adres is toegewezen, maar het adres is standaard het netwerk adres dat door Azure wordt vertaald naar een onvoorspelbaar openbaar adres. Door een openbaar IP-adres toe te wijzen aan een resource kunt u bepalen welk IP-adres wordt gebruikt voor de uitgaande verbinding. Het adres kan echter wel worden gewijzigd, afhankelijk van de gekozen toewijzings methode. Zie [een openbaar IP-adres maken](#create-a-public-ip-address)voor meer informatie. Zie [begrijpen van uitgaande verbindingen](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over uitgaande verbindingen van Azure-resources.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voer de volgende taken uit voordat u de stappen in een van de secties van dit artikel uitvoert:

- Als u nog geen Azure-account hebt, kunt u zich aanmelden voor een [gratis proef account](https://azure.microsoft.com/free).
- Als u de portal gebruikt, opent https://portal.azure.com u en meldt u zich aan met uw Azure-account.
- Als u Power shell-opdrachten gebruikt om taken in dit artikel te volt ooien, moet u de opdrachten uitvoeren in de [Azure Cloud shell](https://shell.azure.com/powershell)of Power shell uitvoeren vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Voor deze zelf studie is de Azure PowerShell module versie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.
- Als u Azure-opdracht regel interface opdrachten gebruikt om taken in dit artikel te volt ooien, moet u de opdrachten uitvoeren in de [Azure Cloud shell](https://shell.azure.com/bash)of door de CLI vanaf uw computer uit te voeren. Voor deze zelf studie is de Azure CLI-versie 2.0.31 of hoger vereist. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Als u de Azure CLI lokaal uitvoert, moet u ook uitvoeren `az login` om een verbinding te maken met Azure.

Het account waarmee u zich aanmeldt of verbinding maakt met Azure met, moet worden toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in [machtigingen](#permissions).

Open bare IP-adressen hebben een nominale belasting. Lees de pagina met [prijzen voor IP-adressen](https://azure.microsoft.com/pricing/details/ip-addresses) om de prijzen weer te geven.

## <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Raadpleeg de volgende pagina's voor instructies over het maken van open bare IP-adressen met behulp van de portal, Power shell of CLI:

 * [Openbaar IP-adressen maken - Portal](https://docs.microsoft.com/azure/virtual-network/create-public-ip-portal?tabs=option-create-public-ip-standard-zones)
 * [Openbaar IP-adressen maken - PowerShell](https://docs.microsoft.com/azure/virtual-network/create-public-ip-powershell?tabs=option-create-public-ip-standard-zones)
 * [Openbaar IP-adressen maken - Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-public-ip-cli?tabs=option-create-public-ip-standard-zones)

>[!NOTE]
>Hoewel de Portal de mogelijkheid biedt om twee open bare IP-adres bronnen te maken (één IPv4 en één IPv6), maken de Power shell-en CLI-opdrachten één resource met een adres voor één of meer IP-versies. Als u twee open bare IP-adres bronnen wilt, één voor elke IP-versie, moet u de opdracht twee keer uitvoeren, waarbij u verschillende namen en IP-versies opgeeft voor de open bare IP-adres bronnen.

Zie de onderstaande tabel voor meer informatie over de specifieke kenmerken van een openbaar IP-adres tijdens het maken.

   |Instelling|Vereist?|Details|
   |---|---|---|
   |IP-versie|Ja| Selecteer IPv4 of IPv6 of beide. Als u beide selecteert, worden er twee open bare IP-adressen gemaakt: 1 IPv4-adres en één IPv6-adres. Meer informatie over [IPv6 in azure VNETs](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
   |SKU|Ja|Alle open bare IP-adressen die zijn gemaakt voor de introductie van Sku's zijn de open bare IP-adressen van de **basis** -SKU. U kunt de SKU niet wijzigen nadat het open bare IP-adres is gemaakt. Een zelfstandige virtuele machine, virtuele machines binnen een beschikbaarheidsset of schaal sets voor virtuele machines kunnen gebruikmaken van basis-of standaard-Sku's. Het is niet toegestaan Sku's te mengen tussen virtuele machines binnen beschikbaarheids sets of schaal sets of zelfstandige Vm's. **Basis** SKU: als u een openbaar IP-adres maakt in een regio die beschikbaarheids zones ondersteunt, wordt de instelling voor de **beschikbaarheids zone** standaard ingesteld op *geen* . Algemene open bare Ip's bieden geen ondersteuning voor beschikbaarheids zones. **Standaard** SKU: een openbaar IP-adres van een standaard-SKU kan worden gekoppeld aan een virtuele machine of een load balancer front-end. Als u een openbaar IP-adres maakt in een regio die beschikbaarheids zones ondersteunt, wordt de instelling voor de **beschikbaarheids zone** standaard ingesteld op *zone-redundant* . Zie de instelling **beschikbaarheids zone** voor meer informatie over beschikbaarheids zones. De standaard-SKU is vereist als u het adres koppelt aan een standaard load balancer. Zie [Azure Load Balancer Standard SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over standaard load balancers. Als u een openbaar IP-adres van een standaard-SKU toewijst aan een netwerkinterface van een virtuele machine, moet u het bedoelde verkeer expliciet toestaan met een [netwerkbeveiligingsgroep](security-overview.md#network-security-groups). Communicatie met de resource mislukt totdat u een netwerkbeveiligingsgroep maakt en koppelt en het gewenste verkeer expliciet toestaat.|
   |Laag|Ja|Hiermee wordt aangegeven of het IP-adres is gekoppeld aan een regio (**regionaal**) of ' anycast ' is vanuit meerdere regio's (**globaal**). *Houd er rekening mee dat een ' globale laag ' IP is Preview-functionaliteit voor standaard ip's en momenteel alleen wordt gebruikt voor de cross-regio Load Balancer*.|
   |Naam|Ja|De naam moet uniek zijn binnen de resource groep die u selecteert.|
   |IP-adrestoewijzing|Ja|**Dynamisch:** Dynamische adressen worden alleen toegewezen nadat een openbaar IP-adres aan een Azure-resource is gekoppeld en de resource voor de eerste keer wordt gestart. Dynamische adressen kunnen worden gewijzigd als ze zijn toegewezen aan een resource, zoals een virtuele machine, en de virtuele machine is gestopt (toewijzing ongedaan gemaakt) en vervolgens opnieuw gestart. Het adres blijft hetzelfde als een virtuele machine opnieuw wordt opgestart of gestopt (maar niet ongedaan gemaakt). Dynamische adressen worden vrijgegeven wanneer een open bare IP-adres bron wordt ontkoppeld van een bron waaraan deze is gekoppeld. **Statisch:** Statische adressen worden toegewezen wanneer een openbaar IP-adres wordt gemaakt. Statische adressen worden pas vrijgegeven nadat een open bare IP-adres resource is verwijderd. Als het adres niet aan een resource is gekoppeld, kunt u de toewijzings methode wijzigen nadat het adres is gemaakt. Als het adres aan een resource is gekoppeld, kunt u de toewijzings methode mogelijk niet wijzigen. Als u *IPv6* selecteert voor de **IP-versie**, moet de toewijzings methode *dynamisch* zijn voor de basis-SKU.  Standaard-SKU-adressen zijn *statisch* voor zowel IPv4 als IPv6. |
   |Time-out voor inactiviteit (minuten)|Nee|Hoe lang duurt het voordat een TCP-of HTTP-verbinding is geopend, zonder dat er wordt gebruikgemaakt van clients om Keep-Alive-berichten te verzenden. Als u IPv6 selecteert voor de **IP-versie**, kan deze waarde niet worden gewijzigd. |
   |DNS-naamlabel|Nee|Moet uniek zijn binnen de Azure-locatie waar u de naam maakt in (voor alle abonnementen en alle klanten). Azure registreert automatisch de naam en het IP-adres in DNS zodat u verbinding kunt maken met een resource met de naam. Azure voegt een standaard-subnet, zoals *location.cloudapp.Azure.com* (waarbij locatie de locatie is die u hebt geselecteerd), toe aan de naam die u opgeeft, om de volledig gekwalificeerde DNS-naam te maken. Als u ervoor kiest beide adres versies te maken, wordt dezelfde DNS-naam toegewezen aan zowel IPv4-als IPv6-adressen. De standaard-DNS van Azure bevat IPv4 A-en IPv6 AAAA-naam records en reageert met beide records wanneer de DNS-naam wordt opgezocht. De client kiest voor welk adres (IPv4 of IPv6) moet worden gecommuniceerd. In plaats van of naast het gebruik van het DNS-naamlabel met het standaardachtervoegsel, kunt u de Azure DNS-service gebruiken om een DNS-naam met een aangepast achtervoegsel te configureren dat wordt omgezet naar het openbare IP-adres. Zie [Azure DNS gebruiken met een openbaar IP-adres van Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) voor meer informatie.|
   |Naam (alleen zichtbaar als u de IP-versie van **beide** selecteert)|Ja, als u de IP-versie van **beide** selecteert|De naam moet anders zijn dan de naam die u invoert voor de eerste **naam** in deze lijst. Als u ervoor kiest om zowel een IPv4-als een IPv6-adres te maken, worden er in de portal twee afzonderlijke open bare IP-adres bronnen gemaakt, waarvan elke toegewezen IP-adres versie hieraan is gekoppeld.|
   |Toewijzing van IP-adressen (alleen zichtbaar als u de IP-versie van **beide** selecteert)|Ja, als u de IP-versie van **beide** selecteert|Dezelfde beperkingen als voor de toewijzing van IP-adressen hierboven|
   |Abonnement|Ja|Moet zich in hetzelfde [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) bevinden als de resource waaraan u het open bare IP-adres wilt koppelen.|
   |Resourcegroep|Ja|Kan zich in dezelfde of verschillende [resource groepen](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) bevinden als de resource waaraan u het open bare IP-adres wilt koppelen.|
   |Locatie|Ja|Moet zich op dezelfde [locatie](https://azure.microsoft.com/regions)bevinden, ook wel regio genoemd, als de resource waaraan u het open bare IP-adres koppelt.|
   |Beschikbaarheidszone| Nee | Deze instelling wordt alleen weer gegeven als u een ondersteunde locatie selecteert. Zie [overzicht van beschikbaarheids zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor een lijst met ondersteunde locaties. Als u de **basis** -SKU hebt *geselecteerd, wordt er niet* automatisch voor u geselecteerd. Als u liever een specifieke zone wilt garanderen, kunt u een specifieke zone selecteren. Een van de keuzesen is niet zone-redundant. Als u de **Standard** -SKU hebt geselecteerd: zone-redundante wordt automatisch voor u geselecteerd en wordt het gegevenspad flexibeler voor zone storingen. Als u de voor keur geeft aan een specifieke zone, wat niet kan leiden tot een zone storing, kunt u een specifieke zone selecteren.

## <a name="view-modify-settings-for-or-delete-a-public-ip-address"></a>Een openbaar IP-adres weer geven, wijzigen of verwijderen

   - **Weer geven/vermelden**: om instellingen voor een openbaar IP-adres te controleren, met inbegrip van de SKU, de naam en eventuele toepasselijke koppelingen (bijvoorbeeld Virtual Machine NIC, Load Balancer frontend).
   - **Wijzigen**: om instellingen te wijzigen met behulp van de informatie in stap 4 van [een openbaar IP-adres maken](#create-a-public-ip-address), zoals de time-out voor inactiviteit, het label voor de DNS-naam of de toewijzings methode.  (Zie een [upgrade uitvoeren voor open bare IP-adressen van Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address-upgrade)voor het volledige proces van het upgraden van een open bare IP-SKU van Basic naar Standard.)
   >[!WARNING]
   >Als u de toewijzing voor een openbaar IP-adres van statisch naar dynamisch wilt wijzigen, moet u eerst het adres loskoppelen van de toepasselijke IP-configuraties (zie sectie **verwijderen** ).  Houd er ook rekening mee dat wanneer u de toewijzings methode van statisch naar dynamisch wijzigt, het IP-adres dat is toegewezen aan het open bare IP-adres, verloren gaat. Terwijl de Azure open bare DNS-servers een toewijzing onderhouden tussen statische of dynamische adressen en een DNS-naam label (als u deze hebt gedefinieerd), kan een dynamisch IP-adres worden gewijzigd wanneer de virtuele machine wordt gestart nadat de status gestopt (toewijzing ongedaan gemaakt) is. Wijs een statisch IP-adres toe om te voor komen dat het adres wordt gewijzigd.
   
|Bewerking|Azure Portal|Azure PowerShell|Azure CLI|
|---|---|---|---|
|Weergave | In het gedeelte **overzicht** van een openbaar IP-adres |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) om een openbaar IP-adres object op te halen en de instellingen weer te geven| [AZ Network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) to show settings|
|Lijst | Onder de categorie **open bare IP-adressen** |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) om een of meer open bare IP-adres objecten op te halen en de instellingen weer te geven|[AZ Network Public-IP List](/cli/azure/network/public-ip#az-network-public-ip-list) to list open bare IP-adressen|
|Wijzigen | Voor een IP-adres dat is ontkoppeld, selecteert u **configuratie** om de time-out voor inactiviteit, het DNS-naam label of de toewijzing van het basis-IP-adres van statisch naar dynamisch te wijzigen  |[Stel-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) in om de instellingen bij te werken |[AZ Network Public-IP update](/cli/azure/network/public-ip#az-network-public-ip-update) to update |

   - **Verwijderen**: voor het verwijderen van open bare ip's moet het open bare IP-object niet worden gekoppeld aan een IP-configuratie of een NIC van de virtuele machine. Zie de onderstaande tabel voor meer informatie.

|Resource|Azure Portal|Azure PowerShell|Azure CLI|
|---|---|---|---|
|[Virtuele machine](https://docs.microsoft.com/azure/virtual-network/remove-public-ip-address-vm)|Selecteer **loskoppelen** om het IP-adres te ontkoppelen van de NIC-configuratie en selecteer vervolgens **verwijderen**.|[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) om het IP-adres te ontkoppelen van de NIC-configuratie; [Verwijder-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) om te verwijderen|[AZ Network Public-IP update--Remove](/cli/azure/network/public-ip#az-network-public-ip-update) om het IP-adres te ontkoppelen van de NIC-configuratie; [AZ Network Public-IP delete](/cli/azure/network/public-ip#az-network-public-ip-delete) om te verwijderen |
|Load Balancer front-end | Navigeer naar een ongebruikt openbaar IP-adres en selecteer **koppelen** en kies de Load Balancer met de relevante front-end-IP-configuratie om deze te vervangen (daarna kan de oude IP worden verwijderd met dezelfde methode als voor VM)  | [Set-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig) om nieuwe frontend-IP-configuratie te koppelen aan open bare Load Balancer; [Verwijder-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) om te verwijderen; kan ook [Remove-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/remove-azloadbalancerfrontendipconfig) gebruiken om de frontend-IP-configuratie te verwijderen als er meer dan één |[AZ Network lb frontend-IP update](/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az_network_lb_frontend_ip_update) een nieuwe frontend-IP-configuratie koppelen met open bare Load Balancer; [Verwijder-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) om te verwijderen; kan ook worden gebruikt [AZ Network lb frontend-IP delete](/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az_network_lb_frontend_ip_delete) om de frontend-IP-configuratie te verwijderen als er meer dan één|
|Firewall|N.v.t.| Toewijzing ongedaan maken [()](https://docs.microsoft.com/azure/firewall/firewall-faq#how-can-i-stop-and-start-azure-firewall) voor het opheffen van de toewijzing van firewall en het verwijderen van alle IP-configuraties | [AZ Network Firewall IP-config delete](/cli/azure/ext/azure-firewall/network/firewall/ip-config#ext_azure_firewall_az_network_firewall_ip_config_delete) om IP te verwijderen (maar moet eerst Power shell gebruiken om de toewijzing op te heffen)|

## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

Wanneer u een schaalset voor virtuele machines met open bare Ip's gebruikt, zijn er geen afzonderlijke open bare IP-objecten die zijn gekoppeld aan de afzonderlijke exemplaren van de virtuele machine. Een openbaar IP-prefix object [kan echter worden gebruikt voor het genereren van de instantie-ip's](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).

Als u de open bare Ip's op een schaalset voor virtuele machines wilt weer geven, kunt u Power shell ([Get-AzPublicIpAddress-VirtualMachineScaleSetName](/powershell/module/az.network/get-azpublicipaddress)) of cli ([AZ vmss List-instance-Public-ip's](/cli/azure/vmss?view=azure-cli-latest#az_vmss_list_instance_public_ips)) gebruiken.

Zie voor meer informatie [Netwerken voor schaalsets voor virtuele Azure-machines](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking#public-ipv4-per-virtual-machine).

## <a name="assign-a-public-ip-address"></a>Een openbaar IP-adres toewijzen

Meer informatie over het toewijzen van een openbaar IP-adres aan de volgende bronnen:

- Een virtuele [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -of [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -machine (bij het maken) of een [bestaande virtuele machine](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Open bare Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Site-naar-site-verbinding met behulp van een VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Schaalset voor virtuele machines](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Machtigingen

Als u taken wilt uitvoeren op open bare IP-adressen, moet uw account worden toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rol waaraan de juiste acties in de volgende tabel zijn toegewezen:

| Bewerking                                                             | Naam                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Een openbaar IP-adres lezen                                          |
| Microsoft.Network/publicIPAddresses/write                          | Een openbaar IP-adres maken of bijwerken                           |
| Micro soft. Network/publicIPAddresses/verwijderen                         | Een openbaar IP-adres verwijderen                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Een openbaar IP-adres aan een resource koppelen                    |

## <a name="next-steps"></a>Volgende stappen

- Een openbaar IP-adres maken met behulp van [Power shell](powershell-samples.md) of [Azure cli](cli-samples.md) -voorbeeld scripts of met behulp van Azure [Resource Manager-sjablonen](template-samples.md)
- [Azure Policy definities](policy-samples.md) voor open bare IP-adressen maken en toewijzen
