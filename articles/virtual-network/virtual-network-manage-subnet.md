---
title: Een subnet van een virtueel Azure-netwerk toevoegen, wijzigen of verwijderen
titlesuffix: Azure Virtual Network
description: Ontdek waar u informatie vindt over virtuele netwerken en hoe u een subnet van een virtueel netwerk toevoegt, wijzigt of verwijdert in Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2020
ms.author: kumud
ms.openlocfilehash: 1e655b20d2f6295f0d6cfe8008fee7b360525611
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774283"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Een subnet van een virtueel netwerk maken, wijzigen of verwijderen

Meer informatie over het toevoegen, wijzigen of verwijderen van een subnet van een virtueel netwerk. Alle Azure-resources die worden geïmplementeerd in een virtueel netwerk worden geïmplementeerd in een subnet binnen het virtuele netwerk. Als u geen kennis hebt met virtuele netwerken, [](virtual-networks-overview.md) kunt u hier meer over te weten komen in het Overzicht van virtueel netwerk of door een [quickstart te voltooien.](quick-create-portal.md) Zie Een virtueel netwerk maken, wijzigen of verwijderen voor meer informatie over het beheren [van een virtueel netwerk.](manage-virtual-network.md)

## <a name="before-you-begin"></a>Voordat u begint

Als u nog geen abonnement hebt, stelt u een Azure-account in met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Voltooi vervolgens een van deze taken voordat u de stappen in een van de secties van dit artikel start: 

