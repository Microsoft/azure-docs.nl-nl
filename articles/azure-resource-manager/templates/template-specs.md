---
title: SjabSjabloonspecificaties maken en implementeren maken en implementeren
description: Hierin wordt beschreven hoe u sjabloon specificaties maakt en deze deelt met andere gebruikers in uw organisatie.
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 76573e4415dffb2212dd025ed486d834446d3851
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043895"
---
# <a name="azure-resource-manager-template-specs-preview"></a>Azure Resource Manager sjabloon specificaties (preview-versie)

Een sjabloon specificatie is een resource type voor het opslaan van een Azure Resource Manager sjabloon (ARM-sjabloon) in azure voor latere implementatie. Met dit resource type kunt u ARM-Sjablonen delen met andere gebruikers in uw organisatie. Net als elke andere Azure-resource kunt u Azure RBAC (op rollen gebaseerd toegangs beheer) gebruiken om de sjabloon specificatie te delen.

**Micro soft. resources/templateSpecs** is het resource type voor sjabloon specificaties. Het bestaat uit een hoofd sjabloon en een wille keurig aantal gekoppelde sjablonen. De sjabloon specificaties van Azure worden veilig opgeslagen in resource groepen. Sjabloon specificaties ondersteunen [versie beheer](#versioning).

Als u de sjabloon specificatie wilt implementeren, gebruikt u standaard Azure-hulpprogram ma's zoals Power shell, Azure CLI, Azure Portal, REST en andere ondersteunde Sdk's en clients. U gebruikt dezelfde opdrachten als voor de sjabloon.

> [!NOTE]
> Sjabloonspecificaties is momenteel beschikbaar als preview-versie. Voor gebruik met Azure PowerShell moet u [versie 5.0.0 of hoger](/powershell/azure/install-az-ps) installeren. Voor gebruik met Azure CLI hebt u [versie 2.14.2 of hoger](/cli/azure/install-azure-cli) nodig.

## <a name="why-use-template-specs"></a>Waarom sjabloon specificaties gebruiken?

Als u uw sjablonen momenteel in een GitHub-opslag plaats of-opslag account hebt, kunt u een aantal uitdagingen uitvoeren wanneer u de sjablonen probeert te delen en gebruiken. Een gebruiker kan deze alleen implementeren als de sjabloon lokaal of de URL voor de sjabloon openbaar toegankelijk is. Om deze beperking te omzeilen, kunt u kopieën van de sjabloon delen met gebruikers die deze moeten implementeren, of toegang tot de opslag plaats of het opslag account openen. Wanneer gebruikers een eigen lokale kopie van een sjabloon hebben, kunnen deze kopieën uiteindelijk afwijken van de oorspronkelijke sjabloon. Wanneer u een opslag plaats-of opslag account openbaar maakt, kunt u onbedoelde gebruikers toegang geven tot de sjabloon.

Het voor deel van het gebruik van sjabloon specificaties is dat u canonieke sjablonen kunt maken en delen met teams in uw organisatie. De sjabloon specificaties zijn veilig omdat ze beschikbaar zijn voor Azure Resource Manager voor implementatie, maar niet toegankelijk zijn voor gebruikers zonder Azure RBAC-machtiging. Gebruikers hebben alleen lees toegang tot de sjabloon specificatie nodig om de sjabloon te implementeren, zodat u de sjabloon kunt delen zonder dat anderen deze kunnen wijzigen.

De sjablonen die u in een sjabloon specificatie opneemt, moeten worden geverifieerd door beheerders in uw organisatie om de vereisten en richt lijnen van de organisatie te volgen.

## <a name="create-template-spec"></a>Sjabloonspecificatie maken

In het volgende voor beeld ziet u een eenvoudige sjabloon voor het maken van een opslag account in Azure.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[concat('store', uniquestring(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      }
    }
  ]
}
```

Wanneer u de sjabloon specificatie maakt, wordt het hoofd sjabloon bestand door gegeven aan de Power shell-of CLI-opdrachten. Als de hoofd sjabloon verwijst naar gekoppelde sjablonen, worden de opdrachten gevonden en verpakt om de sjabloon specificatie te maken. Zie [een sjabloon specificatie maken met gekoppelde sjablonen](#create-a-template-spec-with-linked-templates)voor meer informatie.

Een sjabloon specificatie maken met behulp van:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0a -ResourceGroupName templateSpecsRg -Location westus2 -TemplateFile ./mainTemplate.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0a" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

---

U kunt alle sjabloon specificaties in uw abonnement weer geven met behulp van:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts list
```

---

