---
title: Azure SSIS Integration runtime implementeren met behulp van Power shell
description: Met dit Power shell-script wordt een Azure SSIS Integration runtime gemaakt waarmee SSIS-pakketten kunnen worden uitgevoerd in de Cloud.
ms.service: data-factory
ms.topic: article
ms.author: sawinark
author: swinarko
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: 158e2db5261ce35713e1564cd24f0dd1d563ab78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100370203"
---
# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>Power shell-script-Azure SSIS Integration runtime implementeren

Met dit Power shell-voorbeeld script wordt een Azure SSIS Integration runtime gemaakt waarmee uw SSIS-pakketten in azure kunnen worden uitgevoerd.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/data-factory/deploy-azure-ssis-integration-runtime/deploy-azure-ssis-integration-runtime.ps1 "Deploy Azure-SSIS Integration Runtime")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Nadat u het voorbeeld script hebt uitgevoerd, kunt u de volgende opdracht gebruiken om de resource groep en alle bijbehorende resources te verwijderen:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Als u de data factory uit de resource groep wilt verwijderen, voert u de volgende opdracht uit: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Een data factory maken. |
| [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) | Hiermee wordt een Azure SSIS Integration runtime gemaakt waarmee SSIS-pakketten kunnen worden uitgevoerd in de Cloud |
| [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) | Hiermee wordt de Azure SSIS Integration runtime gestart. |
| [Get-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/get-Azdatafactoryv2integrationruntime) | Haalt informatie op over de Azure SSIS Integration runtime. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Data Factory Power shell-voorbeeld scripts vindt u in de [Azure Data Factory Power shell](../samples-powershell.md)-voor beelden.