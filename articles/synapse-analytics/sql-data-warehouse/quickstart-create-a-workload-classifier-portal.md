---
title: 'Quickstart: Een workloadclassificatie maken - Portal'
description: Azure Portal gebruiken om een workloadclassificatie met hoge urgentie te maken.
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
ms.openlocfilehash: 1f4d113f3bc6add67dd34a7ef5e3f8cdc08cecf0
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677518"
---
# <a name="quickstart-create-a-dedicated-sql-pool-workload-classifier-using-the-azure-portal"></a>Quickstart: Een workloadclassificatie voor een toegewezen SQL-pool maken met behulp van de Azure Portal

In deze quickstart maakt u een [workloadclassificatie](sql-data-warehouse-workload-classification.md) voor het toewijzen van query’s aan een workloadgroep.  De workloadclassificatie wijst aanvragen van SQL-gebruiker `ELTLogin` toe aan workloadgroep `DataLoads`.   Volg het artikel [Quickstart: Isolatie van workload configureren](quickstart-configure-workload-isolation-portal.md) voor het maken van de workloadgroep `DataLoads`.  In deze zelfstudie maakt u een workloadclassificatie met de optie WLM_LABEL voor het correct verder classificeren van aanvragen.  Met deze classificatie wordt ook de [workloadurgentie](sql-data-warehouse-workload-importance.md) `HIGH` toegewezen aan deze aanvragen.


Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.


## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

