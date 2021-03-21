---
title: Aangepaste Azure Resource Manager rol maken en toewijzen aan Service-Principal-Azure
description: Dit artikel bevat richt lijnen voor het maken van aangepaste Azure Resource Manager-rollen en het toewijzen van de service-principal voor live video Analytics op IoT Edge met behulp van Azure CLI.
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: 80974c111dd451314635d06334766322bc68e437
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210441"
---
# <a name="create-custom-azure-resource-manager-role-and-assign-to-service-principal"></a>Aangepaste Azure Resource Manager rol maken en toewijzen aan Service-Principal

Voor het goed functioneren van live video Analytics op IoT Edge module-exemplaar is een actief Azure Media Services account vereist. De relatie tussen de live video Analytics op IoT Edge module en het Azure media-service account wordt tot stand gebracht via een set module-dubbele eigenschappen. Een van deze dubbele eigenschappen is een [Service-Principal](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) waarmee het module-exemplaar kan communiceren met en de benodigde bewerkingen op het Media Services-account kan activeren. Deze service-principal moet de minste bevoegdheden hebben om mogelijke misbruik en/of accidentele gegevens bloot stelling van het edge-apparaat te minimaliseren.

In dit artikel worden de stappen beschreven voor het maken van een aangepaste Azure Resource Manager rol met Azure Cloud Shell, die vervolgens wordt gebruikt om een service-principal te maken.

## <a name="prerequisites"></a>Vereisten  

De vereisten voor dit artikel zijn als volgt:

* Azure-abonnement met eigenaars abonnement.
* Een Azure Active Directory met bevoegdheden voor het maken van een app en het toewijzen van een service-principal aan een rol.

De eenvoudigste manier om te controleren of uw account over de juiste machtigingen beschikt, verloopt via de portal. Zie [Check required permission](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) (Vereiste machtiging controleren).

## <a name="overview"></a>Overzicht  

De stappen voor het maken van een aangepaste rol en het koppelen hiervan aan een Service-Principal vindt u in de volgende volg orde:

1. Maak een media service-account, als u er nog geen hebt.
1. Een service-principal maken.
1. Maak een aangepaste Azure Resource Manager rol met beperkte bevoegdheden.
1. ' Beperken ' de bevoegdheden van de service-principal met behulp van de aangepaste rol die is gemaakt.
1. Voer een eenvoudige test uit om te controleren of de service-principal kan worden beperkt.
1. Vastleg de para meters die worden gebruikt in de IoT Edge implementatie manifesten.

### <a name="create-a-media-services-account"></a>Een Media Services-account kunt maken  

Als u geen media service-account hebt, gebruikt u de volgende stappen om er een te maken.

