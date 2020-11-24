---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 26c158145de6ce729d8a7060152b19fb14b63d58
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95556557"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Authenticatie van Azure Resource Manager aanvragen voorbereiden
U moet alle bewerkingen die u uitvoert op resources verifiëren met behulp van de [Azure Resource Manager][lnk-authenticate-arm] met Azure Active Directory (AD). De eenvoudigste manier om dit te configureren is met Power shell of Azure CLI.

Installeer de [Azure PowerShell-cmdlets][lnk-powershell-install] voordat u doorgaat.

De volgende stappen laten zien hoe u wachtwoord verificatie instelt voor een AD-toepassing met behulp van Power shell. U kunt deze opdrachten uitvoeren in een standaard Power shell-sessie.

1. Meld u aan bij uw Azure-abonnement met behulp van de volgende opdracht:

    ```powershell
    Connect-AzAccount
    ```

1. Als u meerdere Azure-abonnementen hebt en u zich aanmeldt bij Azure, hebt u toegang tot alle Azure-abonnementen die zijn gekoppeld aan uw referenties. Gebruik de volgende opdracht om de Azure-abonnementen weer te geven die beschikbaar zijn voor gebruik:

    ```powershell
    Get-AzSubscription
    ```

    Gebruik de volgende opdracht om het abonnement te selecteren dat u wilt gebruiken voor het uitvoeren van de opdrachten voor het beheren van uw IoT-hub. U kunt de naam van het abonnement of de id van de uitvoer van de vorige opdracht gebruiken:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Noteer uw **TenantId** en **SubscriptionId**. U hebt deze later nog nodig.
3. Maak een nieuwe Azure Active Directory-toepassing met behulp van de volgende opdracht, waarbij de locatie houders worden vervangen:
   
   * **{Weergave naam}:** een weergave naam voor uw toepassing, zoals **MySampleApp**
   * **{URL van start pagina}:** de URL van de start pagina van uw app, zoals **http: \/ /mysampleapp/Home**. Deze URL hoeft niet naar een echte toepassing te verwijzen.
   * **{Toepassings-id}:** Een unieke id, zoals **http: \/ /mysampleapp**. Deze URL hoeft niet naar een echte toepassing te verwijzen.
   * **{Wacht woord}:** Een wacht woord dat u gebruikt voor verificatie bij uw app.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. Noteer de **ApplicationId** van de toepassing die u hebt gemaakt. U hebt dit later nodig.
5. Maak een nieuwe service-principal met behulp van de volgende opdracht, waarbij u **{MyApplicationId}** vervangt door de **ApplicationId** van de vorige stap:
   
    ```powershell
    New-AzADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Stel een roltoewijzing in met behulp van de volgende opdracht, waarbij **{MyApplicationId}** wordt vervangen door uw **ApplicationId**.
   
    ```powershell
    New-AzRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

U hebt nu de Azure AD-toepassing gemaakt waarmee u kunt verifiëren vanuit uw aangepaste C#-toepassing. Verderop in deze zelf studie hebt u de volgende waarden nodig:

* TenantId
* SubscriptionId
* ApplicationID
* Wachtwoord

[lnk-authenticate-arm]: /rest/api/
[lnk-powershell-install]: /powershell/azure/install-az-ps