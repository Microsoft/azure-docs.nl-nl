---
title: Veelgestelde vragen over verschillende Api's in Azure Cosmos DB
description: Krijg antwoorden op veelgestelde vragen over Azure Cosmos DB, een wereld wijd gedistribueerde, multi-model database service. Meer informatie over capaciteit, prestatie niveaus en schalen.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 81c959467c4bb3700120917cf150773fb18e866d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359826"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Veelgestelde vragen over verschillende API's in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Wat zijn de typische gebruiks voorbeelden voor Azure Cosmos DB?

Azure Cosmos DB is een goede keuze voor nieuwe web-, mobiele, gaming-en IoT-toepassingen waarbij automatisch schalen, voorspel bare prestaties, snelle volg orde van milliseconde reactie tijden en de mogelijkheid om gegevens op te vragen over schema's. Dit is belang rijk. Azure Cosmos DB onderleent zichzelf bij het snel ontwikkelen en ondersteunen van de continue herhaling van toepassings gegevens modellen. Toepassingen die door de gebruiker gegenereerde inhoud en gegevens beheren, zijn [veelvoorkomende use cases voor Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Hoe biedt Azure Cosmos DB voorspel bare prestaties?

Een [aanvraag eenheid](request-units.md) (ru) is de maat eenheid voor door voer in azure Cosmos db. Een 1RU-door Voer komt overeen met de door Voer van het ophalen van een document van 1 KB. Elke bewerking in Azure Cosmos DB, inclusief Lees bewerkingen, schrijf bewerkingen, SQL-query's en uitvoeringen van opgeslagen procedures, heeft een deterministische RU-waarde die is gebaseerd op de door Voer die is vereist om de bewerking te volt ooien. In plaats van te denken over CPU, i/o en het geheugen en de manier waarop deze elk van invloed zijn op de door Voer van uw toepassing, kunt u overwegen in termen van één RU-meting.

