---
title: gegevens migreren naar een Cassandra-API-account in Azure Cosmos DB - zelfstudie
description: In deze zelf studie leert u hoe u gegevens kopieert van Apache Cassandra naar een Cassandra-API-account in Azure Cosmos DB.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 7c55cbf4b8739a885c499c820a7e68825522ea4e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449287"
---
# <a name="tutorial-migrate-your-data-to-a-cassandra-api-account"></a>Zelf studie: uw gegevens naar een Cassandra-API-account migreren
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Als ontwikkelaar hebt u mogelijk bestaande Cassandra-workloads die on-premises worden uitgevoerd of in de cloud, die u mogelijk wilt migreren naar Azure. U kunt dergelijke workloads migreren naar een Cassandra-API-account in Azure Cosmos DB. In deze zelfstudie vindt u instructies over verschillende opties voor het migreren van Apache Cassandra-gegevens naar het Cassandra-API-account in Azure Cosmos DB.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Plan voor migratie
> * Vereisten voor migratie
> * Gegevens migreren met behulp van de `cqlsh` `COPY` opdracht
> * Gegevens migreren met Spark

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites-for-migration"></a>Vereisten voor migratie

* **Maak een schatting van de doorvoervereisten:** Voordat u gegevens migreert naar het Cassandra-API-account in Azure Cosmos DB, moet u een schatting maken van de doorvoervereisten voor uw workload. In het algemeen begint u met de gemiddelde door Voer die vereist is voor de ruwe bewerkingen en neemt u vervolgens de extra door Voer op die vereist is voor de piekige-bewerkingen voor het uitpakken van trans formatie U hebt de volgende gegevens nodig om de migratie te plannen: 

  * **Bestaande gegevensgrootte of een schatting van de gegevensgrootte:** de minimale databasegrootte en de vereiste voor de doorvoer. Als u de gegevens grootte voor een nieuwe toepassing wilt schatten, kunt u ervan uitgaan dat de gegevens gelijkmatig over de rijen worden gedistribueerd en de waarde schatten door te vermenigvuldigen met de grootte van de gegevens. 

  * **Vereiste door Voer:** Geschatte doorvoer snelheid van Lees bewerkingen (query/Get) en schrijven (bijwerken/verwijderen/invoegen). Deze waarde is vereist voor het berekenen van de vereiste aanvraag eenheden, samen met de gegevens grootte van de constante status.  

  * **Het schema:** Maak verbinding met uw bestaande Cassandra-cluster via `cqlsh` en exporteer het schema van Cassandra: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    Nadat u de vereisten van uw bestaande werk belasting hebt geïdentificeerd, maakt u een Azure Cosmos DB-account,-data base en containers, volgens de vereisten voor verzamelde door voer.  

  * **De RU-kosten voor een bewerking bepalen:** u kunt de RU's bepalen met behulp van een van de SDK's die worden ondersteund door de Cassandra-API. In dit voorbeeld ziet u de .NET-versie van het ophalen van RU-kosten.

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
      {
         byte[] valueInBytes = customPayload[key];
         double value = Encoding.UTF8.GetString(valueInBytes);
         Console.WriteLine($"CustomPayload:  {key}: {value}");
      }
    ```

* **Toewijzen van de vereiste doorvoer:** Azure Cosmos DB kan opslag en doorvoer automatisch schalen als uw vereisten veranderen. U kunt uw doorvoer schatten met behulp van de [calculator voor aanvraageenheden en gegevensopslag](https://www.documentdb.com/capacityplanner). 

* **Tabellen maken in het Cassandra-API-account:** Voordat u begint met het migreren van gegevens, maakt u vooraf al uw tabellen van de Azure Portal of van `cqlsh` . Als u migreert naar een Azure Cosmos DB-account met door Voer op database niveau, moet u ervoor zorgen dat u een partitie sleutel opgeeft wanneer u de containers maakt.

* **Doorvoer verhogen**: de duur van de gegevensmigratie is afhankelijk van de hoeveelheid doorvoer die u voor de tabellen hebt ingericht in Azure Cosmos DB. Verhoog de doorvoer voor de duur van de migratie. Met een hogere doorvoer voorkomt u frequentielimieten en kost migreren minder tijd. Nadat u de migratie hebt voltooid, verlaagt u de doorvoer om kosten te besparen. U wordt ook aangeraden het Azure Cosmos DB-account in dezelfde regio te hebben als de bron database. 

* **TLS inschakelen:** Voor Azure Cosmos DB gelden strenge beveiligingsvereisten en -normen. Schakel TLS in wanneer u uw account gebruikt. Wanneer u CQL gebruikt met SSH, hebt u een optie om TLS-gegevens op te geven.

## <a name="options-to-migrate-data"></a>Mogelijkheden voor migreren van gegevens

U kunt gegevens van bestaande Cassandra-workloads naar Azure Cosmos DB verplaatsen met behulp van de `cqlsh` `COPY` opdracht of met Spark. 

### <a name="migrate-data-by-using-the-cqlsh-copy-command"></a>Gegevens migreren met behulp van de cqlsh-Kopieer opdracht

Gebruik de [Kopieer opdracht CQL](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) om lokale gegevens te kopiëren naar het Cassandra-API-account in azure Cosmos db.

1. Vraag de verbindingsreeks van uw Cassandra API-account op:

   * Meld u aan bij de [Azure Portal](https://portal.azure.com)en ga naar uw Azure Cosmos DB-account.

   * Open het deelvenster **Verbindingsreeks**. Hier ziet u alle informatie die u nodig hebt om verbinding te maken met uw Cassandra-API-account `cqlsh` .

1. Meld u aan bij met `cqlsh` behulp van de verbindings gegevens van de portal.

1. Gebruik de `CQL` `COPY` opdracht om lokale gegevens naar het Cassandra-API-account te kopiëren.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

### <a name="migrate-data-by-using-spark"></a>Gegevens migreren met Spark 

Gebruik de volgende stappen om gegevens met Spark naar het Cassandra API-account te migreren:

1. Een [Azure Databricks cluster](cassandra-spark-databricks.md) of een [Azure HDInsight-cluster](cassandra-spark-hdinsight.md)inrichten. 

1. Gegevens verplaatsen naar het doel Cassandra-API eind punt met behulp van de [tabel Kopieer bewerking](cassandra-spark-table-copy-ops.md). 

Het migreren van gegevens met behulp van Spark-taken wordt aanbevolen als er gegevens aanwezig zijn in een bestaand cluster op virtuele Azure-machines of in een andere cloud. Hiervoor moet u Spark als een intermediair instellen voor eenmalige of regel matige opname. U kunt deze migratie versnellen door gebruik te maken van de Azure ExpressRoute-connectiviteit tussen uw on-premises omgeving en Azure. 

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, kunt u de resource groep, het Azure Cosmos DB-account en alle gerelateerde resources verwijderen. Daarvoor selecteert u de resourcegroep voor de virtuele machine en selecteert u **Verwijderen**. Vervolgens bevestigt u de naam van de resourcegroep die u wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u uw gegevens naar een Cassandra-API-account kunt migreren in Azure Cosmos DB. U vindt nu meer informatie over andere concepten in Azure Cosmos DB:

> [!div class="nextstepaction"]
> [Instelbare gegevensconsistentieniveaus in Azure Cosmos DB](../cosmos-db/consistency-levels.md)




