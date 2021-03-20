---
title: Azure Active Directory gebruiken om oplossingen voor Batch Management te verifiëren
description: Verken het gebruik van Azure Active Directory om te verifiëren vanuit toepassingen die gebruikmaken van de Batch Management .NET-bibliotheek.
ms.topic: how-to
ms.date: 04/27/2017
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: ff49d5e88df7c56ed4dee0685f09e45eb372aa5c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88930210"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Oplossingen voor Batch Management verifiëren met Active Directory

Toepassingen die de verificatie van de Azure Batch-beheer service aanroepen met [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD). Azure AD is de multi tenant-Cloud service van micro soft voor het beheren van Directory-en identiteits beheer. Azure zelf gebruikt Azure AD voor de verificatie van zijn klanten, service beheerders en gebruikers van de organisatie.

In de Batch Management .NET-bibliotheek worden typen weer gegeven voor het werken met batch-accounts, account sleutels, toepassingen en toepassings pakketten. De Batch Management .NET-bibliotheek is een Azure resource provider-client en wordt samen met [Azure Resource Manager](../azure-resource-manager/management/overview.md) gebruikt voor het programmatisch beheren van deze resources. Azure AD is vereist voor de verificatie van aanvragen die worden gedaan via elke client van een Azure-resource provider, met inbegrip van de Batch Management .NET-bibliotheek en via Azure Resource Manager.

In dit artikel verkennen we het gebruik van Azure AD voor het verifiëren van toepassingen die gebruikmaken van de Batch Management .NET-bibliotheek. We laten zien hoe u met behulp van geïntegreerde verificatie een abonnements beheerder of mede beheerder kunt verifiëren met Azure AD. We maken gebruik van het [AccountManagement](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement) -voorbeeld project, beschikbaar op github, om Azure ad te laten door lopen met de Batch Management .net-bibliotheek.

