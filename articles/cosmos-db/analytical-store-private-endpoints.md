---
title: Configureer persoonlijke eind punten voor Azure Cosmos DB-analytische opslag.
description: Meer informatie over het instellen van beheerde privé-eind punten voor Azure Cosmos DB-analytische opslag om netwerk toegang te beperken.
author: AnithaAdusumilli
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anithaa
ms.openlocfilehash: 2f15b397fbceb9e097d94080ba03fba50a96ed06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048502"
---
# <a name="configure-private-endpoints-for-azure-cosmos-db-analytical-store"></a>Persoonlijke eind punten configureren voor Azure Cosmos DB-analytische opslag
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

In dit artikel vindt u informatie over het instellen van beheerde privé-eind punten voor Azure Cosmos DB-analytische opslag. Als u de transactionele Store gebruikt, raadpleegt u [privé-eind punten voor het artikel transactionele Store](how-to-configure-private-endpoints.md) . Met beheerde privé-eind punten kunt u de netwerk toegang van Azure Cosmos DB-analytische opslag beperken tot door Azure Synapse beheerd virtueel netwerk. Beheerde persoonlijke eind punten maken een persoonlijke koppeling naar uw analytische opslag.

## <a name="enable-private-endpoint-for-the-analytical-store"></a>Persoonlijk eind punt inschakelen voor de analytische opslag

### <a name="set-up-an-azure-synapse-analytics-workspace-with-a-managed-virtual-network"></a>Een Azure Synapse Analytics-werk ruimte met een beheerd virtueel netwerk instellen

[Maak een werk ruimte in azure Synapse Analytics met data-exfiltration ingeschakeld.](../synapse-analytics/security/how-to-create-a-workspace-with-data-exfiltration-protection.md) Met [beveiliging tegen gegevens exfiltration](../synapse-analytics/security/workspace-data-exfiltration-protection.md)kunt u ervoor zorgen dat kwaadwillende gebruikers geen gegevens van uw Azure-resources kunnen kopiëren of overdragen naar locaties buiten het bereik van uw organisatie.

De volgende toegangs beperkingen zijn van toepassing wanneer de exfiltration-beveiliging is ingeschakeld voor een Azure Synapse Analytics-werk ruimte:

* Als u Azure Spark gebruikt voor Azure Synapse Analytics, is toegang alleen toegestaan voor de goedgekeurde beheerde privé-eind punten voor Azure Cosmos DB-analytische opslag.

* Als u Synapse serverloze SQL-groepen gebruikt, kunt u een query uitvoeren op elk Azure Cosmos DB account met behulp van de koppeling van Azure Synapse. Het schrijven van aanvragen die [externe tabellen maken als Select (CETAS)](../synapse-analytics/sql/develop-tables-cetas.md) zijn echter alleen toegestaan voor de goedgekeurde persoonlijke eind punten voor beheer in het virtuele netwerk van de werk ruimte.

> [!NOTE]
> U kunt de configuratie van het beheerde virtuele netwerk en de gegevens exfiltration niet wijzigen nadat de werk ruimte is gemaakt.

### <a name="add-a-managed-private-endpoint-for-azure-cosmos-db-analytical-store"></a>Een beheerd privé-eind punt voor Azure Cosmos DB-analytische opslag toevoegen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

1. Ga vanuit het Azure Portal naar de Synapse Analytics-werk ruimte en open het deel venster **overzicht** .

1. Start Synapse Studio door te navigeren naar het deel venster aan **de slag en** Selecteer **openen** onder **Open Synapse Studio**.

1. Open in de Synapse Studio het tabblad **beheren** .

1. Navigeer naar **beheerde persoonlijke eind punten** en selecteer **Nieuw**

   :::image type="content" source="./media/analytical-store-private-endpoints/create-new-private-endpoint.png" alt-text="Maak een nieuw persoonlijk eind punt voor de analytische opslag." border="true":::

1. Selecteer **Azure Cosmos DB-account type (SQL-API)** > **door gaan**.

   :::image type="content" source="./media/analytical-store-private-endpoints/select-private-endpoint.png" alt-text="Selecteer Azure Cosmos DB SQL-API om een persoonlijk eind punt te maken." border="true":::

1. Vul het nieuwe formulier voor het **beheerde persoonlijke eind punt** in met de volgende details:

   * **Naam** -naam voor uw beheerde persoonlijke eind punt. Deze naam kan niet worden bijgewerkt nadat deze is gemaakt.
   * **Beschrijving** : Geef een beschrijvende beschrijving op om uw persoonlijke eind punt te identificeren.
   * **Azure-abonnement** : selecteer een Azure Cosmos DB-account in de lijst met beschik bare accounts in uw Azure-abonnementen.
   * **Azure Cosmos DB account naam** : Selecteer een bestaand Azure Cosmos DB account van het type SQL of MongoDb.
   * **Subbron van doel** : Selecteer een van de volgende opties: **analytisch**: als u het persoonlijke eind punt voor Azure Cosmos DB Analytical Store wilt toevoegen.
     **SQL** (of **MongoDb**): als u een OLTP of een transactioneel account eindpunt wilt toevoegen.

   > [!NOTE]
   > U kunt zowel transactionele opslag als persoonlijke eind punten van een analytische opslag toevoegen aan hetzelfde Azure Cosmos DB-account in een Azure Synapse Analytics-werk ruimte. Als u alleen analytische query's wilt uitvoeren, wilt u mogelijk alleen het analytische persoonlijke eind punt toewijzen.

   :::image type="content" source="./media/analytical-store-private-endpoints/choose-analytical-private-endpoint.png" alt-text="Kies analytische voor de doel-subresource." border="true":::

