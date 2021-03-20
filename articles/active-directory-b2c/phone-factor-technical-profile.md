---
title: Een technisch profiel voor telefoon factor in een aangepast beleid definiëren
titleSuffix: Azure AD B2C
description: Definieer een telefoon factor technisch profiel in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 322e4b78fbfb38f1822fb7a7cdcdbfcc0738b303
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91950394"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een technisch profiel voor telefoon factor definiëren in een Azure Active Directory B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor het registreren en verifiëren van telefoon nummers. Dit technische profiel:

- Biedt een gebruikers interface om te communiceren met de gebruiker om een telefoon nummer te verifiëren of in te schrijven.
- Ondersteunt telefoon gesprekken en SMS-berichten om het telefoon nummer te valideren.
- Ondersteunt meerdere telefoon nummers. De gebruiker kan een van de telefoon nummers selecteren om te verifiëren.  
- Retourneert een claim die aangeeft of de gebruiker een nieuw telefoon nummer heeft gegeven. U kunt deze claim gebruiken om te bepalen of het telefoon nummer moet worden bewaard in het Azure AD B2C gebruikers profiel.  
- Maakt gebruik van een [inhouds definitie](contentdefinitions.md) om het uiterlijk te bepalen.

## <a name="protocol"></a>Protocol

Het **naam** kenmerk van het **protocol** element moet worden ingesteld op `Proprietary` . Het kenmerk **handler** moet de volledig gekwalificeerde naam van de assembly van de protocolhandler bevatten die wordt gebruikt door Azure AD B2C voor de telefoon factor: `Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

In het volgende voor beeld ziet u een technisch profiel voor telefoon factor voor inschrijving en validatie:

```xml
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>Invoer van claim transformaties

Het InputClaimsTransformations-element kan een verzameling invoer claim transformaties bevatten die worden gebruikt voor het wijzigen van de invoer claims of nieuwe genereren. Met de volgende invoer claims transformatie `UserId` wordt een claim gegenereerd die later in de claim verzameling voor invoer wordt gebruikt.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>Invoer claims

Het element InputClaims moet de volgende claims bevatten. U kunt de naam van uw claim ook toewijzen aan de naam die is gedefinieerd in het technische profiel voor telefoon factor. 

|  Gegevenstype| Vereist | Beschrijving |
| --------- | -------- | ----------- | 
| tekenreeks| Ja | Een unieke id voor de gebruiker. De claim naam of PartnerClaimType moet worden ingesteld op `UserId` . Deze claim mag geen persoonlijke gegevens bevatten.|
| tekenreeks| Ja | Lijst met claim typen. Elke claim bevat één telefoon nummer. Als een van de invoer claims geen telefoon nummer bevat, wordt de gebruiker gevraagd om een nieuw telefoon nummer in te schrijven en te verifiëren. Het gevalideerde telefoon nummer wordt geretourneerd als een uitvoer claim. Als een van de invoer claims een telefoon nummer bevat, wordt de gebruiker gevraagd om dit te verifiëren. Als meerdere invoer claims een telefoon nummer bevatten, wordt de gebruiker gevraagd een van de telefoon nummers te kiezen en te controleren. |

In het volgende voor beeld ziet u hoe u meerdere telefoon nummers gebruikt. Zie [voorbeeld beleid](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa)voor meer informatie.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>Uitvoer claims

Het OutputClaims-element bevat een lijst met claims die worden geretourneerd door het technische profiel voor de telefoon factor.

|  Gegevenstype| Vereist | Beschrijving |
|  -------- | ----------- |----------- |
| booleaans | Ja | Hiermee wordt aangegeven of het nieuwe telefoon nummer door de gebruiker is ingevoerd. De claim naam of PartnerClaimType moet zijn ingesteld op `newPhoneNumberEntered`|
| tekenreeks| Ja | Het geverifieerde telefoon nummer. De claim naam of PartnerClaimType moet worden ingesteld op `Verified.OfficePhone` .|

Het OutputClaimsTransformations-element kan een verzameling OutputClaimsTransformation-elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of nieuwe genereren.

## <a name="cryptographic-keys"></a>Cryptografische sleutels

Het element **CryptographicKeys** wordt niet gebruikt.


## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Ja | De id van de [inhouds definitie](contentdefinitions.md) die aan dit technische profiel is gekoppeld. |
| ManualPhoneNumberEntryAllowed| Nee | Opgeven of een gebruiker een telefoon nummer hand matig mag invoeren. Mogelijke waarden: `true` , of `false` (standaard).|
| setting. authenticationMode | Nee | De methode voor het valideren van het telefoon nummer. Mogelijke waarden: `sms` , `phone` , of `mixed` (standaard).|
| instelling. automatisch kiezen| Nee| Geef op of het technische profiel automatisch moet worden gekozen of automatisch een SMS mag verzenden. Mogelijke waarden: `true` , of `false` (standaard). Voor automatisch kiezen moet de `setting.authenticationMode` meta gegevens zijn ingesteld op `sms` of `phone` . De invoerende claim verzameling moet één telefoon nummer hebben. |

### <a name="ui-elements"></a>UI-elementen

De gebruikers interface-elementen van de telefoon Factor Authentication-pagina kunnen worden [gelokaliseerd](localization-string-ids.md#phone-factor-authentication-page-user-interface-elements).

## <a name="next-steps"></a>Volgende stappen

- Controleer de [sociale en lokale accounts met MFA](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) Starter Pack.
