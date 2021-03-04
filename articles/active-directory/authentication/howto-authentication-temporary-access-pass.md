---
title: Een tijdelijke toegangs fase configureren in azure AD om authenticatie methoden met een wacht woord te registreren
description: Meer informatie over het configureren en inschakelen van gebruikers om verificatie methoden met een wacht woord te registreren met behulp van een tijdelijke toegangs doorvoer
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: justinha
author: inbarckms
manager: daveba
ms.reviewer: inbarckms
ms.collection: M365-identity-device-management
ms.openlocfilehash: 101e3ee9279d3560c0b561f0ea7ea695387bee15
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096459"
---
# <a name="configure-temporary-access-pass-in-azure-ad-to-register-passwordless-authentication-methods-preview"></a>Tijdelijke toegangs fase configureren in azure AD om verificatie methoden met een wacht woord (preview-versie) te registreren

Verificatie methoden met een wacht woord, zoals FIDO2 en wacht woordloze aanmelding via Microsoft Authenticator de app, zorgen ervoor dat gebruikers veilig kunnen worden aangemeld zonder wacht woord. Gebruikers kunnen methoden met een wacht woord op een van de volgende twee manieren Boots trapen:

- Bestaande Azure AD-methoden voor multi-factor Authentication gebruiken 
- Een tijdelijke toegangs doorvoer gebruiken 

Een tijdelijke toegangs doorgifte is een tijdgebonden wachtwoord code die door een beheerder is uitgegeven en die voldoet aan de vereisten voor sterke verificatie, en kan worden gebruikt om andere verificatie methoden te gebruiken, met inbegrip van wacht woorden. Een tijdelijke toegangs doorgifte zorgt er ook voor dat de herstel bewerking eenvoudiger is wanneer een gebruiker de sterke authenticatie factor zoals een FIDO2 of Microsoft Authenticator-app kwijtraakt of vergeet, maar moet zich aanmelden om nieuwe krachtige authenticatie methoden te registreren.


In dit artikel wordt beschreven hoe u een tijdelijke toegangs fase in azure AD kunt inschakelen en gebruiken met behulp van de Azure Portal. U kunt deze acties ook uitvoeren met behulp van de REST-Api's. 

>[!NOTE]
>De tijdelijke toegangs fase is momenteel beschikbaar als open bare preview. Sommige functies worden mogelijk niet ondersteund of hebben beperkte mogelijkheden. 

## <a name="enable-the-temporary-access-pass-policy"></a>Het beleid voor tijdelijke toegang inschakelen

Een tijdelijk toegangs beleid definieert instellingen, zoals de levens duur van gangen die zijn gemaakt in de Tenant, of de gebruikers en groepen die een tijdelijke toegangs doorgifte kunnen gebruiken om zich aan te melden. Voordat iemand zich kan aanmelden met een tijdelijke toegangs fase, moet u het beleid voor de verificatie methode inschakelen en kiezen welke gebruikers en groepen zich kunnen aanmelden met een tijdelijke toegang door gegeven.
Hoewel u voor elke gebruiker een tijdelijke toegangs doorgifte kunt maken, kunnen alleen de gebruikers die zijn opgenomen in het beleid zich aanmelden.

Globale beheerder en authenticatie methode beleids beheerder rol houders kunnen het beleid voor de verificatie methode voor tijdelijke toegang bijwerken.
Het beleid voor de verificatie methode van tijdelijke toegang door geven:

1. Meld u aan bij de Azure portal als globale beheerder en klik op **Azure Active Directory**  >  **beveiligings**  >  **verificatie methoden**  >  **tijdelijke toegang door geven**.
1. Klik op **Ja** om het beleid in te scha kelen, Selecteer op welke gebruikers het beleid moet worden toegepast en geef **algemene** instellingen op.

   ![Scherm afbeelding van het inschakelen van het beleid voor de verificatie methode van tijdelijke toegang door geven](./media/how-to-authentication-temporary-access-pass/policy.png)

   De standaard waarde en het bereik van toegestane waarden worden in de volgende tabel beschreven.


   | Instelling          | Standaardwaarden | Toegestane waarden               | Opmerkingen                                                                                                                                                                                                                                                                 |   |
   |------------------|----------------|------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    Minimale levens duur | 1 uur         | 10 – 43200 minuten (30 dagen) | Minimum aantal minuten dat de tijdelijke toegangs doorgifte geldig is.                                                                                                                                                                                                                         |   |
   | Maximale levens duur | 24 uur       | 10 – 43200 minuten (30 dagen) | Maximum aantal minuten dat de tijdelijke toegangs doorgifte geldig is.                                                                                                                                                                                                                         |   |
   | Standaard levensduur | 1 uur         | 10 – 43200 minuten (30 dagen) | Standaard waarden kunnen worden overschreven door de afzonderlijke fasen, binnen de minimale en maximale levens duur die door het beleid zijn geconfigureerd                                                                                                                                                |   |
   | Eenmalig gebruik     | Niet waar          | Waar/onwaar                 | Wanneer het beleid is ingesteld op ONWAAR, kunnen door gegeven in de Tenant één keer of meerdere keren worden gebruikt tijdens de geldigheid (maximale levens duur). Door eenmalig gebruik af te dwingen in het beleid voor tijdelijke toegang, worden alle door gegeven die zijn gemaakt in de Tenant, gemaakt als eenmalig gebruik. |   |
   | Lengte           | 8              | 8-48 tekens              | Hiermee definieert u de lengte van de wachtwoord code.                                                                                                                                                                                                                                      |   |

