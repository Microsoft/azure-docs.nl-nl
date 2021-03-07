---
title: De doorvoer kosten optimaliseren in Azure Cosmos DB
description: In dit artikel wordt uitgelegd hoe u doorvoer kosten optimaliseert voor de gegevens die zijn opgeslagen in Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 0c95fc9e416399b5c8fe032e0d3af0c3b7f9cf6e
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102433570"
---
# <a name="optimize-provisioned-throughput-cost-in-azure-cosmos-db"></a>Kosten voor ingerichte doorvoer optimaliseren in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Dankzij een ingericht doorvoer model biedt Azure Cosmos DB voorspel bare prestaties op elke schaal. Door de door Voer vooraf te leveren of in te richten, elimineert u het ' ruislijke neighbor-effect ' op uw prestaties. U geeft de exacte hoeveelheid door Voer die u nodig hebt en Azure Cosmos DB garandeert de geconfigureerde door Voer, ondersteund door SLA.

U kunt beginnen met een minimale door Voer van 400 RU/SEC en tot tien tallen miljoenen aanvragen per seconde of zelfs meer schalen. Elke aanvraag die u uitgeeft tegen uw Azure Cosmos-container of-Data Base, zoals een lees aanvraag, schrijf aanvraag, query aanvraag, opgeslagen procedures hebben een overeenkomende kosten die worden afgetrokken van uw ingerichte door voer. Als u 400 RU/s inricht en een query uitgeeft die de kosten 40 RUs heeft, kunt u 10 dergelijke query's per seconde verzenden. Alle aanvragen die groter zijn dan de frequentie beperkt zijn en u moet de aanvraag opnieuw proberen. Als u client Stuur Programma's gebruikt, ondersteunen ze de automatische logica voor opnieuw proberen.

U kunt doorvoer inrichten in databases of containers, en elke strategie kan helpen kosten te besparen, afhankelijk van het scenario.

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>Optimaliseren door door Voer in te richten op verschillende niveaus

* Als u de door Voer inricht voor een Data Base, kunnen alle containers, bijvoorbeeld verzamelingen/tabellen/grafieken binnen die data base, de door Voer delen op basis van de belasting. De door Voer die is gereserveerd op database niveau, wordt ongelijk gedeeld, afhankelijk van de werk belasting voor een specifieke set containers.

* Als u de door Voer voor een container inricht, wordt de door Voer gegarandeerd voor die container, ondersteund door de SLA. De keuze van een logische partitie sleutel is van cruciaal belang voor een gelijkmatige verdeling van de belasting over alle logische partities van een container. Zie artikelen [partitioneren](partitioning-overview.md) en [horizon taal schalen](partitioning-overview.md) voor meer informatie.

Hier volgen enkele richt lijnen voor het kiezen van een ingerichte doorvoer strategie:

**Overweeg de door Voer in te richten op een Azure Cosmos-data base (met een set containers) als**:

1. U hebt een aantal dozijn Azure Cosmos-containers en u kunt door Voer voor een aantal of alle daarvan delen. 

2. U migreert vanuit een Data Base met één Tenant die is ontworpen om te worden uitgevoerd op virtuele machines met IaaS of on-premises, bijvoorbeeld NoSQL of relationele data bases om Azure Cosmos DB. En als u veel verzamelingen/tabellen/grafieken hebt en u geen wijzigingen wilt aanbrengen in uw gegevens model. Houd er rekening mee dat u een aantal van de voor delen van Azure Cosmos DB als u uw gegevens model niet bijwerkt tijdens het migreren van een on-premises data base. Het is raadzaam om altijd toegang te krijgen tot uw gegevens model om optimaal te profiteren van de prestaties en om de kosten te optimaliseren. 

3. U wilt ongeplande pieken in werk belastingen opvangen door een gegroepeerde door Voer op het database niveau dat is onderhevig aan onverwachte pieken in de werk belasting. 

