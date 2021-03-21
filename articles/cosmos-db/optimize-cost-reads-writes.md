---
title: De kosten van uw aanvragen in Azure Cosmos DB optimaliseren
description: In dit artikel wordt uitgelegd hoe u kosten optimaliseert bij het uitgeven van aanvragen op Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 36ecef007e10f9a090dbabc8b5a91fd473930141
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102633849"
---
# <a name="optimize-request-cost-in-azure-cosmos-db"></a>De aanvraag kosten optimaliseren in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

In dit artikel wordt beschreven hoe lees-en schrijf aanvragen vertalen naar [aanvraag eenheden](request-units.md) en hoe u de kosten van deze aanvragen optimaliseert. Lees bewerkingen zijn onder andere punt lezen en query's. Schrijf bewerkingen zijn onder andere invoegen, vervangen, verwijderen en upsert van items.

Azure Cosmos DB biedt een uitgebreide set database bewerkingen die worden uitgevoerd op de items in een container. De kosten die gepaard gaan met elke bewerking hangen af van de CPU, de IO en het geheugen, vereist om de bewerking uit te voeren. In plaats van te denken over en het beheren van hardwarebronnen, kunt u een aanvraag eenheid (RU) beschouwen als een enkele maat eenheid voor de resources die nodig zijn om verschillende database bewerkingen uit te voeren om een aanvraag te behandelen.

## <a name="measuring-the-ru-charge-of-a-request"></a>De RU-kosten van een aanvraag meten

Het is belang rijk om de RU-kosten van uw aanvragen te meten om inzicht te krijgen in de werkelijke kosten en ook de effectiviteit van uw optimalisaties te evalueren. U kunt deze kosten ophalen met behulp van de Azure Portal of inspecteren van Azure Cosmos DB via een van de Sdk's. Zie [de kosten voor aanvraag eenheden zoeken in azure Cosmos DB](find-request-unit-charge.md) voor gedetailleerde instructies voor het verkrijgen van dat.

## <a name="reading-data-point-reads-and-queries"></a>Gegevens lezen: Lees bewerkingen en query's

Lees bewerkingen in Azure Cosmos DB meestal worden besteld van snelste/meest efficiënte naar langzamer/minder efficiënte manier van het gebruik van RU als volgt:  

* Punt lezen (sleutel/waarde zoeken op één item-ID en partitie sleutel).
* Query met een filter component binnen één partitie sleutel.
* Query zonder een gelijkheids-of bereik filter component voor een eigenschap.
* Query zonder filters.

### <a name="role-of-the-consistency-level"></a>De rol van het consistentie niveau

Bij het gebruik van de **sterke** of **gebonden** [consistentie niveaus](consistency-levels.md)voor veroudering, wordt de ru-kosten van elke Lees bewerking (punt lezen of query) verdubbeld.

### <a name="point-reads"></a>Punt lezen

De enige factor die van invloed is op de RU-kosten van een punt dat wordt gelezen (naast het gebruikte consistentie niveau) is de grootte van het opgehaalde item. In de volgende tabel ziet u de RU-kosten van punt Lees bewerkingen voor items die 1 KB en 100 KB groot zijn.

| **Item grootte** | **Kosten van één punt lezen** |
| --- | --- |
| 1 kB | 1 RU |
| 100 kB | Tien aanvraageenheden |

Omdat punt Lees bewerkingen (sleutel/waarde lookups voor de item-ID) het meest efficiënt zijn, moet u ervoor zorgen dat uw item-ID een zinvolle waarde heeft, zodat u uw items kunt ophalen met een punt dat kan worden gelezen (in plaats van een query).

### <a name="queries"></a>Query's

