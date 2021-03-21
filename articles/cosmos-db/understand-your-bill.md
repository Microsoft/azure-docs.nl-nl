---
title: Meer informatie over uw Azure Cosmos DB factuur
description: In dit artikel wordt uitgelegd hoe u uw Azure Cosmos DB factuur begrijpt met enkele voor beelden.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2020
ms.reviewer: sngun
ms.openlocfilehash: b0d27cb2f941db374a144cbd5c026444730e24e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98247485"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>Meer informatie over uw Azure Cosmos DB-factuur
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Als volledig beheerde Cloud-systeem eigen database service, Azure Cosmos DB vereenvoudigt de facturering door alleen de database bewerkingen en de gebruikte opslag ruimte te gebruiken. Er zijn geen aanvullende licentie kosten, hardware, kosten voor hulp programma of faciliteiten in vergelijking met on-premises of door IaaS gehoste alternatieven. Wanneer u rekening houdt met de mogelijkheden van meerdere regio's van Azure Cosmos DB, biedt de database service een aanzienlijke verlaging van de kosten in vergelijking met bestaande on-premises of IaaSe oplossingen.

- **Database bewerkingen**: de manier waarop u uw database bewerkingen in rekening brengt, is afhankelijk van het type Azure Cosmos-account dat u gebruikt.

  - **Ingerichte door Voer**: u wordt per uur gefactureerd voor de maximale ingerichte door Voer voor een bepaald uur, in stappen van 100 ru/s.
  - **Serverloos**: u wordt per uur gefactureerd voor de totale hoeveelheid aanvraag eenheden die door uw database bewerkingen worden verbruikt.

- **Opslag**: er wordt een vast bedrag in rekening gebracht voor de totale hoeveelheid opslag ruimte (in GB) die door uw gegevens en indexen voor een bepaald uur wordt verbruikt.

Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) voor de meest recente prijs informatie.

In dit artikel worden enkele voorbeelden gebruikt om inzicht te krijgen in de details van de maandelijkse factuur. De getallen in de voorbeelden kunnen afwijken wanneer voor uw Azure Cosmos-containers een andere hoeveelheid doorvoer is ingericht, wanneer ze meerdere regio’s bestrijken, of als ze worden uitgevoerd voor een andere periode dan een maand. In alle voor beelden in dit artikel wordt de factuur berekend op basis van de prijs informatie die wordt weer gegeven op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/).

> [!NOTE]
> Facturering is voor elk deel van een uur in de periode, geen 60 minuut. Alle voor beelden in dit document zijn gebaseerd op de prijs van een Azure Cosmos-account dat is geïmplementeerd in een niet-overheids regio in de Verenigde Staten. De prijzen en berekening variëren afhankelijk van de regio die u gebruikt. Zie de [pagina met prijzen voor Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) voor de meest recente prijs informatie.

## <a name="billing-examples"></a>Facturerings voorbeelden

### <a name="billing-example---provisioned-throughput-on-a-container-full-month"></a>Voor beeld van facturering: ingerichte door Voer voor een container (volledige maand)

* We gaan ervan uit dat u een door Voer van 1.000 RU/SEC op een container configureert en er 24 uur * 30 dagen voor de maand = 720 uur totaal bestaat.  

* 1.000 RU/SEC 10 eenheden van 100 RU/sec per uur voor elk uur dat de containers bestaan (1000/100 = 10). 

* 10 eenheden per uur vermenigvuldigen met de kosten van $0,008 (per 100 RU/sec per uur) = $0,08 per uur. 

* Vermenigvuldiging van $0,08 per uur met het aantal uren in de maand is gelijk aan $0,08 * 24 uur * 30 dagen = $57,60 voor de maand.  

* De totale maandelijkse factuur toont 7.200 eenheden (van 100 RUs), wat de kosten $57,60 kost.

### <a name="billing-example---provisioned-throughput-on-a-container-partial-month"></a>Voor beeld van facturering: ingerichte door Voer voor een container (gedeeltelijke maand)

