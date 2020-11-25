---
title: Label resources, resource groepen en abonnementen voor logische organisatie
description: Laat zien hoe u Tags toepast om Azure-resources te organiseren voor facturering en beheer.
ms.topic: conceptual
ms.date: 11/20/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9e9ef96a712e5ac2ba483170fb8ef9c89115b4f8
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95972556"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>Tags gebruiken om uw Azure-resources en-beheer hiërarchie te organiseren

U past tags toe op uw Azure-resources, resource groepen en abonnementen om ze logisch in een taxonomie te organiseren. Elke tag bestaat uit een naam en een waardepaar. U kunt de naam Omgeving en de waarde Productie bijvoorbeeld toepassen op alle resources in de productie.

Zie voor meer informatie over het implementeren van een coderings strategie de [hand leiding resource naam geven en Tags nemen](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).

> [!IMPORTANT]
> Namen van tags zijn hoofdletter gevoelig voor bewerkingen. Een tag met de naam van een tag, ongeacht het hoofdletter gebruik, wordt bijgewerkt of opgehaald. De resource provider kan echter de behuizing die u opgeeft voor de naam van de tag blijven gebruiken. U ziet dat de behuizing in kosten rapporten wordt weer gegeven.
> 
> Label waarden zijn hoofdletter gevoelig.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>Vereiste toegang