Zie voor meer informatie over het gebruik van de Batch Management .NET-bibliotheek en het AccountManagement- [voor beeld batch-accounts en Quota's beheren met de Batch Management-client bibliotheek voor .net](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Uw toepassing registreren bij Azure AD

De [Azure Active Directory Authentication Library](../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) biedt een programmatische interface voor Azure AD voor gebruik in uw toepassingen. Als u ADAL vanuit uw toepassing wilt aanroepen, moet u uw toepassing registreren in een Azure AD-Tenant. Wanneer u uw toepassing registreert, geeft u Azure AD op met informatie over uw toepassing, met inbegrip van een naam voor de app in de Azure AD-Tenant. Azure AD biedt vervolgens een toepassings-ID die u gebruikt om uw toepassing te koppelen aan Azure AD tijdens runtime. Zie voor meer informatie over de toepassings-ID [toepassings-en Service-Principal-objecten in azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Als u de voorbeeld toepassing AccountManagement wilt registreren, volgt u de stappen in de sectie [een toepassing toevoegen](../active-directory/develop/quickstart-register-app.md) in [toepassingen integreren met Azure Active Directory](../active-directory/develop/quickstart-register-app.md). Geef een **systeem eigen client toepassing** op voor het type toepassing. De standaard OAuth 2,0 URI voor de **omleidings-URI** is `urn:ietf:wg:oauth:2.0:oob` . U kunt echter een wille keurige geldige URI opgeven `http://myaccountmanagementsample` voor de **omleidings-URI**, omdat deze geen echt eind punt hoeft te zijn.

![Een toepassing toevoegen](./media/batch-aad-auth-management/app-registration-management-plane.png)

Zodra u het registratie proces hebt voltooid, ziet u de toepassings-ID en de object-ID (Service-Principal) die voor uw toepassing wordt vermeld.

![Registratie proces is voltooid](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>De Azure Resource Manager API-toegang verlenen tot uw toepassing

Vervolgens moet u de toegang tot uw toepassing delegeren naar de Azure Resource Manager-API. De Azure AD-id voor de Resource Manager-API is **Windows Azure Service Management-API**.

Volg deze stappen in Azure Portal:

1. Kies in het navigatie deel venster aan de linkerkant van de Azure Portal **alle services**, klik op **app-registraties** en klik op **toevoegen**.
2. Zoek de naam van uw toepassing in de lijst met app-registraties:

    ![Zoek naar de naam van uw toepassing](./media/batch-aad-auth-management/search-app-registration.png)

3. De Blade **instellingen** weer geven. Selecteer in de sectie **API-toegang** de optie **vereiste machtigingen**.
4. Klik op **toevoegen** om een nieuwe vereiste machtiging toe te voegen.
5. Voer in stap 1 **Windows Azure Service Management-API** in, selecteer de API in de lijst met resultaten en klik op de knop **selecteren** .
6. Schakel in stap 2 het selectie vakje naast **toegang tot het klassieke Azure-implementatie model in als organisatie gebruikers** en klik op de knop **selecteren** .
7. Klik op de knop **gereed** .

Op de Blade **vereiste machtigingen** ziet u nu dat machtigingen voor uw toepassing worden verleend aan zowel de ADAL-als de Resource Manager-api's. Er worden standaard machtigingen verleend aan ADAL wanneer u uw app voor het eerst registreert bij Azure AD.

![Machtigingen voor delegeren aan de Azure Resource Manager-API](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Azure AD-eind punten

Als u uw batch-beheer oplossingen wilt verifiëren met Azure AD, hebt u twee bekende eind punten nodig.

- Het **algemene Azure AD-eind punt** biedt een algemene interface voor het verzamelen van referenties wanneer er geen specifieke Tenant is opgegeven, zoals in het geval van geïntegreerde verificatie:

    `https://login.microsoftonline.com/common`

- Het **Azure Resource Manager-eind punt** wordt gebruikt om een token te verkrijgen voor het verifiëren van aanvragen voor de Batch Management-service:

    `https://management.core.windows.net/`

De voorbeeld toepassing AccountManagement definieert constanten voor deze eind punten. Laat deze constanten ongewijzigd:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Referentie voor uw toepassings-ID

Uw client toepassing maakt gebruik van de toepassings-ID (ook wel de client-ID genoemd) om toegang te krijgen tot Azure AD tijdens runtime. Wanneer u uw toepassing hebt geregistreerd in de Azure Portal, werkt u uw code bij om de toepassings-ID van Azure AD te gebruiken voor uw geregistreerde toepassing. Kopieer in de voorbeeld toepassing AccountManagement uw toepassings-ID van de Azure Portal naar de juiste constante:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Graph API. For information
// about registering an application in Azure Active Directory, please see "Register an application with the Microsoft identity platform" here:
// https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app
private const string ClientId = "<application-id>";
```
Kopieer ook de omleidings-URI die u tijdens het registratie proces hebt opgegeven. De omleidings-URI die is opgegeven in de code moet overeenkomen met de omleidings-URI die u hebt opgegeven tijdens het registreren van de toepassing.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Een Azure AD-verificatietoken verkrijgen

Nadat u het AccountManagement-voor beeld in de Azure AD-Tenant hebt geregistreerd en de voorbeeld broncode hebt bijgewerkt met uw waarden, is het voor beeld gereed voor verificatie met behulp van Azure AD. Wanneer u het voor beeld uitvoert, probeert de ADAL een verificatie token te verkrijgen. Tijdens deze stap wordt u gevraagd om uw micro soft-referenties:

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

Nadat u uw referenties hebt verstrekt, kan de voorbeeld toepassing geverifieerde aanvragen door geven aan de Batch Management-service.

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over het uitvoeren van de [AccountManagement-voorbeeld toepassing](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement) [batch-accounts en quota's beheren met de Batch Management-client bibliotheek voor .net](batch-management-dotnet.md).
- Zie de [Azure Active Directory-documentatie](../active-directory/index.yml)voor meer informatie over Azure AD.
- Gedetailleerde voor beelden waarin wordt getoond hoe u ADAL kunt gebruiken, zijn beschikbaar in de [Azure code samples](https://azure.microsoft.com/resources/samples/?service=active-directory) -bibliotheek.
- Zie [batch-service oplossingen verifiëren met Active Directory](batch-aad-auth.md)voor het verifiëren van batch service toepassingen met Azure AD.
