---
title: Problemen bij het aanmelden bij een micro soft-toepassing | Microsoft Docs
description: Algemene problemen oplossen die zich voordoen bij het aanmelden bij micro soft-toepassingen voor de eerste partij met behulp van Azure AD (zoals Microsoft 365).
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/10/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79c8d6d072379853d6eca561d372f61dbb8acc8a
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260008"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Problemen bij het aanmelden bij een micro soft-toepassing

Micro soft-toepassingen (zoals Exchange, share point, Yammer, enz.) zijn toegewezen en worden beheerd op een andere manier dan SaaS-toepassingen van derden of andere toepassingen die u met Azure AD integreert voor eenmalige aanmelding.

Er zijn drie belang rijke manieren waarop een gebruiker toegang kan krijgen tot een door micro soft gepubliceerde toepassing.

-   Voor toepassingen in de Microsoft 365 of andere betaalde suites, krijgen gebruikers toegang via **licentie toewijzing** rechtstreeks aan hun gebruikers account, of via een groep via onze toewijzing van licentie toewijzingen op basis van groepen.

-   Voor toepassingen die door micro soft of een derde partij gratis worden gepubliceerd voor iedereen die ze wil gebruiken, kunnen gebruikers toegang krijgen via toestemming van de **gebruiker**. Dit betekent dat ze zich bij de toepassing aanmelden met hun Azure AD-werk-of school account en toegang hebben tot een beperkt aantal gegevens op hun account.

-   Voor toepassingen die door micro soft of een derde partij vrij worden gepubliceerd voor iedereen, kunnen gebruikers ook toegang krijgen via **toestemming** van de beheerder. Dit betekent dat een beheerder heeft vastgesteld dat de toepassing door iedereen in de organisatie kan worden gebruikt, zodat deze zich bij de toepassing aanmeldt met een account van de globale beheerder en toegang verleent aan iedereen in de organisatie.

