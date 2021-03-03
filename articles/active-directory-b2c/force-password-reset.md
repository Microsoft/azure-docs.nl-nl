---
title: Een geforceerde wacht woord opnieuw instellen in Azure AD B2C configureren
titleSuffix: Azure AD B2C
description: Meer informatie over het instellen van een geforceerde stroom voor wachtwoord herstel in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 4be73c126d9f153243b833a7b348244f4d1efcaa
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693195"
---
# <a name="set-up-a-force-password-reset-flow-in-azure-active-directory-b2c"></a>Een wacht woord voor geforceerde wachtwoord herstel instellen in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Als beheerder kunt u het wacht woord van een gebruiker opnieuw instellen als de gebruiker het wacht woord vergeet. Of u wilt hen dwingen om het wacht woord opnieuw in te stellen. In dit artikel leert u hoe u het opnieuw instellen van wacht woorden in deze scenario's afdwingt.

## <a name="overview"></a>Overzicht

Wanneer een beheerder het wacht woord van een gebruiker opnieuw instelt via de Azure Portal, wordt de waarde van het kenmerk [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) ingesteld op `true` .

Met de [traject voor aanmelden en registreren](add-sign-up-and-sign-in-policy.md) wordt de waarde van dit kenmerk gecontroleerd. Nadat de gebruiker de aanmelding heeft voltooid, `true` moet de gebruiker het wacht woord opnieuw instellen als het kenmerk is ingesteld op. Vervolgens wordt de waarde van het kenmerk ingesteld op terug `false` .

![Stroom voor het opnieuw instellen van wacht woorden forceren](./media/force-password-reset/force-password-reset-flow.png)

De stroom voor het opnieuw instellen van wacht woorden is van toepassing op lokale accounts in Azure AD B2C die gebruikmaken van een [e-mail adres](identity-provider-local.md#email-sign-in) of [gebruikers naam](identity-provider-local.md#username-sign-in) met een wacht woord voor aanmelden.

### <a name="force-a-password-reset-after-90-days"></a>Het opnieuw instellen van een wacht woord forceren na 90 dagen

Als beheerder kunt u het wacht woord van een gebruiker instellen op 90 dagen, met behulp van [MS Graph](microsoft-graph-operation.md). Na 90 dagen wordt de waarde van het kenmerk [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) automatisch ingesteld op `true` . Zie [wachtwoord beleid kenmerk](user-profile-attributes.md#password-policy-attribute)voor meer informatie over het instellen van het wachtwoord verloop beleid van een gebruiker.

Wanneer het beleid voor het verlopen van het wacht woord is ingesteld, moet u ook de stroom voor geforceerde wacht woord opnieuw instellen configureren, zoals beschreven in dit artikel.  

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="configure-your-policy"></a>Uw beleid configureren

::: zone pivot="b2c-user-flow"

De instelling **geforceerde wacht woord opnieuw instellen** inschakelen in de stroom registratie of aanmeldings gebruiker:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Selecteer **gebruikers stromen**.
1. Selecteer de registratie-en registratie-of aanmeldings stroom van de gebruiker (van het type **Aanbevolen**) die u wilt aanpassen.
1. Selecteer in het linkermenu onder **instellingen** de optie **Eigenschappen**.
1. Onder **wachtwoord complexiteit** selecteert u **geforceerd wacht woord opnieuw instellen**.
1. Selecteer **Opslaan**.

### <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een gebruikers beheerder of een wachtwoord beheerder. Zie [beheerders rollen toewijzen in azure Active Directory](../active-directory/roles/permissions-reference#available-roles)voor meer informatie over de beschik bare rollen.
1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Selecteer **Gebruikers**. Zoek en selecteer de gebruiker die u wilt gebruiken om het wacht woord opnieuw in te stellen en selecteer vervolgens **wacht woord opnieuw instellen**.
1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Selecteer **gebruikers stromen**.
1. Selecteer een gebruikers stroom voor registratie of aanmelding (van het type **Aanbevolen**) dat u wilt testen.
1. Selecteer **Gebruikersstroom uitvoeren**.
1. Selecteer voor **Toepassing** de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Selecteer **Gebruikersstroom uitvoeren**.
1. Meld u aan met het gebruikers account waarvoor u het wacht woord opnieuw hebt ingesteld.
1. U moet nu het wacht woord voor de gebruiker wijzigen. Wijzig het wachtwoord en selecteer **Doorgaan**. Het token wordt geretourneerd aan `https://jwt.ms` en moet worden weergegeven.

::: zone-end

::: zone pivot="b2c-custom-policy"

Deze functie is momenteel alleen beschikbaar voor gebruikers stromen.

::: zone-end

## <a name="next-steps"></a>Volgende stappen

Stel een [self-service voor het opnieuw instellen van wacht woorden](add-password-reset-policy.md)in.