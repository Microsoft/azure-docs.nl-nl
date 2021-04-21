---
title: Sjabloonimlementatie what-if
description: Bepaal welke wijzigingen er in uw resources plaatsvinden voordat u een Azure Resource Manager implementeert.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: tomfitz
ms.openlocfilehash: 7e300f896bb11ed7c77738836f894cff41cc8bf3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781825"
---
# <a name="arm-template-deployment-what-if-operation"></a>ARM template deployment what-if operation (Wat-als-bewerking bij het implementeren van ARM-sjablonen)

Voordat u een Azure Resource Manager (ARM-sjabloon) implementeert, kunt u een voorbeeld bekijken van de wijzigingen die zullen plaatsvinden. Azure Resource Manager biedt de what-if-bewerking om u te laten zien hoe resources worden gewijzigd als u de sjabloon implementeert. Met de 'wat-als'-bewerking worden er geen wijzigingen doorgevoerd voor bestaande resources. In plaats daarvan worden de wijzigingen voorspeld, mocht de opgegeven sjabloon worden geïmplementeerd.

U kunt de what-if-bewerking gebruiken met Azure PowerShell, Azure CLI of REST API bewerkingen. Wat-als wordt ondersteund voor implementaties op resourcegroep-, abonnements-, beheergroep- en tenantniveau.

## <a name="install-azure-powershell-module"></a>Een Azure PowerShell installeren

Als u what-if wilt gebruiken in PowerShell, moet u versie **4.2 of hoger van de Az-module hebben.**

Als u de module wilt installeren, gebruikt u:

```powershell
Install-Module -Name Az -Force
```

Zie Install Azure PowerShell voor [meer informatie over het installeren Azure PowerShell.](/powershell/azure/install-az-ps)

## <a name="install-azure-cli-module"></a>Azure CLI-module installeren

Als u what-if wilt gebruiken in Azure CLI, moet u Azure CLI 2.14.0 of hoger hebben. Installeer indien nodig [de meest recente versie van Azure CLI](/cli/azure/install-azure-cli).

## <a name="see-results"></a>Resultaten bekijken

Wanneer u what-if gebruikt in PowerShell of Azure CLI, bevat de uitvoer resultaten met kleurcode die u helpen de verschillende typen wijzigingen te zien.

