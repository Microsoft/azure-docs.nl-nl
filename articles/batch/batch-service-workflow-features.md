---
title: Workflow en resources van batchservice
description: Meer informatie over de functies van de batch-service en de werk stroom op hoog niveau van een ontwikkelings standpunt.
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 76a0b140f3bea4b07a6de632abbcbc3fd26e582d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85965209"
---
# <a name="batch-service-workflow-and-resources"></a>Workflow en resources van batchservice

In dit overzicht van de kern onderdelen van de Azure Batch-service bespreken we de werk stroom op hoog niveau die door batch ontwikkelaars kan worden gebruikt om grootschalige parallelle reken oplossingen te bouwen, samen met de primaire service resources die worden gebruikt.

Of u nu een gedistribueerde reken kundige toepassing of service ontwikkelt die directe [rest API](/rest/api/batchservice/) -aanroepen uitgeeft of u een andere van de [batch-sdk's](batch-apis-tools.md#batch-service-apis)gebruikt, u maakt gebruik van veel van de resources en functies die hier worden besproken.

> [!TIP]
> Zie [Basisbeginselen van Azure Batch](batch-technical-overview.md) voor een introductie op hoger niveau van de Batch-service. Zie ook de meest recente [Batch-service-updates](https://azure.microsoft.com/updates/?product=batch).

## <a name="basic-workflow"></a>Basiswerkstroom

De volgende werkstroom op hoog niveau is gangbaar voor bijna alle toepassingen en services die gebruikmaken van de Batch-service voor de verwerking van parallelle workloads:

1. Upload de **gegevensbestanden** die u wilt verwerken voor een [Azure-opslagaccount](../storage/index.yml). Batch bevat ingebouwde ondersteuning voor toegang tot Azure-blobopslag en uw taken kunnen deze bestanden downloaden naar [rekenknooppunten](nodes-and-pools.md#nodes) wanneer de taken worden uitgevoerd.
2. Upload de **toepassingsbestanden** die uw taken gaan uitvoeren. Deze bestanden kunnen binaire bestanden of scripts en ervan afhankelijke elementen zijn, en worden uitgevoerd door de taken in uw jobs. Uw taken kunnen deze bestanden downloaden uit uw opslagaccount, maar u kunt ook de functie voor [toepassingspakketten](nodes-and-pools.md#application-packages) van Batch voor het beheren en implementeren van toepassingen gebruiken.
3. Maak een [pool](nodes-and-pools.md#pools) van reken knooppunten. Wanneer u een pool maakt, geeft u het aantal rekenknooppunten voor de pool, de grootte en het besturingssysteem op. Wanneer elke taak in de job wordt uitgevoerd, wordt deze voor uitvoering toegewezen aan een van de knooppunten in uw pool.
4. Een [taak](jobs-and-tasks.md#jobs)maken. Een job beheert een verzameling van taken. U koppelt elke job aan een specifieke pool waar de taken van die job worden uitgevoerd.
5. Voeg [taken](jobs-and-tasks.md#tasks) toe aan de job. Elke taak voert de toepassing of het script uit die door u is geüpload voor het verwerken van de uit uw opslagaccount gedownloade gegevensbestanden. Wanner een taak is voltooid, kunt u de uitvoer ervan uploaden naar Azure Storage.
6. Bewaak de voortgang van de taak en haal de taakuitvoer op uit Azure Storage.

> [!NOTE]
> U hebt een [Batch-account](accounts.md) nodig om de Batch-service te kunnen gebruiken. Voor de meeste Batch-oplossingen wordt ook een gekoppeld [Azure Storage](../storage/index.yml)-account gebruikt om bestanden op te slaan en op te halen.

## <a name="batch-service-resources"></a>Batch-serviceresources

In de volgende onderwerpen worden de bronnen van de batch besproken die uw gedistribueerde reken kundige scenario's mogelijk maken.

- [Batch-accounts en-opslag accounts](accounts.md)
- [Knooppunten en pools](nodes-and-pools.md)
- [Taken](jobs-and-tasks.md)
- [Bestanden en mappen](files-and-directories.md)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Batch-API's en -hulpprogramma's](batch-apis-tools.md) die beschikbaar zijn voor het bouwen van Batch-oplossingen.
- Lees de basisbeginselen van het ontwikkelen van een voor Batch geschikte toepassing met behulp van de [clientbibliotheek Batch .NET](quick-run-dotnet.md) of [Python](quick-run-python.md). Deze snelstarts leiden u stapsgewijs door een voorbeeldtoepassing die gebruikmaakt van de Batch-service voor het uitvoeren van een workload op meerdere rekenknooppunten. U vindt er ook informatie over het gebruik van Azure Storage voor het faseren en ophalen van een workloadbestand.
- Download en installeer [Batch Explorer](https://azure.github.io/BatchExplorer/) voor gebruik bij het ontwikkelen van uw Batch-oplossingen. Gebruik Batch Explorer bij het maken en bewaken van en opsporen van fouten in Azure Batch-toepassingen.
- Zie community-bronnen, waaronder [stack overflow](https://stackoverflow.com/questions/tagged/azure-batch), de [batch-Community opslag plaats](https://github.com/Azure/Batch)en het [Azure batch-forum](/answers/topics/azure-batch.html).
