---
title: Veelgestelde vragen over het beheerde exemplaar van Azure voor Apache Cassandra van de Azure Portal
description: Veelgestelde vragen over het beheerde exemplaar van Azure voor Apache Cassandra. Dit artikel heeft betrekking op vragen over het gebruik van beheerde instanties, voor delen, doorvoer limieten, ondersteunde regio's en andere configuratie details.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 1ba2b7d648c86912118b83a566bf2eb0800baee2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748484"
---
# <a name="frequently-asked-questions-about-azure-managed-instance-for-apache-cassandra-preview"></a>Veelgestelde vragen over het beheerde exemplaar van Azure voor Apache Cassandra (preview)

In dit artikel worden veelgestelde vragen over Azure Managed instance voor Apache Cassandra behandeld. U leert wanneer u beheerde instanties, hun voor delen, doorvoer limieten, ondersteunde regio's en de bijbehorende configuratie gegevens gebruikt.

> [!IMPORTANT]
> Een door Azure beheerde instantie voor Apache Cassandra is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="general-faq"></a>Veelgestelde algemene vragen

### <a name="what-are-the-benefits-azure-managed-instance-for-apache-cassandra"></a>Wat zijn de voor delen van het Azure Managed instance voor Apache Cassandra?

De Apache Cassandra-data base is de juiste keuze als u schaal baarheid en hoge Beschik baarheid nodig hebt zonder de prestaties te nadelig beïnvloeden. Het is een geweldig platform voor essentiële gegevens vanwege de lineaire schaal baarheid en de bewezen fout tolerantie op basis van hardware-of Cloud infrastructuur. Een door Azure beheerde instantie voor Apache Cassandra is een service voor het beheren van exemplaren van open source Apache Cassandra-data centers die zijn geïmplementeerd in Azure.

Het kan worden gebruikt ofwel volledig in de Cloud of als onderdeel van een hybride Cloud en een on-premises cluster. Deze service is een uitstekende keuze als u een nauw keurige configuratie en controle hebt die u in open-source Apache Cassandra hebt, zonder enige onderhouds overhead.

### <a name="why-should-i-use-this-service-instead-of-azure-cosmos-db-cassandra-api"></a>Waarom moet ik deze service gebruiken in plaats van Azure Cosmos DB Cassandra-API?

Het door Azure beheerde exemplaar voor Apache Cassandra wordt geleverd door het Azure Cosmos DB-team. Het is een zelfstandige beheerde service voor het implementeren, onderhouden en schalen van open-source Apache Cassandra-data centers en-clusters. [Azure Cosmos DB Cassandra-API](../cosmos-db/cassandra-introduction.md) daarentegen is een platform-as-a-service en biedt een compatibiliteits laag voor het Apache Cassandra wire-protocol. Als u verwacht dat het platform zich op exact dezelfde manier als een Apache Cassandra-cluster bevindt, moet u de beheerde exemplaar service kiezen. Raadpleeg voor meer informatie het artikel [Azure Managed instance voor Apache Cassandra Vs Azure Cosmos DB Cassandra-API](compare-cosmosdb-managed-instance.md) .

### <a name="is-azure-managed-instance-for-apache-cassandra-dependent-on-azure-cosmos-db"></a>Is een door Azure beheerd exemplaar voor Apache Cassandra afhankelijk van Azure Cosmos DB?

Nee, er is geen architectuur afhankelijkheid tussen het beheerde exemplaar van Azure voor Apache Cassandra en de back-end van de Azure Cosmos DB. 

#### <a name="can-i-deploy-azure-managed-instance-for-apache-cassandra-in-any-region"></a>Kan ik een beheerd exemplaar van Azure implementeren voor Apache Cassandra in elke regio?

Het beheerde exemplaar is tijdens de preview-periode beschikbaar in een beperkt aantal regio's.

### <a name="what-are-the-storage-and-throughput-limits-of-azure-managed-instance-for-apache-cassandra"></a>Wat zijn de opslag-en doorvoer limieten van een door Azure beheerde instantie voor Apache Cassandra?

Deze limieten zijn afhankelijk van de Sku's van de virtuele machine die u kiest.

### <a name="what-is-the-cost-of-azure-managed-instance-for-apache-cassandra"></a>Wat zijn de kosten van een Azure Managed instance voor Apache Cassandra?