Om het probleem op te lossen, begint u met de [algemene probleem gebieden met toegang tot de toepassing om rekening te houden](#general-problem-areas-with-application-access-to-consider) met de procedure: stappen voor het oplossen van problemen met micro soft-toepassingen om de details op te halen.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Algemene probleem gebieden met toegang tot de toepassing

Hieronder volgt een lijst met algemene probleem gebieden die u kunt inzoomen als u een idee hebt van waar u moet beginnen, maar we raden u aan om snel aan de slag te gaan: scenario: stappen voor het oplossen van problemen met micro soft-toepassingen.

-   [Problemen met het account van de gebruiker](#problems-with-the-users-account)

-   [Problemen met groepen](#problems-with-groups)

-   [Problemen met beleid voor voorwaardelijke toegang](#problems-with-conditional-access-policies)

-   [Problemen met toestemming van de toepassing](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Stappen voor het oplossen van problemen met micro soft-toepassingen

Hieronder volgen enkele veelvoorkomende problemen die mensen uitvoeren wanneer hun gebruikers zich niet kunnen aanmelden bij een micro soft-toepassing.

- Algemene problemen om eerst te controleren

  * Zorg ervoor dat de gebruiker zich aanmeldt bij de **juiste URL** en niet de URL van een lokale toepassing.

  * Controleer of het account van de gebruiker **niet is vergrendeld.**

  * Zorg ervoor dat het **account van de gebruiker bestaat** in azure Active Directory. [Controleren of een gebruikers account bestaat in Azure Active Directory](#problems-with-the-users-account)

  * Zorg ervoor dat het gebruikers account is **ingeschakeld** voor aanmeldingen. [De account status van een gebruiker controleren](#problems-with-the-users-account)

  * Zorg ervoor dat het wacht woord van de gebruiker **niet is verlopen of is verg eten.** [Het wacht woord van een gebruiker opnieuw instellen](#reset-a-users-password) of [selfservice voor wachtwoord herstel inschakelen](../authentication/tutorial-enable-sspr.md)

  * Zorg ervoor dat **multi-factor Authentication** de gebruikers toegang niet blokkeert. [Controleer de multi-factor Authentication-status van een gebruiker](#check-a-users-multi-factor-authentication-status) of [Controleer de contact gegevens voor de verificatie van een gebruiker](#check-a-users-authentication-contact-info)

  * Zorg ervoor dat het beleid voor **voorwaardelijke toegang** of het beleid voor **identiteits beveiliging** de gebruikers toegang niet blokkeert. [Een specifiek beleid voor voorwaardelijke toegang controleren](#problems-with-conditional-access-policies) of [het beleid voor voorwaardelijke toegang van een specifieke toepassing controleren](#check-a-specific-applications-conditional-access-policy) of [een specifiek beleid voor voorwaardelijke toegang uitschakelen](#disable-a-specific-conditional-access-policy)

  * Zorg ervoor dat de **contact gegevens** van de verificatie van een gebruiker up-to-date zijn om multi-factor Authentication of beleid voor voorwaardelijke toegang te kunnen afdwingen. [Controleer de multi-factor Authentication-status van een gebruiker](#check-a-users-multi-factor-authentication-status) of [Controleer de contact gegevens voor de verificatie van een gebruiker](#check-a-users-authentication-contact-info)

- Voor **micro soft** - **toepassingen waarvoor een licentie is vereist** (zoals Office365), zijn er enkele specifieke problemen om te controleren wanneer u de bovenstaande algemene problemen hebt opgelost:

  * Controleer of er een licentie is **toegewezen aan** de gebruiker. [De toegewezen licenties van een gebruiker controleren](#check-a-users-assigned-licenses) of [de toegewezen licenties van een groep controleren](#check-a-groups-assigned-licenses)

  * Als de licentie wordt **toegewezen aan een** **statische groep**, controleert u of de **gebruiker lid is** van die groep. [De groepslid maatschappen van een gebruiker controleren](#check-a-users-group-memberships)

  * Als de licentie is **toegewezen aan een** **dynamische groep**, moet u ervoor zorgen dat de **dynamische groeps regel juist is ingesteld**. [De lidmaatschaps criteria van een dynamische groep controleren](#check-a-dynamic-groups-membership-criteria)

  * Als de licentie is **toegewezen aan een** **dynamische groep**, controleert u of de dynamische groep de verwerking van het lidmaatschap heeft **voltooid** en of de **gebruiker lid is** (dit kan enige tijd duren). [De groepslid maatschappen van een gebruiker controleren](#check-a-users-group-memberships)

  *  Nadat u hebt gecontroleerd of de licentie is toegewezen, controleert u of de licentie **niet is verlopen**.

  *  Zorg ervoor dat de licentie **voor de toepassing** is die ze gebruiken.

- Voor **micro soft** - **toepassingen waarvoor geen licentie is vereist**, kunt u het volgende controleren:

  * Als de toepassing machtigingen op **gebruikers niveau** aanvraagt (bijvoorbeeld ' toegang tot het postvak van de gebruiker '), moet u ervoor zorgen dat de gebruiker zich heeft aangemeld bij de toepassing en dat er een **bewerking op gebruikers niveau** is uitgevoerd waarmee de toepassing toegang kan krijgen tot de gegevens.

  * Als de toepassing machtigingen op **beheerders niveau** aanvraagt (bijvoorbeeld ' toegang tot de post vakken van alle gebruikers '), moet u ervoor zorgen dat een globale beheerder een **toestemmings bewerking op beheerders niveau heeft uitgevoerd namens alle gebruikers** in de organisatie.

## <a name="problems-with-the-users-account"></a>Problemen met het account van de gebruiker

Toegang tot toepassingen kan worden geblokkeerd vanwege een probleem met een gebruiker die is toegewezen aan de toepassing. Hieronder vindt u enkele manieren waarop u problemen met gebruikers en hun account instellingen kunt oplossen en oplossen:

-   [Controleren of een gebruikers account bestaat in Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [De account status van een gebruiker controleren](#check-a-users-account-status)

-   [Het wacht woord van een gebruiker opnieuw instellen](#reset-a-users-password)

-   [Selfservice voor wachtwoordherstel inschakelen](#enable-self-service-password-reset)

-   [De multi-factor Authentication-status van een gebruiker controleren](#check-a-users-multi-factor-authentication-status)

-   [De contact gegevens voor de verificatie van een gebruiker controleren](#check-a-users-authentication-contact-info)

-   [De groepslid maatschappen van een gebruiker controleren](#check-a-users-group-memberships)

-   [De toegewezen licenties van een gebruiker controleren](#check-a-users-assigned-licenses)

-   [Een licentie toewijzen aan een gebruiker](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Controleren of een gebruikers account bestaat in Azure Active Directory

Voer de volgende stappen uit om te controleren of het account van een gebruiker aanwezig is:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **gebruikers en groepen** in het navigatie menu.

5.  Klik op **alle gebruikers**.

6.  **Zoek** naar de gebruiker die u wilt interesseren en **Klik op de rij om deze** te selecteren.

7.  Controleer de eigenschappen van het gebruikers object om er zeker van te zijn dat ze eruitzien zoals verwacht en er geen gegevens ontbreken.

### <a name="check-a-users-account-status"></a>De account status van een gebruiker controleren

Voer de volgende stappen uit om de account status van een gebruiker te controleren:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **gebruikers en groepen** in het navigatie menu.

5.  Klik op **alle gebruikers**.

6.  **Zoek** naar de gebruiker die u wilt interesseren en **Klik op de rij om deze** te selecteren.

7.  Klik op **profiel**.

8.  Onder **instellingen** zorgt u ervoor dat **blok keren aanmelden** is ingesteld op **Nee**.

### <a name="reset-a-users-password"></a>Het wacht woord van een gebruiker opnieuw instellen

Voer de volgende stappen uit om het wacht woord van een gebruiker opnieuw in te stellen:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **gebruikers en groepen** in het navigatie menu.

5.  Klik op **alle gebruikers**.

6.  **Zoek** naar de gebruiker die u wilt interesseren en **Klik op de rij om deze** te selecteren.

7.  Klik boven aan het deel venster van de gebruiker op de knop **wacht woord opnieuw instellen** .

8.  Klik op de knop **wacht woord opnieuw instellen** in het deel venster **wacht woord opnieuw instellen** dat wordt weer gegeven.

9.  Kopieer het **tijdelijke wacht woord** of **Voer een nieuw wacht woord** voor de gebruiker in.

10. Dit nieuwe wacht woord aan de gebruiker door geven, is het verplicht dit wacht woord te wijzigen bij de volgende aanmelding bij Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Selfservice voor wachtwoordherstel inschakelen

Als u de selfservice voor wachtwoord herstel wilt inschakelen, volgt u de onderstaande stappen voor de implementatie:

-   [Gebruikers in staat stellen hun Azure Active Directory wacht woorden opnieuw in te stellen](../authentication/tutorial-enable-sspr.md)

-   [Gebruikers in staat stellen om hun Active Directory on-premises wacht woorden opnieuw in te stellen of te wijzigen](../authentication/tutorial-enable-sspr.md)

### <a name="check-a-users-multi-factor-authentication-status"></a>De multi-factor Authentication-status van een gebruiker controleren

Voer de volgende stappen uit om de multi-factor Authentication-status van een gebruiker te controleren:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **gebruikers en groepen** in het navigatie menu.

5. Klik op **alle gebruikers**.

6. Klik boven aan het deel venster op de knop **multi-factor Authentication** .

7. Nadat de **multi-factor Authentication beheer Portal** is geladen, controleert u of u zich op het tabblad **gebruikers** bevindt.

8. Zoek de gebruiker in de lijst met gebruikers door te zoeken, te filteren of te sorteren.

9. Selecteer de gebruiker in de lijst met gebruikers en **Schakel** indien **gewenst multi-** Factor Authentication in, **uit** of af.

   * **Opmerking**: als een gebruiker zich in een **afgedwongen** status bevindt, kunt u deze zo instellen dat ze tijdelijk worden **uitgeschakeld** zodat ze weer in hun account kunnen worden gezet. Zodra ze weer in zijn, kunt u de status wijzigen in **ingeschakeld** zodat ze hun contact gegevens opnieuw moeten registreren tijdens de volgende aanmelding. U kunt ook de stappen volgen in de [contact gegevens van een gebruiker controleren](#check-a-users-authentication-contact-info) om te controleren of deze gegevens voor hen in te stellen.

### <a name="check-a-users-authentication-contact-info"></a>De contact gegevens voor de verificatie van een gebruiker controleren

Voer de volgende stappen uit om de contact gegevens van een gebruiker te controleren die wordt gebruikt voor multi-factor Authentication, voorwaardelijke toegang, identiteits beveiliging en wachtwoord herstel:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **gebruikers en groepen** in het navigatie menu.

5.  Klik op **alle gebruikers**.

6.  **Zoek** naar de gebruiker die u wilt interesseren en **Klik op de rij om deze** te selecteren.

7.  Klik op **profiel**.

8.  Schuif omlaag naar de **contact gegevens** van de verificatie.

9.  **Controleer** de gegevens die voor de gebruiker zijn geregistreerd en werk deze indien nodig bij.

### <a name="check-a-users-group-memberships"></a>De groepslid maatschappen van een gebruiker controleren

Voer de volgende stappen uit om de groepslid maatschappen van een gebruiker te controleren:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **gebruikers en groepen** in het navigatie menu.

5.  Klik op **alle gebruikers**.

6.  **Zoek** naar de gebruiker die u wilt interesseren en **Klik op de rij om deze** te selecteren.

7.  Klik op **groepen** om te zien van welke groepen de gebruiker lid is.

### <a name="check-a-users-assigned-licenses"></a>De toegewezen licenties van een gebruiker controleren

Voer de volgende stappen uit om de toegewezen licenties van een gebruiker te controleren:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **gebruikers en groepen** in het navigatie menu.

5.  Klik op **alle gebruikers**.

6.  **Zoek** naar de gebruiker die u wilt interesseren en **Klik op de rij om deze** te selecteren.

7.  Klik op **licenties** om te zien welke licenties de gebruiker momenteel heeft toegewezen.

### <a name="assign-a-user-a-license"></a>Een licentie toewijzen aan een gebruiker 

Voer de volgende stappen uit om een licentie aan een gebruiker toe te wijzen:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **gebruikers en groepen** in het navigatie menu.

5.  Klik op **alle gebruikers**.

6.  **Zoek** naar de gebruiker die u wilt interesseren en **Klik op de rij om deze** te selecteren.

7.  Klik op **licenties** om te zien welke licenties de gebruiker momenteel heeft toegewezen.

8.  Klik op de knop **toewijzen** .

9.  Selecteer **een of meer producten** uit de lijst met beschik bare producten.

10. **Optioneel** klikt u op het item **toewijzings opties** om producten nauw keurig toe te wijzen. Klik op **OK** wanneer dit is voltooid.

11. Klik op de knop **toewijzen** om deze licenties aan deze gebruiker toe te wijzen.

## <a name="problems-with-groups"></a>Problemen met groepen

Toegang tot toepassingen kan worden geblokkeerd vanwege een probleem met een groep die is toegewezen aan de toepassing. Hieronder vindt u enkele manieren waarop u problemen met groepen en groepslid maatschappen kunt oplossen en oplossen:

-   [Het lidmaatschap van een groep controleren](#check-a-groups-membership)

-   [De lidmaatschaps criteria van een dynamische groep controleren](#check-a-dynamic-groups-membership-criteria)

-   [De toegewezen licenties van een groep controleren](#check-a-groups-assigned-licenses)

-   [De licenties van een groep opnieuw verwerken](#reprocess-a-groups-licenses)

-   [Een licentie toewijzen aan een groep](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Het lidmaatschap van een groep controleren

Voer de volgende stappen uit om het lidmaatschap van een groep te controleren:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **gebruikers en groepen** in het navigatie menu.

5.  Klik op **alle groepen**.

6.  **Zoek** de groep waarin u bent geïnteresseerd en **Klik op de rij** die u wilt selecteren.

7.  Klik op **leden** om de lijst met gebruikers die aan deze groep zijn toegewezen, te bekijken.

### <a name="check-a-dynamic-groups-membership-criteria"></a>De lidmaatschaps criteria van een dynamische groep controleren 

Als u de lidmaatschaps criteria van een dynamische groep wilt controleren, voert u de volgende stappen uit:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **gebruikers en groepen** in het navigatie menu.

5.  Klik op **alle groepen**.

6.  **Zoek** de groep waarin u bent geïnteresseerd en **Klik op de rij** die u wilt selecteren.

7.  Klik op **dynamische lidmaatschaps regels.**

8.  Controleer de **eenvoudige** of **Geavanceerde** regel die voor deze groep is gedefinieerd en zorg ervoor dat de gebruiker die u lid wilt maken van deze groep aan deze criteria voldoet.

### <a name="check-a-groups-assigned-licenses"></a>De toegewezen licenties van een groep controleren

Voer de volgende stappen uit om de toegewezen licenties van een groep te controleren:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **gebruikers en groepen** in het navigatie menu.

5.  Klik op **alle groepen**.

6.  **Zoek** de groep waarin u bent geïnteresseerd en **Klik op de rij** die u wilt selecteren.

7.  Klik op **licenties** om te zien welke licenties de groep momenteel heeft toegewezen.

### <a name="reprocess-a-groups-licenses"></a>De licenties van een groep opnieuw verwerken

Voer de volgende stappen uit om de toegewezen licenties van een groep opnieuw te verwerken:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **gebruikers en groepen** in het navigatie menu.

5. Klik op **alle groepen**.

6. **Zoek** de groep waarin u bent geïnteresseerd en **Klik op de rij** die u wilt selecteren.

7. Klik op **licenties** om te zien welke licenties de groep momenteel heeft toegewezen.

8. Klik op de knop opnieuw **verwerken** om ervoor te zorgen dat de licenties die zijn toegewezen aan de leden van deze groep up-to-date zijn. Dit kan enige tijd duren, afhankelijk van de grootte en complexiteit van de groep.

   >[!NOTE]
   >Als u dit sneller wilt doen, kunt u overwegen om een tijdelijke licentie rechtstreeks aan de gebruiker toe te wijzen. [Een licentie toewijzen aan een gebruiker](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Een licentie toewijzen aan een groep

Voer de volgende stappen uit om een licentie toe te wijzen aan een groep:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **gebruikers en groepen** in het navigatie menu.

5. Klik op **alle groepen**.

6. **Zoek** de groep waarin u bent geïnteresseerd en **Klik op de rij** die u wilt selecteren.

7. Klik op **licenties** om te zien welke licenties de groep momenteel heeft toegewezen.

8. Klik op de knop **toewijzen** .

9. Selecteer **een of meer producten** uit de lijst met beschik bare producten.

10. **Optioneel** klikt u op het item **toewijzings opties** om producten nauw keurig toe te wijzen. Klik op **OK** wanneer dit is voltooid.

11. Klik op de knop **toewijzen** om deze licenties aan deze groep toe te wijzen. Dit kan enige tijd duren, afhankelijk van de grootte en complexiteit van de groep.

    >[!NOTE]
    >Als u dit sneller wilt doen, kunt u overwegen om een tijdelijke licentie rechtstreeks aan de gebruiker toe te wijzen. [Een licentie toewijzen aan een gebruiker](#problems-with-application-consent).
    > 
    >

## <a name="problems-with-conditional-access-policies"></a>Problemen met beleid voor voorwaardelijke toegang

### <a name="check-a-specific-conditional-access-policy"></a>Een specifiek beleid voor voorwaardelijke toegang controleren

Eén beleid voor voorwaardelijke toegang controleren of valideren:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik in het navigatie menu op **bedrijfs toepassingen** .

5. Klik op het navigatie-item **voorwaardelijke toegang** .

6. Klik op het beleid dat u wilt inspecteren.

7. Controleer of er geen specifieke voor waarden, toewijzingen of andere instellingen zijn die gebruikers toegang kunnen blok keren.

   >[!NOTE]
   >U kunt dit beleid tijdelijk uitschakelen om te controleren of het geen invloed heeft op aanmeldingen. Als u dit wilt doen, stelt u de schakel optie voor het **inschakelen van beleid** in op **Nee** en klikt u op de knop **Opslaan** .
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Het beleid voor voorwaardelijke toegang van een specifieke toepassing controleren

Controleren of valideren van het momenteel geconfigureerde beleid voor voorwaardelijke toegang van één toepassing:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik in het navigatie menu op **bedrijfs toepassingen** .

5.  Klik op **alle toepassingen**.

6.  Zoek naar de toepassing waarin u geïnteresseerd bent of probeer zich aan te melden bij de weergave naam van de toepassing of de toepassings-ID.

     >[!NOTE]
     >Als u de toepassing die u zoekt niet ziet, klikt u op de knop **filter** en breidt u het bereik van de lijst uit naar **alle toepassingen**. Als u meer kolommen wilt zien, klikt u op de knop **kolommen** om meer informatie over uw toepassingen toe te voegen.
     >
     >

7.  Klik op het navigatie-item **voorwaardelijke toegang** .

8.  Klik op het beleid dat u wilt inspecteren.

9.  Controleer of er geen specifieke voor waarden, toewijzingen of andere instellingen zijn die gebruikers toegang kunnen blok keren.

     >[!NOTE]
     >U kunt dit beleid tijdelijk uitschakelen om te controleren of het geen invloed heeft op aanmeldingen. Als u dit wilt doen, stelt u de schakel optie voor het **inschakelen van beleid** in op **Nee** en klikt u op de knop **Opslaan** .
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Een specifiek beleid voor voorwaardelijke toegang uitschakelen

Eén beleid voor voorwaardelijke toegang controleren of valideren:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik in het navigatie menu op **bedrijfs toepassingen** .

5.  Klik op het navigatie-item **voorwaardelijke toegang** .

6.  Klik op het beleid dat u wilt inspecteren.

7.  Schakel het beleid uit door de **Schakel** optie voor het inschakelen van beleid in te stellen op **Nee** en op de knop **Opslaan** te klikken.

## <a name="problems-with-application-consent"></a>Problemen met toestemming van de toepassing

Toegang tot toepassingen kan worden geblokkeerd omdat de machtiging voor de juiste machtigingen niet is opgetreden. Hieronder vindt u enkele manieren waarop u problemen met de toepassings instemming kunt oplossen en oplossen:

-   [Een toestemmings bewerking op gebruikers niveau uitvoeren](#perform-a-user-level-consent-operation)

-   [Bewerking op beheerders niveau uitvoeren voor elke toepassing](#perform-administrator-level-consent-operation-for-any-application)

-   [Toestemming op beheerders niveau uitvoeren voor een toepassing met één Tenant](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Toestemming op beheerders niveau uitvoeren voor een toepassing met meerdere tenants](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Een toestemmings bewerking op gebruikers niveau uitvoeren

-   Voor elke open-ID-toepassing met Connect die machtigingen aanvraagt, wordt door het aanmelden op het aanmeldings scherm van de toepassing een gebruikers niveau met toestemming voor de aangemelde gebruiker uitgevoerd.

-   Als u dit wilt doen via een programma, raadpleegt u de [vraag om toestemming van individuele gebruiker](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent).

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Bewerking op beheerders niveau uitvoeren voor elke toepassing

-   Alleen voor toepassingen die zijn **ontwikkeld met behulp van het v1-toepassings model** kunt u toestemming geven voor dit beheer niveau door '**? prompt = Administrator \_ toestemming**' toe te voegen aan het einde van de aanmeldings-URL van de toepassing.

-   Voor **elke toepassing die is ontwikkeld met behulp van het v2-toepassings model**, kunt u deze toestemming op beheerders niveau afdwingen met behulp van de instructies in de sectie **machtigingen aanvragen van een Directory-beheerder** van [het bestemmings eindpunt van de](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)beheerder.

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Toestemming op beheerders niveau uitvoeren voor een toepassing met één Tenant

-   Voor **toepassingen met één Tenant** die machtigingen aanvragen (zoals de ontwikkelings-of eigenaar van uw organisatie), kunt u een **toestemmings bewerking op beheer niveau** uitvoeren namens alle gebruikers door u aan te melden als globale beheerder en te klikken op de knop **machtigingen verlenen** boven aan het **toepassings register- &gt; alle toepassingen-Selecteer een deel venster voor &gt; app- &gt; vereiste machtigingen** .

-   Voor **elke toepassing die is ontwikkeld met behulp van het v1-of v2-toepassings model**, kunt u deze toestemming op beheerders niveau afdwingen met behulp van de instructies in de sectie **machtigingen aanvragen van een Directory-beheerder** van [het beheerders toestemming-eind punt](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)te volgen.

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Toestemming op beheerders niveau uitvoeren voor een toepassing met meerdere tenants

-   Voor **toepassingen met meerdere tenants** die machtigingen aanvragen (zoals een toepassing die door derden wordt uitgevoerd of micro soft zich ontwikkelt), kunt u een **toestemmings bewerking op beheer niveau** uitvoeren. Meld u aan als globale beheerder en klik op de knop **machtigingen verlenen** onder **bedrijfs toepassingen- &gt; alle toepassingen- &gt; Selecteer een app- &gt; machtigingen** venster (binnenkort beschikbaar).

-   U kunt deze toestemming op beheerders niveau ook afdwingen met behulp van de instructies in de sectie **machtigingen aanvragen van een Directory-beheerder** van [het gebruik van het afstemmings eindpunt van de](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)beheerder.

## <a name="next-steps"></a>Volgende stappen
[Het afstemmings eindpunt van de beheerder gebruiken](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)