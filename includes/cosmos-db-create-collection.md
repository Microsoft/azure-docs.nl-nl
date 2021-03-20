---
title: bestand opnemen
description: bestand opnemen
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 076afcfc8abf4e90d3ccedbb26653656032e59be
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85115381"
---
U kunt nu het hulpprogramma Data Explorer in de Azure-portal gebruiken om een database en een container te maken. 

1. Selecteer **Data Explorer** > **Nieuwe container**. 
    
    Uiterst rechts wordt het gedeelte **Container toevoegen** weergegeven. Mogelijk moet u naar rechts scrollen om het te kunnen zien.

    :::image type="content" source="./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png" alt-text="Data Explorer in Azure Portal, het deelvenster Container toevoegen":::

2. Geef op de pagina **Container toevoegen** de instellingen voor de nieuwe container op.

    |Instelling|Voorgestelde waarde|Beschrijving
    |---|---|---|
    |**Database-id**|Taken|Voer *Taken* in als de naam voor de nieuwe database. Databasenamen moeten tussen de 1 en 255 tekens zijn en mogen geen `/, \\, #, ?` bevatten en mogen niet eindigen met een spatie. Schakel de optie **Doorvoer voor databases inrichten** in, zodat u de doorvoer die is ingericht voor de database, kunt delen in alle containers in de database. Met deze optie bespaart u bovendien op de kosten. |
    |**Doorvoer**|400|Wijzig de doorvoer in 400 aanvraageenheden per seconde (RU/s). U kunt de doorvoer later opschalen als u de latentie wilt beperken.| 
    |**Container-id**|Items|Voer *Items* in als de naam voor de nieuwe container. Voor id's van containers gelden dezelfde tekenvereisten als voor databasenamen.|
    |**Partitiesleutel**| /category| In het voorbeeld dat in dit artikel wordt beschreven, wordt */category* als de partitiesleutel gebruikt.|
    
    Naast de voornoemde instellingen kunt u indien gewenst **unieke sleutels** voor de container toevoegen. In dit voorbeeld laten we het veld leeg. Unieke sleutels bieden ontwikkelaars de mogelijkheid om een gegevensintegriteitslaag aan de database toe te voegen. Door een beleid met unieke sleutels te maken als u een container maakt, zorgt u ervoor dat een of meer waarden per partitiesleutel uniek zijn. Raadpleeg het artikel [Unique keys in Azure Cosmos DB](../articles/cosmos-db/unique-keys.md) (Unieke sleutels in Azure Cosmos DB) voor meer informatie.
    
    Selecteer **OK**. In Data Explorer worden de nieuwe database en container weergegeven.