---
title: Resource dashboards voor toegangs beoordelingen in PIM-Azure AD | Microsoft Docs
description: Hierin wordt beschreven hoe u een resource dashboard gebruikt voor het uitvoeren van een toegangs beoordeling in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e95eaa5b0e86a7470fc48edc23b2dbfb47e4b10c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84743725"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>Een resource dashboard gebruiken om een toegangs beoordeling uit te voeren in Privileged Identity Management

U kunt een resource dashboard gebruiken om een toegangs beoordeling uit te voeren in Privileged Identity Management (PIM). Het dash board van de beheer weergave in Azure Active Directory (Azure AD) heeft drie primaire onderdelen:

- Een grafische weer gave van de activering van een resource functie
- Grafieken waarin de distributie van roltoewijzingen per toewijzings type wordt weer gegeven
- Een gegevens gebied met informatie over nieuwe roltoewijzingen

![Scherm opname van het dash board van de beheer weergave, met grafieken en grafieken](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Scherm opname van het dash board van de beheer weergave, met gegevens lijsten](media/pim-resource-roles-overview-dashboards/role-settings.png)

De grafische weer gave van de functie activeringen van de resource behandelt de afgelopen zeven dagen. Deze gegevens zijn gericht op de geselecteerde resource en weer gave-activeringen voor de meest voorkomende rollen (eigenaar, Inzender, gebruikers toegangs beheer) en voor alle rollen gecombineerd.

Aan de ene kant van de activerings grafiek worden in twee grafieken de verdeling van roltoewijzingen per toewijzings type weer gegeven voor gebruikers en groepen. U kunt de waarde wijzigen in een percentage (of andersom) door een segment van de grafiek te selecteren.

Onder de grafieken ziet u het aantal gebruikers en groepen met nieuwe roltoewijzingen in de afgelopen 30 dagen, en rollen gesorteerd op totaal aantal toewijzingen in aflopende volg orde.

## <a name="next-steps"></a>Volgende stappen

- [Een toegangs beoordeling starten voor Azure-resource rollen in Privileged Identity Management](pim-resource-roles-start-access-review.md)
