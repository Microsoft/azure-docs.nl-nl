---
title: On-premises SQL Server Integration Services (SSIS)-workloads migreren naar SSIS in Azure Data Factory (ADF)
description: Migratie van on-premises SSIS-workloads naar SSIS in ADF.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 78c488302a874319f79a143e4657d161fe849855
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373603"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>On-premises SSIS-workloads migreren naar SSIS in ADF

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Overzicht

Wanneer u de werk belasting van uw data base migreert van SQL Server on-premises naar Azure Data Base-Services, dat wil zeggen Azure SQL Database of Azure SQL Managed instance, moeten uw ETL-workloads op SQL Server Integration Services (SSIS) als een van de services met primaire toegevoegde waarde ook worden gemigreerd.

Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF) ondersteunt actieve SSIS-pakketten. Als Azure-SSIS IR is ingericht, kunt u vervolgens vertrouwde hulpprogram ma's gebruiken, zoals SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) en opdracht regel Programma's, zoals dtinstall/dtutil/dtexec, om uw pakketten te implementeren en uit te voeren in Azure. Zie [Azure SSIS Lift-and-Shift Overview (](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)Engelstalig) voor meer informatie.

In dit artikel wordt het migratie proces van de ETL-workloads van on-premises SSIS naar SSIS in ADF uitgelegd. Het migratie proces bestaat uit twee fasen: **evaluatie** en **migratie**.

## <a name="assessment"></a>Beoordeling

Een grondige evaluatie helpt bij het vaststellen van een volledig migratie plan voor het identificeren van problemen met de bron-SSIS-pakketten die een geslaagde migratie belemmeren.

Data Migration Assistant (DMA) is een vrij downloadbaar hulp programma voor dit doel dat lokaal kan worden geïnstalleerd en uitgevoerd. Een DMA-beoordelings project van het type **integratie Services** kan worden gemaakt om SSIS-pakketten in batches te beoordelen en compatibiliteits problemen te identificeren die worden weer gegeven in de volgende categorieën:

- Migratie blok keringen: compatibiliteits problemen waarbij de migratie bron pakketten worden geblokkeerd om te worden uitgevoerd op Azure-SSIS IR. DMA biedt hulp bij het oplossen van deze problemen.

- Informatieve problemen: gedeeltelijk ondersteunde of afgeschafte functies die worden gebruikt in bron pakketten. DMA biedt een uitgebreide set aanbevelingen, alternatieve benaderingen die beschikbaar zijn in Azure en het beperken van de stappen voor het oplossen van problemen.

### <a name="four-storage-types-for-ssis-packages"></a>Vier opslag typen voor SSIS-pakketten

- SSIS-catalogus (SSISDB). Geïntroduceerd met SQL Server 2012 en bevat een set opgeslagen procedures, weer gaven en tabelwaardefunctie die worden gebruikt voor het werken met SSIS-projecten/-pakketten.
- Bestands systeem.
- SQL Server systeem database (MSDB).
- SSIS-pakket opslag. Een pakket beheer-laag boven op twee subtypen:
  - MSDB, een systeem database in SQL Server gebruikt om SSIS-pakketten op te slaan.
  - Beheerd bestands systeem, een specifieke map in SQL Server installatiepad dat wordt gebruikt voor het opslaan van SSIS-pakketten.

DMA ondersteunt momenteel de batch-evaluatie van pakketten die zijn opgeslagen in **Bestands systeem**, **pakket opslag** en **SSIS-catalogus** sinds **DMA versie v 5.0**.

U kunt [DMA](/sql/dma/dma-overview)ophalen en [uw pakket evaluatie met IT uitvoeren](/sql/dma/dma-assess-ssis).

## <a name="migration"></a>Migratie

