---
title: Self-service voor wacht woord opnieuw instellen aanpassen-Azure Active Directory
description: Meer informatie over het aanpassen van de gebruikers weergave en ervarings opties voor de selfservice voor wachtwoord herstel van Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3766db0d5070f15216ac612353e2c25737ad092a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96741623"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>De gebruikers ervaring voor Azure Active Directory self-service voor wachtwoord herstel aanpassen

Self-service voor wachtwoord herstel (SSPR) biedt gebruikers in Azure Active Directory (Azure AD) de mogelijkheid om hun wacht woord te wijzigen of opnieuw in te stellen, zonder beheerders-of Help Desk. Als het account van een gebruiker is vergrendeld of als deze zijn of haar wachtwoord is vergeten, kan de gebruiker de vergrendeling aan de hand van instructies zelf ongedaan maken en weer aan het werk gaan. Op deze manier wordt het aantal telefoontjes naar de helpdesk en productieverlies verminderd wanneer een gebruiker zich niet kan aanmelden bij een apparaat of toepassing.

Als u de SSPR-ervaring voor gebruikers wilt verbeteren, kunt u het uiterlijk aanpassen van de pagina voor het opnieuw instellen van het wacht woord, e-mail meldingen of aanmeldings pagina's. Met deze aanpassings opties kunt u het duidelijk maken dat de gebruiker zich op de juiste plaats bevindt, en ze hun vertrouwen geven om toegang te krijgen tot bedrijfs resources.
    
Dit artikel laat u zien hoe u de SSPR-e-mail koppeling kunt aanpassen voor gebruikers, huis stijl en AD FS aanmeldings pagina.

## <a name="customize-the-contact-your-administrator-link"></a>Pas de koppeling ' Neem contact op met de beheerder ' aan

De koppeling ' Neem contact op met uw beheerder ' wordt weer gegeven in de portal voor het opnieuw instellen van wacht woorden om gebruikers te helpen bereiken voor hulp bij het opnieuw instellen van een wacht woord. Als een gebruiker deze koppeling selecteert, doet dit een van de volgende twee dingen:

* Als deze contact persoon de standaard status heeft, wordt er een e-mail bericht naar uw beheerders verzonden en wordt u gevraagd om hulp te bieden bij het wijzigen van het wacht woord van de gebruiker. In het volgende voor beeld wordt dit e-mail bericht weer gegeven:

    ![Voorbeeld aanvraag voor het opnieuw instellen van e-mail die is verzonden naar de beheerder](./media/howto-sspr-customization/sspr-contact-admin.png)

* Als u dit hebt aangepast, wordt de gebruiker naar een webpagina of e-mail adres gestuurd dat door de beheerder is opgegeven.
    * Als u dit aanpast, is het raadzaam om dit in te stellen op een van de gebruikers die al bekend zijn met het oog op ondersteuning.

    > [!WARNING]
    > Als u deze instelling aanpast met een e-mail adres en account waarvoor een wacht woord opnieuw moet worden ingesteld, kan de gebruiker mogelijk niet om hulp vragen.

### <a name="default-email-behavior"></a>Standaard gedrag van e-mail

Het e-mail adres van de standaard contact persoon wordt in de volgende volg orde naar de ontvangers verzonden:

1. Als de rol *helpdesk beheerder* of *Wachtwoord beheerder* is toegewezen, worden beheerders met deze rollen hiervan op de hoogte gebracht.
1. Als er geen helpdesk beheerder of wachtwoord beheerder is toegewezen, worden beheerders met de rol *gebruikers beheerder* hiervan op de hoogte gesteld.
1. Als geen van de vorige rollen zijn toegewezen, worden de *globale beheerders* hiervan op de hoogte gesteld.

In alle gevallen worden Maxi maal 100 ontvangers hiervan op de hoogte gebracht.

