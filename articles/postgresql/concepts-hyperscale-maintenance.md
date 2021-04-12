---
title: Gepland onderhoud-Azure Database for PostgreSQL-grootschalige (Citus)
description: In dit artikel wordt de functie voor gepland onderhoud in Azure Database for PostgreSQL-grootschalige (Citus) beschreven.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: dee7257a296f523dbc9040d65fe68c14edf928f6
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106782"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Gepland onderhoud in Azure Database for PostgreSQL – grootschalige (Citus)

Azure Database for PostgreSQL-grootschalige (Citus) doet periodiek onderhoud om uw beheerde data base veilig, stabiel en bijgewerkt te houden.  Tijdens het onderhoud krijgen alle knoop punten in de Server groep nieuwe functies, updates en patches.

De belangrijkste functies van gepland onderhoud voor grootschalige (Citus) zijn:

* Updates worden gelijktijdig toegepast op alle knoop punten in de Server groep
* Meldingen over aanstaande onderhoud worden Azure Service Health vijf dagen vooraf geplaatst
* Doorgaans zijn er ten minste 30 dagen tussen geslaagde onderhouds gebeurtenissen voor een server groep
* De voorkeurs dag van het week-en tijd venster binnen die dag voor het starten van onderhoud kan afzonderlijk worden gedefinieerd voor elke server groep

## <a name="selecting-a-maintenance-window-and-notification-about-upcoming-maintenance"></a>Een onderhouds venster en melding over aanstaande onderhoud selecteren

U kunt onderhoud plannen gedurende een specifieke dag van de week en een tijd venster binnen die dag. U kunt ook instellen dat het systeem een dag en een tijd venster automatisch voor u kiest. In beide gevallen waarschuwt het systeem u vijf dagen voordat er onderhoud wordt uitgevoerd. Het systeem laat u ook weten wanneer het onderhoud wordt gestart en wanneer het is voltooid.

Meldingen over gepland onderhoud worden naar Azure Service Health gepost en kunnen zijn:

* Verzonden naar een specifiek adres
* E-mail verzonden naar een Azure Resource Manager rol
* Verzonden in een tekst bericht (SMS) naar mobiele apparaten
* Gepusht als een melding naar een Azure-app
* Bezorgd als een voicemail-bericht

Wanneer u voorkeuren voor het onderhoudsschema opgeeft, kunt u een dag van de week en een tijdvenster kiezen. Als u niet opgeeft, zal het systeem tijden kiezen tussen 23:00 uur en 7am in de regio tijd van uw server groep. U kunt verschillende schema's definiëren voor elke grootschalige-Server groep (Citus) in uw Azure-abonnement.

> [!IMPORTANT]
> Normaal gesp roken zijn er ten minste 30 dagen tussen geslaagde geplande onderhouds gebeurtenissen voor een server groep.
>
> In het geval van een essentiële nood-update, zoals een ernstig beveiligingsprobleem, kan het meldingsvenster echter korter zijn dan vijf dagen. De essentiële update kan worden toegepast op uw server, zelfs als in de afgelopen 30 dagen geslaagd gepland onderhoud is uitgevoerd.

U kunt de plannings instellingen op elk gewenst moment bijwerken. Als er onderhoud is gepland voor uw grootschalige-Server groep (Citus) en u het schema bijwerkt, worden bestaande gebeurtenissen gewoon uitgevoerd zoals gepland. De gewijzigde instellingen worden van kracht nadat bestaande gebeurtenissen zijn voltooid.

Als het onderhoud mislukt of wordt geannuleerd, maakt het systeem een melding.
Er wordt opnieuw geprobeerd onderhoud uit te proberen volgens de huidige plannings instellingen en u krijgt vijf dagen voor de volgende onderhouds gebeurtenis op de hoogte.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het wijzigen van de onderhouds planning](howto-hyperscale-maintenance.md)
* Meer informatie [over het verkrijgen van meldingen over aanstaande onderhouds werkzaamheden](../service-health/service-notifications.md) met Azure service Health
* Meer informatie [over het instellen van waarschuwingen over aanstaande geplande onderhouds gebeurtenissen](../service-health/resource-health-alert-monitor-guide.md)