Aanvraag eenheden voor query's zijn afhankelijk van een aantal factoren. Bijvoorbeeld het aantal Azure Cosmos-items dat is geladen/geretourneerd, het aantal lookups op basis van de index, de compilatie tijd van de query etc. Details. Azure Cosmos DB garandeert dat bij het uitvoeren van dezelfde query altijd hetzelfde aantal aanvraag eenheden wordt gebruikt, zelfs bij herhalingen. Het query profiel met metrische gegevens voor query uitvoering geeft u een goed beeld van de manier waarop de aanvraag eenheden worden uitgegeven.  

In sommige gevallen ziet u mogelijk een reeks van 200-en 429-reacties en variabele-aanvraag eenheden in een wissel bare uitvoering van query's, dat wil zeggen dat query's zo snel mogelijk worden uitgevoerd op basis van het beschik bare RUs. Mogelijk wordt het uitvoeren van een query met meerdere pagina's of afrondingen tussen de server en de client weer geven. 10.000-items kunnen bijvoorbeeld worden geretourneerd als meerdere pagina's, waarbij elke kosten worden berekend op basis van de berekening die voor die pagina is uitgevoerd. Wanneer u de som van deze pagina's opneemt, moet u hetzelfde aantal RUs ophalen als voor de hele query.

#### <a name="metrics-for-troubleshooting-queries"></a>Metrische gegevens voor het oplossen van problemen

De prestaties en de door Voer die worden gebruikt door query's (inclusief door de gebruiker gedefinieerde functies) zijn voornamelijk afhankelijk van de functie hoofd tekst. De eenvoudigste manier om erachter te komen hoe lang de uitvoering van de query wordt uitgevoerd in de UDF en hoeveel RUs wordt verbruikt, is door de metrische gegevens van de query in te scha kelen. Als u de .NET SDK gebruikt, ziet u hier de metrische gegevens van de voorbeeld query die door de SDK worden geretourneerd:

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

#### <a name="best-practices-to-cost-optimize-queries"></a>Aanbevolen procedures voor het optimaliseren van query's 

Houd rekening met de volgende aanbevolen procedures bij het optimaliseren van query's voor kosten:

* **Meerdere entiteits typen selecteren**

   Probeer meerdere entiteits typen te verplaatsen binnen één of een kleiner aantal containers. Deze methode levert niet alleen voor delen op uit een prijs perspectief, maar ook voor het uitvoeren van query's en trans acties. Query's zijn binnen het bereik van één container; en atomische trans acties ten opzichte van meerdere records via opgeslagen procedures/triggers bevinden zich binnen één container op een partitie sleutel. Het verplaatsen van entiteiten binnen dezelfde container kan het aantal netwerk round trips voor het oplossen van relaties tussen records verminderen. Hierdoor worden de end-to-end-prestaties verhoogd, kunnen atomische trans acties worden uitgevoerd via meerdere records voor een grotere gegevensset, en als gevolg hiervan worden de kosten verlaagd. Als het verplaatsen van meerdere entiteits typen binnen één of een kleiner aantal containers moeilijk is voor uw scenario, meestal omdat u een bestaande toepassing migreert en u geen code wijzigingen wilt aanbrengen, moet u de door Voer inrichten op database niveau.  

* **Meten en afstemmen voor lagere aanvraag eenheden/tweede gebruik**

   De complexiteit van een query is van invloed op het aantal aanvraag eenheden (RUs) dat voor een bewerking wordt gebruikt. Het aantal predikaten, de aard van de predikaten, het aantal Udf's en de grootte van de bron gegevensverzameling. Al deze factoren beïnvloeden de kosten van de query bewerkingen. 

Azure Cosmos DB biedt voorspel bare prestaties in termen van door Voer en latentie met behulp van een ingericht doorvoer model. De ingerichte door Voer is weer gegeven in termen van [aanvraag eenheden](request-units.md) per seconde, of ru/s. Een aanvraag eenheid (RU) is een logische abstractie voor reken resources, zoals CPU, geheugen, IO, enzovoort, die vereist zijn om een aanvraag uit te voeren. De ingerichte door Voer (RUs) wordt gereserveerd en toegewezen aan uw container of Data Base om voorspel bare door Voer en latentie te bieden. Met ingerichte door Voer kunnen Azure Cosmos DB voorspel bare en consistente prestaties bieden, gegarandeerd lage latentie en hoge Beschik baarheid op elke schaal. Aanvraag eenheden vertegenwoordigen de genormaliseerde valuta die de reden vereenvoudigt van het aantal resources dat een toepassing nodig heeft.