> [!NOTE]
> Het maken van een instantie van een toegewezen SQL-pool in Azure Synapse Analytics kan resulteren in een nieuwe factureerbare service.  Zie [Prijzen voor Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

In deze quickstart wordt ervan uitgegaan dat u al een instantie van een toegewezen SQL-pool hebt, met CONTROL DATABASE-rechten voor de pool. Gebruik [Maken en koppelen - portal](create-data-warehouse-portal.md) om een toegewezen SQL-pool met de naam **mySampleDataWarehouse** te maken als dat nodig is.
<br><br>
Er bestaat een workloadgroep `DataLoads`.  Zie de zelfstudie [Quickstart: Isolatie van workload configureren](quickstart-configure-workload-isolation-portal.md) voor het maken van de workloadgroep.
<br><br>
>[!IMPORTANT] 
>Uw toegewezen SQL-pool moet online zijn als u workloadbeheer wilt configureren. 


## <a name="create-a-login-for-eltlogin"></a>Aanmeldingsgegevens maken voor ELTLogin

Maak een SQL Server-verificatieaanmeldingsgegevens in de `master`database met behulp van [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) voor `ELTLogin`.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user-and-grant-permissions"></a>Gebruiker maken en machtigingen verlenen

Nadat de aanmeldingsgegevens zijn gemaakt, moet er een gebruiker worden gemaakt in de database.  Gebruik [CREATE USER](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) om de SQL-gebruiker `ELTRole` te maken in **mySampleDataWarehouse**.  Omdat we de classificatie gaan testen tijdens deze zelfstudie, moet u `ELTLogin` machtigingen verlenen voor **mySampleDataWarehouse**. 

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
GRANT CONTROL ON DATABASE::mySampleDataWarehouse TO ELTLogin 
END
;
```

## <a name="configure-workload-classification"></a>Workloadclassificatie configureren
Met classificaties kunt u aanvragen routeren naar een workloadgroep op basis van een set regels.  In de zelfstudie [Quickstart: Isolatie van workload configureren](quickstart-configure-workload-isolation-portal.md) hebt u de workloadgroep `DataLoads` gemaakt.  U gaat nu een workloadclassificatie maken voor het routeren van aanvragen naar de workloadgroep `DataLoads`.


1.  Ga naar de pagina met de toegewezen SQL-pool **mySampleDataWarehouse**.
3.  Selecteer **Workloadbeheer**.

    ![Klik op Menu](./media/quickstart-create-a-workload-classifier-portal/menu.png)

4.  Selecteer **Instellingen en classificaties** aan de rechterkant van de workloadgroep `DataLoads`.

    ![Klik op Maken](./media/quickstart-create-a-workload-classifier-portal/settings-classifiers.png)

5. Selecteer **Niet geconfigureerd** onder de kolom Classificaties.
6. Selecteer **+ Classificatie toevoegen**.

    ![Klik op Add.](./media/quickstart-create-a-workload-classifier-portal/add-wc.png)

7.  Voer `ELTLoginDataLoads` in bij **Naam**.
8.  Voer `ELTLogin` in bij **Lid**.
9.  Kies `High` voor **Urgentie van aanvraag**.  *Optioneel*, normale urgentie is de standaardinstelling.
10. Voer `fact_loads` in bij **Label**.
11. Selecteer **Toevoegen**.
12. Selecteer **Opslaan**.

    ![Klik op Configureren](./media/quickstart-create-a-workload-classifier-portal/config-wc.png)

## <a name="verify-and-test-classification"></a>Classificatie verifiëren en testen
Controleer de catalogusweergave [sys.workload_management_workload_classifiers](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql?view=azure-sqldw-latest&preserve-view=true) om de aanwezigheid van classificatie `ELTLoginDataLoads` te verifiëren.

```sql
SELECT * FROM sys.workload_management_workload_classifiers WHERE name = 'ELTLoginDataLoads'
```

Controleer de catalogusweergave [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest&preserve-view=true) om de details van de classificatie te verifiëren.

```sql
SELECT c.[name], c.group_name, c.importance, cd.classifier_type, cd.classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ELTLoginDataLoads'
```

Voer de volgende instructies uit om de classificatie te testen.  Zorg dat u verbonden bent als ``ELTLogin`` en dat ``Label`` wordt gebruikt in de query.
```sql
CREATE TABLE factstaging (ColA int)
INSERT INTO factstaging VALUES(0)
INSERT INTO factstaging VALUES(1)
INSERT INTO factstaging VALUES(2)
GO

CREATE TABLE testclassifierfact WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT * FROM factstaging
OPTION (LABEL='fact_loads')
```

Verifieer dat de instructie `CREATE TABLE` is geclassificeerd naar de workloadgroep `DataLoads` met de workloadclassificatie `ELTLoginDataLoads`.
```sql 
SELECT TOP 1 request_id, classifier_name, group_name, resource_allocation_percentage, submit_time, [status], [label], command 
FROM sys.dm_pdw_exec_requests 
WHERE [label] = 'fact_loads'
ORDER BY submit_time DESC
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de workloadclassificatie `ELTLoginDataLoads` die u in deze zelfstudie hebt gemaakt, wilt verwijderen:

1. Klik op **1 classificatie** aan de rechterkant van de workloadgroep `DataLoads`.

    ![Klik op Verwijderen](./media/quickstart-create-a-workload-classifier-portal/delete-wc.png)

2. Klik op **Classificaties**.
3. Klik op de **`...`** rechts van de workloadclassificatie `ELTLoginDataLoads`.
4. Klik op **Verwijderen**.
5. Klik op **Opslaan**.

    ![Op Opslaan klikken](./media/quickstart-create-a-workload-classifier-portal/delete-save-wc.png)

Er worden kosten in rekening gebracht voor datawarehouse-eenheden en gegevens die zijn opgeslagen in uw toegewezen SQL-pool. Deze compute- en opslagresources worden apart in rekening gebracht.

- Als u de gegevens in de opslag wilt houden, kunt u het berekenen onderbreken wanneer u de toegewezen SQL-pool niet gebruikt. Als u het berekenen onderbreekt, worden er alleen kosten in rekening gebracht voor de gegevensopslag. Wanneer u klaar bent om met de gegevens te werken, hervat u de berekening.
- Als u in de toekomst geen kosten meer wilt maken, kunt u de toegewezen SQL-pool verwijderen.

Volg deze stappen om de resources op te schonen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer uw toegewezen SQL-pool.

    ![Resources opschonen](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Als u het berekenen wilt onderbreken, selecteert u de knop **Onderbreken**. Wanneer de toegewezen SQL-pool is onderbroken, ziet u een knop **Starten**.  Als u de berekening wilt hervatten, selecteert u **Starten**.

3. Als u de toegewezen SQL-pool wilt verwijderen zodat er geen kosten in rekening worden gebracht voor berekenen of opslaan, selecteert u **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Uw workload bewaken met de metrische controlegegevens van Azure Portal.  Zie [Workloadbeheer beheren en bewaken](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) voor meer informatie.
