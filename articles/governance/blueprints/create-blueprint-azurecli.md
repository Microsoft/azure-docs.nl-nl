---
title: 'Quickstart: Een blauwdruk maken met de Azure CLI'
description: In deze quickstart gebruikt u Azure Blueprints om artefacten te maken, te definiëren en te implementeren met de Azure CLI.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 6ce3031c93f973c2efb251fad371a6f3750ae0fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98920237"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-azure-cli"></a>Quickstart: Een Azure Blueprint definiëren en toewijzen met Azure CLI

Als u leert hoe u blauwdrukken maakt en toewijst, kunnen er algemene patronen worden gedefinieerd voor de ontwikkeling van herbruikbare en snel implementeerbare configuraties op basis van Azure Resource Manager-sjablonen (ARM-sjablonen), beleid, beveiliging en meer. In deze zelfstudie leert u hoe u Azure Blueprints gebruikt om algemene taken uit te voeren met betrekking tot het maken, publiceren en toewijzen van een blauwdruk binnen uw organisatie, zoals:

## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.
- Als u nog geen Azure-blauw drukken hebt gebruikt, registreert u de resource provider via Azure CLI met `az provider register --namespace Microsoft.Blueprint` .

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-blueprint-extension"></a>De Blueprint-extensie toevoegen

De extensie moet worden toegevoegd om Azure CLI in te schakelen voor het beheren van blauwdrukdefinities en -toewijzingen.
Deze extensie werkt overal waar Azure CLI kan worden gebruikt, met inbegrip van [bash in Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (zowel zelfstandige als in de portal), de [Azure CLI Docker-installatiekopie](https://hub.docker.com/_/microsoft-azure-cli), of lokaal geïnstalleerd.

1. Controleer of de meest recente versie van Azure CLI is geïnstalleerd (minimaal **2.0.76**). Als deze nog niet is geïnstalleerd, volgt u [deze instructies](/cli/azure/install-azure-cli-windows) op.

1. In de gewenste Azure CLI-omgeving kunt u deze importeren met de volgende opdracht:

   ```azurecli-interactive
   # Add the Blueprint extension to the Azure CLI environment
   az extension add --name blueprint
   ```

1. Controleer of de extensie is geïnstalleerd en of deze de verwachte versie heeft (ten minste **0.1.0**):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for extension options
   az blueprint -h
   ```

## <a name="create-a-blueprint"></a>Een blauwdruk maken

De eerste stap bij het definiëren van een standaardpatroon voor naleving bestaat uit het samenstellen van een blauwdruk uit de beschikbare resources. U maakt een blauwdruk met de naam MyBlueprint om de rol en de beleidstoewijzingen voor het abonnement te configureren. Vervolgens voegt u een resourcegroep en een ARM-sjabloon toe en voegt u een roltoewijzing aan de resourcegroep toe.

> [!NOTE]
> Wanneer u de Azure CLI gebruikt, wordt het object _blauwdruk_ eerst gemaakt. Voor elk _artefact_ dat wordt toegevoegd en parameters bevat, moeten de parameters vooraf worden gedefinieerd in de eerste _blauwdruk_.

1. Maak het eerste _blauwdruk_ object. De parameter **parameters** accepteert een JSON-bestand dat alle blauwdrukparameters bevat. De parameters worden tijdens het toewijzen ingesteld en gebruikt door de artefacten die in latere stappen worden toegevoegd.

   - JSON-bestand - blueprintparms.json

     ```json
     {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
                "Standard_LRS",
                "Standard_GRS",
                "Standard_ZRS",
                "Premium_LRS"
            ],
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
                "description": "List of AAD object IDs that is assigned Contributor role at the subscription",
                "strongType": "PrincipalId"
            }
        },
        "owners": {
            "type": "array",
            "metadata": {
                "description": "List of AAD object IDs that is assigned Owner role at the resource group",
                "strongType": "PrincipalId"
            }
        }
     }
     ```

   - Azure CLI-opdracht

     ```azurecli-interactive
     # Login first with az login if not using Cloud Shell

     # Create the blueprint object
     az blueprint create \
        --name 'MyBlueprint' \
        --description 'This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.' \
        --parameters blueprintparms.json
     ```

     > [!NOTE]
     > Gebruik de bestandsnaam _blueprint.json_ bij het importeren van uw blauwdrukdefinities.
     > Deze bestandsnaam wordt gebruikt bij het aanroepen van [az blueprint import](/cli/azure/ext/blueprint/blueprint#ext_blueprint_az_blueprint_import).

     Het blauwdrukobject wordt standaard gemaakt in het standaardabonnement. Als u de beheergroep wilt opgeven, gebruikt u de parameter **beheergroep**. Als u het abonnement wilt opgeven, gebruikt u de parameter **abonnement**.

1. Voeg de resourcegroep voor de opslagartefacten toe aan de definitie.

   ```azurecli-interactive
   az blueprint resource-group add \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'storageRG' \
      --description 'Contains the resource template deployment and a role assignment.'
   ```

1. Voeg de roltoewijzing toe aan het abonnement. In het onderstaande voorbeeld worden de principal-identiteiten en de opgegeven rol geconfigureerd in een parameter die wordt ingesteld tijdens de toewijzing van de blauwdruk. In dit voorbeeld wordt de ingebouwde rol van _Inzender_ met een GUID van `b24988ac-6180-42a0-ab88-20f7382dd24c` gebruikt.

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleContributor' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c' \
      --principal-ids "[parameters('contributors')]"
   ```

