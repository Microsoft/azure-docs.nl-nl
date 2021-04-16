---
title: Veelgestelde vragen over Azure Synapse Link voor Azure Cosmos DB
description: Krijg antwoorden op veelgestelde vragen over Synapse Link voor Azure Cosmos DB op gebieden zoals facturering, analytische opslag, beveiliging, time to live in analytische opslag.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: synapse-cosmos-db
ms.openlocfilehash: 906651f8c48824e391879e0a579c6587231e7dfd
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483823"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>Veelgestelde vragen over Azure Synapse Link voor Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Synapse Link voor Azure Cosmos DB maakt een nauwe integratie tussen Azure Cosmos DB en Azure Synapse Analytics. Hiermee kunnen klanten bijna realtime analyses uitvoeren voor hun operationele gegevens met volledige prestatie-isolatie van hun transactionele workloads en zonder etl-pijplijn. Dit artikel beantwoordt veelgestelde vragen over Synapse Link voor Azure Cosmos DB.

## <a name="general-faq"></a>Veelgestelde algemene vragen

### <a name="is-azure-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>Wordt Azure Synapse Link ondersteund voor alle Azure Cosmos DB API's?

Azure Synapse Link wordt ondersteund voor de Azure Cosmos DB SQL (Core) API en voor de Azure Cosmos DB API voor MongoDB. 

### <a name="is-azure-synapse-link-supported-for-multi-region-azure-cosmos-db-accounts"></a>Wordt Azure Synapse Link ondersteund voor accounts met meerdere regio Azure Cosmos DB s?

Ja, voor Azure Cosmos-accounts met meerdere regio's worden de gegevens die zijn opgeslagen in de analytische opslag ook wereldwijd gedistribueerd. Analytische query's die vanuit Azure Synapse Analytics worden uitgevoerd, kunnen vanuit de dichtstbijzijnde lokale regio worden geleverd, ongeacht of er één of meer schrijfregio's zijn.

Wanneer u van plan bent om een account voor meerdere regio Azure Cosmos DB te configureren met ondersteuning voor analytische opslag, is het raadzaam om alle benodigde regio's toe te voegen op het moment dat het account wordt gemaakt.

### <a name="can-i-choose-to-enable-azure-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>Kan ik ervoor kiezen om Azure Synapse Link alleen in te stellen voor een bepaalde regio en niet voor alle regio's in een account voor meerdere regio's?

Wanneer Azure Synapse Link is ingeschakeld voor een account met meerdere regio's, wordt de analytische opslag in alle regio's gemaakt. De onderliggende gegevens zijn geoptimaliseerd voor doorvoer en transactionele consistentie in de transactionele opslag.

### <a name="is-analytical-store-supported-in-all-azure-cosmos-db-regions"></a>Wordt analytische opslag ondersteund in Azure Cosmos DB regio's?

Ja.

### <a name="is-backup-and-restore-supported-for-azure-synapse-link-enabled-accounts"></a>Wordt back-up en herstel ondersteund voor Azure Synapse Link-accounts?

Voor de containers waarin analytische opslag is ingeschakeld, wordt automatische back-up en herstel van uw gegevens in de analytische opslag op dit moment niet ondersteund. 

Wanneer Synapse Link is ingeschakeld voor een databaseaccount, blijft Azure Cosmos DB automatisch [](./online-backup-and-restore.md) back-ups maken van uw gegevens in de transactionele opslag (alleen) van containers met een gepland back-upinterval, zoals altijd. Het is belangrijk te weten dat wanneer een container met analytische opslag is ingeschakeld, wordt hersteld naar een nieuw account, de container wordt hersteld met alleen transactionele opslag en geen analytische opslag ingeschakeld. 

### <a name="can-i-disable-the-azure-synapse-link-feature-for-my-azure-cosmos-db-account"></a>Kan ik de functie Azure Synapse link uitschakelen voor mijn Azure Cosmos DB account?

Nadat de Synapse Link-functie op accountniveau is ingeschakeld, kunt u deze momenteel niet uitschakelen. Als de Synapse Link-functie is ingeschakeld op accountniveau is dit niet van invloed op de facturering en zijn er geen containers met analytische opslag.

Als u de functie wilt uitschakelen, zijn er twee opties. De eerste is om het Azure Cosmos DB-account te verwijderen en een nieuw account te maken, waarbij u de gegevens eventueel kunt migreren. De tweede optie is om een ondersteuningsticket te openen om hulp te krijgen bij een gegevensmigratie naar een ander account.

