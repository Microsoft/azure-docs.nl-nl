---
title: Meer informatie over het Azure AD-schema en de aangepaste expressies
description: In dit artikel worden het Azure AD-schema beschreven, de kenmerken die worden verstrekt door de agent van de inrichting en aangepaste expressies.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 750bc2f4f535238dbb757afc8b615fa71063d74d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98613904"
---
# <a name="understand-the-azure-ad-schema"></a>Het Azure AD-schema begrijpen
Een object in Azure Active Directory (Azure AD), zoals elke directory, is een programmatische gegevens constructie op hoog niveau waarmee items worden aangeduid als gebruikers, groepen en contact personen. Wanneer u een nieuwe gebruiker of contact persoon in azure AD maakt, maakt u een nieuw exemplaar van dat object. Deze instanties kunnen worden gedifferentieerd op basis van hun eigenschappen.

Eigenschappen in azure AD zijn de elementen die verantwoordelijk zijn voor het opslaan van informatie over een exemplaar van een object in azure AD.

Het Azure AD-schema definieert de regels waarvoor eigenschappen kunnen worden gebruikt in een item, de soorten waarden die deze eigenschappen hebben en hoe gebruikers met deze waarden kunnen werken. 

Azure AD heeft twee soorten eigenschappen:
- **Ingebouwde eigenschappen**: eigenschappen die zijn gedefinieerd door het Azure AD-schema. Deze eigenschappen bieden verschillende toepassingen en zijn mogelijk wel of niet toegankelijk.
- **Directory-extensies**: eigenschappen die worden gegeven, zodat u Azure AD kunt aanpassen voor eigen gebruik. Als u bijvoorbeeld uw on-premises Active Directory hebt uitgebreid met een bepaald kenmerk en u dat kenmerk wilt debiet, kunt u een van de aangepaste eigenschappen gebruiken die worden gegeven. 

## <a name="attributes-and-expressions"></a>Kenmerken en expressies
Wanneer een object zoals een gebruiker is ingericht voor Azure AD, wordt er een nieuw exemplaar van het gebruikers object gemaakt. Deze maken bevat de eigenschappen van dat object, ook wel bekend als kenmerken. In eerste instantie heeft het zojuist gemaakte object de kenmerken ingesteld op waarden die worden bepaald door de synchronisatie regels. Deze kenmerken worden vervolgens up-to-date gehouden via de Cloud-inrichtings agent.

![Object inrichting](media/concept-attributes/attribute-1.png)

Een gebruiker kan bijvoorbeeld deel uitmaken van een marketing afdeling. Het kenmerk van de Azure AD-afdeling wordt in eerste instantie gemaakt wanneer ze worden ingericht en de waarde is ingesteld op marketing. Zes maanden later als ze worden gewijzigd in verkoop, wordt het on-premises Active Directory afdelings kenmerk gewijzigd in verkoop. Deze wijziging wordt gesynchroniseerd met Azure AD en wordt weer gegeven in hun Azure AD-gebruikers object.

Kenmerk synchronisatie kan direct worden ingeschakeld, waarbij de waarde in azure AD rechtstreeks wordt ingesteld op de waarde van het on-premises kenmerk. Een programmatische expressie kan de synchronisatie ook afhandelen. Er is een programmatische expressie nodig in gevallen waarin een bepaalde logica of een bepaling moet worden gemaakt om de waarde in te vullen.

Als u bijvoorbeeld het kenmerk mail had john.smith@contoso.com en nodig hebt om het gedeelte ' ' te verwijderen @contoso.com en alleen de waarde ' John. Smit ' te gebruiken, zou u er ongeveer als volgt moeten uitzien:

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Voor beeld van invoer/uitvoer:** <br>

* **Invoer** (e-mail adres): " john.smith@contoso.com "
* **Uitvoer**: "Jan. Smit"

Zie [expressies schrijven voor kenmerk toewijzingen in azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md)voor meer informatie over het schrijven van aangepaste expressies en de syntaxis.

De volgende tabel bevat algemene kenmerken en hoe deze worden gesynchroniseerd met Azure AD.


|On-premises Active Directory|Toewijzings type|Azure AD|
|-----|-----|-----|
|cn|Direct|commonName
|countryCode|Direct|countryCode|
|displayName|Direct|displayName|
|givenName|Expression|givenName|
|GUID|Direct|sourceAnchorBinary|  
|userprincipalName|Direct|userPrincipalName|
|ProxyAdress|Direct|ProxyAddress attribuut|

## <a name="view-the-schema"></a>Het schema weer geven
> [!WARNING]
> De Cloud synchronisatie configuratie maakt een service-principal. De Service-Principal is zichtbaar in de Azure Portal. Wijzig de kenmerk toewijzingen niet met behulp van de Service-Principal-ervaring in de Azure Portal.  Dit wordt niet ondersteund.

Volg deze stappen om het schema weer te geven en te controleren.

