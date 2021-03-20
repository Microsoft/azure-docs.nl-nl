---
title: Elastisch schalen met Cassandra-API in Azure Cosmos DB
description: Meer informatie over de beschik bare opties voor het schalen van een Azure Cosmos DB Cassandra-API-account en hun voor delen/nadelen
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: thvankra
ms.openlocfilehash: aad2e80598146be7b45a8a7b8a02cfe050163102
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340954"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Een Azure Cosmos DB Cassandra-API account elastisch schalen
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Er zijn diverse opties voor het verkennen van de elastische aard van de Azure Cosmos DB-API voor Cassandra. Om te begrijpen hoe effectief kan worden geschaald in Azure Cosmos DB, is het belang rijk om te begrijpen hoe u de juiste hoeveelheid aanvraag eenheden (RU/s) inricht om rekening te houden met de prestatie vereisten in uw systeem. Zie het artikel [aanvraag eenheden](request-units.md) voor meer informatie over aanvraag eenheden. 

Voor de Cassandra-API kunt u de kosten voor aanvraag eenheden ophalen voor afzonderlijke query's met behulp van de [.net-en Java-sdk's](./find-request-unit-charge-cassandra.md). Dit is handig bij het bepalen van de hoeveelheid RU/s die u moet inrichten in de service.

:::image type="content" source="./media/request-units/request-units.png" alt-text="Database bewerkingen gebruiken aanvraag eenheden" border="false":::

## <a name="handling-rate-limiting-429-errors"></a>Beperking van de verwerkings frequentie (429 fouten)

Azure Cosmos DB retourneert een frequentie-Limited (429) fouten als clients meer bronnen (RU/s) verbruiken dan de hoeveelheid die u hebt ingericht. Met de Cassandra-API in Azure Cosmos DB worden deze uitzonderingen vertaald in overbelastfouten in het systeemeigen Cassandra-protocol. 

