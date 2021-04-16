---
title: Continue integratie en implementatie voor toegewezen SQL-pool
description: Database DevOps-ervaring van bedrijfsklasse voor toegewezen SQL-pool in Azure Synapse Analytics met ingebouwde ondersteuning voor continue integratie en implementatie met behulp van Azure Pipelines.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 95bf3a8c614b8b7c0269257cb62b3c3a0f60be13
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568281"
---
# <a name="continuous-integration-and-deployment-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Continue integratie en implementatie voor toegewezen SQL-pool in Azure Synapse Analytics

In deze eenvoudige zelfstudie wordt beschreven hoe u uw SSDT-databaseproject (SQL Server Data Tools) integreert met Azure DevOps en hoe u Azure Pipelines gebruikt om continue integratie en implementatie in te stellen. Deze zelfstudie is de tweede stap bij het bouwen van uw pijplijn voor continue integratie en implementatie voor datawarehousing.

## <a name="before-you-begin"></a>Voordat u begint

- Door de zelfstudie [voor integratie van broncodebeheer](sql-data-warehouse-source-control-integration.md)

- Instellen van en verbinding maken met Azure DevOps

## <a name="continuous-integration-with-visual-studio-build"></a>Continue integratie met Visual Studio build

1. Navigeer naar Azure Pipelines en maak een nieuwe build-pipeline.

      ![Nieuwe pijplijn](./media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Nieuwe pijplijn")

2. Selecteer uw opslagplaats voor broncode (Azure Repos Git) en selecteer de .NET Desktop-app-sjabloon.

      ![Pijplijn instellen](./media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Pijplijn instellen")

3. Bewerk uw YAML-bestand om de juiste pool van uw agent te gebruiken. Uw YAML-bestand moet er als het volgende uitzien:

      ![YAML](./media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

Op dit punt hebt u een eenvoudige omgeving waarin het inchecken bij uw opslagplaats voor broncodebeheer main branch automatisch een geslaagde Visual Studio van uw databaseproject moet activeren. Controleer of de automatisering end-to-end werkt door een wijziging aan te brengen in uw lokale databaseproject en deze wijziging in te checken in uw main branch.

## <a name="continuous-deployment-with-the-azure-synapse-analytics-or-database-deployment-task"></a>Continue implementatie met de Azure Synapse Analytics (of database)-implementatietaak

1. Voeg een nieuwe taak toe met behulp [Azure SQL Database implementatietaak](/azure/devops/pipelines/targets/azure-sqldb) en vul de vereiste velden in om verbinding te maken met uw doeldatawarehouse. Wanneer deze taak wordt uitgevoerd, wordt de DACPAC die is gegenereerd op basis van het vorige buildproces geïmplementeerd in het doeldatawarehouse. U kunt ook de [implementatietaak Azure Synapse Analytics gebruiken.](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment)

      ![Implementatietaak](./media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Implementatietaak")

2. Als u een zelf-hostende agent gebruikt, moet u uw omgevingsvariabele instellen op het gebruik van de juiste SqlPackage.exe voor Azure Synapse Analytics. Het pad ziet er als het volgende uit:

      ![Omgevingsvariabele](./media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Omgevingsvariabele")

   C:\Program Files (x86)\Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Voer uw pijplijn uit en valideer deze. U kunt lokaal wijzigingen aanbrengen en wijzigingen in uw broncodebeheer controleren die een automatische build en implementatie moeten genereren.

## <a name="next-steps"></a>Volgende stappen

- Architectuur [van toegewezen SQL-pool (voorheen SQL DW) verkennen](massively-parallel-processing-mpp-architecture.md)
- Snel [een toegewezen SQL-pool maken (voorheen SQL DW)](create-data-warehouse-portal.md)
- [Voorbeeldgegevens laden](./load-data-from-azure-blob-storage-using-copy.md)
- [Video's verkennen](sql-data-warehouse-videos.md)