1.  Ga naar [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).
1.  Meld u aan met het account van uw globale beheerder.
1.  Selecteer aan de linkerkant de optie **machtigingen wijzigen** en zorg ervoor dat **map. readwrite. all** is *gezonden*.
1.  Voer de query uit `https://graph.microsoft.com/beta/serviceprincipals/?$filter=startswith(Displayname,'Active')` . Deze query retourneert een gefilterde lijst met Service-principals.
1.  Zoek `"appDisplayName": "Active Directory to Azure Active Directory Provisioning"` en noteer de waarde voor `"id"` .
    ```
    "value": [
            {
                "id": "00d41b14-7958-45ad-9d75-d52fa29e02a1",
                "deletedDateTime": null,
                "accountEnabled": true,
                "appDisplayName": "Active Directory to Azure Active Directory Provisioning",
                "appId": "1a4721b3-e57f-4451-ae87-ef078703ec94",
                "applicationTemplateId": null,
                "appOwnerOrganizationId": "47df5bb7-e6bc-4256-afb0-dd8c8e3c1ce8",
                "appRoleAssignmentRequired": false,
                "displayName": "Active Directory to Azure Active Directory Provisioning",
                "errorUrl": null,
                "homepage": "https://account.activedirectory.windowsazure.com:444/applications/default.aspx?metadata=AD2AADProvisioning|ISV9.1|primary|z",
                "loginUrl": null,
                "logoutUrl": null,
                "notificationEmailAddresses": [],
                "preferredSingleSignOnMode": null,
                "preferredTokenSigningKeyEndDateTime": null,
                "preferredTokenSigningKeyThumbprint": null,
                "publisherName": "Active Directory Application Registry",
                "replyUrls": [],
                "samlMetadataUrl": null,
                "samlSingleSignOnSettings": null,
                "servicePrincipalNames": [
                    "http://adapplicationregistry.onmicrosoft.com/adprovisioningtoaad/primary",
                    "1a4721b3-e57f-4451-ae87-ef078703ec94"
                ],
                "signInAudience": "AzureADMultipleOrgs",
                "tags": [
                    "WindowsAzureActiveDirectoryIntegratedApp"
                ],
                "addIns": [],
                "api": {
                    "resourceSpecificApplicationPermissions": []
                },
                "appRoles": [
                    {
                        "allowedMemberTypes": [
                            "User"
                        ],
                        "description": "msiam_access",
                        "displayName": "msiam_access",
                        "id": "a0326856-1f51-4311-8ae7-a034d168eedf",
                        "isEnabled": true,
                        "origin": "Application",
                        "value": null
                    }
                ],
                "info": {
                    "termsOfServiceUrl": null,
                    "supportUrl": null,
                    "privacyStatementUrl": null,
                    "marketingUrl": null,
                    "logoUrl": null
                },
                "keyCredentials": [],
                "publishedPermissionScopes": [
                    {
                        "adminConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on behalf of the signed-in user.",
                        "adminConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "id": "d40ed463-646c-4efe-bb3e-3fa7d0006688",
                        "isEnabled": true,
                        "type": "User",
                        "userConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on your behalf.",
                        "userConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "value": "user_impersonation"
                    }
                ],
                "passwordCredentials": []
            },
    ```
1. Vervang door `{Service Principal id}` uw waarde en voer de query uit `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/` .
1. Zoek `"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"` en noteer de waarde voor `"id"` .
    ```
    {
                "id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976",
                "templateId": "AD2AADProvisioning",
                "schedule": {
                    "expiration": null,
                    "interval": "PT2M",
                    "state": "Active"
                },
                "status": {
                    "countSuccessiveCompleteFailures": 0,
                    "escrowsPruned": false,
                    "code": "Active",
                    "lastSuccessfulExecutionWithExports": null,
                    "quarantine": null,
                    "steadyStateFirstAchievedTime": "2019-11-08T15:48:05.7360238Z",
                    "steadyStateLastAchievedTime": "2019-11-20T16:17:24.7957721Z",
                    "troubleshootingUrl": "",
                    "lastExecution": {
                        "activityIdentifier": "2dea06a7-2960-420d-931e-f6c807ebda24",
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 15,
                        "countEscrowedRaw": 15,
                        "countExported": 0,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "2019-11-20T16:15:21.116098Z",
                        "timeEnded": "2019-11-20T16:17:24.7488681Z"
                    },
                    "lastSuccessfulExecution": {
                        "activityIdentifier": null,
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 0,
                        "countEscrowedRaw": 0,
                        "countExported": 5,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "0001-01-01T00:00:00Z",
                        "timeEnded": "2019-11-20T14:09:46.8855027Z"
                    },
                    "progress": [],
                    "synchronizedEntryCountByType": [
                        {
                            "key": "group to Group",
                            "value": 33
                        },
                        {
                            "key": "user to User",
                            "value": 3
                        }
                    ]
                },
                "synchronizationJobSettings": [
                    {
                        "name": "Domain",
                        "value": "{\"DomainFQDN\":\"contoso.com\",\"DomainNetBios\":\"CONTOSO\",\"ForestFQDN\":\"contoso.com\",\"ForestNetBios\":\"CONTOSO\"}"
                    },
                    {
                        "name": "DomainFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "DomainNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "ForestFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "ForestNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "QuarantineTooManyDeletesThreshold",
                        "value": "500"
                    }
                ]
            }
    ```
1. Voer nu de query uit `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema` .
 
    Voorbeeld: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

   Vervang `{Service Principal Id}` en `{AD2ADD Provisioning Id}` door uw eigen waarden.

1. Deze query retourneert het schema.

   ![Geretourneerd schema](media/concept-attributes/schema-1.png)
 
## <a name="next-steps"></a>Volgende stappen

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect--cloudsynchronisatie?](what-is-cloud-sync.md)