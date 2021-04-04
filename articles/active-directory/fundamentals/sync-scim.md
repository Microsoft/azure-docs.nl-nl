---
title: SCIM synchronisatie met Azure Active Directory
description: Architectuur richtlijnen voor het bereiken van SCIM-synchronisatie met Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b95aac504bc6ee72c353faecad25384e2dc90840
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172415"
---
# <a name="scim-synchronization-with-azure-active-directory"></a>SCIM synchronisatie met Azure Active Directory

Het systeem voor Identity Management (SCIM) tussen domeinen is een open standaard protocol voor het automatiseren van de uitwisseling van gebruikers identiteits gegevens tussen identiteits domeinen en IT-systemen. SCIM zorgt ervoor dat werk nemers die zijn toegevoegd aan het HCM-systeem (Human Capital Management) automatisch accounts hebben die zijn gemaakt in Azure Active Directory (Azure AD) of Windows Server Active Directory. Gebruikers kenmerken en-profielen worden gesynchroniseerd tussen de twee systemen, waarbij het verwijderen van gebruikers wordt bijgewerkt op basis van de gebruikers status of de wijziging van de rol.

SCIM is een gestandaardiseerde definitie van twee eind punten: een/users-eind punt en een/groups-eind punt. Het gebruikt veelgebruikte REST-bewerkingen om objecten te maken, bij te werken en te verwijderen. Er wordt ook een vooraf gedefinieerd schema gebruikt voor algemene kenmerken zoals groeps naam, gebruikers naam, voor naam, achternaam en e-mail adres. Toepassingen die een SCIM 2,0-REST API bieden, kunnen de pijn van het werken met eigen gebruikers beheer-Api's of-producten beperken of elimineren. Zo kan elke SCIM-compatibele client een HTTP POST van een JSON-object naar het/users-eind punt maken om een nieuwe gebruikers vermelding te maken. Apps die voldoen aan de SCIM-standaard kunnen direct profiteren van bestaande clients, hulpprogramma's en code, in plaats van dat ze een iets andere API voor dezelfde basisacties moeten gebruiken. 

## <a name="use-when"></a>Gebruiken wanneer: 

U wilt gebruikers gegevens automatisch inrichten van een HCM-systeem naar Azure AD en Windows Server Active Directory en vervolgens, indien nodig, naar doel systemen. 

![architectuur diagram](./media/authentication-patterns/scim-auth.png)


## <a name="components-of-system"></a>Onderdelen van systeem 

* **HCM System**: toepassingen en technologieën waarmee HR-processen kunnen worden ondersteund en geautomatiseerd gedurende de levens cyclus van werk nemers. 

* **Azure AD-inrichtings service**: maakt gebruik van het scim 2,0-protocol voor automatische inrichting. De service maakt verbinding met het SCIM-eind punt voor de toepassing en maakt gebruik van het schema voor de SCIM-gebruikers objecten en REST-Api's voor het automatiseren van het inrichten en het ongedaan maken van de inrichting van gebruikers en groepen.  

* **Azure AD**: opslag plaats voor gebruikers die wordt gebruikt voor het beheren van de levens cyclus van identiteiten en hun rechten. 

* **Doel systeem**: toepassing of systeem met scim-eind punt en werkt met de Azure AD-inrichting om automatische inrichting van gebruikers en groepen in te scha kelen.  

## <a name="implement-scim-with-azure-ad"></a>SCIM implementeren met Azure AD 

* [Hoe het inrichten werkt in azure AD ](../app-provisioning/how-provisioning-works.md)

* [Het inrichten van gebruikers accounts voor zakelijke apps beheren in de Azure Portal ](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Een SCIM-eind punt bouwen en gebruikers inrichten met Azure AD configureren  ](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Naleving van SCIM 2,0-protocol van de Azure AD-inrichtings service](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md)