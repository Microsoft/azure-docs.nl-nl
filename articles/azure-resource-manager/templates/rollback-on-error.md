---
title: Fout bij het terugdraaien naar een geslaagde implementatie
description: Geef op dat een mislukte implementatie moet worden teruggedraaid naar een geslaagde implementatie.
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 742a8f16a2dce3204b48085759091540586a4522
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99492209"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Ongedaan maken bij fout bij geslaagde implementatie

Wanneer een implementatie mislukt, kunt u automatisch een eerdere, geslaagde implementatie uit de implementatie geschiedenis opnieuw implementeren. Deze functie is handig als u een bekende goede status hebt voor de implementatie van uw infra structuur en u wilt terugkeren naar deze status. U kunt een bepaalde eerdere implementatie of de laatste geslaagde implementatie opgeven.

> [!IMPORTANT]
> Met deze functie wordt een mislukte implementatie teruggedraaid door een eerdere implementatie opnieuw te implementeren. Dit resultaat kan afwijken van wat u zou verwachten van het ongedaan maken van de mislukte implementatie. Zorg ervoor dat u begrijpt hoe de eerdere implementatie opnieuw wordt geïmplementeerd.

## <a name="considerations-for-redeploying"></a>Overwegingen voor opnieuw implementeren

Voordat u deze functie gaat gebruiken, moet u rekening houden met de volgende informatie over de manier waarop de implementatie wordt verwerkt:

- De vorige implementatie wordt uitgevoerd met de [volledige modus](./deployment-modes.md#complete-mode), zelfs als u tijdens de eerdere implementatie de [incrementele modus](./deployment-modes.md#incremental-mode) hebt gebruikt. Opnieuw implementeren in de volledige modus kan leiden tot onverwachte resultaten wanneer de eerdere implementatie incrementeel werd gebruikt. De volledige modus houdt in dat alle resources die niet in de vorige implementatie zijn opgenomen, worden verwijderd. Geef een eerdere implementatie op die alle resources en de bijbehorende statussen vertegenwoordigt die u in de resource groep wilt bevinden. Zie [implementatie modi](./deployment-modes.md)voor meer informatie.
- De implementatie wordt precies zo uitgevoerd als deze eerder met dezelfde para meters is uitgevoerd. U kunt de para meters niet wijzigen.
- De herimplementatie heeft alleen invloed op de resources, maar wijzigingen in de gegevens worden niet beïnvloed.
- U kunt deze functie alleen gebruiken met implementaties van resource groepen. Er wordt geen ondersteuning geboden voor abonnementen, beheer groepen of implementaties op Tenant niveau. Zie [resource groepen en-resources op abonnements niveau maken](./deploy-to-subscription.md)voor meer informatie over de implementatie op abonnements niveau.
- U kunt deze optie alleen gebruiken bij implementaties op hoofd niveau. Implementaties van een geneste sjabloon zijn niet beschikbaar voor opnieuw implementeren.

Als u deze optie wilt gebruiken, moeten uw implementaties unieke namen hebben in de implementatie geschiedenis. Het is alleen mogelijk met unieke namen die een specifieke implementatie kan identificeren. Als u geen unieke namen hebt, kan een mislukte implementatie worden overschreven in de geschiedenis.

Als u een eerdere implementatie opgeeft die niet voor komt in de implementatie geschiedenis, retourneert het terugdraaien een fout.

## <a name="powershell"></a>PowerShell

Als u de laatste geslaagde implementatie opnieuw wilt implementeren, voegt u de `-RollbackToLastDeployment` para meter toe als een vlag.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Als u een specifieke implementatie opnieuw wilt implementeren, gebruikt u de `-RollBackDeploymentName` para meter en geeft u de naam van de implementatie op. De opgegeven implementatie moet zijn geslaagd.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Azure CLI

Als u de laatste geslaagde implementatie opnieuw wilt implementeren, voegt u de `--rollback-on-error` para meter toe als een vlag.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Als u een specifieke implementatie opnieuw wilt implementeren, gebruikt u de `--rollback-on-error` para meter en geeft u de naam van de implementatie op. De opgegeven implementatie moet zijn geslaagd.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>REST-API

Gebruik de volgende opdracht om de laatste geslaagde implementatie opnieuw te implementeren als de huidige implementatie mislukt:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Als u een specifieke implementatie opnieuw wilt implementeren als de huidige implementatie mislukt, gebruikt u:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

De opgegeven implementatie moet zijn geslaagd.

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Resource Manager implementatie modi](deployment-modes.md)voor meer informatie over de volledige en incrementele modus.
- Zie [inzicht in de structuur en syntaxis van Azure Resource Manager sjablonen](template-syntax.md)voor meer informatie over het definiëren van para meters in uw sjabloon.
