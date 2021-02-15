---
title: Een tijdelijke toegangs fase configureren in azure AD om authenticatie methoden met een wacht woord te registreren
description: Meer informatie over het configureren en inschakelen van gebruikers om verificatie methoden met een wacht woord te registreren met behulp van een tijdelijke toegangs doorvoer (tik)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: justinha
author: inbarckms
manager: daveba
ms.reviewer: inbarckms
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56d45119fa86ab47e6a625c628d8cb9763db83bd
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520917"
---
# <a name="configure-temporary-access-pass-in-azure-ad-to-register-passwordless-authentication-methods-preview"></a>Tijdelijke toegangs fase configureren in azure AD om verificatie methoden met een wacht woord (preview-versie) te registreren

Verificatie methoden met een wacht woord, zoals FIDO2 en wacht woordloze aanmelding via Microsoft Authenticator de app, zorgen ervoor dat gebruikers veilig kunnen worden aangemeld zonder wacht woord. Gebruikers kunnen methoden met een wacht woord op een van de volgende twee manieren Boots trapen:

- Bestaande Azure AD-methoden voor multi-factor Authentication gebruiken 
- Een tijdelijke toegangs doorvoer gebruiken (tik) 

Tik is een tijdgebonden wachtwoord code die door een beheerder is uitgegeven en die voldoet aan de vereisten voor sterke verificatie en kan worden gebruikt om andere verificatie methoden op te maken, inclusief wacht woorden. Met tikken wordt het herstel ook eenvoudiger wanneer een gebruiker de sterke verificatie factor, zoals een FIDO2 of Microsoft Authenticator-app kwijtraakt of vergeet, maar moet zich aanmelden om nieuwe krachtige authenticatie methoden te registreren.


Dit artikel laat u zien hoe u een tik in azure AD kunt inschakelen en gebruiken met behulp van de Azure Portal. U kunt deze acties ook uitvoeren met behulp van de REST-Api's. 

>[!NOTE]
>De tijdelijke toegangs fase is momenteel beschikbaar als open bare preview. Sommige functies worden mogelijk niet ondersteund of hebben beperkte mogelijkheden. 

## <a name="enable-the-tap-policy"></a>Het TAP-beleid inschakelen

Een tik-beleid definieert instellingen, zoals de levens duur van gangen die zijn gemaakt in de Tenant, of de gebruikers en groepen die een tik kunnen gebruiken om zich aan te melden. Voordat iemand zich kan aanmelden met een tik, moet u het beleid voor de verificatie methode inschakelen en kiezen welke gebruikers en groepen zich kunnen aanmelden met behulp van een tik.
Hoewel u een tik voor een wille keurige gebruiker kunt maken, kunnen alleen de gebruikers die zijn opgenomen in het beleid zich aanmelden.

Globale beheerder en authenticatie methode beleids beheerder rol houders kunnen het beleid voor de Tik-verificatie methode bijwerken.
Het beleid voor de verificatie methode tikken configureren:

1. Meld u aan bij de Azure portal als globale beheerder en klik op **Azure Active Directory**  >  **beveiligings**  >  **verificatie methoden**  >  **tijdelijke toegang door geven**.
1. Klik op **Ja** om het beleid in te scha kelen, Selecteer op welke gebruikers het beleid moet worden toegepast en geef **algemene** instellingen op.

   ![Scherm afbeelding van het inschakelen van het beleid voor verificatie methode tikken](./media/how-to-authentication-temporary-access-pass/policy.png)

   De standaard waarde en het bereik van toegestane waarden worden in de volgende tabel beschreven.


   | Instelling          | Standaardwaarden | Toegestane waarden               | Opmerkingen                                                                                                                                                                                                                                                                 |   |
   |------------------|----------------|------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    Minimale levens duur | 1 uur         | 10 – 43200 minuten (30 dagen) | Minimum aantal minuten dat de kraan geldig is.                                                                                                                                                                                                                         |   |
   | Maximale levens duur | 24 uur       | 10 – 43200 minuten (30 dagen) | Maximum aantal minuten dat de kraan geldig is.                                                                                                                                                                                                                         |   |
   | Standaard levensduur | 1 uur         | 10 – 43200 minuten (30 dagen) | Standaard waarden kunnen worden overschreven door de afzonderlijke fasen, binnen de minimale en maximale levens duur die door het beleid zijn geconfigureerd                                                                                                                                                |   |
   | Eenmalig gebruik     | Niet waar          | Waar/onwaar                 | Wanneer het beleid is ingesteld op ONWAAR, kunnen door gegeven in de Tenant één keer of meerdere keren worden gebruikt tijdens de geldigheid (maximale levens duur). Door eenmalig gebruik af te dwingen in het TAP-beleid, worden alle door gegeven in de Tenant gemaakte fasen gemaakt als eenmalig gebruik. |   |
   | Lengte           | 8              | 8-48 tekens              | Hiermee definieert u de lengte van de wachtwoord code.                                                                                                                                                                                                                                      |   |

