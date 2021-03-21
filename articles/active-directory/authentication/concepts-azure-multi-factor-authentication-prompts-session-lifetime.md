---
title: Azure AD Multi-Factor Authentication-prompts en de levens duur van sessies
description: Meer informatie over de aanbevolen configuratie voor herverificaties prompts met Azure AD Multi-Factor Authentication en hoe de levens duur van de sessie wordt toegepast.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0033b6985313de351ffdaf7028d2c1e9bd76ba72
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97963516"
---
# <a name="optimize-reauthentication-prompts-and-understand-session-lifetime-for-azure-ad-multi-factor-authentication"></a>Herauthenticatie vragen en de levens duur van sessies voor Azure AD-Multi-Factor Authentication optimaliseren

Azure Active Directory (Azure AD) heeft meerdere instellingen die bepalen hoe vaak gebruikers opnieuw moeten worden geverifieerd. Deze herauthenticatie kan worden uitgevoerd met een eerste factor zoals wacht woord, FIDO of wacht woord Microsoft Authenticator, of om multi-factor Authentication (MFA) uit te voeren. U kunt deze instellingen voor opnieuw verifiëren zo nodig configureren voor uw eigen omgeving en de gewenste gebruikers ervaring.

De standaard configuratie van Azure AD voor de aanmeldings frequentie van gebruikers is een rolling venster van 90 dagen. Het is vaak handig om gebruikers te vragen om referenties, maar dit kan Backfire. Als gebruikers zijn getraind om hun referenties op te geven zonder te denken, kunnen ze per ongeluk ze aan een kwaad aardige referentie prompt leveren.

Het kan erop klinken dat een waarschuwing wordt weer gegeven om te voor komen dat een gebruiker zich opnieuw aanmeldt, hoewel een schending van het beleid de sessie intrekt. Enkele voor beelden zijn onder andere het wijzigen van een wacht woord, het incompatibele apparaat of het uitschakelen van een account. U kunt [gebruikers sessies ook expliciet intrekken met behulp van Power shell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken).

In dit artikel worden de aanbevolen configuraties beschreven en wordt uitgelegd hoe verschillende instellingen werken en met elkaar communiceren.

## <a name="recommended-settings"></a>Aanbevolen instellingen

Om uw gebruikers het juiste evenwicht te geven tussen beveiliging en gebruiks gemak door hen te vragen om zich aan te melden bij de juiste frequentie, raden wij de volgende configuraties aan:

* Als u Azure AD Premium hebt:
    * Schakel eenmalige aanmelding (SSO) in voor toepassingen die gebruikmaken van [beheerde apparaten](../devices/overview.md) of [naadloze SSO](../hybrid/how-to-connect-sso.md).
    * Als herauthenticatie is vereist, gebruikt u een [aanmeldings frequentie beleid voor](../conditional-access/howto-conditional-access-session-lifetime.md)voorwaardelijke toegang.
    * Voor gebruikers die zich aanmelden vanaf niet-beheerde apparaten of scenario's voor mobiele apparaten, kunt u voorwaardelijke toegang gebruiken om permanente browser sessies en beleids regels voor aanmeldings frequentie in te scha kelen.
* Als u Microsoft 365 apps-licenties of de gratis Azure AD-laag hebt:
    * Schakel eenmalige aanmelding (SSO) in voor toepassingen die gebruikmaken van [beheerde apparaten](../devices/overview.md) of [naadloze SSO](../hybrid/how-to-connect-sso.md).
    * Houd de optie *aangemeld blijven* ingeschakeld en richt uw gebruikers in om deze te accepteren.
* Zorg ervoor dat uw gebruikers de app Microsoft Authenticator gebruiken voor scenario's voor mobiele apparaten. Deze app wordt gebruikt als een Broker voor andere federatieve Azure AD-apps en vermindert verificatie prompts op het apparaat.

