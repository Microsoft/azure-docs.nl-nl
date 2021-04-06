---
title: Taal aanpassing in Azure Active Directory B2C
description: Meer informatie over het aanpassen van de taal ervaring in uw gebruikers stromen in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 418f0797343a64728c4e48084b09bd0e426cec62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686407"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Taal aanpassing in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Door de taal aanpassing in Azure Active Directory B2C (Azure AD B2C) kan uw gebruikers stroom voorzien in verschillende talen die aan de behoeften van uw klant voldoen. Micro soft biedt de vertalingen voor [36 talen](#supported-languages), maar u kunt ook uw eigen vertalingen voor elke taal opgeven. Zelfs als uw ervaring is bedoeld voor slechts één taal, kunt u tekst op de pagina's aanpassen.

## <a name="how-language-customization-works"></a>De werking van taal aanpassing

U gebruikt taal aanpassing om te selecteren in welke talen uw gebruikers stroom beschikbaar is. Nadat de functie is ingeschakeld, kunt u de query teken reeks parameter opgeven, `ui_locales` vanuit uw toepassing. Wanneer u Azure AD B2C aanroept, wordt uw pagina vertaald naar de land instellingen die u hebt opgegeven. Dit type configuratie geeft u volledige controle over de talen in uw gebruikers stroom en negeert de taal instellingen van de browser van de klant.

Mogelijk hebt u niet het controle niveau nodig over de talen die uw klant ziet. Als u geen `ui_locales` para meter opgeeft, wordt de gebruikers ervaring bepaald door de instellingen van de browser. U kunt nog steeds bepalen in welke talen uw gebruikers stroom wordt vertaald door deze toe te voegen als een ondersteunde taal. Als de browser van een klant is ingesteld om een taal weer te geven die u niet wilt ondersteunen, wordt in plaats daarvan de taal weer gegeven die u als standaard waarde hebt geselecteerd in ondersteunde cult uren.

* **gebruikers interface-land instellingen opgegeven taal**: nadat u de taal aanpassing hebt ingeschakeld, wordt uw gebruikers stroom vertaald naar de taal die u hier opgeeft.
* Door **browser aangevraagde taal**: als er geen `ui_locales` para meter is opgegeven, wordt uw gebruikers stroom vertaald naar de door de browser aangevraagde taal, *als de taal wordt ondersteund*.
* **Standaard taal van beleid**: als in de browser geen taal is opgegeven of als er een niet wordt ondersteund, wordt de gebruikers stroom vertaald naar de standaard taal van de gebruikers stroom.

