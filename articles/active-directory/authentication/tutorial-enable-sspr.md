---
title: Self-service voor wachtwoordherstel voor Azure Active Directory inschakelen
description: In deze zelfstudie leert u hoe u self-service voor wachtwoordherstel voor Azure Active Directory voor een groep gebruikers inschakelt en het proces voor het opnieuw instellen van het wachtwoord kunt testen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 03/25/2021
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39eec4fb6e9907b36908a87c09aceabd0dd1a678
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075163"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Zelfstudie: Gebruikers in staat stellen hun account te ontgrendelen of wachtwoorden opnieuw in te stellen met self-service voor wachtwoordherstel voor Azure Active Directory

Self-service voor wachtwoordherstel (SSPR) voor Azure Active Directory (Azure AD) biedt gebruikers de mogelijkheid hun wachtwoord te wijzigen of opnieuw in te stellen zonder tussenkomst van een beheerder of helpdesk. Als Azure AD het account van een gebruiker vergrendelt of als het wacht woord is verg eten, kunnen ze vragen om zichzelf te deblokkeren en weer aan de slag te gaan. Op deze manier wordt het aantal telefoontjes naar de helpdesk en productieverlies verminderd wanneer een gebruiker zich niet kan aanmelden bij een apparaat of toepassing. We raden u aan deze video [te gebruiken voor het inschakelen en configureren van SSPR in azure AD](https://www.youtube.com/watch?v=rA8TvhNcCvQ). We hebben ook een video voor IT-beheerders bij [het oplossen van de zes meest voorkomende fout berichten voor eind gebruikers met SSPR](https://www.youtube.com/watch?v=9RPrNVLzT8I).

> [!IMPORTANT]
> Aan de hand van deze zelfstudie leert een beheerder hoe self-service voor wachtwoordherstel moet worden ingeschakeld. Ga naar de pagina [micro soft online wacht woord opnieuw instellen](https://passwordreset.microsoftonline.com/) als u een eind gebruiker al hebt geregistreerd voor de selfservice voor het opnieuw instellen van wacht woorden en terug wilt gaan naar uw account.
>
> Als uw IT-team u de mogelijkheid niet heeft gegeven uw eigen wachtwoord opnieuw in te stellen, kunt u contact opnemen met de helpdesk voor meer informatie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Self-service voor wachtwoordherstel inschakelen voor een groep Azure AD-gebruikers
> * Verificatie methoden en registratie opties instellen
> * Het SSPR-proces testen als een gebruiker

## <a name="prerequisites"></a>Vereisten

Als u deze zelf studie wilt volt ooien, hebt u de volgende resources en bevoegdheden nodig:

* Een werkende Azure AD-Tenant met ten minste een Azure AD-gratis of proef licentie ingeschakeld. In de gratis laag werkt SSPR alleen voor cloudgebruikers in Azure AD.
    * Voor latere zelf studies in deze serie hebt u een Azure AD Premium P1 of een proef licentie nodig voor on-premises terugschrijven van wacht woorden.
    * Als dat nodig is, [maakt u gratis een Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een account met de bevoegdheden van een *globale beheerder*.
* Een niet-beheerders gebruiker met een wacht woord dat u weet, zoals *test User*. In deze zelf studie test u de SSPR-ervaring voor eind gebruikers met dit account.
    * Als u een gebruiker wilt maken, raadpleegt u [Snelstart: Nieuwe gebruikers toevoegen aan Azure Active Directory](../fundamentals/add-users-azure-active-directory.md) als een testgebruiker zonder beheerdersbevoegdheden een wachtwoord heeft dat u kent, en u een gebruiker moet maken.
* Een groep waarvan de niet-beheerders gebruiker lid is, vindt *SSPR-test groep*. In deze zelf studie schakelt u SSPR in voor deze groep.
    * Als u een groep wilt maken, raadpleegt u [een basis groep maken en leden toevoegen met behulp van Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Selfservice voor wachtwoordherstel inschakelen

In Azure AD kunt u SSPR inschakelen voor *Geen*, *Geselecteerde* of *Alle* gebruikers. Dankzij deze opsplitsing kunt u een subset van gebruikers kiezen om het registratieproces en de werkstroom van SSPR te testen. Wanneer u vertrouwd bent met het proces en de tijd het beste is om de vereisten met een grotere set gebruikers te communiceren, kunt u een groep gebruikers selecteren om in te scha kelen voor SSPR. U kunt ook SSPR inschakelen voor iedereen in de Azure AD-tenant.

> [!NOTE]
> Op dit moment kunt u slechts één Azure AD-groep voor SSPR inschakelen met behulp van de Azure Portal. Azure AD biedt ondersteuning voor geneste groepen als onderdeel van een bredere implementatie van SSPR. Zorg ervoor dat aan de gebruikers in de groep(en) die u kiest de juiste licenties zijn toegewezen. Er is momenteel geen validatieproces voor deze licentievereisten.

In deze zelf studie stelt u SSPR in voor een set gebruikers in een test groep. Gebruik de *SSPR-test groep* en geef uw eigen Azure AD-groep op als dat nodig is:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een account met machtigingen op het niveau van *globale beheerder*.
1. Zoek en selecteer **Azure Active Directory** en selecteer vervolgens **wacht woord opnieuw instellen** in het menu aan de linkerkant.
1. Selecteer op de pagina **Eigenschappen** onder de optie *self-service voor wacht woord opnieuw instellen ingeschakeld* een **groep selecteren**
1. Blader naar en selecteer uw Azure AD-groep, zoals *SSPR-test groep*, en kies vervolgens *selecteren*.

    [![Een groep in de Azure-portal selecteren waarvoor u self-service voor wachtwoordherstel wilt inschakelen](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png)](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. Als u SSPR wilt inschakelen voor de geselecteerde gebruikers, selecteert u **Opslaan**.

## <a name="select-authentication-methods-and-registration-options"></a>Verificatiemethoden en registratieopties selecteren

Wanneer gebruikers hun account moeten ontgrendelen of hun wacht woord opnieuw moeten instellen, wordt ze gevraagd een andere bevestigings methode op te vragen. Deze extra verificatie factor zorgt ervoor dat Azure AD alleen goedgekeurde SSPR-gebeurtenissen heeft voltooid. U kunt kiezen welke verificatiemethoden u wilt toestaan, op basis van de registratiegegevens die door de gebruiker worden verstrekt.

1. Stel in het menu aan de linkerkant van de pagina **verificatie methoden** het **aantal methoden** in dat moet worden ingesteld op *1*.

    Voor het verbeteren van de beveiliging kunt u het aantal verificatiemethoden verhogen dat nodig is voor SSPR.

1. Kies uit **Methoden voor gebruikers** die uw organisatie wil toestaan. In deze zelfstudie selecteert u de selectievakjes voor de methoden die u wilt inschakelen:

    * *Meldingen via mobiele app*
    * *Code via mobiele app*
    * *E-mail*
    * *Mobiele telefoon*

    U kunt indien nodig andere verificatie methoden, zoals *Office Phone* -of *beveiligings vragen*, inschakelen om aan uw bedrijfs vereisten te voldoen.

1. Als u de verificatiemethoden wilt toepassen, selecteert u **Opslaan**.

Voordat gebruikers hun account kunnen ontgrendelen of een wachtwoord opnieuw kunnen instellen, moeten ze hun contactgegevens registreren. Azure AD gebruikt deze contact gegevens voor de verschillende verificatie methoden die in de vorige stappen zijn ingesteld.

Een beheerder kan deze contactgegevens handmatig opgeven. Gebruikers kunnen naar een registratieportal gaan om de gegevens zelf te verstrekken. In deze zelf studie stelt u Azure AD in om de gebruikers te vragen om registratie bij de volgende keer dat ze zich aanmelden.

1. Selecteer in het menu aan de linkerkant van de **registratie** pagina de optie *Ja* als **u wilt dat gebruikers zich registreren bij het aanmelden**.
1. Stel **Aantal dagen waarna gebruikers wordt gevraagd om de verificatiegegevens opnieuw te bevestigen** in op *180*.

    Het is belang rijk om de contact gegevens up-to-date te houden. Als er verouderde contact gegevens bestaan wanneer een SSPR-gebeurtenis wordt gestart, kan de gebruiker de account niet meer ontgrendelen of hun wacht woord opnieuw instellen.

1. Als u de registratie-instellingen wilt toepassen, selecteert u **Opslaan**.

## <a name="set-up-notifications-and-customizations"></a>Meldingen en aanpassingen instellen

Om gebruikers op de hoogte te houden van de account activiteit, kunt u Azure AD instellen om e-mail meldingen te verzenden wanneer er een SSPR-gebeurtenis plaatsvindt. Deze meldingen betreffen normale gebruikersaccounts en beheerdersaccounts. Voor beheerders accounts bevat deze melding een andere beveiligingslaag wanneer een bevoegd beheerders account wacht woord opnieuw wordt ingesteld met behulp van SSPR. Azure AD stuurt alle globale beheerders een melding wanneer iemand SSPR gebruikt voor een beheerders account.

1. Stel in het menu aan de linkerkant van de pagina **meldingen** de volgende opties in:

   * Stel **Gebruikers een melding tonen over het opnieuw instellen van hun wachtwoord** in op *Ja*.
   * Stel **Alle beheerders waarschuwen wanneer andere beheerders hun wachtwoord opnieuw instellen** in op *Ja*.

1. Als u de meldingsvoorkeuren wilt toepassen, selecteert u **Opslaan**.

Als gebruikers meer hulp nodig hebben bij het SSPR-proces, kunt u de koppeling ' Neem contact op met de beheerder '. De gebruiker kan deze koppeling in het registratie proces voor SSPR selecteren en wanneer ze hun account ontgrendelen of hun wacht woord opnieuw instellen. Om ervoor te zorgen dat uw gebruikers de benodigde ondersteuning krijgen, raden wij u ten zeerste aan een aangepaste Help Desk-e-mail of-URL op te geven.

1. Stel in het menu aan de linkerkant van de pagina **aanpassing** de **koppeling Help Desk aanpassen** in op *Ja*.
1. Geef in het **aangepaste e-mail adres of URL** -veld van de Help Desk een e-mailadres of webpagina-URL op waar uw gebruikers meer hulp kunnen krijgen van uw organisatie, zoals *https: \/ /support.contoso.com/*
1. Als u de aangepaste koppeling wilt toepassen, selecteert u **Opslaan**.

## <a name="test-self-service-password-reset"></a>Selfservice voor wachtwoord opnieuw instellen testen

Als SSPR is ingeschakeld en ingesteld, test u het SSPR-proces met een gebruiker die deel uitmaakt van de groep die u in de vorige sectie hebt geselecteerd, zoals *test-SSPR-Group*. In het volgende voor beeld wordt het *test* account gebruikt. Geef uw eigen gebruikers account op. Het maakt deel uit van de groep die u hebt ingeschakeld voor SSPR in de eerste sectie van deze zelf studie.

> [!NOTE]
> Gebruik een niet-beheerdersaccount als u de self-service voor wachtwoordherstel test. Standaard maakt Azure AD de self-service voor wachtwoord herstel voor beheerders mogelijk. Ze moeten twee verificatie methoden gebruiken om hun wacht woord opnieuw in te stellen. Zie [Verschillen in beleid voor het opnieuw instellen van beheerders](concept-sspr-policy.md#administrator-reset-policy-differences) voor meer informatie.

1. Als u het hand matige registratie proces wilt bekijken, opent u een nieuw browser venster in de modus InPrivate of incognito en bladert u naar *https: \/ /aka.MS/ssprsetup*. Azure AD stuurt gebruikers naar deze registratie Portal wanneer ze zich de volgende keer aanmeldt.
1. Meld u aan met een gebruiker die geen beheerder is, *zoals test User,* en registreer uw aanmeldings gegevens voor de verificatie methoden.
1. Wanneer u klaar bent, selecteert u de gemarkeerde knop **goed** en sluit u het browser venster.
1. Open een nieuw browser venster in de modus InPrivate of incognito en blader naar *https: \/ /aka.MS/sspr*.
1. Voer de gegevens van het account van de niet-beheerder test gebruikers in, zoals *test User*, de tekens van de captcha en selecteer vervolgens **volgende**.

    ![Accountgegevens van gebruikers invoeren om het wachtwoord opnieuw in te stellen](media/tutorial-enable-sspr/password-reset-page.png)

1. Volg de verificatiestappen om uw wachtwoord opnieuw in te stellen. Wanneer u klaar bent, ontvangt u een e-mail melding dat uw wacht woord opnieuw is ingesteld.

## <a name="clean-up-resources"></a>Resources opschonen

In een latere zelf studie in deze serie stelt u wacht woord terugschrijven in. Met deze functie worden wachtwoordwijzigingen vanuit Azure AD SSPR teruggeschreven naar een on-premises AD-omgeving. Als u wilt door gaan met deze zelf studie serie om het terugschrijven van wacht woorden in te stellen, moet u SSPR nu niet uitschakelen.

Als u de SSPR-functionaliteit die u als onderdeel van deze zelf studie hebt ingesteld, niet meer wilt gebruiken, stelt u de status SSPR in op **geen** door de volgende stappen uit te voeren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoek en selecteer **Azure Active Directory** en selecteer vervolgens **wacht woord opnieuw instellen** in het menu aan de linkerkant.
1. Selecteer op de pagina **Eigenschappen** onder de optie *self-service voor wacht woord opnieuw instellen ingeschakeld* **.**
1. Als u de wijziging van de SSPR wilt toepassen, selecteert u **Opslaan**.

## <a name="faqs"></a>Veelgestelde vragen

In deze sectie worden algemene vragen van beheerders en eind gebruikers uitgelegd die het volgende proberen SSPR:

- Waarom wachten federatieve gebruikers nog twee minuten nadat ze **uw wacht woord opnieuw hebben ingesteld** voordat ze wacht woorden kunnen gebruiken die zijn gesynchroniseerd van on-premises?

  Voor federatieve gebruikers waarvan de wacht woorden worden gesynchroniseerd, is de bron van de autoriteit voor de wacht woorden on-premises. Als gevolg daarvan werkt SSPR alleen de on-premises wacht woorden bij. Synchronisatie van wacht woord-hash naar Azure AD wordt elke 2 minuten gepland.

- Wanneer een nieuw gemaakte gebruiker die vooraf is gevuld met SSPR-gegevens, zoals telefoon-en e-mail, de registratie pagina van SSPR **kwijtraakt, hebt u geen toegang meer tot uw account.** wordt weer gegeven als de titel van de pagina. Waarom zie ik het bericht niet voor andere gebruikers die de SSPR-gegevens vooraf hebben ingevuld?

  Een gebruiker die ziet dat **de toegang tot uw account niet verloren gaat.** is een lid van SSPR/gecombineerde registratie groepen die zijn geconfigureerd voor de Tenant. Gebruikers die niet zien, **hebben geen toegang meer tot uw account.** maakt geen deel uit van de SSPR/gecombineerde registratie groepen.

- Waarom wordt de indicator voor wachtwoord sterkte niet weer geven wanneer sommige gebruikers het SSPR-proces door lopen en hun wacht woord opnieuw instellen?

  Gebruikers die geen zwakke/sterke wachtwoord sterkte zien, hebben synchronisatie van wacht woord terugschrijven ingeschakeld. Omdat SSPR het wachtwoord beleid van de on-premises omgeving van de klant niet kan bepalen, kan de wachtwoord sterkte of zwakte niet worden gevalideerd. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u self-service voor wachtwoordherstel van Azure AD voor een bepaalde groep gebruikers ingeschakeld. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Self-service voor wachtwoordherstel inschakelen voor een groep Azure AD-gebruikers
> * Verificatie methoden en registratie opties instellen
> * Het SSPR-proces testen als een gebruiker

> [!div class="nextstepaction"]
> [Azure AD Multi-Factor Authentication inschakelen](./tutorial-enable-azure-mfa.md)
