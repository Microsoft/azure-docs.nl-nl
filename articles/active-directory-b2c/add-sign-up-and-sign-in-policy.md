---
title: Een registratie en aanmeldings stroom instellen
titleSuffix: Azure AD B2C
description: Meer informatie over het instellen van een registratie en aanmeldings stroom in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 29dd67e9e6e15aaafec0cc47d89da32cbf369938
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97618752"
---
# <a name="set-up-a-sign-up-and-sign-in-flow-in-azure-active-directory-b2c"></a>Een registratie en aanmeldings stroom instellen in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-up-and-sign-in-flow"></a>Registratie en aanmeldings stroom

Met registratie en aanmeldings beleid kunnen gebruikers: 

* Aanmelden met een lokaal account
* Aanmelden met een lokaal account
* Registreren of aanmelden met een sociaal account
* Wachtwoord opnieuw instellen

![Bewerkings stroom voor profielen](./media/add-sign-up-and-sign-in-policy/add-sign-up-and-sign-in-flow.png)

## <a name="prerequisites"></a>Vereisten

Als u dit nog niet hebt gedaan, [registreert u een webtoepassing in azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Een gebruikersstroom voor registratie en aanmelding maken

Met de gebruikersstroom voor registratie en aanmelding worden zowel registratie als aanmelding met een enkele configuratie afgehandeld. Gebruikers van uw toepassing worden naar het juiste pad geleid, afhankelijk van de context.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Selecteer onder **Beleid** de optie **Gebruikersstromen** en selecteer vervolgens **Nieuwe gebruikersstroom**.

    ![De pagina Gebruikersstromen in de portal met de knop Nieuwe gebruikersstroom gemarkeerd](./media/add-sign-up-and-sign-in-policy/signup-signin-user-flow.png)

1. Selecteer op de pagina **Een gebruikersstroom maken** de gebruikersstroom **Registreren en aanmelden**.

    ![Een gebruikersstroompagina selecteren met de stroom voor registratie en aanmelding gemarkeerd](./media/add-sign-up-and-sign-in-policy/select-user-flow-type.png)

1. Onder **Selecteer een versie** selecteert u **Aanbevolen** en vervolgens **Maken**. ([Meer informatie](user-flow-versions.md) over gebruikersstroomversies.)

    ![De pagina Gebruikersstroom maken in de Azure-portal met eigenschappen gemarkeerd](./media/add-sign-up-and-sign-in-policy/select-version.png)

1. Voer een **Naam** in voor de gebruikersstroom. Bijvoorbeeld *signupsignin1*.
1. Selecteer voor **id**-providers **e-mail registratie**.
1. Kies voor **Gebruikerskenmerken en claims** de claims en kenmerken van de gebruiker die u tijdens de registratie wilt verzamelen en verzenden. Selecteer bijvoorbeeld **Meer weergeven** en kies vervolgens kenmerken en claims voor **Land/regio**, **Weergavenaam** en **Postcode**. Klik op **OK**.

    ![Selectiepagina voor gebruikerskenmerken en claims met drie claims geselecteerd](./media/add-sign-up-and-sign-in-policy/signup-signin-attributes.png)

1. Klik op **Maken** om de gebruikersstroom toe te voegen. Het voorvoegsel *B2C_1* wordt automatisch voor de naam geplaatst.

### <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Selecteer de gebruikersstroom die u hebt gemaakt om de overzichtspagina te openen en selecteer **Gebruikersstroom uitvoeren**.
1. Selecteer voor **Toepassing** de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Klik op **Gebruikersstroom uitvoeren** en selecteer **Nu registreren**.

    ![De pagina Gebruikersstroom uitvoeren in de portal met de knop Gebruikersstroom uitvoeren gemarkeerd](./media/add-sign-up-and-sign-in-policy/signup-signin-run-now.PNG)

1. Voer een geldig e-mailadres in, klik op **Verificatiecode verzenden**, voer de verificatiecode in die u ontvangt en selecteer **Code verifiëren**.
1. Voer een nieuw wachtwoord in en bevestig het wachtwoord.
1. Selecteer uw land en regio, voer de naam in die u wilt weergeven, voer een postcode in en klik vervolgens op **Maken**. Het token wordt geretourneerd aan `https://jwt.ms` en moet worden weergegeven.
1. U kunt de gebruikersstroom nu opnieuw uitvoeren en u moet zich kunnen aanmelden met het account dat u hebt gemaakt. Het geretourneerde token bevat de claims die u hebt geselecteerd voor land/regio, naam en postcode.

> [!NOTE]
> De 'Gebruikersstroom uitvoeren'-ervaring is momenteel niet compatibel met de SPA-antwoord-URL met autorisatiecodestroom. Als u de 'Gebruikersstroom uitvoeren'-ervaring wilt gebruiken met deze typen apps, moet u een antwoord-URL van het type 'Web' registreren en de impliciete stroom inschakelen zoals [hier](tutorial-register-spa.md) beschreven.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-sign-up-and-sign-in-policy"></a>Een registratie-en aanmeldings beleid maken

Aangepaste beleids regels zijn een set van XML-bestanden die u uploadt naar uw Azure AD B2C-Tenant om gebruikers trajecten te definiëren. We bieden Starter Packs met verschillende vooraf ontwikkelde beleids regels, waaronder: aanmelden en aanmelden, wacht woord opnieuw instellen en profiel bewerkings beleid. Zie [aan de slag met aangepast beleid in azure AD B2C](custom-policy-get-started.md)voor meer informatie.

::: zone-end

## <a name="next-steps"></a>Volgende stappen

* Een [aanmelding met een sociale ID-provider](add-identity-provider.md)toevoegen.
* Stel een [wachtwoord herstel stroom](add-password-reset-policy.md)in.