> [!NOTE]
> Als u aangepaste gebruikers kenmerken gebruikt, moet u uw eigen vertalingen opgeven. Zie [uw teken reeksen aanpassen](#customize-your-strings)voor meer informatie.

::: zone pivot="b2c-custom-policy"

Lokalisatie vereist drie stappen: 

1. De expliciete lijst met ondersteunde talen instellen
1. Taalspecifieke teken reeksen en verzamelingen opgeven
1. Bewerk de [inhouds definitie](contentdefinitions.md) voor de pagina. 

::: zone-end 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="support-requested-languages-for-ui_locales"></a>Ondersteuning voor aangevraagde talen voor ui_locales

Beleids regels die zijn gemaakt voor de algemene Beschik baarheid van taal aanpassing, moeten deze functie eerst inschakelen. Beleids regels en gebruikers stromen die zijn gemaakt nadat de taal aanpassing standaard is ingeschakeld.

Wanneer u taal aanpassing inschakelt voor een gebruikers stroom, kunt u de taal van de gebruikers stroom beheren door de para meter toe te voegen `ui_locales` .

1. Selecteer in uw Azure AD B2C-Tenant **gebruikers stromen**.
1. Klik op de gebruikers stroom die u wilt inschakelen voor vertalingen.
1. Selecteer **talen**.
1. Selecteer **taal aanpassing inschakelen**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Selecteren welke talen in uw gebruikers stroom zijn ingeschakeld

Schakel een set talen in voor uw gebruikers stroom die moet worden omgezet naar wanneer dit wordt aangevraagd door de browser zonder de `ui_locales` para meter.

1. Zorg ervoor dat de taal aanpassing van uw gebruikers stroom is ingeschakeld vanuit de vorige instructies.
1. Selecteer op de pagina **talen** voor de gebruikers stroom een taal die u wilt ondersteunen.
1. Wijzig in het deel venster Eigenschappen de **instelling ingeschakeld** in **Ja**.
1. Selecteer **Opslaan** boven in het deel venster Eigenschappen.

>[!NOTE]
>Als er `ui_locales` geen para meter wordt gegeven, wordt de pagina alleen vertaald naar de browser taal van de klant als deze is ingeschakeld.
>

## <a name="customize-your-strings"></a>Uw teken reeksen aanpassen

Door taal aanpassing kunt u een wille keurige teken reeks in uw gebruikers stroom aanpassen.

1. Zorg ervoor dat de taal aanpassing voor uw gebruikers stroom is ingeschakeld in de vorige instructies.
1. Selecteer op de pagina **talen** voor de gebruikers stroom de taal die u wilt aanpassen.
1. Selecteer onder **bronnen bestanden op pagina niveau** de pagina die u wilt bewerken.
1. Selecteer **standaard waarden downloaden** (of **down loads negeren** als u deze taal eerder hebt bewerkt).

Deze stappen geven u een JSON-bestand dat u kunt gebruiken om te beginnen met het bewerken van uw teken reeksen.

### <a name="change-any-string-on-the-page"></a>Een wille keurige teken reeks op de pagina wijzigen

1. Open het JSON-bestand dat u hebt gedownload van de vorige instructies in een JSON-editor.
1. Zoek het element dat u wilt wijzigen. U kunt zoeken naar `StringId` de teken reeks die u zoekt, of zoeken naar het `Value` kenmerk dat u wilt wijzigen.
1. Werk het `Value` kenmerk bij met wat u wilt weer geven.
1. Voor elke teken reeks die u wilt wijzigen in, wijzigt u `Override` in `true` .
1. Sla het bestand op en upload uw wijzigingen. (U kunt het upload beheer vinden op dezelfde locatie als waar u het JSON-bestand hebt gedownload.)

> [!IMPORTANT]
> Als u een teken reeks moet overschrijven, moet u ervoor zorgen dat u de `Override` waarde instelt op `true` . Als de waarde niet wordt gewijzigd, wordt de vermelding genegeerd.

### <a name="change-extension-attributes"></a>Extensie kenmerken wijzigen

Als u de teken reeks voor een aangepast gebruikers kenmerk wilt wijzigen of een wilt toevoegen aan de JSON, heeft dit de volgende notatie:

```json
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
  ]
}
```

Vervang door `<ExtensionAttribute>` de naam van uw aangepaste gebruikers kenmerk.

Vervang door `<ExtensionAttributeValue>` de nieuwe teken reeks die moet worden weer gegeven.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Een lijst met waarden opgeven met behulp van LocalizedCollections

Als u een lijst met waarden voor antwoorden wilt opgeven, moet u een `LocalizedCollections` kenmerk maken. `LocalizedCollections` is een matrix van `Name` en- `Value` paren. De volg orde van de items is de volg orde waarin ze worden weer gegeven. Gebruik de volgende indeling om toe te voegen `LocalizedCollections` :

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
        {
          "Name":"<Response1>",
          "Value":"<Value1>"
        },
        {
          "Name":"<Response2>",
          "Value":"<Value2>"
        }
      ]
    }
  ]
}
```

* `ElementId` is het gebruikers kenmerk waarvan dit `LocalizedCollections` kenmerk een antwoord is.
* `Name` is de waarde die wordt weer gegeven voor de gebruiker.
* `Value` is wat wordt geretourneerd in de claim wanneer deze optie is geselecteerd.

### <a name="upload-your-changes"></a>Uw wijzigingen uploaden

1. Nadat u de wijzigingen in het JSON-bestand hebt voltooid, gaat u terug naar de B2C-Tenant.
1. Selecteer **gebruikers stromen** en klik op de gebruikers stroom die u wilt inschakelen voor vertalingen.
1. Selecteer **talen**.
1. Selecteer de taal waarin u wilt vertalen.
1. Selecteer de pagina waar u vertalingen wilt opgeven.
1. Selecteer het mappictogram en selecteer het JSON-bestand dat u wilt uploaden.

De wijzigingen worden automatisch opgeslagen in de stroom van uw gebruikers.

## <a name="customize-the-page-ui-by-using-language-customization"></a>De pagina gebruikers interface aanpassen met behulp van taal aanpassing

Er zijn twee manieren om uw [HTML-inhoud](customize-ui-with-html.md)te lokaliseren. Een manier is om [taal aanpassing](language-customization.md)in te scha kelen. Als u deze functie inschakelt, kunnen Azure AD B2C de OpenID Connect Connect-para meter, `ui-locales` naar uw eind punt door sturen. De inhouds server kan deze para meter gebruiken om aangepaste HTML-pagina's te bieden die specifiek zijn voor een specifieke taal.

U kunt ook inhoud vanaf verschillende locaties ophalen op basis van de land instelling die wordt gebruikt. In het eind punt waarvoor CORS is ingeschakeld, kunt u een mapstructuur instellen om inhoud voor specifieke talen te hosten. U belt het juiste nummer als u de Joker teken waarde gebruikt `{Culture:RFC5646}` . Stel dat dit uw aangepaste pagina-URI is:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

U kunt de pagina laden in `fr` . Wanneer de pagina HTML-en CSS-inhoud haalt, wordt deze opgehaald uit:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Aangepaste talen toevoegen

U kunt ook talen toevoegen waarnaar micro soft momenteel geen vertalingen levert. U moet de vertalingen opgeven voor alle teken reeksen in de gebruikers stroom. Taal-en land codes zijn beperkt tot die in de ISO 639-1-standaard. De notatie voor de land code moet ISO_639-1_code '-' CountryCode ' zijn `en-GB` . Raadpleeg voor meer informatie over landinstellings-ID-indelingen https://docs.microsoft.com/openspecs/office_standards/ms-oe376/6c085406-a698-4e12-9d4d-c3b0ee3dbc4a

1. Selecteer in uw Azure AD B2C-Tenant **gebruikers stromen**.
2. Klik op de gebruikers stroom waaraan u aangepaste talen wilt toevoegen en klik vervolgens op **talen**.
3. Selecteer **aangepaste taal toevoegen** boven aan de pagina.
4. In het context venster dat wordt geopend, identificeert u in welke taal u de vertalingen levert door een geldige land instelling in te voeren.
5. Voor elke pagina kunt u een set onderdrukkingen downloaden voor Engels en werken aan de vertalingen.
6. Nadat u klaar bent met de JSON-bestanden, kunt u deze voor elke pagina uploaden.
7. Selecteer **inschakelen** en uw gebruikers stroom kan deze taal nu weer geven voor uw gebruikers.
8. Sla de taal op.

>[!IMPORTANT]
>Voordat u kunt opslaan, moet u de aangepaste talen inschakelen of overschrijvingen uploaden.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="set-up-the-list-of-supported-languages"></a>De lijst met ondersteunde talen instellen

Open het bestand extensies van uw beleid. Bijvoorbeeld <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Zoek het element [BuildingBlocks](buildingblocks.md) . Als het element niet bestaat, voegt u het toe.
1. Voeg het- `Localization` element toe met de ondersteunde talen: Engels (standaard) en Spaans.  


```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="provide-language-specific-labels"></a>Taalspecifieke labels opgeven

