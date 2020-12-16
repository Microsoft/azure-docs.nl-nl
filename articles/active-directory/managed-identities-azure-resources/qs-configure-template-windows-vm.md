---
title: Beheerde identiteiten configureren op Azure VM met behulp van sjabloon - Azure AD
description: Stapsgewijze instructies voor het configureren van beheerde identiteiten voor Azure-resources op een Azure VM, met behulp van een Azure Resource Manager-sjabloon.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 960e1fc19567b03024000e84217b3846f89f94f3
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588553"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-templates"></a>Beheerde identiteiten voor Azure-resources op een Azure VM configureren met behulp van sjablonen

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij alle services die Microsoft Azure AD-verificatie ondersteunen, zonder dat u aanmeldingsgegevens in uw code hoeft te hebben.

In dit artikel leert u, met behulp van de Azure Resource Manager-implementatiesjabloon, om de volgende beheerde identiteiten uit te voeren voor bewerkingen van Azure-resources op een Azure VM:

## <a name="prerequisites"></a>Vereisten

- Raadpleeg de [overzichtssectie](overview.md) als u niet bekend bent met het gebruiken van Azure Resource Manager-implementatiesjabloon. **Let op dat u nagaat wat het [verschil is tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#managed-identity-types)** .
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen

Net als Azure Portal en het uitvoeren van scripts, bieden [Azure Resource Manager](../../azure-resource-manager/management/overview.md)-sjablonen de mogelijkheid om nieuwe of gewijzigde resources gedefinieerd door een Azure-resourcegroep te implementeren. Er zijn verschillende opties beschikbaar voor het bewerken en implementeren van sjablonen, zowel lokaal als op basis van een portal, waaronder:

   - Door een [aangepaste sjabloon van de Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) te gebruiken, waarmee u een volledig nieuwe sjabloon kunt maken, of door het te baseren op een bestaande algemene sjabloon of een [quickstart-sjabloon](https://azure.microsoft.com/documentation/templates/).
   - Door af te leiden van een bestaande resourcegroep, door een sjabloon te exporteren vanaf ofwel [de oorspronkelijke implementatie](../../azure-resource-manager/templates/export-template-portal.md) ofwel van de [huidige status van de implementatie](../../azure-resource-manager/templates/export-template-portal.md).
   - Door een lokale [JSON-editor (zoals VS Code)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) te gebruiken en deze vervolgens te uploaden en implementeren met behulp van PowerShell of CLI.
   - Door het [Azure Resource Group-project](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) van Visual Studio te gebruiken om een sjabloon te maken en te implementeren.  

Welke optie u ook kiest, de sjabloonsyntaxis is dezelfde tijdens de eerste implementatie en herimplementatie. Het inschakelen van een door een systeem of gebruiker toegewezen beheerde identiteit op een nieuwe of bestaande virtuele machine gebeurt op dezelfde manier. Azure Resource Manager voert ook standaard een [incrementele update](../../azure-resource-manager/templates/deployment-modes.md) uit naar implementaties.

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie gaat u een door het systeem toegewezen beheerde identiteit in- en uitschakelen met behulp van een Azure Resource Manager-sjabloon.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een Azure VM of een bestaande VM

Als u de door het systeem toegewezen beheerde identiteit op een VM wilt inschakelen, moet uw account beschikken over de roltoewijzing [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

1. Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat, of u zich nu lokaal aanmeldt of via Azure Portal.

2. Als u de door het systeem toegewezen beheerde identiteit wilt inschakelen, laadt u de sjabloon in een editor, zoekt u de gewenste `Microsoft.Compute/virtualMachines`-resource op in de sectie `resources` en voegt u de eigenschap `"identity"` op hetzelfde niveau toe als de eigenschap `"type": "Microsoft.Compute/virtualMachines"`. Gebruik de volgende syntaxis:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   },
   ```



3. Wanneer u klaar bent, zijn de volgende secties toegevoegd aan de sectie `resource` van uw sjabloon en ziet deze er als volgt uit:

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                },
            },

            //The following appears only if you provisioned the optional VM extension (to be deprecated)
            {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2018-06-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                }
            }
        }
    ]
   ```

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>Een rol toewijzen aan de door het systeem toegewezen beheerde identiteit van de VM

Nadat u de door het systeem toegewezen beheerde identiteit op uw virtuele machine hebt ingeschakeld, wilt u deze wellicht een rol geven, zoals **Lezer**-toegang tot de resourcegroep waarin deze is gemaakt.

Om een rol toe te wijzen aan de door het systeem toegewezen identiteit van uw VM, heeft uw account de roltoewijzing [Beheerder voor gebruikerstoegang](../../role-based-access-control/built-in-roles.md#user-access-administrator) nodig.

1. Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat, of u zich nu lokaal aanmeldt of via Azure Portal.

2. Laad de sjabloon in een [editor](#azure-resource-manager-templates) en voeg de volgende gegevens toe om uw VM **Lezer**-toegang te geven tot de resourcegroep waarin deze is gemaakt.  De structuur van uw sjabloon is afhankelijk van de editor en het implementatiemodel dat u kiest.

   Voeg het volgende toe onder de sectie `parameters`:

    ```JSON
    "builtInRoleType": {
        "type": "string",
        "defaultValue": "Reader"
    },
    "rbacGuid": {
        "type": "string"
    }
    ```

    Voeg het volgende toe onder de sectie `variables`:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    Voeg het volgende toe onder de sectie `resources`:

    ```JSON
    {
        "apiVersion": "2017-09-01",
        "type": "Microsoft.Authorization/roleAssignments",
        "name": "[parameters('rbacGuid')]",
        "properties": {
            "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
            "principalId": "[reference(variables('vmResourceId'), '2017-12-01', 'Full').identity.principalId]",
            "scope": "[resourceGroup().id]"
        },
         "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
        ]
    }
    ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Een door het systeem toegewezen beheerde identiteit van een Azure VM uitschakelen

Als u de door het systeem toegewezen beheerde identiteit uit een VM wilt verwijderen, moet uw account beschikken over de roltoewijzing [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

1. Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat, of u zich nu lokaal aanmeldt of via Azure Portal.

2. Laad de sjabloon in een [editor](#azure-resource-manager-templates) en zoek de gewenste `Microsoft.Compute/virtualMachines`-resource op in de sectie `resources`. Als u een VM hebt die alleen een door het systeem toegewezen beheerde identiteit heeft, kunt u deze uitschakelen door het identiteitstype te wijzigen in `None`.  

   **Microsoft.Compute/virtualMachines API versie 01-06-2018**

   Als uw virtuele machine zowel door het systeem als door de gebruiker toegewezen beheerde identiteiten heeft, verwijdert u `SystemAssigned` uit het identiteitstype en behoudt u `UserAssigned` samen met de woordenlijstwaarden `userAssignedIdentities`.

   **Microsoft.Compute/virtualMachines API versie 01-06-2018**

   Als uw `apiVersion` `2017-12-01` is en uw virtuele machine heeft zowel door het systeem als door de gebruiker toegewezen beheerde identiteiten, verwijdert u `SystemAssigned` uit het identiteitstype en behoudt u `UserAssigned` samen met de matrix `identityIds` van door de gebruiker toegewezen beheerde identiteiten.  

In het volgende voorbeeld ziet u hoe u een door het systeem toegewezen beheerde identiteit verwijdert uit een VM zonder door de gebruiker toegewezen beheerde identiteiten:

 ```JSON
 {
     "apiVersion": "2018-06-01",
     "type": "Microsoft.Compute/virtualMachines",
     "name": "[parameters('vmName')]",
     "location": "[resourceGroup().location]",
     "identity": {
         "type": "None"
     }
 }
 ```

## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie wijst u een door het systeem toegewezen beheerde identiteit toe aan een Azure VM met behulp van Azure Resource Manager-sjabloon.

> [!Note]
> Zie [Een door de gebruiker toegewezen beheerde identiteit maken](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity) als u een door de gebruiker toegewezen beheerde identiteit wilt maken met behulp van een Azure Resource Manager-sjabloon.

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een Azure VM

Als u een door een gebruiker toegewezen identiteit wilt toewijzen aan een VM, moet uw account beschikken over de roltoewijzingen [Inzender van de virtuele machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) en [Operator van de beheerde identiteit](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

1. Voeg onder het element `resources` de volgende vermelding toe om een door de gebruiker toegewezen beheerde identiteit aan uw virtuele machine toe te wijzen.  Vervang `<USERASSIGNEDIDENTITY>` door de naam van de door de gebruiker toegewezen beheerde identiteit die u hebt gemaakt.

   **Microsoft.Compute/virtualMachines API versie 01-06-2018**

   Als uw `apiVersion` `2018-06-01` is, worden uw door de gebruiker toegewezen beheerde identiteiten opgeslagen in de woordenlijstindeling `userAssignedIdentities` en moet de waarde `<USERASSIGNEDIDENTITYNAME>` worden opgeslagen in een variabele die is gedefinieerd in de sectie `variables` van uw sjabloon.

   ```JSON
    {
        "apiVersion": "2018-06-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
            }
        }
    }
   ```

   **Microsoft.Compute/virtualMachines API versie 01-12-2017**

   Als uw `apiVersion` `2017-12-01` is, worden uw door de gebruiker toegewezen beheerde identiteiten opgeslagen in de matrix `identityIds` en moet de waarde `<USERASSIGNEDIDENTITYNAME>` worden opgeslagen in een variabele die is gedefinieerd in de sectie `variables` van uw sjabloon.

   ```JSON
   {
       "apiVersion": "2017-12-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
           ]
       }
   }
   ```

3. Wanneer u klaar bent, zijn de volgende secties toegevoegd aan de sectie `resource` van uw sjabloon en ziet deze er als volgt uit:

   **Microsoft.Compute/virtualMachines API versie 01-06-2018**    

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "userAssignedIdentities": {
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            }
        },
        //The following appears only if you provisioned the optional VM extension (to be deprecated)                  
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2018-06-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                }
            }
        }
    ]   
   ```
   **Microsoft.Compute/virtualMachines API versie 01-12-2017**

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "identityIds": [
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            }
        },

        //The following appears only if you provisioned the optional VM extension (to be deprecated)                   
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                }
            }
       }
    ]
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>De door de gebruiker toegewezen beheerde identiteit van een Azure VM verwijderen

