---
title: Problemen met aanmelden bij een Microsoft-toepassing | Microsoft Docs
description: Veelvoorkomende problemen oplossen bij het aanmelden bij eigen Microsoft-toepassingen met behulp van Azure AD (zoals Microsoft 365).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/10/2018
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c56e356a9bacc6479d3a3a33be905457c26e732e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378173"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Problemen met aanmelden bij een Microsoft-toepassing

Microsoft-toepassingen (zoals Exchange, SharePoint, Yammer, enzovoort) worden iets anders toegewezen en beheerd dan SaaS-toepassingen van derden of andere toepassingen die u integreert met Azure AD voor een enkele aanmelding.

Er zijn drie belangrijke manieren waarop een gebruiker toegang kan krijgen tot een door Microsoft gepubliceerde toepassing.

-   Voor toepassingen in de Microsoft 365 of andere betaalde suites  krijgen gebruikers toegang via licentietoewijzing, rechtstreeks aan hun gebruikersaccount of via een groep met behulp van onze groepslicentietoewijzingsmogelijkheid.

-   Voor toepassingen die Microsoft of een derde partij vrijelijk publiceert voor iedereen om te gebruiken, kunnen gebruikers toegang krijgen via **toestemming van de gebruiker.** Dit betekent dat ze zich aanmelden bij de toepassing met hun Azure AD-werk- of schoolaccount en dat ze toegang hebben tot een beperkte set gegevens in hun account.

-   Voor toepassingen die Microsoft of een externe partij vrijelijk publiceert voor iedereen om te gebruiken, kunnen gebruikers ook toegang krijgen via **beheerders toestemming.** Dit betekent dat een beheerder heeft vastgesteld dat de toepassing kan worden gebruikt door iedereen in de organisatie, zodat ze zich aanmelden bij de toepassing met een globale beheerdersaccount en toegang verlenen aan iedereen in de organisatie.

