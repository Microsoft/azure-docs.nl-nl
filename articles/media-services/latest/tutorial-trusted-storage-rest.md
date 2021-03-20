---
title: Vertrouwde opslag Azure Media Services
description: In deze zelf studie leert u hoe u vertrouwde opslag kunt inschakelen voor Azure Media Services, beheerd-identiteiten kunt gebruiken voor vertrouwde opslag en Azure-Services toegang verleent tot een opslag account wanneer u een firewall of VPN gebruikt.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 2/8/2021
ms.openlocfilehash: 18cb4e3ada94822c2f4cb1ca7675310a37e44e84
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100590656"
---
# <a name="tutorial-media-services-trusted-storage"></a>Zelf studie: vertrouwde opslag Media Services

In deze zelfstudie leert u:

> [!div class="checklist"]
> - Vertrouwde opslag inschakelen voor Azure Media Services
> - Beheerde identiteiten gebruiken voor vertrouwde opslag
> - Azure-Services toegang geven tot een opslag account bij gebruik van netwerk toegangs beheer, zoals een firewall of VPN

Met de 2020-05-01-API kunt u vertrouwde opslag inschakelen door een beheerde identiteit te koppelen aan een Media Services-account.

>[!NOTE]
>Vertrouwde opslag is alleen beschikbaar in de API en is momenteel niet ingeschakeld in de Azure Portal.

Media Services kunt automatisch toegang krijgen tot uw opslag account met behulp van systeem verificatie. Media Services valideert of het Media Services-account en het opslag account zich in hetzelfde abonnement bevinden. Ook wordt gevalideerd of de gebruiker die de koppeling toevoegt, toegang heeft tot het opslag account met Azure Resource Manager RBAC.

Als u echter netwerk toegangs beheer wilt gebruiken om uw opslag account te beveiligen en vertrouwde opslag in te scha kelen, is [Managed Identities](concept-managed-identities.md) -verificatie vereist. Hiermee kan Media Services toegang krijgen tot het opslag account dat is geconfigureerd met een firewall of een VNet-beperking via vertrouwde opslag toegang.

## <a name="overview"></a>Overzicht

> [!IMPORTANT]
> Gebruik de 2020-05-01-API voor alle aanvragen om Media Services.

Dit zijn de algemene stappen voor het maken van vertrouwde opslag voor Media Services:

1. Een resourcegroep maken.
1. Een opslagaccount maken.
1. Poll het opslag account totdat het klaar is. Wanneer het opslag account klaar is, wordt de Service-Principal-ID door de aanvraag geretourneerd.
1. Zoek de ID op van de rol van de *BLOB voor gegevens opslag* .
1. Roep de autorisatie provider aan en voeg een roltoewijzing toe.
1. Werk het Media Services-account bij om te verifiëren bij het opslag account met behulp van beheerde identiteit.
1. Verwijder de resources als u deze niet wilt blijven gebruiken en hiervoor in rekening wordt gebracht.

<!-- Link to storage role contributor role access differences information -->

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig om aan de slag te gaan.  Als u nog geen Azure-abonnement hebt, [maakt u een gratis proefaccount](https://azure.microsoft.com/free/).

### <a name="get-your-tenant-id-and-subscription-id"></a>Uw Tenant-ID en abonnements-ID ophalen

Als u niet weet hoe u uw Tenant-ID en abonnements-ID kunt ophalen, raadpleegt u [uw Tenant-id zoeken](how-to-set-azure-tenant.md) en [uw Tenant-id zoeken](how-to-set-azure-tenant.md).

### <a name="create-a-service-principal-and-secret"></a>Een Service-Principal en een geheim maken

Als u niet weet hoe u een Service-Principal en een geheim maakt, raadpleegt u [referenties ophalen voor toegang tot Media Services-API](access-api-howto.md).

## <a name="use-a-rest-client"></a>Een REST-client gebruiken

Dit script is bedoeld voor gebruik met een REST-client, zoals wat beschikbaar is in Visual Studio code Extensions.  Past u deze aan voor uw ontwikkel omgeving.

## <a name="set-initial-variables"></a>Begin variabelen instellen

Dit deel van het script is voor gebruik in een REST-client. U kunt variabelen anders in uw ontwikkel omgeving gebruiken.

```rest
### AAD details
@tenantId = your tenant ID
@servicePrincipalId = the service principal ID
@servicePrincipalSecret = the service principal secret

### AAD resources
@armResource = https%3A%2F%2Fmanagement.core.windows.net%2F
@graphResource = https%3A%2F%2Fgraph.windows.net%2F
@storageResource = https%3A%2F%2Fstorage.azure.com%2F

### Service endpoints
@armEndpoint = management.azure.com
@graphEndpoint = graph.windows.net
@aadEndpoint = login.microsoftonline.com

### ARM details
@subscription = your subscription id
@resourceGroup = the resource group you'll be creating
@storageName = the name of the storage you'll be creating
@accountName = the name of the account you'll be creating
@resourceLocation = East US (or the location that works best for your region)
```

## <a name="get-a-token-for-azure-resource-manager"></a>Een Token ophalen voor Azure Resource Manager

```rest
// @name getArmToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{armResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-a-token-for-the-graph-api"></a>Een Token ophalen voor de Graph API

Dit deel van het script is voor gebruik in een REST-client. U kunt variabelen anders in uw ontwikkel omgeving gebruiken.

```rest
// @name getGraphToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{graphResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-the-service-principal-details"></a>De details van de Service-Principal ophalen

```rest
// @name getServicePrincipals
GET https://{{graphEndpoint}}/{{tenantId}}/servicePrincipals?$filter=appId%20eq%20'{{servicePrincipalId}}'&api-version=1.6
x-ms-client-request-id: cae3e4f7-17a0-476a-a05a-0dab934ba959
Authorization:  Bearer {{getGraphToken.response.body.access_token}}
```

## <a name="store-the-service-principal-id"></a>De Service-Principal-ID opslaan

```rest
@servicePrincipalObjectId = {{getServicePrincipals.response.body.value[0].objectId}}
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

```rest
// @name createResourceGroup
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}
    ?api-version=2016-09-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "location": "{{resourceLocation}}"
}
```

## <a name="create-storage-account"></a>Een opslagaccount maken

```rest
// @name createStorageAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
    }
}
```

## <a name="get-the-storage-account-status"></a>De status van het opslag account ophalen

Het opslag account neemt enige tijd in beslag, zodat de status van de aanvraag wordt gecontroleerd.  Herhaal deze aanvraag totdat het opslag account klaar is.

```rest
// @name getStorageAccountStatus
GET {{createStorageAccount.response.headers.Location}}
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-the-storage-account-details"></a>De details van het opslag account ophalen