* We gaan ervan uit dat we een container maken met een ingerichte door Voer van 2.500 RU/sec. De container bevindt zich 24 uur per maand (het is bijvoorbeeld 24 uur nadat u deze hebt gemaakt).  

* Vervolgens zien we 600 eenheden op de factuur (2.500 RU/SEC/100 RU/SEC/eenheid * 24 uur). De kosten zijn $4,80 (600 eenheden * $0.008/Unit).

* De totale factuur voor de maand is $4,80.

### <a name="billing-example---serverless-container"></a>Voor beeld van facturering-container zonder server

* We gaan ervan uit dat we een serverloze container maken. 

* In de loop van een maand geven we database aanvragen die een totaal van 500.000 aanvraag eenheden verbruiken. De kosten zijn $0,125 (500.000 * $0,25/miljoen).

* De totale factuur voor de maand is $0,125.

### <a name="billing-rate-if-storage-size-changes"></a>Facturerings tarief als de opslag grootte verandert

Opslag capaciteit wordt in rekening gebracht in eenheden van de maximale hoeveelheid opgeslagen gegevens per uur, in GB, gedurende een maandelijkse periode. Als u bijvoorbeeld 100 GB aan opslag hebt gebruikt voor de helft van de maand en 50 GB voor de tweede helft van de maand, wordt u in de maand gefactureerd voor een equivalent van 75 GB opslag ruimte.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Het facturerings tarief wanneer een container of een set containers minder dan een uur actief is

Er wordt een vast bedrag in rekening gebracht voor elk uur dat de container of data base bestaat, ongeacht het gebruik of als de container of data base minder dan een uur actief is. Als u bijvoorbeeld een container of Data Base maakt en deze vijf minuten later verwijdert, zal uw factuur één uur bevatten.

### <a name="billing-rate-when-provisioned-throughput-on-a-container-or-database-scales-updown"></a>Facturerings tarief wanneer de ingerichte door Voer voor een container of data base omhoog/omlaag wordt geschaald

Als u de ingerichte door Voer op 9:30 uur verhoogt van 400 RU/sec naar 1.000 RU/SEC en vervolgens een lagere ingerichte door Voer op 10:45 A.M. terugbrengt naar 400 RU/sec., worden er twee uur aan 1.000 RU/sec in rekening gebracht. 

Als u de ingerichte door Voer voor een container of een set containers op 9:30 uur verhoogt van 100-K RU/sec tot 200-K RU/sec. en vervolgens de ingerichte door Voer op 10:45 uur terug naar 100-K RU/sec., worden er twee uur aan 200 K RU/sec in rekening gebracht.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Voor beeld van facturering: meerdere containers, elk met een speciale ingerichte doorvoer modus

* Als u een Azure Cosmos-account in VS-Oost 2 maakt met twee containers met ingerichte door Voer van 500 RU/SEC en 700 RU per seconde, hebt u een totale ingerichte door Voer van 1.200 RU/sec.  

* Er worden 1200/100 * $0,008 = $0.096/uur in rekening gebracht. 

* Als uw door Voer is gewijzigd en u de capaciteit van elke container hebt verhoogd met 500 RU/sec, terwijl u ook een nieuwe onbeperkte container maakt met 20.000 RU/sec., is uw totale ingerichte capaciteit 22.200 RU/SEC (1.000 RU/sec. + 1.200 RU/sec. + 20, verbruik/seconde).  

* Uw factuur wordt dan gewijzigd in: $0,008 x 222 = $1.776/uur. 

* Als er in een maand van 720 uur (24 uur * 30 dagen) een ingerichte door Voer van 500 uur 1.200 RU/SEC is en voor de resterende 220 uur door Voer is 22.200 RU/sec, wordt uw maandelijkse factuur weer gegeven: 500 x $0.096/uur + 220 x $1.776/Hour = $438.72/maand.

:::image type="content" source="./media/understand-your-bill/bill-example1.png" alt-text="Voor beeld van toegewezen doorvoer facturering":::