4. In plaats van specifieke door Voer in te stellen voor afzonderlijke containers, zorgt u ervoor dat u de geaggregeerde door Voer voor een set containers binnen de Data Base kunt ophalen.

**Overweeg door Voer in te richten op een afzonderlijke container als:**

1. U hebt een aantal Azure Cosmos-containers. Omdat Azure Cosmos DB schema-neutraal is, kan een container items bevatten die heterogene schema's hebben en hoeven klanten niet meerdere container typen te maken, één voor elke entiteit. Het is altijd een optie om na te gaan of het groeperen van afzonderlijke aanbiedingen van 10-20-containers in één container zinvol is. Met een minimum van 400 RUs voor containers kan de pooling van alle 10-20-containers in één kosten effectief zijn. 

2. U de door Voer voor een specifieke container wilt beheren en de gegarandeerde door Voer op een bepaalde container wilt ophalen die wordt ondersteund door SLA.

**Overweeg een hybride van de bovenstaande twee strategieën:**

1. Zoals eerder vermeld, kunt u met Azure Cosmos DB de bovenstaande twee strategieën combi neren en vergelijken, zodat u nu een aantal containers in azure Cosmos-Data Base hebt, die de door Voer inrichten in de data base kunnen delen, evenals een aantal containers in dezelfde data base, die mogelijk specifieke hoeveel heden ingerichte door voer hebben. 

2. U kunt de bovenstaande strategieën Toep assen om te gaan met een hybride configuratie, waarbij u zowel de door Voer op database niveau als een aantal containers met een speciale door Voer hebt.

Zoals in de volgende tabel wordt weer gegeven, is afhankelijk van de keuze van de API, kunt u de door Voer op verschillende granulariteit inrichten.

|API|Configureer voor **gedeelde** door Voer |Voor **specifieke** door Voer configureren |
|----|----|----|
|SQL-API|Database|Container|
|Azure Cosmos DB-API voor MongoDB|Database|Verzameling|
|Cassandra-API|Keys Pace|Tabel|
|Gremlin-API|Databaseaccount|Graph|
|Tabel-API|Databaseaccount|Tabel|

Door de door Voer op verschillende niveaus in te richten, kunt u uw kosten optimaliseren op basis van de kenmerken van uw werk belasting. Zoals eerder vermeld, kunt u programmatisch en op elk gewenst moment uw ingerichte door Voer verg Roten of verkleinen voor afzonderlijke container (s) of gezamenlijk over een set containers. Door de door voer te verg Roten of verkleinen als uw werk belasting wordt gewijzigd, betaalt u alleen voor de door u geconfigureerde door voer. Als uw container of een set containers wordt gedistribueerd over meerdere regio's, wordt de door u geconfigureerde door Voer voor de container of een set containers gegarandeerd in alle regio's beschikbaar gesteld.

## <a name="optimize-with-rate-limiting-your-requests"></a>Optimaliseren door snelheidsbeperking toe te passen op aanvragen

Voor workloads die niet gevoelig zijn voor latentie, kunt u minder door Voer inrichten en de toepassing de snelheids beperking laten afhandelen wanneer de werkelijke door Voer de ingerichte door Voer overschrijdt. De server preventief de aanvraag met `RequestRateTooLarge` (http-status code 429) en retourneert de `x-ms-retry-after-ms` header die de hoeveelheid tijd, in milliseconden, aangeeft dat de gebruiker moet wachten voordat de aanvraag opnieuw wordt uitgevoerd. 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>Logica voor opnieuw proberen in Sdk's 

Met de systeem eigen Sdk's (.NET/.NET core, Java, Node.js en python) wordt dit antwoord impliciet onderschept, respecteert u de door de server opgegeven nieuwe poging na de header en voert u de aanvraag opnieuw uit. Tenzij uw account gelijktijdig wordt geopend door meerdere clients, zal de volgende poging slagen.

