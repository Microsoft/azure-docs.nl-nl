---
title: Aanmelden met Apple configureren (preview-versie)
description: Meer informatie over het configureren van aanmelden met Apple als een id-provider voor uw App Service of Azure Functions app.
ms.topic: article
ms.date: 11/19/2020
ms.reviewer: mikarmar
ms.openlocfilehash: b77e0613f502d003b5e4651e34be4cadbd4209a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96603226"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-sign-in-using-a-sign-in-with-apple-provider-preview"></a>Configureer uw App Service-of Azure Functions-app om u aan te melden met een aanmelding met Apple provider (preview-versie)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Dit artikel laat u zien hoe u Azure App Service of Azure Functions kunt configureren voor het gebruik van aanmelden met Apple als verificatie provider. 

Als u de procedure in dit artikel wilt uitvoeren, moet u zijn Inge schreven bij het Apple-ontwikkelaars programma. Als u wilt registreren in het Apple-ontwikkelaars programma, gaat u naar [Developer.Apple.com/programs/Enroll](https://developer.apple.com/programs/enroll/).

> [!CAUTION]
> Als u aanmelden met Apple inschakelt, wordt het beheer van de functie voor App Service verificatie/autorisatie voor uw toepassing via sommige clients, zoals de Azure Portal, Azure CLI en Azure PowerShell, uitgeschakeld. De functie is afhankelijk van een nieuw API-Opper vlak dat tijdens de preview-periode nog niet is verwerkt in alle beheer ervaringen.

[comment]: <Remove this caution block when V2 becomes available for use.> 

## <a name="create-an-application-in-the-apple-developer-portal"></a><a name="createApplication"> </a>Een toepassing maken in de Apple Developer-Portal
U moet een app-ID en een service-ID maken in de Apple Developer-portal.

1. Ga in de Apple Developer-Portal naar **certificaten, id's & profielen**.
2. Selecteer de knop **(+)** op het tabblad **id's** .
3. Kies op de pagina **een nieuwe id registreren** de optie **app-Id's** en selecteer **door gaan**. (App-Id's bevatten een of meer service-Id's.) ![Een nieuwe app-id registreren in de Apple Developer-Portal](media/configure-authentication-provider-apple/apple-register-app.jpg)
4. Op de pagina **een app-id registreren** geeft u een beschrijving en een bundel-id op en selecteert u **Aanmelden met Apple** vanuit de lijst met mogelijkheden. Selecteer vervolgens **Doorgaan**. Noteer uw **App-ID-voor voegsel (team-ID)** uit deze stap. u hebt dit later nodig.
![Een nieuwe app-id configureren in de Apple Developer-Portal](media/configure-authentication-provider-apple/apple-configure-app.jpg)
5. Controleer de registratie gegevens van de app en selecteer **registreren**.
6. Op het tabblad **id's** selecteert u de knop **(+)** .
![Een nieuwe service-id maken in de Apple Developer-Portal](media/configure-authentication-provider-apple/apple-new-app.jpg) 
7. Kies op de pagina **een nieuwe id registreren** de optie **Services-Id's** en selecteer **door gaan**.
![Een nieuwe service-ID registreren in de Apple Developer-Portal](media/configure-authentication-provider-apple/apple-register-service.jpg)
8. Geef op de pagina **een service-ID registreren** een beschrijving en een id op. De beschrijving wordt weer gegeven op het scherm toestemming van de gebruiker. De id is uw client-ID die wordt gebruikt voor het configureren van de Apple-provider met uw app service. Selecteer vervolgens **Configureren**.
![Een beschrijving en een id opgeven](media/configure-authentication-provider-apple/apple-configure-service-1.jpg)
9. Stel in het pop-upvenster de primaire app-id in op de app-id die u eerder hebt gemaakt. Geef het domein van uw toepassing op in de sectie domein. Gebruik de URL voor de retour-URL `<app-url>/.auth/login/apple/callback` . Bijvoorbeeld `https://contoso.azurewebsites.net/.auth/login/apple/callback`. Selecteer vervolgens **toevoegen** en **Opslaan**.
![Het domein en de retour-URL voor de registratie opgeven](media/configure-authentication-provider-apple/apple-configure-service-2.jpg)
10. Controleer de registratie gegevens van de service en selecteer **Opslaan**.

## <a name="generate-the-client-secret"></a><a name="generateClientSecret"> </a>Het client geheim genereren
Apple vereist dat app-ontwikkel aars een JWT-token kunnen maken en ondertekenen als de geheime waarde van de client. Als u dit geheim wilt genereren, moet u eerst een elliptische curve-persoonlijke sleutel genereren en downloaden vanuit de Apple Developer-portal. Vervolgens gebruikt u die sleutel om [een JWT](#sign-the-client-secret-jwt) met een [specifieke Payload](#structure-the-client-secret-jwt)te ondertekenen.

### <a name="create-and-download-the-private-key"></a>De persoonlijke sleutel maken en downloaden
1. Kies op het tabblad **sleutels** in de Apple Developer-Portal de optie **een sleutel maken** of de knop **(+)** selecteren.
2. Op de pagina **een nieuwe sleutel registreren** geeft u de sleutel een naam, schakelt u het selectie vakje naast **Aanmelden met Apple in** en selecteert **u configureren**.
3. Op de pagina **sleutel configureren** koppelt u de sleutel aan de primaire app-id die u eerder hebt gemaakt en selecteert u **Opslaan**.
4. Voltooi de sleutel door de gegevens te bevestigen en **door gaan** te selecteren en vervolgens de informatie te controleren en **registreren** te selecteren.
5. Down load de sleutel op de pagina **uw sleutel downloaden** . Het wordt gedownload als een `.p8` bestand (PKCS # 8). u gebruikt de bestands inhoud om uw client Secret JWT te ondertekenen.

### <a name="structure-the-client-secret-jwt"></a>De client Secret JWT structureren
Apple vereist dat het client geheim de base64-code ring van een JWT-token is. De gedecodeerde JWT-token moet een Payload hebben die is gestructureerd zoals in dit voor beeld:
```json
{
  "alg": "ES256",
  "kid": "URKEYID001",
}.{
  "sub": "com.yourcompany.app1",
  "nbf": 1560203207,
  "exp": 1560289607,
  "iss": "ABC123DEFG",
  "aud": "https://appleid.apple.com"
}.[Signature]
```
- **Sub**: de Apple-client-id (ook de Service-id)
- **ISS**: uw Apple Developer Team-ID
- **AUD**: Apple ontvangt het token, zodat ze de doel groep zijn
- **exp**: niet meer dan zes maanden na **NBF**

De met base64 gecodeerde versie van de bovenstaande nettolading ziet er als volgt uit: ```eyJhbGciOiJFUzI1NiIsImtpZCI6IlVSS0VZSUQwMDEifQ.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDIwMzIwNywiZXhwIjoxNTYwMjg5NjA3LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.ABSXELWuTbgqfrIUz7bLi6nXvkXAz5O8vt0jB2dSHTQTib1x1DSP4__4UrlKI-pdzNg1sgeocolPNTmDKazO8-BHAZCsdeeTNlgFEzBytIpMKFfVEQbEtGRkam5IeclUK7S9oOva4EK4jV4VmgDrr-LGWWO3TaAxAvy3_ZoKohvFFkVG```

_Opmerking: Apple accepteert geen client geheim JWTs met een verval datum van meer dan zes maanden na de aanmaak datum (of NBF). Dit betekent dat u uw client geheim, ten minste om de zes maanden, moet draaien._

Meer informatie over het genereren en valideren van tokens vindt u in [de Apple-documentatie voor ontwikkel aars](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens). 

### <a name="sign-the-client-secret-jwt"></a>De client Secret JWT ondertekenen
U gebruikt het `.p8` bestand dat u eerder hebt gedownload om de client Secret JWT te ondertekenen. Dit bestand is een [PCKS-bestand](https://en.wikipedia.org/wiki/PKCS_8) dat de persoonlijke ondertekeningssleutel bevat in de PEM-indeling. Er zijn veel bibliotheken die de JWT voor u kunnen maken en ondertekenen. 

Er zijn verschillende soorten open source-bibliotheken die online beschikbaar zijn voor het maken en ondertekenen van JWT-tokens. Zie jwt.io voor meer informatie over het genereren van JWT-tokens. Een voor beeld: een manier om het client geheim te genereren is door het importeren van het [NuGet-pakket micro soft. Identity model. tokens](https://www.nuget.org/packages/Microsoft.IdentityModel.Tokens/) en het uitvoeren van een kleine hoeveelheid C#-code die hieronder wordt weer gegeven.

```csharp
using Microsoft.IdentityModel.Tokens;

public static string GetAppleClientSecret(string teamId, string clientId, string keyId, string p8key)
{
    string audience = "https://appleid.apple.com";

    string issuer = teamId;
    string subject = clientId;
    string kid = keyId;

    IList<Claim> claims = new List<Claim> {
        new Claim ("sub", subject)
    };

    CngKey cngKey = CngKey.Import(Convert.FromBase64String(p8key), CngKeyBlobFormat.Pkcs8PrivateBlob);

    SigningCredentials signingCred = new SigningCredentials(
        new ECDsaSecurityKey(new ECDsaCng(cngKey)),
        SecurityAlgorithms.EcdsaSha256
    );

    JwtSecurityToken token = new JwtSecurityToken(
        issuer,
        audience,
        claims,
        DateTime.Now,
        DateTime.Now.AddDays(180),
        signingCred
    );
    token.Header.Add("kid", kid);
    token.Header.Remove("typ");

    JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler();

    return tokenHandler.WriteToken(token);
}
```
- **teamId**: uw Apple Developer Team-ID
- **clientId**: de Apple-client-id (ook de Service-id)
- **p8key**: de PEM-indelings sleutel-u kunt de sleutel verkrijgen door het bestand te openen `.p8` in een tekst editor en alles te kopiëren tussen `-----BEGIN PRIVATE KEY-----` en `-----END PRIVATE KEY-----` zonder regel einden
- **keyId**: de id van de gedownloade sleutel

Dit token dat wordt geretourneerd, is de geheim waarde van de client die u gebruikt om de Apple-provider te configureren.

> [!IMPORTANT]
> Het client geheim is een belang rijke beveiligings referentie. Deel dit geheim niet met iemand of distribueer het in een client toepassing.
>

Voeg het client geheim toe als een [toepassings instelling](./configure-common.md#configure-app-settings) voor de app, waarbij u de naam van uw keuze kunt opgeven. Noteer deze naam voor later.

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Provider gegevens toevoegen aan uw toepassing

> [!NOTE]
> De vereiste configuratie heeft een nieuwe API-indeling, die momenteel alleen wordt ondersteund door op [bestanden gebaseerde configuratie (preview)](.\app-service-authentication-how-to.md#config-file). U moet de onderstaande stappen volgen met behulp van een dergelijk bestand.

In deze sectie wordt uitgelegd hoe u de configuratie bijwerkt om uw nieuwe IDP op te nemen. Hier volgt een voorbeeld configuratie.

1. Voeg binnen het- `identityProviders` object een `apple` object toe als er nog geen bestaat.
2. Wijs een object toe aan die sleutel met een `registration` object binnen het, en optioneel een `login` object:
    
    ```json
    "apple" : {
       "registration" : {
            "clientId": "<client id>",
            "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_CLIENT_SECRET" 
        },
       "login": {
             "scopes": []
       }
    }
    ```
    a. In het `registration` -object stelt `clientId` u de client-id in die u hebt verzameld.
    
    b. Stel in het- `registration` object `clientSecretSettingName` de naam in van de toepassings instelling waar u het client geheim hebt opgeslagen.
    
    c. Binnen het `login` -object kunt u ervoor kiezen om de `scopes` matrix in te stellen op een lijst met bereiken die worden gebruikt bij het verifiëren met Apple, zoals ' naam ' en ' e-mail '. Als scopes zijn geconfigureerd, worden ze expliciet aangevraagd op het scherm toestemming wanneer gebruikers zich voor de eerste keer aanmelden.

Zodra deze configuratie is ingesteld, bent u klaar om uw Apple-provider te gebruiken voor verificatie in uw app.

Een volledige configuratie kan eruitzien als in het volgende voor beeld (waarbij de APPLE_GENERATED_CLIENT_SECRET instelling verwijst naar een toepassings instelling die een gegenereerde JWT bevat):

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
        "apple": {
            "registration": {
                "clientId": "com.contoso.example.client",
                "clientSecretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
            },
            "login": {
                "scopes": []
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