## <a name="create-a-temporary-access-pass-in-the-azure-ad-portal"></a>Een tijdelijke toegangs fase maken in de Azure AD-Portal

Nadat u een beleid hebt ingeschakeld, kunt u een tijdelijke toegangs doorgifte voor een gebruiker in azure AD maken. Deze rollen kunnen de volgende acties uitvoeren met betrekking tot een tijdelijke toegangs doorvoer.

- De globale beheerder kan op een wille keurige gebruiker een tijdelijke toegangs doorgifte maken, verwijderen en bekijken (behalve zelf)
- Bevoegde authenticatie beheerders kunnen een tijdelijke toegangs doorgifte voor beheerders en leden maken, verwijderen en bekijken (behalve zelf)
- Verificatie beheerders kunnen een tijdelijke toegangs doorgifte voor leden maken, verwijderen en bekijken (behalve zelf)
- De globale beheerder kan de gegevens van de tijdelijke toegangs doorgifte weer geven voor de gebruiker (zonder de code zelf te lezen).

Een tijdelijke toegangs fase maken:

1. Meld u aan bij de portal als een globale beheerder, bevoegde verificatie beheerder of verificatie beheerder. 
1. Klik op **Azure Active Directory**, blader naar gebruikers, selecteer een gebruiker, zoals *Chris groen*, en kies vervolgens **verificatie methoden**.
1. Selecteer, indien nodig, de optie voor **het uitproberen van de nieuwe gebruikers verificatie methoden**.
1. Selecteer de optie om **verificatie methoden toe te voegen**.
1. Klik onder **methode kiezen** op **tijdelijke toegang door geven (preview-versie)**.
1. Definieer een aangepaste activerings tijd of-duur en klik op **toevoegen**.

   ![Scherm afbeelding van het maken van een tijdelijke toegangs fase](./media/how-to-authentication-temporary-access-pass/create.png)

1. Zodra het apparaat is toegevoegd, worden de details van de tijdelijke toegangs fase weer gegeven. Noteer de werkelijke waarde voor tijdelijke toegang door gegeven. U geeft deze waarde aan de gebruiker. U kunt deze waarde niet weer geven nadat u op **OK** hebt geklikt.
   
   ![Scherm afbeelding van de details van de tijdelijke toegang](./media/how-to-authentication-temporary-access-pass/details.png)

## <a name="use-a-temporary-access-pass"></a>Een tijdelijke toegangs doorvoer gebruiken