Als u meer dan één client cumulatief op dezelfde manier hebt uitgevoerd, is het standaard aantal nieuwe pogingen, dat momenteel is ingesteld op 9, niet voldoende. In dergelijke gevallen genereert de client een `RequestRateTooLargeException` met de status code 429 naar de toepassing. Het standaard aantal nieuwe pogingen kan worden gewijzigd door de `RetryOptions` in te stellen op het Connection Policy-exemplaar. Standaard `RequestRateTooLargeException` wordt de met de status code 429 geretourneerd na een cumulatieve wacht tijd van 30 seconden als de aanvraag boven het aanvraag aantal blijft. Dit gebeurt zelfs wanneer het huidige aantal nieuwe pogingen kleiner is dan het maximum aantal nieuwe pogingen. Dit is de standaard waarde van 9 of een door de gebruiker gedefinieerd getal. 

[MaxRetryAttemptsOnThrottledRequests](/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests) is ingesteld op 3. in dit geval, als een aanvraag is beperkt door de gereserveerde door Voer voor de container te overschrijden, wordt de aanvraag bewerking drie keer opnieuw geprobeerd voordat de uitzonde ring voor de toepassing wordt gegenereerd. [MaxRetryWaitTimeInSeconds](/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) is ingesteld op 60. in dit geval geldt dat als de cumulatieve wacht tijd voor opnieuw proberen in seconden sinds de eerste aanvraag 60 seconden overschrijdt, de uitzonde ring wordt gegenereerd.

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 
connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 
connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>Partitioneringsstrategie en kosten van ingerichte doorvoer

Een goede partitionatie strategie is belang rijk voor het optimaliseren van de kosten in Azure Cosmos DB. Zorg ervoor dat er geen scheefheid van partities bestaat, die worden weer gegeven via metrische opslag gegevens. Zorg ervoor dat er geen asymmetrische door Voer voor een partitie is, die wordt weer gegeven met metrische gegevens over de door voer. Zorg ervoor dat er geen scheefheid is voor bepaalde partitie sleutels. Dominante sleutels in de opslag worden weer gegeven via metrische gegevens, maar de sleutel is afhankelijk van het toegangs patroon van uw toepassing. U kunt het beste nadenken over de juiste logische partitie sleutel. Er wordt naar verwachting een goede partitie sleutel met de volgende kenmerken:

* Kies een partitie sleutel waarmee de werk belasting gelijkmatig over alle partities en gelijkmatig over de tijd wordt verdeeld. Met andere woorden, u hebt geen sleutels met een meerderheid van de gegevens en bepaalde sleutels met minder of geen gegevens. 

* Kies een partitie sleutel waarmee de toegang tot patronen gelijkmatig over logische partities kan worden verdeeld. De werk belasting is redelijk zelfs in alle sleutels. Met andere woorden, het meren deel van de werk belasting mag niet worden gericht op enkele specifieke sleutels. 

* Kies een partitie sleutel met een breed scala aan waarden. 

Het is een goed idee om de gegevens en de activiteit in uw container over de set logische partities te verdelen, zodat resources voor gegevens opslag en-door Voer kunnen worden gedistribueerd over de logische partities. Kandidaten voor partitie sleutels kunnen de eigenschappen bevatten die regel matig worden weer gegeven als een filter in uw query's. Query's kunnen efficiënt worden gerouteerd door de partitiesleutel op te nemen in het filterpredicaat. Met een dergelijke partitie strategie is het optimaliseren van ingerichte door Voer veel eenvoudiger. 

### <a name="design-smaller-items-for-higher-throughput"></a>Kleinere items ontwerpen voor een hogere door Voer 

De verwerkings kosten voor aanvragen of de belasting van de aanvraag voor een bepaalde bewerking worden rechtstreeks gecorreleerd aan de grootte van het item. Bewerkingen voor grote items hebben meer kosten dan bewerkingen op kleinere items. 

## <a name="data-access-patterns"></a>Patronen voor gegevens toegang 

