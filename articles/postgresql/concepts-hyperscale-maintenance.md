---
title: Gepland onderhoud-Azure Database for PostgreSQL-grootschalige (Citus)
description: In dit artikel wordt de functie voor gepland onderhoud in Azure Database for PostgreSQL-grootschalige (Citus) beschreven.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 0d11ec8815cc0f5082f95c5331321f043e86eece
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027567"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Gepland onderhoud in Azure Database for PostgreSQL – grootschalige (Citus)

Azure Database for PostgreSQL-grootschalige (Citus) doet periodiek onderhoud om uw beheerde data base veilig, stabiel en bijgewerkt te houden.  Tijdens het onderhoud krijgen alle knoop punten in de Server groep nieuwe functies, updates en patches.

De belangrijkste functies van gepland onderhoud voor grootschalige (Citus) zijn:

* Updates worden gelijktijdig toegepast op alle knoop punten in de Server groep
* Meldingen over aanstaande onderhoud worden Azure Service Health vijf dagen vooraf geplaatst
* Doorgaans zijn er ten minste 30 dagen tussen geslaagde onderhouds gebeurtenissen voor een server groep

## <a name="notification-about-upcoming-maintenance"></a>Melding over aanstaande onderhouds werkzaamheden

Meldingen over gepland onderhoud worden naar Azure Service Health gepost en kunnen zijn:

* Verzonden naar een specifiek adres
* E-mail verzonden naar een Azure Resource Manager rol
* Verzonden in een tekst bericht (SMS) naar mobiele apparaten
* Gepusht als een melding naar een Azure-app
* Bezorgd als een voicemail-bericht

> [!IMPORTANT]
> Normaal gesp roken zijn er ten minste 30 dagen tussen geslaagde geplande onderhouds gebeurtenissen voor een server groep.
>
> In het geval van een essentiële nood-update, zoals een ernstig beveiligingsprobleem, kan het meldingsvenster echter korter zijn dan vijf dagen. De essentiële update kan worden toegepast op uw server, zelfs als in de afgelopen 30 dagen geslaagd gepland onderhoud is uitgevoerd.

Als het onderhoud mislukt of wordt geannuleerd, maakt het systeem een melding.
Er wordt opnieuw geprobeerd onderhoud uit te proberen volgens de huidige plannings instellingen en u krijgt vijf dagen voor de volgende onderhouds gebeurtenis op de hoogte.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over het verkrijgen van meldingen over aanstaande onderhouds werkzaamheden](../service-health/service-notifications.md) met Azure service Health
* Meer informatie [over het instellen van waarschuwingen over aanstaande geplande onderhouds gebeurtenissen](../service-health/resource-health-alert-monitor-guide.md)