## <a name="create-a-tap-in-the-azure-ad-portal"></a>Een tik maken in de Azure AD-Portal

Nadat u een TAP-beleid hebt ingeschakeld, kunt u een tik maken voor een gebruiker in azure AD. Deze rollen kunnen de volgende acties uitvoeren met betrekking tot tikken.

- De globale beheerder kan op een wille keurige gebruiker tikken maken, verwijderen, bekijken (behalve zelf)
- Bevoegde authenticatie beheerders kunnen tikken maken, verwijderen, weer geven op beheerders en leden (behalve zelf)
- Verificatie beheerders kunnen tikken op leden maken, verwijderen, bekijken (behalve zelf)
- De globale beheerder kan de TAP-gegevens weer geven voor de gebruiker (zonder de code zelf te lezen).

Een tik maken:

1. Meld u aan bij de portal als een globale beheerder, bevoegde verificatie beheerder of verificatie beheerder. 
1. Klik op **Azure Active Directory**, blader naar gebruikers, selecteer een gebruiker, zoals *Chris groen*, en kies vervolgens **verificatie methoden**.
1. Selecteer, indien nodig, de optie voor **het uitproberen van de nieuwe gebruikers verificatie methoden**.
1. Selecteer de optie om **verificatie methoden toe te voegen**.
1. Klik onder **methode kiezen** op **tijdelijke toegang door geven (preview-versie)**.
1. Definieer een aangepaste activerings tijd of-duur en klik op **toevoegen**.

   ![Scherm afbeelding van het maken van een tik](./media/how-to-authentication-temporary-access-pass/create.png)

1. Zodra de gegevens zijn toegevoegd, worden deze weer gegeven. Noteer de werkelijke TAP-waarde. U geeft deze waarde aan de gebruiker. U kunt deze waarde niet weer geven nadat u op **OK** hebt geklikt.
   
   ![Scherm afbeelding van Tik op Details](./media/how-to-authentication-temporary-access-pass/details.png)

## <a name="use-a-tap"></a>Tik gebruiken

