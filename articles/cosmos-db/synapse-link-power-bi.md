---
title: Power BI en serverloze SQL-groep voor het analyseren van Azure Cosmos DB gegevens met een Synapse-koppeling
description: Meer informatie over het maken van een serverloze SQL-groeps database en weer gaven via de Synapse-koppeling voor Azure Cosmos DB, het opvragen van de Azure Cosmos DB containers en het bouwen van een model met Power BI over die weer gaven.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/30/2020
ms.author: rosouz
ms.custom: synapse-cosmos-db
ms.openlocfilehash: d84508a7629481a7138f1080c86f4a203d35894d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626245"
---
# <a name="use-power-bi-and-serverless-synapse-sql-pool-to-analyze-azure-cosmos-db-data-with-synapse-link"></a>Power BI en serverloze Synapse SQL-groep gebruiken om Azure Cosmos DB gegevens te analyseren met Synapse-koppeling 
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

In dit artikel leert u hoe u een serverloze SQL-pool database en weer gaven kunt maken via de Synapse-koppeling voor Azure Cosmos DB. U voert een query uit op de Azure Cosmos DB containers en bouwt vervolgens een model met Power BI over die weer gaven om die query weer te geven.

In dit scenario gebruikt u dummy gegevens over de verkoop van Surface-producten in een Retail Store van partners. U gaat de omzet per winkel analyseren op basis van de nabijheid van grote gezinnen en de impact van de reclame voor een specifieke week. In dit artikel maakt u twee weer gaven met de naam **RetailSales** en **StoreDemographics** en een query ertussen. U kunt de voorbeeld product gegevens ophalen uit deze [github](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData) opslag plaats.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u de volgende resources maakt voordat u begint:

* [Maak een Azure Cosmos DB account van het type SQL (kern geheugen) of MongoDB.](create-cosmosdb-resources-portal.md)

