---
title: 'Zelfstudie: SQL Server online migreren naar een SQL-database'
titleSuffix: Azure Database Migration Service
description: Lees hoe u een onlinemigratie uitvoert van SQL Server naar Azure SQL Database met behulp van de Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/21/2020
ms.openlocfilehash: 29a154e33d8025f53b929bf0bb7602b2d0a64b13
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955018"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-online-using-dms"></a>Zelfstudie: SQL Server online migreren naar een individuele of pooldatabase in Azure SQL Database met behulp van DMS

U kunt de Azure Database Migration Service gebruiken voor het migreren van de databases van een SQL Server-exemplaar naar [Azure SQL Database](/azure/sql-database/) met minimale downtime. In deze zelfstudie migreert u de database **Adventureworks2012**, hersteld van een on-premises exemplaar van SQL Server 2016 (of hoger), naar een individuele of pooldatabase in Azure SQL Database met behulp van de Azure Database Migration Service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> - Uw on-premises database evalueren met behulp van de Data Migration Assistant.
> - Het voorbeeldschema migreren met behulp van de Data Migration Assistant.
> - De Azure-portal gebruiken om een Azure Database Migration Service-exemplaar te maken.
> - Een migratieproject maken met behulp van de Azure Database Migration Service.
> - De migratie uitvoeren.
> - Houd de migratie in de gaten.
> - Een migratierapport downloaden.

> [!NOTE]
> Als u Azure Database Migration Service gebruikt om een onlinemigratie uit te voeren, is het vereist dat u een exemplaar maakt op basis van de prijscategorie Premium. Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/database-migration/) van Azure Database Migration Service voor meer informatie.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt Microsoft u aan een exemplaar van de Azure Database Migration Service te maken in dezelfde Azure-regio als de doeldatabase. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In dit artikel wordt een onlinemigratie beschreven vanuit SQL Server naar een individuele of pooldatabase in Azure SQL Database. Zie [SQL Server offline migreren naar Azure SQL Database met behulp van DMS](tutorial-sql-server-to-azure-sql.md) voor een offlinemigratie.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

