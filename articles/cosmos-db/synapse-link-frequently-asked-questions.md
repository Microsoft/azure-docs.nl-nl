---
title: Veelgestelde vragen over Azure Synapse Link voor Azure Cosmos DB
description: Krijg antwoorden op veelgestelde vragen over de Synapse-koppeling voor Azure Cosmos DB op gebieden zoals facturering, analytische opslag, beveiliging, time to Live in een analytische opslag.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: 120bec65c92e2a13022682265b83bfe0f69d8ed0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592078"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>Veelgestelde vragen over Azure Synapse Link voor Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Met Azure Synapse link voor Azure Cosmos DB maakt u een nauwe integratie tussen Azure Cosmos DB en Azure Synapse Analytics. Hierdoor kunnen klanten bijna realtime analyses uitvoeren via hun operationele gegevens met volledige prestatie isolatie van hun transactionele werk belastingen en zonder een ETL-pijp lijn. Dit artikel beantwoordt veelgestelde vragen over Synapse Link voor Azure Cosmos DB.

## <a name="general-faq"></a>Veelgestelde algemene vragen

### <a name="is-azure-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>Wordt de Azure Synapse-koppeling ondersteund voor alle Api's van Azure Cosmos DB?

Azure Synapse-koppeling wordt ondersteund voor de Azure Cosmos DB SQL-API (core) en voor de Azure Cosmos DB-API voor MongoDB. 

### <a name="is-azure-synapse-link-supported-for-multi-region-azure-cosmos-db-accounts"></a>Wordt Azure Synapse-koppeling ondersteund voor accounts met meerdere regio's Azure Cosmos DB?

Ja, voor Azure Cosmos-accounts met meerdere regio's, worden de gegevens die zijn opgeslagen in de analytische opslag ook wereld wijd gedistribueerd. Analytische query's die vanuit Azure Synapse Analytics worden uitgevoerd, kunnen vanuit de dichtstbijzijnde lokale regio worden geleverd, ongeacht of er één of meer schrijfregio's zijn.

Wanneer u een multi-regio Azure Cosmos DB-account met ondersteuning voor analytische opslag wilt configureren, is het raadzaam om alle benodigde regio's toe te voegen op het moment dat het account wordt gemaakt.

### <a name="can-i-choose-to-enable-azure-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>Kan ik ervoor kiezen om de Azure Synapse-koppeling in te scha kelen voor slechts een bepaalde regio en niet voor alle regio's in een account met meerdere regio's?

Wanneer de koppeling van Azure Synapse is ingeschakeld voor een account met meerdere regio's, wordt de analytische opslag in alle regio's gemaakt. De onderliggende gegevens worden geoptimaliseerd voor de door Voer en transactionele consistentie in het transactionele archief.

### <a name="is-analytical-store-supported-in-all-azure-cosmos-db-regions"></a>Wordt het analytische archief ondersteund in alle Azure Cosmos DB regio's?

Ja.

### <a name="is-backup-and-restore-supported-for-azure-synapse-link-enabled-accounts"></a>Wordt back-up en herstel ondersteund voor accounts die zijn gekoppeld aan Azure Synapse?

Voor de containers waarop het analytische archief is ingeschakeld, wordt automatische back-up en herstel van uw gegevens in de analytische opslag op dit moment niet ondersteund. 

Wanneer de Synapse-koppeling is ingeschakeld voor een database account, blijven Azure Cosmos DB automatisch [back-ups](./online-backup-and-restore.md) van uw gegevens in het transactionele archief (alleen) van containers maken op het geplande back-upinterval, zoals altijd. Het is belang rijk te weten dat wanneer een container waarop een analytische opslag is ingeschakeld, wordt hersteld naar een nieuw account, de container wordt hersteld met alleen transactionele opslag en er geen analytische opslag is ingeschakeld. 

### <a name="can-i-disable-the-azure-synapse-link-feature-for-my-azure-cosmos-db-account"></a>Kan ik de functie Azure Synapse link voor mijn Azure Cosmos DB-account uitschakelen?

Nadat de Synapse Link-functie op accountniveau is ingeschakeld, kunt u deze momenteel niet uitschakelen. Als de Synapse Link-functie is ingeschakeld op accountniveau is dit niet van invloed op de facturering en zijn er geen containers met analytische opslag.

