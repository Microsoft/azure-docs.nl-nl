---
title: Toegang tot Self-service toepassingen gebruiken in azure AD
description: Selfservice inschakelen zodat gebruikers apps kunnen vinden in azure AD
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere,asteen
ms.openlocfilehash: 13f91fdd9e2d9501fba426bd6facbf9824a39285
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257014"
---
# <a name="how-to-use-self-service-application-access"></a>Toegang tot Self-service toepassingen gebruiken

Voordat uw gebruikers zelf toepassingen van hun pagina mijn apps kunnen detecteren, moet u de toegang van **selfservice toepassingen** inschakelen voor alle toepassingen waarvoor u gebruikers de mogelijkheid wilt bieden om zichzelf te detecteren en toegang tot te vragen.

Deze functie is een uitstekende manier om tijd en geld te besparen als een IT-groep en wordt ten zeerste aanbevolen als onderdeel van een moderne implementatie van toepassingen met Azure Active Directory.

Zie [de Help van mijn apps-Portal](../user-help/my-apps-portal-end-user-access.md)voor meer informatie over het gebruik van mijn apps vanuit het oogpunt van eind gebruikers.

Met deze functie kunt u het volgende doen:

-   Laat gebruikers zelf toepassingen van [mijn apps](https://myapps.microsoft.com/) detecteren zonder dat ze de IT-groep hoeven te verzorgen.
-   Voeg deze gebruikers toe aan een vooraf geconfigureerde groep, zodat u kunt zien wie toegang heeft aangevraagd, de toegang moet verwijderen en de functies die eraan zijn toegewezen, moeten beheren.
-   Desgewenst toestaan dat iemand toegang tot aanvragen voor apps kan goed keuren, zodat de IT-groep het niet hoeft te doen.
-   U kunt Maxi maal 10 personen configureren die de toegang tot deze toepassing goed keuren.
-   Desgewenst toestaan dat iemand de wacht woorden instelt die gebruikers kunnen gebruiken om zich aan te melden bij de toepassing.
-   Automatisch toegewezen Self-Service gebruikers aan een toepassingsrol rechtstreeks toewijzen.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>De toegang van self-service toepassingen inschakelen om gebruikers toe te staan hun eigen toepassingen te vinden

Toegang voor selfservice toepassingen is een uitstekende manier om gebruikers de mogelijkheid te bieden om zelf toepassingen te detecteren, zodat de bedrijfs groep de toegang tot deze toepassingen kan goed keuren. U kunt de bedrijfs groep toestaan om de referenties te beheren die aan deze gebruikers zijn toegewezen voor wachtwoord Single-Sign op toepassingen rechts van hun pagina mijn apps.

Volg de onderstaande stappen om de toegang van selfservice toepassingen voor een toepassing in te scha kelen:
1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**
2. Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd navigatie menu te selecteren.
3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.
4. Selecteer **Bedrijfstoepassingen** in het linkernavigatiemenu van Azure Active Directory.
5. Selecteer **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.
   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**
6. Selecteer de toepassing waarvoor u selfservice toegang wilt inschakelen in de lijst.
7. Zodra de toepassing is geladen, selecteert u **self-service** in het navigatie menu van de toepassing aan de linkerkant.
8. Als u toegang voor selfservice toepassingen voor deze toepassing wilt inschakelen, schakelt u de optie **gebruikers toestaan om toegang te vragen tot deze toepassing?** in op **Ja.**
9. Vervolgens selecteert u de groep waartoe gebruikers die toegang tot deze toepassing vragen moeten worden toegevoegd, selecteert u de kiezer naast het label **waaraan gebruikers moeten worden toegevoegd** en selecteert u een groep.
10. **Optioneel:** Als u een zakelijke goed keuring wilt vereisen voordat gebruikers toegang worden verleend, **stelt u de** optie **goed keuring vereisen alvorens toegang tot deze toepassing verlenen** in.
11. **Optioneel: voor toepassingen die alleen gebruikmaken van wacht woord voor eenmalige aanmelding,** als u wilt toestaan dat de goed keurders van deze gebruiker de wacht woorden opgeven die naar deze toepassing worden verzonden voor goedgekeurde gebruikers, stelt u de **goed keurders toestaan om gebruikers wachtwoorden in te stellen voor deze toepassing?** **in-of uitschakelen.**
12. **Optioneel:** De zakelijke goed keurders opgeven die de toegang tot deze app mogen goed keuren. Selecteren **wie de toegang tot deze toepassing mag goed keuren?** Selecteer vervolgens Maxi maal 10 afzonderlijke goed keurders voor bedrijven.
    * Groepen worden niet ondersteund.
13. **Optioneel:** **voor toepassingen die rollen beschikbaar** maken, moet u, als u goedgekeurde selfservice gebruikers wilt toewijzen aan een rol, de selector selecteren naast de **rol waaraan gebruikers moeten worden toegewezen in deze toepassing?** om de rol te selecteren waaraan deze gebruikers moeten worden toegewezen.
14. Selecteer de knop **Opslaan** bovenaan om te volt ooien.

Zodra u de configuratie van de selfservice toepassing hebt voltooid, kunnen gebruikers naar [mijn apps](https://myapps.microsoft.com/) navigeren en de knop **+ toevoegen** selecteren om de apps te vinden waarvoor u selfservice toegang hebt ingeschakeld. Zakelijke goed keurders zien ook een melding op de pagina [mijn apps](https://myapps.microsoft.com/) . U kunt een e-mail bericht ontvangen wanneer een gebruiker toegang tot een toepassing heeft aangevraagd waarvoor de goed keuring is vereist. 

Deze goed keuringen ondersteunen alleen één goedkeurings werk stromen, wat betekent dat als u meerdere goed keurders opgeeft, één fiatteur de toegang tot de toepassing kan goed keuren.

## <a name="things-to-check-if-self-service-isnt-working"></a>Dingen om te controleren of selfservice werkt
-   Zorg ervoor dat de gebruiker of groep is ingeschakeld om self-service toepassings toegang aan te vragen.
-   Zorg ervoor dat de gebruiker de juiste locatie voor de toegang tot de selfservice toepassing bezoekt. gebruikers kunnen naar de pagina [mijn apps](https://myapps.microsoft.com/) navigeren en de knop **+ toevoegen** selecteren om te zoeken naar de apps waarvoor u selfservice toegang hebt ingeschakeld.
-   Als de toegang van selfservice toepassingen onlangs is geconfigureerd, probeert u zich na een paar minuten opnieuw aan te melden bij mijn apps van de gebruiker en te controleren of de wijzigingen in de self-service toegang zijn verschenen.

## <a name="next-steps"></a>Volgende stappen
[Azure Active Directory instellen voor groepsbeheer met self-service](../enterprise-users/groups-self-service-management.md)
