---
title: Een voorvoegsel voor een openbaar IP-adres van Azure maken, wijzigen of verwijderen
titlesuffix: Azure Virtual Network
description: Meer informatie over voorvoegsels voor openbare IP-adressen en hoe u deze kunt maken, wijzigen of verwijderen. Zie waar u aanvullende informatie kunt vinden.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/13/2019
ms.author: allensu
ms.openlocfilehash: 173fa3a8288ccceb07048e83fcec35d67b2fd35f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783427"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Een voorvoegsel van een openbaar IP-adres maken, wijzigen of verwijderen

Meer informatie over een voorvoegsel voor een openbaar IP-adres en het maken, wijzigen en verwijderen van een voorvoegsel. Een voorvoegsel voor een openbaar IP-adres is een aaneengesloten reeks adressen op basis van het aantal openbare IP-adressen dat u opgeeft. De adressen worden toegewezen aan uw abonnement. Wanneer u een resource voor een openbaar IP-adres maakt, kunt u een statisch openbaar IP-adres toewijzen vanuit het voorvoegsel en het adres koppelen aan virtuele machines, load balancers of andere resources om internetverbinding mogelijk te maken. Zie Overzicht van voorvoegsels voor openbare IP-adressen als u niet bekend bent met voorvoegsels voor [openbare IP-adressen](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voltooi de volgende taken voordat u de stappen in een van de secties van dit artikel uitvoert:

- Als u nog geen Azure-account hebt, kunt u zich aanmelden voor een [gratis proefaccount.](https://azure.microsoft.com/free)
- Als u de portal gebruikt, opent https://portal.azure.com u en meld u zich aan met uw Azure-account.
- Als u PowerShell-opdrachten gebruikt om taken in dit artikel uit te voeren, voert u de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/powershell)of door PowerShell uit te voeren vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Voor deze zelfstudie is Azure PowerShell moduleversie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.
- Als u azure CLI-opdrachten (Opdrachtregelinterface) gebruikt om taken in dit artikel uit te voeren, voert u de opdrachten uit in [de Azure Cloud Shell](https://shell.azure.com/bash)of door de CLI uit te voeren vanaf uw computer. Voor deze zelfstudie is azure CLI versie 2.0.41 of hoger vereist. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli). Als u de Azure CLI lokaal gebruikt, moet u ook uitvoeren om `az login` een verbinding met Azure te maken.

Het account dat u aanmeldt of verbinding maakt met [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) Azure, moet worden [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) toegewezen aan de rol Inzender voor het netwerk of aan een aangepaste rol die wordt toegewezen aan de juiste acties die worden vermeld in [Machtigingen.](#permissions)

Voor voorvoegsels voor openbare IP-adressen worden kosten in rekening brengen. Zie prijzen voor [meer informatie.](https://azure.microsoft.com/pricing/details/ip-addresses)

## <a name="create-a-public-ip-address-prefix"></a>Een voorvoegsel voor een openbaar IP-adres maken

1. Selecteer in de linkerbovenhoek van de portal **+ Een resource maken.**
2. Voer *het openbare IP-voorvoegsel* in het *vak Marketplace doorzoeken* in. Wanneer **het voorvoegsel openbaar IP-adres** wordt weergegeven in de zoekresultaten, selecteert u het.
3. Selecteer **onder Voorvoegsel van openbaar IP-adres** **de optie Maken.**
4. Voer waarden in of selecteer waarden voor de volgende instellingen, onder **Openbaar IP-adres** voorvoegsel maken en selecteer **vervolgens Maken:**

   |Instelling|Vereist?|Details|
   |---|---|---|
   |Abonnement|Yes|Moet bestaan in hetzelfde [abonnement als](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) de resource waar u het openbare IP-adres aan wilt koppelen.|
   |Resourcegroep|Yes|Kan bestaan in dezelfde of een andere [resourcegroep](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) als de resource waar u het openbare IP-adres aan wilt koppelen.|
   |Name|Yes|De naam moet uniek zijn binnen de resourcegroep die u selecteert.|
   |Region|Yes|Moet bestaan in dezelfde [regio als](https://azure.microsoft.com/regions)de openbare IP-adressen die u toewijst aan adressen uit het bereik.|
   |Voorvoegselgrootte|Yes| De grootte van het voorvoegsel dat u nodig hebt. Een /28 of 16 IP-adressen is de standaardinstelling.

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create)|
|PowerShell|[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

>[!NOTE]
>In regio's met beschikbaarheidszones kunt u PowerShell- of CLI-opdrachten gebruiken om een voorvoegsel voor een openbaar IP-adres te maken: niet-zonelijk, gekoppeld aan een specifieke zone of zone-redundantie gebruiken.  Voor API-versie 2020-08-01 of hoger wordt een niet-zoneparameter gemaakt als er geen zoneparameter is opgegeven. Voor versies van de API die ouder zijn dan 2020-08-01, wordt een zone-redundant openbaar IP-adres voorvoegsel gemaakt. 

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Een statisch openbaar IP-adres maken van een voorvoegsel
Zodra u een voorvoegsel hebt gemaakt, moet u statische IP-adressen maken van het voorvoegsel. Volg de onderstaande stappen om dit te doen.

1. Typ in het vak met de tekst *Resources* zoeken bovenaan het Azure Portal het *voorvoegsel van het openbare IP-adres.* Wanneer **voorvoegsels voor openbare IP-adressen** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer het voorvoegsel waar u openbare IP's van wilt maken.
3. Wanneer deze wordt weergegeven in de zoekresultaten, selecteert u deze en klikt u op **+IP-adres toevoegen** in de sectie Overzicht.
4. Voer waarden in of selecteer waarden voor de volgende instellingen onder **Openbaar IP-adres maken.** Omdat een voorvoegsel voor standard-SKU, IPv4 en statisch is, hoeft u alleen de volgende informatie op te geven:

   |Instelling|Vereist?|Details|
   |---|---|---|
   |Name|Yes|De naam van het openbare IP-adres moet uniek zijn binnen de resourcegroep die u selecteert.|
   |Time-out voor inactiviteit (minuten)|No|Hoeveel minuten een TCP- of HTTP-verbinding open moet houden zonder dat clients keep-alive-berichten moeten verzenden. |
   |DNS-naamlabel|No|Moet uniek zijn binnen de Azure-regio waarin u de naam maakt (voor alle abonnementen en alle klanten). Azure registreert automatisch de naam en het IP-adres in de DNS, zodat u met de naam verbinding kunt maken met een resource. Azure voegen een standaardsubnet, zoals *location.cloudapp.azure.com* (waarbij locatie de locatie is die u selecteert) toe aan de naam die u op geeft, om de volledig gekwalificeerde DNS-naam te maken. Zie Use Azure DNS with an Azure public IP address (Een ip-adres [gebruiken met een openbaar Azure-IP-adres) voor meer informatie.](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)|

U kunt ook de onderstaande CLI- en PS-opdrachten gebruiken met de parameters --public-ip-prefix (CLI) en -PublicIpPrefix (PS) om een resource voor een openbaar IP-adres te maken. 

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-or-delete-a-prefix"></a>Een voorvoegsel weergeven of verwijderen

1. Typ in het vak met de tekst *Resources* zoeken bovenaan het Azure Portal het *voorvoegsel van het openbare IP-adres.* Wanneer **voorvoegsels voor openbare IP-adressen** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer de naam van het openbare IP-adres voorvoegsel dat u wilt weergeven, wijzig de instellingen voor of verwijder uit de lijst.
3. Voltooi een van de volgende opties, afhankelijk van of u het voorvoegsel van het openbare IP-adres wilt weergeven, verwijderen of wijzigen.
   - **Weergave:** in **de sectie Overzicht** ziet u de belangrijkste instellingen voor het voorvoegsel van het openbare IP-adres, zoals het voorvoegsel.
   - **Verwijderen:** als u het voorvoegsel van het openbare IP-adres wilt verwijderen, **selecteert u Verwijderen** in **de sectie** Overzicht. Als adressen binnen het voorvoegsel zijn gekoppeld aan openbare IP-adresresources, moet u eerst de openbare IP-adresresources verwijderen. Zie [Een openbaar IP-adres verwijderen.](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az network public-ip prefix list to](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_list) list public IP addresses, [az network public-ip prefix show to](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_show) show settings; [az network public-ip prefix update](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_update) to update; [az network public-ip prefix delete to](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_delete) delete|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) om een openbaar IP-adresobject op te halen en de instellingen ervan weer te geven, [Set-AzPublicIpPrefix om](/powershell/module/az.network/set-azpublicipprefix) instellingen bij te werken; [Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) om te verwijderen|

## <a name="permissions"></a>Machtigingen

Als u taken wilt uitvoeren op voorvoegsels voor openbare [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) IP-adressen, [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) moet uw account worden toegewezen aan de rol Netwerkbijdrager of aan een aangepaste rol die de juiste acties krijgt toegewezen die worden vermeld in de volgende tabel:

| Bewerking                                                            | Name                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Een voorvoegsel voor een openbaar IP-adres lezen                                |
| Microsoft.Network/publicIPPrefixes/write                          | Een voorvoegsel voor een openbaar IP-adres maken of bijwerken                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Een voorvoegsel voor een openbaar IP-adres verwijderen                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | Een openbaar IP-adres maken van een voorvoegsel |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over scenario's en voordelen van het gebruik van [een openbaar IP-voorvoegsel](public-ip-address-prefix.md)