- Download en installeer [SQL Server 2012 of hoger](https://www.microsoft.com/sql-server/sql-server-downloads).
- Schakel het TCP/IP-protocol in, dat standaard is uitgeschakeld tijdens de installatie van SQL Server Express, door de instructies in het artikel [In- of uitschakelen van een Server Network Protocol](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) te volgen.
- Maak een individuele of pooldatabase in Azure SQL Database. Dit doet u door de stappen te volgen in het artikel [Een individuele database in Azure SQL Database maken met behulp van de Azure-portal](../azure-sql/database/single-database-create-quickstart.md).

    > [!NOTE]
    > Als u SQL Server Integration Services (SSIS) gebruikt en de catalogusdatabase voor uw SSIS-projecten/-pakketten (SSISDB) wilt migreren van SQL Server naar Azure SQL Database, wordt de doel-SSISDB automatisch namens u gemaakt en beheerd wanneer u SSIS in Azure Data Factory (ADF) inricht. Zie het artikel [Pakketten van SQL Server Integration Services migreren naar Azure](./how-to-migrate-ssis-packages.md) voor meer informatie over het migreren van SSIS-pakketten.

- Download en installeer de [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) v3.3 of hoger.
- Maak een Microsoft Azure Virtual Network voor Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel. Dit geeft site-naar-site-verbinding met uw on-premises bronservers met behulp van [ExpressRoute](../expressroute/expressroute-introduction.md) of [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Voor meer informatie over het maken van een virtueel netwerk raadpleegt u de [Documentatie over virtuele netwerken](../virtual-network/index.yml) en dan met name de quickstart-artikelen met stapsgewijze informatie.

    > [!NOTE]
    > Als u bij de installatie van een virtueel netwerk gebruikmaakt van ExpressRoute met netwerkpeering voor Microsoft, voegt u de volgende service-[eindpunten](../virtual-network/virtual-network-service-endpoints-overview.md) toe aan het subnet waarin de service wordt ingericht:
    > - Eindpunt van de doeldatabase (bijvoorbeeld SQL-eindpunt, Cosmos DB-eindpunt, enzovoort)
    > - Opslageindpunt
    > - Service Bus-eindpunt
    >
    > Deze configuratie is noodzakelijk omdat Azure Database Migration Service geen internetverbinding biedt.

- Zorg ervoor dat de regels voor netwerkbeveiligingsgroep van uw virtueel netwerk niet de volgende poorten voor inkomende communicatie naar Azure Database Migration Service blokkeren: 443, 53, 9354, 445, 12000. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/virtual-network-vnet-plan-design-arm.md) voor meer informatie over filteren van verkeer via de netwerkbeveiligingsgroep voor virtuele Azure-netwerken.
- Configureer uw [Windows Firewall voor toegang tot de database-engine](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Open uw Windows-firewall voor toegang voor de Azure Database Migration Service tot de SQL Server-bron, die standaard TCP-poort 1433 gebruikt. Als uw standaardexemplaar op een andere poort luistert, voegt u deze poort toe aan de firewall.
- Als u meerdere benoemde SQL Server-exemplaren uitvoert met behulp van dynamische poorten, kunt u desgewenst de SQL Browser Service inschakelen en toegang tot de UDP-poort 1434 via uw firewalls toestaan, zodat de Azure Database Migration Service verbinding kan maken met een benoemd exemplaar op uw bronserver.
- Wanneer u een firewallapparaat gebruikt voor de brondatabase(s), moet u mogelijk firewallregels toevoegen om voor de Azure Database Migration Service toegang tot de brondatabase(s) voor de migratie toe te staan.
- Maak een [firewallregel](../azure-sql/database/firewall-configure.md) op serverniveau voor Azure SQL Database om voor Azure Database Migration Service toegang tot de doeldatabase toe te staan. Geef het subnetbereik van het virtuele netwerk op dat wordt gebruikt voor Azure Database Migration Service.
- Zorg ervoor dat de referenties waarmee verbinding wordt gemaakt met het SQL Server-bronexemplaar [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql)-machtigingen hebben.
- Zorg ervoor dat de referenties waarmee verbinding wordt gemaakt met het Azure SQL Database-doelexemplaar CONTROL DATABASE-machtiging hebben op de Azure SQL-doeldatabases.
- De versie van de SQL-bronserver moet SQL Server 2005 of hoger zijn. Zie het artikel [Hoe de versie, de editie en het updateniveau van de SQL-server en de bijbehorende onderdelen worden bepaald](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an) om te bepalen welke versie van uw SQL Server-exemplaar wordt uitgevoerd.
- Databases moeten in de modus Bulksgewijs geregistreerd of Volledig herstel staan. Zie het artikel [Het herstelmodel van een database (SQL-server) weergeven of wijzigen](/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server?view=sql-server-2017) om het herstelmodel te bepalen dat is geconfigureerd voor uw SQL Server-exemplaar.
- Zorg ervoor dat u de volledige databaseback-ups voor de databases maakt. Zie het artikel [Een volledige databaseback-up maken (Transact-SQL)](/previous-versions/sql/sql-server-2008-r2/ms191304(v=sql.105)) voor informatie over het maken van een volledige databaseback-up.
- Als een van de tabellen geen primaire sleutel heeft, schakelt u Change Data Capture (CDC) in voor de database en de specifieke tabel(len).
    > [!NOTE]
    > U kunt het onderstaande script gebruiken om naar tabellen te zoeken die geen primaire sleutel hebben.
    
    ```sql
    USE <DBName>;
    go
    SELECT is_tracked_by_cdc, name AS TableName
    FROM sys.tables WHERE type = 'U' and is_ms_shipped = 0 AND
    OBJECTPROPERTY(OBJECT_ID, 'TableHasPrimaryKey') = 0;
    ```

    Als in de resultaten bij een of meer tabellen 'is_tracked_by_cdc' wordt weergegeven als '0', schakelt u Change Data Capture voor de database en de specifieke tabellen in met behulp van de procedure die wordt beschreven in het artikel [Change Data Capture in- en uitschakelen (SQL Server)](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server?view=sql-server-2017).

- Configureer de rol van de distributeur voor de SQL-bronserver.

    >[!NOTE]
    > U kunt bepalen of er replicatieonderdelen worden geïnstalleerd met de onderstaande query.

    ```sql
    USE master;
    DECLARE @installed int;
    EXEC @installed = sys.sp_MS_replication_installed;
    SELECT @installed as installed;
    ```

    Als het resultaat een foutbericht retourneert waarin wordt voorgesteld om replicatieonderdelen te installeren, installeert u de SQL-serverreplicatieonderdelen volgens de procedure in het artikel [SQL Server-replicatie installeren](/sql/database-engine/install-windows/install-sql-server-replication?view=sql-server-2017).

    Als de replicatie al is geïnstalleerd, controleert u of de distributierol is geconfigureerd op de SQL-bronserver met behulp van de onderstaande T-SQL-opdracht.

    ```sql
    EXEC sp_get_distributor;
    ```

    Als de distributie niet is ingesteld en de distributieserver NULL voor de bovenstaande uitvoer weergeeft, configureert u de distributie met behulp van de richtlijnen in artikel [Distributie configureren](/sql/relational-databases/replication/configure-publishing-and-distribution?view=sql-server-2017).

- Schakel databasetriggers voor de Azure SQL-doeldatabase uit.
    >[!NOTE]
    > U kunt de databasetriggers voor de Azure SQL-doeldatabase zoeken met behulp van de volgende query:

    ```sql
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```

    Zie het artikel [TRIGGER UITSCHAKELEN (Transact-SQL)](/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017) voor meer informatie. 

## <a name="assess-your-on-premises-database"></a>Uw on-premises resources evalueren

Voordat u gegevens uit een SQL Server-exemplaar naar Azure SQL Database kunt migreren, moet u de SQL Server-database controleren op blokkerende problemen die mogelijk de migratie verhinderen. Volg met behulp van de Data Migration Assistant v3.3 of hoger de stappen in het artikel [Uitvoeren van een SQL Server-migratie-evaluatie](/sql/dma/dma-assesssqlonprem) uit om de on-premises database-evaluatie uit te voeren.

Voer de volgende stappen uit om een on-premises database te evalueren:

1. Selecteer in DMA het pictogram Nieuw (+) en selecteer vervolgens het projecttype **Evaluatie**.
2. Geef een projectnaam op, selecteer in het tekstvak **Bronservertype****SQL Server**, in het tekstvak **Doelservertype****Azure SQL Database**, en selecteer vervolgens **Maken** om het project te maken.

   Wanneer u de SQL Server-brondatabase controleert die u naar een individuele of pooldatabase in Azure SQL Database wilt migreren, kunt u kiezen voor een of beide van de volgende typen evaluatierapport:

   - Databasecompatibiliteit controleren
   - Functiepariteit controleren

   Beide rapporttypen zijn standaard geselecteerd.

3. Selecteer in DMA **Volgende** in het scherm **Opties**.
4. Geef in het scherm **Bronnen selecteren**, in het dialoogvenster **Verbinding maken met een server**, de verbindingsdetails met uw SQL Server op, en selecteer vervolgens **Verbinding maken**.
5. Selecteer in het dialoogvenster **Bronnen toevoegen****AdventureWorks2012**, selecteer **Toevoegen**, en selecteer vervolgens **Evaluatie starten**.

    > [!NOTE]
    > Als u SSIS gebruikt, wordt de evaluatie van de bron-SSISDB momenteel niet door DMA ondersteund. SSIS-projecten/-pakketten worden echter beoordeeld/gevalideerd als ze zijn geïmplementeerd in de doel-SSISDB die wordt gehost door Azure SQL Database. Zie het artikel [Pakketten van SQL Server Integration Services migreren naar Azure](./how-to-migrate-ssis-packages.md) voor meer informatie over het migreren van SSIS-pakketten.

    Wanneer de evaluatie voltooid is, worden de resultaten weergegeven zoals in de volgende afbeelding:

    ![Gegevensmigratie beoordelen](media/tutorial-sql-server-to-azure-sql-online/dma-assessments.png)

    Voor individuele databases of pooldatabases in Azure SQL Database identificeren de beoordelingen problemen met betrekking tot functiepariteit en problemen die de migratie blokkeren bij een implementatie naar een individuele of pooldatabase.

    - De categorie **SQL Server-functiepariteit** biedt een uitgebreide set met aanbevelingen, alternatieve methoden die beschikbaar zijn in Azure, en beperkende stappen voor het plannen van de inzet in uw migratieprojecten.
    - De categorie **compatibiliteitsproblemen** identificeert gedeeltelijk ondersteunde of niet-ondersteunde functies die compatibiliteitsproblemen laten zien die mogelijk de migratie van SQL Server-databases naar Azure SQL Database blokkeren. Aanbevelingen om deze problemen op te lossen worden ook gegeven.

6. Bekijk de resultaten van de evaluatie voor migratieblokkerende problemen en problemen met de functiepariteit door de specifieke opties te selecteren.

## <a name="migrate-the-sample-schema"></a>Het voorbeeldschema migreren

Wanneer u tevreden bent over de evaluatie en overtuigd bent dat de geselecteerde database een goede kandidaat is voor migratie naar een individuele of pooldatabase in Azure SQL Database, kunt u de DMA gebruiken om het schema naar Azure SQL Database te migreren.

> [!NOTE]
> Voordat u een migratieproject in DMA maakt, moet u ervoor zorgen dat u al een SQL-database hebt ingericht in Azure zoals is vermeld in de vereisten. Voor deze zelfstudie wordt ervan uitgegaan dat de naam van de Azure SQL Database **AdventureWorksAzure** is, maar u kunt elke naam die u wenst opgeven.

> [!IMPORTANT]
> Als u SSIS gebruikt, biedt DMA momenteel geen ondersteuning voor de migratie van de bron-SSISDB, maar u kunt uw SSIS-projecten/-pakketten opnieuw implementeren naar de doel-SSISDB die wordt gehost door Azure SQL Database. Zie het artikel [Pakketten van SQL Server Integration Services migreren naar Azure](./how-to-migrate-ssis-packages.md) voor meer informatie over het migreren van SSIS-pakketten.

Voer de volgende stappen uit om het schema **AdventureWorks2012** te migreren naar een individuele of pooldatabase in Azure SQL Database:

1. Selecteer het pictogram Nieuw (+) in de Data Migration Assistant, en selecteer vervolgens onder **Projecttype****Migratie**.
2. Geef een projectnaam op, selecteer in het tekstvak **Bronservertype****SQL Server** en in het tekstvak **Doelservertype****Azure SQL Database**.
3. Selecteer onder **Migratiebereik**, selecteer **Alleen schema**.

    Na het uitvoeren van de vorige stappen, moet de DMA-interface worden weergegeven zoals in de volgende afbeelding:

    ![Data Migration Assistant-Project maken](media/tutorial-sql-server-to-azure-sql-online/dma-create-project.png)

4. Selecteer **Maken** om het project te maken.
5. Geef in DMA de details van de bronverbinding voor uw SQL Server op, selecteer **Verbinden** en selecteer vervolgens de database **AdventureWorks2012**.

    ![Details Data Migration Assistant Bronverbinding](media/tutorial-sql-server-to-azure-sql-online/dma-source-connect.png)

6. Selecteer **Volgende** onder **Verbinding maken met doelserver**, geef de details van de doelverbinding voor de Azure SQL Database op, selecteer **Verbinden** en selecteer vervolgens de database **AdventureWorksAzure** die u vooraf hebt ingericht in Azure SQL Database.

    ![Details Data Migration Assistant-doelverbinding](media/tutorial-sql-server-to-azure-sql-online/dma-target-connect.png)

7. Selecteer **Volgende** om door te gaan naar het scherm **Objecten selecteren** waarin u de schemaobjecten in de **AdventureWorks2012**-database kunt opgeven, die moeten worden geïmplementeerd naar Azure SQL Database.

    Standaard worden alle objecten geselecteerd.

    ![SQL-scripts genereren](media/tutorial-sql-server-to-azure-sql-online/dma-assessment-source.png)

8. Selecteer **SQL-script genereren** voor het maken van de SQL-scripts en controleer vervolgens de scripts op fouten.

    ![Schemascript](media/tutorial-sql-server-to-azure-sql-online/dma-schema-script.png)

9. Selecteer **Schema implementeren** om het schema te implementeren naar Azure SQL Database, en nadat het schema is geïmplementeerd, controleer de doelserver op eventuele afwijkingen.

    ![Schema implementeren](media/tutorial-sql-server-to-azure-sql-online/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-sql-server-to-azure-sql-online/portal-select-subscription1.png)

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/tutorial-sql-server-to-azure-sql-online/portal-register-resource-provider.png)

## <a name="create-an-instance"></a>Een instantie maken

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql-online/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-sql-server-to-azure-sql-online/dms-create1.png)
  