U kunt elke Azure Cosmos-container met ingerichte door Voer configureren in termen van RUs of door Voer per seconde. Voor toepassingen van elke schaal kunt u afzonderlijke aanvragen voor het meten van de RU-waarden bepalen en een container inrichten voor het afhandelen van het totale aantal aanvraag eenheden voor alle aanvragen. U kunt de door Voer van uw container ook omhoog of omlaag schalen als de behoeften van uw toepassing ontwikkelen. Voor meer informatie over aanvraag eenheden en voor hulp bij het bepalen van de vereisten voor containers, probeert u de [doorvoer Calculator](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Hoe ondersteunt Azure Cosmos DB verschillende gegevens modellen, zoals sleutel/waarde, in kolom vorm, document en grafiek?

Gegevens modellen voor sleutel/waarde (tabel), in kolom vorm, document en grafiek worden allemaal systeem eigen ondersteund vanwege het ontwerp van de ARS (atomen, records en sequencers) waarop Azure Cosmos DB is gebouwd. Atomen, records en reeksen kunnen eenvoudig worden toegewezen en geprojecteerd aan verschillende gegevens modellen. De Api's voor een subset van modellen zijn nu beschikbaar (SQL, MongoDB, Table en Gremlin) en andere specifiek voor aanvullende gegevens modellen zullen in de toekomst beschikbaar zijn.

Azure Cosmos DB heeft een neutraal indexerings engine waarmee alle gegevens die worden opgenomen, automatisch kunnen worden geïndexeerd zonder schema of secundaire indexen van de ontwikkelaar. De engine is afhankelijk van een set logische index indelingen (omgekeerd, in kolom vorm, structuur) waarmee de opslag indeling wordt losgekoppeld van de subsystemen voor index en query verwerking. Cosmos DB heeft ook de mogelijkheid om een set bedradings protocollen en Api's op een uitbreid bare manier te ondersteunen en ze efficiënt te vertalen naar het kern gegevens model (1) en de logische index indelingen (2), waardoor het uniek is om meer dan één gegevens model te ondersteunen.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Kan ik meerdere Api's gebruiken voor toegang tot mijn gegevens?

Azure Cosmos DB is de globaal gedistribueerde multimodel-databaseservice van Microsoft. Wanneer multi-model betekent Azure Cosmos DB meerdere Api's en meerdere gegevens modellen ondersteunt, gebruiken verschillende Api's verschillende gegevens indelingen voor Storage en wire-protocol. SQL maakt bijvoorbeeld gebruik van JSON, MongoDB gebruikt BSON, tabel gebruikt EDM, Cassandra gebruikt CQL, Gremlin maakt gebruik van JSON-indeling. Als gevolg hiervan raden we u aan dezelfde API te gebruiken voor alle toegang tot de gegevens in een bepaald account.

Elke API werkt onafhankelijk, met uitzonde ring van de Gremlin-en SQL-API, die interoperabel zijn.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Is Azure Cosmos DB HIPAA-compatibel?

Ja, Azure Cosmos DB is HIPAA-compatibel. HIPAA stelt vereisten vast voor het gebruik, de openbaarmaking en de beveiliging van individueel identificeerbare gezondheidsinformatie. Zie het [vertrouwens centrum van micro soft](/compliance/regulatory/offering-hipaa-hitech)voor meer informatie.

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Wat zijn de opslag limieten van Azure Cosmos DB?

Er is geen limiet voor de totale hoeveelheid gegevens die een container kan opslaan in Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Wat zijn de doorvoer limieten van Azure Cosmos DB?

Er is geen limiet voor het totale aantal door voer dat een container kan ondersteunen in Azure Cosmos DB. Het is belang rijk om uw workload ongeveer gelijkmatig te verdelen over een voldoende aantal partitie sleutels.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Zijn directe en gateway connectiviteits modi versleuteld?

Ja beide modi zijn altijd volledig versleuteld.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Hoeveel kost het Azure Cosmos DB?

Raadpleeg de pagina met [prijs informatie voor Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) voor meer informatie. Azure Cosmos DB gebruiks kosten worden bepaald door het aantal ingerichte containers, het aantal uur dat de containers online waren en de ingerichte door Voer voor elke container.

### <a name="is-a-free-account-available"></a>Is er een gratis account beschikbaar?

Ja, u kunt zich gratis aanmelden voor een gratis account, zonder toezeg ging. Als u zich wilt registreren, gaat u naar [Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) of leest u meer in het [Azure Cosmos DB Veelgestelde vragen over try](#try-cosmos-db)-out.

Als u niet bekend bent met Azure, kunt u zich aanmelden voor een [gratis Azure-account](https://azure.microsoft.com/free/), waardoor u 30 dagen en een tegoed krijgt om alle Azure-Services te proberen. Als u een Visual Studio-abonnement hebt, kunt u ook [gratis Azure-tegoeden](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) gebruiken voor elke Azure-service.

U kunt ook de [Azure Cosmos DB-emulator](local-emulator.md) gebruiken om uw toepassing gratis lokaal te ontwikkelen en te testen zonder een Azure-abonnement te maken. Als u tevreden bent over hoe uw toepassing in de Azure Cosmos DB Emulator werkt, kunt u overstappen naar een Azure Cosmos DB-account in de cloud.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Hoe kan ik aanvullende hulp krijgen bij Azure Cosmos DB?

Als u een technische vraag wilt stellen, kunt u een bericht plaatsen op een van deze twee vraag-en antwoord forums:

* [Microsoft Q&A-vragenpagina](/answers/topics/azure-cosmos-db.html)
* [Stack overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow is het meest geschikt voor het Program meren van vragen. Zorg ervoor dat u de vraag hebt [over het onderwerp](https://stackoverflow.com/help/on-topic) en [Geef zo veel mogelijk details op, zodat u de vraag duidelijk en beantwoord kunt krijgen](https://stackoverflow.com/help/how-to-ask).

Als u nieuwe functies wilt aanvragen, maakt u een nieuwe aanvraag voor [gebruikers spraak](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Als u een probleem met uw account wilt oplossen, kunt u een [ondersteuningsaanvraag](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) indienen in Azure Portal.

## <a name="try-azure-cosmos-db-subscriptions"></a><a id="try-cosmos-db"></a>Probeer Azure Cosmos DB-abonnementen

U kunt nu profiteren van een beperkte Azure Cosmos DB ervaring zonder abonnement, kosteloos en toezeg gingen. Als u zich wilt registreren voor een try Azure Cosmos DB-abonnement, gaat u naar [Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) en gebruikt u persoonlijke Microsoft-account (MSA). Dit abonnement is gescheiden van de [gratis proef versie van Azure](https://azure.microsoft.com/free/)en kan worden gebruikt in combi natie met een gratis proef versie van Azure of een betaald Azure-abonnement.

Probeer Azure Cosmos DB abonnementen worden weer gegeven in de Azure Portal volgende andere abonnementen die zijn gekoppeld aan uw gebruikers-ID.

De volgende voor waarden zijn van toepassing om Azure Cosmos DB-abonnementen te proberen:

* Account toegang kan worden verleend aan persoonlijke micro soft-accounts (MSA). Vermijd het gebruik van Azure Active Directory (Azure AD)-accounts of accounts die horen bij zakelijke Azure AD-tenants, ze kunnen beperkingen hebben waardoor het verlenen van toegang kan worden geblokkeerd.
* Eén [door Voer ingerichte container](./set-throughput.md#set-throughput-on-a-container) per abonnement voor SQL, GREMLIN-API en tabel accounts.
* Maxi maal drie [ingerichte door Voer ingerichte verzamelingen](./set-throughput.md#set-throughput-on-a-container) per abonnement voor MongoDb-accounts.
* Eén [door Voer ingerichte data base](./set-throughput.md#set-throughput-on-a-database) per abonnement. Data bases met door Voer ingericht kunnen een wille keurig aantal containers bevatten.
* opslag capaciteit van 10 GB.
* Globale replicatie is beschikbaar in de volgende [Azure-regio's](https://azure.microsoft.com/regions/): Centraal vs, Europa-Noord en Zuidoost-Azië
* Maximale door Voer van 5 K RU/s wanneer ingericht op het niveau van de container.
* Maximale door Voer van 20 K RU/s wanneer ingericht op database niveau.
* Abonnementen verlopen na 30 dagen en kunnen worden verlengd tot een bedrag van Maxi maal 31 dagen. Na verloop van tijd wordt de informatie verwijderd.
* Er kunnen geen ondersteunings tickets voor Azure worden gemaakt voor het proberen van Azure Cosmos DB accounts; Er wordt echter ondersteuning geboden voor abonnees met bestaande ondersteunings abonnementen.

## <a name="set-up-azure-cosmos-db"></a>Azure Cosmos DB instellen

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Hoe kan ik zich registreren voor Azure Cosmos DB?

Azure Cosmos DB is beschikbaar in de Azure Portal. Meld u eerst aan voor een Azure-abonnement. Nadat u zich hebt geregistreerd, kunt u een Azure Cosmos DB-account toevoegen aan uw Azure-abonnement.

### <a name="what-is-a-primary-key"></a>Wat is een primaire sleutel?

Een primaire sleutel is een beveiligings token voor toegang tot alle resources voor een account. Personen met de sleutel hebben lees-en schrijf toegang tot alle resources in het database account. Wees voorzichtig wanneer u primaire sleutels distribueert. De primaire primaire sleutel en secundaire primaire sleutel zijn beschikbaar op de Blade **sleutels** van de [Azure Portal][azure-portal]. Zie [Toegangssleutels weergeven, kopiëren en opnieuw genereren](manage-with-cli.md#list-account-keys) voor meer informatie over sleutels.

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Wat zijn de regio's waarop PreferredLocations kan worden ingesteld?

De PreferredLocations-waarde kan worden ingesteld op een van de Azure-regio's waarin Cosmos DB beschikbaar is. Zie [Azure-regio's](https://azure.microsoft.com/regions/)voor een lijst met beschik bare regio's.

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Is er iets wat ik moet weten bij het distribueren van gegevens over de hele wereld via de Azure-data centers?

Azure Cosmos DB is aanwezig in alle Azure-regio's, zoals is opgegeven op de pagina [Azure-regio's](https://azure.microsoft.com/regions/) . Omdat het de kern service is, heeft elk nieuw Data Center een Azure Cosmos DB aanwezigheid.

Wanneer u een regio instelt, moet u er rekening mee houden dat Azure Cosmos DB respecteert aan soevereine en overheids Clouds. Dat wil zeggen, als u een account in een [soevereine regio](https://azure.microsoft.com/global-infrastructure/)maakt, u niet uit die [soevereine regio](https://azure.microsoft.com/global-infrastructure/)kunt repliceren. Op dezelfde manier kunt u de replicatie niet inschakelen in andere soevereine locaties vanuit een extern account.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Is het mogelijk om over te scha kelen van de inrichting voor het inrichten van container niveau naar het inrichten van de doorvoer capaciteit op database niveau? Of andersom

Het inrichten van een doorvoer capaciteit op basis van containers en data bases is afzonderlijke aanbiedingen en scha kelen tussen beide vereisen het migreren van gegevens van bron naar bestemming. Dit betekent dat u een nieuwe data base of een nieuwe container moet maken en vervolgens gegevens kunt migreren met behulp van de [bibliotheek voor bulksgewijs](bulk-executor-overview.md) uitvoerder of [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Ondersteunt Azure CosmosDB tijd reeks analyse?

Ja Azure CosmosDB ondersteunt tijd reeks analyse. Hier volgt een voor beeld van een [tijds reeks patroon](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). In dit voor beeld ziet u hoe u een wijzigings feed kunt gebruiken om geaggregeerde weer gaven te bouwen voor tijdreeks gegevens. U kunt deze aanpak uitbreiden door gebruik te maken van Spark streaming of een andere stream-gegevens processor.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Wat zijn de Azure Cosmos DB Service quota's en doorvoer limieten?

Zie de Azure Cosmos DB [service quota's](concepts-limits.md) en de [volledige limieten per container en database](set-throughput.md#comparison-of-models) artikelen voor meer informatie.

## <a name="frequently-asked-questions-about-sql-api"></a><a id="sql-api-faq"></a>Veelgestelde vragen over SQL API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Hoe kan ik het ontwikkelen van de SQL-API te starten?

U moet zich eerst aanmelden voor een Azure-abonnement. Zodra u zich hebt aangemeld voor een Azure-abonnement, kunt u een SQL API-container toevoegen aan uw Azure-abonnement. Zie [een Azure Cosmos-database account maken](create-sql-api-dotnet.md#create-account)voor instructies over het toevoegen van een Azure Cosmos DB-account.

Er zijn [SDK's](sql-api-sdk-dotnet.md) beschikbaar voor .NET, Python, Node.js, JavaScript en Java. Ontwikkel aars kunnen ook gebruikmaken van de [rest HTTP-api's](/rest/api/cosmos-db/) om te communiceren met Azure Cosmos DB resources van verschillende platforms en talen.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Kan ik een aantal kant-en-klare voor beelden gebruiken om aan de slag te gaan?

Voor beelden voor de SQL API [.net](sql-api-dotnet-samples.md)-, [Java](https://github.com/Azure/azure-documentdb-java)-, [Node.js](sql-api-nodejs-samples.md)-en [python](sql-api-python-samples.md) -sdk's zijn beschikbaar op github.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>Ondersteunt de SQL API-data base schema-vrije gegevens?

Ja, met de SQL-API kunnen toepassingen wille keurige JSON-documenten opslaan zonder schema definities of hints. Gegevens zijn direct beschikbaar voor query's via de Azure Cosmos DB SQL-query interface.

### <a name="does-the-sql-api-support-acid-transactions"></a>Ondersteunt de SQL API ACID-trans acties?

Ja, de SQL-API ondersteunt cross-document transacties, uitgedrukt in Java script-opgeslagen procedures en triggers. Trans acties bevinden zich in een enkele partitie binnen elke container en worden uitgevoerd met zure semantiek als ' alles of niets ', geïsoleerd van andere gelijktijdig uitvoering van code-en gebruikers aanvragen. Als uitzonde ringen worden veroorzaakt door de uitvoering van Java script-toepassings code aan de server zijde, wordt de hele trans actie teruggedraaid. 

### <a name="what-is-a-container"></a>Wat is een container?

Een container is een groep documenten en de bijbehorende java script-toepassings logica. Een container is een factureer bare entiteit, waarbij de [kosten](performance-levels.md) worden bepaald door de door Voer en de gebruikte opslag. Containers kunnen een of meer partities of servers omvatten en kunnen worden geschaald om vrijwel onbeperkte volumes van opslag of door voer te verwerken.

* Voor de SQL-API wordt de resource een container genoemd.
* Voor de API van Cosmos DB voor MongoDB-accounts wordt een container toegewezen aan een verzameling.
* Voor Cassandra-en Table-API-accounts wordt een container toegewezen aan een tabel.
* Voor Gremlin-API-accounts wordt een container toegewezen aan een grafiek.

Containers zijn ook de facturerings entiteiten voor Azure Cosmos DB. Elke container wordt per uur gefactureerd, op basis van de ingerichte door Voer en gebruikte opslag ruimte. Zie [Azure Cosmos DB prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/)voor meer informatie.

### <a name="how-do-i-create-a-database"></a>Hoe maak ik een database?

U kunt data bases maken met behulp van de [Azure Portal](https://portal.azure.com), zoals beschreven in [een container toevoegen](create-sql-api-java.md#add-a-container), een van de [Azure Cosmos DB Sdk's](sql-api-sdk-dotnet.md)of de [rest-api's](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Hoe stel ik gebruikers en machtigingen in?

U kunt gebruikers en machtigingen maken met behulp van een van de [Cosmos DB-API-sdk's](sql-api-sdk-dotnet.md) of de rest- [api's](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>Wordt SQL door de SQL-API ondersteund?

De SQL-query taal die wordt ondersteund door SQL-API-accounts is een uitgebreide subset van de query functionaliteit die wordt ondersteund door SQL Server. De Azure Cosmos DB SQL-query taal biedt uitgebreide hiërarchische en relationele Opera tors en uitbreid baarheid via op Java script gebaseerde, door de gebruiker gedefinieerde functies (Udf's). Met de JSON-grammatica kunnen JSON-documenten worden gemodelleerd als structuren met gelabelde knoop punten. deze worden gebruikt door de Azure Cosmos DB automatische indexerings technieken en het SQL-query dialect van Azure Cosmos DB. Zie het [SQL-query][query] artikel voor meer informatie over het gebruik van SQL-grammatica.

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Ondersteunt de SQL-API SQL-aggregatie functies?

De SQL-API ondersteunt aggregatie met lage latentie op elke schaal via statistische functies `COUNT` , `MIN` ,, `MAX` `AVG` en `SUM` via de SQL-grammatica. Zie [statistische functies](sql-query-aggregate-functions.md)voor meer informatie.

### <a name="how-does-the-sql-api-provide-concurrency"></a>Hoe biedt de SQL-API gelijktijdigheid?

De SQL API ondersteunt optimistische gelijktijdigheids beheer (OCC) via HTTP-entiteit Tags of ETags. Elke SQL-API-resource heeft een ETag en de ETag wordt ingesteld op de server telkens wanneer een document wordt bijgewerkt. De ETag-kop en de huidige waarde zijn opgenomen in alle antwoord berichten. ETags kan worden gebruikt met de If-Match-header om de server toe te staan om te bepalen of een resource moet worden bijgewerkt. De If-Match waarde is de ETag-waarde die moet worden gecontroleerd. Als de ETag-waarde overeenkomt met de server ETag-waarde, wordt de resource bijgewerkt. Als de ETag niet langer actueel is, wordt de bewerking door de server geweigerd met de respons code ' HTTP 412-voor waarde voor fout '. De client haalt de resource vervolgens opnieuw op om de huidige ETag-waarde voor de resource te verkrijgen. Daarnaast kan ETags worden gebruikt in combi natie met de If-None-Match-header om te bepalen of een resource opnieuw moet worden opgehaald.

Als u optimistische gelijktijdigheid wilt gebruiken in .NET, gebruikt u de [AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition) -klasse. Zie [Program. cs](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/DocumentManagement/Program.cs) in het DocumentManagement-voor beeld op github voor een .net-voor beeld.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Hoe kan ik trans acties uit te voeren in de SQL-API?

De SQL-API ondersteunt in talen geïntegreerde trans acties via Java script-opgeslagen procedures en triggers. Alle database bewerkingen binnen scripts worden uitgevoerd onder snap shot-isolatie. Als het een container met één partitie is, wordt de uitvoering binnen het bereik van de container. Als de container is gepartitioneerd, wordt de uitvoering beperkt tot documenten met dezelfde partitie sleutel waarde in de container. Er wordt aan het begin van de transactie een schermopname van de documentversies (ETags) gemaakt en de transactie wordt alleen uitgevoerd als het script kan worden uitgevoerd. Als JavaScript een fout genereert, wordt de transactie teruggedraaid. Zie [Java script-programmering aan de server zijde voor Azure Cosmos DB](stored-procedures-triggers-udfs.md)voor meer informatie.

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Hoe kan ik documenten bulksgewijs invoegen in Cosmos DB?

U kunt op een van de volgende manieren bulksgewijs documenten in Azure Cosmos DB invoegen:

* Het hulp programma bulk-uitvoerder, zoals beschreven in het gebruik [](bulk-executor-java.md) van een bulk-uitvoerder [.net-bibliotheek](bulk-executor-dot-net.md)
* Het hulp programma voor gegevens migratie, zoals beschreven in [hulp programma voor database migratie voor Azure Cosmos DB](import-data.md).
* Opgeslagen procedures, zoals beschreven in [Java script-programmering aan server zijde voor Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>Ondersteunt de SQL-API bron koppeling in cache?

Ja, omdat Azure Cosmos DB een REST-service is, zijn resource koppelingen onveranderbaar en kunnen ze in de cache worden opgeslagen. SQL API-clients kunnen de header If-None-Match opgeven voor lees bewerkingen voor een resource-achtige document of container en vervolgens de lokale kopieën bijwerken nadat de server versie is gewijzigd.

### <a name="is-a-local-instance-of-sql-api-available"></a>Is er een lokaal exemplaar van SQL API beschikbaar?

Ja. De [Azure Cosmos DB-emulator](local-emulator.md) biedt een zeer betrouw bare emulatie van de Cosmos DB-service. Het ondersteunt functionaliteit die identiek is aan Azure Cosmos DB, waaronder ondersteuning voor het maken en opvragen van JSON-documenten, het inrichten en schalen van verzamelingen en het uitvoeren van opgeslagen procedures en triggers. U kunt toepassingen ontwikkelen en testen met behulp van de Azure Cosmos DB emulator en deze implementeren in azure op een wereld wijde schaal door één configuratie wijziging aan te brengen in het verbindings eindpunt voor Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Waarom worden lange drijvende-komma waarden in een document afgerond wanneer dit wordt weer gegeven vanuit Data Explorer in de portal.

Dit is een beperking van JavaScript. Java script maakt gebruik van drijvende-komma notatie nummers met dubbele precisie, zoals is opgegeven in IEEE 754. het kan veilig zijn om getallen tussen-(2<sup>53</sup> -1) en 2<sup>53</sup>-1 (bijvoorbeeld 9007199254740991) op te slaan.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Waar zijn de machtigingen toegestaan in de object hiërarchie?

Het maken van machtigingen met behulp van ResourceTokens is toegestaan op container niveau en de onderliggende items (zoals documenten, bijlagen). Dit betekent dat een poging om een machtiging te maken op de data base of een account niveau momenteel niet is toegestaan.

[azure-portal]: https://portal.azure.com
[query]: ./sql-query-getting-started.md

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over veelgestelde vragen in andere Api's:

* Veelgestelde vragen over [de API van Azure Cosmos DB voor MongoDb](mongodb-api-faq.md)
* Veelgestelde vragen over de [Gremlin-API in azure Cosmos DB](gremlin-api-faq.md)
* Veelgestelde vragen over [Cassandra-API in azure Cosmos DB](cassandra-faq.md)
* Veelgestelde vragen over [Table-API in azure Cosmos DB](table-api-faq.md)