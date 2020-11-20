---
title: SSIS-pakketten opnieuw implementeren naar SQL-Data Base 1
titleSuffix: Azure Database Migration Service
description: Meer informatie over het migreren of opnieuw implementeren van SQL Server Integration Services-pakketten en-projecten naar Azure SQL Database van de ene data base met behulp van de Azure Database Migration Service en Data Migration Assistant.
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
ms.openlocfilehash: e6f94c006de8914fe3ae27cdb8ac4d75a0ac49cc
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962991"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>SSIS-pakketten opnieuw implementeren om te Azure SQL Database met Azure Database Migration Service

Als u SQL Server Integration Services (SSIS) gebruikt en u uw SSIS-projecten/-pakketten wilt migreren van de bron-SSISDB die wordt gehost door SQL Server naar de doel-SSISDB die door Azure SQL Database worden gehost, kunt u ze opnieuw implementeren met de wizard Integratie Services implementeren. U kunt de wizard starten vanuit SQL Server Management Studio (SSMS).

Als de gebruikte SSIS-versie ouder is dan 2012, moet u deze eerst converteren met behulp van de wizard Integratie Services-project conversie, die ook kan worden gestart vanuit SSMS, voordat u uw SSIS-projecten/-pakketten opnieuw implementeert in het implementatie model van het project. Zie het artikel [Converting projects to the project Deployment model](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)(Engelstalig) voor meer informatie.

> [!NOTE]
> De Azure Database Migration Service (DMS) biedt momenteel geen ondersteuning voor de migratie van een bron-SSISDB naar Azure SQL Database, maar u kunt uw SSIS-projecten/-pakketten opnieuw implementeren met behulp van het volgende proces.

In dit artikel leert u het volgende:
> [!div class="checklist"]
>
> * Beoordeel de bron-SSIS-projecten/-pakketten.
> * SSIS-projecten/-pakketten migreren naar Azure.

## <a name="prerequisites"></a>Vereisten

Als u deze stappen wilt uitvoeren, hebt u het volgende nodig:

* SSMS-versie 17,2 of hoger.
* Een exemplaar van de doel database server voor het hosten van SSISDB. Als u er nog geen hebt, maakt u een [logische SQL-Server](../azure-sql/database/logical-servers.md) (zonder een Data Base) met behulp van de Azure portal door te navigeren naar het [formulier](https://ms.portal.azure.com/#create/Microsoft.SQLServer)SQL Server (alleen logische server).
* SSIS moet worden ingericht in Azure Data Factory (ADF) met Azure-SSIS Integration Runtime (IR) met de doel-SSISDB die wordt gehost door SQL Database (zoals beschreven in het artikel [richt de Azure-SSIS Integration runtime in azure Data Factory in](../data-factory/tutorial-deploy-ssis-packages-azure.md)).

## <a name="assess-source-ssis-projectspackages"></a>Bron-SSIS-projecten/-pakketten beoordelen

Hoewel de evaluatie van de bron-SSISDB nog niet is geïntegreerd in de data base Migration Assistant (DMA) of de Azure Database Migration Service (DMS), worden uw SSIS-projecten/-pakketten beoordeeld/gevalideerd omdat ze opnieuw worden geïmplementeerd naar de doel-SSISDB die worden gehost door Azure SQL Database.

## <a name="migrate-ssis-projectspackages"></a>SSIS-projecten/-pakketten migreren

Voer de volgende stappen uit om SSIS-projecten/-pakketten naar Azure SQL Database te migreren.

1. Open SSMS en selecteer vervolgens **Opties** om het dialoog venster **verbinding maken met server** weer te geven.

2. Geef op het tabblad **aanmelding** de gegevens op die nodig zijn om verbinding te maken met de server die als host moet fungeren voor de doel-SSISDB.

    ![Tabblad SSIS-aanmelding](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. Selecteer, op het tabblad **verbindings eigenschappen** , in het tekstvak **verbinding maken met data base** , **SSISDB**, en selecteer vervolgens **verbinding maken**.

    ![Tabblad Eigenschappen van SSIS-verbinding](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. Vouw in het Objectverkenner SSMS het knoop punt **Integration Services Catalogs** uit, vouw **SSISDB** uit en als er geen bestaande mappen zijn, klikt u met de rechter muisknop op **SSISDB** en maakt u een nieuwe map.

5. Vouw onder **SSISDB** een wille keurige map uit, klik met de rechter muisknop op **projecten** en selecteer vervolgens **project implementeren**.

    ![SSIS SSISDB-knoop punt uitgebreid](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. Controleer de informatie in de wizard Integratie Services implementeren op de pagina **Inleiding** en selecteer **volgende**.

    ![Introductie pagina van wizard implementatie](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. Geef op de pagina **bron selecteren** het bestaande SSIS-project op dat u wilt implementeren.

    Als SSMS ook is verbonden met de SQL Server die als host fungeert voor de bron SSISDB, selecteert u **Integration Services Catalog** en voert u de server naam en het pad van het project in uw catalogus in om uw project rechtstreeks te implementeren.

    U kunt ook een **project implementatie bestand** selecteren en vervolgens het pad naar een bestaand project implementatie bestand (. ispac) opgeven om uw project te implementeren.

    ![Wizard implementatie bron pagina selecteren](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. Selecteer **Next**.
9. Geef op de pagina **doel selecteren** het doel voor uw project op.

    a. Voer in het tekstvak Server naam de volledig gekwalificeerde server naam in (<server_name>. database.windows.net).

    b. Geef de verificatie gegevens op en selecteer vervolgens **verbinding maken**.

    ![Wizard implementatie-doel pagina selecteren](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Selecteer **Bladeren** om de doelmap op te geven in SSISDB en selecteer **volgende**.

    > [!NOTE]
    > De knop **volgende** is alleen ingeschakeld nadat u **verbinding maken** hebt geselecteerd.

10. Bekijk eventuele fouten/waarschuwingen op de pagina **valideren** en wijzig indien nodig de pakketten dienovereenkomstig.

    ![Pagina validatie van wizard implementatie](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Selecteer **Next**.

12. Controleer de implementatie-instellingen op de pagina **controleren** .

    > [!NOTE]
    > U kunt de instellingen wijzigen door **vorige** te selecteren of door een van de stap koppelingen in het linkerdeel venster te selecteren.

13. Selecteer **implementeren** om het implementatie proces te starten.

14. Nadat het implementatie proces is voltooid, kunt u de resultaten pagina bekijken, waarin het slagen of mislukken van elke implementatie actie wordt weer gegeven.
    a. Als een actie is mislukt, selecteert u in de kolom **resultaat** een **niet** -uitleg van de fout weer gegeven.
    b. Selecteer eventueel **rapport opslaan** om de resultaten op te slaan in een XML-bestand.

15. Selecteer **sluiten** om de wizard Integratie Services implementeren af te sluiten.

Als de implementatie van uw project zonder fouten is geslaagd, kunt u alle pakketten selecteren die in de Azure-SSIS IR worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

* Bekijk de richt lijnen voor migratie in de micro soft- [Data Base-migratie handleiding](https://datamigration.microsoft.com/).