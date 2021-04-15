---
title: SQL Insights inschakelen
description: SQL-inzichten inschakelen in Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: cfcb34b731855fd26ddad191b819e308406117cb
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478332"
---
# <a name="enable-sql-insights-preview"></a>SQL-inzichten inschakelen (preview)
In dit artikel wordt beschreven hoe u [SQL-inzichten kunt inschakelen om](sql-insights-overview.md) uw SQL-implementaties te bewaken. Bewaking wordt uitgevoerd vanaf een virtuele Azure-machine die verbinding maakt met uw SQL-implementaties en dynamische beheerweergaven (DMV's) gebruikt om bewakingsgegevens te verzamelen. U kunt bepalen welke gegevenssets worden verzameld en de frequentie van het verzamelen met behulp van een bewakingsprofiel.

## <a name="create-log-analytics-workspace"></a>Een Log Analytics-werkruimte maken
SQL Insights slaat de gegevens op in een of meer [Log Analytics-werkruimten.](../logs/data-platform-logs.md#log-analytics-workspaces)  Voordat u SQL Insights kunt inschakelen, moet u [een werkruimte](../logs/quick-create-workspace.md) maken of een bestaande werkruimte selecteren. Eén werkruimte kan worden gebruikt met meerdere bewakingsprofielen, maar de werkruimte en profielen moeten zich in dezelfde Azure-regio bevinden. Als u de functies in SQL Insights wilt inschakelen en openen, moet u de rol [Log Analytics-inzender](../logs/manage-access.md) hebben in de werkruimte. 

## <a name="create-monitoring-user"></a>Bewakingsgebruiker maken 
U hebt een gebruiker nodig voor de SQL-implementaties die u wilt bewaken. Volg de onderstaande procedures voor verschillende typen SQL-implementaties.

### <a name="azure-sql-database"></a>Azure SQL-database
Open Azure SQL Database met [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) of [Query-editor (preview)](../../azure-sql/database/connect-query-portal.md) in de Azure Portal.

Voer het volgende script uit om een gebruiker met de vereiste machtigingen te maken. Vervang *user* door een gebruikersnaam en *mystrongpassword* door een wachtwoord.

```sql
CREATE USER [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW DATABASE STATE TO [user]; 
GO 
```

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-script.png" alt-text="Telegraf-gebruikersscript maken." lightbox="media/sql-insights-enable/telegraf-user-database-script.png":::

Controleer of de gebruiker is gemaakt.

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-verify.png" alt-text="Verifieer het telegraf-gebruikersscript." lightbox="media/sql-insights-enable/telegraf-user-database-verify.png":::

```sql
select name as username,
       create_date,
       modify_date,
       type_desc as type,
       authentication_type_desc as authentication_type
from sys.database_principals
where type not in ('A', 'G', 'R', 'X')
       and sid is not null
order by username
```

### <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance
Meld u aan bij Azure SQL Managed Instance en gebruik [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) hulpprogramma om het volgende script uit te voeren om de controlegebruiker te maken met de benodigde machtigingen. Vervang *user* door een gebruikersnaam en *mystrongpassword* door een wachtwoord.

 
```sql
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO 
```

### <a name="sql-server"></a>SQL Server
Meld u aan bij uw virtuele Azure-machine met SQL Server en gebruik [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) of een soortgelijk hulpprogramma om het volgende script uit te voeren om de controlegebruiker te maken met de benodigde machtigingen. Vervang *user* door een gebruikersnaam en *mystrongpassword* door een wachtwoord.

 
```sql
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO
```

Controleer of de gebruiker is gemaakt.

```sql
select name as username,
       create_date,
       modify_date,
       type_desc as type,
from sys.server_principals
where type not in ('A', 'G', 'R', 'X')
       and sid is not null
order by username
```

## <a name="create-azure-virtual-machine"></a>Virtuele Azure-machine maken 
U moet een of meer virtuele Azure-machines maken die worden gebruikt voor het verzamelen van gegevens voor het bewaken van SQL.  

> [!NOTE]
>  De [bewakingsprofielen](#create-sql-monitoring-profile) geven aan welke gegevens u wilt verzamelen van de verschillende typen SQL die u wilt bewaken. Aan elke bewakings-virtuele machine kan slechts één bewakingsprofiel zijn gekoppeld. Als u meerdere bewakingsprofielen nodig hebt, moet u voor elke virtuele machine een virtuele machine maken.

### <a name="azure-virtual-machine-requirements"></a>Vereisten voor virtuele Azure-machines
Voor de virtuele Azure-machines gelden de volgende vereisten.

- Besturingssysteem: Ubuntu 18.04 
- Aanbevolen grootten voor virtuele Azure-machines: Standard_B2s (2 CPU's, 4 GiB-geheugen) 
- Ondersteunde regio's: [elke regio die wordt ondersteund door de Azure Monitor agent](../agents/azure-monitor-agent-overview.md#supported-regions)

> [!NOTE]
> De Standard_B2s virtuele machine (2 cpu's, 4 GiB geheugen) ondersteunt maximaal 100 verbindingsreeksen. Wijs niet meer dan 100 verbindingen toe aan één virtuele machine.

Afhankelijk van de netwerkinstellingen van uw SQL-resources, moeten de virtuele machines mogelijk in hetzelfde virtuele netwerk worden geplaatst als uw SQL-resources, zodat ze netwerkverbindingen kunnen maken om bewakingsgegevens te verzamelen.  

## <a name="configure-network-settings"></a>Netwerkinstellingen configureren
Elk type SQL biedt methoden voor uw virtuele bewakingsmachine om veilig toegang te krijgen tot SQL.  In de onderstaande secties worden de opties beschreven op basis van het type SQL.

### <a name="azure-sql-databases"></a>Azure SQL Databases  

SQL Insights ondersteunt toegang tot uw Azure SQL Database via het openbare eindpunt en vanuit het virtuele netwerk.

Voor toegang via het openbare eindpunt voegt u een regel toe onder de pagina **Firewallinstellingen** en de sectie [IP-firewallinstellingen.](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-firewall-rules)  Voor het opgeven van toegang vanuit een virtueel netwerk kunt u [firewallregels](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#virtual-network-firewall-rules) voor virtuele netwerken instellen en de servicetags instellen die vereist zijn voor [de Azure Monitor agent.](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview#networking)  [In dit artikel](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-vs-virtual-network-firewall-rules) worden de verschillen tussen deze twee typen firewallregels beschreven.

:::image type="content" source="media/sql-insights-enable/set-server-firewall.png" alt-text="Serverfirewall instellen" lightbox="media/sql-insights-enable/set-server-firewall.png":::

:::image type="content" source="media/sql-insights-enable/firewall-settings.png" alt-text="Firewallinstellingen." lightbox="media/sql-insights-enable/firewall-settings.png":::


### <a name="azure-sql-managed-instances"></a>Azure SQL Managed Instances 

Als uw virtuele machine voor bewaking zich in hetzelfde VNet als uw SQL MI-resources zal hebben, ziet u Verbinding maken [binnen hetzelfde VNet.](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-the-same-vnet) Als uw virtuele machine voor bewaking zich in het andere VNet dan uw SQL MI-resources zal hebben, ziet u Verbinding maken [binnen een ander VNet.](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-a-different-vnet)


### <a name="azure-virtual-machine-and-azure-sql-virtual-machine"></a>Virtuele Azure-machine Azure SQL virtuele machine  
Als uw virtuele machine voor bewaking zich in hetzelfde VNet als de resources van uw virtuele SQL-machine, ziet u Verbinding maken met SQL Server [in een virtueel netwerk.](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-within-a-virtual-network) Als uw virtuele machine voor bewaking zich in het andere VNet dan de resources van uw virtuele SQL-machine zal hebben, gaat u naar Verbinding maken met SQL Server [via internet.](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-over-the-internet)

## <a name="store-monitoring-password-in-key-vault"></a>Bewakingswachtwoord opslaan in Key Vault
U moet de wachtwoorden voor sql-gebruikersverbindingen opslaan in een Key Vault in plaats van ze rechtstreeks in te stellen in de verbindingsreeksen van uw bewakingsprofiel.

Wanneer u uw profiel voor SQL-bewaking in de instellingen inwerkt, hebt u een van de volgende machtigingen nodig voor de Key Vault resource die u wilt gebruiken:

- Microsoft.Authorization/roleAssignments/write 
- Microsoft.Authorization/roleAssignments/delete-machtigingen, zoals Beheerder voor gebruikerstoegang of Eigenaar 

Er wordt automatisch een nieuw toegangsbeleid gemaakt als onderdeel van het maken van uw SQL Monitoring profiel dat gebruikmaakt van de Key Vault die u hebt opgegeven. Gebruik *Toegang vanuit alle netwerken toestaan voor Key Vault* Netwerkinstellingen.


## <a name="create-sql-monitoring-profile"></a>SQL-bewakingsprofiel maken
Open SQL-inzichten door **SQL (preview) te selecteren** in de sectie **Inzichten** van het **Azure Monitor** menu in de Azure Portal. Klik **op Nieuw profiel maken.** 

:::image type="content" source="media/sql-insights-enable/create-new-profile.png" alt-text="Maak een nieuw profiel." lightbox="media/sql-insights-enable/create-new-profile.png":::

In het profiel worden de gegevens opgeslagen die u wilt verzamelen van uw SQL-systemen.  Deze heeft specifieke instellingen voor: 

- Azure SQL Database 
- Azure SQL Managed Instances 
- SQL Server uitgevoerd op virtuele machines  

U kunt bijvoorbeeld één profiel maken met de naam *SQL Production* en een ander met de naam *SQL Staging* met verschillende instellingen voor de frequentie van gegevensverzameling, welke gegevens moeten worden verzameld en naar welke werkruimte de gegevens moeten worden verzenden. 

Het profiel wordt opgeslagen als een regel [voor gegevensverzameling](../agents/data-collection-rule-overview.md) in het abonnement en de resourcegroep die u selecteert. Voor elk profiel is het volgende nodig:

- Naam. Kan niet worden bewerkt nadat deze is gemaakt.
- Locatie. Dit is een Azure-regio.
- Log Analytics-werkruimte voor het opslaan van de bewakingsgegevens.
- Verzamelingsinstellingen voor de frequentie en het type sql-bewakingsgegevens dat moet worden verzameld.

> [!NOTE]
> De locatie van het profiel moet zich op dezelfde locatie bevinden als de Log Analytics-werkruimte waar u de bewakingsgegevens naar wilt verzenden.


:::image type="content" source="media/sql-insights-enable/profile-details.png" alt-text="Profieldetails." lightbox="media/sql-insights-enable/profile-details.png":::

Klik **op Bewakingsprofiel** maken nadat u de details voor uw bewakingsprofiel hebt ingevoerd. Het kan tot een minuut duren voor het profiel is geïmplementeerd.  Als u het nieuwe profiel niet  ziet in de keuzelijst met invoervak Bewakingsprofiel, klikt u op de knop Vernieuwen en wordt het weergegeven zodra de implementatie is voltooid.  Nadat u het nieuwe profiel hebt geselecteerd, selecteert u **het** tabblad Profiel beheren om een bewakingsmachine toe te voegen die aan het profiel wordt gekoppeld.

### <a name="add-monitoring-machine"></a>Bewakingsmachine toevoegen
Selecteer **Bewakingsmachine toevoegen om** een contextvenster te openen om de virtuele machine te kiezen die u wilt instellen om uw SQL-exemplaren te bewaken en de verbindingsreeksen op te geven.

Selecteer het abonnement en de naam van uw virtuele bewakingsmachine. Als u Key Vault gebruikt om uw wachtwoord voor de bewakingsgebruiker op te slaan, selecteert u de Key Vault-resources met deze geheimen en voert u de URL en geheime naam in die moeten worden gebruikt in de verbindingsreeksen. Zie de volgende sectie voor meer informatie over het identificeren van de connection string voor verschillende SQL-implementaties.


:::image type="content" source="media/sql-insights-enable/add-monitoring-machine.png" alt-text="Bewakingsmachine toevoegen." lightbox="media/sql-insights-enable/add-monitoring-machine.png":::


### <a name="add-connection-strings"></a>Verbindingsreeksen toevoegen 
De connection string geeft de gebruikersnaam op die SQL Insights moet gebruiken bij het aanmelden bij SQL om de dynamische beheerweergaven uit te voeren. Als u een wachtwoord gebruikt Key Vault het wachtwoord voor de controlegebruiker op te slaan, geeft u de URL en naam op van het geheim dat u wilt gebruiken. 

De verbindingsreeks varieert voor elk type SQL-resource:

#### <a name="azure-sql-databases"></a>Azure SQL Databases 
Voer de connection string in het formulier in:

```
sqlAzureConnections": [ 
   "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=$username;Password=$password;" 
}
```

Haal de details op uit het **menu-item Verbindingsreeksen** voor de database.

:::image type="content" source="media/sql-insights-enable/connection-string-sql-database.png" alt-text="SQL database connection string" lightbox="media/sql-insights-enable/connection-string-sql-database.png":::

Als u een leesbare secundaire wilt bewaken, moet u de sleutelwaarde `ApplicationIntent=ReadOnly` in de connection string. SQL Insights ondersteunt het bewaken van één secundaire database. De verzamelde gegevens worden getagd om de primaire of secundaire gegevens weer te geven. 


#### <a name="azure-virtual-machines-running-sql-server"></a>Virtuele Azure-machines met SQL Server 
Voer de connection string in het formulier in:

```
"sqlVmConnections": [ 
   "Server=MyServerIPAddress;Port=1433;User Id=$username;Password=$password;" 
] 
```

Als uw virtuele machine voor bewaking zich in hetzelfde VNET, gebruikt u het privé-IP-adres van de server.  Gebruik anders het openbare IP-adres. Als u een virtuele Azure SQL machine gebruikt, kunt u hier zien welke poort u moet gebruiken op **de pagina** Beveiliging voor de resource.

:::image type="content" source="media/sql-insights-enable/sql-vm-security.png" alt-text="Beveiliging van virtuele SQL-machines" lightbox="media/sql-insights-enable/sql-vm-security.png":::


### <a name="azure-sql-managed-instances"></a>Azure SQL Managed Instances 
Voer de connection string in het formulier in:

```
"sqlManagedInstanceConnections": [ 
      "Server= mysqlserver.database.windows.net;Port=1433;User Id=$username;Password=$password;", 
    ] 
```
Haal de details op uit het **menu-item Verbindingsreeksen** voor het beheerde exemplaar.


:::image type="content" source="media/sql-insights-enable/connection-string-sql-managed-instance.png" alt-text="SQL Managed Instance connection string" lightbox="media/sql-insights-enable/connection-string-sql-managed-instance.png":::

Als u een leesbare secundaire wilt bewaken, moet u de sleutelwaarde `ApplicationIntent=ReadOnly` in de connection string. SQL Insights ondersteunt de bewaking van één secundaire database en de verzamelde gegevens worden getagd om de primaire of secundaire gegevens weer te geven. 


## <a name="monitoring-profile-created"></a>Bewakingsprofiel gemaakt 

Selecteer **Bewakings-virtuele machine toevoegen om** de virtuele machine te configureren voor het verzamelen van gegevens van uw SQL-resources. Ga niet terug naar het **tabblad** Overzicht.  In een paar minuten moet de kolom Status worden gewijzigd in 'Verzamelen'. Als het goed is, ziet u de gegevens voor de SQL-resources die u wilt bewaken.

Zie Problemen met [SQL-inzichten](sql-insights-troubleshoot.md) oplossen om het probleem te identificeren als u geen gegevens ziet. 

:::image type="content" source="media/sql-insights-enable/profile-created.png" alt-text="Profiel gemaakt" lightbox="media/sql-insights-enable/profile-created.png":::

> [!NOTE]
> Als u uw bewakingsprofiel of de verbindingsreeksen op uw bewakings-VM's moet bijwerken, kunt u dit doen via het tabblad SQL Insights **Profiel** beheren.  Zodra uw updates zijn opgeslagen, worden de wijzigingen in ongeveer 5 minuten toegepast.

## <a name="next-steps"></a>Volgende stappen

- Zie [Problemen met SQL-inzichten oplossen](sql-insights-troubleshoot.md) als SQL-inzichten niet goed werken nadat deze zijn ingeschakeld.
