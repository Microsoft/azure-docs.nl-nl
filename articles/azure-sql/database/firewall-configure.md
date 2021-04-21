---
title: IP-firewallregels
description: IP-firewallregels op serverniveau configureren voor een database in Azure SQL Database of Azure Synapse Analytics firewall. Toegang beheren en IP-firewallregels op databaseniveau configureren voor SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 06/17/2020
ms.openlocfilehash: 200df14e7d18c4bdfb903bef46c169f6f7bf5ca5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781771"
---
# <a name="azure-sql-database-and-azure-synapse-ip-firewall-rules"></a>Azure SQL Database EN Azure Synapse IP-firewallregels
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Wanneer u bijvoorbeeld een nieuwe server maakt in Azure SQL Database of Azure Synapse Analytics met de naam *mysqlserver,* blokkeert een firewall op serverniveau alle toegang tot het openbare eindpunt voor de server (dat toegankelijk is *op mysqlserver.database.windows.net*). Voor het gemak *wordt SQL Database* gebruikt om te verwijzen naar zowel SQL Database als Azure Synapse Analytics.

> [!IMPORTANT]
> Dit artikel is *niet* van toepassing op *Azure SQL Managed Instance*. Zie Connect your application to Azure SQL Managed Instance (Uw toepassing verbinden met de [netwerkconfiguratie) Azure SQL Managed Instance.](../managed-instance/connect-application-instance.md)
>
> Azure Synapse ondersteunt alleen IP-firewallregels op serverniveau. Het biedt geen ondersteuning voor IP-firewallregels op databaseniveau.

## <a name="how-the-firewall-works"></a>Hoe de firewall werkt

Verbindingspogingen via internet en Azure moeten de firewall passeren voordat ze uw server of database bereiken, zoals in het volgende diagram wordt weergegeven.

   ![Firewallconfiguratiediagram][1]

### <a name="server-level-ip-firewall-rules"></a>IP-firewallregels op serverniveau

Met deze regels kunnen clients toegang krijgen tot uw hele server, dat wil zeggen, alle databases die door de server worden beheerd. De regels worden opgeslagen in de *hoofddatabase.* U kunt voor een server maximaal 128 firewallregels op serverniveau opgeven. Als u de instelling **Azure-services** en -resources toegang geven tot deze server hebt ingeschakeld, telt dit als één firewallregel voor de server.
  
U kunt IP-firewallregels op serverniveau configureren met behulp van de Azure Portal-, PowerShell- of Transact-SQL-instructies.

- Als u de portal of PowerShell wilt gebruiken, moet u de eigenaar van het abonnement of een inzender van het abonnement zijn.
- Als u Transact-SQL wilt gebruiken, moet u verbinding maken met de hoofddatabase als de principal-aanmelding op serverniveau of als Azure Active Directory beheerder.  (Een IP-firewallregel op serverniveau moet eerst worden gemaakt door een gebruiker met machtigingen op Azure-niveau.)

> [!NOTE]
> Tijdens het maken van een nieuwe logische SQL-server van de Azure Portal is de instelling **Azure-services** en -resources toegang geven tot deze server standaard ingesteld op **Nee.**

### <a name="database-level-ip-firewall-rules"></a>IP-firewallregels op databaseniveau

Met IP-firewallregels op databaseniveau hebben clients toegang tot bepaalde (beveiligde) databases. U maakt de regels voor elke database (inclusief de *hoofddatabase)* en ze worden opgeslagen in de afzonderlijke database.
  
