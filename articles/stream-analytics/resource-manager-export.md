---
title: Een Azure Resource Manager-sjabloon voor een Azure Stream Analytics-taak exporteren
description: In dit artikel wordt beschreven hoe u een Azure Resource Manager sjabloon voor uw Azure Stream Analytics taak exporteert.
services: stream-analytics
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: aa17d83dcc14675db5ff6aa4597314baffbffdbb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98015416"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Een Azure Resource Manager-sjabloon voor een Azure Stream Analytics-taak exporteren

Met [Azure Resource Manager sjablonen](../azure-resource-manager/templates/overview.md) kunt u de infra structuur als code implementeren. De sjabloon is een JavaScript Object Notation-bestand (JSON) waarmee de infra structuur en configuratie voor uw resources worden gedefinieerd. U geeft de resources op die u wilt implementeren en de eigenschappen voor deze resources.

U kunt een Azure Stream Analytics taak opnieuw implementeren door de sjabloon Azure Resource Manager te exporteren.

## <a name="open-a-job-in-vs-code"></a>Open een taak in VS code

Voordat u een sjabloon kunt exporteren, moet u eerst een bestaande Stream Analytics-taak openen in Visual Studio code. 

Als u een taak wilt exporteren naar een lokaal project, zoekt u de taak die u wilt exporteren in de **Stream Analytics Verkenner** in de Azure Portal. Selecteer op de pagina **query** de optie **openen in Visual Studio**. Selecteer vervolgens **Visual Studio code**.

![Open Stream Analytics-taak in Visual Studio code](./media/resource-manager-export/open-job-vs-code.png)

Zie [Visual Studio code Quick](quick-create-visual-studio-code.md)start (Engelstalig) voor meer informatie over het gebruik van Visual Studio code voor het beheren van stream Analytics taken.

## <a name="compile-the-script"></a>Het script compileren 

De volgende stap is het compileren van het taak script naar een Azure Resource Manager sjabloon. Controleer voordat u het script compileert of er ten minste één invoer en één uitvoer is geconfigureerd voor uw taak. Als er geen invoer of uitvoer is geconfigureerd, moet u eerst de invoer en uitvoer configureren.

1. Ga in Visual Studio code naar het bestand *Transformation. asaql* van uw taak.

   ![Trans formatie. asaql-bestand in Visual Studio code](./media/resource-manager-export/transformation-asaql.png)

1. Klik met de rechter muisknop op het bestand *Transformation. asaql* en selecteer **ASA: script compileren** in het menu.

1. U ziet dat een **Deploy** -map wordt weer gegeven in de werk ruimte stream Analytics taak.

1. Verken de *JobTemplate.jsop* bestand, de Azure resource management-sjabloon die wordt gebruikt voor de implementatie.

## <a name="complete-the-parameters-file"></a>Het parameter bestand volt ooien

Vervolgens voltooit u het Azure resource management-sjabloon parameter bestand.

1. Open de *JobTemplate.parameters.jsvoor* het bestand dat zich bevindt in de map **Deploy** van uw werk ruimte stream Analytics taak in Visual Studio code.

1. U ziet dat de invoer-en uitvoer sleutels null zijn. Vervang de Null-waarden door de daad werkelijke toegangs sleutels voor de invoer-en uitvoer resources.

1. Sla het parameter bestand op.

## <a name="deploy-using-templates"></a>Implementeren met behulp van sjablonen

U bent klaar om uw Azure Stream Analytics-taak te implementeren met behulp van de Azure Resource Manager sjablonen die u hebt gegenereerd in de vorige sectie.

Voer de volgende opdracht uit in een Power shell-venster. Zorg ervoor dat u de reaplce van *ResourceGroupName*, *TemplateFile* en *TemplateParameterFile* met de werkelijke naam van de resource groep en de volledige bestands paden naar het *JobTemplate.jsop* en *JobTemplate.parameters.jsop* bestanden in de **map Deploy** van uw werk ruimte.

Als Azure PowerShell niet is geconfigureerd, volgt u de stappen in de [module Azure PowerShell installeren](/powershell/azure/install-Az-ps).

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>Volgende stappen

* [Azure Stream Analytics taken lokaal met live input testen met Visual Studio code](visual-studio-code-local-run-live-input.md)

* [Azure Stream Analytics-taken verkennen met Visual Studio code (preview)](visual-studio-code-explore-jobs.md)