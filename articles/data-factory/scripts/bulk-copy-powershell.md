---
title: Gegevens bulksgewijs kopiëren met Power shell
description: Dit Power shell-script laat zien hoe u Azure Data Factory gebruikt voor het kopiëren van gegevens van een brongegevens archief naar een doel gegevens archief.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: 16cb08d49efcd46e2746c5031793af8b747a2b31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373569"
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Power shell-script: meerdere tabellen bulksgewijs kopiëren met behulp van Azure Data Factory

Met dit Power shell-voorbeeld script worden gegevens uit meerdere tabellen in Azure SQL Database naar Azure Synapse Analytics gekopieerd.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Zie [zelf studie: bulksgewijs kopiëren](../tutorial-bulk-copy.md#prerequisites) voor de vereisten voor het uitvoeren van dit voor beeld.

## <a name="sample-script"></a>Voorbeeldscript

> [!IMPORTANT]
> Met dit script worden JSON-bestanden gemaakt waarmee Data Factory entiteiten (gekoppelde service, gegevensset en pijp lijn) op de vaste schijf worden gedefinieerd in de c:\ map.

[!code-powershell[main](../../../powershell_scripts/data-factory/bulk-copy-from-sql-databse-to-sql-data-warehouse/bulk-copy-from-sql-database-to-sql-data-warehouse.ps1 "Bulk copy from Azure SQL Database => Azure Synapse Analytics")]

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
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-azdatafactoryv2) | Een data factory maken. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-azdatafactoryv2linkedservice) | Hiermee maakt u een gekoppelde service in de data factory. Een gekoppelde service koppelt een gegevens archief of kan worden berekend op een data factory. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/set-azdatafactoryv2dataset) | Hiermee maakt u een gegevensset in de data factory. Een gegevensset vertegenwoordigt invoer/uitvoer voor een activiteit in een pijp lijn. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-azdatafactoryv2pipeline) | Hiermee maakt u een pijp lijn in de data factory. Een pijp lijn bevat een of meer activiteiten die een bepaalde bewerking uitvoeren. In deze pijp lijn kopieert een Kopieer activiteit gegevens van de ene locatie naar een andere locatie in een Azure-Blob Storage. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-azdatafactoryv2pipeline) | Hiermee maakt u een uitvoering voor de pijp lijn. Met andere woorden, de pijp lijn uitvoeren. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-azdatafactoryv2activityrun) | Haalt Details op over de uitvoering van de activiteit (uitvoering van de activiteit) in de pijp lijn. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Data Factory Power shell-script voorbeelden vindt u in de [Azure Data Factory Power shell-scripts](../samples-powershell.md).