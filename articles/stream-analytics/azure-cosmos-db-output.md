---
title: Azure Cosmos DB uitvoer van Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u gegevens kunt uitvoeren van Azure Stream Analytics naar Azure Cosmos DB.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 9382ac8dc71d1ccb3a85dc0a7a027c8e99296cc6
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016574"
---
# <a name="azure-cosmos-db-output-from-azure-stream-analytics"></a>Azure Cosmos DB uitvoer van Azure Stream Analytics

[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) is een wereld wijd gedistribueerde database service die onbeperkte elastische schaal rond de wereld, uitgebreide query en automatische indexering via schema-neutraal gegevens modellen biedt. Zie het artikel [Stream Analytics met Azure Cosmos DB als uitvoer](stream-analytics-documentdb-output.md) voor meer informatie over Azure Cosmos DB container opties voor stream Analytics.

Azure Cosmos DB uitvoer van Stream Analytics is momenteel niet beschikbaar in de regio's 21Vianet en Azure Duitsland (T-Systems International) van Azure China.

> [!Note]
> Azure Stream Analytics ondersteunt alleen de verbinding met Azure Cosmos DB met behulp van de SQL-API.
> Andere Azure Cosmos DB Api's worden nog niet ondersteund. Als u Azure Stream Analytics naar de Azure Cosmos DB-accounts die zijn gemaakt met andere Api's, zijn de gegevens mogelijk niet op de juiste manier opgeslagen.

In de volgende tabel worden de eigenschappen beschreven voor het maken van een Azure Cosmos DB uitvoer.

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Uitvoeralias | Een alias om deze uitvoer in uw Stream Analytics-query te verwijzen. |
| Sink | Azure Cosmos DB. |
| Importoptie | Kies **Cosmos DB selecteren in uw abonnement** of **Cosmos DB instellingen hand matig opgeven**.
| Account-id | De naam of eind punt-URI van het Azure Cosmos DB-account. |
| Accountsleutel | De gedeelde toegangs sleutel voor het Azure Cosmos DB-account. |
| Database | De naam van de Azure Cosmos DB-Data Base. |
| Containernaam | De container naam die moet worden gebruikt, die voor komt in Cosmos DB. Voorbeeld:  <br /><ul><li> _MyContainer_: er moet een container met de naam ' MyContainer ' bestaan.</li>|
| Document-ID |Optioneel. De naam van het veld in uitvoer gebeurtenissen dat wordt gebruikt om de primaire sleutel op te geven waarop invoeg-of update bewerkingen zijn gebaseerd.

## <a name="partitioning"></a>Partitionering

De partitie sleutel is gebaseerd op de component PARTITION BY in de query. Het aantal schrijvers van de uitvoer volgt de invoer partities voor [volledig parallelle query's](stream-analytics-scale-jobs.md). Stream Analytics converteert de Cosmos DB uitvoer partitie sleutel naar een teken reeks. Als u bijvoorbeeld een partitie sleutel hebt met de waarde 1 van het type bigint, wordt deze geconverteerd naar 1 van het type teken reeks. Deze conversie vindt altijd plaats ongeacht of de eigenschap Partition naar Cosmos DB wordt geschreven.

## <a name="output-batch-size"></a>Grootte van uitvoer batch

Zie [Azure Cosmos DB limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits)voor de maximale bericht grootte. Batch grootte en schrijf frequentie worden dynamisch aangepast op basis van Azure Cosmos DB reacties. Er zijn geen vooraf vastgestelde beperkingen van Stream Analytics.

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md)
* [Quickstart: een Azure Stream Analytics-taak maken via de Azure CLI](quick-create-azure-cli.md)
* [Quickstart: Een Azure Stream Analytics-taak maken via een ARM-sjabloon](quick-create-azure-resource-manager.md)
* [Quickstart: Een Stream Analytics-taak maken met behulp van Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Quickstart: Een Azure Stream Analytics-taak maken met behulp van Visual Studio](stream-analytics-quick-create-vs.md)
* [Snelstartgids: een Azure Stream Analytics-taak maken in Visual Studio code](quick-create-visual-studio-code.md)
