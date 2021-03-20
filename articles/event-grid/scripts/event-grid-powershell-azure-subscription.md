---
title: Azure PowerShell - abonneren op Azure-abonnement
description: Dit artikel bevat een voorbeeld van een Azure PowerShell-script dat laat zien hoe u zich kunt abonneren op gebeurtenissen voor Event Grid voor een Azure-abonnement.
ms.devlang: powershell
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: 0aae1d64b789cedab6420ac1f323a2379bff17be
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87460810"
---
# <a name="subscribe-to-events-for-an-azure-subscription-with-powershell"></a>Abonneren op gebeurtenissen voor een Azure-abonnement met PowerShell

Met dit script maakt u een Event Grid-abonnement op de gebeurtenissen voor een Azure-abonnement.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Voorbeeldscript - stabiel

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-azure-subscription/subscribe-to-azure-subscription.ps1 "Subscribe to Azure subscription")]

## <a name="sample-script---preview-module"></a>Voorbeeldscript - preview-module

Voor het voorbeeldscript van deze preview is de Event Grid-module vereist. Voer `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery` uit om deze te installeren

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-azure-subscription-preview/subscribe-to-azure-subscription-preview.ps1 "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script wordt de volgende opdracht gebruikt om het abonnement op de gebeurtenis te maken. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) | Hiermee wordt een Event Grid-abonnement gemaakt. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Overzicht van door Azure beheerde toepassingen](../overview.md) voor algemene informatie over beheerde toepassingen.
* Zie [Documentatie over Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie over PowerShell.