### <a name="billing-example-containers-with-shared-provisioned-throughput-mode"></a>Facturerings voorbeeld: containers met gedeelde (ingerichte) doorvoer modus

* Als u een Azure Cosmos-account in VS-Oost 2 maakt met twee Azure Cosmos-data bases (met een set containers die de door Voer op database niveau delen) met de ingerichte door Voer van 50-K RU/SEC en 70-K RU/sec, hebt u een totale ingerichte door Voer van 120 K RU/sec.  

* Er worden 1200 x $0,008 = $9.60/uur in rekening gebracht. 

* Als uw door Voer is gewijzigd en u de ingerichte door Voer van elke Data Base hebt verhoogd door 10K RU/SEC voor elke Data Base, en u voegt een nieuwe container toe aan de eerste data base met de toegewezen doorvoer modus van 15-K RU/sec. de totale ingerichte capaciteit zou 155-K RU/SEC (60 K RU/sec. + 80 K RU/sec. + 15 K RU/sec.) zijn.  

* Uw factuur wordt dan gewijzigd in: 1.550 * $0,008 = $12.40/uur.  

* Als er in een maand van 720 uur een ingerichte door Voer van 300 uur 120-K RU/SEC is en voor de resterende 420 uur door Voer is 155-K RU/sec., wordt uw maandelijkse factuur weer gegeven: 300 x $9.60/uur + 420 x $12.40/Hour = $2.880 + $5.208 = $8088/maand. 

:::image type="content" source="./media/understand-your-bill/bill-example2.png" alt-text="Voor beeld van een facturering van gedeelde door Voer":::

## <a name="billing-examples-with-geo-replication"></a>Facturerings voorbeelden met geo-replicatie  

U kunt Azure-regio's overal ter wereld toevoegen aan of verwijderen uit uw Azure Cosmos-database account. De door u geconfigureerde doorvoer voor de verschillende Azure Cosmos-data bases en containers wordt gereserveerd in elk van de Azure-regio's die zijn gekoppeld aan uw Azure Cosmos-database account. Als de som van de ingerichte door Voer (RU/sec) die is geconfigureerd voor alle data bases en containers in uw Azure Cosmos-database account (ingericht per uur) is en het aantal Azure-regio's dat aan uw database account is gekoppeld, N is en de totale ingerichte door Voer voor een bepaald uur gelijk is aan T x N RU/sec. Ingerichte door Voer (enkele schrijf regio) kosten $0.008/uur per 100 RU/SEC en ingerichte door Voer met meerdere Beschrijf bare regio's (multi-regio writes config) kosten $0,016/per uur per 100 RU/SEC (Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/)). Met Azure Cosmos DB kunt u de gegevens uit een wille keurige regio lezen, ongeacht of er een enkele schrijf regio of meerdere schrijf regio's zijn.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Voor beeld van facturering: meerdere regio's Azure Cosmos-account, schrijf bewerkingen in één regio

We gaan ervan uit dat u een Azure Cosmos-container hebt in VS West. De container wordt gemaakt met een door Voer van 10.000 RU/SEC en u hebt 1 TB aan gegevens per maand opgeslagen. We gaan ervan uit dat u drie regio's (VS-Oost, Europa-noord en Azië-oost) toevoegt aan uw Azure Cosmos-account, elk met dezelfde opslag en door voer. Uw totale maandelijkse factuur wordt (ervan uitgaande in 30 dagen per maand). Uw factuur zou er als volgt uitzien: 

|**Item** |**Gebruik (maand)** |**Snelheid** |**Maandelijkse kosten** |
|---------|---------|---------|-------|
|Doorvoer factuur voor container in West-VS      | 10.000 RU/SEC * 24 * 30    |$0,008 per 100 RU/sec per uur   |$576|
|Doorvoer factuur voor drie extra regio's: VS-Oost, Europa-noord en Azië-oost       | 3 * 10.000 RU/SEC * 24 * 30    |$0,008 per 100 RU/sec per uur  |$1.728|
|Opslag factuur voor container in West-VS      | 250 GB    |$0,25/GB  |$62,50|
|Opslag factuur voor 3 extra regio's-VS-Oost, Europa-noord en Azië-oost      | 3 * 250 GB    |$0,25/GB  |$187,50|
|**Totaal**     |     |  |**$2.554**|