Als u de door de gebruiker toegewezen identiteit uit een VM wilt verwijderen, moet uw account beschikken over de roltoewijzing [Inzender voor virtuele machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor). Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

1. Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat, of u zich nu lokaal aanmeldt of via Azure Portal.

2. Laad de sjabloon in een [editor](#azure-resource-manager-templates) en zoek de gewenste `Microsoft.Compute/virtualMachines`-resource op in de sectie `resources`. Als u een VM hebt die alleen een door de gebruiker toegewezen beheerde identiteit heeft, kunt u deze uitschakelen door het identiteitstype te wijzigen in `None`.

   In het volgende voorbeeld ziet u hoe u alle door de gebruiker beheerde identiteiten verwijdert uit een VM zonder door het systeem toegewezen beheerde identiteiten:

   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": {
          "type": "None"
          },
    }
   ```

   **Microsoft.Compute/virtualMachines API versie 01-06-2018**

   Als u één door de gebruiker toegewezen beheerde identiteit uit een virtuele machine wilt verwijderen, verwijdert u deze uit de woordenlijst `useraAssignedIdentities`.

   Als u een door het systeem toegewezen beheerde identiteit hebt, bewaart u deze in de waarde `type` onder de waarde `identity`.

   **Microsoft.Compute/virtualMachines API versie 01-12-2017**

   Als u één door de gebruiker toegewezen beheerde identiteit uit een virtuele machine wilt verwijderen, verwijdert u deze uit de matrix `identityIds`.

   Als u een door het systeem toegewezen beheerde identiteit hebt, bewaart u deze in de waarde `type` onder de waarde `identity`.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beheerde identiteiten voor Azure-resources](overview.md).