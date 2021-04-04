---
title: Verificatie strategieën voor Marketplace-meet service | Azure Marketplace
description: Verificatie strategieën voor meet service worden ondersteund in azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/21/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: b418a9cae6f6d58dbe82babcfe6fe1e1a5027d43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97657070"
---
# <a name="marketplace-metering-service-authentication-strategies"></a>Verificatie strategieën voor Marketplace-meet service

Marketplace meter service ondersteunt twee verificatie strategieën:

* [Beveiligings token van Azure AD](../../active-directory/develop/access-tokens.md)
* [Beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md) 

Er wordt uitgelegd wanneer en hoe u de verschillende verificatie strategieën gebruikt voor het veilig verzenden van aangepaste meters met behulp van Marketplace-meet service.

## <a name="using-the-azure-ad-security-token"></a>Het Azure AD-beveiligings token gebruiken

Toepasselijke aanbiedings typen zijn transactable SaaS en Azure Applications met een beheerd type toepassings plan.  

Verzend aangepaste meters met behulp van een vooraf gedefinieerde, vaste Azure AD-toepassings-ID om te verifiëren.

Voor SaaS-aanbiedingen is dit de enige beschik bare optie. Het is een verplichte stap voor het publiceren van een SaaS-aanbieding zoals beschreven in [een SaaS-toepassing registreren](./pc-saas-registration.md).

Voor Azure-toepassingen met een beheerd toepassings abonnement kunt u overwegen deze strategie in de volgende gevallen te gebruiken:

* U hebt al een mechanisme om te communiceren met uw back-end-services en u wilt deze methode uitbreiden om aangepaste meters van een centrale service te verzenden.
* U hebt complexe aangepaste meters Logic.  Voer deze logica uit op een centrale locatie in plaats van de resources van de beheerde toepassing.

Zodra u uw toepassing hebt geregistreerd, kunt u via een programma een Azure AD-beveiligings token aanvragen. De uitgever wordt verwacht dit token te gebruiken en een aanvraag te doen om deze op te lossen.

Zie [Azure Active Directory toegangs tokens](../../active-directory/develop/access-tokens.md)voor meer informatie over deze tokens.

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Een Token ophalen op basis van de Azure AD-app

#### <a name="http-method"></a>HTTP-methode

**Verzenden**

#### <a name="request-url"></a>*Aanvraag-URL*

**`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`**

#### <a name="uri-parameter"></a>*URI-para meter*

|  **Parameternaam** |  **Vereist**  |  **Beschrijving**          |
|  ------------------ |--------------- | ------------------------  |
|  `tenantId`         |   Waar         | De Tenant-ID van de geregistreerde Azure AD-toepassing.   |
| | | |

#### <a name="request-header"></a>*Aanvraag header*

|  **Headernaam**    |  **Vereist**  |  **Beschrijving**          |
|  ------------------ |--------------- | ------------------------  |
|  `Content-Type`     |   Waar         | Het inhouds type dat is gekoppeld aan de aanvraag. De standaardwaarde is `application/x-www-form-urlencoded`.  |
| | | |

#### <a name="request-body"></a>*Aanvraagbody*

|  **Eigenschapsnaam**  |  **Vereist**  |  **Beschrijving**          |
|  ------------------ |--------------- | ------------------------  |
|  `Grant_type`       |   Waar         | Toekennings type. Gebruik `client_credentials`. |
|  `Client_id`        |   Waar         | Client/App-ID die is gekoppeld aan de Azure AD-app.|
|  `client_secret`    |   Waar         | Geheim dat is gekoppeld aan de Azure AD-app.  |
|  `Resource`         |   Waar         | Doel resource waarvoor het token wordt aangevraagd. Gebruik `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`. |
| | | |

#### <a name="response"></a>*Response*

|  **Naam**    |  **Type**  |  **Beschrijving**          |
|  ------------------ |--------------- | ----------------------  |
|  `200 OK`     |   `TokenResponse`    | De aanvraag is voltooid.  |
| | | |

#### <a name="tokenresponse"></a>*TokenResponse*

Voorbeeld token voor antwoorden:

```JSON
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

## <a name="using-the-azure-managed-identities-token"></a>Het token Azure Managed Identities gebruiken

Toepasselijk aanbiedings type is Azure-toepassingen met een beheerd toepassings plan type.

Met deze methode kan de geïmplementeerde bronnen identiteit worden geverifieerd voor het verzenden van gebruiks gebeurtenissen voor aangepaste meters.  U kunt de code die gebruik uitmaakt, insluiten binnen de grenzen van uw implementatie.

>[!Note]
>Publisher moet ervoor zorgen dat de resources die gebruik maken, zijn vergrendeld, waardoor niet wordt geknoeid.

Uw beheerde toepassing kan een ander type resources bevatten, van Virtual Machines tot Azure Functions.  Zie [Managed Identities voor Azure resources](../../active-directory/managed-identities-azure-resources/overview.md#how-can-i-use-managed-identities-for-azure-resources)voor meer informatie over het verifiëren met behulp van beheerde identiteiten voor verschillende services.

Volg de onderstaande stappen om te verifiëren met behulp van een Windows-VM,

1. Zorg ervoor dat beheerde identiteit is geconfigureerd met een van de volgende methoden:
    * [Azure Portal gebruikers interface](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
    * [CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
    * [PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
    * [Azure Resource Manager sjabloon](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
    * [Rest](../../active-directory/managed-identities-azure-resources/qs-configure-rest-vm.md#system-assigned-managed-identity))
    * [Azure-SDK's](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

1. Een toegangs token voor de service toepassings-ID () voor Marketplace-licentie controle `20e940b3-4c77-4b0b-9a53-9e16a1b010a7` gebruiken met behulp van de systeem identiteit RDP van de VM, open de Power shell-console en voer de onderstaande opdracht uit

    ```powershell
    # curl is an alias to Web-Invoke PowerShell command
    # Get system identity access tokenn
    $MetadataUrl = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F"
    $Token = curl -H @{"Metadata" = "true"} $MetadataUrl | Select-Object -Expand Content | ConvertFrom-Json
    $Headers = @{}
    $Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
    ```

1. De beheerde App-ID ophalen uit de huidige ManagedBy-eigenschap van de resource groepen

    ```powershell
    # Get subscription and resource group
    $metadata = curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content | ConvertFrom-Json 

    # Make sure the system identity has at least reader permission on the resource group
    $managementUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "?api-version=2019-10-01"
    $resourceGroupInfo = curl -Headers $Headers $managementUrl | select -ExpandProperty Content | ConvertFrom-Json
    $managedappId = $resourceGroupInfo.managedBy 
    ```

1. Marketplace-meet service vereist het gebruik van het rapport op een `resourceID` en `resourceUsageId` als een beheerde toepassing.

    ```powershell
    # Get resourceUsageId from the managed app
    $managedAppUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "/providers/Microsoft.Solutions/applications/" + $managedappId + "\?api-version=2019-07-01"
    $ManagedApp = curl $managedAppUrl -H $Headers | Select-Object -Expand Content | ConvertFrom-Json
    # Use this resource ID to emit usage 
    $resourceUsageId = $ManagedApp.properties.billingDetails.resourceUsageId
    ```

1. Gebruik de [Marketplace meter Service-API](./marketplace-metering-service-apis.md) om het gebruik te verzenden.

## <a name="next-steps"></a>Volgende stappen

* [Een Azure-toepassingsaanbieding maken](../create-new-azure-apps-offer.md)
* [Een SaaS-aanbieding plannen](../plan-saas-offer.md)
