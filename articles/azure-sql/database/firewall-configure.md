---
title: IP-firewallregels
description: Configureer IP-firewall regels op server niveau voor een data base in Azure SQL Database of Azure Synapse Analytics firewall. Toegang beheren en IP-firewall regels op database niveau configureren voor SQL Database.
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
ms.openlocfilehash: bbad7dcaa1d92df4969c88e4ba86a62987509e39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98632796"
---
# <a name="azure-sql-database-and-azure-synapse-ip-firewall-rules"></a>Azure SQL Database-en Azure Synapse IP-firewall regels
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Wanneer u een nieuwe server maakt in Azure SQL Database of Azure Synapse Analytics met de naam *MySqlServer*, bijvoorbeeld een firewall op server niveau, blokkeert de toegang tot het open bare eind punt voor de server (die toegankelijk is op *MySqlServer.database.Windows.net*). *SQL database* wordt gebruikt om te verwijzen naar zowel SQL database als Azure Synapse Analytics.

> [!IMPORTANT]
> Dit artikel is *niet* van toepassing op *Azure SQL Managed Instance*. Zie [uw toepassing verbinden met Azure SQL Managed instance](../managed-instance/connect-application-instance.md)voor meer informatie over de netwerk configuratie.
>
> Azure Synapse ondersteunt alleen IP-firewall regels op server niveau. Het biedt geen ondersteuning voor IP-firewall regels op database niveau.

## <a name="how-the-firewall-works"></a>Hoe de firewall werkt

Verbindings pogingen via internet en Azure moeten door de firewall worden door gegeven voordat ze uw server of data base bereiken, zoals in het volgende diagram wordt weer gegeven.

   ![Diagram firewall configuratie][1]

### <a name="server-level-ip-firewall-rules"></a>IP-firewallregels op serverniveau

Met deze regels kunnen clients toegang krijgen tot uw hele server, dat wil zeggen, alle databases die door de server worden beheerd. De regels worden opgeslagen in de Data Base *Master* . U kunt voor een server maximaal 128 firewallregels op serverniveau opgeven. Als u de instelling **Azure-Services en-resources toegang tot deze server toestaan** hebt ingeschakeld, telt dit als een enkele firewall regel voor de-server.
  
U kunt IP-firewall regels op server niveau configureren met behulp van de instructies Azure Portal, Power shell of Transact-SQL.

- Als u de portal of Power shell wilt gebruiken, moet u de eigenaar van het abonnement of een mede werker van het abonnement zijn.
- Als u Transact-SQL wilt gebruiken, moet u verbinding maken met de *hoofd* database als de principal-aanmelding op server niveau of als de beheerder van Azure Active Directory. (Een IP-firewall regel op server niveau moet eerst worden gemaakt door een gebruiker met machtigingen op Azure-niveau.)

> [!NOTE]
> Tijdens het maken van een nieuwe logische SQL-Server vanuit het Azure Portal, is de instelling **Azure-Services en-resources voor toegang tot deze server** standaard ingesteld op **Nee**.

### <a name="database-level-ip-firewall-rules"></a>IP-firewall regels op database niveau

Met IP-firewall regels op database niveau kunnen clients toegang krijgen tot bepaalde (beveiligde) data bases. U maakt de regels voor elke Data Base (met inbegrip van de *hoofd* database), en deze worden opgeslagen in de afzonderlijke data base.
  