Als u de functie wilt uitschakelen, zijn er twee opties. De eerste is om het Azure Cosmos DB-account te verwijderen en een nieuw account te maken, waarbij u de gegevens eventueel kunt migreren. De tweede optie is om een ondersteuningsticket te openen om hulp te krijgen bij een gegevensmigratie naar een ander account.

### <a name="does-analytical-store-have-any-impact-on-cosmos-db-transactional-slas"></a>Heeft de analytische Store invloed op Cosmos DB transactionele Sla's?

Nee, er is geen impact.

## <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB-analytische archief

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>Kan ik het analytische archief op bestaande containers inschakelen?

Op dit moment kan de analytische opslag alleen worden ingeschakeld voor nieuwe containers (zowel in nieuwe als bestaande accounts).

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-db-containers-after-enabling-it-during-container-creation"></a>Kan ik de analytische opslag op mijn Azure Cosmos DB containers uitschakelen nadat ik deze heb ingeschakeld tijdens het maken van de container?

Op dit moment kan de analytische opslag niet worden uitgeschakeld op een Azure Cosmos DB-container nadat deze is ingeschakeld tijdens het maken van de container.

### <a name="is-analytical-store-supported-for-azure-cosmos-db-containers-with-autoscale-provisioned-throughput"></a>Wordt het analytische archief ondersteund voor Azure Cosmos DB containers met een door Voer ingericht voor automatisch schalen?

Ja, de analytische opslag kan worden ingeschakeld op containers met de door Voer ingericht voor automatisch schalen.

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>Is er een effect op Azure Cosmos DB transactionele opslag met het RUs-aanbod?