3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken. 

5. Selecteer een bestaand virtueel netwerk of maak een nieuw netwerk.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bron-SQL Server en het doel-Azure SQL Database-exemplaar.

    Zie het artikel [Een virtueel netwerk maken met de Azure-portal](../virtual-network/quick-create-portal.md) voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

6. Selecteer een prijscategorie.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![Instellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-sql-server-to-azure-sql-online/dms-settings2.png)

7. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Een migratieproject maken

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

    ![Zoek alle exemplaren van de Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql-online/dms-search.png)

2. Zoek in het scherm **Azure Database Migration service** naar de naam van de Azure Database Migration Service-instantie die u hebt gemaakt en selecteer vervolgens het exemplaar.

    ![Zoek uw exemplaar van de Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql-online/dms-instance-search.png)

3. Selecteer + **Nieuw migratieproject**.
4. Geef in het scherm **Nieuw migratieproject** een naam op voor het project, selecteer in het tekstvak **Bronservertype** de optie **SQL Server** en selecteer in het tekstvak **Doelservertype** de optie **Azure SQL Database**.
5. Selecteer in de sectie **Het type activiteit kiezen** de optie **Onlinegegevensmigratie**.

    ![Azure Database Migration Service-project maken](media/tutorial-sql-server-to-azure-sql-online/dms-create-project3.png)

    > [!NOTE]
    > U kunt ook **Alleen project maken** kiezen om het migratieproject nu te maken en de migratie later uit te voeren.

