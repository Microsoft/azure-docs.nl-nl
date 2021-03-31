---
title: Aan de slag met Elastic Database-Hulpprogram Ma's
description: Basis uitleg van het onderdeel Elastic Database Hulpprogram Ma's van Azure SQL Database, met inbegrip van een eenvoudige voor beeld-app.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: anumjs
ms.author: anjangsh
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 74343b2f05bb4a59e475449c87524ff66cdd605d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919541"
---
# <a name="get-started-with-elastic-database-tools"></a>Aan de slag met Elastic Database-Hulpprogram Ma's
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dit document bevat een inleiding tot de ervaring voor ontwikkel aars voor de [client bibliotheek voor Elastic data base](elastic-database-client-library.md) door u te helpen een voor beeld-app uit te voeren. Met de voor beeld-app wordt een eenvoudige Shard-toepassing gemaakt en worden de belangrijkste functies van de functie Hulpprogram Ma's voor Elastic Database van Azure SQL Database verkend. Het richt zich op het gebruik van cases voor [Shard-toewijzings beheer](elastic-scale-shard-map-management.md), [gegevens afhankelijke route ring](elastic-scale-data-dependent-routing.md)en [query's met meerdere Shard](elastic-scale-multishard-querying.md). De client bibliotheek is beschikbaar voor .NET en in Java.

## <a name="elastic-database-tools-for-java"></a>Elastic Database-Hulpprogram Ma's voor Java

### <a name="prerequisites"></a>Vereisten

