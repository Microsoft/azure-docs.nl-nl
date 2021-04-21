---
title: Aangepaste Azure Resource Manager maken en toewijzen aan service-principal - Azure
description: Dit artikel bevat richtlijnen voor het maken van een aangepaste Azure Resource Manager en het toewijzen aan een service-principal voor Live Video Analytics op IoT Edge met behulp van Azure CLI.
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: 6c33f6703522fc0b28237e22c16c96587467df40
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788507"
---
# <a name="create-custom-azure-resource-manager-role-and-assign-to-service-principal"></a>Aangepaste Azure Resource Manager maken en toewijzen aan service-principal

Live Video Analytics voor IoT Edge module-instantie is een actief Azure Media Services nodig om het goed te laten werken. De relatie tussen de Live Video Analytics op IoT Edge module en het Azure Media Service-account wordt tot stand gebracht via een set eigenschappen van module-dubbels. Een van deze dubbele eigenschappen is een [service-principal](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) waarmee het module-exemplaar kan communiceren met en de benodigde bewerkingen op het Media Services activeren. Om mogelijke misbruik en/of onbedoelde blootstelling van gegevens van het edge-apparaat te minimaliseren, moet deze service-principal de minste bevoegdheden hebben.

In dit artikel worden de stappen beschreven voor het maken van een aangepaste Azure Resource Manager-rol met Azure Cloud Shell, die vervolgens wordt gebruikt om een service-principal te maken.

## <a name="prerequisites"></a>Vereisten  

De vereisten voor dit artikel zijn als volgt:

* Azure-abonnement met eigenaarsabonnement.
* Een Azure Active Directory bevoegdheden om een app te maken en service-principal toe te wijzen aan een rol.

De eenvoudigste manier om te controleren of uw account over de juiste machtigingen beschikt, verloopt via de portal. Zie [Check required permission](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) (Vereiste machtiging controleren).

## <a name="overview"></a>Overzicht  

We gaan de stappen voor het maken van een aangepaste rol doorlopen en deze in de volgende volgorde koppelen aan een service-principal:

1. Maak een Media Service-account als u er nog geen hebt.
1. Een service-principal maken.
1. Maak een aangepaste Azure Resource Manager met beperkte bevoegdheden.
1. 'Beperk' de bevoegdheden van de service-principal met behulp van de aangepaste rol die is gemaakt.
1. Voer een eenvoudige test uit om te zien of we de service-principal kunnen beperken.
1. Leg de parameters vast die worden gebruikt in de IoT Edge implementatiemanifests.

### <a name="create-a-media-services-account"></a>Een Media Services-account kunt maken  

Als u geen Media Service-account hebt, gebruikt u de volgende stappen om er een te maken.