1. Voeg de beleidstoewijzing toe aan het abonnement. In dit voorbeeld wordt het ingebouwde beleid _Tag met standaardwaarde op resourcegroepen toepassen_ met een GUID van `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` gebruikt.

   - JSON-bestand - artifacts\policyTags.json

     ```json
     {
        "tagName": {
           "value": "[parameters('tagName')]"
        },
        "tagValue": {
           "value": "[parameters('tagValue')]"
        }
     }
     ```

   - Azure CLI-opdracht

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply tag and its default value to resource groups' \
        --description 'Apply tag and its default value to resource groups' \
        --parameters artifacts\policyTags.json
     ```

     > [!NOTE]
     > Wanneer u `az blueprint` op een Mac gebruikt, vervangt u door `\` `/` voor parameter waarden die het pad bevatten. In dit geval wordt de waarde voor **para meters** `artifacts/policyTags.json` .

1. Voeg nog een beleidstoewijzing toe voor de Storage-tag (gebruik hierbij de parameter _storageAccountType_ opnieuw) aan het abonnement. Deze aanvullende beleidstoewijzingsartefact laat zien dat een in de blauwdruk gedefinieerde parameter door meer dan één artefact kan worden gebruikt. In dit voorbeeld wordt **storageAccountType** gebruikt voor het instellen van een tag op de resourcegroep. Deze waarde geeft informatie over het opslagaccount dat in de volgende stap wordt gemaakt. In dit voorbeeld wordt het ingebouwde beleid _Tag met standaardwaarde op resourcegroepen toepassen_ met een GUID van `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` gebruikt.

   - JSON-bestand-artifacts\policyStorageTags.json

     ```json
     {
        "tagName": {
           "value": "StorageType"
        },
        "tagValue": {
           "value": "[parameters('storageAccountType')]"
        }
     }
     ```

   - Azure CLI-opdracht

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyStorageTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply storage tag to resource group' \
        --description 'Apply storage tag and the parameter also used by the template to resource groups' \
        --parameters artifacts\policyStorageTags.json
     ```

     > [!NOTE]
     > Wanneer u `az blueprint` op een Mac gebruikt, vervangt u door `\` `/` voor parameter waarden die het pad bevatten. In dit geval wordt de waarde voor **para meters** `artifacts/policyStorageTags.json` .

1. Voeg een sjabloon toe onder resourcegroep. De parameter **template** voor een ARM-sjabloon bevat de normale JSON-onderdelen van de sjabloon. De sjabloon maakt ook opnieuw gebruik van de blauwdrukparameters **storageAccountType**, **tagName** en **tagValue** door ze allemaal door te geven aan de sjabloon. De blauwdrukparameters zijn voor de sjabloon beschikbaar door de parameter **parameters** te gebruiken. Binnen de sjabloon-JSON wordt dat sleutel-waardepaar gebruikt om de waarde in te voeren. De namen van de blauwdruk- en sjabloonparameters kunnen dezelfde zijn.

   - JSON ARM-sjabloonbestand - artifacts\templateStorage.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "type": "string",
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
             "location": "[resourceGroup().location]",
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
     }
     ```

   - Parameterbestand voor JSON ARM-sjabloon - artifacts\templateStorageParams.json

     ```json
     {
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
     ```

   - Azure CLI-opdracht

     ```azurecli-interactive
     az blueprint artifact template create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'templateStorage' \
        --template artifacts\templateStorage.json \
        --parameters artifacts\templateStorageParams.json \
        --resource-group-art 'storageRG'
     ```

     > [!NOTE]
     > Wanneer u `az blueprint` op een Mac gebruikt, vervangt u door `\` `/` voor parameter waarden die het pad bevatten. In dit geval wordt de waarde voor de **sjabloon** weer `artifacts/templateStorage.json` en worden **para meters** weer `artifacts/templateStorageParams.json` .

1. Voeg een roltoewijzing toe onder resourcegroep. Net als bij de vorige vermelding van een roltoewijzing wordt in het onderstaande voorbeeld de definitie-id van de rol **Eigenaar** gebruikt en krijgt deze een andere parameter van de blauwdruk. In dit voorbeeld wordt de ingebouwde rol van _Eigenaar_ met een GUID van `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` gebruikt.

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleOwner' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635' \
      --principal-ids "[parameters('owners')]" \
      --resource-group-art 'storageRG'
   ```

