---
title: Een Service Fabric-app implementeren met aan het systeem toegewezen MI
description: In dit artikel wordt beschreven hoe u een door het systeem toegewezen beheerde identiteit toewijst aan een Azure Service Fabric-toepassing
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: c5c7a17c51eee18d9b7276f2c57289a5de5c8181
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "81415657"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity"></a>Service Fabric toepassing implementeren met door het systeem toegewezen beheerde identiteit

Als u toegang wilt krijgen tot de functie Managed Identity voor Azure Service Fabric-toepassingen, moet u eerst de service beheerde identiteits token inschakelen op het cluster. Deze service is verantwoordelijk voor de verificatie van Service Fabric toepassingen met behulp van hun beheerde identiteiten en voor het verkrijgen van toegangs tokens in hun naam. Zodra de service is ingeschakeld, kunt u deze weer geven in Service Fabric Explorer onder het gedeelte **systeem** in het linkerdeel venster, dat wordt uitgevoerd onder de naam **Fabric:/System/ManagedIdentityTokenService** naast andere systeem services.

> [!NOTE] 
> De implementatie van Service Fabric toepassingen met beheerde identiteiten wordt ondersteund vanaf de API-versie `"2019-06-01-preview"` . U kunt ook dezelfde API-versie gebruiken voor het toepassings type, de versie van het toepassings type en service bronnen. De mini maal ondersteunde Service Fabric runtime is 6,5 CU2. In additoin moet de ontwikkel-en pakket omgeving ook de SF .NET SDK op CU2 of hoger hebben

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

### <a name="application-template"></a>Toepassingsjabloon

Als u een toepassing met een door het systeem toegewezen beheerde identiteit wilt inschakelen, voegt u de eigenschap **Identity** toe aan de toepassings resource, met het type **systemAssigned** , zoals wordt weer gegeven in het volgende voor beeld:

```json
    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
Met deze eigenschap declareert u (voor Azure Resource Manager en de beheerde identiteits-en Service Fabric bron providers, die door deze resource een impliciet ( `system assigned` ) beheerde identiteit hebben.

### <a name="application-and-service-package"></a>Toepassings-en service pakket

1. Werk het toepassings manifest bij om een **ManagedIdentity** -element toe te voegen aan de sectie **principals** met een enkel item, zoals hieronder wordt weer gegeven:

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    Hiermee wijst u de identiteit die aan de toepassing is toegewezen als een resource toe aan een beschrijvende naam, voor verdere toewijzing aan de services die deel uitmaakt van de toepassing. 

2. Voeg in de sectie **ServiceManifestImport** die overeenkomt met de service waaraan de beheerde identiteit wordt toegewezen, een **IdentityBindingPolicy** -element toe, zoals hieronder wordt aangegeven:

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    Dit element wijst de identiteit van de toepassing toe aan de service. zonder deze toewijzing heeft de service geen toegang meer tot de identiteit van de toepassing. In het bovenstaande code fragment `SystemAssigned` is de identiteit (een gereserveerd tref woord) toegewezen aan de definitie van de service onder de beschrijvende naam `WebAdmin` .

3. Werk het service manifest bij om een **ManagedIdentity** -element toe te voegen aan de sectie **resources** met de naam die overeenkomt met de waarde van de `ServiceIdentityRef` instelling van de `IdentityBindingPolicy` definitie in het manifest van de toepassing:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    Dit is de equivalente toewijzing van een identiteit aan een service zoals hierboven wordt beschreven, maar vanuit het perspectief van de service definitie. Er wordt naar de identiteit verwezen met de beschrijvende naam ( `WebAdmin` ), zoals aangegeven in het manifest van de toepassing.

## <a name="next-steps"></a>Volgende stappen
* [Ondersteuning voor beheerde identiteiten](./concepts-managed-identity.md) bekijken in azure service Fabric
* [Een nieuwe implementeren](./configure-new-azure-service-fabric-enable-managed-identity.md) Azure Service Fabric-cluster met ondersteuning voor beheerde identiteiten 
* [Beheerde identiteit inschakelen](./configure-existing-cluster-enable-managed-identity-token-service.md) in een bestaand Azure service Fabric-cluster
* Gebruikmaken van de [beheerde identiteit van](./how-to-managed-identity-service-fabric-app-code.md) een service Fabric toepassing uit de bron code
* [Een Azure Service Fabric-toepassing implementeren met een door de gebruiker toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Een Azure Service Fabric-toepassing toegang verlenen tot andere Azure-resources](./how-to-grant-access-other-resources.md)