Als u labels wilt Toep assen op een resource, moet u schrijf toegang hebben tot het resource type **micro soft. resources/Tags** . Met de rol van [Label Inzender](../../role-based-access-control/built-in-roles.md#tag-contributor) kunt u Tags Toep assen op een entiteit zonder dat u toegang hebt tot de entiteit zelf. Op dit moment kan de rol van label Inzender geen tags Toep assen op resources of resource groepen via de portal. Het kan labels Toep assen op abonnementen via de portal. Het ondersteunt alle label bewerkingen via Power shell en REST API.  

De rol [Inzender](../../role-based-access-control/built-in-roles.md#contributor) verleent ook de vereiste toegang om labels toe te passen op een wille keurige entiteit. Als u labels wilt Toep assen op slechts één resource type, gebruikt u de rol Inzender voor die resource. Gebruik bijvoorbeeld de Inzender van de [virtuele machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)om labels toe te passen op virtuele machines.

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>Tags Toep assen

Azure PowerShell biedt twee opdrachten voor het Toep assen van labels: [New-AzTag](/powershell/module/az.resources/new-aztag) en [Update-AzTag](/powershell/module/az.resources/update-aztag). U moet de module AZ. Resources 1.12.0 of hoger hebben. U kunt uw versie controleren met `Get-Module Az.Resources` . U kunt die module installeren of [Azure PowerShell](/powershell/azure/install-az-ps) 3.6.1 of later installeren.

De **New-AzTag** vervangt alle labels op de resource, resource groep of het abonnement. Wanneer u de opdracht aanroept, geeft u de resource-ID van de entiteit die u wilt labelen door.

In het volgende voor beeld wordt een set tags toegepast op een opslag account:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

Wanneer de opdracht is voltooid, ziet u dat de resource twee labels heeft.

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

Als u de opdracht opnieuw uitvoert, maar deze keer met andere tags, ziet u dat de eerdere Tags worden verwijderd.

```azurepowershell-interactive
$tags = @{"Team"="Compliance"; "Environment"="Production"}
New-AzTag -ResourceId $resource.id -Tag $tags
```

```output
Properties :
        Name         Value
        ===========  ==========
        Environment  Production
        Team         Compliance
```

Om tags toe te voegen aan een resource die al labels heeft, gebruikt u **Update-AzTag**. Stel de para meter **-Operation** in op **Merge**.

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

U ziet dat de twee nieuwe tags zijn toegevoegd aan de twee bestaande tags.

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Elke label naam kan slechts één waarde hebben. Als u een nieuwe waarde voor een tag opgeeft, wordt de oude waarde vervangen, zelfs als u de bewerking merge gebruikt. In het volgende voor beeld wordt de status tag gewijzigd van normaal in groen.

```azurepowershell-interactive
$tags = @{"Status"="Green"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Green
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Wanneer u de para meter **-Operation** instelt op **vervangen**, worden de bestaande tags vervangen door de nieuwe set tags.

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

Alleen de nieuwe tags blijven op de resource.

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

Dezelfde opdrachten werken ook met resource groepen of-abonnementen. U geeft de id van de resource groep of het abonnement dat u wilt labelen.

Gebruik het volgende om een nieuwe set tags toe te voegen aan een resource groep:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

Als u de tags voor een resource groep wilt bijwerken, gebruikt u:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

Gebruik het volgende om een nieuwe set tags toe te voegen aan een abonnement:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

Als u de tags voor een abonnement wilt bijwerken, gebruikt u:

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

Een resource groep kan meer dan één resource met dezelfde naam bevatten. In dat geval kunt u elke resource instellen met de volgende opdrachten:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>Tags weergeven

Als u de tags voor een resource, resource groep of abonnement wilt ophalen, gebruikt u de opdracht [Get-AzTag](/powershell/module/az.resources/get-aztag) en geeft u de resource-id voor de entiteit door.

Als u de tags voor een resource wilt weer geven, gebruikt u:

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

Als u de tags voor een resource groep wilt weer geven, gebruikt u:

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

Als u de tags voor een abonnement wilt zien, gebruikt u:

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>Lijst op label

Als u resources wilt ophalen met een specifieke label naam en-waarde, gebruikt u:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

Gebruik de volgende informatie om resources te verkrijgen met een specifieke label naam met een wille keurige tag-waarde:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Als u resource groepen wilt ophalen met een specifieke label naam en-waarde, gebruikt u:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>Tags verwijderen

Als u specifieke tags wilt verwijderen, gebruikt u **Update-AzTag** en set **-Operation** om te **verwijderen**. Geef de labels op die u wilt verwijderen.

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

De opgegeven labels worden verwijderd.

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

Als u alle tags wilt verwijderen, gebruikt u de opdracht [Remove-AzTag](/powershell/module/az.resources/remove-aztag) .

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>Azure CLI

### <a name="apply-tags"></a>Tags Toep assen

Azure CLI biedt twee opdrachten voor het Toep assen van tags- [AZ tag Create](/cli/azure/tag#az_tag_create) en [AZ tag update](/cli/azure/tag#az_tag_update). U moet beschikken over Azure CLI 2.10.0 of hoger. U kunt uw versie controleren met `az version` . Zie [de Azure cli installeren](/cli/azure/install-azure-cli)als u wilt bijwerken of installeren.

De **AZ-tag Create** vervangt alle labels voor de resource, resource groep of het abonnement. Wanneer u de opdracht aanroept, geeft u de resource-ID van de entiteit die u wilt labelen door.

In het volgende voor beeld wordt een set tags toegepast op een opslag account:

```azurecli-interactive
resource=$(az resource show -g demoGroup -n demoStorage --resource-type Microsoft.Storage/storageAccounts --query "id" --output tsv)
az tag create --resource-id $resource --tags Dept=Finance Status=Normal
```

Wanneer de opdracht is voltooid, ziet u dat de resource twee labels heeft.

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Status": "Normal"
  }
},
```

Als u de opdracht opnieuw uitvoert, maar deze keer met andere tags, ziet u dat de eerdere Tags worden verwijderd.

```azurecli-interactive
az tag create --resource-id $resource --tags Team=Compliance Environment=Production
```

```output
"properties": {
  "tags": {
    "Environment": "Production",
    "Team": "Compliance"
  }
},
```

Gebruik **AZ tag update** om tags toe te voegen aan een resource die al labels heeft. Stel de para meter **--Operation** in om **samen te voegen**.

```azurecli-interactive
az tag update --resource-id $resource --operation Merge --tags Dept=Finance Status=Normal
```

U ziet dat de twee nieuwe tags zijn toegevoegd aan de twee bestaande tags.

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Status": "Normal",
    "Team": "Compliance"
  }
},
```

Elke label naam kan slechts één waarde hebben. Als u een nieuwe waarde voor een tag opgeeft, wordt de oude waarde vervangen, zelfs als u de bewerking merge gebruikt. In het volgende voor beeld wordt de status tag gewijzigd van normaal in groen.

```azurecli-interactive
az tag update --resource-id $resource --operation Merge --tags Status=Green
```

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Status": "Green",
    "Team": "Compliance"
  }
},
```