- **Portalgebruikers:** meld u aan bij [de Azure Portal](https://portal.azure.com) met uw Azure-account.

- **PowerShell-gebruikers:** voer de opdrachten uit in [de Azure Cloud Shell](https://shell.azure.com/powershell)of voer PowerShell uit vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Zoek op Azure Cloud Shell browsertabblad  de vervolgkeuzelijst Omgeving selecteren en kies **vervolgens PowerShell** als dit nog niet is geselecteerd.

    Als u PowerShell lokaal gebruikt, gebruikt u Azure PowerShell moduleversie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az.Network` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Voer ook `Connect-AzAccount` uit om een verbinding met Azure te maken.

- **Azure CLI-gebruikers (opdrachtregelinterface)**: voer de opdrachten uit in de Azure Cloud Shell [of](https://shell.azure.com/bash)voer de CLI uit vanaf uw computer. Gebruik Azure CLI versie 2.0.31 of hoger als u de Azure CLI lokaal gebruikt. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Voer ook `az login` uit om een verbinding met Azure te maken.

Het account waar u zich bij aan- of verbinding [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) maakt met Azure, [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) moet worden toegewezen aan de rol Netwerkbijdrager of aan een Aangepaste rol die wordt toegewezen aan de juiste acties die worden vermeld in [Machtigingen](#permissions).

## <a name="add-a-subnet"></a>Een subnet toevoegen

1. Ga naar de [Azure Portal](https://portal.azure.com) virtuele netwerken weer te bieden. Zoek en selecteer **Virtuele netwerken**.

2. Selecteer de naam van het virtuele netwerk waar u een subnet aan wilt toevoegen.

3. Selecteer **subnetten** **subnet bij**  >  **Instellingen.**

4. Voer in **het dialoogvenster Subnet** toevoegen waarden in voor de volgende instellingen:

    | Instelling | Beschrijving |
    | --- | --- |
    | **Naam** | De naam moet uniek zijn binnen het virtuele netwerk. Voor maximale compatibiliteit met andere Azure-services raden we u aan een letter als eerste teken van de naam te gebruiken. Een Azure Application Gateway bijvoorbeeld niet implementeren in een subnet met een naam die begint met een getal. |
    | **Adresbereik** | <p>Het bereik moet uniek zijn binnen de adresruimte voor het virtuele netwerk. Het bereik mag niet overlappen met andere subnetadresbereiken binnen het virtuele netwerk. De adresruimte moet worden opgegeven met behulp van de CIDR-notatie (Classless Inter-Domain Routing).</p><p>In een virtueel netwerk met adresruimte *10.0.0.0/16* kunt u bijvoorbeeld een subnetadresruimte *van 10.0.0.0/22 definiëren.* Het kleinste bereik dat u kunt opgeven is */29,* dat acht IP-adressen voor het subnet biedt. Azure reserveert het eerste en laatste adres in elk subnet voor protocolconformie. Er zijn drie extra adressen gereserveerd voor het gebruik van de Azure-service. Als gevolg hiervan resulteert het definiëren van een subnet met *een /29-adresbereik* in drie bruikbaar IP-adressen in het subnet.</p><p>Als u van plan bent om een virtueel netwerk te verbinden met een VPN-gateway, moet u een gatewaysubnet maken. Meer informatie over [specifieke overwegingen voor adresbereiken voor gatewaysubnetten.](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) U kunt het adresbereik wijzigen nadat het subnet is toegevoegd, onder specifieke voorwaarden. Zie Subnetinstellingen wijzigen voor meer informatie over het wijzigen van een [subnetadresbereik.](#change-subnet-settings)</p> |
    | **Netwerkbeveiligingsgroep** | Als u het binnenkomende en uitgaande netwerkverkeer voor het subnet wilt filteren, kunt u een bestaande netwerkbeveiligingsgroep aan een subnet koppelen. De netwerkbeveiligingsgroep moet zich in hetzelfde abonnement en dezelfde locatie bevinden als het virtuele netwerk. Meer informatie over [netwerkbeveiligingsgroepen](./network-security-groups-overview.md) en [het maken van een netwerkbeveiligingsgroep.](tutorial-filter-network-traffic.md) |
    | **Routetabel** | Als u de routering van netwerkverkeer naar andere netwerken wilt controleren, kunt u eventueel een bestaande routetabel aan een subnet koppelen. De routetabel moet zich in hetzelfde abonnement en dezelfde locatie bevinden als het virtuele netwerk. Meer informatie over [Azure-routering](virtual-networks-udr-overview.md) [en het maken van een routetabel.](tutorial-create-route-table-portal.md) |
    | **Service-eindpunten** | <p>Voor een subnet kunnen eventueel een of meer service-eindpunten zijn ingeschakeld. Als u een service-eindpunt voor een service wilt inschakelen, selecteert u de service of services voor wie u service-eindpunten wilt inschakelen in de **lijst** Services. Azure configureert de locatie automatisch voor een eindpunt. Azure configureert standaard de service-eindpunten voor de regio van het virtuele netwerk. Ter ondersteuning van regionale failoverscenario's configureert Azure automatisch eindpunten naar [gekoppelde Azure-regio's](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) voor Azure Storage.</p><p>Als u een service-eindpunt wilt verwijderen, verwijdert u de selectie van de service voor wie u het service-eindpunt wilt verwijderen. Zie Service-eindpunten voor virtueel netwerk voor meer informatie over service-eindpunten en de services waar ze voor [kunnen worden ingeschakeld.](virtual-network-service-endpoints-overview.md) Zodra u een service-eindpunt voor een service hebt ingeschakeld, moet u ook netwerktoegang inschakelen voor het subnet voor een resource die met de service is gemaakt. Als u bijvoorbeeld het service-eindpunt voor **Microsoft.Storage** inschakelen, moet u ook netwerktoegang inschakelen voor alle Azure Storage-accounts die u netwerktoegang wilt verlenen. Als u netwerktoegang wilt inschakelen tot subnetten voor een service-eindpunt, bekijkt u de documentatie voor de afzonderlijke service voor wie u het service-eindpunt hebt ingeschakeld.</p><p>Als u wilt controleren of een service-eindpunt is ingeschakeld voor een subnet, bekijkt u de [effectieve routes](diagnose-network-routing-problem.md) voor elke netwerkinterface in het subnet. Wanneer u een eindpunt configureert, ziet u een standaardroute met de adresvoorvoegsels van de service en een volgend hoptype van **VirtualNetworkServiceEndpoint.**  Zie Routering van virtueel netwerkverkeer voor meer informatie [over routering.](virtual-networks-udr-overview.md)</p> |
    | **Delegatie van subnet** | Voor een subnet kunnen eventueel een of meer delegeringen zijn ingeschakeld. Delegering van subnetten geeft de service expliciete machtigingen om servicespecifieke resources in het subnet te maken met behulp van een unieke id tijdens de implementatie van de service. Als u een service wilt delegeren, selecteert u de service die u wilt delegeren in de **lijst** Services. |

5. Selecteer OK om het subnet toe te voegen aan het virtuele netwerk dat u hebt **geselecteerd.**

### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>Subnetinstellingen wijzigen

1. Ga naar de [Azure Portal](https://portal.azure.com) virtuele netwerken weer te bieden. Zoek en selecteer **Virtuele netwerken**.

2. Selecteer de naam van het virtuele netwerk met het subnet dat u wilt wijzigen.

3. Selecteer **in Instellingen** de optie **Subnetten.**

4. Selecteer in de lijst met subnetten het subnet voor wie u de instellingen wilt wijzigen.

5. Wijzig een van de volgende instellingen op de subnetpagina:

    | Instelling | Beschrijving |
    | --- | --- |
    | **Adresbereik** | Als er geen resources in het subnet zijn geïmplementeerd, kunt u het adresbereik wijzigen. Als er resources in het subnet bestaan, moet u de resources naar een ander subnet verplaatsen of ze eerst verwijderen uit het subnet. Welke stappen u moet nemen om een resource te verplaatsen of te verwijderen, is afhankelijk van de resource. Lees de documentatie voor elk van deze resourcetypen voor meer informatie over het verplaatsen of verwijderen van resources in subnetten. Zie de beperkingen voor **Adresbereik** in stap 4 van [Een subnet toevoegen.](#add-a-subnet) |
    | **Gebruikers** | U kunt de toegang tot het subnet bepalen met behulp van ingebouwde rollen of uw eigen aangepaste rollen. Zie Azure-rollen toewijzen voor meer informatie over het toewijzen van rollen en gebruikers voor toegang [tot het subnet.](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |
    | **Netwerkbeveiligingsgroep** en **Routetabel** | Zie stap 4 van [Een subnet toevoegen.](#add-a-subnet) |
    | **Service-eindpunten** | <p>Zie service-eindpunten in stap 4 van [Een subnet toevoegen.](#add-a-subnet) Wanneer u een service-eindpunt inschakelen voor een bestaand subnet, zorg ervoor dat er geen kritieke taken worden uitgevoerd op een resource in het subnet. Service-eindpunten schakelen tussen routes op elke netwerkinterface in het subnet. De service-eindpunten gaan van het gebruik van de standaardroute met het adresvoorvoegsel *0.0.0.0/0* en het volgende hoptype *van Internet* naar het gebruik van een nieuwe route met de adresvoorvoegsels van de service en een volgend hoptype van *VirtualNetworkServiceEndpoint.*</p><p>Tijdens de switch kunnen alle geopende TCP-verbindingen worden beëindigd. Het service-eindpunt wordt pas ingeschakeld als het verkeer naar de service voor alle netwerkinterfaces is bijgewerkt met de nieuwe route. Zie Routering van virtueel netwerkverkeer voor meer informatie [over routering.](virtual-networks-udr-overview.md)</p> |
    | **Delegatie van subnet** | Zie service-eindpunten in stap 4 van [Een subnet toevoegen.](#add-a-subnet) Subnetdelegering kan worden gewijzigd in nul of meerdere overdrachten ingeschakeld. Als een resource voor een service al is geïmplementeerd in het subnet, kan delegering van het subnet niet worden toegevoegd of verwijderd totdat alle resources voor de service zijn verwijderd. Als u wilt delegeren voor een andere service, selecteert u de service die u wilt delegeren in de **lijst** Services. |

6. Selecteer **Opslaan**.

### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>Een subnet verwijderen

U kunt een subnet alleen verwijderen als er geen resources in het subnet zijn. Als er resources in het subnet zijn, moet u deze resources verwijderen voordat u het subnet kunt verwijderen. Welke stappen u moet ondernemen om een resource te verwijderen, is afhankelijk van de resource. Lees de documentatie voor elk van deze resourcetypen voor meer informatie over het verwijderen van resources in subnetten.

1. Ga naar de [Azure Portal](https://portal.azure.com) virtuele netwerken weer te bieden. Zoek en selecteer **Virtuele netwerken**.

2. Selecteer de naam van het virtuele netwerk met het subnet dat u wilt verwijderen.

3. Selecteer **in** Instellingen **de optie Subnetten.**

4. Selecteer in de lijst met subnetten het subnet dat u wilt verwijderen.

5. Selecteer **Verwijderen** en selecteer vervolgens **Ja** in het bevestigingsvenster.

### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network vnet subnet delete](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_delete) |
| PowerShell | [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>Machtigingen

Als u taken wilt uitvoeren op subnetten, [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) moet uw account [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zijn toegewezen aan de rol Netwerkbijdrager of aan een Aangepaste rol die de juiste acties in de volgende tabel heeft toegewezen:

|Bewerking                                                                   |   Name                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Een subnet van een virtueel netwerk lezen              |
|Microsoft.Network/virtualNetworks/subnetten/write                          |   Een subnet van een virtueel netwerk maken of bijwerken  |
|Microsoft.Network/virtualNetworks/subnetten/delete                         |   Een subnet van een virtueel netwerk verwijderen            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Deelnemen aan een virtueel netwerk                     |
|Microsoft.Network/virtualNetworks/subnetten/joinViaServiceEndpoint/action  |   Een service-eindpunt inschakelen voor een subnet     |
|Microsoft.Network/virtualNetworks/subnetten/virtualMachines/read           |   De virtuele machines in een subnet op te halen       |

## <a name="next-steps"></a>Volgende stappen

- Een virtueel netwerk en subnetten maken met behulp van [PowerShell-](powershell-samples.md) of Azure CLI-voorbeeldscripts, of met behulp van Azure [](cli-samples.md) [Resource Manager sjablonen](template-samples.md)
- Definities voor [virtuele Azure Policy maken](./policy-reference.md) en toewijzen