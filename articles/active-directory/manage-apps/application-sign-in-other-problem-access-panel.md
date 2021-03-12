---
title: Problemen oplossen bij het aanmelden bij een toepassing vanuit Azure AD mijn apps
description: Problemen oplossen bij het aanmelden bij een toepassing vanuit Azure AD mijn apps
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: a54c7d6cc4ccf1d9f42702be030598ad1edfab24
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225146"
---
# <a name="troubleshoot-problems-signing-in-to-an-application-from-azure-ad-my-apps"></a>Problemen oplossen bij het aanmelden bij een toepassing vanuit Azure AD mijn apps

Mijn apps is een webgebaseerde portal die een gebruiker met een werk-of school account in Azure Active Directory (Azure AD) in staat stelt om Cloud toepassingen te bekijken en te starten waartoe de Azure AD-beheerder hen toegang heeft verleend. Mijn apps wordt geopend met een webbrowser op [https://myapps.microsoft.com](https://myapps.microsoft.com) .

Voor meer informatie over het gebruik van Azure AD als een id-provider voor een app, Zie [Wat is toepassings beheer in azure AD](what-is-application-management.md). Bekijk de Quick Start- [serie op toepassings beheer](view-applications-portal.md)om snel aan de slag te gaan.

Deze toepassingen worden geconfigureerd namens de gebruiker in de Azure AD-Portal. De toepassing moet op de juiste wijze zijn geconfigureerd en toegewezen aan de gebruiker of een groep waarvan de gebruiker lid is om de toepassing in mijn apps weer te geven. 

Het type apps dat een gebruiker kan zien, kan worden weer gegeven in de volgende categorieën:
-   Microsoft 365 toepassingen
-   Toepassingen van micro soft en derden die zijn geconfigureerd met SSO op basis van Federatie
-   SSO-toepassingen op basis van wacht woorden
-   Toepassingen met bestaande SSO-oplossingen

Hier volgen enkele dingen die u kunt controleren of een app wordt weer gegeven of niet wordt weer gegeven.
- Zorg ervoor dat de app is toegevoegd aan Azure AD en controleer of de gebruiker is toegewezen. Zie voor meer informatie de Quick Start- [serie over toepassings beheer](add-application-portal.md).
- Als een app onlangs is toegevoegd, moet de gebruiker zich opnieuw afmelden en weer aanmelden. 
- Als de app een licentie vereist, zoals Office, controleert u of de gebruiker de juiste licentie heeft toegewezen.
- De tijd die nodig is voor het wijzigen van de licentie wijzigingen kan variëren, afhankelijk van de grootte en complexiteit van de groep.

## <a name="general-issues-to-check-first"></a>Algemene problemen om eerst te controleren

-   Zorg ervoor dat de webbrowser aan de vereisten voldoet. Zie [mijn apps ondersteunde browsers](../user-help/my-apps-portal-end-user-access.md).
-   Zorg ervoor dat de browser van de gebruiker de URL van de toepassing heeft toegevoegd aan de **vertrouwde sites**.
-   Controleer of de toepassing correct is **geconfigureerd** .
-   Zorg ervoor dat het gebruikers account is **ingeschakeld** voor aanmeldingen.
-   Controleer of het account van de gebruiker **niet is vergrendeld.**
-   Zorg ervoor dat het wacht woord van de gebruiker **niet is verlopen of is verg eten.**
-   Zorg ervoor dat **multi-factor Authentication** de gebruikers toegang niet blokkeert.
-   Zorg ervoor dat het beleid voor **voorwaardelijke toegang** of het beleid voor **identiteits beveiliging** de gebruikers toegang niet blokkeert.
-   Zorg ervoor dat de **contact gegevens** van de verificatie van een gebruiker up-to-date zijn om multi-factor Authentication of beleid voor voorwaardelijke toegang te kunnen afdwingen.
-   Probeer ook de cookies van uw browser te wissen en opnieuw aan te melden.

## <a name="problems-with-the-users-account"></a>Problemen met het account van de gebruiker
Toegang tot mijn apps kan worden geblokkeerd vanwege een probleem met het account van de gebruiker. Hieronder vindt u enkele manieren waarop u problemen met gebruikers en hun account instellingen kunt oplossen en oplossen:
-   [Controleren of een gebruikers account bestaat in Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)
-   [De account status van een gebruiker controleren](#check-a-users-account-status)
-   [Het wacht woord van een gebruiker opnieuw instellen](#reset-a-users-password)
-   [Selfservice voor wachtwoordherstel inschakelen](#enable-self-service-password-reset)
-   [De multi-factor Authentication-status van een gebruiker controleren](#check-a-users-multi-factor-authentication-status)
-   [De contact gegevens voor de verificatie van een gebruiker controleren](#check-a-users-authentication-contact-info)
-   [De groepslid maatschappen van een gebruiker controleren](#check-a-users-group-memberships)
-   [Controleren of een gebruiker meer dan 999 app-roltoewijzingen heeft](#check-if-a-user-has-more-than-999-app-role-assignments)
-   [De toegewezen licenties van een gebruiker controleren](#check-a-users-assigned-licenses)
-   [Een licentie toewijzen aan een gebruiker](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Controleren of een gebruikers account bestaat in Azure Active Directory
Voer de volgende stappen uit om te controleren of het account van een gebruiker aanwezig is:
1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**
2.  Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd navigatie menu te selecteren.
3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.
4.  Selecteer **gebruikers en groepen** in het navigatie menu.
5.  Selecteer **alle gebruikers**.
6.  **Zoek** naar de gebruiker die u wilt interesseren en **Selecteer de rij** die u wilt selecteren.
7.  Controleer de eigenschappen van het gebruikers object om er zeker van te zijn dat ze eruitzien zoals verwacht en er geen gegevens ontbreken.

### <a name="check-a-users-account-status"></a>De account status van een gebruiker controleren
Voer de volgende stappen uit om de account status van een gebruiker te controleren:
1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**
2.  Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd navigatie menu te selecteren.
3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.
4.  Selecteer **gebruikers en groepen** in het navigatie menu.
5.  Selecteer **alle gebruikers**.
6.  **Zoek** naar de gebruiker die u wilt interesseren en **Selecteer de rij** die u wilt selecteren.
7.  Selecteer een **profiel**.
8.  Onder **instellingen** zorgt u ervoor dat **blok keren aanmelden** is ingesteld op **Nee**.

### <a name="reset-a-users-password"></a>Het wacht woord van een gebruiker opnieuw instellen
Voer de volgende stappen uit om het wacht woord van een gebruiker opnieuw in te stellen:
1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**
2.  Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd navigatie menu te selecteren.
3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.
4.  Selecteer **gebruikers en groepen** in het navigatie menu.
5.  Selecteer **alle gebruikers**.
6.  **Zoek** naar de gebruiker die u wilt interesseren en **Selecteer de rij** die u wilt selecteren.
7.  Selecteer de knop **wacht woord opnieuw instellen** boven aan het deel venster van de gebruiker.
8.  Selecteer de knop **wacht woord opnieuw instellen** in het deel venster **wacht woord opnieuw instellen** dat wordt weer gegeven.
9.  Kopieer het **tijdelijke wacht woord** of **Voer een nieuw wacht woord** voor de gebruiker in.
10. Dit nieuwe wacht woord aan de gebruiker door geven, moet dit wacht woord tijdens de volgende aanmelding worden gewijzigd in Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Selfservice voor wachtwoordherstel inschakelen
Voer de volgende implementatie stappen uit om selfservice voor wachtwoord herstel in te scha kelen:
-   [Gebruikers in staat stellen hun Azure Active Directory wacht woorden opnieuw in te stellen](../authentication/tutorial-enable-sspr.md)
-   [Gebruikers in staat stellen om hun Active Directory on-premises wacht woorden opnieuw in te stellen of te wijzigen](../authentication/tutorial-enable-sspr.md)

### <a name="check-a-users-multi-factor-authentication-status"></a>De multi-factor Authentication-status van een gebruiker controleren
Voer de volgende stappen uit om de multi-factor Authentication-status van een gebruiker te controleren:
1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**
2. Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd navigatie menu te selecteren.
3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.
4. Selecteer **gebruikers en groepen** in het navigatie menu.
5. Selecteer **alle gebruikers**.
6. Selecteer de knop **multi-factor Authentication** boven aan het deel venster.
7. Nadat de **multi-factor Authentication beheer Portal** is geladen, controleert u of u zich op het tabblad **gebruikers** bevindt.
8. Zoek de gebruiker in de lijst met gebruikers door te zoeken, te filteren of te sorteren.
9. Selecteer de gebruiker in de lijst met gebruikers en **Schakel** indien **gewenst multi-** Factor Authentication in, **uit** of af.
   >[!NOTE]
   >Als een gebruiker een **afgedwongen** status heeft, kunt u deze zo instellen dat ze tijdelijk worden **uitgeschakeld** om ze weer in hun account te laten staan. Zodra ze weer in zijn, kunt u de status wijzigen in **ingeschakeld** zodat ze hun contact gegevens opnieuw moeten registreren tijdens de volgende aanmelding. U kunt ook de stappen volgen in de [contact gegevens van een gebruiker controleren](#check-a-users-authentication-contact-info) om te controleren of deze gegevens voor hen in te stellen.

### <a name="check-a-users-authentication-contact-info"></a>De contact gegevens voor de verificatie van een gebruiker controleren
Voer de volgende stappen uit om de contact gegevens van een gebruiker te controleren die wordt gebruikt voor multi-factor Authentication, voorwaardelijke toegang, identiteits beveiliging en wachtwoord herstel:
1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**
2.  Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd navigatie menu te selecteren.
3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.
4.  Selecteer **gebruikers en groepen** in het navigatie menu.
5.  Selecteer **alle gebruikers**.
6.  **Zoek** naar de gebruiker die u wilt interesseren en **Selecteer de rij** die u wilt selecteren.
7.  Selecteer een **profiel**.
8.  Schuif omlaag naar de **contact gegevens** van de verificatie.
9.  **Controleer** de gegevens die voor de gebruiker zijn geregistreerd en werk deze indien nodig bij.

### <a name="check-a-users-group-memberships"></a>De groepslid maatschappen van een gebruiker controleren
Voer de volgende stappen uit om de groepslid maatschappen van een gebruiker te controleren:
1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**
2.  Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd navigatie menu te selecteren.
3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.
4.  Selecteer **gebruikers en groepen** in het navigatie menu.
5.  Selecteer **alle gebruikers**.
6.  **Zoek** naar de gebruiker die u wilt interesseren en **Selecteer de rij** die u wilt selecteren.
7.  Selecteer **groepen** om te zien van welke groepen de gebruiker lid is.

### <a name="check-if-a-user-has-more-than-999-app-role-assignments"></a>Controleren of een gebruiker meer dan 999 app-roltoewijzingen heeft
Als een gebruiker meer dan 999 app-roltoewijzingen heeft, worden mogelijk niet alle apps in mijn apps weer geven.

Dit komt doordat mijn apps momenteel Maxi maal 999 app-roltoewijzingen lezen om te bepalen aan welke apps gebruikers worden toegewezen. Als een gebruiker is toegewezen aan meer dan 999 apps, is het niet mogelijk om te bepalen welke van deze apps wordt weer gegeven in de portal mijn apps.

Voer de volgende stappen uit om te controleren of een gebruiker meer dan 999 app-roltoewijzingen heeft:
1. Installeer de Power shell-module [**micro soft. Graph**](https://github.com/microsoftgraph/msgraph-sdk-powershell) .
2. Voer `Connect-MgGraph -Scopes "User.ReadBasic.All Application.Read.All"` uit.
3. Voer uit `(Get-MgUserAppRoleAssignment -UserId "<user-id>" -PageSize 999).Count` om te bepalen hoeveel app-roltoewijzingen aan de gebruiker momenteel zijn verleend.
4. Als het resultaat 999 is, heeft de gebruiker waarschijnlijk meer dan 999 app-rollen toegewezen.

### <a name="check-a-users-assigned-licenses"></a>De toegewezen licenties van een gebruiker controleren
Voer de volgende stappen uit om de toegewezen licenties van een gebruiker te controleren:
1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**
2.  Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd navigatie menu te selecteren.
3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.
4.  Selecteer **gebruikers en groepen** in het navigatie menu.
5.  Selecteer **alle gebruikers**.
6.  **Zoek** naar de gebruiker die u wilt interesseren en **Selecteer de rij** die u wilt selecteren.
7.  Selecteer **licenties** om te zien welke licenties de gebruiker momenteel heeft toegewezen.

### <a name="assign-a-user-a-license"></a>Een licentie toewijzen aan een gebruiker 
Voer de volgende stappen uit om een licentie aan een gebruiker toe te wijzen:
1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**
2.  Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd navigatie menu te selecteren.
3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.
4.  Selecteer **gebruikers en groepen** in het navigatie menu.
5.  Selecteer **alle gebruikers**.
6.  **Zoek** naar de gebruiker die u wilt interesseren en **Selecteer de rij** die u wilt selecteren.
7.  Selecteer **licenties** om te zien welke licenties de gebruiker momenteel heeft toegewezen.
8.  Selecteer de knop **toewijzen** .
9.  Selecteer **een of meer producten** uit de lijst met beschik bare producten.
10. Selecteer **optioneel** het item **toewijzings opties** om producten nauw keurig toe te wijzen. Selecteer **OK**.
11. Selecteer de knop **toewijzen** om deze licenties aan deze gebruiker toe te wijzen.

## <a name="troubleshooting-deep-links"></a>Problemen met diepe koppelingen oplossen
Diep gaande koppelingen of Url's voor gebruikers toegang zijn koppelingen die uw gebruikers kunnen gebruiken om rechtstreeks vanuit hun browser-URL-balken toegang te krijgen tot hun wacht woord-SSO-toepassingen. Als u naar deze koppeling navigeert, worden gebruikers automatisch aangemeld bij de toepassing zonder dat ze eerst naar mijn apps hoeven te gaan. De koppeling is hetzelfde als die gebruikers gebruiken om toegang te krijgen tot deze toepassingen vanuit het start programma voor toepassingen van Microsoft 365.

### <a name="checking-the-deep-link"></a>De diepe koppeling controleren

Voer de volgende stappen uit om te controleren of u de juiste diepte koppeling hebt:
1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**
2. Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd navigatie menu te selecteren.
3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.
4. Selecteer **Bedrijfstoepassingen** in het linkernavigatiemenu van Azure Active Directory.
5. Selecteer **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.
   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**
6. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**
7. Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd navigatie menu te selecteren.
8. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.
9. Selecteer **Bedrijfstoepassingen** in het linkernavigatiemenu van Azure Active Directory.
10. Selecteer **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.
    * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**
11. Selecteer de toepassing waarvoor u de diepe koppeling wilt controleren.
12. Zoek het label **gebruikers toegangs-URL**. Uw diepte koppeling moet overeenkomen met deze URL.

## <a name="contact-support"></a>Contact opnemen met ondersteuning
Open een ondersteunings ticket met de volgende informatie, indien beschikbaar:
-   ID correlatie fout
-   UPN (e-mail adres van gebruiker)
-   TenantID
-   Browsertype
-   Tijd zone en tijd/tijds duur tijdens fout
-   Fiddler traceringen

## <a name="next-steps"></a>Volgende stappen
- [Quickstartreeks over toepassingsbeheer](view-applications-portal.md)
