---
title: Een regel voor een netwerkbeveiligingsgroep toevoegen in Powershell
description: 'Voorbeeld van een Azure PowerShell-script: hiermee wordt een netwerkbeveiligingsgroep toegevoegd om binnenkomend verkeer op een specifieke poort toe te staan.'
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 11/28/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 167fa74b04af7c086c2c95b0fdd56e5932483080
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87076198"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Een regel voor inkomend verkeer voor een netwerkbeveiligingsgroep toevoegen

Met dit voorbeeldscript maakt u een regel voor de netwerkbeveiligingsgroep om binnenkomend verkeer toe te staan op poort 8081.  Met het script wordt de netwerkbeveiligingsgroep opgehaald, een nieuwe netwerkbeveiligingsconfiguratieregel gemaakt en de netwerkbeveiligingsgroep bijgewerkt. Pas de parameters zo nodig aan.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installeer zo nodig Azure PowerShell met behulp van de instructies in de [Azure PowerShell-handleiding](/powershell/azure/). 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Haalt de `Microsoft.Network/networkSecurityGroups`-resource op. |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| Hiermee wordt de netwerkbeveiligingsgroep op naam opgehaald.|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hiermee voegt u de configuratie van een netwerkbeveiligingsregel toe aan een netwerkbeveiligingsgroep. |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| Hiermee wordt de doelstatus voor een netwerkbeveiligingsgroep ingesteld.|

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).
