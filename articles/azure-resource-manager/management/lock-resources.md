---
title: Resources vergren delen om wijzigingen te voor komen
description: Voor komen dat gebruikers Azure-resources bijwerken of verwijderen door een vergren deling toe te passen voor alle gebruikers en rollen.
ms.topic: conceptual
ms.date: 02/01/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 912c7e86d253aa18b9a6c60717ceaa70e32fcf0e
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428314"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Resources vergrendelen om onverwachte wijzigingen te voorkomen

Als beheerder kunt u een abonnement, resource groep of resource vergren delen om te voor komen dat andere gebruikers in uw organisatie per ongeluk essentiële bronnen verwijderen of wijzigen. De vergren deling overschrijft alle machtigingen die de gebruiker mogelijk heeft.

U kunt de vergrendeling instellen op **CanNotDelete** of **ReadOnly**. In de portal worden de vergren delingen respectievelijk **verwijderen** en **alleen-lezen** genoemd.

* **CanNotDelete** betekent dat geautoriseerde gebruikers nog steeds een resource kunnen lezen en wijzigen, maar de resource niet kan verwijderen.
* **Alleen-lezen** houdt in dat gemachtigde gebruikers een resource kunnen lezen, maar de resource niet kunnen verwijderen of bijwerken. Het Toep assen van deze vergren deling is vergelijkbaar met het beperken van alle gemachtigde gebruikers tot de machtigingen die door de rol van **lezer** worden verleend.

## <a name="how-locks-are-applied"></a>Hoe vergrendelingen worden toegepast

Wanneer u een vergren deling toepast op een bovenliggend bereik, nemen alle resources binnen die scope dezelfde vergren deling. Zelfs resources die u later toevoegt, nemen de vergren deling van het bovenliggende knoop punt over. De meest beperkende vergren deling in de overname heeft prioriteit.

