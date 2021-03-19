---
title: Integratie van bronbeheer
description: Database DevOps-ervaring van bedrijfsklasse voor toegewezen SQL-pools, met systeemeigen integratie van broncodebeheer met behulp van Azure-opslagplaatsen (Git en GitHub).
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 08/23/2019
ms.author: gaursa
ms.reviewer: igorstan
ms.openlocfilehash: 0864ab90f35e4af0443b418bc139ee5f8a7db665
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585533"
---
# <a name="source-control-integration-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Integratie van broncodebeheer voor toegewezen SQL-pools in Azure Synapse Analytics

In deze zelfstudie leert u hoe u uw SSDT-databaseproject (SQL Server Data Tools) integreert met broncodebeheer.  Integratie met broncodebeheer is de eerste stap voor het bouwen van een doorlopende integratie- en implementatiepijplijn met de toegewezen SQL-pool in Azure Synapse Analytics.

## <a name="before-you-begin"></a>Voordat u begint

- Meld u aan voor een [Azure DevOps-organisatie](https://azure.microsoft.com/services/devops/)
- Voltooi de zelfstudie [Maken en koppelen](create-data-warehouse-portal.md)
- [Installeer Visual Studio 2019](https://visualstudio.microsoft.com/vs/older-downloads/)

## <a name="set-up-and-connect-to-azure-devops"></a>Instellen van en verbinding maken met Azure DevOps

1. Maak in uw Azure DevOps-organisatie een project voor het hosten van uw SSDT-databaseproject via een Azure-opslagplaats.

   ![Project maken](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Project maken")

2. Open Visual Studio en selecteer **Verbindingen beheren** om verbinding te maken met uw Azure DevOps-organisatie en project uit stap één.

   ![Verbindingen beheren](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Verbindingen beheren")

3. Maak verbinding met uw project door **Verbindingen beheren** te selecteren en daarna **Verbinding maken met een project**.
 
    ![Verbinding maken1](./media/sql-data-warehouse-source-control-integration/3-connect-project.png "Verbinding maken")


4. Zoek het project dat u hebt gemaakt in stap één en selecteer **Verbinding maken**.
 
    ![Verbinding maken2](./media/sql-data-warehouse-source-control-integration/3.5-connect.png "Verbinding maken")


3. Uw Azure DevOps-opslagplaats klonen vanuit uw project naar uw lokale machine.

   ![Opslagplaats klonen](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Opslagplaats klonen")

Voor meer informatie over het verbinden van projecten met Visual Studio, gaat u naar [Verbinding maken met projecten in Team Explorer](/visualstudio/ide/connect-team-project?view=vs-2019&preserve-view=true). Raadpleeg voor hulp bij het klonen van een opslagplaats met behulp van Visual Studio het artikel [Een afsluitende Git-opslagplaats klonen](/azure/devops/repos/git/clone?tabs=visual-studio). 

## <a name="create-and-connect-your-project"></a>Uw project maken en ermee verbinding maken

1. Maak in Visual Studio een nieuw SQL Server-databaseproject met zowel een map als een lokale Git-opslag plaats in uw **lokaal gekloonde opslagplaats**.

   ![Nieuw project maken](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Nieuw project maken")  

2. Klik met de rechtermuisknop op uw lege sqlproject en importeer uw datawarehouse in het databaseproject.

   ![Project importeren](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Project importeren")  

3. Voer in Team Explorer in Visual Studio wijzigingen in uw lokale Git-opslagplaats door.

   ![Doorvoeren](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Doorvoeren")  

4. Nu u de wijzigingen lokaal hebt doorgevoerd in de gekloonde opslagplaats, synchroniseert u uw wijzigingen met en pusht u ze naar uw Azure-opslagplaats in uw Azure DevOps-project.

   ![Synchronisatie en push - fasering](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Synchronisatie en push - fasering")

   ![Synchronisatie en push](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Synchronisatie en push")  

## <a name="validation"></a>Validatie

1. Controleer of de wijzigingen zijn gepusht naar uw Azure -opslagplaats door een tabelkolom in uw databaseproject bij te werken vanuit Visual Studio SQL Server Data Tools (SSDT).

   ![Bijwerken kolom valideren](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Bijwerken kolom valideren")

2. Voer de wijziging door en push die van uw lokale opslagplaats naar uw Azure-opslagplaats.

   ![Wijzigingen pushen](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Wijzigingen pushen")

3. Controleer of de wijziging naar uw Azure-opslagplaats is gepusht.

   ![Verifiëren](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Wijzigingen controleren")

4. (**Optioneel**) Gebruik schemavergelijking en werk de wijzigingen in de toegewezen doel-SQL-pool bij met SSDT om ervoor te zorgen dat de objectdefinities in uw Azure-opslagplaats en lokale opslagplaats overeenkomen met uw toegewezen SQL-pool.

## <a name="next-steps"></a>Volgende stappen

- [Ontwikkelen voor toegewezen SQL-pools](sql-data-warehouse-overview-develop.md)