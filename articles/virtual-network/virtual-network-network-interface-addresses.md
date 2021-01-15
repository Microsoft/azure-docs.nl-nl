---
title: IP-adressen configureren voor een Azure-netwerk interface | Microsoft Docs
description: Meer informatie over het toevoegen, wijzigen en verwijderen van persoonlijke en open bare IP-adressen voor een netwerk interface.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: allensu
ms.openlocfilehash: 3fd0cfe644ad78059e25d5386cd1a01f56ad9fba
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216985"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>IP-adressen voor Azure-netwerkinterfaces toevoegen, wijzigen en verwijderen

Meer informatie over het toevoegen, wijzigen en verwijderen van open bare en privé-IP-adressen voor een netwerk interface. Met persoonlijke IP-adressen die zijn toegewezen aan een netwerk interface kan een virtuele machine communiceren met andere resources in een virtueel Azure-netwerk en verbonden netwerken. Een privé-IP-adres schakelt ook uitgaande communicatie met internet in met behulp van een onvoorspelbaar IP-adres. Een [openbaar IP-adres](virtual-network-public-ip-address.md) dat is toegewezen aan een netwerk interface, maakt inkomende communicatie via internet mogelijk voor een virtuele machine. Het adres schakelt ook uitgaande communicatie van de virtuele machine naar Internet met behulp van een voorspelbaar IP-adres. Zie voor meer informatie [over uitgaande verbindingen in azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Als u een netwerk interface moet maken, wijzigen of verwijderen, raadpleegt u het artikel [een netwerk interface beheren](virtual-network-network-interface.md) . Als u netwerk interfaces wilt toevoegen aan of verwijderen uit een virtuele machine, leest u het artikel [netwerk interfaces toevoegen of verwijderen](virtual-network-network-interface-vm.md) .

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voer de volgende taken uit voordat u de stappen in een van de secties van dit artikel uitvoert:

- Als u nog geen Azure-account hebt, kunt u zich aanmelden voor een [gratis proef account](https://azure.microsoft.com/free).
- Als u de portal gebruikt, opent https://portal.azure.com u en meldt u zich aan met uw Azure-account.
- Als u Power shell-opdrachten gebruikt om taken in dit artikel te volt ooien, moet u de opdrachten uitvoeren in de [Azure Cloud shell](https://shell.azure.com/powershell)of Power shell uitvoeren vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Voor deze zelf studie is de Azure PowerShell module versie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.
- Als u Azure-opdracht regel interface opdrachten gebruikt om taken in dit artikel te volt ooien, moet u de opdrachten uitvoeren in de [Azure Cloud shell](https://shell.azure.com/bash)of door de CLI vanaf uw computer uit te voeren. Voor deze zelf studie is de Azure CLI-versie 2.0.31 of hoger vereist. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Als u de Azure CLI lokaal uitvoert, moet u ook uitvoeren `az login` om een verbinding te maken met Azure.

Het account waarmee u zich aanmeldt of verbinding maakt met Azure met, moet worden toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in de [netwerk interface machtigingen](virtual-network-network-interface.md#permissions).

## <a name="add-ip-addresses"></a>IP-adressen toevoegen

U kunt zo nodig zoveel [persoonlijke](#private) en [open bare](#public) [IPv4](#ipv4) -adressen toevoegen aan een netwerk interface, binnen de limieten die worden vermeld in het artikel [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) . U kunt een privé-IPv6-adres toevoegen aan één [secundaire IP-configuratie](#secondary) (zolang er geen bestaande secundaire IP-configuraties zijn) voor een bestaande netwerk interface. Elke netwerk interface kan Maxi maal één persoonlijk IPv6-adres hebben. U kunt eventueel een openbaar IPv6-adres toevoegen aan een configuratie van een IPv6-netwerk interface. Zie [IPv6](#ipv6) voor meer informatie over het gebruik van IPv6-adressen.

1. In het vak met de tekst *zoeken resources* boven aan de Azure Portal, typt u *netwerk interfaces*. Wanneer de **netwerk interfaces** in de zoek resultaten worden weer gegeven, selecteert u deze.
2. Selecteer de netwerk interface waarvoor u een IPv4-adres wilt toevoegen in de lijst.
3. Selecteer onder **instellingen** de optie **IP-configuraties**.
4. Selecteer **+ toevoegen** onder **IP-configuraties**.
5. Geef het volgende op en selecteer **OK**:

   |Instelling|Vereist?|Details|
   |---|---|---|
   |Naam|Yes|Moet uniek zijn voor de netwerk interface|
   |Type|Yes|Omdat u een IP-configuratie toevoegt aan een bestaande netwerk interface en elke netwerk interface moet een [primaire](#primary) IP-configuratie hebben, is de enige optie **secundair**.|
   |Toewijzings methode voor privé-IP-adres|Yes|[**Dynamisch**](#dynamic): Azure wijst het volgende beschik bare adres toe voor het adres bereik van het subnet waarin de netwerk interface is geïmplementeerd. [**Statisch**](#static): u wijst een ongebruikt adres toe voor het adres bereik van het subnet waarin de netwerk interface is geïmplementeerd.|
   |Openbaar IP-adres|No|**Uitgeschakeld:** Er is momenteel geen open bare IP-adres resource gekoppeld aan de IP-configuratie. **Ingeschakeld:** Selecteer een bestaand openbaar IP-adres voor IPv4 of maak een nieuwe. Lees het artikel [open bare IP-adressen](virtual-network-public-ip-address.md#create-a-public-ip-address) voor meer informatie over het maken van een openbaar IP-adres.|
6. Voeg hand matig secundaire privé-IP-adressen toe aan het besturings systeem van de virtuele machine door de instructies in het artikel [meerdere IP-adressen toewijzen aan virtuele machines te](virtual-network-multiple-ip-addresses-portal.md#os-config) volt ooien. Zie [privé](#private) -IP-adressen voor speciale overwegingen voordat u IP-adressen hand matig toevoegt aan een besturings systeem van een virtuele machine. Voeg geen open bare IP-adressen toe aan het besturings systeem van de virtuele machine.

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config)|
|PowerShell|[Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>Instellingen voor IP-adressen wijzigen

Mogelijk moet u de toewijzings methode van een IPv4-adres wijzigen, het statische IPv4-adres wijzigen of het open bare IP-adres wijzigen dat is toegewezen aan een netwerk interface. Als u het privé-IPv4-adres van een secundaire IP-configuratie wijzigt die is gekoppeld aan een secundaire netwerk interface in een virtuele machine (meer informatie over de [primaire en secundaire netwerk interfaces](virtual-network-network-interface-vm.md)), plaatst u de virtuele machine in de status gestopt (toewijzing opgeheven) voordat u de volgende stappen uitvoert:

1. In het vak met de tekst *zoeken resources* boven aan de Azure Portal, typt u *netwerk interfaces*. Wanneer de **netwerk interfaces** in de zoek resultaten worden weer gegeven, selecteert u deze.
2. Selecteer de netwerk interface waarvan u de IP-adres instellingen wilt weer geven of wijzigen in de lijst.
3. Selecteer onder **instellingen** de optie **IP-configuraties**.
4. Selecteer de IP-configuratie die u wilt wijzigen in de lijst.
5. Wijzig de instellingen, naar wens, met behulp van de informatie over de instellingen in stap 5 van [een IP-configuratie toevoegen](#add-ip-addresses).
6. Selecteer **Opslaan**.

>[!NOTE]
>Als de primaire netwerk interface meerdere IP-configuraties heeft en u het privé-IP-adres van de primaire IP-configuratie wijzigt, moet u de primaire en secundaire IP-adressen hand matig opnieuw toewijzen aan de netwerk interface in Windows (niet vereist voor Linux). Zie [meerdere IP-adressen toewijzen aan virtuele machines](virtual-network-multiple-ip-addresses-portal.md#os-config)om hand matig IP-adressen toe te wijzen aan een netwerk interface binnen een besturings systeem. Zie [privé](#private) IP-adressen voor speciale overwegingen voordat u IP-adressen hand matig toevoegt aan een besturings systeem voor een virtuele machine. Voeg geen open bare IP-adressen toe aan het besturings systeem van de virtuele machine.

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[AZ Network NIC IP-config update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>IP-adressen verwijderen

U kunt [persoonlijke](#private) en [open bare](#public) IP-adressen verwijderen van een netwerk interface, maar aan een netwerk interface moet altijd ten minste één persoonlijk IPv4-adres zijn toegewezen.

1. In het vak met de tekst *zoeken resources* boven aan de Azure Portal, typt u *netwerk interfaces*. Wanneer de **netwerk interfaces** in de zoek resultaten worden weer gegeven, selecteert u deze.
2. Selecteer de netwerk interface waarvan u de IP-adressen uit de lijst wilt verwijderen.
3. Selecteer onder **instellingen** de optie **IP-configuraties**.
4. Klik met de rechter muisknop op een [secundaire](#secondary) IP-configuratie (u kunt de [primaire](#primary) configuratie niet verwijderen) die u wilt verwijderen, selecteer **verwijderen** en selecteer vervolgens **Ja** om de verwijdering te bevestigen. Als aan de configuratie een resource met een openbaar IP-adres is gekoppeld, wordt de bron losgekoppeld van de IP-configuratie, maar de bron wordt niet verwijderd.

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[AZ Network NIC IP-config Delete](/cli/azure/network/nic/ip-config)|
|PowerShell|[Remove-AzNetworkInterfaceIpConfig](/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>IP-configuraties

[Persoonlijke](#private) en (optioneel) [open bare](#public) IP-adressen worden toegewezen aan een of meer IP-configuraties die zijn toegewezen aan een netwerk interface. Er zijn twee soorten IP-configuraties:

### <a name="primary"></a>Primair

Aan elke netwerk interface wordt één primaire IP-configuratie toegewezen. Een primaire IP-configuratie:

- Een [persoonlijk](#private) [IPv4](#ipv4) -adres is toegewezen. U kunt een privé [-IPv6-](#ipv6) adres niet toewijzen aan een primaire IP-configuratie.
- Er kan ook een [openbaar](#public) IPv4-adres aan zijn toegewezen. U kunt een openbaar IPv6-adres niet toewijzen aan een primaire (IPv4) IP-configuratie. 

### <a name="secondary"></a>Secundair

Naast een primaire IP-configuratie kan er aan een netwerk interface geen of meer secundaire IP-configuraties worden toegewezen. Een secundaire IP-configuratie:

- Er moet een persoonlijk IPv4-of IPv6-adres zijn toegewezen. Als het adres IPv6 is, kan de netwerk interface slechts één secundaire IP-configuratie hebben. Als het adres IPv4 is, kunnen er meerdere secundaire IP-configuraties aan de netwerk interface worden toegewezen. Zie het artikel [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor meer informatie over het aantal persoonlijke en open bare IPv4-adressen dat kan worden toegewezen aan een netwerk interface.
- Er kan ook een openbaar IPv4-of IPv6-adres aan zijn toegewezen. Het toewijzen van meerdere IPv4-adressen aan een netwerk interface is handig in scenario's zoals:
  - Meerdere websites of services hosten met verschillende IP-adressen en TLS/SSL-certificaten op één server.
  - Een virtuele machine die fungeert als een virtueel netwerk apparaat, zoals een firewall of load balancer.
  - De mogelijkheid om een persoonlijk IPv4-adres toe te voegen voor een van de netwerk interfaces aan een Azure Load Balancer back-end-pool. In het verleden kon alleen het primaire IPv4-adres voor de primaire netwerk interface worden toegevoegd aan een back-end-pool. Zie het artikel [taak verdeling meerdere IP-configuraties](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie over het verdelen van meerdere IPv4-configuraties. 
  - De mogelijkheid om taken te verdelen over één IPv6-adres dat is toegewezen aan een netwerk interface. Zie het artikel [taak verdeling van IPv6-adressen](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie over het verdelen van taken naar een privé-IPv6-adres.

## <a name="address-types"></a>Adres typen

U kunt de volgende typen IP-adressen toewijzen aan een [IP-configuratie](#ip-configurations):

### <a name="private"></a>Persoonlijk

Met een particulier [IPv4](#ipv4) -of IPv6-adres kunnen virtuele machines communiceren met andere resources in een virtueel netwerk of andere verbonden netwerken. 

De Azure DHCP-servers wijzen standaard het privé-IPv4-adres voor de [primaire IP-configuratie](#primary) van de Azure-netwerk interface toe aan de netwerk interface binnen het besturings systeem van de virtuele machine. Tenzij nodig moet u het IP-adres van een netwerk interface in het besturings systeem van de virtuele machine nooit hand matig instellen.

> [!WARNING]
> Als het IPv4-adres dat is ingesteld als het primaire IP-adres van een netwerk interface binnen het besturings systeem van een virtuele machine, verschilt van het privé-IPv4-adres dat is toegewezen aan de primaire IP-configuratie van de primaire netwerk interface die is gekoppeld aan een virtuele machine in azure, verliest u de verbinding met de virtuele machine.

Er zijn scenario's waarin het nodig is om het IP-adres van een netwerk interface hand matig in te stellen binnen het besturings systeem van de virtuele machine. U moet de primaire en secundaire IP-adressen van een Windows-besturings systeem bijvoorbeeld hand matig instellen wanneer u meerdere IP-adressen toevoegt aan een virtuele Azure-machine. Voor een virtuele Linux-machine hoeft u de secundaire IP-adressen mogelijk alleen hand matig in te stellen. Zie [IP-adressen toevoegen aan een VM-besturings systeem](virtual-network-multiple-ip-addresses-portal.md#os-config) voor meer informatie. Als u ooit het adres moet wijzigen dat aan een IP-configuratie is toegewezen, kunt u het beste het volgende doen:

1. Zorg ervoor dat de virtuele machine een adres ontvangt van de Azure DHCP-servers. Zodra u dat hebt, wijzigt u de toewijzing van het IP-adres weer in DHCP in het besturings systeem en start u de virtuele machine opnieuw op.
2. Stop de virtuele machine (hef de toewijzing op).
3. Wijzig het IP-adres voor de IP-configuratie in Azure.
4. Start de virtuele machine.
5. Configureer de secundaire IP-adressen binnen het besturings systeem (en ook het primaire IP-adres in Windows) [hand matig](virtual-network-multiple-ip-addresses-portal.md#os-config) om te voldoen aan wat u in azure hebt ingesteld.

Door de vorige stappen te volgen, blijven het privé-IP-adres dat is toegewezen aan de netwerk interface binnen Azure en binnen het besturings systeem van een virtuele machine, hetzelfde. Als u wilt bijhouden welke virtuele machines in uw abonnement u hand matig IP-adressen hebt ingesteld binnen een besturings systeem voor, kunt u een Azure- [tag](../azure-resource-manager/management/tag-resources.md) toevoegen aan de virtuele machines. U kunt bijvoorbeeld ' IP-adres toewijzing: static ' gebruiken. Op deze manier kunt u eenvoudig de virtuele machines in uw abonnement vinden waarvoor u het IP-adres hand matig hebt ingesteld in het besturings systeem.

Naast het inschakelen van een virtuele machine om te communiceren met andere resources binnen dezelfde of verbonden virtuele netwerken, kan een persoonlijk IP-adres ook een virtuele machine in staat stellen om uitgaande berichten te verzenden naar het internet. Uitgaande verbindingen zijn bron netwerk adres vertaald door Azure naar een onvoorspelbaar openbaar IP-adres. Lees het artikel [Azure uitgaande internet connectiviteit](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie over uitgaande internet connectiviteit van Azure. U kunt geen inkomende communicatie met het privé-IP-adres van een virtuele machine via internet. Als voor uw uitgaande verbindingen een voorspelbaar openbaar IP-adres is vereist, koppelt u een open bare IP-adres resource aan een netwerk interface.

### <a name="public"></a>Openbaar

Open bare IP-adressen die zijn toegewezen via een open bare IP-adres resource, kunnen binnenkomende verbindingen met een virtuele machine via internet. Uitgaande verbindingen met internet maken gebruik van een voorspelbaar IP-adres. Zie informatie [over uitgaande verbindingen in azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie. U kunt een openbaar IP-adres toewijzen aan een IP-configuratie, maar dit is niet vereist voor. Als u geen openbaar IP-adres toewijst aan een virtuele machine door een open bare IP-adres resource te koppelen, kan de virtuele machine nog steeds uitgaande berichten verzenden naar het internet. In dit geval is het particuliere IP-adres het bron netwerk adres dat door Azure is vertaald naar een onvoorspelbaar openbaar IP-adres. Zie [open bare IP-adres](virtual-network-public-ip-address.md)bronnen voor meer informatie over open bare IP-adressen.

Er zijn limieten voor het aantal privé-en open bare IP-adressen dat u kunt toewijzen aan een netwerk interface. Lees het artikel Azure- [limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor meer informatie.

> [!NOTE]
> Azure vertaalt het privé IP-adres van een virtuele machine naar een openbaar IP-adres. Als gevolg hiervan is het besturings systeem van de virtuele machine niet op de hoogte van een openbaar IP-adres dat eraan is toegewezen. u hoeft dus nooit hand matig een openbaar IP-adres toe te wijzen binnen het besturings systeem.

## <a name="assignment-methods"></a>Toewijzings methoden

Open bare en privé-IP-adressen worden toegewezen aan de hand van een van de volgende toewijzings methoden:

### <a name="dynamic"></a>Dynamisch

Dynamische persoonlijke IPv4-en IPv6-adressen (optioneel) worden standaard toegewezen.

- **Alleen openbaar**: Azure wijst het adres toe uit een bereik dat uniek is voor elke Azure-regio. Zie [Microsoft Azure Data Center IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653)voor meer informatie over welke bereiken aan elke regio worden toegewezen. Het adres kan worden gewijzigd wanneer een virtuele machine wordt gestopt (toewijzing ongedaan gemaakt) en opnieuw wordt gestart. U kunt een openbaar IPv6-adres niet toewijzen aan een IP-configuratie met behulp van een van beide toewijzings methoden.
- **Alleen persoonlijk**: Azure reserveert de eerste vier adressen in elk adres bereik van het subnet en wijst de adressen niet toe. Azure wijst het eerstvolgende beschikbare adres in het adresbereik van het subnet toe aan een resource. Als het adres bereik van het subnet bijvoorbeeld 10.0.0.0/16 is en adressen 10.0.0.4-10.0.0.14 al zijn toegewezen (. 0-.3 zijn gereserveerd), wijst Azure 10.0.0.15 toe aan de resource. Dynamisch is de standaardmethode voor toewijzing. Nadat dynamische IP-adressen zijn toegewezen, worden ze alleen vrijgegeven als een netwerkinterface wordt verwijderd of wordt toegewezen aan een ander subnet binnen hetzelfde virtuele netwerk of als de toewijzingsmethode wordt gewijzigd in statisch en een ander IP-adres wordt opgegeven. Standaard wijst Azure het vorige dynamisch toegewezen adres toe als het statische adres wanneer u de toewijzingsmethode van dynamisch wijzigt in statisch. 

### <a name="static"></a>Statisch

U kunt desgewenst een openbaar of privé statisch IPv4-of IPv6-adres toewijzen aan een IP-configuratie. Zie [open bare IP-adressen](virtual-network-public-ip-address.md)voor meer informatie over hoe Azure statische open bare IPv4-adressen toewijst.

- **Alleen openbaar**: Azure wijst het adres toe uit een bereik dat uniek is voor elke Azure-regio. U kunt de lijst met bereiken (voorvoegsels) downloaden voor de Azure-clouds [Openbaar](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) en [Duitsland](https://www.microsoft.com/download/details.aspx?id=57064). Het adres wordt niet gewijzigd totdat de resource van het open bare IP-adres waaraan deze is toegewezen, wordt verwijderd of de toewijzings methode wordt gewijzigd in dynamisch. Als de bron van het open bare IP-adres is gekoppeld aan een IP-configuratie, moet deze worden ontkoppeld van de IP-configuratie voordat de toewijzings methode wordt gewijzigd.
- **Alleen persoonlijk**: u selecteert een adres uit het adres bereik van het subnet en wijst dit toe. Het adres dat u toewijst, kan elk adres binnen het adresbereik van het subnet zijn dat niet een van de eerste vier adressen in het adresbereik van het subnet is en momenteel niet is toegewezen aan een andere resource in het subnet. Statische adressen worden alleen vrijgegeven als een netwerkinterface wordt verwijderd. Als u de toewijzings methode wijzigt in statisch, wijst Azure het eerder toegewezen dynamische IP-adres dynamisch toe als het statische adres, zelfs als het adres niet het volgende beschik bare adres in het adres bereik van het subnet is. Het adres verandert ook als de netwerkinterface wordt toegewezen aan een ander subnet binnen hetzelfde virtuele netwerk, maar als u de netwerkinterface wilt toewijzen aan een ander subnet, moet u eerst de toewijzingsmethode wijzigen van statisch in dynamisch. Nadat u de netwerkinterface hebt toegewezen aan een ander subnet, kunt u de toewijzingsmethode weer wijzigen in statisch en een IP-adres uit het adresbereik van het nieuwe subnet toewijzen.

## <a name="ip-address-versions"></a>Versies van IP-adressen

U kunt de volgende versies opgeven wanneer u adressen toewijst:

### <a name="ipv4"></a>IPv4

Elke netwerk interface moet één [primaire](#primary) IP-configuratie met een toegewezen [particulier](#private) [IPv4](#ipv4) -adres hebben. U kunt een of meer [secundaire](#secondary) IP-configuraties toevoegen die elk een IPv4-particulier en (optioneel) een [openbaar](#public) IP-adres van IPv4 hebben.

### <a name="ipv6"></a>IPv6

U kunt nul of één particulier [IPv6-](#ipv6) adres toewijzen aan één secundaire IP-configuratie van een netwerk interface. De netwerk interface kan geen bestaande secundaire IP-configuraties hebben. Elke netwerk interface kan Maxi maal één persoonlijk IPv6-adres hebben. U kunt eventueel een openbaar IPv6-adres toevoegen aan een configuratie van een IPv6-netwerk interface.

> [!NOTE]
> Hoewel u een netwerk interface met een IPv6-adres kunt maken met behulp van de portal, kunt u geen bestaande netwerk interface toevoegen aan een nieuwe of bestaande virtuele machine met behulp van de portal. Gebruik Power shell of de Azure CLI om een netwerk interface te maken met een privé-IPv6-adres en koppel vervolgens de netwerk interface bij het maken van een virtuele machine. U kunt geen netwerk interface koppelen aan een persoonlijk IPv6-adres dat aan een bestaande virtuele machine is toegewezen. U kunt geen privé-IPv6-adres toevoegen aan een IP-configuratie voor een netwerk interface die is gekoppeld aan een virtuele machine met behulp van alle hulpprogram ma's (Portal, CLI of Power shell).

U kunt een openbaar IPv6-adres niet toewijzen aan een primaire of secundaire IP-configuratie.

## <a name="skus"></a>SKU's

Een openbaar IP-adres wordt gemaakt met de Basic-of Standard-SKU. Zie [Public IP addresses beheren](virtual-network-public-ip-address.md)voor meer informatie over de verschillen tussen de verschillende sku's.

> [!NOTE]
> Als u een openbaar IP-adres van een standaard-SKU toewijst aan een netwerkinterface van een virtuele machine, moet u het bedoelde verkeer expliciet toestaan met een [netwerkbeveiligingsgroep](./network-security-groups-overview.md#network-security-groups). Communicatie met de resource mislukt totdat u een netwerkbeveiligingsgroep maakt en koppelt en het gewenste verkeer expliciet toestaat.

## <a name="next-steps"></a>Volgende stappen
Als u een virtuele machine met verschillende IP-configuraties wilt maken, leest u de volgende artikelen:

|Taak|Hulpprogramma|
|---|---|
|Een virtuele machine met meerdere netwerkinterfaces maken|[Cli](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Power shell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Eén NIC-VM met meerdere IPv4-adressen maken|[Cli](virtual-network-multiple-ip-addresses-cli.md), [Power shell](virtual-network-multiple-ip-addresses-powershell.md)|
|Een enkele NIC-VM maken met een privé-IPv6-adres (achter een Azure Load Balancer)|[Cli](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [power shell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-sjabloon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|