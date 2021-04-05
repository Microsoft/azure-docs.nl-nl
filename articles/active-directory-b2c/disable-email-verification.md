---
title: E-mail verificatie uitschakelen tijdens het aanmelden van de klant
titleSuffix: Azure AD B2C
description: Meer informatie over het uitschakelen van e-mail verificatie tijdens het aanmelden van een klant in Azure Active Directory B2C.
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
ms.openlocfilehash: 0f70c8d501a7d56f4bc29e0f2b065760cad625e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97585017"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>E-mail verificatie uitschakelen tijdens het aanmelden van de klant in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) controleert standaard het e-mail adres van uw klant voor lokale accounts (accounts voor gebruikers die zich aanmelden met een e-mail adres of gebruikers naam). Azure AD B2C zorgt voor geldige e-mail adressen door klanten te verplichten te verifiëren tijdens het registratie proces. Ook wordt voor komen dat kwaad aardige actors geautomatiseerde processen gebruiken om frauduleuze accounts in uw toepassingen te genereren.

Sommige toepassings ontwikkelaars geven de voor keur aan een e-mail verificatie overs Laan tijdens het registratie proces. ook kunnen klanten hun e-mail adres later verifiëren. Ter ondersteuning hiervan kan Azure AD B2C worden geconfigureerd om e-mail verificatie uit te scha kelen. Dit maakt een probleemloos registratie proces en biedt ontwikkel aars de flexibiliteit om klanten te onderscheiden die hun e-mail adres van klanten hebben gecontroleerd.

> [!WARNING]
> Als u e-mail verificatie in het registratie proces uitschakelt, kan dit leiden tot spam. Als u de standaard verificatie van e-mail berichten voor Azure AD B2C uitschakelt, wordt u aangeraden een vervangend verificatie systeem te implementeren.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]
## <a name="disable-email-verification"></a>E-mailverificatie uitschakelen

::: zone pivot="b2c-user-flow"

Volg deze stappen om de e-mail verificatie uit te scha kelen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com)
1. Gebruik het filter voor **adres lijst en abonnementen** in het bovenste menu om de map te selecteren die uw Azure AD B2C Tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **gebruikers stromen**.
1. Selecteer de gebruikers stroom waarvoor u de e-mail verificatie wilt uitschakelen. Bijvoorbeeld *B2C_1_signinsignup*.
1. Selecteer **pagina-indelingen**.
1. Selecteer de **pagina voor het registreren van een lokaal account**.
1. Onder **gebruikers kenmerken** selecteert u **e-mail adres**.
1. Selecteer in de vervolg keuzelijst **verificatie vereist** de optie **Nee**.
1. Selecteer **Opslaan**. E-mail verificatie is nu uitgeschakeld voor deze gebruikers stroom.

::: zone-end

::: zone pivot="b2c-custom-policy"
Het technische profiel voor **LocalAccountSignUpWithLogonEmail** is een [zelfbevestigend](self-asserted-technical-profile.md), dat wordt aangeroepen tijdens de registratie stroom. Als u de e-mail verificatie wilt uitschakelen, stelt `EnforceEmailVerification` u de meta gegevens in op ONWAAR. Overschrijf de technische profielen van LocalAccountSignUpWithLogonEmail in het extensie bestand. 

1. Open het bestand extensies van uw beleid. Bijvoorbeeld <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Het `ClaimsProviders` element zoeken. Als het element niet bestaat, voegt u het toe.
1. Voeg de volgende claim provider toe aan het- `ClaimsProviders` element:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-policy"></a>Uw beleid testen 

1. Meld u aan bij [Azure Portal](https://portal.azure.com)
1. Gebruik het filter voor **adres lijst en abonnementen** in het bovenste menu om de map te selecteren die uw Azure AD B2C Tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **gebruikers stromen**.
1. Selecteer de gebruikers stroom waarvoor u de e-mail verificatie wilt uitschakelen. Bijvoorbeeld *B2C_1_signinsignup*.
1. Als u het beleid wilt testen, selecteert u **gebruikers stroom uitvoeren**.
1. Selecteer voor **toepassing** de webtoepassing met de naam *testapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Klik op **gebruikers stroom uitvoeren**
1. U moet zich kunnen aanmelden met een e-mail adres zonder de validatie.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Het Relying Party bestand bijwerken en testen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD-tenant bevat door in het bovenste menu op het filter **Map en abonnement** te klikken en de map te kiezen waarin de Azure AD-tenant zich bevindt.
1. Kies linksboven in de Azure Portal **Alle services**, zoek **App-registraties** en selecteer deze.
1. Selecteer een **Framework voor identiteits ervaring**.
1. Selecteer **aangepast beleid uploaden** en upload vervolgens de twee beleids bestanden die u hebt gewijzigd.
1. Selecteer het registratie-of aanmeldings beleid dat u hebt geüpload en klik op de knop **nu uitvoeren** .
1. U moet zich kunnen aanmelden met een e-mail adres zonder de validatie.

::: zone-end


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het aanpassen van de gebruikers interface in azure Active Directory B2C](customize-ui-with-html.md)