De kosten voor het beheerde exemplaar zijn gebaseerd op de onderliggende VM-kosten, met een kleine opmaak. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/managed-instance-apache-cassandra/) voor meer informatie.

### <a name="can-i-use-yaml-file-settings-to-configure-behavior"></a>Kan ik YAML-Bestands instellingen gebruiken om gedrag te configureren?

Ja, u kunt YAML-bestands configuraties insluiten als onderdeel van een Azure Resource Manager-sjabloon implementatie.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>Hoe kan ik de infra structuur naast de door Voer controleren?

De [Prometheus](https://prometheus.io/docs/introduction/overview/) -server wordt gehost voor het bewaken van activiteiten in uw cluster en er wordt een eind punt weer gegeven. Dit houdt 10 minuten of 10 GB aan gegevens in bestand (waarbij de drempel waarde eerst wordt bereikt). Als u deze bewaking wilt gebruiken, moet u een [Federatie](https://prometheus.io/docs/prometheus/latest/federation/) en een geschikt hulp programma voor het dash board instellen, zoals Grafana.

### <a name="does-azure-managed-instance-for-apache-cassandra-provide-full-backups"></a>Biedt Azure Managed instance voor Apache Cassandra volledige back-ups?

Ja, het biedt volledige back-ups voor Azure Storage en herstel naar een nieuw cluster

### <a name="how-can-i-migrate-data-from-my-existing-apache-cassandra-cluster-to-azure-managed-instance-for-apache-cassandra"></a>Hoe kan ik gegevens migreren van mijn bestaande Apache Cassandra-cluster naar een beheerd exemplaar van Azure voor Apache Cassandra?

Azure Managed instance voor Apache Cassandra ondersteunt alle functies in Apache Cassandra om gegevens tussen data centers te repliceren en te streamen.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-azure-managed-instance-for-apache-cassandra"></a>Kan ik een on-premises Apache Cassandra-cluster koppelen met het door Azure beheerde exemplaar voor Apache Cassandra?

Ja, u kunt een hybride cluster met Azure configureren Virtual Network geïnjecteerde data centers die door de service zijn geïmplementeerd. Data Centers van beheerde exemplaren kunnen communiceren met on-premises data centers binnen dezelfde cluster ring.

### <a name="where-can-i-give-feedback-on-azure-managed-instance-for-apache-cassandra-features"></a>Waar kan ik feedback geven over Azure Managed instance voor Apache Cassandra-functies?

Feedback geven via [feedback](https://feedback.azure.com/forums/263030-azure-cosmos-db?category_id=398548) van de gebruiker met behulp van de categorie Managed Apache Cassandra.

Als u een probleem met uw account wilt oplossen, kunt u een [ondersteuningsaanvraag](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) indienen in Azure Portal.

## <a name="deployment-specific-faq"></a>Specifieke veelgestelde vragen over implementatie

### <a name="will-the-managed-instance-support-node-addition-cluster-status-and-node-status-commands"></a>Worden het toevoegen van knoop punten, cluster status en knooppunt status opdrachten ondersteund door het beheerde exemplaar?

Alle *alleen-lezen* Nodetool-opdrachten, zoals `status` beschikbaar via Azure cli. Bewerkingen zoals het toevoegen van een *knoop punt* zijn echter niet beschikbaar, omdat we de status van knoop punten in het beheerde exemplaar beheren. In de hybride modus kunt u verbinding maken met het cluster met behulp van *Nodetool*. Het gebruik van Nodetool wordt echter niet aanbevolen, omdat hierdoor het cluster kan worden gestabiliseerd. Het kan ook een SLA voor productie ondersteuning ongeldig maken met betrekking tot de status van de data centers van beheerde instanties in het cluster.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>Wat gebeurt er met verschillende instellingen voor de meta gegevens van de tabel?

De instellingen voor tabel-meta gegevens zoals het filter voor bloei, caching, inkomend herstel, gc_grace en compressie memtable_flush_period volledig worden ondersteund, net als bij elke zelf-hostende Apache Cassandra-omgeving.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over veelgestelde vragen in andere Api's:

* [Een beheerd exemplaar cluster maken op basis van de Azure Portal](create-cluster-portal.md)
* [Een beheerd Apache Spark cluster implementeren met Azure Databricks](deploy-cluster-databricks.md)
* [Een beheerd exemplaar van Azure beheren voor Apache Cassandra-resources met behulp van Azure CLI](manage-resources-cli.md)