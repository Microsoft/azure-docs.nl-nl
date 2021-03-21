---
title: De Azure-SSIS-integratieruntime opnieuw configureren
description: Meer informatie over het opnieuw configureren van een Azure SSIS Integration runtime in Azure Data Factory nadat u deze al hebt ingericht.
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 724411dc12654aec1614230c943923062b334cd2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100370679"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>De Azure-SSIS-integratieruntime opnieuw configureren

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt beschreven hoe u een bestaande Azure SSIS Integration runtime opnieuw kunt configureren. Zie [een Azure SSIS Integration runtime maken](create-azure-ssis-integration-runtime.md)voor het maken van een Azure-SSIS Integration runtime (IR) in azure Data Factory.  

## <a name="data-factory-ui"></a>Gebruikersinterface van Data Factory 
U kunt Data Factory gebruikers interface gebruiken om een Azure-SSIS IR te stoppen, te bewerken/opnieuw te configureren of te verwijderen. 

1. Open Data Factory gebruikers interface door de tegel **Author & monitor** te selecteren op de start pagina van uw Data Factory.
2. Selecteer de hub **beheren** onder **Start**, **bewerken** en **controleren** hubs om het deel venster **verbindingen** weer te geven.

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Een Azure-SSIS IR opnieuw configureren
Ga in het deelvenster **Verbindingen** van de hub **Beheren** naar de pagina **Integratieruntimes** en selecteer **Vernieuwen**. 

   ![Deelvenster Verbindingen](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   U kunt uw Azure-SSIS IR bewerken/opnieuw configureren door de naam ervan te selecteren. U kunt ook de relevante knoppen voor het bewaken/starten/stoppen/verwijderen van uw Azure-SSIS IR selecteren, automatisch een ADF-pijplijn genereren met de activiteit voor het uitvoeren van SSIS-pakketten om deze op uw Azure-SSIS IR uit te voeren en de JSON-code/payload van uw Azure-SSIS IR weergeven.  U kunt uw Azure-SSIS IR alleen bewerken/verwijderen wanneer deze is gestopt.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nadat u een exemplaar van Azure SSIS Integration runtime hebt ingericht en gestart, kunt u het opnieuw configureren door een reeks `Stop`  -  `Set`  -  `Start` Power shell-cmdlets achter elkaar uit te voeren. Het volgende Power shell-script wijzigt bijvoorbeeld het aantal knoop punten dat voor het Azure-SSIS Integration runtime-exemplaar is toegewezen aan vijf.

### <a name="reconfigure-an-azure-ssis-ir"></a>Een Azure-SSIS IR opnieuw configureren

1. Stop eerst de Azure SSIS Integration runtime met behulp van de cmdlet [Stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) . Met deze opdracht worden alle knoop punten vrijgegeven en wordt de facturering gestopt.

   ```powershell
   Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
   ```
2. Configureer vervolgens de Azure-SSIS IR opnieuw met behulp van de cmdlet [set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) . Met de volgende voorbeeld opdracht wordt een Azure-SSIS-integratie-runtime geschaald naar vijf knoop punten.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
   ```  
3. Start vervolgens de Azure SSIS Integration runtime met behulp van de cmdlet [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) . Met deze opdracht worden alle knoop punten toegewezen voor het uitvoeren van SSIS-pakketten.   

   ```powershell
   Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
   ```

### <a name="delete-an-azure-ssis-ir"></a>Een Azure-SSIS IR verwijderen
1. Eerst een lijst van alle bestaande Azure SSIS-IRs onder uw data factory.

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
   ```
2. Stop vervolgens alle bestaande Azure SSIS IRs in uw data factory.

   ```powershell
   Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
   ```
3. Verwijder vervolgens alle bestaande Azure SSIS-IRs in uw data factory een voor een.

   ```powershell
   Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
   ```
4. Verwijder ten slotte de data factory.

   ```powershell
   Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
   ```
5. Als u een nieuwe resource groep hebt gemaakt, verwijdert u de resource groep.

   ```powershell
   Remove-AzResourceGroup -Name $ResourceGroupName -Force 
   ```

## <a name="next-steps"></a>Volgende stappen
Zie de volgende onderwerpen voor meer informatie over Azure-SSIS runtime: 

- [Azure-SSIS Integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Dit artikel bevat conceptuele informatie over integratie-Runtimes in het algemeen, met inbegrip van de Azure-SSIS IR. 
- [Zelfstudie: SSIS-pakketten implementeren in Azure](./tutorial-deploy-ssis-packages-azure.md). In dit artikel vindt u stapsgewijze instructies voor het maken van een Azure-SSIS IR en het gebruik van Azure SQL Database voor het hosten van de SSIS-catalogus. 
- [Procedure: Een Azure SSIS Integration Runtime maken](create-azure-ssis-integration-runtime.md). In dit artikel wordt de zelf studie uitgebreid en vindt u instructies voor het gebruik van Azure SQL Managed instance en het toevoegen van de IR aan een virtueel netwerk. 
- [Een Azure-SSIS-integratieruntime toevoegen aan een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md). Dit artikel bevat algemene informatie over het toevoegen van een Azure-SSIS IR aan een virtueel netwerk van Azure. Er wordt ook beschreven hoe u Azure Portal gebruikt om een virtueel netwerk te configureren voor het deelnemen van Azure-SSIS IR aan het virtueel netwerk. 
- [Een Azure-SSIS IR controleren](monitor-integration-runtime.md#azure-ssis-integration-runtime). In dit artikel leest u hoe u informatie over een Azure-SSIS IR ophaalt. Daarnaast bevat het artikel beschrijvingen van statuswaarden die worden gebruikt in de geretourneerde informatie.