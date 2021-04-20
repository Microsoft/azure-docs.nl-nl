---
title: Regio's verplaatsen voor resources in Microsoft.Resources
description: Laat zien hoe u resources in de naamruimte Microsoft.Resources naar nieuwe regio's verplaatst.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 898e5efef22f76dc07395fcfcad413ef4582dafd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725868"
---
# <a name="move-microsoftresources-resources-to-new-region"></a>Resources van Microsoft.Resources verplaatsen naar een nieuwe regio

Mogelijk moet u een bestaande resource verplaatsen naar een nieuwe regio. In dit artikel wordt beschreven hoe u twee resourcetypen ( templateSpecs en deploymentScripts) verplaatst die zich in de naamruimte Microsoft.Resources.

## <a name="move-template-specs-to-new-region"></a>Sjabloonspecificaties verplaatsen naar een nieuwe regio

Als u een [sjabloonspecificatie](../templates/template-specs.md) in één regio hebt en deze wilt verplaatsen naar een nieuwe regio, kunt u de sjabloonspecificatie exporteren en opnieuw uitvoeren.

1. Gebruik de opdracht om een bestaande sjabloonspecificatie te exporteren. Geef voor de parameterwaarden de waarden op die overeenkomen met de sjabloonspecificatie die u wilt exporteren.

   Gebruik Azure PowerShell voor meer informatie:

   ```azurepowershell
   Export-AzTemplateSpec `
     -ResourceGroupName demoRG `
     -Name demoTemplateSpec `
     -Version 1.0 `
     -OutputFolder c:\export
   ```

   Gebruik voor Azure CLI:

   ```azurecli
   az template-specs export \
     --resource-group demoRG \
     --name demoTemplateSpec \
     --version 1.0 \
     --output-folder c:\export
   ```

1. Gebruik de geëxporteerde sjabloonspecificatie om een nieuwe sjabloonspecificatie te maken. De volgende voorbeelden worden `westus` voor de nieuwe regio weer gegeven, maar u kunt de want-regio verstrekken.

   Voor Azure PowerShell gebruikt u:

   ```azurepowershell
   New-AzTemplateSpec `
     -Name movedTemplateSpec `
     -Version 1.0 `
     -ResourceGroupName newRG `
     -Location westus `
     -TemplateJsonFile c:\export\1.0.json
   ```

   Gebruik voor Azure CLI:

   ```azurecli
   az template-specs create \
     --name movedTemplateSpec \
     --version "1.0" \
     --resource-group newRG \
     --location "westus" \
     --template-file "c:\export\demoTemplateSpec.json"
   ```

## <a name="move-deployment-scripts-to-new-region"></a>Implementatiescripts verplaatsen naar een nieuwe regio

1. Selecteer de resourcegroep die het implementatiescript bevat dat u wilt verplaatsen naar een nieuwe regio.

1. [Exporteert u de sjabloon](../templates/export-template-portal.md). Selecteer bij het exporteren het implementatiescript en eventuele andere vereiste resources.

1. Verwijder in de geëxporteerde sjabloon de volgende eigenschappen:

   * tenantId
   * principalId
   * clientId

1. De geëxporteerde sjabloon heeft een hardcoded waarde voor de regio van het implementatiescript.

   ```json
   "location": "westus2",
   ```

   Wijzig de sjabloon om een parameter toe te staan voor het instellen van de locatie. Zie Resourcelocatie instellen [in ARM-sjabloon voor meer informatie](../templates/resource-location.md)

   ```json
   "location": "[parameters('location')]",
   ```

1. [Implementeer de geëxporteerde sjabloon en](../templates/deploy-powershell.md) geef een nieuwe regio op voor het implementatiescript.

## <a name="next-steps"></a>Volgende stappen

* Zie Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement voor meer informatie over het verplaatsen van [resources naar een nieuwe resourcegroep of een nieuw abonnement.](move-resource-group-and-subscription.md)
* Zie Resources verplaatsen tussen regio's voor meer informatie over het verplaatsen [van resources naar een nieuwe regio.](move-resources-overview.md#move-resources-across-regions)
