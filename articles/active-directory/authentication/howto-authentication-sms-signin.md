---
title: Aanmelden van gebruikers op basis van sms voor Azure Active Directory
description: Meer informatie over het configureren en inschakelen van gebruikers om zich aan te melden bij Azure Active Directory via sms
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: b84d55e2d3a2f49a870c1e57eeed3c5c0caeba4a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530422"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory"></a>Gebruikers configureren en inschakelen voor verificatie op basis van sms met Azure Active Directory 

Ter vereenvoudiging en veilige aanmelding bij toepassingen en services biedt Azure Active Directory (Azure AD) meerdere verificatieopties. Met verificatie op basis van sms kunnen gebruikers zich aanmelden zonder hun gebruikersnaam en wachtwoord op te geven of zelfs te kennen. Nadat het account is gemaakt door een identiteitsbeheerder, kunnen ze hun telefoonnummer invoeren bij de aanmeldingsprompt. Ze ontvangen een verificatiecode via een sms-bericht dat ze kunnen verstrekken om de aanmelding te voltooien. Deze verificatiemethode vereenvoudigt de toegang tot toepassingen en services, met name voor frontline workers.

In dit artikel wordt beschreven hoe u verificatie op basis van sms kunt inschakelen voor bepaalde gebruikers of groepen in Azure AD.

## <a name="before-you-begin"></a>Voordat u begint

U hebt de volgende resources en bevoegdheden nodig om dit artikel te voltooien:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory tenant die is gekoppeld aan uw abonnement.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* U hebt globale *beheerdersbevoegdheden* nodig in uw Azure AD-tenant om verificatie op basis van sms in te schakelen.
* Elke gebruiker die is ingeschakeld in het verificatiemethodebeleid voor tekstberichten, moet een licentie hebben, zelfs als deze niet wordt gebruikt. Elke ingeschakelde gebruiker moet een van de volgende Azure AD-, EMS- en Microsoft 365 hebben:
    * [Microsoft 365 (M365) F1 of F3][m365-firstline-workers-licensing]
    * [Enterprise Mobility + Security (EMS) E3 of E5][ems-licensing] of [Microsoft 365 (M365) E3 of E5][m365-licensing]
    * [Office 365 F3][o365-f3]

## <a name="limitations"></a>Beperkingen

De volgende beperkingen gelden voor verificatie op basis van sms:

* Verificatie op basis van sms is momenteel niet compatibel met Azure AD Multi-Factor Authentication.
* Met uitzondering van Teams is verificatie op basis van sms niet compatibel met native Office-toepassingen.
* Verificatie op basis van sms wordt niet aanbevolen voor B2B-accounts.
* Federatief gebruikers worden niet geverifieerd in de thuisten tenant. Ze worden alleen geverifieerd in de cloud.

## <a name="enable-the-sms-based-authentication-method"></a>De verificatiemethode op basis van sms inschakelen

Er zijn drie belangrijke stappen voor het inschakelen en gebruiken van verificatie op basis van sms in uw organisatie:

* Schakel het beleid voor de verificatiemethode in.
* Selecteer gebruikers of groepen die de verificatiemethode op basis van sms kunnen gebruiken.
* Wijs een telefoonnummer toe voor elk gebruikersaccount.
    * Dit telefoonnummer kan worden toegewezen in de Azure Portal (zoals wordt weergegeven in dit artikel) en in *Mijn medewerkers* of *Mijn account.*

Laten we eerst verificatie op basis van sms inschakelen voor uw Azure AD-tenant.

