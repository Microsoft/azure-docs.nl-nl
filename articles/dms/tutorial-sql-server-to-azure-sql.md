---
title: 'Zelf studie: SQL Server offline migreren naar Azure SQL Database'
titleSuffix: Azure Database Migration Service
description: Informatie over het offline migreren van SQL Server naar Azure SQL Database met behulp van Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/03/2021
ms.openlocfilehash: 9c3fa0d8ac4540495e8580fd208507a2c1aaa7ce
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180636"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-using-dms"></a>Zelf studie: SQL Server migreren naar Azure SQL Database met behulp van DMS

U kunt Azure Database Migration Service gebruiken om de databases te migreren van een SQL Server-exemplaar naar [Azure SQL Database](/azure/sql-database/). In deze zelfstudie migreert u de database [Adventureworks2016](/sql/samples/adventureworks-install-configure#download-backup-files), hersteld van een on-premises exemplaar van SQL Server 2016 (of hoger), naar een individuele of pooldatabase in Azure SQL Database met behulp van Azure Database Migration Service.

U leert het volgende:
> [!div class="checklist"]
>
> - Controleer uw on-premises database voor blokkerende problemen met de Data Migration Assistant.
> - Gebruik de Data Migration Assistant om het databasevoorbeeldschema te migreren. 
> - Registreer de Azure Data Migration-resourceprovider.
> - Maak een exemplaar van de Azure Database Migration Service.
> - Maak een migratieproject met behulp van Azure Database Migration Service.
> - De migratie uitvoeren.
> - Houd de migratie in de gaten.


## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

- Download en installeer [SQL Server 2016 of hoger](https://www.microsoft.com/sql-server/sql-server-downloads).
- Schakel het TCP/IP-protocol in, dat standaard is uitgeschakeld tijdens de installatie van SQL Server Express, door de instructies in het artikel [In- of uitschakelen van een Server Network Protocol](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) te volgen.
- Maak een database in Azure SQL Database. Dit doet u door de stappen te volgen in het artikel [Een database in Azure SQL Database maken met behulp van de Azure-portal](../azure-sql/database/single-database-create-quickstart.md). Voor deze zelfstudie wordt ervan uitgegaan dat de naam van de Azure SQL Database **AdventureWorksAzure** is, maar u kunt elke naam die u wenst opgeven.

    > [!NOTE]
    > Als u SQL Server Integration Services (SSIS) gebruikt en de catalogusdatabase voor uw SSIS-projecten/-pakketten (SSISDB) wilt migreren van SQL Server naar Azure SQL Database, wordt de doel-SSISDB automatisch namens u gemaakt en beheerd wanneer u SSIS in Azure Data Factory (ADF) inricht. Zie het artikel [Pakketten van SQL Server Integration Services migreren naar Azure](./how-to-migrate-ssis-packages.md) voor meer informatie over het migreren van SSIS-pakketten.
  
- Download en installeer de nieuwste versie van de [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595).
- Maak een Microsoft Azure Virtual Network voor Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel. Dit geeft site-naar-site-verbinding met uw on-premises bronservers met behulp van [ExpressRoute](../expressroute/expressroute-introduction.md) of [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Voor meer informatie over het maken van een virtueel netwerk raadpleegt u de [documentatie over virtuele netwerken](../virtual-network/index.yml) en dan met name de quickstart-artikelen met stapsgewijze informatie.

    > [!NOTE]
    > Als u bij de installatie van een virtueel netwerk gebruikmaakt van ExpressRoute met netwerkpeering voor Microsoft, voegt u de volgende service-[eindpunten](../virtual-network/virtual-network-service-endpoints-overview.md) toe aan het subnet waarin de service wordt ingericht:
    >
    > - Eindpunt van de doeldatabase (bijvoorbeeld SQL-eindpunt, Cosmos DB-eindpunt, enzovoort)
    > - Opslageindpunt
    > - Service Bus-eindpunt
    >
    > Deze configuratie is noodzakelijk omdat Azure Database Migration Service geen internetverbinding biedt.
    >
    >Als u geen site-naar-site-verbinding hebt tussen het on-premises netwerk en Azure of als er beperkte bandbreedte beschikbaar is voor de site-naar-site-verbinding, kunt u overwegen Azure Database Migration Service te gebruiken in de hybride modus (preview). De hybride modus maakt gebruik van een on-premises migratiewerkrol en een exemplaar van Azure Database Migration Service dat in de cloud wordt uitgevoerd. Als u een exemplaar van Azure Database Migration Service in de hybride modus wilt maken, raadpleegt u het artikel [Create an instance of the Azure Database Migration Service by using the Azure portal](./quickstart-create-data-migration-service-hybrid-portal.md) (Een exemplaar van Azure Database Migration Service maken in de hybride modus met behulp van de Azure-portal).

- Zorg ervoor dat de uitgaande beveiligings regels voor de netwerk beveiligings groep voor het virtuele netwerk de uitgaande poort 443 van ServiceTag voor ServiceBus, Storage en AzureMonitor niet blok keren. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/virtual-network-vnet-plan-design-arm.md) voor meer informatie over filteren van verkeer via de netwerkbeveiligingsgroep voor virtuele Azure-netwerken.
- Configureer uw [Windows Firewall voor toegang tot de database-engine](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Stel uw Windows-firewall open voor toegang van Azure Database Migration Service tot de brondatabase van SQL Server. Standaard verloopt dit via TCP-poort 1433. Als uw standaardexemplaar op een andere poort luistert, voegt u deze toe aan de firewall.
- Als u meerdere benoemde SQL Server-exemplaren uitvoert met behulp van dynamische poorten, kunt u desgewenst de SQL Browser Service inschakelen en toegang tot de UDP-poort 1434 via uw firewalls toestaan, zodat Azure Database Migration Service verbinding kan maken met een benoemd exemplaar op uw bronserver.
- Wanneer u een firewallapparaat gebruikt voor de brondatabase(s), moet u mogelijk firewallregels toevoegen om voor Azure Database Migration Service toegang tot de brondatabase(s) voor de migratie toe te staan.
- Maak een [IP-firewallregel](../azure-sql/database/firewall-configure.md) op serverniveau voor Azure SQL Database om voor Azure Database Migration Service toegang tot de doeldatabase toe te staan. Geef het subnetbereik van het virtuele netwerk op dat wordt gebruikt voor Azure Database Migration Service.
- Zorg ervoor dat de referenties waarmee verbinding wordt gemaakt met het SQL Server-bronexemplaar [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql)-machtigingen hebben.
- Zorg ervoor dat de referenties waarmee verbinding wordt gemaakt met het Azure SQL Database-doelexemplaar [CONTROL DATABASE](/sql/t-sql/statements/grant-database-permissions-transact-sql)-machtiging hebben op de doeldatabases.

## <a name="assess-your-on-premises-database"></a>Uw on-premises resources evalueren

Voordat u gegevens uit een SQL Server-exemplaar naar een individuele of pooldatabase in Azure SQL Database kunt migreren, moet u controleren of de SQL Server-database blokkerende problemen bevat die mogelijk de migratie verhinderen. Volg met behulp van de Data Migration Assistant de stappen in het artikel [Uitvoeren van een SQL Server-migratie-evaluatie](/sql/dma/dma-assesssqlonprem) uit om de on-premises database-evaluatie uit te voeren. Hier volgt een samenvatting van de vereiste stappen:

1. Selecteer het pictogram Nieuw (+) in de Data Migration Assistant, en selecteer vervolgens het projecttype **Evaluatie**.
2. Geef een projectnaam op. Selecteer uit het vervolgkeuzemenu **Type evaluatie** **Database-engine**, selecteer in het tekstvak **Bronservertype** **SQL Server**, selecteer in het tekstvak **Doelservertype** **Azure SQL Database** en selecteer vervolgens **Maken** om het project te maken.

    Wanneer u de SQL Server-brondatabase controleert die u naar een individuele of pooldatabase in Azure SQL Database wilt migreren, kunt u kiezen voor een of beide van de volgende typen evaluatierapport:

   - Databasecompatibiliteit controleren
   - Functiepariteit controleren

    Beide rapporttypen zijn standaard geselecteerd.

3. Selecteer in de Data Migration Assistant, in het scherm **Opties**, **Volgende**.
4. Geef in het scherm **Bronnen selecteren**, in het dialoogvenster **Verbinding maken met een server**, de verbindingsdetails met uw SQL Server op, en selecteer vervolgens **Verbinding maken**.
5. Selecteer in het dialoogvenster **Bronnen toevoegen** **Adventureworks2016**, selecteer **Toevoegen**, en selecteer vervolgens **Evaluatie starten**.

    > [!NOTE]
    > Als u SSIS gebruikt, wordt de evaluatie van de bron-SSISDB momenteel niet door DMA ondersteund. SSIS-projecten/-pakketten worden echter beoordeeld/gevalideerd als ze zijn geïmplementeerd in de doel-SSISDB die wordt gehost door Azure SQL Database. Zie het artikel [Pakketten van SQL Server Integration Services migreren naar Azure](./how-to-migrate-ssis-packages.md) voor meer informatie over het migreren van SSIS-pakketten.

    Wanneer de evaluatie voltooid is, worden de resultaten weergegeven zoals in de volgende afbeelding:

    ![Gegevensmigratie beoordelen](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    Voor databases in Azure SQL Database identificeren de beoordelingen problemen met betrekking tot functiepariteit en problemen die de migratie blokkeren bij een implementatie naar een individuele of pooldatabase.

    - De categorie **SQL Server-functiepariteit** biedt een uitgebreide set met aanbevelingen, alternatieve methoden die beschikbaar zijn in Azure, en beperkende stappen voor het plannen van de inzet in uw migratieprojecten.
    - De categorie **compatibiliteitsproblemen** identificeert gedeeltelijk ondersteunde of niet-ondersteunde functies die compatibiliteitsproblemen laten zien die mogelijk de migratie van SQL Server-databases naar Azure SQL Database blokkeren. Aanbevelingen om deze problemen op te lossen worden ook gegeven.

6. Bekijk de resultaten van de evaluatie voor migratieblokkerende problemen en problemen met de functiepariteit door de specifieke opties te selecteren.

## <a name="migrate-the-sample-schema"></a>Het voorbeeldschema migreren

Wanneer u tevreden bent over de evaluatie en overtuigd bent dat de geselecteerde database een goede kandidaat is voor migratie naar een individuele of pooldatabase in Azure SQL Database, kunt u de DMA gebruiken om het schema naar Azure SQL Database te migreren.

> [!NOTE]
> Voordat u een migratieproject in Data Migration Assistant maakt, moet u ervoor zorgen dat u in Azure al een database hebt ingericht zoals vermeld in de vereisten. 

> [!IMPORTANT]
> Als u SSIS gebruikt, biedt DMA momenteel geen ondersteuning voor de migratie van de bron-SSISDB, maar u kunt uw SSIS-projecten/-pakketten opnieuw implementeren naar de doel-SSISDB die wordt gehost door Azure SQL Database. Zie het artikel [Pakketten van SQL Server Integration Services migreren naar Azure](./how-to-migrate-ssis-packages.md) voor meer informatie over het migreren van SSIS-pakketten.

Voer de volgende stappen uit om het schema **Adventureworks2016** te migreren naar een individuele of pooldatabase in Azure SQL Database:

1. Selecteer het pictogram Nieuw (+) in de Data Migration Assistant, en selecteer vervolgens onder **Projecttype****Migratie**.
2. Geef een projectnaam op, selecteer in het tekstvak **Bronservertype****SQL Server** en in het tekstvak **Doelservertype****Azure SQL Database**.
3. Selecteer onder **Migratiebereik**, selecteer **Alleen schema**.

    Na het uitvoeren van de vorige stappen, moet de Data Migration Assistant-interface worden weergegeven zoals in de volgende afbeelding:

    ![Data Migration Assistant-Project maken](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. Selecteer **Maken** om het project te maken.
5. Geef in de Data Migration Assistant de details van de bronverbinding voor uw SQL Server op, selecteer **Connect**, en selecteer vervolgens de **Adventureworks2016** database.

    ![Details Data Migration Assistant Bronverbinding](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. Selecteer **Volgende** onder **Verbinding maken met doelserver**, geef de details van de doelverbinding voor de Azure SQL-database op, selecteer **Verbinden** en selecteer vervolgens de database **AdventureWorksAzure** die u vooraf hebt ingericht in Azure SQL Database.

    ![Details Data Migration Assistant-doelverbinding](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. Selecteer **Volgende** om door te gaan naar het scherm **Objecten selecteren** waarin u de schemaobjecten in de **Adventureworks2016**-database kunt opgeven, die moeten worden geïmplementeerd naar Azure SQL Database.

    Standaard worden alle objecten geselecteerd.

    ![SQL-scripts genereren](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. Selecteer **SQL-script genereren** voor het maken van de SQL-scripts en controleer vervolgens de scripts op fouten.

    ![Schemascript](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. Selecteer **Schema implementeren** om het schema te implementeren naar Azure SQL Database, en nadat het schema is geïmplementeerd, controleer de doelserver op eventuele afwijkingen.

    ![Schema implementeren](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij Azure Portal. Zoek en selecteer **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-sql-server-to-azure-sql/portal-select-subscription-1.png)

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer **Registreren** voor **Microsoft.DataMigration**.

    ![Resourceprovider registreren](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Een instantie maken

1. Selecteer in het menu van de Azure-portal of op de **startpagina** de optie **Een resource maken**. Zoek en selecteer **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-sql-server-to-azure-sql/dms-create-1.png)
  
3. Ga in het basisscherm **Migratieservice maken** als volgt te werk:

     - Selecteer het abonnement.
     - Maak een nieuwe resourcegroep of kies een bestaande resourcegroep.
     - Geef een naam op voor het exemplaar van de Azure Database Migration Service.
     - Selecteer de locatie waarop u het exemplaar van Azure Database Migration Service wilt maken.
     - Kies **Azure** als de servicemodus.
     - Selecteer een prijscategorie. Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![Basisinstellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-sql-server-to-azure-sql/dms-settings-2.png)

     - Selecteer **Volgende: Netwerken**.

4. Ga in het netwerkscherm **Migratieservice maken** als volgt te werk:

    - Selecteer een bestaand virtueel netwerk of maak een nieuw netwerk. Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bron-SQL Server en het doel-Azure SQL Database-exemplaar. Zie het artikel [Een virtueel netwerk maken met de Azure-portal](../virtual-network/quick-create-portal.md) voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

    ![Netwerkinstellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-sql-server-to-azure-sql/dms-settings-3.png)

    - Selecteer **Beoordelen en maken** om de service te maken.

## <a name="create-a-migration-project"></a>Een migratieproject maken

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.

1. Selecteer **Alle services** in het menu van Azure Portal. Zoek en selecteer **Azure Database Migration Service**.

     ![Zoek alle exemplaren van Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. Selecteer in het scherm **Azure Database Migration Service** het exemplaar van Azure Database Migration Service dat u hebt gemaakt.

3. Selecteer **Nieuw migratieproject**.

     ![Zoek uw exemplaar van Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. Geef in het scherm **Nieuw migratie project** een naam op voor het project, Selecteer in het tekstvak **type bron server** de optie **SQL Server**, Selecteer in het tekstvak **doel server type** de optie **Azure SQL database** en klik vervolgens voor * * type migratie activiteit kiezen * *, selecteer **gegevens migratie**.

    ![Azure Database Migration Service-project maken](media/tutorial-sql-server-to-azure-sql/dms-create-project-2.png)

5. Selecteer **Maken en uitvoeren van de activiteit** om het project te maken en de migratieactiviteit uit te voeren.

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Bron selecteren** de details van de verbinding op voor het bron-SQL Server-exemplaar.

    Zorg ervoor dat u een Fully Qualified Domain Name (FQDN) gebruikt voor de naam van het bron-SQL Server-exemplaar. U kunt ook het IP-adres gebruiken voor situaties waarin DNS-naamomzetting niet mogelijk is.

2. Als u geen vertrouwd certificaat op de bronserver hebt geïnstalleerd, selecteert u het selectievakje **Servercertificaat vertrouwen**.

    Wanneer geen vertrouwd certificaat is geïnstalleerd, genereert SQL Server een zelfondertekend certificaat wanneer het exemplaar wordt gestart. Dit certificaat wordt gebruikt voor het versleutelen van de referenties voor clientverbindingen.

    > [!CAUTION]
    > TLS-verbindingen die zijn versleuteld met behulp van een zelfondertekend certificaat bieden geen sterke beveiliging. Ze zijn vatbaar voor man-in-the-middle-aanvallen. U moet niet vertrouwen op TLS met behulp van zelfondertekende certificaten in een productieomgeving of op servers die zijn verbonden met internet.

    > [!IMPORTANT]
    > Als u SSIS gebruikt, biedt DMA momenteel geen ondersteuning voor de migratie van de bron-SSISDB, maar u kunt uw SSIS-projecten/-pakketten opnieuw implementeren naar de doel-SSISDB die wordt gehost door Azure SQL Database. Zie het artikel [Pakketten van SQL Server Integration Services migreren naar Azure](./how-to-migrate-ssis-packages.md) voor meer informatie over het migreren van SSIS-pakketten.

   ![Brondetails](media/tutorial-sql-server-to-azure-sql/dms-source-details-2.png)
   
3. Selecteer **volgende: data bases selecteren**.

## <a name="select-databases-for-migration"></a>Data bases voor migratie selecteren

Selecteer alle data bases of specifieke data bases die u naar Azure SQL Database wilt migreren. DMS biedt u de verwachte migratie tijd voor geselecteerde data bases. Als de downtime van de migratie acceptabel is, gaat u door met de migratie. Als de downtime van de migratie niet acceptabel is, kunt u overwegen om te migreren naar een [SQL Managed instance met bijna-nul downtime](tutorial-sql-server-managed-instance-online.md) of contact op te nemen met het [DMS-team](mailto:DMSFeedback@microsoft.com) voor andere opties. 

1. Kies de data base (s) die u wilt migreren uit de lijst met beschik bare data bases. 
1. Bekijk de verwachte downtime. Als het acceptabel is, selecteert u **volgende: doel >>selecteren**

   ![Bron databases](media/tutorial-sql-server-to-azure-sql/select-database.png)



## <a name="specify-target-details"></a>Doeldetails opgeven

1. Geef in het scherm **doel selecteren** verificatie-instellingen op voor uw Azure SQL database. 

   ![Doel selecteren](media/tutorial-sql-server-to-azure-sql/select-target.png)

1. Selecteer **Volgende: Toewijzen aan doeldatabases** en wijs de bron- en doeldatabase voor de migratie toe.

    Als de doeldatabase de naam van de dezelfde database als de bron-database bevat, wordt in Azure Database Migration Service de doeldatabase standaard geselecteerd.

    ![Toewijzen aan doeldatabases](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity-2.png)

1. Selecteer **Volgende: Migratie-instellingen configureren**, breid de tabelvermelding uit en beoordeel de lijst beïnvloede velden.

    Azure Database Migration Service selecteert automatisch alle lege brontabellen die beschikbaar zijn op het Azure SQL Database-doelexemplaar. Als u tabellen wilt terug migreren die al gegevens bevatten, moet u expliciet de tabellen op deze blade selecteren.

    ![Selecteer tabellen](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity-2.png)

1. Selecteer **Volgende: Samenvatting**, controleer de migratieconfiguratie en geef in het tekstvak **Naam activiteit** een naam op voor de migratieactiviteit.

    ![Validatieoptie kiezen](media/tutorial-sql-server-to-azure-sql/dms-configuration-2.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

- Selecteer **Migratie starten**.

    Het venster van de migratieactiviteit wordt weergegeven, en de **Status** van de activiteit is **In behandeling**.

    ![Activiteitenstatus](media/tutorial-sql-server-to-azure-sql/dms-activity-status-1.png)

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer op het scherm van de activiteitenmigratie **Vernieuwen** om de weergave bij te werken totdat de **Status** van de migratie als **Voltooid** wordt weergegeven.

    ![Activiteitenstatus voltooid](media/tutorial-sql-server-to-azure-sql/dms-completed-activity-1.png)

2. Verifieer de doeldatabase(s) in het doel **Azure SQL Database**.

### <a name="additional-resources"></a>Aanvullende bronnen

- Zie het artikel [Wat is de Azure Database Migration Service?](./dms-overview.md) voor informatie over Azure Database Migration Service.
- Zie het artikel [Wat is de service Azure SQL Database?](../azure-sql/database/sql-database-paas-overview.md) voor informatie over Azure SQL Database.