Wanneer u de para meter **--Operation** instelt op **vervangen**, worden de bestaande tags vervangen door de nieuwe set tags.

```azurecli-interactive
az tag update --resource-id $resource --operation Replace --tags Project=ECommerce CostCenter=00123 Team=Web
```

Alleen de nieuwe tags blijven op de resource.

```output
"properties": {
  "tags": {
    "CostCenter": "00123",
    "Project": "ECommerce",
    "Team": "Web"
  }
},
```

Dezelfde opdrachten werken ook met resource groepen of-abonnementen. U geeft de id van de resource groep of het abonnement dat u wilt labelen.

Gebruik het volgende om een nieuwe set tags toe te voegen aan een resource groep:

```azurecli-interactive
group=$(az group show -n demoGroup --query id --output tsv)
az tag create --resource-id $group --tags Dept=Finance Status=Normal
```

Als u de tags voor een resource groep wilt bijwerken, gebruikt u:

```azurecli-interactive
az tag update --resource-id $group --operation Merge --tags CostCenter=00123 Environment=Production
```

Gebruik het volgende om een nieuwe set tags toe te voegen aan een abonnement:

```azurecli-interactive
sub=$(az account show --subscription "Demo Subscription" --query id --output tsv)
az tag create --resource-id /subscriptions/$sub --tags CostCenter=00123 Environment=Dev
```

Als u de tags voor een abonnement wilt bijwerken, gebruikt u:

```azurecli-interactive
az tag update --resource-id /subscriptions/$sub --operation Merge --tags Team="Web Apps"
```

### <a name="list-tags"></a>Tags weergeven