Azure Cosmos DB garandeert de prestaties van de isolatie tussen transactionele en analytische werk belastingen. Het inschakelen van het analytische archief in een container heeft geen invloed op de RU/s die zijn ingericht in de Azure Cosmos DB transactionele Store. De trans acties (Lees & schrijven) en opslag kosten voor de analytische opslag worden afzonderlijk in rekening gebracht. Zie de [prijzen voor de Azure Cosmos DB-analytische opslag](analytical-store-introduction.md#analytical-store-pricing) voor meer informatie.

### <a name="can-i-restrict-access-to-azure-cosmos-db-analytical-store"></a>Kan ik de toegang tot Azure Cosmos DB analytische archief beperken?

Ja, u kunt een [beheerd privé-eind punt](analytical-store-private-endpoints.md) configureren en netwerk toegang tot de analytische opslag beperken tot het virtuele Azure Synapse-netwerk dat wordt beheerd. Beheerde persoonlijke eind punten maken een persoonlijke koppeling naar uw analytische opslag. Dit persoonlijke eind punt beperkt ook schrijf toegang tot transactionele Store, onder andere Azure Data Services.

U kunt zowel transactionele opslag als persoonlijke eind punten van een analytische opslag toevoegen aan hetzelfde Azure Cosmos DB-account in een Azure Synapse Analytics-werk ruimte. Als u alleen analytische query's wilt uitvoeren, wilt u mogelijk alleen het analytische persoonlijke eind punt toewijzen.

### <a name="can-i-use-customer-managed-keys-with-the-azure-cosmos-db-analytical-store"></a>Kan ik door de klant beheerde sleutels gebruiken met de Azure Cosmos DB Analytical Store?

U kunt de gegevens op een automatische en transparante manier naadloos versleutelen over transactionele en analytische archieven met dezelfde door de klant beheerde sleutels. Voor het gebruik van door de klant beheerde sleutels met de Azure Cosmos DB analytische opslag is momenteel extra configuratie vereist voor uw account. Neem contact op met het [Azure Cosmos DB team](mailto:azurecosmosdbcmk@service.microsoft.com)  voor meer informatie.

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>Worden de bewerkingen voor verwijderen en bijwerken in de transactionele Store weer gegeven in de analytische opslag?

Ja, verwijderingen en updates van de gegevens in het transactionele archief worden weer gegeven in de analytische opslag. U kunt de Time to Live (TTL) configureren op de container om historische gegevens op te nemen, zodat de analytische opslag alle versies van items behoudt die voldoen aan de criteria van de analytische TTL. Zie het [overzicht van de analyse-TTL](analytical-store-introduction.md#analytical-ttl) voor meer informatie.

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>Kan ik verbinding maken met een analytische archief met andere analyse-engines dan Azure Synapse Analytics?

Het openen en uitvoeren van query's in de analytische opslag is alleen mogelijk met behulp van de verschillende runtimes die door Azure Synapse Analytics worden voorzien. De analytische opslag kan worden doorzocht en geanalyseerd met:

* Synapse Spark met volledige ondersteuning voor scala, Python, SparkSQL en C#. Synapse Spark neemt een centrale plaats in data engineering- en wetenschappelijke scenario's in
* Een serverloze SQL-pool met T-SQL-taal en ondersteuning voor bekende BI-hulpprogram ma's (bijvoorbeeld Power BI Premium, enzovoort)

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>Kan ik verbinding maken met het analytische archief vanuit Synapse SQL ingericht?

Op dit moment is het analytische archief niet toegankelijk vanuit Synapse SQL provisioned.

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>Kan ik de resultaten van de query aggregatie terugschrijven van Synapse terug naar de analytische opslag?

Analytische opslag is een alleen-lezen archief in een Azure Cosmos DB-container. U kunt de aggregatie resultaten dus niet rechtstreeks naar de analytische opslag schrijven, maar ze kunnen wel naar het Azure Cosmos DB transactionele archief van een andere container schrijven, die later kunnen worden gebruikt als een te leveren laag.

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>Is de automatische sync-replicatie van transactionele opslag naar de analytische opslag asynchroon of synchroon en wat zijn de latenties?

Latentie van automatische synchronisatie is doorgaans binnen twee minuten. In het geval van een gedeelde doorvoer database met een groot aantal containers, kan de latentie van de automatische synchronisatie van afzonderlijke containers hoger zijn en kan het tot vijf minuten duren. We willen graag meer informatie over hoe deze latentie past bij uw scenario's. Neem hiervoor contact op met het Azure Cosmos DB- [team](mailto:cosmosdbsynapselink@microsoft.com).

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>Zijn er scenario's waarbij de items uit het transactionele archief niet automatisch worden door gegeven aan de analytische opslag?

Als bepaalde items in uw container het [goed gedefinieerde schema voor analyse](analytical-store-introduction.md#analytical-schema)schenden, worden ze niet opgenomen in de analytische opslag. Als u scenario's hebt geblokkeerd door goed gedefinieerd schema voor analyse, kunt u het [Azure Cosmos DB-team](mailto:cosmosdbsynapselink@microsoft.com) een e-mail sturen.

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>Kan ik de gegevens in de analytische opslag anders partitioneren dan transactie opslag?

De gegevens in analytische opslag zijn gepartitioneerd op basis van de horizontale partitionering van shards in de transactionele opslag. U kunt momenteel geen andere partitioneringsstrategie voor de analytische opslag kiezen.

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>Kan ik de manier aanpassen of negeren waarop transactionele gegevens worden omgezet in de kolom indeling in de analytische opslag?

Op dit moment kunt u de gegevens items niet transformeren wanneer ze automatisch worden door gegeven vanuit het transactionele archief naar de analytische opslag. Als u scenario's hebt geblokkeerd door deze beperking, moet u een e-mail sturen naar het [Azure Cosmos DB team](mailto:cosmosdbsynapselink@microsoft.com).

### <a name="is-analytical-store-supported-by-terraform"></a>Wordt analytische opslag ondersteund voor Terraform?

Momenteel biedt Terraform geen ondersteuning voor containers voor analytische opslag. Raadpleeg [Terraform GitHub-problemen](https://github.com/hashicorp/terraform/issues) voor meer informatie.

## <a name="analytical-time-to-live-ttl"></a>Analytische time to Live (TTL)

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>Is TTL voor analytische gegevens die worden ondersteund op container-en item niveau?

Op dit moment kan TTL voor analytische gegevens alleen worden geconfigureerd op container niveau en is er geen ondersteuning voor het instellen van de analytische TTL op itemniveau.

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>Kan ik na het instellen van de analyse-TTL op container niveau in een Azure Cosmos DB container later overschakelen naar een andere waarde?

Ja, de analyse-TTL kan worden bijgewerkt naar een wille keurige geldige waarde. Zie het artikel over de [analytische TTL](analytical-store-introduction.md#analytical-ttl) voor meer informatie over de analyse-TTL.

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>Kan ik een item uit het analytische archief bijwerken of verwijderen nadat het TTL is verwijderd uit het transactionele archief?

Alle transactionele updates en verwijderingen worden gekopieerd naar het analytische archief, maar als het item uit het transactionele archief is verwijderd, kan het niet worden bijgewerkt in de analytische opslag. Zie het artikel over de [analytische TTL](analytical-store-introduction.md#analytical-ttl) voor meer informatie.

## <a name="billing"></a>Billing

### <a name="what-is-the-billing-model-of-azure-synapse-link-for-azure-cosmos-db"></a>Wat is het facturerings model van de Azure Synapse-koppeling voor Azure Cosmos DB?

Het facturerings model van de koppeling Azure Synapse bevat de kosten die zijn gemaakt met behulp van het Azure Cosmos DB-analytische archief en de Synapse-runtime. Voor meer informatie raadpleegt u de artikelen over prijzen van [Azure Cosmos DB Analytical Store](analytical-store-introduction.md#analytical-store-pricing) en [prijzen voor Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/) .

### <a name="what-is-the-billing-impact-if-i-enable-synapse-link-in-my-azure-cosmos-db-database-account"></a>Wat is de invloed op facturering als ik Synapse-koppeling in mijn Azure Cosmos DB database account inschakel?

Geen. Er worden alleen kosten in rekening gebracht wanneer u een container voor een analytische opslag maakt en begint met het laden van gegevens.


## <a name="security"></a>Beveiliging

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>Wat zijn de manieren om te verifiëren met de analytische opslag?

Verificatie met de analytische opslag is hetzelfde als een transactioneel archief. Voor een bepaalde data base kunt u verifiëren met de primaire of alleen-lezen sleutel. U kunt gebruikmaken van de gekoppelde service in azure Synapse Studio om te voor komen dat de Azure Cosmos DB sleutels in de Spark-notebooks worden geplakt. Toegang tot deze gekoppelde service is beschikbaar voor iedereen die toegang heeft tot de werk ruimte.

## <a name="synapse-run-times"></a>Synapse-uitvoerings tijden

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>Wat zijn de momenteel ondersteunde Synapse-uitvoerings tijden om toegang te krijgen tot Azure Cosmos DB Analytical Store?

|Azure Synapse runtime |Huidige ondersteuning |
|---------|---------|
|Azure Synapse Spark-Pools | Lezen, schrijven (via transactionele Store), tabel, tijdelijke weer gave |
|Azure Synapse serverloze SQL-groep    | Lezen, weer geven |
|Azure Synapse SQL ingericht   |  Niet beschikbaar |

### <a name="do-my-azure-synapse-spark-tables-sync-with-my-azure-synapse-serverless-sql-pool-tables-the-same-way-they-do-with-azure-data-lake"></a>Worden mijn Azure Synapse Spark-tabellen gesynchroniseerd met mijn Azure Synapse serverloze SQL-pool tabellen op dezelfde manier als Azure Data Lake?

Deze functie is momenteel niet beschikbaar.

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>Kan ik Structured streaming streamen vanuit de analytische opslag?

Momenteel wordt de ondersteuning Azure Cosmos DB voor het gebruik van gewijzigde feeds van het transactionele Archief door Spark Structured streaming ondersteund en wordt het nog niet ondersteund vanuit de analytische opslag.

### <a name="is-streaming-supported"></a>Wordt streaming ondersteund?

Het streamen van gegevens uit de analytische opslag wordt niet ondersteund.

## <a name="azure-synapse-studio"></a>Azure Synapse Studio

### <a name="in-the-azure-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>Hoe kan ik in azure Synapse Studio herkennen of ik verbinding heb met een Azure Cosmos DB-container terwijl het Analytics-archief is ingeschakeld?

Een Azure Cosmos DB container waarvoor een analytische opslag is ingeschakeld, heeft het volgende pictogram:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/analytical-store-icon.png" alt-text="Azure Cosmos DB container ingeschakeld met analytische archief-pictogram":::

Een transactionele opslag container wordt weer gegeven met het volgende pictogram:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/transactional-store-icon.png" alt-text="Azure Cosmos DB container ingeschakeld met transactionele Store-pictogram":::
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-azure-synapse-studio"></a>Hoe geeft u Azure Cosmos DB referenties door van Azure Synapse Studio?

Er worden momenteel Azure Cosmos DB referenties door gegeven tijdens het maken van de gekoppelde service door de gebruiker die toegang heeft tot de Azure Cosmos DB-data bases. Toegang tot de Store is beschikbaar voor andere gebruikers die toegang hebben tot de werk ruimte.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [voor delen van Azure Synapse link](synapse-link.md#synapse-link-benefits)

* Meer informatie over de [integratie tussen de Azure Synapse-koppeling en de Azure Cosmos DB](synapse-link.md#synapse-link-integration).
