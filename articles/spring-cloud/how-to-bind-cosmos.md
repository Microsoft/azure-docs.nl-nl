---
title: Een Azure Cosmos DB aan uw Azure Spring Cloud-toepassing verbinden
description: Meer informatie over het verbinden van Azure Cosmos DB met uw Azure Spring Cloud-toepassing
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: bf761bded38ffa975610c7a7f217e911704ae3bf
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227939"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Een Azure Cosmos DB verbinden met uw Azure Spring Cloud-toepassing

**Dit artikel is van toepassing op:** ✔️ Java

In plaats van uw Spring Boot-toepassingen handmatig te configureren, kunt u de geselecteerde Azure-services automatisch met uw toepassingen verbinden met behulp van Azure Spring Cloud. In dit artikel wordt beschreven hoe u uw toepassing verbindt met een Azure Cosmos DB-database.

Vereisten:

* Een geïmplementeerd Azure Spring Cloud-exemplaar. Volg onze [quickstart voor het implementeren via de Azure CLI](spring-cloud-quickstart.md) om aan de slag te gaan.
* Een Azure Cosmos DB-account met een minimaal machtigingsniveau van Inzender.

## <a name="bind-azure-cosmos-db"></a>Azure Cosmos DB verbinden

Azure Cosmos DB heeft vijf verschillende typen API's die ondersteuning bieden voor binding. De volgende procedure laat zien hoe u deze gebruikt:

1. Hiermee maakt u een Azure Cosmos DB-database. Raadpleeg de quickstart over [het maken van een database](../cosmos-db/create-cosmosdb-resources-portal.md) voor hulp. 

1. Noteer de naam van uw database. Voor deze procedure is de databasenaam **testdb**.

1. Voeg een van de volgende afhankelijkheden toe aan het bestand pom.xml van uw Azure Spring Cloud-toepassing. Kies de afhankelijkheid die geschikt is voor uw type API.

    * Type API: Core (SQL)

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
          <version>2.1.6</version>
      </dependency>
      ```

    * Type API: MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * Type API: Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * Type API: Gremlin (Graph)

      ```xml
      <dependency>
          <groupId>com.microsoft.spring.data.gremlin</groupId>
          <artifactId>spring-data-gremlin</artifactId>
          <version>2.1.7</version>
      </dependency>
      ```

    * Type API: Azure Table

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. Gebruik `az spring-cloud app update` om de huidige implementatie bij te werken of gebruik `az spring-cloud app deployment create` om een nieuwe implementatie te maken. Met deze opdrachten kunt u de toepassing met de nieuwe afhankelijkheid bijwerken of maken.

1. Ga naar de pagina van Azure Spring Cloud in de Azure-portal. Ga naar **Toepassingsdashboard** en selecteer de toepassing die u met Azure Cosmos DB wilt verbinden. Dit is dezelfde toepassing als die u in de vorige stap hebt bijgewerkt of geïmplementeerd.

1. Selecteer **Servicebinding** en selecteer de knop **Servicebinding maken**. Selecteer het volgende om het formulier in te vullen:
   * Voor het **Bindingstype** de waarde **Azure Cosmos DB**.
   * Het type API.
   * De naam van uw database.
   * Het Azure Cosmos DB-account.

    > [!NOTE]
    > Als u Cassandra gebruikt, gebruikt u een sleutelruimte voor de naam van de database.

1. Start de toepassing opnieuw door **Opnieuw opstarten** te selecteren op de toepassingspagina.

1. Om er zeker van te zijn dat de service correct is verbonden, selecteert u de naam van de binding en controleert u de gegevens. Het veld `property` moet er ongeveer als volgt uitzien:

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u meer geleerd over het verbinden van uw Azure Spring Cloud-toepassing met een Azure Cosmos DB-database. Zie [Verbinden met een Azure Cache voor Redis-cache](spring-cloud-howto-bind-redis.md) voor meer informatie over bindingsservices voor uw toepassing.