## <a name="publish-a-blueprint"></a>Een blauwdruk publiceren

Nu de artefacten zijn toegevoegd aan de blauwdruk, is het tijd om deze te publiceren. Als de blauwdruk wordt gepubliceerd, kan deze worden toegewezen aan een abonnement.

```azurecli-interactive
az blueprint publish --blueprint-name 'MyBlueprint' --version '{BlueprintVersion}'
```

De waarde voor `{BlueprintVersion}` is een reeks letters, cijfers en afbreekstreepjes (geen spaties of andere speciale tekens) met een maximale lengte van twintig tekens. Gebruik iets unieks en informatiefs als **v20200605-135541**.

## <a name="assign-a-blueprint"></a>Een blauwdruk toewijzen

Nadat een blauwdruk is gepubliceerd met Azure CLI, kan deze worden toegewezen aan een abonnement. Wijs de blauwdruk die u hebt gemaakt toe aan een van de abonnementen in uw beheergroephiërarchie. Als de blauwdruk is opgeslagen in een abonnement, kan deze alleen aan dat abonnement worden toegewezen. De parameter **blauwdruknaam** vermeldt de blauwdruk die moet worden toegewezen. Als u de parameters naam, locatie, identiteit, vergrendeling en blauwdruk wilt opgeven, gebruikt u de overeenkomende Azure CLI-parameters op de `az blueprint assignment create`-opdracht of geeft u deze op in het JSON-bestand **parameters**.

1. Voer de blauwdrukimplementatie uit door deze toe te wijzen aan een abonnement. Als voor de parameters **inzenders** en **eigenaren** voor de roltoewijzing een matrix van objectIds van de principals vereist is, gebruikt u [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) om de objectIds op te halen voor gebruik in de **parameters** voor uw eigen gebruikers, groepen of service-principals.

   - JSON-bestand - blueprintAssignment.json

     ```json
     {
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
     ```

   - Azure CLI-opdracht

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

   - Door een gebruiker toegewezen beheerde identiteit

     Een blauwdruktoewijzing kan ook gebruikmaken van een [door een gebruiker toegewezen beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md).
     In dit geval wordt de parameter **identiteit-type** ingesteld op _UserAssigned_ en geeft de parameter **gebruiker-toegewezen-identiteiten** de identiteit op. Vervang `{userIdentity}` door de naam van de door de gebruiker toegewezen beheerde identiteit.

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --identity-type UserAssigned \
        --user-assigned-identities {userIdentity} \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

     De **door een gebruiker toegewezen beheerde identiteit** kan in elk abonnement en in elke resourcegroep aanwezig zijn waarvoor de gebruiker die de blauwdruk toewijst, is gemachtigd.

     > [!IMPORTANT]
     > De door een gebruiker toegewezen beheerde identiteit wordt niet beheerd door Azure Blueprints. Gebruikers zijn verantwoordelijk voor het toewijzen van voldoende rollen en machtigingen, anders mislukken de blauwdruktoewijzingen.

## <a name="clean-up-resources"></a>Resources opschonen

### <a name="unassign-a-blueprint"></a>De toewijzing van een blauwdruk ongedaan maken

U kunt een blauwdruk uit een abonnement verwijderen. Het verwijderen wordt vaak uitgevoerd als de artefactresources niet langer nodig zijn. Wanneer een blauwdruk wordt verwijderd, blijven de artefacten die als onderdeel van die blauwdruk zijn toegewezen, achter. Als u een blauwdruktoewijzing wilt verwijderen, gebruikt u de opdracht `az blueprint assignment delete`:

```azurecli-interactive
az blueprint assignment delete --name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een blauwdruk gemaakt, toegewezen en verwijderd met Azure CLI. Ga verder met het artikel over de levenscyclus van blauwdrukken voor meer informatie over Azure Blueprints.

> [!div class="nextstepaction"]
> [Meer informatie over de levenscyclus van een blauwdruk](./concepts/lifecycle.md)