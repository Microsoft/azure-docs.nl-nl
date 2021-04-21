---
title: Resources vergrendelen om wijzigingen te voorkomen
description: Voorkom dat gebruikers Azure-resources bijwerken of verwijderen door een vergrendeling toe te passen voor alle gebruikers en rollen.
ms.topic: conceptual
ms.date: 04/07/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 71637318a60e66bf5000de2f564d740cc101cc60
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768719"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Resources vergrendelen om onverwachte wijzigingen te voorkomen

Als beheerder kunt u een abonnement, resourcegroep of resource vergrendelen om te voorkomen dat andere gebruikers in uw organisatie per ongeluk kritieke resources verwijderen of wijzigen. De vergrendeling overschrijven alle machtigingen die de gebruiker mogelijk heeft.

U kunt de vergrendeling instellen op **CanNotDelete** of **ReadOnly**. In de portal worden de vergrendelingen respectievelijk **Verwijderen** en **Alleen-lezen** genoemd.

* **CanNotDelete** betekent dat gemachtigde gebruikers een resource nog steeds kunnen lezen en wijzigen, maar dat ze de resource niet kunnen verwijderen.
* **ReadOnly betekent** dat gemachtigde gebruikers een resource kunnen lezen, maar ze kunnen de resource niet verwijderen of bijwerken. Het toepassen van deze vergrendeling is vergelijkbaar met het beperken van alle gemachtigde gebruikers tot de machtigingen die worden verleend door de **rol** Lezer.

## <a name="how-locks-are-applied"></a>Hoe vergrendelingen worden toegepast

Wanneer u een vergrendeling op een bovenliggend bereik toe passen, nemen alle resources binnen dat bereik dezelfde vergrendeling over. Zelfs resources die u later toevoegt, nemen de vergrendeling over van het bovenliggende. De meest beperkende vergrendeling in de overname heeft prioriteit.

In tegenstelling tot op rollen gebaseerd toegangsbeheer wordt met beheervergrendelingen een beperking toegepast op alle gebruikers en rollen. Zie Op rollen gebaseerd toegangsbeheer van [Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)voor meer informatie over het instellen van machtigingen voor gebruikers en rollen.

Vergrendelingen van Resource Manager gelden alleen voor bewerkingen die zich op beheerniveau voordoen, wat bewerkingen zijn die worden verzonden naar `https://management.azure.com`. De vergrendelingen hebben geen invloed op hoe resources hun eigen functies uitvoeren. Resourcewijzigingen worden beperkt, maar resourcebewerkingen worden niet beperkt. Een ReadOnly-vergrendeling op een logische SQL Database voorkomt bijvoorbeeld dat u de server kunt verwijderen of wijzigen. Dit voorkomt niet dat u gegevens in de databases op die server kunt maken, bijwerken of verwijderen. Gegevenstransacties zijn toegestaan omdat deze bewerkingen niet naar `https://management.azure.com` worden verzonden.

## <a name="considerations-before-applying-locks"></a>Overwegingen voordat u vergrendelingen gaat toepassen

Het toepassen van vergrendelingen kan leiden tot onverwachte resultaten omdat sommige bewerkingen die de resource niet lijken te wijzigen, acties vereisen die door de vergrendeling worden geblokkeerd. Vergrendelingen verhinderen bewerkingen waarvoor een POST-aanvraag is vereist voor de Azure Resource Manager API. Enkele veelvoorkomende voorbeelden van de bewerkingen die worden geblokkeerd door vergrendelingen zijn:

* Een alleen-lezenvergrendeling voor **een opslagaccount** voorkomt dat gebruikers de accountsleutels kunnen bekijken. De [Azure Storage-lijstsleutels](/rest/api/storagerp/storageaccounts/listkeys) wordt verwerkt via een POST-aanvraag om de toegang tot de accountsleutels te beveiligen, die volledige toegang bieden tot gegevens in het opslagaccount. Wanneer een alleen-lezenvergrendeling is geconfigureerd voor een opslagaccount, moeten gebruikers die niet over de accountsleutels hebben, Azure AD-referenties gebruiken om toegang te krijgen tot blob- of wachtrijgegevens. Een alleen-lezenvergrendeling voorkomt ook de toewijzing van Azure RBAC-rollen die zijn beperkt tot het opslagaccount of aan een gegevenscontainer (blobcontainer of wachtrij).

* Een vergrendeling voor een **opslagaccount kan niet worden** verwijderd, maar voorkomt niet dat gegevens in dat account worden verwijderd of gewijzigd. Met dit type vergrendeling wordt alleen het opslagaccount zelf beschermd tegen het verwijderen en worden blob-, wachtrij-, tabel- of bestandsgegevens in dat opslagaccount niet beschermd. 

