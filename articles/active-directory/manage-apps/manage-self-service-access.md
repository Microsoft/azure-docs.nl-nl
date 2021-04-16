---
title: Selfservice voor toepassingstoewijzing configureren | Microsoft Docs
description: Selfservicetoepassingstoegang inschakelen zodat gebruikers hun eigen toepassingen kunnen vinden
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29c3043cc38c8ab3d3387b171ea6f3793d485730
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373958"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Selfservicetoepassingstoewijzing configureren

Voordat uw gebruikers zelf toepassingen van hun Mijn apps kunnen ontdekken, moet u **selfservicetoepassingstoegang** inschakelen tot toepassingen die u gebruikers zelf wilt laten ontdekken en toegang aanvragen. Deze functionaliteit is beschikbaar voor toepassingen die zijn toegevoegd vanuit de [Azure AD-galerie,](./add-application-portal.md) [Azure AD toepassingsproxy](./application-proxy.md) of zijn toegevoegd via toestemming van de gebruiker of [beheerder.](../develop/application-consent-experience.md) 

Deze functie is een uitstekende manier om tijd en geld te besparen als IT-groep en wordt ten zeerste aanbevolen als onderdeel van een implementatie van moderne toepassingen met Azure Active Directory.

Met deze functie kunt u het volgende doen:

-   Laat gebruikers zelf toepassingen van de Mijn apps [zonder](https://myapps.microsoft.com/) dat de IT-groep hier last van heeft.

-   Voeg deze gebruikers toe aan een vooraf geconfigureerde groep, zodat u kunt zien wie toegang heeft aangevraagd, toegang heeft verwijderd en de rollen kunt beheren die aan hen zijn toegewezen.

-   Optioneel toestaan dat een zakelijke goedkeurder aanvragen voor toegang tot toepassingen goedkeurt, zodat de IT-groep dit niet hoeft te doen.

-   Configureer desgewenst maximaal 10 personen die toegang tot deze toepassing goedkeuren.

-   Optioneel toestaan dat een zakelijke goedkeurder de wachtwoorden in stelt die gebruikers kunnen gebruiken om zich aan te melden bij de toepassing, direct vanuit de Mijn apps [.](https://myapps.microsoft.com/)

-   Wijs optioneel automatisch selfservice toegewezen gebruikers rechtstreeks toe aan een toepassingsrol.

> [!NOTE]
> Er Azure Active Directory Premium een P1- of P2-licentie vereist voor gebruikers om aan te vragen om lid te worden van een selfservice-app en voor eigenaren om aanvragen goed te keuren of te weigeren. Zonder een Azure Active Directory Premium kunnen gebruikers geen selfservice-apps toevoegen.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Selfservicetoepassingstoegang inschakelen zodat gebruikers hun eigen toepassingen kunnen vinden

Selfservice voor toegang tot toepassingen is een uitstekende manier om gebruikers toe te staan toepassingen zelf te ontdekken en optioneel de bedrijfsgroep de toegang tot deze toepassingen te laten goedkeuren. Voor toepassingen met een wachtwoord voor een Mijn apps kunt u de bedrijfsgroep ook toestaan om de referenties te beheren die aan deze gebruikers zijn toegewezen.

Als u selfservicetoepassingstoegang tot een toepassing wilt inschakelen, volgt u de onderstaande stappen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) globale beheerder.

2. Selecteer **Azure Active Directory**. Selecteer Bedrijfstoepassingen in het navigatiemenu **aan de linkerkant.**

3. Selecteer de toepassing in de lijst. Als u de toepassing niet ziet, typt u de naam ervan in het zoekvak. Of gebruik de filterbesturingselementen om het toepassingstype, de status of zichtbaarheid te selecteren en selecteer vervolgens **Toepassen.**

4. Selecteer selfservice in het navigatiemenu **aan de linkerkant.**

5. Als u selfservicetoepassingstoegang voor deze toepassing wilt inschakelen, zet u de schakelknop Gebruikers toestaan om toegang tot deze toepassing aan te **vragen?** op **Ja.**

6. Klik naast **Aan welke groep moeten gebruikers worden toegevoegd?** op Groep **selecteren.** Kies een groep en klik vervolgens op **Selecteren.** Wanneer de aanvraag van een gebruiker is goedgekeurd, wordt deze toegevoegd aan deze groep. Wanneer u het lidmaatschap van deze groep bekijkt, kunt u zien wie toegang tot de toepassing heeft gekregen via selfservicetoegang.
  
    > [!NOTE]
    > Deze instelling biedt geen ondersteuning voor groepen die on-premises zijn gesynchroniseerd.

7. **Optioneel:** Als u bedrijfsgoedkeuring wilt vereisen voordat gebruikers toegang krijgen, stelt u de schakelknop Goedkeuring vereisen voordat u toegang verleent tot **deze toepassing?** in op **Ja.**

8. **Optioneel:** als u wilt dat zakelijke goedkeurders de wachtwoorden kunnen opgeven die naar deze toepassing  worden verzonden voor goedgekeurde gebruikers, stelt u Goedkeurders toestaan om wachtwoorden van gebruikers in te stellen voor deze toepassing in op Ja voor toepassingen.

9. **Optioneel:** Als u de zakelijke goedkeurders wilt opgeven die toegang tot deze toepassing mogen goedkeuren, klikt u naast Wie mag toegang tot deze toepassing **goedkeuren?** op Goedkeurders selecteren en selecteert u vervolgens maximaal 10 afzonderlijke zakelijke goedkeurders. Klik vervolgens op **Selecteren**.

    >[!NOTE]
    >Groepen worden niet ondersteund. U kunt maximaal 10 afzonderlijke zakelijke goedkeurders selecteren. Als u meerdere goedkeurders opgeeft, kan één goedkeurder een toegangsaanvraag goedkeuren.

10. **Optioneel:** voor toepassingen die rollen beschikbaar **maken,** klikt u op Rol selecteren om selfservice goedgekeurde gebruikers toe te wijzen aan een rol, naast de rol Waaraan moeten gebruikers worden toegewezen **in deze toepassing?**, klikt u op Rol selecteren en kiest u vervolgens de rol waaraan deze gebruikers moeten worden toegewezen. Klik vervolgens op **Selecteren**.

11. Klik op **de** knop Opslaan bovenaan het deelvenster om het te voltooien.

Nadat u de configuratie van de selfservicetoepassing hebt voltooid, kunnen gebruikers naar hun [Mijn apps](https://myapps.microsoft.com/) navigeren en op de knop **Selfservice-apps** toevoegen klikken om de apps te vinden die zijn ingeschakeld met selfservicetoegang. Zakelijke goedkeurders zien ook een melding in [hun Mijn apps.](https://myapps.microsoft.com/) U kunt een e-mail met een melding inschakelen wanneer een gebruiker toegang heeft aangevraagd tot een toepassing die goedkeuring vereist.

## <a name="next-steps"></a>Volgende stappen
[Azure Active Directory instellen voor groepsbeheer met self-service](../enterprise-users/groups-self-service-management.md)