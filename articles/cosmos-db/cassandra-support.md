---
title: Door Azure Cosmos DB Cassandra API ondersteunde Apache Cassandra-functies
description: Meer informatie over de ondersteuning van Apache Cassandra-functies in Azure Cosmos DB Cassandra-API
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: ecf4229c95ff9103cd27fd161fdd19c9e7a0f76b
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636959"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Door Azure Cosmos DB Cassandra API ondersteunde Apache Cassandra-functies 
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft. U kunt communiceren met de Azure Cosmos DB Cassandra-API via open-source Cassandra-[clientstuurprogramma's](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) die compatibel zijn met de [wire-protocol](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) van CQL Binary Protocol v4. 

Door de Azure Cosmos DB Cassandra API te gebruiken, kunt u profiteren van de voordelen van de Apache Cassandra API’s en van de enterprise-mogelijkheden die Azure Cosmos DB biedt. De enterprise-mogelijkheden zijn onder andere [wereldwijde distributie](distribute-data-globally.md), [automatische scale-out partitionering](cassandra-partitioning.md), beschikbaarheid- en latentiegaranties, versleuteling van gegevens in rust, back-ups en nog veel meer.

## <a name="cassandra-protocol"></a>Cassandra-protocol 

De Azure Cosmos DB Cassandra-API is compatibel met de CQL (Cassandra Query Language) v3.11 API (compatibel met de eerdere versie 2.x). De ondersteunde CQL-opdrachten, hulpprogramma's, beperkingen en uitzonderingen worden hieronder vermeld. Elk clientstuurprogramma dat deze protocollen kent, kan verbinding maken met Azure Cosmos DB Cassandra-API.

## <a name="cassandra-driver"></a>Cassandra-stuurprogramma

De volgende versies van de Cassandra-stuurprogramma's worden ondersteund door Azure Cosmos DB Cassandra-API:

* [Java 3.5+](https://github.com/datastax/java-driver)  
* [C# 3.5+](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5+](https://github.com/datastax/nodejs-driver)  
* [Python 3.15+](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 

## <a name="cql-data-types"></a>CQL-gegevenstypen 

Azure Cosmos DB Cassandra-API ondersteunt de volgende CQL-gegevenstypen:

|Opdracht  |Ondersteund |
|---------|---------|
| ascii  | Ja |
| bigint  | Ja |
| blob  | Ja |
| booleaans  | Ja |
| counter  | Ja |
| date  | Ja |
| decimal  | Ja |
| double  | Ja |
| float  | Ja |
| frozen  | Ja |
| inet  | Ja |
| int  | Ja |
| list  | Ja |
| set  | Ja |
| smallint  | Ja |
| tekst  | Ja |
| tijd  | Ja |
| tijdstempel  | Ja |
| timeuuid  | Ja |
| tinyint  | Ja |
| tuple  | Ja |
| uuid  | Ja |
| varchar  | Ja |
| varint  | Ja |
| tuples | Ja | 
| udts  | Ja |
| map | Ja |

Statisch wordt ondersteund als declaratie van het gegevenstype.

## <a name="cql-functions"></a>CQL-functies

Azure Cosmos DB Cassandra-API ondersteunt de volgende CQL-functies:

|Opdracht  |Ondersteund |
|---------|---------|
| Token * | Ja |
| ttl | Ja |
| writetime | Ja |
| cast | Nee |

> [!NOTE]
> \* Cassandra-API biedt ondersteuning voor token als een projectie/selector, en staat token(pk) alleen toe aan de linkerkant van een WHERE-component. `WHERE token(pk) > 1024` wordt bijvoorbeeld ondersteund, maar `WHERE token(pk) > token(100)` wordt **niet** ondersteund.



Statistische functies:

|Opdracht  |Ondersteund |
|---------|---------|
| gemiddeld | Ja |
| count | Ja |
| min | Ja |
| max | Ja |
| sum | Ja |

> [!NOTE]
> Statistische functies werken in gewone kolommen, maar aggregaties in clusterkolommen worden **niet** ondersteund.


Blob-conversiefuncties:
 
|Opdracht  |Ondersteund |
|---------|---------|
| typeAsBlob(value)   | Ja |
| blobAsType(value) | Ja |


UUID- en timeuuid-functies:
 
|Opdracht  |Ondersteund |
|---------|---------|
| dateOf()  | Ja |
| now()  | Ja |
| minTimeuuid()  | Ja |
| unixTimestampOf()  | Ja |
| toDate(timeuuid)  | Ja |
| toTimestamp(timeuuid)  | Ja |
| toUnixTimestamp(timeuuid)  | Ja |
| toDate(timestamp)  | Ja |
| toUnixTimestamp(timestamp)  | Ja |
| toTimestamp(date)  | Ja |
| toUnixTimestamp(date) | Ja |


  
## <a name="cql-commands"></a>CQL-opdrachten

Azure Cosmos DB ondersteunt de volgende databaseopdrachten op Cassandra-API-accounts.

|Opdracht  |Ondersteund |
|---------|---------|
| ALLOW FILTERING | Ja |
| ALTER KEYSPACE | N.v.t (PaaS-service, replicatie intern beheerd)|
| ALTER MATERIALIZED VIEW | Nee |
| ALTER ROLE | Nee |
| ALTER TABLE | Ja |
| ALTER TYPE | Nee |
| ALTER USER | Nee |
| BATCH | Ja (alleen niet-geregistreerde batches)|
| COMPACT STORAGE | N.v.t. (PaaS-service) |
| CREATE AGGREGATE | Nee | 
| CREATE CUSTOM INDEX (SASI) | Nee |
| CREATE INDEX | Ja (zonder [de indexnaam op te geven](cassandra-secondary-index.md), en indexen op clustersleutels of een volledige FROZEN-verzameling worden niet ondersteund) |
| CREATE FUNCTION | Nee |
| CREATE KEYSPACE (replicatie-instellingen genegeerd) | Ja |
| CREATE MATERIALIZED VIEW | Nee |
| CREATE TABLE | Ja |
| CREATE TRIGGER | Nee |
| CREATE TYPE | Ja |
| CREATE ROLE | Nee |
| CREATE USER (afgeschaft in systeemeigen Apache Cassandra) | Nee |
| DELETE | Ja |
| DELETE (lichtgewicht transacties met IF CONDITION)| Ja |
| DISTINCT | Nee |
| DROP AGGREGATE | Nee |
| DROP FUNCTION | Nee |
| DROP INDEX | Ja |
| DROP KEYSPACE | Ja |
| DROP MATERIALIZED VIEW | Nee |
| DROP ROLE | Nee |
| DROP TABLE | Ja |
| DROP TRIGGER | Nee | 
| DROP TYPE | Ja |
| DROP USER (afgeschaft in systeemeigen Apache Cassandra) | Nee |
| GRANT | Nee |
| INSERT | Ja |
| INSERT (lichtgewicht transacties met IF CONDITION)| Ja |
| LIST PERMISSIONS | Nee |
| LIST ROLES | Nee |
| LIST USERS (afgeschaft in systeemeigen Apache Cassandra) | Nee |
| REVOKE | Nee |
| SELECT | Ja |
| SELECT (lichtgewicht transacties met IF CONDITION)| Nee |
| UPDATE | Ja |
| UPDATE (lichtgewicht transacties met IF CONDITION)| Nee |
| TRUNCATE | Nee |
| USE | Ja |

## <a name="json-support"></a>JSON-ondersteuning
|Opdracht  |Ondersteund |
|---------|---------|
| SELECT JSON | Ja |
| INSERT JSON | Ja |
| fromJson() | Nee |
| toJson() | Nee |


## <a name="cassandra-api-limits"></a>Limieten voor Cassandra-API

Azure Cosmos DB Cassandra-API heeft geen limiet wat betreft de grootte van gegevens die in een tabel worden opgeslagen. Er kunnen honderden terabytes of petabytes aan gegevens worden opgeslagen terwijl de partitiesleutellimieten worden gerespecteerd. Evenzo heeft elke entiteit of rij-equivalent geen limieten voor het aantal kolommen. De totale omvang van de entiteit mag echter niet groter zijn dan 2 MB. De gegevens per partitiesleutel mogen niet groter zijn dan 20 GB, zoals in alle andere API's.

## <a name="tools"></a>Hulpprogramma's 

Azure Cosmos DB Cassandra-API is een beheerd serviceplatform. Het vereist geen beheeroverhead of hulpprogramma's zoals Garbage Collector, Java Virtual Machine (JVM) en nodetool om het cluster te beheren. Het ondersteunt hulpprogramma’s zoals cqlsh, dat binaire CQLv4-compatibiliteit gebruikt. 

* Andere ondersteunde mechanismen voor het beheren van het account zijn Data Explorer, metrische gegevens, logboekdiagnose, PowerShell en CLI in de Azure-portal.

## <a name="hosted-cql-shell-preview"></a>Gehoste CQL-shell (preview)

U kunt een gehoste Cassandra-shell (CQLSH v5.0.1) rechtstreeks vanuit Data Explorer openen in de [Azure-portal](data-explorer.md) of in [Azure Cosmos Explorer](https://cosmos.azure.com/). Voordat u de CQL-shell inschakelt, moet u de functie [Notebooks](enable-notebooks.md) inschakelen in uw account (als deze nog niet is ingeschakeld, wordt u gevraagd dit te doen wanneer u klikt op `Open Cassandra Shell`). Controleer de gemarkeerde notitie in [Notebooks inschakelen voor Azure Cosmos DB-accounts](enable-notebooks.md) voor ondersteunde Azure-regio's.

:::image type="content" source="./media/cassandra-support/cqlsh.png" alt-text="CQLSH openen":::

U kunt ook verbinding maken met de Cassandra-API in Azure Cosmos DB met behulp van de CQLSH die is geïnstalleerd op een lokale computer. De API wordt geleverd met Apache Cassandra 3.1.1 en is meteen klaar voor gebruik door de omgevingsvariabelen in te stellen. De volgende secties bevatten instructies voor het installeren, configureren en verbinding maken met de Cassandra-API in Azure Cosmos DB, in Windows of Linux met behulp van CQLSH.

> [!NOTE]
> Verbindingen met Azure Cosmos DB Cassandra-API werken niet met DSE-versies (DataStax Enterprise) van CQLSH. Zorg ervoor dat u alleen de open-source Apache Cassandra-versies van CQLSH gebruikt bij het verbinding maken met Cassandra-API. 

**Windows:**

Als u Windows gebruikt, raden we u aan om het [Windows-bestandssysteem voor Linux](/windows/wsl/install-win10#install-the-windows-subsystem-for-linux) in te schakelen. U kunt vervolgens de onderstaande Linux-opdrachten volgen.

**UNIX/Linux/Mac:**

```bash
# Install default-jre and default-jdk
sudo apt install default-jre
sudo apt-get update
sudo apt install default-jdk

# Import the Baltimore CyberTrust root certificate:
curl https://cacert.omniroot.com/bc2025.crt > bc2025.crt
keytool -importcert -alias bc2025ca -file bc2025.crt

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to Azure Cosmos DB API for Cassandra:
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl

```

Alle CRUD-bewerkingen die worden uitgevoerd via een SDK die compatibel is met CQL v4 retourneren, extra informatie over fouten en verbruikte aanvraageenheden. De opdrachten DELETE en UPDATE moeten worden afgehandeld met resourcebeheer om te zorgen voor het meest efficiënte gebruik van de ingerichte doorvoer.

* Let wel: de waarde gc_grace_seconds moet nul zijn als deze is opgegeven.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($"CustomPayload:  {key}: {value}"); 
        } 
```

## <a name="consistency-mapping"></a>Consistentietoewijzing 

Azure Cosmos DB Cassandra-API biedt een keuze aan consistentie voor leesbewerkingen.  [Hier](./cassandra-consistency.md#mapping-consistency-levels) vindt u de details van de consistentietoewijzing.

## <a name="permission-and-role-management"></a>Machtigings- en rolbeheer

Azure Cosmos DB biedt Azure-RBAC (op rollen gebaseerd toegangsbeheer voor Azure) voor inrichten, wisselen van sleutels, weergeven van metrische gegevens, en wachtwoorden voor lezen/schrijven, en wachtwoorden met het kenmerk alleen-lezen die kunnen worden verkregen via [Azure Portal](https://portal.azure.com). Azure Cosmos DB biedt geen ondersteuning voor rollen voor CRUD-activiteiten.

## <a name="keyspace-and-table-options"></a>Opties voor Keyspace en tabel

De opties region name, class, replication_factor en datacenter in de opdracht CREATE KEYSPACE worden momenteel genegeerd. Het systeem maakt gebruik van de onderliggende replicatiemethode voor [globale distributie](global-dist-under-the-hood.md) van Azure Cosmos DB om de regio's toe te voegen. Als u wilt dat gegevens in meerdere regio's aanwezig zijn, kunt u dit op accountniveau inschakelen met PowerShell, CLI of de portal. Raadpleeg het artikel [Regio's toevoegen](how-to-manage-database-account.md#addremove-regions-from-your-database-account) voor meer informatie. Durable_writes kan niet worden uitgeschakeld omdat in Azure Cosmos DB elke schrijfbewerking gegarandeerd duurzaam is. Met Azure Cosmos DB worden de gegevens in elke regio gerepliceerd in de hele replicaset die bestaat uit vier replica’s, en de [configuratie](global-dist-under-the-hood.md) van deze replicaset kan niet worden gewijzigd.
 
Alle opties worden genegeerd bij het maken van de tabel, met uitzondering van gc_grace_seconds, die moeten worden ingesteld op nul.
Keyspace en tabel hebben een extra optie genaamd cosmosdb_provisioned_throughput, met een minimumwaarde van 400 RU/s. De Keyspace-doorvoer staat delen van doorvoer in meerdere tabellen toe, en is handig voor scenario’s waarbij geen van de tabellen gebruikmaakt van de ingerichte doorvoer. De opdracht Alter Table maakt het wijzigen van ingerichte doorvoer mogelijk in alle regio’s. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```
## <a name="secondary-index"></a>Secundaire index
Cassandra-API ondersteunt secundaire indexen voor alle gegevenstypen, met uitzondering van de typen bevroren verzameling, decimaal en variant. 

## <a name="usage-of-cassandra-retry-connection-policy"></a>Gebruik van het Cassandra-beleid voor opnieuw proberen

Azure Cosmos DB is een systeem voor resourcebeheer. Dit betekent dat u een bepaald aantal bewerkingen in een opgegeven seconde kunt uitvoeren, op basis van de aanvraageenheden die bij deze bewerkingen worden verbruikt. Als een toepassing deze limiet in een opgegeven seconde overschrijdt, gelden er beperkingen voor aanvragen en worden er uitzonderingen gegenereerd. Met de Cassandra-API in Azure Cosmos DB worden deze uitzonderingen vertaald in overbelastfouten in het systeemeigen Cassandra-protocol. De [Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper)- en [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions)-extensies worden geboden om ervoor te zorgen dat de toepassing aanvragen kan onderscheppen en opnieuw kan proberen wanneer er sprake is van een beperking. Bekijk ook de Java-codevoorbeelden voor [versie 3](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) en [versie 4](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4) van de Datastax-stuurprogramma's, wanneer u verbinding maakt met de Cassandra-API in Azure Cosmos DB. Als u andere SDK's gebruikt om toegang te krijgen tot de Cassandra-API in Azure Cosmos DB, maakt u verbindingsbeleid om deze uitzonderingen opnieuw te proberen.

## <a name="next-steps"></a>Volgende stappen

- Ga aan de slag met het [maken van een Cassandra-API-account, -database en een tabel](create-cassandra-api-account-java.md) met behulp van een Java toepassing