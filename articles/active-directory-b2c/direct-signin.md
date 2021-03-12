---
title: Direct aanmelden instellen met behulp van Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over het vooraf invullen van de aanmeldings naam of het direct omleiden naar een sociale ID-provider.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 35e8efa269ab72477b06e86824d368d0a3dced03
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103197325"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Direct aanmelden instellen met behulp van Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Wanneer u aanmelden voor uw toepassing instelt met behulp van Azure Active Directory (AD) B2C, kunt u de aanmeldings naam of de directe aanmelding vooraf invullen bij een specifieke ID-provider voor sociale netwerken, zoals Facebook, LinkedIn of een Microsoft-account.

## <a name="prepopulate-the-sign-in-name"></a>Vul de aanmeldings naam vooraf in

Tijdens een traject voor aanmeldings gebruikers kan een Relying Party toepassing een specifieke gebruiker of domein naam aanrichten. Bij het instellen van een gebruiker kan een toepassing, in de autorisatie aanvraag, de `login_hint` query parameter met de aanmeldings naam van de gebruiker opgeven. Azure AD B2C vult de aanmeldings naam automatisch in, terwijl de gebruiker alleen het wacht woord hoeft op te geven.

![Aanmeldings pagina voor aanmelding met login_hint query parameter gemarkeerd in URL](./media/direct-signin/login-hint.png)

De gebruiker kan de waarde in het tekstvak voor aanmelden wijzigen.

::: zone pivot="b2c-custom-policy"

U kunt het technische profiel overschrijven om de hint voor het aanmelden te ondersteunen `SelfAsserted-LocalAccountSignin-Email` . Stel in de `<InputClaims>` sectie de DefaultValue van de signInName-claim in op `{OIDC:LoginHint}` . De `{OIDC:LoginHint}` variabele bevat de waarde van de `login_hint` para meter. Azure AD B2C leest de waarde van de claim signInName en vult het tekstvak signInName vooraf in.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <InputClaims>
        <!-- Add the login hint value to the sign-in names claim type -->
        <InputClaim ClaimTypeReferenceId="signInName" DefaultValue="{OIDC:LoginHint}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end

## <a name="redirect-sign-in-to-a-social-provider"></a>Aanmelding door sturen naar een sociale provider

Als u de aanmeldings traject hebt geconfigureerd zodat uw toepassing sociale accounts kan bevatten, zoals Facebook, LinkedIn of Google, kunt u de `domain_hint` para meter opgeven. Deze query parameter biedt een hint voor het Azure AD B2C over de ID-provider voor sociale netwerken die moet worden gebruikt voor aanmelding. Als de toepassing bijvoorbeeld `domain_hint=facebook.com` is opgegeven, wordt de aanmelding direct naar de aanmeldings pagina van Facebook verzonden.

![Aanmeldings pagina voor aanmelding met domain_hint query parameter gemarkeerd in URL](./media/direct-signin/domain-hint.png)

::: zone pivot="b2c-user-flow"

De query teken reeks parameter domein hint kan worden ingesteld op een van de volgende domeinen:

- amazon.com
- facebook.com
- github.com
- google.com
- linkedin.com
- microsoft.com
- qq.com
- twitter.com
- wechat.com
- weibo.com 
- Zie [domein Hint](identity-provider-generic-openid-connect.md#response-mode)voor [generic OpenID Connect Connect](identity-provider-generic-openid-connect.md).

::: zone-end

::: zone pivot="b2c-custom-policy"

Ter ondersteuning van de para meter domein Hint kunt u de domein naam configureren met behulp `<Domain>domain name</Domain>` van het XML-element van elke `<ClaimsProvider>` .

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```

::: zone-end

