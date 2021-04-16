---
title: Begrijpen hoe gebruikers worden toegewezen aan apps in Azure Active Directory
description: Begrijpen hoe gebruikers worden toegewezen aan een app die gebruik Azure Active Directory voor identiteitsbeheer.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: iangithinji
ms.openlocfilehash: 84700bca6ff306dbcce01a837c312c4c0c90066d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376406"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>Begrijpen hoe gebruikers worden toegewezen aan apps in Azure Active Directory
Dit artikel helpt u te begrijpen hoe gebruikers worden toegewezen aan een toepassing in uw tenant.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Hoe worden gebruikers toegewezen aan een toepassing in Azure AD?
Een gebruiker kan alleen toegang krijgen tot een toepassing als deze eerst op een of andere manier aan de toepassing wordt toegewezen. Toewijzing kan worden uitgevoerd door een beheerder, een zakelijke gemachtigde of soms de gebruiker zelf. Hieronder worden de manieren beschreven waarop gebruikers kunnen worden toegewezen aan toepassingen:

*  Een beheerder [wijst een gebruiker rechtstreeks](./assign-user-or-group-access-portal.md) toe aan de toepassing
*  Een beheerder [wijst een groep toe](./assign-user-or-group-access-portal.md) waarvan de gebruiker lid is, met inbegrip van:
    * Een groep die on-premises is gesynchroniseerd
    * Een statische beveiligingsgroep die is gemaakt in de cloud
    * Een [dynamische beveiligingsgroep die](../enterprise-users/groups-dynamic-membership.md) is gemaakt in de cloud
    * Een Microsoft 365 die in de cloud is gemaakt
    * De [groep Alle](../fundamentals/active-directory-groups-create-azure-portal.md) gebruikers
*  Een beheerder schakelt [selfservicetoepassingstoegang](./manage-self-service-access.md) in om een gebruiker toe te staan een toepassing toe te voegen met behulp [van Mijn apps](../user-help/my-apps-portal-end-user-access.md) **functie App** toevoegen zonder goedkeuring van **het bedrijf**
*  Een beheerder schakelt [selfservicetoepassingstoegang](./manage-self-service-access.md) in om een gebruiker toe te staan een toepassing toe te voegen met behulp van [de functie Mijn apps](../user-help/my-apps-portal-end-user-access.md) **App** toevoegen, maar alleen met voorafgaande goedkeuring van een geselecteerde set zakelijke **goedkeurders**
*  Een beheerder schakelt [selfservicegroepsbeheer in](../enterprise-users/groups-self-service-management.md) om een gebruiker toe te staan lid te worden van een groep aan een toepassing die is toegewezen **zonder goedkeuring van het bedrijf**
*  Een beheerder stelt [Selfservicegroepsbeheer](../enterprise-users/groups-self-service-management.md) in staat om een gebruiker toe te staan lid te worden van een groep aan wie een toepassing is toegewezen, maar alleen met voorafgaande goedkeuring van een geselecteerde set zakelijke **goedkeurders**
*  Een beheerder wijst een licentie rechtstreeks toe aan een gebruiker voor een eigen toepassing, [zoals Microsoft 365](https://products.office.com/)
*  Een beheerder wijst een licentie toe aan een groep waar de gebruiker lid van is bij een eigen toepassing, [zoals Microsoft 365](https://products.office.com/)
*  Een [beheerder geeft toestemming voor een toepassing](../develop/howto-convert-app-to-be-multi-tenant.md) die door alle gebruikers moet worden gebruikt en vervolgens meldt een gebruiker zich aan bij de toepassing
* Een gebruiker [geeft zelf toestemming voor een toepassing](../develop/howto-convert-app-to-be-multi-tenant.md) door zich aan te melden bij de toepassing

## <a name="next-steps"></a>Volgende stappen
* [Quickstartreeks over toepassingsbeheer](view-applications-portal.md)
* [Wat is toepassingsbeheer?](what-is-application-management.md)
* [Wat is een aanmelding?](what-is-single-sign-on.md)