In tegenstelling tot op rollen gebaseerd toegangsbeheer wordt met beheervergrendelingen een beperking toegepast op alle gebruikers en rollen. Zie voor meer informatie over het instellen van machtigingen voor gebruikers en rollen [het toegangs beheer op basis van rollen (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).

Vergrendelingen van Resource Manager gelden alleen voor bewerkingen die zich op beheerniveau voordoen, wat bewerkingen zijn die worden verzonden naar `https://management.azure.com`. De vergrendelingen hebben geen invloed op hoe resources hun eigen functies uitvoeren. Resourcewijzigingen worden beperkt, maar resourcebewerkingen worden niet beperkt. Een alleen-lezen vergrendeling op een SQL Database logische server voor komt dat u de server kunt verwijderen of wijzigen. Het is niet mogelijk om gegevens in de data bases op die server te maken, bij te werken of te verwijderen. Gegevenstransacties zijn toegestaan omdat deze bewerkingen niet naar `https://management.azure.com` worden verzonden.

## <a name="considerations-before-applying-locks"></a>Overwegingen voor het Toep assen van vergren delingen

Het Toep assen van vergren delingen kan leiden tot onverwachte resultaten omdat sommige bewerkingen die niet van invloed zijn op het wijzigen van de resource, acties nodig hebben die door de vergren deling worden geblokkeerd. Met vergren delingen wordt voor komen dat bewerkingen waarvoor een POST-aanvraag naar de Azure Resource Manager-API is vereist. Enkele algemene voor beelden van de bewerkingen die worden geblokkeerd door de vergren delingen zijn:

* Met een alleen-lezen vergrendeling op een **opslag account** kunnen alle gebruikers de sleutels niet weer geven. De bewerking voor het weergeven van de lijst met sleutels wordt verwerkt via een POST-aanvraag, omdat de geretourneerde sleutels beschikbaar zijn voor schrijfbewerkingen.

* Met een alleen-lezen vergrendeling voor een **app service** resource kan Visual Studio Server Explorer bestanden voor de resource niet weer geven omdat die interactie schrijf toegang vereist.

* Een alleen-lezen vergrendeling voor een **resource groep** die een **virtuele machine** bevat, voor komt dat alle gebruikers de virtuele machine starten of opnieuw starten. Deze bewerkingen vereisen een POST-aanvraag.

* Als u een vergren deling van een **resource groep** niet kunt verwijderen, voor komt u dat Azure Resource Manager [implementaties automatisch verwijdert](../templates/deployment-history-deletions.md) in de geschiedenis. Als u 800-implementaties in de geschiedenis bereikt, zullen uw implementaties mislukken.

* Het is niet mogelijk om de vergren deling van de **resource groep** die is gemaakt door de **Azure backup-service** te verwijderen, waardoor back-ups mislukken. De service ondersteunt Maxi maal 18 herstel punten. Wanneer deze is vergrendeld, kan de back-upservice geen herstel punten opschonen. Zie [Veelgestelde vragen-back-ups maken van Azure vm's](../../backup/backup-azure-vm-backup-faq.yml)voor meer informatie.

* Een alleen-lezen vergrendeling voor een **abonnement** voor komt dat **Azure Advisor** goed werkt. Advisor kan de resultaten van de query's niet opslaan.

## <a name="who-can-create-or-delete-locks"></a>Wie kan vergren delingen maken of verwijderen?

Als u beheer vergrendelingen wilt maken of verwijderen, moet u toegang tot `Microsoft.Authorization/*` of `Microsoft.Authorization/locks/*` acties hebben. Van de ingebouwde rollen worden deze acties alleen toegekend aan **Eigenaar** en **Administrator voor gebruikerstoegang**.

## <a name="managed-applications-and-locks"></a>Beheerde toepassingen en vergren delingen

Sommige Azure-Services, zoals Azure Databricks, gebruiken [beheerde toepassingen](../managed-applications/overview.md) voor het implementeren van de service. In dat geval maakt de service twee resource groepen. Een resource groep bevat een overzicht van de service en is niet vergrendeld. De andere resource groep bevat de infra structuur voor de service en is vergrendeld.

Als u de infrastructuur resource groep probeert te verwijderen, wordt er een fout bericht weer gegeven met de mede deling dat de resource groep is vergrendeld. Als u de vergren deling voor de infrastructuur resource groep probeert te verwijderen, wordt er een fout bericht weer gegeven dat de vergren deling niet kan worden verwijderd omdat deze het eigendom is van een systeem toepassing.

Verwijder in plaats daarvan de service, waardoor ook de infrastructuur resource groep wordt verwijderd.

Selecteer voor beheerde toepassingen de service die u hebt geïmplementeerd.

![Service selecteren](./media/lock-resources/select-service.png)

U ziet dat de service een koppeling voor een **beheerde resource groep** bevat. Deze resource groep bevat de infra structuur en is vergrendeld. Het kan niet rechtstreeks worden verwijderd.

![Beheerde groep weer geven](./media/lock-resources/show-managed-group.png)

Als u alles wilt verwijderen voor de service, inclusief de resource groep vergrendelde infra structuur, selecteert u **verwijderen** voor de service.

![Service verwijderen](./media/lock-resources/delete-service.png)

## <a name="configure-locks"></a>Vergren delingen configureren

### <a name="portal"></a>Portal

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

### <a name="arm-template"></a>ARM-sjabloon

Wanneer u een Azure Resource Manager sjabloon (ARM-sjabloon) gebruikt om een vergren deling te implementeren, moet u rekening houden met het bereik van de vergren deling en het bereik van de implementatie. Als u een vergren deling op het implementatie bereik wilt Toep assen, zoals het vergren delen van een resource groep of-abonnement, stelt u de eigenschap scope niet in. Stel de eigenschap scope in wanneer u een resource vergrendelt binnen het implementatie bereik.

Met de volgende sjabloon wordt een vergren deling toegepast op de resource groep waarop deze is geïmplementeerd. U ziet dat er geen bereik eigenschap is voor de vergren deling van de resource omdat het bereik van de vergren deling overeenkomt met het implementatie bereik. Deze sjabloon wordt geïmplementeerd op het niveau van de resource groep.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {  
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/locks",
            "apiVersion": "2016-09-01",
            "name": "rgLock",
            "properties": {
                "level": "CanNotDelete",
                "notes": "Resource Group should not be deleted."
            }
        }
    ]
}
```

Als u een resource groep wilt maken en deze wilt vergren delen, implementeert u de volgende sjabloon op abonnements niveau.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2019-10-01",
            "name": "[parameters('rgName')]",
            "location": "[parameters('rgLocation')]",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "lockDeployment",
            "resourceGroup": "[parameters('rgName')]",
            "dependsOn": [
                "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Authorization/locks",
                            "apiVersion": "2016-09-01",
                            "name": "rgLock",
                            "properties": {
                                "level": "CanNotDelete",
                                "notes": "Resource group and its resources should not be deleted."
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ],
    "outputs": {}
}
```