Het is altijd een goed idee om uw gegevens logisch te scheiden in logische categorieën, op basis van hoe vaak u toegang krijgt tot de gegevens. Door deze te categoriseren als dynamische, middel grote of koude gegevens, kunt u de verbruikte opslag en de door Voer aanpassen. Afhankelijk van de frequentie van de toegang, kunt u de gegevens in afzonderlijke containers plaatsen (bijvoorbeeld tabellen, grafieken en verzamelingen) en de ingerichte door Voer voor hen verfijnen om te voldoen aan de behoeften van het gegevens segment. 

Daarnaast kunt u, als u Azure Cosmos DB gebruikt, en u weet dat u niet op bepaalde gegevens waarden wilt zoeken of zelden toegang wilt krijgen, moet u de gecomprimeerde waarden van deze kenmerken opslaan. Met deze methode bespaart u opslag ruimte, index ruimte en ingerichte door Voer, en resulteert dit in lagere kosten.

## <a name="optimize-by-changing-indexing-policy"></a>Optimaliseren door het indexerings beleid te wijzigen 

Standaard indexeert Azure Cosmos DB automatisch elke eigenschap van elke record. Dit is bedoeld om de ontwikkeling te vereenvoudigen en uitstekende prestaties te garanderen in veel verschillende soorten ad hoc-query's. Als u grote records met duizenden eigenschappen hebt, kunt u de door Voer voor het indexeren van elke eigenschap onbruikbaar maken, met name als u alleen een query uitvoert op 10 of 20 van die eigenschappen. Wanneer u een greep op uw specifieke werk belasting dichterbij komt, kunt u het index beleid afstemmen op onze richt lijnen. Volledige informatie over Azure Cosmos DB indexerings beleid vindt u [hier](index-policy.md). 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>Beschik bare en geconsumeerde door Voer bewaken 

U kunt het totale aantal ingerichte RUs, het aantal tarieven voor beperkte aanvragen en het aantal RUs dat u hebt verbruikt, bewaken in de Azure Portal. In de volgende afbeelding ziet u een voor beeld van een gebruiks metriek:

:::image type="content" source="./media/optimize-cost-throughput/monitoring.png" alt-text="Aanvraag eenheden bewaken in de Azure Portal":::

U kunt ook waarschuwingen instellen om te controleren of het aantal aanvragen met beperkte geldigheid een specifieke drempel waarde overschrijdt. Zie [Azure Cosmos DB artikel controleren](use-metrics.md) voor meer informatie. Deze waarschuwingen kunnen een e-mail verzenden naar de account beheerders of een aangepaste HTTP-webhook of een Azure-functie aanroepen om de ingerichte door Voer automatisch te verg Roten. 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>Uw door Voer elastisch en op aanvraag schalen 

Omdat u de ingerichte door Voer hebt gefactureerd, kunt u met de ingerichte door Voer aan uw behoeften het voor komen van de kosten voor de ongebruikte door voer. U kunt uw ingerichte door Voer op elk gewenst moment omhoog of omlaag schalen, indien nodig. Als uw doorvoer behoeften zeer voorspelbaar zijn, kunt u Azure Functions gebruiken en een timer trigger gebruiken om [de door Voer van een planning te verg Roten of verkleinen](scale-on-schedule.md). 

* Het bewaken van het verbruik van uw RUs en de verhouding van aanvragen met beperkte snelheid kan ertoe leiden dat u gedurende de hele dag of de week niet over de gehele constante hoeft te beschikken. Mogelijk ontvangt u 's nachts of tijdens het weekend minder verkeer. Door gebruik te maken van Azure Portal of Azure Cosmos DB systeem eigen Sdk's of REST API kunt u de ingerichte door Voer op elk gewenst moment schalen. De REST API van Azure Cosmos DB biedt eind punten om het prestatie niveau van uw containers programmatisch bij te werken, waardoor het eenvoudig is om de door Voer van uw code aan te passen, afhankelijk van de tijd van de dag of de dag van de week. De bewerking wordt uitgevoerd zonder uitval tijd en duurt meestal minder dan een minuut. 

