---
title: Beheerde app met beheerde identiteit
description: Configureer beheerde toepassingen met beheerde identiteit voor het koppelen van bestaande resources, het beheren van Azure-resources en het leveren van operationele identiteit voor het activiteiten logboek.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: 277faa2d47df9fddd1762d90d9aa2fb5bf00d4df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "82508123"
---
# <a name="azure-managed-application-with-managed-identity"></a>Door Azure beheerde toepassing met beheerde identiteit

> [!NOTE]
> Beheerde identiteits ondersteuning voor beheerde toepassingen is momenteel beschikbaar als preview-versie. Gebruik de API-versie 2018-09-01-Preview om de beheerde identiteit te gebruiken.

Meer informatie over hoe u een beheerde toepassing kunt configureren voor het opnemen van een beheerde identiteit. Beheerde identiteit kan worden gebruikt om de klant in staat te stellen de toegang tot de beheerde toepassing toe te kennen aan aanvullende bestaande resources. De identiteit wordt beheerd door het Azure-platform en u hoeft geen geheimen in te richten of te draaien. Zie [beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten in azure Active Directory (Aad).

Aan uw toepassing kunnen twee typen identiteiten worden verleend:

- Een door het **systeem toegewezen identiteit** is gekoppeld aan uw toepassing en wordt verwijderd als uw app wordt verwijderd. Een app kan slechts één door het systeem toegewezen identiteit hebben.
- Een door de **gebruiker toegewezen identiteit** is een zelfstandige Azure-resource die aan uw app kan worden toegewezen. Een app kan meerdere door de gebruiker toegewezen identiteiten hebben.

## <a name="how-to-use-managed-identity"></a>Beheerde identiteit gebruiken

Met beheerde identiteit kunt u veel scenario's voor beheerde toepassingen maken. Enkele veelvoorkomende scenario's die kunnen worden opgelost zijn:

- Een beheerde toepassing implementeren die is gekoppeld aan bestaande Azure-resources. Een voor beeld is het implementeren van een virtuele Azure-machine (VM) in de beheerde toepassing die is gekoppeld aan een [bestaande netwerk interface](../../virtual-network/virtual-network-network-interface-vm.md).
- Het verlenen van de beheerde toepassing en uitgever toegang tot Azure-resources buiten de **beheerde resource groep**.
- Het bieden van een operationele identiteit van beheerde toepassingen voor activiteiten logboeken en andere services in Azure.

## <a name="adding-managed-identity"></a>Beheerde identiteit toevoegen

Voor het maken van een beheerde toepassing met een beheerde identiteit moet een extra eigenschap worden ingesteld voor de Azure-resource. In het volgende voor beeld ziet u een voor beeld van een **identiteits** eigenschap:

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Er zijn twee algemene manieren om een beheerde toepassing te maken met **identiteit**: [CreateUIDefinition.jsop](./create-uidefinition-overview.md) en [Azure Resource Manager sjablonen](../templates/template-syntax.md). Voor eenvoudige enkelvoudige scenario's moet CreateUIDefinition worden gebruikt om beheerde identiteit in te scha kelen, omdat het een rijkere ervaring biedt. Bij het verwerken van geavanceerde of complexe systemen waarvoor geautomatiseerde of meerdere beheerde toepassings implementaties zijn vereist, kunnen sjablonen echter worden gebruikt.

### <a name="using-createuidefinition"></a>CreateUIDefinition gebruiken

Een beheerde toepassing kan met beheerde identiteit worden geconfigureerd via de [CreateUIDefinition.jsop](./create-uidefinition-overview.md). In de [sectie outputs](./create-uidefinition-overview.md#outputs)kan de sleutel `managedIdentity` worden gebruikt om de eigenschap Identity van de sjabloon beheerde toepassing te overschrijven. Met de voor beeld-onderstaande wordt de door het **systeem toegewezen** identiteit ingeschakeld voor de beheerde toepassing. Complexere identiteits objecten kunnen worden gevormd met behulp van CreateUIDefinition-elementen om de gebruiker te vragen om invoer. Deze invoer kan worden gebruikt om beheerde toepassingen te bouwen met door de **gebruiker toegewezen identiteit**.

```json
"outputs": {
    "managedIdentity": { "Type": "SystemAssigned" }
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Wanneer u CreateUIDefinition gebruikt voor beheerde identiteit

Hieronder vindt u enkele aanbevelingen voor het gebruik van CreateUIDefinition voor het inschakelen van beheerde identiteit voor beheerde toepassingen.

- Het maken van beheerde toepassingen gaat via de Azure Portal of Marketplace.
- Voor de beheerde identiteit is complexe consumenten invoer vereist.
- De beheerde identiteit is vereist bij het maken van de beheerde toepassing.

#### <a name="managed-identity-createuidefinition-control"></a>Beheer van beheerde identiteit CreateUIDefinition

CreateUIDefinition biedt ondersteuning voor een ingebouwd [beheerd identiteits beheer](./microsoft-managedidentity-identityselector.md).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.0.1-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "applicationSettings",
        "label": "Application Settings",
        "subLabel": {
          "preValidation": "Configure your application settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Application Settings",
        "elements": [
          {
            "name": "appName",
            "type": "Microsoft.Common.TextBox",
            "label": "Managed application Name",
            "toolTip": "Managed application instance name",
            "visible": true
          },
          {
            "name": "appIdentity",
            "type": "Microsoft.ManagedIdentity.IdentitySelector",
            "label": "Managed Identity Configuration",
            "toolTip": {
              "systemAssignedIdentity": "Enable system assigned identity to grant the managed application access to additional existing resources.",
              "userAssignedIdentity": "Add user assigned identities to grant the managed application access to additional existing resources."
            },
            "defaultValue": {
              "systemAssignedIdentity": "Off"
            },
            "options": {
              "hideSystemAssignedIdentity": false,
              "hideUserAssignedIdentity": false,
              "readOnlySystemAssignedIdentity": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "applicationResourceName": "[steps('applicationSettings').appName]",
      "location": "[location()]",
      "managedIdentity": "[steps('applicationSettings').appIdentity]"
    }
  }
}
```

![Beheerde identiteit CreateUIDefinition](./media/publish-managed-identity/msi-cuid.png)

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen gebruiken

> [!NOTE]
> Door Marketplace beheerde toepassings sjablonen worden automatisch gegenereerd voor klanten die de Azure Portal maken.
> Voor deze scenario's moet de `managedIdentity` uitvoer sleutel op de CreateUIDefinition worden gebruikt om de identiteit in te scha kelen.

De beheerde identiteit kan ook via Azure Resource Manager sjablonen worden ingeschakeld. Met de voor beeld-onderstaande wordt de door het **systeem toegewezen** identiteit ingeschakeld voor de beheerde toepassing. Complexere identiteits objecten kunnen worden gevormd door Azure Resource Manager sjabloon parameters te gebruiken voor het leveren van invoer. Deze invoer kan worden gebruikt om beheerde toepassingen te bouwen met door de **gebruiker toegewezen identiteit**.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Wanneer u Azure Resource Manager sjablonen voor beheerde identiteiten wilt gebruiken

Hieronder vindt u enkele aanbevelingen voor het gebruik van Azure Resource Manager sjablonen voor het inschakelen van beheerde identiteit voor beheerde toepassingen.

- Beheerde toepassingen kunnen programmatisch worden geïmplementeerd op basis van een sjabloon.
- Aangepaste roltoewijzingen voor de beheerde identiteit zijn nodig om de beheerde toepassing in te richten.
- De beheerde toepassing heeft de stroom voor het maken van Azure Portal en Marketplace niet nodig.

#### <a name="systemassigned-template"></a>SystemAssigned-sjabloon

Een basis Azure Resource Manager sjabloon waarmee een beheerde toepassing wordt geïmplementeerd met een door het **systeem toegewezen** identiteit.

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>UserAssigned-sjabloon

Een basis Azure Resource Manager sjabloon waarmee een beheerde toepassing met een door de **gebruiker toegewezen identiteit** wordt geïmplementeerd.

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>Toegang verlenen tot Azure-resources

Zodra een beheerde toepassing een identiteit heeft verleend, kan deze toegang krijgen tot bestaande Azure-resources. Dit proces kan worden uitgevoerd via de interface toegangs beheer (IAM) in de Azure Portal. De naam van de beheerde toepassing of de door de **gebruiker toegewezen identiteit** kan worden doorzocht om een roltoewijzing toe te voegen.

![Roltoewijzing toevoegen voor beheerde toepassing](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Bestaande Azure-resources koppelen

> [!NOTE]
> Een door de **gebruiker toegewezen identiteit** moet worden [geconfigureerd](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) voordat u de beheerde toepassing implementeert. Daarnaast wordt het implementeren van gekoppelde resources van beheerde toepassingen alleen ondersteund voor het **Marketplace** -type.

Beheerde identiteiten kunnen ook worden gebruikt voor het implementeren van een beheerde toepassing die toegang tot bestaande bronnen vereist tijdens de implementatie. Wanneer de beheerde toepassing wordt ingericht door de klant, kunnen door de **gebruiker toegewezen identiteiten** worden toegevoegd om extra autorisaties toe te voegen aan de **mainTemplate** -implementatie.

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>De CreateUIDefinition ontwerpen met een gekoppelde resource

Wanneer u de implementatie van de beheerde toepassing koppelt aan bestaande resources, moeten zowel de bestaande Azure-resource als een door de **gebruiker toegewezen identiteit** worden gegeven met de toepasselijke roltoewijzing voor die bron.

 Een voor beeld van een CreateUIDefinition waarvoor twee invoer vereist is: een resource-ID van een netwerk interface en een door de gebruiker toegewezen id-Resource-ID.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

Deze CreateUIDefinition.jsop het genereren van een gebruikers ervaring met twee velden. In het eerste veld kan de gebruiker de Azure-Resource-ID invoeren voor de resource die wordt gekoppeld aan de implementatie van de beheerde toepassing. De tweede is voor een consument dat de door de **gebruiker toegewezen identiteit** Azure-resource-id wordt ingevoerd die toegang heeft tot de gekoppelde Azure-resource. De gegenereerde ervaring ziet er als volgt uit:

![Voor beeld van CreateUIDefinition met twee invoer: een resource-ID van een netwerk interface en een door de gebruiker toegewezen id Resource-ID](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>De mainTemplate ontwerpen met een gekoppelde resource

Naast het bijwerken van de CreateUIDefinition moet de hoofd sjabloon ook worden bijgewerkt om het door gegeven in de gekoppelde Resource-ID te accepteren. De hoofd sjabloon kan worden bijgewerkt om de nieuwe uitvoer te accepteren door een nieuwe para meter toe te voegen. Omdat de `managedIdentity` uitvoer de waarde van de gegenereerde sjabloon voor de beheerde toepassing overschrijft, wordt deze niet door gegeven aan de hoofd sjabloon en mag deze niet worden opgenomen in de para meters van de sectie.

Een voor beeld van een hoofd sjabloon waarmee het netwerk profiel wordt ingesteld op een bestaande netwerk interface van de CreateUIDefinition.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>De beheerde toepassing gebruiken met een gekoppelde resource

Zodra het beheerde toepassings pakket is gemaakt, kan de beheerde toepassing worden gebruikt via de Azure Portal. Voordat deze kan worden gebruikt, zijn er verschillende vereiste stappen.

- Er moet een exemplaar van de vereiste gekoppelde Azure-resource worden gemaakt.
- De door de **gebruiker toegewezen identiteit** moet worden [gemaakt en toegewezen roltoewijzingen](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) aan de gekoppelde resource.
- De bestaande gekoppelde Resource-ID en de door de **gebruiker toegewezen identiteits** -id worden aan de CreateUIDefinition toegewezen.

## <a name="accessing-the-managed-identity-token"></a>Toegang tot het beheerde identiteits token

Het token van de beheerde toepassing is nu toegankelijk via de `listTokens` API van de Publisher-Tenant. Een voorbeeld aanvraag kan er als volgt uitzien:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

Para meters van aanvraag hoofdtekst:

Parameter | Vereist | Beschrijving
---|---|---
authorizationAudience | *geen* | De App-ID-URI van de doel resource. Het is ook de `aud` claim (doel groep) van het uitgegeven token. De standaard waarde is " https://management.azure.com/ "
userAssignedIdentities | *geen* | De lijst met door de gebruiker toegewezen beheerde identiteiten voor het ophalen van een token voor. Als u niets opgeeft, `listTokens` wordt het token geretourneerd voor de door het systeem toegewezen beheerde identiteit.


Een voor beeld van een antwoord kan er als volgt uitzien:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

Het antwoord bevat een matrix met tokens onder de `value` eigenschap:

Parameter | Beschrijving
---|---
access_token | Het aangevraagde toegangs token.
expires_in | Het aantal seconden dat het toegangs token geldig is.
expires_on | De time span op het moment dat het toegangs token verloopt. Dit wordt weer gegeven als het aantal seconden vanaf epoche.
not_before | De time span op het moment dat het toegangs token van kracht wordt. Dit wordt weer gegeven als het aantal seconden vanaf epoche.
authorizationAudience | De `aud` (doel groep) het toegangs token is aangevraagd. Dit is hetzelfde als de gegevens in de `listTokens` aanvraag.
resourceId | De Azure-Resource-ID voor het gepubliceerde token. Dit is de ID van de beheerde toepassing of de door de gebruiker toegewezen identiteits-ID.
token_type | Het type van het token.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een beheerde toepassing configureren met een aangepaste provider](../custom-providers/overview.md)