1. Nadat u hebt gemaakt, gaat u naar de naam van het persoonlijke eind punt en selecteert u **goed keuringen beheren in azure Portal**.

1. Navigeer naar uw Azure Cosmos DB-account, selecteer het persoonlijke eind punt en selecteer **goed keuren**.

1. Ga terug naar de werk ruimte Synapse Analytics en klik op **vernieuwen** in het deel venster **beheerde privé-eind punten** . Controleer of het persoonlijke eind punt de status **goedgekeurd** heeft.

   :::image type="content" source="./media/analytical-store-private-endpoints/approved-private-endpoint.png" alt-text="Controleer of het persoonlijke eind punt is goedgekeurd." border="true":::

## <a name="use-apache-spark-for-azure-synapse-analytics"></a>Apache Spark gebruiken voor Azure Synapse Analytics

Als u een Azure Synapse-werk ruimte hebt gemaakt waarbij gegevens exfiltration-beveiliging is ingeschakeld, wordt de uitgaande toegang van Synapse Spark naar Azure Cosmos DB accounts standaard geblokkeerd. Als de Azure Cosmos DB al een bestaand persoonlijk eind punt heeft, wordt Synapse Spark geblokkeerd om het te openen.

Toegang tot Azure Cosmos DB gegevens toestaan:

* Als u een Azure Synapse-koppeling gebruikt om Azure Cosmos DB gegevens op te vragen, voegt u een beheerd uitgaand **analyse** -eind punt toe voor het Azure Cosmos DB-account.

* Als u batch schrijft/Lees bewerkingen en/of streaming schrijft/leest naar transactionele Store, voegt u een beheerd *SQL* -of *MongoDb* -persoonlijk eind punt toe voor het Azure Cosmos DB-account. Daarnaast moet u ook *connectionMode* instellen op *Gateway* , zoals wordt weer gegeven in het volgende code fragment:

  ```python
  # Write a Spark DataFrame into an Azure Cosmos DB container
  # To select a preferred lis of regions in a multi-region account, add .option("spark.cosmos.preferredRegions", "<Region1>, <Region2>")
  
  YOURDATAFRAME.write\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<your-Cosmos-DB-linked-service-name>")\
    .option("spark.cosmos.container","<your-Cosmos-DB-container-name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .option("spark.cosmos.connection.mode", "Gateway")\
    .mode('append')\
    .save()
  
  ```

## <a name="using-synapse-serverless-sql-pools"></a>Synapse SQL-groepen zonder server gebruiken

Synapse SQL-Pools zonder server gebruiken multi tenant-mogelijkheden die niet worden geïmplementeerd in een beheerd virtueel netwerk. Als het Azure Cosmos DB account een bestaand persoonlijk eind punt heeft, wordt de toegang tot het account via een Synapse serverloze SQL-groep geblokkeerd vanwege netwerk isolatie controles voor het Azure Cosmos DB-account.

Netwerk isolatie configureren voor dit account vanuit een Synapse-werk ruimte:

1. Geef de Synapse-werk ruimte toegang tot het Azure Cosmos DB-account door `NetworkAclBypassResourceId` de instelling voor het account op te geven.

   **PowerShell gebruiken**

   ```powershell-interactive
   Update-AzCosmosDBAccount -Name MyCosmosDBDatabaseAccount -ResourceGroupName MyResourceGroup -NetworkAclBypass AzureServices -NetworkAclBypassResourceId "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   **Azure CLI gebruiken**

   ```azurecli-interactive
   az cosmosdb update --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --network-acl-bypass AzureServices --network-acl-bypass-resource-ids "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   > [!NOTE]
   > Azure Cosmos DB-account en de Azure Synapse Analytics-werk ruimte moeten zich onder dezelfde Azure Active Directory (AD) Tenant bevinden.

2. U hebt nu toegang tot het account vanuit SQL-groepen zonder server, met behulp van T-SQL-query's via Azure Synapse link. Als u echter zeker wilt zijn van netwerk isolatie voor de gegevens in de analytische opslag, moet u een door **analytisch** beheerd particulier eind punt toevoegen voor dit account. Anders worden de gegevens in het analytische archief niet geblokkeerd voor open bare toegang.

> [!IMPORTANT]
> Als u Azure Synapse-koppeling gebruikt en netwerk isolatie voor uw gegevens in de analytische opslag nodig hebt, moet u het Azure Cosmos DB account toewijzen aan de Synapse-werk ruimte met behulp van een door **analytisch** beheerd particulier eind punt.

## <a name="next-steps"></a>Volgende stappen

* Aan de slag met het uitvoeren van een [query op analytisch archief met Azure Synapse Spark](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* Aan de slag met het uitvoeren van een [query op analytische opslag met Azure Synapse serverloze SQL-groepen](../synapse-analytics/sql/query-cosmos-db-analytical-store.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