U kunt details van een sjabloon specificatie bekijken, inclusief de versies met:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts show \
    --name storageSpec \
    --resource-group templateSpecRG \
    --version "1.0a"
```

---

## <a name="deploy-template-spec"></a>Sjabloonspecificatie implementeren

Nadat u de sjabloon specificatie hebt gemaakt, kunnen gebruikers met **Lees** toegang tot de sjabloon specificatie deze implementeren. Zie [zelf studie: een groep toegang verlenen tot Azure-resources met behulp van Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md)voor meer informatie over het verlenen van toegang.

Sjabloon specificaties kunnen worden geïmplementeerd via de portal, Power shell, Azure CLI of als een gekoppelde sjabloon in een grotere sjabloon implementatie. Gebruikers in een organisatie kunnen een sjabloon specificatie implementeren naar elk bereik in azure (resource groep, abonnement, beheer groep of Tenant).

In plaats van een pad of URI voor een sjabloon door te geven, implementeert u een sjabloon specificatie door de resource-ID op te geven. De resource-ID heeft de volgende indeling:

**/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versions/{template-spec-version}**

U ziet dat de resource-ID een versie naam bevat voor de sjabloon specificatie.

U implementeert bijvoorbeeld een sjabloon specificatie met de volgende opdracht.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0a"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0a"

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

In de praktijk voert u meestal uit `Get-AzTemplateSpec` of `az ts show` haalt u de id op van de sjabloon specificatie die u wilt implementeren.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0a).Versions.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0a" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

U kunt ook een URL in de volgende indeling openen om een sjabloon specificatie te implementeren:

```url
https://portal.azure.com/#create/Microsoft.Template/templateSpecVersionId/%2fsubscriptions%2f{subscription-id}%2fresourceGroups%2f{resource-group-name}%2fproviders%2fMicrosoft.Resources%2ftemplateSpecs%2f{template-spec-name}%2fversions%2f{template-spec-version}
```

## <a name="parameters"></a>Parameters

Het door geven van para meters aan sjabloon spec is precies hetzelfde als het door geven van para meters aan een ARM-sjabloon. Voeg de parameter waarden in inline of in een parameter bestand toe.

Als u een para meter inline wilt door geven, gebruikt u:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -StorageAccountType Standard_GRS
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters storageAccountType='Standard_GRS'
```

---

Als u een lokaal parameter bestand wilt maken, gebruikt u:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "StorageAccountType": {
      "value": "Standard_GRS"
    }
  }
}
```

En geef dat parameter bestand door:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -TemplateParameterFile ./mainTemplate.parameters.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters "./mainTemplate.parameters.json"
```

---

## <a name="versioning"></a>Versiebeheer

Wanneer u een sjabloon specificatie maakt, geeft u er een versie naam voor op. Wanneer u de sjabloon code herhaalt, kunt u een bestaande versie bijwerken (voor hotfixes) of een nieuwe versie publiceren. De versie is een teken reeks. U kunt ervoor kiezen om een versie systeem te volgen, met inbegrip van semantische versie beheer. Gebruikers van de sjabloon specificatie kunnen de versie naam opgeven die ze willen gebruiken bij het implementeren ervan.

## <a name="use-tags"></a>Tags gebruiken

[Tags](../management/tag-resources.md) zijn een hulpmiddel bij het logisch ordenen van uw resources. U kunt labels toevoegen aan sjabloon specificaties met behulp van Azure PowerShell en Azure CLI:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzTemplateSpec `
  -Name storageSpec `
  -Version 1.0a `
  -ResourceGroupName templateSpecsRg `
  -Location westus2 `
  -TemplateFile ./mainTemplate.json `
  -Tag @{Dept="Finance";Environment="Production"}
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0a" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json" \
  --tags Dept=Finance Environment=Production
```

---

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzTemplateSpec `
  -Name storageSpec `
  -Version 1.0a `
  -ResourceGroupName templateSpecsRg `
  -Location westus2 `
  -TemplateFile ./mainTemplate.json `
  -Tag @{Dept="Finance";Environment="Production"}
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts update \
  --name storageSpec \
  --version "1.0a" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json" \
  --tags Dept=Finance Environment=Production
```

---

Bij het maken of wijzigen van een sjabloon spec met de opgegeven versie parameter, maar zonder de tag/Tags-para meter:

- Als de sjabloon specificatie bestaat en labels heeft, maar de versie niet bestaat, neemt de nieuwe versie dezelfde tags over als de bestaande sjabloon specificatie.

Bij het maken of wijzigen van een sjabloon spec met de para meter tag/Tags en de opgegeven versie parameter:

