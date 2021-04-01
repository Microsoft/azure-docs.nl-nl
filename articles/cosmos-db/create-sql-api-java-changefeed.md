---
title: Een end-to-end Azure Cosmos DB Java SDK v4-toepassingsvoorbeeld maken met behulp van de wijzigingenfeed
description: In deze handleiding wordt uitgelegd hoe u een eenvoudige Java SQL API-toepassing maakt die documenten invoegt in een Azure Cosmos DB-container, terwijl u een gerealiseerde weergave van de container behoudt met de wijzigingenfeed.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 06/11/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 765fd3afc7fe688d3e6b0e3394e7dc8c39af69b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93096849"
---
# <a name="how-to-create-a-java-application-that-uses-azure-cosmos-db-sql-api-and-change-feed-processor"></a>Een Java-toepassing maken die Azure Cosmos DB SQL API en de wijzigingenfeedverwerer gebruikt
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In deze handleiding wordt uitgelegd hoe u een eenvoudige Java-toepassing maakt die gebruikmaakt van de Azure Cosmos DB SQL API om documenten in een Azure Cosmos DB-container in te voegen, terwijl u een gerealiseerde weergave van de container behoudt met de wijzigingenfeed en de wijzigingenfeedverwerker. De Java-toepassing communiceert met de Azure Cosmos DB SQL API met behulp van Azure Cosmos DB Java SDK v4.

> [!IMPORTANT]  
> Deze zelfstudie geldt alleen voor Azure Cosmos DB Java SDK v4. Bekijk de [release-opmerkingen](sql-api-sdk-java-v4.md) bij Azure Cosmos DB Java SDK v4, de [Maven-opslagplaats](https://mvnrepository.com/artifact/com.azure/azure-cosmos), [prestatietips](performance-tips-java-sdk-v4-sql.md) voor Azure Cosmos DB Java SDK v4 en de [probleemoplossingsgids](troubleshoot-java-sdk-v4-sql.md) voor Azure Cosmos DB Java SDK v4 voor meer informatie. Als u momenteel een oudere versie dan v4 gebruikt, raadpleegt u de gids [Migreren naar Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) voor hulp om te upgraden naar v4.
>

## <a name="prerequisites"></a>Vereisten

* De URI en sleutel voor uw Azure Cosmos DB-account

* Maven

* Java 8

## <a name="background"></a>Achtergrond

De Azure Cosmos DB-wijzigingenfeed biedt een op gebeurtenissen gebaseerde interface om acties te activeren als reactie op het invoegen van documenten. Dit heeft veel toepassingen. Bijvoorbeeld in toepassingen die waarin zowel veel lees- als schrijfbewerkingen plaatsvinden is een belangrijk gebruik van de wijzigingenfeed om een realtime, **gerealiseerde weergave** te maken van een container terwijl er documenten worden opgenomen. De gerealiseerde weergavecontainer bevat dezelfde gegevens, maar dan gepartitioneerd voor efficiënte inzage, waardoor de toepassing efficiënt is om zowel in te lezen als in te schrijven.

Gebeurtenissen in de wijzigingenfeed bijhouden wordt grotendeels gedaan door de bibliotheek voor wijzigingenfeedverwerking die in de SDK is ingebouwd. Deze bibliotheek is krachtig genoeg om gebeurtenissen in de wijzigingenfeed tussen meerdere werkrollen te verdelen, indien dat is gewenst. U hoeft alleen een callback op te geven in de bibliotheek van de wijzigingenfeed.

Dit eenvoudige voorbeeld demonstreert de verwerkingsbibliotheek van de wijzigingenfeed met een enkele werkrol die documenten maakt en verwijdert vanuit een gerealiseerde weergave.

## <a name="setup"></a>Instellen

Kloon de opslagplaats van het app-voorbeeld als u dat nog niet hebt gedaan:

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

Open een terminal in de opslagplaatsmap. Bouw de app door de volgende opdracht uit te voeren

```bash
mvn clean package
```

## <a name="walkthrough"></a>Walkthrough

1. Controleer eerst of u een Azure Cosmos DB-account hebt. Open de **Azure-portal** in uw browser, ga naar uw Azure Cosmos DB-account en navigeer naar **Data Explorer** in het linker deelvenster.

   :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_account_empty.JPG" alt-text="Azure Cosmos DB-account":::

1. Voer de app in de terminal uit met de volgende opdracht:

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. Druk op enter wanneer u het volgende ziet

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    Ga vervolgens terug naar de Azure Portal-gegevensverkenner in uw browser. U ziet dat een database met de naam **GroceryStoreDatabase** is toegevoegd met drie lege containers: 

    * **InventoryContainer**: de inventarisrecord voor ons supermarktvoorbeeld, gepartitioneerd op item ```id```, dat een UUID is.
    * **InventoryContainer-pktype**: een gerealiseerde weergave van het inventarisrecord, geoptimaliseerd voor query's in item ```type```
    * **InventoryContainer-leases**: een leasecontainer is altijd nodig voor een wijzigingenfeed. Deze houdt de leesvoortgang van de app bij in de wijzigingenfeed.

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG" alt-text="Lege containers":::

1. In de terminal ziet u nu een prompt

    ```bash
    Press enter to start creating the materialized view...
    ```

    Druk op Enter. Het volgende codeblok voert de verwerker van de wijzigingenfeed uit en initialiseert deze in een andere thread: 

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=InitializeCFP)]

    ```"SampleHost_1"``` is de naam van de werkrol Wijzigingenfeedverwerker. ```changeFeedProcessorInstance.start()``` is waarmee de Wijzigingenfeedverwerker wordt gestart.

    Ga terug naar de Azure Portal-gegevensverkenner in uw browser. Klik op **items** in de container **InventoryContainer-leases** om de inhoud ervan te bekijken. U ziet dat de wijzigingenfeedverwerker de leasecontainer heeft gevuld, ofwel de verwerker heeft de werkrol ```SampleHost_1``` een lease gegeven op een aantal partities van de **InventoryContainer**.

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_leases.JPG" alt-text="Leases":::

