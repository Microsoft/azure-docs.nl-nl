---
title: Aangepaste kenmerken definiëren in Azure Active Directory B2C | Microsoft Docs
description: Aangepaste kenmerken definiëren voor uw toepassing in Azure Active Directory B2C om informatie over uw klanten te verzamelen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 17c73257db371bbec0c72a23b1303847a8d14102
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102607914"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Aangepaste kenmerken definiëren in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

In het artikel [claims toevoegen en gebruikers invoer aanpassen met behulp van aangepast beleid](configure-user-input.md) leert u hoe u ingebouwde [kenmerken van gebruikers profielen](user-profile-attributes.md)gebruikt. In dit artikel schakelt u een aangepast kenmerk in de map Azure Active Directory B2C (Azure AD B2C) in. Later kunt u het nieuwe kenmerk gebruiken als aangepaste claim in [gebruikers stromen](user-flow-overview.md) of [aangepaste beleids regels](custom-policy-get-started.md) tegelijk.

Uw Azure AD B2C-directory wordt geleverd met een [ingebouwde set kenmerken](user-profile-attributes.md). Het is echter vaak nodig om uw eigen kenmerken te maken voor het beheren van uw specifieke scenario, bijvoorbeeld wanneer:

* Een klant gerichte toepassing moet een **LoyaltyId** -kenmerk persistent maken.
* Een id-provider heeft een unieke gebruikers-id, **uniqueUserGUID**, die persistent moet zijn.
* Voor een aangepaste gebruikers traject moet de status van de gebruiker, **migrationStatus**, worden behouden, zodat er een andere logica kan worden uitgevoerd.

De *extensie-eigenschap*, het *aangepaste kenmerk* en de *aangepaste claim* verwijzen naar hetzelfde item in de context van dit artikel. De naam kan variëren, afhankelijk van de context, zoals toepassing, object of beleid.

Met Azure AD B2C kunt u de set met kenmerken die zijn opgeslagen op elk gebruikers account uitbreiden. U kunt deze kenmerken ook lezen en schrijven met behulp van de [Microsoft Graph-API](microsoft-graph-operations.md).

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-custom-attribute"></a>Een aangepast kenmerk maken

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door hiernaar over te schakelen rechtsboven in de Azure Portal. Selecteer de abonnementsgegevens en selecteer vervolgens **Schakelen tussen mappen**.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **gebruikers kenmerken** en selecteer vervolgens **toevoegen**.
1. Geef een **naam** op voor het aangepaste kenmerk (bijvoorbeeld ' ShoeSize ')
1. Kies een **gegevens type**. Alleen **String**, **Boolean** en **int** zijn beschikbaar.
1. Voer desgewenst een **Beschrijving** in voor informatieve doel einden.
1. Klik op **Create**.

Het aangepaste kenmerk is nu beschikbaar in de lijst met **gebruikers kenmerken** en voor gebruik in uw gebruikers stromen. Een aangepast kenmerk wordt alleen gemaakt wanneer het voor de eerste keer wordt gebruikt in een gebruikers stroom en niet wanneer u het toevoegt aan de lijst met **gebruikers kenmerken**.

::: zone pivot="b2c-user-flow"

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Een aangepast kenmerk gebruiken in uw gebruikers stroom

1. Selecteer in uw Azure AD B2C-Tenant **gebruikers stromen**.
1. Selecteer uw beleid (bijvoorbeeld ' B2C_1_SignupSignin ') om het te openen.
1. Selecteer **gebruikers kenmerken** en selecteer vervolgens het aangepaste kenmerk (bijvoorbeeld ' ShoeSize '). Klik op **Opslaan**.
1. Selecteer **toepassings claims** en selecteer vervolgens het aangepaste kenmerk.
1. Klik op **Opslaan**.

Zodra u een nieuwe gebruiker hebt gemaakt met behulp van een gebruikers stroom, die gebruikmaakt van het zojuist gemaakte aangepaste kenmerk, kan het object in [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)worden opgevraagd. U kunt ook de functie [gebruikers stroom uitvoeren](./tutorial-create-user-flows.md) op de gebruikers stroom gebruiken om de klant ervaring te controleren. Nu ziet u **ShoeSize** in de lijst met kenmerken die tijdens de registratie worden verzameld en wordt deze weer gegeven in het token dat wordt teruggestuurd naar uw toepassing.

::: zone-end

## <a name="azure-ad-b2c-extensions-app"></a>App voor Azure AD B2C extensies

