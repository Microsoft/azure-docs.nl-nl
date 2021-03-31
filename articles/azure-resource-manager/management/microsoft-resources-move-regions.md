---
title: Regio's verplaatsen voor resources in micro soft. resources
description: Weer geven hoe resources die zich in de micro soft. resources-naam ruimte bevinden, worden verplaatst naar nieuwe regio's.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 098e5c36969da12966d793b6e1eddd4f79701ead
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88951052"
---
# <a name="move-microsoftresources-resources-to-new-region"></a>Resources van micro soft. resources naar een nieuwe regio verplaatsen

Mogelijk moet u een bestaande resource verplaatsen naar een nieuwe regio. In dit artikel wordt beschreven hoe u twee resource typen kunt verplaatsen: templateSpecs en deploymentScripts, die zich in de micro soft. resources-naam ruimte bevinden.

## <a name="move-template-specs-to-new-region"></a>Sjabloon specificaties verplaatsen naar nieuwe regio

Als u een [sjabloon specificatie](../templates/template-specs.md) in één regio hebt en u deze wilt verplaatsen naar een nieuwe regio, kunt u de sjabloon specificatie exporteren en opnieuw implementeren.

1. Gebruik de opdracht om een bestaande sjabloon specificatie te exporteren. Geef voor de parameter waarden de waarden op die overeenkomen met de sjabloon specificatie die u wilt exporteren.

   Voor Azure PowerShell gebruikt u:

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

1. Gebruik de geëxporteerde sjabloon specificatie om een nieuwe sjabloon specificatie te maken. In de volgende voor beelden ziet u `westus` voor de nieuwe regio, maar u kunt de gewenste regio opgeven.

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

## <a name="move-deployment-scripts-to-new-region"></a>Implementatie scripts verplaatsen naar nieuwe regio

1. Selecteer de resource groep die het implementatie script bevat dat u wilt verplaatsen naar een nieuwe regio.

1. [Exporteer de sjabloon](../templates/export-template-portal.md). Bij het exporteren selecteert u het implementatie script en eventuele andere vereiste resources.

1. Verwijder de volgende eigenschappen in de geëxporteerde sjabloon:

   * tenantId
   * principalId
   * clientId

1. De geëxporteerde sjabloon bevat een hardcoded waarde voor de regio van het implementatie script.

   ```json
   "location": "westus2",
   ```

   Wijzig de sjabloon zodanig dat een para meter voor het instellen van de locatie is toegestaan. Zie voor meer informatie [resource locatie instellen in arm-sjabloon](../templates/resource-location.md)

   ```json
   "location": "[parameters('location')]",
   ```

1. [Implementeer de geëxporteerde sjabloon](../templates/deploy-powershell.md) en geef een nieuwe regio op voor het implementatie script.

## <a name="next-steps"></a>Volgende stappen

* Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](move-resource-group-and-subscription.md)als u meer wilt weten over het verplaatsen van resources naar een nieuwe resource groep of een nieuw abonnement.
* Zie [Azure-resources verplaatsen tussen regio's](move-region.md)voor meer informatie over het verplaatsen van resources naar een nieuwe regio.
