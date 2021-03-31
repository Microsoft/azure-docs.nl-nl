---
title: Een app implementeren met een door de gebruiker toegewezen beheerde identiteit
description: In dit artikel wordt beschreven hoe u Service Fabric-toepassing implementeert met een door de gebruiker toegewezen beheerde identiteit
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 79d8654733b580be96d59e78f31105077929ac78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260090"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity"></a>Service Fabric toepassing implementeren met een User-Assigned beheerde identiteit

Als u een Service Fabric toepassing wilt implementeren met beheerde identiteit, moet de toepassing worden geïmplementeerd via Azure Resource Manager, meestal met een Azure Resource Manager-sjabloon. Zie [toepassingen en services beheren als Azure Resource Manager bronnen](service-fabric-application-arm-resource.md)voor meer informatie over het implementeren van service Fabric-toepassing via Azure Resource Manager.

> [!NOTE] 
> 
> Toepassingen die niet zijn geïmplementeerd als een Azure-resource, **kunnen** geen beheerde identiteiten hebben. 
>
> Service Fabric toepassings implementatie met een beheerde identiteit wordt ondersteund met API-versie `"2019-06-01-preview"` . U kunt ook dezelfde API-versie gebruiken voor het toepassings type, de versie van het toepassings type en service bronnen.
>

## <a name="user-assigned-identity"></a>User-Assigned identiteit

Als u de toepassing met User-Assigned identiteit wilt inschakelen, moet u eerst de eigenschap **Identity** toevoegen aan de toepassings resource met het type **userAssigned** en de door de gebruiker toegewezen identiteiten. Voeg vervolgens een sectie **managedIdentities** toe aan de sectie **Eigenschappen** van de **toepassings** resource die een lijst met beschrijvende namen bevat voor principalId-toewijzing voor elk van de door de gebruiker toegewezen identiteiten. Zie [een door de gebruiker toegewezen beheerde identiteit maken, weer geven of verwijderen](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)voor meer informatie over door de gebruiker toegewezen identiteiten.

### <a name="application-template"></a>Toepassingsjabloon

Als u de toepassing met de door de gebruiker toegewezen identiteit wilt inschakelen, voegt u eerst de eigenschap **Identity** toe aan de toepassings resource met het type **userAssigned** en de door de gebruiker toegewezen identiteiten en voegt u vervolgens een **managedIdentities** -object toe aan de sectie **Eigenschappen** die een lijst met beschrijvende namen bevat waarmee de door de gebruiker toegewezen identiteiten worden principalId.

```json
{
  "apiVersion": "2019-06-01-preview",
  "type": "Microsoft.ServiceFabric/clusters/applications",
  "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]",
    "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
  ],
  "identity": {
    "type" : "userAssigned",
    "userAssignedIdentities": {
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
    }
  },
  "properties": {
    "typeName": "[parameters('applicationTypeName')]",
    "typeVersion": "[parameters('applicationTypeVersion')]",
    "parameters": {
    },
    "managedIdentities": [
      {
        "name" : "[parameters('userAssignedIdentityName')]",
        "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
      }
    ]
  }
}
```

In het bovenstaande voor beeld wordt de resource naam van de door de gebruiker toegewezen identiteit gebruikt als beschrijvende naam van de beheerde identiteit voor de toepassing. In de volgende voor beelden wordt ervan uitgegaan dat de daad werkelijke beschrijvende naam ' AdminUser ' is.

### <a name="application-package"></a>Toepassings pakket

1. Voor elke identiteit die is gedefinieerd in de `managedIdentities` sectie van de Azure Resource Manager sjabloon, voegt u een `<ManagedIdentity>` tag toe in het manifest van de toepassing, in de sectie **principals** . Het `Name` kenmerk moet overeenkomen met de `name` eigenschap die in de sectie is gedefinieerd `managedIdentities` .

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. Voeg in de sectie **ServiceManifestImport** een **IdentityBindingPolicy** toe voor de service die gebruikmaakt van de beheerde identiteit. Dit beleid wijst de `AdminUser` identiteit toe aan een servicespecifieke identiteits naam die later aan het service manifest moet worden toegevoegd.

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Werk het service manifest bij om een **ManagedIdentity** toe te voegen aan de sectie **resources** met de naam die overeenkomt met de `ServiceIdentityRef` in het `IdentityBindingPolicy` manifest van de toepassing:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>Volgende stappen

* [Beheerde identiteit gebruiken in Service Fabric toepassings code](how-to-managed-identity-service-fabric-app-code.md)
* [Toegang tot Service Fabric toepassing verlenen aan andere Azure-resources](how-to-grant-access-other-resources.md)
