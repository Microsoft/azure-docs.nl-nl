---
title: Self-service voor wachtwoordherstel voor Azure Active Directory inschakelen
description: In deze zelfstudie leert u hoe u self-service voor wachtwoordherstel voor Azure Active Directory voor een groep gebruikers inschakelt en het proces voor het opnieuw instellen van het wachtwoord kunt testen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59de3123dc991d0314cd9046d83712d17da53d9f
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837818"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Zelfstudie: Gebruikers in staat stellen hun account te ontgrendelen of wachtwoorden opnieuw in te stellen met self-service voor wachtwoordherstel voor Azure Active Directory

Self-service voor wachtwoordherstel (SSPR) voor Azure Active Directory (Azure AD) biedt gebruikers de mogelijkheid hun wachtwoord te wijzigen of opnieuw in te stellen zonder tussenkomst van een beheerder of helpdesk. Als het account van een gebruiker is vergrendeld of als deze zijn of haar wachtwoord is vergeten, kan de gebruiker de vergrendeling aan de hand van instructies zelf ongedaan maken en weer aan het werk gaan. Op deze manier wordt het aantal telefoontjes naar de helpdesk en productieverlies verminderd wanneer een gebruiker zich niet kan aanmelden bij een apparaat of toepassing.

> [!IMPORTANT]
> Aan de hand van deze zelfstudie leert een beheerder hoe self-service voor wachtwoordherstel moet worden ingeschakeld. Als u een eindgebruiker bent die al is geregistreerd voor self-service voor wachtwoordherstel en u weer toegang tot uw account wilt hebben, gaat u naar https://aka.ms/sspr.
>
> Als uw IT-team u de mogelijkheid niet heeft gegeven uw eigen wachtwoord opnieuw in te stellen, kunt u contact opnemen met de helpdesk voor meer informatie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Self-service voor wachtwoordherstel inschakelen voor een groep Azure AD-gebruikers
> * Verificatiemethoden en registratieopties configureren
> * Het SSPR-proces testen als een gebruiker

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u de volgende resources en machtigingen nodig:

* Een werkende Azure AD-tenant met minstens een gratis licentie of proeflicentie voor Azure AD. In de gratis laag werkt SSPR alleen voor cloudgebruikers in Azure AD.
    * Voor latere zelfstudies in deze reeks is een Azure AD Premium P1-licentie of een proeflicentie vereist voor on-premises terugschrijven van wachtwoorden.
    * [Maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) indien nodig.
* Een account met de bevoegdheden van een *globale beheerder*.
* Een niet-beheerder met een wachtwoord dat u kent, zoals *testuser*. In deze zelfstudie test u SSPR voor eindgebruikers met behulp van dit account.
    * Als u een gebruiker wilt maken, raadpleegt u [Snelstart: Nieuwe gebruikers toevoegen aan Azure Active Directory](../fundamentals/add-users-azure-active-directory.md) als een testgebruiker zonder beheerdersbevoegdheden een wachtwoord heeft dat u kent, en u een gebruiker moet maken.
* Een groep waarvan de niet-beheerder lid is, zoals *SSPR-Test-Group*. In deze zelfstudie schakelt u SSPR in voor deze groep.
    * Zie [Een groep maken en leden toevoegen in Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md) als u een groep wilt maken.

## <a name="enable-self-service-password-reset"></a>Selfservice voor wachtwoordherstel inschakelen

In Azure AD kunt u SSPR inschakelen voor *Geen*, *Geselecteerde* of *Alle* gebruikers. Dankzij deze opsplitsing kunt u een subset van gebruikers kiezen om het registratieproces en de werkstroom van SSPR te testen. Wanneer u vertrouwd bent met het proces en de vereisten kunt doorgeven aan een groter aantal gebruikers, kunt u een groep gebruikers selecteren waarvoor u SSPR wilt inschakelen. U kunt ook SSPR inschakelen voor iedereen in de Azure AD-tenant.

> [!NOTE]
>
> Er kan via Azure Portal momenteel slechts één Azure AD-groep worden ingeschakeld voor SSPR. Als onderdeel van een bredere implementatie van SSPR worden geneste groepen ondersteund. Zorg ervoor dat aan de gebruikers in de groep(en) die u kiest de juiste licenties zijn toegewezen. Er is momenteel geen validatieproces voor deze licentievereisten.

