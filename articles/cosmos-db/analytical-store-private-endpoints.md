---
title: Configureer privé-eindpunten voor Azure Cosmos DB analytische opslag.
description: Meer informatie over het instellen van beheerde privé-eindpunten voor Azure Cosmos DB analytische opslag om de netwerktoegang te beperken.
author: AnithaAdusumilli
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anithaa
ms.openlocfilehash: fd0b3ada5fec283562cee9727e3f805a7d34c532
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479046"
---
# <a name="configure-azure-private-link-for-azure-cosmos-db-analytical-store"></a>Een Azure Private Link configureren Azure Cosmos DB analytische opslag
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

In dit artikel leert u hoe u beheerde privé-eindpunten kunt instellen voor Azure Cosmos DB analytische opslag. Als u de transactionele opslag gebruikt, zie dan het artikel [Privé-eindpunten voor de transactionele](how-to-configure-private-endpoints.md) opslag. Met [behulp van beheerde privé-eindpunten](../synapse-analytics/security/synapse-workspace-managed-private-endpoints.md)kunt u de netwerktoegang van uw analytische Azure Cosmos DB-opslag beperken tot een beheerde Virtual Network die is gekoppeld aan uw Azure Synapse werkruimte. Beheerde privé-eindpunten maken een privékoppeling naar uw analytische opslag.

## <a name="enable-a-private-endpoint-for-the-analytical-store"></a>Een privé-eindpunt inschakelen voor de analytische opslag

### <a name="set-up-azure-synapse-analytics-workspace-with-a-managed-virtual-network"></a>Een werkruimte Azure Synapse Analytics een beheerd virtueel netwerk instellen

[Maak een werkruimte in Azure Synapse Analytics met gegevens-exfiltratie ingeschakeld.](../synapse-analytics/security/how-to-create-a-workspace-with-data-exfiltration-protection.md) Met [beveiliging tegen gegevensexfiltratie](../synapse-analytics/security/workspace-data-exfiltration-protection.md)kunt u ervoor zorgen dat kwaadwillende gebruikers geen gegevens van uw Azure-resources kunnen kopiëren of overdragen naar locaties buiten het bereik van uw organisatie.

De volgende toegangsbeperkingen zijn van toepassing wanneer gegevens exfiltratiebeveiliging is ingeschakeld voor een Azure Synapse Analytics werkruimte:

* Als u Azure Spark gebruikt voor Azure Synapse Analytics, is toegang tot de goedgekeurde beheerde privé-eindpunten alleen toegestaan voor Azure Cosmos DB analytische opslag.

* Als u serverloze SQL-pools van Synapse gebruikt, kunt u een query uitvoeren op elk Azure Cosmos DB met behulp van Azure Synapse Link. Schrijfaanvragen die externe tabellen als select [(CETAS)](../synapse-analytics/sql/develop-tables-cetas.md) maken, zijn echter alleen toegestaan voor de goedgekeurde privé-eindpunten voor beheer in het virtuele netwerk van de werkruimte.

> [!NOTE]
> U kunt de configuratie van het beheerde virtuele netwerk en de configuratie van gegevens-exfiltratie niet wijzigen nadat de werkruimte is gemaakt.

### <a name="add-a-managed-private-endpoint-for-azure-cosmos-db-analytical-store"></a>Een beheerd privé-eindpunt toevoegen voor Azure Cosmos DB analytische opslag

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

1. Navigeer Azure Portal de werkruimte Synapse Analytics en open het **deelvenster** Overzicht.

1. Start Synapse Studio door te navigeren naar **Aan de slag** deelvenster en **selecteer Openen** onder **Openen Synapse Studio**.

1. Open in Synapse Studio het **tabblad** Beheren.

1. **Navigeer naar Beheerde privé-eindpunten** en selecteer **Nieuw**

   :::image type="content" source="./media/analytical-store-private-endpoints/create-new-private-endpoint.png" alt-text="Maak een nieuw privé-eindpunt voor analytische opslag." border="true":::

1. Selecteer **Azure Cosmos DB accounttype (SQL API)** > **Doorgaan.**

   :::image type="content" source="./media/analytical-store-private-endpoints/select-private-endpoint.png" alt-text="Selecteer Azure Cosmos DB SQL-API om een privé-eindpunt te maken." border="true":::

1. Vul het formulier **Nieuw beheerd privé-eindpunt** in met de volgende gegevens:

   * **Naam:** naam voor uw beheerde privé-eindpunt. Deze naam kan niet worden bijgewerkt nadat deze is gemaakt.
   * **Beschrijving:** geef een beschrijvende beschrijving op om uw privé-eindpunt te identificeren.
   * **Azure-abonnement:** selecteer Azure Cosmos DB account in de lijst met beschikbare accounts in uw Azure-abonnementen.
   * **Azure Cosmos DB accountnaam:** selecteer een bestaand Azure Cosmos DB account van het type SQL of MongoDB.
   * **Doelsubopslag** : selecteer een van de volgende **opties:** Analytisch: als u het privé-eindpunt wilt toevoegen voor Azure Cosmos DB analytische opslag.
     **SQL** (of **MongoDB):** als u het eindpunt van het OLTP- of transactionele account wilt toevoegen.

   > [!NOTE]
   > U kunt privé-eindpunten voor zowel transactionele als analytische opslag toevoegen aan hetzelfde Azure Cosmos DB account in een Azure Synapse Analytics werkruimte. Als u alleen analytische query's wilt uitvoeren, wilt u mogelijk alleen het analytische privé-eindpunt in kaart brengen.

   :::image type="content" source="./media/analytical-store-private-endpoints/choose-analytical-private-endpoint.png" alt-text="Kies analytical voor de doelsubresource." border="true":::

