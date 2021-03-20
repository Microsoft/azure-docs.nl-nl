---
title: Kosten voor aanvraag eenheden voor Azure Cosmos DB als een sleutel waarde-archief
description: Meer informatie over de kosten voor aanvraag eenheden van Azure Cosmos DB voor eenvoudige schrijf-en lees bewerkingen wanneer deze worden gebruikt als sleutel/waarde-archief.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 9354ae0a22ef2e8ab4ee6a57563d3f3c4c8e4547
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339296"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB als sleutel waarde-archief – kosten overzicht
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB is een wereld wijd gedistribueerde, multi-model database service voor het bouwen van Maxi maal beschik bare toepassingen op grote schaal. Azure Cosmos DB standaard automatisch en efficiënt alle opgenomen gegevens worden geïndexeerd. Dit maakt snelle en consistente [SQL](./sql-query-getting-started.md) -query's (en [Java script](stored-procedures-triggers-udfs.md)) mogelijk voor de gegevens. 

In dit artikel worden de kosten van Azure Cosmos DB beschreven voor eenvoudige schrijf-en lees bewerkingen wanneer deze worden gebruikt als sleutel/waarde-archief. Schrijf bewerkingen zijn onder andere invoegen, vervangen, verwijderen en upsert van gegevens items. Naast het garanderen van een SLA van 99,999% Beschik baarheid voor alle accounts met meerdere regio's, biedt Azure Cosmos DB gegarandeerde <10-MS-latentie voor lees bewerkingen en voor de (geïndexeerde) schrijf bewerkingen in het 99e percentiel. 

## <a name="why-we-use-request-units-rus"></a>Waarom we aanvraag eenheden gebruiken (RUs)

Azure Cosmos DB prestaties zijn gebaseerd op de hoeveelheid ingerichte door Voer uitgedrukt in [aanvraag eenheden](request-units.md) (ru/s). Het inrichten bevindt zich in een tweede granulatie en wordt aangeschaft in RU/s ([niet worden verward met de facturering per uur](https://azure.microsoft.com/pricing/details/cosmos-db/)). RUs moet worden beschouwd als een logische abstractie (een valuta) waarmee het inrichten van de vereiste door Voer voor de toepassing wordt vereenvoudigd. Gebruikers hoeven geen onderscheid te maken tussen lees-en schrijf doorvoer. Het model voor één valuta van RUs maakt efficiency verbeteringen om de ingerichte capaciteit te delen tussen lees-en schrijf bewerkingen. Met dit model met ingerichte capaciteit kan de service een **voorspel bare en consistente door Voer, een gegarandeerde lage latentie en hoge Beschik baarheid** bieden. Ten slotte, terwijl RU-model wordt gebruikt voor de door Voer, heeft elke ingerichte RU ook een gedefinieerde hoeveelheid resources (bijvoorbeeld geheugen, kernen/CPU en IOPS).

Als wereld wijd gedistribueerd database systeem is Cosmos DB de enige Azure-service die voorziet in uitgebreide Sla's met betrekking tot latentie, door Voer, consistentie en hoge Beschik baarheid. De door u ingerichte door Voer wordt toegepast op elk van de regio's die zijn gekoppeld aan uw Cosmos-account. Voor lees bewerkingen biedt Cosmos DB meerdere, goed gedefinieerde [consistentie niveaus](consistency-levels.md) waaruit u kunt kiezen. 

In de volgende tabel ziet u het aantal dat is vereist om Lees-en schrijf bewerkingen uit te voeren op basis van een gegevens item met een grootte van 1 KB en 100 Kb's met standaard automatische indexering uitgeschakeld. 

|Item grootte|1 lezen|1 schrijven|
|-------------|------|-------|
|1 kB|1 RU|5 RUs|
|100 kB|Tien aanvraageenheden|50 RUs|

## <a name="cost-of-reads-and-writes"></a>Kosten van lees-en schrijf bewerkingen

Als u 1.000 RU/s inricht, is dit een bedrag van 3.600.000 RU/uur en kost het $0,08 voor het uur (in de VS en Europa). Voor een gegevens item van 1 KB, betekent dit dat u 3.600.000 Lees-of 720.000-schrijf bewerkingen (3.600.000 RU/5) kunt gebruiken met behulp van de ingerichte door voer. De kosten zijn genormaliseerd tot miljoen Lees-en schrijf bewerkingen en zijn $0,022/miljoen van Lees bewerkingen ($0,08/3,6) en $0.111/miljoen schrijf bewerkingen ($0,08/0,72). De kosten per miljoen worden mini maal, zoals in de onderstaande tabel wordt weer gegeven.

|Item grootte|Kosten van 1.000.000 Lees bewerkingen|Kosten van 1.000.000 schrijf bewerkingen|
|-------------|-------|--------|
|1 kB|$0,022|$0,111|
|100 kB|$0,222|$1,111|


Het meren deel van de basis-BLOB of het object slaat de services in rekening van $0,40 per miljoen Lees transactie en $5 per miljoen schrijf transactie. Als Cosmos DB optimaal wordt gebruikt, kan het tot 98% goed koper zijn dan deze andere oplossingen (voor 1 KB trans acties).

## <a name="next-steps"></a>Volgende stappen

* Gebruik [ru Calculator](https://cosmos.azure.com/capacitycalculator/) om een schatting te maken van de door Voer voor uw workloads.