---
title: Azure-SSIS Integration runtime voor SQL Database failover configureren
description: In dit artikel wordt beschreven hoe u de Azure SSIS Integration runtime configureert met Azure SQL Database geo-replicatie en failover voor de SSISDB-data base
ms.service: data-factory
ms.devlang: powershell
author: swinarko
ms.author: sawinark
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/06/2020
ms.openlocfilehash: e12939d1003ce708889ca0b3dbc710096f9ee955
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364440"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-sql-database-geo-replication-and-failover"></a>De Azure SSIS Integration runtime configureren met SQL Database geo-replicatie en failover

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt beschreven hoe u de Azure SSIS Integration runtime (IR) configureert met Azure SQL Database geo-replicatie voor de SSISDB-data base. Wanneer een failover optreedt, kunt u ervoor zorgen dat de Azure-SSIS IR met de secundaire data base werkt.

Zie [overzicht: actieve geo-replicatie en groepen voor automatische failover](../azure-sql/database/auto-failover-group-overview.md)voor meer informatie over geo-replicatie en failover voor SQL database.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-a-sql-managed-instance"></a>Azure-SSIS IR failover met een door SQL beheerd exemplaar

### <a name="prerequisites"></a>Vereisten

Een door Azure SQL beheerd exemplaar maakt gebruik van een *database hoofd sleutel (DMK)* om gegevens, referenties en verbindings gegevens die zijn opgeslagen in een Data Base te beveiligen. Als u de automatische ontsleuteling van DMK wilt inschakelen, wordt een kopie van de sleutel versleuteld via de *Server hoofd sleutel (SMK)*. 

De SMK wordt niet gerepliceerd in een failover-groep. U moet een wacht woord toevoegen aan de primaire en secundaire instanties voor DMK-ontsleuteling na een failover.

1. Voer de volgende opdracht uit voor SSISDB op het primaire exemplaar. Met deze stap voegt u een nieuw versleutelings wachtwoord toe.

   ```sql
   ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
   ```

2. Een failover-groep maken op een door SQL beheerd exemplaar.

3. Voer **sp_control_dbmasterkey_password** uit op het secundaire exemplaar met behulp van het nieuwe versleutelings wachtwoord.

   ```sql
   EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB', @password = N'<password>', @action = N'add';  
   GO
   ```

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Scenario 1: Azure-SSIS IR verwijst naar een listener-eind punt voor lezen/schrijven

Als u wilt dat de Azure-SSIS IR naar een listener-eind punt voor lezen/schrijven wijst, moet u eerst naar het eerste eind punt van de primaire server verwijzen. Nadat u SSISDB in een failovergroep hebt geplaatst, kunt u uw Azure-SSIS IR stoppen, wijzigen om naar het listener-eind punt voor lezen/schrijven te verwijzen met Azure PowerShell en start u het opnieuw.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -CatalogServerEndpoint "Azure SQL Managed Instance read/write listener endpoint"
```

#### <a name="solution"></a>Oplossing

Als er een failover optreedt, voert u de volgende stappen uit:

1. Stop de Azure-SSIS IR in de primaire regio.

2. Bewerk de Azure-SSIS IR met een nieuwe regio, een virtueel netwerk en een SAS-URI (Shared Access Signature) voor aangepaste installatie op het secundaire exemplaar. Omdat de Azure-SSIS IR verwijst naar een listener voor lezen/schrijven en het eind punt transparant is voor de Azure-SSIS IR, hoeft u het eind punt niet te bewerken.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
      -VNetId "new VNet" `
      -Subnet "new subnet" `
      -SetupScriptContainerSasUri "new custom setup SAS URI"
   ```

3. Start de Azure-SSIS IR opnieuw op.

### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>Scenario 2: Azure-SSIS IR wijst naar een primair server eindpunt

Dit scenario is geschikt als de Azure-SSIS IR verwijst naar een primair server eindpunt.

#### <a name="solution"></a>Oplossing

Als er een failover optreedt, voert u de volgende stappen uit:

1. Stop de Azure-SSIS IR in de primaire regio.

