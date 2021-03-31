---
title: Beveiligings waarschuwingen configureren voor Azure-resource rollen in Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Meer informatie over het configureren van beveiligings waarschuwingen voor Azure-resource rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bfca096eb49ee9f1807935de1dac49151cc8ac3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84743759"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Beveiligings waarschuwingen configureren voor Azure-resource rollen in Privileged Identity Management

Privileged Identity Management (PIM) genereert waarschuwingen wanneer er verdachte of onveilige activiteit is in uw Azure Active Directory-organisatie (Azure AD). Wanneer een waarschuwing wordt geactiveerd, wordt deze weer gegeven op de pagina waarschuwingen.

![Azure-resources-waarschuwingen pagina vermelding, risico niveau en aantal](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Waarschuwingen weergeven

Selecteer een waarschuwing om een rapport weer te geven met een lijst met de gebruikers of rollen die de waarschuwing hebben geactiveerd, samen met richt lijnen voor herstel.

![Waarschuwings rapport met de laatste scan tijd, beschrijving, stappen voor het beperken, het type, de ernst, de beveiliging en het voor komen van de volgende keer](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Waarschuwingen

| Waarschuwing | Severity | Trigger | Aanbeveling |
| --- | --- | --- | --- |
| **Te veel eigen aren toegewezen aan een resource** |Normaal |Te veel gebruikers hebben de rol van eigenaar. |Controleer de gebruikers in de lijst en wijs een aantal toe aan minder privilegede rollen. |
| **Er zijn te veel permanente eigen aren toegewezen aan een resource** |Normaal |Te veel gebruikers zijn permanent toegewezen aan een rol. |Controleer de gebruikers in de lijst en wijs een aantal opnieuw toe om te activeren voor het gebruik van rollen. |
| **Dubbele rol gemaakt** |Normaal |Meerdere rollen hebben hetzelfde criterium. |Gebruik slechts één van deze rollen. |

### <a name="severity"></a>Severity

- **Hoog**: vereist direct actie vanwege een beleids schending. 
- **Medium**: vereist geen onmiddellijke actie, maar signaleert een mogelijke beleids schending.
- **Low**: vereist geen onmiddellijke actie, maar stelt een voorkeurs wijziging van het beleid voor.

## <a name="configure-security-alert-settings"></a>Instellingen voor beveiligings waarschuwingen configureren

Ga op de pagina waarschuwingen naar **instellingen**.

![Pagina waarschuwingen met gemarkeerde instellingen](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Pas de instellingen voor de verschillende waarschuwingen aan om te werken met uw omgeving en beveiligings doelen.

![Pagina instellen voor een waarschuwing om instellingen in te scha kelen en te configureren](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Volgende stappen

- [Instellingen voor Azure-resource-rollen configureren in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
