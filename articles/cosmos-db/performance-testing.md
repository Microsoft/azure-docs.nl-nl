---
title: Prestaties en schaal testen met Azure Cosmos DB
description: Meer informatie over het testen van schalen en prestaties met Azure Cosmos DB. U kunt vervolgens de functionaliteit van Azure Cosmos DB evalueren voor scenario's met een hoge prestatie toepassing.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: cb0dd8eb879c5511da3c79a3dee6a715fb1275e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97358636"
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Prestaties en schaal testen met Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Prestaties en schaal testen is een belang rijke stap bij het ontwikkelen van toepassingen. Voor veel toepassingen heeft de databaserol een grote invloed op de algehele prestaties en schaal baarheid. Daarom is het een essentieel onderdeel van prestatie testen. [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) is een doel dat is gebaseerd op Elastic Scale en voorspel bare prestaties. Deze mogelijkheden maken het prima voor toepassingen die behoefte hebben aan een hoogwaardige database laag. 

Dit artikel bevat een Naslag informatie voor ontwikkel aars die prestatie test suites implementeren voor hun Azure Cosmos DB werk belastingen. Het kan ook worden gebruikt om Azure Cosmos DB te evalueren voor scenario's met hoge prestatie toepassingen. Het is voornamelijk gericht op geïsoleerde prestatie tests van de data base, maar bevat ook aanbevolen procedures voor productie toepassingen.

Na het lezen van dit artikel kunt u de volgende vragen beantwoorden: 

* Waar vind ik een voor beeld van een .NET-client toepassing voor het testen van de prestaties van Azure Cosmos DB? 
* Hoe kan ik worden hoge doorvoer niveaus gerealiseerd met Azure Cosmos DB van mijn client toepassing?

