---
title: Verschillen tussen het beheerde exemplaar van Azure voor Apache Cassandra en Azure Cosmos DB Cassandra-API
description: Meer informatie over de verschillen tussen het beheerde Azure-exemplaar voor Apache Cassandra en Cassandra-API in Azure Cosmos DB. U leert ook de voor delen van elk van deze services en wanneer u deze kunt kiezen.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 3050dda4b3c0e1a05d751a4f5969bba69ad0506d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748527"
---
# <a name="differences-between-azure-managed-instance-for-apache-cassandra-preview-and-azure-cosmos-db-cassandra-api"></a>Verschillen tussen het beheerde exemplaar van Azure voor Apache Cassandra (preview) en Azure Cosmos DB Cassandra-API 

In dit artikel vindt u informatie over de verschillen tussen het beheerde exemplaar van Azure voor Apache Cassandra en de Cassandra-API in Azure Cosmos DB. In dit artikel vindt u aanbevelingen voor het kiezen tussen de twee services of wanneer u uw eigen Apache Cassandra-omgeving wilt hosten.

> [!IMPORTANT]
> Een door Azure beheerde instantie voor Apache Cassandra is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="key-differences"></a>Belangrijke verschillen

Een door Azure beheerde instantie voor Apache Cassandra biedt geautomatiseerde implementatie, schaling en bewerkingen om de knooppunt status voor open-source Apache Cassandra-instanties in azure te onderhouden. Het biedt ook de mogelijkheid om de capaciteit van bestaande on-premises of in de Cloud gehoste Apache Cassandra-clusters uit te schalen. Het wordt uitgeschaald door beheerde Cassandra-data centers toe te voegen aan de bestaande cluster ring.

De [Cassandra-API](../cosmos-db/cassandra-introduction.md) in azure Cosmos DB is een compatibiliteit slaag over de wereld wijd gedistribueerde Cloud service- [Azure Cosmos DB](../cosmos-db/index.yml)van micro soft. De combi natie van deze services in Azure biedt een continuüm keuze aan gebruikers van Apache Cassandra in complexe hybride Cloud omgevingen.

## <a name="how-to-choose"></a>Hoe kiezen?

De volgende tabel bevat de algemene scenario's, de vereisten voor de werk belasting en aanzuigers waarbij elk van deze implementaties aanpakt:

| |Zelf-hostende Apache Cassandra on-premises of in azure | Door Azure beheerde instantie voor Apache Cassandra | Azure Cosmos DB Cassandra-API |
|---------|---------|---------|---------|
|**Implementatie type**| U hebt een zeer aangepaste Apache Cassandra-implementatie met aangepaste patches of snitches. | U hebt een standaard open source Apache Cassandra-implementatie zonder aangepaste code. | U bent inhoud met een platform dat niet onder Apache Cassandra, maar compatibel is met alle open-source client Stuur Programma's op een [wire-protocol](../cosmos-db/cassandra-support.md) niveau. |
| **Operationele overhead**| U hebt bestaande Cassandra-experts die uw clusters kunnen implementeren, configureren en onderhouden.  | U wilt de operationele overhead verlagen voor de status van uw Apache Cassandra-knoop punt, maar nog steeds controle houden over de configuraties op platform niveau, zoals replicatie en consistentie. | U wilt de operationele overhead elimineren door gebruik te maken van een volledig beheerde platform-as-service-data base in de Cloud. |
| **Vereisten voor het besturingssysteem**| U hebt een vereiste voor het onderhouden van installatie kopieën van het besturings systeem van de aangepaste of gouden virtuele machine. | U kunt vanille-installatie kopieën gebruiken, maar u wilt controle hebben over Sku's, geheugen, schijven en IOPS. | U wilt dat capaciteits inrichting vereenvoudigd is en wordt uitgedrukt als één genormaliseerde metriek, met een een-op-een-relatie tot door Voer, zoals [aanvraag eenheden](../cosmos-db/request-units.md) in azure Cosmos db. |
| **Prijsmodel**| U wilt beheer software gebruiken, zoals Datastax-hulp programma, en u tevreden bent met de licentie kosten. | U hebt de voor keur aan zuivere open source-licenties en prijzen op basis van VM-exemplaren. | U wilt Cloud-native prijzen gebruiken, waaronder [automatisch schalen](../cosmos-db/manage-scale-cassandra.md#use-autoscale) en [serverloze](../cosmos-db/serverless.md) aanbiedingen. |
| **Analyse**| U wilt de volledige controle over het inrichten van analytische pijp lijnen, ongeacht de overhead om ze te bouwen en te onderhouden. | U wilt Cloud Services zoals Azure Databricks gebruiken. | U wilt bijna realtime hybride transactionele analyses ingebouwd in het platform met [Azure Synapse link voor Cosmos DB](../cosmos-db/synapse-link.md). |
| **Werkbelasting patroon**| Uw workload is redelijk stabiel en u hoeft geen knoop punten in het cluster regel matig te schalen. | Uw werk belasting is vluchtig en u moet in staat zijn om knoop punten in een Data Center omhoog of omlaag te schalen of door data centers eenvoudig toe te voegen of te verwijderen. | Uw workload is vaak vluchtig en u moet snel omhoog of omlaag schalen en op een aanzienlijk volume kunnen schalen. |
| **SLA's**| U bent tevreden over uw processen voor het onderhouden van service overeenkomsten met consistentie, door Voer, Beschik baarheid en herstel na nood gevallen. | U bent tevreden over uw processen voor het onderhouden van service overeenkomsten met consistentie, door Voer en beschik baarheid, maar u hebt hulp nodig met back-ups. | U wilt volledig uitgebreide service overeenkomsten met consistentie, door Voer, Beschik baarheid en herstel na nood gevallen. |

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met een van de volgende Quick starts:

* [Een beheerd exemplaar cluster maken op basis van de Azure Portal](create-cluster-portal.md)