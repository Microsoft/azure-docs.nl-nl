---
title: Aangepaste e-mailverificatie met SendGrid
titleSuffix: Azure AD B2C
description: Leer hoe u kunt integreren met SendGrid om de verificatie-e-mail aan te passen die naar uw klanten wordt verzonden wanneer ze zich registreren, zodat ze uw Azure AD B2C toepassingen kunnen gebruiken.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d63e7916423038e53c375b2be4114582cf4d6152
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725760"
---
# <a name="custom-email-verification-with-sendgrid"></a>Aangepaste e-mailverificatie met SendGrid

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Gebruik aangepaste e-mail in Azure Active Directory B2C (Azure AD B2C) om aangepaste e-mail te verzenden naar gebruikers die zich registreren om uw toepassingen te gebruiken. Met behulp van de externe e-mailprovider SendGrid kunt u uw eigen e-mailsjabloon en *Van:* adres en onderwerp gebruiken, evenals ondersteuning voor lokalisatie en aangepaste OTP-instellingen (one-time password).

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Voor aangepaste e-mailverificatie is het gebruik van een externe e-mailprovider vereist, zoals [SendGrid,](https://sendgrid.com) [Mailprovider](https://Mailjet.com)of [SparkPost,](https://sparkpost.com)een aangepaste REST API of een HTTP-e-mailprovider (inclusief uw eigen provider). In dit artikel wordt beschreven hoe u een oplossing instelt die gebruikmaakt van SendGrid.

## <a name="create-a-sendgrid-account"></a>Een SendGrid-account maken

Als u nog geen account hebt, begint u met het instellen van een SendGrid-account (Azure-klanten kunnen elke maand 25.000 gratis e-mails ontgrendelen). Zie de sectie Een [SendGrid-account](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) maken van E-mail verzenden met [SendGrid met Azure voor installatie-instructies.](../sendgrid-dotnet-how-to-send-email.md)

Zorg ervoor dat u de sectie voltooit waarin u [een SendGrid API-sleutel maakt.](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key) Neem de API-sleutel op voor gebruik in een latere stap.

> [!IMPORTANT]
> SendGrid biedt klanten de mogelijkheid om e-mailberichten te verzenden vanaf gedeelde IP-adressen en [toegewezen IP-adressen.](https://sendgrid.com/docs/ui/account-and-settings/dedicated-ip-addresses/) Wanneer u toegewezen IP-adressen gebruikt, moet u uw eigen reputatie goed opbouwen met het opwarmen van een IP-adres. Zie Een [IP-adres up up voor meer informatie.](https://sendgrid.com/docs/ui/sending-email/warming-up-an-ip-address/)

## <a name="create-azure-ad-b2c-policy-key"></a>Een Azure AD B2C maken

Sla vervolgens de SendGrid-API-sleutel op in een Azure AD B2C-beleidssleutel waarnaar uw beleid moet verwijzen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C tenant. Selecteer het **filter Map en abonnement** in het bovenste menu en kies uw Azure AD B2C map.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer op de pagina Overzicht **de optie Identity Experience Framework**.
1. Selecteer **Beleidssleutels** en selecteer vervolgens **Toevoegen.**
1. Kies **handmatig** bij **Opties.**
1. Voer een **naam in** voor de beleidssleutel. Bijvoorbeeld `SendGridSecret`. Het `B2C_1A_` voorvoegsel wordt automatisch toegevoegd aan de naam van uw sleutel.
1. Voer **in Geheim** de SendGrid-API-sleutel in die u eerder hebt genoteerd.
1. Bij **Sleutelgebruik selecteert** u **Handtekening.**
1. Selecteer **Maken**.

## <a name="create-sendgrid-template"></a>SendGrid-sjabloon maken

Nu een SendGrid-account is gemaakt en de SendGrid API-sleutel is opgeslagen in een Azure AD B2C-beleidssleutel, maakt u een dynamische [transactionele sendGrid-sjabloon](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/).

1. Open op de SendGrid-site de [pagina transactionele sjablonen](https://sendgrid.com/dynamic_templates) en selecteer **Sjabloon maken.**
1. Voer een unieke sjabloonnaam in, `Verification email` zoals en selecteer vervolgens **Opslaan.**
1. Als u de nieuwe sjabloon wilt bewerken, selecteert **u Versie toevoegen.**
1. Selecteer **Code-editor** en vervolgens **Doorgaan.**
1. Plak de volgende HTML-sjabloon in de HTML-editor of gebruik uw eigen HTML-sjabloon. De parameters en worden dynamisch vervangen door de waarde van een een `{{otp}}` keer wachtwoord en het `{{email}}` e-mailadres van de gebruiker.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
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
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
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

1. Vouw **Instellingen aan** de linkerkant uit en voer bij **E-mailonderwerp** `{{subject}}` in.
1. Selecteer **Sjabloon opslaan.**
1. Ga terug naar **de pagina Transactionele sjablonen** door de pijl-terug te selecteren.
1. Neem de **id op** van de sjabloon die u hebt gemaakt voor gebruik in een latere stap. Bijvoorbeeld `d-989077fbba9746e89f3f6411f596fb96`. U geeft deze id op wanneer u [de claimtransformatie toevoegt.](#add-the-claims-transformation)

## <a name="add-azure-ad-b2c-claim-types"></a>Claimtypen Azure AD B2C toevoegen

Voeg in uw beleid de volgende claimtypen toe aan het `<ClaimsSchema>` element binnen `<BuildingBlocks>` .

Deze claimtypen zijn nodig om het e-mailadres te genereren en te verifiëren met behulp van een OTP-code (one-time password).

```xml
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="Otp">
      <DisplayName>Secondary One-time password</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="emailRequestBody">
      <DisplayName>SendGrid request body</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="VerificationCode">
      <DisplayName>Secondary Verification Code</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Enter your email verification code</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

## <a name="add-the-claims-transformation"></a>De claimtransformatie toevoegen

Vervolgens hebt u een claimtransformatie nodig om een JSON-tekenreeksclaim uit te geven die de body is van de aanvraag die naar SendGrid wordt verzonden.

De structuur van het JSON-object wordt gedefinieerd door de -ID's in punt-notatie van de InputParameters en de TransformationClaimTypes van de InputClaims. Getallen in de punt notatie impliceren matrices. De waarden zijn afkomstig van de waarden van InputClaims en de eigenschappen 'Value' van InputParameters. Zie JSON claims transformations (JSON-claimtransformaties) voor meer informatie over [JSON-claimtransformaties.](json-transformations.md)

Voeg de volgende claimtransformatie toe aan het `<ClaimsTransformations>` element in `<BuildingBlocks>` . Maak de volgende updates voor het XML-bestand voor claimtransformatie:

* Werk de waarde inputParameter bij met de id van de transactionele SendGrid-sjabloon die u eerder hebt gemaakt `template_id` in [SendGrid-sjabloon maken.](#create-sendgrid-template)
* Werk de `from.email` adreswaarde bij. Gebruik een geldig e-mailadres om te voorkomen dat de verificatie-e-mail wordt gemarkeerd als spam.
* Werk de waarde van de `personalizations.0.dynamic_template_data.subject` onderwerpregelinvoerparameter bij met een onderwerpregel die geschikt is voor uw organisatie.

```xml
<!-- 
<BuildingBlocks>
  <ClaimsTransformations> -->
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
      </InputClaims>
      <InputParameters>
        <!-- Update the template_id value with the ID of your SendGrid template. -->
        <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
        <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
        <!-- Update with a subject line appropriate for your organization. -->
        <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
      </OutputClaims>
    </ClaimsTransformation>
  <!--
  </ClaimsTransformations>
</BuildingBlocks> -->
```

## <a name="add-datauri-content-definition"></a>Definitie van DataUri-inhoud toevoegen

Voeg onder de claimtransformaties in de volgende ContentDefinition toe om te verwijzen naar de gegevens-URI van versie `<BuildingBlocks>` 2.1.2: [](contentdefinitions.md)

```xml
<!--
<BuildingBlocks> -->
  <ContentDefinitions>
   <ContentDefinition Id="api.localaccountsignup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountpasswordreset">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
  </ContentDefinitions>
<!--
</BuildingBlocks> -->
```

## <a name="create-a-displaycontrol"></a>Een DisplayControl maken

Er wordt een besturingselement voor het weergeven van verificaties gebruikt om het e-mailadres te verifiëren met een verificatiecode die naar de gebruiker wordt verzonden.

Dit voorbeeld van een weergavebesturingselement is geconfigureerd voor:

1. Verzamel het `email` type adresclaim van de gebruiker.
1. Wacht tot de gebruiker het `verificationCode` claimtype heeft verstrekt met de code die naar de gebruiker is verzonden.
1. Ga terug `email` naar het zelfver bevestigde technische profiel dat een verwijzing naar dit weergavebesturingselement heeft.
1. Genereer met `SendCode` behulp van de actie een OTP-code en verzend een e-mailbericht met de OTP-code naar de gebruiker.

![E-mailactie verificatiecode verzenden](media/custom-email-sendgrid/display-control-verification-email-action-01.png)

Voeg onder inhoudsdefinities, nog steeds binnen `<BuildingBlocks>` , de volgende [DisplayControl](display-controls.md) van het type [VerificationControl toe](display-control-verification.md) aan uw beleid.

```xml
<!--
<BuildingBlocks> -->
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
<!--
</BuildingBlocks> -->
```

## <a name="add-otp-technical-profiles"></a>Technische OTP-profielen toevoegen

Het `GenerateOtp` technische profiel genereert een code voor het e-mailadres. Het `VerifyOtp` technische profiel verifieert de code die is gekoppeld aan het e-mailadres. U kunt de configuratie van de indeling en de vervaldatum van het een time-wachtwoord wijzigen. Zie Define a one-time password technical profile (Een technisch profiel voor een [eendtijdswachtwoord](one-time-password-technical-profile.md)definiëren) voor meer informatie over technische OTP-profielen.

> [!NOTE]
> OTP-codes die worden gegenereerd door het protocol Web.TPEngine.Providers.OneTimePasswordProtocolProvider, zijn gekoppeld aan de browsersessie. Dit betekent dat een gebruiker unieke OTP-codes kan genereren in verschillende browsersessies die elk geldig zijn voor de bijbehorende sessies. Een OTP-code die wordt gegenereerd door de ingebouwde gebruikersstroom is daarentegen onafhankelijk van de browsersessie, dus als een gebruiker een nieuwe OTP-code genereert in een nieuwe browsersessie, wordt de vorige OTP-code vervangen.

Voeg de volgende technische profielen toe aan het `<ClaimsProviders>` -element.

```xml
<!--
<ClaimsProviders> -->
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
          <Item Key="NumRetryAttempts">5</Item>
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
<!--
</ClaimsProviders> -->
```

## <a name="add-a-rest-api-technical-profile"></a>Een technisch REST API toevoegen

Met REST API technische profiel wordt de e-mailinhoud gegenereerd (met behulp van de SendGrid-indeling). Zie Define a RESTful technical profile (Een technisch RESTful-profiel definiëren) voor meer informatie over [technische RESTful-profielen.](restful-technical-profile.md)

Voeg net als bij de technische OTP-profielen de volgende technische profielen toe aan het `<ClaimsProviders>` -element.

```xml
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendOtp">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">emailRequestBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
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

## <a name="make-a-reference-to-the-displaycontrol"></a>Een verwijzing naar DisplayControl maken

Voeg in de laatste stap een verwijzing toe naar de DisplayControl die u hebt gemaakt. Vervang uw bestaande `LocalAccountSignUpWithLogonEmail` en `LocalAccountDiscoveryUsingEmailAddress` zelf-bevestigde technische profielen door het volgende. Als u een eerdere versie van het Azure AD B2C gebruikt. Deze technische profielen gebruiken `DisplayClaims` met een verwijzing naar DisplayControl.

Zie Zelf-bevestigd technisch [profiel en](restful-technical-profile.md) [DisplayControl](display-controls.md)voor meer informatie.

```xml
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

## <a name="optional-localize-your-email"></a>[Optioneel] Uw e-mail lokaliseren

Als u het e-mailbericht wilt lokaliseren, moet u gelokaliseerde tekenreeksen verzenden naar SendGrid of uw e-mailprovider. U kunt bijvoorbeeld het onderwerp, de hoofdcode, het codebericht of de handtekening van het e-mailbericht lokaliseren. Als u dit wilt doen, kunt u de transformatie van [GetLocalizedStringsTransformation-claims](string-transformations.md) gebruiken om gelokaliseerde tekenreeksen naar claimtypen te kopiëren. De `GenerateEmailRequestBody` claimtransformatie, waarmee de JSON-nettolading wordt gegenereerd, maakt gebruik van invoerclaims die de gelokaliseerde tekenreeksen bevatten.

1. Definieer in uw beleid de volgende tekenreeksclaims: onderwerp, bericht, codeIntro en handtekening.
1. Definieer [een transformatie voor GetLocalizedStringsTransformation-claims](string-transformations.md) om gelokaliseerde tekenreekswaarden in de claims uit stap 1 te vervangen.
1. Wijzig de `GenerateEmailRequestBody` claimtransformatie om invoerclaims te gebruiken met het volgende XML-fragment.
1. Werk uw SendGrid-sjabloon bij om dynamische parameters te gebruiken in plaats van alle tekenreeksen die worden gelokaliseerd door Azure AD B2C.

    ```xml
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
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
        <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="personalizations.0.dynamic_template_data.subject" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
        <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="personalizations.0.dynamic_template_data.message" />
        <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="personalizations.0.dynamic_template_data.codeIntro" />
        <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="personalizations.0.dynamic_template_data.signature" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="template_id" DataType="string" Value="d-1234567890" />
        <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
    ```

1. Voeg het volgende [lokalisatie-element](localization.md) toe.

    ```xml
    <!--
    <BuildingBlocks> -->
      <Localization Enabled="true">
        <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
          <SupportedLanguage>en</SupportedLanguage>
          <SupportedLanguage>es</SupportedLanguage>
        </SupportedLanguages>
        <LocalizedResources Id="api.custom-email.en">
          <LocalizedStrings>
            <!--Email template parameters-->
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for validating the account</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
          </LocalizedStrings>
        </LocalizedResources>
        <LocalizedResources Id="api.custom-email.es">
          <LocalizedStrings>
            <!--Email template parameters-->
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sinceramente</LocalizedString>
          </LocalizedStrings>
        </LocalizedResources>
      </Localization>
    <!--
    </BuildingBlocks> -->
    ```

1. Voeg verwijzingen toe naar de LocalizedResources-elementen door het element [ContentDefinitions bij te](contentdefinitions.md) werken.

    ```XML
    <!--
    <BuildingBlocks> -->
      <ContentDefinitions>
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
          <LocalizedResourcesReferences MergeBehavior="Prepend">
            <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
            <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
          </LocalizedResourcesReferences>
        </ContentDefinition>
        <ContentDefinition Id="api.localaccountpasswordreset">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
          <LocalizedResourcesReferences MergeBehavior="Prepend">
            <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
            <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
          </LocalizedResourcesReferences>
        </ContentDefinition>
      </ContentDefinitions>
    <!--
    </BuildingBlocks> -->
    ```

1. Voeg ten slotte de volgende transformatie van invoerclaims toe aan de `LocalAccountSignUpWithLogonEmail` technische `LocalAccountDiscoveryUsingEmailAddress` profielen en .

    ```XML
    <InputClaimsTransformations>
      <InputClaimsTransformation ReferenceId="GetLocalizedStringsForEmail" />
    </InputClaimsTransformations>
    ```
    
## <a name="optional-localize-the-ui"></a>[Optioneel] De gebruikersinterface lokaliseren

Met het element Lokalisatie kunt u meerdere talen of talen in het beleid ondersteunen voor de gebruikerstrajecten. Met de lokalisatieondersteuning in beleidsregels kunt u taalspecifieke tekenreeksen opgeven voor zowel de elementen van de gebruikersinterface [voor](localization-string-ids.md#verification-display-control-user-interface-elements)controle als foutberichten met een [wachtwoord voor één keer.](localization-string-ids.md#one-time-password-error-messages) Voeg de volgende LocalizedString toe aan uw LocalizedResources. 

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

Nadat u de gelokaliseerde tekenreeksen hebt toevoegen, verwijdert u de metagegevens van de OTP-validatiefoutberichten uit de technische profielen LocalAccountSignUpWithLogonEmail en LocalAccountDiscoveryUsingEmailAddress.

## <a name="next-steps"></a>Volgende stappen

U vindt een voorbeeld van een aangepast beleid voor e-mailverificatie op GitHub:

- [Aangepaste e-mailverificatie - DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)
- Zie Define a RESTful technical profile in an Azure AD B2C [custom policy (Een technisch RESTful-profiel](restful-technical-profile.md)definiëren in een Azure AD B2C beleid) voor meer informatie over het gebruik van een aangepaste REST API of een HTTP-gebaseerde SMTP-e-mailprovider.

::: zone-end