* Een alleen-lezenvergrendeling voor **een opslagaccount** voorkomt niet dat gegevens in dat account worden verwijderd of gewijzigd. Met dit type vergrendeling wordt alleen het opslagaccount zelf beschermd tegen het verwijderen of wijzigen, en worden blob-, wachtrij-, tabel- of bestandsgegevens in dat opslagaccount niet beschermd. 

* Een alleen-lezenvergrendeling op een **App Service** voorkomt dat Visual Studio Server Explorer bestanden voor de resource kan weergeven, omdat voor die interactie schrijftoegang is vereist.

* Een alleen-lezenvergrendeling voor een **resourcegroep** die een **App Service-abonnement bevat,** voorkomt dat u het plan omhoog [of uitschaalt.](../../app-service/manage-scale-up.md)

* Een alleen-lezenvergrendeling voor **een resourcegroep** die een virtuele **machine** bevat, voorkomt dat alle gebruikers de virtuele machine starten of opnieuw starten. Voor deze bewerkingen is een POST-aanvraag vereist.

* Een vergrendeling voor een **resourcegroep** die niet kan worden verwijderd, voorkomt Azure Resource Manager [implementaties](../templates/deployment-history-deletions.md) in de geschiedenis automatisch te verwijderen. Als u 800 implementaties in de geschiedenis bereikt, mislukken uw implementaties.

