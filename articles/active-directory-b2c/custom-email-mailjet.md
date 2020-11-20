---
title: Aangepaste e-mail verificatie met Mailjet
titleSuffix: Azure AD B2C
description: Leer hoe u kunt integreren met Mailjet om de verificatie-e-mail die naar uw klanten wordt verzonden, aan te passen wanneer deze zich aanmeldt om uw Azure AD B2C-toepassingen te gebruiken.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b74de2bdf1f6239f1006c820579a336946939421
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949578"
---
# <a name="custom-email-verification-with-mailjet"></a>Aangepaste e-mail verificatie met Mailjet

Gebruik aangepaste e-mail in Azure Active Directory B2C (Azure AD B2C) om aangepaste e-mail te verzenden naar gebruikers die zich registreren voor het gebruik van uw toepassingen. Met behulp van [DisplayControls](display-controls.md) (momenteel in Preview) en de e-mail provider Mailjet van derden kunt u uw eigen e-mail sjabloon gebruiken, *van:* adres en onderwerp, en ondersteuning bieden voor lokalisatie en aangepaste otp-instellingen (eenmalig wacht woord).

Voor aangepaste e-mail verificatie is het gebruik van een e-mail provider van derden, zoals [Mailjet](https://Mailjet.com), [SendGrid](./custom-email-sendgrid.md)of [SparkPost](https://sparkpost.com), een aangepaste rest API, of een op http gebaseerde e-mail provider (inclusief uw eigen) vereist. In dit artikel wordt beschreven hoe u een oplossing instelt die gebruikmaakt van Mailjet.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-mailjet-account"></a>Een Mailjet-account maken

Als u er nog geen hebt, moet u eerst een Mailjet-account instellen (Azure-klanten kunnen 6.000 e-mails ontgrendelen met een limiet van 200 e-mails per dag). 

1. Volg de instructies in de installatie van [een Mailjet-account maken](https://www.mailjet.com/guides/azure-mailjet-developer-resource-user-guide/enabling-mailjet/).
1. Om e-mail berichten te kunnen verzenden, moet u het e-mail adres of domein van de afzender [registreren en valideren](https://www.mailjet.com/guides/azure-mailjet-developer-resource-user-guide/enabling-mailjet/#how-to-configure-mailjet-for-use) .
2. Navigeer naar de [pagina API-sleutel beheer](https://app.mailjet.com/account/api_keys). Noteer de **API-sleutel** en de **geheime sleutel** voor gebruik in een latere stap. Beide sleutels worden automatisch gegenereerd wanneer uw account wordt gemaakt.  

## <a name="create-azure-ad-b2c-policy-key"></a>Azure AD B2C-beleids sleutel maken

Sla vervolgens de Mailjet API-sleutel op in een Azure AD B2C-beleids sleutel voor uw beleids regels waarnaar moet worden verwezen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies uw Azure AD B2C Directory.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer op de pagina **overzicht** **identiteits ervaring-Framework**.
1. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
1. Kies voor **Opties** de optie **hand matig**.
1. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld `MailjetApiKey`. Het voor voegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
1. Voer in het **geheim** uw Mailjet- **API-sleutel** in die u eerder hebt vastgelegd.
1. Selecteer voor **sleutel gebruik** **hand tekening**.
1. Selecteer **Maken**.
1. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
1. Kies voor **Opties** de optie **hand matig**.
1. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld `MailjetSecretKey`. Het voor voegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
1. Voer in het **geheim** uw geheime Mailjet- **sleutel** in die u eerder hebt vastgelegd.
1. Selecteer voor **sleutel gebruik** **hand tekening**.
1. Selecteer **Maken**.

## <a name="create-a-mailjet-template"></a>Een Mailjet-sjabloon maken

Als er een Mailjet-account is gemaakt en de Mailjet-API-sleutel die is opgeslagen in een Azure AD B2C-beleids sleutel, maakt u een Mailjet- [sjabloon voor dynamische transactionele acties](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/).

1. Open de pagina [transactionele sjablonen](https://app.mailjet.com/templates/transactional) op de site Mailjet en selecteer **een nieuwe sjabloon maken**.
1. Selecteer **deze optie door deze te coderen in HTML** en vervolgens **code** te selecteren.
1. Voer een unieke sjabloon naam in `Verification email` , zoals, en selecteer vervolgens **maken**.
1. Plak in de HTML-editor de volgende HTML-sjabloon of gebruik uw eigen. De `{{var:otp:""}}` `{{var:email:""}}` para meters en worden dynamisch vervangen door de waarde voor eenmalig wacht woord en het e-mail adres van de gebruiker.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{var:email:""}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{var:otp:""}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. **Bewerkings onderwerp** aan de linkerkant uitbreiden
    1. Voer bij **onderwerp** een standaard waarde in voor het onderwerp. Mailjet gebruikt deze waarde wanneer de API geen onderwerp-para meter bevat.
    1. Typ de naam van uw bedrijf voor de **naam**.
    1. Selecteer voor het **adres** uw e-mail adres
    1. Selecteer **Opslaan**.
1. Selecteer in de rechter bovenhoek **opslaan & publiceren** en klik vervolgens op **Ja, wijzigingen publiceren**
1. Noteer de **sjabloon-id** van de sjabloon die u hebt gemaakt voor gebruik in een latere stap. U geeft deze ID op wanneer u [de claim transformatie toevoegt](#add-the-claims-transformation).


## <a name="add-azure-ad-b2c-claim-types"></a>Azure AD B2C claim typen toevoegen

Voeg in uw beleid de volgende claim typen toe aan het- `<ClaimsSchema>` element binnen `<BuildingBlocks>` .

Deze claim typen zijn nodig voor het genereren en verifiëren van het e-mail adres met een OTP-code (eenmalig wacht woord).

```XML
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="emailRequestBody">
  <DisplayName>Mailjet request body</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="VerificationCode">
  <DisplayName>Secondary Verification Code</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter your email verification code</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

## <a name="add-the-claims-transformation"></a>De claim transformatie toevoegen

Vervolgens hebt u een claim transformatie nodig voor het uitvoeren van een JSON-teken reeks claim die de hoofd tekst van de aanvraag is die wordt verzonden naar Mailjet.

De structuur van het JSON-object wordt gedefinieerd door de Id's in punt notatie van de invoer parameters en de TransformationClaimTypes van de InputClaims. Getallen in de punt notatie impliceren matrices. De waarden zijn afkomstig van de waarden van de InputClaims en de waarde van de eigenschap input parameters. Zie voor meer informatie over JSON-claim transformaties [JSON-claim transformaties](json-transformations.md).

Voeg de volgende claim transformatie toe aan het- `<ClaimsTransformations>` element binnen `<BuildingBlocks>` . Voer de volgende updates uit voor de XML van de claim transformatie:

* Werk de `Messages.0.TemplateID` waarde input parameter bij met de id van de transactionele sjabloon voor Mailjet die u eerder hebt gemaakt in [een Mailjet-sjabloon maken](#create-a-mailjet-template).
* Werk de `Messages.0.From.Email` adres waarde bij. Gebruik een geldig e-mail adres om te voor komen dat de verificatie-e-mail als spam wordt gemarkeerd.
* Werk de waarde van de `Messages.0.Subject` invoer parameter voor de regel onderwerp bij met een onderwerpregel die geschikt is voor uw organisatie.

```XML
<ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.To.0.Email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="Messages.0.Variables.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.Variables.email" />
  </InputClaims>
  <InputParameters>
    <!-- Update the template_id value with the ID of your Mailjet template. -->
    <InputParameter Id="Messages.0.TemplateID" DataType="int" Value="1234567"/>
    <InputParameter Id="Messages.0.TemplateLanguage" DataType="boolean" Value="true"/>

    <!-- Update with an email appropriate for your organization. -->
    <InputParameter Id="Messages.0.From.Email" DataType="string" Value="my_email@mydomain.com"/>

    <!-- Update with a subject line appropriate for your organization. -->
    <InputParameter Id="Messages.0.Subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>Definitie van DataUri-inhoud toevoegen

Voeg onder de claim transformaties binnen de `<BuildingBlocks>` volgende [ContentDefinition](contentdefinitions.md) toe om te verwijzen naar de gegevens-URI van versie 2.1.0:

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>Een DisplayControl maken

Een besturings element voor controle wordt gebruikt om het e-mail adres te verifiëren met een verificatie code die wordt verzonden naar de gebruiker.

Dit voor beeld wordt weer gegeven met de volgende instellingen:

1. Verzamel het `email` adres claim type van de gebruiker.
1. Wacht totdat de gebruiker het `verificationCode` claim type heeft verstrekt met de code die naar de gebruiker wordt verzonden.
1. Ga terug `email` naar het zelf-bebevestigde technische profiel met een verwijzing naar dit weergave besturings element.
1. `SendCode`Genereer een otp-code met behulp van de actie en verzend een e-mail met de otp-code naar de gebruiker.

   ![E-mail actie voor verificatie code verzenden](media/custom-email-mailjet/display-control-verification-email-action-01.png)

Voeg onder inhouds definities nog steeds `<BuildingBlocks>` de volgende [DisplayControl](display-controls.md) van het type [VerificationControl](display-control-verification.md) toe aan uw beleid.

```XML
<DisplayControls>
  <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
    <DisplayClaims>
      <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
      <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
    </DisplayClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="email" />
    </OutputClaims>
    <Actions>
      <Action Id="SendCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendOtp" />
        </ValidationClaimsExchange>
      </Action>
      <Action Id="VerifyCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
        </ValidationClaimsExchange>
      </Action>
    </Actions>
  </DisplayControl>
</DisplayControls>
```

## <a name="add-otp-technical-profiles"></a>Technische OTP-profielen toevoegen

Het `GenerateOtp` technische profiel genereert een code voor het e-mail adres. Het `VerifyOtp` technische profiel controleert de code die is gekoppeld aan het e-mail adres. U kunt de configuratie van de indeling en de verval datum van het eenmalige wacht woord wijzigen. Zie [een eenmalig wacht woord technische profiel definiëren](one-time-password-technical-profile.md)voor meer informatie over technische otp-profielen.

Voeg de volgende technische profielen toe aan het- `<ClaimsProviders>` element.

```XML
<ClaimsProvider>
  <DisplayName>One time password technical profiles</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="GenerateOtp">
      <DisplayName>Generate one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">1200</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="ReuseSameCode">true</Item>
        <Item Key="MaxNumAttempts">5</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
      </OutputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="VerifyOtp">
      <DisplayName>Verify one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">VerifyCode</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
      </InputClaims>
    </TechnicalProfile>
   </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="add-a-rest-api-technical-profile"></a>Een REST API technisch profiel toevoegen

In dit REST API technische profiel wordt de e-mail inhoud gegenereerd (met de Mailjet-indeling). Zie een onderliggend [technisch profiel definiëren](restful-technical-profile.md)voor meer informatie over onderhouds technische profielen.

Net als bij de technische profielen voor OTP, voegt u de volgende technische profielen toe aan het- `<ClaimsProviders>` element.

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="sendOtp">
      <DisplayName>Use email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.mailjet.com/v3.1/send</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">emailRequestBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_MailjetApiKey" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_MailjetSecretKey" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateEmailRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="emailRequestBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Een verwijzing naar de DisplayControl maken

In de laatste stap voegt u een verwijzing toe naar het DisplayControl dat u hebt gemaakt. Vervang uw bestaande `LocalAccountSignUpWithLogonEmail` en `LocalAccountDiscoveryUsingEmailAddress` zelfondertekende technische profielen door de volgende. Als u een eerdere versie van Azure AD B2C-beleid hebt gebruikt. Deze technische profielen `DisplayClaims` worden gebruikt met een verwijzing naar de DisplayControl..

Zie voor meer informatie [het zelf beweringen technisch profiel](restful-technical-profile.md) en weer [gave](display-controls.md).

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="LocalAccountDiscoveryUsingEmailAddress">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
      </DisplayClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>Beschrijving Uw e-mail adres lokaliseren

Als u het e-mail bericht wilt lokaliseren, moet u gelokaliseerde teken reeksen verzenden naar Mailjet of uw e-mail provider. U kunt bijvoorbeeld het onderwerp van de e-mail, de hoofd tekst, het code bericht of de hand tekening van de e-mail lokaliseren. Hiervoor kunt u de [GetLocalizedStringsTransformation](string-transformations.md) -claim transformatie gebruiken om gelokaliseerde teken reeksen te kopiëren naar claim typen. De `GenerateEmailRequestBody` claim transformatie, waarmee de JSON-nettolading wordt gegenereerd, gebruikt invoer claims die de gelokaliseerde teken reeksen bevatten.

1. In uw beleid definieert u de volgende teken reeks claims: onderwerp, bericht, codeIntro en hand tekening.
1. Definieer een [GetLocalizedStringsTransformation](string-transformations.md) -claim transformatie om gelokaliseerde teken reeks waarden in de claims uit stap 1 te vervangen.
1. Wijzig de `GenerateEmailRequestBody` claim transformatie voor het gebruik van invoer claims met het volgende XML-code fragment.
1. Werk uw Mailjet-sjabloon bij om dynamische para meters te gebruiken in plaats van alle teken reeksen die worden gelokaliseerd door Azure AD B2C.

    ```XML
    <ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
        <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
        <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
        <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.To.0.Email" />
        <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="Messages.0.Subject" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="Messages.0.Variables.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.Variables.email" />
        <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="Messages.0.Variables.otpmessage" />
        <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="Messages.0.Variables.otpcodeIntro" />
        <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="Messages.0.Variables.otpsignature" />
      </InputClaims>
      <InputParameters>
        <!-- Update the template_id value with the ID of your Mailjet template. -->
        <InputParameter Id="Messages.0.TemplateID" DataType="int" Value="1234567"/>
        <InputParameter Id="Messages.0.TemplateLanguage" DataType="boolean" Value="true"/>

        <!-- Update with an email appropriate for your organization. -->
        <InputParameter Id="Messages.0.From.Email" DataType="string" Value="my_email@mydomain.com"/>
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
      </OutputClaims>
    </ClaimsTransformation>
    ```

1. Voeg het volgende [lokalisatie](localization.md) element toe.

    ```xml
    <Localization Enabled="true">
      <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
        <SupportedLanguage>en</SupportedLanguage>
        <SupportedLanguage>es</SupportedLanguage>
      </SupportedLanguages>
      <LocalizedResources Id="api.custom-email.en">
        <LocalizedStrings>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for validating the account</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
        </LocalizedStrings>
        </LocalizedStrings>
      </LocalizedResources>
      <LocalizedResources Id="api.custom-email.es">
        <LocalizedStrings>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sinceramente</LocalizedString>
        </LocalizedStrings>
      </LocalizedResources>
    </Localization>
    ```

1. Voeg referenties toe aan de LocalizedResources-elementen door het element [ContentDefinitions](contentdefinitions.md) bij te werken.

    ```xml
    <ContentDefinitions>
      <ContentDefinition Id="api.localaccountsignup">
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri>
        <LocalizedResourcesReferences MergeBehavior="Prepend">
          <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
          <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
        </LocalizedResourcesReferences>
      </ContentDefinition>
      <ContentDefinition Id="api.localaccountpasswordreset">
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri>
        <LocalizedResourcesReferences MergeBehavior="Prepend">
          <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
          <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
        </LocalizedResourcesReferences>
      </ContentDefinition>
    </ContentDefinitions>
    ```

1. Voeg ten slotte de volgende invoer claims-trans formatie toe aan de `LocalAccountSignUpWithLogonEmail` `LocalAccountDiscoveryUsingEmailAddress` technische profielen en.

    ```xml
    <InputClaimsTransformations>
      <InputClaimsTransformation ReferenceId="GetLocalizedStringsForEmail" />
    </InputClaimsTransformations>
    ```
    
## <a name="optional-localize-the-ui"></a>Beschrijving De gebruikers interface lokaliseren

Het lokalisatie-element biedt ondersteuning voor meerdere land instellingen of talen in het beleid voor de gebruikers ritten. Met de lokalisatie ondersteuning in beleids regels kunt u taalspecifieke teken reeksen opgeven voor de [gebruikers interface-elementen van controle weergave](localization-string-ids.md#verification-display-control-user-interface-elements)en [eenmalige wachtwoord fout berichten](localization-string-ids.md#one-time-password-error-messages). Voeg de volgende LocalizedString toe aan uw LocalizedResources. 

```XML
<LocalizedResources Id="api.custom-email.en">
  <LocalizedStrings>
    ...
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
    <!-- Claims-->
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="DisplayName">Verification Code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="UserHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="AdminHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Eamil</LocalizedString>
    <!-- Email validation error messages-->
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">The verification has failed, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

Nadat u de gelokaliseerde teken reeksen hebt toegevoegd, verwijdert u de meta gegevens van het OTP-validatie fout bericht uit de technische profielen LocalAccountSignUpWithLogonEmail en LocalAccountDiscoveryUsingEmailAddress.

## <a name="next-steps"></a>Volgende stappen

U kunt een voor beeld van een aangepast e-mail verificatie beleid vinden op GitHub:

- [Aangepaste e-mail verificatie-DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)
- Voor informatie over het gebruik van een aangepaste REST API of een SMTP-e-mail provider op basis van HTTP, raadpleegt u een onderliggend [technisch profiel definiëren in een Azure AD B2C aangepast beleid](restful-technical-profile.md).