*Er wordt ook van uitgegaan dat u elke maand 100 GB aan gegevens van de container in West-US uitmaakt om gegevens te repliceren naar de VS-Oost, Europa-noord en Azië-oost. U wordt gefactureerd voor uitgaand verkeer volgens tarieven per gegevens overdracht.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Facturerings voorbeeld: meerdere regio's Azure Cosmos-account, meerdere regio's schrijven

We gaan ervan uit dat u een Azure Cosmos-container maakt in VS West. De container wordt gemaakt met een door Voer van 10.000 RU/SEC en u hebt 1 TB aan gegevens per maand opgeslagen. We gaan ervan uit dat u drie regio's toevoegt (VS-Oost, Europa-noord en Azië-oost), elk met dezelfde opslag en door Voer en u de mogelijkheid wilt geven om te schrijven naar de containers in alle regio's die zijn gekoppeld aan uw Azure Cosmos-account. De totale maandelijkse factuur wordt als volgt (indien 30 dagen per maand):

|**Item** |**Gebruik (maand)**|**Snelheid** |**Maandelijkse kosten** |
|---------|---------|---------|-------|
|Doorvoer factuur voor container in West-VS (alle regio's zijn schrijfbaar)       | 10.000 RU/SEC * 24 * 30    |$0,016 per 100 RU/sec per uur    |$1.152 |
|Doorvoer factuur voor drie extra regio's: VS-Oost, Europa-noord en Azië-oost (alle regio's zijn schrijfbaar)        | 3 * 10.000 RU/SEC * 24 * 30    |$0,016 per 100 RU/sec per uur   |$3.456 |
|Opslag factuur voor container in West-VS      | 250 GB    |$0,25/GB  |$62,50|
|Opslag factuur voor 3 extra regio's-VS-Oost, Europa-noord en Azië-oost      | 3 * 250 GB    |$0,25/GB  |$187,50|
|**Totaal**     |     |  |**$6.010**|

*Er wordt ook van uitgegaan dat u elke maand 100 GB aan gegevens van de container in West-US uitmaakt om gegevens te repliceren naar de VS-Oost, Europa-noord en Azië-oost. U wordt gefactureerd voor uitgaand verkeer volgens tarieven per gegevens overdracht.*

### <a name="billing-example-azure-cosmos-account-with-multi-region-writes-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Voor beeld van facturering: Azure Cosmos-account met meerdere regio's schrijf bewerkingen, data base-niveau doorvoer, inclusief toegewezen doorvoer modus voor sommige containers

Bekijk het volgende voor beeld, waarbij we een Azure Cosmos-account met meerdere regio's bevatten waarin alle regio's schrijfbaar zijn (configuratie van meerdere schrijf regio's). Ter vereenvoudiging gaan we ervan uit dat de opslag ruimte constant blijft en niet wordt gewijzigd en deze hier weglaat om het voor beeld eenvoudiger te houden. De ingerichte door Voer in de maand varieerde als volgt (ervan uitgaande dat er 30 dagen of 720 uur is): 

[0-100 uur]:  

* We hebben een Azure Cosmos-account gemaakt (VS-West, VS-Oost, Europa-noord), waarbij alle regio's beschrijfbaar zijn 

* We hebben een Data Base (D1) gemaakt met gedeelde door Voer 10.000 RU/SEC 

* We hebben een Data Base (D2) gemaakt met gedeelde door Voer 30-K RU/SEC en  

* Er is een container (C1) gemaakt met een speciale door Voer 20 K RU/sec. 

[101-200 uur]:  

* We hebben de data base (D1) omhoog geschaald tot 50 K RU/sec. 

* We hebben de data base (D2) omhoog geschaald tot 70 K RU/sec.  

* De container (C1) is verwijderd  

[201-300 uur]:  

* We hebben de container (C1) opnieuw gemaakt met de toegewezen door Voer 20 K RU/sec. 

[301-400 uur]:  

* Er is een van de regio's uit het Azure Cosmos-account verwijderd (aantal Beschrijf bare regio's is nu 2) 

