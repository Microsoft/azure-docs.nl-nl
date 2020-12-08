---
title: 'Quickstart: Isolatie van werkbelastingen configureren - Portal'
description: Gebruik Azure Portal om isolatie van workloads te configureren voor een toegewezen SQL-pool.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 05/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 302249b7d8490e43b841116c52500e686626433d
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460561"
---
# <a name="quickstart-configure-dedicated-sql-pool-workload-isolation-using-a-workload-group-in-the-azure-portal"></a>Quickstart: Isolatie van workloads in een toegewezen SQL-pool configureren met een workloadgroep in Azure Portal

In deze quickstart configureert u [isolatie van werkbelastingen](sql-data-warehouse-workload-isolation.md) door een werkbelastinggroep te maken voor het reserveren van resources.  Voor deze zelfstudie maken we de werkbelastinggroep voor het laden van gegevens met de naam `DataLoads`. Voor de werkbelastinggroep wordt 20% van de systeembronnen gereserveerd.  Met een isolatie van 20% voor gegevenswerkbelastingen, zijn er gegarandeerd resources om te voldoen aan de SLA's.  Nadat u de werkbelastinggroep hebt gemaakt, [maakt u een werkbelastingclassificatie](quickstart-create-a-workload-classifier-portal.md) om query's toe te wijzen aan deze werkbelastinggroep.


Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.


## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

> [!NOTE]
> Het maken van een instantie van een toegewezen SQL-pool in Azure Synapse Analytics kan resulteren in een nieuwe factureerbare service.  Zie [Prijzen voor Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

In deze quickstart wordt ervan uitgegaan dat u al een instantie van een toegewezen SQL-pool in Synapse SQL en CONTROL DATABASE-rechten hebt. Als u er een wilt maken, raadpleegt u [Quickstart: Een toegewezen SQL-pool maken - portal](../quickstart-create-sql-pool-portal.md) om een toegewezen SQL-pool met de naam **mySampleDataWarehouse** te maken.

>[!IMPORTANT] 
>Uw toegewezen SQL-pool moet online zijn als u workloadbeheer wilt configureren. 

## <a name="configure-workload-isolation"></a>Isolatie van werkbelastingen configureren

Resources van toegewezen SQL-pools kunnen worden geïsoleerd en gereserveerd voor specifieke werkbelastingen door workloadgroepen te maken.  Zie de conceptdocumentatie [Isolatie van werkbelastingen](sql-data-warehouse-workload-isolation.md) voor meer informatie over hoe u uw werkbelasting kunt beheren met werkbelastinggroepen.  In de quickstart [Maken en verbinden - portal](create-data-warehouse-portal.md) is **mySampleDataWarehouse** gemaakt en vervolgens gestart met DW100c. In de volgende stappen maakt u een werkbelastinggroep in **mySampleDataWarehouse**.

Een werkbelastinggroep maken met een isolatie van 20%:
1.  Ga naar de pagina met de toegewezen SQL-pool **mySampleDataWarehouse**.
1.  Klik op **Workloadbeheer**.
1.  Klik op **Nieuwe workloadgroep**.
1.  selecteer **Aangepast**.

    ![Klik op Aangepast](./media/quickstart-configure-workload-isolation-portal/create-wg.png)

6.  Voer `DataLoads` in voor de **Werkbelastinggroep**.
7.  Voer `20` in voor **Min. perc. resources**.
8.  Voer `5` in voor **Min. perc. resources per aanvraag**.
9.  Voer `100` in voor **Perc. cap. resources**.
10. Voer **Opslaan** in.

   ![Op Opslaan klikken](./media/quickstart-configure-workload-isolation-portal/configure-wg.png)

Er wordt een portalmelding weergegeven wanneer de werkbelastinggroep is gemaakt.  De resources van de werkbelastinggroep worden weergegeven in de grafieken onder de geconfigureerde waarden.

   ![Klik op Definitief](./media/quickstart-configure-workload-isolation-portal/display-wg.png)

## <a name="clean-up-resources"></a>Resources opschonen

De werkbelastinggroep `DataLoads` verwijderen die u in deze zelfstudie hebt gemaakt:
1. Klik op de **`...`** rechts van de werkbelastinggroep `DataLoads`.
2. Klik op **Werkbelastinggroep verwijderen**.
3. Klik op **Ja** wanneer u wordt gevraagd het verwijderen van de werkbelastinggroep te bevestigen.
4. Klik op **Opslaan**.

   ![Klik op Verwijderen](./media/quickstart-configure-workload-isolation-portal/delete-wg.png)



Er worden kosten in rekening gebracht voor datawarehouse-eenheden en gegevens die zijn opgeslagen in uw datawarehouse. Deze compute- en opslagresources worden apart in rekening gebracht.

- Als u de gegevens in de opslag wilt houden, kunt u het berekenen onderbreken wanneer u het datawarehouse niet gebruikt. Als u het berekenen onderbreekt, worden er alleen kosten in rekening gebracht voor de gegevensopslag. Wanneer u klaar bent om met de gegevens te werken, hervat u de berekening.
- Als u in de toekomst geen kosten meer wilt hebben, kunt u de datawarehouse verwijderen.

Volg deze stappen om de resources op te schonen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer uw toegewezen SQL-pool.

    ![Resources opschonen](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Als u het berekenen wilt onderbreken, selecteert u de knop **Onderbreken**. Als het datawarehouse is onderbroken, ziet u een knop **Start**.  Als u de berekening wilt hervatten, selecteert u **Starten**.

3. Als u de datawarehouse wilt verwijderen zodat er geen kosten in rekening worden gebracht voor berekenen of opslaan, selecteert u **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Als u de werkbelastinggroep `DataLoads` wilt gebruiken, moet er een [werkbelastingclassificatie](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) worden gemaakt om aanvragen naar de werkbelastinggroep te routeren.  Ga door naar de zelfstudie [Een werkbelastingclassficatie maken](quickstart-create-a-workload-classifier-portal.md) om een werkbelastingclassificatie te maken voor `DataLoads`.

## <a name="see-also"></a>Zie ook
Zie het artikel [Procedures voor het beheren en bewaken van werkbelastingbeheer](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) voor meer informatie over het bewaken van werkbelastingen voor werkbelastingbeheer.
