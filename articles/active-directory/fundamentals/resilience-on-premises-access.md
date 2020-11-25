---
title: Tolerantie in toepassings toegang bouwen met toepassings proxy
description: Een hand leiding voor architecten en IT-beheerders over het gebruik van toepassings proxy voor robuuste toegang tot on-premises toepassingen
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8bfc3fb239f30911eddf0aa27496a465e36c486
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919567"
---
# <a name="build-resilience-in-application-access-with-application-proxy"></a>Tolerantie in toepassings toegang bouwen met toepassings proxy

Toepassings proxy is een functie van Azure AD waarmee gebruikers toegang kunnen krijgen tot on-premises webtoepassingen vanaf een externe client. Toepassings proxy bevat zowel de service toepassings proxy in de Cloud als de connectors van de toepassings proxy, die worden uitgevoerd op een on-premises server. 

Gebruikers hebben toegang tot on-premises resources via een URL die is gepubliceerd via een toepassings proxy. Ze worden omgeleid naar de aanmeldings pagina van Azure AD. De Application proxy-service in azure AD stuurt vervolgens een token naar de connector voor toepassings proxy in het bedrijfs netwerk, waarmee het token wordt door gegeven aan de on-premises Active Directory de geverifieerde gebruiker toegang tot de on-premises resource kan krijgen. In het onderstaande diagram worden [connectors](../manage-apps/application-proxy-connectors.md) weer gegeven in een [connector groep](../manage-apps/application-proxy-connector-groups.md).

> [!IMPORTANT]
> Wanneer u uw toepassingen publiceert via toepassings proxy, moet u [de capaciteits planning en de juiste redundantie voor de Application proxy-connectors](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning)implementeren.

![Architectuur diagram van toepassings y](./media/resilience-on-prem-access/admin-resilience-app-proxy.png))

## <a name="how-do-i-implement-application-proxy"></a>Hoe kan ik toepassings proxy implementeren?

Raadpleeg de volgende bronnen voor het implementeren van externe toegang met Azure AD-toepassingsproxy.

* [Een implementatie van een toepassingsproxy plannen](../manage-apps/application-proxy-deployment-plan.md)

* [Best practices voor hoge Beschik baarheid en taak verdeling](../manage-apps/application-proxy-high-availability-load-balancing.md)

* [Proxy servers configureren](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

* [Een robuuste strategie voor toegangs beheer ontwerpen](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>Volgende stappen
Flexibiliteits bronnen voor beheerders en architecten
 
* [Tolerantie bouwen met referentie beheer](resilience-in-credentials.md)

* [Een tolerantie bouwen met de status van het apparaat](resilience-with-device-states.md)

* [Een tolerantie bouwen met behulp van voortdurende toegang (CAE)](resilience-with-continuous-access-evaluation.md)

* [Flexibiliteit in externe gebruikers verificatie maken](resilience-b2b-authentication.md)

* [Flexibiliteit in uw hybride verificatie maken](resilience-in-hybrid.md)

Flexibiliteits bronnen voor ontwikkel aars

* [IAM-tolerantie bouwen in uw toepassingen](resilience-app-development-overview.md)

* [Maak flexibiliteit in uw CIAM-systemen](resilience-b2c.md)