* Een JDK (Java Developer Kit), versie 1,8 of hoger
* [Maven](https://maven.apache.org/download.cgi)
* SQL Database of een lokale SQL Server instantie

### <a name="download-and-run-the-sample-app"></a>De voor beeld-app downloaden en uitvoeren

Ga als volgt te werk om de JAR-bestanden te maken en aan de slag te gaan met het voorbeeld project:

1. Kloon de [github-opslag plaats](https://github.com/Microsoft/elastic-db-tools-for-java) met de client bibliotheek, samen met de voor beeld-app.

2. Bewerk het _./sample/src/main/resources/resource.Properties_ -bestand om het volgende in te stellen:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. Als u het voorbeeld project wilt maken, voert u in de map _./sample_ de volgende opdracht uit:

    ```
    mvn install
    ```

4. Als u het voorbeeld project wilt starten, voert u in de map _./sample_ de volgende opdracht uit:

    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```

5. Voor meer informatie over de mogelijkheden van de client bibliotheek kunt u experimenteren met de verschillende opties. U kunt de code verkennen voor meer informatie over de implementatie van de voor beeld-app.

    ![Voortgang-java][5]

Gefeliciteerd U hebt uw eerste Shard-toepassing gemaakt en uitgevoerd met behulp van Elastic Database-Hulpprogram Ma's op Azure SQL Database. Gebruik Visual Studio of SQL Server Management Studio om verbinding te maken met uw data base en Bekijk een kort overzicht van de Shards waarin het voor beeld is gemaakt. U ziet nieuwe voor beelden van Shard-data bases en een Shard map Manager-Data Base die het voor beeld heeft gemaakt.

Voeg de volgende afhankelijkheden toe aan uw POM-bestand om de client bibliotheek toe te voegen aan uw eigen Maven-project:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>elastic-db-tools</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="elastic-database-tools-for-net"></a>Elastic Database-Hulpprogram Ma's voor .NET

### <a name="prerequisites"></a>Vereisten

* Visual Studio 2012 of hoger met C#. Down load een gratis versie van [Visual Studio down loads](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2,7 of hoger. Zie [Installing NuGet](https://docs.nuget.org/docs/start-here/installing-nuget)om de nieuwste versie te downloaden.

### <a name="download-and-run-the-sample-app"></a>De voor beeld-app downloaden en uitvoeren

Als u de bibliotheek wilt installeren, gaat u naar [micro soft. Azure. SqlDatabase. ElasticScale. client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). De-bibliotheek wordt geïnstalleerd met de voor beeld-app die wordt beschreven in de volgende sectie.

Voer de volgende stappen uit om het voor beeld te downloaden en uit te voeren:

1. Down load de [Hulpprogram ma's voor elastische data bases voor Azure SQL-voor beeld aan](https://github.com/Azure/elastic-db-tools)de slag. Decomprimeer het voor beeld naar een locatie die u kiest.

2. Als u een project wilt maken, opent u de oplossing *ElasticDatabaseTools. SLN* vanuit de map *Elastic-db-tools-Master* . 

3. Stel het *ElasticScaleStarterKit* -project in als het opstart project.

4. Open het *App.config* -bestand in het project *ElasticScaleStarterKit* . Volg vervolgens de instructies in het bestand om uw server naam en aanmeldings gegevens (gebruikers naam en wacht woord) toe te voegen.

5. Maak de toepassing en voer deze uit. Wanneer u hierom wordt gevraagd, schakelt u Visual Studio in om de NuGet-pakketten van de oplossing te herstellen. Met deze actie wordt de meest recente versie van de client bibliotheek voor Elastic data base gedownload van NuGet.

6. Voor meer informatie over de mogelijkheden van de client bibliotheek kunt u experimenteren met de verschillende opties. Let op de stappen die de toepassing uitvoert in de-console-uitvoer en u kunt de code op de achtergrond verkennen.

   ![Voortgang][4]

Gefeliciteerd U hebt uw eerste Shard-toepassing gemaakt en uitgevoerd met behulp van Elastic Database-Hulpprogram Ma's op SQL Database. Gebruik Visual Studio of SQL Server Management Studio om verbinding te maken met uw data base en Bekijk een kort overzicht van de Shards waarin het voor beeld is gemaakt. U ziet nieuwe voor beelden van Shard-data bases en een Shard map Manager-Data Base die het voor beeld heeft gemaakt.

> [!IMPORTANT]
> U wordt aangeraden altijd de nieuwste versie van Management Studio te gebruiken, zodat u gesynchroniseerd blijft met updates voor Azure en SQL Database. [SQL Server Management Studio bijwerken](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="key-pieces-of-the-code-sample"></a>Belangrijkste onderdelen van het code voorbeeld

* **Shards-en Shard-kaarten beheren**: de code illustreert hoe u kunt werken met Shards, bereiken en toewijzingen in het bestand *ShardManagementUtils. cs* . Zie [data bases schalen met Shard-toewijzings beheer](https://go.microsoft.com/?linkid=9862595)voor meer informatie.  

* **Gegevens afhankelijke route ring**: route ring van trans acties naar de juiste Shard wordt weer gegeven in het bestand *DataDependentRoutingSample. cs* . Zie [gegevens afhankelijke route ring](https://go.microsoft.com/?linkid=9862596)voor meer informatie.

* **Query's uitvoeren via meerdere Shards**: het uitvoeren van query's op Shards wordt geïllustreerd in het bestand *MultiShardQuerySample. cs* . Zie [multi-Shard query's](https://go.microsoft.com/?linkid=9862597)voor meer informatie.

* **Lege Shards toevoegen**: het iteratieve toevoegen van nieuwe lege Shards wordt uitgevoerd door de code in het bestand *CreateShardSample. cs* . Zie [data bases schalen met Shard-toewijzings beheer](https://go.microsoft.com/?linkid=9862595)voor meer informatie.

## <a name="other-elastic-scale-operations"></a>Andere Elastic Scale-bewerkingen

* **Een bestaande Shard splitsen**: de mogelijkheid om Shards te splitsen, wordt bepaald door het hulp programma voor splitsen en samen voegen. Zie [gegevens verplaatsen tussen uitgeschaalde Cloud databases](elastic-scale-overview-split-and-merge.md)voor meer informatie.

* Het samen voegen van **bestaande Shards**: Shard-samen voegingen worden ook uitgevoerd met behulp van de functie voor splitsen en samen voegen. Zie [gegevens verplaatsen tussen uitgeschaalde Cloud databases](elastic-scale-overview-split-and-merge.md)voor meer informatie.

## <a name="cost"></a>Kosten

De bibliotheek met Elastic Database Hulpprogram Ma's is gratis. Wanneer u Elastic Database-Hulpprogram Ma's gebruikt, zijn er geen extra kosten in rekening gebracht dan de kosten van uw Azure-gebruik.

De voorbeeld toepassing maakt bijvoorbeeld nieuwe data bases. De kosten van deze mogelijkheid zijn afhankelijk van de SQL Database versie die u kiest en het Azure-gebruik van uw toepassing.

Zie [SQL database prijs](https://azure.microsoft.com/pricing/details/sql-database/)informatie voor prijzen.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Elastic Database-Hulpprogram Ma's:

* Code voorbeelden:
  * Elastic Database-Hulpprogram Ma's ([.net](https://github.com/Azure/elastic-db-tools), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Elastic Database-Hulpprogram Ma's voor integratie met Azure SQL-Entity Framework](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Shard-elasticiteit op Script Center](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [aankondiging voor Elastic Scale](https://azure.microsoft.com/blog/20../../introducing-elastic-scale-preview-for-azure-sql-database/)
* Channel 9: [overzicht van video over Elastic Scale](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Discussie forum: [micro soft Q&een vraag pagina voor Azure SQL database](/answers/topics/azure-sql-database.html)
* Prestaties meten: [prestatie meter items voor Shard-toewijzings beheer](elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/elastic-scale-get-started/newProject.png
[2]: ./media/elastic-scale-get-started/click-online.png
[3]: ./media/elastic-scale-get-started/click-CSharp.png
[4]: ./media/elastic-scale-get-started/output2.png
[5]: ./media/elastic-scale-get-started/java-client-library.PNG