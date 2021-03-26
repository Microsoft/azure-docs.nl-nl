---
title: 'Quickstart: Een blauwdruk maken met de REST-API'
description: In deze quickstart gebruikt u Azure Blueprints om artefacten te maken, te definiëren en te implementeren met de REST API.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: ceea54957e52f3b33d2c3fb2af190f15e6c94ec3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558972"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-rest-api"></a>Quickstart: Een Azure Blueprint definiëren en toewijzen met REST API

Als u leert hoe u blauwdrukken maakt en toewijst, kunnen er algemene patronen worden gedefinieerd voor de ontwikkeling van herbruikbare en snel implementeerbare configuraties op basis van Azure Resource Manager-sjablonen (ARM-sjablonen), beleid, beveiliging en meer. In deze zelfstudie leert u hoe u Azure Blueprints gebruikt om algemene taken uit te voeren met betrekking tot het maken, publiceren en toewijzen van een blauwdruk binnen uw organisatie, zoals:

## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.
- Registreer de `Microsoft.Blueprint`-resourceprovider. Zie [Resourceproviders en -typen](../../azure-resource-manager/management/resource-providers-and-types.md) voor aanwijzingen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>Aan de slag met REST API

Als u niet bekend bent met REST API, begint u met de [Azure REST API-verwijzing](/rest/api/azure/) voor een algemeen begrip van REST API, in het bijzonder van aanvraag-URI en aanvraagbody. In dit artikel worden deze concepten gebruikt om u aan de slag te helpen met Azure Blueprints. Er wordt van uitgegaan dat u er praktische kennis van hebt. Hulpprogramma's zoals [ARMClient](https://github.com/projectkudu/ARMClient) en anderen verwerken autorisatie automatisch en zijn voor beginners aangeraden.

Zie [Azure Blueprints REST API](/rest/api/blueprints/) voor de specificaties van Azure Blueprints.

### <a name="rest-api-and-powershell"></a>REST API en PowerShell

Als u nog geen hulpprogramma hebt om REST API-aanroepen te doen, kunt u overwegen PowerShell te gebruiken voor deze instructies. Hieronder volgt een voorbeeldheader voor verificatie met Azure. Genereer een verificatieheader, ook wel een **Bearer-token** genoemd, en zorg ervoor dat de REST API-URI verbinding maakt met parameters of een **Aanvraagbody**:

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

Vervang `{subscriptionId}` in de bovenstaande variabele **$restUri** om informatie over uw abonnement op te halen. De variabele $response bevat het resultaat van de cmdlet `Invoke-RestMethod`, die kan worden geparseerd met cmdlets zoals [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json). Als het service-eindpunt van REST API een **Aanvraagbody** verwacht, geeft u een met JSON geformatteerde variabele op in de parameter `-Body` van `Invoke-RestMethod`.

## <a name="create-a-blueprint"></a>Een blauwdruk maken

De eerste stap bij het definiëren van een standaardpatroon voor naleving bestaat uit het samenstellen van een blauwdruk uit de beschikbare resources. U maakt een blauwdruk met de naam MyBlueprint om de rol en de beleidstoewijzingen voor het abonnement te configureren. Vervolgens voegt u een resourcegroep en een ARM-sjabloon toe en voegt u een roltoewijzing aan de resourcegroep toe.

> [!NOTE]
> Wanneer u de REST API gebruikt, wordt het object _blauwdruk_ eerst gemaakt. Voor elk _artefact_ dat wordt toegevoegd en parameters bevat, moeten de parameters vooraf worden gedefinieerd in de eerste _blauwdruk_.

In elke REST API-URI zijn er verschillende variabelen die worden gebruikt en die u moet vervangen door uw eigen waarden:

- Vervang `{YourMG}` door de ID van uw beheergroep
- Vervang `{subscriptionId}` door uw abonnements-ID

