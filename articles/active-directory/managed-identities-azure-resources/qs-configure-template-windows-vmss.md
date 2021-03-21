---
title: Sjabloon configureren om beheerder identiteiten te gebruiken op virtuele-machineschaalsets - Azure AD
description: Stapsgewijze instructies voor het configureren van beheerde identiteiten voor Azure-resources op een virtuele-machineschaalset met behulp van een Azure Resource Manager-sjabloon.
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
ms.openlocfilehash: 377bbb9ce111f3cf2daf8426e128186711c30e5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97587448"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>Beheerde identiteiten voor Azure-resources configureren op een virtuele-machineschaalset van Azure met een sjabloon

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij alle services die Microsoft Azure AD-verificatie ondersteunen, zonder dat u aanmeldingsgegevens in uw code hoeft te hebben.

In dit artikel leert u om de volgende beheerde identiteiten uit te voeren voor bewerkingen van Azure-resources op een virtuele-machineschaalset van Azure met behulp van Azure Resource Manager-implementatiesjabloon:
- De door het systeem toegewezen beheerde identiteit inschakelen en uitschakelen op een virtuele-machineschaalset van Azure
- Een door de gebruiker toegewezen beheerde identiteit toevoegen aan en verwijderen uit een virtuele-machineschaalset van Azure

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de sectie [Overzicht](overview.md). **Let op dat u nagaat wat het [verschil is tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#managed-identity-types)** .
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Voor het uitvoeren van de beheerbewerkingen in dit artikel zijn voor uw account de volgende toewijzingen van op rollen gebaseerd toegangsbeheer van Azure nodig:

    > [!NOTE]
    > Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-map.

    - [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) voor het maken van een virtuele-machineschaalset en het inschakelen en verwijderen van een door het systeem en/of de gebruiker toegewezen beheerde identiteit op/uit een virtuele-machineschaalset.
    - De rol van [inzender voor beheerde identiteit](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) voor het maken van een door de gebruiker toegewezen beheerde identiteit.
    - De rol van [operator voor beheerde identiteit](../../role-based-access-control/built-in-roles.md#managed-identity-operator) voor het toewijzen/verwijderen van een door de gebruiker toegewezen beheerde identiteit aan/uit een virtuele-machine schaalset.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen

Net als Azure Portal en het uitvoeren van scripts, bieden [Azure Resource Manager](../../azure-resource-manager/management/overview.md)-sjablonen de mogelijkheid om nieuwe of gewijzigde resources gedefinieerd door een Azure-resourcegroep te implementeren. Er zijn verschillende opties beschikbaar voor het bewerken en implementeren van sjablonen, zowel lokaal als op basis van een portal, waaronder:

   - Door een [aangepaste sjabloon van de Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) te gebruiken, waarmee u een volledig nieuwe sjabloon kunt maken, of door het te baseren op een bestaande algemene sjabloon of een [quickstart-sjabloon](https://azure.microsoft.com/documentation/templates/).
   - Door af te leiden van een bestaande resourcegroep, door een sjabloon te exporteren vanaf ofwel [de oorspronkelijke implementatie](../../azure-resource-manager/templates/export-template-portal.md) ofwel van de [huidige status van de implementatie](../../azure-resource-manager/templates/export-template-portal.md).
   - Door een lokale [JSON-editor (zoals VS Code)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) te gebruiken en deze vervolgens te uploaden en implementeren met behulp van PowerShell of CLI.
   - Door het [Azure Resource Group-project](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) van Visual Studio te gebruiken om een sjabloon te maken en te implementeren.  

Welke optie u ook kiest, de sjabloonsyntaxis is dezelfde tijdens de eerste implementatie en herimplementatie. Het inschakelen van beheerde identiteiten op een nieuwe of bestaande virtuele machine gebeurt op dezelfde manier. Azure Resource Manager voert ook standaard een [incrementele update](../../azure-resource-manager/templates/deployment-modes.md) uit naar implementaties.

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie gaat u de door het systeem toegewezen beheerde identiteit in- en uitschakelen met behulp van een Azure Resource Manager-sjabloon.

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een virtuele-machineschaalset of een bestaande virtuele-machineschaalset

1. Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele-machineschaalset bevat, of u zich nu lokaal aanmeldt of via Azure Portal.
2. Als u de door het systeem toegewezen beheerde identiteit wilt inschakelen, laadt u de sjabloon in een editor, zoekt u de gewenste `Microsoft.Compute/virtualMachinesScaleSets`-resource op in de resourcessectie en voegt u de eigenschap `identity` op hetzelfde niveau toe als de eigenschap `"type": "Microsoft.Compute/virtualMachinesScaleSets"`. Gebruik de volgende syntaxis:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   }
   ```

> [!NOTE]
> U kunt eventueel de beheerde identiteiten voor de virtuele-machine schaalset-extensie van Azure-resources inrichten door deze op te geven in het `extensionProfile`-element van de sjabloon. Deze stap is optioneel, omdat u het identiteitseindpunt van Azure Instance Metadata Service (IMDS) ook kunt gebruiken om tokens op te halen.  Zie [Migreren van VM-extensie naar Azure IMDS voor verificatie](howto-migrate-vm-extension.md) voor meer informatie.


4. Wanneer u klaar bent, zijn de volgende secties toegevoegd aan de resourcesectie van uw sjabloon en ziet deze er als volgt uit:

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
            },
           "properties": {
                //other resource provider properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Een door het systeem toegewezen beheerde identiteit uitschakelen vanuit een virtuele-machineschaalset van Azure

Als u een virtuele-machineschaalset hebt waarvoor geen door het systeem toegewezen beheerde identiteit meer nodig is, doet u het volgende:

1. Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele-machineschaalset bevat, of u zich nu lokaal aanmeldt of via Azure Portal.

2. Laad de sjabloon in een [editor](#azure-resource-manager-templates) en zoek de gewenste `Microsoft.Compute/virtualMachineScaleSets`-resource op in de sectie `resources`. Als u een VM hebt die alleen een door het systeem toegewezen beheerde identiteit heeft, kunt u deze uitschakelen door het identiteitstype te wijzigen in `None`.

   **Microsoft.Compute/virtualMachineScaleSets API versie 01-06-2018**

   Als uw apiVersion `2018-06-01` is en uw virtuele machine heeft zowel door het systeem als door de gebruiker toegewezen beheerde identiteiten, verwijdert u `SystemAssigned` uit het identiteitstype en bewaart u `UserAssigned` samen met de woordenlijstwaarden userAssignedIdentities.

   **Microsoft.Compute/virtualMachineScaleSets API versie 01-06-2018**

   Als uw apiVersion `2017-12-01` is en uw virtuele-machineschaalset heeft zowel door het systeem als door de gebruiker toegewezen beheerde identiteiten, verwijdert u `SystemAssigned` uit het identiteitstype en bewaart u `UserAssigned` samen met de matrix `identityIds` van de door de gebruiker toegewezen beheerde identiteiten.



   In het volgende voorbeeld ziet u hoe u een door het systeem toegewezen beheerde identiteit verwijdert uit een virtuele-machineschaalset zonder door de gebruiker toegewezen beheerde identiteiten:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie wijst u een door het systeem toegewezen beheerde identiteit toe aan een virtuele-machineschaalset met behulp van Azure Resource Manager-sjabloon.

> [!Note]
> Zie [Een door de gebruiker toegewezen beheerde identiteit maken](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity) als u een door de gebruiker toegewezen beheerde identiteit wilt maken met behulp van een Azure Resource Manager-sjabloon.

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Een door een gebruiker toegewezen beheerde identiteit toewijzen aan een virtuele-machineschaalset

1. Voeg onder het element `resources` de volgende vermelding toe om een door de gebruiker toegewezen beheerde identiteit aan uw virtuele-machineschaalset toe te wijzen.  Vervang `<USERASSIGNEDIDENTITY>` door de naam van de door de gebruiker toegewezen beheerde identiteit die u hebt gemaakt.

   **Microsoft.Compute/virtualMachineScaleSets API versie 01-06-2018**

   Als uw apiVersion `2018-06-01` is, worden uw door de gebruiker toegewezen beheerde identiteiten opgeslagen in de woordenlijstindeling `userAssignedIdentities` en moet de waarde `<USERASSIGNEDIDENTITYNAME>` worden opgeslagen in een variabele die is gedefinieerd in de sectie `variables` van uw sjabloon.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
       }

   }
   ```   

   **Microsoft.Compute/virtualMachineScaleSets API versie 01-12-2017**

   Als uw `apiVersion` `2017-12-01` of eerder is, worden uw door de gebruiker toegewezen beheerde identiteiten opgeslagen in de matrix `identityIds` en moet de waarde `<USERASSIGNEDIDENTITYNAME>` worden opgeslagen in een variabele die is gedefinieerd in de variabelensectie van uw sjabloon.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }
   ```
> [!NOTE]
> U kunt eventueel de beheerde identiteiten voor de virtuele-machine schaalset-extensie van Azure-resources inrichten door deze op te geven in het `extensionProfile`-element van de sjabloon. Deze stap is optioneel, omdat u het identiteitseindpunt van Azure Instance Metadata Service (IMDS) ook kunt gebruiken om tokens op te halen.  Zie [Migreren van VM-extensie naar Azure IMDS voor verificatie](howto-migrate-vm-extension.md) voor meer informatie.

3. Wanneer u klaar bent, moet uw sjabloon er ongeveer als volgt uitzien:

   **Microsoft.Compute/virtualMachineScaleSets API versie 01-06-2018**   

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```

   **Microsoft.Compute/virtualMachines API versie 01-12-2017**

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "identityIds": [
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)    
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```
   ### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Door de gebruiker toegewezen beheerde identiteit uit een virtuele-machineschaalset van Azure verwijderen

Als u een virtuele-machineschaalset hebt waarvoor geen door de gebruiker toegewezen beheerde identiteit meer nodig is, doet u het volgende:

1. Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele-machineschaalset bevat, of u zich nu lokaal aanmeldt of via Azure Portal.

2. Laad de sjabloon in een [editor](#azure-resource-manager-templates) en zoek de gewenste `Microsoft.Compute/virtualMachineScaleSets`-resource op in de sectie `resources`. Als u een virtuele-machineschaalset hebt die alleen een door de gebruiker toegewezen beheerde identiteit heeft, kunt u deze uitschakelen door het identiteitstype te wijzigen in `None`.

   In het volgende voorbeeld ziet u hoe u alle door de gebruiker beheerde identiteiten verwijdert uit een VM zonder door het systeem toegewezen beheerde identiteiten:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }
   }
   ```

   **Microsoft.Compute/virtualMachineScaleSets API versie 01-06-2018**

   Als u één door de gebruiker toegewezen beheerde identiteit uit een virtuele-machineschaalset wilt verwijderen, verwijdert u deze uit de woordenlijst `userAssignedIdentities`.

   Als u een door het systeem toegewezen identiteit hebt, bewaart u deze in de waarde `type` onder de waarde `identity`.

   **Microsoft.Compute/virtualMachineScaleSets API versie 01-12-2017**

   Als u één door de gebruiker toegewezen beheerde identiteit uit een virtuele-machineschaalset wilt verwijderen, verwijdert u deze uit de matrix `identityIds`.

   Als u een door het systeem toegewezen beheerde identiteit hebt, bewaart u deze in de waarde `type` onder de waarde `identity`.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beheerde identiteiten voor Azure-resources](overview.md).