---
title: Een aanmeldings stroom instellen
titleSuffix: Azure Active Directory B2C
description: Meer informatie over het instellen van een aanmeldings stroom in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/04/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2310bd39c39036b6d6ac919517fa5539d7b70779
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581861"
---
# <a name="set-up-a-sign-in-flow-in-azure-active-directory-b2c"></a>Een aanmeldings stroom instellen in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-in-flow-overview"></a>Overzicht van aanmeldings stroom

Met het aanmeldings beleid kunnen gebruikers het volgende doen: 

* Gebruikers kunnen zich aanmelden met een Azure AD B2C lokaal account
* Registreren of aanmelden met een sociaal account
* Wachtwoord opnieuw instellen
* Gebruikers kunnen zich niet aanmelden voor een Azure AD B2C lokaal account. Een beheerder kan [Azure Portal](manage-users-portal.md#create-a-consumer-user)of [MS Graph API](microsoft-graph-operations.md)gebruiken om een account te maken.

![Bewerkings stroom voor profielen](./media/add-sign-in-policy/sign-in-user-flow.png)

## <a name="prerequisites"></a>Vereisten

Als u dit nog niet hebt gedaan, [registreert u een webtoepassing in azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-in-user-flow"></a>Een aanmeldings gebruikers stroom maken

Aanmeldings beleid toevoegen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Selecteer onder **Beleid** de optie **Gebruikersstromen** en selecteer vervolgens **Nieuwe gebruikersstroom**.
1. Selecteer op de pagina **een gebruikers stroom maken** de stroom voor het **Aanmelden van** de gebruiker.
1. Onder **Selecteer een versie** selecteert u **Aanbevolen** en vervolgens **Maken**. ([Meer informatie](user-flow-versions.md) over gebruikersstroomversies.)
1. Voer een **Naam** in voor de gebruikersstroom. Bijvoorbeeld *signupsignin1*.
1. Voor **id-providers** selecteert u **e-mail aanmelding**.
1. Voor **toepassings claims** kiest u de claims en kenmerken die u naar uw toepassing wilt verzenden. Selecteer bijvoorbeeld **meer weer geven** en kies vervolgens kenmerken en claims voor de **weergave naam**, de voor naam, de voor **waarde** en **de object-id van de gebruiker**. Klik op **OK**.
1. Klik op **Maken** om de gebruikersstroom toe te voegen. Het voorvoegsel *B2C_1* wordt automatisch voor de naam geplaatst.

### <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Selecteer de gebruikersstroom die u hebt gemaakt om de overzichtspagina te openen en selecteer **Gebruikersstroom uitvoeren**.
1. Selecteer voor **Toepassing** de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Klik op **gebruikers stroom uitvoeren**.
1. U moet zich kunnen aanmelden met het account dat u hebt gemaakt (met behulp van MS Graph API), zonder de registratie koppeling. Het geretourneerde token bevat de claims die u hebt geselecteerd.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="remove-the-sign-up-link"></a>De registratie koppeling verwijderen

Het technische profiel van de **SelfAsserted-LocalAccountSignin-e-mail** is een [zelfbevestigend](self-asserted-technical-profile.md), die wordt aangeroepen tijdens de registratie-of aanmeldings stroom. Als u de registratie koppeling wilt verwijderen, stelt `setting.showSignupLink` u de meta gegevens in op `false` . Overschrijf de technische profielen van de SelfAsserted-LocalAccountSignin-e-mail in het extensie bestand. 

1. Open het bestand extensies van uw beleid. Bijvoorbeeld _`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**_ .
1. Het `ClaimsProviders` element zoeken. Als het element niet bestaat, voegt u het toe.
1. Voeg de volgende claim provider toe aan het- `ClaimsProviders` element:

    ```xml
    <!--
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Local Account</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
            <Metadata>
              <Item Key="setting.showSignupLink">false</Item>
            </Metadata>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!--
    </ClaimsProviders> -->
    ```

1. `<BuildingBlocks>`Voeg binnen element de volgende [ContentDefinition](contentdefinitions.md) toe om te verwijzen naar de versie 1.2.0 of de nieuwere gegevens-URI:

    ```XML
    <!-- 
    <BuildingBlocks> 
      <ContentDefinitions>-->
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
        </ContentDefinition>
      <!--
      </ContentDefinitions>
    </BuildingBlocks> -->
    ```

## <a name="update-and-test-your-policy"></a>Uw beleid bijwerken en testen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD-tenant bevat door in het bovenste menu op het filter **Map en abonnement** te klikken en de map te kiezen waarin de Azure AD-tenant zich bevindt.
1. Kies linksboven in de Azure Portal **Alle services**, zoek **App-registraties** en selecteer deze.
1. Selecteer een **Framework voor identiteits ervaring**.
1. Selecteer **aangepast beleid uploaden** en upload het beleids bestand dat u hebt gewijzigd, *TrustFrameworkExtensions.xml*.
1. Selecteer het aanmeldings beleid dat u hebt geüpload en klik op de knop **nu uitvoeren** .
1. U moet zich kunnen aanmelden met het account dat u hebt gemaakt (met behulp van MS Graph API), zonder de registratie koppeling.

::: zone-end

## <a name="next-steps"></a>Volgende stappen

* Een [aanmelding met een sociale ID-provider](add-identity-provider.md)toevoegen.
* Stel een [wachtwoord herstel stroom](add-password-reset-policy.md)in.
