---
title: Een beheerde identiteit toevoegen aan een Service Fabric beheerd cluster knooppunt type (preview-versie)
description: In dit artikel wordt uitgelegd hoe u een beheerde identiteit kunt toevoegen aan een Service Fabric beheerd cluster knooppunt type
ms.topic: how-to
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 00e679b07a44b799b6ac6677201bb59eeddcd6cf
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841467"
---
# <a name="add-a-managed-identity-to-a-service-fabric-managed-cluster-node-type-preview"></a>Een beheerde identiteit toevoegen aan een Service Fabric beheerd cluster knooppunt type (preview-versie)

Elk knooppunt type in een door Service Fabric beheerd cluster wordt ondersteund door een schaalset voor virtuele machines. Om beheerde identiteiten te kunnen gebruiken met een beheerd cluster knooppunt type, is er een eigenschap `vmManagedIdentity` toegevoegd aan de definities van knooppunt typen met een lijst met identiteiten die kunnen worden gebruikt `userAssignedIdentities` . De functionaliteit komt overeen met de manier waarop beheerde identiteiten kunnen worden gebruikt in niet-beheerde clusters, zoals het gebruik van een beheerde identiteit met de [Azure Key Vault extensie voor virtuele-machine schaal sets](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows).


Zie [deze sjabloon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-MI)voor een voor beeld van een service Fabric beheerde cluster implementatie die gebruikmaakt van een beheerde identiteit op een knooppunt type. Raadpleeg de [Veelgestelde vragen over beheerde clusters](https://docs.microsoft.com/azure/service-fabric/faq-managed-cluster#what-regions-are-supported-in-the-preview)voor een lijst met ondersteunde regio's.

> [!NOTE]
> Er worden op dit moment alleen door de gebruiker toegewezen identiteiten ondersteund voor deze functie.

## <a name="prerequisites"></a>Vereisten

Voordat u begint:

* Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Als u van plan bent Power shell te gebruiken, moet u de Azure CLI [installeren](https://docs.microsoft.com/cli/azure/install-azure-cli) om CLI-referentie opdrachten uit te voeren.

## <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken 

Een door de gebruiker toegewezen beheerde identiteit kan worden gedefinieerd in de sectie resources van een sjabloon Azure Resource Manager (ARM) voor het maken van de implementatie:

```JSON
{ 
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities", 
    "name": "[parameters('userAssignedIdentityName')]", 
    "apiVersion": "2018-11-30", 
    "location": "[resourceGroup().location]"  
},
```

of gemaakt via Power shell:

```powershell
az group create --name <resourceGroupName> --location <location>
az identity create --name <userAssignedIdentityName> --resource-group <resourceGroupName>
```

## <a name="add-a-role-assignment-with-service-fabric-resource-provider"></a>Een roltoewijzing met Service Fabric resource provider toevoegen

Een roltoewijzing toevoegen aan de beheerde identiteit met de Service Fabric resource provider toepassing. Met deze toewijzing kan Service Fabric resource provider de identiteit toewijzen aan de schaalset voor virtuele machines van het beheerde cluster. 

De volgende waarden moeten worden gebruikt, indien van toepassing:

|Naam|Waarde van bijbehorende Service Fabric resource provider|
|----|-------------------------------------|
|Toepassings-id|74cb6831-0dbb-4be1-8206-fd4df301cdc2|
|Object-id|fbc587f2-66f5-4459-a027-bcd908b9d278|


|Naam van roldefinitie|Roldefinitie-ID|
|----|-------------------------------------|
|Beheerde identiteits operator|f1a07417-d97a-45cb-824c-7a7467783830
|



Deze roltoewijzing kan worden gedefinieerd in de sectie resources met behulp van de object-ID en de roldefinitie-ID:

```JSON
{
    "type": "Microsoft.Authorization/roleAssignments", 
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('vmIdentityRoleNameGuid')]",
    "scope": "[concat('Microsoft.ManagedIdentity/userAssignedIdentities', '/', parameters('userAssignedIdentityName'))]",
    "dependsOn": [ 
        "[concat('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
    ], 
    "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'f1a07417-d97a-45cb-824c-7a7467783830')]",
        "principalId": "fbc587f2-66f5-4459-a027-bcd908b9d278" 
    } 
}, 
```

of via Power shell gemaakt met behulp van de toepassings-ID en de roldefinitie-ID:

```powershell
New-AzRoleAssignment -ApplicationId 74cb6831-0dbb-4be1-8206-fd4df301cdc2 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

of object-ID en roldefinitie-ID:

```powershell
New-AzRoleAssignment -PrincipalId fbc587f2-66f5-4459-a027-bcd908b9d278 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

## <a name="add-managed-identity-properties-to-node-type-definition"></a>Eigenschappen van beheerde identiteit toevoegen aan de definitie van het knooppunt type

Voeg ten slotte de `vmManagedIdentity` Eigenschappen en toe `userAssignedIdentities` aan de knooppunt type definitie van het beheerde cluster:

```json

"properties": {
    "isPrimary" : true,
    "vmInstanceCount": 5,
    "dataDiskSizeGB": 100,
    "vmSize": "Standard_D2",
    "vmImagePublisher" : "MicrosoftWindowsServer",
    "vmImageOffer" : "WindowsServer",
    "vmImageSku" : "2019-Datacenter",
    "vmImageVersion" : "latest",
    "vmManagedIdentity": {
        "userAssignedIdentities": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
        ]
    }
}
```

Na de implementatie is de gemaakte beheerde identiteit toegevoegd aan de schaalset van de virtuele machine van het aangewezen knoop punt en kan worden gebruikt zoals verwacht, net als bij een niet-beheerd cluster.

## <a name="troubleshooting"></a>Problemen oplossen

Fout bij het goed toevoegen van een roltoewijzing wordt voldaan aan de volgende fout bij de implementatie:

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-error.png" alt-text="Azure Portal implementatie fout bij het weer geven van de client met object/toepassings-ID van SFRP die geen machtiging heeft voor het uitvoeren van identiteits beheer activiteit":::

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een app implementeren naar een beheerde Service Fabric-cluster](https://docs.microsoft.com/azure/service-fabric/tutorial-managed-cluster-deploy-app) 
