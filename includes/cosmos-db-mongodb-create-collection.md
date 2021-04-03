---
title: bestand opnemen
description: bestand opnemen
services: cosmos-db
author: LuisBosquez
ms.service: cosmos-db
ms.topic: include
ms.date: 04/15/2020
ms.author: lbosq
ms.custom: include file
ms.openlocfilehash: 37cdb6b466417add8dae69464304ce2f32247c8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95563277"
---
U kunt nu het hulpprogramma Data Explorer in Azure Portal gebruiken om een API van Azure Cosmos DB voor MongoDB-database en -container te maken. 

1. Selecteer **Data Explorer** > **Nieuwe container**. 
    
    Uiterst rechts wordt het gedeelte **Container toevoegen** weergegeven. Mogelijk moet u naar rechts scrollen om het te kunnen zien.

    :::image type="content" source="./media/cosmos-db-create-collection/azure-cosmosdb-mongodb-data-explorer.png" alt-text="Data Explorer in Azure Portal, het deelvenster Container toevoegen":::

2. Geef op de pagina **Container toevoegen** de instellingen voor de nieuwe container op.

    |Instelling|Voorgestelde waarde|Beschrijving
    |---|---|---|
    |**Database-id**|db|Voer *db* in als de naam voor de nieuwe database. Databasenamen moeten tussen de 1 en 255 tekens zijn en mogen geen `/, \\, #, ?` bevatten en mogen niet eindigen met een spatie. Schakel de optie **Doorvoer voor databases inrichten** in, zodat u de doorvoer die is ingericht voor de database, kunt delen in alle containers in de database. Met deze optie bespaart u bovendien op de kosten. |
    |**Doorvoer**|400|Wijzig de doorvoer in 400 aanvraageenheden per seconde (RU/s). U kunt de doorvoer later opschalen als u de latentie wilt beperken. U kunt ook de [modus Automatische schaalaanpassing](../articles/cosmos-db/provision-throughput-autoscale.md) kiezen, waarmee u een reeks RU/s krijgt die naar behoefte dynamisch wordt vergroot of verkleind.| 
    |**Verzamelings-id**|coll|Voer `coll` in als de naam voor de nieuwe container. Voor id's van containers gelden dezelfde tekenvereisten als voor databasenamen.|
    |**Opslagcapaciteit**|Vast (10 GB)|Voer *Vast (10 GB)* voor deze toepassing in. Als u *Onbeperkt* selecteert, moet u een `Shard Key` maken, die voor alle opgenomen items nodig is.|
    |**Shardsleutel**| /_id| Het voorbeeld dat in dit artikel wordt beschreven, maakt geen gebruik van een shardsleutel, dus als u deze instelt op */_id*, wordt het veld Automatisch gegenereerde id als shardsleutel gebruikt. Meer informatie over sharding, ook wel partitioneren genoemd, vindt u in [Partitionering in Azure Cosmos DB](../articles/cosmos-db/partitioning-overview.md)|
        
    Selecteer **OK**. In Data Explorer worden de nieuwe database en container weergegeven.