Als u aan de slag wilt gaan met code, downloadt u het project van [Azure Cosmos DB voor beeld van prestatie testen](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> Het doel van deze toepassing is te demonstreren hoe u de beste prestaties kunt verkrijgen van Azure Cosmos DB met een klein aantal client computers. Het doel van het voor beeld is dat de piek doorvoer capaciteit van Azure Cosmos DB (dat kan worden geschaald zonder limieten) niet wordt bereikt.

Als u op zoek bent naar client-side configuratie opties voor het verbeteren van de prestaties van Azure Cosmos DB, raadpleegt u [Azure Cosmos DB prestatie tips](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>De prestatie test toepassing uitvoeren
De snelste manier om aan de slag te gaan is het compileren en uitvoeren van het .NET-voor beeld, zoals beschreven in de volgende stappen. U kunt ook de bron code controleren en vergelijk bare configuraties implementeren op uw eigen client toepassingen.

**Stap 1:** Down load het project van [Azure Cosmos db test voorbeeld voor prestaties](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/documentdb-benchmark)of Splits de GitHub-opslag plaats.

**Stap 2:** Wijzig de instellingen voor EndpointUrl, AuthorizationKey, CollectionThroughput en DocumentTemplate (optioneel) in App.config.

> [!NOTE]
> Voordat u verzamelingen met hoge door Voer inrichten, raadpleegt u de [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) voor het schatten van de kosten per verzameling. Azure Cosmos DB opslag en door Voer onafhankelijk van de kosten per uur. U kunt kosten besparen door de door Voer van uw Azure Cosmos-containers na het testen te verwijderen of te verlagen.
> 
> 

**Stap 3:** Compileer de console-app en voer deze uit vanaf de opdracht regel. Als het goed is, wordt ongeveer de volgende uitvoer weergegeven:

```bash
C:\Users\cosmosdb\Desktop\Benchmark>DocumentDBBenchmark.exe
Summary:
---------------------------------------------------------------------
Endpoint: https://arramacquerymetrics.documents.azure.com:443/
Collection : db.data at 100000 request units per second
Document Template*: Player.json
Degree of parallelism*: -1
---------------------------------------------------------------------
DocumentDBBenchmark starting...
Found collection data with 100000 RU/s
Starting Inserts with 100 tasks
Inserted 4503 docs @ 4491 writes/s, 47070 RU/s (122B max monthly 1KB reads)
Inserted 17910 docs @ 8862 writes/s, 92878 RU/s (241B max monthly 1KB reads)
Inserted 32339 docs @ 10531 writes/s, 110366 RU/s (286B max monthly 1KB reads)
Inserted 47848 docs @ 11675 writes/s, 122357 RU/s (317B max monthly 1KB reads)
Inserted 58857 docs @ 11545 writes/s, 120992 RU/s (314B max monthly 1KB reads)
Inserted 69547 docs @ 11378 writes/s, 119237 RU/s (309B max monthly 1KB reads)
Inserted 80687 docs @ 11345 writes/s, 118896 RU/s (308B max monthly 1KB reads)
Inserted 91455 docs @ 11272 writes/s, 118131 RU/s (306B max monthly 1KB reads)
Inserted 102129 docs @ 11208 writes/s, 117461 RU/s (304B max monthly 1KB reads)
Inserted 112444 docs @ 11120 writes/s, 116538 RU/s (302B max monthly 1KB reads)
Inserted 122927 docs @ 11063 writes/s, 115936 RU/s (301B max monthly 1KB reads)
Inserted 133157 docs @ 10993 writes/s, 115208 RU/s (299B max monthly 1KB reads)
Inserted 144078 docs @ 10988 writes/s, 115159 RU/s (298B max monthly 1KB reads)
Inserted 155415 docs @ 11013 writes/s, 115415 RU/s (299B max monthly 1KB reads)
Inserted 166126 docs @ 10992 writes/s, 115198 RU/s (299B max monthly 1KB reads)
Inserted 173051 docs @ 10739 writes/s, 112544 RU/s (292B max monthly 1KB reads)
Inserted 180169 docs @ 10527 writes/s, 110324 RU/s (286B max monthly 1KB reads)
Inserted 192469 docs @ 10616 writes/s, 111256 RU/s (288B max monthly 1KB reads)
Inserted 199107 docs @ 10406 writes/s, 109054 RU/s (283B max monthly 1KB reads)
Inserted 200000 docs @ 9930 writes/s, 104065 RU/s (270B max monthly 1KB reads)

Summary:
---------------------------------------------------------------------
Inserted 200000 docs @ 9928 writes/s, 104063 RU/s (270B max monthly 1KB reads)
---------------------------------------------------------------------
DocumentDBBenchmark completed successfully.
Press any key to exit...
```

**Stap 4 (indien nodig):** De door Voer gerapporteerd (RU/s) van het hulp programma moet gelijk zijn aan of hoger zijn dan de ingerichte door Voer van de verzameling of een set verzamelingen. Als dat niet het geval is, kan het verhogen van de DegreeOfParallelism in kleine stappen helpen u de limiet te bereiken. Als de door Voer van uw client-app verwerkt, start u meerdere exemplaren van de app op extra client computers. Als u hulp nodig hebt bij dit stap bestand, moet u een ondersteunings ticket van de [Azure Portal](https://portal.azure.com).

Nadat u de app hebt uitgevoerd, kunt u verschillende [indexerings beleidsregels](index-policy.md) en [consistentie niveaus](consistency-levels.md) uitproberen om inzicht te krijgen in de gevolgen voor de door Voer en latentie. U kunt ook de bron code controleren en vergelijk bare configuraties implementeren voor uw eigen test suites of productie toepassingen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u prestatie-en schaal tests kunt uitvoeren met Azure Cosmos DB met behulp van een .NET-console-app. Raadpleeg voor meer informatie de volgende artikelen:

* [Voor beeld van Azure Cosmos DB prestaties testen](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/documentdb-benchmark)
* [Client configuratie opties voor het verbeteren van de Azure Cosmos DB prestaties](performance-tips.md)
* [Partitioneren aan de server zijde in Azure Cosmos DB](partitioning-overview.md)