### <a name="does-analytical-store-have-any-impact-on-cosmos-db-transactional-slas"></a>Heeft analytische opslag gevolgen voor Cosmos DB transactionele SLA's?

Nee, er is geen impact.

## <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB analytische opslag

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>Kan ik analytische opslag inschakelen voor bestaande containers?

Op dit moment kan de analytische opslag alleen worden ingeschakeld voor nieuwe containers (zowel in nieuwe als bestaande accounts).

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-db-containers-after-enabling-it-during-container-creation"></a>Kan ik analytische opslag uitschakelen op mijn Azure Cosmos DB containers nadat ik deze heb inschakelen tijdens het maken van de container?

Op dit moment kan de analytische opslag niet worden uitgeschakeld op een Azure Cosmos DB-container nadat deze is ingeschakeld tijdens het maken van de container.

### <a name="is-analytical-store-supported-for-azure-cosmos-db-containers-with-autoscale-provisioned-throughput"></a>Wordt analytische opslag ondersteund voor Azure Cosmos DB containers met automatisch geschaalde inrichtende doorvoer?

Ja, de analytische opslag kan worden ingeschakeld voor containers met automatisch geschaalde inrichtende doorvoer.

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>Is er een effect op Azure Cosmos DB in de transactionele opslag inrichtende RUs?

