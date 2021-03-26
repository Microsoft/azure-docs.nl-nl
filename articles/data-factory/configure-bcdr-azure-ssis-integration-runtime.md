---
title: Azure-SSIS Integration runtime configureren voor bedrijfs continuïteit en herstel na nood gevallen (BCDR)
description: In dit artikel wordt beschreven hoe u Azure SSIS Integration runtime in Azure Data Factory kunt configureren met een Azure SQL Database/Managed instance failover groep voor bedrijfs continuïteit en herstel na nood gevallen (BCDR).
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/05/2021
ms.openlocfilehash: a426ee39ba3c0f50b9a6c1fb9c7de1ef8e7291b2
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566350"
---
# <a name="configure-azure-ssis-integration-runtime-for-business-continuity-and-disaster-recovery-bcdr"></a>Azure-SSIS Integration runtime configureren voor bedrijfs continuïteit en herstel na nood gevallen (BCDR) 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Azure SQL Database/Managed instance en SQL Server Integration Services (SSIS) in Azure Data Factory (ADF) kunnen worden gecombineerd als de aanbevolen PaaS-oplossing (all-platform as a Service) voor SQL Server migratie. U kunt uw SSIS-projecten implementeren in SSIS Catalog data base (SSISDB) die wordt gehost door Azure SQL Database/beheerd exemplaar en uw SSIS-pakketten uitvoeren op Azure SSIS Integration runtime (IR) in ADF.

Voor bedrijfs continuïteit en herstel na nood gevallen (BCDR) kan Azure SQL Database/beheerd exemplaar worden geconfigureerd met een [geo-replicatie/failover-groep](../azure-sql/database/auto-failover-group-overview.md), waarbij SSISDB in een primaire Azure-regio met lees-en schrijf toegang (primaire rol) voortdurend wordt gerepliceerd naar een secundaire regio met alleen-lezen toegang (secundaire rol). Wanneer een nood situatie optreedt in de primaire regio, wordt een failover geactiveerd, waarbij de primaire en secundaire SSISDBs rollen zullen wisselen.

Voor BCDR kunt u ook een Dual standby-Azure SSIS IR-paar configureren dat wordt gesynchroniseerd met de failovergroep Azure SQL Database/Managed instance. Op die manier kunt u een combi natie van een Azure-SSIS-IRs die op elk gewenst moment toegang heeft tot de primaire SSISDB om pakketten op te halen en uit te voeren, evenals logboeken voor het uitvoeren van pakket uitvoer (primaire rol), terwijl de andere alleen hetzelfde kan doen voor pakketten die elders worden geïmplementeerd, bijvoorbeeld Azure Files (secundaire rol). Wanneer er een SSISDB-failover optreedt, worden de primaire en secundaire Azure-SSIS-IRs ook gebruikt om rollen te wisselen en als beide worden uitgevoerd, is er bijna nul uitval tijd.

In dit artikel wordt beschreven hoe u Azure-SSIS IR configureert met een Azure SQL Database/Managed instance failover Group voor BCDR.

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-database-failover-group"></a>Een dubbele stand-by Azure-SSIS IR-set configureren met Azure SQL Database failovergroep

Voer de volgende stappen uit als u een dubbele stand-by Azure-SSIS IR-paar wilt configureren dat in synchronisatie met Azure SQL Database failover Group werkt.

