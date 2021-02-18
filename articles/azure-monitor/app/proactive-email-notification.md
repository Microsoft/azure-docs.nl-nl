---
title: Wijziging van detectie melding van Smart-Azure-toepassing inzichten
description: Wijzig de standaard ontvangers voor meldingen van Slimme detectie. Met Slimme detectie kunt u toepassings traceringen bewaken met Azure-toepassing Insights voor ongebruikelijke patronen in de telemetrie Trace.
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 03/13/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0bf3a95814aadf687efcc27294b760e14f7d45d8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585640"
---
# <a name="smart-detection-e-mail-notification-change"></a>Wijziging e-mail melding voor slimme detectie

Op basis van feedback van klanten worden op 1 april 2019 de standaard rollen gewijzigd die e-mail meldingen ontvangen van Slimme detectie.

## <a name="what-is-changing"></a>Wat wordt er gewijzigd?

Op dit moment worden er e-mail meldingen voor slimme detectie standaard verzonden naar de rol van het _abonnement_, de _mede_ werkers van het abonnement en de functie voor _abonnements lezers_ . Deze rollen omvatten vaak gebruikers die niet actief betrokken zijn bij de bewaking, waardoor veel van deze gebruikers onnodig meldingen ontvangen. Om deze ervaring te verbeteren, worden er een wijziging aangebracht zodat e-mail meldingen alleen de [bewakings lezer controleren](../../role-based-access-control/built-in-roles.md#monitoring-reader) en [Inzender rollen controleren](../../role-based-access-control/built-in-roles.md#monitoring-contributor) .

## <a name="scope-of-this-change"></a>Bereik van deze wijziging

Deze wijziging is van invloed op alle slimme detectie regels, met uitzonde ring van de volgende:

* Slimme detectie regels zijn gemarkeerd als preview. Deze slimme detectie regels ondersteunen vandaag nog geen e-mail meldingen.

* Regel voor afwijkingen van de fout. Deze regel begint met het richten op de nieuwe standaard rollen, zodra deze zijn gemigreerd van een klassieke waarschuwing naar het Unified Alerts-platform (meer informatie is [hier](../alerts/monitoring-classic-retirement.md)beschikbaar.)

## <a name="how-to-prepare-for-this-change"></a>Hoe bereidt u zich voor op deze wijziging?

Om ervoor te zorgen dat e-mail meldingen van Slimme detectie naar relevante gebruikers worden verzonden, moeten die gebruikers worden toegewezen aan de [bewakings lezer](../../role-based-access-control/built-in-roles.md#monitoring-reader) of [Inzender](../../role-based-access-control/built-in-roles.md#monitoring-contributor) rollen van het abonnement.

Volg de stappen die worden beschreven in het artikel [Azure roles toewijzen](../../role-based-access-control/role-assignments-portal.md) om gebruikers toe te wijzen aan de bewakings lezer of Inzender rollen te bewaken via de Azure Portal. Zorg ervoor dat u de _bewakings lezer_ of het _bewaken_ van de Inzender selecteert als de rol waaraan gebruikers worden toegewezen.

> [!NOTE]
> Specifieke ontvangers van Slimme detectie meldingen, geconfigureerd met de optie _extra e-mail ontvangers_ in de regel instellingen, worden niet beïnvloed door deze wijziging. Deze ontvangers blijven de e-mail meldingen ontvangen.

Als u vragen hebt of problemen hebt met betrekking tot deze wijziging, kunt u het [beste contact met ons](mailto:smart-alert-feedback@microsoft.com)opnemen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over slimme detectie:

- [Foutafwijkingen](./proactive-failure-diagnostics.md)
- [Geheugen lekken](./proactive-potential-memory-leak.md)
- [Prestatieafwijkingen](./proactive-performance-diagnostics.md)