Extensie kenmerken kunnen alleen worden geregistreerd voor een toepassings object, hoewel ze mogelijk gegevens voor een gebruiker bevatten. Het kenmerk extension is gekoppeld aan de toepassing `b2c-extensions-app` . Wijzig deze toepassing niet, omdat deze wordt gebruikt door Azure AD B2C om gebruikers gegevens op te slaan. U kunt deze toepassing vinden onder Azure AD B2C, app-registraties. De toepassings eigenschappen ophalen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **app-registraties** en selecteer vervolgens **alle toepassingen**.
1. Selecteer de `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.`-toepassing.
1. Kopieer de volgende id's naar het klem bord en sla ze op:
    * **Toepassings-id**. Bijvoorbeeld: `11111111-1111-1111-1111-111111111111`.
    * **Object-id**. Bijvoorbeeld: `22222222-2222-2222-2222-222222222222`.

::: zone pivot="b2c-custom-policy"

## <a name="modify-your-custom-policy"></a>Aangepast beleid wijzigen

Als u aangepaste kenmerken in uw beleid wilt inschakelen, geeft u de **toepassings-id** en toepassings **object-id** op in de AAD-Common technische profiel meta gegevens. Het *Aad-algemene* technische profiel is te vinden in het basis [Azure Active Directory](active-directory-technical-profile.md) technische profiel en biedt ondersteuning voor Azure AD-gebruikers beheer. Andere technische profielen van Azure AD bevatten de AAD-Common om de configuratie te benutten. Overschrijf het AAD-Common technische profiel in het extensie bestand.

1. Open het bestand extensies van uw beleid. Bijvoorbeeld <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Zoek het element ClaimsProviders. Voeg een nieuwe ClaimsProvider toe aan het ClaimsProviders-element.
1. Voeg de **toepassings-id** in die u eerder hebt opgenomen, tussen de openings- `<Item Key="ClientId">` en sluitings `</Item>` elementen.
1. Plaats de **ObjectID** van de toepassing die u eerder hebt opgenomen tussen de openings- `<Item Key="ApplicationObjectId">` en sluitings `</Item>` elementen.

    ```xml
    <!-- 
    <ClaimsProviders> -->
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
    <!-- 
    </ClaimsProviders> -->
    ```

## <a name="upload-your-custom-policy"></a>Uw aangepaste beleid uploaden

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map met uw Azure AD-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Azure AD B2C-Tenant bevat.
3. Kies linksboven in de Azure Portal **Alle services**, zoek **App-registraties** en selecteer deze.
4. Selecteer een **Framework voor identiteits ervaring**.
5. Selecteer **aangepast beleid uploaden** en upload de TrustFrameworkExtensions.xml-beleids bestanden die u hebt gewijzigd.

> [!NOTE]
> De eerste keer dat het technische profiel van Azure AD de claim naar de map persistent maakt, wordt gecontroleerd of het aangepaste kenmerk bestaat. Als dat niet het geval is, wordt het aangepaste kenmerk gemaakt.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Een aangepast kenmerk maken via Azure Portal

Dezelfde extensie kenmerken worden gedeeld door ingebouwde en aangepaste beleids regels. Wanneer u aangepaste kenmerken toevoegt via de portal-ervaring, worden deze kenmerken geregistreerd met behulp van de **B2C-extensies-app** die aanwezig zijn in elke B2C-Tenant.

U kunt deze kenmerken maken met behulp van de portal-gebruikers interface voor of nadat u ze in uw aangepaste beleids regels gebruikt. Wanneer u een kenmerk **loyaltyId** in de portal maakt, moet u dit als volgt:

|Name     |Gebruikt in |
|---------|---------|
|`extension_loyaltyId`  | Aangepast beleid|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](microsoft-graph-operations.md)|

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

::: zone-end

## <a name="using-custom-attribute-with-ms-graph-api"></a>Aangepast kenmerk gebruiken met MS Graph API

Microsoft Graph-API ondersteunt het maken en bijwerken van een gebruiker met extensie kenmerken. Extensie kenmerken in de Graph API worden genoemd met behulp van de Conventie `extension_ApplicationClientID_attributename` , waarbij de de `ApplicationClientID` toepassing is van de **toepassings-id (client)** van de `b2c-extensions-app` toepassing. Houd er rekening mee dat de ID van de **toepassing (client)** zoals deze wordt weer gegeven in de naam van het uitbreidings kenmerk geen afbreek streepjes bevat. Bijvoorbeeld:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyId": "212342" 
``` 

## <a name="next-steps"></a>Volgende stappen

Volg de richt lijnen voor het [toevoegen van claims en het aanpassen van gebruikers invoer met aangepast beleid](configure-user-input.md). In dit voor beeld wordt een ingebouwde claim ' City ' gebruikt. Als u een aangepast kenmerk wilt gebruiken, vervangt u ' City ' door uw eigen aangepaste kenmerken.
