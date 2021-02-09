---
title: Openbaar eind punt configureren-Azure SQL Managed instance
description: Meer informatie over het configureren van een openbaar eind punt voor Azure SQL Managed instance
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, sstein
ms.date: 02/08/2021
ms.openlocfilehash: 7d5f40be895aea26a234d9ae622aa5bf22528231
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981439"
---
# <a name="configure-public-endpoint-in-azure-sql-managed-instance"></a>Openbaar eindpunt configureren in een beheerd Azure SQL-exemplaar
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Het open bare eind punt voor een [beheerd exemplaar](./sql-managed-instance-paas-overview.md) maakt gegevens toegang tot uw beheerde exemplaar mogelijk buiten het [virtuele netwerk](../../virtual-network/virtual-networks-overview.md). U hebt toegang tot uw beheerde exemplaar vanuit multi tenant Azure-Services, zoals Power BI, Azure App Service of een on-premises netwerk. Als u het open bare eind punt op een beheerd exemplaar gebruikt, hoeft u geen VPN te gebruiken. Dit kan helpen om problemen met de door Voer van VPN te voor komen.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> - Open bare eind punt inschakelen voor uw beheerde instantie in de Azure Portal
> - Open bare eind punt voor uw beheerde instantie inschakelen met Power shell
> - De beveiligings groep voor een beheerd exemplaar netwerk configureren om verkeer naar het open bare eind punt van het beheerde exemplaar toe te staan
> - Het open bare endpoint van het beheerde exemplaar verkrijgen connection string

## <a name="permissions"></a>Machtigingen

Als gevolg van de gevoeligheid van gegevens die zich in een beheerd exemplaar bevindt, vereist de configuratie voor het inschakelen van het open bare eind punt van het beheerde exemplaar een proces dat uit twee stappen bestaat. Deze veiligheids maatregel voldoet aan de schei ding van taken (SoD):