2. Bewerk de Azure-SSIS IR met nieuwe informatie over de regio, het eind punt en het virtuele netwerk voor het secundaire exemplaar.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
      -CatalogServerEndpoint "Azure SQL Database endpoint" `
      -CatalogAdminCredential "Azure SQL Database admin credentials" `
      -VNetId "new VNet" `
      -Subnet "new subnet" `
      -SetupScriptContainerSasUri "new custom setup SAS URI"
   ```

3. Start de Azure-SSIS IR opnieuw op.

### <a name="scenario-3-azure-ssis-ir-is-pointing-to-a-public-endpoint-of-a-sql-managed-instance"></a>Scenario 3: Azure-SSIS IR wijst naar een openbaar eind punt van een SQL-beheerd exemplaar

Dit scenario is geschikt als de Azure-SSIS IR verwijst naar een openbaar eind punt van een door Azure SQL beheerd exemplaar en het niet wordt toegevoegd aan een virtueel netwerk. Het enige verschil van scenario 2 is dat u de gegevens van het virtuele netwerk voor de Azure-SSIS IR na een failover niet hoeft te bewerken.

#### <a name="solution"></a>Oplossing

Als er een failover optreedt, voert u de volgende stappen uit:

1. Stop de Azure-SSIS IR in de primaire regio.

2. Bewerk de Azure-SSIS IR met de nieuwe regio-en eindpunt gegevens voor het secundaire exemplaar.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
      -CatalogServerEndpoint "Azure SQL Database server endpoint" `
      -CatalogAdminCredential "Azure SQL Database server admin credentials" `
      -SetupScriptContainerSasUri "new custom setup SAS URI"
   ```

3. Start de Azure-SSIS IR opnieuw op.

### <a name="scenario-4-attach-an-existing-ssisdb-instance-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenario 4: een bestaande SSISDB-instantie (SSIS-catalogus) koppelen aan een nieuwe Azure-SSIS IR

Dit scenario is geschikt als u wilt dat SSISDB met een nieuwe Azure-SSIS IR in een nieuwe regio werkt wanneer een Azure Data Factory of Azure-SSIS IR nood geval in de huidige regio optreedt.

#### <a name="solution"></a>Oplossing

Als er een failover optreedt, voert u de volgende stappen uit.

> [!NOTE]
> Gebruik Power shell voor stap 4 (maken van de IR). Als u dit niet doet, rapporteert de Azure Portal een fout melding waarin wordt aangegeven dat SSISDB al bestaat.

1. Stop de Azure-SSIS IR in de primaire regio.

2. Voer een opgeslagen procedure uit om meta gegevens in SSISDB bij te werken om verbindingen van en te accepteren **\<new_data_factory_name\>** **\<new_integration_runtime_name\>** .
   
   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

3. Maak een nieuwe data factory die wordt genoemd **\<new_data_factory_name\>** in de nieuwe regio.

   ```powershell
   Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
      -Location "new region"`
      -Name "<new_data_factory_name>"
   ```
   
   Zie [een Azure-Data Factory maken met Power shell](quickstart-create-data-factory-powershell.md)voor meer informatie over deze Power shell-opdracht.

4. Maak een nieuwe Azure-SSIS IR met **\<new_integration_runtime_name\>** de naam in de nieuwe regio met behulp van Azure PowerShell.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
      -DataFactoryName "new data factory name" `
      -Name "<new_integration_runtime_name>" `
      -Description $AzureSSISDescription `
      -Type Managed `
      -Location $AzureSSISLocation `
      -NodeSize $AzureSSISNodeSize `
      -NodeCount $AzureSSISNodeNumber `
      -Edition $AzureSSISEdition `
      -LicenseType $AzureSSISLicenseType `
      -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
      -VnetId "new vnet" `
      -Subnet "new subnet" `
      -CatalogServerEndpoint $SSISDBServerEndpoint `
      -CatalogPricingTier $SSISDBPricingTier
   ```
   
   Zie [de Azure-SSIS Integration runtime in azure Data Factory maken](create-azure-ssis-integration-runtime.md)voor meer informatie over deze Power shell-opdracht.

## <a name="azure-ssis-ir-failover-with-sql-database"></a>Azure-SSIS IR failover met SQL Database

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Scenario 1: Azure-SSIS IR verwijst naar een listener-eind punt voor lezen/schrijven

Dit scenario is geschikt wanneer:

- De Azure-SSIS IR verwijst naar het Lees-of schrijf-listener-eind punt van de failovergroep.
- De SQL Database-Server is *niet* geconfigureerd met de regel voor het service-eind punt van het virtuele netwerk.

