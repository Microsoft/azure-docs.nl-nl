---
title: Begrijpen hoe gebruikers worden toegewezen aan apps in Azure Active Directory
description: Krijg inzicht in hoe gebruikers worden toegewezen aan een app die Azure Active Directory voor identiteits beheer gebruikt.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: kenwith
ms.openlocfilehash: 161df0446c9478ca0f2b135c1e426f3786b164fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99257437"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>Begrijpen hoe gebruikers worden toegewezen aan apps in Azure Active Directory
Dit artikel helpt u te begrijpen hoe gebruikers aan een toepassing in uw Tenant worden toegewezen.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Hoe krijgen gebruikers toegewezen aan een toepassing in azure AD?
Een gebruiker kan alleen toegang krijgen tot een toepassing als deze eerst op een of andere manier aan het programma worden toegewezen. De toewijzing kan worden uitgevoerd door een beheerder, een bedrijfs gemachtigde of soms ook de gebruiker zelf. Hieronder worden de manieren beschreven waarop gebruikers kunnen worden toegewezen aan toepassingen:

*  Een beheerder [wijst een gebruiker](./assign-user-or-group-access-portal.md) rechtstreeks aan de toepassing toe
*  Een beheerder [wijst een groep toe](./assign-user-or-group-access-portal.md) die de gebruiker lid is van de toepassing, met inbegrip van:
    * Een groep die is gesynchroniseerd vanuit on-premises
    * Een statische beveiligings groep die in de Cloud is gemaakt
    * Een [dynamische beveiligings groep](../enterprise-users/groups-dynamic-membership.md) die in de Cloud is gemaakt
    * Een Microsoft 365 groep die in de Cloud is gemaakt
    * De groep [alle gebruikers](../fundamentals/active-directory-groups-create-azure-portal.md)
*  Een beheerder maakt [toegang van selfservice toepassingen](./manage-self-service-access.md) mogelijk om een gebruiker toe te staan een toepassing toe te voegen met [mijn apps](../user-help/my-apps-portal-end-user-access.md) **app** -functie toevoegen **zonder zakelijke goed keuring**
*  Een beheerder maakt [toegang van selfservice toepassingen](./manage-self-service-access.md) mogelijk om een gebruiker toe te staan een toepassing toe te voegen met behulp van [mijn apps](../user-help/my-apps-portal-end-user-access.md) **app** -functie toevoegen, maar alleen **met voorafgaande goed keuring vanuit een geselecteerde set van zakelijke goed keurders**
*  Een beheerder kan [self-service groeps beheer](../enterprise-users/groups-self-service-management.md) toestaan een gebruiker toe te voegen aan een groep waaraan een toepassing **zonder zakelijke goed keuring** is toegewezen.
*  Een beheerder kan [self-service groeps beheer](../enterprise-users/groups-self-service-management.md) toestaan om een gebruiker toe te voegen aan een groep waaraan een toepassing is toegewezen, maar alleen **met voorafgaande goed keuring vanuit een geselecteerde set van goed keurders van het bedrijf**
*  Een beheerder wijst rechtstreeks een licentie toe aan een gebruiker voor een toepassing in de eerste partij, zoals [Microsoft 365](https://products.office.com/)
*  Een beheerder wijst een licentie toe aan een groep waarvan de gebruiker lid is van een toepassing voor de eerste partij, zoals [Microsoft 365](https://products.office.com/)
*  Een [beheerder wordt gestemd op een toepassing](../develop/howto-convert-app-to-be-multi-tenant.md) die door alle gebruikers moet worden gebruikt en vervolgens meldt een gebruiker zich aan bij de toepassing
* Een gebruiker wordt door gegeven aan [een toepassing](../develop/howto-convert-app-to-be-multi-tenant.md) zelf door zich aan te melden bij de toepassing

## <a name="next-steps"></a>Volgende stappen
* [Quickstartreeks over toepassingsbeheer](view-applications-portal.md)
* [Wat is toepassingsbeheer?](what-is-application-management.md)
* [Wat is eenmalige aanmelding?](what-is-single-sign-on.md)