Als u de tags voor een resource, resource groep of abonnement wilt ophalen, gebruikt u de opdracht [AZ Tags List](/cli/azure/tag#az_tag_list) en geeft u de resource-id voor de entiteit door.

Als u de tags voor een resource wilt weer geven, gebruikt u:

```azurecli-interactive
resource=$(az resource show -g demoGroup -n demoStorage --resource-type Microsoft.Storage/storageAccounts --query "id" --output tsv)
az tag list --resource-id $resource
```

Als u de tags voor een resource groep wilt weer geven, gebruikt u:

```azurecli-interactive
group=$(az group show -n demoGroup --query id --output tsv)
az tag list --resource-id $group
```

Als u de tags voor een abonnement wilt zien, gebruikt u:

```azurecli-interactive
sub=$(az account show --subscription "Demo Subscription" --query id --output tsv)
az tag list --resource-id /subscriptions/$sub
```

### <a name="list-by-tag"></a>Lijst op label

Als u resources wilt ophalen met een specifieke label naam en-waarde, gebruikt u:

```azurecli-interactive
az resource list --tag CostCenter=00123 --query [].name
```

Gebruik de volgende informatie om resources te verkrijgen met een specifieke label naam met een wille keurige tag-waarde:

```azurecli-interactive
az resource list --tag Team --query [].name
```

Als u resource groepen wilt ophalen met een specifieke label naam en-waarde, gebruikt u:

```azurecli-interactive
az group list --tag Dept=Finance
```

### <a name="remove-tags"></a>Tags verwijderen

Als u specifieke tags wilt verwijderen, gebruikt u **AZ tag update** en set **--Operation** om te **verwijderen**. Geef de labels op die u wilt verwijderen.

```azurecli-interactive
az tag update --resource-id $resource --operation Delete --tags Project=ECommerce Team=Web
```

De opgegeven labels worden verwijderd.

```output
"properties": {
  "tags": {
    "CostCenter": "00123"
  }
},
```

Als u alle tags wilt verwijderen, gebruikt u de opdracht [AZ tag delete](/cli/azure/tag#az_tag_delete) .

```azurecli-interactive
az tag delete --resource-id $resource
```

### <a name="handling-spaces"></a>Afhandelings ruimten

Als de namen van tags of waarden spaties bevatten, plaatst u deze tussen dubbele aanhalings tekens.

```azurecli-interactive
az tag update --resource-id $group --operation Merge --tags "Cost Center"=Finance-1222 Location="West US"
```

## <a name="templates"></a>Sjablonen

U kunt resources, resource groepen en abonnementen labelen tijdens de implementatie met een resource manager-sjabloon.

### <a name="apply-values"></a>Waarden Toep assen

In het volgende voor beeld wordt een opslag account met drie Tags geïmplementeerd. Twee van de tags ( `Dept` en `Environment` ) worden ingesteld op letterlijke waarden. Eén tag ( `LastDeployed` ) wordt ingesteld op een para meter die standaard op de huidige datum staat.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production",
                "LastDeployed": "[parameters('utcShort')]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-an-object"></a>Een object Toep assen

U kunt een objectparameter definiëren waarmee verschillende tags worden opgeslagen en dit object vervolgens toepassen op het tagelement. Deze benadering biedt meer flexibiliteit dan het vorige voor beeld omdat het object andere eigenschappen kan hebben. Elke eigenschap in het object wordt een afzonderlijke tag voor de resource. Het volgende voorbeeld heeft een parameter met de naam `tagValues` die wordt toegepast op het tagelement.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string"></a>Een JSON-teken reeks Toep assen

Als u veel waarden wilt opslaan in een enkele tag, past u een JSON-tekenreeks toe die de waarden vertegenwoordigt. De volledige JSON-teken reeks wordt opgeslagen als één tag die niet langer is dan 256 tekens. Het volgende voorbeeld heeft een enkele tag met de naam `CostCenter` die verschillende waarden uit een JSON-tekenreeks bevat:  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>Tags Toep assen vanuit de resource groep

Als u labels van een resource groep wilt Toep assen op een resource, gebruikt u de functie [resourceGroup ()](../templates/template-functions-resource.md#resourcegroup) . Wanneer u de tag-waarde ophaalt, gebruikt u de `tags[tag-name]` syntaxis in plaats van de `tags.tag-name` syntaxis, omdat sommige tekens niet goed worden geparseerd in de punt notatie.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>Tags Toep assen op resource groepen of abonnementen

U kunt Tags toevoegen aan een resource groep of een abonnement door het resource type **micro soft. resources/Tags** te implementeren. De tags worden toegepast op de doel resource groep of het abonnement voor de implementatie. Telkens wanneer u de sjabloon implementeert, vervangt u alle tags die u eerder hebt toegepast.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tagName": {
            "type": "string",
            "defaultValue": "TeamName"
        },
        "tagValue": {
            "type": "string",
            "defaultValue": "AppTeam1"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": {
                    "[parameters('tagName')]": "[parameters('tagValue')]"
                }
            }
        }
    ]
}
```

Gebruik Power shell of Azure CLI om de tags toe te passen op een resource groep. Implementeer de resource groep die u wilt labelen.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Als u de tags wilt Toep assen op een abonnement, gebruikt u Power shell of Azure CLI. Implementeer het abonnement dat u wilt labelen.

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Zie [resource groepen en-resources op abonnements niveau maken](../templates/deploy-to-subscription.md)voor meer informatie over abonnements implementaties.

De volgende sjabloon voegt de tags van een object toe aan een resource groep of een abonnement.

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tags": {
            "type": "object",
            "defaultValue": {
                "TeamName": "AppTeam1",
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": "[parameters('tags')]"
            }
        }
    ]
}
```

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST-API

