---
title: Azure Cosmos DB op basis van een schema schalen door gebruik te maken van Azure Functions timer
description: Meer informatie over het schalen van wijzigingen in de door Voer in Azure Cosmos DB met behulp van Power shell en Azure Functions.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: c60f3fc6b4ce4a1aead273fedb81e39de697f576
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339254"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Azure Cosmos DB door Voer schalen met behulp van Azure Functions timer trigger
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

De prestaties van een Azure Cosmos-account zijn gebaseerd op de hoeveelheid ingerichte door Voer uitgedrukt in aanvraag eenheden per seconde (RU/s). Het inrichten bevindt zich op een tweede granulatie en wordt gefactureerd op basis van de hoogste RU/s per uur. Met dit model met ingerichte capaciteit kan de service een voorspel bare en consistente door Voer, een gegarandeerde lage latentie en hoge Beschik baarheid bieden. De meeste productiewerk belastingen deze functies. In ontwikkel-en test omgevingen waarin Azure Cosmos DB alleen wordt gebruikt tijdens de werk uren, kunt u de door Voer in de ochtend op morgen opschalen en op een later tijdstip in de avond omlaag schalen.

U kunt de door Voer instellen via [Azure Resource Manager sjablonen](./templates-samples-sql.md), [Azure cli](cli-samples.md)en [Power shell](powershell-samples.md), voor core-(SQL) API-accounts of met behulp van de taalspecifieke Azure Cosmos DB-sdk's. Het voor deel van het gebruik van Resource Manager-sjablonen, Azure CLI of Power shell is dat ze alle Azure Cosmos DB model-Api's ondersteunen.

## <a name="throughput-scheduler-sample-project"></a>Voor beeld van een doorvoer planner-project

Om het proces voor het schalen van Azure Cosmos DB op basis van een planning te vereenvoudigen, hebben we een voorbeeld project gemaakt met de naam [scheduler Azure Cosmos-door Voer](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler). Dit project is een Azure Functions-app met twee timer triggers: ' ScaleUpTrigger ' en ' ScaleDownTrigger '. De triggers voeren een Power shell-script uit waarmee de door Voer voor elke resource wordt ingesteld, zoals gedefinieerd in het `resources.json` bestand in elke trigger. De ScaleUpTrigger is geconfigureerd om te worden uitgevoerd op 8 uur UTC en de ScaleDownTrigger is zo geconfigureerd dat deze wordt uitgevoerd op 6 uur UTC en deze tijden kunnen eenvoudig worden bijgewerkt in het `function.json` bestand voor elke trigger.

U kunt dit project lokaal klonen, wijzigen om de Azure Cosmos DB resources op te geven die u omhoog en omlaag wilt schalen en het schema dat moet worden uitgevoerd. Later kunt u de app implementeren in een Azure-abonnement en deze beveiligen met behulp van de beheerde service-identiteit met [Azure RBAC-machtigingen (op rollen gebaseerd toegangs beheer)](role-based-access-control.md) met de rol ' Azure Cosmos DB operator ' om de door Voer in te stellen voor uw Azure Cosmos-accounts.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie en down load het voor beeld van [Azure Cosmos DB doorvoer planner](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler).