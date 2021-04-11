---
title: SSIS-pakketten migreren naar een beheerd exemplaar van SQL
titleSuffix: Azure Database Migration Service
description: Informatie over het migreren van SQL Server Integration Services (SSIS)-pakketten en-projecten naar een met Azure SQL beheerd exemplaar met behulp van de Azure Database Migration Service of de Data Migration Assistant.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: 1f6df64a66c0700f3a13a40ea5046515a2bf1a51
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643844"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-managed-instance"></a>SQL Server Integration Services-pakketten migreren naar een beheerd exemplaar van Azure SQL
Als u SQL Server Integration Services (SSIS) gebruikt en u uw SSIS-projecten/-pakketten wilt migreren van de bron-SSISDB die wordt gehost door SQL Server naar de doel-SSISDB die wordt gehost door een door Azure SQL beheerd exemplaar, kunt u Azure Database Migration Service gebruiken.

Als de gebruikte SSIS-versie ouder is dan 2012 of als u een niet-SSISDB gebruikt voor het migreren van uw SSIS-projecten/-pakketten, moet u deze converteren met behulp van de wizard Integratie Services-project conversie, die ook kan worden gestart vanuit SSMS. Zie het artikel [Converting projects to the project Deployment model](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#convert)(Engelstalig) voor meer informatie.

> [!NOTE]
> Azure Database Migration Service (DMS) biedt momenteel geen ondersteuning voor Azure SQL Database als doel migratie bestemming. Als u SSIS-projecten/-pakketten opnieuw wilt implementeren op Azure SQL Database, raadpleegt u het artikel [SQL Server Integration Services pakketten opnieuw implementeren voor Azure SQL database](./how-to-migrate-ssis-packages.md).

In dit artikel leert u het volgende:
> [!div class="checklist"]
>
> * Beoordeel de bron-SSIS-projecten/-pakketten.
> * SSIS-projecten/-pakketten migreren naar Azure.

## <a name="prerequisites"></a>Vereisten

Als u deze stappen wilt uitvoeren, hebt u het volgende nodig:

* Als u een Microsoft Azure Virtual Network voor de Azure Database Migration Service wilt maken met behulp van het Azure Resource Manager implementatie model, dat een site-naar-site-verbinding met uw on-premises bron servers biedt met behulp van [ExpressRoute](../expressroute/expressroute-introduction.md) of [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Zie het artikel [netwerktopologieën voor SQL Managed instance-migraties met Azure database Migration service]( https://aka.ms/dmsnetworkformi)voor meer informatie. Voor meer informatie over het maken van een virtueel netwerk raadpleegt u de [documentatie over virtuele netwerken](../virtual-network/index.yml) en dan met name de quickstart-artikelen met stapsgewijze informatie.
* Om ervoor te zorgen dat de regels voor de netwerk beveiligings groep voor het virtuele netwerk niet de uitgaande poort 443 van ServiceTag voor ServiceBus, Storage en AzureMonitor blok keren. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/virtual-network-vnet-plan-design-arm.md) voor meer informatie over verkeer filteren van verkeer via de netwerkbeveiligingsgroep voor virtuele netwerken.
* De Windows Firewall configureren [voor toegang tot de bron database-engine](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Als u uw Windows Firewall wilt openen om de Azure Database Migration Service toegang te geven tot de bron SQL Server, die standaard TCP-poort 1433 is.
* Als u meerdere benoemde SQL Server-exemplaren uitvoert met behulp van dynamische poorten, kunt u desgewenst de SQL Browser Service inschakelen en toegang tot de UDP-poort 1434 via uw firewalls toestaan, zodat de Azure Database Migration Service verbinding kan maken met een benoemd exemplaar op uw bronserver.
* Als u een firewallapparaat gebruikt vóór de brondatabases, moet u mogelijk firewallregels toevoegen om de Azure Database Migration Service toegang te geven tot de brondatabase(s) voor migratie. Hetzelfde geldt voor bestanden via SMB-poort 445.
* Een door SQL beheerd exemplaar voor het hosten van SSISDB. Als u er een wilt maken, volgt u de details in het artikel [een door Azure SQL beheerd exemplaar maken](../azure-sql/managed-instance/instance-create-quickstart.md).
* Om ervoor te zorgen dat de aanmeldingen die worden gebruikt om verbinding te maken met de bron SQL Server en het beheerde exemplaar van het doel lid zijn van de serverrol sysadmin.
* Als u wilt controleren of SSIS is ingericht in Azure Data Factory (ADF) met Azure-SSIS Integration Runtime (IR) met de doel-SSISDB die wordt gehost door een SQL Managed instance (zoals beschreven in het artikel, [maakt u de Azure-SSIS Integration runtime in azure Data Factory](../data-factory/create-azure-ssis-integration-runtime.md)).

## <a name="assess-source-ssis-projectspackages"></a>Bron-SSIS-projecten/-pakketten beoordelen

Hoewel de evaluatie van de bron-SSISDB nog niet is geïntegreerd in de data base Migration Assistant (DMA), worden uw SSIS-projecten/-pakketten beoordeeld/gevalideerd omdat ze opnieuw worden geïmplementeerd naar de doel-SSISDB die worden gehost op een door Azure SQL beheerd exemplaar.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

    ![Portal-abonnementen weergeven](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Een Azure Database Migration Service-exemplaar maken

1. Selecteer in de Azure-portal + **Een resource maken**, zoek naar **Azure Database Migration Service** en selecteer vervolgens **Azure Database Migration Service** in de vervolgkeuzelijst.

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer de locatie waarop u het exemplaar van DMS wilt maken.

5. Selecteer een bestaand virtueel netwerk of maak er een.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bron SQL Server en het doel exemplaar van Azure SQL Managed instance.

    Zie het artikel [Een virtueel netwerk maken met de Azure-portal](../virtual-network/quick-create-portal.md) voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

    Zie het artikel [netwerk topologieën voor Azure SQL Managed instance-migraties met behulp van de Azure database Migration service](./resource-network-topologies.md)voor meer informatie.

6. Selecteer een prijscategorie.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![DMS-service starten](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Een migratieproject maken

Nadat er een exemplaar van de service is gemaakt, zoekt u het exemplaar in de Azure-portal, opent u het en maakt u vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

    ![Zoek alle exemplaren van de Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. Zoek in het scherm **Azure Database Migration Service** naar de naam van het exemplaar dat u hebt gemaakt en selecteer vervolgens het exemplaar.

3. Selecteer + **Nieuw migratieproject**.

4. Geef in het scherm **Nieuw migratie project** een naam op voor het project, Selecteer in het tekstvak **type bron server** de optie **SQL Server**, Selecteer in het tekstvak **doel server type** de optie **Azure SQL Managed instance** en selecteer vervolgens voor **type activiteit** **SSIS-pakket migratie**.

   ![DMS-project maken](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. Selecteer **Maken** om het project te maken.

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Brongegevens** de verbindingsgegevens op voor de brondatabase van SQL Server.

2. Als u geen vertrouwd certificaat hebt geïnstalleerd op de server, schakelt u het selectievakje **Servercertificaat vertrouwen** in.

    Wanneer er geen vertrouwd certificaat is geïnstalleerd, genereert SQL Server een zelfondertekend certificaat wanneer het exemplaar wordt gestart. Dit certificaat wordt gebruikt voor het versleutelen van de referenties voor clientverbindingen.

    > [!CAUTION]
    > TLS-verbindingen die worden versleuteld met behulp van een zelfondertekend certificaat bieden geen sterke beveiliging. Ze zijn vatbaar voor man-in-the-middle-aanvallen. U moet niet vertrouwen op TLS met behulp van zelfondertekende certificaten in een productieomgeving of op servers die zijn verbonden met internet.

   ![Brondetails](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Selecteer **Opslaan**.

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Geef in het scherm **doel Details van migratie** de verbindings gegevens voor het doel op.

     ![Doeldetails](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Selecteer **Opslaan**.

## <a name="review-the-migration-summary"></a>Migratieoverzicht controleren

1. Geef in het tekstvak **Naam activiteit** van het scherm **Migratieoverzicht** een naam op voor de migratieactiviteit.

2. Voor de **optie voor het SSIS-project (en) en de omgeving (en)** moet u opgeven of bestaande SSIS-projecten en-omgevingen moeten worden overschreven of genegeerd.

    ![Overzicht van migratieproject](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Bekijk en controleer de gegevens van het migratieproject.

## <a name="run-the-migration"></a>De migratie uitvoeren

* Selecteer **Migratie uitvoeren**.

## <a name="next-steps"></a>Volgende stappen

* Bekijk de richt lijnen voor migratie in de micro soft- [Data Base-migratie handleiding](https://datamigration.microsoft.com/).