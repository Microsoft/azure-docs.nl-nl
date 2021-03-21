---
title: Aanmelden met de telefoon en aanmelden met aangepast beleid
titleSuffix: Azure AD B2C
description: Eenmalige wacht woorden (OTP) in tekst berichten verzenden naar de telefoons van uw toepassings gebruikers met aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2600ea3488c643bcf215b058425de42cd439dcff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98660264"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c"></a>Stel aanmelding via de telefoon in en meld u aan met aangepast beleid in Azure AD B2C

Met aanmelden en aanmelden in Azure Active Directory B2C (Azure AD B2C) kunnen uw gebruikers zich registreren en aanmelden bij uw toepassingen met behulp van een eenmalig wacht woord (OTP) dat in een tekst bericht naar de telefoon wordt verzonden. Eenmalige wacht woorden kunnen u helpen het risico te verkleinen dat uw gebruikers hun wacht woord verg eten of het probleem hebben aangetast.

Volg de stappen in dit artikel om het aangepaste beleid te gebruiken zodat uw klanten zich kunnen registreren en aanmelden bij uw toepassingen met behulp van een eenmalig wacht woord dat naar hun telefoon wordt verzonden.

## <a name="pricing"></a>Prijzen

Eenmalige wacht woorden worden naar uw gebruikers verzonden met behulp van SMS-berichten en er worden kosten in rekening gebracht voor elk bericht dat wordt verzonden. Zie de sectie **afzonderlijke kosten** van [Azure Active Directory B2C prijzen](https://azure.microsoft.com/pricing/details/active-directory-b2c/)voor prijs informatie.

## <a name="user-experience-for-phone-sign-up-and-sign-in"></a>Gebruikers ervaring voor het registreren van de telefoon en het aanmelden

Met de telefoon registratie en aanmelding kan de gebruiker zich aanmelden voor de app met behulp van een telefoon nummer als de primaire id. De ervaring van de eind gebruiker tijdens het aanmelden en aanmelden wordt hieronder beschreven.

> [!NOTE]
> We raden u ten zeerste aan om informatie over toestemming op te nemen in uw registratie-en aanmeldings ervaring, vergelijkbaar met de onderstaande voorbeeld tekst. Deze voorbeeld tekst is alleen ter informatie bedoeld. Raadpleeg de korte hand leiding voor code controle op de [website van CTIA](https://www.ctia.org/programs) en neem contact op met uw eigen juridische of compliance-experts voor hulp bij de uiteindelijke configuratie van tekst en onderdelen om te voldoen aan uw eigen vereisten voor naleving:
>
> *Als u uw telefoon nummer opgeeft, stemt u in met het ontvangen van een eenmalige wachtwoord code die door SMS wordt verzonden om u aan te melden bij *&lt; het invoegen: de &gt; naam van uw toepassing*. Standaard bericht-en gegevens tarieven kunnen van toepassing zijn.*
>
> *&lt;invoegen: een koppeling naar de privacyverklaring&gt;*<br/>*&lt;invoegen: een koppeling naar uw service voorwaarden&gt;*

Pas het volgende voor beeld aan om uw eigen toestemming gegevens toe te voegen. Neem deze op in de `LocalizedResources` voor de ContentDefinition die wordt gebruikt door de zelfbevestigende pagina met het besturings element voor weer gave (het *Phone_Email_Base.xml* bestand in het hulp programma voor aanmelding bij de [telefoon en het Starter Pack voor aanmelden][starter-pack-phone]):

```xml
<LocalizedResources Id="phoneSignUp.en">        
    <LocalizedStrings>
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_msg_intro">By providing your phone number, you consent to receiving a one-time passcode sent by text message to help you sign into {insert your application name}. Standard message and data rates may apply.</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_text">Privacy Statement</LocalizedString>                
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_url">{insert your privacy statement URL}</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_text">Terms and Conditions</LocalizedString>             
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_url">{insert your terms and conditions URL}</LocalizedString>          
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please verify your country code and phone number</LocalizedString>        
    </LocalizedStrings>      
</LocalizedResources>
   ```

### <a name="phone-sign-up-experience"></a>Aanmeldings ervaring voor de telefoon

Als de gebruiker nog geen account voor uw toepassing heeft, kan deze er een maken door de koppeling **nu registreren** te kiezen. Er wordt een aanmeldings pagina weer gegeven, waar de gebruiker hun **land** selecteert, het telefoon nummer invoert en **code verzenden** selecteert.

![Aanmelding van de gebruiker wordt gestart](media/phone-authentication/phone-signup-start.png)

Er wordt een eenmalige verificatie code verzonden naar het telefoon nummer van de gebruiker. De gebruiker voert de **verificatie code** op de registratie pagina in en selecteert vervolgens **code controleren**. (Als de gebruiker de code niet kan ophalen, kunnen ze **nieuwe code verzenden** selecteren.)

![De gebruiker verifieert code tijdens de telefoon registratie](media/phone-authentication/phone-signup-verify-code.png)

De gebruiker voert andere gevraagde informatie op de registratie pagina in. De **weergave naam**, de voor **naam** en de naam **(land** en telefoon nummer blijven bijvoorbeeld ingevuld). Als de gebruiker een ander telefoon nummer wil gebruiken, kunnen ze het **wijzigings nummer** kiezen om de aanmelding opnieuw te starten. Wanneer u klaar bent, selecteert de gebruiker **door gaan**.

![Gebruiker bevat aanvullende informatie](media/phone-authentication/phone-signup-additional-info.png)

Vervolgens wordt de gebruiker gevraagd een herstel bericht op te geven. De gebruiker voert het e-mail adres in en selecteert vervolgens **verificatie code verzenden**. Er wordt een code verzonden naar het postvak in van het e-mail adres van de gebruiker, die ze kunnen ophalen en invoeren in het vak **verificatie code** . Vervolgens selecteert de gebruiker **code verifiëren**. 

Zodra de code is geverifieerd, selecteert de gebruiker **maken** om hun account te maken. Of als de gebruiker een ander e-mail adres wil gebruiken, kunnen ze de optie **E-mail wijzigen** kiezen.

![Gebruiker maakt account](media/phone-authentication/email-verification.png)

### <a name="phone-sign-in-experience"></a>Aanmeldings ervaring voor de telefoon

Als de gebruiker een bestaand account met een telefoon nummer als id heeft, voert de gebruiker het telefoon nummer in en wordt **door gaan** geselecteerd. Ze bevestigen het land en telefoon nummer door **door gaan** te selecteren en er wordt een eenmalige verificatie code naar de telefoon verzonden. De gebruiker voert de verificatie code in en selecteert aanmelden **blijven** .

![Gebruikers ervaring voor aanmelding via de telefoon](media/phone-authentication/phone-signin-screens.png)

## <a name="deleting-a-user-account"></a>Een gebruikers account verwijderen

In bepaalde gevallen moet u een gebruiker en de bijbehorende gegevens uit uw Azure AD B2C Directory verwijderen. Raadpleeg [deze instructies](/microsoft-365/compliance/gdpr-dsr-azure#step-5-delete)voor meer informatie over het verwijderen van een gebruikers account via de Azure Portal. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]



## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources nodig voordat u OTP kunt instellen.

* [Azure AD B2C-tenant](tutorial-create-tenant.md)
* [Webtoepassing geregistreerd](tutorial-register-applications.md) in uw Tenant
* [Aangepast beleid](custom-policy-get-started.md) dat is geüpload naar uw Tenant

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Het Start pakket voor aanmelding via de telefoon &

Begin met het bijwerken van de aangepaste beleids bestanden voor het registreren van de telefoon en het aanmelden om met uw Azure AD B2C-Tenant te werken.

1. Zoek de [aangepaste beleids bestanden][starter-pack-phone] voor het registreren van de telefoon en het aanmelden in uw lokale kloon van de opslag plaats van het Start pakket, of down load deze rechtstreeks. De XML-beleids bestanden bevinden zich in de volgende map:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. Vervang in elk bestand de teken reeks door `yourtenant` de naam van uw Azure AD B2C-Tenant. Als de naam van uw B2C-Tenant bijvoorbeeld *contosob2c* is, worden alle exemplaren van `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com` .

1. Volg de stappen in de sectie [toepassings-Id's toevoegen aan het aangepaste beleid](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) van aan de [slag met aangepast beleid in azure Active Directory B2C](custom-policy-get-started.md). In dit geval moet `/phone-number-passwordless/` **`Phone_Email_Base.xml`** u bijwerken met de **toepassings-id's (client)** van de twee toepassingen die u hebt geregistreerd bij het volt ooien van de vereisten, *IdentityExperienceFramework* en *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>De beleids bestanden uploaden

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw Azure AD B2C-Tenant.
1. Onder **beleids regels** selecteert u **identiteits ervaring-Framework**.
1. Selecteer **aangepast beleid uploaden**.
1. Upload de beleids bestanden in de volgende volg orde:
    1. *Phone_Email_Base.xml*
    1. *SignUpOrSignInWithPhone.xml*
    1. *SignUpOrSignInWithPhoneOrEmail.xml*
    1. *ProfileEditPhoneOnly.xml*
    1. *ProfileEditPhoneEmail.xml*
    1. *ChangePhoneNumber.xml*
    1. *PasswordResetEmail.xml*

Wanneer u elk bestand uploadt, voegt Azure het voor voegsel toe `B2C_1A_` .

## <a name="test-the-custom-policy"></a>Het aangepaste beleid testen

1. Selecteer **B2C_1A_SignUpOrSignInWithPhone** onder **aangepast beleid**.
1. Onder **toepassing selecteren** selecteert u de *webapp1* -toepassing die u hebt geregistreerd bij het volt ooien van de vereisten.
1. Kies bij **Selecteer antwoord**-URL `https://jwt.ms` .
1. Selecteer **nu uitvoeren** en meld u aan met een e-mail adres of telefoon nummer.
1. Selecteer **nu opnieuw uitvoeren** en meld u aan met hetzelfde account om te controleren of u de juiste configuratie hebt.

## <a name="get-user-account-by-phone-number"></a>Gebruikers account op telefoon nummer ophalen

Een gebruiker die zich aanmeldt met een telefoon nummer zonder een herstel-e-mail adres wordt vastgelegd in uw Azure AD B2C Directory met hun telefoon nummer als aanmeldings naam. Als u het telefoon nummer wilt wijzigen, moet uw Help Desk of ondersteunings team eerst hun account vinden en vervolgens hun telefoon nummer bijwerken.

U kunt een gebruiker vinden op basis van hun telefoon nummer (aanmeldings naam) door gebruik te maken van [Microsoft Graph](microsoft-graph-operations.md):

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Bijvoorbeeld:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>Volgende stappen

U kunt het aangepaste beleid voor het registreren van de telefoon en het aanmelden van het Starter Pack (en andere Starter Packs) vinden op GitHub: [Azure-samples/Active-Directory-B2C-Custom-Policy-starterpack/scenarios/telefoon nummer-wacht woord][starter-pack-phone] voor de Starter Pack-beleids bestanden gebruiken multi-factor Authentication-technische profielen en claim transformaties voor telefoon nummers:
* [Een Azure AD Multi-Factor Authentication technisch profiel definiëren](multi-factor-auth-technical-profile.md)
* [Claim transformaties voor telefoon nummers definiëren](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