* We hebben de data base (D1) geschaald naar 10.000 RU/sec. 

* We hebben de data base (D2) omhoog geschaald tot 80 K RU/sec.  

* De container (C1) wordt opnieuw verwijderd 

[401-500 uur]:  

* We hebben de data base (D2) geschaald naar 10.000 RU/SEC  

* We hebben de container (C1) opnieuw gemaakt met de toegewezen door Voer 20 K RU/sec. 

[501-700 uur]:  

* We hebben de data base (D1) omhoog geschaald tot 20 K RU/sec.  

* We hebben de data base (D2) omhoog geschaald tot 100 K RU/sec.  

* De container (C1) wordt opnieuw verwijderd  

[701-720 uur]:  

* We hebben de data base (D2) omlaag geschaald tot 50 K RU/sec.  

In de onderstaande afbeelding ziet u de wijzigingen in de totale ingerichte door Voer gedurende 720 uur voor de maand. 

:::image type="content" source="./media/understand-your-bill/bill-example3.png" alt-text="Voor beeld van Real-Life":::

De totale maandelijkse factuur (ervan uitgaande tot 30 dagen/720 uur per maand) wordt als volgt berekend:

|**Hours**  |**RU/s** |**Item** |**Gebruik (elk uur)** |**Kosten** |
|---------|---------|---------|-------|-------|
|[0-100] |D1:10K <br/>D2:30K <br/>C1:20.000 |Doorvoer factuur voor container in West-VS (alle regio's zijn schrijfbaar)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$960  |
| | |Doorvoer factuur voor twee extra regio's: VS-Oost, Europa-noord (alle regio's zijn schrijfbaar)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2.880  |
|[101-200] |D1:50.000 <br/>D2:70K <br/>C1:-- |Doorvoer factuur voor container in West-VS (alle regio's zijn schrijfbaar)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |$1920  |
| | |Doorvoer factuur voor twee extra regio's: VS-Oost, Europa-noord (alle regio's zijn schrijfbaar)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |$5.760  |
|[201-300]  |D1:50.000 <br/>D2:70K <br/>C1:20.000 |Doorvoer factuur voor container in West-VS (alle regio's zijn schrijfbaar)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$2.240  |
| | |Doorvoer factuur voor twee extra regio's: VS-Oost, Europa-noord (alle regio's zijn schrijfbaar)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |$6.720 |
|[301-400] |D1:10K <br/>D2:80K <br/>C1:-- |Doorvoer factuur voor container in West-VS (alle regio's zijn schrijfbaar)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |$1.440   |
| | |Doorvoer factuur voor twee extra regio's: VS-Oost, Europa-noord (alle regio's zijn schrijfbaar)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2.880  |
|[401-500] |D1:10K <br>D2:10K <br>C1:20.000 |Doorvoer factuur voor container in West-VS (alle regio's zijn schrijfbaar)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$640  |
| | |Doorvoer factuur voor twee extra regio's: VS-Oost, Europa-noord (alle regio's zijn schrijfbaar)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |$1.280  |
|[501-700] |D1:20.000 <br>D2:100.000 <br>C1:-- |Doorvoer factuur voor container in West-VS (alle regio's zijn schrijfbaar)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |$3.840  |
| | |Doorvoer factuur voor twee extra regio's: VS-Oost, Europa-noord (alle regio's zijn schrijfbaar)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |$7.680  |
|[701-720] |D1:20.000 <br/>D2:50.000 <br/>C1:-- |Doorvoer factuur voor container in West-VS (alle regio's zijn schrijfbaar)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |$224  |
| | |Doorvoer factuur voor twee extra regio's: VS-Oost, Europa-noord (alle regio's zijn schrijfbaar)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |$224  |
|| |**Totale maandelijkse kosten**  | |**$38.688**   |