1. Blader naar de [Cloud shell](https://shell.azure.com/).
1. Selecteer ' bash ' als uw omgeving in de vervolg keuzelijst aan de linkerkant van het shell-venster.

    ![Scherm opnamen toont bash geselecteerd in het shell venster.](./media/create-custom-azure-resource-manager-role-how-to/bash.png)
1. Stel uw Azure-abonnement in als het standaard account met behulp van de volgende opdracht sjabloon:
    
    ```
    az account set --subscription " <yourSubscriptionName or yourSubscriptionId>"
    ```
1. Maak een [resource groep](/cli/azure/group#az-group-create) en een [opslag account](/cli/azure/storage/account#az-storage-account-create).
1. Maak nu een Azure media service-account met behulp van de volgende opdracht sjabloon in Cloud Shell:

    ```
    az ams account create --name <yourAMSAccountName>  --resource-group <yourResouceGroup>  --storage-account <yourStorageAccountName>
    ```

### <a name="create-service-principal"></a>Een service-principal maken  

We gaan nu een nieuwe service-principal maken en deze koppelen aan uw media service-account.

Zonder verificatie parameters wordt verificatie op basis van wacht woorden gebruikt met een wille keurig wacht woord voor uw service-principal. Gebruik in Cloud Shell de volgende opdracht sjabloon:

```
az ams account sp create --account-name < yourAMSAccountName > --resource-group < yourResouceGroup >
```

Met deze opdracht wordt een antwoord gegenereerd dat er als volgt uitziet:

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
1. De uitvoer van een service-principal met wachtwoord verificatie bevat de wachtwoord sleutel die in dit geval de para meter ' AadSecret ' is. 

    Zorg ervoor dat u deze waarde kopieert. deze kan niet worden opgehaald. Als u het wacht woord vergeet, moet u [de referenties van de Service-Principal opnieuw instellen](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials).
1. De appId en Tenant sleutel worden respectievelijk weer gegeven in de uitvoer als ' AadClientId ' en ' AadTenantId '. Ze worden gebruikt in Service-Principal-verificatie. Noteer hun waarden, maar ze kunnen op elk moment worden opgehaald met [AZ AD SP List](/cli/azure/ad/sp#az-ad-sp-list).

### <a name="create-a-custom-role-definition"></a>Een aangepaste roldefinitie maken  

Als u een aangepaste rol wilt maken, kunt u het volgende doen:

1. Maak een JSON-bestand met een roldefinitie op het lokale systeem en sla de volgende tekst op in het bestand. 
    1. < yourSubscriptionId> vervangen door de ID van uw Azure-abonnement
    1. De enige acties die zijn toegestaan voor deze rol zijn:
        * listContainerSas: helpt de module de opslag container-Url's te vermelden met Shared Access signatures (SAS) voor het uploaden en downloaden van de inhoud van de Asset.
        * Activa schrijven: helpt de module om activa te maken of bij te werken
        * listEdgePolicies: geeft een lijst van de beleids regels die worden toegepast op het edge-apparaat  
        
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
          
1. Nadat deze is gemaakt, voert u de volgende opdracht sjabloon uit om de definitie van de nieuwe functie in het abonnement te maken:
    
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

Als u een roltoewijzing wilt toevoegen, hebt u de objectId nodig van de service-principal die u zojuist hebt gemaakt.

Gebruik de volgende opdracht in Cloud Shell om de objectId op te halen:

```
az ad sp show --id "<appId>" | Select-String "objectId"
```

> [!NOTE]
> `<appId>` kan worden opgehaald uit de uitvoer van de stap [Service-Principal maken](#create-service-principal) .

Met de bovenstaande opdracht wordt de objectId van de Service-Principal afgedrukt. 

```
“objectId” : “<yourObjectId>”,
```

Gebruik [AZ Role Assignment opdracht sjabloon maken](/cli/azure/role/assignment#az-role-assignment-create) om de aangepaste rol te koppelen aan de Service-Principal:

```
az role assignment create --role “LVAEdge User” --assignee-object-id < objectId>    
```

Parameters:

|Parameters|Beschrijving| 
|---|---|
|--rol |De naam of ID van de aangepaste rol. In ons geval: "LVAEdge gebruiker".|
|--object-id van de gebruiker|De object-ID van de service-principal die u wilt gebruiken.|

Het resultaat ziet er als volgt uit:

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

### <a name="confirm-that-role-assignment-happened"></a>Bevestigen dat de roltoewijzing heeft plaatsgevonden

Als u wilt controleren of de Service-Principal nu is gekoppeld aan de aangepaste rol die we zojuist hebben gemaakt, voert u de volgende opdracht uit:

```
az role assignment list  --assignee < objectId>
```

Het resultaat moet er als volgt uitzien:

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
 
Zoek naar de "roleDefinitionName" en controleer of de waarde ervan is ingesteld op "LVAEdge User". 

Hiermee wordt bevestigd dat de aangepaste gebruikersrol is gekoppeld aan de service-principal die wordt gebruikt voor de toepassing.

### <a name="test-the-service-principal-access-control"></a>De Service-Principal Access Control testen

1. Meld u aan met behulp van de Service-Principal. Daarom hebben we 3 stukjes informatie nodig voor de Azure Active Directory om ons het juiste toegangs token te geven dat we kunnen verkrijgen van de uitvoer van de stap [Service-Principal maken](#create-service-principal) :
    1. AadClientID 
    1. AadSecret
    1. AadTenantId
1. U kunt nu proberen zich aan te melden met behulp van de onderstaande opdracht sjabloon:
    
    ```
    az login --service-principal --username "< AadClientID>" --password " <AadSecret>" --tenant "<AadTenantId>"
    ```
3.  Nu gaan we kijken of de aanmelding is beperkt tot de service-principal met de rol ' LVAEdge User ' door te proberen een resource groep te maken om ervoor te zorgen dat deze mislukt. Voer de volgende opdracht in Cloud Shell uit:

    ```
    az group create --location "central us" --name "testresourcegroup"
    ```

    Deze opdracht moet mislukken en zal er als volgt uitzien:
    
    ```
    The client '<AadClientId>' with object id '<AadClientId>' does not have authorization to perform action 'Microsoft.Resources/subscriptions/resourcegroups/write' over scope '/subscriptions/<yourSubscriptionId>/resourcegroups/testresourcegroup' or the scope is invalid. If access was recently granted, please refresh your credentials.
    ```

## <a name="next-steps"></a>Volgende stappen  

Let op de volgende waarden in dit artikel. Deze waarden zijn vereist voor het configureren van de dubbele eigenschappen van de live video Analytics op IoT Edge module, Zie [module dubbele JSON-schema](module-twin-configuration-schema.md).

| Variabele van dit artikel|Dubbele eigenschaps naam voor live video Analytics op IoT Edge|
|---|---|
|AadSecret |    aadServicePrincipalPassword|
|AadTenantId |  aadTenantId|
|AadClientId    |aadServicePrincipalAppId|
