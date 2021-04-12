---
title: Azure Database for PostgreSQL-grootschalige (Citus)-gepland onderhoud-Azure Portal
description: Meer informatie over het configureren van geplande onderhouds instellingen voor een Azure Database for PostgreSQL-grootschalige (Citus) van de Azure Portal.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 04dd37e3e9abbfbec7badb036802e645cc7732b0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108371"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-postgresql--hyperscale-citus"></a>Geplande onderhouds instellingen beheren voor Azure Database for PostgreSQL – grootschalige (Citus)

U kunt onderhouds opties opgeven voor elke grootschalige-Server groep (Citus) in uw Azure-abonnement. Opties zijn onder andere de onderhouds planning en meldings instellingen voor toekomstige en voltooide onderhouds gebeurtenissen.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

- Een [Citus-Server groep (Azure database for PostgreSQL-grootschalige)](quickstart-create-hyperscale-portal.md)

## <a name="specify-maintenance-schedule-options"></a>Opties voor onderhouds planning opgeven

1. Kies op de pagina grootschalige (Citus) Server groep onder de kop **instellingen** de optie **onderhoud** om opties voor gepland onderhoud te openen.
2. Het standaard schema (door systeem beheerd) is een wille keurige dag van de week en een periode van 30 minuten voor het starten van het onderhoud tussen de [Azure-regio tijd](https://go.microsoft.com/fwlink/?linkid=2143646)van de 23:00 uur-en 7am-Server groep. Als u dit schema wilt aanpassen, kiest u **aangepast schema**. U kunt vervolgens een voorkeurs dag van de week en een venster van 30 minuten voor de start tijd van het onderhoud selecteren.

## <a name="notifications-about-scheduled-maintenance-events"></a>Meldingen over geplande onderhouds gebeurtenissen

U kunt Azure Service Health gebruiken om [meldingen](../service-health/service-notifications.md) over aanstaande en eerder gepland onderhoud te bekijken op de Server groep van uw grootschalige (Citus). U kunt ook waarschuwingen [instellen](../service-health/resource-health-alert-monitor-guide.md) in azure service Health om meldingen over onderhouds gebeurtenissen te ontvangen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [gepland onderhoud in azure database for PostgreSQL – grootschalige (Citus)](concepts-hyperscale-maintenance.md)
* [Azure service Health](../service-health/overview.md) van Lean