In de aanvraag header worden de kosten van een bepaalde query geretourneerd. Als een query bijvoorbeeld 1000 1 KB-items retourneert, zijn de kosten van de bewerking 1000. Binnen één seconde voldoet de server slechts twee aanvragen voordat de volgende aanvragen worden beperkt. Zie het artikel [aanvraag eenheden](request-units.md) en de Calculator van de aanvraag eenheid voor meer informatie.

## <a name="writing-data"></a>Gegevens schrijven

De RU-kosten voor het schrijven van een item zijn afhankelijk van:

- De grootte van het item.
- Het aantal eigenschappen dat onder het [indexerings beleid](index-policy.md) valt en dat moet worden geïndexeerd.

Een 1 KB-item invoegen zonder kosten te indexeren rond ~ 5,5 RUs. Het vervangen van een item kost twee keer de kosten die nodig zijn om hetzelfde item in te voegen.

### <a name="optimizing-writes"></a>Schrijf bewerkingen optimaliseren

De beste manier om de RU-kosten van schrijf bewerkingen te optimaliseren, is door uw items te optimaliseren en het aantal eigenschappen dat wordt geïndexeerd.

- Het opslaan van zeer grote items in Azure Cosmos DB resulteert in hoge RU-kosten en kan worden beschouwd als een anti-patroon. Sla in het bijzonder geen binaire inhoud op of grote delen van tekst die u niet hoeft op te vragen. Een best practice is om dit soort gegevens in [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) te plaatsen en een verwijzing (of koppeling) naar de BLOB op te slaan in het item dat u naar Azure Cosmos DB schrijft.
- Als u het indexerings beleid optimaliseert, worden alleen de eigenschappen geïndexeerd waarmee uw query's filteren op een enorm verschil in het RUs dat door uw schrijf bewerkingen kan worden gebruikt. Bij het maken van een nieuwe container indexeert het standaard indexerings beleid elke eigenschap die in uw items is gevonden. Hoewel dit een goede standaard is voor ontwikkelings activiteiten, is het raadzaam om [het indexerings beleid](how-to-manage-indexing-policy.md) opnieuw te evalueren en aan te passen wanneer het gaat om productie of wanneer uw workload aanzienlijk verkeer ontvangt.

Wanneer gegevens bulksgewijs worden opgenomen, is het raadzaam om de [Azure Cosmos DB bulk](bulk-executor-overview.md) -uitvoerder bibliotheek te gebruiken, omdat het is ontworpen om het gebruik van deze bewerkingen te optimaliseren. U kunt eventueel ook [Azure Data Factory](../data-factory/introduction.md) gebruiken dat is gebaseerd op dezelfde bibliotheek.

## <a name="next-steps"></a>Volgende stappen

Daarna kunt u meer te weten komen over cost Optimization in Azure Cosmos DB met de volgende artikelen:

* Meer informatie over het [optimaliseren voor ontwikkeling en testen](optimize-dev-test.md)
* Meer informatie over [uw Azure Cosmos DB factuur](understand-your-bill.md)
* Meer informatie over het [optimaliseren van doorvoer kosten](optimize-cost-throughput.md)
* Meer informatie over het [optimaliseren van opslag kosten](optimize-cost-storage.md)
* Meer informatie over [het optimaliseren van de kosten voor Azure Cosmos-accounts met meerdere regio's](optimize-cost-regions.md)
* Meer informatie over [Azure Cosmos DB gereserveerde capaciteit](cosmos-db-reserved-capacity.md)
