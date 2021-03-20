---
title: Azure Cosmos DB SQL SDK-connectiviteits modi
description: Meer informatie over de verschillende connectiviteits modi die beschikbaar zijn op de Azure Cosmos DB SQL-Sdk's.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: maquaran
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: cf985999bac0cf45eec5d8f0f5f9e921b6f4591c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934948"
---
# <a name="azure-cosmos-db-sql-sdk-connectivity-modes"></a>Azure Cosmos DB SQL SDK-connectiviteits modi
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Hoe een client verbinding maakt met Azure Cosmos DB heeft belang rijke gevolgen voor de prestaties, met name voor de waargenomen latentie aan de client zijde. Azure Cosmos DB biedt een eenvoudig open, REST-programmeer model via HTTPS-gateway modus. Daarnaast biedt het een efficiënt TCP-protocol, dat ook rust in het communicatie model en gebruikmaakt van TLS voor initiële verificatie en het versleutelen van verkeer, direct-modus genoemd.

## <a name="available-connectivity-modes"></a>Beschik bare connectiviteits modi

De twee beschik bare connectiviteits modi zijn:

  * Gatewaymodus
      
    De gateway modus wordt op alle SDK-platformen ondersteund. Als uw toepassing wordt uitgevoerd in een bedrijfs netwerk met strikte firewall beperkingen, is de gateway modus de beste keuze, omdat deze gebruikmaakt van de standaard HTTPS-poort en één DNS-eind punt. De verhoudingen van de prestaties zijn echter wel dat de gateway modus een extra netwerk-hop omvat telkens wanneer gegevens worden gelezen vanuit of geschreven naar Azure Cosmos DB. We raden ook de verbindings modus voor de gateway aan wanneer u toepassingen uitvoert in omgevingen met een beperkt aantal socket verbindingen.

    Wanneer u de SDK in Azure Functions gebruikt, met name in het [verbruiks abonnement](../azure-functions/consumption-plan.md), moet u rekening houden met de huidige [limieten voor verbindingen](../azure-functions/manage-connections.md).

  * Directe modus

    Directe modus ondersteunt connectiviteit via een TCP-protocol en biedt betere prestaties omdat er minder netwerk-hops zijn. De toepassing maakt rechtstreeks verbinding met de back-end-replica's. Directe modus wordt momenteel alleen ondersteund op .NET-en Java SDK-platforms.
     
:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="De Azure Cosmos DB connectiviteits modi" border="false":::

Deze connectiviteits modi hebben in wezen de voor waarde dat de route die het data-vlak vraagt, het document leest en schrijft van uw client computer naar partities in de Azure Cosmos DB back-end. De directe modus is de voorkeurs optie voor de beste prestaties. Hiermee kan uw client TCP-verbindingen rechtstreeks openen op partities in de Azure Cosmos DB back-end en *aanvragen verzenden* zonder tussen komst. Daarentegen wordt in de gateway modus aanvragen die door uw client worden ingediend, doorgestuurd naar een zogenaamde Gateway server in de Azure Cosmos DB front-end, die op zijn beurt uw aanvragen naar de juiste partitie (s) in de Azure Cosmos DB back-end uitschakelt.

## <a name="service-port-ranges"></a>Service poortbereiken

Wanneer u de directe modus gebruikt naast de gateway poorten, moet u ervoor zorgen dat het poort bereik tussen 10000 en 20000 open is, omdat Azure Cosmos DB dynamische TCP-poorten gebruikt. Wanneer u directe modus op [particuliere eind punten](./how-to-configure-private-endpoints.md)gebruikt, moet u het volledige bereik van TCP-poorten van 0 tot 65535 openen. Als deze poorten niet zijn geopend en u het TCP-protocol wilt gebruiken, kan de fout melding 503 Service niet beschikbaar worden weer gegeven.

De volgende tabel bevat een samen vatting van de connectiviteits modi die beschikbaar zijn voor verschillende Api's en de service poorten die voor elke API worden gebruikt:

|Verbindingsmodus  |Ondersteund protocol  |Ondersteunde Sdk's  |API/service poort  |
|---------|---------|---------|---------|
|Gateway  |   HTTPS    |  Alle Sdk's    |   SQL (443), MongoDB (10250, 10255, 10256), Table (443), Cassandra (10350), Graph (443) <br> Poort 10250 wordt toegewezen aan een standaard Azure Cosmos DB-API voor MongoDB-instantie zonder geo-replicatie. Terwijl de poorten 10255 en 10256 worden toegewezen aan het exemplaar met geo-replicatie.   |
|Direct    |     TCP    |  .NET SDK Java SDK    | Bij gebruik van open bare/service-eind punten: poorten in het 10000 tot en met 20000-bereik<br>Bij het gebruik van privé-eind punten: poorten in het bereik 0 tot en met 65535 |

## <a name="next-steps"></a>Volgende stappen

Voor specifieke optimalisaties voor SDK-platform prestaties:

* [Tips voor de prestaties van .NET v2 SDK](performance-tips.md)

* [Tips voor de prestaties van .NET v3 SDK](performance-tips-dotnet-sdk-v3-sql.md)
 
* [Tips voor de prestaties van Java v4 SDK](performance-tips-java-sdk-v4-sql.md)