> [!NOTE]
> Blauwdrukken kunnen ook worden gemaakt op abonnementsniveau. Zie [voorbeeld van het maken van een blauwdruk op abonnementsniveau](/rest/api/blueprints/blueprints/createorupdate#subscriptionblueprint) voor een voorbeeld.

1. Maak het eerste _blauwdruk_ object. De **Aanvraagbody** bevat eigenschappen van de blauwdruk, te maken resourcegroepen en alle parameters op blauwdrukniveau. De parameters worden tijdens het toewijzen ingesteld en gebruikt door de artefacten die in latere stappen worden toegevoegd.

   - REST API-URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
     ```

   - Aanvraagtekst

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
                     "metadata": {
                         "displayName": "storage account type.",
                         "description": null
                     }
                 },
                 "tagName": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The name of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "tagValue": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The value of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "contributors": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                     }
                 }
             },
             "resourceGroups": {
                 "storageRG": {
                     "description": "Contains the resource template deployment and a role assignment."
                 }
             }
         }
     }
     ```

1. Voeg de roltoewijzing toe aan het abonnement. De **Aanvraagbody** definieert het _soort_ artefact en de eigenschappen die zijn afgestemd op de roldefinitie-id en de principal-identiteiten worden doorgegeven als een matrix met waarden. In het onderstaande voorbeeld worden de principal-identiteiten en de opgegeven rol geconfigureerd in een parameter die wordt ingesteld tijdens de toewijzing van de blauwdruk. In dit voorbeeld wordt de ingebouwde rol van _Inzender_ met een GUID van `b24988ac-6180-42a0-ab88-20f7382dd24c` gebruikt.

   - REST API-URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleContributor?api-version=2018-11-01-preview
     ```

   - Aanvraagtekst

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

1. Voeg de beleidstoewijzing toe aan het abonnement. De **Aanvraagbody** definieert het _soort_ artefact, de eigenschappen die zijn afgestemd op een beleid of een initiatiefdefinitie, en configureert de beleidstoewijzing, zodat deze de gedefinieerde blauwdrukparameters kan gebruiken die tijdens het toewijzen van de blauwdruk moeten worden geconfigureerd. In dit voorbeeld wordt het ingebouwde beleid _Tag met standaardwaarde op resourcegroepen toepassen_ met een GUID van `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` gebruikt.

   - REST API-URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyTags?api-version=2018-11-01-preview
     ```

   - Aanvraagtekst

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "description": "Apply tag and its default value to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValue": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

1. Voeg nog een beleidstoewijzing toe voor de Storage-tag (gebruik hierbij de parameter _storageAccountType_ opnieuw) aan het abonnement. Deze aanvullende beleidstoewijzingsartefact laat zien dat een in de blauwdruk gedefinieerde parameter door meer dan één artefact kan worden gebruikt. In dit voorbeeld wordt **storageAccountType** gebruikt voor het instellen van een tag op de resourcegroep. Deze waarde geeft informatie over het opslagaccount dat in de volgende stap wordt gemaakt. In dit voorbeeld wordt het ingebouwde beleid _Tag met standaardwaarde op resourcegroepen toepassen_ met een GUID van `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` gebruikt.

   - REST API-URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
     ```

   - Aanvraagtekst

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "description": "Apply storage tag and the parameter also used by the template to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "StorageType"
                 },
                 "tagValue": {
                     "value": "[parameters('storageAccountType')]"
                 }
             }
         }
     }
     ```

1. Voeg een sjabloon toe onder resourcegroep. De **aanvraagbody** voor een ARM-sjabloon omvat de normale JSON-component van de sjabloon en definieert de doelresourcegroep met **properties.resourceGroup**. De sjabloon maakt ook opnieuw gebruik van de blauwdrukparameters **storageAccountType**, **tagName** en **tagValue** door ze allemaal door te geven aan de sjabloon. De blauwdrukparameters zijn voor de sjabloon beschikbaar door **properties.parameters** te definiëren. Binnen de sjabloon-JSON wordt die sleutel/waarde gebruikt om de waarde in te voeren. De namen van de blauwdruk- en sjabloonparameters kunnen dezelfde zijn, maar zijn verschillend gemaakt om aan te geven hoe elke parameter wordt doorgegeven van de blauwdruk aan de sjabloonartefact.

   - REST API-URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/templateStorage?api-version=2018-11-01-preview
     ```

   - Aanvraagtekst

     ```json
     {
         "kind": "template",
         "properties": {
             "template": {
                 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                 "contentVersion": "1.0.0.0",
                 "parameters": {
                     "storageAccountTypeFromBP": {
                         "type": "string",
                         "defaultValue": "Standard_LRS",
                         "allowedValues": [
                             "Standard_LRS",
                             "Standard_GRS",
                             "Standard_ZRS",
                             "Premium_LRS"
                         ],
                         "metadata": {
                             "description": "Storage Account type"
                         }
                     },
                     "tagNameFromBP": {
                         "type": "string",
                         "defaultValue": "NotSet",
                         "metadata": {
                             "description": "Tag name from blueprint"
                         }
                     },
                     "tagValueFromBP": {
                         "type": "string",
                         "defaultValue": "NotSet",
                         "metadata": {
                             "description": "Tag value from blueprint"
                         }
                     }
                 },
                 "variables": {
                     "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
                 },
                 "resources": [{
                     "type": "Microsoft.Storage/storageAccounts",
                     "name": "[variables('storageAccountName')]",
                     "apiVersion": "2016-01-01",
                     "tags": {
                        "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
                     },
                     "location": "[resourceGroups('storageRG').location]",
                     "sku": {
                         "name": "[parameters('storageAccountTypeFromBP')]"
                     },
                     "kind": "Storage",
                     "properties": {}
                 }],
                 "outputs": {
                     "storageAccountSku": {
                         "type": "string",
                         "value": "[variables('storageAccountName')]"
                     }
                 }
             },
             "resourceGroup": "storageRG",
             "parameters": {
                 "storageAccountTypeFromBP": {
                     "value": "[parameters('storageAccountType')]"
                 },
                 "tagNameFromBP": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValueFromBP": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

1. Voeg een roltoewijzing toe onder resourcegroep. Net als bij de vorige vermelding van een roltoewijzing wordt in het onderstaande voorbeeld de definitie-id van de rol **Eigenaar** gebruikt en krijgt deze een andere parameter van de blauwdruk. In dit voorbeeld wordt de ingebouwde rol van _Eigenaar_ met een GUID van `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` gebruikt.

   - REST API-URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
     ```

   - Aanvraagtekst

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "resourceGroup": "storageRG",
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
             "principalIds": "[parameters('owners')]"
         }
     }
     ```

## <a name="publish-a-blueprint"></a>Een blauwdruk publiceren

Nu de artefacten zijn toegevoegd aan de blauwdruk, is het tijd om deze te publiceren. Als de blauwdruk wordt gepubliceerd, kan deze worden toegewezen aan een abonnement.

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/versions/{BlueprintVersion}?api-version=2018-11-01-preview
  ```

De waarde voor `{BlueprintVersion}` is een reeks letters, cijfers en afbreekstreepjes (geen spaties of andere speciale tekens) met een maximale lengte van twintig tekens. Gebruik iets unieks en informatiefs als **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Een blauwdruk toewijzen

Nadat een blauwdruk is gepubliceerd met REST API, kan deze worden toegewezen aan een abonnement. Wijs de blauwdruk die u hebt gemaakt toe aan een van de abonnementen in uw beheergroephiërarchie. Als de blauwdruk is opgeslagen in een abonnement, kan deze alleen aan dat abonnement worden toegewezen. De **Aanvraagbody** geeft de op te geven blauwdruk op, evenals een naam en locatie voor resourcegroepen in de blauwdrukdefinitie en alle parameters die in de blauwdruk zijn gedefinieerd en die zijn gebruikt door een of meer gekoppelde artefacten.

In elke REST API-URI zijn er verschillende variabelen die worden gebruikt en die u moet vervangen door uw eigen waarden:

- Vervang `{tenantId}` door uw tenant-id
- Vervang `{YourMG}` door de ID van uw beheergroep
- Vervang `{subscriptionId}` door uw abonnements-ID

1. Geef in de Azure Blueprint-service-principal de rol **Eigenaar** op in het doelabonnement. De AppId is statisch (`f71766dc-90d9-4b7d-bd9d-4499c4331c3f`), maar de service-principal-ID verschilt per tenant. U kunt voor uw tenant details aanvragen met de volgende REST API. Deze gebruikt [Azure Active Directory Graph API](/graph/migrate-azure-ad-graph-planning-checklist), die een andere autorisatie heeft.

   - REST API-URI

     ```http
     GET https://graph.windows.net/{tenantId}/servicePrincipals?api-version=1.6&$filter=appId eq 'f71766dc-90d9-4b7d-bd9d-4499c4331c3f'
     ```

1. Voer de blauwdrukimplementatie uit door deze toe te wijzen aan een abonnement. Als de parameters **inzenders** en **eigenaren** voor de roltoewijzing een matrix van objectIds van de principals nodig hebben, gebruikt u [Azure Active Directory Graph API](/graph/migrate-azure-ad-graph-planning-checklist) om de objectIds op te halen voor gebruik in de **Aanvraagbody** voor uw eigen gebruikers, groepen of service-principals.

   - REST API-URI

     ```http
     PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
     ```

   - Aanvraagtekst

     ```json
     {
         "properties": {
             "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint",
             "resourceGroups": {
                 "storageRG": {
                     "name": "StorageAccount",
                     "location": "eastus2"
                 }
             },
             "parameters": {
                 "storageAccountType": {
                     "value": "Standard_GRS"
                 },
                 "tagName": {
                     "value": "CostCenter"
                 },
                 "tagValue": {
                     "value": "ContosoIT"
                 },
                 "contributors": {
                     "value": [
                         "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                         "38833b56-194d-420b-90ce-cff578296714"
                     ]
                 },
                 "owners": {
                     "value": [
                         "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                         "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                     ]
                 }
             }
         },
         "identity": {
             "type": "systemAssigned"
         },
         "location": "westus"
     }
     ```

   - Door een gebruiker toegewezen beheerde identiteit

     Een blauwdruktoewijzing kan ook gebruikmaken van een [door een gebruiker toegewezen beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md).
     In dit geval wordt het **identiteitsgedeelte** van de aanvraagtekst als volgt gewijzigd. Vervang de naam van de resourcegroep en de naam van de door een gebruiker toegewezen beheerde identiteit door respectievelijk `{yourRG}` en `{userIdentity}`.

     ```json
     "identity": {
         "type": "userAssigned",
         "tenantId": "{tenantId}",
         "userAssignedIdentities": {
             "/subscriptions/{subscriptionId}/resourceGroups/{yourRG}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userIdentity}": {}
         }
     },
     ```

     De **door een gebruiker toegewezen beheerde identiteit** kan in elk abonnement en in elke resourcegroep aanwezig zijn waarvoor de gebruiker die de blauwdruk toewijst, is gemachtigd.

     > [!IMPORTANT]
     > De door een gebruiker toegewezen beheerde identiteit wordt niet beheerd door Azure Blueprints. Gebruikers zijn verantwoordelijk voor het toewijzen van voldoende rollen en machtigingen, anders mislukken de blauwdruktoewijzingen.

## <a name="clean-up-resources"></a>Resources opschonen

### <a name="unassign-a-blueprint"></a>De toewijzing van een blauwdruk ongedaan maken

U kunt een blauwdruk uit een abonnement verwijderen. Het verwijderen wordt vaak uitgevoerd als de artefactresources niet langer nodig zijn. Wanneer een blauwdruk wordt verwijderd, blijven de artefacten die als onderdeel van die blauwdruk zijn toegewezen, achter. Als u de toewijzing van een blauwdruk ongedaan wilt maken, gebruikt u de volgende REST API-bewerking:

- REST API-URI

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

### <a name="delete-a-blueprint"></a>Een blauwdruk verwijderen

Als u de blauwdruk zelf wilt verwijderen, gebruikt u de volgende REST API-bewerking:

- REST API-URI

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een blauwdruk gemaakt, toegewezen en verwijderd met REST API. Ga verder met het artikel over de levenscyclus van blauwdrukken voor meer informatie over Azure Blueprints.

> [!div class="nextstepaction"]
> [Meer informatie over de levenscyclus van een blauwdruk](./concepts/lifecycle.md)