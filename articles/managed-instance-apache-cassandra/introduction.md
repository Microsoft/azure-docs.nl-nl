---
title: Inleiding tot Azure Managed instance voor Apache Cassandra
description: Meer informatie over het beheerde exemplaar van Azure voor Apache Cassandra. Deze service beheert de implementatie en schaal van systeem eigen open-source exemplaren van Apache Cassandra in Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 03/02/2021
ms.openlocfilehash: d99c62e7d88510c351f87d85b4f8c5c271767cb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748471"
---
# <a name="what-is-azure-managed-instance-for-apache-cassandra-preview"></a>Wat is een beheerd exemplaar van Azure voor Apache Cassandra? (Preview)

> [!IMPORTANT]
> Een door Azure beheerde instantie voor Apache Cassandra is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Het door Azure beheerde exemplaar voor Apache Cassandra service voorziet in geautomatiseerde implementatie-en schaal bewerkingen voor beheerde open source Apache Cassandra-data centers. Met deze service kunt u hybride scenario's versnellen en doorlopend onderhoud verminderen. Het heeft uitgebreide integratie en mogelijkheden voor gegevens verplaatsing met [Azure Cosmos DB Cassandra-API](../cosmos-db/cassandra-introduction.md) bij de algemene release.

:::image type="content" source="./media/introduction/icon.gif" alt-text="Een door Azure beheerde instantie voor Apache Cassandra is een beheerde service voor Apache Cassandra." border="false":::

## <a name="key-benefits"></a>Belangrijkste voordelen

### <a name="hybrid-deployments"></a>Hybride implementaties

Met deze service kunt u eenvoudig beheerde exemplaren van Apache Cassandra-data centers, die automatisch worden geïmplementeerd als virtuele-machine schaal sets, in een nieuwe of bestaande Azure-Virtual Network plaatsen. Deze data centers kunnen worden toegevoegd aan uw bestaande Apache Cassandra-ring die on-premises wordt uitgevoerd via [Azure ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) in azure, of in een andere cloud omgeving.

- **Vereenvoudigde implementatie:** Nadat de hybride verbinding tot stand is gebracht, is de implementatie eenvoudig via het Gossip-protocol.
- **Gehoste metrische gegevens:** De metrische gegevens worden gehost in [Prometheus](https://prometheus.io/docs/introduction/overview/) om de activiteit in uw cluster te bewaken.

### <a name="simplified-scaling"></a>Vereenvoudigd schalen

In het beheerde exemplaar wordt het omhoog en omlaag schalen van knoop punten in een Data Center volledig beheerd. U voert het aantal knoop punten in dat u nodig hebt, en de uitbreidings functie zorgt ervoor dat de werking ervan binnen de Cassandra ring tot stand wordt gebracht.

### <a name="managed-and-cost-effective"></a>Beheerd en kosten effectief

De service biedt beheer bewerkingen voor de volgende algemene Apache Cassandra-taken:

- Een cluster inrichten
- Een Data Center inrichten
- Een Data Center schalen
- Een Data Center verwijderen
- Een herstel actie starten op een spatie
- Configuratie van een Data Center wijzigen
- Back-ups instellen

Het prijs model is flexibel, op aanvraag, op basis van een exemplaar en heeft geen licentie kosten. Met dit prijs model kunt u aanpassen aan uw specifieke werkbelasting behoeften. U kunt kiezen hoeveel kernen, welke VM-SKU, welke geheugen grootte en welke schijf ruimte u nodig hebt.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met een van de volgende Quick starts:

* [Een beheerd exemplaar cluster maken op basis van de Azure Portal](create-cluster-portal.md)
* [Een beheerd Apache Spark cluster implementeren met Azure Databricks](deploy-cluster-databricks.md)
* [Een beheerd exemplaar van Azure beheren voor Apache Cassandra-resources met behulp van Azure CLI](manage-resources-cli.md)
