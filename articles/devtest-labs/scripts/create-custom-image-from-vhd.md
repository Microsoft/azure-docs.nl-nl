---
title: 'PowerShell: aangepaste installatiekopie maken van VHD-bestand in Azure Lab Services'
description: Met dit PowerShell-script wordt een aangepaste installatiekopie gemaakt van een VHD-bestand in Azure Lab Services.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: d22e6e1d226e826bf114a0fdb378879b828d9b4a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96022231"
---
# <a name="use-powershell-to-create-a-custom-image-from-a-vhd-file-in-azure-lab-services"></a>PowerShell gebruiken om een aangepaste installatiekopie te maken van een VHD-bestand in Azure Lab Services

Met dit PowerShell-voorbeeldscript wordt een aangepaste installatiekopie gemaakt van een VHD-bestand in Azure Lab Services

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Vereisten
* **Een lab**. Het script vereist dat u een bestaand lab hebt. 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-image-from-vhd/create-custom-image-from-vhd.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Haalt resources op. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Hiermee worden de toegangssleutels voor Azure opslagaccounts weergegeven. |
| [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) | Voegt een Azure-implementatie aan een resourcegroep toe. |

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/) voor meer informatie over Azure PowerShell.

Er zijn extra voorbeelden van Azure Lab Services PowerShell-scripts te vinden in de [PowerShell-voorbeelden van Azure Lab Services CLI](../samples-powershell.md).