Zie [beheerders rollen toewijzen in azure Active Directory](../roles/permissions-reference.md)voor meer informatie over de verschillende beheerders rollen en hoe u deze toewijst.

### <a name="disable-contact-your-administrator-emails"></a>E-mail berichten van contact opnemen met de beheerder uitschakelen

Als uw organisatie geen beheerders wil waarschuwen over aanvragen voor het opnieuw instellen van wacht woorden, kunnen de volgende configuratie opties worden gebruikt:

* De Help Desk aanpassen om een web-URL of mailto:-adres op te geven dat gebruikers kunnen gebruiken om hulp te krijgen. Deze optie wordt onder **wacht woord opnieuw instellen**  >  **aanpassen**  >  **aangepaste Help Desk e-mail of URL**.
* Schakel selfservice voor wacht woord opnieuw instellen voor alle gebruikers in. Deze optie bevindt zich onder instellingen voor **wacht woord opnieuw instellen**  >  . Als u niet wilt dat gebruikers hun eigen wacht woord opnieuw instellen, kunt u de toegang tot een lege groep bereiken. *Deze optie wordt niet aanbevolen.*

## <a name="customize-the-sign-in-page-and-access-panel"></a>De aanmeldings pagina en het toegangs venster aanpassen

U kunt de aanmeldings pagina aanpassen, zoals het toevoegen van een logo dat wordt weer gegeven samen met de afbeelding die past bij de huis stijl van uw bedrijf. Voor meer informatie over het configureren van de huis stijl van uw bedrijf, Zie [bedrijfs huisstijl toevoegen aan uw aanmeldings pagina in azure AD](../fundamentals/customize-branding.md).

De afbeeldingen die u kiest, worden in de volgende omstandigheden weer gegeven:

* Nadat een gebruiker de gebruikers naam heeft ingevoerd
* Als de gebruiker toegang heeft tot de aangepaste URL:
   * Door de `whr` para meter door te geven aan de pagina voor het opnieuw instellen van wacht woorden, zoals `https://login.microsoftonline.com/?whr=contoso.com`
   * Door de `username` para meter door te geven aan de pagina voor het opnieuw instellen van wacht woorden, zoals `https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>Mapnaam

Om ervoor te zorgen dat dingen meer gebruikers vriendelijk zijn, kunt u de naam van de organisatie wijzigen in de portal en in automatische communicatie. Als u het kenmerk van de mapnaam wilt wijzigen in de Azure Portal, bladert u naar **Azure Active Directory**  >  **Eigenschappen**. Deze beschrijvende organisatie naam optie is het meest zichtbaar in automatische e-mail berichten, zoals in de volgende voor beelden:

* De beschrijvende naam in het e-mail bericht, bijvoorbeeld '*micro soft* namens de CONTOSO-demo '
* De regel onderwerp in het e-mail bericht, bijvoorbeeld '*CONTOSO-demo account e-mail verificatie code*'

## <a name="customize-the-ad-fs-sign-in-page"></a>De AD FS aanmeldings pagina aanpassen

Als u Active Directory Federation Services (AD FS) gebruikt voor aanmeldings gebeurtenissen voor gebruikers, kunt u een koppeling toevoegen aan de aanmeldings pagina met behulp van de richt lijnen in het artikel om een beschrijving van de [aanmeldings pagina toe te voegen](/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Gebruikers een koppeling naar de pagina geven om ze de SSPR-werk stroom in te voeren, zoals *https://passwordreset.microsoftonline.com* . Als u een koppeling naar de aanmeldings pagina van AD FS wilt toevoegen, gebruikt u de volgende opdracht op uw AD FS server:

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>Volgende stappen

Zie [rapportage-opties voor Azure AD-wachtwoord beheer voor](howto-sspr-reporting.md)meer informatie over het gebruik van SSPR in uw omgeving.

Zie problemen met het [opnieuw instellen van wacht woorden oplossen](./troubleshoot-sspr.md) als u of gebruikers problemen hebben met SSPR