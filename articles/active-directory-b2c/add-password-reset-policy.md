---
title: Een stroom voor wachtwoord herstel instellen
titleSuffix: Azure AD B2C
description: Meer informatie over het instellen van een wachtwoord herstel stroom in Azure Active Directory B2C.
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
ms.openlocfilehash: 6e6e4aa4ece781f415308b638323f8d4d4b34038
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618755"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Een wachtwoord herstel stroom instellen in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-rest-flow"></a>Wacht woord rest flow

Met het beleid voor het opnieuw instellen van wacht woorden kunnen gebruikers hun eigen verg eten wacht woord opnieuw instellen. De stroom voor het opnieuw instellen van wacht woorden bestaat uit de volgende stappen: 
1. Op de pagina aanmelden en aanmelden klikt de gebruiker op het wacht woord verg eten? . Azure AD B2C retourneert de AADB2C90118-fout code naar uw app. De app verwerkt deze fout code door het beleid voor het opnieuw instellen van het wacht woord aan te roepen. 
1. Gebruikers bieden en verifiëren hun e-mail adres met een eenmalige wachtwoord code.
1. Voer een nieuw wacht woord in.

![Wachtwoord herstel stroom](./media/add-password-reset-policy/password-reset-flow.png)

## <a name="prerequisites"></a>Vereisten

Als u dit nog niet hebt gedaan, [registreert u een webtoepassing in azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-password-reset-user-flow"></a>Een gebruikersstroom voor het opnieuw instellen van het wachtwoord maken

Als u gebruikers van uw toepassing de mogelijkheid wilt bieden hun wachtwoord opnieuw in te stellen, gebruikt u een gebruikersstroom voor het opnieuw instellen van wachtwoorden.

1. Selecteer in het overzichtsmenu van de Azure AD B2C-tenant de optie **Gebruikersstromen** en selecteer vervolgens **Nieuwe gebruikersstroom**.
1. Selecteer op de pagina **Een gebruikersstroom maken** de gebruikersstroom **Wachtwoord opnieuw instellen**. 
1. Onder **Selecteer een versie** selecteert u **Aanbevolen** en vervolgens **Maken**.
1. Voer een **Naam** in voor de gebruikersstroom. Bijvoorbeeld *passwordreset1*.
1. Schakel voor de optie **Id-providers** de optie **Het wachtwoord opnieuw instellen met e-mailadres** in.
2. Klik onder Toepassingsclaims op **Meer weergeven** en kies de claims die u wilt retourneren in de autorisatietokens die worden teruggestuurd naar uw toepassing. Selecteer bijvoorbeeld **Object-ID van gebruiker**.
3. Klik op **OK**.
4. Klik op **Maken** om de gebruikersstroom toe te voegen. Het voorvoegsel *B2C_1* wordt automatisch aan de naam toegevoegd.

### <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Selecteer de gebruikersstroom die u hebt gemaakt om de overzichtspagina te openen en selecteer **Gebruikersstroom uitvoeren**.
1. Selecteer voor **Toepassing** de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Klik op **Gebruikersstroom uitvoeren**, controleer het e-mailadres van het account dat u eerder hebt gemaakt en selecteer **Doorgaan**.
1. U hebt nu de mogelijkheid om het wachtwoord voor de gebruiker te wijzigen. Wijzig het wachtwoord en selecteer **Doorgaan**. Het token wordt geretourneerd aan `https://jwt.ms` en moet worden weergegeven.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-password-reset-policy"></a>Een beleid voor het opnieuw instellen van het wachtwoord maken

Aangepaste beleids regels zijn een set van XML-bestanden die u uploadt naar uw Azure AD B2C-Tenant om gebruikers trajecten te definiëren. We bieden Starter Packs met verschillende vooraf ontwikkelde beleids regels, waaronder: aanmelden en aanmelden, wacht woord opnieuw instellen en profiel bewerkings beleid. Zie [aan de slag met aangepast beleid in azure AD B2C](custom-policy-get-started.md)voor meer informatie.

::: zone-end

## <a name="next-steps"></a>Volgende stappen

Stel een [bewerkings stroom](add-profile-editing-policy.md)voor het profiel in.