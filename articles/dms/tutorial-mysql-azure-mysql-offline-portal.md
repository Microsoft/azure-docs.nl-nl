---
title: 'Zelfstudie: MySQL migreren naar Azure Database for MySQL offline met behulp van DMS'
titleSuffix: Azure Database Migration Service
description: Leer hoe u een offlinemigratie kunt uitvoeren van MySQL on-premises naar Azure Database for MySQL met behulp van Azure Database Migration Service.
services: dms
author: sumitgaurin
ms.author: sgaur
manager: arthiaga
ms.reviewer: arthiaga
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2021
ms.openlocfilehash: 71363771359ffef0ff165bd4a6744d864ea1856c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819609"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-offline-using-dms"></a>Zelfstudie: MySQL migreren naar Azure Database for MySQL offline met behulp van DMS

U kunt Azure Database Migration Service een een time-time volledige databasemigratie uitvoeren op een [](../mysql/index.yml) on-premises MySQL-exemplaar om een Azure Database for MySQL met snelle gegevensmigratie uit te voeren. In deze zelfstudie migreren we een voorbeelddatabase van een on-premises exemplaar van MySQL 5.7 naar Azure Database for MySQL (v5.7) met behulp van een offlinemigratieactiviteit in Azure Database Migration Service. Hoewel in de artikelen wordt aangenomen dat de bron een MySQL-database-exemplaar en doel is om Azure Database for MySQL te zijn, kan deze worden gebruikt om van het ene Azure Database for MySQL naar het andere te migreren door alleen de naam en referenties van de bronserver te wijzigen. Migratie van MySQL-servers met een lagere versie (v5.6 en hoger) naar hogere versies wordt ook ondersteund.

> [!IMPORTANT]
> Voor onlinemigraties kunt u deze nieuwe aanbieding gebruiken samen met [replicatie van binnenkomende gegevens.](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) U kunt ook opensource-hulpprogramma's zoals [MyDumper/MyLoader](https://centminmod.com/mydumper.html) gebruiken met replicatie van binnenkomende gegevens voor onlinemigraties. 

