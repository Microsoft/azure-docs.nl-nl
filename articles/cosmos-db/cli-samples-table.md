---
title: Voorbeelden van Azure CLI voor Azure Cosmos DB Table-API
description: Voorbeelden van Azure CLI voor Azure Cosmos DB Table-API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: c9ed8c6547429d21d0a4da9c02b3a0d72581f25a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93342059"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-table-api"></a>Voorbeelden van Azure CLI voor Azure Cosmos DB Table-API
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

De volgende tabel bevat koppelingen naar Azure CLI-voorbeeldscripts voor Azure Cosmos DB. Gebruik de links aan de rechterkant om te navigeren naar voorbeelden voor specifieke API's. Algemene voorbeelden zijn hetzelfde voor alle API's. Referentiepagina's voor alle Azure Cosmos DB CLI-opdrachten zijn beschikbaar in de [naslaginformatie voor Azure CLI](/cli/azure/cosmosdb). CLI-voorbeeldscripts voor Azure Cosmos DB kunt u ook vinden in de [opslagplaats van Azure Cosmos DB CLI in GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Voor deze voorbeelden hebt u Azure CLI versie 2.12.1 of hoger nodig. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI wilt installeren of upgraden, raadpleegt u [Azure CLI installeren](/cli/azure/install-azure-cli).

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

## <a name="table-api-samples"></a>Voorbeelden van Table-API

|Taak | Beschrijving |
|---|---|
| [Een Azure Cosmos-account en -tabel maken](scripts/cli/table/create.md?toc=%2fcli%2fazure%2ftoc.json)| Hiermee worden een Azure Cosmos-account en -tabel gemaakt voor Table-API. |
| [Een account en tabel van Azure Cosmos met automatische schaalaanpassing maken](scripts/cli/table/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Hiermee maakt u een Azure Cosmos-account en -tabel met automatische schaalaanpassing voor de Table-API. |
| [Doorvoerbewerkingen](scripts/cli/table/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Lezen, bijwerken en migreren tussen doorvoer met automatische schaalaanpassing en standaarddoorvoer voor een tabel.|
| [Resources vergrendelen tegen verwijdering](scripts/cli/table/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Voorkomen dat resources worden verwijderd met een resourcevergrendeling.|
|||
