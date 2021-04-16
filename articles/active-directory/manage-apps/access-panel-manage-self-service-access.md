---
title: Selfservice voor toegang tot toepassingen gebruiken in Azure AD
description: Selfservice inschakelen zodat gebruikers apps kunnen vinden in Azure AD
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: japere,asteen
ms.openlocfilehash: 8e3851a702da46d07634a4141c774275845fa44d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377612"
---
# <a name="how-to-use-self-service-application-access"></a>Selfservice voor toegang tot toepassingen gebruiken

Voordat uw gebruikers zelf toepassingen kunnen ontdekken op hun Mijn apps-pagina, moet u **selfservicetoepassingstoegang** inschakelen tot toepassingen die u wilt toestaan dat gebruikers zelf ontdekken en toegang aanvragen.

Deze functie is een uitstekende manier om tijd en geld te besparen als IT-groep en wordt ten zeerste aanbevolen als onderdeel van een implementatie van moderne toepassingen met Azure Active Directory.

Zie help voor Mijn apps portal voor meer informatie over het gebruik [Mijn apps het perspectief van eindgebruikers.](../user-help/my-apps-portal-end-user-access.md)

Met deze functie kunt u het volgende doen:

-   Gebruikers zelf toepassingen laten ontdekken van [Mijn apps](https://myapps.microsoft.com/) it-groep.
-   Voeg deze gebruikers toe aan een vooraf geconfigureerde groep, zodat u kunt zien wie toegang heeft aangevraagd, toegang heeft verwijderd en de rollen kunt beheren die aan hen zijn toegewezen.
-   U kunt optioneel iemand toestaan toegangsaanvragen voor apps goed te keuren, zodat de IT-groep dit niet hoeft te doen.
-   Configureer desgewenst maximaal 10 personen die de toegang tot deze toepassing goedkeuren.
-   Optioneel kan iemand de wachtwoorden instellen die gebruikers kunnen gebruiken om zich aan te melden bij de toepassing.
-   Wijs optioneel automatisch selfservice toegewezen gebruikers rechtstreeks toe aan een toepassingsrol.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Selfservicetoepassingstoegang inschakelen zodat gebruikers hun eigen toepassingen kunnen vinden

Selfservice voor toegang tot toepassingen is een uitstekende manier om gebruikers toe te staan toepassingen zelf te ontdekken. Optioneel kan de bedrijfsgroep de toegang tot deze toepassingen goedkeuren. U kunt toestaan dat de bedrijfsgroep de referenties die zijn toegewezen aan die gebruikers voor Wachtwoord Single-Sign op toepassingen, direct vanaf hun Mijn apps beheren.

Als u selfservicetoepassingstoegang tot een toepassing wilt inschakelen, volgt u de onderstaande stappen:
1. Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als **globale beheerder.**
2. Open de **Azure Active Directory door** Alle **services boven** aan het hoofdnavigatiemenu aan de linkerkant te selecteren.
3. Typ **'Azure Active Directory'** in het filterzoekvak en selecteer het **Azure Active Directory** item.
4. Selecteer **Bedrijfstoepassingen** in het linkernavigatiemenu van Azure Active Directory.
5. Selecteer **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.
   * Als u de toepassing die u hier wilt zien niet ziet,  gebruikt u het **besturingselement Filter** bovenaan de lijst Alle toepassingen en stelt u de **optie** Tonen in op **Alle toepassingen.**
6. Selecteer in de lijst de toepassing waarmee u selfservicetoegang wilt inschakelen.
7. Nadat de toepassing is geladen, **selecteert u Selfservice** in het navigatiemenu aan de linkerkant van de toepassing.
8. Als u selfservicetoepassingstoegang voor deze toepassing wilt inschakelen, zet u de schakelknop Gebruikers toestaan om toegang tot deze toepassing aan te **vragen?** op **Ja.**
9. Als u vervolgens de groep wilt selecteren waaraan gebruikers die toegang tot deze toepassing aanvragen, moet worden toegevoegd, selecteert u de selector naast het label Waaraan de groep gebruikers moet worden toegevoegd? en selecteert u **een groep.**
10. **Optioneel:** Als u een bedrijfsgoedkeuring wilt vereisen voordat gebruikers toegang krijgen, stelt u de schakelknop Goedkeuring vereisen voordat u toegang verleent tot **deze toepassing?** in op **Ja.**
11. **Optioneel:** als u wilt toestaan dat deze zakelijke goedkeurders de wachtwoorden opgeven die naar deze toepassing worden  verzonden voor goedgekeurde gebruikers, stelt u De goedkeurders toestaan om wachtwoorden van gebruikers in te stellen voor deze toepassing in op Ja voor toepassingen.
12. **Optioneel:** Geef de zakelijke goedkeurders op die toegang tot deze app mogen goedkeuren. Selecteer **Wie mag toegang tot deze toepassing goedkeuren?** Selecteer vervolgens maximaal 10 afzonderlijke zakelijke goedkeurders.
    * Groepen worden niet ondersteund.
13. **Optioneel:** voor toepassingen die rollen beschikbaar **stellen,** als u selfservice goedgekeurde gebruikers aan een rol wilt toewijzen, selecteert u de selector naast de rol Waaraan moeten gebruikers worden toegewezen **in deze toepassing?** om de rol te selecteren waaraan deze gebruikers moeten worden toegewezen.
14. Selecteer de **knop** Opslaan bovenaan om te voltooien.

Nadat u de configuratie van de selfservicetoepassing hebt voltooid, kunnen gebruikers naar [Mijn apps](https://myapps.microsoft.com/) navigeren en de knop **+Toevoegen** selecteren om de apps te vinden waarvoor u selfservicetoegang hebt ingeschakeld. Zakelijke goedkeurders zien ook een melding op hun [Mijn apps](https://myapps.microsoft.com/) pagina. U kunt een e-mail met een melding inschakelen wanneer een gebruiker toegang heeft aangevraagd tot een toepassing die goedkeuring vereist. 

Deze goedkeuringen ondersteunen alleen enkele goedkeuringswerkstromen, wat betekent dat als u meerdere goedkeurders opgeeft, één goedkeurder de toegang tot de toepassing kan goedkeuren.

## <a name="things-to-check-if-self-service-isnt-working"></a>Dingen om te controleren of selfservice niet werkt
-   Zorg ervoor dat de gebruiker of groep is ingeschakeld om toegang tot selfservice-toepassingen aan te vragen.
-   Zorg ervoor dat de gebruiker de juiste plaats bezoekt voor toegang tot selfservice-toepassingen. gebruikers kunnen naar hun [Mijn apps](https://myapps.microsoft.com/) navigeren en de knop **+Toevoegen** selecteren om de apps te vinden waarvoor u selfservicetoegang hebt ingeschakeld.
-   Als de selfservice voor toegang tot toepassingen onlangs is geconfigureerd, probeert u zich na een paar minuten opnieuw aan te melden en weer af te melden bij de Mijn apps van de gebruiker om te zien of de selfservice-toegangswijzigingen zijn verschenen.

## <a name="next-steps"></a>Volgende stappen
[Azure Active Directory instellen voor groepsbeheer met self-service](../enterprise-users/groups-self-service-management.md)
