---
title: Inzichten verkrijgen met behulp van Back-upcentrum
description: Meer informatie over het analyseren van historische trends en het verkrijgen van meer inzicht in uw back-ups met Back-upcentrum.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: c5f707ce3bc8d1172805ac14958a50bf59d1412d
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98893690"
---
# <a name="obtain-insights-using-backup-center-preview"></a>Inzichten verkrijgen met behulp van Back-upcentrum (preview-versie)

Voor het analyseren van historische trends en het verkrijgen van diepere inzichten op uw back-ups biedt Back-upcentrum een interface voor het [maken van back-uprapporten (preview)](configure-reports.md), waarin [Azure monitor-logboeken](../azure-monitor/platform/data-platform-logs.md) en [Azure-werkmappen](../azure-monitor/platform/workbooks-overview.md)worden gebruikt. Back-uprapporten bieden de volgende mogelijkheden:

- Het toewijzen en voors pellen van verbruikte Cloud opslag.

- Controle van back-ups en herstel bewerkingen.

- De belangrijkste trends op verschillende niveaus nauw keurig te identificeren.

- Krijg inzicht in de mogelijkheden van de kosten optimalisatie voor uw back-ups.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

- Back-uprapporten (preview) zijn momenteel niet beschikbaar voor back-up van Azure Database for PostgreSQL server.

- Raadpleeg de [ondersteunings matrix](backup-center-support-matrix.md) voor een gedetailleerde lijst met ondersteunde en niet-ondersteunde scenario's.

## <a name="get-started"></a>Aan de slag

### <a name="configure-your-vaults-to-send-data-to-a-log-analytics-workspace"></a>Uw kluizen configureren voor het verzenden van gegevens naar een Log Analytics-werk ruimte

[Meer informatie over het configureren van diagnostische instellingen op schaal voor uw kluizen](./configure-reports.md#get-started)

### <a name="view-backup-reports-in-the-backup-center-portal"></a>Back-uprapporten weer geven in de portal van het Back-upcentrum

Als u het menu **-item back-uprapporten** selecteert in Back-upcentrum, worden de rapporten geopend. Kies een of meer Log Analytics werk ruimten om de belangrijkste informatie over uw back-ups weer te geven en te analyseren.

![Back-uprapporten in Back-upcentrum](./media/backup-center-obtain-insights/backup-center-backup-reports.png)

Hieronder ziet u de beschik bare weer gaven:

1. **Samen vatting** : gebruik dit tabblad om een overzicht te krijgen van uw back-ups op hoog niveau. [Meer informatie](./configure-reports.md#summary)

1. **Back-** upitems: gebruik dit tabblad om informatie en trends in Cloud opslag te bekijken die worden gebruikt op een back-upitemniveau. [Meer informatie](./configure-reports.md#backup-items)

1. **Gebruik** : gebruik dit tabblad om de para meters voor de sleutel facturering voor uw back-ups weer te geven. [Meer informatie](./configure-reports.md#usage)

1. **Taken** : gebruik dit tabblad om langlopende trends voor taken weer te geven, zoals het aantal mislukte taken per dag en de belangrijkste oorzaken van een mislukte taak. [Meer informatie](./configure-reports.md#jobs)

1. **Beleid** : gebruik dit tabblad om informatie weer te geven over al uw actieve beleids regels, zoals het aantal gekoppelde items en de totale Cloud opslag die wordt gebruikt door items waarvan een back-up is gemaakt onder een bepaald beleid. [Meer informatie](./configure-reports.md#policies)

1. **Optimaliseer** : gebruik dit tabblad om inzicht te krijgen in potentiële mogelijkheden voor kosten optimalisatie voor uw back-ups. [Meer informatie](./configure-reports.md#optimize)

## <a name="next-steps"></a>Volgende stappen

- [Back-ups controleren en uitvoeren](backup-center-monitor-operate.md)
- [Uw back-up maken](backup-center-govern-environment.md)
- [Acties uitvoeren met behulp van Back-upcentrum](backup-center-actions.md)