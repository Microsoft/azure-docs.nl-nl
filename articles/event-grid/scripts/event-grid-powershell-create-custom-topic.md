---
title: Voorbeeld van Azure PowerShell-script - Aangepast onderwerp maken | Microsoft Docs
description: Dit artikel bevat een voorbeeld van een Azure PowerShell-script dat laat zien hoe u een Event Grid kunt maken voor een aangepast onderwerp.
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: tomfitz
ms.openlocfilehash: 915b8e13adaa440063f5db62b72fa39e1035d576
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87460776"
---
# <a name="create-event-grid-custom-topic-with-powershell"></a>Aangepast Event Grid-onderwerp maken met PowerShell

Met dit script wordt een aangepast Event Grid-onderwerp gemaakt.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/event-grid/create-custom-topic/create-custom-topic.ps1 "Create custom topic")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script wordt de volgende opdracht gebruikt om het aangepaste onderwerp te maken. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzEventGridTopic](/powershell/module/az.eventgrid/new-azeventgridtopic) | Hiermee wordt een aangepast Event Grid-onderwerp gemaakt. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Overzicht van door Azure beheerde toepassingen](../overview.md) voor algemene informatie over beheerde toepassingen.
* Zie [Documentatie over Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie over PowerShell.
