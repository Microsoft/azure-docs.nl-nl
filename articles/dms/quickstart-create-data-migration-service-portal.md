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
ms.date: 07/21/2020
ms.openlocfilehash: ff9fc2baaf1563d4a02364db00344ffc0bc46a6a
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060262"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Quickstart: Een Azure Database Migration Service-exemplaar maken met behulp van Azure Portal

In deze quickstart maakt u gebruik van de Azure-portal om een Azure Database Migration Service-exemplaar te maken.  Nadat u het exemplaar hebt gemaakt, kunt u deze gebruiken om gegevens vanuit SQL Server te migreren naar Azure SQL Database.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Open de webbrowser, navigeer naar [Microsoft Azure Portal](https://portal.azure.com/), en voer vervolgens uw referenties in om u aan te melden bij de portal.

De standaardweergave is uw service-dashboard.

> [!NOTE]
> U kunt maximaal tien DMS-exemplaren per abonnement per regio maken. Als u meer exemplaren nodig hebt, maakt u een ondersteuningsticket.

## <a name="register-the-resource-provider"></a>De resourceprovider registreren

Registreer de Microsoft.DataMigration-resourceprovider voordat u uw eerste Database Migration Service-exemplaar maakt.

1. Selecteer in Azure Portal de optie **Alle services** en vervolgens **Abonnementen**.

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Een exemplaar van de service maken

1. Selecteer **+ Een resource maken** om een exemplaar te maken van Azure Database Migration Service.

2. Zoek in de marketplace naar 'migration', selecteer **Azure Database Migration Service** en selecteer vervolgens in het scherm **Azure Database Migration Service**  de optie **Maken**.

3. Ga in het scherm **Migratieservice maken** als volgt te werk:

    - Kies een unieke en gemakkelijk te onthouden **Servicenaam** om uw Azure Database Migration Service-exemplaar te identificeren.
    - Selecteer het Azure-**abonnement** waarin u het exemplaar wilt maken.
    - Selecteer een bestaande **Resourcegroep** of maak een nieuwe.
    - Kies de **locatie** die zich het dichtst bij uw bron- of doelserver bevindt.
    - Selecteer een bestaand **Virtueel netwerk** of maak een nieuwe.

        Het virtuele netwerk biedt Azure Database Migration Service toegang tot de brondatabase en doelomgeving.

        Zie het artikel [Een virtueel netwerk maken met de Azure-portal](../virtual-network/quick-create-portal.md) voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

    - Selecteer Basic: 1 vCore als **prijscategorie**.

        ![Migratieservice maken](media/quickstart-create-data-migration-service-portal/dms-create-service1.png)

4. Selecteer **Maken**.

    Na enkele ogenblikken is uw Azure Database Migration Service-exemplaar gemaakt en klaar voor gebruik. Azure Database Migration Service wordt weergegeven zoals in de volgende afbeelding:

    ![Migratieservice gemaakt](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Resources opschonen

Alle resources die u in deze QuickStart hebt gemaakt, kunt u verwijderen door de [Azure-resourcegroep](../azure-resource-manager/management/overview.md) te verwijderen. U verwijdert de resourcegroep door te navigeren naar het Azure Database Migration Service-exemplaar dat u hebt gemaakt. Selecteer bij **Resourcegroep** de naam van de resourcegroep en selecteer vervolgens **Resourcegroep verwijderen**. Door deze actie worden alle items in de resourcegroep verwijderd, evenals de groep zelf.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [SQL Server naar Azure SQL Database migreren](tutorial-sql-server-to-azure-sql.md)