- Als de sjabloon specificatie en de versie niet bestaan, worden de tags toegevoegd aan zowel de nieuwe sjabloon specificatie als de nieuwe versie.
- Als de sjabloon specificatie bestaat, maar de versie niet bestaat, worden de tags alleen toegevoegd aan de nieuwe versie.
- Als zowel de sjabloon specificatie als de versie bestaan, zijn de labels alleen van toepassing op de versie.

Wanneer u een sjabloon wijzigt waarvoor de para meter tag/Tags is opgegeven, maar er geen versie parameter is opgegeven, worden de tags alleen toegevoegd aan de sjabloon specificatie.

## <a name="create-a-template-spec-with-linked-templates"></a>Een sjabloon specificatie met gekoppelde sjablonen maken

Als de hoofd sjabloon voor uw sjabloon specificatie verwijst naar gekoppelde sjablonen, kunnen de Power shell-en CLI-opdrachten de gekoppelde sjablonen automatisch vinden en verpakken vanaf uw lokale station. U hoeft Storage-accounts of opslag plaatsen niet hand matig te configureren voor het hosten van de sjabloon specificaties: alles is zelf opgenomen in de sjabloon specificatie resource.

Het volgende voor beeld bestaat uit een hoofd sjabloon met twee gekoppelde sjablonen. Het voor beeld is slechts een fragment van de sjabloon. U ziet dat er een eigenschap met de naam wordt gebruikt om een `relativePath` koppeling naar de andere sjablonen te maken. U moet `apiVersion` `2020-06-01` of hoger voor de implementaties Resource gebruiken.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/webapp.json"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/database.json"
        }
      }
    }
  ],
  "outputs": {}
}
```

Wanneer de Power shell-of CLI-opdracht voor het maken van de sjabloon specificatie wordt uitgevoerd voor het voor gaande voor beeld, wordt met de opdracht drie bestanden gevonden: de hoofd sjabloon, de sjabloon web-app ( `webapp.json` ) en de database sjabloon ( `database.json` ) en verpakt in de sjabloon specificatie.

Zie [zelf studie: een sjabloon specificatie maken met gekoppelde sjablonen](template-specs-create-linked.md)voor meer informatie.

## <a name="deploy-template-spec-as-a-linked-template"></a>Sjabloon specificatie als gekoppelde sjabloon implementeren

Zodra u een sjabloon specificatie hebt gemaakt, kunt u deze eenvoudig opnieuw gebruiken uit een ARM-sjabloon of een andere sjabloon specificatie. U koppelt een sjabloon specificatie door de resource-ID toe te voegen aan uw sjabloon. De gekoppelde sjabloon specificatie wordt automatisch geïmplementeerd wanneer u de hoofd sjabloon implementeert. Met dit gedrag kunt u modulaire sjabloon specificaties ontwikkelen en deze indien nodig opnieuw gebruiken.

U kunt bijvoorbeeld een sjabloon specificatie maken waarmee netwerk bronnen worden geïmplementeerd, en een andere sjabloon specificatie waarmee opslag bronnen worden geïmplementeerd. In ARM-sjablonen koppelt u op elk gewenst moment een koppeling naar deze twee sjabloon specificaties om netwerk-of opslag bronnen te configureren.

Het volgende voor beeld is vergelijkbaar met het vorige voor beeld, maar u gebruikt de `id` eigenschap om een koppeling te maken naar een sjabloon specificatie in plaats van de `relativePath` eigenschap om te koppelen aan een lokale sjabloon. Gebruiken `2020-06-01` voor API-versie voor de implementaties-resource. In het voor beeld bevinden de sjabloon specificaties zich in een resource groep met de naam **templateSpecsRG**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "networkingDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'networkingSpec', '1.0a')]"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0a')]"
        }
      }
    }
  ],
  "outputs": {}
}
```

Zie [zelf studie: een sjabloon specificatie implementeren als gekoppelde sjabloon](template-specs-deploy-linked-template.md)voor meer informatie over het koppelen van sjabloon specificaties.

## <a name="next-steps"></a>Volgende stappen

* Als u een sjabloon specificatie wilt maken en implementeren, raadpleegt u [Quick Start: Create and Deploy template spec](quickstart-create-template-specs.md).

* Zie [zelf studie: een sjabloon specificatie maken met gekoppelde sjablonen](template-specs-create-linked.md)voor meer informatie over het koppelen van sjablonen in sjabloon specificaties.

* Zie [zelf studie: een sjabloon specificatie implementeren als gekoppelde sjabloon](template-specs-deploy-linked-template.md)voor meer informatie over het implementeren van een sjabloon specificatie als een gekoppelde sjabloon.
