---
title: Azure Data Lake Storage-query versnelling
description: Met de functie voor het versnellen van query's kunnen toepassingen en analyse raamwerken gegevens verwerking aanzienlijk optimaliseren door alleen de gegevens op te halen die nodig zijn voor een verwerkings bewerking.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 09/09/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: dc1d217dba64c36aa219abbd4d2220a494347689
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912753"
---
# <a name="azure-data-lake-storage-query-acceleration"></a>Azure Data Lake Storage-query versnelling

Met de functie voor het versnellen van query's kunnen toepassingen en analyse raamwerken gegevens verwerking aanzienlijk optimaliseren door alleen de gegevens op te halen die nodig zijn voor het uitvoeren van een bepaalde bewerking. Dit vermindert de tijd en verwerkings kracht die nodig is om essentiële inzichten in opgeslagen gegevens te krijgen.

## <a name="overview"></a>Overzicht

Query versnelling accepteert filter *predikaten* en *kolom projecties* waarmee toepassingen rijen en kolommen kunnen filteren op het moment dat gegevens van de schijf worden gelezen. Alleen de gegevens die aan de voor waarden van een predikaat voldoen, worden via het netwerk overgezet naar de toepassing. Hierdoor worden de netwerk latentie en de reken kosten verminderd.  

U kunt SQL gebruiken om de rij filter predikaten en kolom projectie op te geven in een aanvraag voor query versnelling. Met een aanvraag wordt slechts één bestand verwerkt. Daarom worden geavanceerde relationele functies van SQL, zoals samen voegingen en groeperen op aggregaties, niet ondersteund. Query versnelling ondersteunt gegevens in CSV-en JSON-indeling als invoer voor elke aanvraag.

De functie voor het versnellen van query's is niet beperkt tot Data Lake Storage (opslag accounts waarvoor de hiërarchische naam ruimte is ingeschakeld). De query versnelling is volledig compatibel met de blobs in opslag accounts **waarvoor geen** hiërarchische naam ruimte is ingeschakeld. Dit betekent dat u dezelfde verlaging van de netwerk latentie en de reken kosten kunt krijgen wanneer u gegevens verwerkt die u al hebt opgeslagen als blobs in opslag accounts.

Zie voor een voor beeld van het gebruik van de query versnelling in een client toepassing [gegevens filteren met behulp van Azure data Lake Storage-query versnelling](data-lake-storage-query-acceleration-how-to.md).

## <a name="data-flow"></a>Gegevensstroom

In het volgende diagram ziet u hoe een typische toepassing query versnelling gebruikt voor het verwerken van gegevens.