Als u wilt werken met tags via de Azure REST API, gebruikt u:

* [Tags-maken of bijwerken bij bereik](/rest/api/resources/tags/createorupdateatscope) (put-bewerking)
* [Tags-bijwerken bij bereik](/rest/api/resources/tags/updateatscope) (patch bewerking)
* [Tags: ophalen bij bereik](/rest/api/resources/tags/getatscope) (Get-bewerking)
* [Tags-verwijderen bij bereik](/rest/api/resources/tags/deleteatscope) (Verwijder bewerking)

## <a name="inherit-tags"></a>Tags overnemen

Labels die worden toegepast op de resource groep of het abonnement, worden niet overgenomen door de resources. Zie [Azure-beleid-Tags](tag-policies.md)voor het Toep assen van tags van een abonnement of resource groep op de resources.

## <a name="tags-and-billing"></a>Tags en facturering

U kunt tags gebruiken om uw factureringsgegevens te groeperen. Als u bijvoorbeeld meerdere VM's voor verschillende organisaties uitvoert, kunt u de tags gebruiken om het gebruiker te groeperen op basis van de kostenplaats. U kunt ook tags gebruiken om de kosten te categoriseren op basis van de runtimeomgeving, zoals de facturering van het gebruik voor VM's die worden uitgevoerd in de productieomgeving.

U kunt informatie over Tags ophalen via de [Azure resource usage-en rate-api's](../../cost-management-billing/manage/usage-rate-card-overview.md) of het bestand met door komma's gescheiden waarden (CSV). U downloadt het gebruiks bestand van de Azure Portal. Zie uw Azure-factuur [en dagelijks gebruiks gegevens downloaden of weer geven](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)voor meer informatie. Selecteer **versie 2** bij het downloaden van het gebruiks bestand van de Azure-Accountcentrum. Voor services die Tags ondersteunen met facturering, worden de tags weer gegeven in de kolom **Tags** .

Zie voor REST API bewerkingen de [Naslag informatie voor Azure billing rest API](/rest/api/billing/).

## <a name="limitations"></a>Beperkingen

Voor tags gelden de volgende beperkingen:

* Niet alle resource typen ondersteunen Tags. Zie [tag-ondersteuning voor Azure-resources](tag-support.md)om te bepalen of u een tag kunt Toep assen op een resource type.
* Elke resource, resource groep en elk abonnement kan Maxi maal 50 label naam/waarde-paren hebben. Als u meer tags wilt Toep assen dan het Maxi maal toegestane aantal, gebruikt u een JSON-teken reeks voor de waarde van de tag. De JSON-tekenreeks kan veel waarden bevatten die worden toegepast op een enkele tagnaam. Een resource groep of-abonnement kan een groot aantal resources bevatten die elk een 50-tag met de naam/waarde-paren hebben.
* De tagnaam is beperkt tot 512 tekens en de tagwaarde is beperkt tot 256 tekens. Voor opslagaccounts is de tagnaam beperkt tot 128 tekens en de tagwaarde beperkt tot 256 tekens.
* Labels kunnen niet worden toegepast op klassieke resources, zoals Cloud Services.
* Label namen mogen niet de volgende tekens bevatten: `<` , `>` , `%` , `&` , `\` , `?` , `/`

   > [!NOTE]
   > Op dit moment staat Azure DNS-zones en Traffic Manager services ook niet toe dat spaties in het label worden gebruikt.
   >
   > De front-deur van Azure biedt geen ondersteuning voor het gebruik van `#` in de label naam.
   >
   > Azure Automation en Azure CDN bieden alleen ondersteuning voor 15 labels op resources.

## <a name="next-steps"></a>Volgende stappen

* Niet alle resource typen ondersteunen Tags. Zie [tag-ondersteuning voor Azure-resources](tag-support.md)om te bepalen of u een tag kunt Toep assen op een resource type.
* Zie voor meer informatie over het implementeren van een coderings strategie de [hand leiding resource naam geven en Tags nemen](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).