Wanneer u een vergren deling toepast op een **resource** in de resource groep, voegt u de eigenschap scope toe. Stel het bereik in op de naam van de resource die u wilt vergren delen.

In het volgende voor beeld ziet u een sjabloon waarmee u een app service-plan, een website en een vergren deling op de website maakt. Het bereik van de vergren deling is ingesteld op de website.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "hostingPlanName": {
      "type": "string"
    },
    "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2020-06-01",
      "name": "[parameters('hostingPlanName')]",
      "location": "[parameters('location')]",
      "sku": {
        "tier": "Free",
        "name": "f1",
        "capacity": 0
      },
      "properties": {
        "targetWorkerCount": 1
      }
    },
    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2020-06-01",
      "name": "[variables('siteName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      }
    },
    {
      "type": "Microsoft.Authorization/locks",
      "apiVersion": "2016-09-01",
      "name": "siteLock",
      "scope": "[concat('Microsoft.Web/sites/', variables('siteName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
      ],
      "properties": {
        "level": "CanNotDelete",
        "notes": "Site should not be deleted."
      }
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell

U vergrendelt geïmplementeerde resources met Azure PowerShell met behulp van de opdracht [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) .

Als u een resource wilt vergren delen, geeft u de naam van de resource, het resource type en de naam van de resource groep op.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Als u een resource groep wilt vergren delen, geeft u de naam van de resource groep op.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Gebruik [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock)om informatie over een vergren deling op te halen. Als u alle vergren delingen in uw abonnement wilt ophalen, gebruikt u:

```azurepowershell-interactive
Get-AzResourceLock
```

Als u alle vergren delingen voor een resource wilt ophalen, gebruikt u:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Als u alle vergren delingen voor een resource groep wilt ophalen, gebruikt u:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Als u een vergren deling voor een resource wilt verwijderen, gebruikt u:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

Als u een vergren deling voor een resource groep wilt verwijderen, gebruikt u:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup).LockId
Remove-AzResourceLock -LockId $lockId
```

### <a name="azure-cli"></a>Azure CLI

U vergrendelt geïmplementeerde resources met Azure CLI met behulp van de opdracht [AZ Lock Create](/cli/azure/lock#az-lock-create) .

Als u een resource wilt vergren delen, geeft u de naam van de resource, het resource type en de naam van de resource groep op.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Als u een resource groep wilt vergren delen, geeft u de naam van de resource groep op.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Gebruik [AZ Lock List](/cli/azure/lock#az-lock-list)om informatie over een vergren deling op te halen. Als u alle vergren delingen in uw abonnement wilt ophalen, gebruikt u:

```azurecli
az lock list
```

Als u alle vergren delingen voor een resource wilt ophalen, gebruikt u:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Als u alle vergren delingen voor een resource groep wilt ophalen, gebruikt u:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Als u een vergren deling voor een resource wilt verwijderen, gebruikt u:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

Als u een vergren deling voor een resource groep wilt verwijderen, gebruikt u:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup  --output tsv --query id)
az lock delete --ids $lockid
```

### <a name="rest-api"></a>REST-API

U kunt geïmplementeerde resources vergren delen met de [rest API voor beheer vergrendelingen](/rest/api/resources/managementlocks). Met de REST API kunt u vergren delingen maken en verwijderen, en informatie over bestaande vergren delingen ophalen.

Voer de volgende handelingen uit om een vergren deling te maken:

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}
```

Het bereik kan een abonnement, een resource groep of een resource zijn. De naam van de vergren deling is datgene wat u aan de vergren deling wilt aanroepen. Gebruik **2016-09-01** voor de API-versie.

Neem in de aanvraag een JSON-object op waarmee de eigenschappen voor de vergren deling worden opgegeven.

```json
{
  "properties": {
  "level": "CanNotDelete",
  "notes": "Optional text notes."
  }
}
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Tags gebruiken om uw resources te organiseren](tag-resources.md)voor meer informatie over het logisch ordenen van uw resources.
* U kunt beperkingen en conventies Toep assen op uw abonnement met aangepast beleid. Zie [Wat is Azure Policy?](../../governance/policy/overview.md) voor meer informatie.
* Voor begeleiding bij de manier waarop ondernemingen Resource Manager effectief kunnen gebruiken voor het beheer van abonnementen, gaat u naar [Azure enterprise-platform - Prescriptieve abonnementsgovernance](/azure/architecture/cloud-adoption-guide/subscription-governance).