Het meest voorkomende gebruik van een tijdelijke toegangs fase is dat een gebruiker tijdens de eerste aanmelding verificatie gegevens kan registreren zonder dat er extra beveiligings vragen moeten worden uitgevoerd. Verificatie methoden zijn geregistreerd op [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Gebruikers kunnen hier ook bestaande verificatie methoden bijwerken.

1. Open een webbrowser naar [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) .
1. Voer de UPN in van het account waarvoor u de tijdelijke toegangs fase hebt gemaakt, zoals *tapuser@contoso.com* .
1. Als de gebruiker is opgenomen in het tijdelijke toegangs beleid, wordt er een scherm weer gegeven waarin de tijdelijke toegangs fase wordt ingevoerd.
1. Voer de tijdelijke toegangs doorgifte in die wordt weer gegeven in de Azure Portal.

   ![Scherm afbeelding van het invoeren van een tijdelijke toegangs fase](./media/how-to-authentication-temporary-access-pass/enter.png)

>[!NOTE]
>Voor federatieve domeinen geldt een tijdelijke toegangs fase voor de voor keur boven Federatie. Een gebruiker met een tijdelijke toegangs fase voltooit de verificatie in azure AD en wordt niet omgeleid naar de provider voor federatieve identiteiten (IdP).

De gebruiker is nu aangemeld en kan een methode, zoals FIDO2-beveiligings sleutel, bijwerken of registreren. Gebruikers die hun verificatie methoden bijwerken omdat ze hun referenties of apparaat kwijt raken, moeten er zeker van zijn dat ze de oude verificatie methoden verwijderen.

Gebruikers kunnen ook hun tijdelijke toegangs fase gebruiken om zich rechtstreeks vanuit de verificator-app te registreren voor aanmelding zonder wacht woord. Zie [uw werk-of school account toevoegen aan de app Microsoft Authenticator](../user-help/user-help-auth-app-add-work-school-account.md)voor meer informatie.

![Scherm afbeelding van het invoeren van een tijdelijke toegang door geven met werk-of school account](./media/how-to-authentication-temporary-access-pass/enter-work-school.png)

## <a name="delete-a-temporary-access-pass"></a>Een tijdelijke toegangs doorgifte verwijderen

Een verlopen tijdelijke toegangs doorvoer kan niet worden gebruikt. Onder de **verificatie methoden** voor een gebruiker, wordt in de **detail** kolom weer gegeven wanneer de tijdelijke toegangs doorgifte is verlopen. U kunt een verlopen tijdelijke toegangs fase verwijderen door de volgende stappen uit te voeren:

1. Blader in de Azure AD-Portal naar **gebruikers**, selecteer een gebruiker, zoals *Tik op gebruiker* en kies **verificatie methoden**.
1. Selecteer aan de rechter kant van de verificatie methode voor de **tijdelijke toegangs fase (preview)** die in de lijst wordt weer gegeven, de optie **verwijderen**.

## <a name="replace-a-temporary-access-pass"></a>Een tijdelijke toegangs doorvoer vervangen 

- Een gebruiker kan slechts één tijdelijke toegang door geven. De wachtwoord code kan worden gebruikt tijdens de begin-en eind tijd van de tijdelijke toegangs fase.
- Als de gebruiker een nieuwe tijdelijke toegangs fase vereist:
  - Als de bestaande tijdelijke toegangs doorgifte geldig is, moet de beheerder de bestaande tijdelijke toegangs fase verwijderen en een nieuwe Pass-bewerking maken voor de gebruiker. Als u een geldige tijdelijke toegangs doorvoer verwijdert, worden de sessies van de gebruiker ingetrokken. 
  - Als de bestaande tijdelijke toegangs fase is verlopen, wordt door een nieuwe tijdelijke toegangs fase de bestaande tijdelijke toegangs fase overschreven en worden de sessies van de gebruiker niet ingetrokken.

Zie voor meer informatie over de NIST-standaarden voor onboarding en herstel de [specifieke NIST-publicatie 800-63a](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4).

## <a name="limitations"></a>Beperkingen

Houd u aan de volgende beperkingen:

- Wanneer u een eenmalige tijdelijke toegangs fase gebruikt voor het registreren van een wacht woordloze methode zoals FIDO2 of aanmelding via de telefoon, moet de gebruiker de registratie volt ooien binnen 10 minuten na het aanmelden met de eenmalige tijdelijke toegangs fase. Deze beperking is niet van toepassing op een tijdelijke toegangs doorgifte die meer dan één keer kan worden gebruikt.
- Gast gebruikers kunnen zich niet aanmelden met tijdelijke toegang door gegeven.
- Gebruikers binnen het bereik voor selfservice voor wachtwoord herstel (SSPR) moeten een van de SSPR-methoden registreren nadat ze zich hebben aangemeld met een tijdelijke toegangs fase. Als de gebruiker alleen de FIDO2-sleutel gaat gebruiken, moet u deze uitsluiten van het SSPR-beleid of het SSPR-registratie beleid uitschakelen. 
- Een tijdelijke toegangs doorvoer kan niet worden gebruikt met de extensie Network Policy Server (NPS) en Active Directory Federation Services (AD FS).
- Wanneer naadloze SSO is ingeschakeld op de Tenant, wordt de gebruiker gevraagd een wacht woord in te voeren. De koppeling **gebruik van uw tijdelijke toegang in plaats daarvan** is beschikbaar voor de gebruiker om u aan te melden met een tijdelijke toegangs fase.

![Scherm opname van het gebruik van een tijdelijke Access-door Voer](./media/how-to-authentication-temporary-access-pass/alternative.png)

## <a name="troubleshooting"></a>Problemen oplossen    

- Als er tijdens het aanmelden geen tijdelijke toegangs doorvoer wordt aangeboden aan een gebruiker, controleert u het volgende:
  - De gebruiker is in het bereik voor het beleid voor de verificatie methode van tijdelijke toegang door gegeven.
  - De gebruiker heeft een geldige tijdelijke toegangs fase en als deze eenmalig wordt gebruikt, is deze nog niet gebruikt.
- Als **tijdelijke toegang door geven is geblokkeerd vanwege beleid voor gebruikers referenties** wordt weer gegeven tijdens het aanmelden met een tijdelijke toegangs fase, controleert u het volgende:
  - De gebruiker heeft een tijdelijke toegangs doorvoer voor meerdere gebruiks tijden, terwijl het beleid voor de verificatie methode een eenmalige tijdelijke toegangs fase vereist.
  - Er wordt al een eenmalige tijdelijke toegang gebruikt.

## <a name="next-steps"></a>Volgende stappen

- [Een authenticatie-implementatie met een wacht woord plannen in Azure Active Directory](howto-authentication-passwordless-deployment.md)