[!INCLUDE [preview features callout](../../includes/dms-boilerplate-preview.md)]


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Migreert het databaseschema met behulp van het hulpprogramma mysqldump.
> * Maak een exemplaar van de Azure Database Migration Service.
> * Maak een migratieproject met behulp van Azure Database Migration Service.
> * De migratie uitvoeren.
> * Houd de migratie in de gaten.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* U moet beschikken over een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free)
* Een on-premises MySQL-database met versie 5.7. Als dat niet het beste is, downloadt en installeert [u MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.7.
* [Maak een exemplaar in Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Raadpleeg het artikel [MySQL Workbench](../mysql/connect-workbench.md) gebruiken om verbinding te maken en gegevens op te vragen voor meer informatie over het maken en verbinden van een database met behulp van de Workbench-toepassing. De Azure Database for MySQL versie moet gelijk zijn aan of hoger zijn dan de on-premises MySQL-versie . MySQL 5.7 kan bijvoorbeeld worden gemigreerd naar Azure Database for MySQL 5.7 of worden geüpgraded naar 8. 
* Maak een Microsoft Azure Virtual Network voor Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel. Dit biedt site-naar-site-connectiviteit met uw on-premises bronservers met behulp van [ExpressRoute](../expressroute/expressroute-introduction.md) of [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Voor meer informatie over het maken van een virtueel netwerk raadpleegt u de [Documentatie over virtuele netwerken](../virtual-network/index.yml) en dan met name de quickstart-artikelen met stapsgewijze informatie.

    > [!NOTE]
    > Als u bij de installatie van een virtueel netwerk gebruikmaakt van ExpressRoute met netwerkpeering voor Microsoft, voegt u de volgende service-[eindpunten](../virtual-network/virtual-network-service-endpoints-overview.md) toe aan het subnet waarin de service wordt ingericht:
    >
    > * Eindpunt van de doeldatabase (bijvoorbeeld SQL-eindpunt, Cosmos DB-eindpunt, enzovoort)
    > * Opslageindpunt
    > * Service Bus-eindpunt
    >
    > Deze configuratie is noodzakelijk omdat Azure Database Migration Service geen internetverbinding biedt.

* Zorg ervoor dat de regels voor de netwerkbeveiligingsgroep van uw virtuele netwerk de uitgaande poort 443 van ServiceTag voor ServiceBus, Storage en AzureMonitor niet blokkeren. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/virtual-network-vnet-plan-design-arm.md) voor meer informatie over verkeer filteren van verkeer via de netwerkbeveiligingsgroep voor virtuele netwerken.
* Open uw Windows-firewall om verbindingen vanaf Virtual Network voor Azure Database Migration Service toegang te geven tot de bronserver van MySQL, die standaard TCP-poort 3306 is.
* Wanneer u een firewallapparaat gebruikt vóór uw brondatabase(s), moet u mogelijk firewallregels toevoegen om verbindingen vanuit Virtual Network voor Azure Database Migration Service toegang te geven tot de brondatabase(s) voor migratie.
* Maak een firewallregel op [serverniveau](../azure-sql/database/firewall-configure.md) of configureer [VNET-service-eindpunten](../mysql/howto-manage-vnet-using-portal.md) voor doel-Azure Database for MySQL zodat Virtual Network toegang Azure Database Migration Service tot de doeldatabases.
* De brondatabase van MySQL moet op een ondersteunde MySQL-communityversie zijn. Om de versie van het MySQL-exemplaar te bepalen, voert u in het MySQL-hulpprogramma of MySQL Workbench de volgende opdracht uit:

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL ondersteunt alleen InnoDB-tabellen. Raadpleeg het artikel [Tabellen van MyISAM naar InnoDB converteren](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) om MyISAM-tabellen te converteren naar InnoDB.
* De gebruiker moet over de bevoegdheden beschikken om gegevens in de brondatabase te lezen.

## <a name="migrate-database-schema"></a>Databaseschema migreren

Als u alle databaseobjecten, zoals tabelschema's, indexen en opgeslagen procedures, wilt overdragen, moeten we het schema uit de brondatabase extraheren en toepassen op de doeldatabase. Om het schema te extraheren, kunt u mysqldump gebruiken met de parameter `--no-data`. Hiervoor hebt u een computer nodig die verbinding kan maken met zowel de MySQL-brondatabase als de doeldatabase Azure Database for MySQL.

Voer de volgende opdracht uit om het schema te exporteren met mysqldump:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Bijvoorbeeld:

```
mysqldump -h 10.10.123.123 -u root -p --databases migtestdb --no-data > d:\migtestdb.sql
```

Voer de volgende opdracht uit om het schema Azure Database for MySQL doel te importeren:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Bijvoorbeeld:

```
mysql.exe -h mysqlsstrgt.mysql.database.azure.com -u docadmin@mysqlsstrgt -p migtestdb < d:\migtestdb.sql
 ```

Als uw schema vreemde sleutels bevat, wordt de parallelle gegevensbelasting tijdens de migratie afgehandeld door de migratietaak. U hoeft geen vreemde sleutels te laten vallen tijdens de schemamigratie.

Als de database triggers bevat, wordt de gegevensintegriteit in het doel afgedwongen, voorafgaand aan volledige gegevensmigratie vanuit de bron. Het wordt aanbevolen om triggers voor alle tabellen in het doel uit te schakelen tijdens de migratie en de triggers vervolgens in te schakelen nadat de migratie is uitgevoerd.

Voer het volgende script uit in MySQL Workbench op de doeldatabase om het triggerscript voor neerzetten te extraheren en een triggerscript toe te voegen.

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

Voer de gegenereerde drop trigger-query (kolom DropQuery) uit in het resultaat om triggers in de doeldatabase te verwijderen. De triggerquery toevoegen kan worden opgeslagen om te worden gebruikt na voltooiing van de gegevensmigratie.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

Registratie van de resourceprovider hoeft slechts één keer te worden uitgevoerd voor elk Azure-abonnement. Zonder de registratie kunt u geen exemplaar van de **Azure Database Migration Service.**

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-mysql-to-azure-mysql-offline-portal/01-dms-portal-select-subscription.png)

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/tutorial-mysql-to-azure-mysql-offline-portal/02-dms-portal-register-rp.png)

## <a name="create-a-database-migration-service-instance"></a>Een Database Migration Service maken

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-offline-portal/03-dms-portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-mysql-to-azure-mysql-offline-portal/04-dms-portal-marketplace-create.png)
  
3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer een prijscategorie en ga naar het netwerkscherm. De mogelijkheid voor offlinemigratie is beschikbaar in de prijscategorie Standard en Premium.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![Basisinstellingen Azure Database Migration Service configureren](media/tutorial-mysql-to-azure-mysql-offline-portal/05-dms-portal-create-basic.png)

