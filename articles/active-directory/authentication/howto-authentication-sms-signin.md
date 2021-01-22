---
title: Aanmelden op basis van een gebruiker voor Azure Active Directory van SMS
description: Meer informatie over het configureren en inschakelen van gebruikers om zich aan te melden bij Azure Active Directory met behulp van SMS
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/21/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43573b54be6884e01121e404370d2e1d85a3c4e8
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660878"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory"></a>Gebruikers configureren en inschakelen voor verificatie op basis van SMS met Azure Active Directory 

Azure Active Directory (Azure AD) biedt meerdere verificatie opties om het aanmelden bij toepassingen en services te vereenvoudigen en te beveiligen. Met verificatie op basis van een SMS kunnen gebruikers zich aanmelden zonder de gebruikers naam en het wacht woord op te geven of zelfs te weten. Nadat het account is gemaakt door een identiteits beheerder, kunnen ze hun telefoon nummer invoeren bij de aanmeldings prompt. Ze ontvangen een verificatie code via een SMS-bericht dat ze kunnen opgeven om de aanmelding te volt ooien. Deze verificatie methode vereenvoudigt de toegang tot toepassingen en services, met name voor werk nemers van de eerste regel.

Dit artikel laat u zien hoe u verificatie op basis van SMS kunt inschakelen voor geselecteerde gebruikers of groepen in azure AD.

## <a name="before-you-begin"></a>Voordat u begint

U hebt de volgende resources en bevoegdheden nodig om dit artikel te volt ooien:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-Tenant die aan uw abonnement is gekoppeld.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* U hebt *globale beheerders* bevoegdheden nodig in uw Azure AD-Tenant om verificatie op basis van SMS in te scha kelen.
* Voor elke gebruiker die is ingeschakeld in het beleid voor tekst bericht authenticatie, moet een licentie worden verleend, zelfs als ze deze niet gebruiken. Elke ingeschakelde gebruiker moet een van de volgende Azure AD-, EMS-Microsoft 365-licenties hebben:
    * [Azure AD Premium P1 of P2][azuread-licensing]
    * [Microsoft 365 (M365) F1 of F3][m365-firstline-workers-licensing]
    * [Enterprise Mobility + Security (EMS) E3 of E5][ems-licensing] of [Microsoft 365 (M365) E3 of E5][m365-licensing]

## <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing op verificatie op basis van SMS:

* Verificatie op basis van SMS is momenteel niet compatibel met Azure AD Multi-Factor Authentication.
* Met uitzonde ring van teams is verificatie op basis van SMS niet compatibel met systeem eigen Office-toepassingen.
* Verificatie op basis van SMS wordt niet aanbevolen voor B2B-accounts.
* Federatieve gebruikers worden niet geverifieerd in de thuis Tenant. Ze worden alleen geverifieerd in de Cloud.

## <a name="enable-the-sms-based-authentication-method"></a>De op SMS gebaseerde verificatie methode inschakelen

Er zijn drie belang rijke stappen voor het inschakelen en gebruiken van verificatie op basis van SMS in uw organisatie:

* Schakel het beleid voor de verificatie methode in.
* Selecteer gebruikers of groepen die de op SMS gebaseerde verificatie methode kunnen gebruiken.
* Wijs een telefoon nummer toe aan elk gebruikers account.
    * Dit telefoon nummer kan worden toegewezen in de Azure Portal (die in dit artikel wordt weer gegeven) en in *mijn personeel* of *Mijn account*.

Eerst gaan we verificatie op basis van SMS inschakelen voor uw Azure AD-Tenant.

