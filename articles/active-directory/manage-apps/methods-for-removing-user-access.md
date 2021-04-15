---
title: De toegang van een gebruiker tot een toepassing in een Azure Active Directory
description: Meer informatie over het verwijderen van de toegang van een gebruiker tot een toepassing in Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2020
ms.author: iangithinji
ms.openlocfilehash: 958abc5f9be443d66037a6d9fe8d8779e6e37e0e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379584"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>De toegang van een gebruiker tot een toepassing verwijderen

Dit artikel helpt u te begrijpen hoe u de toegang van een gebruiker tot een toepassing verwijdert.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Ik wil de toewijzing van een specifieke gebruiker of groep aan een toepassing verwijderen

Als u een gebruikers- of groepstoewijzing aan een toepassing wilt verwijderen, volgt u de stappen in het artikel Een gebruikers- of groepstoewijzing verwijderen uit een [bedrijfs-app in Azure Active Directory.](./assign-user-or-group-access-portal.md)

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Ik wil alle toegang tot een toepassing uitschakelen voor elke gebruiker

Als u alle aanmeldingen van gebruikers bij een toepassing wilt uitschakelen, volgt u de stappen in het artikel Aanmeldingen van gebruikers uitschakelen voor een [bedrijfs-app in Azure Active Directory](./disable-user-sign-in-portal.md) toepassing.

## <a name="i-want-to-delete-an-application-entirely"></a>Ik wil een toepassing volledig verwijderen

De [Quickstart-reeks over toepassingsbeheer](delete-application-portal.md) bevat richtlijnen voor het verwijderen van een toepassing uit Azure Active Directory tenant.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Ik wil alle toekomstige toestemmingsbewerkingen voor gebruikers uitschakelen voor elke toepassing

Als u toestemming van de gebruiker voor uw hele directory uit te keurt, kunnen eindgebruikers geen toestemming geven voor een toepassing. Beheerders kunnen namens de gebruiker nog steeds toestemming geven. Lees Understanding user and admin consent (Toestemming van gebruiker en beheerder begrijpen) voor meer informatie over toepassings toestemming en waarom u dit wel of niet [wilt doen.](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent) Zie ook [Machtigingen en toestemming.](../develop/v2-permissions-and-consent.md)

Volg **deze instructies om alle toekomstige gebruikersbewerkingen voor toestemming in uw hele directory** uit te schakelen:

1.  Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory extensie** 

3.  Klik **op Bedrijfstoepassingen** in het navigatiemenu.

5.  Klik **op Gebruikersinstellingen.**

6.  Stel de **schakelknop Gebruikers kunnen apps** namens hen toegang geven tot bedrijfsgegevens in op Nee **en** klik op de knop Opslaan.


## <a name="next-steps"></a>Volgende stappen

[Toegang tot apps beheren](what-is-access-management.md)