![Resource Manager-sjabloonimplementatie what-if-bewerking fullresourcepayload en wijzigingstypen](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

De tekstuitvoer is:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

> [!NOTE]
> De what-if-bewerking kan de [verwijzingsfunctie niet oplossen.](template-functions-resource.md#reference) Telkens wanneer u een eigenschap in stelt op een sjabloonexpressie die de verwijzingsfunctie bevat, verandert de eigenschap in what-if-rapporten. Dit gedrag teert omdat what-if de huidige waarde van de eigenschap (zoals of voor een Booleaanse waarde) vergelijkt met de niet-opgeloste `true` `false` sjabloonexpressie. Deze waarden komen natuurlijk niet overeen. Wanneer u de sjabloon implementeert, verandert de eigenschap alleen wanneer de sjabloonexpressie wordt opgelost naar een andere waarde.

## <a name="what-if-commands"></a>What-if-opdrachten

### <a name="azure-powershell"></a>Azure PowerShell

Als u een voorbeeld van wijzigingen wilt bekijken voordat u een sjabloon implementeert, gebruikt u [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) of [New-AzSubscriptionDeployment.](/powershell/module/az.resources/new-azdeployment) Voeg de `-Whatif` schakelparameter toe aan de implementatieopdracht.

* `New-AzResourceGroupDeployment -Whatif` voor resourcegroepimplementaties
* `New-AzSubscriptionDeployment -Whatif` en `New-AzDeployment -Whatif` voor implementaties op abonnementsniveau

U kunt de `-Confirm` schakelparameter gebruiken om een voorbeeld van de wijzigingen te bekijken en wordt gevraagd om door te gaan met de implementatie.

* `New-AzResourceGroupDeployment -Confirm` voor resourcegroepimplementaties
* `New-AzSubscriptionDeployment -Confirm` en `New-AzDeployment -Confirm` voor implementaties op abonnementsniveau

De voorgaande opdrachten retourneren een tekstsamenvatting die u handmatig kunt inspecteren. Gebruik [Get-AzResourceGroupDeploymentWhatIfResult](/powershell/module/az.resources/get-azresourcegroupdeploymentwhatifresult) of [Get-AzSubscriptionDeploymentWhatIfResult](/powershell/module/az.resources/get-azdeploymentwhatifresult)om een object op te halen dat u programmatisch kunt controleren op wijzigingen.

* `$results = Get-AzResourceGroupDeploymentWhatIfResult` voor resourcegroepimplementaties
* `$results = Get-AzSubscriptionDeploymentWhatIfResult` of `$results = Get-AzDeploymentWhatIfResult` voor implementaties op abonnementsniveau

### <a name="azure-cli"></a>Azure CLI

Als u een voorbeeld van wijzigingen wilt bekijken voordat u een sjabloon implementeert, gebruikt u:

* [az deployment group what-if](/cli/azure/deployment/group#az_deployment_group_what_if) voor resourcegroepimplementaties
* [az deployment sub what-if voor](/cli/azure/deployment/sub#az_deployment_sub_what_if) implementaties op abonnementsniveau
* [az deployment mg what-if voor](/cli/azure/deployment/mg#az_deployment_mg_what_if) implementaties van beheergroep
* [az deployment tenant what-if](/cli/azure/deployment/tenant#az_deployment_tenant_what_if) voor tenantimplementaties

U kunt de switch (of de korte vorm ) gebruiken om een voorbeeld van de wijzigingen te bekijken en wordt gevraagd om door te `--confirm-with-what-if` `-c` gaan met de implementatie. Voeg deze schakelknop toe aan:

* [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create)
* [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create).
* [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create)
* [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create)

Gebruik bijvoorbeeld `az deployment group create --confirm-with-what-if` of voor `-c` implementaties van resourcegroep.

De voorgaande opdrachten retourneren een tekstsamenvatting die u handmatig kunt inspecteren. Gebruik de schakelknop om een JSON-object op te halen dat u programmatisch kunt controleren op `--no-pretty-print` wijzigingen. Gebruik bijvoorbeeld voor `az deployment group what-if --no-pretty-print` implementaties van resourcegroep.

Als u de resultaten zonder kleuren wilt retourneren, opent u uw [Azure CLI-configuratiebestand.](/cli/azure/azure-cli-configuration) Stel **no_color** in op **ja.**

### <a name="azure-rest-api"></a>Azure REST-API

Gebruik REST API voor meer informatie:

* [Implementaties: What If](/rest/api/resources/deployments/whatif) voor resourcegroepimplementaties
* [Implementaties : What If abonnementsbereik voor](/rest/api/resources/deployments/whatifatsubscriptionscope) abonnementsimplementaties
* [Implementaties - What If het bereik van beheergroep voor implementaties](/rest/api/resources/deployments/whatifatmanagementgroupscope) van beheergroep
* [Implementaties: What If tenantbereik voor](/rest/api/resources/deployments/whatifattenantscope) tenantimplementaties.

## <a name="change-types"></a>Wijzigingstypen

De what-if-bewerking bevat zes verschillende typen wijzigingen:

- **Maken:** de resource bestaat momenteel niet, maar is gedefinieerd in de sjabloon. De resource wordt gemaakt.

- **Verwijderen:** dit wijzigingstype is alleen van toepassing wanneer u de [volledige modus voor](deployment-modes.md) implementatie gebruikt. De resource bestaat, maar wordt niet gedefinieerd in de sjabloon. Met de volledige modus wordt de resource verwijderd. Alleen resources die ondersteuning [bieden voor het verwijderen van de volledige](complete-mode-deletion.md) modus zijn opgenomen in dit wijzigingstype.

- **Negeren:** de resource bestaat wel, maar is niet gedefinieerd in de sjabloon. De resource wordt niet geïmplementeerd of gewijzigd.

- **NoChange:** de resource bestaat en wordt gedefinieerd in de sjabloon. De resource wordt opnieuw geïmplementeerd, maar de eigenschappen van de resource worden niet gewijzigd. Dit wijzigingstype wordt geretourneerd wanneer [ResultFormat](#result-format) is ingesteld op `FullResourcePayloads` . Dit is de standaardwaarde.

- **Wijzigen:** de resource bestaat en wordt gedefinieerd in de sjabloon. De resource wordt opnieuw geïmplementeerd en de eigenschappen van de resource worden gewijzigd. Dit wijzigingstype wordt geretourneerd wanneer [ResultFormat](#result-format) is ingesteld op `FullResourcePayloads` . Dit is de standaardwaarde.

- **Implementeren:** de resource bestaat en wordt gedefinieerd in de sjabloon. De resource wordt opnieuw geïmplementeerd. De eigenschappen van de resource worden mogelijk wel of niet gewijzigd. De bewerking retourneert dit wijzigingstype wanneer er niet genoeg informatie is om te bepalen of eigenschappen worden gewijzigd. U ziet deze voorwaarde alleen wanneer [ResultFormat](#result-format) is ingesteld op `ResourceIdOnly` .

## <a name="result-format"></a>Resultaatopmaak

U kunt het detailniveau bepalen dat wordt geretourneerd over de voorspelde wijzigingen. U hebt hiervoor twee opties:

* **FullResourcePayloads:** retourneert een lijst met resources die worden gewijzigd en details over de eigenschappen die worden gewijzigd
* **ResourceIdOnly:** retourneert een lijst met resources die worden gewijzigd

De standaardwaarde is **FullResourcePayloads.**

Gebruik de parameter voor `-WhatIfResultFormat` PowerShell-implementatieopdrachten. Gebruik in de programmatische objectopdrachten de `ResultFormat` parameter .

Gebruik voor Azure CLI de `--result-format` parameter .
 
De volgende resultaten tonen de twee verschillende uitvoerindelingen:

- Volledige nettoladingen van resources

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- Alleen resource-id

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>What-if-bewerking uitvoeren

### <a name="set-up-environment"></a>Omgeving instellen

Om te zien hoe what-if werkt, gaan we een aantal tests uitvoeren. Implementeer eerst een [sjabloon die een virtueel netwerk maakt.](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json) U gebruikt dit virtuele netwerk om te testen hoe wijzigingen worden gerapporteerd door what-if.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az group create \
  --name ExampleGroup \
  --location "Central US"
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

---

### <a name="test-modification"></a>Testaanpassingen

Nadat de implementatie is voltooid, kunt u de what-if-bewerking testen. Deze keer implementeert u een [sjabloon die het virtuele netwerk wijzigt.](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json) Er ontbreekt een van de oorspronkelijke tags, er is een subnet verwijderd en het adres voorvoegsel is gewijzigd.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az deployment group what-if \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

---

De wat-als-uitvoer lijkt op het volgende:

![Resource Manager wat-als-bewerkingsuitvoer voor sjabloonimplementatie](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

De tekstuitvoer is:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

Boven aan de uitvoer ziet u dat kleuren zijn gedefinieerd om het type wijzigingen aan te geven.

Onderaan de uitvoer ziet u dat de tag Eigenaar is verwijderd. Het adres voorvoegsel is gewijzigd van 10.0.0.0/16 in 10.0.0.0/15. Het subnet subnet001 is verwijderd. Onthoud dat deze wijzigingen niet zijn geïmplementeerd. U ziet een voorbeeld van de wijzigingen die zullen plaatsvinden als u de sjabloon implementeert.

Sommige eigenschappen die worden vermeld als verwijderd, worden niet daadwerkelijk gewijzigd. Eigenschappen kunnen onjuist worden gerapporteerd als verwijderd wanneer ze niet in de sjabloon staan, maar worden tijdens de implementatie automatisch ingesteld als standaardwaarden. Dit resultaat wordt beschouwd als 'ruis' in het what-if-antwoord. De uiteindelijk geïmplementeerde resource heeft de waarden die zijn ingesteld voor de eigenschappen. Naarmate de what-if-bewerking zich verder ontwikkelde, worden deze eigenschappen uit het resultaat gefilterd.

## <a name="programmatically-evaluate-what-if-results"></a>Programmatisch what-if-resultaten evalueren

Nu gaan we programmatisch de what-if-resultaten evalueren door de opdracht in te stellen op een variabele.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

U kunt een samenvatting van elke wijziging bekijken.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
results=$(az deployment group what-if --resource-group ExampleGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json" --no-pretty-print)
```

---

## <a name="confirm-deletion"></a>Verwijdering bevestigen

De what-if-bewerking ondersteunt het gebruik [van de implementatiemodus](deployment-modes.md). Wanneer de modus Volledig is ingesteld, worden resources die niet in de sjabloon staan, verwijderd. In het volgende voorbeeld wordt een sjabloon [geïmplementeerd waarin geen resources zijn gedefinieerd](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) in de volledige modus.

Als u de wijzigingen wilt bekijken voordat u een sjabloon implementeert, gebruikt u de parameter confirm switch met de implementatieopdracht. Als de wijzigingen zijn zoals verwacht, reageert u dat u wilt dat de implementatie wordt voltooid.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -Mode Complete `
  -Confirm `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group ExampleGroup \
  --mode Complete \
  --confirm-with-what-if \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

---

Omdat er geen resources zijn gedefinieerd in de sjabloon en de implementatiemodus is ingesteld om te worden voltooid, wordt het virtuele netwerk verwijderd.

![Resource Manager implementatie van sjabloon-what-if-bewerkingsuitvoer is voltooid](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

De tekstuitvoer is:

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001&quot;
      location:        &quot;centralus&quot;
      name:            &quot;vnet-001&quot;
      tags.CostCenter: &quot;12345&quot;
      tags.Owner:      &quot;Team A&quot;
      type:            &quot;Microsoft.Network/virtualNetworks&quot;

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is &quot;Y"):
```

U ziet de verwachte wijzigingen en u kunt bevestigen dat u de implementatie wilt uitvoeren.

## <a name="sdks"></a>SDK's

U kunt de what-if-bewerking gebruiken via de Azure SDK's.

* Gebruik [what-if voor](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.deploymentsoperations#what-if-resource-group-name--deployment-name--properties--location-none--custom-headers-none--raw-false--polling-true----operation-config-)Python.

* Gebruik voor Java [DeploymentWhatIf-klasse](/java/api/com.microsoft.azure.management.resources.deploymentwhatif).

* Gebruik voor .NET [DeploymentWhatIf-klasse](/dotnet/api/microsoft.azure.management.resourcemanager.models.deploymentwhatif).

## <a name="next-steps"></a>Volgende stappen

- Zie ARM-sjablonen testen met What-If in een pijplijn voor het [gebruik van de what-if-bewerking in een pijplijn.](https://4bes.nl/2021/03/06/test-arm-templates-with-what-if/)
- Als u onjuiste resultaten ziet van de what-if-bewerking, meldt u de problemen op [https://aka.ms/whatifissues](https://aka.ms/whatifissues) .
- Zie Preview-wijzigingen Microsoft Learn Azure-resources valideren met behulp van what-if en de [ARM-sjabloontesttoolkit](/learn/modules/arm-template-test/)voor een Microsoft Learn module over het gebruik van what if.
