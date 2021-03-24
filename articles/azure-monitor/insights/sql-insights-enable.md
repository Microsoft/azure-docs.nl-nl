---
title: SQL Insights inschakelen
description: SQL Insights in Azure Monitor inschakelen
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: ac37a6de4197d5e7cae20d2bde759b98fe474047
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889617"
---
# <a name="enable-sql-insights-preview"></a>SQL Insights inschakelen (preview-versie)
In dit artikel wordt beschreven hoe u [SQL Insights](sql-insights-overview.md) inschakelt om uw SQL-implementaties te bewaken. Bewaking wordt uitgevoerd vanaf een virtuele machine van Azure die verbinding maakt met uw SQL-implementaties en gebruikmaakt van dynamische beheer weergaven (Dmv's) om bewakings gegevens te verzamelen. U kunt bepalen welke gegevens sets worden verzameld en de frequentie van het verzamelen met behulp van een bewakings profiel.

## <a name="create-log-analytics-workspace"></a>Een Log Analytics-werkruimte maken
De gegevens van SQL Insights worden opgeslagen in een of meer [log Analytics-werk ruimten](../logs/data-platform-logs.md#log-analytics-workspaces).  Voordat u SQL Insights kunt inschakelen, moet u [een werk ruimte maken](../logs/quick-create-workspace.md) of een bestaande selecteren. Eén werk ruimte kan worden gebruikt met meerdere bewakings profielen, maar de werk ruimte en profielen moeten zich in dezelfde Azure-regio bevinden. Als u de functies in SQL Insights wilt inschakelen en gebruiken, moet u de [rol log Analytics Inzender](../logs/manage-access.md) hebben in de werk ruimte. 

## <a name="create-monitoring-user"></a>Bewakings gebruiker maken 
U hebt een gebruiker nodig voor de SQL-implementaties die u wilt bewaken. Volg de onderstaande procedures voor verschillende soorten SQL-implementaties.

### <a name="azure-sql-database"></a>Azure SQL-database
Open Azure SQL Database met [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) of [query-editor (preview)](../../azure-sql/database/connect-query-portal.md) in de Azure Portal.

Voer het volgende script uit om een gebruiker te maken met de vereiste machtigingen. Vervang de *gebruiker* door een gebruikers naam en *mystrongpassword* met een wacht woord.

```
CREATE USER [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW DATABASE STATE TO [user]; 
GO 
```

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-script.png" alt-text="Een telegrafisch gebruikers script maken." lightbox="media/sql-insights-enable/telegraf-user-database-script.png":::

Controleer of de gebruiker is gemaakt.

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-verify.png" alt-text="Het telegrafie gebruikers script controleren." lightbox="media/sql-insights-enable/telegraf-user-database-verify.png":::

### <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance
Meld u aan bij uw Azure SQL Managed instance en gebruik [SSMS](../../azure-sql/database/connect-query-ssms.md) of soortgelijk hulp programma om het volgende script uit te voeren om de bewakings gebruiker met de benodigde machtigingen te maken. Vervang de *gebruiker* door een gebruikers naam en *mystrongpassword* met een wacht woord.

 
```
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
Meld u aan bij de virtuele machine van Azure met SQL Server en gebruik [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) of een soortgelijk hulp programma om het volgende script uit te voeren om de bewakings gebruiker met de benodigde machtigingen te maken. Vervang de *gebruiker* door een gebruikers naam en *mystrongpassword* met een wacht woord.

 
```
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO
```

## <a name="create-azure-virtual-machine"></a>Virtuele Azure-machine maken 
U moet een of meer virtuele Azure-machines maken die worden gebruikt om gegevens te verzamelen om SQL te bewaken.  

> [!NOTE]
>  De [bewakings profielen](#create-sql-monitoring-profile) geeft aan welke gegevens u gaat verzamelen van de verschillende typen SQL die u wilt bewaken. Aan elke virtuele bewakings machine kan slechts één bewakings profiel zijn gekoppeld. Als u meerdere bewakings profielen nodig hebt, moet u voor elk profiel een virtuele machine maken.

### <a name="azure-virtual-machine-requirements"></a>Vereisten voor virtuele Azure-machines
Voor de virtuele machines van Azure gelden de volgende vereisten.

- Besturings systeem: Ubuntu 18,04 
- Aanbevolen grootten voor virtuele machines in Azure: Standard_B2s (2 cpu's, 4 GiB geheugen) 
- Ondersteunde regio's: elke [regio die wordt ondersteund door de Azure monitor-agent](../agents/azure-monitor-agent-overview.md#supported-regions)

> [!NOTE]
> De Standard_B2s (2 cpu's, 4 GiB geheugen) de grootte van de virtuele machine biedt ondersteuning voor Maxi maal 100 verbindings reeksen. U moet niet meer dan 100 verbindingen aan één virtuele machine toewijzen.

De virtuele machines moeten in hetzelfde VNET worden geplaatst als uw SQL-systemen, zodat ze netwerk verbindingen kunnen maken om bewakings gegevens te verzamelen. Als u de virtuele machine voor bewaking gebruikt om SQL te bewaken die wordt uitgevoerd op virtuele machines van Azure of als een beheerd exemplaar van Azure, kunt u overwegen om de virtuele machine voor bewaking in een toepassings beveiligings groep of hetzelfde virtuele netwerk als die bronnen te plaatsen, zodat u geen openbaar netwerk eindpunt hoeft op te geven voor het bewaken van de SQL Server. 

## <a name="configure-network-settings"></a>Netwerkinstellingen configureren
Elk type SQL biedt methoden voor uw virtuele bewakings machine om veilig toegang te krijgen tot SQL.  In de volgende secties worden de opties behandeld op basis van het type SQL.

### <a name="azure-sql-databases"></a>Azure SQL Databases  

SQL Insights biedt ondersteuning voor toegang tot uw Azure SQL Database via het open bare eind punt en het virtuele netwerk.

Voor toegang via het open bare eind punt voegt u een regel toe op de pagina **firewall instellingen** en de sectie [IP-Firewall-instellingen](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-firewall-rules) .  Als u toegang vanuit een virtueel netwerk wilt opgeven, kunt u [firewall regels voor het virtuele netwerk](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#virtual-network-firewall-rules) instellen en de [service tags instellen die vereist zijn voor de Azure monitor-agent](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview#networking).  In [dit artikel](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-vs-virtual-network-firewall-rules) worden de verschillen tussen deze twee typen firewall regels beschreven.

:::image type="content" source="media/sql-insights-enable/set-server-firewall.png" alt-text="Serverfirewall instellen" lightbox="media/sql-insights-enable/set-server-firewall.png":::

:::image type="content" source="media/sql-insights-enable/firewall-settings.png" alt-text="Firewall instellingen." lightbox="media/sql-insights-enable/firewall-settings.png":::

> [!NOTE]
> SQL Insights biedt momenteel geen ondersteuning voor Azure private endpoint voor Azure SQL Database.  U kunt het beste [service Tags](https://docs.microsoft.com/azure/virtual-network/service-tags-overview) gebruiken in uw netwerk beveiligings groep of firewall instellingen van het virtuele netwerk die door de [Azure monitor-agent worden ondersteund](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview#networking).

### <a name="azure-sql-managed-instances"></a>Azure SQL Managed Instances 

Als uw virtuele machine voor bewaking zich in hetzelfde VNet bevindt als uw SQL MI-resources, raadpleegt u [Connect in hetzelfde vnet](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-the-same-vnet). Als uw virtuele machine voor bewaking zich in het andere VNet bevindt dan uw SQL MI-resources, raadpleegt u [Connect in een ander vnet](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-a-different-vnet).


### <a name="azure-virtual-machine-and-azure-sql-virtual-machine"></a>Virtuele Azure-machine en Azure SQL-machine  
Als uw virtuele machine voor bewaking zich in hetzelfde VNet bevindt als de resources van uw virtuele SQL-machine, raadpleegt [u verbinding maken met SQL Server in een virtueel netwerk](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-within-a-virtual-network). Als uw virtuele machine voor bewaking zich in het andere VNet bevinden dan de resources van uw virtuele SQL-machine, raadpleegt  [u verbinding maken met SQL Server via internet](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-over-the-internet).

## <a name="store-monitoring-password-in-key-vault"></a>Bewakings wachtwoord opslaan in Key Vault
U moet uw wacht woorden voor SQL-gebruikers verbindingen opslaan in een Key Vault in plaats van deze rechtstreeks in uw verbindings reeksen voor het bewakings profiel in te voeren.

Wanneer u uw profiel instelt voor SQL-bewaking, hebt u een van de volgende machtigingen nodig voor de Key Vault resource die u wilt gebruiken:

- Microsoft.Authorization/roleAssignments/write 
- Micro soft. Authorization/roleAssignments/delete permissions, zoals beheerder of eigenaar van gebruikers toegang 

Er wordt automatisch een nieuw toegangs beleid gemaakt als onderdeel van het maken van uw SQL Monitoring profiel dat gebruikmaakt van de Key Vault die u hebt opgegeven. Gebruik *toegang vanaf alle netwerken toestaan* voor Key Vault netwerk instellingen.


## <a name="create-sql-monitoring-profile"></a>SQL-bewakings profiel maken
Open SQL Insights door **SQL (preview)** te selecteren in het gedeelte **inzichten** van het menu **Azure monitor** van de Azure Portal. Klik op **Nieuw profiel maken**. 

:::image type="content" source="media/sql-insights-enable/create-new-profile.png" alt-text="Nieuw profiel maken." lightbox="media/sql-insights-enable/create-new-profile.png":::

In het profiel worden de gegevens opgeslagen die u wilt verzamelen uit uw SQL-systemen.  Er zijn specifieke instellingen voor: 

- Azure SQL Database 
- Azure SQL Managed Instances 
- SQL Server uitgevoerd op virtuele machines  

U kunt bijvoorbeeld één profiel maken met de naam *SQL productie* en een andere benoemde *SQL-fase* met verschillende instellingen voor de frequentie van gegevens verzameling, welke gegevens moeten worden verzameld en naar welke werk ruimte de gegevens moeten worden verzonden. 

Het profiel wordt opgeslagen als een [regel resource voor gegevens verzameling](../agents/data-collection-rule-overview.md) in het abonnement en de resource groep die u selecteert. Elk profiel moet het volgende hebben:

- Naam. Kan niet worden bewerkt nadat deze is gemaakt.
- Locatie. Dit is een Azure-regio.
- Log Analytics werk ruimte om de bewakings gegevens op te slaan.
- Verzamelings instellingen voor de frequentie en het type van SQL-bewakings gegevens die moeten worden verzameld.

> [!NOTE]
> De locatie van het profiel moet zich op dezelfde locatie bevinden als de Log Analytics werk ruimte waarnaar u de bewakings gegevens wilt verzenden.


:::image type="content" source="media/sql-insights-enable/profile-details.png" alt-text="Profiel gegevens." lightbox="media/sql-insights-enable/profile-details.png":::

Klik op **bewakings profiel maken** zodra u de gegevens voor uw bewakings profiel hebt opgegeven. Het kan Maxi maal een minuut duren voordat het profiel is geïmplementeerd.  Als het nieuwe profiel niet wordt weer gegeven in de keuze lijst met invoervak voor **bewakings profiel** , klikt u op de knop Vernieuwen. deze moet worden weer gegeven nadat de implementatie is voltooid.  Wanneer u het nieuwe profiel hebt geselecteerd, selecteert u het tabblad **profiel beheren** om een bewakings machine toe te voegen die aan het profiel wordt gekoppeld.

### <a name="add-monitoring-machine"></a>Bewakings machine toevoegen
Selecteer **bewakings machine toevoegen** om een context paneel te openen om de virtuele machine te selecteren die u wilt instellen om uw SQL-exemplaren te controleren en de verbindings reeksen op te geven.

Selecteer het abonnement en de naam van de virtuele machine voor bewaking. Als u Key Vault gebruikt om uw wacht woord op te slaan voor de bewakings gebruiker, selecteert u de Key Vault resources met deze geheimen en voert u de URL en geheime naam in die moeten worden gebruikt in de verbindings reeksen. Zie de volgende sectie voor meer informatie over het identificeren van de connection string voor verschillende SQL-implementaties.


:::image type="content" source="media/sql-insights-enable/add-monitoring-machine.png" alt-text="Bewakings machine toevoegen." lightbox="media/sql-insights-enable/add-monitoring-machine.png":::


### <a name="add-connection-strings"></a>Verbindings reeksen toevoegen 
Met de connection string geeft u de gebruikers naam op die SQL Insights moet gebruiken wanneer u zich aanmeldt bij SQL om de dynamische beheer weergaven uit te voeren. Als u een Key Vault gebruikt om het wacht woord voor uw bewakings gebruiker op te slaan, geeft u de URL en de naam op van het geheim dat moet worden gebruikt. 

De verbindings reeks is afhankelijk van elk type SQL-resource:

#### <a name="azure-sql-databases"></a>Azure SQL Databases 
Voer de connection string in het formulier in:

```
sqlAzureConnections": [ 
   "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=$username;Password=$password;" 
}
```

Haal de details op uit de menu opdracht **verbindings reeksen** voor de data base.

:::image type="content" source="media/sql-insights-enable/connection-string-sql-database.png" alt-text="SQL database connection string" lightbox="media/sql-insights-enable/connection-string-sql-database.png":::

Als u een lees bare secundaire wilt bewaken, neemt u de sleutel waarde `ApplicationIntent=ReadOnly` op in de Connection String.


#### <a name="azure-virtual-machines-running-sql-server"></a>Virtuele Azure-machines met SQL Server 
Voer de connection string in het formulier in:

```
"sqlVmConnections": [ 
   "Server=MyServerIPAddress;Port=1433;User Id=$username;Password=$password;" 
] 
```

Als uw virtuele machine voor bewaking zich in hetzelfde VNET bevindt, gebruikt u het privé-IP-adres van de server.  Gebruik anders het open bare IP-adres. Als u de virtuele machine van Azure SQL gebruikt, ziet u welke poort u hier kunt gebruiken op de pagina **beveiliging** voor de resource.

:::image type="content" source="media/sql-insights-enable/sql-vm-security.png" alt-text="Beveiliging van virtuele SQL-machines" lightbox="media/sql-insights-enable/sql-vm-security.png":::

Als u een lees bare secundaire wilt bewaken, neemt u de sleutel waarde `ApplicationIntent=ReadOnly` op in de Connection String.


### <a name="azure-sql-managed-instances"></a>Azure SQL Managed Instances 
Voer de connection string in het formulier in:

```
"sqlManagedInstanceConnections": [ 
      "Server= mysqlserver.database.windows.net;Port=1433;User Id=$username;Password=$password;", 
    ] 
```
Haal de details op uit de menu opdracht **verbindings reeksen** voor het beheerde exemplaar.


:::image type="content" source="media/sql-insights-enable/connection-string-sql-managed-instance.png" alt-text="connection string van SQL-beheerd exemplaar" lightbox="media/sql-insights-enable/connection-string-sql-managed-instance.png":::

Als u een lees bare secundaire wilt bewaken, neemt u de sleutel waarde `ApplicationIntent=ReadOnly` op in de Connection String.



## <a name="profile-created"></a>Profiel gemaakt 
Selecteer **virtuele machine voor bewaking toevoegen** om de virtuele machine te configureren voor het verzamelen van gegevens uit uw SQL-implementaties. Ga niet terug naar het tabblad **overzicht** .  In een paar minuten moet de kolom Status worden gewijzigd om ' verzamelen ' te zeggen, moeten de gegevens worden weer gegeven voor de systemen die u wilt bewaken.

Als u geen gegevens ziet, raadpleegt u [Troubleshooting SQL Insights](sql-insights-troubleshoot.md) om het probleem te identificeren. 

:::image type="content" source="media/sql-insights-enable/profile-created.png" alt-text="Profiel gemaakt" lightbox="media/sql-insights-enable/profile-created.png":::

## <a name="next-steps"></a>Volgende stappen

- Zie [probleem oplossing voor SQL Insights](sql-insights-troubleshoot.md) als SQL Insights niet goed werkt nadat het is ingeschakeld.