Afhankelijk van de [opslag typen](#four-storage-types-for-ssis-packages) van de bron-SSIS-pakketten en het migratie doel van data base-workloads, zijn de stappen voor het migreren van  **SSIS-pakketten** en **SQL Server Agent taken** die de uitvoering van SSIS-pakket plannen kunnen variëren. Er zijn twee scenario's:

- [**Azure SQL Managed instance** as data base-werk belasting doel](#azure-sql-managed-instance-as-database-workload-destination)
- [**Azure SQL database** als doel van de database werk belasting](#azure-sql-database-as-database-workload-destination)

Het is ook een praktische manier om [SSIS DevOps-Hulpprogram ma's](/sql/integration-services/devops/ssis-devops-overview)te gebruiken om de herimplementatie van batch pakketten naar het migratie doel uit te voeren.  

### <a name="azure-sql-managed-instance-as-database-workload-destination"></a>**Azure SQL Managed instance** as data base-werk belasting doel

| **Type pakket opslag** |Hoe batch migratie SSIS-pakketten|Hoe kan ik SSIS-taken batchgewijs migreren|
|-|-|-|
|SSISDB|[**SSISDB** migreren](scenario-ssis-migration-ssisdb-mi.md)|<li>[SSIS-taken migreren naar Azure SQL Managed instance agent](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li>Zet ze om in ADF-pijp lijnen/activiteiten/triggers via scripts/SSMS/ADF Portal. Zie [SSMS planning feature](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)(Engelstalig) voor meer informatie.|
|Bestandssysteem|Implementeer deze opnieuw naar bestands shares/Azure Files via dtinstall/dtutil/hand matig kopiëren of bewaar de bestands systemen via VNet/zelf-Hostende IR. Zie [dtutil Utility](/sql/integration-services/dtutil-utility)(Engelstalig) voor meer informatie.|<li>[SSIS-taken migreren naar Azure SQL Managed instance agent](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> Migreren met de [wizard SSIS-taak migratie in SSMS](how-to-migrate-ssis-job-ssms.md) <li>Zet ze om in ADF-pijp lijnen/activiteiten/triggers via scripts/SSMS/ADF Portal. Zie [SSMS planning feature](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)(Engelstalig) voor meer informatie.|
|SQL Server (MSDB)|Exporteren naar bestands systemen/bestands shares/Azure Files via SSMS/dtutil. Zie [SSIS-pakketten exporteren](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages)voor meer informatie.|Zet ze om in ADF-pijp lijnen/activiteiten/triggers via scripts/SSMS/ADF Portal. Zie [SSMS planning feature](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)(Engelstalig) voor meer informatie.|
|Pakket archief|Exporteer deze naar de pakket opslag via SSMS/dtutil of implementeer ze opnieuw in pakket opslag via dtinstall/dtutil/Manual copy. Zie [Pakketten beheren met Azure-SSIS Integration runtime-pakket opslag](azure-ssis-integration-runtime-package-store.md)voor meer informatie.|<li>[SSIS-taken migreren naar Azure SQL Managed instance agent](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> Zet ze om in ADF-pijp lijnen/activiteiten/triggers via scripts/SSMS/ADF Portal. Zie [SSMS planning feature](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)(Engelstalig) voor meer informatie.|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Azure SQL database** als doel van de database werk belasting

| **Type pakket opslag** |Hoe batch migratie SSIS-pakketten|Taken batchgewijs migreren|
|-|-|-|
|SSISDB|Implementeer opnieuw naar Azure-SSISDB via SSDT/SSMS. Zie [SSIS-pakketten implementeren in azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)voor meer informatie.|Zet ze om in ADF-pijp lijnen/activiteiten/triggers via scripts/SSMS/ADF Portal. Zie [SSMS planning feature](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)(Engelstalig) voor meer informatie.|
|Bestandssysteem|Implementeer deze opnieuw naar bestands shares/Azure Files via dtinstall/dtutil/hand matig kopiëren of bewaar de bestands systemen via VNet/zelf-Hostende IR. Zie [dtutil Utility](/sql/integration-services/dtutil-utility)(Engelstalig) voor meer informatie.|<li> Migreren met de [wizard SSIS-taak migratie in SSMS](how-to-migrate-ssis-job-ssms.md) <li> Zet ze om in ADF-pijp lijnen/activiteiten/triggers via scripts/SSMS/ADF Portal. Zie [SSMS planning feature](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)(Engelstalig) voor meer informatie.|
|SQL Server (MSDB)|Exporteren naar bestands systemen/bestands shares/Azure Files via SSMS/dtutil. Zie [SSIS-pakketten exporteren](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages)voor meer informatie.|Zet ze om in ADF-pijp lijnen/activiteiten/triggers via scripts/SSMS/ADF Portal. Zie [SSMS planning feature](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)(Engelstalig) voor meer informatie.|
|Pakket archief|Exporteer deze naar bestands systemen/bestands shares/Azure Files via SSMS/dtutil of implementeer ze opnieuw op bestands shares/Azure Files via dtinstall/dtutil/hand matig kopiëren of bewaar ze in bestands systemen voor toegang via een VNet/zelf-Hostende IR. Zie dtutil Utility (Engelstalig) voor meer informatie. Zie [dtutil Utility](/sql/integration-services/dtutil-utility)(Engelstalig) voor meer informatie.|Zet ze om in ADF-pijp lijnen/activiteiten/triggers via scripts/SSMS/ADF Portal. Zie [SSMS planning feature](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)(Engelstalig) voor meer informatie.|

## <a name="additional-resources"></a>Aanvullende bronnen

- [Azure Data Factory](./introduction.md)
- [Database Migration Assistant](/sql/dma/dma-overview)
- [SSIS-workloads in de Cloud optillen en verplaatsen](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)
- [SSIS DevOps Tools](/sql/integration-services/devops/ssis-devops-overview)
- [SSIS-pakketten naar Azure SQL Managed Instance migreren](../dms/how-to-migrate-ssis-packages-managed-instance.md)
- [Pakketten opnieuw implementeren naar Azure SQL Database](../dms/how-to-migrate-ssis-packages.md)

- [Toegang tot on-premises gegevens vanaf Azure-SSIS Integration Runtime](https://techcommunity.microsoft.com/t5/sql-server-integration-services/vnet-or-no-vnet-secure-data-access-from-ssis-in-azure-data/ba-p/1062056)
- [De instellingen voor een Azure-SSIS Integration Runtime aanpassen](how-to-configure-azure-ssis-ir-custom-setup.md)
- [Toegang tot gegevensarchieven en bestandsshares met Windows-verificatie van SSIS-pakketten in Azure](ssis-azure-connect-with-windows-auth.md)
- [Beheerde identiteits verificatie gebruiken](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
- [Azure Key Vault gebruiken](store-credentials-in-key-vault.md)
- [De Azure-SSIS Integration Runtime configureren voor hoge prestaties](configure-azure-ssis-integration-runtime-performance.md)
- [Azure-SSIS Integration Runtime gepland starten en stoppen](how-to-schedule-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>Volgende stappen

- [SSIS-pakketten valideren die zijn geïmplementeerd in azure](/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [SSIS-pakketten uitvoeren die zijn geïmplementeerd in azure](/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Azure-SSIS Integration Runtime bewaken](./monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Uitvoeringen van SSIS-pakketten plannen in azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)