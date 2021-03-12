---
title: Id van lokalisatie reeks-Azure Active Directory B2C | Microsoft Docs
description: Geef de Id's op voor een inhouds definitie met de ID API. signuporsignin in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/10/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b60b447d8302b89813ca462c3220603ef926eb26
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102631180"
---
# <a name="localization-string-ids"></a>Id's van lokalisatiereeksen

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Het **lokalisatie** -element biedt ondersteuning voor meerdere land instellingen of talen in het beleid voor de gebruikers ritten. Dit artikel bevat de lijst met lokalisatie-Id's die u in uw beleid kunt gebruiken. Zie [lokalisatie](localization.md)om vertrouwd te raken met de lokalisatie van de gebruikers interface.

## <a name="sign-up-or-sign-in-page-elements"></a>Registratie-of aanmeldings pagina-elementen

De volgende Id's worden gebruikt voor een inhouds definitie met een ID van `api.signuporsignin` en een [zelf-bevestigd technisch profiel](self-asserted-technical-profile.md).

| Id | Standaardwaarde | Versie van pagina-indeling |
| -- | ------------- | ------ |
| **forgotpassword_link** | Bent u uw wachtwoord vergeten? | `All` |
| **createaccount_intro** | Hebt u geen account? | `All` |
| **button_signin** | Aanmelden | `All` |
| **social_intro** | Meld u aan met uw sociale account | `All` |
| **remember_me** |Blijf aangemeld. | `All` |
| **unknown_error** | U ondervindt problemen bij het aanmelden. Probeert u het later nog eens. | `All` |
| **divider_title** | OF | `All` |
| **local_intro_email** | Meld u aan met uw bestaande account | `< 2.0.0` |
| **logonIdentifier_email** | E-mailadres | `< 2.0.0` |
| **requiredField_email** | Voer uw e-mail adres in | `< 2.0.0` |
| **invalid_email** | Voer een geldig e-mail adres in | `< 2.0.0` |
| **email_pattern** | ^ [a-zA-Z0-9.! # $% & ' ' \* +/=? ^ \_ \` { \| } ~-] + @ [a-zA-Z0-9-] + (?: \\ . [ a-zA-Z0-9-] +) \* $ |`< 2.0.0` |
| **local_intro_username** | Meld u aan met uw gebruikers naam | `< 2.0.0` |
| **logonIdentifier_username** | Gebruikersnaam | `< 2.0.0` |
| **requiredField_username** | Voer uw gebruikers naam in | `< 2.0.0` |
| **password** | Wachtwoord | `< 2.0.0` |
| **requiredField_password** | Voer uw wacht woord in | `< 2.0.0` |
| **createaccount_link** | Nu registreren | `< 2.0.0` |
| **cancel_message** | De gebruiker is het wacht woord verg eten | `< 2.0.0` |
| **invalid_password** | Het wacht woord dat u hebt opgegeven, heeft niet de verwachte indeling. | `< 2.0.0` |
| **createaccount_one_link** | Nu registreren | `>= 2.0.0` |
| **createaccount_two_links** | Meld u aan met {0} of {1} | `>= 2.0.0` |
| **createaccount_three_links** | Meld u aan met {0} , {1} of {2} | `>= 2.0.0` |
| **local_intro_generic** | Meld u aan met uw {0} | `>= 2.1.0` |
| **requiredField_generic** | Voer uw {0} | `>= 2.1.0` |
| **invalid_generic** | Voer een geldige {0} | `>= 2.1.1` |
| **sectiekop** | Aanmelden | `>= 2.1.1` |


> [!NOTE]
> * Tijdelijke aanduidingen zoals worden {0} automatisch ingevuld met de `DisplayName` waarde van `ClaimType` . 
> * `ClaimType`Zie [Aanmelden of aanmelden voor beeld](#signupsigninexample)voor meer informatie over het lokaliseren van.

In het volgende voor beeld ziet u het gebruik van een aantal van de elementen van de gebruikers interface op de registratie-of aanmeldings pagina:

:::image type="content" source="./media/localization-string-ids/localization-susi-2.png" alt-text="Scherm opname van de U X-elementen voor registratie of aanmeldings pagina.":::

### <a name="sign-up-or-sign-in-identity-providers"></a>Registratie-en registratie-id-providers

De id's van de ID-providers worden geconfigureerd in het  **ClaimsExchange** -element van de gebruikers reis. Als u de naam van de ID-provider wilt lokaliseren, wordt het **element type** ingesteld op `ClaimsProvider` , terwijl de **StringId** is ingesteld op de id van de `ClaimsExchange` .

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

In het volgende voor beeld wordt de Facebook-ID-provider naar Arabisch gelokaliseerd:

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

### <a name="sign-up-or-sign-in-error-messages"></a>Fout berichten bij het registreren of aanmelden

| Id | Standaardwaarde |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | Het wacht woord is onjuist. |
| **UserMessageIfPasswordExpired**| Uw wacht woord is verlopen.|
| **UserMessageIfClaimsPrincipalDoesNotExist** | Uw account is niet gevonden. |
| **UserMessageIfOldPasswordUsed** | Het lijkt erop dat u een oud wacht woord hebt gebruikt. |
| **DefaultMessage** | Ongeldige gebruikers naam of wacht woord. |
| **UserMessageIfUserAccountDisabled** | Uw account is vergrendeld. Neem contact op met de ondersteunings medewerker om deze te ontgrendelen en probeer het opnieuw. |
| **UserMessageIfUserAccountLocked** | Uw account is tijdelijk vergrendeld om onbevoegd gebruik te voor komen. Probeer het later opnieuw. |
| **AADRequestsThrottled** | Er zijn op dit moment te veel aanvragen. Wacht enige tijd en probeer het opnieuw. |

<a name="signupsigninexample"></a>
### <a name="sign-up-or-sign-in-example"></a>Registreren of aanmelden voor beeld

```xml
<LocalizedResources Id="api.signuporsignin.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="heading">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="social_intro">Sign in with your social account</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_intro_generic">Sign in with your {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_password">Please enter your password</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_generic">Please enter your {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_generic">Please enter a valid {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_one_link">Sign up now</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_two_links">Sign up with {0} or {1}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_three_links">Sign up with {0}, {1}, or {2}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">Forgot your password?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_signin">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="divider_title">OR</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="unknown_error">We are having trouble signing you in. Please try again later.</LocalizedString>
    <!-- Uncomment the remember_me only if the keep me signed in is activated. 
    <LocalizedString ElementType="UxElement" StringId="remember_me">Keep me signed in</LocalizedString> -->
    <LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfPasswordExpired">Your password has expired.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">Looks like you used an old password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">Invalid username or password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Gebruikers interface-elementen voor het registreren en zelf bevestigingen van pagina's

Hieronder ziet u de Id's voor een inhouds definitie met een ID van `api.localaccountsignup` of een inhouds definitie die begint met `api.selfasserted` , zoals `api.selfasserted.profileupdate` en `api.localaccountpasswordreset` , en een [zelfondertekend technisch profiel](self-asserted-technical-profile.md).

| Id | Standaardwaarde |
| -- | ------------- |
| **ver_sent** | De verificatie code is verzonden naar: |
| **ver_but_default** | Standaard |
| **cancel_message** | De gebruiker heeft het invoeren van zelfbevestigende informatie geannuleerd |
| **preloader_alt** | Een ogenblik geduld |
| **ver_but_send** | Verificatie code verzenden |
| **alert_yes** | Ja |
| **error_fieldIncorrect** | Een of meer velden zijn onjuist ingevuld. Controleer uw vermeldingen en probeer het opnieuw. |
| **jaareinde** | Year |
| **verifying_blurb** | De gegevens worden verwerkt. een ogen blik geduld... |
| **button_cancel** | Annuleren |
| **ver_fail_no_retry** | U hebt te veel onjuiste pogingen gedaan. Probeert u het later nog eens. |
| **blijft** | Maand |
| **ver_success_msg** | E-mail adres geverifieerd. U kunt nu door gaan. |
| **maanden** | Januari, februari, maart, april, mei, juni, juli, augustus, september, oktober, november, december |
| **ver_fail_server** | Er zijn problemen met het controleren van uw e-mail adres. Geef een geldig e-mail adres op en probeer het opnieuw. |
| **error_requiredFieldMissing** | Een vereist veld ontbreekt. Vul alle vereiste velden in en probeer het opnieuw. |
| **initial_intro** | Geef de volgende details op. |
| **ver_but_resend** | Nieuwe code verzenden |
| **button_continue** | Maken |
| **error_passwordEntryMismatch** | De invoer velden voor het wacht woord komen niet overeen. Voer hetzelfde wacht woord in beide velden in en probeer het opnieuw. |
| **ver_incorrect_format** | Onjuiste indeling. |
| **ver_but_edit** | E-mail wijzigen |
| **ver_but_verify** | Code verifiëren |
| **alert_no** | Nee |
| **ver_info_msg** | De verificatie code is verzonden naar het postvak in. Kopieer het naar het invoervak hieronder. |
| **profieldag** | Dag |
| **ver_fail_throttled** | Er zijn te veel aanvragen om dit e-mail adres te controleren. Wacht even en probeer het opnieuw. |
| **helplink_text** | Wat is dit? |
| **ver_fail_retry** | Deze code is onjuist. Probeer het opnieuw. |
| **alert_title** | Het invoeren van uw gegevens annuleren |
| **required_field** | Deze gegevens zijn vereist. |
| **alert_message** | Weet u zeker dat u het invoeren van uw gegevens wilt annuleren? |
| **ver_intro_msg** | Verificatie is nood zakelijk. Klik op de knop verzenden. |
| **ver_input** | Verificatiecode |

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>Fout berichten over het registreren en automatisch bevestigen van pagina's

| Id | Standaardwaarde |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Er bestaat al een gebruiker met de opgegeven ID. Kies een andere naam. |
| **UserMessageIfClaimNotVerified** | Claim niet geverifieerd: {0} |
| **UserMessageIfIncorrectPattern** | Onjuist patroon voor: {0} |
| **UserMessageIfMissingRequiredElement** | Vereist element ontbreekt: {0} |
| **UserMessageIfValidationError** | Fout in validatie door: {0} |
| **UserMessageIfInvalidInput** | {0} heeft ongeldige invoer. |
| **ServiceThrottled** | Er zijn op dit moment te veel aanvragen. Wacht enige tijd en probeer het opnieuw. |

In het volgende voor beeld ziet u het gebruik van enkele van de elementen van de gebruikers interface op de registratie pagina:

![Registratie pagina met de namen van de gebruikers interface-elementen labelen](./media/localization-string-ids/localization-sign-up.png)

In het volgende voor beeld ziet u het gebruik van enkele van de elementen van de gebruikers interface op de registratie pagina, nadat de gebruiker op de knop verificatie code verzenden heeft geklikt:

![Aanmeldings pagina e-mail verificatie UX-elementen](./media/localization-string-ids/localization-email-verification.png)

## <a name="sign-up-and-self-asserted-pages-example"></a>Voor beeld van registratie en zelf bevestiging van pagina's

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Email address that can be used to contact you.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">Enter new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">Confirm New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">Confirm new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">Display Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">Your display name.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">Surname</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">Your surname (also known as family name or last name).</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">Given Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">Your given name (also known as first name).</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">A required field is missing. Please fill out all required fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="helplink_text">What is this?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please provide the following details.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="preloader_alt">Please wait</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="required_field">This information is required.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_edit">Change e-mail</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_resend">Send new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_send">Send verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_verify">Verify code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">That code is incorrect. Please try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_server">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_info_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_input">Verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_success_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">Claim not verified: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">A user with the specified ID already exists. Please choose a different one.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">Incorrect pattern for: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">{0} has invalid input.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">Missing required element: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">Error in validation by: {0}</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="phone-factor-authentication-page-user-interface-elements"></a>Gebruikers interface-elementen van de pagina telefoon Factor Authentication

Hier volgen de Id's voor een inhouds definitie met de ID en het `api.phonefactor` [technische profiel van de telefoon factor](phone-factor-technical-profile.md).

| Id | Standaardwaarde |
| -- | ------------- |
| **button_verify** | Bel me |
| **country_code_label** | Landnummer |
| **cancel_message** | De gebruiker heeft de multi-factor Authentication geannuleerd |
| **text_button_send_second_code** | een nieuwe code verzenden |
| **code_pattern** | \\d{6} |
| **intro_mixed** | We hebben het volgende nummer in de record voor u. We kunnen een code via SMS of telefoon verzenden om u te verifiëren. |
| **intro_mixed_p** | We hebben het volgende nummer in de record voor u. Kies een nummer dat wij telefonisch kunnen of verzend code via SMS om u te verifiëren. |
| **button_verify_code** | Code verifiëren |
| **requiredField_code** | Voer de verificatie code in die u hebt ontvangen |
| **invalid_code** | Voer de 6-cijferige code in die u hebt ontvangen |
| **button_cancel** | Annuleren |
| **local_number_input_placeholder_text** | Telefoonnummer |
| **button_retry** | Opnieuw proberen |
| **alternative_text** | Ik heb mijn telefoon niet |
| **intro_phone_p** | We hebben het volgende nummer in de record voor u. Kies een telefoon nummer dat kan worden geverifieerd. |
| **intro_phone** | We hebben het volgende nummer in de record voor u. We zullen u telefonisch verifiëren. |
| **enter_code_text_intro** | Voer uw verificatie code hieronder in of  |
| **intro_entry_phone** | Voer hieronder een nummer in waarop u kunt worden geverifieerd. |
| **intro_entry_sms** | Voer hieronder een nummer in dat we via SMS een code kunnen verzenden om u te verifiëren. |
| **button_send_code** | Code verzenden |
| **invalid_number** | Voer een geldig telefoon nummer in |
| **intro_sms** | We hebben het volgende nummer in de record voor u. Er wordt een code via SMS verzonden om u te verifiëren. |
| **intro_entry_mixed** | Voer hieronder een nummer in waarmee u een code via SMS of telefoon kunt verzenden om u te verifiëren. |
| **number_pattern** | ^\\+ (?: [0-9] [ \\ x20-]?) {6,14} [0-9] $ |
| **intro_sms_p** |We hebben het volgende nummer in de record voor u. Kies een nummer dat via SMS een code kan verzenden om u te verifiëren. |
| **requiredField_countryCode** | Selecteer uw land code |
| **requiredField_number** | Voer uw telefoon nummer in |
| **country_code_input_placeholder_text** |Land of regio |
| **number_label** | Telefoonnummer |
| **error_tryagain** | Het telefoon nummer dat u hebt ingevoerd, is bezet of niet beschikbaar. Controleer het nummer en probeer het opnieuw. |
| **error_incorrect_code** | De ingevoerde verificatie code komt niet overeen met onze records. Probeer het opnieuw of vraag een nieuwe code aan. |
| **countryList** | Zie [de lijst met landen](#phone-factor-authentication-page-example). |
| **error_448** | Het telefoon nummer dat u hebt ingevoerd, is onbereikbaar. |
| **error_449** | De gebruiker heeft het aantal nieuwe pogingen overschreden. |
| **verification_code_input_placeholder_text** | Verificatiecode |

In het volgende voor beeld ziet u het gebruik van een aantal van de elementen van de gebruikers interface op de pagina MFA-inschrijving:

![UX-elementen voor inschrijving van telefoon factor authenticatie](./media/localization-string-ids/localization-mfa1.png)

In het volgende voor beeld ziet u het gebruik van enkele van de elementen van de gebruikers interface op de pagina MFA-validatie:

![UX-elementen voor validatie van telefoon factor authenticatie](./media/localization-string-ids/localization-mfa2.png)

## <a name="phone-factor-authentication-page-example"></a>Pagina voor beeld van de verificatie van de telefoon factor

```xml
<LocalizedResources Id="api.phonefactor.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="button_verify">Call Me</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_label">Country Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="cancel_message">The user has canceled multi-factor authentication</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="text_button_send_second_code">Send a new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="code_pattern">\d{6}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed">We have the following number on record for you. We can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed_p">We have the following numbers on record for you. Choose a number that we can phone or send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_code">Please enter the verification code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_code">Please enter the 6-digit code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_number_input_placeholder_text">Phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_retry">Retry</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="alternative_text">I don't have my phone</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone_p">We have the following numbers on record for you. Choose a number that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone">We have the following number on record for you. We will phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="enter_code_text_intro">Enter your verification code below, or</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_phone">Enter a number below that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_sms">Enter a number below that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_number">Please enter a valid phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms">We have the following number on record for you. We will send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_mixed">Enter a number below that we can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_pattern">^\+(?:[0-9][\x20-]?){6,14}[0-9]$</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms_p">We have the following numbers on record for you. Choose a number that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_countryCode">Please select your country code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_number">Please enter your phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_input_placeholder_text">Country or region</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_label">Phone Number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_tryagain">The phone number you provided is busy or unavailable. Please check the number and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_incorrect_code">The verification code you have entered does not match our records. Please try again, or request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="countryList">{"DEFAULT":"Country/Region","AF":"Afghanistan","AX":"Åland Islands","AL":"Albania","DZ":"Algeria","AS":"American Samoa","AD":"Andorra","AO":"Angola","AI":"Anguilla","AQ":"Antarctica","AG":"Antigua and Barbuda","AR":"Argentina","AM":"Armenia","AW":"Aruba","AU":"Australia","AT":"Austria","AZ":"Azerbaijan","BS":"Bahamas","BH":"Bahrain","BD":"Bangladesh","BB":"Barbados","BY":"Belarus","BE":"Belgium","BZ":"Belize","BJ":"Benin","BM":"Bermuda","BT":"Bhutan","BO":"Bolivia","BQ":"Bonaire","BA":"Bosnia and Herzegovina","BW":"Botswana","BV":"Bouvet Island","BR":"Brazil","IO":"British Indian Ocean Territory","VG":"British Virgin Islands","BN":"Brunei","BG":"Bulgaria","BF":"Burkina Faso","BI":"Burundi","CV":"Cabo Verde","KH":"Cambodia","CM":"Cameroon","CA":"Canada","KY":"Cayman Islands","CF":"Central African Republic","TD":"Chad","CL":"Chile","CN":"China","CX":"Christmas Island","CC":"Cocos (Keeling) Islands","CO":"Colombia","KM":"Comoros","CG":"Congo","CD":"Congo (DRC)","CK":"Cook Islands","CR":"Costa Rica","CI":"Côte d'Ivoire","HR":"Croatia","CU":"Cuba","CW":"Curaçao","CY":"Cyprus","CZ":"Czech Republic","DK":"Denmark","DJ":"Djibouti","DM":"Dominica","DO":"Dominican Republic","EC":"Ecuador","EG":"Egypt","SV":"El Salvador","GQ":"Equatorial Guinea","ER":"Eritrea","EE":"Estonia","ET":"Ethiopia","FK":"Falkland Islands","FO":"Faroe Islands","FJ":"Fiji","FI":"Finland","FR":"France","GF":"French Guiana","PF":"French Polynesia","TF":"French Southern Territories","GA":"Gabon","GM":"Gambia","GE":"Georgia","DE":"Germany","GH":"Ghana","GI":"Gibraltar","GR":"Greece","GL":"Greenland","GD":"Grenada","GP":"Guadeloupe","GU":"Guam","GT":"Guatemala","GG":"Guernsey","GN":"Guinea","GW":"Guinea-Bissau","GY":"Guyana","HT":"Haiti","HM":"Heard Island and McDonald Islands","HN":"Honduras","HK":"Hong Kong SAR","HU":"Hungary","IS":"Iceland","IN":"India","ID":"Indonesia","IR":"Iran","IQ":"Iraq","IE":"Ireland","IM":"Isle of Man","IL":"Israel","IT":"Italy","JM":"Jamaica","JP":"Japan","JE":"Jersey","JO":"Jordan","KZ":"Kazakhstan","KE":"Kenya","KI":"Kiribati","KR":"Korea","KW":"Kuwait","KG":"Kyrgyzstan","LA":"Laos","LV":"Latvia","LB":"Lebanon","LS":"Lesotho","LR":"Liberia","LY":"Libya","LI":"Liechtenstein","LT":"Lithuania","LU":"Luxembourg","MO":"Macao SAR","MK":"North Macedonia","MG":"Madagascar","MW":"Malawi","MY":"Malaysia","MV":"Maldives","ML":"Mali","MT":"Malta","MH":"Marshall Islands","MQ":"Martinique","MR":"Mauritania","MU":"Mauritius","YT":"Mayotte","MX":"Mexico","FM":"Micronesia","MD":"Moldova","MC":"Monaco","MN":"Mongolia","ME":"Montenegro","MS":"Montserrat","MA":"Morocco","MZ":"Mozambique","MM":"Myanmar","NA":"Namibia","NR":"Nauru","NP":"Nepal","NL":"Netherlands","NC":"New Caledonia","NZ":"New Zealand","NI":"Nicaragua","NE":"Niger","NG":"Nigeria","NU":"Niue","NF":"Norfolk Island","KP":"North Korea","MP":"Northern Mariana Islands","NO":"Norway","OM":"Oman","PK":"Pakistan","PW":"Palau","PS":"Palestinian Authority","PA":"Panama","PG":"Papua New Guinea","PY":"Paraguay","PE":"Peru","PH":"Philippines","PN":"Pitcairn Islands","PL":"Poland","PT":"Portugal","PR":"Puerto Rico","QA":"Qatar","RE":"Réunion","RO":"Romania","RU":"Russia","RW":"Rwanda","BL":"Saint Barthélemy","KN":"Saint Kitts and Nevis","LC":"Saint Lucia","MF":"Saint Martin","PM":"Saint Pierre and Miquelon","VC":"Saint Vincent and the Grenadines","WS":"Samoa","SM":"San Marino","ST":"São Tomé and Príncipe","SA":"Saudi Arabia","SN":"Senegal","RS":"Serbia","SC":"Seychelles","SL":"Sierra Leone","SG":"Singapore","SX":"Sint Maarten","SK":"Slovakia","SI":"Slovenia","SB":"Solomon Islands","SO":"Somalia","ZA":"South Africa","GS":"South Georgia and South Sandwich Islands","SS":"South Sudan","ES":"Spain","LK":"Sri Lanka","SH":"St Helena, Ascension, Tristan da Cunha","SD":"Sudan","SR":"Suriname","SJ":"Svalbard","SZ":"Swaziland","SE":"Sweden","CH":"Switzerland","SY":"Syria","TW":"Taiwan","TJ":"Tajikistan","TZ":"Tanzania","TH":"Thailand","TL":"Timor-Leste","TG":"Togo","TK":"Tokelau","TO":"Tonga","TT":"Trinidad and Tobago","TN":"Tunisia","TR":"Turkey","TM":"Turkmenistan","TC":"Turks and Caicos Islands","TV":"Tuvalu","UM":"U.S. Outlying Islands","VI":"U.S. Virgin Islands","UG":"Uganda","UA":"Ukraine","AE":"United Arab Emirates","GB":"United Kingdom","US":"United States","UY":"Uruguay","UZ":"Uzbekistan","VU":"Vanuatu","VA":"Vatican City","VE":"Venezuela","VN":"Vietnam","WF":"Wallis and Futuna","YE":"Yemen","ZM":"Zambia","ZW":"Zimbabwe"}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_448">The phone number you provided is unreachable.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_449">User has exceeded the number of retry attempts.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_code_input_placeholder_text">Verification code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="strongAuthenticationPhoneNumber" StringId="DisplayName">Phone Number</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>

```

## <a name="verification-display-control-user-interface-elements"></a>Gebruikers interface-elementen voor controle weergave

Hier volgen de Id's voor een [besturings element](display-control-verification.md) voor het controleren van de controle met de [pagina-indeling versie](page-layout.md) 2.1.0 of hoger.

| Id | Standaardwaarde |
| -- | ------------- |
|intro_msg <sup>*</sup>| Verificatie is nood zakelijk. Klik op de knop verzenden.|
|success_send_code_msg | De verificatie code is verzonden. Kopieer het naar het invoervak hieronder.|
|failure_send_code_msg | Er zijn problemen met het controleren van uw e-mail adres. Geef een geldig e-mail adres op en probeer het opnieuw.|
|success_verify_code_msg | E-mail adres geverifieerd. U kunt nu door gaan.|
|failure_verify_code_msg | Er zijn problemen met het controleren van uw e-mail adres. Probeer het opnieuw.|
|but_send_code | Verificatie code verzenden|
|but_verify_code | Code verifiëren|
|but_send_new_code | Nieuwe code verzenden|
|but_change_claims | E-mail wijzigen|

Opmerking: het `intro_msg` element is verborgen en wordt niet weer gegeven op de pagina zelf-bevestigingen. Gebruik het [HTML-customiztion](customize-ui-with-html.md) met Cascading Style Sheet om het zichtbaar te maken. Bijvoorbeeld:
    
```css
.verificationInfoText div{display: block!important}
```

### <a name="verification-display-control-example"></a>Controle voorbeeld voor verificatie weergave

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
   <!-- Display control UI elements-->
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_send_code_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_send_code_msg">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_verify_code_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_verify_code_msg">We are having trouble verifying your email address. Please try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_new_code">Send new code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_change_claims">Change e-mail</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="verification-display-control-user-interface-elements-deprecated"></a>Controle van gebruikers interface-elementen voor verificatie weergave (afgeschaft)

Hier volgen de Id's voor een [besturings element](display-control-verification.md) voor het controleren van de controle met de [pagina-indeling versie](page-layout.md) 2.0.0.

| Id | Standaardwaarde |
| -- | ------------- |
|verification_control_but_change_claims |Wijziging |
|verification_control_fail_send_code |Kan de code niet verzenden. Probeer het later opnieuw. |
|verification_control_fail_verify_code |De code kan niet worden geverifieerd. Probeer het later opnieuw. |
|verification_control_but_send_code |Code verzenden |
|verification_control_but_send_new_code |Nieuwe code verzenden |
|verification_control_but_verify_code |Code verifiëren |
|verification_control_code_sent| De verificatie code is verzonden. Kopieer het naar het invoervak hieronder. |

### <a name="verification-display-control-example-deprecated"></a>Voor beeld van controle voor beeld weergave (afgeschaft)

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_change_claims">Change</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_send_code">Failed to send the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_verify_code">Failed to verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_new_code">Send New Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_code_sent">Verification code has been sent. Please copy it to the input box below.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="restful-service-error-messages"></a>Fout berichten van de rest-service

Hier volgen de Id's voor het [ondersteunings fout bericht van het onderhouds service technisch profiel](restful-technical-profile.md) :

| Id | Standaardwaarde |
| -- | ------------- |
|DefaultUserMessageIfRequestFailed | Kan geen verbinding maken met een onderliggend service-eind punt. URL van de rest-service: {0} |
|UserMessageIfCircuitOpen | {0} URL van de rest-service: {1} |
|UserMessageIfDnsResolutionFailed | Het omzetten van de hostnaam van het rest-service-eind punt is mislukt. URL van de rest-service: {0} |
|UserMessageIfRequestTimeout | Kan geen verbinding tot stand brengen met een onderliggend service-eind punt binnen een time-outlimiet van {0} seconden. URL van de rest-service: {1} |


### <a name="restful-service-example"></a>Voor beeld van een rest-service

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultUserMessageIfRequestFailed">Failed to establish connection to restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCircuitOpen">Unable to connect to the restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfDnsResolutionFailed">Failed to resolve the hostname of the restful service endpoint.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfRequestTimeout">Failed to establish connection to restful service end point within timeout limit.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-mfa-error-messages"></a>Azure AD MFA-fout berichten

Hier volgen de Id's voor een fout bericht van het [technische profiel van Azure AD MFA](multi-factor-auth-technical-profile.md) :

| Id | Standaardwaarde |
| -- | ------------- |
|UserMessageIfCouldntSendSms | Kan geen SMS-berichten verzenden naar de telefoon. Probeer een ander telefoon nummer. |
|UserMessageIfInvalidFormat | Uw telefoon nummer heeft geen geldige indeling. Corrigeer dit en probeer het opnieuw.|
|UserMessageIfMaxAllowedCodeRetryReached | Onjuiste code is te vaak ingevoerd. Probeer het later opnieuw.|
|UserMessageIfServerError | Kan geen MFA-service gebruiken. Probeer het later opnieuw.|
|UserMessageIfThrottled | Uw aanvraag is beperkt. Probeer het later opnieuw.|
|UserMessageIfWrongCodeEntered|Er is een onjuiste code ingevoerd. Probeer het opnieuw.|

### <a name="azure-ad-mfa-example"></a>Azure AD MFA-voor beeld

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCouldntSendSms">Cannot Send SMS to the phone, please try another phone number.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidFormat">Your phone number is not in a valid format, please correct it and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxAllowedCodeRetryReached">Wrong code entered too many times, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfServerError">Cannot use MFA service, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">Your request has been throttled, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfWrongCodeEntered">Wrong code entered, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-sspr"></a>Azure AD SSPR

Hier volgen de Id's voor [Azure AD SSPR-technische profiel](aad-sspr-technical-profile.md) fout berichten:

| Id | Standaardwaarde |
| -- | ------------- |
|UserMessageIfChallengeExpired | De code is verlopen.|
|UserMessageIfInternalError | De e-mail service heeft een interne fout aangetroffen. Probeer het later opnieuw.|
|UserMessageIfThrottled | U hebt te veel aanvragen verzonden. Probeer het later opnieuw.|
|UserMessageIfVerificationFailedNoRetry | U hebt het maximum aantal verificatie pogingen overschreden.|
|UserMessageIfVerificationFailedRetryAllowed | De verificatie is mislukt, probeer het opnieuw.|


### <a name="azure-ad-sspr-example"></a>Azure AD SSPR-voor beeld

```XML
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInternalError">We are having trouble verifying your email address. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfChallengeExpired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedNoRetry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="one-time-password-error-messages"></a>Eenmalige fout berichten voor wacht woord

Hier volgen de Id's voor een [eenmalig wacht woord technische profiel](one-time-password-technical-profile.md) fout berichten

| Id | Standaardwaarde |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |Eenmalige verificatie van het wacht woord heeft het maximum aantal pogingen overschreden |
|UserMessageIfSessionDoesNotExist |Eenmalige sessie voor wachtwoord verificatie is verlopen |
|UserMessageIfSessionConflict |Eenmalige sessie voor wachtwoord verificatie is een conflict |
|UserMessageIfInvalidCode |Er is een onjuist wacht woord opgegeven voor de verificatie |
|UserMessageIfVerificationFailedRetryAllowed |Deze code is onjuist. Probeer het opnieuw. | 

### <a name="one-time-password-example"></a>Voor beeld van eenmalig wacht woord

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="claims-transformations-error-messages"></a>Fout berichten over claim transformaties

Hier volgen de Id's voor claim transformaties fout berichten:

| Id | Claimstransformatie | Standaardwaarde |
| -- | ------------- |------------- |
|UserMessageIfClaimsTransformationBooleanValueIsNotEqual |[AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) | Vergelijking van Boole-claim waarde is mislukt voor claim type "input claim".| 
|DateTimeGreaterThan |[AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan) | Vergelijking van claim waarde is mislukt: de gegeven linkeroperand is groter dan de rechter operand.|
|UserMessageIfClaimsTransformationStringsAreNotEqual |[AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal) | Het vergelijken van de claim waarde is mislukt met StringComparison ' OrdinalIgnoreCase '.|

### <a name="claims-transformations-example"></a>Voor beeld van claim transformaties

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Your email address hasn't been verified.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DateTimeGreaterThan">Expiration date must be greater that the current date.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationStringsAreNotEqual">The email entry fields do not match. Please enter the same email address in both fields and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor lokalisatie van voor beelden:

- [Taal aanpassing met aangepast beleid in Azure Active Directory B2C](language-customization.md)
- [Taal aanpassing met gebruikers stromen in Azure Active Directory B2C](language-customization.md)