- U kunt alleen IP-firewall regels op database niveau maken en beheren voor hoofd-en gebruikers databases met behulp van Transact-SQL-instructies en alleen nadat u de eerste firewall op server niveau hebt geconfigureerd.
- Als u een IP-adres bereik opgeeft in de IP-firewall regel op database niveau buiten het bereik van de IP-firewall regel op server niveau, hebben alleen clients met IP-adressen in het bereik op database niveau toegang tot de data base.
- U kunt Maxi maal 128 IP-firewall regels op database niveau voor een Data Base hebben. Zie het voor beeld verderop in dit artikel voor meer informatie over het configureren van IP-firewall regels op database niveau en Zie [sp_set_database_firewall_rule (Azure SQL database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database).

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Aanbevelingen voor het instellen van firewall regels

U wordt aangeraden IP-firewall regels op database niveau te gebruiken wanneer dit mogelijk is. Dit gebruik verbetert de beveiliging en maakt uw database draagbaarder. Gebruik IP-firewall regels op server niveau voor beheerders. Gebruik deze ook wanneer u veel data bases hebt die dezelfde toegangs vereisten hebben en u niet elke Data Base afzonderlijk wilt configureren.

> [!NOTE]
> Voor meer informatie over draagbare databases in de context van bedrijfscontinuïteit raadpleegt u [Authentication requirements for disaster recovery](active-geo-replication-security-configure.md) (Verificatievereisten voor herstel na noodgevallen).

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>IP-firewallregels op server- en databaseniveau

*Moeten gebruikers van een Data Base volledig worden geïsoleerd van een andere data base?*

Zo *Ja*, gebruik IP-firewall regels op database niveau om toegang te verlenen. Deze methode vermijdt het gebruik van IP-firewall regels op server niveau, waardoor toegang via de firewall tot alle data bases wordt toegestaan. Dit vermindert de diepte van uw verdedigings.

*Hebben gebruikers op de IP-adressen toegang tot alle data bases nodig?*

Zo *Ja*, gebruik IP-firewall regels op server niveau om het aantal keren te beperken dat u de IP-firewall regels moet configureren.

*Heeft de persoon of het team dat de IP-firewall regels configureert alleen toegang via de Azure Portal, Power shell of de REST API?*

Als dat het geval is, moet u IP-firewall regels op server niveau gebruiken. IP-firewall regels op database niveau kunnen alleen worden geconfigureerd via Transact-SQL.  

*Is de persoon die of het team dat de IP-firewall regels configureert die geen machtiging op hoog niveau hebben op database niveau?*

Als dit het geval is, gebruikt u IP-firewall regels op server niveau. U hebt ten minste de machtiging *beheer database* op database niveau nodig om IP-firewall regels op database niveau te configureren via Transact-SQL.  

*Beheert de persoon of het team die de IP-firewall regels configureert of controleert, worden de IP-firewall regels voor veel (mogelijk honderden) data bases centraal beheerd?*

In dit scenario worden aanbevolen procedures bepaald aan de hand van uw behoeften en omgeving. IP-firewall regels op server niveau zijn mogelijk gemakkelijker te configureren, maar met scripts kunnen regels op database niveau worden geconfigureerd. En zelfs als u IP-firewall regels op server niveau gebruikt, moet u mogelijk IP-firewall regels op database niveau controleren om te zien of gebruikers met de machtiging *beheren* voor de data base IP-firewall regels op database niveau maken.

*Kan ik een combi natie van IP-firewall regels op server niveau en op database niveau gebruiken?*

Ja. Sommige gebruikers, zoals beheerders, hebben mogelijk IP-firewall regels op server niveau nodig. Andere gebruikers, zoals gebruikers van een database toepassing, kunnen mogelijk IP-firewall regels op database niveau hebben.

### <a name="connections-from-the-internet"></a>Verbindingen van Internet

Wanneer een computer via Internet verbinding probeert te maken met uw server, controleert de firewall eerst het oorspronkelijke IP-adres van de aanvraag met de IP-firewall regels op database niveau voor de data base die de verbindings aanvragen.

- Als het adres zich binnen een bereik bevindt dat is opgegeven in de IP-firewall regels op database niveau, wordt de verbinding verleend aan de data base die de regel bevat.
- Als het adres zich niet in een bereik van de IP-firewall regels op database niveau bevindt, controleert de firewall de IP-firewall regels op server niveau. Als het adres zich binnen een bereik bevindt dat op de IP-firewall regels op server niveau is ingesteld, wordt de verbinding verleend. IP-firewall regels op server niveau zijn van toepassing op alle data bases die worden beheerd door de server.  
- Als het adres zich niet in een bereik bevindt dat in een van de IP-firewall regels op database niveau of op server niveau is, mislukt de verbindings aanvraag.

> [!NOTE]
> Om toegang te krijgen tot Azure SQL Database vanaf uw lokale computer, moet u ervoor zorgen dat de firewall op uw netwerk en lokale computer uitgaande communicatie toestaat op TCP-poort 1433.

### <a name="connections-from-inside-azure"></a>Verbindingen van binnen Azure

Azure-verbindingen moeten zijn ingeschakeld om toepassingen die in Azure worden gehost, toe te staan om verbinding te maken met uw SQL Server. Als u Azure-verbindingen wilt inschakelen, moet er een firewall regel zijn met de begin-en eind-IP-adressen die zijn ingesteld op 0.0.0.0.

Wanneer een toepassing van Azure probeert verbinding te maken met de server, controleert de firewall of Azure-verbindingen zijn toegestaan door te controleren of deze firewall regel bestaat. Dit kan rechtstreeks op de Blade Azure Portal worden ingeschakeld door de instellingen voor **Azure-Services en-resources voor toegang tot deze server** **in te scha kelen in de** **firewalls en virtuele netwerken** . Als wordt ingesteld op ON maakt een regel voor binnenkomende Firewall voor IP 0.0.0.0-0.0.0.0 met de naam **AllowAllWindowsIP**. Gebruik Power shell of de Azure CLI om een firewall regel te maken met de begin-en eind-IP-adressen die zijn ingesteld op 0.0.0.0 als u de portal niet wilt gebruiken. 

> [!IMPORTANT]
> Met deze optie configureert u de firewall om alle verbindingen van Azure toe te staan, met inbegrip van verbindingen van de abonnementen van andere klanten. Als u deze optie selecteert, moet u ervoor zorgen dat uw aanmelding en gebruikers machtigingen alleen toegang tot geautoriseerde gebruikers beperken.

## <a name="permissions"></a>Machtigingen

Als u IP-firewallregels voor de Azure SQL Server wilt maken en beheren, moet u een van de volgende rollen hebben:

- in de rol [inzender SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor)
- in de rol [SQL Security Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager)
- de eigenaar van de resource die de Azure-SQL Server bevat

## <a name="create-and-manage-ip-firewall-rules"></a>IP-firewallregels maken en beheren

U kunt de eerste firewall instelling op server niveau maken met behulp van de [Azure Portal](https://portal.azure.com/) of programmatisch met behulp van [Azure POWERSHELL](/powershell/module/az.sql), [Azure CLI](/cli/azure/sql/server/firewall-rule)of een Azure- [rest API](/rest/api/sql/firewallrules/createorupdate). U maakt en beheert extra IP-firewall regels op server niveau met behulp van deze methoden of Transact-SQL.

> [!IMPORTANT]
> IP-firewall regels op database niveau kunnen alleen worden gemaakt en beheerd met behulp van Transact-SQL.

Voor betere prestaties worden IP-firewallregels op serverniveau tijdelijk in de cache op databaseniveau opgeslagen. Zie [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql) als u de cache wilt vernieuwen.

> [!TIP]
> U kunt [database controle](../../azure-sql/database/auditing-overview.md) gebruiken om wijzigingen in de firewall op server niveau en op database niveau te controleren.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>De Azure Portal gebruiken om IP-firewall regels op server niveau te beheren

Als u een IP-firewall regel op server niveau wilt instellen in de Azure Portal, gaat u naar de pagina overzicht voor uw data base of uw server.

> [!TIP]
> Zie [een Data Base maken met behulp van de Azure Portal](single-database-create-quickstart.md)voor een zelf studie.

#### <a name="from-the-database-overview-page"></a>Op de pagina overzicht van data base

1. Als u een IP-firewall regel op server niveau wilt instellen op de overzichts pagina van de data base, selecteert u **Server firewall instellen** op de werk balk, zoals wordt weer gegeven in de volgende afbeelding.

    ![Server-IP-firewall regel](./media/firewall-configure/sql-database-server-set-firewall-rule.png)

    De pagina **Firewallinstellingen** voor de server wordt geopend.

2. Selecteer **client-IP toevoegen** op de werk balk om het IP-adres van de computer die u gebruikt toe te voegen en selecteer vervolgens **Opslaan**. Er wordt een IP-firewall regel op server niveau gemaakt voor uw huidige IP-adres.

    ![IP-firewall regel op server niveau instellen](./media/firewall-configure/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>Op de pagina overzicht van server

De overzichts pagina voor de server wordt geopend. De volledig gekwalificeerde server naam (zoals *mynewserver20170403.database.Windows.net*) wordt weer gegeven en biedt opties voor verdere configuratie.

1. Als u op deze pagina een regel op server niveau wilt instellen, selecteert u in het menu **instellingen** aan de linkerkant de optie **firewall** .

2. Selecteer **client-IP toevoegen** op de werk balk om het IP-adres van de computer die u gebruikt toe te voegen en selecteer vervolgens **Opslaan**. Er wordt een IP-firewall regel op server niveau gemaakt voor uw huidige IP-adres.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Transact-SQL gebruiken voor het beheren van IP-firewall regels

| Catalogus weergave of opgeslagen procedure | Niveau | Description |
| --- | --- | --- |
| [sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database) |Server |Hiermee worden de huidige IP-firewall regels op server niveau weer gegeven |
| [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database) |Server |Hiermee worden IP-firewall regels op server niveau gemaakt of bijgewerkt |
| [sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database) |Server |Hiermee worden IP-firewall regels op server niveau verwijderd |
| [sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database) |Database |Hiermee worden de huidige IP-firewall regels op database niveau weer gegeven |
| [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) |Database |Hiermee worden de IP-firewall regels op database niveau gemaakt of bijgewerkt |
| [sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database) |Databases |Hiermee verwijdert u IP-firewall regels op database niveau |

In het volgende voor beeld worden de bestaande regels beoordeeld, wordt een bereik van IP-adressen op de server *Contoso* ingeschakeld en wordt een IP-firewall regel verwijderd:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Voeg vervolgens een IP-firewall regel op server niveau toe.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Als u een IP-firewall regel op server niveau wilt verwijderen, voert u de opgeslagen *sp_delete_firewall_rule* procedure uit. In het volgende voor beeld wordt de regel *ContosoFirewallRule* verwijderd:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>Power shell gebruiken voor het beheren van IP-firewall regels op server niveau

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle ontwikkeling is nu voor de module AZ. SQL. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de modules AZ en AzureRm zijn aanzienlijk identiek.

| Cmdlet | Niveau | Description |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Server |Retourneert de huidige firewallregels op serverniveau |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Server |Maakt een nieuwe firewallregel op serverniveau |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Server |Werkt de eigenschappen van een bestaande firewallregel op serverniveau bij |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Server |Verwijdert firewallregels op serverniveau |

In het volgende voor beeld wordt Power shell gebruikt om een IP-firewall regel op server niveau in te stellen:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```

> [!TIP]
> Voor $servername geeft u de server naam op en niet de volledig gekwalificeerde DNS-naam, bijvoorbeeld **mysqldbserver** in plaats van **mysqldbserver.database.Windows.net** opgeven
>
> Voor Power shell-voor beelden in de context van een Snelstartgids raadpleegt u [Create DB-Power shell](powershell-script-content-guide.md) en [een afzonderlijke data base maken en een IP-firewall regel op server niveau configureren met behulp van Power shell](scripts/create-and-configure-database-powershell.md).

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>CLI gebruiken voor het beheren van IP-firewall regels op server niveau

| Cmdlet | Niveau | Description |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Server|Hiermee maakt u een server-IP-firewall regel|
|[AZ SQL Server firewall-Rule List](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Server|Een lijst met de IP-firewall regels op een server|
|[AZ SQL Server firewall-Rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Server|Toont de details van een IP-firewall regel|
|[AZ SQL Server firewall-Rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Server|Hiermee wordt een IP-firewall regel bijgewerkt|
|[AZ SQL Server firewall-Rule Delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Server|Hiermee verwijdert u een IP-firewall regel|

In het volgende voor beeld wordt CLI gebruikt om een IP-firewall regel op server niveau in te stellen:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```

> [!TIP]
> Voor $servername geeft u de server naam op en niet de volledig gekwalificeerde DNS-naam, bijvoorbeeld **mysqldbserver** in plaats van **mysqldbserver.database.Windows.net** opgeven
>
> Zie [Create DB-Azure cli](az-cli-script-samples-content-guide.md) en een [enkele data base maken en een IP-firewall regel op server niveau configureren met behulp van de Azure CLI](scripts/create-and-configure-database-cli.md)voor een CLI-voor beeld in de context van een Snelstartgids.

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Een REST API gebruiken om IP-firewall regels op server niveau te beheren

| API | Niveau | Description |
| --- | --- | --- |
| [Firewall regels weer geven](/rest/api/sql/firewallrules/listbyserver) |Server |Hiermee worden de huidige IP-firewall regels op server niveau weer gegeven |
| [Firewall regels maken of bijwerken](/rest/api/sql/firewallrules/createorupdate) |Server |Hiermee worden IP-firewall regels op server niveau gemaakt of bijgewerkt |
| [Firewall regels verwijderen](/rest/api/sql/firewallrules/delete) |Server |Hiermee worden IP-firewall regels op server niveau verwijderd |
| [Firewall regels ophalen](/rest/api/sql/firewallrules/get) | Server | Hiermee worden IP-firewall regels op server niveau opgehaald |

## <a name="troubleshoot-the-database-firewall"></a>Problemen met de databasefirewall oplossen

Houd rekening met de volgende punten wanneer de toegang tot Azure SQL Database niet werkt zoals verwacht.

- **Lokale firewall configuratie:**

  Voordat de computer toegang kan krijgen tot Azure SQL Database, moet u mogelijk een firewall-uitzonde ring op uw computer maken voor TCP-poort 1433. Als u verbindingen wilt maken binnen de grenzen van de Azure-Cloud, moet u mogelijk extra poorten openen. Zie voor meer informatie de sectie "SQL Database: buiten de binnen" van de [poorten na 1433 voor ADO.NET 4,5 en Azure SQL database](adonet-v12-develop-direct-route-ports.md).

- **Netwerkadresomzetting:**

  Vanwege Network Address Translation (NAT) is het IP-adres dat door uw computer wordt gebruikt om verbinding te maken met Azure SQL Database, mogelijk anders dan het IP-adres in de IP-configuratie-instellingen van uw computer. Het IP-adres weer geven dat door uw computer wordt gebruikt om verbinding te maken met Azure:
    1. Meld u aan bij de portal.
    1. Ga naar het tabblad **configureren** op de server die als host fungeert voor uw data base.
    1. Het **huidige client-IP-adres** wordt weer gegeven in de sectie **toegestane IP-adressen** . Selecteer **toevoegen** voor **toegestane IP-adressen** , zodat deze computer toegang heeft tot de server.

- **Wijzigingen in de acceptatie lijst zijn nog niet doorgevoerd:**

  Het kan een vertraging van vijf minuten duren voordat wijzigingen in de configuratie van de Azure SQL Database firewall van kracht worden.

- **De aanmelding is niet geautoriseerd of er is een onjuist wacht woord gebruikt:**

  Als een aanmelding geen machtigingen heeft op de server of het wacht woord onjuist is, wordt de verbinding met de server geweigerd. Het maken van een firewall instelling biedt clients alleen de *mogelijkheid* om verbinding te maken met uw server. De client moet nog steeds de benodigde beveiligings referenties opgeven. Zie [toegang tot data base beheren en verlenen](logins-create-manage.md)voor meer informatie over het voorbereiden van aanmeldingen.

- **Dynamisch IP-adres:**

  Als u een Internet verbinding hebt die gebruikmaakt van dynamische IP-adres sering en u problemen hebt via de firewall, kunt u een van de volgende oplossingen proberen:
  
  - Vraag uw Internet provider om het IP-adres bereik dat is toegewezen aan de client computers die toegang hebben tot de server. Voeg dat IP-adres bereik toe als een IP-firewall regel.
  - Neem in plaats daarvan statische IP-adressen op voor uw client computers. Voeg de IP-adressen als IP-firewall regels toe.

## <a name="next-steps"></a>Volgende stappen

- Controleer of de bedrijfs netwerk omgeving binnenkomende communicatie toestaat van de compute IP-adresbereiken (inclusief SQL-bereiken) die worden gebruikt door de Azure-data centers. Mogelijk moet u deze IP-adressen toevoegen aan de acceptatie lijst. Zie [Microsoft Azure Data Center IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653).  
- Bekijk onze Snelstartgids over het [maken van één data base in Azure SQL database](single-database-create-quickstart.md).
- Voor hulp bij het maken van verbinding met een data base in Azure SQL Database van open-source of toepassingen van derden raadpleegt [u Quick Start code samples to Azure SQL database](connect-query-content-reference-guide.md#libraries).
- Voor informatie over aanvullende poorten die u mogelijk moet openen, raadpleegt u de sectie ' SQL Database: buiten de binnenkant ' van poorten van meer [dan 1433 voor ADO.NET 4,5 en SQL database](adonet-v12-develop-direct-route-ports.md)
- Zie [uw data base beveiligen](security-overview.md)voor een overzicht van Azure SQL database beveiliging.

<!--Image references-->
[1]: ./media/firewall-configure/sqldb-firewall-1.png