In ons onderzoek ziet u dat deze instellingen geschikt zijn voor de meeste tenants. Sommige combi Naties van deze instellingen, zoals *MFA onthouden* en *blijven aanwezig*, kunnen ertoe leiden dat uw gebruikers te vaak te verifiëren worden gewaarschuwd. Regel matige herverificaties vragen zijn slecht voor gebruikers productiviteit en kunnen deze kwetsbaarder maken voor aanvallen.

## <a name="azure-ad-session-lifetime-configuration-settings"></a>Configuratie-instellingen voor levens duur van Azure AD-sessie

Als u de frequentie van verificatie prompts voor uw gebruikers wilt optimaliseren, kunt u de opties voor de levens duur van Azure AD-sessies configureren. Inzicht in de behoeften van uw bedrijf en gebruikers en Configureer instellingen die het beste evenwicht bieden voor uw omgeving.

### <a name="evaluate-session-lifetime-policies"></a>Levens duur van sessie beleid evalueren

Zonder de instellingen voor de levens duur van sessies zijn er geen permanente cookies in de browser sessie. Telkens wanneer een gebruiker de browser sluit en opent, wordt u gevraagd om de verificatie te verhelpen. In Office-clients is de standaard periode een voortschrijdende Window van 90 dagen. Als de gebruiker de standaard configuratie van Office heeft ingesteld op het opnieuw instellen van het wacht woord of als er sinds 90 dagen inactiviteit is, moet de gebruiker opnieuw worden geverifieerd met alle vereiste factoren (eerste en tweede factor).

Een gebruiker ziet mogelijk meerdere MFA-prompts op een apparaat dat geen identiteit heeft in azure AD. Wanneer elke toepassing een eigen OAuth-vernieuwings token heeft dat niet wordt gedeeld met andere client-apps, worden er meerdere prompts geretourneerd. In dit scenario vraagt MFA meerdere keren aan dat elke toepassing een OAuth-vernieuwings token moet aanvragen om te worden gevalideerd met MFA.

In azure AD bepaalt het meest beperkende beleid voor de levens duur van de sessie of de gebruiker opnieuw moet worden geverifieerd. Denkt u zich het volgende scenario eens in:

* U schakelt *aangemeld blijven in*, waarbij gebruik wordt gemaakt van een permanente browser cookie, en
* U kunt *MFA ook voor 14 dagen bewaren* .

In dit voorbeeld scenario moet de gebruiker om de 14 dagen opnieuw worden geverifieerd. Dit gedrag volgt het meest beperkende beleid, ook al is de gebruiker *aangemeld* voor herauthenticatie op de browser.

### <a name="managed-devices"></a>Beheerde apparaten

Apparaten die zijn gekoppeld aan Azure AD via Azure AD join of hybride Azure AD join, ontvangen een [primaire vernieuwings token (PRT)](../devices/concept-primary-refresh-token.md) voor het gebruik van eenmalige aanmelding (SSO) voor alle toepassingen. Met deze PRT kunnen gebruikers zich eenmalig aanmelden op het apparaat en kunnen IT-mede werkers controleren of aan de normen voor beveiliging en naleving wordt voldaan. Als een gebruiker moet worden gevraagd om zich vaker aan te melden op een gekoppeld apparaat voor sommige apps of scenario's, kan dit worden bereikt met de [aanmeldings frequentie voor voorwaardelijke toegang](../conditional-access/howto-conditional-access-session-lifetime.md).

### <a name="show-option-to-remain-signed-in"></a>Optie weer geven om aangemeld te blijven

Wanneer een gebruiker **Ja** selecteert in de optie *aangemeld blijven?* tijdens het aanmelden, wordt een permanente cookie ingesteld op de browser. Deze permanente cookie onthoudt zowel de eerste als de tweede factor en is alleen van toepassing op verificatie aanvragen in de browser.

![Scherm afbeelding van een voor beeld van een prompt om aangemeld te blijven](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/stay-signed-in-prompt.png)