1. Blader naar de [Cloud Shell](https://shell.azure.com/).
1. Selecteer 'Bash' als uw omgeving in de vervolgkeuzelijn aan de linkerkant van het shell-venster

    ![Scherm capturs toont Bash geselecteerd in het shell-venster.](./media/create-custom-azure-resource-manager-role-how-to/bash.png)
1. Stel uw Azure-abonnement in als het standaardaccount met behulp van de volgende opdrachtsjabloon:
    
    ```
    az account set --subscription " <yourSubscriptionName or yourSubscriptionId>"
    ```
1. Maak een [resourcegroep](/cli/azure/group#az_group_create) en een [opslagaccount.](/cli/azure/storage/account#az_storage_account_create)
1. Maak nu een Azure Media Service-account met behulp van de volgende opdrachtsjabloon in Cloud Shell:

    ```
    az ams account create --name <yourAMSAccountName>  --resource-group <yourResouceGroup>  --storage-account <yourStorageAccountName>
    ```

### <a name="create-service-principal"></a>Een service-principal maken  

We gaan nu een nieuwe service-principal maken en deze koppelen aan uw Media Service-account.

Zonder verificatieparameters wordt verificatie op basis van een wachtwoord gebruikt met een willekeurig wachtwoord voor uw service-principal. Gebruik Cloud Shell volgende opdrachtsjabloon:

```
az ams account sp create --account-name < yourAMSAccountName > --resource-group < yourResouceGroup >
```

Met deze opdracht wordt een antwoord als dit geproduceerd:

```
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "<yourServicePrincipalPassword>",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": " <yourAMSAccountName >",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "Central US",
  "ResourceGroup": " <yourResouceGroup >",
  "SubscriptionId": "<yourSubscriptionId>"
}

```
1. De uitvoer voor een service-principal met wachtwoordverificatie bevat de wachtwoordsleutel die in dit geval de parameter AadSecret is. 

    Zorg ervoor dat u deze waarde kopieert. Deze kan niet worden opgehaald. Als u het wachtwoord bent vergeten, stelt [u de referenties van de service-principal opnieuw in.](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials)
1. De appId en tenantsleutel worden in de uitvoer respectievelijk weergegeven als 'AadClientId' en 'AadTenantId'. Ze worden gebruikt bij verificatie van service-principals. Neem hun waarden op, maar ze kunnen op elk moment worden opgehaald met [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list).

### <a name="create-a-custom-role-definition"></a>Een aangepaste roldefinitie maken  

Als u een aangepaste rol wilt maken, volgt u de volgende stappen:

1. Maak een JSON-bestand met roldefinitie op uw lokale systeem en sla de volgende tekst op in het bestand. 
    1. Vervang < yourSubscriptionId> door uw Azure-abonnements-id
    1. De enige acties die zijn toegestaan voor deze rol zijn:
        * listContainerSas: helpt de module opslagcontainer-URL's weer te geven met SHARED Access Signatures (SAS) voor het uploaden en downloaden van assetinhoud.
        * Assets schrijven: helpt de module bij het maken of bijwerken van assets
        * listEdgePolicies: bevat de beleidsregels die worden toegepast op het edge-apparaat  
        
        ```
        {
          "Name": "LVAEdge User",
          "IsCustom": true,
          "Description": "Can create assets, view list of containers and view Edge policies",
          "Actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action"
          ],
          "NotActions": [],
          "DataActions": [],
          "NotDataActions": [],
          "AssignableScopes": [
            "/subscriptions/<yourSubscriptionId>"
          ]
        }
        ```  
          
1. Voer na het maken de volgende opdrachtsjabloon uit om de nieuwe roldefinitie in het abonnement te maken:
    
    ```
    az role definition create --role-definition "<location of the Role Definition JSON file >"
    ```

    Wanneer de opdracht is uitgevoerd, ziet u de volgende uitvoer:
    
    ```
    {
      "assignableScopes": [
      "/subscriptions/<yourSubscriptionId>"
      ],
      "description": "Can create assets, view list of containers and view Edge policies",
      "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<unique name>",
      "name": "<unique name>",
      "permissions": [
        {
          "actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action",
          ],
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": " LVAEdge User ",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

### <a name="create-role-assignment"></a>Roltoewijzing maken  

Als u een roltoewijzing wilt toevoegen, hebt u de objectId nodig van de service-principal die u wilt toewijzen aan de aangepaste rol die u zojuist hebt gemaakt.

Gebruik de volgende opdracht in Cloud Shell objectId op te halen:

```
az ad sp show --id "<appId>" | Select-String "objectId"
```

> [!NOTE]
> `<appId>` kan worden opgehaald uit de uitvoer van de [stap Service-principal](#create-service-principal) maken.

Met de bovenstaande opdracht wordt de objectId van de service-principal afgedrukt. 

```
“objectId” : “<yourObjectId>”,
```

Gebruik [de opdrachtsjabloon az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) om de aangepaste rol te koppelen aan de service-principal:

```
az role assignment create --role “LVAEdge User” --assignee-object-id < objectId>    
```

Parameters:

|Parameters|Description| 
|---|---|
|--role |Aangepaste rolnaam of -id. In ons geval: 'LVAEdge User'.|
|--assignee-object-id|Object-id van de service-principal die u gaat gebruiken.|

Het resultaat ziet er als volgende uit:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleAssignments/<yourCustomRoleId>",
  "name": "<yourCustomRoleId>",
  "principalId": "<yourServicePrincipalId>",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<yourRoleDefinitionId>",
  "scope": "/subscriptions/<yourSubscriptionId>",
  "type": "Microsoft.Authorization/roleAssignments"
} 
```

### <a name="confirm-that-role-assignment-happened"></a>Controleren of de roltoewijzing is gebeurd

Voer de volgende opdracht uit om te bevestigen dat de service-principal nu is gekoppeld aan de aangepaste rol die we zojuist hebben gemaakt:

```
az role assignment list  --assignee < objectId>
```

Het resultaat moet er als volgende uitzien:

```
[
  {
    "canDelegate": null,
    "id": "/subscriptions/xxx/providers/Microsoft.Authorization/roleAssignments/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "principalId": "<yourServicePrincipalID>",
    "principalName": "<yourServicePrincipalName>",
    "principalType": "ServicePrincipal",
    "roleDefinitionId": "/subscriptions/xxx/providers/Microsoft.Authorization/roleDefinitions/zzz",
    "roleDefinitionName": "LVAEdge User",
    "scope": "/subscriptions/<yourSubscription ID>",
    "type": "Microsoft.Authorization/roleAssignments
  }
]  
```
 
Zoek naar de roleDefinitionName en zie dat de waarde ervan is ingesteld op 'LVAEdge User'. 

Hiermee wordt bevestigd dat de aangepaste gebruikersrol is gekoppeld aan de service-principal die wordt gebruikt voor onze toepassing.

### <a name="test-the-service-principal-access-control"></a>Het toegangsbeheer voor de service-principal testen

1. Meld u aan met behulp van de service-principal. Hiervoor hebben we drie stukjes informatie nodig voor de Azure Active Directory om ons het juiste toegangsteken te verlenen dat we kunnen verkrijgen uit de uitvoer van de stap [Service-principal](#create-service-principal) maken:
    1. AadClientID 
    1. AadSecret
    1. AadTenantId
1. Nu gaan we proberen u aan te melden met behulp van de onderstaande opdrachtsjabloon:
    
    ```
    az login --service-principal --username "< AadClientID>" --password " <AadSecret>" --tenant "<AadTenantId>"
    ```
3.  Laten we nu eens kijken of de aanmelding is beperkt tot de service-principal met de rol 'LVAEdge User' door een resourcegroep te maken om er zeker van te zijn dat deze mislukt. Voer de volgende opdracht in Cloud Shell uit:

    ```
    az group create --location "central us" --name "testresourcegroup"
    ```

    Deze opdracht mislukt en ziet er als volgende uit:
    
    ```
    The client '<AadClientId>' with object id '<AadClientId>' does not have authorization to perform action 'Microsoft.Resources/subscriptions/resourcegroups/write' over scope '/subscriptions/<yourSubscriptionId>/resourcegroups/testresourcegroup' or the scope is invalid. If access was recently granted, please refresh your credentials.
    ```

## <a name="next-steps"></a>Volgende stappen  

Noteer de volgende waarden uit dit artikel. Deze waarden zijn vereist voor het configureren van de dubbeleigenschappen van de Live Video Analytics op IoT Edge module. Zie [JSON-schema van moduletwee.](module-twin-configuration-schema.md)

| Variabele uit dit artikel|Eigenschapsnaam van tweeling voor Live Video Analytics op IoT Edge|
|---|---|
|AadSecret |    aadServicePrincipalPassword|
|AadTenantId |  aadTenantId|
|AadClientId    |aadServicePrincipalAppId|
