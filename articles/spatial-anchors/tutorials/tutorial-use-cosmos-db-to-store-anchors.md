---
title: 'Zelfstudie: Ankers delen met Azure Cosmos DB'
description: In deze zelfstudie leert u hoe u Azure Spatial Anchors-id's deelt tussen Android-/iOS-apparaten in Unity met een back-endservice en Azure Cosmos DB.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: ff888cd98cc79f3e2d508b01f092102eaa038c86
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95494757"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Zelfstudie: Azure Spatial Anchors delen in sessies en apparaten met een Azure Cosmos DB-back-end

Deze zelfstudie is een vervolg van [Azure Spatial Anchors delen in sessies en apparaten](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md). Deze leidt u door de procedure voor het toevoegen van nog een aantal mogelijkheden om Azure Cosmos DB als de back-endopslag te gebruiken terwijl u Azure Spatial Anchors deelt in sessies en apparaten.

![GIF die objectpersistentie illustreert](./media/persistence.gif)

Hoewel u in deze zelfstudie gebruikmaakt van Unity en Azure Cosmos DB, is dit alleen om u een voorbeeld te geven van hoe u Spatial Anchors-id's deelt op apparaten. U kunt andere talen en back-endtechnologieën gebruiken om hetzelfde doel te bereiken.

## <a name="create-a-database-account"></a>Een databaseaccount maken

Voeg een Azure Cosmos Database toe aan de resourcegroep die u eerder hebt gemaakt.

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Kopieer de `Connection String`, want u hebt deze later nodig.

## <a name="make-minor-changes-to-the-sharingservice-files"></a>Kleine wijzigingen aanbrengen in de SharingService-bestanden

In **Solution Explorer** opent u `SharingService\Startup.cs`.

Zoek `#define INMEMORY_DEMO` bovenaan het bestand en maak commentaar van die regel. Sla het bestand op.

In **Solution Explorer** opent u `SharingService\appsettings.json`.

Zoek de eigenschap `StorageConnectionString` en stel de waarde in op de `Connection String`-waarde die u hebt gekopieerd in de [stap voor het maken van een databaseaccount](#create-a-database-account). Sla het bestand op.

U kunt de deelservice nogmaals publiceren en de voorbeeld-app uitvoeren.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Azure Cosmos DB gebruikt voor het delen van anker-id's op apparaten. Ga verder met de volgende zelfstudie voor meer informatie over het gebruik van Azure Spatial Anchors in een nieuwe Unity HoloLens-app.

> [!div class="nextstepaction"]
> [Een nieuwe HoloLens Unity-app starten](./tutorial-new-unity-hololens-app.md)
