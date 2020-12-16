---
title: Feed wijzigen in de Azure Cosmos DB-API voor Cassandra
description: Meer informatie over het gebruik van Change feed in de Azure Cosmos DB-API voor Cassandra om de wijzigingen op te halen die zijn aangebracht in uw gegevens.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: 10f037dddcce43a1e023982af816660bd325d57f
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605090"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Feed wijzigen in de Azure Cosmos DB-API voor Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

De ondersteuning voor het [wijzigen van feeds](change-feed.md) in de Azure Cosmos DB-API voor Cassandra is beschikbaar via de query predikaten in de Cassandra query language (CQL). Met behulp van deze predikaten kunt u een query uitvoeren op de feed-API voor wijzigingen. Toepassingen kunnen de wijzigingen die in een tabel zijn aangebracht, ophalen met behulp van de primaire sleutel (ook wel bekend als de partitie sleutel) als vereist in CQL. Vervolgens kunt u verdere acties uitvoeren op basis van de resultaten. Wijzigingen in de rijen in de tabel worden vastgelegd in de volg orde van de wijzigings tijd en de sorteer volgorde per partitie sleutel.

In het volgende voor beeld ziet u hoe u een wijzigings feed kunt ophalen voor alle rijen in een Cassandra-API tabel met behulp van .NET. Het predicaat COSMOS_CHANGEFEED_START_TIME () wordt direct in CQL gebruikt om een query uit te voeren op items in de feed van een opgegeven begin tijd (in dit geval de huidige datum/tijd). U kunt hier het volledige voor beeld downloaden, voor C# [hier](/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/) en [voor Java.](https://github.com/Azure-Samples/cosmos-changefeed-cassandra-java)

Bij elke iteratie wordt de query hervat op het laatste punt dat wijzigingen zijn gelezen, met behulp van de wissel status. We kunnen een doorlopende stroom van nieuwe wijzigingen in de tabel in de spatie weer geven. Er worden wijzigingen in rijen weer geven die zijn ingevoegd of bijgewerkt. Het is niet mogelijk om bewerkingen voor verwijderen te volgen met behulp van Change feed in Cassandra-API.

# <a name="java"></a>[Java](#tab/java)

```java
    Session cassandraSession = utils.getSession();

    try {
        DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");  
        LocalDateTime now = LocalDateTime.now().minusHours(6).minusMinutes(30);  
        String query="SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME()='" 
            + dtf.format(now)+ "'";
        
        byte[] token=null; 
        System.out.println(query); 
        while(true)
        {
            SimpleStatement st=new  SimpleStatement(query);
            st.setFetchSize(100);
            if(token!=null)
                st.setPagingStateUnsafe(token);
            
            ResultSet result=cassandraSession.execute(st) ;
            token=result.getExecutionInfo().getPagingState().toBytes();
            
            for(Row row:result)
            {
                System.out.println(row.getString("user_name"));
            }
        }
    } finally {
        utils.close();
        LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL");
    }
```

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```
---

U kunt de primaire sleutel in de query toevoegen om de wijzigingen in één rij op primaire sleutel op te halen. In het volgende voor beeld ziet u hoe u wijzigingen bijhoudt voor de rij met ' user_id = 1 '

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

# <a name="java"></a>[Java](#tab/java)

```java
    String query="SELECT * FROM uprofile.user where user_id=1 and COSMOS_CHANGEFEED_START_TIME()='" 
                       + dtf.format(now)+ "'";
    SimpleStatement st=new  SimpleStatement(query);
```
---
## <a name="current-limitations"></a>Huidige beperkingen

De volgende beperkingen zijn van toepassing wanneer u Change feed gebruikt met Cassandra-API:

* Toevoegingen en updates worden momenteel ondersteund. De Verwijder bewerking wordt nog niet ondersteund. Als tijdelijke oplossing kunt u een zachte markering toevoegen aan rijen die worden verwijderd. Voeg bijvoorbeeld een veld toe aan de rij ' verwijderd ' en stel deze in op ' True '.
* De laatste update is persistent als in de kern-SQL-API en tussenliggende updates voor de entiteit zijn niet beschikbaar.


## <a name="error-handling"></a>Foutafhandeling

De volgende fout codes en-berichten worden ondersteund bij het gebruik van Change feed in Cassandra-API:

* **HTTP-fout code 429** : wanneer de wijzigings feed de frequentie beperkt is, wordt er een lege pagina geretourneerd.

## <a name="next-steps"></a>Volgende stappen

* [Azure Cosmos DB Cassandra-API resources beheren met behulp van Azure Resource Manager sjablonen](./templates-samples-cassandra.md)