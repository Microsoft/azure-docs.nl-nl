---
title: Een OpenID Connect Connect provider (preview-versie) configureren
description: Meer informatie over het configureren van een OpenID Connect Connect-provider als een id-provider voor uw App Service-of Azure Functions-app.
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.openlocfilehash: e8112f2dc20175e81cfa8388440b2d9aef6a419c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90983880"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-login-using-an-openid-connect-provider-preview"></a>Uw App Service- of Azure Functions-app configureren om u aan te melden met een OpenID Connect-provider (preview)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In dit artikel wordt beschreven hoe u Azure App Service of Azure Functions kunt configureren voor het gebruik van een aangepaste verificatie provider die voldoet aan de [OpenID Connect Connect-specificatie](https://openid.net/connect/). OpenID Connect Connect (OIDC) is een industrie norm die wordt gebruikt door veel id-providers (id). U hoeft geen inzicht te krijgen in de details van de specificatie om uw app te configureren voor het gebruik van een adherent-IDP.

U kunt uw app configureren voor het gebruik van een of meer OIDC-providers. Elk moet een unieke naam hebben in de configuratie en slechts één kan fungeren als het standaard doel voor omleiden.

> [!CAUTION]
> Als u een OpenID Connect-verbindings provider inschakelt, wordt het beheer van de functie voor App Service verificatie/autorisatie voor uw toepassing via sommige clients, zoals de Azure Portal, Azure CLI en Azure PowerShell, uitgeschakeld. De functie is afhankelijk van een nieuw API-Opper vlak dat tijdens de preview-periode nog niet is verwerkt in alle beheer ervaringen.

## <a name="register-your-application-with-the-identity-provider"></a><a name="register"> </a>Uw toepassing registreren bij de ID-provider

Uw provider vereist dat u de details van uw toepassing registreert. Raadpleeg de instructies die relevant zijn voor deze provider. U moet een **client-id** en **client geheim** voor uw toepassing verzamelen.

> [!IMPORTANT]
> Het app-geheim is een belang rijke beveiligings referentie. Deel dit geheim niet met iemand of distribueer het in een client toepassing.
>

> [!NOTE]
> Voor sommige providers zijn mogelijk extra stappen vereist voor de configuratie en het gebruik van de waarden die ze bieden. Apple biedt bijvoorbeeld een persoonlijke sleutel die niet zelf wordt gebruikt als het OIDC-client geheim, en u moet in plaats daarvan een JWT gebruiken die wordt behandeld als het geheim dat u in uw app-configuratie hebt verstrekt (Zie de sectie ' het client geheim maken ' van de [documentatie bij Apple Signing with in](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)).
>

Voeg het client geheim toe als een [toepassings instelling](./configure-common.md#configure-app-settings) voor de app, waarbij u de naam van uw keuze kunt opgeven. Noteer deze naam voor later.

Daarnaast hebt u de OpenID Connect Connect-meta gegevens voor de provider nodig. Dit wordt vaak weer gegeven via een [meta gegevens document voor configuratie](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig), dat wil zeggen, de URL van de uitgever van de provider met `/.well-known/openid-configuration` . Verzamel deze configuratie-URL.

Als u geen meta gegevens document voor configuratie kunt gebruiken, moet u de volgende waarden afzonderlijk verzamelen:

- De URL van de uitgever (soms weer gegeven als `issuer` )
- Het [OAuth 2,0-autorisatie-eind punt](https://tools.ietf.org/html/rfc6749#section-3.1) (soms weer gegeven als `authorization_endpoint` )
- Het [OAuth 2,0-token-eind punt](https://tools.ietf.org/html/rfc6749#section-3.2) (soms weer gegeven als `token_endpoint` )
- De URL van het [OAuth 2,0 JSON Web key set](https://tools.ietf.org/html/rfc8414#section-2) -document (soms weer gegeven als `jwks_uri` )

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Provider gegevens toevoegen aan uw toepassing

> [!NOTE]
> De vereiste configuratie heeft een nieuwe API-indeling, die momenteel alleen wordt ondersteund door op [bestanden gebaseerde configuratie (preview)](.\app-service-authentication-how-to.md#config-file). U moet de onderstaande stappen volgen met behulp van een dergelijk bestand.

In deze sectie wordt uitgelegd hoe u de configuratie bijwerkt om uw nieuwe IDP op te nemen. Hier volgt een voorbeeld configuratie.

1. Voeg binnen het- `identityProviders` object een `openIdConnectProviders` object toe als er nog geen bestaat.
1. Voeg binnen het- `openIdConnectProviders` object een sleutel toe voor uw nieuwe provider. Dit is een beschrijvende naam die wordt gebruikt om te verwijzen naar de provider in de rest van de configuratie. Als u bijvoorbeeld wilt vereisen dat alle aanvragen worden geverifieerd met deze provider, stelt u `globalValidation.unauthenticatedClientAction` in op ' RedirectToLoginPage ' en stelt u `redirectToProvider` deze dezelfde beschrijvende naam in.
1. Wijs een object toe aan die sleutel met een `registration` object binnen het, en optioneel een `login` object:
    
    ```json
    "myCustomIDP" : {
       "registration" : {},
       "login": {
             "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
             "scope": [],
             "loginParameterNames": [],
       }
    }
    ```

1. Stel binnen het registratie object de `clientId` in op de client-id die u hebt verzameld, stel `clientCredential.secretSettingName` in op de naam van de toepassings instelling waar u het client geheim hebt opgeslagen en maak een `openIdConnectConfiguration` object:

    ```json
    "registration": {
      "clientId": "bd96cf8a-3f2b-4806-b180-d3c5fd11a2be",
      "clientCredential": {
         "secretSettingName": "IDP_CLIENT_SECRET"
      },
      "openIdConnectConfiguration" : {}
    }
    ```

1. Geef binnen het `openIdConnectConfiguration` object de OpenID Connect Connect-meta gegevens op die u eerder hebt verzameld. Er zijn twee opties voor dit, op basis van de gegevens die u hebt verzameld:

    - Stel de `wellKnownOpenIdConfiguration` eigenschap in op de URL van de configuratie-meta gegevens die u eerder hebt verzameld.
    - U kunt ook de vier afzonderlijke waarden instellen die als volgt worden verzameld:
        - Ingesteld `issuer` op de URL van de uitgever
        - Instellen `authorizationEndpoint` op het autorisatie-eind punt
        - Instellen `tokenEndpoint` op het token eindpunt
        - Stel `certificationUri` in op de URL van het JSON-Websleutelset-document

    Deze twee opties sluiten elkaar wederzijds uit.

Zodra deze configuratie is ingesteld, kunt u uw OpenID Connect Connect-provider gebruiken voor verificatie in uw app.

Een voorbeeld configuratie kan er als volgt uitzien (met aanmelden bij Apple als voor beeld waarbij de instelling van de APPLE_GENERATED_CLIENT_SECRET naar een gegenereerde JWT verwijst volgens de [Apple-documentatie](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)):

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "openIdConnectProviders": {
            "apple": {
                "registration": {
                    "clientId": "com.contoso.example.client",
                    "clientCredential": {
                        "secretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
                    },
                    "openIdConnectConfiguration": {
                        "wellKnownOpenIdConfiguration": "https://appleid.apple.com/.well-known/openid-configuration"
                    }
                },
                "login": {
                    "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
                    "scope": [],
                    "loginParameterNames": []
                }
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>Volgende stappen

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
