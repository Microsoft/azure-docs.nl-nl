---
title: Een subnet van een virtueel netwerk van Azure toevoegen, wijzigen of verwijderen
titlesuffix: Azure Virtual Network
description: Informatie over het zoeken naar informatie over virtuele netwerken en het toevoegen, wijzigen of verwijderen van een subnet van een virtueel netwerk in Azure.
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
ms.openlocfilehash: 15fe5d6d16948875253d65e70d9d440214a4a2e8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995655"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Een subnet van een virtueel netwerk maken, wijzigen of verwijderen

Meer informatie over het toevoegen, wijzigen of verwijderen van een subnet van een virtueel netwerk. Alle Azure-resources die worden geïmplementeerd in een virtueel netwerk worden geïmplementeerd in een subnet binnen het virtuele netwerk. Als u geen ervaring hebt met virtuele netwerken, kunt u meer informatie hierover vinden in het [overzicht van Virtual Network](virtual-networks-overview.md) of door een [Snelstartgids](quick-create-portal.md)te volt ooien. Zie [een virtueel netwerk maken, wijzigen of verwijderen](manage-virtual-network.md)voor meer informatie over het beheren van een virtueel netwerk.

## <a name="before-you-begin"></a>Voordat u begint

Als u er nog geen hebt, stelt u een Azure-account in met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Voer vervolgens een van deze taken uit voordat u begint met de stappen in een van de volgende secties: 