* Een van de gebieden die u moet door Voer schalen is wanneer u gegevens opneemt in Azure Cosmos DB, bijvoorbeeld tijdens de gegevens migratie. Wanneer u de migratie hebt voltooid, kunt u de ingerichte door Voer omlaag schalen om de stabiele status van de oplossing af te handelen.  

* Houd er rekening mee dat de facturering één uur nauw keurig is, zodat u geen geld bespaart als u de ingerichte door Voer vaker dan één uur per keer wijzigt.

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>De door Voer bepalen die nodig is voor een nieuwe workload 

U kunt de volgende stappen gebruiken om de ingerichte door Voer voor een nieuwe workload te bepalen: 

1. Voer een initiële, ruwe evaluatie uit met behulp van de capaciteits planner en pas uw schattingen aan met behulp van de Azure Cosmos DB Explorer in de Azure Portal. 

2. Het is raadzaam om de containers met een hogere door voer te maken dan verwacht en vervolgens naar behoefte te schalen. 

3. Het is raadzaam een van de systeem eigen Azure Cosmos DB Sdk's te gebruiken om te profiteren van automatische nieuwe pogingen wanneer aanvragen een beperkt aantal zijn. Als u werkt met een platform dat niet wordt ondersteund en gebruikmaakt van de REST API van Cosmos DB, implementeert u uw eigen beleid voor opnieuw proberen met behulp van de `x-ms-retry-after-ms` header. 

4. Zorg ervoor dat uw toepassings code de situatie op de juiste wijze ondersteunt wanneer alle pogingen mislukken. 

5. U kunt waarschuwingen van de Azure Portal configureren voor het ontvangen van meldingen voor de frequentie beperking. U kunt beginnen met een conservatieve limiet zoals tien aanvragen met een beperkte frequentie in de afgelopen 15 minuten en overschakelen naar meer regels wanneer u het werkelijke verbruik hebt afgesteld. De limieten voor incidentele tarieven zijn nauw keurig, ze geven aan dat u de limieten die u hebt ingesteld en die precies zijn wat u wilt doen. 

6. Gebruik bewaking om inzicht te krijgen in uw verkeers patroon, zodat u rekening moet houden met de nood zaak om uw doorvoer inrichting binnen de dag of een week dynamisch aan te passen. 

7. Controleer uw ingerichte versus verbruikte doorvoer ratio regel matig om er zeker van te zijn dat u niet meer dan het vereiste aantal containers en data bases hebt ingericht. Een beetje over de ingerichte door Voer is een goede veiligheids controle.  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>Aanbevolen procedures voor het optimaliseren van ingerichte door Voer 

De volgende stappen helpen u bij het gebruik van Azure Cosmos DB om uw oplossingen uiterst schaalbaar en rendabel te maken.  

1. Als u de ingerichte door Voer voor alle containers en data bases aanzienlijk hebt overbelast, moet u de werk belastingen die zijn ingericht versus gebruikt voor het verfijnen van de workloads controleren.  

2. Een methode voor het schatten van de hoeveelheid gereserveerde door Voer die is vereist door uw toepassing is het vastleggen van de RU-kosten voor aanvraag eenheden die zijn gekoppeld aan het uitvoeren van gang bare bewerkingen voor een representatieve Azure Cosmos-container of-Data Base die wordt gebruikt door uw toepassing. vervolgens wordt een schatting gemaakt van het aantal bewerkingen dat u per seconde moet uitvoeren. Zorg ervoor dat u normale query's en hun gebruik ook bemeett en opneemt. Zie [de kosten van Query's optimaliseren](./optimize-cost-reads-writes.md)voor meer informatie over het programmatisch schatten van de kosten van query's via een programma of het gebruik van de portal. 