De [LocalizedResources](localization.md#localizedresources) van het `Localization` element bevat de lijst met gelokaliseerde teken reeksen. Het gelokaliseerde resources-element heeft een id die wordt gebruikt om gelokaliseerde bronnen uniek te identificeren. Deze id wordt later in het [inhouds definitie](contentdefinitions.md) -element gebruikt.

U configureert gelokaliseerde bronnen elementen voor de inhouds definitie en alle talen die u wilt ondersteunen. Als u de Unified Sign-up-en aanmeldings pagina's voor Engels en Spaans wilt aanpassen, voegt u de volgende `LocalizedResources` elementen toe na het sluiten van het `</SupportedLanguages>` element.

> [!NOTE]
> In het volgende voor beeld is het `#` symbool symbol aan het begin van elke regel toegevoegd, zodat u gemakkelijk de gelokaliseerde labels op het scherm kunt vinden.

```xml
<!--Local account sign-up or sign-in page English-->
<Localization Enabled="true">
  ...
  <LocalizedResources Id="api.signuporsignin.en">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Email Address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Please enter your email</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Username</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Sign up now</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Please enter your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#Don't have an account?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#Forgot your password?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#OR</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#The user has forgotten their password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Sign in</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Sign in with your social account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Please enter your password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#The password you entered is not in the expected format.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Sign in with your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Sign in with your existing account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Please enter a valid email address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#We are having trouble signing you in. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Your password is incorrect.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#We can't seem to find your account.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Looks like you used an old password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#Invalid username or password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up or sign-in page Spanish-->
  <LocalizedResources Id="api.signuporsignin.es">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Este campo es obligatorio</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Registrarse ahora</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Escriba su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#¿No tiene una cuenta?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#¿Olvidó su contraseña?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#O</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#El usuario ha olvidado su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Iniciar sesión</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Iniciar sesión con su cuenta de redes sociales</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Escriba su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#La contraseña que ha escrito no está en el formato esperado.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Iniciar sesión con su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Iniciar sesión con su cuenta existente</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Escriba una dirección de correo electrónico válida</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#Tenemos problemas para iniciar su sesión. Vuelva a intentarlo más tarde.  </LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Su contraseña es incorrecta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#Parece que no podemos encontrar su cuenta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Parece que ha usado una contraseña antigua.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#El nombre de usuario o la contraseña no son válidos.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Se bloqueó su cuenta. Póngase en contacto con la persona responsable de soporte técnico para desbloquearla y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Su cuenta se bloqueó temporalmente para impedir un uso no autorizado. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page English-->
  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Email Address</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Email address that can be used to contact you.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Please enter a valid email address.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Enter new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirm New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirm new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Display Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Your display name.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Surname</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Your surname (also known as family name or last name).  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Given Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Your given name (also known as first name).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Create</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#A required field is missing. Please fill out all required fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#What is this?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Please provide the following details.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Please wait</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#This information is required.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Change e-mail</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Send new code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Send verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Verify code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#That code is expired. Please request a new code.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#You've made too many incorrect attempts. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#That code is incorrect. Please try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#Verification is necessary. Please click Send button.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#E-mail address verified. You can now continue.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Claim not verified: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#A user with the specified ID already exists. Please choose a different one.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Incorrect pattern for: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} has invalid input.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Missing required element: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error in validation by: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page Spanish-->
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Dirección de correo electrónico</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Introduzca una dirección de correo electrónico válida.  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#Nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Escriba la contraseña nueva</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#De 8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Nombre para mostrar</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Su nombre para mostrar.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Apellido</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Su apellido.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Nombre</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Su nombre (también conocido como nombre de pila).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Crear</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#Hay uno o varios campos rellenados de forma incorrecta. Compruebe las entradas y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#Los campos de entrada de contraseña no coinciden. Escriba la misma contraseña en ambos campos y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#Falta un campo obligatorio. Rellene todos los campos necesarios y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#¿Qué es esto?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Proporcione los siguientes detalles.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Espere</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#Esta información es obligatoria.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Cambiar correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Enviar nuevo código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Enviar código de comprobación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Comprobar código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#El código ha expirado. Solicite otro nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#Ha realizado demasiados intentos incorrectos. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#Ese código es incorrecto. Inténtelo de nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#Tenemos problemas para comprobar la dirección de correo electrónico. Escriba una dirección de correo electrónico válida y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#Ha habido demasiadas solicitudes para comprobar esta dirección de correo electrónico. Espere un poco y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Se ha enviado el código de verificación a su Bandeja de entrada. Cópielo en el siguiente cuadro de entrada.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Código de verificación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#La comprobación es obligatoria. Haga clic en el botón Enviar.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#Dirección de correo electrónico comprobada. Puede continuar.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Reclamación no comprobada: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Patrón incorrecto para: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} tiene una entrada no válida.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Falta un elemento obligatorio: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error en la validación de: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

## <a name="edit-the-content-definition-with-the-localization"></a>De inhouds definitie met de lokalisatie bewerken

Plak de volledige inhoud van het ContentDefinitions-element dat u hebt gekopieerd als onderliggend element van het Building Blocks-object.

In het volgende voor beeld worden de aangepaste teken reeksen Engels (en) en Spaans (ES) toegevoegd aan de registratie pagina voor aanmelden of aanmelden en op de registratie pagina van het lokale account. De **LocalizedResourcesReferenceId** voor elke **LocalizedResourcesReference** is hetzelfde als de land instelling, maar u kunt elke wille keurige teken reeks als de id gebruiken. Voor elke combi natie van taal en pagina wijst u de overeenkomende **LocalizedResources** aan die u eerder hebt gemaakt.

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.signuporsignin">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>

  <ContentDefinition Id="api.localaccountsignup">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>
</ContentDefinitions>
```

##  <a name="upload-and-test-your-updated-custom-policy"></a>Uw bijgewerkte aangepaste beleid uploaden en testen

### <a name="upload-the-custom-policy"></a>Het aangepaste beleid uploaden

1. Sla het bestand met extensies op.
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
1. Zoek en selecteer **Azure AD B2C**.
1. Onder **beleids regels** selecteert u **identiteits ervaring-Framework**.
1. Selecteer **aangepast beleid uploaden**.
1. Upload het extensie bestand dat u eerder hebt gewijzigd.

### <a name="test-the-custom-policy-by-using-run-now"></a>Het aangepaste beleid testen met behulp van **nu uitvoeren**

1. Selecteer het beleid dat u hebt geüpload en selecteer **nu uitvoeren**.
1. U moet de gelokaliseerde registratie-of aanmeldings pagina kunnen zien.
1. Klik op de registratie koppeling en u zou de gelokaliseerde registratie pagina moeten kunnen zien.
1. Schakel de standaard taal van uw browser in op Spaans. U kunt ook de query teken reeks parameter toevoegen `ui_locales` aan de autorisatie aanvraag. Bijvoorbeeld: 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize&client_id=0239a9cc-309c-4d41-12f1-31299feb2e82&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&ui_locales=es
```

::: zone-end


## <a name="additional-information"></a>Aanvullende informatie

::: zone pivot="b2c-user-flow"

### <a name="page-ui-customization-labels-as-overrides"></a>Labels voor pagina-UI aanpassen als onderdrukkingen

Wanneer u taal aanpassing inschakelt, worden uw vorige wijzigingen voor labels met de aanpassing van de pagina-UI persistent gemaakt in een JSON-bestand voor Engels (en). U kunt door gaan met het wijzigen van uw labels en andere teken reeksen door taal resources te uploaden bij het aanpassen van de taal.

::: zone-end

### <a name="up-to-date-translations"></a>Up-to-date vertalingen

Micro soft streeft ernaar de meest recente vertalingen te leveren voor uw gebruik. Micro soft verbetert de vertalingen voortdurend en houdt ze op de naleving voor u. Micro soft identificeert bugs en wijzigingen in globale terminologie en maakt updates die naadloos werken in uw gebruikers stroom.

### <a name="support-for-right-to-left-languages"></a>Ondersteuning voor talen die van rechts naar links worden geschreven

Micro soft biedt momenteel geen ondersteuning voor talen die van rechts naar links worden geschreven. U kunt dit doen door aangepaste land instellingen te gebruiken en CSS te gebruiken voor het wijzigen van de manier waarop de teken reeksen worden weer gegeven. Als u deze functie nodig hebt, kunt u hieraan stemmen voor [Azure feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Vertalingen van de sociale ID-provider

Micro soft biedt de `ui_locales` para meter OIDC aan sociale aanmeldingen. Maar sommige aanbieders van sociale identiteiten, waaronder Facebook en Google, voldoen niet aan deze providers.

### <a name="browser-behavior"></a>Browser gedrag

Chrome en Firefox hebben beide aanvragen voor de ingestelde taal. Als het een ondersteunde taal is, wordt deze weer gegeven vóór de standaard waarde. Micro soft Edge vraagt momenteel geen taal aan en gaat rechtstreeks naar de standaard taal.

## <a name="supported-languages"></a>Ondersteunde talen

Azure AD B2C biedt ondersteuning voor de volgende talen. De talen van de gebruikers stroom worden door Azure AD B2C verschaft. De meldingen voor multi-factor Authentication (MFA) worden geleverd door [Azure AD MFA](../active-directory/authentication/concept-mfa-howitworks.md).

| Taal              | Taalcode | Gebruikersstromen         | MFA-meldingen  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabisch                | ar            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Bulgaars             | bg            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Bengaals                | bn            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) |
| Catalaans               | ca            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Tsjechisch                 | cs            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Deens                | da            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Duits                | de            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Grieks                 | el            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Engels               | en            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Spaans               | es            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Ests              | et            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Baskisch                | eu            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Fins               | fi            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Frans                | fr            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Galicisch              | gl            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Gujarati              | gu            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) |
| Hebreeuws                | he            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Hindi                 | hi            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Kroatisch              | uur            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Hongaars             | hu            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Indonesisch            | id            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Italiaans               | it            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Japans              | ja            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Kazachs                | kk            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Kannada               | kn            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) |
| Koreaans                | ko            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Litouws            | lt            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Lets               | lv            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Malayalam             | ml            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) |
| Mahrati               | mr            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) |
| Maleisisch                 | ms            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Noors (Bokmål)      | nb            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) |
| Nederlands                 | nl            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Noors             | nee            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Punjabi               | pa            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) |
| Pools                | pl            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Portuguese - Brazil   | pt-br         | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Portugees - Portugal | pt-pt         | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Roemeens              | ro            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Russisch               | ru            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Slowaaks                | sk            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Sloveens             | sl            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Servisch - Cyrillisch    | SR-Cryl-CS    | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Servisch - Latijn       | SR-latn-cs    | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Zweeds               | sv            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Tamil                 | ta            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) |
| Telugu                | te            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) |
| Thai                  | th            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Turks               | tr            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Oekraïens             | uk            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Vietnamees            | vi            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Chinese - vereenvoudigd  | zh-Hans       | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Chinese - traditioneel | zh-hant       | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |


## <a name="next-steps"></a>Volgende stappen

::: zone pivot="b2c-user-flow"

Meer informatie over hoe u de gebruikers interface van uw toepassingen kunt aanpassen in [het aanpassen van de gebruikers interface van uw toepassing in azure Active Directory B2C](customize-ui-with-html.md).

::: zone-end 

::: zone pivot="b2c-custom-policy"

- Meer informatie over het [lokalisatie](localization.md) -element vindt u in de IEF-verwijzing.
- Zie de lijst met beschik bare [lokalisatie teken reeks-id's](localization-string-ids.md) in azure AD B2C.

::: zone-end 