In deze zelf studie configureert u SSPR voor een aantal gebruikers in een testgroep. In het volgende voorbeeld wordt de groep *SSPR-Test-Group* gebruikt. Geef uw eigen Azure AD-groep op als dat nodig is:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een account met machtigingen op het niveau van *globale beheerder*.
1. Zoek en selecteer **Azure Active Directory** en kies in het menu aan de linkerkant vervolgens **Wachtwoord opnieuw instellen**.
1. Op de pagina **Eigenschappen** kiest u onder *Selfservice voor wachtwoord opnieuw instellen is ingeschakeld* de optie **Groep selecteren**
1. Selecteer uw Azure AD-groep, bijvoorbeeld *SSPR-Test-Group* en kies vervolgens *Selecteren*.

    [ ![Een groep in de Azure-portal selecteren waarvoor u self-service voor wachtwoordherstel wilt inschakelen](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png) ](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. Als u SSPR wilt inschakelen voor de geselecteerde gebruikers, selecteert u **Opslaan**.

## <a name="select-authentication-methods-and-registration-options"></a>Verificatiemethoden en registratieopties selecteren

Wanneer gebruikers hun account moeten ontgrendelen of hun wachtwoord opnieuw moeten instellen, wordt ze gevraagd naar een extra bevestigingsmethode. Deze extra verificatiefactor zorgt ervoor dat alleen goedgekeurde SSPR-gebeurtenissen worden voltooid. U kunt kiezen welke verificatiemethoden u wilt toestaan, op basis van de registratiegegevens die door de gebruiker worden verstrekt.

1. Op de pagina **Verificatiemethoden** in het menu aan de linkerkant stelt u de optie **Het aantal methoden dat is vereist om het wachtwoord opnieuw in te stellen** in op *1*.

    Voor het verbeteren van de beveiliging kunt u het aantal verificatiemethoden verhogen dat nodig is voor SSPR.

1. Kies uit **Methoden voor gebruikers** die uw organisatie wil toestaan. In deze zelfstudie selecteert u de selectievakjes voor de methoden die u wilt inschakelen:

    * *Meldingen via mobiele app*
    * *Code via mobiele app*
    * *E-mail*
    * *Mobiele telefoon*

    Aanvullende verificatiemethoden, zoals *elefoon (werk)* of *beveiligingsvragen*, kunnen zo nodig worden ingeschakeld om aan uw bedrijfsvereisten te voldoen.

1. Als u de verificatiemethoden wilt toepassen, selecteert u **Opslaan**.

Voordat gebruikers hun account kunnen ontgrendelen of een wachtwoord opnieuw kunnen instellen, moeten ze hun contactgegevens registreren. Deze contactgegevens worden gebruikt voor de verschillende verificatiemethoden die in de vorige stappen zijn geconfigureerd.

Een beheerder kan deze contactgegevens handmatig opgeven. Gebruikers kunnen naar een registratieportal gaan om de gegevens zelf te verstrekken. In deze zelfstudie maakt u een configuratie waarbij de gebruikers om registratie wordt gevraagd wanneer ze zich de volgende keer aanmelden.

1. Selecteer op de pagina **Registratie**, in het menu aan de linkerkant, de optie *Ja* voor **Vereisen dat gebruiker zich bij aanmelding registreren**.
1. Het is belangrijk dat de contactgegevens up-to-date blijven. Als de contactgegevens verouderd zijn wanneer een SSPR-gebeurtenis wordt gestart, kan de gebruiker het account niet meer ontgrendelen of het wachtwoord opnieuw instellen.

    Stel **Aantal dagen waarna gebruikers wordt gevraagd om de verificatiegegevens opnieuw te bevestigen** in op *180*.
1. Als u de registratie-instellingen wilt toepassen, selecteert u **Opslaan**.

## <a name="configure-notifications-and-customizations"></a>Meldingen en aanpassingen configureren

Als u gebruikers op de hoogte wilt houden van accountactiviteiten, kunt u e-mailmeldingen instellen, zodat ze worden verzonden wanneer er een SSPR-gebeurtenis plaatsvindt. Deze meldingen betreffen normale gebruikersaccounts en beheerdersaccounts. Voor beheerdersaccounts biedt deze melding een extra beveiligingslaag wanneer een wachtwoord voor een bevoegd beheerdersaccount opnieuw wordt ingesteld met behulp van SSPR. Alle hoofdbeheerders worden gewaarschuwd wanneer SSPR wordt gebruikt voor een beheerdersaccount.

1. Configureer op de pagina **Meldingen**, vanuit het menu aan de linkerkant, de volgende opties:

   * Stel **Gebruikers een melding tonen over het opnieuw instellen van hun wachtwoord** in op *Ja*.
   * Stel **Alle beheerders waarschuwen wanneer andere beheerders hun wachtwoord opnieuw instellen** in op *Ja*.

1. Als u de meldingsvoorkeuren wilt toepassen, selecteert u **Opslaan**.

Als gebruikers extra hulp nodig hebben bij het SSPR-proces, kunt u de koppeling naar Neem contact op met de beheerder aanpassen. Deze koppeling wordt gebruikt in het SSPR-registratieproces wanneer een gebruiker zijn of haar account ontgrendelt of het wachtwoord opnieuw instelt. Om ervoor te zorgen dat uw gebruikers de benodigde ondersteuning krijgen, is het raadzaam een aangepast e-mailadres of aangepaste URL van de helpdesk te verstrekken.

1. Stel op de pagina **Aanpassing**, in het menu aan de linkerkant, de optie *Helpdeskkoppeling aanpassen* in op **Ja**.
1. Geef in het veld **Aangepast e-mailadres of aangepaste URL van de helpdesk** een e-mailadres of URL op waar gebruikers aanvullende hulp van uw organisatie kunnen krijgen, bijvoorbeeld *`https://support.contoso.com/`* .
1. Als u de aangepaste koppeling wilt toepassen, selecteert u **Opslaan**.

## <a name="test-self-service-password-reset"></a>Selfservice voor wachtwoord opnieuw instellen testen

Als SSPR is ingeschakeld en geconfigureerd, moet u het SSPR-proces testen met een gebruiker die deel uitmaakt van de groep die u in de vorige sectie hebt geselecteerd, bijvoorbeeld *Test-SSPR-Group*. In het volgende voorbeeld wordt het account voor *testuser* gebruikt. Geef uw eigen gebruikersaccount op dat deel uitmaakt van de groep die u in de eerste sectie van deze zelfstudie voor SSPR hebt ingeschakeld.

> [!NOTE]
> Gebruik een niet-beheerdersaccount als u de self-service voor wachtwoordherstel test. Beheerders kunnen standaard gebruikmaken van de self-service voor wachtwoordherstel en moeten twee verificatiemethoden gebruiken om hun wachtwoord opnieuw in te stellen. Zie [Verschillen in beleid voor het opnieuw instellen van beheerders](concept-sspr-policy.md#administrator-reset-policy-differences) voor meer informatie.

1. Als u het handmatige registratieproces wilt zien, opent u een nieuw browservenster in de InPrivate- of incognitomodus en bladert u naar [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup). Als een gebruiker zich de eerstvolgende keer aanmeldt, moet deze worden omgeleid naar deze registratieportal.
1. Meld u aan met een testgebruiker die geen beheerder is, bijvoorbeeld *testuser*, en registreer de contactgegevens voor de verificatiemethoden.
1. Als u klaar bent, selecteert u de knop **Ziet er goed uit** en sluit u het browservenster.
1. Open een nieuw browservenster in de InPrivate- of incognitomodus en browse naar [https://aka.ms/sspr](https://aka.ms/sspr).
1. Voer de accountgegevens in van de gebruikers die geen beheerder zijn, bijvoorbeeld *testuser*, de tekens van de CAPTCHA en selecteer **Volgende**.

    ![Accountgegevens van gebruikers invoeren om het wachtwoord opnieuw in te stellen](media/tutorial-enable-sspr/password-reset-page.png)

1. Volg de verificatiestappen om uw wachtwoord opnieuw in te stellen. Als u klaar bent, ontvangt u per e-mail de melding dat uw wachtwoord opnieuw is ingesteld.

## <a name="clean-up-resources"></a>Resources opschonen

In een van de volgende zelfstudies in deze reeks configureert u het terugschrijven van wachtwoorden. Met deze functie worden wachtwoordwijzigingen vanuit Azure AD SSPR teruggeschreven naar een on-premises AD-omgeving. Als u wilt doorgaan met deze reeks zelfstudies om terugschrijven van wachtwoorden te configureren, schakelt u SSPR nu niet uit.

Als u niet langer gebruik wilt maken van de SSPR-functionaliteit die u als onderdeel van deze zelfstudie hebt geconfigureerd, stelt u in de volgende stappen de SSPR-status in op **Geen**:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek en selecteer **Azure Active Directory** en kies in het menu aan de linkerkant vervolgens **Wachtwoord opnieuw instellen**.
1. Op de pagina **Eigenschappen** kiest u onder *Selfservice voor wachtwoord opnieuw instellen is ingeschakeld* de optie **Geen**.
1. Als u de wijziging van de SSPR wilt toepassen, selecteert u **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u self-service voor wachtwoordherstel van Azure AD voor een bepaalde groep gebruikers ingeschakeld. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Self-service voor wachtwoordherstel inschakelen voor een groep Azure AD-gebruikers
> * Verificatiemethoden en registratieopties configureren
> * Het SSPR-proces testen als een gebruiker

> [!div class="nextstepaction"]
> [Azure AD Multi-Factor Authentication inschakelen](./tutorial-enable-azure-mfa.md)
