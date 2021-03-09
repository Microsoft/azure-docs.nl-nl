---
title: Voorbeelden van Azure PowerShell voor Azure Cosmos DB Cassandra-API
description: Download de Azure PowerShell-voorbeelden voor het uitvoeren van verschillende algemene taken Azure Cosmos DB Cassandra-API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: 15e0f221141062b2239b588889036849d3b1d91e
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505202"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-cassandra-api"></a>Voorbeelden van Azure PowerShell voor Azure Cosmos DB Cassandra-API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

De volgende tabel bevat koppelingen naar veelgebruikte Azure PowerShell-scripts voor Azure Cosmos DB. Gebruik de links aan de rechterkant om te navigeren naar voorbeelden voor specifieke API's. Algemene voorbeelden zijn hetzelfde voor alle API's. Referentiepagina's voor alle PowerShell-cmdlets voor Azure Cosmos DB zijn beschikbaar in de [naslaginformatie voor Azure PowerShell](/powershell/module/az.cosmosdb). De `Az.CosmosDB` module maakt nu deel uit van de `Az` module. [Down load en installeer](/powershell/azure/install-az-ps) de meest recente versie van AZ module om de cmdlets voor Azure Cosmos DB op te halen. U kunt ook de nieuwste versie van de [PowerShell Gallery](https://www.powershellgallery.com/packages/Az/5.4.0)ophalen. U kunt deze PowerShell-voorbeelden ook forken voor Cosmos DB vanaf onze GitHub-opslagplaats, te weten [Cosmos DB PowerShell Samples op GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Algemene voorbeelden

|Taak | Beschrijving |
|---|---|
|[Een account bijwerken](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Werk het standaard consistentieniveau van een Cosmos DB-account bij. |
|[De regio’s van een account bijwerken](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Werk de regio’s van een Cosmos DB-account bij. |
|[Failoverprioriteit wijzigen of failover activeren](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Wijzig de prioriteit van de regionale failover van een Azure Cosmos-account of activeer een handmatige failover. |
|[Accountsleutels of verbindingsreeksen](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Haal de primaire en secundaire sleutels en verbindingsreeksen op of genereer opnieuw een accountsleutel van een Azure Cosmos DB-account. |
|[Een Cosmos-account maken met een IP-firewall](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Maak een Azure Cosmos DB-account waarvoor IP-firewall is ingeschakeld. |
|||

## <a name="cassandra-api-samples"></a>Voorbeelden van Cassandra-API

|Taak | Beschrijving |
|---|---|
|[Een account, keyspace en een tabel maken](scripts/powershell/cassandra/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hiermee wordt een Azure Cosmos-account, -keyspace en -tabel gemaakt. |
|[Een account, keyspace en tabel maken met automatische schaalaanpassing](scripts/powershell/cassandra/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Maak een account, keyspace en tabel van Azure Cosmos met automatische schaalaanpassing. |
|[Keyspaces of tabellen weergeven of ophalen](scripts/powershell/cassandra/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Geef keyspaces of tabellen weer of haal deze op. |
|[Doorvoerbewerkingen](scripts/powershell/cassandra/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Doorvoerbewerkingen voor een keyspace of tabel, waaronder ophalen, bijwerken en migreren tussen doorvoer met automatische schaalaanpassing en standaarddoorvoer. |
|[Resources vergrendelen tegen verwijdering](scripts/powershell/cassandra/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Voorkomen dat resources worden verwijderd met een resourcevergrendeling. |
|||
