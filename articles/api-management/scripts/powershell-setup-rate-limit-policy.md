---
title: Voorbeeld van PowerShell-script - beleid voor frequentielimiet instellen | Microsoft Docs
description: Ontdek hoe u met Azure PowerShell beleid voor frequentielimieten instelt. Bekijk een voorbeeldscript en extra beschikbare resources.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.topic: sample
ms.date: 11/16/2017
ms.author: apimpm
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: f78639b4f693b5dd0d414bf603b5518ddd2bf5da
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818562"
---
# <a name="set-up-rate-limit-policy"></a>Beleid voor frequentielimiet instellen

Met dit voorbeeldscript stelt u het beleid in voor frequentielimieten. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, wordt voor deze zelfstudie moduleversie 1.0 of hoger van Azure PowerShell vereist. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/api-management/setup-rate-limit-policy/setup_rate_limit_policy.ps1 "Set up rate limit policy")]

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```
## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).

Meer voorbeelden voor Azure PowerShell voor Azure API Management vindt u in [Voorbeelden van PowerShell](../powershell-samples.md).