* Azure Synapse-koppeling voor uw [Azure Cosmos-account](configure-synapse-link.md#enable-synapse-link) inschakelen

* Maak een Data Base binnen het Azure Cosmos-account en twee containers waarvoor het [analytische archief is ingeschakeld.](configure-synapse-link.md#create-analytical-ttl)

* Laad gegevens van producten in de Azure Cosmos-containers, zoals wordt beschreven in deze [batch gegevens opname](https://github.com/Azure-Samples/Synapse/blob/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/spark-notebooks/pyspark/1CosmoDBSynapseSparkBatchIngestion.ipynb) -notebook.

* [Maak een Synapse-werk ruimte met de](../synapse-analytics/quickstart-create-workspace.md) naam **SynapseLinkBI**.

* [Verbind de Azure Cosmos-data base met de Synapse-werk ruimte](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json).

## <a name="create-a-database-and-views"></a>Een Data Base en weer gaven maken

Ga in de Synapse-werk ruimte naar het tabblad **ontwikkelen** , selecteer het **+** pictogram en selecteer **SQL-script**.

:::image type="content" source="./media/synapse-link-power-bi/add-sql-script.png" alt-text="Een SQL-script toevoegen aan de Synapse Analytics-werk ruimte":::

Elke werk ruimte wordt geleverd met een serverloos SQL-eind punt. Nadat u een SQL-script hebt gemaakt, gaat u vanuit de werk balk aan de bovenkant verbinding maken met **ingebouwd**.

:::image type="content" source="./media/synapse-link-power-bi/enable-sql-on-demand-endpoint.png" alt-text="Het SQL-script inschakelen voor het gebruik van het serverloze SQL-eind punt in de werk ruimte":::

Het maken van weer gaven in de **hoofd** -of **standaard** databases wordt niet aanbevolen of ondersteund. Maak een nieuwe Data Base met de naam **RetailCosmosDB** en een SQL-weer gave over de containers die zijn ingeschakeld voor de Synapse-koppeling. De volgende opdracht laat zien hoe u een Data Base maakt:

```sql
-- Create database
Create database RetailCosmosDB
```

Maak vervolgens meerdere weer gaven in verschillende Synapse-koppelingen die zijn ingeschakeld voor Azure Cosmos-containers. Met weer gaven kunt u T-SQL gebruiken om lid te worden van en query's uitvoeren op Azure Cosmos DB gegevens in verschillende containers.  Zorg ervoor dat u de **RetailCosmosDB** -data base selecteert bij het maken van de weer gaven.

De volgende scripts laten zien hoe u weer gaven maakt voor elke container. Voor het gemak gebruiken we de functie [automatisch schema](analytical-store-introduction.md#analytical-schema) innemen van de SERVERloze SQL-groep via Synapse-koppeling ingeschakelde containers:


### <a name="retailsales-view"></a>Weer gave RetailSales:

```sql
-- Create view for RetailSales container
CREATE VIEW  RetailSales
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>',RetailSales)
AS q1
```

Zorg ervoor dat u uw Azure Cosmos DB-regio en de primaire sleutel invoegt in het vorige SQL-script. Alle tekens in de regio naam moeten in kleine letters zonder spaties worden gereduceerd. In tegens telling tot de andere para meters van de `OPENROWSET` opdracht, moet u de para meter container name opgeven zonder aanhalings tekens.

### <a name="storedemographics-view"></a>Weer gave StoreDemographics:

```sql
-- Create view for StoreDemographics container
CREATE VIEW StoreDemographics
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>', StoreDemographics)
AS q1
```

Voer nu het SQL-script uit door de opdracht **uitvoeren** te selecteren.

## <a name="query-the-views"></a>Query's uitvoeren op de weer gaven

Nu de twee weer gaven zijn gemaakt, definiëren we de query om deze twee weer gaven als volgt toe te voegen:

```sql
SELECT 
sum(p.[revenue]) as revenue
,p.[advertising]
,p.[storeId]
,p.[weekStarting]
,q.[largeHH]
 FROM [dbo].[RetailSales] as p
INNER JOIN [dbo].[StoreDemographics] as q ON q.[storeId] = p.[storeId]
GROUP BY p.[advertising], p.[storeId], p.[weekStarting], q.[largeHH]
```

Selecteer **uitvoeren** die de volgende tabel als resultaat geeft:

:::image type="content" source="./media/synapse-link-power-bi/join-views-query-results.png" alt-text="Query resultaten na het toevoegen van de weer gaven StoreDemographics en RetailSales":::

## <a name="model-views-over-containers-with-power-bi"></a>Model weergaven via containers met Power BI

Open vervolgens het Power BI bureau blad en maak verbinding met het serverloze SQL-eind punt met behulp van de volgende stappen:

1. Open de Power BI Desktop-toepassing. Selecteer **gegevens ophalen** en selecteer **meer**.

1. Kies **Azure Synapse Analytics (SQL DW)** in de lijst met verbindings opties.

1. Voer de naam in van het SQL-eind punt waar de data base zich bevindt. Voer `SynapseLinkBI-ondemand.sql.azuresynapse.net` in het veld **Server** in. In dit voor beeld is  **SynapseLinkBI** de naam van de werk ruimte. Vervang deze als u een andere naam hebt opgegeven voor uw werk ruimte. Selecteer **direct query** voor de gegevens connectiviteits modus en klik vervolgens op **OK**.

1. Selecteer de gewenste verificatie methode, zoals Azure AD.

1. Selecteer de **RetailCosmosDB** -data base en de weer gaven **RetailSales**, **StoreDemographics** .

1. Selecteer **laden** om de twee weer gaven in de direct query-modus te laden.

1. Selecteer **model** om een relatie tussen de twee weer gaven te maken via de **storeId** -kolom.

1. Sleep de kolom **StoreID** van de weer gave **RetailSales** naar de kolom **StoreID** in de weer gave **StoreDemographics** .

1. Selecteer de relatie veel op één (*: 1) omdat er meerdere rijen met dezelfde archief-ID zijn in de weer gave **RetailSales** . **StoreDemographics** heeft slechts één Store ID-rij (het is een dimensie tabel).

Ga nu naar het **rapport** venster en maak een rapport om het relatieve belang van de huishoud grootte te vergelijken met de gemiddelde omzet per opslag op basis van de verstrooide weer gave van omzet-en LargeHH-index:

1. Selecteer een **spreidings diagram**.

1. Sleep de **LargeHH** van de **StoreDemographics** -weer gave naar de X-as en zet deze neer.

1. Sleep de **opbrengst** van de weer gave **RetailSales** naar de Y-as. Selecteer **gemiddeld** om de gemiddelde verkoop per product per winkel en per week te berekenen.

1. Sleep de product code van de weer gave **RetailSales** **naar de** legenda om een specifieke productlijn te selecteren.
Nadat u deze opties hebt gekozen, ziet u een grafiek zoals in de volgende scherm afbeelding:

:::image type="content" source="./media/synapse-link-power-bi/household-size-average-revenue-report.png" alt-text="Rapport dat het relatieve belang van de omvang van de huishoudens vergelijkt met de gemiddelde omzet per winkel":::

## <a name="next-steps"></a>Volgende stappen

[T-SQL gebruiken om Azure Cosmos DB gegevens op te vragen met behulp van de Azure Synapse-koppeling](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)

Een serverloze SQL-groep gebruiken voor [het analyseren van Azure open gegevens sets en het visualiseren van de resultaten in azure Synapse Studio](../synapse-analytics/sql/tutorial-data-analyst.md)