* Een vergrendeling voor de **resourcegroep** die is gemaakt door **de Azure Backup Service** zorgt ervoor dat back-ups mislukken. De service ondersteunt maximaal 18 herstelpunten. Wanneer de back-upservice is vergrendeld, kunnen herstelpunten niet worden opsschoond. Zie Veelgestelde vragen- [Back-up maken van Azure-VM's voor meer informatie.](../../backup/backup-azure-vm-backup-faq.yml)

* Een alleen-lezenvergrendeling **voor een abonnement** voorkomt **Azure Advisor** goed werkt. Advisor kan de resultaten van de query's niet opslaan.

## <a name="who-can-create-or-delete-locks"></a>Wie kan vergrendelingen maken of verwijderen

Als u beheervergrendelingen wilt maken of verwijderen, moet u toegang hebben tot `Microsoft.Authorization/*` - of `Microsoft.Authorization/locks/*` -acties. Van de ingebouwde rollen worden deze acties alleen toegekend aan **Eigenaar** en **Administrator voor gebruikerstoegang**.

## <a name="managed-applications-and-locks"></a>Beheerde toepassingen en vergrendelingen

Sommige Azure-services, zoals Azure Databricks, gebruiken [beheerde toepassingen](../managed-applications/overview.md) om de service te implementeren. In dat geval maakt de service twee resourcegroepen. Eén resourcegroep bevat een overzicht van de service en is niet vergrendeld. De andere resourcegroep bevat de infrastructuur voor de service en is vergrendeld.

Als u de infrastructuurresourcegroep probeert te verwijderen, wordt er een foutbericht weergegeven waarin staat dat de resourcegroep is vergrendeld. Als u de vergrendeling voor de infrastructuurresourcegroep probeert te verwijderen, wordt er een foutbericht weergegeven dat de vergrendeling niet kan worden verwijderd omdat deze eigendom is van een systeemtoepassing.

Verwijder in plaats daarvan de service, waarmee ook de infrastructuurresourcegroep wordt verwijderd.

Selecteer voor beheerde toepassingen de service die u hebt geïmplementeerd.

![Service selecteren](./media/lock-resources/select-service.png)

U ziet dat de service een koppeling bevat voor een **beheerde resourcegroep.** Die resourcegroep bevat de infrastructuur en is vergrendeld. Deze kan niet rechtstreeks worden verwijderd.

![Beheerde groep tonen](./media/lock-resources/show-managed-group.png)

Als u alles voor de service wilt verwijderen, inclusief de resourcegroep voor de vergrendelde infrastructuur, **selecteert u Verwijderen** voor de service.

![Service verwijderen](./media/lock-resources/delete-service.png)

## <a name="configure-locks"></a>Vergrendelingen configureren

### <a name="portal"></a>Portal

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

### <a name="arm-template"></a>ARM-sjabloon

Wanneer u een AZURE RESOURCE MANAGER sjabloon (ARM-sjabloon) gebruikt om een vergrendeling te implementeren, moet u rekening houden met het bereik van de vergrendeling en het bereik van de implementatie. Als u een vergrendeling wilt toepassen op het implementatiebereik, zoals het vergrendelen van een resourcegroep of abonnement, moet u de eigenschap bereik niet instellen. Wanneer u een resource binnen het implementatiebereik vergrendelt, stelt u de eigenschap scope in.

Met de volgende sjabloon wordt een vergrendeling toegepast op de resourcegroep waar deze op is geïmplementeerd. U ziet dat er geen bereik-eigenschap voor de vergrendelingsresource is omdat het bereik van de vergrendeling overeenkomt met het bereik van de implementatie. Deze sjabloon wordt geïmplementeerd op het niveau van de resourcegroep.

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

Als u een resourcegroep wilt maken en vergrendelen, implementeert u de volgende sjabloon op abonnementsniveau.

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

Wanneer u een vergrendeling op een **resource binnen de resourcegroep** wilt toepassen, voegt u de eigenschap bereik toe. Stel bereik in op de naam van de resource die u wilt vergrendelen.

In het volgende voorbeeld ziet u een sjabloon waarmee een App Service-plan, een website en een vergrendeling op de website worden gemaakt. Het bereik van de vergrendeling is ingesteld op de website.

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

U vergrendelt geïmplementeerde resources met Azure PowerShell met behulp van [de opdracht New-AzResourceLock.](/powershell/module/az.resources/new-azresourcelock)

Als u een resource wilt vergrendelen, geeft u de naam van de resource, het resourcetype en de naam van de resourcegroep op.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Als u een resourcegroep wilt vergrendelen, geeft u de naam van de resourcegroep op.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Gebruik [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock)om informatie over een vergrendeling op te halen. Gebruik het volgende om alle vergrendelingen in uw abonnement op te halen:

```azurepowershell-interactive
Get-AzResourceLock
```

Gebruik het volgende om alle vergrendelingen voor een resource op te halen:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Gebruik het volgende om alle vergrendelingen voor een resourcegroep op te halen:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Als u een vergrendeling voor een resource wilt verwijderen, gebruikt u:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

Als u een vergrendeling voor een resourcegroep wilt verwijderen, gebruikt u:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup).LockId
Remove-AzResourceLock -LockId $lockId
```

### <a name="azure-cli"></a>Azure CLI

U vergrendelt geïmplementeerde resources met Azure CLI met behulp van de [opdracht az lock create.](/cli/azure/lock#az_lock_create)

Als u een resource wilt vergrendelen, geeft u de naam van de resource, het resourcetype en de naam van de resourcegroep op.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Als u een resourcegroep wilt vergrendelen, geeft u de naam van de resourcegroep op.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Gebruik az lock list om informatie over een [vergrendeling op te halen.](/cli/azure/lock#az_lock_list) Gebruik het volgende om alle vergrendelingen in uw abonnement op te halen:

```azurecli
az lock list
```

Gebruik het volgende om alle vergrendelingen voor een resource op te halen:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Gebruik het volgende om alle vergrendelingen voor een resourcegroep op te halen:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Als u een vergrendeling voor een resource wilt verwijderen, gebruikt u:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

Als u een vergrendeling voor een resourcegroep wilt verwijderen, gebruikt u:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup  --output tsv --query id)
az lock delete --ids $lockid
```

### <a name="rest-api"></a>REST-API

U kunt geïmplementeerde resources vergrendelen met de REST API [voor beheervergrendelingen.](/rest/api/resources/managementlocks) Met REST API kunt u vergrendelingen maken en verwijderen en informatie over bestaande vergrendelingen ophalen.

Voer het volgende uit om een vergrendeling te maken:

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}
```

Het bereik kan een abonnement, resourcegroep of resource zijn. De naam van de vergrendeling is de naam die u de vergrendeling wilt aanroepen. Gebruik voor api-version **2016-09-01.**

Neem in de aanvraag een JSON-object op dat de eigenschappen voor de vergrendeling specificeert.

```json
{
  "properties": {
  "level": "CanNotDelete",
  "notes": "Optional text notes."
  }
}
```

## <a name="next-steps"></a>Volgende stappen

* Zie Tags gebruiken om uw resources te organiseren voor meer informatie over het [logisch ordenen van uw resources.](tag-resources.md)
* U kunt beperkingen en conventies toepassen voor uw abonnement met aangepaste beleidsregels. Zie [Wat is Azure Policy?](../../governance/policy/overview.md) voor meer informatie.
* Voor begeleiding bij de manier waarop ondernemingen Resource Manager effectief kunnen gebruiken voor het beheer van abonnementen, gaat u naar [Azure enterprise-platform - Prescriptieve abonnementsgovernance](/azure/architecture/cloud-adoption-guide/subscription-governance).
