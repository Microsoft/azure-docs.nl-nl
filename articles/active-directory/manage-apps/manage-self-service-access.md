---
title: Toewijzing van self-service toepassingen configureren | Microsoft Docs
description: De toegang van self-service toepassingen inschakelen om gebruikers toe te staan hun eigen toepassingen te vinden
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
ms.topic: how-to
ms.date: 04/20/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9742a21cf00733607237c0eaf548f96b434abb33
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260211"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Toewijzing van self-service toepassingen configureren

Voordat uw gebruikers zelf toepassingen van hun apps kunnen detecteren, moet u de **toegang van selfservice toepassingen** inschakelen voor alle toepassingen waarvoor u gebruikers de mogelijkheid wilt bieden om zelf toegang te krijgen tot en om deze te vragen. Deze functionaliteit is beschikbaar voor toepassingen die zijn toegevoegd vanuit de [Azure AD-galerie](./add-application-portal.md), [Azure AD-toepassingsproxy](./application-proxy.md) of die zijn toegevoegd via toestemming van de [gebruiker of beheerder](../develop/application-consent-experience.md). 

Deze functie is een uitstekende manier om tijd en geld te besparen als een IT-groep en wordt ten zeerste aanbevolen als onderdeel van een moderne implementatie van toepassingen met Azure Active Directory.

Met deze functie kunt u het volgende doen:

-   Laat gebruikers zelf toepassingen van [mijn apps](https://myapps.microsoft.com/) detecteren zonder de IT-groep te hoeven verkennen.

-   Voeg deze gebruikers toe aan een vooraf geconfigureerde groep, zodat u kunt zien wie toegang heeft aangevraagd, de toegang moet verwijderen en de functies die eraan zijn toegewezen, moeten beheren.

-   Een zakelijke goed keurder toestaan om toegangs aanvragen voor toepassingen goed te keuren zodat de IT-groep deze niet hoeft te gebruiken.

-   U kunt Maxi maal 10 personen configureren die de toegang tot deze toepassing goed keuren.

-   Zo kunt u een zakelijke goed keurder toestaan om de wacht woorden in te stellen die gebruikers kunnen gebruiken om zich aan te melden bij de toepassing, rechtstreeks vanuit de [mijn apps](https://myapps.microsoft.com/)van de zakelijke goed keurder.

-   Automatisch toegewezen Self-Service gebruikers aan een toepassingsrol rechtstreeks toewijzen.

> [!NOTE]
> Een Azure Active Directory Premium-licentie (P1 of P2) is vereist voor gebruikers om lid te worden van een self-service-app en voor eigen aars om aanvragen goed te keuren of te weigeren. Zonder een Azure Active Directory Premium licentie kunnen gebruikers geen self-service apps toevoegen.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>De toegang van self-service toepassingen inschakelen om gebruikers toe te staan hun eigen toepassingen te vinden

De toegang van selfservice toepassingen is een uitstekende manier om gebruikers de mogelijkheid te bieden om zelf toepassingen te detecteren en de bedrijfs groep toe te staan om de toegang tot deze toepassingen goed te keuren. Als u een wacht woord wilt gebruiken voor eenmalige aanmelding, kunt u de bedrijfs groep ook toestaan om de referenties te beheren die aan deze gebruikers zijn toegewezen vanuit hun eigen toegangs Vensters voor mijn apps.

Volg de onderstaande stappen om de toegang van selfservice toepassingen voor een toepassing in te scha kelen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder.

2. Selecteer **Azure Active Directory**. Selecteer in het navigatie menu links de optie **bedrijfs toepassingen**.

3. Selecteer de toepassing in de lijst. Als u de toepassing niet ziet, begint u met het invoeren van de naam in het zoekvak. Of gebruik de filter besturings elementen om het toepassings type, de status of de zicht baarheid te selecteren en selecteer vervolgens **Toep assen**.

4. Selecteer **self-service** in het navigatie menu links.

5. Als u toegang voor selfservice toepassingen voor deze toepassing wilt inschakelen, schakelt u de optie **gebruikers toestaan om toegang te vragen tot deze toepassing?** in op **Ja.**

6. Klik op **groep selecteren** naast de **groep waaraan u gebruikers wilt toevoegen**. Kies een groep en klik vervolgens op **selecteren**. Wanneer de aanvraag van een gebruiker wordt goedgekeurd, worden deze toegevoegd aan deze groep. Wanneer u het lidmaatschap van deze groep bekijkt, kunt u zien wie toegang tot de toepassing heeft gekregen via self-service toegang.
  
    > [!NOTE]
    > Deze instelling biedt geen ondersteuning voor groepen die zijn gesynchroniseerd vanuit on-premises.

7. **Optioneel:** Als u een zakelijke goed keuring wilt vereisen voordat gebruikers toegang krijgen, **stelt u de** optie **goed keuring vereisen alvorens toegang tot deze toepassing verlenen** in.

8. **Optioneel: voor toepassingen die alleen gebruikmaken van een wacht woord voor eenmalige aanmelding,** zodat goed keurders van het bedrijf de wacht woorden kunnen opgeven die worden verzonden naar deze toepassing voor goedgekeurde gebruikers, stelt u de optie **goed keurders toestaan om wacht woorden van gebruikers in te stellen voor deze toepassing?** in te scha kelen naar **Ja**.

9. **Optioneel:** Als u de zakelijke goed keurders wilt opgeven die de toegang tot **deze toepassing mogen** goed keuren, klikt u op **fiatteurs selecteren** en selecteert u Maxi maal 10 individuele goed keurders van het bedrijf. Klik vervolgens op **Selecteren**.

    >[!NOTE]
    >Groepen worden niet ondersteund. U kunt Maxi maal 10 afzonderlijke goed keurders voor bedrijven selecteren. Als u meerdere goed keurders opgeeft, kan één fiatteur een toegangs aanvraag goed keuren.

10. **Optioneel:** **voor toepassingen die rollen weer** geven, om selfservice goedgekeurde gebruikers toe te wijzen aan een rol, klikt u naast de rol **waaraan gebruikers moeten worden toegewezen in deze toepassing?** op **rol selecteren** en kiest u vervolgens de rol waaraan deze gebruikers moeten worden toegewezen. Klik vervolgens op **Selecteren**.

11. Klik boven aan het deel venster op de knop **Opslaan** om de bewerking te volt ooien.

Zodra u de configuratie van de selfservice toepassing hebt voltooid, kunnen gebruikers naar hun [apps](https://myapps.microsoft.com/) navigeren en op de knop **self-service apps toevoegen** klikken om de apps te vinden die geschikt zijn voor selfservice toegang. Zakelijke goed keurders zien ook een melding in hun [apps](https://myapps.microsoft.com/). U kunt een e-mail bericht ontvangen wanneer een gebruiker toegang tot een toepassing heeft aangevraagd waarvoor de goed keuring is vereist.

## <a name="next-steps"></a>Volgende stappen
[Azure Active Directory instellen voor groepsbeheer met self-service](../enterprise-users/groups-self-service-management.md)