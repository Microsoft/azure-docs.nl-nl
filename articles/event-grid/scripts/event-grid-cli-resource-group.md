---
title: Voorbeeld van Azure CLI-script - abonneren op resourcegroep | Microsoft Docs
description: Dit artikel bevat een voorbeeld van een Azure CLI-script dat laat zien hoe u zich kunt abonneren op gebeurtenissen voor Azure Event Grid voor een resourcegroep.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: f655ed3f2b6a3cef89c493137bd34732a60e414a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87494091"
---
# <a name="subscribe-to-events-for-a-resource-group-with-azure-cli"></a>Abonneren op gebeurtenissen voor een resourcegroep met Azure CLI

Met dit script maakt u een Event Grid-abonnement op de gebeurtenissen voor een resourcegroep. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Voor het voorbeeldscript van de preview is de Event Grid-extensie vereist. Voer `az extension add --name eventgrid` uit om deze te installeren.

## <a name="sample-script---stable"></a>Voorbeeldscript - stabiel

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-resource-group/subscribe-to-resource-group.sh "Subscribe to resource group")]

## <a name="sample-script---preview-extension"></a>Voorbeeldscript - preview-extensie

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-resource-group-preview/subscribe-to-resource-group-preview.sh "Subscribe to resource group")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script wordt de volgende opdracht gebruikt om het abonnement op de gebeurtenis te maken. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | Hiermee wordt een Event Grid-abonnement gemaakt. |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) - extensieversie | Hiermee wordt een Event Grid-abonnement gemaakt. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Query Event Grid subscriptions](../query-event-subscriptions.md) (Query's uitvoeren op Event Grid-abonnementen) voor informatie over het uitvoeren van query's op abonnementen.
* Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.