- Het inschakelen van een openbaar eind punt op een beheerd exemplaar moet worden uitgevoerd door de beheerder van het beheerde exemplaar. De beheerder van het beheerde exemplaar kan worden gevonden op de **overzichts** pagina van de bron van het beheerde exemplaar.
- Verkeer toestaan met behulp van een netwerk beveiligings groep die moet worden uitgevoerd door een netwerk beheerder. Zie [machtigingen voor netwerk beveiligings groepen](../../virtual-network/manage-network-security-group.md#permissions)voor meer informatie.

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Openbaar eind punt inschakelen voor een beheerd exemplaar in het Azure Portal

1. Start de Azure Portal op <https://portal.azure.com/.>
1. Open de resource groep met het beheerde exemplaar en selecteer het **SQL Managed instance** waarvoor u het open bare eind punt wilt configureren.
1. Selecteer op de pagina **beveiligings** instellingen het tabblad **virtueel netwerk** .
1. Selecteer op de pagina configuratie van virtueel netwerk de optie **inschakelen** en klik vervolgens op het pictogram **Opslaan** om de configuratie bij te werken.

![Scherm afbeelding toont een virtuele netwerk pagina van het SQL Managed instance waarbij het open bare eind punt is ingeschakeld.](./media/public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Openbaar eind punt inschakelen voor een beheerd exemplaar met behulp van Power shell

### <a name="enable-public-endpoint"></a>Openbaar eind punt inschakelen

Voer de volgende Power shell-opdrachten uit. Vervang **abonnement-id** door uw abonnements-id. Vervang ook de **naam van RG** door de resource groep voor uw beheerde exemplaar en vervang de **mi-naam** door de naam van uw beheerde exemplaar.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi = $mi | Set-AzSqlInstance -PublicDataEndpointEnabled $true -force
```

### <a name="disable-public-endpoint"></a>Openbaar eind punt uitschakelen

Als u het open bare eind punt wilt uitschakelen met behulp van Power shell, voert u de volgende opdracht uit (en vergeet niet om de NSG voor de binnenkomende poort 3342 te sluiten als u deze hebt geconfigureerd):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Openbaar eindpunt verkeer toestaan voor de netwerk beveiligings groep

1. Als u de configuratie pagina van het beheerde exemplaar nog steeds geopend hebt, gaat u naar het tabblad **overzicht** . Als dat niet het geval is, gaat u terug naar uw **SQL Managed instance** -resource. Selecteer de koppeling **virtueel netwerk/subnet** , waarmee u naar de configuratie pagina van het virtuele netwerk gaat.

    ![Scherm afbeelding toont de configuratie pagina van het virtuele netwerk waar u de waarde van het virtuele netwerk/subnet kunt vinden.](./media/public-endpoint-configure/mi-overview.png)

1. Selecteer het tabblad **subnetten** in het linkerdeel venster configuratie van het virtuele netwerk en noteer de **beveiligings groep** voor uw beheerde exemplaar.

    ![Scherm afbeelding toont het tabblad subnet, waar u de BEVEILIGINGS groep voor uw beheerde exemplaar kunt ophalen.](./media/public-endpoint-configure/mi-vnet-subnet.png)

1. Ga terug naar de resource groep die uw beheerde exemplaar bevat. De naam van de **netwerk beveiligings groep** die hierboven wordt vermeld, wordt weer gegeven. Selecteer de naam om naar de configuratie pagina voor de netwerk beveiligings groep te gaan.

1. Selecteer het tabblad **binnenkomende beveiligings regels** en voeg een regel **toe** met een hogere prioriteit dan de **deny_all_inbound** regel met de volgende instellingen: </br> </br>

    |Instelling  |Voorgestelde waarde  |Beschrijving  |
    |---------|---------|---------|
    |**Bron**     |Een IP-adres of service label         |<ul><li>Voor Azure-Services, zoals Power BI, selecteert u de Azure Cloud service-tag</li> <li>Gebruik NAT IP-adres voor uw computer of virtuele Azure-machine</li></ul> |
    |**Poortbereiken van bron**     |* |Dit op * (alle) laten staan als bron poorten worden meestal dynamisch toegewezen en als zodanig, onvoorspelbaar |
    |**Doel**     |Alle         |Bestemming weglaten om verkeer naar het subnet van het beheerde exemplaar toe te staan |
    |**Poortbereiken van doel**     |3342         |Doel poort van bereik tot 3342, wat het open bare TDS-eind punt van het beheerde exemplaar is |
    |**Protocol**     |TCP         |SQL Managed instance gebruikt TCP-protocol voor TDS |
    |**Actie**     |Toestaan         |Binnenkomend verkeer naar een beheerd exemplaar via het open bare eind punt toestaan |
    |**Priority**     |1300         |Zorg ervoor dat deze regel een hogere prioriteit heeft dan de **deny_all_inbound** regel |

    ![Scherm afbeelding toont de binnenkomende beveiligings regels met uw nieuwe public_endpoint_inbound regel boven de deny_all_inbound regel.](./media/public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > Poort 3342 wordt gebruikt voor open bare eindpunt verbindingen met een beheerd exemplaar en kan op dit moment niet worden gewijzigd.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Het open bare endpoint van het beheerde exemplaar verkrijgen connection string

1. Ga naar de configuratie pagina van het beheerde exemplaar die is ingeschakeld voor het open bare eind punt. Selecteer het tabblad **verbindings reeksen** onder de configuratie- **instellingen** .
1. Houd er rekening mee dat de hostnaam van het open bare eind punt de indeling <mi_name> bevat. **Public**. <dns_zone>. database.Windows.net en de poort die voor de verbinding wordt gebruikt, is 3342. Hier volgt een voor beeld van een server waarde van de connection string het identificeren van de open bare eindpunt poort die in SQL Server Management Studio of Azure Data Studio verbindingen kan worden gebruikt: `<mi_name>.public.<dns_zone>.database.windows.net,3342`

    ![Scherm afbeelding toont de verbindings reeksen voor uw open bare en privé-eind punten.](./media/public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van [Azure SQL Managed instance veilig met een openbaar eind punt](public-endpoint-overview.md).