Als u het probleem [](#general-problem-areas-with-application-access-to-consider) wilt oplossen, begint u met de algemene probleemgebieden met toegang tot toepassingen om rekening mee te houden en leest u vervolgens walkthrough: stappen voor het oplossen van problemen met toegang tot Microsoft-toepassingen voor meer informatie.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Algemene probleemgebieden met toegang tot toepassingen om rekening mee te houden

Hier volgt een lijst met algemene probleemgebieden waarop u kunt inzoomen als u een idee hebt van waar u moet beginnen, maar we raden u aan het overzicht te lezen om snel aan de slag te gaan: Walkthrough: Steps to troubleshoot Microsoft Application access (Stappen voor het oplossen van problemen met toegang tot Microsoft-toepassingen).

-   [Problemen met het account van de gebruiker](#problems-with-the-users-account)

-   [Problemen met groepen](#problems-with-groups)

-   [Problemen met beleid voor voorwaardelijke toegang](#problems-with-conditional-access-policies)

-   [Problemen met toepassings toestemming](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Stappen voor het oplossen van problemen met toegang tot Microsoft-toepassingen

Hieronder volgen enkele veelvoorkomende problemen die mensen tegen komen wanneer hun gebruikers zich niet kunnen aanmelden bij een Microsoft-toepassing.

- Algemene problemen die u het eerst moet controleren

  * Zorg ervoor dat de gebruiker zich aanmeldt bij de juiste **URL** en niet op een lokale toepassings-URL.

  * Zorg ervoor dat het account van de gebruiker **niet is vergrendeld.**

  * Zorg ervoor dat **het account van de gebruiker in de** Azure Active Directory. [Controleer of er een gebruikersaccount bestaat in Azure Active Directory](#problems-with-the-users-account)

  * Zorg ervoor dat het gebruikersaccount is **ingeschakeld** voor aanmeldingen. [De accountstatus van een gebruiker controleren](#problems-with-the-users-account)

  * Zorg ervoor dat het wachtwoord van **de gebruiker niet is verlopen of vergeten.** [Het wachtwoord van een gebruiker opnieuw instellen of](#reset-a-users-password) [selfservice voor wachtwoord opnieuw instellen inschakelen](../authentication/tutorial-enable-sspr.md)

  * Zorg ervoor **dat Multi-Factor Authentication** de gebruikerstoegang niet blokkeert. [Controleer de multi-factor authentication-status van een gebruiker](#check-a-users-multi-factor-authentication-status) of controleer de contactgegevens [voor verificatie van een gebruiker](#check-a-users-authentication-contact-info)

  * Zorg ervoor dat **een beleid voor voorwaardelijke toegang** of **identiteitsbeveiliging** gebruikerstoegang niet blokkeert. [Een specifiek beleid voor voorwaardelijke toegang controleren](#problems-with-conditional-access-policies) of [het](#check-a-specific-applications-conditional-access-policy) beleid voor voorwaardelijke toegang van een specifieke toepassing controleren of Een specifiek beleid [voor voorwaardelijke](#disable-a-specific-conditional-access-policy) toegang uitschakelen

  * Zorg ervoor dat de contactgegevens voor verificatie **van** een gebruiker up-to-date zijn, zodat Multi-Factor Authentication of beleid voor voorwaardelijke toegang kan worden afgedwongen. [Controleer de multi-factor authentication-status van een gebruiker](#check-a-users-multi-factor-authentication-status) of controleer de contactgegevens [voor verificatie van een gebruiker](#check-a-users-authentication-contact-info)

- Voor  **Microsoft-toepassingen waarvoor een** licentie is vereist (zoals Office365), zijn hier enkele specifieke problemen die u moet controleren nadat u de bovenstaande algemene problemen hebt uitgesloten:

  * Zorg ervoor dat de gebruiker of een **licentie is toegewezen.** [Controleer de toegewezen licenties van een](#check-a-users-assigned-licenses) gebruiker of controleer de toegewezen licenties van een [groep](#check-a-groups-assigned-licenses)

  * Als de licentie is **toegewezen aan een statische** **groep,** moet u ervoor zorgen dat de **gebruiker lid is** van die groep. [De groepslidmaatschap van een gebruiker controleren](#check-a-users-group-memberships)

  * Als de licentie is **toegewezen aan een dynamische** **groep,** controleert u of de regel voor dynamische groepen **juist is ingesteld.** [De lidmaatschapscriteria van een dynamische groep controleren](#check-a-dynamic-groups-membership-criteria)

  * Als de licentie **is** toegewezen aan een dynamische **groep,** moet u ervoor zorgen dat de dynamische groep klaar is met het verwerken van het lidmaatschap en dat de gebruiker lid **is** (dit kan enige tijd duren).  [De groepslidmaatschap van een gebruiker controleren](#check-a-users-group-memberships)

  *  Nadat u hebt zeker dat de licentie is toegewezen, moet u ervoor zorgen dat de **licentie niet is verlopen.**

  *  Zorg ervoor dat de licentie is **voor de toepassing** die ze openen.

- Voor  **Microsoft-toepassingen waarvoor geen licentie is vereist,** kunt u het volgende controleren:

  * Als de toepassing machtigingen op gebruikersniveau aanvraagt (bijvoorbeeld 'Toegang tot het postvak van deze gebruiker'),  moet u ervoor zorgen dat de gebruiker zich heeft aangemeld bij de toepassing en een **toestemmingsbewerking** op gebruikersniveau heeft uitgevoerd om de toepassing toegang te geven tot de gegevens.

  * Als de toepassing machtigingen op beheerdersniveau aanvraagt (bijvoorbeeld 'Toegang tot alle postvakken van gebruikers'), moet u ervoor zorgen dat een globale beheerder namens alle gebruikers **in** de organisatie een **toestemmingsbewerking** op beheerdersniveau heeft uitgevoerd.

## <a name="problems-with-the-users-account"></a>Problemen met het account van de gebruiker

Toegang tot toepassingen kan worden geblokkeerd vanwege een probleem met een gebruiker die is toegewezen aan de toepassing. Hier volgen enkele manieren waarop u problemen met gebruikers en hun accountinstellingen kunt oplossen:

-   [Controleer of er een gebruikersaccount bestaat in Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [De accountstatus van een gebruiker controleren](#check-a-users-account-status)

-   [Het wachtwoord van een gebruiker opnieuw instellen](#reset-a-users-password)

-   [Selfservice voor wachtwoordherstel inschakelen](#enable-self-service-password-reset)

-   [De multi-factor authentication-status van een gebruiker controleren](#check-a-users-multi-factor-authentication-status)

-   [Controleer de contactgegevens voor verificatie van een gebruiker](#check-a-users-authentication-contact-info)

-   [De groepslidmaatschap van een gebruiker controleren](#check-a-users-group-memberships)

-   [De toegewezen licenties van een gebruiker controleren](#check-a-users-assigned-licenses)

-   [Een gebruiker een licentie toewijzen](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Controleer of er een gebruikersaccount bestaat in Azure Active Directory

Volg deze stappen om te controleren of het account van een gebruiker aanwezig is:

1.  Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory door** te klikken op **Alle services** bovenaan het navigatiemenu aan de linkerkant.

3.  Typ **'Azure Active Directory'** in het filterzoekvak en selecteer het **Azure Active Directory** item.

4.  klik **op Gebruikers en groepen** in het navigatiemenu.

5.  klik **op Alle gebruikers.**

6.  **Zoek** de gebruiker waarin u bent geïnteresseerd en klik **op de rij die u** wilt selecteren.

7.  Controleer de eigenschappen van het gebruikersobject om er zeker van te zijn dat ze er zoals verwacht uitzien en dat er geen gegevens ontbreken.

### <a name="check-a-users-account-status"></a>De accountstatus van een gebruiker controleren

Volg deze stappen om de accountstatus van een gebruiker te controleren:

1.  Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory door** te klikken op **Alle services** bovenaan het navigatiemenu linksboven.

3.  Typ **'Azure Active Directory'** in het filterzoekvak en selecteer het **Azure Active Directory** item.

4.  klik **op Gebruikers en groepen** in het navigatiemenu.

5.  klik **op Alle gebruikers.**

6.  **Zoek** de gebruiker waarin u bent geïnteresseerd en klik **op de rij die** u wilt selecteren.

7.  klik **op Profiel.**

8.  Controleer **onder** Instellingen of **Aanmelden blokkeren** is ingesteld op **Nee.**

### <a name="reset-a-users-password"></a>Het wachtwoord van een gebruiker opnieuw instellen

Volg deze stappen om het wachtwoord van een gebruiker opnieuw in te stellen:

1.  Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory door** te klikken op **Alle services** bovenaan het navigatiemenu linksboven.

3.  Typ **'Azure Active Directory'** in het filterzoekvak en selecteer het **Azure Active Directory** item.

4.  klik **op Gebruikers en groepen** in het navigatiemenu.

5.  klik **op Alle gebruikers.**

6.  **Zoek** de gebruiker waarin u bent geïnteresseerd en klik **op de rij die** u wilt selecteren.

7.  klik op **de knop** Wachtwoord opnieuw instellen bovenaan het gebruikersvenster.

8.  klik op **de knop Wachtwoord** opnieuw instellen in het **deelvenster** Wachtwoord opnieuw instellen dat wordt weergegeven.

9.  Kopieer het **tijdelijke wachtwoord of** voer een nieuw wachtwoord **in** voor de gebruiker.

10. Geef dit nieuwe wachtwoord door aan de gebruiker. Deze moet dit wachtwoord wijzigen tijdens de volgende aanmelding bij Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Selfservice voor wachtwoordherstel inschakelen

Als u selfservice voor wachtwoord opnieuw instellen wilt inschakelen, volgt u de onderstaande implementatiestappen:

-   [Gebruikers in staat stellen hun wachtwoord opnieuw in Azure Active Directory stellen](../authentication/tutorial-enable-sspr.md)

-   [Gebruikers in staat stellen hun on-premises Active Directory-wachtwoorden opnieuw in te stellen of te wijzigen](../authentication/tutorial-enable-sspr.md)

### <a name="check-a-users-multi-factor-authentication-status"></a>De multi-factor authentication-status van een gebruiker controleren

Volg deze stappen om de multi-factor authentication-status van een gebruiker te controleren:

1. Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory door** te klikken op **Alle services** bovenaan het navigatiemenu aan de linkerkant.

3. Typ **'Azure Active Directory'** in het filterzoekvak en selecteer het **Azure Active Directory** item.

4. klik **op Gebruikers en groepen** in het navigatiemenu.

5. klik **op Alle gebruikers.**

6. klik **op de knop Multi-Factor Authentication** bovenaan het deelvenster.

7. Nadat de **Portal voor multi-factor Authentication-beheer** is geladen, controleert u of u zich op het **tabblad Gebruikers.**

8. Zoek de gebruiker in de lijst met gebruikers door te zoeken, te filteren of te sorteren.

9. Selecteer de gebruiker in de lijst met gebruikers **en** Schakel , **Uitschakelen** of **Meervoudige** verificatie naar wens afdwingen in.

   * **Opmerking:** als een gebruiker de status Afgedwongen **heeft,** kunt u deze tijdelijk instellen op Uitgeschakeld om ze weer toegang te geven tot hun account.  Zodra ze weer zijn ingeschakeld, kunt  u de status weer wijzigen in Ingeschakeld, zodat ze hun contactgegevens opnieuw moeten registreren tijdens de volgende aanmelding. U kunt ook de stappen [](#check-a-users-authentication-contact-info) volgen in De contactgegevens voor verificatie van een gebruiker controleren om deze gegevens voor hen te controleren of in te stellen.

### <a name="check-a-users-authentication-contact-info"></a>Controleer de contactgegevens voor verificatie van een gebruiker

Volg deze stappen om de contactgegevens voor verificatie van een gebruiker te controleren die worden gebruikt voor Meervoudige verificatie, voorwaardelijke toegang, Identiteitsbeveiliging en Wachtwoord opnieuw instellen:

1.  Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory door** te klikken op **Alle services** bovenaan het navigatiemenu aan de linkerkant.

3.  Typ **'Azure Active Directory'** in het filterzoekvak en selecteer het **Azure Active Directory** item.

4.  klik **op Gebruikers en groepen** in het navigatiemenu.

5.  klik **op Alle gebruikers.**

6.  **Zoek** de gebruiker waarin u bent geïnteresseerd en klik **op de rij die u** wilt selecteren.

7.  klik **op Profiel.**

8.  Schuif omlaag naar **Contactgegevens voor verificatie.**

9.  **Controleer** de gegevens die zijn geregistreerd voor de gebruiker en werk deze zo nodig bij.

### <a name="check-a-users-group-memberships"></a>De groepslidmaatschap van een gebruiker controleren

Volg deze stappen om de groepslidmaatschap van een gebruiker te controleren:

1.  Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory door** te klikken op **Alle services** bovenaan het navigatiemenu aan de linkerkant.

3.  Typ **'Azure Active Directory'** in het filterzoekvak en selecteer het **Azure Active Directory** item.

4.  klik **op Gebruikers en groepen** in het navigatiemenu.

5.  klik **op Alle gebruikers.**

6.  **Zoek** de gebruiker waarin u bent geïnteresseerd en klik **op de rij die u** wilt selecteren.

7.  Klik **op Groepen** om te zien van welke groepen de gebruiker lid is.

### <a name="check-a-users-assigned-licenses"></a>De toegewezen licenties van een gebruiker controleren

Volg deze stappen om de toegewezen licenties van een gebruiker te controleren:

1.  Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory door** te klikken op **Alle services** bovenaan het navigatiemenu aan de linkerkant.

3.  Typ **'Azure Active Directory'** in het filterzoekvak en selecteer het **Azure Active Directory** item.

4.  klik **op Gebruikers en groepen** in het navigatiemenu.

5.  Klik **op Alle gebruikers.**

6.  **Zoek** de gebruiker waarin u bent geïnteresseerd en klik **op de rij die** u wilt selecteren.

7.  klik **op Licenties om** te zien welke licenties de gebruiker momenteel heeft toegewezen.

### <a name="assign-a-user-a-license"></a>Een gebruiker een licentie toewijzen 

Volg deze stappen om een licentie toe te wijzen aan een gebruiker:

1.  Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory extensie** door te klikken op **Alle services** boven aan het navigatiemenu aan de linkerkant.

3.  Typ **'Azure Active Directory'** in het filterzoekvak en selecteer het **Azure Active Directory** item.

4.  klik **op Gebruikers en groepen** in het navigatiemenu.

5.  klik **op Alle gebruikers.**

6.  **Zoek** de gebruiker waarin u bent geïnteresseerd en klik **op de rij die** u wilt selecteren.

7.  klik **op Licenties om** te zien welke licenties de gebruiker momenteel heeft toegewezen.

8.  klik op **de knop** Toewijzen.

9.  Selecteer **een of meer producten** in de lijst met beschikbare producten.

10. **Klik** optioneel op het item **toewijzingsopties** om producten granulair toe te wijzen. Klik **op OK** wanneer dit is voltooid.

11. Klik op **de knop** Toewijzen om deze licenties aan deze gebruiker toe te wijzen.

## <a name="problems-with-groups"></a>Problemen met groepen

Toegang tot toepassingen kan worden geblokkeerd vanwege een probleem met een groep die is toegewezen aan de toepassing. Hier volgen enkele manieren waarop u problemen met groepen en groepslidmaatschap kunt oplossen:

-   [Het lidmaatschap van een groep controleren](#check-a-groups-membership)

-   [De lidmaatschapscriteria van een dynamische groep controleren](#check-a-dynamic-groups-membership-criteria)

-   [De toegewezen licenties van een groep controleren](#check-a-groups-assigned-licenses)

-   [De licenties van een groep opnieuw verwerken](#reprocess-a-groups-licenses)

-   [Een licentie toewijzen aan een groep](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Het lidmaatschap van een groep controleren

Volg deze stappen om het lidmaatschap van een groep te controleren:

1.  Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory door** te klikken op **Alle services** bovenaan het navigatiemenu aan de linkerkant.

3.  Typ **'Azure Active Directory'** in het filterzoekvak en selecteer het **Azure Active Directory** item.

4.  klik **op Gebruikers en groepen** in het navigatiemenu.

5.  klik **op Alle groepen.**

6.  **Zoek** de groep waarin u bent geïnteresseerd en klik **op de rij die u** wilt selecteren.

7.  klik **op Leden** om de lijst met gebruikers te bekijken die aan deze groep zijn toegewezen.

### <a name="check-a-dynamic-groups-membership-criteria"></a>De lidmaatschapscriteria van een dynamische groep controleren 

Volg deze stappen om de lidmaatschapscriteria van een dynamische groep te controleren:

1.  Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory door** te klikken op **Alle services** bovenaan het navigatiemenu aan de linkerkant.

3.  Typ **'Azure Active Directory'** in het filterzoekvak en selecteer het **Azure Active Directory** item.

4.  klik **op Gebruikers en groepen** in het navigatiemenu.

5.  Klik **op Alle groepen.**

6.  **Zoek** de groep waarin u bent geïnteresseerd en klik **op de rij die** u wilt selecteren.

7.  klik **op Dynamische lidmaatschapsregels.**

8.  Controleer de **eenvoudige** of **geavanceerde** regel die voor deze groep is gedefinieerd en zorg ervoor dat de gebruiker die u lid wilt zijn van deze groep aan deze criteria voldoet.

### <a name="check-a-groups-assigned-licenses"></a>De toegewezen licenties van een groep controleren

Volg deze stappen om de toegewezen licenties van een groep te controleren:

1.  Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory door** te klikken op **Alle services** bovenaan het navigatiemenu linksboven.

3.  Typ **'Azure Active Directory'** in het filterzoekvak en selecteer het **Azure Active Directory** item.

4.  klik **op Gebruikers en groepen** in het navigatiemenu.

5.  Klik **op Alle groepen.**

6.  **Zoek** de groep waarin u bent geïnteresseerd en klik **op de rij die** u wilt selecteren.

7.  klik **op Licenties** om te zien welke licenties de groep momenteel heeft toegewezen.

### <a name="reprocess-a-groups-licenses"></a>De licenties van een groep opnieuw verwerken

Volg deze stappen om de toegewezen licenties van een groep opnieuw te verwerken:

1. Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory door** te klikken op **Alle services** bovenaan het navigatiemenu linksboven.

3. Typ **'Azure Active Directory'** in het filterzoekvak en selecteer het **Azure Active Directory** item.

4. klik **op Gebruikers en groepen** in het navigatiemenu.

5. Klik **op Alle groepen.**

6. **Zoek** de groep waarin u bent geïnteresseerd en klik **op de rij die** u wilt selecteren.

7. klik **op Licenties** om te zien welke licenties de groep momenteel heeft toegewezen.

8. Klik op **de knop Opnieuw** verwerken om ervoor te zorgen dat de licenties die zijn toegewezen aan de leden van deze groep, up-to-date zijn. Dit kan lang duren, afhankelijk van de grootte en complexiteit van de groep.

   >[!NOTE]
   >U kunt dit sneller doen door tijdelijk een licentie rechtstreeks aan de gebruiker toe te wijzen. [Wijs een licentie toe aan een gebruiker.](#problems-with-application-consent)
   >
   >

### <a name="assign-a-group-a-license"></a>Een licentie toewijzen aan een groep

Volg deze stappen om een licentie toe te wijzen aan een groep:

1. Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory door** te klikken op **Alle services** bovenaan het navigatiemenu linksboven.

3. Typ **'Azure Active Directory'** in het filterzoekvak en selecteer het **Azure Active Directory** item.

4. klik **op Gebruikers en groepen** in het navigatiemenu.

5. Klik **op Alle groepen.**

6. **Zoek** de groep waarin u bent geïnteresseerd en klik **op de rij die** u wilt selecteren.

7. klik **op Licenties** om te zien welke licenties de groep momenteel heeft toegewezen.

8. klik op **de knop** Toewijzen.

9. Selecteer **een of meer producten** in de lijst met beschikbare producten.

10. **Klik** optioneel op het item **toewijzingsopties** om producten gedetailleerd toe te wijzen. Klik **op OK** wanneer dit is voltooid.

11. Klik op **de knop** Toewijzen om deze licenties aan deze groep toe te wijzen. Dit kan lang duren, afhankelijk van de grootte en complexiteit van de groep.

    >[!NOTE]
    >U kunt dit sneller doen door een licentie tijdelijk rechtstreeks aan de gebruiker toe te wijzen. [Wijs een licentie toe aan een gebruiker.](#problems-with-application-consent)
    > 
    >

## <a name="problems-with-conditional-access-policies"></a>Problemen met beleid voor voorwaardelijke toegang

### <a name="check-a-specific-conditional-access-policy"></a>Een specifiek beleid voor voorwaardelijke toegang controleren

Eén beleid voor voorwaardelijke toegang controleren of valideren:

1. Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory door** te klikken op **Alle services** bovenaan het navigatiemenu aan de linkerkant.

3. Typ **'Azure Active Directory'** in het filterzoekvak en selecteer het **Azure Active Directory** item.

4. klik **op Bedrijfstoepassingen** in het navigatiemenu.

5. klik op **het navigatie-item Voorwaardelijke** toegang.

6. klik op het beleid dat u wilt inspecteren.

7. Controleer of er geen specifieke voorwaarden, toewijzingen of andere instellingen zijn die gebruikerstoegang blokkeren.

   >[!NOTE]
   >Mogelijk wilt u dit beleid tijdelijk uitschakelen om ervoor te zorgen dat dit geen invloed heeft op aanmeldingen. Hiervoor stelt u de schakelknop **Beleid inschakelen** in op **Nee** en klikt u op **de knop** Opslaan.
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Het beleid voor voorwaardelijke toegang van een specifieke toepassing controleren

Het momenteel geconfigureerde beleid voor voorwaardelijke toegang van één toepassing controleren of valideren:

1.  Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory door** te klikken op **Alle services** bovenaan het navigatiemenu aan de linkerkant.

3.  Typ **'Azure Active Directory'** in het filterzoekvak en selecteer het **Azure Active Directory** item.

4.  klik **op Bedrijfstoepassingen** in het navigatiemenu.

5.  klik **op Alle toepassingen.**

6.  Zoek naar de toepassing waarin u bent geïnteresseerd of de gebruiker probeert zich aan te melden met de weergavenaam of toepassings-id van de toepassing.

     >[!NOTE]
     >Als u de toepassing die u zoekt niet ziet, klikt u op de **knop Filteren** en vouwt u het bereik van de lijst uit naar **Alle toepassingen.** Als u meer kolommen wilt zien, klikt u op de **knop Kolommen** om aanvullende details voor uw toepassingen toe te voegen.
     >
     >

7.  klik op het **navigatie-item Voorwaardelijke** toegang.

8.  klik op het beleid dat u wilt inspecteren.

9.  Controleer of er geen specifieke voorwaarden, toewijzingen of andere instellingen zijn die gebruikerstoegang mogelijk blokkeren.

     >[!NOTE]
     >Mogelijk wilt u dit beleid tijdelijk uitschakelen om ervoor te zorgen dat dit geen invloed heeft op aanmeldingen. Als u dit wilt doen, stelt u de schakelknop Beleid **inschakelen** in **op Nee** en klikt u op de **knop** Opslaan.
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Een specifiek beleid voor voorwaardelijke toegang uitschakelen

Eén beleid voor voorwaardelijke toegang controleren of valideren:

1.  Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory door** te klikken op **Alle services** bovenaan het navigatiemenu linksboven.

3.  Typ **'Azure Active Directory'** in het filterzoekvak en selecteer het **Azure Active Directory** item.

4.  klik **op Bedrijfstoepassingen** in het navigatiemenu.

5.  klik op het **navigatie-item Voorwaardelijke** toegang.

6.  klik op het beleid dat u wilt inspecteren.

7.  Schakel het beleid uit door de **schakelknop Beleid inschakelen** in te stellen **op Nee** en op de knop **Opslaan te** klikken.

## <a name="problems-with-application-consent"></a>Problemen met toepassings toestemming

Toegang tot toepassingen kan worden geblokkeerd omdat de juiste toestemmingsbewerking voor machtigingen niet is uitgevoerd. Hier volgen enkele manieren waarop u problemen met toestemming van toepassingen kunt oplossen:

-   [Een toestemmingsbewerking op gebruikersniveau uitvoeren](#perform-a-user-level-consent-operation)

-   [Toestemmingsbewerking op beheerdersniveau uitvoeren voor elke toepassing](#perform-administrator-level-consent-operation-for-any-application)

-   [Toestemming op beheerdersniveau uitvoeren voor een toepassing met één tenant](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Toestemming op beheerdersniveau uitvoeren voor een toepassing met meerdere tenants](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Een toestemmingsbewerking op gebruikersniveau uitvoeren

-   Voor elke toepassing met Open ID Connect die machtigingen aanvraagt, wordt bij het navigeren naar het aanmeldingsscherm van de toepassing een toestemming op gebruikersniveau uitgevoerd voor de toepassing voor de aangemelde gebruiker.

-   Als u dit programmatisch wilt doen, zie Toestemming van afzonderlijke [gebruiker aanvragen.](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent)

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Toestemmingsbewerking op beheerdersniveau uitvoeren voor elke toepassing

-   Voor **alleen** toepassingen die zijn ontwikkeld met behulp van het V1-toepassingsmodel, kunt u deze toestemming op beheerdersniveau forceeren door **'?prompt=beheerders \_** toestemming' toe te voegen aan het einde van de aanmeldings-URL van een toepassing.

-   Voor elke toepassing die is ontwikkeld met behulp van het **V2-toepassingsmodel,** kunt u afdwingen dat deze toestemming op beheerdersniveau wordt uitgevoerd door de instructies te volgen onder de sectie De machtigingen aanvragen bij een **directorybeheerder** van Het eindpunt voor beheerders toestemming [gebruiken.](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Toestemming op beheerdersniveau uitvoeren voor een toepassing met één tenant

-   Voor toepassingen met één **tenant** die machtigingen aanvragen (zoals toepassingen die u  ontwikkelt of die eigenaar zijn in uw organisatie), kunt u een toestemmingsbewerking op beheerdersniveau uitvoeren namens alle gebruikers door u aan te melden als globale beheerder en te klikken op de knop Machtigingen verlenen boven aan het deelvenster **Toepassingsregister** - Alle toepassingen - Een app selecteren **&gt; &gt; - &gt; Vereiste** machtigingen.

-   Voor elke toepassing die is ontwikkeld met het V1- [of](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint) **V2-toepassingsmodel,** kunt u deze toestemming op beheerdersniveau afdwingen door de instructies te volgen in de sectie De machtigingen aanvragen bij een **directorybeheerder** van Het eindpunt voor beheerders toestemming gebruiken.

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Toestemming op beheerdersniveau uitvoeren voor een toepassing met meerdere tenants

-   Voor **toepassingen met meerdere tenants** die machtigingen aanvragen (zoals een toepassing die een derde partij of Microsoft ontwikkelt), kunt u een toestemmingsbewerking op beheerniveau uitvoeren.  Meld u aan als globale beheerder  en klik op de knop Machtigingen verlenen onder het deelvenster Bedrijfstoepassingen - Alle toepassingen - Een app selecteren **&gt; &gt; - &gt;** Machtigingen (binnenkort beschikbaar).

-   U kunt deze toestemming op beheerdersniveau ook afdwingen door de instructies te volgen in de sectie De machtigingen aanvragen bij een **directorybeheerder** van Het eindpunt voor beheerders toestemming [gebruiken.](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)

## <a name="next-steps"></a>Volgende stappen
[Het eindpunt voor beheerdersmachtiging gebruiken](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)