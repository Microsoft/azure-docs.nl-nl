---
title: MSAL.js gebruiken met Azure AD B2C
titleSuffix: Microsoft identity platform
description: Met de micro soft-verificatie bibliotheek voor Java script (MSAL.js) kunnen toepassingen met Azure AD B2C werken en tokens verkrijgen om beveiligde web-Api's aan te roepen. Deze web-Api's kunnen worden Microsoft Graph, andere Api's van micro soft, Web-Api's van anderen of uw eigen web-API.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/05/2020
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev devx-track-js
ms.openlocfilehash: 53294a30b38bed7ab7516443277cac24e4fef4c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98063719"
---
# <a name="use-the-microsoft-authentication-library-for-javascript-to-work-with-azure-ad-b2c"></a>Gebruik de micro soft-verificatie bibliotheek voor Java script om met Azure AD B2C te werken

Met de [micro soft-verificatie bibliotheek voor Java script (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) kunnen java script-ontwikkel aars gebruikers verifiëren met sociale en lokale identiteiten met behulp van [Azure Active Directory B2C](../../active-directory-b2c/overview.md) (Azure AD B2C).

Door Azure AD B2C als een service voor identiteits beheer te gebruiken, kunt u aanpassen en bepalen hoe uw klanten zich registreren, aanmelden en hun profielen beheren wanneer ze uw toepassingen gebruiken. Met Azure AD B2C kunt u ook de gebruikers interface merken en aanpassen die door uw toepassing worden weer gegeven tijdens het verificatie proces.

In de volgende secties ziet u hoe u:

- Een Node.js Web-API beveiligen
- Ondersteuning voor aanmelden in een toepassing met één pagina (SPA) en *de* beveiligde web-API aanroepen
- Ondersteuning voor het opnieuw instellen van wacht woorden inschakelen

## <a name="prerequisites"></a>Vereisten

Als u dit nog niet hebt gedaan, maakt u een [Azure AD B2C-Tenant](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="nodejs-web-api"></a>Node.js-web-API

De volgende stappen laten zien hoe een **Web-API** Azure AD B2C kan gebruiken om zichzelf te beschermen en geselecteerde bereiken beschikbaar te maken voor een client toepassing.

MSAL.js voor het knoop punt is momenteel in ontwikkeling. Zie het [schema](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap) op github voor meer informatie. We raden u aan gebruik te maken van [Pass Port-Azure-AD](https://github.com/AzureAD/passport-azure-ad), een verificatie bibliotheek voor Node.js ontwikkeld en ondersteund door micro soft.

### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren

Als u uw web-API met Azure AD B2C wilt beveiligen, moet u deze eerst registreren. Zie [uw toepassing registreren](../../active-directory-b2c/add-web-api-application.md) voor gedetailleerde stappen.

### <a name="step-2-download-the-sample-application"></a>Stap 2: de voorbeeld toepassing downloaden

Down load het voor beeld als een zip-bestand of kloon het uit GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>Stap 3: verificatie configureren

1. Open de *config.js* in het bestand in het voor beeld.

2. Configureer het voor beeld met de referenties van de toepassing die u eerder hebt verkregen tijdens het registreren van uw toepassing. Wijzig de volgende regels code door de waarden te vervangen door de naam van uw Tenant, de client-ID en de naam van het beleid.

    ```json
         "credentials": {
             "tenantName": "<your-tenant-name>",
             "clientID": "<your-webapi-application-ID>"
         },
         "policies": {
             "policyName": "B2C_1_signupsignin1"
         },
         "resource": {
             "scope": ["demo.read"] 
         },
    ```

Bekijk dit [Node.js B2C Web API](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)-voor beeld voor meer informatie.

## <a name="javascript-spa"></a>Java script SPA

De volgende stappen laten zien hoe een **toepassing met één pagina** kan Azure AD B2C gebruiken om zich aan te melden, zich aan te melden en een beveiligde web-API aan te roepen.

### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren

Als u verificatie wilt implementeren, moet u de toepassing eerst registreren. Zie [uw toepassing registreren](../../active-directory-b2c/tutorial-register-applications.md) voor gedetailleerde stappen.

### <a name="step-2-download-the-sample-application"></a>Stap 2: de voorbeeld toepassing downloaden

Down load het code voorbeeld [. ZIP-archief](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) of kloon de GitHub-opslag plaats:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Stap 3: verificatie configureren

Er zijn twee belang rijke punten bij het configureren van uw toepassing:

- API-eind punt en weer gegeven bereiken configureren
- Verificatie parameters en Token scopes configureren

1. Open het *apiConfig.js* -bestand in het voor beeld.

2. Configureer het voor beeld met de para meters die u eerder hebt verkregen tijdens het registreren van uw web-API. Wijzig de volgende regels code door de waarden te vervangen door het adres van uw web-API en weer gegeven bereiken.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

1. Open het *authConfig.js* -bestand in het voor beeld.

1. Configureer het voor beeld met de para meters die u eerder hebt verkregen tijdens het registreren van uw toepassing met één pagina. Wijzig de volgende regels code door de waarden te vervangen door uw ClientId-, Authority-meta gegevens en Token aanvraag scopes.

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

Lees voor meer informatie dit [Java script B2C-voorbeeld toepassing voor één pagina](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

## <a name="support-password-reset"></a>Ondersteuning voor wacht woord opnieuw instellen

In deze sectie breidt u uw toepassing met één pagina uit voor het gebruik van de Azure AD B2C wacht woord opnieuw instellen gebruikers stroom. Hoewel MSAL.js momenteel niet meerdere gebruikers stromen of aangepaste beleids regels ondersteunt, kunt u de bibliotheek gebruiken voor het afhandelen van veelvoorkomende use-cases, zoals het opnieuw instellen van wacht woorden.

Bij de volgende stappen wordt ervan uitgegaan dat u de stappen in de voor gaande [Java script-Spa](#javascript-spa) -sectie al hebt gevolgd.

### <a name="step-1-define-the-authority-string-for-password-reset-user-flow"></a>Stap 1: de instantie teken reeks definiëren voor de gebruikers stroom voor het opnieuw instellen van wacht woorden

1. Maak eerst een object waar u de CA-Uri's opslaat:

    ```javascript
        const b2cPolicies = {
            names: {
                signUpSignIn: "b2c_1_susi",
                forgotPassword: "b2c_1_reset"
            },
            authorities: {
                signUpSignIn: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
                },
                forgotPassword: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
                },
            },
        }
    ```

1. Initialiseer vervolgens uw MSAL-object met het `signInSignUp` beleid als standaard (Zie het voor gaande code fragment). Wanneer een gebruiker zich probeert aan te melden, wordt het volgende scherm weer gegeven:

    :::image type="content" source="media/msal-b2c-overview/user-journey-01-signin.png" alt-text="Aanmeldings scherm weer gegeven door Azure AD B2C":::

### <a name="step-2-catch-and-handle-authentication-errors-in-your-login-method"></a>Stap 2: authenticatie fouten in uw aanmeldings methode ondervangen en verwerken

Wanneer een gebruiker een **verg eten wacht woord** selecteert, genereert uw toepassing een fout die u moet opvangen in uw code en gaat u vervolgens door met de juiste gebruikers stroom. In dit geval wordt de `b2c_1_reset` stroom voor het opnieuw instellen van het wacht woord.

1. Breid uw aanmeldings methode als volgt uit:

    ```javascript
    function signIn() {
      myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log("id_token acquired at: " + new Date().toString());

            if (myMSALObj.getAccount()) {
              updateUI();
            }

        }).catch(function (error) {
          console.log(error);

          // error handling
          if (error.errorMessage) {
            // check for forgot password error
            if (error.errorMessage.indexOf("AADB2C90118") > -1) {

              //call login method again with the password reset user flow
              myMSALObj.loginPopup(b2cPolicies.authorities.forgotPassword)
                .then(loginResponse => {
                  console.log(loginResponse);
                  window.alert("Password has been reset successfully. \nPlease sign-in with your new password.");
                })
            }
          }
        });
    }
    ```

1. In het voor gaande code fragment ziet u hoe u het scherm wacht woord opnieuw instellen kunt weer geven nadat de fout is opgetreden met de code `AADB2C90118` .

    Nadat het wacht woord opnieuw is ingesteld, keert de gebruiker terug naar de toepassing om u opnieuw aan te melden.

    :::image type="content" source="media/msal-b2c-overview/user-journey-02-password-reset.png" alt-text="Scherm voor wachtwoord herstel wordt weer gegeven door Azure AD B2C" border="false":::

    Zie [MSAL-fout-en uitzonderings codes](msal-error-handling-js.md)voor meer informatie over fout codes en het verwerken van uitzonde ringen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over deze Azure AD B2C-concepten:

- [Gebruikersstromen](../../active-directory-b2c/tutorial-create-user-flows.md)
- [Aangepast beleid](../../active-directory-b2c/custom-policy-get-started.md)
- [UX-aanpassing](../../active-directory-b2c/configure-user-input.md)
