---
title: Basisconcepten van Apache Spark
description: Inleiding tot kernconcepten voor Apache Spark in Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 51b2e8cd968c4c14777d196d90686b13158aef42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98120305"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Basisconcepten van Apache Spark in Azure Synapse Analytics

Apache Spark is een framework voor parallelle verwerking dat ondersteuning biedt voor in-memory verwerking om de prestaties van toepassingen voor de analyse van big data te verbeteren. Apache Spark in Azure Synapse Analytics is een van de implementaties van Apache Spark van Microsoft in de cloud. 

Met Azure Synapse kunt u eenvoudig Spark-functies maken en configureren in Azure. Azure Synapse biedt een andere implementatie van de Spark-functies die hier worden beschreven.

## <a name="spark-pools"></a>Spark-pools

Een serverloze Apache Spark-pool maakt u in Azure Portal. Een Spark-pool wordt, wanneer deze wordt geïnstantieerd, standaard gebruikt voor het maken van een Spark-exemplaar dat gegevens verwerkt. Wanneer er een Spark-pool wordt gemaakt, bestaat deze alleen als een set metagegevens;en er geen resources worden verbruikt, uitgevoerd of in rekening gebracht. Een Spark-groep bevat een reeks eigenschappen die de kenmerken van een Spark-exemplaar bepalen. Deze kenmerken omvatten, maar zijn niet beperkt tot naam, grootte, schaalgedrag, time to live.

Omdat er geen resourcekosten zijn verbonden aan het maken van Spark-pools, kunt u een onbeperkt aantal met verschillende configuraties maken. Er kunnen ook machtigingen op Spark-pools worden toegepast, zodat gebruikers alleen toegang hebben tot sommige pools.

U kunt het beste kleinere Spark-pools maken die kunnen worden gebruikt voor ontwikkeling en foutopsporing en vervolgens grotere maken voor het uitvoeren van productieworkloads.

In [Get started with Spark pools in Azure Synapse Analytics](../quickstart-create-apache-spark-pool-portal.md) (Aan de slag met Spark-pools in Azure Synapse Analytics) kunt u lezen hoe u een Spark-pool maakt en alle bijbehorende eigenschappen bekijkt

## <a name="spark-instances"></a>Spark-exemplaren

Spark-exemplaren worden gemaakt wanneer u verbinding maakt met een Spark-pool, een sessie maakt of een taak uitvoert. Aangezien meerdere gebruikers toegang tot één Spark-pool kunnen hebben, wordt er een nieuw Spark-exemplaar gemaakt voor elke gebruiker die ermee verbinding maakt. 

Wanneer u een tweede taak indient en er capaciteit in de pool is, heeft de bestaande Spark-instantie ook capaciteit. Vervolgens wordt de taak verwerkt door het bestaande exemplaar. Anders wordt er een nieuw Spark-exemplaar gemaakt als er capaciteit beschikbaar is op groepsniveau.

## <a name="examples"></a>Voorbeelden

### <a name="example-1"></a>Voorbeeld 1

- U maakt een Spark-pool met de naam SP1; deze heeft een vaste clustergrootte van 20 knooppunten.
- U verzendt een notebooktaak, J1, die gebruikmaakt van 10 knooppunten. Een Spark-exemplaar, SI1, wordt gemaakt om de taak te verwerken.
- U verzendt nu een andere taak, J2, die 10 knooppunten gebruikt omdat er nog capaciteit in de pool is. Het exemplaar, J2, wordt verwerkt door SI1.
- Als J2 11 knooppunten nodig had gehad, was er geen capaciteit in SP1 of SI1 geweest. Als J2 in dat geval afkomstig is van een notebook, wordt de taak geweigerd. Als J2 afkomstig is van een batch-taak, wordt deze in de wachtrij geplaatst.

### <a name="example-2"></a>Voorbeeld 2