3. Een andere manier om bewerkingen en hun kosten in RUs te verkrijgen, is door Azure Monitor-Logboeken in te scha kelen, waarmee u de bewerkings-en tijds duur en de aanvraag kosten kunt opsplitsen. Azure Cosmos DB biedt voor elke bewerking een aanvraag kosten, zodat elke bewerkings kosten kunnen worden opgeslagen in het antwoord en vervolgens worden gebruikt voor analyse. 

4. U kunt de ingerichte door Voer op een flexibele manier omhoog en omlaag schalen als u behoefte hebt aan uw werk belasting. 

5. U kunt regio's die zijn gekoppeld aan uw Azure Cosmos-account toevoegen en verwijderen wanneer u deze nodig hebt en de kosten wilt beheren. 

6. Zorg ervoor dat u een gelijkmatige verdeling van gegevens en workloads over logische partities van uw containers hebt. Als u een ongelijke partitie distributie hebt, kan dit ertoe leiden dat er een hogere hoeveelheid door Voer wordt ingesteld dan de waarde die nodig is. Als u identificeert dat u een scheefe distributie hebt, raden we u aan de werk belasting gelijkmatig over de partities te verdelen of de gegevens opnieuw te partitioneren. 

7. Als er veel containers zijn en voor deze containers geen service overeenkomsten zijn vereist, kunt u de op een Data Base gebaseerde aanbieding gebruiken voor de gevallen waarin de Sla's per container doorvoer niet van toepassing zijn. U moet bepalen welke van de Azure Cosmos-containers u wilt migreren naar de aanbieding voor de door Voer van het data base-niveau en deze vervolgens migreren met behulp van een oplossing voor wijzigings invoer. 

8. Overweeg het gebruik van de Cosmos DB gratis laag (gratis voor één jaar), probeer Cosmos DB (Maxi maal drie regio's) of down loadable Cosmos DB-emulator voor ontwikkel-en test scenario's. Door deze opties voor test-dev te gebruiken, kunt u uw kosten aanzienlijk verlagen.  

9. U kunt verdere workload-specifieke kosten optimalisaties uitvoeren, bijvoorbeeld het verg Roten van de Batch-grootte, de taak verdeling over meerdere regio's en het verdubbelen van gegevens, indien van toepassing.

10. Met Azure Cosmos DB gereserveerde capaciteit kunt u gedurende drie jaar aanzienlijke kortingen krijgen voor Maxi maal 65%. Azure Cosmos DB gereserveerde capaciteits model is een toezeg ging van aanvragen voor aanvraag eenheden die gedurende een bepaalde periode nodig zijn. De kortingen worden zodanig gelaagd dat de meer aanvraag eenheden die u gedurende een langere periode gebruikt, hoe meer uw korting zal zijn. Deze kortingen worden onmiddellijk toegepast. Elk RUs dat wordt gebruikt voor uw ingerichte waarden wordt in rekening gebracht op basis van de niet-gereserveerde capaciteits kosten. Zie [Cosmos DB gereserveerde capaciteit](cosmos-db-reserved-capacity.md)) voor meer informatie. Overweeg gereserveerde capaciteit aan te schaffen om uw ingerichte doorvoer kosten verder te verlagen.  

## <a name="next-steps"></a>Volgende stappen

Daarna kunt u meer te weten komen over cost Optimization in Azure Cosmos DB met de volgende artikelen:

* Meer informatie over het [optimaliseren voor ontwikkeling en testen](optimize-dev-test.md)
* Meer informatie over [uw Azure Cosmos DB factuur](understand-your-bill.md)
* Meer informatie over het [optimaliseren van opslag kosten](optimize-cost-storage.md)
* Meer informatie over [het optimaliseren van de kosten van lees-en schrijf bewerkingen](optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten van query's](./optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten voor Azure Cosmos-accounts met meerdere regio's](optimize-cost-regions.md)