- **Portal gebruikers**: Meld u aan bij de [Azure Portal](https://portal.azure.com) met uw Azure-account.

- **Power shell-gebruikers**: Voer de opdrachten uit in de [Azure Cloud shell](https://shell.azure.com/powershell)of voer Power shell uit vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Zoek de vervolg keuzelijst **omgeving selecteren** op het tabblad Azure Cloud shell browser en kies vervolgens **Power shell** als deze optie nog niet is geselecteerd.

    Als u Power shell lokaal uitvoert, gebruikt u Azure PowerShell module versie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az.Network` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Voer ook uit `Connect-AzAccount` om een verbinding te maken met Azure.

- **Azure-opdracht regel interface (CLI)-gebruikers**: Voer de opdrachten uit in de [Azure Cloud shell](https://shell.azure.com/bash)of voer de CLI uit vanaf uw computer. Gebruik Azure CLI versie 2.0.31 of hoger als u de Azure CLI lokaal uitvoert. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Voer ook uit `az login` om een verbinding te maken met Azure.

Het account waarmee u zich aanmeldt bij of verbinding maakt met Azure met, moet worden toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in [machtigingen](#permissions).

## <a name="add-a-subnet"></a>Een subnet toevoegen

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw virtuele netwerken weer te geven. Zoek en selecteer **Virtuele netwerken**.

2. Selecteer de naam van het virtuele netwerk waaraan u een subnet wilt toevoegen.

3. Selecteer uit **instellingen** subnet **subnets**  >  **Subnet**.

4. Voer in het dialoog venster **subnet toevoegen** waarden in voor de volgende instellingen:

    | Instelling | Beschrijving |
    | --- | --- |
    | **Naam** | De naam moet uniek zijn binnen het virtuele netwerk. Voor een maximale compatibiliteit met andere Azure-Services kunt u het beste een letter gebruiken als het eerste teken van de naam. Azure-toepassing gateway kan bijvoorbeeld niet worden geïmplementeerd in een subnet met een naam die begint met een getal. |
    | **Adresbereik** | <p>Het bereik moet uniek zijn binnen de adres ruimte voor het virtuele netwerk. Het bereik mag niet overlappen met andere adresbereiken in het virtuele netwerk. De adres ruimte moet worden opgegeven met behulp van de CIDR-notatie (Class Free Inter-Domain Routing).</p><p>In een virtueel netwerk met adres ruimte *10.0.0.0/16* kunt u bijvoorbeeld een subnetadres definiëren van *10.0.0.0/22*. Het kleinste bereik dat u kunt opgeven, is */29*, dat acht IP-adressen voor het subnet bevat. Azure reserveert het eerste en laatste adres in elk subnet voor protocol conformiteit. Er zijn drie extra adressen gereserveerd voor Azure-service gebruik. Als gevolg hiervan resulteert het definiëren van een subnet met een */29* -adres bereik in drie bruikbare IP-adressen in het subnet.</p><p>Als u van plan bent om een virtueel netwerk te verbinden met een VPN-gateway, moet u een gateway-subnet maken. Meer informatie over [specifieke aandachtspunten voor de adres bereik voor gateway-subnetten](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). U kunt het adres bereik wijzigen nadat het subnet is toegevoegd, onder specifieke voor waarden. Zie [subnet Settings wijzigen](#change-subnet-settings)voor meer informatie over het wijzigen van het adres bereik van een subnet.</p> |
    | **Netwerkbeveiligingsgroep** | Als u inkomend en uitgaand netwerk verkeer voor het subnet wilt filteren, kunt u een bestaande netwerk beveiligings groep koppelen aan een subnet. De netwerk beveiligings groep moet zich in hetzelfde abonnement en dezelfde locatie bevinden als het virtuele netwerk. Meer informatie over [netwerk beveiligings groepen](security-overview.md) en [het maken van een netwerk beveiligings groep](tutorial-filter-network-traffic.md). |
    | **Routetabel** | Als u de route ring van netwerk verkeer naar andere netwerken wilt beheren, kunt u eventueel een bestaande route tabel koppelen aan een subnet. De route tabel moet zich in hetzelfde abonnement en dezelfde locatie bevinden als het virtuele netwerk. Meer informatie over [Azure-route ring](virtual-networks-udr-overview.md) en [het maken van een route tabel](tutorial-create-route-table-portal.md). |
    | **Service-eindpunten** | <p>Voor een subnet kunnen eventueel een of meer service-eind punten zijn ingeschakeld. Als u een service-eind punt voor een service wilt inschakelen, selecteert u de service of services waarvoor u service-eind punten in de lijst met **Services** wilt inschakelen. Azure configureert de locatie automatisch voor een eind punt. Standaard configureert Azure de service-eind punten voor de regio van het virtuele netwerk. Ter ondersteuning van regionale failover-scenario's worden eind punten automatisch door Azure geconfigureerd voor [gekoppelde Azure-regio's](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) voor Azure Storage.</p><p>Als u een service-eind punt wilt verwijderen, moet u de service waarvoor u het service-eind punt wilt verwijderen, uitschakelen. Zie [service-eind punten voor virtuele netwerken](virtual-network-service-endpoints-overview.md)voor meer informatie over service-eind punten en de services waarvoor ze kunnen worden ingeschakeld. Zodra u een service-eind punt voor een service inschakelt, moet u ook netwerk toegang inschakelen voor het subnet voor een resource die is gemaakt met de service. Als u bijvoorbeeld het service-eind punt voor **micro soft. Storage** inschakelt, moet u ook netwerk toegang inschakelen voor alle Azure Storage accounts waaraan u netwerk toegang wilt verlenen. Als u netwerk toegang wilt inschakelen voor subnetten waarvoor een service-eind punt is ingeschakeld, raadpleegt u de documentatie voor de afzonderlijke service waarvoor u het service-eind punt hebt ingeschakeld.</p><p>Als u wilt controleren of een service-eind punt is ingeschakeld voor een subnet, bekijkt u de [efficiënte routes](diagnose-network-routing-problem.md) voor elke netwerk interface in het subnet. Wanneer u een eind punt configureert, ziet u een *standaard* route met de adres voorvoegsels van de service en het type van de volgende hop van **VirtualNetworkServiceEndpoint**. Zie [virtueel netwerk verkeer routeren](virtual-networks-udr-overview.md)voor meer informatie over route ring.</p> |
    | **Delegatie van subnet** | Een subnet kan eventueel een of meer delegaties hebben ingeschakeld. Subnet delegering geeft expliciete machtigingen voor de service voor het maken van servicespecifieke bronnen in het subnet met een unieke id tijdens de implementatie van de service. Als u een service wilt delegeren, selecteert u de service die u wilt delegeren in de lijst met **Services** . |

5. Selecteer **OK** om het subnet toe te voegen aan het virtuele netwerk dat u hebt geselecteerd.

### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>Subnet-instellingen wijzigen

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw virtuele netwerken weer te geven. Zoek en selecteer **Virtuele netwerken**.

2. Selecteer de naam van het virtuele netwerk met het subnet dat u wilt wijzigen.

3. Selecteer bij **instellingen** de optie **subnetten**.

4. Selecteer in de lijst met subnetten het subnet waarvoor u de instellingen wilt wijzigen.

5. Wijzig op de pagina subnet een van de volgende instellingen:

    | Instelling | Beschrijving |
    | --- | --- |
    | **Adresbereik** | Als er geen resources binnen het subnet zijn geïmplementeerd, kunt u het adres bereik wijzigen. Als er resources in het subnet aanwezig zijn, moet u de resources naar een ander subnet verplaatsen of eerst verwijderen uit het subnet. De stappen die u moet nemen om een resource te verplaatsen of te verwijderen, variëren afhankelijk van de resource. Raadpleeg de documentatie voor elk van deze resource typen voor meer informatie over het verplaatsen of verwijderen van resources in subnetten. Zie de beperkingen voor het **adres bereik** in stap 4 van [een subnet toevoegen](#add-a-subnet). |
    | **Gebruikers** | U kunt de toegang tot het subnet beheren door gebruik te maken van ingebouwde rollen of uw eigen aangepaste rollen. Zie [een roltoewijzing toevoegen](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment)voor meer informatie over het toewijzen van rollen en gebruikers voor toegang tot het subnet. |
    | **Netwerkbeveiligingsgroep** en **Routetabel** | Zie stap 4 van [een subnet toevoegen](#add-a-subnet). |
    | **Service-eindpunten** | <p>Zie Service-eind punten in stap 4 van [een subnet toevoegen](#add-a-subnet). Wanneer u een service-eind punt voor een bestaand subnet inschakelt, moet u ervoor zorgen dat er geen kritieke taken worden uitgevoerd op een wille keurige bron in het subnet. Service-eind punten scha kelen routes op elke netwerk interface in het subnet. De service-eind punten gaan van het gebruik van de standaard route met het adres voorvoegsel *0.0.0.0/0* en het volgende hop-type *Internet* om een nieuwe route te gebruiken met de adres prefixen van de service en het volgende hop-type *VirtualNetworkServiceEndpoint*.</p><p>Tijdens de switch kunnen alle open TCP-verbindingen worden beëindigd. Het service-eind punt is niet ingeschakeld totdat verkeer naar de service voor alle netwerk interfaces wordt bijgewerkt met de nieuwe route. Zie [virtueel netwerk verkeer routeren](virtual-networks-udr-overview.md)voor meer informatie over route ring.</p> |
    | **Delegatie van subnet** | Zie Service-eind punten in stap 4 van [een subnet toevoegen](#add-a-subnet). Subnet delegering kan worden gewijzigd in nul of er zijn meerdere overdrachten ingeschakeld. Als een resource voor een service al in het subnet is geïmplementeerd, kan er geen subnet-overdracht worden toegevoegd of verwijderd totdat alle resources voor de service zijn verwijderd. Als u wilt delegeren voor een andere service, selecteert u de service die u wilt delegeren in de lijst met **Services** . |

6. Selecteer **Opslaan**.

### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>Een subnet verwijderen

U kunt een subnet alleen verwijderen als er geen resources in het subnet aanwezig zijn. Als resources zich in het subnet bevinden, moet u deze bronnen verwijderen voordat u het subnet kunt verwijderen. De stappen die u moet nemen om een resource te verwijderen, variëren afhankelijk van de resource. Raadpleeg de documentatie voor elk van deze resource typen voor meer informatie over het verwijderen van bronnen in subnetten.

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw virtuele netwerken weer te geven. Zoek en selecteer **Virtuele netwerken**.

2. Selecteer de naam van het virtuele netwerk met het subnet dat u wilt verwijderen.

3. Selecteer bij **instellingen** de optie **subnetten**.

4. Selecteer in de lijst met subnetten het subnet dat u wilt verwijderen.

5. Selecteer **verwijderen** en selecteer vervolgens **Ja** in het bevestigings venster.

### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [AZ Network vnet subnet Delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete) |
| PowerShell | [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>Machtigingen

Om taken uit te voeren op subnetten, moet uw account worden toegewezen aan de [rol netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties in de volgende tabel zijn toegewezen:

|Actie                                                                   |   Name                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Een subnet van een virtueel netwerk lezen              |
|Micro soft. Network/virtualNetworks/subnetten/schrijven                          |   Een subnet van een virtueel netwerk maken of bijwerken  |
|Micro soft. Network/virtualNetworks/subnetten/verwijderen                         |   Subnet van een virtueel netwerk verwijderen            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Lid worden van een virtueel netwerk                     |
|Micro soft. netwerk/virtualNetworks/subnetten/joinViaServiceEndpoint/actie  |   Een service-eind punt inschakelen voor een subnet     |
|Micro soft. Network/virtualNetworks/subnetten/informatie/lezen           |   De virtuele machines in een subnet ophalen       |

## <a name="next-steps"></a>Volgende stappen

- Een virtueel netwerk en subnetten maken met behulp van [Power shell](powershell-samples.md) of [Azure cli](cli-samples.md) -voorbeeld scripts of met behulp van Azure [Resource Manager-sjablonen](template-samples.md)
- [Azure Policy definities](policy-samples.md) voor virtuele netwerken maken en toewijzen