Als u wilt dat de Azure-SSIS IR naar een listener-eind punt voor lezen/schrijven wijst, moet u eerst naar het eerste eind punt van de primaire server verwijzen. Nadat u SSISDB in een failovergroep hebt geplaatst, kunt u uw Azure-SSIS IR stoppen, wijzigen om naar het listener-eind punt voor lezen/schrijven te verwijzen met Azure PowerShell en start u het opnieuw.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -CatalogServerEndpoint "Azure SQL Database read/write listener endpoint"
```

#### <a name="solution"></a>Oplossing

Wanneer een failover optreedt, is dit transparant voor de Azure-SSIS IR. De Azure-SSIS IR maakt automatisch verbinding met de nieuwe primaire groep van de failovergroep. 

Als u de regio of andere informatie in de Azure-SSIS IR wilt bijwerken, kunt u deze stoppen, bewerken en opnieuw starten.


### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>Scenario 2: Azure-SSIS IR wijst naar een primair server eindpunt

Dit scenario is geschikt als de Azure-SSIS IR verwijst naar een primair server eindpunt.

#### <a name="solution"></a>Oplossing

Als er een failover optreedt, voert u de volgende stappen uit:

1. Stop de Azure-SSIS IR in de primaire regio.

2. Bewerk de Azure-SSIS IR met nieuwe informatie over de regio, het eind punt en het virtuele netwerk voor het secundaire exemplaar.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
      -CatalogServerEndpoint "Azure SQL Database endpoint" `
      -CatalogAdminCredential "Azure SQL Database admin credentials" `
      -VNetId "new VNet" `
      -Subnet "new subnet" `
      -SetupScriptContainerSasUri "new custom setup SAS URI"
   ```

3. Start de Azure-SSIS IR opnieuw op.

### <a name="scenario-3-attach-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenario 3: een bestaande SSISDB (SSIS-catalogus) koppelen aan een nieuwe Azure-SSIS IR

Dit scenario is geschikt als u een nieuwe Azure-SSIS IR wilt inrichten in een secundaire regio. Het is ook geschikt als u wilt dat uw SSISDB met een nieuwe Azure-SSIS IR in een nieuwe regio werkt wanneer een Azure Data Factory of Azure-SSIS IR nood geval in de huidige regio optreedt.

#### <a name="solution"></a>Oplossing

Als er een failover optreedt, voert u de volgende stappen uit.

> [!NOTE]
> Gebruik Power shell voor stap 4 (maken van de IR). Als u dit niet doet, rapporteert de Azure Portal een fout melding waarin wordt aangegeven dat SSISDB al bestaat.

1. Stop de Azure-SSIS IR in de primaire regio.

2. Voer een opgeslagen procedure uit om meta gegevens in SSISDB bij te werken om verbindingen van en te accepteren **\<new_data_factory_name\>** **\<new_integration_runtime_name\>** .
   
   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

3. Maak een nieuwe data factory die wordt genoemd **\<new_data_factory_name\>** in de nieuwe regio.

   ```powershell
   Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
      -Location "new region"`
      -Name "<new_data_factory_name>"
   ```
   
   Zie [een Azure-Data Factory maken met Power shell](quickstart-create-data-factory-powershell.md)voor meer informatie over deze Power shell-opdracht.

4. Maak een nieuwe Azure-SSIS IR met **\<new_integration_runtime_name\>** de naam in de nieuwe regio met behulp van Azure PowerShell.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
      -DataFactoryName "new data factory name" `
      -Name "<new_integration_runtime_name>" `
      -Description $AzureSSISDescription `
      -Type Managed `
      -Location $AzureSSISLocation `
      -NodeSize $AzureSSISNodeSize `
      -NodeCount $AzureSSISNodeNumber `
      -Edition $AzureSSISEdition `
      -LicenseType $AzureSSISLicenseType `
      -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
      -VnetId "new vnet" `
      -Subnet "new subnet" `
      -CatalogServerEndpoint $SSISDBServerEndpoint `
      -CatalogPricingTier $SSISDBPricingTier
   ```

   Zie [de Azure-SSIS Integration runtime in azure Data Factory maken](create-azure-ssis-integration-runtime.md)voor meer informatie over deze Power shell-opdracht.

## <a name="next-steps"></a>Volgende stappen

Houd rekening met de volgende andere configuratie opties voor de Azure-SSIS IR:

- [De Azure SSIS Integration runtime configureren voor hoge prestaties](configure-azure-ssis-integration-runtime-performance.md)

- [Instelling voor Azure-SSIS Integration Runtime aanpassen](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Enter prise Edition voor Azure-SSIS Integration runtime inrichten](how-to-configure-azure-ssis-ir-enterprise-edition.md)
