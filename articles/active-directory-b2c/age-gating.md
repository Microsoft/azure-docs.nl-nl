---
title: Leeftijds beperking inschakelen in Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over hoe u minder jarigen kunt identificeren met uw toepassing.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 71a3b38da6a63824a42f64052bf16a5fe0e25483
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102522418"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Leeftijds beperking inschakelen in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Met leeftijds beperking in Azure Active Directory B2C (Azure AD B2C) kunt u minder jarigen identificeren die uw toepassing willen gebruiken, met of zonder ouder toestemming. U kunt ervoor kiezen om de secundaire te blok keren om u aan te melden bij de toepassing. Of gebruik toestaan om de aanmelding te volt ooien en de toepassing de secundaire status te geven. 

>[!IMPORTANT]
>Deze functie is beschikbaar voor openbare preview. Gebruik geen functie voor productie toepassingen.
>

Wanneer leeftijds beperking is ingeschakeld voor een gebruikers stroom, wordt gebruikers gevraagd hun geboorte datum en land van verblijf op te vragen. Als een gebruiker zich aanmeldt dat de gegevens niet eerder zijn ingevoerd, moeten ze de volgende keer dat ze zich aanmelden invoeren. De regels worden toegepast telkens wanneer een gebruiker zich aanmeldt.

![Scherm opname van de gegevens stroom voor het verzamelen van leeftijds beperking](./media/age-gating/age-gating-information-gathering.png)

Azure AD B2C gebruikt de informatie die de gebruiker invoert om te bepalen of ze een kleine zijn. Het veld **ageGroup** wordt vervolgens bijgewerkt in hun account. De waarde kan,,, `null` `Undefined` `Minor` `Adult` en `NotAdult` .  De velden **ageGroup** en **consentProvidedForMinor** worden vervolgens gebruikt om de waarde van **legalAgeGroupClassification** te berekenen.


## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="set-up-your-tenant-for-age-gating"></a>Uw Tenant instellen voor leeftijds beperking

Als u leeftijds beperking in een gebruikers stroom wilt gebruiken, moet u uw Tenant configureren voor extra eigenschappen.

1. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu. Selecteer de map die uw Tenant bevat.
1. Selecteer **alle services** in de linkerbovenhoek van de Azure Portal, zoek en selecteer **Azure AD B2C**.
1. Selecteer **Eigenschappen** voor uw Tenant in het menu aan de linkerkant.
1. Selecteer **configureren** onder de **leeftijds beperking**.
1. Wacht totdat de bewerking is voltooid en uw Tenant is ingesteld voor leeftijds beperking.

::: zone pivot="b2c-user-flow"

## <a name="enable-age-gating-in-your-user-flow"></a>Leeftijds beperking inschakelen in uw gebruikers stroom

Nadat uw Tenant is ingesteld voor het gebruik van leeftijd beperking, kunt u deze functie gebruiken in [gebruikers stromen](user-flow-versions.md) waar deze is ingeschakeld. U schakelt leeftijds beperking in met de volgende stappen:

1. Maak een gebruikers stroom waarvoor leeftijds beperking is ingeschakeld.
1. Nadat u de gebruikers stroom hebt gemaakt, selecteert u **Eigenschappen** in het menu.
1. Selecteer in de sectie **leeftijds beperking** de optie **ingeschakeld**.
1. Voor **Aanmelden of aanmelden** selecteert u hoe u gebruikers wilt beheren:
    - Stel minder jarigen in staat toegang te krijgen tot uw toepassing.
    - Blok keer alleen minder jarigen onder leeftijd van toestemming voor toegang tot uw toepassing.
    - Blok keer alle minder jarigen om toegang te krijgen tot uw toepassing.
1. Selecteer een van de volgende opties voor **op blok**:
    - **Een JSON terugsturen naar de toepassing** : met deze optie wordt een antwoord verzonden naar de toepassing die een secundaire is geblokkeerd.
    - **Een fout pagina weer geven** : er wordt een pagina weer gegeven waarin de gebruiker wordt geïnformeerd dat ze geen toegang tot de toepassing hebben.

## <a name="test-your-user-flow"></a>Uw gebruikers stroom testen

1. Als u het beleid wilt testen, selecteert u **gebruikers stroom uitvoeren**.
1. Selecteer voor **toepassing** de webtoepassing met de naam *testapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Selecteer de knop **gebruikers stroom uitvoeren** .
1. Meld u aan met een lokaal of sociaal account. Selecteer vervolgens het land waar u bent gevestigd en de geboorte datum die een kleine simulatie simuleert. 
1. Herhaal de test en selecteer een geboorte datum die een volwassene simuleert.  

Wanneer u zich als een secundaire versie aanmeldt, wordt het volgende fout bericht weer gegeven: *helaas is uw aanmelding geblokkeerd. Privacy en online veiligheids wetgeving in uw land verhinderen toegang tot accounts die tot kinderen behoren.*

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="enable-age-gating-in-your-custom-policy"></a>Leeftijds beperking inschakelen in uw aangepaste beleid

1. Bekijk het voor beeld van een leeftijds beleid voor beperking op [github](https://github.com/azure-ad-b2c/samples/tree/master/age-gating).
1. Vervang in elk bestand de teken reeks door `yourtenant` de naam van uw Azure AD B2C-Tenant. Als de naam van uw B2C-Tenant bijvoorbeeld *contosob2c* is, worden alle exemplaren van `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com` .
1. Upload de beleids bestanden.

::: zone-end

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [beheren van gebruikers toegang in azure AD B2C](manage-user-access.md).