Als u een licentie voor Azure AD Premium 1 hebt, raden we u aan het beleid voor voorwaardelijke toegang te gebruiken voor een *permanente browser sessie*. Dit beleid overschrijft de instelling *aangemeld blijven?* en biedt een verbeterde gebruikers ervaring. Als u geen licentie voor Azure AD Premium 1 hebt, wordt u aangeraden de instelling aangemeld blijven in te scha kelen voor uw gebruikers.

Zie [uw aanmeldings pagina voor Azure AD aanpassen](../fundamentals/customize-branding.md#customize-your-azure-ad-sign-in-page)voor meer informatie over het configureren van de optie om gebruikers aangemeld te laten blijven.

### <a name="remember-multi-factor-authentication"></a>Onthouden Multi-Factor Authentication  

Met deze instelling kunt u waarden tussen 1-365 dagen configureren en een permanente cookie op de browser instellen wanneer een gebruiker de optie **niet opnieuw vragen voor X dagen** selecteert bij het aanmelden.

![Scherm afbeelding van de voorbeeld prompt voor het goed keuren van een aanmeldings aanvraag](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/approve-sign-in-request.png)

Hoewel deze instelling het aantal authenticaties op Web-apps vermindert, wordt het aantal authenticaties voor moderne verificatie-clients, zoals Office-clients, verhoogd. Deze clients vragen normaal gesp roken alleen na het opnieuw instellen van het wacht woord of de inactiviteit van 90 dagen. Als u deze waarde instelt op minder dan 90 dagen, worden de standaard MFA-prompts voor Office-clients verkort en wordt de frequentie van de herauthenticatie verlengd. Bij gebruik in combi natie met **blijven aangemeld** of beleid voor voorwaardelijke toegang, kan het aantal verificatie aanvragen toenemen.

Als u *MFA onthouden* gebruikt en Azure AD Premium 1 licenties hebt, kunt u deze instellingen migreren naar de aanmeldings frequentie voor voorwaardelijke toegang. Als dat niet het geval is, kunt u in plaats daarvan aanmelden gebruiken *?*

Zie [multi-factor Authentication onthouden](howto-mfa-mfasettings.md#remember-multi-factor-authentication)voor meer informatie.

### <a name="authentication-session-management-with-conditional-access"></a>Beheer van verificatie sessies met voorwaardelijke toegang

Met de **aanmeldings frequentie** kan de beheerder de aanmeldings frequentie kiezen die van toepassing is voor zowel de eerste als de tweede factor in zowel de client als de browser. We raden u aan deze instellingen te gebruiken, samen met het gebruik van beheerde apparaten, in scenario's wanneer u de verificatie sessie wilt beperken, zoals voor kritieke zakelijke toepassingen.

Met een **permanente browser sessie** kunnen gebruikers aangemeld blijven nadat ze het browser venster hebben gesloten en opnieuw hebben geopend. Net als bij de instelling *Inblijvend aangemeld* , wordt een permanente cookie ingesteld op de browser. Omdat de gebruiker echter is geconfigureerd door de beheerder, is de optie **Ja** niet vereist in de functie *aangemeld blijven?* , zodat u een betere gebruikers ervaring hebt. Als u de optie *aangemeld blijven?* gebruikt, raden we u aan om het beleid voor **permanente browser sessies** in te scha kelen.

Voor meer informatie. Zie [verificatie sessie beheer met voorwaardelijke toegang configureren](../conditional-access/howto-conditional-access-session-lifetime.md).

### <a name="configurable-token-lifetimes"></a>Configureer bare levens duur van tokens

Met deze instelling kan de levens duur worden geconfigureerd voor tokens die zijn uitgegeven door Azure Active Directory. Dit beleid wordt vervangen door *verificatie sessie beheer met voorwaardelijke toegang*. Als u vandaag nog *Configureer bare token levensduur* gebruikt, wordt u aangeraden de migratie naar het beleid voor voorwaardelijke toegang te starten.

## <a name="review-your-tenant-configuration"></a>Uw Tenant configuratie controleren  

Nu u begrijpt hoe verschillende instellingen werken en de aanbevolen configuratie, is het tijd om de configuratie van uw tenants te controleren en de wijzigingen dienovereenkomstig aan te brengen:

Voer de volgende stappen uit als u de optie *aangemeld blijven* wilt configureren of bekijken:

1. Zoek en selecteer *Azure Active Directory* in de Azure AD-Portal.
1. Selecteer de **huis stijl** van het bedrijf en kies voor elke land instelling de **optie weer geven om aangemeld te blijven**.
1. Kies *Ja* en selecteer vervolgens **Opslaan**.

Voer de volgende stappen uit om multi-factor Authentication-instellingen op vertrouwde apparaten te onthouden:

1. Zoek en selecteer *Azure Active Directory* in de Azure AD-Portal.
1. Selecteer **beveiliging** en vervolgens **MFA**.
1. Selecteer onder **configureren** **Extra op de cloud gebaseerde MFA-instellingen**.
1. Ga op de pagina *instellingen voor multi-factor Authentication-Service* naar **instellingen voor multi-factor Authentication onthouden**. Schakel de instelling uit door het selectie vakje uit te scha kelen.

Als u beleid voor voorwaardelijke toegang wilt configureren voor aanmeldings frequentie en permanente browser sessie, voert u de volgende stappen uit:

1. Zoek en selecteer *Azure Active Directory* in de Azure AD-Portal.
1. Selecteer **beveiliging** en vervolgens **voorwaardelijke toegang**.
1. Configureer een beleid met de aanbevolen opties voor sessie beheer die in dit artikel worden beschreven.

Als u de levens duur van tokens wilt bekijken, [gebruikt u Azure AD Power shell voor het uitvoeren van een query op Azure AD-beleid](../develop/configure-token-lifetimes.md#get-started) Schakel alle beleids regels uit die u hebt geïnstalleerd.

Als er meer dan één instelling is ingeschakeld in uw Tenant, wordt u aangeraden uw instellingen bij te werken op basis van de licenties die voor u beschikbaar zijn. Als u bijvoorbeeld Azure AD Premium-licenties hebt, moet u alleen het beleid voor voorwaardelijke toegang van de *aanmeldings frequentie* en *permanente browser sessie* gebruiken. Als u Microsoft 365 apps of gratis Azure AD-licenties hebt, moet u de configuratie *ingelogd blijven gebruiken?*

Als u Configureer bare token levensduur hebt ingeschakeld, wordt deze functie binnenkort verwijderd. Plan een migratie naar een beleid voor voorwaardelijke toegang.

De volgende tabel bevat een overzicht van de aanbevelingen op basis van licenties:

|              | Azure AD Free-en Microsoft 365-apps | Azure AD Premium |
|------------------------------|-----------------------------------|------------------|
| **Eenmalige aanmelding**                      | Deelname aan [Azure AD](../devices/concept-azure-ad-join.md) of [hybride Azure AD](../devices/concept-azure-ad-join-hybrid.md)of [naadloze SSO](../hybrid/how-to-connect-sso.md) voor onbeheerde apparaten. | Azure AD-koppeling<br />Hybride Azure AD-koppeling |
| **Instellingen voor opnieuw verifiëren** | Blijven aangemeld                  | Beleid voor voorwaardelijke toegang gebruiken voor aanmeldings frequentie en permanente browser sessie |

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt gaan, voltooit u de zelf studie voor het [beveiligen van aanmeldings gebeurtenissen van gebruikers met Azure ad multi-factor Authentication](tutorial-enable-azure-mfa.md) of [gebruikt u risico detecties voor gebruikers aanmeldingen om Azure AD-multi-factor Authentication te activeren](tutorial-risk-based-sspr-mfa.md).