1. Meld u als globale [Azure Portal][azure-portal] aan bij *de Azure Portal.*
1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer in het navigatiemenu aan de linkerkant van het Azure Active Directory het venster **Security > Authentication methods > Authentication method policy**.

    [![Blader naar en selecteer het venster Verificatiemethodebeleid in het Azure Portal.](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png)](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. Selecteer Tekstbericht in de lijst met beschikbare **verificatiemethoden.**
1. Stel **Inschakelen** in op *Ja.*

    ![Tekstverificatie inschakelen in het venster voor verificatiemethodebeleid](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    U kunt ervoor kiezen om verificatie op basis van sms in te schakelen *voor Alle gebruikers* of Gebruikers en *groepen* selecteren. In de volgende sectie gaat u verificatie op basis van sms inschakelen voor een testgebruiker.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>De verificatiemethode toewijzen aan gebruikers en groepen

Als verificatie op basis van sms is ingeschakeld in uw Azure AD-tenant, selecteert u nu enkele gebruikers of groepen die deze verificatiemethode mogen gebruiken.

1. Stel in het venster verificatiebeleid voor tekstberichten **Doel in** op *Gebruikers selecteren.*
1. Kies Gebruikers **of groepen toevoegen en** selecteer vervolgens een testgebruiker of -groep, zoals *Contoso User* of *Contoso SMS Users.*

    [![Kies gebruikers of groepen die u wilt inschakelen voor verificatie op basis van sms in Azure Portal.](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png)](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. Wanneer u uw gebruikers of groepen hebt geselecteerd, kiest u **Selecteren** en vervolgens **Het** bijgewerkte verificatiemethodebeleid opslaan.

Elke gebruiker die is ingeschakeld in het verificatiemethodebeleid voor tekstberichten, moet een licentie hebben, zelfs als deze niet wordt gebruikt. Zorg ervoor dat u de juiste licenties hebt voor de gebruikers die u in het verificatiemethodebeleid inschakelen, met name wanneer u de functie voor grote groepen gebruikers inschakelen.

## <a name="set-a-phone-number-for-user-accounts"></a>Een telefoonnummer instellen voor gebruikersaccounts

Gebruikers zijn nu ingeschakeld voor verificatie op basis van sms, maar hun telefoonnummer moet zijn gekoppeld aan het gebruikersprofiel in Azure AD voordat ze zich kunnen aanmelden. De gebruiker kan [dit telefoonnummer zelf instellen](../user-help/sms-sign-in-explainer.md) in Mijn *account* of u kunt het telefoonnummer toewijzen met behulp van de Azure Portal. Telefoonnummers kunnen worden ingesteld door *globale beheerders,* *verificatiebeheerders* of *beheerders van bevoegde verificatie.*

Wanneer een telefoonnummer is ingesteld voor sms-teken, is het ook beschikbaar voor gebruik met [Azure AD Multi-Factor Authentication][tutorial-azure-mfa] en selfservice voor wachtwoord opnieuw [instellen.][tutorial-sspr]

1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer in het navigatiemenu aan de linkerkant van het Azure Active Directory **gebruikers.**
1. Selecteer de gebruiker die u in de vorige sectie hebt ingeschakeld voor verificatie op basis van sms, zoals *Contoso-gebruiker,* en selecteer **vervolgens Verificatiemethoden.**
1. Selecteer **+ Verificatiemethode toevoegen**  en kies vervolgens telefoonnummer in het vervolgkeuzemenu Methode **kiezen.**

    Voer het telefoonnummer van de gebruiker in, inclusief het landnummer, zoals *+1 xxxxxxxxx.* De Azure Portal controleert of het telefoonnummer de juiste indeling heeft.

    Selecteer vervolgens in *de vervolgkeuzelijst* Telefoontype de *optie* Mobiel, *Alternatief* mobiel *of* Overige als dat nodig is.

    :::image type="content" source="media/howto-authentication-sms-signin/set-user-phone-number.png" alt-text="Een telefoonnummer instellen voor een gebruiker in de Azure Portal te gebruiken met verificatie op basis van sms":::

    Het telefoonnummer moet uniek zijn in uw tenant. Als u hetzelfde telefoonnummer voor meerdere gebruikers probeert te gebruiken, wordt er een foutbericht weergegeven.

1. Als u het telefoonnummer wilt toepassen op het account van een gebruiker, selecteert **u Toevoegen.**

Wanneer de inrichting is geslaagd, wordt er een vinkje weergegeven voor *sms-aanmelding ingeschakeld.*

## <a name="test-sms-based-sign-in"></a>Aanmelding op basis van sms testen

Als u het gebruikersaccount wilt testen dat nu is ingeschakeld voor aanmelding via sms, moet u de volgende stappen uitvoeren:

1. Open een nieuw InPrivate- of Incognito-webbrowservenster om [https://www.office.com][office]
1. Selecteer aanmelden in de **rechterbovenhoek.**
1. Voer bij de aanmeldingsprompt het telefoonnummer in dat is gekoppeld aan de gebruiker in de vorige sectie en selecteer **volgende.**

    ![Voer een telefoonnummer in bij de aanmeldingsprompt voor de testgebruiker](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. Er wordt een sms-bericht verzonden naar het opgegeven telefoonnummer. Voer de 6-cijferige code in het tekstbericht in bij de aanmeldingsprompt om het aanmeldingsproces te voltooien.

    ![Voer de bevestigingscode in die via sms naar het telefoonnummer van de gebruiker wordt verzonden](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. De gebruiker is nu aangemeld zonder een gebruikersnaam of wachtwoord op te geven.

## <a name="troubleshoot-sms-based-sign-in"></a>Problemen met aanmelden via sms oplossen

De volgende scenario's en stappen voor probleemoplossing kunnen worden gebruikt als u problemen hebt met het inschakelen en gebruiken van aanmelding via sms.

### <a name="phone-number-already-set-for-a-user-account"></a>Telefoonnummer dat al is ingesteld voor een gebruikersaccount

Als een gebruiker zich al heeft geregistreerd voor Azure AD Multi-Factor Authentication en/of selfservice voor wachtwoord opnieuw instellen (SSPR), is er al een telefoonnummer gekoppeld aan zijn account. Dit telefoonnummer is niet automatisch beschikbaar voor gebruik met aanmelding via sms.

Een gebruiker die al een telefoonnummer voor zijn of haar account heeft ingesteld, wordt op de pagina **Mijn** profiel een knop Inschakelen voor *sms-aanmelding* weergegeven. Selecteer deze knop. Het account is ingeschakeld voor gebruik met aanmelding via sms en de vorige Azure AD Multi-Factor Authentication- of SSPR-registratie.

Zie Gebruikerservaring voor sms-aanmelding voor telefoonnummer voor meer informatie over de [eindgebruikerservaring.](../user-help/sms-sign-in-explainer.md)

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>Fout bij het instellen van een telefoonnummer voor het account van een gebruiker

Als er een foutbericht wordt weergegeven wanneer u een telefoonnummer voor een gebruikersaccount in de Azure Portal, controleert u de volgende stappen voor probleemoplossing:

1. Zorg ervoor dat u bent ingeschakeld voor de aanmelding op basis van sms.
1. Controleer of het gebruikersaccount is ingeschakeld in het verificatiemethodebeleid *voor* tekstberichten.
1. Zorg ervoor dat u het telefoonnummer in de juiste notatie in stelt, zoals gevalideerd in de Azure Portal (zoals *+1 4251234567).*
1. Zorg ervoor dat het telefoonnummer niet elders in uw tenant wordt gebruikt.
1. Controleer of er geen spraaknummer is ingesteld voor het account. Als een spraaknummer is ingesteld, verwijdert u het nummer en probeert u het opnieuw.

## <a name="next-steps"></a>Volgende stappen

Zie Verificatieopties zonder wachtwoord voor [Azure][concepts-passwordless]AD voor aanvullende manieren om u aan te melden bij Azure AD zonder wachtwoord, zoals de Microsoft Authenticator-app of FIDO2-beveiligingssleutels.

U kunt ook de Microsoft Graph REST API om aanmelding [op][rest-enable] basis van [sms][rest-disable] in of uit te schakelen.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md
[rest-enable]: /graph/api/phoneauthenticationmethod-enablesmssignin?tabs=http
[rest-disable]: /graph/api/phoneauthenticationmethod-disablesmssignin?tabs=http

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
[o365-f1]: https://www.microsoft.com/microsoft-365/business/office-365-f1?market=af
[o365-f3]: https://www.microsoft.com/microsoft-365/business/office-365-f3?activetab=pivot%3aoverviewtab