> [!div class="mx-imgBorder"]
> ![Overzicht van query versnelling](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. De client toepassing vraagt bestands gegevens aan door predikaten en kolom projecties op te geven.

2. Met de query versnelling wordt de opgegeven SQL-query geparseerd en wordt het werk gedistribueerd om gegevens te parseren en te filteren.

3. Processors lezen de gegevens van de schijf, parseert de gegevens met behulp van de juiste indeling en filtert vervolgens gegevens door de opgegeven predikaten en kolom projecties toe te passen.

4. Met de query versnelling wordt de reactie Shards gecombineerd om weer naar de client toepassing te streamen.

5. De client toepassing ontvangt en parseert het gestreamde antwoord. De toepassing hoeft geen extra gegevens te filteren en kan de gewenste berekening of trans formatie direct Toep assen.

## <a name="better-performance-at-a-lower-cost"></a>Betere prestaties tegen lagere kosten

De snelheid van query's optimaliseert de prestaties door de hoeveelheid gegevens die wordt overgedragen en verwerkt door uw toepassing te verminderen.

Als u een geaggregeerde waarde wilt berekenen **, haalt de** toepassing de gegevens meestal op uit een bestand en worden de gegevens vervolgens lokaal verwerkt en gefilterd. Een analyse van de invoer/uitvoer patronen voor analyse-workloads laat zien dat toepassingen doorgaans slechts 20% van de gegevens nodig hebben die ze hebben gelezen om een bepaalde berekening uit te voeren. Deze statistische waarde is waar, zelfs na het Toep assen van technieken zoals het [weghalen van partities](../../hdinsight/hdinsight-hadoop-optimize-hive-query.md#hive-partitioning). Dit betekent dat 80% van die gegevens onnodig over het netwerk worden overgedragen, geparseerd en gefilterd op toepassingen. Dit patroon, dat in feite is ontworpen voor het verwijderen van overbodige gegevens, leidt tot een aanzienlijke reken kosten.  

Hoewel Azure een toonaangevende netwerk bevat, zowel door Voer als latentie, is het onnodig van gegevens over dat netwerk nog steeds kostbaar voor de prestaties van toepassingen. Door de ongewenste gegevens tijdens de opslag aanvraag uit te filteren, elimineert de query versnelling deze kosten.

Daarnaast moet de toepassing, die nodig is voor het parseren en filteren van overbodige gegevens, een groter aantal en grotere Vm's inrichten om het werk te kunnen doen. Door deze Compute-belasting over te dragen aan de query versnelling kunnen toepassingen aanzienlijke kosten besparingen realiseren.

## <a name="applications-that-can-benefit-from-query-acceleration"></a>Toepassingen die kunnen profiteren van de versnelling van query's

Query versnelling is ontworpen voor gedistribueerde analyse frameworks en toepassingen voor gegevens verwerking. 

Gedistribueerde analyse frameworks, zoals Apache Spark en Apache Hive, bevatten een opslag abstractie-laag binnen het Framework. Deze engines bevatten ook zoek optimalisatie Programma's die kennis kunnen bevatten van de onderliggende I/O-service mogelijkheden bij het bepalen van een optimaal query plan voor gebruikers query's. Deze frameworks worden gestart om de query versnelling te integreren. Als gevolg hiervan zien gebruikers van deze frameworks betere latentie van query's en een lagere total cost of ownership zonder dat ze wijzigingen hoeven aan te brengen in de query's. 

Query versnelling is ook ontworpen voor toepassingen voor gegevens verwerking. Deze typen toepassingen voeren doorgaans grootschalige gegevens transformaties uit die mogelijk niet rechtstreeks leiden tot Analytics Insights, zodat ze niet altijd vastgelegde gedistribueerde analyse raamwerken gebruiken. Deze toepassingen hebben vaak een meer rechtstreekse relatie met de onderliggende opslag service zodat ze rechtstreeks kunnen profiteren van functies als query versnelling. 

Zie voor een voor beeld van hoe een toepassing query versnelling kan integreren, [gegevens filteren met behulp van Azure data Lake Storage-query versnelling](data-lake-storage-query-acceleration-how-to.md).

## <a name="pricing"></a>Prijzen

Als gevolg van de toegenomen reken belasting in de Azure Data Lake Storage-service verschilt het prijs model voor het gebruik van de query versnelling van het normale Azure Data Lake Storage transactie model. Met de query versnelling worden kosten in rekening gebracht voor de hoeveelheid gescande gegevens en de kosten voor de hoeveelheid gegevens die naar de oproepende functie wordt geretourneerd. Zie [Azure data Lake Storage Gen2 prijzen](https://azure.microsoft.com/pricing/details/storage/data-lake/)voor meer informatie.

Ondanks de wijziging van het facturerings model is het prijs model van de query versnelling ontworpen om de total cost of ownership voor een werk belasting te verlagen, gezien de minder dure VM-kosten.

## <a name="next-steps"></a>Volgende stappen

- [Gegevens filteren met behulp van Azure Data Lake Storage-query versnelling](data-lake-storage-query-acceleration-how-to.md)
- [Naslag informatie over SQL-taal voor query versnelling](query-acceleration-sql-reference.md)