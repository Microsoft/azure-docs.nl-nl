---
title: Azure CLI - abonneren op resourcegroep en op resource filteren
description: Dit artikel bevat een voorbeeld van een Azure CLI-script dat laat zien hoe u zich kunt abonneren op gebeurtenissen voor Event Grid voor een resource en filter voor een resource.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2662795e5ebc43e37eab7cab3dfc03582f072f8b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871410"
---
# <a name="subscribe-to-events-for-a-resource-group-and-filter-for-a-resource-with-azure-cli"></a>Abonneren op gebeurtenissen voor een resourcegroep en filteren op een resource met Azure CLI

Met dit script maakt u een Event Grid-abonnement op de gebeurtenissen voor een resourcegroep. Er wordt een filter gebruikt om alleen gebeurtenissen op te halen voor een bepaalde resource in de resourcegroep.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Voor het voorbeeldscript van de preview is de Event Grid-extensie vereist. Voer `az extension add --name eventgrid` uit om deze te installeren.

## <a name="sample-script---stable"></a>Voorbeeldscript - stabiel

[!code-azurecli[main](../../../cli_scripts/event-grid/filter-events/filter-events.sh "Subscribe to Azure subscription")]

## <a name="sample-script---preview-extension"></a>Voorbeeldscript - preview-extensie

[!code-azurecli[main](../../../cli_scripts/event-grid/filter-events-preview/filter-events-preview.sh "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script wordt de volgende opdracht gebruikt om het abonnement op de gebeurtenis te maken. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) | Hiermee wordt een Event Grid-abonnement gemaakt. |
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) - extensieversie | Hiermee wordt een Event Grid-abonnement gemaakt. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Query Event Grid subscriptions](../query-event-subscriptions.md) (Query's uitvoeren op Event Grid-abonnementen) voor informatie over het uitvoeren van query's op abonnementen.
* Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.