- U kunt IP-firewallregels op databaseniveau alleen maken en beheren voor hoofd- en gebruikersdatabases met behulp van Transact-SQL-instructies en pas nadat u de eerste firewall op serverniveau hebt geconfigureerd.
- Als u een IP-adresbereik opgeeft in de IP-firewallregel op databaseniveau die buiten het bereik van de IP-firewallregel op serverniveau ligt, hebben alleen clients met IP-adressen in het bereik op databaseniveau toegang tot de database.
- U kunt maximaal 128 IP-firewallregels op databaseniveau hebben voor een database. Zie het voorbeeld verder in dit artikel voor meer informatie over het configureren van IP-firewallregels op databaseniveau en zie sp_set_database_firewall_rule [(Azure SQL Database).](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Aanbevelingen voor het instellen van firewallregels

We raden u aan om waar mogelijk IP-firewallregels op databaseniveau te gebruiken. Dit gebruik verbetert de beveiliging en maakt uw database draagbaarder. Gebruik IP-firewallregels op serverniveau voor beheerders. Gebruik ze ook wanneer u veel databases hebt met dezelfde toegangsvereisten en u niet elke database afzonderlijk wilt configureren.

> [!NOTE]
> Voor meer informatie over draagbare databases in de context van bedrijfscontinuïteit raadpleegt u [Authentication requirements for disaster recovery](active-geo-replication-security-configure.md) (Verificatievereisten voor herstel na noodgevallen).

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>IP-firewallregels op server- en databaseniveau

*Moeten gebruikers van de ene database volledig zijn geïsoleerd van een andere database?*

Zo *ja,* gebruik IP-firewallregels op databaseniveau om toegang te verlenen. Met deze methode voorkomt u het gebruik van IP-firewallregels op serverniveau, die toegang via de firewall tot alle databases toestaan. Dat zou de diepte van uw verdediging verminderen.

*Hebben gebruikers op de IP-adressen toegang nodig tot alle databases?*

Zo *ja,* gebruik DAN IP-firewallregels op serverniveau om het aantal keren te beperken dat u IP-firewallregels moet configureren.

*Heeft de persoon of het team dat de IP-firewallregels configureert alleen toegang via de Azure Portal, PowerShell of de REST API?*

Als dat het zo is, moet u IP-firewallregels op serverniveau gebruiken. IP-firewallregels op databaseniveau kunnen alleen worden geconfigureerd via Transact-SQL.  

*Is het niet toegestaan dat de persoon of het team dat de IP-firewallregels configureert machtigingen op hoog niveau heeft op databaseniveau?*

Als dat het zo is, gebruikt u IP-firewallregels op serverniveau. U hebt ten minste de *machtiging CONTROL DATABASE* op databaseniveau nodig om IP-firewallregels op databaseniveau te configureren via Transact-SQL.  

*Beheert de persoon of het team dat de IP-firewallregels configureert of controleert de IP-firewallregels centraal voor veel (mogelijk honderden) databases?*

In dit scenario worden best practices bepaald door uw behoeften en omgeving. IP-firewallregels op serverniveau zijn mogelijk eenvoudiger te configureren, maar met scripting kunnen regels op databaseniveau worden geconfigureerd. En zelfs als u IP-firewallregels op serverniveau gebruikt, moet u mogelijk IP-firewallregels op databaseniveau controleren om te zien of gebruikers met de machtiging CONTROL voor de database IP-firewallregels op databaseniveau maken. 

*Kan ik een combinatie van IP-firewallregels op server- en databaseniveau gebruiken?*

Ja. Sommige gebruikers, zoals beheerders, hebben mogelijk IP-firewallregels op serverniveau nodig. Andere gebruikers, zoals gebruikers van een databasetoepassing, hebben mogelijk IP-firewallregels op databaseniveau nodig.

### <a name="connections-from-the-internet"></a>Internetverbindingen

Wanneer een computer verbinding probeert te maken met uw server via internet, controleert de firewall eerst het oorspronkelijke IP-adres van de aanvraag op de IP-firewallregels op databaseniveau voor de database die de verbinding aanvraagt.

- Als het adres binnen een bereik ligt dat is opgegeven in de IP-firewallregels op databaseniveau, wordt de verbinding verleend aan de database die de regel bevat.
- Als het adres zich niet binnen een bereik in de IP-firewallregels op databaseniveau bevinden, controleert de firewall de IP-firewallregels op serverniveau. Als het adres zich binnen een bereik van de IP-firewallregels op serverniveau bevinden, wordt de verbinding verleend. IP-firewallregels op serverniveau zijn van toepassing op alle databases die worden beheerd door de server.  
- Als het adres zich niet binnen een bereik van de IP-firewallregels op database- of serverniveau bevinden, mislukt de verbindingsaanvraag.

> [!NOTE]
> Als u Azure SQL Database uw lokale computer wilt openen, moet u ervoor zorgen dat de firewall op uw netwerk en lokale computer uitgaande communicatie op TCP-poort 1433 toestaat.

### <a name="connections-from-inside-azure"></a>Verbindingen vanuit Azure

Als u wilt dat toepassingen die in Azure worden gehost, verbinding kunnen maken met uw SQL-server, moeten Azure-verbindingen zijn ingeschakeld. Als u Azure-verbindingen wilt inschakelen, moet er een firewallregel zijn met ip-begin- en eindadressen ingesteld op 0.0.0.0.

Wanneer een toepassing uit Azure verbinding probeert te maken met de server, controleert de firewall of Azure-verbindingen zijn toegestaan door te controleren of deze firewallregel bestaat. Dit kan rechtstreeks vanuit de blade Azure Portal worden ingeschakeld door **Azure-services** en -resources toegang geven tot deze server in te schakelen op **AAN** in de instellingen voor **firewalls en virtuele** netwerken. Als u deze instelling in op AAN instelt, wordt er een inkomende firewallregel gemaakt voor IP 0.0.0.0 - 0.0.0 met de naam **AllowAllWindowsIP.** Gebruik PowerShell of de Azure CLI om een firewallregel te maken met begin- en eind-IP-adressen die zijn ingesteld op 0.0.0.0 als u de portal niet gebruikt. 

> [!IMPORTANT]
> Met deze optie configureert u de firewall om alle verbindingen vanuit Azure toe te staan, inclusief verbindingen van de abonnementen van andere klanten. Als u deze optie selecteert, moet u ervoor zorgen dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.

## <a name="permissions"></a>Machtigingen

Als u IP-firewallregels voor de Azure SQL Server wilt maken en beheren, moet u een van de volgende rollen hebben:

- in de [rol SQL Server Inzender](../../role-based-access-control/built-in-roles.md#sql-server-contributor)
- in de [SQL Security Manager-rol](../../role-based-access-control/built-in-roles.md#sql-security-manager)
- de eigenaar van de resource die de Azure SQL server

## <a name="create-and-manage-ip-firewall-rules"></a>IP-firewallregels maken en beheren

U maakt de eerste firewallinstelling op serverniveau met behulp van de [Azure Portal](https://portal.azure.com/) of programmatisch met behulp [van Azure PowerShell](/powershell/module/az.sql), [Azure CLI](/cli/azure/sql/server/firewall-rule)of een Azure [REST API.](/rest/api/sql/firewallrules/createorupdate) U maakt en beheert aanvullende IP-firewallregels op serverniveau met behulp van deze methoden of Transact-SQL.

> [!IMPORTANT]
> IP-firewallregels op databaseniveau kunnen alleen worden gemaakt en beheerd met behulp van Transact-SQL.

Voor betere prestaties worden IP-firewallregels op serverniveau tijdelijk in de cache op databaseniveau opgeslagen. Zie [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql) als u de cache wilt vernieuwen.

> [!TIP]
> U kunt Database [Auditing gebruiken om](../../azure-sql/database/auditing-overview.md) firewallwijzigingen op server- en databaseniveau te controleren.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Gebruik de Azure Portal IP-firewallregels op serverniveau te beheren

Als u een IP-firewallregel op serverniveau in de Azure Portal, gaat u naar de overzichtspagina voor uw database of server.

> [!TIP]
> Zie Een database maken met behulp van de Azure Portal voor [een Azure Portal.](single-database-create-quickstart.md)

#### <a name="from-the-database-overview-page"></a>Op de overzichtspagina van de database

1. Als u een IP-firewallregel op serverniveau wilt instellen op de overzichtspagina van de database, selecteert u **Serverfirewall instellen** op de werkbalk, zoals in de volgende afbeelding wordt weergegeven.

    ![IP-firewallregel voor server](./media/firewall-configure/sql-database-server-set-firewall-rule.png)

    De pagina **Firewallinstellingen** voor de server wordt geopend.

2. Selecteer **IP van client toevoegen** op de werkbalk om het IP-adres toe te voegen van de computer die u gebruikt en selecteer vervolgens **Opslaan.** Er wordt een IP-firewallregel op serverniveau gemaakt voor uw huidige IP-adres.

    ![IP-firewallregel op serverniveau instellen](./media/firewall-configure/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>Op de overzichtspagina van de server

De overzichtspagina voor uw server wordt geopend. U ziet de volledig gekwalificeerde servernaam (zoals *mynewserver20170403.database.windows.net*) en opties voor verdere configuratie.

1. Als u vanaf deze pagina een regel op serverniveau wilt instellen, selecteert u **Firewall** in **het** menu Instellingen aan de linkerkant.

2. Selecteer **IP van client toevoegen** op de werkbalk om het IP-adres toe te voegen van de computer die u gebruikt en selecteer vervolgens **Opslaan.** Er wordt een IP-firewallregel op serverniveau gemaakt voor uw huidige IP-adres.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Transact-SQL gebruiken om IP-firewallregels te beheren

| Catalogusweergave of opgeslagen procedure | Niveau | Description |
| --- | --- | --- |
| [sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database) |Server |Geeft de huidige IP-firewallregels op serverniveau weer |
| [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database) |Server |Hiermee worden IP-firewallregels op serverniveau gemaakt of bijgewerkt |
| [sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database) |Server |Hiermee verwijdert u IP-firewallregels op serverniveau |
| [sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database) |Database |Geeft de huidige IP-firewallregels op databaseniveau weer |
| [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) |Database |Hiermee worden de IP-firewallregels op databaseniveau gemaakt of bijgewerkt |
| [sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database) |Databases |Hiermee verwijdert u IP-firewallregels op databaseniveau |

In het volgende voorbeeld worden de bestaande regels vergeleken, wordt een bereik van IP-adressen op de server *Contoso* mogelijk gemaakt en wordt een IP-firewallregel verwijderd:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Voeg vervolgens een IP-firewallregel op serverniveau toe.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Als u een IP-firewallregel op serverniveau wilt verwijderen, voert u de *sp_delete_firewall_rule* opgeslagen procedure uit. In het volgende voorbeeld wordt de regel *ContosoFirewallRule verwijderd:*

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>PowerShell gebruiken om IP-firewallregels op serverniveau te beheren

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle ontwikkeling is nu voor de Az.Sql-module. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de Az- en AzureRm-modules zijn aanzienlijk identiek.

| Cmdlet | Niveau | Description |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Server |Retourneert de huidige firewallregels op serverniveau |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Server |Maakt een nieuwe firewallregel op serverniveau |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Server |Werkt de eigenschappen van een bestaande firewallregel op serverniveau bij |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Server |Verwijdert firewallregels op serverniveau |

In het volgende voorbeeld wordt PowerShell gebruikt om een IP-firewallregel op serverniveau in te stellen:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```

> [!TIP]
> Geef $servername servernaam op en niet de volledig gekwalificeerde DNS-naam, bijvoorbeeld **mysqldbserver** in plaats van **mysqldbserver.database.windows.net**
>
> Zie Database maken - PowerShell en Een individuele database maken en een [IP-firewallregel](scripts/create-and-configure-database-powershell.md)op serverniveau configureren met behulp van PowerShell voor PowerShell voor [PowerShell-voorbeelden](powershell-script-content-guide.md) in de context van een quickstart.

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>CLI gebruiken om IP-firewallregels op serverniveau te beheren

| Cmdlet | Niveau | Description |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Server|Hiermee maakt u een IP-firewallregel voor de server|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Server|Hiermee worden de IP-firewallregels op een server vermeld|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Server|Toont de details van een IP-firewallregel|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Server|Een IP-firewallregel wordt bijgewerkt|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Server|Hiermee verwijdert u een IP-firewallregel|

In het volgende voorbeeld wordt CLI gebruikt om een IP-firewallregel op serverniveau in te stellen:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```

> [!TIP]
> Geef $servername servernaam op en niet de volledig gekwalificeerde DNS-naam, bijvoorbeeld **mysqldbserver** in plaats **van mysqldbserver.database.windows.net**
>
> Zie Database maken [- Azure CLI](az-cli-script-samples-content-guide.md) en Een individuele database maken en een [IP-firewallregel](scripts/create-and-configure-database-cli.md)op serverniveau configureren met behulp van de Azure CLI voor een CLI-voorbeeld in de context van een quickstart.

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Een ip REST API gebruiken voor het beheren van IP-firewallregels op serverniveau

| API | Niveau | Description |
| --- | --- | --- |
| [Firewallregels op een lijst zetten](/rest/api/sql/firewallrules/listbyserver) |Server |Geeft de huidige IP-firewallregels op serverniveau weer |
| [Firewallregels maken of bijwerken](/rest/api/sql/firewallrules/createorupdate) |Server |Hiermee worden IP-firewallregels op serverniveau gemaakt of bijgewerkt |
| [Firewallregels verwijderen](/rest/api/sql/firewallrules/delete) |Server |Hiermee verwijdert u IP-firewallregels op serverniveau |
| [Firewallregels op halen](/rest/api/sql/firewallrules/get) | Server | Haalt IP-firewallregels op serverniveau op |

## <a name="troubleshoot-the-database-firewall"></a>Problemen met de databasefirewall oplossen

Houd rekening met de volgende punten wanneer Azure SQL Database zich niet gedraagt zoals verwacht.

- **Lokale firewallconfiguratie:**

  Voordat uw computer toegang krijgt tot Azure SQL Database, moet u mogelijk een firewall-uitzondering op uw computer maken voor TCP-poort 1433. Als u verbindingen wilt maken binnen de grenzen van de Azure-cloud, moet u mogelijk extra poorten openen. Zie de sectie 'SQL Database: Outside vs inside' van Poorten boven [1433 voor ADO.NET 4.5](adonet-v12-develop-direct-route-ports.md)en Azure SQL Database.

- **Netwerkadresvertaling:**

  Vanwege Network Address Translation (NAT) kan het IP-adres dat door uw computer wordt gebruikt om verbinding te maken met Azure SQL Database anders zijn dan het IP-adres in de IP-configuratie-instellingen van uw computer. Als u het IP-adres wilt weergeven dat uw computer gebruikt om verbinding te maken met Azure:
    1. Meld u aan bij de portal.
    1. Ga naar het **tabblad Configureren** op de server die als host voor uw database wordt gebruikt.
    1. Het **IP-adres van de huidige** client wordt weergegeven in de sectie **Toegestane IP-adressen.** Selecteer **Toevoegen** bij **Toegestane IP-adressen** om deze computer toegang te geven tot de server.

- **Wijzigingen in de lijst met toegestane wijzigingen zijn nog niet van kracht:**

  Het kan vijf minuten duren voor wijzigingen in de configuratie van Azure SQL Database firewall van kracht zijn.

- **De aanmelding is niet geautoriseerd of er is een onjuist wachtwoord gebruikt:**

  Als een aanmelding geen machtigingen op de server heeft of als het wachtwoord onjuist is, wordt de verbinding met de server geweigerd. Het maken van een firewallinstelling biedt clients alleen de *mogelijkheid* om verbinding te maken met uw server. De client moet nog steeds de benodigde beveiligingsreferenties verstrekken. Zie Databasetoegang beheren en verlenen voor meer informatie over het voorbereiden [van aanmeldingen.](logins-create-manage.md)

- **Dynamisch IP-adres:**

  Als u een internetverbinding hebt die gebruikmaakt van dynamische IP-adressering en u problemen hebt bij het passeren van de firewall, kunt u een van de volgende oplossingen proberen:
  
  - Vraag uw internetprovider om het IP-adresbereik dat is toegewezen aan uw clientcomputers die toegang hebben tot de server. Voeg dat IP-adresbereik toe als een IP-firewallregel.
  - Haal in plaats daarvan statische IP-adressering op voor uw clientcomputers. Voeg de IP-adressen toe als IP-firewallregels.

## <a name="next-steps"></a>Volgende stappen

- Controleer of uw bedrijfsnetwerk inkomende communicatie toestaat vanuit de IP-adresbereiken (inclusief SQL-adresbereiken) die worden gebruikt door de Azure-datacenters. Mogelijk moet u deze IP-adressen toevoegen aan de lijst met toegestane adressen. Zie [Microsoft Azure IP-adresbereiken van datacenters.](https://www.microsoft.com/download/details.aspx?id=41653)  
- Zie onze quickstart over [het maken van een individuele database in Azure SQL Database](single-database-create-quickstart.md).
- Zie Client quickstart code samples to Azure SQL Database voor hulp bij het maken van verbinding met een database in Azure SQL Database vanuit opensource-toepassingen [of toepassingen van derden.](connect-query-content-reference-guide.md#libraries)
- Zie de sectie 'SQL Database: Buiten versus binnen' van Poorten boven [1433 voor ADO.NET 4.5](adonet-v12-develop-direct-route-ports.md) en SQL Database
- Zie Uw database beveiligen Azure SQL Database een overzicht [van de beveiliging van uw database.](security-overview.md)

<!--Image references-->
[1]: ./media/firewall-configure/sqldb-firewall-1.png