6. Selecteer **Opslaan**.

7. Selecteer **Maken en uitvoeren van de activiteit** om het project te maken en de migratieactiviteit uit te voeren.

    ![Een Azure Database Migration Service-activiteit maken en uitvoeren](media/tutorial-sql-server-to-azure-sql-online/dms-create-and-run-activity.png)

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Migratiebron** de details van de verbinding op voor het bron-SQL Server-exemplaar.

    Zorg ervoor dat u een Fully Qualified Domain Name (FQDN) gebruikt voor de naam van het bron-SQL Server-exemplaar. U kunt ook het IP-adres gebruiken voor situaties waarin DNS-naamomzetting niet mogelijk is.

2. Als u geen vertrouwd certificaat op de bronserver hebt geïnstalleerd, schakelt u het selectievakje **Servercertificaat vertrouwen** in.

    Wanneer er geen vertrouwd certificaat is geïnstalleerd, genereert SQL Server een zelfondertekend certificaat wanneer het exemplaar wordt gestart. Dit certificaat wordt gebruikt voor het versleutelen van de referenties voor clientverbindingen.

    > [!CAUTION]
    > TLS-verbindingen die zijn versleuteld met behulp van een zelfondertekend certificaat bieden geen sterke beveiliging. Ze zijn vatbaar voor man-in-the-middle-aanvallen. U moet niet vertrouwen op TLS met behulp van zelfondertekende certificaten in een productieomgeving of op servers die zijn verbonden met internet.

   ![Brondetails](media/tutorial-sql-server-to-azure-sql-online/dms-source-details3.png)

    > [!IMPORTANT]
    > Als u SSIS gebruikt, biedt DMA momenteel geen ondersteuning voor de migratie van de bron-SSISDB, maar u kunt uw SSIS-projecten/-pakketten opnieuw implementeren naar de doel-SSISDB die wordt gehost door Azure SQL Database. Zie het artikel [Pakketten van SQL Server Integration Services migreren naar Azure](./how-to-migrate-ssis-packages.md) voor meer informatie over het migreren van SSIS-pakketten.

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Selecteer **Opslaan** en geef vervolgens in het scherm **Details migratiedoel** de details van de verbinding op voor de Azure SQL-doeldatabase. Dit is de vooraf ingerichte Azure SQL Database waarnaar het **AdventureWorks2012**-schema is geïmplementeerd met behulp van de DMA.

    ![Doel selecteren](media/tutorial-sql-server-to-azure-sql-online/dms-select-target3.png)