1. Meld u aan bij de [Azure Portal][azure-portal] als *globale beheerder*.
1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer in het navigatie menu aan de linkerkant van het Azure Active Directory venster **beveiligings > verificatie methoden > verificatie methode beleid**.

    [![Blader naar en selecteer het venster verificatie methode beleid in het Azure Portal.](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png)](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. Selecteer **tekst bericht** in de lijst met beschik bare verificatie methoden.
1. Stel **Enable** in op *Ja*.

    ![Tekst verificatie inschakelen in het venster verificatie methode beleid](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    U kunt ervoor kiezen om verificatie op basis van SMS in te scha kelen voor *alle gebruikers* of gebruikers en groepen te *selecteren* . In de volgende sectie schakelt u verificatie op basis van SMS in voor een test gebruiker.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>De verificatie methode toewijzen aan gebruikers en groepen

Als verificatie op basis van SMS is ingeschakeld in uw Azure AD-Tenant, moet u nu sommige gebruikers of groepen selecteren dat deze verificatie methode mag worden gebruikt.

1. Stel in het venster tekst bericht verificatie beleid het selectie vakje **doel** in op *gebruikers selecteren*.
1. Kies om **gebruikers of groepen toe te voegen** en selecteer vervolgens een test gebruiker of groep, zoals *Contoso-gebruiker* of *Contoso SMS-gebruikers*.

    [![Kies gebruikers of groepen die u wilt inschakelen voor verificatie op basis van SMS in de Azure Portal.](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png)](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. Wanneer u uw gebruikers of groepen hebt geselecteerd, kiest u **selecteren** en **slaat** u het bijgewerkte verificatie methode beleid op.

Voor elke gebruiker die is ingeschakeld in het beleid voor tekst bericht authenticatie, moet een licentie worden verleend, zelfs als ze deze niet gebruiken. Zorg ervoor dat u over de juiste licenties beschikt voor de gebruikers die u inschakelt in het beleid voor verificatie methoden, met name wanneer u de functie voor grote groepen gebruikers inschakelt.

## <a name="set-a-phone-number-for-user-accounts"></a>Een telefoon nummer voor gebruikers accounts instellen

Gebruikers zijn nu ingeschakeld voor verificatie op basis van SMS, maar hun telefoon nummer moet worden gekoppeld aan het gebruikers profiel in azure AD voordat ze zich kunnen aanmelden. De gebruiker kan [Dit telefoon nummer zelf instellen](../user-help/sms-sign-in-explainer.md) in *Mijn account* of u kunt het telefoon nummer toewijzen met behulp van de Azure Portal. Telefoon nummers kunnen worden ingesteld door *globale beheerders*, *authenticatie beheerders* of *geprivilegieerde verificatie beheerders*.

Wanneer een telefoon nummer is ingesteld voor SMS-Sign, is het ook beschikbaar voor gebruik met [Azure AD multi-factor Authentication][tutorial-azure-mfa] en [selfservice voor wachtwoord herstel][tutorial-sspr].

1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer in het navigatie menu aan de linkerkant van het Azure Active Directory venster **gebruikers**.
1. Selecteer de gebruiker die u hebt ingeschakeld voor verificatie op basis van SMS in de vorige sectie, zoals *Contoso-gebruiker*, en selecteer vervolgens **verificatie methoden**.
1. Selecteer **+ verificatie methode toevoegen** en kies in de vervolg keuzelijst *methode kiezen* de optie **telefoon nummer**.

    Voer het telefoon nummer van de gebruiker in, met inbegrip van de land code, zoals *+ 1 xxxxxxxxx*. De Azure Portal valideert het telefoon nummer in de juiste indeling.

    Ga vervolgens naar de vervolg keuzelijst *telefoon type* en selecteer *mobiele*, *alternatieve mobiele apparaten* of *andere* als dat nodig is.

    :::image type="content" source="media/howto-authentication-sms-signin/set-user-phone-number.png" alt-text="Een telefoon nummer instellen voor een gebruiker in de Azure Portal voor gebruik met verificatie op basis van SMS":::

    Het telefoon nummer moet uniek zijn in uw Tenant. Als u hetzelfde telefoon nummer voor meerdere gebruikers probeert te gebruiken, wordt er een fout bericht weer gegeven.

1. Selecteer **toevoegen** om het telefoon nummer toe te passen op het account van een gebruiker.

Wanneer het is ingericht, wordt er een vinkje weer gegeven voor *SMS-aanmelding ingeschakeld*.

## <a name="test-sms-based-sign-in"></a>Op SMS gebaseerde aanmelding testen

Voer de volgende stappen uit om het gebruikers account te testen dat nu is ingeschakeld voor aanmelden op basis van SMS:

1. Open een nieuw InPrivate-of incognito-webbrowser venster om [https://www.office.com][office]
1. Selecteer **Aanmelden** in de rechter bovenhoek.
1. Voer bij de aanmeldings prompt het telefoon nummer in dat is gekoppeld aan de gebruiker in de vorige sectie en selecteer vervolgens **volgende**.

    ![Voer een telefoon nummer in bij de aanmeldings prompt voor de test gebruiker](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. Er wordt een SMS-bericht verzonden naar het telefoon nummer dat u hebt ontvangen. Om het aanmeldings proces te volt ooien, voert u de 6-cijferige code in die in het tekst bericht bij de aanmeldings prompt wordt vermeld.

    ![Voer de bevestigings code in die via een SMS-bericht is verzonden naar het telefoon nummer van de gebruiker](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. De gebruiker is nu aangemeld zonder dat u een gebruikers naam of wacht woord hoeft op te geven.

## <a name="troubleshoot-sms-based-sign-in"></a>Problemen met aanmelden op basis van SMS oplossen

De volgende scenario's en probleemoplossings stappen kunnen worden gebruikt als u problemen ondervindt met het inschakelen en gebruiken van aanmelden op basis van SMS.

### <a name="phone-number-already-set-for-a-user-account"></a>Het telefoon nummer is al ingesteld voor een gebruikers account

Als een gebruiker al is geregistreerd voor Azure AD Multi-Factor Authentication en/of selfservice voor het opnieuw instellen van wacht woorden (SSPR), is er al een telefoon nummer gekoppeld aan het account. Dit telefoon nummer is niet automatisch beschikbaar voor gebruik met aanmelden op basis van SMS.

Een gebruiker met een telefoon nummer dat al is ingesteld voor het account wordt weer gegeven met een knop om *SMS-aanmelding in te scha kelen* op hun **Mijn profiel** pagina. Selecteer deze knop en het account is ingeschakeld voor gebruik met aanmelden op basis van SMS en de vorige registratie van Azure AD Multi-Factor Authentication of SSPR.

Zie voor meer informatie over de ervaring van de eind gebruiker [SMS-aanmeld gebruikers ervaring voor telefoon nummer](../user-help/sms-sign-in-explainer.md).

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>Fout bij het instellen van een telefoon nummer op het account van een gebruiker

Als er een fout bericht wordt weer gegeven wanneer u probeert een telefoon nummer in te stellen voor een gebruikers account in de Azure Portal, raadpleegt u de volgende stappen voor probleem oplossing:

1. Zorg ervoor dat u bent ingeschakeld voor de aanmelding op basis van SMS.
1. Controleer of het gebruikers account is ingeschakeld in het beleid voor *tekst bericht* authenticatie methode.
1. Zorg ervoor dat u het telefoon nummer instelt met de juiste indeling, zoals gevalideerd in de Azure Portal (zoals *+ 1 4251234567*).
1. Zorg ervoor dat het telefoon nummer elders in uw Tenant niet wordt gebruikt.
1. Controleer of er geen spraak nummer is ingesteld voor het account. Als er een spraak nummer is ingesteld, verwijdert u dit en probeert u het opnieuw.

## <a name="next-steps"></a>Volgende stappen

Zie [Opties voor wacht woordloze verificatie voor Azure AD][concepts-passwordless]voor meer manieren om u aan te melden bij Azure AD zonder een wacht woord, zoals de Microsoft Authenticator app-of FIDO2-beveiligings sleutels.

U kunt ook de Microsoft Graph REST API bèta gebruiken om de aanmelding op basis van SMS in of [uit][rest-disable] [te scha kelen][rest-enable] .

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md
[rest-enable]: /graph/api/phoneauthenticationmethod-enablesmssignin?view=graph-rest-beta&tabs=http
[rest-disable]: /graph/api/phoneauthenticationmethod-disablesmssignin?view=graph-rest-beta&tabs=http

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
[o365-f1]: https://www.microsoft.com/microsoft-365/business/office-365-f1?market=af
[o365-f3]: https://www.microsoft.com/microsoft-365/business/office-365-f3?activetab=pivot%3aoverviewtab
