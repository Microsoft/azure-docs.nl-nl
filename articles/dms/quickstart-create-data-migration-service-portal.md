---
title: 'Quickstart: Een exemplaar maken met behulp van de Azure-portal'
titleSuffix: Azure Database Migration Service
description: Gebruik de Azure-portal om een Azure Database Migration Service-exemplaar te maken.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 01/29/2021
ms.openlocfilehash: 8a500104a0273b9e131815c4dc832bd33729cd51
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566588"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Quickstart: Een Azure Database Migration Service-exemplaar maken met behulp van Azure Portal

In deze Quick Start gebruikt u de Azure Portal om een exemplaar van Azure Database Migration Service te maken. Nadat u het exemplaar hebt gemaakt, kunt u het gebruiken om gegevens te migreren van meerdere database bronnen naar Azure-gegevens platformen, zoals van SQL Server naar Azure SQL Database of van SQL Server naar een beheerd exemplaar van Azure SQL.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Open de webbrowser, navigeer naar [Microsoft Azure Portal](https://portal.azure.com/), en voer vervolgens uw referenties in om u aan te melden bij de portal. De standaardweergave is uw service-dashboard.

> [!NOTE]
> U kunt maximaal tien DMS-exemplaren per abonnement per regio maken. Als u meer exemplaren nodig hebt, maakt u een ondersteuningsticket.

## <a name="register-the-resource-provider"></a>De resourceprovider registreren

Registreer de Microsoft.DataMigration-resourceprovider voordat u uw eerste Database Migration Service-exemplaar maakt.

1. Zoek en selecteer **Abonnementen** in Azure Portal.

   ![Portal-abonnementen weergeven](media/quickstart-create-data-migration-service-portal/portal-select-subscription.png)

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/quickstart-create-data-migration-service-portal/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer **Registreren** voor **Microsoft.DataMigration**.

    ![Resourceprovider registreren](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Een exemplaar van de service maken

1. Selecteer in het menu van de Azure-portal of op de **startpagina** de optie **Een resource maken**. Zoek en selecteer **Azure Database Migration Service**.

    ![Azure Marketplace](media/quickstart-create-data-migration-service-portal/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/quickstart-create-data-migration-service-portal/dms-create.png)

3. Ga in het basisscherm **Migratieservice maken** als volgt te werk:

     - Selecteer het abonnement.
     - Maak een nieuwe resourcegroep of kies een bestaande resourcegroep.
     - Geef een naam op voor het exemplaar van de Azure Database Migration Service.
     - Selecteer de locatie waarop u het exemplaar van Azure Database Migration Service wilt maken.
     - Kies **Azure** als de servicemodus.
     - Selecteer een prijscategorie. Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).
     
    ![Basisinstellingen configureren van een Azure Database Migration Service-exemplaar](media/quickstart-create-data-migration-service-portal/dms-create-basics.png)

     - Selecteer Volgende: Netwerken.

4. Ga in het netwerkscherm **Migratieservice maken** als volgt te werk:

    - Selecteer een bestaand virtueel netwerk of maak een nieuw netwerk. Het virtuele netwerk biedt Azure Database Migration Service toegang tot de brondatabase en doelomgeving. Zie het artikel [Een virtueel netwerk maken met de Azure-portal](../virtual-network/quick-create-portal.md) voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

    ![Netwerkinstellingen configureren van een Azure Database Migration Service-exemplaar](media/quickstart-create-data-migration-service-portal/dms-network-settings.png)

    - Selecteer **Beoordelen en maken** om de service te maken. 
    
    - Na enkele ogen blikken is uw exemplaar van Azure data base Migration service gemaakt en klaar voor gebruik:

    ![Migratieservice gemaakt](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resources die u in deze Quick Start hebt gemaakt, opschonen door de [Azure-resource groep](../azure-resource-manager/management/overview.md)te verwijderen. U verwijdert de resourcegroep door te navigeren naar het Azure Database Migration Service-exemplaar dat u hebt gemaakt. Selecteer bij **Resourcegroep** de naam van de resourcegroep en selecteer vervolgens **Resourcegroep verwijderen**. Door deze actie worden alle items in de resourcegroep verwijderd, evenals de groep zelf.

## <a name="next-steps"></a>Volgende stappen

* [SQL Server offline migreren naar Azure SQL Database](tutorial-sql-server-to-azure-sql.md)
* [SQL Server online migreren naar Azure SQL Database](./tutorial-sql-server-to-azure-sql.md)
* [SQL Server voor het offline migreren naar een beheerd exemplaar van Azure SQL](tutorial-sql-server-to-managed-instance.md)
* [SQL Server naar een Azure SQL Managed instance online migreren](tutorial-sql-server-managed-instance-online.md)