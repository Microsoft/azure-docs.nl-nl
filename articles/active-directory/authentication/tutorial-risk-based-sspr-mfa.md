---
title: Op risico gebaseerde bescherming van gebruikersaanmeldingen in Azure Active Directory
description: In deze zelfstudie leert u hoe u Azure Identity Protection inschakelt om gebruikers te beschermen wanneer riskant aanmeldingsgedrag wordt gedetecteerd in een account.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97aabfa39954aa6ba937166eb54c05ac4874ea7e
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741335"
---
# <a name="tutorial-use-risk-detections-for-user-sign-ins-to-trigger-azure-ad-multi-factor-authentication-or-password-changes"></a>Zelfstudie: Risicodetectie voor gebruikersaanmeldingen gebruiken als trigger voor Azure AD Multi-Factor Authentication of het wijzigen van wachtwoorden

Ter bescherming van gebruikers kunt u op risicogebeurtenissen gebaseerde beleidsregels configureren in Azure Active Directory (Azure AD) die automatisch op risicovol gedrag reageren. Het beleid van Azure AD Identity Protection kan automatisch een aanmeldingspoging blokkeren of extra actie vereisen, zoals een wachtwoordwijziging of Azure AD Multi-Factor Authentication. Deze beleidsregels werken met bestaande beleidsregels voor voorwaardelijke toegang van Azure AD als extra beveiligingslaag voor uw organisatie. Gebruikers activeren mogelijk nooit riskant gedrag in een van deze beleidsregels, maar uw organisatie is beschermd wanneer er inbraakpoging op uw beleid wordt gedaan.

> [!IMPORTANT]
> Deze zelfstudie laat zien hoe een beheerder Azure AD Multi-Factor Authentication op basis van risico's kan inschakelen.
>
> Neem voor hulp contact op met de helpdesk als uw IT-team de mogelijkheid om Azure AD Multi-Factor Authentication te gebruiken niet heeft ingeschakeld of als u problemen ondervindt tijdens het aanmelden.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Inzicht in het beschikbare beleid voor Azure AD Identity Protection
> * Azure AD Multi-Factor Authentication-registratie inschakelen
> * Op risicogebeurtenissen gebaseerde wachtwoordwijzigingen inschakelen
> * Op risicogebeurtenissen gebaseerde Multi-Factor Authentication inschakelen
> * Op risico gebaseerd beleid voor aanmeldingspogingen van gebruikers

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, hebt u de volgende resources en machtigingen nodig:

* Een werkende Azure AD-tenant waarop minimaal een Azure Ad Premium P2- of -proeflicentie is ingeschakeld.
    * [Maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) indien nodig.
* Een account met de bevoegdheden van een *globale beheerder*.
* Azure AD die is geconfigureerd voor self-service voor wachtwoordherstel en Azure AD Multi-Factor Authentication
    * Voltooi indien nodig [de zelfstudie om Azure Active Directory SSPR in te schakelen](tutorial-enable-sspr.md).
    * Voltooi, indien nodig, [de zelfstudie om Azure AD Multi-Factor Authentication in te schakelen](tutorial-enable-azure-mfa.md).

## <a name="overview-of-azure-ad-identity-protection"></a>Overzicht van Azure AD Identity Protection

Elke dag verzamelt en analyseert Microsoft triljoenen geanonimiseerde signalen als onderdeel van aanmeldingspogingen van gebruikers. Deze signalen helpen patronen van goed aanmeldingsgedrag van gebruikers te bouwen en mogelijk riskante aanmeldingen te identificeren. Azure AD Identity Protection kan aanmeldingspogingen van gebruikers beoordelen en extra actie nemen als er verdacht gedrag optreedt:

Sommige van de volgende acties activeren mogelijk Azure AD Identity Protection-risicodetectie:

* Gebruikers van wie de referenties zijn gelekt.
* Aanmeldingen vanaf anonieme IP-adressen.
* Onmogelijke reis naar ongewone locaties.
* Aanmeldingen vanaf geïnfecteerde apparaten.
* Aanmeldingen van IP-adressen met verdachte activiteit.
* Aanmeldingen vanaf onbekende locaties.

De volgende drie beleidsregels zijn beschikbaar in Azure AD Identity Protection om gebruikers te beschermen en te reageren op verdachte activiteit. U kunt ervoor kiezen om beleidsafdwinging in of uit te schakelen, gebruiker of groepen selecteren om het beleid op toe te passen en besluiten of u toegang bij aanmelding wilt blokkeren of extra actie wilt vragen.

