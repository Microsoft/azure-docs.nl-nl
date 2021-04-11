---
title: Een bewerkings stroom voor een profiel instellen
titleSuffix: Azure AD B2C
description: Meer informatie over het instellen van een bewerkings stroom voor een profiel in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d84756a2ae4f8897c42e1846e3a91dbb9f7ad7e1
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257038"
---
# <a name="set-up-a-profile-editing-flow-in-azure-active-directory-b2c"></a>Een bewerkings stroom voor een profiel instellen in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="profile-editing-flow"></a>Bewerkings stroom voor profielen

Met het bewerkings beleid voor profielen kunnen gebruikers hun profiel kenmerken beheren, zoals weergave naam, achternaam, voor waarde, plaats en andere. De stroom voor het bewerken van profielen bestaat uit de volgende stappen: 

1. Meld u aan of Meld u aan met een lokaal of sociaal account. Als de sessie nog steeds actief is, wordt de gebruiker door Azure AD B2C geautoriseerd en wordt de volgende stap overs Laan.
1. Azure AD B2C leest het gebruikers profiel uit de map en laat de gebruiker de kenmerken bewerken.

![Bewerkings stroom voor profielen](./media/add-profile-editing-policy/profile-editing-flow.png)


## <a name="prerequisites"></a>Vereisten

Als u dit nog niet hebt gedaan, [registreert u een webtoepassing in azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-profile-editing-user-flow"></a>Een gebruikersstroom voor profielbewerking maken

Als u gebruikers in staat wilt stellen hun profiel te bewerken in uw toepassing, gebruikt u een gebruikersstroom voor het bewerken van profielen.

1. Selecteer in het menu van het paginaoverzicht van de Azure AD B2C-tenant de optie **Gebruikersstromen** en selecteer vervolgens **Nieuwe gebruikersstroom**.
1. Selecteer op de pagina **Een gebruikersstroom maken** de gebruikersstroom **Profiel bewerken**. 
1. Onder **Selecteer een versie** selecteert u **Aanbevolen** en vervolgens **Maken**.
1. Voer een **Naam** in voor de gebruikersstroom. Bijvoorbeeld *profileediting1*.
1. Voor **id-providers** selecteert u **e-mail aanmelding**.
1. Kies voor **Gebruikerskenmerken** de kenmerken waarvan u wilt dat de klant deze in zijn profiel kan bewerken. Selecteer bijvoorbeeld **Meer weergeven** en kies vervolgens kenmerken en claims voor **Weergavenaam** en **Functie**. Klik op **OK**.
1. Klik op **Maken** om de gebruikersstroom toe te voegen. Het voorvoegsel *B2C_1* wordt automatisch aan de naam toegevoegd.

### <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Selecteer de gebruikersstroom die u hebt gemaakt om de overzichtspagina te openen en selecteer **Gebruikersstroom uitvoeren**.
1. Selecteer voor **Toepassing** de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Klik op **Gebruikersstroom uitvoeren** en meld u vervolgens aan met het account dat u eerder hebt gemaakt.
1. U hebt nu de mogelijkheid om de weergavenaam en de functie voor de gebruiker te wijzigen. Klik op **Doorgaan**. Het token wordt geretourneerd aan `https://jwt.ms` en moet worden weergegeven.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-profile-editing-policy"></a>Een beleid voor profielbewerking maken

Aangepaste beleids regels zijn een set van XML-bestanden die u uploadt naar uw Azure AD B2C-Tenant om gebruikers trajecten te definiëren. We bieden Starter Packs met verschillende vooraf ontwikkelde beleids regels, waaronder: aanmelden en aanmelden, wacht woord opnieuw instellen en profiel bewerkings beleid. Zie [aan de slag met aangepast beleid in azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)voor meer informatie.

::: zone-end

## <a name="next-steps"></a>Volgende stappen

* Een [aanmelding met een sociale ID-provider](add-identity-provider.md)toevoegen.