1. Druk in de terminal nogmaals op enter. Hiermee worden 10 documenten ingevoegd in **InventoryContainer**. Elke invoeging van documenten verschijnt in de wijzigingenfeed als JSON. De volgende callback-code verwerkt deze gebeurtenissen door de JSON-documenten te spiegelen in een gerealiseerde weergave:

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=CFPCallback)]

1. Laat de code 5-10 seconden lopen Ga vervolgens terug naar de Azure Portal-gegevensverkenner en navigeer naar **InventoryContainer > items**. U ziet dat de items worden ingevoegd in de inventariscontainer. Let op de partitiesleutel (```id```).

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_items.JPG" alt-text="Feedcontainer":::

1. Navigeer nu in Data Explorer naar **InventoryContainer-pktype > items**. Dit is de gerealiseerde weergave. De items in deze container weergeven **InventoryContainer**, omdat ze programmatisch zijn ingevoegd door de wijzigingenfeed. Let op de partitiesleutel (```type```). Deze gerealiseerde weergave is geoptimaliseerd voor queryfilters in ```type```, dat in **InventoryContainer** inefficiënt zou zijn, omdat die is gepartitioneerd in ```id```.

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG" alt-text="Scherm afbeelding toont de Data Explorer-pagina voor een Azure Cosmos D B-account waarvoor items zijn geselecteerd.":::

1. We verwijderen een document uit zowel **InventoryContainer** als **InventoryContainer-pktype** met alleen de aanroep ```upsertItem()```. Bekijk eerste de Azure Portal-gegevensverkenner. We verwijderen het document voor ```/type == "plums"```. Deze is hieronder rood omcirkelt

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG" alt-text="Scherm afbeelding toont de Data Explorer-pagina voor een Azure Cosmos D B-account met een bepaald item dat u hebt geselecteerd.":::

    Druk opnieuw op enter om de functie ```deleteDocument()``` in de voorbeeldcode aan te roepen. Deze functie die hieronder wordt weergegeven, voegt een nieuwe versie van het document in met ```/ttl == 5```, dat de TTL (time-to-live) van het document instelt op 5 seconden. 
    
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=DeleteWithTTL)]

    De wijzigingenfeed ```feedPollDelay``` is ingesteld op 100 ms. Daarom reageert de wijzigingenfeed bijna onmiddellijk op deze update en wordt ```updateInventoryTypeMaterializedView()``` hierboven aangeroepen. Die laatste functieaanroep voegt het nieuwe document met een TTL van 5 seconden in **InventoryContainer-pktype** in.

    Het resultaat is dat na 5 seconden het document verloopt en uit beide containers wordt verwijderd.

    Deze procedure is nodig, omdat de wijzigingenfeed alleen gebeurtenissen weergeeft wanneer items worden ingevoegd of bijgewerkt, niet wanneer deze worden verwijderd.

1. Druk nogmaals op enter om het programma te sluiten en de resources ervan te wissen.