2. Selecteer **Opslaan**, en klik vervolgens in het scherm **Toewijzen aan doeldatabases**, wijs de bron- en de doeldatabase voor de migratie toe.

    Als de doeldatabase de naam van de dezelfde database als de bron-database bevat, wordt in de Azure Database Migration Service de doeldatabase standaard geselecteerd.

    ![Toewijzen aan doeldatabases](media/tutorial-sql-server-to-azure-sql-online/dms-map-targets-activity3.png)

3. Selecteer **Opslaan** in het scherm **Tabellen selecteren**, vouw de tabellenlijst uit en controleer de lijst met betrokken velden.

    De Azure Database Migration Service selecteert automatisch alle lege brontabellen die beschikbaar zijn op het Azure SQL Database-doelexemplaar. Als u tabellen wilt terug migreren die al gegevens bevatten, moet u expliciet de tabellen op deze blade selecteren.

    ![Selecteer tabellen](media/tutorial-sql-server-to-azure-sql-online/dms-configure-setting-activity3.png)

4. Selecteer **Opslaan**, geef op het scherm **Migratieoverzicht** in het tekstvak **Naam activiteit** een naam op voor de migratieactiviteit, en controleer vervolgens het overzicht om te verzekeren dat de bron- en doeldetails overeenkomen met wat u eerder hebt opgegeven.

    ![Migratieoverzicht](media/tutorial-sql-server-to-azure-sql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

- Selecteer **Migratie uitvoeren**.

    Het venster van de migratieactiviteit wordt weergegeven en de **Status** van de activiteit is **Initialiseren**.

    ![Activiteitsstatus - initialiseren](media/tutorial-sql-server-to-azure-sql-online/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer in het scherm van de activiteitenmigratie **Vernieuwen** om de weergave bij te werken totdat de **Status** van de migratie als **Actief** wordt weergegeven.

2. Klik op een specifieke database om de migratiestatus voor de bewerkingen **Alle gegevens worden geladen** en **Incrementele gegevenssynchronisatie** te bekijken.

    ![Activiteitsstatus - actief](media/tutorial-sql-server-to-azure-sql-online/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Migratie-cutover uitvoeren

Nadat de eerste volledige lading is voltooid, worden de databases gemarkeerd als **Gereed voor cutover**.

1. Wanneer u klaar bent om de databasemigratie te voltooien, selecteert u **Cutover starten**.

    ![Cutover starten](media/tutorial-sql-server-to-azure-sql-online/dms-start-cutover.png)

2. Zorg dat u alle transacties stopt die bij de brondatabase binnenkomen; wacht totdat de teller van **Wijzigingen in behandeling** op **0** staat.
3. Selecteer **Bevestigen** en selecteer vervolgens **Toepassen**.
4. Wanneer de databasemigratiestatus **Voltooid** toont, verbindt u uw toepassingen met de nieuwe doel-Azure SQL Database.

    ![Activiteitsstatus - voltooid](media/tutorial-sql-server-to-azure-sql-online/dms-activity-completed.png)

## <a name="next-steps"></a>Volgende stappen

- Zie het artikel [Bekende problemen met en tijdelijke oplossingen voor online migraties naar Azure SQL Database](known-issues-azure-sql-online.md) voor informatie over bekende problemen en beperkingen bij het uitvoeren van online migraties naar Azure SQL Database.
- Raadpleeg het artikel [Wat is de Azure Database Migration Service?](./dms-overview.md) voor informatie over de Azure Database Migration Service.
- Zie het artikel [Wat is de service Azure SQL Database?](../azure-sql/database/sql-database-paas-overview.md) voor informatie over Azure SQL Database.