---
title: Voorbeeld van Azure CLI-script - abonneren op Blob Storage-account | Microsoft Docs
description: Dit artikel bevat een voorbeeld van een Azure CLI-script dat laat zien hoe u zich kunt abonneren op gebeurtenissen voor een Azure Blob Storage-account.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: ab0995a49f944344b6914bd0e51f1d437dd28c69
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766738"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-azure-cli"></a>Abonneren op gebeurtenissen voor een Blob Storage-account met Azure CLI

Met dit script maakt u een Event Grid-abonnement op de gebeurtenissen voor een Blob Storage-account. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.sh "Subscribe to Blob storage")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script wordt de volgende opdracht gebruikt om het abonnement op de gebeurtenis te maken. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) | Hiermee wordt een Event Grid-abonnement gemaakt. |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) - extensieversie | Hiermee wordt een Event Grid-abonnement gemaakt. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Query Event Grid subscriptions](../query-event-subscriptions.md) (Query's uitvoeren op Event Grid-abonnementen) voor informatie over het uitvoeren van query's op abonnementen.
* Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.
