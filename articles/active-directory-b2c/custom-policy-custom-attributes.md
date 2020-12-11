---
title: Uw eigen kenmerken toevoegen aan aangepaste beleids regels
titleSuffix: Azure AD B2C
description: Een overzicht van het gebruik van extensie-eigenschappen en aangepaste kenmerken en het opnemen ervan in de gebruikers interface.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9ec99558f3a168b770ad19fb4f6c811a31c44f08
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108873"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Azure Active Directory B2C: aangepaste kenmerken in een aangepast profiel beleid inschakelen

In het artikel [claims toevoegen en gebruikers invoer aanpassen met behulp van aangepast beleid](configure-user-input.md) leert u hoe u ingebouwde [kenmerken van gebruikers profielen](user-profile-attributes.md)gebruikt. In dit artikel schakelt u een aangepast kenmerk in de map Azure Active Directory B2C (Azure AD B2C) in. Later kunt u het nieuwe kenmerk gebruiken als aangepaste claim in [gebruikers stromen](user-flow-overview.md) of [aangepaste beleids regels](custom-policy-get-started.md) tegelijk.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>Vereisten

Volg de stappen in het artikel [Azure Active Directory B2C: aan de slag met aangepast beleid](custom-policy-get-started.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>Aangepaste kenmerken gebruiken om informatie over uw klanten te verzamelen 

Uw Azure AD B2C Directory wordt geleverd met een [ingebouwde set kenmerken](user-profile-attributes.md). Het is echter vaak nodig om uw eigen kenmerken te maken voor het beheren van uw specifieke scenario, bijvoorbeeld wanneer:

* Een klant gerichte toepassing moet een **LoyaltyId** -kenmerk persistent maken.
* Een id-provider heeft een unieke gebruikers-id, **uniqueUserGUID**, die persistent moet zijn.
* Voor een aangepaste gebruikers traject moet de status van de gebruiker, **migrationStatus**, worden behouden, zodat er een andere logica kan worden uitgevoerd.

Met Azure AD B2C kunt u de set met kenmerken die zijn opgeslagen op elk gebruikers account uitbreiden. U kunt deze kenmerken ook lezen en schrijven met behulp van de [Microsoft Graph-API](manage-user-accounts-graph-api.md).

## <a name="azure-ad-b2c-extensions-app"></a>App voor Azure AD B2C extensies

Extensie kenmerken kunnen alleen worden geregistreerd voor een toepassings object, hoewel ze mogelijk gegevens voor een gebruiker bevatten. Het kenmerk extension is gekoppeld aan de toepassing met de naam B2C-Extensions-app. Wijzig deze toepassing niet, omdat deze wordt gebruikt door Azure AD B2C om gebruikers gegevens op te slaan. U kunt deze toepassing vinden onder Azure AD B2C, app-registraties.

De *extensie-eigenschap*, het *aangepaste kenmerk* en de *aangepaste claim* verwijzen naar hetzelfde item in de context van dit artikel. De naam kan variëren, afhankelijk van de context, zoals toepassing, object of beleid.

## <a name="get-the-application-properties"></a>De toepassings eigenschappen ophalen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **app-registraties** en selecteer vervolgens **alle toepassingen**.
1. Selecteer de `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.`-toepassing.
1. Kopieer de volgende id's naar het klem bord en sla ze op:
    * **Toepassings-id**. Bijvoorbeeld: `11111111-1111-1111-1111-111111111111`.
    * **Object-id**. Bijvoorbeeld: `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>Aangepast beleid wijzigen

Als u aangepaste kenmerken in uw beleid wilt inschakelen, geeft u de **toepassings-id** en toepassings **object-id** op in de AAD-Common technische profiel meta gegevens. Het *Aad-algemene* technische profiel is te vinden in het basis [Azure Active Directory](active-directory-technical-profile.md) technische profiel en biedt ondersteuning voor Azure AD-gebruikers beheer. Andere technische profielen van Azure AD bevatten de AAD-Common om de configuratie te benutten. Overschrijf het AAD-Common technische profiel in het extensie bestand.

1. Open het bestand extensies van uw beleid. Bijvoorbeeld <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Zoek het element ClaimsProviders. Voeg een nieuwe ClaimsProvider toe aan het ClaimsProviders-element.
1. Vervang door `ApplicationObjectId` de object-id die u eerder hebt vastgelegd. Vervang vervolgens door `ClientId` de toepassings-id die u eerder in het onderstaande fragment hebt vastgelegd.

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-Common">
          <Metadata>
            <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
            <Item Key="ClientId"></Item>
            <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
            <Item Key="ApplicationObjectId"></Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>Uw aangepaste beleid uploaden

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Zorg ervoor dat u de map met uw Azure AD-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Azure AD B2C-Tenant bevat.
3. Kies linksboven in de Azure Portal **Alle services**, zoek **App-registraties** en selecteer deze.
4. Selecteer een **Framework voor identiteits ervaring**.
5. Selecteer **aangepast beleid uploaden** en upload de TrustFrameworkExtensions.xml-beleids bestanden die u hebt gewijzigd.

> [!NOTE]
> De eerste keer dat het technische profiel van Azure AD de claim naar de map persistent maakt, wordt gecontroleerd of het aangepaste kenmerk bestaat. Als dat niet het geval is, wordt het aangepaste kenmerk gemaakt.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Een aangepast kenmerk maken via Azure Portal

Dezelfde extensie kenmerken worden gedeeld door ingebouwde en aangepaste beleids regels. Wanneer u aangepaste kenmerken toevoegt via de portal-ervaring, worden deze kenmerken geregistreerd met behulp van de **B2C-extensies-app** die aanwezig zijn in elke B2C-Tenant.

U kunt deze kenmerken maken met behulp van de portal-gebruikers interface voor of nadat u ze in uw aangepaste beleids regels gebruikt. Volg de richt lijnen voor het [definiëren van aangepaste kenmerken in azure Active Directory B2C](user-flow-custom-attributes.md). Wanneer u een kenmerk **loyaltyId** in de portal maakt, moet u dit als volgt:

|Name     |Gebruikt in |
|---------|---------|
|`extension_loyaltyId`  | Aangepast beleid|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](manage-user-accounts-graph-api.md)|

In het volgende voor beeld ziet u hoe aangepaste kenmerken in een Azure AD B2C aangepaste beleids claim definitie worden gebruikt.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

In het volgende voor beeld ziet u het gebruik van een aangepast kenmerk in Azure AD B2C aangepast beleid in een technisch profiel, invoer, uitvoer en persistente claims.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

## <a name="use-a-custom-attribute-in-a-policy"></a>Een aangepast kenmerk gebruiken in een beleid

Volg de richt lijnen voor het [toevoegen van claims en het aanpassen van gebruikers invoer met aangepast beleid](configure-user-input.md). In dit voor beeld wordt een ingebouwde claim ' City ' gebruikt. Als u een aangepast kenmerk wilt gebruiken, vervangt u ' City ' door uw eigen aangepaste kenmerken.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over:

- [Kenmerken van Azure AD B2C gebruikers profiel](user-profile-attributes.md)
- [Definitie van extensie kenmerken](user-profile-attributes.md#extension-attributes)
- [Azure AD B2C gebruikers accounts beheren met Microsoft Graph](manage-user-accounts-graph-api.md)
