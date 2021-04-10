---
title: Voorbeelden van Azure PowerShell - Apps installeren
description: Met dit script maakt u een virtuele-machineschaalset met Windows Server 2016 die de Custom Script-extensie gebruikt voor het installeren van een eenvoudige webtoepassing.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 0f985d9f43074f8dc9f8ad93aba71e2dce8ae7b9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932264"
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-powershell"></a>Toepassing installeren in een virtuele-machineschaalset met PowerShell
Met dit script maakt u een virtuele-machineschaalset met Windows Server 2016 die de Custom Script-extensie gebruikt voor het installeren van een eenvoudige webtoepassing. Nadat het script is uitgevoerd, kunt u de web-app via een webbrowser openen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/install-apps/install-apps.ps1 "Install apps into a scale set")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie
Gebruik de volgende opdracht om de resourcegroep, de schaalset en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script
In dit script worden de volgende opdrachten gebruikt om de implementatie te maken. Elk item in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzVmss](/powershell/module/az.compute/new-azvmss) | Hiermee maakt u de virtuele-machineschaalset en alle ondersteunende resources, met inbegrip van virtueel netwerk, load balancer en NAT-regels. |
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Hiermee wordt informatie over de virtuele-machineschaalset opgehaald. |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) | Hiermee voegt u een VM-extensie voor Custom Script toe om een eenvoudige webtoepassing te installeren. |
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss) | Hiermee werkt u het model van de virtuele-machineschaalset bij om de VM-extensie toe te passen. |
| [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) | Hiermee haalt u informatie op over het toegewezen openbare IP-adres dat door de load balancer wordt gebruikt. |
|  [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep en alle daarin opgenomen resources. |

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).
