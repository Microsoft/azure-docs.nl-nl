---
title: Self-service voor wachtwoordherstel voor Azure Active Directory inschakelen
description: In deze zelfstudie leert u hoe u self-service voor wachtwoordherstel voor Azure Active Directory voor een groep gebruikers inschakelt en het proces voor het opnieuw instellen van het wachtwoord kunt testen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 04/21/2021
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c18dd231a708030e3a454ab8708e3f0f11dbecf
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861819"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Zelfstudie: Gebruikers in staat stellen hun account te ontgrendelen of wachtwoorden opnieuw in te stellen met self-service voor wachtwoordherstel voor Azure Active Directory

Self-service voor wachtwoordherstel (SSPR) voor Azure Active Directory (Azure AD) biedt gebruikers de mogelijkheid hun wachtwoord te wijzigen of opnieuw in te stellen zonder tussenkomst van een beheerder of helpdesk. Als Azure AD het account van een gebruiker vergrendelt of het wachtwoord is vergeten, kunnen ze de prompts volgen om zichzelf te deblokkeren en weer aan het werk te gaan. Op deze manier wordt het aantal telefoontjes naar de helpdesk en productieverlies verminderd wanneer een gebruiker zich niet kan aanmelden bij een apparaat of toepassing. We raden deze video aan over [SSPR inschakelen en configureren in Azure AD.](https://www.youtube.com/watch?v=rA8TvhNcCvQ) We hebben ook een video voor IT-beheerders over het oplossen van de zes meest voorkomende foutberichten voor eindgebruikers [met SSPR.](https://www.youtube.com/watch?v=9RPrNVLzT8I)

> [!IMPORTANT]
> Aan de hand van deze zelfstudie leert een beheerder hoe self-service voor wachtwoordherstel moet worden ingeschakeld. Als u een eindgebruiker bent die al is geregistreerd voor selfservice voor wachtwoord opnieuw instellen en u weer toegang wilt krijgen tot uw account, gaat u naar de pagina Voor wachtwoord opnieuw instellen van [Microsoft Online.](https://passwordreset.microsoftonline.com/)
>
> Als uw IT-team u de mogelijkheid niet heeft gegeven uw eigen wachtwoord opnieuw in te stellen, kunt u contact opnemen met de helpdesk voor meer informatie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Self-service voor wachtwoordherstel inschakelen voor een groep Azure AD-gebruikers
> * Verificatiemethoden en registratieopties instellen
> * Het SSPR-proces testen als een gebruiker

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u de volgende resources en bevoegdheden nodig:

* Een werkende Azure AD-tenant met ten minste een gratis Azure AD-licentie of proeflicentie ingeschakeld. In de gratis laag werkt SSPR alleen voor cloudgebruikers in Azure AD. Wachtwoordwijziging wordt ondersteund in de gratis laag, maar wachtwoord opnieuw instellen niet. 
    * Voor latere zelfstudies in deze reeks hebt u een licentie Azure AD Premium P1 of een proeflicentie nodig voor het terugschrijven van on-premises wachtwoorden.
    * Maak indien nodig [gratis een Azure-account.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Een account met de bevoegdheden van een *globale beheerder*.
* Een niet-beheerder met een wachtwoord dat u kent, zoals *testuser*. In deze zelfstudie gaat u de ervaring met SSPR voor eindgebruikers testen met dit account.
    * Als u een gebruiker wilt maken, raadpleegt u [Snelstart: Nieuwe gebruikers toevoegen aan Azure Active Directory](../fundamentals/add-users-azure-active-directory.md) als een testgebruiker zonder beheerdersbevoegdheden een wachtwoord heeft dat u kent, en u een gebruiker moet maken.
* Een groep waar de niet-beheerder lid van is, is bijvoorbeeld *SSPR-Test-Group.* In deze zelfstudie gaat u SSPR inschakelen voor deze groep.
    * Als u een groep wilt maken, zie [Een basisgroep maken en leden toevoegen](../fundamentals/active-directory-groups-create-azure-portal.md)met behulp van Azure Active Directory .

## <a name="enable-self-service-password-reset"></a>Selfservice voor wachtwoordherstel inschakelen

In Azure AD kunt u SSPR inschakelen voor *Geen*, *Geselecteerde* of *Alle* gebruikers. Dankzij deze opsplitsing kunt u een subset van gebruikers kiezen om het registratieproces en de werkstroom van SSPR te testen. Wanneer u vertrouwd bent met het proces en het tijd is om de vereisten te communiceren met een bredere set gebruikers, kunt u een groep gebruikers selecteren die u wilt inschakelen voor SSPR. U kunt ook SSPR inschakelen voor iedereen in de Azure AD-tenant.

> [!NOTE]
> Op dit moment kunt u slechts één Azure AD-groep inschakelen voor SSPR met behulp van de Azure Portal. Als onderdeel van een bredere implementatie van SSPR ondersteunt Azure AD geneste groepen. Zorg ervoor dat aan de gebruikers in de groep(en) die u kiest de juiste licenties zijn toegewezen. Er is momenteel geen validatieproces voor deze licentievereisten.

In deze zelfstudie stelt u SSPR in voor een set gebruikers in een testgroep. Gebruik de *SSPR-Test-Group en* geef zo nodig uw eigen Azure AD-groep op:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een account met machtigingen op het niveau van *globale beheerder*.
1. Zoek en selecteer **Azure Active Directory** selecteer vervolgens **Wachtwoord opnieuw instellen** in het menu aan de linkerkant.
1. Selecteer op **de** pagina Eigenschappen onder *selfservice* voor wachtwoord opnieuw instellen is ingeschakeld de optie **Groep selecteren**
1. Blader naar en selecteer uw Azure AD-groep, zoals *SSPR-Test-Group,* en kies *vervolgens Selecteren.*

    [![Een groep in de Azure-portal selecteren waarvoor u self-service voor wachtwoordherstel wilt inschakelen](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png)](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. Als u SSPR wilt inschakelen voor de geselecteerde gebruikers, selecteert u **Opslaan**.

## <a name="select-authentication-methods-and-registration-options"></a>Verificatiemethoden en registratieopties selecteren

Wanneer gebruikers hun account moeten ontgrendelen of hun wachtwoord opnieuw moeten instellen, wordt ze gevraagd om een andere bevestigingsmethode. Deze extra verificatiefactor zorgt ervoor dat Azure AD alleen goedgekeurde SSPR-gebeurtenissen heeft voltooid. U kunt kiezen welke verificatiemethoden u wilt toestaan, op basis van de registratiegegevens die door de gebruiker worden verstrekt.

1. Stel in het menu aan de linkerkant **van** de pagina Verificatiemethoden het Aantal methoden in dat is vereist om opnieuw in **te stellen** *op 1.*

    Voor het verbeteren van de beveiliging kunt u het aantal verificatiemethoden verhogen dat nodig is voor SSPR.

1. Kies uit **Methoden voor gebruikers** die uw organisatie wil toestaan. In deze zelfstudie selecteert u de selectievakjes voor de methoden die u wilt inschakelen:

    * *Meldingen via mobiele app*
    * *Code via mobiele app*
    * *E-mail*
    * *Mobiele telefoon*

    U kunt naar behoefte andere verificatiemethoden inschakelen, zoals *Telefoon* op kantoor of Beveiligingsvragen, om aan uw bedrijfsvereisten te voldoen.

1. Als u de verificatiemethoden wilt toepassen, selecteert u **Opslaan**.

Voordat gebruikers hun account kunnen ontgrendelen of een wachtwoord opnieuw kunnen instellen, moeten ze hun contactgegevens registreren. Azure AD gebruikt deze contactgegevens voor de verschillende verificatiemethoden die in de vorige stappen zijn ingesteld.

Een beheerder kan deze contactgegevens handmatig opgeven. Gebruikers kunnen naar een registratieportal gaan om de gegevens zelf te verstrekken. In deze zelfstudie stelt u Azure AD in om de gebruikers de volgende keer dat ze zich aanmelden om registratie te vragen.

1. Selecteer ja in het menu aan de linkerkant **van de pagina** Registratie voor Vereisen dat gebruikers zich registreren bij **aanmelding.** 
1. Stel **Aantal dagen waarna gebruikers wordt gevraagd om de verificatiegegevens opnieuw te bevestigen** in op *180*.

    Het is belangrijk om de contactgegevens up-to-date te houden. Als er verouderde contactgegevens bestaan wanneer een SSPR-gebeurtenis wordt gestart, kan de gebruiker mogelijk geen account ontgrendelen of het wachtwoord opnieuw instellen.

1. Als u de registratie-instellingen wilt toepassen, selecteert u **Opslaan**.

## <a name="set-up-notifications-and-customizations"></a>Meldingen en aanpassingen instellen

Als u gebruikers op de hoogte wilt houden van accountactiviteit, kunt u Azure AD instellen om e-mailmeldingen te verzenden wanneer er een SSPR-gebeurtenis wordt uitgevoerd. Deze meldingen betreffen normale gebruikersaccounts en beheerdersaccounts. Voor beheerdersaccounts biedt deze melding een andere laag van bewustzijn wanneer het wachtwoord van een bevoorrecht beheerdersaccount opnieuw wordt ingesteld met SSPR. Azure AD informeert alle globale beheerders wanneer iemand SSPR gebruikt voor een beheerdersaccount.

1. Stel in het menu aan de linkerkant van **de pagina Meldingen** de volgende opties in:

   * Stel **Gebruikers een melding tonen over het opnieuw instellen van hun wachtwoord** in op *Ja*.
   * Stel **Alle beheerders waarschuwen wanneer andere beheerders hun wachtwoord opnieuw instellen** in op *Ja*.

1. Als u de meldingsvoorkeuren wilt toepassen, selecteert u **Opslaan**.

Als gebruikers meer hulp nodig hebben bij het SSPR-proces, kunt u de koppeling Contact opnemen met uw beheerder aanpassen. Gebruikers kunnen deze koppeling selecteren in het SSPR-registratieproces en wanneer ze hun account ontgrendelen of hun wachtwoord opnieuw instellen. Om ervoor te zorgen dat uw gebruikers de benodigde ondersteuning krijgen, raden we u ten zeerste aan een aangepaste e-mail of URL van de helpdesk op te geven.

1. Stel in het menu aan de linkerkant **van** de pagina Aanpassing de koppeling Helpdesk **aanpassen in** op *Ja.*
1. Geef in het veld Aangepast **e-mailadres** of aangepaste URL van de helpdesk een e-mailadres of webpagina-URL op waar uw gebruikers meer hulp kunnen krijgen van uw organisatie, zoals *https: \/ /support.contoso.com/*
1. Als u de aangepaste koppeling wilt toepassen, selecteert u **Opslaan**.

## <a name="test-self-service-password-reset"></a>Selfservice voor wachtwoord opnieuw instellen testen

Als SSPR is ingeschakeld en ingesteld, test u het SSPR-proces met een gebruiker die deel uitmaakt van de groep die u in de vorige sectie hebt geselecteerd, zoals *Test-SSPR-Group.* In het volgende voorbeeld wordt het *testuser-account* gebruikt. Geef uw eigen gebruikersaccount op. Het maakt deel uit van de groep die u hebt ingeschakeld voor SSPR in de eerste sectie van deze zelfstudie.

> [!NOTE]
> Gebruik een niet-beheerdersaccount als u de self-service voor wachtwoordherstel test. Standaard schakelt Azure AD selfservice voor wachtwoord opnieuw instellen in voor beheerders. Ze moeten twee verificatiemethoden gebruiken om hun wachtwoord opnieuw in te stellen. Zie [Verschillen in beleid voor het opnieuw instellen van beheerders](concept-sspr-policy.md#administrator-reset-policy-differences) voor meer informatie.

1. Als u het handmatige registratieproces wilt zien, opent u een nieuw browservenster in de InPrivate- of incognitomodus en bladert u naar *https: \/ /aka.ms/ssprsetup*. Azure AD leidt gebruikers naar deze registratieportal wanneer ze zich de volgende keer aanmelden.
1. Meld u aan met een testgebruiker die geen beheerder is, zoals *testuser,* en registreer de contactgegevens van uw verificatiemethoden.
1. Als u klaar bent, selecteert u de knop **Ziet er goed uit** en sluit u het browservenster.
1. Open een nieuw browservenster in de InPrivate- of incognitomodus en blader naar *https: \/ /aka.ms/sspr*.
1. Voer de accountgegevens van uw testgebruikers die geen beheerder zijn in, zoals *testuser*, de tekens uit de CAPTCHA en selecteer **Volgende.**

    ![Accountgegevens van gebruikers invoeren om het wachtwoord opnieuw in te stellen](media/tutorial-enable-sspr/password-reset-page.png)

1. Volg de verificatiestappen om uw wachtwoord opnieuw in te stellen. Wanneer u klaar bent, ontvangt u een e-mailmelding dat uw wachtwoord opnieuw is ingesteld.

## <a name="clean-up-resources"></a>Resources opschonen

In een latere zelfstudie in deze reeks gaat u wachtwoord terugschrijven instellen. Met deze functie worden wachtwoordwijzigingen vanuit Azure AD SSPR teruggeschreven naar een on-premises AD-omgeving. Als u wilt doorgaan met deze reeks zelfstudies om wachtwoord terugschrijven in te stellen, moet u SSPR nu niet uitschakelen.

Als u de SSPR-functionaliteit die u hebt ingesteld als onderdeel van deze zelfstudie niet meer wilt gebruiken, stelt u de SSPR-status in op Geen door de volgende stappen uit te voeren: 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoek en selecteer **Azure Active Directory** selecteer vervolgens **Wachtwoord opnieuw instellen** in het menu aan de linkerkant.
1. Selecteer op **de** pagina Eigenschappen onder *Selfservice* voor wachtwoord opnieuw instellen is ingeschakeld de optie **Geen.**
1. Als u de wijziging van de SSPR wilt toepassen, selecteert u **Opslaan**.

## <a name="faqs"></a>Veelgestelde vragen

In deze sectie worden veelvoorkomende vragen uitgelegd van beheerders en eindgebruikers die SSPR proberen:

- Waarom wachten federatief gebruikers maximaal twee minuten  nadat uw wachtwoord opnieuw is ingesteld wordt gezien voordat ze wachtwoorden kunnen gebruiken die on-premises zijn gesynchroniseerd?

  Voor federatief gebruikers van wie de wachtwoorden zijn gesynchroniseerd, is de bron van autoriteit voor de wachtwoorden on-premises. Als gevolg hiervan werkt SSPR alleen de on-premises wachtwoorden bij. Wachtwoord-hashsynchronisatie terug naar Azure AD wordt elke 2 minuten gepland.

- Wanneer een nieuwe gebruiker die vooraf is ingevuld met SSPR-gegevens, zoals telefoon en e-mail, de SSPR-registratiepagina bezoekt, kunt u de toegang tot **uw account niet verliezen.** wordt weergegeven als de titel van de pagina. Waarom wordt het bericht niet weergegeven voor andere gebruikers die vooraf SSPR-gegevens hebben ingevuld?

  Een gebruiker die de toegang **tot uw account niet verliest!** is lid van SSPR/gecombineerde registratiegroepen die zijn geconfigureerd voor de tenant. Gebruikers die uw account niet zien, krijgen **geen toegang meer!** maakten geen deel uit van de SSPR/gecombineerde registratiegroepen.

- Waarom zien sommige gebruikers de wachtwoordsterkte-indicator niet wanneer ze het SSPR-proces doorlopen en hun wachtwoord opnieuw instellen?

  Gebruikers die geen zwakke/sterke wachtwoordsterkte zien, hebben gesynchroniseerd wachtwoord terugschrijven ingeschakeld. Omdat SSPR het wachtwoordbeleid van de on-premises omgeving van de klant niet kan bepalen, kan deze de wachtwoordsterkte of het zwakke punt niet valideren. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u self-service voor wachtwoordherstel van Azure AD voor een bepaalde groep gebruikers ingeschakeld. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Self-service voor wachtwoordherstel inschakelen voor een groep Azure AD-gebruikers
> * Verificatiemethoden en registratieopties instellen
> * Het SSPR-proces testen als een gebruiker

> [!div class="nextstepaction"]
> [Azure AD Multi-Factor Authentication inschakelen](./tutorial-enable-azure-mfa.md)