## <a name="billing-examples-with-azure-cosmos-db-free-tier-accounts"></a><a id="azure-free-tier"></a>Facturerings voorbeelden met Azure Cosmos DB-accounts voor een gratis laag

Met Azure Cosmos DB gratis laag krijgt u de eerste 400 RU/s en 5 GB aan opslag ruimte in uw account gratis, toegepast op het niveau van de account. Voor elke RU/s en opslag van meer dan 400 RU/s en 5 GB worden de reguliere prijs tarieven per de pagina met prijzen in rekening gebracht. Op de factuur worden geen kosten of een regel item weer geven voor de gratis 400 ru/s en 5 GB, alleen de RU/s en opslag buiten wat wordt gedekt door de gratis laag. De 400 RU/s is van toepassing op alle typen RU/s-ingerichte door Voer, automatisch schalen en meerdere regio's.  

> [!NOTE]
> Azure Cosmos DB gratis laag wijkt af van het gratis Azure-account. Het gratis Azure-account biedt Azure-tegoeden en-bronnen gratis gedurende een beperkte periode. Wanneer u Azure Cosmos DB gebruikt als onderdeel van dit gratis account, krijgt u 25 GB opslag ruimte en 400 RU/s aan ingerichte door Voer gedurende 12 maanden.

### <a name="billing-example---container-or-database-with-provisioned-throughput"></a>Voor beeld van facturering: container of Data Base met ingerichte door Voer
- Stel dat we een Data Base of container maken in een gratis laag account met 400 RU/s en 5 GB aan opslag ruimte.
- In uw factuur worden geen kosten voor deze resource weer gegeven. De kosten voor elk uur en maandelijks worden $0.
- Nu gaan we in hetzelfde account een andere data base of container toevoegen met 1000 RU/s en 10 GB aan opslag ruimte.
- Uw factuur toont nu kosten voor de 1000 RU/s en 10 GB aan opslag ruimte. 

### <a name="billing-example---container-with-autoscale-throughput"></a>Voor beeld van facturering-container met automatisch schalen door Voer
- Stel dat u een gratis laag account hebt en dat er een container wordt gemaakt waarvoor automatisch schalen is ingeschakeld, met een maximum van RU/s 4000 RU/s. Deze resource wordt automatisch geschaald tussen 400 RU/s-4000 RU/s. 
- Stel dat in uur 1 tot en met uur 10 de resource ten minste 400 RU/s is. Tijdens het uur 11 wordt de resource geschaald tot 1000 RU/s en wordt deze in het hele uur opnieuw ingesteld op 400 RU/s.
- In uur 1 tot en met 10 wordt u gefactureerd $0 voor door Voer, omdat de 400 RU/s onder de gratis laag vallen. 
- In uur 11 wordt u gefactureerd voor een effectief 1000 RU/s-400 RU/s = 600 RU/s, omdat dit de hoogste RU/s in het uur is. Dit zijn zes eenheden van 100 RU/s voor het uur, waardoor de totale doorvoer kosten voor het uur 6 eenheden * $0,012 = $0,072 zijn. 
- Alle opslag die zich buiten de eerste 5 GB bevindt, wordt gefactureerd tegen normale opslag tarieven. 

### <a name="billing-example---multi-region-single-write-region-account"></a>Voor beeld van facturering: meerdere regio's account voor één schrijf regio
- Stel dat u in een gratis laag account een Data Base of container maakt met 1200 RU/s en 10 GB aan opslag ruimte. Het account wordt gerepliceerd naar drie regio's en er is één schrijf regio-account.
- In totaal, zonder gratis laag, worden er kosten in rekening gebracht voor 3 * 1200 RU/s = 3600 RU/s en 3 * 10 GB = 30 GB aan opslag ruimte.
- Na het verwijderen van 400 RU/s en 5 GB aan opslag worden er kosten in rekening gebracht voor een effectief aantal 3200 RU/s (32 eenheden) van ingerichte door Voer op het tarief voor de afzonderlijke schrijf regio's en 25 GB aan opslag ruimte.
- De maandelijkse kosten voor RU/s zijn: 32 eenheden * $0,008 * 24 uur * 31 dagen = $190,46. De maandelijkse kosten voor opslag zijn: 25 GB * 0,25/GB = $6,25. De totale kosten zijn $190,46 + $6,25 = $196,71.
- Opmerking: als de eenheids prijs voor RU/s of opslag verschilt in de regio's, worden in de gratis laag 400 RU/s en 5 GB de tarieven weer gegeven van de regio waarin het account is gemaakt.

