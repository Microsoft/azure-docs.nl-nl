---
title: Een vertrouwelijke client-app instantiëren (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over hoe u een vertrouwelijke client toepassing kunt instantiëren met configuratie opties met behulp van de micro soft Authentication Library voor .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: d477c419bb677a6b8f24a3aae26c403e47cc96cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99583939"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Een vertrouwelijke client toepassing instantiëren met configuratie opties met behulp van MSAL.NET

In dit artikel wordt beschreven hoe u een [vertrouwelijke client toepassing](msal-client-applications.md) kunt instantiëren met behulp van de micro soft Authentication Library voor .net (MSAL.net).  De toepassing wordt geïnstantieerd met configuratie opties die zijn gedefinieerd in een instellingen bestand.

Voordat u een toepassing initialiseert, moet u deze eerst [registreren](quickstart-register-app.md) , zodat uw app kan worden geïntegreerd met het micro soft Identity-platform. Na de registratie hebt u mogelijk de volgende informatie nodig (die kan worden gevonden in de Azure Portal):

- De client-ID (een teken reeks die een GUID vertegenwoordigt)
- De URL van de identiteits provider (de naam van het exemplaar) en de aanmeldings doel groep voor uw toepassing. Deze twee para meters worden gezamenlijk bekend als de-instantie.
- De Tenant-ID als u alleen een line-of-Business-toepassing schrijft voor uw organisatie (ook wel een toepassing met één Tenant genoemd).
- Het toepassings geheim (client Secret String) of certificaat (van het type X509Certificate2) als het een vertrouwelijke client-app is.
- Voor web-apps en soms voor open bare client-apps (met name wanneer uw app een Broker moet gebruiken), moet u ook de redirectUri instellen waar de ID-provider verbinding maakt met uw toepassing met de beveiligings tokens.

## <a name="configure-the-application-from-the-config-file"></a>De toepassing configureren vanuit het configuratie bestand
De naam van de eigenschappen van de opties in MSAL.NET overeenkomt met de naam van de eigenschappen van de `AzureADOptions` in ASP.net core, dus u hoeft geen lijm code te schrijven.

Een ASP.NET Core toepassings configuratie wordt beschreven in een *appsettings.jsin* het bestand:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

Vanaf MSAL.NET v3. x kunt u uw vertrouwelijke client toepassing configureren vanuit het configuratie bestand.

Declareer een object in de klasse waar u uw toepassing wilt configureren en instantiëren `ConfidentialClientApplicationOptions` .  Koppel de configuratie gelezen van de bron (inclusief de appconfig.jsin het bestand) aan het exemplaar van de toepassings opties, met behulp `IConfigurationRoot.Bind()` van de methode van de [Microsoft.Extensions.Configuratie. Binder NuGet-pakket](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder):

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Hiermee wordt de inhoud van de sectie ' AzureAD ' van de *appsettings.jsin* het bestand gekoppeld aan de bijbehorende eigenschappen van het `ConfidentialClientApplicationOptions` object.  Bouw vervolgens een `ConfidentialClientApplication` object:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Runtime configuratie toevoegen
In een vertrouwelijke client toepassing hebt u meestal een cache per gebruiker. Daarom moet u de cache ophalen die aan de gebruiker is gekoppeld en de opbouw functie voor toepassingen op de hoogte stellen die u wilt gebruiken. Op dezelfde manier kunt u een dynamisch berekende omleidings-URI hebben. In dit geval ziet de code er als volgt uit:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