Azure Cosmos DB garandeert prestatie-isolatie tussen de transactionele en analytische workloads. Het inschakelen van de analytische opslag op een container heeft geen invloed op de RU/s die zijn ingericht op Azure Cosmos DB transactionele opslag. De transacties (lezen & schrijven) en opslagkosten voor de analytische opslag worden afzonderlijk in rekening gebracht. Zie de [prijzen voor Azure Cosmos DB analytische opslag](analytical-store-introduction.md#analytical-store-pricing) voor meer informatie.

### <a name="can-i-restrict-network-access-to-azure-cosmos-db-analytical-store"></a>Kan ik de netwerktoegang tot de analytische Azure Cosmos DB beperken?

Ja, u kunt een beheerd [privé-eindpunt configureren](analytical-store-private-endpoints.md) en de netwerktoegang van analytische opslag beperken tot Azure Synapse beheerd virtueel netwerk. Beheerde privé-eindpunten maken een privékoppeling naar uw analytische opslag. 

U kunt privé-eindpunten voor zowel transactionele opslag als analytische opslag toevoegen aan hetzelfde Azure Cosmos DB account in een Azure Synapse Analytics werkruimte. Als u alleen analytische query's wilt uitvoeren, wilt u mogelijk alleen het analytische privé-eindpunt inschakelen in Synapse Analytics werkruimte.

### <a name="can-i-use-customer-managed-keys-with-the-azure-cosmos-db-analytical-store"></a>Kan ik door de klant beheerde sleutels gebruiken met Azure Cosmos DB analytische opslag?

U kunt de gegevens naadloos versleutelen in transactionele en analytische opslag met behulp van dezelfde door de klant beheerde sleutels op een automatische en transparante manier. Als u door de klant beheerde sleutels wilt gebruiken met de analytische opslag, moet u de door het systeem toegewezen beheerde identiteit van uw Azure Cosmos DB-account gebruiken in uw Azure Key Vault toegangsbeleid. Dit wordt hier [beschreven.](how-to-setup-cmk.md#using-managed-identity) Vervolgens moet u de analytische opslag in uw account kunnen inschakelen.

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>Worden verwijder- en updatebewerkingen in de transactionele opslag weerspiegeld in de analytische opslag?

Ja, verwijderen en updates van de gegevens in de transactionele opslag worden weergegeven in de analytische opslag. U kunt de Time to Live (TTL) op de container zodanig configureren dat historische gegevens worden opgeslagen, zodat de analytische opslag alle versies van items behoudt die voldoen aan de analytische TTL-criteria. Zie het [overzicht van analytische TTL voor](analytical-store-introduction.md#analytical-ttl) meer informatie.

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>Kan ik verbinding maken met analytische opslag vanuit andere analyse-engines dan Azure Synapse Analytics?

Het openen en uitvoeren van query's in de analytische opslag is alleen mogelijk met behulp van de verschillende runtimes die door Azure Synapse Analytics worden voorzien. De analytische opslag kan worden doorzocht en geanalyseerd met:

* Synapse Spark met volledige ondersteuning voor Scala, Python, SparkSQL en C#. Synapse Spark neemt een centrale plaats in data engineering- en wetenschappelijke scenario's in
* Serverloze SQL-pool met T-SQL-taal en ondersteuning voor vertrouwde BI-hulpprogramma's (bijvoorbeeld Power BI Premium, enzovoort)

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>Kan ik verbinding maken met analytische opslag vanuit Synapse SQL ingericht?

Op dit moment kan de analytische opslag niet worden gebruikt vanuit Synapse SQL ingericht.

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>Kan ik de resultaten van de queryaggregatie van Synapse terugschrijven naar de analytische opslag?

Analytische opslag is een alleen-lezenopslag in een Azure Cosmos DB container. U kunt de aggregatieresultaten dus niet rechtstreeks terugschrijven naar de analytische opslag, maar u kunt ze wel schrijven naar de Azure Cosmos DB transactionele opslag van een andere container, die later kan worden gebruikt als een afhandelingslaag.

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>Is de automatische synchronisatie van transactionele opslag naar de analytische opslag asynchroon of synchroon en wat zijn de latentie?

Latentie voor automatische synchronisatie is doorgaans binnen 2 minuten. In het geval van een gedeelde doorvoerdatabase met een groot aantal containers kan de latentie voor automatische synchronisatie van afzonderlijke containers hoger zijn en kan dit tot vijf minuten duren. We willen graag meer weten over hoe deze latentie in uw scenario's past. Neem dan contact op met het [Azure Cosmos DB team](mailto:cosmosdbsynapselink@microsoft.com).

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>Zijn er scenario's waarin de items uit de transactionele opslag niet automatisch worden doorgegeven aan de analytische opslag?

Als specifieke items in uw container het [goed gedefinieerde schema](analytical-store-introduction.md#analytical-schema)voor analyse schenden, worden ze niet opgenomen in de analytische opslag. Als er scenario's zijn geblokkeerd door een goed gedefinieerd schema voor analyse, stuur dan een e-mail naar Azure Cosmos DB [team](mailto:cosmosdbsynapselink@microsoft.com) voor hulp.

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>Kan ik de gegevens in analytische opslag anders partitioneren dan transactionele opslag?

De gegevens in analytische opslag zijn gepartitioneerd op basis van de horizontale partitionering van shards in de transactionele opslag. U kunt momenteel geen andere partitioneringsstrategie voor de analytische opslag kiezen.

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>Kan ik de manier waarop transactionele gegevens worden getransformeerd in kolomindeling in de analytische opslag aanpassen of overschrijven?

Momenteel kunt u de gegevensitems niet transformeren wanneer ze automatisch worden doorgegeven vanuit de transactionele opslag naar de analytische opslag. Als u scenario's hebt geblokkeerd door deze beperking, stuur dan een e-mail [naar Azure Cosmos DB team](mailto:cosmosdbsynapselink@microsoft.com).

### <a name="is-analytical-store-supported-by-terraform"></a>Wordt analytische opslag ondersteund voor Terraform?

Momenteel biedt Terraform geen ondersteuning voor containers voor analytische opslag. Raadpleeg [Terraform GitHub-problemen](https://github.com/hashicorp/terraform/issues) voor meer informatie.

## <a name="analytical-time-to-live-ttl"></a>Analytical Time to Live (TTL)

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>Wordt TTL voor analytische gegevens ondersteund op zowel container- als itemniveau?

Op dit moment kan TTL voor analytische gegevens alleen worden geconfigureerd op container niveau en is er geen ondersteuning voor het instellen van de analytische TTL op itemniveau.

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>Kan ik na het instellen van de analytische TTL op containerniveau op Azure Cosmos DB container later een andere waarde krijgen?

Ja, analytische TTL kan worden bijgewerkt naar een geldige waarde. Zie het [artikel Analytical TTL (Analytische TTL)](analytical-store-introduction.md#analytical-ttl) voor meer informatie over analytische TTL.

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>Kan ik een item uit de analytische opslag bijwerken of verwijderen nadat het TTL-bestand uit de transactionele opslag is verwijderd?

Alle transactionele updates en verwijderen worden gekopieerd naar de analytische opslag, maar als het item uit de transactionele opslag is verwijderd, kan het niet worden bijgewerkt in de analytische opslag. Zie het artikel [Analytical TTL (Analytische TTL) voor meer](analytical-store-introduction.md#analytical-ttl) informatie.

## <a name="billing"></a>Billing

### <a name="what-is-the-billing-model-of-azure-synapse-link-for-azure-cosmos-db"></a>Wat is het factureringsmodel van Azure Synapse Link voor Azure Cosmos DB?

Het factureringsmodel van Azure Synapse Link omvat de kosten die worden gemaakt met behulp van Azure Cosmos DB analytische opslag en de Synapse-runtime. Zie de artikelen Prijzen voor [analytische Azure Cosmos DB en](analytical-store-introduction.md#analytical-store-pricing) Azure Synapse Analytics voor [meer](https://azure.microsoft.com/pricing/details/synapse-analytics/) informatie.

### <a name="what-is-the-billing-impact-if-i-enable-synapse-link-in-my-azure-cosmos-db-database-account"></a>Wat zijn de gevolgen voor de facturering als ik Synapse Link in mijn Azure Cosmos DB databaseaccount?

Geen. Er worden alleen kosten in rekening gebracht wanneer u een container met analytische opslag maakt en begint met het laden van gegevens.


## <a name="security"></a>Beveiliging

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>Wat zijn de manieren om te verifiëren met de analytische opslag?

Verificatie met de analytische opslag is hetzelfde als een transactionele opslag. Voor een bepaalde database kunt u verifiëren met de primaire of alleen-lezensleutel. U kunt gebruikmaken van de gekoppelde service in Azure Synapse Studio om te voorkomen dat de sleutels Azure Cosmos DB in de Spark-notebooks. Toegang tot deze gekoppelde service is beschikbaar voor iedereen die toegang heeft tot de werkruimte.

Wanneer u serverloze SQL-pools van Synapse gebruikt, kunt u een query uitvoeren op de analytische opslag van Azure Cosmos DB door vooraf SQL-referenties te maken voor het opslaan van de accountsleutels en deze te verwijzen in de functie OPENROWSET. Zie Het artikel [Query's uitvoeren met een serverloze SQL-pool in Azure Synapse Link voor meer](../synapse-analytics/sql/query-cosmos-db-analytical-store.md) informatie.

## <a name="synapse-run-times"></a>Synapse-run times

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>Wat zijn de momenteel ondersteunde Synapse-run times voor toegang Azure Cosmos DB analytische opslag?

|Azure Synapse runtime |Huidige ondersteuning |
|---------|---------|
|Azure Synapse Spark-pools | Lezen, Schrijven (via transactionele opslag), Tabel, Tijdelijke weergave |
|Azure Synapse serverloze SQL-pool    | Lezen, Weergeven |
|Azure Synapse SQL ingericht   |  Niet beschikbaar |

### <a name="do-my-azure-synapse-spark-tables-sync-with-my-azure-synapse-serverless-sql-pool-tables-the-same-way-they-do-with-azure-data-lake"></a>Worden mijn Azure Synapse Spark-tabellen gesynchroniseerd met mijn Azure Synapse serverloze SQL-pooltabellen op dezelfde manier als met Azure Data Lake?

Deze functie is momenteel niet beschikbaar.

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>Kan ik Spark Structured Streaming vanuit de analytische opslag doen?

Momenteel wordt ondersteuning voor spark structured streaming Azure Cosmos DB geïmplementeerd met behulp van de functionaliteit van de wijzigingsfeed van de transactionele opslag en wordt deze nog niet ondersteund vanuit de analytische opslag.

### <a name="is-streaming-supported"></a>Wordt streaming ondersteund?

We bieden geen ondersteuning voor het streamen van gegevens uit de analytische opslag.

## <a name="azure-synapse-studio"></a>Azure Synapse Studio

### <a name="in-the-azure-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>Hoe herkent Azure Synapse in Azure Synapse Studio of ik ben verbonden met een Azure Cosmos DB container waarop de analyseopslag is ingeschakeld?

Een Azure Cosmos DB container ingeschakeld met analytische opslag heeft het volgende pictogram:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/analytical-store-icon.png" alt-text="Azure Cosmos DB container ingeschakeld met analytische opslag- pictogram":::

Een transactionele opslagcontainer wordt weergegeven met het volgende pictogram:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/transactional-store-icon.png" alt-text="Azure Cosmos DB container ingeschakeld met transactioneel winkelpictogram":::
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-azure-synapse-studio"></a>Hoe kunt u de Azure Cosmos DB van Azure Synapse Studio doorgeven?

Momenteel Azure Cosmos DB doorgegeven tijdens het maken van de gekoppelde service door de gebruiker die toegang heeft tot de Azure Cosmos DB databases. Toegang tot die store is beschikbaar voor andere gebruikers die toegang hebben tot de werkruimte.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [de voordelen van Azure Synapse Link](synapse-link.md#synapse-link-benefits)

* Meer informatie over [de integratie tussen Azure Synapse Link en Azure Cosmos DB](synapse-link.md#synapse-link-integration).
