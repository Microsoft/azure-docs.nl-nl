---
title: Azure CLI-voorbeelden voor Azure Cosmos DB Core (SQL) API
description: Azure CLI-voorbeelden voor Azure Cosmos DB Core (SQL) API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7c7baf4de56edd25537f93095bfa6ae0402214e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93073176"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-core-sql-api"></a>Azure CLI-voorbeelden voor Azure Cosmos DB Core (SQL) API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

De volgende tabel bevat koppelingen naar Azure CLI-voorbeeldscripts voor Azure Cosmos DB. Gebruik de links aan de rechterkant om te navigeren naar voorbeelden voor specifieke API's. Algemene voorbeelden zijn hetzelfde voor alle API's. Referentiepagina's voor alle Azure Cosmos DB CLI-opdrachten zijn beschikbaar in de [naslaginformatie voor Azure CLI](/cli/azure/cosmosdb). CLI-voorbeeldscripts voor Azure Cosmos DB kunt u ook vinden in de [opslagplaats van Azure Cosmos DB CLI in GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Voor deze voorbeelden hebt u Azure CLI versie 2.12.1 of hoger nodig. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI wilt installeren of upgraden, raadpleegt u [Azure CLI installeren](/cli/azure/install-azure-cli).

Zie voor Azure CLI-voorbeelden voor andere API's [CLI-voorbeelden voor Cassandra](cli-samples-cassandra.md), [CLI-voorbeelden voor MongoDB API](cli-samples-mongodb.md), [CLI-voorbeelden voor Gremlin](cli-samples-gremlin.md), [CLI-voorbeelden voor Table](cli-samples-table.md)

## <a name="common-samples"></a>Algemene voorbeelden

Deze voorbeelden zijn van toepassing op alle API's van Azure Cosmos DB.

|Taak | Beschrijving |
|---|---|
| [Regio's toevoegen of er een failover voor uitvoeren](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Een regio toevoegen, de failoverprioriteit wijzigen, een handmatige failover activeren.|
| [Accountsleutels en verbindingsreeksen](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Accountsleutels en alleen-lezensleutels weergeven, sleutels opnieuw genereren en verbindingsreeksen weergeven.|
| [Beveiligen met IP-firewall](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Een Cosmos-account maken met een geconfigureerde IP-firewall.|
| [Nieuw account beveiligen met service-eindpunten](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Een Cosmos-account maken en beveiligen met service-eindpunten.|
| [Bestaand account beveiligen met service-eind punten](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Een Cosmos-account bijwerken om het te beveiligen met service-eindpunten wanneer het subnet uiteindelijk is geconfigureerd.|
|||

## <a name="core-sql-api-samples"></a>API-voorbeelden Core (SQL)

|Taak | Beschrijving |
|---|---|
| [Een Azure Cosmos DB-account, -database en -container maken](scripts/cli/sql/create.md?toc=%2fcli%2fazure%2ftoc.json)| Hiermee maakt u een Azure Cosmos DB-account, -database en -container voor de Core (SQL) API. |
| [Een Azure Cosmos DB-account, -database en -container maken met automatische schaalaanpassing](scripts/cli/sql/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Hiermee maakt u een Azure Cosmos DB-account, -database en -container met automatische schaalaanpassing voor de Core (SQL) API. |
| [Doorvoerbewerkingen](scripts/cli/sql/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Lezen, bijwerken en migreren tussen doorvoer met automatische schaalaanpassing en standaarddoorvoer voor een database en container.|
| [Resources vergrendelen tegen verwijdering](scripts/cli/sql/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Voorkomen dat resources worden verwijderd met een resourcevergrendeling.|
|||
