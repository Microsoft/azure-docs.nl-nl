---
title: De Enter prise-editie voor de Azure-SSIS Integration Runtime inrichten
description: In dit artikel worden de functies van de Enter prise-editie voor de Azure-SSIS Integration Runtime beschreven en wordt uitgelegd hoe u deze kunt inrichten
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/09/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: f700729c2d059648b1c3a7e451526aefcb436818
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387543"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>De Enter prise-editie voor de Azure-SSIS Integration Runtime inrichten

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Met de Enter prise-editie van de Azure-SSIS Integration Runtime kunt u de volgende geavanceerde en Premium-functies gebruiken:
-   CDC-onderdelen (Change Data Capture)
-   Oracle-, Teradata-en SAP BW-connectors
-   SQL Server Analysis Services-(SSAS) en Azure Analysis Services (AAS) connectors en trans formaties
-   Fuzzy groepering en fuzzy lookup-trans formaties
-   Voor waarden voor het ophalen en opzoeken van termen

Voor sommige van deze functies moet u extra onderdelen installeren om de Azure-SSIS IR aan te passen. Zie [aangepaste installatie voor de Azure-SSIS Integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)voor meer informatie over het installeren van extra onderdelen.

## <a name="enterprise-features"></a>Enter prise-functies

| **Enter prise-functies** | **Beschreven** |
|---|---|
| CDC-onderdelen | De CDC-bron, beheer taak en splitter-trans formatie zijn vooraf geïnstalleerd op de Azure-SSIS IR Enter prise-editie. Als u verbinding wilt maken met Oracle, moet u ook de CDC Designer en service op een andere computer installeren. |
| Oracle-connectors | De Oracle-verbindings beheer,-bron en-bestemming zijn vooraf geïnstalleerd op de Azure-SSIS IR Enter prise-editie. U moet ook het OCI-stuur programma (Oracle Call Interface) installeren en zo nodig het Oracle-transport netwerk (TNS) configureren op de Azure-SSIS IR. Zie [Aangepaste instelling voor de Azure-SSIS-integratieruntime](how-to-configure-azure-ssis-ir-custom-setup.md) voor meer informatie. |
| Teradata-connectors | U moet de Teradata-verbindings beheer, de bron en de bestemming installeren, evenals de TPT-API (parallel trans Porter) en het Teradata-ODBC-stuur programma in de Azure-SSIS IR Enter prise-editie. Zie [Aangepaste instelling voor de Azure-SSIS-integratieruntime](how-to-configure-azure-ssis-ir-custom-setup.md) voor meer informatie. |
| SAP BW-connectors | De SAP BW verbindings beheer, bron en bestemming zijn vooraf geïnstalleerd op de Azure-SSIS IR Enter prise-editie. U moet ook het SAP BW-stuur programma installeren op de Azure-SSIS IR. Deze connectors ondersteunen SAP BW 7,0 of eerdere versies. Als u verbinding wilt maken met latere versies van SAP BW of andere SAP-producten, kunt u SAP-connectors aanschaffen en installeren vanuit Isv's van derden op de Azure-SSIS IR. Zie [aangepaste installatie voor de Azure-SSIS Integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)voor meer informatie over het installeren van extra onderdelen. |
| Analysis Services onderdelen               | De trainings doel locatie voor gegevens analyse modellen, de doel dimensie verwerking en de bestemming van de partitie verwerking, evenals de gegevens analyse query transformatie, worden vooraf geïnstalleerd op de Azure-SSIS IR Enter prise-editie. Al deze onderdelen ondersteunen SQL Server Analysis Services (SSAS), maar alleen de partitie verwerkings bestemming ondersteunt Azure Analysis Services (AAS). Als u verbinding wilt maken met SSAS, moet u ook [Windows-verificatie referenties configureren in SSISDB](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Naast deze onderdelen worden de Analysis Services uitvoeren van DDL-taak, de verwerkings taak Analysis Services en de taak gegevens analyse query ook vooraf geïnstalleerd op de Azure-SSIS IR Standard/Enter prise-editie. |
| Fuzzy groepering en fuzzy lookup-trans formaties  | De onduidelijke groepering en fuzzy lookup-trans formaties worden vooraf geïnstalleerd op de Azure-SSIS IR Enter prise-editie. Deze onderdelen ondersteunen zowel SQL Server als Azure SQL Database om referentie gegevens op te slaan. |
| Voor waarden voor het ophalen en opzoeken van termen | De termen voor het oppakken en het opzoeken van termen worden vooraf geïnstalleerd op de Azure-SSIS IR Enter prise-editie. Deze onderdelen ondersteunen zowel SQL Server als Azure SQL Database om referentie gegevens op te slaan. |

## <a name="instructions"></a>Instructies

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  Down load en Installeer [Azure PowerShell](/powershell/azure/install-az-ps).

2.  Wanneer u de Azure-SSIS IR inricht of opnieuw configureert met Power shell, moet u `Set-AzDataFactoryV2IntegrationRuntime` met **Enter prise** worden uitgevoerd als de waarde voor de **editie** -para meter voordat u de Azure-SSIS IR start. Hier volgt een voorbeeld script:

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>Volgende stappen

-   [Aangepaste installatie voor Azure-SSIS Integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Betaalde of gelicentieerde aangepaste onderdelen ontwikkelen voor Azure-SSIS Integration runtime](how-to-develop-azure-ssis-ir-licensed-components.md)