- U maakt een Spark-poolaanroep SP2. Deze heeft automatische schaalaanpassing voor 10 tot 20 knooppunten
- U verzendt een notebooktaak, J1, die gebruikmaakt van 10 knooppunten. Een Spark-exemplaar, SI1, wordt gemaakt om de taak te verwerken.
- U verzendt nu een andere taak, J2, die 10 knooppunten gebruikt. Omdat er nog capaciteit in de pool is, wordt het exemplaar automatisch uitgebreid naar 20 knooppunten en verwerkt het J2.

### <a name="example-3"></a>Voorbeeld 3

- U maakt een Spark-pool met de naam SP1; deze heeft een vaste clustergrootte van 20 knooppunten.
- U verzendt een notebooktaak, J1, die gebruikmaakt van 10 knooppunten. Een Spark-exemplaar, SI1, wordt gemaakt om de taak te verwerken.
- Een andere gebruiker, U2, verzendt een taak, J3, die gebruikmaakt van 10 knooppunten. Een nieuw Spark-exemplaar, SI2, wordt gemaakt om de taak te verwerken.
- U verzendt nu een andere taak, J2, die 10 knooppunten gebruikt. Omdat er nog capaciteit in de pool en het exemplaar is, wordt J2 verwerkt door SI1.

## <a name="quotas-and-resource-constraints-in-apache-spark-for-azure-synapse"></a>Quota en resourcebeperkingen in Apache Spark voor Azure Synapse

### <a name="workspace-level"></a>Niveau van de werkruimte

Elke Azure Synapse-werkruimte wordt geleverd met een standaardquotum van vCores die kunnen worden gebruikt voor Apache Spark. Het quotum wordt opgesplitst in het gebruikersquotum en het gegevensstroomquotum, zodat geen van beide gebruikspatronen alle vCores in de werkruimte gebruikt. Het quotum verschilt, afhankelijk van het type abonnement, maar is gelijk verdeeld tussen de gebruikers en de gegevensstroom. Als u echter meer vCores aanvraagt dan nog in de werkruimte staan, krijgt u de volgende foutmelding:

```console
Failed to start session: [User] MAXIMUM_WORKSPACE_CAPACITY_EXCEEDED
Your Spark job requested 480 vcores.
However, the workspace only has xxx vcores available out of quota of yyy vcores.
Try reducing the numbers of vcores requested or increasing your vcore quota. Click here for more information - https://go.microsoft.com/fwlink/?linkid=213499
```

De koppeling in het bericht verwijst naar dit artikel.

In het volgende artikel wordt beschreven hoe u een toename van het vCore-quotum van de werkruimte aanvraagt.

- Selecteer 'Azure Synapse Analytics' als het servicetype.
- Selecteer in het venster Quotumgegevens de optie Apache Spark (vCore) per werkruimte

[Een capaciteitstoename aanvragen via Azure Portal](../../azure-portal/supportability/per-vm-quota-requests.md#request-a-standard-quota-increase-from-help--support)

### <a name="spark-pool-level"></a>Niveau van Spark-pool

Wanneer u een Spark-pool definieert, definieert u een quotum per gebruiker voor die pool. Als u meerdere notebooks of taken uitvoert of een mix van de twee, is het mogelijk dat u het poolquotum opmaakt. Als dit het geval is, wordt er een foutbericht zoals het volgende gegenereerd

```console
Failed to start session: Your Spark job requested xx vcores.
However, the pool is consuming yy vcores out of available zz vcores.Try ending the running job(s) in the pool, reducing the numbers of vcores requested, increasing the pool maximum size or using another pool
```

Om dit probleem op te lossen, moet u uw gebruik van de poolresources verminderen voordat u een nieuwe resource-aanvraag indient door een notebook of een taak uit te voeren.

## <a name="next-steps"></a>Volgende stappen

- [Azure Synapse Analytics](../index.yml)
- [Documentatie voor Apache Spark](https://spark.apache.org/docs/2.4.5/)