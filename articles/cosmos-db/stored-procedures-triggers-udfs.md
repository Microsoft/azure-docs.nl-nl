---
title: Werken met opgeslagen procedures, triggers en Udf's in Azure Cosmos DB
description: In dit artikel worden de concepten geïntroduceerd, zoals opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: ad9e6b99b396465c2cff95bd6ab340ef9d668085
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99575954"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB biedt taalgeïntegreerde, transactionele uitvoering van Java script. Wanneer u de SQL-API gebruikt in Azure Cosmos DB, kunt u **opgeslagen procedures**, **Triggers** en door de **gebruiker gedefinieerde functies (Udf's)** schrijven in de Java script-taal. U kunt uw logica schrijven in JavaScript die wordt uitgevoerd binnen de database-engine. U kunt triggers, opgeslagen procedures en UDFs maken en uitvoeren met behulp van [Azure Portal](https://portal.azure.com/), de [geïntegreerde Java script language-query-API in azure Cosmos DB](javascript-query-api.md) of de client-SDK'S van de [Cosmos DB SQL API](how-to-use-stored-procedures-triggers-udfs.md).

## <a name="benefits-of-using-server-side-programming"></a>Voor delen van het gebruik van programmering aan de server zijde

Door opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies (Udf's) te schrijven in Java script, kunt u uitgebreide toepassingen bouwen en beschikken ze over de volgende voor delen:

* **Procedurele logica:** Java script als een programmeer taal op hoog niveau die een uitgebreide en bekende interface biedt voor het expressiseren van bedrijfs logica. U kunt een reeks complexe bewerkingen uitvoeren op de gegevens.

* **Atomische trans acties:** Azure Cosmos DB database bewerkingen die worden uitgevoerd binnen één opgeslagen procedure of een trigger, zijn atomisch. Met deze Atomic-functionaliteit kunt u gerelateerde bewerkingen combi neren in één batch, zodat alle bewerkingen slagen of geen van deze acties slagen.

* **Prestaties:** De JSON-gegevens zijn intrinsiek toegewezen aan het taal type systeem van Java script. Deze toewijzing biedt een aantal optimalisaties zoals een luie materialisatie van JSON-documenten in de buffer groep en maakt ze beschikbaar op aanvraag voor de code die wordt uitgevoerd. Er zijn andere prestatie voordelen verbonden aan het verzenden van bedrijfs logica naar de-data base, waaronder:

   * *Batch verwerking:* U kunt bewerkingen als toevoegingen groeperen en deze bulksgewijs verzenden. De latentie kosten van het netwerk verkeer en de opslag overhead voor het maken van afzonderlijke trans acties worden aanzienlijk verminderd.

   * *Pre-compilatie:* Opgeslagen procedures, triggers en Udf's worden impliciet vooraf gecompileerd naar de byte code-indeling om te voor komen dat compilatie kosten worden berekend op het moment van elke aanroepen van het script. Vanwege de vooraf-compilatie is het aanroepen van opgeslagen procedures snel en heeft deze een geringe footprint.

   * *Sequentiëren:* Bewerkingen hebben soms een activerings mechanisme nodig waarmee een of meer updates voor de gegevens kunnen worden uitgevoerd. Naast de atomiciteit zijn er ook prestatie voordelen bij de uitvoering van aan de server zijde.

* **Inkapseling:** Opgeslagen procedures kunnen worden gebruikt om logica op één locatie te groeperen. Encapsulation voegt een abstractie laag toe boven op de gegevens, zodat u uw toepassingen onafhankelijk van de gegevens kunt ontwikkelen. Deze laag van abstractie is handig wanneer de gegevens schema-minder zijn en u niet hoeft te beheren om extra logica rechtstreeks in uw toepassing toe te voegen. De abstractie zorgt ervoor dat de gegevens veilig blijven door de toegang vanuit de scripts te stroom lijnen.

> [!TIP]
> Opgeslagen procedures zijn het meest geschikt voor bewerkingen die zijn geschreven, en vereisen een trans actie op basis van een partitie sleutel waarde. Wanneer u beslist of opgeslagen procedures moeten worden gebruikt, optimaliseer dan het inkapselen van de maximale schrijf tijd die mogelijk is. Over het algemeen zijn opgeslagen procedures niet de meest efficiënte manier om grote hoeveel heden Lees-of query bewerkingen uit te voeren. Als u opgeslagen procedures gebruikt om te retour neren naar de client, wordt het gewenste voor deel niet in rekening gebracht. Voor de beste prestaties moeten deze Lees bewerkingen worden uitgevoerd aan de client zijde, met behulp van de Cosmos-SDK. 

## <a name="transactions"></a>Transacties

Trans acties in een typische data base kunnen worden gedefinieerd als een reeks bewerkingen die worden uitgevoerd als één logische werk eenheid. Elke trans actie biedt **ACID-eigenschappen garanties**. ZUUR is een bekend acroniem dat staat voor: **een** tomicity, **C** onsistency, **I** solation en **D** urability. 

* Atomiciteit garandeert dat alle bewerkingen die worden uitgevoerd binnen een trans actie, worden behandeld als één eenheid en dat allemaal zijn doorgevoerd of niet. 

* Consistentie zorgt ervoor dat de gegevens altijd een geldige status over trans acties hebben. 

* Door isolatie wordt gegarandeerd dat twee trans acties met elkaar in strijd zijn: veel commerciële systemen bieden meerdere isolatie niveaus die kunnen worden gebruikt op basis van de behoeften van de toepassing. 

* Duurzaamheid zorgt ervoor dat alle wijzigingen die in een Data Base zijn doorgevoerd, altijd aanwezig zijn.

In Azure Cosmos DB wordt Java Script runtime gehost in de data base-engine. Daarom worden aanvragen binnen de opgeslagen procedures en de triggers in hetzelfde bereik als de database sessie uitgevoerd. Met deze functie kunnen Azure Cosmos DB ACID-eigenschappen garanderen voor alle bewerkingen die deel uitmaken van een opgeslagen procedure of een trigger. Zie [How to Implementing trans actions](how-to-write-stored-procedures-triggers-udfs.md#transactions) article (Engelstalig) voor voor beelden.

### <a name="scope-of-a-transaction"></a>Bereik van een trans actie

Opgeslagen procedures zijn gekoppeld aan een Azure Cosmos-container en de uitvoering van de opgeslagen procedure is afgestemd op een logische partitie sleutel. Opgeslagen procedures moeten een logische partitie sleutel waarde bevatten tijdens de uitvoering waarmee de logische partitie voor het bereik van de trans actie wordt gedefinieerd. Zie [Azure Cosmos DB partitioning](partitioning-overview.md) -artikel voor meer informatie.

### <a name="commit-and-rollback"></a>Door voeren en terugdraaien

Trans acties zijn systeem eigen geïntegreerd in het Azure Cosmos DB java script-programmeer model. Binnen een Java script-functie worden alle bewerkingen automatisch verpakt onder één trans actie. Als de Java script-logica in een opgeslagen procedure zonder uitzonde ringen is voltooid, worden alle bewerkingen binnen de trans actie doorgevoerd naar de data base. Instructies `BEGIN TRANSACTION` , zoals en `COMMIT TRANSACTION` (vertrouwde relationele data bases) zijn impliciet in azure Cosmos db. Als er uitzonde ringen van het script zijn, wordt de hele trans actie teruggedraaid met de Azure Cosmos DB Java Script runtime. Als zodanig is een uitzonde ring opgetreden die in feite overeenkomt met een `ROLLBACK TRANSACTION` in azure Cosmos db.

### <a name="data-consistency"></a>Gegevensconsistentie

Opgeslagen procedures en triggers worden altijd uitgevoerd op de primaire replica van een Azure Cosmos-container. Deze functie zorgt ervoor dat lees bewerkingen van opgeslagen procedures een [sterke consistentie](./consistency-levels.md)bieden. Query's met door de gebruiker gedefinieerde functies kunnen worden uitgevoerd op de primaire of een secundaire replica. Opgeslagen procedures en triggers zijn bedoeld ter ondersteuning van transactionele schrijf bewerkingen. in de richting van alleen-lezen logica wordt het beste geïmplementeerd als logica en query's op toepassings zijde met behulp van de [Azure Cosmos DB SQL API sdk's](sql-api-dotnet-samples.md), helpt u de door Voer van de data base te verzadigen. 

> [!TIP]
> De query's die in een opgeslagen procedure of trigger worden uitgevoerd, zien mogelijk geen wijzigingen in items die door dezelfde script transactie zijn gemaakt. Deze instructie is van toepassing op SQL-query's, zoals `getContent().getCollection.queryDocuments()` , en geïntegreerde taal query's, zoals `getContext().getCollection().filter()` .

## <a name="bounded-execution"></a>Gebonden uitvoering

Alle Azure Cosmos DB bewerkingen moeten binnen de opgegeven time-outduur volt ooien. Opgeslagen procedures hebben een time-outlimiet van 5 seconden. Deze beperking is van toepassing op Java script-functies: opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies. Als een bewerking niet binnen deze tijds limiet wordt voltooid, wordt de trans actie teruggedraaid.

U kunt ervoor zorgen dat uw Java script-functies binnen de tijds limiet worden voltooid of een op voortzetting gebaseerd model implementeren voor het uitvoeren van batch/hervatten. Om de ontwikkeling van opgeslagen procedures en triggers voor het afhandelen van tijds limieten te vereenvoudigen, retour neren alle functies onder de Azure Cosmos-container (bijvoorbeeld maken, lezen, bijwerken en verwijderen van items) een Booleaanse waarde die aangeeft of de bewerking wordt voltooid. Als deze waarde False is, is het een indicatie dat de procedure de uitvoering ervan moet oplopen omdat het script meer tijd of ingerichte door Voer verbruikt dan de geconfigureerde waarde. Bewerkingen die in de wachtrij staan vóór de eerste niet-geaccepteerde opslag bewerking, worden gegarandeerd voltooid als de opgeslagen procedure in de tijd is voltooid en er geen aanvragen in de wachtrij worden geplaatst. Bewerkingen moeten dus een voor een in de wachtrij worden geplaatst met de call back-Conventie van Java script om de controle stroom van het script te beheren. Omdat scripts worden uitgevoerd in een omgeving aan de server zijde, zijn ze strikt onderhevig. Scripts die de uitvoerings grenzen herhaaldelijk overschrijden, kunnen worden gemarkeerd als inactief en kunnen niet worden uitgevoerd en ze moeten opnieuw worden gemaakt om de uitvoerings grenzen te controleren.

Java script-functies zijn ook onderhevig aan [ingerichte doorvoer capaciteit](request-units.md). Java script-functies kunnen mogelijk een groot aantal aanvraag eenheden binnen korte tijd gebruiken en zijn mogelijk beperkt als de limiet voor de ingerichte doorvoer capaciteit is bereikt. Het is belang rijk te weten dat scripts extra door Voer gebruiken naast de door Voer die is besteed aan het uitvoeren van database bewerkingen, hoewel deze database bewerkingen iets minder kostbaar zijn dan het uitvoeren van dezelfde bewerkingen van de client.

## <a name="triggers"></a>Triggers

Azure Cosmos DB biedt ondersteuning voor twee typen triggers:

### <a name="pre-triggers"></a>Pre-triggers

Azure Cosmos DB biedt triggers die kunnen worden aangeroepen door een bewerking uit te voeren op een Azure Cosmos-item. U kunt bijvoorbeeld een pre-trigger opgeven wanneer u een item maakt. In dit geval wordt de pre-trigger uitgevoerd voordat het item wordt gemaakt. Pre-triggers kunnen geen invoerparameters hebben. Indien nodig kan het aanvraagobject worden gebruikt om de hoofdtekst van het document van de oorspronkelijke aanvraag bij te werken. Wanneer triggers zijn geregistreerd, kunnen gebruikers de bewerkingen opgeven waarmee deze kunnen worden uitgevoerd. Als een trigger is gemaakt met `TriggerOperation.Create`, betekent dit dat het gebruik van de trigger in een vervangbewerking niet is toegestaan. Zie [het artikel triggers schrijven](how-to-write-stored-procedures-triggers-udfs.md#triggers) voor voor beelden.

### <a name="post-triggers"></a>Post-triggers

Net als bij pretriggers worden post-triggers ook geassocieerd met een bewerking in een Azure Cosmos-item en zijn er geen invoer parameters vereist. Ze worden uitgevoerd *nadat* de bewerking is voltooid en hebben toegang tot het antwoord bericht dat naar de client wordt verzonden. Zie [het artikel triggers schrijven](how-to-write-stored-procedures-triggers-udfs.md#triggers) voor voor beelden.

> [!NOTE]
> Geregistreerde triggers worden niet automatisch uitgevoerd wanneer de bijbehorende bewerkingen (maken/verwijderen/vervangen/bijwerken) plaatsvinden. Ze moeten expliciet worden aangeroepen bij het uitvoeren van deze bewerkingen. Zie [het artikel triggers uitvoeren](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) voor meer informatie.

## <a name="user-defined-functions"></a><a id="udfs"></a>Door de gebruiker gedefinieerde functies

Door de [gebruiker gedefinieerde functies](sql-query-udfs.md) (udf's) worden gebruikt om de SQL API-query taal syntaxis uit te breiden en aangepaste bedrijfs logica eenvoudig te implementeren. Ze kunnen alleen worden aangeroepen binnen query's. Udf's hebben geen toegang tot het context object en zijn bedoeld om te worden gebruikt als alleen-Compute-java script. Daarom kunnen Udf's worden uitgevoerd op secundaire replica's. Zie het artikel door de [gebruiker gedefinieerde functies schrijven](how-to-write-stored-procedures-triggers-udfs.md#udfs) voor voor beelden.

## <a name="javascript-language-integrated-query-api"></a><a id="jsqueryapi"></a>Java script language-geïntegreerde query-API

Naast het uitgeven van query's met behulp van de SQL API-query syntaxis kunt u met de SDK aan de [server zijde](https://azure.github.io/azure-cosmosdb-js-server) query's uitvoeren met behulp van een Java script-interface zonder kennis van SQL. Met de Java script-query-API kunt u programmatisch query's maken door predicaat functies door te geven in reeksen functie aanroepen. Query's worden geparseerd door de Java Script-runtime en worden efficiënt uitgevoerd binnen Azure Cosmos DB. Zie [werken met Java script language integrated query API](javascript-query-api.md) -artikel voor meer informatie over Java script-query-API-ondersteuning. Zie voor voor beelden [hoe u opgeslagen procedures en triggers schrijft met behulp van Java script-query-API](how-to-write-javascript-query-api.md) -artikelen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het schrijven en gebruiken van opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies in Azure Cosmos DB met de volgende artikelen:

* [Opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies schrijven](how-to-write-stored-procedures-triggers-udfs.md)

* [Opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies gebruiken](how-to-use-stored-procedures-triggers-udfs.md)

* [Werken met Java script language integrated query-API](javascript-query-api.md)