5. Selecteer een bestaand virtueel netwerk in de lijst of geef de naam op van het nieuwe virtuele netwerk dat moet worden gemaakt. Ga naar het scherm Beoordelen en maken. U kunt eventueel tags toevoegen aan de service met behulp van het tagsscherm.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bron-SQL Server en het doel-Azure SQL Database-exemplaar.

    ![Netwerkinstellingen Azure Database Migration Service configureren](media/tutorial-mysql-to-azure-mysql-offline-portal/06-dms-portal-create-networking.png)

    Zie het artikel [Een virtueel netwerk maken met de Azure-portal](../virtual-network/quick-create-portal.md) voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

6. Controleer de configuraties en selecteer **Maken om** de service te maken.
    
    ![Azure Database Migration Service maken](media/tutorial-mysql-to-azure-mysql-offline-portal/07-dms-portal-create-submit.png)

## <a name="create-a-migration-project"></a>Een migratieproject maken

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.  

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

    ![Zoek alle exemplaren van Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. Selecteer uw migration service-exemplaar in de zoekresultaten en selecteer + **Nieuw migratieproject.**
    
    ![Een nieuw migratieproject maken](media/tutorial-mysql-to-azure-mysql-offline-portal/08-02-dms-portal-new-project.png)

3. Geef in het scherm  Nieuw **migratieproject** een naam  op voor het project, selecteer in  het selectievak Bronservertype de optie **MySQL,** selecteer in het selectievak Type doelserver de optie **Azure Database For MySQL** en selecteer in het selectievak Migratieactiviteitstype de optie Voorbeeld van **\[ gegevensmigratie. \]** Selecteer **Activiteit maken en uitvoeren**.

    ![Azure Database Migration Service-project maken](media/tutorial-mysql-to-azure-mysql-offline-portal/09-dms-portal-project-mysql-create.png)

    > [!NOTE]
    > U kunt ook **Alleen project maken** kiezen om het migratieproject nu te maken en de migratie later uit te voeren.

## <a name="configure-migration-project"></a>Migratieproject configureren

1. Geef in **het scherm Bron** selecteren de verbindingsgegevens op voor het bron-MySQL-exemplaar en selecteer **Volgende: Doel selecteren>>**

    ![Scherm Brondetails toevoegen](media/tutorial-mysql-to-azure-mysql-offline-portal/10-dms-portal-project-mysql-source.png)

2. Geef in **het scherm** Doel selecteren de verbindingsgegevens op voor het doel-Azure Database for MySQL en selecteer **Volgende: Databases** selecteren>>

    ![Scherm Doeldetails toevoegen](media/tutorial-mysql-to-azure-mysql-offline-portal/11-dms-portal-project-mysql-target.png)

3. Wijs **in het scherm Databases** selecteren de bron- en doeldatabase toe voor migratie en selecteer **Volgende: Migratie-instellingen configureren>>**. U kunt de optie **Bronserver alleen-lezen** maken selecteren om de bron als alleen-lezen te maken, maar wees voorzichtig dat dit een instelling op serverniveau is. Als deze optie is geselecteerd, wordt de hele server ingesteld op alleen-lezen, niet alleen op de geselecteerde databases.
    
    Als de doeldatabase de naam van de dezelfde database als de bron-database bevat, wordt in Azure Database Migration Service de doeldatabase standaard geselecteerd.
    ![Scherm Databasedetails selecteren](media/tutorial-mysql-to-azure-mysql-offline-portal/12-dms-portal-project-mysql-select-db.png)
    
    > [!NOTE] 
    > Hoewel u in deze stap meerdere databases kunt selecteren, zijn er limieten voor het aantal en hoe snel de DATABASES op deze manier kunnen worden gemigreerd, omdat elke database rekencapaciteit deelt. Met de standaardconfiguratie van de Premium-SKU probeert elke migratietaak twee tabellen parallel te migreren. Deze tabellen kunnen afkomstig zijn uit een van de geselecteerde databases. Als dit niet snel genoeg is, kunt u databasemigratieactiviteiten splitsen in verschillende migratietaken en schalen over meerdere services. Ook is er een limiet van tien Azure Database Migration Service-instanties per abonnement per regio.
    > Raadpleeg het artikel PowerShell: Offlinemigratie uitvoeren van [MySQL-database](./migrate-mysql-to-azure-mysql-powershell.md) naar Azure Database for MySQL met DMS voor gedetailleerdere controle over de migratiedoorvoer en -parallellisering

4. Selecteer in **het scherm Migratie-instellingen** configureren de tabellen die deel moeten uitmaken van de migratie en selecteer **Volgende: Samenvatting>>.** Als de doeltabellen gegevens bevatten, worden ze niet standaard geselecteerd, maar u kunt ze expliciet selecteren en worden ze afgekapt voordat de migratie wordt begonnen.

    ![Scherm Tabellen selecteren](media/tutorial-mysql-to-azure-mysql-offline-portal/13-dms-portal-project-mysql-select-tbl.png)

5. Geef op **het** scherm  Samenvatting in het tekstvak Activiteitsnaam een naam op voor de migratieactiviteit en controleer de samenvatting om ervoor te zorgen dat de bron- en doeldetails overeenkomen met wat u eerder hebt opgegeven.

    ![Overzicht van migratieproject](media/tutorial-mysql-to-azure-mysql-offline-portal/14-dms-portal-project-mysql-activity-summary.png)

6. Selecteer **Migratie starten**. Het venster van de migratieactiviteit wordt weergegeven en de **Status** van de activiteit is **Initialiseren**. De **Status** wordt gewijzigd **in Wordt uitgevoerd** wanneer de tabelmigraties starten.
 
     ![Migratie uitvoeren](media/tutorial-mysql-to-azure-mysql-offline-portal/15-dms-portal-project-mysql-running.png)

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer vernieuwen in het scherm van de migratieactiviteit **om** de weergave bij te werken en de voortgang van het aantal voltooide tabellen te bekijken. 

2. U kunt op de naam van de database klikken op het activiteitsscherm om de status van elke tabel te zien terwijl deze worden gemigreerd. Selecteer **Vernieuwen om** de weergave bij te werken. 

     ![Migratiebewaking](media/tutorial-mysql-to-azure-mysql-offline-portal/16-dms-portal-project-mysql-monitor.png)

## <a name="complete-the-migration"></a>Migratie voltooien

1. Selecteer op het scherm van de migratieactiviteit de optie **Vernieuwen** om de weergave bij te werken totdat de **Status** van de migratie als **Voltooid** wordt weergegeven.

    ![Migratie voltooien](media/tutorial-mysql-to-azure-mysql-offline-portal/17-dms-portal-project-mysql-complete.png)

## <a name="post-migration-activities"></a>Activiteiten na migratie

Migratie-cutover in een offlinemigratie is een toepassingsafhankelijk proces dat buiten het bereik van dit document valt, maar de volgende activiteiten na de migratie worden voorgeschreven:

1. Maak aanmeldingen, rollen en machtigingen volgens de toepassingsvereisten.
2. Maak alle triggers voor de doeldatabase opnieuw zoals deze zijn geëxtraheerd tijdens de stap vóór de migratie.
3. Voer een sanitytest van de toepassing uit op de doeldatabase om de migratie te certificeren. 

## <a name="clean-up-resources"></a>Resources opschonen

Als u de service niet meer gaat gebruiken Database Migration Service, kunt u de service verwijderen met de volgende stappen:

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

    ![Alle exemplaren van DMS zoeken](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. Selecteer uw migration service-exemplaar in de zoekresultaten en selecteer **Service verwijderen.**
    
    ![De migratieservice verwijderen](media/tutorial-mysql-to-azure-mysql-offline-portal/18-dms-portal-delete.png)

3. Typ in het bevestigingsvenster de naam van de service in het tekstvak **TYPE THE DATABASE MIGRATION SERVICE NAME** en selecteer **Verwijderen**

    ![Migratieservice verwijderen bevestigen](media/tutorial-mysql-to-azure-mysql-offline-portal/19-dms-portal-deleteconfirm.png)

## <a name="next-steps"></a>Volgende stappen

* Zie het artikel Algemene problemen - Azure Database Migration Service voor meer informatie over bekende problemen en beperkingen bij het uitvoeren [van migraties met behulp van DMS.](./known-issues-troubleshooting-dms.md)
* Zie het artikel Problemen met het verbinden van brondatabases voor het oplossen van verbindingsproblemen met de [brondatabase](./known-issues-troubleshooting-dms-source-connectivity.md)tijdens het gebruik van DMS.
* Zie het artikel [Wat is de Azure Database Migration Service?](./dms-overview.md) voor informatie over Azure Database Migration Service.
* Raadpleeg het artikel [Wat is Azure Database for MySQL?](../mysql/overview.md) voor informatie over Azure Database for MySQL.
* Zie het artikel PowerShell: Offlinemigratie uitvoeren van [MySQL-database](./migrate-mysql-to-azure-mysql-powershell.md) naar Azure Database for MySQL dms voor hulp bij het gebruik van DMS