* Beleid voor gebruikersrisico's
    * Identificeert en reageert op gebruikersaccounts met mogelijk gecompromitteerde referenties. Kan de gebruiker vragen een nieuw wachtwoord te maken.
* Beleid voor aanmeldingsrisico's
    * Identificeert en reageert op verdachte aanmeldingspogingen. Kan de gebruiker om aanvullende vormen van verificatie vragen met behulp van Azure AD Multi-Factor Authentication.
* MFA-registratiebeleid
    * Hiermee wordt ervoor gezorgd dat gebruikers zijn geregistreerd voor Azure AD Multi-Factor Authentication. Als een beleid voor aanmeldingsrisico's vraagt om MFA, moet de gebruiker al zijn geregistreerd voor Azure AD Multi-Factor Authentication.

Wanneer u gebruikersbeleid of aanmeldingsrisicobeleid inschakelt, kunt u ook de drempelwaarde voor het risico instellen: *laag en hoger*, *gemiddeld en hoger* of *hoog*. Met deze flexibiliteit kunt u bepalen hoe agressief u de controles voor verdachte aanmeldingsgebeurtenissen wilt afdwingen.

Voor meer informatie over Azure AD Identity Protection raadpleegt u [Wat is Azure AD Identity Protection?](../identity-protection/overview-identity-protection.md)

## <a name="enable-mfa-registration-policy"></a>Beleid voor MFA-registratie inschakelen

Azure AD Identity Protection bevat een standaardbeleid waarmee gebruikers kunnen worden ingeschreven voor Azure AD Multi-Factor Authentication. Als u aanvullend beleid gebruikt om aanmeldingsgebeurtenissen te beschermen, hebt u gebruikers nodig die al zijn geregistreerd voor MFA. Wanneer u dit beleid inschakelt, zijn er geen gebruikers nodig om MFA uit te voeren bij elke aanmeldingsgebeurtenis. Het beleid controleert alleen de registratiestatus van een gebruiker en vraagt hen vooraf te registreren indien dat nodig is.

Het wordt aanbevolen om het MFA-registratiebeleid in te schakelen voor gebruikers waarvoor ook aanvullend Azure AD Identity Protection-beleid wordt ingeschakeld. Voer de volgende stappen uit om dit beleid in te schakelen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een globale-beheerdersaccount.
1. Zoek en selecteer **Azure Active Directory**, selecteer **Beveiliging** en kies onder het menu *Bescherming* **Identiteitsbescherming**.
1. Selecteer het **MFA-registratiebeleid** in het menu aan de linkerkant.
1. Het beleid is standaard van toepassing op *Alle gebruikers*. Selecteer desgewenst **Toewijzingen** en kies vervolgens de gebruikers of groepen waarop u het beleid wilt toepassen.
1. Selecteer **Toegang** onder *Controles*. Zorg ervoor dat de optie voor *Vereis Azure AD MFA-registratie* is ingeschakeld en kies vervolgens **Selecteren**.
1. Stel **Beleid afdwingen** in op *Aan* en selecteer vervolgens **Opslaan**.

    ![Schermopname over hoe u instelt dat gebruikers zich registreren voor MFA in de Azure-portal](./media/tutorial-risk-based-sspr-mfa/enable-mfa-registration.png)

## <a name="enable-user-risk-policy-for-password-change"></a>Beleid voor gebruikersrisico's inschakelen voor wachtwoordwijziging

Microsoft werkt samen met onderzoekers, justitie en politie, diverse beveiligingsteams bij Microsoft en andere betrouwbare bronnen om naar paren van gebruikersnamen en wachtwoorden te zoeken. Wanneer een van deze paren overeenkomt met een account in uw omgeving, kan een op risicogebeurtenissen gebaseerde wachtwoordwijziging worden aangevraagd. Voor dit beleid en deze actie moet de gebruiker het wachtwoord bijwerken voordat de gebruiker zich kan aanmelden om ervoor te zorgen dat eerder openbaargemaakte referenties niet langer werken.

Voer de volgende stappen uit om dit beleid in te schakelen:

1. Selecteer het **Beleid voor gebruikersrisico's** in het menu aan de linkerkant.
1. Het beleid is standaard van toepassing op *Alle gebruikers*. Selecteer desgewenst **Toewijzingen** en kies vervolgens de gebruikers of groepen waarop u het beleid wilt toepassen.
1. Kies onder *Voorwaarden* de optie **Voorwaarden selecteren > Een risiconiveau selecteren** en kies vervolgens *Gemiddeld en hoger*.
1. Kies **Selecteren** en vervolgens **Voltooid**.
1. Selecteer onder *Toegang* de optie **Toegang**. Zorg ervoor dat de optie voor **Toegang toestaan** en *Wachtwoordwijziging vereisen* is ingeschakeld en kies vervolgens **Selecteren**.
1. Stel **Beleid afdwingen** in op *Aan* en selecteer vervolgens **Opslaan**.

    ![Schermopname over hoe u het beleid voor gebruikersrisico's in de Azure-portal inschakelt](./media/tutorial-risk-based-sspr-mfa/enable-user-risk-policy.png)

## <a name="enable-sign-in-risk-policy-for-mfa"></a>Beleid voor aanmeldingsrisico's inschakelen voor MFA

De meeste gebruikers hebben een normaal gedrag dat kan worden gevolgd. Wanneer ze buiten deze norm vallen, is het mogelijk riskant om ze zich te laten aanmelden. In plaats daarvan kunt u die gebruiker blokkeren of ze meervoudige verificatie laten uitvoeren. Als de gebruiker de meervoudige verificatie afrondt, kunt u dit zien al geldige aanmeldingspoging en toegang geven tot de toepassing of service.

Voer de volgende stappen uit om dit beleid in te schakelen:

1. Selecteer het **Beleid voor aanmeldingsrisico's** in het menu aan de linkerkant.
1. Het beleid is standaard van toepassing op *Alle gebruikers*. Selecteer desgewenst **Toewijzingen** en kies vervolgens de gebruikers of groepen waarop u het beleid wilt toepassen.
1. Kies onder *Voorwaarden* de optie **Voorwaarden selecteren > Een risiconiveau selecteren** en kies vervolgens *Gemiddeld en hoger*.
1. Kies **Selecteren** en vervolgens **Voltooid**.
1. Kies **Een controle selecteren** onder *Toegang*. Zorg ervoor dat de optie voor **Toegang toestaan** en *Meervoudige verificatie vereisen* is ingeschakeld en kies vervolgens **Selecteren**.
1. Stel **Beleid afdwingen** in op *Aan* en selecteer vervolgens **Opslaan**.

    ![Schermopname over hoe u het beleid voor aanmeldingsrisico's in de Azure-portal inschakelt](./media/tutorial-risk-based-sspr-mfa/enable-sign-in-risk-policy.png)

## <a name="test-risky-sign-events"></a>Riskante aanmeldingsgebeurtenissen testen

De meeste aanmeldingen van gebruikers activeren het op risico gebaseerde beleid dat in de vorige stappen is geconfigureerd niet. Het kan zijn dat een gebruiker nooit een vraag voor MFA ziet of zijn of haar wachtwoord opnieuw moet instellen. Als hun referenties beveiligd blijven en hun gedrag consistent is, slagen hun aanmeldingspogingen.

Als u Azure AD Identity Protection-beleid wilt testen dat in de vorige stappen is gemaakt, moet u een manier hebben om riskant gedrag of potentiële aanvallen te simuleren. De stappen om deze tests uit te voeren variëren op basis van het Azure AD Identity Protection-beleid dat u wilt testen. Raadpleeg [Risicodetectie simuleren in Azure AD Identity Protection](../identity-protection/howto-identity-protection-simulate-risk.md) voor meer informatie over scenario's en stappen.

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met testen en er geen behoefte meer aan hebt dat het op risicogebeurtenissen gebaseerd beleid is ingeschakeld, gaat u terug naar elk beleid dat u wilt uitschakelen en stelt u **Beleid afdwingen** in op *Uit*.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u op risico gebaseerd gebruikersbeleid voor Azure AD Identity Protection ingeschakeld. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Inzicht in het beschikbare beleid voor Azure AD Identity Protection
> * Azure AD Multi-Factor Authentication-registratie inschakelen
> * Op risicogebeurtenissen gebaseerde wachtwoordwijzigingen inschakelen
> * Op risicogebeurtenissen gebaseerde Multi-Factor Authentication inschakelen
> * Op risico gebaseerd beleid voor aanmeldingspogingen van gebruikers

> [!div class="nextstepaction"]
> [Meer informatie over Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)