Wanneer het opslag account klaar is, haalt u de eigenschappen van het opslag account op.

```rest
// @name getStorageAccount
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-a-token-for-arm"></a>Een token voor ARM ophalen

```rest
// @name getStorageToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{storageResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="create-a-media-services-account-with-a-system-assigned-managed-identity"></a>Een Media Services-account maken met een door het systeem toegewezen beheerde identiteit

```rest
// @name createMediaServicesAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="get-the-storage-storage-blob-data-role-definition"></a>De roldefinitie voor de BLOB-gegevens van de opslag opslag ophalen

```rest
// @name getStorageBlobDataContributorRoleDefinition
GET https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions?$filter=roleName%20eq%20%27Storage%20Blob%20Data%20Contributor%27&api-version=2015-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

### <a name="set-the-storage-role-assignment"></a>De toewijzing van de opslaglaag instellen

Met de roltoewijzing wordt aangegeven dat de service-principal voor het Media Services-account de rol Storage *BLOB-blobopslag* heeft.  Dit kan enige tijd duren en het is belang rijk om te wachten of het Media Services-account niet correct is ingesteld.

```rest
PUT https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleAssignments/{{$guid}}?api-version=2020-04-01-preview
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "roleDefinitionId": "/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions/{{getStorageBlobDataContributorRoleDefinition.response.body.value[0].name}}",
    "principalId": "{{createMediaServicesAccount.response.body.identity.principalId}}"
  }
}
```

## <a name="give-managed-identity-bypass-access-to-the-storage-account"></a>Toegang tot het opslag account via beheerde identiteit overs Laan

Met deze actie wordt de toegang van door het systeem beheerde identiteit gewijzigd in de beheerde identiteit. Op deze manier kan het opslag account toegang krijgen tot het opslag account via een firewall als Azure-Services toegang krijgen tot het opslag account, ongeacht de IP-toegangs regels (Acl's).

Wacht opnieuw totdat de rol is toegewezen in het opslag account, of het Media Services-account onjuist is ingesteld.

```rest
// @name setStorageAccountFirewall
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
      "minimumTlsVersion": "TLS1_2",
      "networkAcls": {
        "bypass": "AzureServices",
        "virtualNetworkRules": [],
        "ipRules": [],
        "defaultAction": "Deny"
      }
    }
}
```

## <a name="update-the-media-services-account-to-use-the-managed-identity"></a>Het Media Services-account bijwerken om de beheerde identiteit te gebruiken

Het kan een paar minuten duren voordat deze aanvraag opnieuw wordt door gegeven aan de toewijzing van de opslaglaag.

```rest
// @name updateMediaServicesAccountWithManagedStorageAuth
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "storageAuthentication": "ManagedIdentity",
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="test-access"></a>Toegang testen

Test de toegang door een Asset te maken in het opslag account.

```rest
// @name createAsset
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}/assets/testasset{{index}}withoutmi?api-version=2018-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
}
```

## <a name="delete-resources"></a>Resources verwijderen

Als u de resources die u hebt gemaakt niet wilt behouden en er nog steeds kosten in rekening worden gebracht, verwijdert u deze.

```rest
### Clean up the Storage account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="next-steps"></a>Volgende stappen

[Een Asset maken](how-to-create-asset.md)