Als uw systeem niet gevoelig is voor latentie, kan het voldoende zijn om de doorvoer snelheid te verwerken door nieuwe pogingen te gebruiken. Zie het voor [beeld van Java-code](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) voor het transparant afhandelen met behulp van de [Azure Cosmos DB extensie](https://github.com/Azure/azure-cosmos-cassandra-extensions) voor Cassandra-beleid voor [opnieuw proberen](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/) in Java. U kunt ook de [Spark-extensie](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) gebruiken om de frequentie limiet te verwerken.

## <a name="manage-scaling"></a>Schalen beheren

Als u de latentie wilt minimaliseren, is er een breed scala aan opties voor het beheren van de door Voer van schalen en inrichten (RUs) in de Cassandra-API:

* [Hand matig met behulp van de Azure Portal](#use-azure-portal)
* [Programmatisch met behulp van de functies van het besturings vlak](#use-control-plane)
* [Programmatisch met behulp van CQL-opdrachten met een specifieke SDK](#use-cql-queries)
* [Dynamisch door gebruik te maken van automatisch schalen](#use-autoscale)

In de volgende secties worden de voor-en nadelen van elke benadering uitgelegd. U kunt vervolgens besluiten over de beste strategie om de schaal behoeften van uw systeem, de totale kosten en efficiëntie behoeften voor uw oplossing te verdelen.

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>De Azure Portal gebruiken

U kunt de resources in Azure Cosmos DB Cassandra-API-account schalen met behulp van Azure Portal. Zie het artikel over het inrichten van de [door Voer voor containers en data bases](set-throughput.md)voor meer informatie. In dit artikel worden de relatieve voor delen beschreven van het instellen van door Voer op [Data Base](set-throughput.md#set-throughput-on-a-database) -of [container](set-throughput.md#set-throughput-on-a-container) niveau in de Azure Portal. De termen "data base" en "container" die in deze artikelen worden vermeld, worden respectievelijk toegewezen aan "sleutel ruimte" en "tabel" voor de Cassandra-API.

Het voor deel van deze methode is dat het een eenvoudig kant-en-klare manier is om de doorvoer capaciteit van de data base te beheren. Het nadeel is dat in veel gevallen het mogelijk is dat bepaalde niveaus van automatisering worden vereist voor de schaal baarheid en een hoge prestaties. In de volgende secties worden de relevante scenario's en methoden uitgelegd.

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>Het besturings vlak gebruiken

De API van Azure Cosmos DB voor Cassandra biedt de mogelijkheid om de door Voer programmatisch aan te passen met behulp van de verschillende functies van het controle vlak. Zie de artikelen [Azure Resource Manager](./templates-samples-cassandra.md), [Power shell](powershell-samples.md)en [Azure cli](cli-samples.md) voor hulp en voor beelden.

Het voor deel van deze methode is dat u de schaal aanpassing van resources naar boven of beneden kunt automatiseren op basis van een timer voor piek activiteit of peri Oden met een lage activiteit. Bekijk het voor beeld [hier](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) om te zien hoe u dit kunt doen met Azure functions en Power shell.

Een nadeel van deze benadering is dat u in realtime niet kunt reageren op veranderende schaal behoeften. In plaats daarvan moet u mogelijk gebruikmaken van de toepassings context in uw systeem, op het niveau van de client/SDK of met [automatisch schalen](provision-throughput-autoscale.md).

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>CQL-query's gebruiken met een specifieke SDK

U kunt het systeem dynamisch schalen met code door de [CQL Alter-opdrachten](cassandra-support.md#keyspace-and-table-options) voor de opgegeven Data Base of container uit te voeren.

Het voor deel van deze benadering is dat u op dynamische wijze op schaal behoeften kunt reageren op een aangepaste manier die aansluit bij uw toepassing. Met deze methode kunt u nog steeds gebruikmaken van de standaard kosten en-tarieven van de RU. Als de schaal behoefte van uw systeem voornamelijk voorspelbaar is (ongeveer 70% of meer), is het gebruik van SDK met CQL mogelijk een voordeligere methode voor automatisch schalen dan het gebruik van automatische schaling. Het nadeel van deze benadering is dat het lastig kan zijn om nieuwe pogingen te implementeren terwijl de frequentie beperking de latentie kan verg Roten.

## <a name="use-autoscale-provisioned-throughput"></a><a id="use-autoscale"></a>Ingerichte door Voer voor automatisch schalen gebruiken

Naast standaard (hand matig) of programmatische manier om door Voer in te richten, kunt u ook Azure Cosmos-containers configureren in ingerichte door Voer voor automatisch schalen. Automatisch schalen wordt voor de behoeften van het gebruik binnen de opgegeven RU-bereiken en direct aangepast zonder dat er in rekening wordt gebracht over Sla's. Zie het artikel [Azure Cosmos-containers en-data bases maken in automatisch schalen](provision-throughput-autoscale.md) voor meer informatie.

Het voor deel van deze benadering is dat het de eenvoudigste manier is om de schaal vereisten in uw systeem te beheren. Er worden geen beperkings beperkingen toegepast **binnen de geconfigureerde ru-bereiken**. Het nadeel is dat, als de schaal behoefte in uw systeem voorspelbaar is, de functie voor automatisch schalen een minder rendabele manier is om uw schaal behoeften te verwerken dan met behulp van het Bespoke-besturings vlak of het hierboven vermelde SDK-niveau.

Als u de maximale door Voer (RUs) voor automatisch schalen wilt instellen of wijzigen met behulp van CQL, gebruikt u het volgende (Vervang de spatie/tabel naam dienovereenkomstig):

```Bash
# to set max throughput (RUs) for autoscale at keyspace level:
create keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at keyspace level:
alter keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=4000;

# to set max throughput (RUs) for autoscale at table level:
create table <keyspace name>.<table name> (pk int PRIMARY KEY, ck int) WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at table level:
alter table <keyspace name>.<table name> WITH cosmosdb_autoscale_max_throughput=4000;
```

## <a name="next-steps"></a>Volgende stappen

- Ga aan de slag met het [maken van een Cassandra-API-account, -database en een tabel](create-cassandra-api-account-java.md) met behulp van een Java toepassing