### <a name="billing-example---multi-region-account-with-multiple-write-regions"></a>Voor beeld van facturering: meerdere regio's, account met meerdere schrijf regio's

In dit voor beeld worden de [prijzen voor meerdere regio's geschreven](https://azure.microsoft.com/pricing/details/cosmos-db/) voor accounts die zijn gemaakt na 1 december 2019. 
- Stel dat u in een gratis laag account een Data Base of container maakt met 1200 RU/s en 10 GB aan opslag ruimte. Het account wordt gerepliceerd naar drie regio's en er is een account met meerdere schrijf regio's. 
- In totaal, zonder gratis laag, worden er kosten in rekening gebracht voor 3 * 1200 RU/s = 3600 RU/s en 3 * 10 GB = 30 GB aan opslag ruimte.
- Na het verwijderen van 400 RU/s en 5 GB aan opslag worden er kosten in rekening gebracht voor een effectief aantal 3200 RU/s (32 eenheden) van ingerichte door Voer op het niveau van meerdere schrijf regio's en 25 GB aan opslag ruimte.
- De maandelijkse kosten voor RU/s zijn: 32 eenheden * $0,016 * 24 uur * 31 dagen = $380,93. De maandelijkse kosten voor opslag zijn: 25 GB * 0,25/GB = $6,25. De totale kosten zijn $380,93 + $6,25 = $387,18.

## <a name="proactively-estimating-your-monthly-bill"></a>Proactief schatten van uw maandelijkse factuur  

Laten we eens kijken naar een ander voor beeld, waar u proactief uw factuur kunt inschatten vóór het einde van de maand. U kunt uw factuur als volgt schatten:

**Opslagkosten**

* Gem. grootte record (KB) = 1 
* Aantal records = 100.000.000 
* Totale opslag (GB) = 100 
* Maandelijkse kosten per GB = $0,25 
* Verwachte maandelijkse kosten voor opslag = $25,00 

**Doorvoer kosten**

|Het type bewerking| Aanvragen per seconde| Gem. RU/aanvraag| RUs vereist|
|----|----|----|----|
|Schrijven| 100 | 5 | 500|
|Lezen| 400| 1| 400|

Totaal aantal RU/SEC: 500 + 400 = 900 uur kosten: 900/100 * $0,008 = $0,072 verwachte maandelijkse kosten voor door Voer (aangenomen 31 dagen): $0,072 * 24 * 31 = $53,57

**Totale maandelijkse kosten**

Totale maandelijkse kosten = maandelijkse kosten voor opslag en maandelijkse kosten voor het totale aantal maandelijkse kosten voor door Voer = $25,00 + $53,57 = $78,57

*Prijzen kunnen per regio verschillen. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/)voor actuele prijzen.*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Facturering met Azure Cosmos DB gereserveerde capaciteit

Azure Cosmos DB gereserveerde capaciteit biedt u de mogelijkheid om vooraf ingerichte door voer te kopen (een gereserveerde capaciteit of een reserve ring) die kan worden toegepast op alle Azure Cosmos-data bases en-containers (voor elke API of elk gegevens model) in alle Azure-regio's. Omdat de ingerichte doorvoer prijs per regio verschilt, is het handiger om gereserveerde capaciteit te beschouwen als een monetair tegoed dat u bij een korting hebt aangeschaft en dat kan worden opgehaald uit de ingerichte door Voer voor de desbetreffende prijs in elke regio. Stel bijvoorbeeld dat u een Azure Cosmos-account hebt met één container die is ingericht met 50-K RU/SEC en wereld wijd gerepliceerde twee regio's, VS-Oost en Japan-Oost. Als u kiest voor de optie betalen naar gebruik, betaalt u het volgende:  