1. Met behulp van Azure Portal/ADF-gebruikers interface kunt u een nieuwe Azure-SSIS IR met uw primaire Azure SQL Database-Server maken om SSISDB in de primaire regio te hosten. Als u een bestaande Azure-SSIS IR hebt die al is gekoppeld aan SSIDB die wordt gehost door uw primaire Azure SQL Database-Server en deze nog steeds wordt uitgevoerd, moet u deze eerst stoppen om deze opnieuw te configureren. Dit is uw primaire Azure-SSIS IR.

   Wanneer u [selecteert om SSISDB te gebruiken](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) op de pagina **implementatie-instellingen** van het deel venster Setup van **Integration runtime** , selecteert u ook het selectie vakje **dubbele stand-by Azure-SSIS Integration runtime koppelen met SSISDB-failover** . Voer bij **dubbele stand-by-koppelings naam** een naam in om uw paar primaire en secundaire Azure-SSIS-IRs te identificeren. Wanneer u klaar bent met het maken van uw primaire Azure-SSIS IR, wordt het gestart en gekoppeld aan een primaire SSISDB die namens u wordt gemaakt met lees-/schrijftoegang. Als u het zojuist hebt geconfigureerd, moet u het opnieuw starten.

1. Met Azure Portal kunt u controleren of de primaire SSISDB is gemaakt op de **overzichts** pagina van de primaire Azure SQL database-server. Zodra de app is gemaakt, kunt u [een failovergroep voor de primaire en secundaire Azure SQL database servers maken en SSISDB toevoegen aan de groep](../azure-sql/database/failover-group-add-single-database-tutorial.md?tabs=azure-portal#2---create-the-failover-group) op de pagina **failover-groepen** . Als uw failovergroep is gemaakt, kunt u controleren of de primaire SSISDB is gerepliceerd naar een secundaire-server met alleen-lezen toegang op de pagina **overzicht** van de secundaire Azure SQL database-servers.

1. Met behulp van Azure Portal/ADF-gebruikers interface kunt u een andere Azure-SSIS IR met uw secundaire Azure SQL Database-Server maken om SSISDB in de secundaire regio te hosten. Dit is uw secundaire Azure-SSIS IR. Zorg ervoor dat alle resources waarvan het afhankelijk is, zijn gemaakt in de secundaire regio, bijvoorbeeld Azure Storage voor het opslaan van aangepaste Setup-scripts/-bestanden, ADF voor het implementeren van het pakket en het plannen van pakketten, enzovoort.

   Wanneer u [selecteert om SSISDB te gebruiken](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) op de pagina **implementatie-instellingen** van het deel venster Setup van **Integration runtime** , selecteert u ook het selectie vakje **dubbele stand-by Azure-SSIS Integration runtime koppelen met SSISDB-failover** . Voer de naam van de **dubbele stand-by-set** in om de combi natie van primaire en secundaire Azure-SSIS-IRs te identificeren. Wanneer u klaar bent met het maken van de secundaire Azure-SSIS IR, wordt deze gestart en aan de secundaire SSISDB gekoppeld.

1. Als u een bijna nul uitval tijd wilt hebben wanneer er een SSISDB-failover optreedt, moet u de Azure-SSIS-IRs actief laten. Alleen uw primaire Azure-SSIS IR kan toegang krijgen tot de primaire SSISDB om pakketten op te halen en uit te voeren, evenals logboeken voor het uitvoeren van pakket uitvoer, terwijl uw secundaire Azure-SSIS IR alleen hetzelfde kan doen voor pakketten die elders worden geïmplementeerd, bijvoorbeeld in Azure Files.

   Als u de uitgevoerde kosten wilt minimaliseren, kunt u de secundaire Azure-SSIS IR stoppen nadat deze is gemaakt. Wanneer er een SSISDB-failover optreedt, worden de rollen door uw primaire en secundaire Azure-SSIS-IRs gewisseld. Als uw primaire Azure-SSIS IR is gestopt, moet u de computer opnieuw opstarten. Afhankelijk van of het wordt ingevoegd in een virtueel netwerk en de gebruikte injectie methode, duurt het binnen 5 minuten of ongeveer 20-30 minuten om het uit te voeren.

1. Als u [ADF gebruikt voor het indelen/plannen van pakket uitvoeringen](./how-to-invoke-ssis-package-ssis-activity.md), moet u ervoor zorgen dat alle relevante ADF-pijp lijnen met activiteiten voor het uitvoeren van SSIS-pakketten en de bijbehorende triggers worden gekopieerd naar uw secundaire ADF, waarbij de triggers in eerste instantie zijn uitgeschakeld. Wanneer er een SSISDB-failover optreedt, moet u deze inschakelen.

1. U kunt [uw Azure SQL database-failovergroep testen](../azure-sql/database/failover-group-add-single-database-tutorial.md?tabs=azure-portal#3---test-failover) en op [Azure-SSIS IR controle pagina in de ADF-Portal](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) controleren of uw primaire en secundaire Azure-SSIS-IRs rollen hebben gewisseld. 

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-managed-instance-failover-group"></a>Een dubbele stand-by Azure-SSIS IR-set configureren met een failovergroep voor Azure SQL Managed instance

Voer de volgende stappen uit als u een dubbele stand-by Azure-SSIS IR-koppeling wilt configureren die wordt gesynchroniseerd met de failovergroep van een Azure SQL Managed instance-groep.

1. Met Azure Portal kunt u [een failovergroep voor uw primaire en secundaire Azure SQL Managed instances maken](../azure-sql/managed-instance/failover-group-add-instance-tutorial.md?tabs=azure-portal) op de pagina **failover-groepen** van uw primaire Azure SQL Managed instance.

1. Met de Azure Portal/ADF-gebruikers interface kunt u een nieuwe Azure-SSIS IR maken met uw primaire Azure SQL Managed instance om SSISDB te hosten in de primaire regio. Als u een bestaande Azure-SSIS IR hebt die al is gekoppeld aan SSIDB die wordt gehost door uw primaire Azure SQL Managed instance en deze nog steeds wordt uitgevoerd, moet u deze eerst stoppen om deze opnieuw te configureren. Dit is uw primaire Azure-SSIS IR.

   Wanneer u [selecteert om SSISDB te gebruiken](./create-azure-ssis-integration-runtime.md#creating-ssisdb) op de pagina **implementatie-instellingen** van het deel venster Setup van **Integration runtime** , selecteert u ook het selectie vakje **dubbele stand-by Azure-SSIS Integration runtime koppelen met SSISDB-failover** . Voer bij **dubbele stand-by-koppelings naam** een naam in om uw paar primaire en secundaire Azure-SSIS-IRs te identificeren. Wanneer u klaar bent met het maken van uw primaire Azure-SSIS IR, wordt het gestart en gekoppeld aan een primaire SSISDB die namens u wordt gemaakt met lees-/schrijftoegang. Als u het zojuist hebt geconfigureerd, moet u het opnieuw starten. U kunt ook controleren of de primaire SSISDB is gerepliceerd naar een secundaire versie met alleen-lezen toegang op de pagina **overzicht** van uw secundaire Azure SQL Managed instance.

1. Met behulp van Azure Portal/ADF-gebruikers interface kunt u een andere Azure-SSIS IR maken met uw secundaire Azure SQL Managed instance om SSISDB in de secundaire regio te hosten. Dit is uw secundaire Azure-SSIS IR. Zorg ervoor dat alle resources waarvan het afhankelijk is, zijn gemaakt in de secundaire regio, bijvoorbeeld Azure Storage voor het opslaan van aangepaste Setup-scripts/-bestanden, ADF voor het implementeren van het pakket en het plannen van pakketten, enzovoort.

   Wanneer u [selecteert om SSISDB te gebruiken](./create-azure-ssis-integration-runtime.md#creating-ssisdb) op de pagina **implementatie-instellingen** van het deel venster Setup van **Integration runtime** , selecteert u ook het selectie vakje **dubbele stand-by Azure-SSIS Integration runtime koppelen met SSISDB-failover** . Voer de naam van de **dubbele stand-by-set** in om de combi natie van primaire en secundaire Azure-SSIS-IRs te identificeren. Wanneer u klaar bent met het maken van de secundaire Azure-SSIS IR, wordt deze gestart en aan de secundaire SSISDB gekoppeld.

1. Azure SQL Managed instance kan gevoelige gegevens in data bases beveiligen, zoals SSISDB, door ze te versleutelen met behulp van de database hoofd sleutel (DMK). DMK zelf is standaard versleuteld met behulp van de service Master Key (SMK). Op het moment van schrijven repliceert de failovergroep van de Azure SQL Managed instance niet de SMK van de primaire Azure SQL Managed instance, dus DMK en in de SSISDB kunnen niet worden ontsleuteld op het secundaire Azure SQL Managed instance nadat de failover is uitgevoerd. U kunt dit probleem omzeilen door een wachtwoord versleuteling toe te voegen voor DMK die moeten worden ontsleuteld op het secundaire Azure SQL Managed instance. Voer de volgende stappen uit met behulp van SSMS.

   1. Voer de volgende opdracht uit voor SSISDB in uw primaire Azure SQL Managed instance om een wacht woord toe te voegen voor het versleutelen van DMK.

      ```sql
      ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'YourPassword'
      ```
   
   1. Voer de volgende opdracht uit voor SSISDB in de primaire en secundaire Azure SQL Managed instances om het nieuwe wacht woord voor het ontsleutelen van DMK toe te voegen.

      ```sql
      EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB', @password = N'YourPassword', @action = N'add'
      ```

1. Als u een bijna nul uitval tijd wilt hebben wanneer er een SSISDB-failover optreedt, moet u de Azure-SSIS-IRs actief laten. Alleen uw primaire Azure-SSIS IR kan toegang krijgen tot de primaire SSISDB om pakketten op te halen en uit te voeren, evenals logboeken voor het uitvoeren van pakket uitvoer, terwijl uw secundaire Azure-SSIS IR alleen hetzelfde kan doen voor pakketten die elders worden geïmplementeerd, bijvoorbeeld in Azure Files.

   Als u de uitgevoerde kosten wilt minimaliseren, kunt u de secundaire Azure-SSIS IR stoppen nadat deze is gemaakt. Wanneer er een SSISDB-failover optreedt, worden de rollen door uw primaire en secundaire Azure-SSIS-IRs gewisseld. Als uw primaire Azure-SSIS IR is gestopt, moet u de computer opnieuw opstarten. Afhankelijk van of het wordt ingevoegd in een virtueel netwerk en de gebruikte injectie methode, duurt het binnen 5 minuten of ongeveer 20-30 minuten om het uit te voeren.

1. Als u [Azure SQL Managed instance agent gebruikt voor de indelings-en plannings pakket uitvoeringen](./how-to-invoke-ssis-package-managed-instance-agent.md), moet u ervoor zorgen dat alle relevante SSIS-taken met hun taak stappen en gekoppelde schema's worden gekopieerd naar uw secundaire Azure SQL Managed instance met de schema's die in eerste instantie zijn uitgeschakeld. Voer de volgende stappen uit met behulp van SSMS.

   1. Voor elke SSIS-taak klikt u met de rechter muisknop en selecteert u de **script taak als**, **maken op** en nieuw menu in **Query-Editor venster** vervolg keuzelijst items om het script te genereren.

      ![SSIS-taak script genereren](media/configure-bcdr-azure-ssis-integration-runtime/generate-ssis-job-script.png)

   1. Zoek voor elk gegenereerde SSIS-taak script de opdracht voor het uitvoeren van de `sp_add_job` opgeslagen procedure en Wijzig/Verwijder de waarde van de toewijzing naar een `@owner_login_name` argument.

   1. Voor elk bijgewerkt SSIS-taak script voert u dit uit op uw secundaire Azure SQL Managed instance om de taak te kopiëren met de taak stappen en de bijbehorende schema's.

   1. Maak met behulp van het volgende script een nieuwe T-SQL-taak om SSIS-taak planningen in of uit te scha kelen op basis van respectievelijk de primaire/secundaire SSISDB-rol, zowel in uw primaire als secundaire Azure SQL Managed instances, regel matig uitvoeren. Als er een SSISDB-failover plaatsvindt, worden SSIS-taak schema's die zijn uitgeschakeld, ingeschakeld en vice versa.

      ```sql
      IF (SELECT Top 1 role_desc FROM SSISDB.sys.dm_geo_replication_link_status WHERE partner_database = 'SSISDB') = 'PRIMARY'
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 0
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 1
         END
      ELSE
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 1
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 0
         END
      ```

1. Als u [ADF gebruikt voor het indelen/plannen van pakket uitvoeringen](./how-to-invoke-ssis-package-ssis-activity.md), moet u ervoor zorgen dat alle relevante ADF-pijp lijnen met activiteiten voor het uitvoeren van SSIS-pakketten en de bijbehorende triggers worden gekopieerd naar uw secundaire ADF, waarbij de triggers in eerste instantie zijn uitgeschakeld. Wanneer er een SSISDB-failover optreedt, moet u deze inschakelen.

1. U kunt [de failovergroep voor uw Azure SQL Managed instance testen](../azure-sql/managed-instance/failover-group-add-instance-tutorial.md?tabs=azure-portal#test-failover) en op [Azure-SSIS IR bewakings pagina in de ADF-Portal](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) controleren of uw primaire en secundaire Azure-SSIS-IRs rollen hebben gewisseld. 

## <a name="attach-a-new-azure-ssis-ir-to-existing-ssisdb-hosted-by-azure-sql-databasemanaged-instance"></a>Een nieuwe Azure-SSIS IR koppelen aan bestaande SSISDB die worden gehost door Azure SQL Database/beheerd exemplaar

Als er sprake is van een nood situatie en van invloed is op uw bestaande Azure-SSIS IR, maar niet Azure SQL Database/beheerde instantie in dezelfde regio, kunt u deze vervangen door een nieuwe in een andere regio. Voer de volgende stappen uit om uw bestaande door Azure SQL Database/beheerde instantie gehoste SSISDB te koppelen aan een nieuwe Azure-SSIS IR.

1. Als uw bestaande Azure-SSIS IR nog steeds wordt uitgevoerd, moet u het eerst stoppen met de gebruikers interface van Azure Portal/ADF of Azure PowerShell. Als de nood situatie ook van invloed is op ADF in dezelfde regio, kunt u deze stap overs Laan.

1. Gebruik SSMS om de volgende opdracht uit te voeren voor SSISDB in uw Azure SQL Database/Managed instance om de meta gegevens bij te werken waarmee verbindingen van uw nieuwe ADF/Azure-SSIS IR worden toegestaan.

   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name = 'YourNewADF', @integration_runtime_name = 'YourNewAzureSSISIR'
   ```

1. Maak met behulp van [Azure Portal/ADF-gebruikers interface](./create-azure-ssis-integration-runtime.md#use-the-azure-portal-to-create-an-integration-runtime) of [Azure PowerShell](./create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime)uw nieuwe ADF/Azure-SSIS IR met de naam *YourNewADF* / *YourNewAzureSSISIR* in een andere regio. Als u de gebruikers interface van Azure Portal/ADF gebruikt, kunt u de verbindings fout op de pagina **implementatie-instellingen** van het deel venster **Integration runtime-configuratie** negeren.

## <a name="next-steps"></a>Volgende stappen

U kunt deze andere configuratie opties voor uw Azure-SSIS IR overwegen:

- [Pakket archieven configureren voor uw Azure-SSIS IR](./create-azure-ssis-integration-runtime.md#creating-azure-ssis-ir-package-stores)

- [Aangepaste Setup configureren voor uw Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md)

- [De injectie van het virtuele netwerk voor uw Azure-SSIS IR configureren](./join-azure-ssis-integration-runtime-virtual-network.md)

- [Zelf-hostende IR configureren als proxy voor uw Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md)