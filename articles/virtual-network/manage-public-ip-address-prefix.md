---
title: Een voor voegsel van een openbaar IP-adres voor Azure maken, wijzigen of verwijderen
titlesuffix: Azure Virtual Network
description: Meer informatie over open bare IP-adres voorvoegsels en hoe u deze kunt maken, wijzigen of verwijderen. Zie waar u aanvullende informatie kunt vinden.
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
ms.openlocfilehash: 2e32faad698fbf316d51123cc8b7845a3b262c7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938659"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Een voorvoegsel van een openbaar IP-adres maken, wijzigen of verwijderen

Meer informatie over een openbaar IP-adres voorvoegsel en hoe u er een kunt maken, wijzigen en verwijderen. Een voor voegsel van een openbaar IP-adres is een aaneengesloten adres bereik op basis van het aantal open bare IP-adressen dat u opgeeft. De adressen worden toegewezen aan uw abonnement. Wanneer u een resource voor een openbaar IP-adres maakt, kunt u een statisch openbaar IP-adres toewijzen aan het voor voegsel en het adres koppelen aan virtuele machines, load balancers of andere bronnen om verbinding met internet mogelijk te maken. Als u niet bekend bent met open bare IP-adres voorvoegsels, raadpleegt u [overzicht van open bare IP-adressen](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voer de volgende taken uit voordat u de stappen in een van de secties van dit artikel uitvoert:

- Als u nog geen Azure-account hebt, kunt u zich aanmelden voor een [gratis proef account](https://azure.microsoft.com/free).
- Als u de portal gebruikt, opent https://portal.azure.com u en meldt u zich aan met uw Azure-account.
- Als u Power shell-opdrachten gebruikt om taken in dit artikel te volt ooien, moet u de opdrachten uitvoeren in de [Azure Cloud shell](https://shell.azure.com/powershell)of Power shell uitvoeren vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Voor deze zelf studie is de Azure PowerShell module versie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.
- Als u Azure-opdracht regel interface opdrachten gebruikt om taken in dit artikel te volt ooien, moet u de opdrachten uitvoeren in de [Azure Cloud shell](https://shell.azure.com/bash)of door de CLI vanaf uw computer uit te voeren. Voor deze zelf studie is de Azure CLI-versie 2.0.41 of hoger vereist. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli). Als u de Azure CLI lokaal uitvoert, moet u ook uitvoeren `az login` om een verbinding te maken met Azure.

Het account waarmee u zich aanmeldt of verbinding maakt met Azure met, moet worden toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in [machtigingen](#permissions).

Voor voegsels voor open bare IP-adressen gelden kosten. Zie [prijzen](https://azure.microsoft.com/pricing/details/ip-addresses)voor meer informatie.

## <a name="create-a-public-ip-address-prefix"></a>Een voor voegsel voor een openbaar IP-adres maken

1. Selecteer boven aan de linkerbovenhoek van de Portal de optie **+ een resource maken**.
2. Geef het *open bare IP-voor voegsel* op in het vak in *de Marketplace zoeken* . Wanneer het **voor voegsel van een openbaar IP-adres** wordt weer gegeven in de zoek resultaten, selecteert u dit.
3. Selecteer onder **voor voegsel openbaar IP-adres** de optie **maken**.
4. Typ of selecteer waarden voor de volgende instellingen onder **openbaar IP-adres voorvoegsel maken** en selecteer vervolgens **maken**:

   |Instelling|Vereist?|Details|
   |---|---|---|
   |Abonnement|Yes|Moet zich in hetzelfde [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) bevinden als de resource waaraan u het open bare IP-adres wilt koppelen.|
   |Resourcegroep|Yes|Kan zich in dezelfde of verschillende [resource groepen](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) bevinden als de resource waaraan u het open bare IP-adres wilt koppelen.|
   |Name|Yes|De naam moet uniek zijn binnen de resource groep die u selecteert.|
   |Region|Yes|Moet zich in dezelfde [regio](https://azure.microsoft.com/regions)bevinden als de open bare IP-adressen waaraan u adressen uit het bereik toewijst.|
   |Voorvoegsel grootte|Yes| De grootte van het voor voegsel dat u nodig hebt. A/28 of 16 IP-adressen is de standaard waarde.

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[AZ Network Public-IP voorvoegsel Create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)|
|PowerShell|[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

>[!NOTE]
>In regio's met beschikbaarheids zones kunt u Power shell-of CLI-opdrachten gebruiken om een openbaar IP-adres voorvoegsel te maken als ofwel: niet-zonegebonden, gekoppeld aan een specifieke zone of om zone-redundantie te gebruiken.  Voor API-versie 2020-08-01 of hoger geldt dat als er geen zone parameter wordt gegeven, een niet-zonegebonden openbaar IP-adres voorvoegsel wordt gemaakt. Voor versies van de API die ouder zijn dan 2020-08-01, wordt een zone-redundant openbaar IP-adres voorvoegsel gemaakt. 

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Een statisch openbaar IP-adres maken op basis van een voor voegsel
Wanneer u een voor voegsel maakt, moet u vaste IP-adressen maken op basis van het voor voegsel. Volg de onderstaande stappen om dit te doen.

1. In het vak met de tekst *zoeken resources* boven aan de Azure Portal, typt u *voor voegsel openbaar IP-adres*. Wanneer **open bare IP-adres voorvoegsels** worden weer gegeven in de zoek resultaten, selecteert u dit.
2. Selecteer het voor voegsel waarvoor u open bare Ip's wilt maken.
3. Wanneer het wordt weer gegeven in de zoek resultaten, selecteert u deze en klikt u op **+ IP-adres toevoegen** in de sectie Overzicht.
4. Typ of selecteer waarden voor de volgende instellingen onder **openbaar IP-adres maken**. Omdat een voor voegsel voor de standaard-SKU, IPv4 en statisch is, hoeft u alleen de volgende gegevens op te geven:

   |Instelling|Vereist?|Details|
   |---|---|---|
   |Name|Yes|De naam van het open bare IP-adres moet uniek zijn binnen de resource groep die u selecteert.|
   |Time-out voor inactiviteit (minuten)|No|Hoe lang duurt het voordat een TCP-of HTTP-verbinding is geopend, zonder dat er wordt gebruikgemaakt van clients om Keep-Alive-berichten te verzenden. |
   |DNS-naamlabel|No|Moet uniek zijn binnen de Azure-regio waarin u de naam hebt gemaakt (in alle abonnementen en alle klanten). Azure registreert automatisch de naam en het IP-adres in DNS zodat u verbinding kunt maken met een resource met de naam. Azure voegt een standaard-subnet, zoals *location.cloudapp.Azure.com* (waarbij locatie de locatie is die u hebt geselecteerd), toe aan de naam die u opgeeft, om de volledig gekwalificeerde DNS-naam te maken. Zie [Azure DNS gebruiken met een openbaar IP-adres van Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)voor meer informatie.|

U kunt ook de CLI-en PS-opdrachten hieronder met de para meters---Public-IP-prefix (CLI) en-PublicIpPrefix (PS) gebruiken om een open bare IP-adres bron te maken. 

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-or-delete-a-prefix"></a>Een voor voegsel weer geven of verwijderen

1. In het vak met de tekst *zoeken resources* boven aan de Azure Portal, typt u *voor voegsel openbaar IP-adres*. Wanneer **open bare IP-adres voorvoegsels** worden weer gegeven in de zoek resultaten, selecteert u dit.
2. Selecteer de naam van het voor voegsel van het open bare IP-adres dat u wilt weer geven, wijzig de instellingen voor of verwijder deze in de lijst.
3. Voer een van de volgende opties uit, afhankelijk van of u het voor voegsel van het open bare IP-adres wilt weer geven, verwijderen of wijzigen.
   - **Weer gave**: in de sectie **overzicht** ziet u de belangrijkste instellingen voor het voor voegsel van het open bare IP-adres, zoals voor voegsel.
   - **Verwijderen**: als u het voor voegsel van het open bare IP-adres wilt verwijderen, selecteert u **verwijderen** in het gedeelte **overzicht** . Als adressen binnen het voor voegsel zijn gekoppeld aan resources met een openbaar IP-adres, moet u eerst de open bare IP-adres bronnen verwijderen. Zie [een openbaar IP-adres verwijderen](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[AZ Network Public-IP voorvoegsel List](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-list) to list open bare IP-adressen, [AZ Network Public-IP-voor voegsel weer](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show) geven instellingen weer geven; [AZ Network Public-IP-voor voegsel bijwerken](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-update) ; [AZ Network Public-IP-voor voegsel verwijderen](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-delete) om te verwijderen|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) voor het ophalen van een openbaar IP-adres object en het weer geven van de instellingen, [set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) om instellingen bij te werken; [Verwijder-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) om te verwijderen|

## <a name="permissions"></a>Machtigingen

Als u taken wilt uitvoeren op open bare IP-adres voorvoegsels, moet uw account worden toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rol waaraan de juiste acties in de volgende tabel zijn toegewezen:

| Bewerking                                                            | Name                                                           |
| ---------                                                         | -------------                                                  |
| Micro soft. Network/publicIPPrefixes/lezen                           | Een voor voegsel van een openbaar IP-adres lezen                                |
| Micro soft. Network/publicIPPrefixes/schrijven                          | Een voor voegsel voor een openbaar IP-adres maken of bijwerken                    |
| Micro soft. Network/publicIPPrefixes/verwijderen                         | Een voor voegsel voor een openbaar IP-adres verwijderen                              |
|Micro soft. Network/publicIPPrefixes/samen voegen/actie                     | Een openbaar IP-adres maken op basis van een voor voegsel |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over scenario's en voor delen van het gebruik van een [openbaar IP-voor voegsel](public-ip-address-prefix.md)