* in VS-Oost: voor 50-K RU/seconde tegen $0,008 per 100 RU/sec in die regio 

* in Japan-Oost: voor 50-K RU/sec. met een snelheid van $0,009 per 100 RU/sec in die regio

Uw totale factuur (zonder gereserveerde capaciteit) zou worden (waarbij 30 dagen of 720 uur wordt uitgegaan): 

|**Regio**| **Uurtarief per uur op basis van 100 RU/s**|**Eenheden (RU/s)**|**Gefactureerd bedrag (per uur)**| **Gefactureerd bedrag (maandelijks)**|
|----|----|----|----|----|
|VS - oost|$0,008 |50 K|$4|$2.880 |
|Japan - oost|$0,009 |50 K| $4,50 |$3.240 |
|Totaal|||$8,50|$6.120 |

U kunt in plaats daarvan een gereserveerde capaciteit hebben gekocht. U kunt gereserveerde capaciteit voor 100-K RU/seconde kopen tegen de prijs van $56.064 gedurende één jaar (bij 20% korting) of $6,40 per uur. Bekijk de prijzen voor gereserveerde capaciteit op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/).  

* Kosten van door Voer (betalen per gebruik): 100.000 RU/SEC/100 * $0.008/uur * 8760 uur in een jaar = $70.080 

* Kosten van door Voer (met gereserveerde capaciteit) $70.080 gedisconteerd op 20% = $56.064 

Wat u effectief hebt aangeschaft, is een tegoed van $8 per uur, voor 100 K RU per seconde, met behulp van de lijst prijs in VS-Oost, tegen de prijs van $6,40 per uur. U kunt vervolgens op elk uur aftrekken van deze vooraf betaalde door Voer reserve ring voor de ingerichte doorvoer capaciteit in een wereld wijde Azure-regio op basis van de regionale lijst prijzen die zijn ingesteld voor uw abonnement. In dit voor beeld, waarin u 50 K RU/sec. hebt ingericht in VS-Oost en Japan-Oost, kunt u $8,00 waard van ingerichte door Voer per uur opstellen en wordt de overschrijding van $0,50 per uur (of $360/maand) gefactureerd. 

|**Regio**| **Uurtarief per uur op basis van 100 RU/s**|**Eenheden (RU/s)**| **Gefactureerd bedrag (per uur)**| **Gefactureerd bedrag (maandelijks)**|
|----|----|----|----|----|
|VS - oost|$0,008 |50 K|$4|$2.880 |
|Japan - oost|$0,009 |50 K| $4,50 |$3.240 |
|||Betalen naar gebruik|$8,50|$6120|
|Aangeschafte gereserveerde capaciteit|$0,0064 (20% korting) |100 RU/sec of $8 capaciteit vooraf aangeschaft |-$8|-$5.760 |
|Netfactuur|||$ 0,50 |$360 |

## <a name="next-steps"></a>Volgende stappen

U kunt nu door gaan met de volgende artikelen voor meer informatie over cost Optimization in Azure Cosmos DB:

* Meer informatie over [hoe Cosmos DB prijs model rendabel is voor klanten](total-cost-ownership.md)
* Meer informatie over het [optimaliseren voor ontwikkeling en testen](optimize-dev-test.md)
* Meer informatie over het [optimaliseren van doorvoer kosten](optimize-cost-throughput.md)
* Meer informatie over het [optimaliseren van opslag kosten](optimize-cost-storage.md)
* Meer informatie over [het optimaliseren van de kosten van lees-en schrijf bewerkingen](optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten van query's](./optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten voor Azure Cosmos-accounts met meerdere regio's](optimize-cost-regions.md)