1. Ga na het maken naar de naam van het privé-eindpunt en **selecteer Goedkeuringen beheren in Azure Portal**.

1. Navigeer naar Azure Cosmos DB-account, selecteer het privé-eindpunt en selecteer **Goedkeuren.**

1. Ga terug naar Synapse Analytics werkruimte en klik **op Vernieuwen** in het deelvenster **Beheerde privé-eindpunten.** Controleer of het privé-eindpunt de status **Goedgekeurd** heeft.

   :::image type="content" source="./media/analytical-store-private-endpoints/approved-private-endpoint.png" alt-text="Controleer of het privé-eindpunt is goedgekeurd." border="true":::

## <a name="use-apache-spark-for-azure-synapse-analytics"></a>Gebruik Apache Spark voor Azure Synapse Analytics

Als u een Azure Synapse-werkruimte hebt gemaakt met gegevens-exfiltratiebeveiliging ingeschakeld, wordt de uitgaande toegang van Synapse Spark naar Azure Cosmos DB-accounts standaard geblokkeerd. Als de Azure Cosmos DB al een bestaand privé-eindpunt heeft, heeft Synapse Spark geen toegang tot het eindpunt.

Toegang tot gegevens Azure Cosmos DB toestaan:

* Als u Azure Synapse Link gebruikt om een query uit Azure Cosmos DB gegevens, voegt u een beheerd **analytisch** privé-eindpunt toe voor het Azure Cosmos DB account.

* Als u gebruik maakt van batch-schrijf-/lees- en/of streaming-schrijf-/lees-/schrijf- en lees- en schrijf- en/of -leestransacties in transactionele opslag, voegt u een beheerd *SQL-* of *MongoDB-privé-eindpunt* toe voor het Azure Cosmos DB-account. Bovendien moet u *connectionMode* ook instellen op *Gateway,* zoals wordt weergegeven in het volgende codefragment:

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

## <a name="using-synapse-serverless-sql-pools"></a>Serverloze SQL-pools van Synapse gebruiken

Serverloze SQL-pools van Synapse maken gebruik van mogelijkheden voor meerdere tenants die niet zijn geïmplementeerd in het beheerde virtuele netwerk. Als het Azure Cosmos DB-account een bestaand privé-eindpunt heeft, kan de serverloze SQL-pool van Synapse geen toegang krijgen tot het account vanwege netwerkisolatiecontroles op het Azure Cosmos DB-account.

Netwerkisolatie configureren voor dit account vanuit een Synapse-werkruimte:

1. Sta de Synapse-werkruimte toegang tot het Azure Cosmos DB-account door de `NetworkAclBypassResourceId` instelling voor het account op te geven.

   **PowerShell gebruiken**

   ```powershell-interactive
   Update-AzCosmosDBAccount -Name MyCosmosDBDatabaseAccount -ResourceGroupName MyResourceGroup -NetworkAclBypass AzureServices -NetworkAclBypassResourceId "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   **Azure CLI gebruiken**

   ```azurecli-interactive
   az cosmosdb update --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --network-acl-bypass AzureServices --network-acl-bypass-resource-ids "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   > [!NOTE]
   > Azure Cosmos DB account en Azure Synapse Analytics moeten zich onder dezelfde ad-tenant (Azure Active Directory) vallen.

2. U hebt nu toegang tot het account vanuit serverloze SQL-pools met behulp van T-SQL-query's via Azure Synapse Link. Als u echter netwerkisolatie wilt garanderen voor de gegevens in de analytische opslag, moet u een **analytisch** beheerd privé-eindpunt voor dit account toevoegen. Anders worden de gegevens in de analytische opslag niet geblokkeerd voor openbare toegang.

> [!IMPORTANT]
> Als u Azure Synapse Link gebruikt en netwerkisolatie nodig hebt voor uw gegevens in de analytische opslag, moet u het Azure Cosmos DB-account aan de Synapse-werkruimte koppelen met behulp van **een** analytisch beheerd privé-eindpunt.

## <a name="next-steps"></a>Volgende stappen

* Aan de slag met [het uitvoeren van query's op analytische opslag met Azure Synapse Spark](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* Aan de slag met [het uitvoeren van query's op analytische opslag Azure Synapse serverloze SQL-pools](../synapse-analytics/sql/query-cosmos-db-analytical-store.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
