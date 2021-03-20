---
title: Een Service Fabric-cluster maken in Powershell
description: Azure PowerShell-voorbeeldscript - Een Service Fabric-cluster maken dat wordt beveiligd met een X.509-certificaat.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/19/2018
ms.author: atsenthi
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: bae42c9ec8643da1563fb6b566d8db4af6039225
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89078942"
---
# <a name="create-a-service-fabric-cluster"></a>Een Service Fabric-cluster maken

Met dit voorbeeldscript wordt een Service Fabric-cluster met vijf knooppunten gemaakt dat wordt beveiligd met een X.509-certificaat.  Met de opdracht wordt een zelfondertekend certificaat gemaakt en geüpload naar een nieuwe sleutelkluis. Het certificaat wordt ook gekopieerd naar een lokale map.  Stel de *-OS* parameter in om de versie van Windows of Linux die op de clusterknooppunten wordt uitgevoerd te kiezen.  Pas de parameters zo nodig aan.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installeer zo nodig de Azure PowerShell volgens de instructies in de [Azure PowerShell handleiding](/powershell/azure/) en voer vervolgens `Connect-AzAccount` uit om verbinding maken met Azure. 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Nadat het voorbeeldscript is uitgevoerd, kan de volgende opdracht worden gebruikt om de resourcegroep, het cluster en alle gerelateerde resources te verwijderen.

```powershell
$groupname="mysfclustergroup"
Remove-AzResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) | Hiermee wordt een nieuw Service Fabric-cluster gemaakt. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).

Meer voorbeelden voor Azure Powershell voor Azure Service Fabric vindt u in de [voorbeelden van Azure PowerShell](../service-fabric-powershell-samples.md).