Het meest voorkomende gebruik voor een tik is voor een gebruiker om verificatie gegevens te registreren tijdens de eerste aanmelding, zonder dat er extra beveiligings vragen moeten worden uitgevoerd. Verificatie methoden zijn geregistreerd op [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Gebruikers kunnen hier ook bestaande verificatie methoden bijwerken.

1. Open een webbrowser naar [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) .
1. Voer de UPN in van het account waarvoor u het tikken hebt gemaakt, zoals *tapuser@contoso.com* .
1. Als de gebruiker is opgenomen in het TAP-beleid, wordt er een scherm weer gegeven waarin het tikken kan worden ingevoerd.
1. Voer het tikken in dat in de Azure Portal wordt weer gegeven.

   ![Scherm afbeelding van het invoeren van een tik](./media/how-to-authentication-temporary-access-pass/enter.png)

>[!NOTE]
>Voor federatieve domeinen heeft een tik de voor keur boven Federatie. Een gebruiker met een tik voltooit de verificatie in azure AD en wordt niet omgeleid naar de federatieve id-provider (IdP).

De gebruiker is nu aangemeld en kan een methode, zoals FIDO2-beveiligings sleutel, bijwerken of registreren. Gebruikers die hun verificatie methoden bijwerken omdat ze hun referenties of apparaat kwijt raken, moeten er zeker van zijn dat ze de oude verificatie methoden verwijderen.

Gebruikers kunnen ook hun tikken gebruiken om zich rechtstreeks vanuit de verificator-app te registreren voor aanmelding zonder wacht woord. Zie [uw werk-of school account toevoegen aan de app Microsoft Authenticator](../user-help/user-help-auth-app-add-work-school-account.md)voor meer informatie.

![Scherm afbeelding van het invoeren van een tik met een werk-of school account](./media/how-to-authentication-temporary-access-pass/enter-work-school.png)

## <a name="delete-a-tap"></a>Een tik verwijderen

Een verlopen TIKT kan niet worden gebruikt. Onder de **verificatie methoden** voor een gebruiker wordt in de **detail** kolom weer gegeven wanneer het tikken is verlopen. U kunt deze verlopen kranen verwijderen door de volgende stappen uit te voeren:

1. Blader in de Azure AD-Portal naar **gebruikers**, selecteer een gebruiker, zoals *Tik op gebruiker* en kies **verificatie methoden**.
1. Selecteer aan de rechter kant van de verificatie methode voor de **tijdelijke toegangs fase (preview)** die in de lijst wordt weer gegeven, de optie **verwijderen**.

## <a name="replace-a-tap"></a>Een tik vervangen 

- Een gebruiker kan slechts één tik hebben. De wachtwoord code kan worden gebruikt tijdens de begin-en eind tijd van het tikken.
- Als de gebruiker een nieuwe Tik vereist:
  - Als de bestaande Tik geldig is, moet de beheerder de bestaande TAP verwijderen en een nieuwe Pass-bewerking maken voor de gebruiker. Als u een geldig tikken verwijdert, worden de sessies van de gebruiker ingetrokken. 
  - Als de bestaande Tik is verlopen, wordt de bestaande Tik overschreven door een nieuwe Tik en worden de sessies van de gebruiker niet ingetrokken.

Zie voor meer informatie over de NIST-standaarden voor onboarding en herstel de [specifieke NIST-publicatie 800-63a](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4).

## <a name="limitations"></a>Beperkingen

Houd u aan de volgende beperkingen:

- Wanneer u een eenmalige Tik gebruikt voor het registreren van een wacht woordloze methode, zoals FIDO2 of aanmelding via de telefoon, moet de gebruiker de registratie volt ooien binnen 10 minuten na het aanmelden met de eenmalige tik. Deze beperking is niet van toepassing op een tik die meer dan één keer kan worden gebruikt.
- Gast gebruikers kunnen zich niet aanmelden met een tik.
- Gebruikers binnen het bereik voor selfservice voor wachtwoord herstel (SSPR) moeten een van de SSPR-methoden registreren nadat ze zich hebben aangemeld met tikken. Als de gebruiker alleen de FIDO2-sleutel gaat gebruiken, moet u deze uitsluiten van het SSPR-beleid of het SSPR-registratie beleid uitschakelen. 
- Tik kan niet worden gebruikt met de extensie Network Policy Server (NPS) en Active Directory Federation Services (AD FS).
- Wanneer naadloze SSO is ingeschakeld op de Tenant, wordt de gebruiker gevraagd een wacht woord in te voeren. De koppeling **gebruik van uw tijdelijke toegang in plaats daarvan** is beschikbaar voor de gebruiker om u aan te melden met tikken.

![Scherm opname van in plaats daarvan een tik gebruiken](./media/how-to-authentication-temporary-access-pass/alternative.png)

## <a name="troubleshooting"></a>Problemen oplossen    

- Als tikken tijdens het aanmelden niet aan een gebruiker wordt aangeboden, controleert u het volgende:
  - De gebruiker is binnen het bereik van het beleid voor authenticatie methode tikken.
  - De gebruiker heeft een geldig tikken en als het eenmaal wordt gebruikt, is deze nog niet gebruikt.
- Als **tijdelijke toegang door geven is geblokkeerd vanwege beleid voor gebruikers referenties** wordt weer gegeven tijdens het aanmelden met tikken, controleert u het volgende:
  - De gebruiker heeft op meerdere locaties tikken terwijl het beleid voor de verificatie methode één keer moet worden getikt.
  - Er is al een eenmalige Tik gebruikt.

## <a name="next-steps"></a>Volgende stappen

- [Een authenticatie-implementatie met een wacht woord plannen in Azure Active Directory](howto-authentication-passwordless-deployment.md)

