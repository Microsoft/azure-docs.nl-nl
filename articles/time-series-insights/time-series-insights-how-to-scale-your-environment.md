---
title: Uw omgeving schalen-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over het schalen van uw Azure Time Series Insights-omgeving met behulp van de Azure Portal.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 0dadf523c5d17ffb91f4fefa71b52d1d1855c978
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91570223"
---
# <a name="how-to-scale-your-azure-time-series-insights-gen1-environment"></a>Uw Azure Time Series Insights gen1-omgeving schalen

> [!CAUTION]
> Dit is een Gen1-artikel.

In dit artikel wordt beschreven hoe u de capaciteit van uw Azure Time Series Insights omgeving kunt wijzigen met behulp van de [Azure Portal](https://portal.azure.com). Capaciteit is de vermenigvuldiger die wordt toegepast op de ingangs frequentie, opslag capaciteit en kosten die zijn gekoppeld aan uw geselecteerde SKU.

U kunt de Azure Portal gebruiken om de capaciteit binnen een bepaalde prijs-SKU te verg Roten of te verkleinen.

Het wijzigen van de SKU van de prijs categorie is echter niet toegestaan. Een omgeving met een mijn prijs-SKU voor S1 kan bijvoorbeeld niet worden geconverteerd naar een S2 of andersom.

## <a name="ga-limits"></a>GA-limieten

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>De capaciteit van uw omgeving wijzigen

1. Zoek en selecteer uw Azure Time Series Insights omgeving in het Azure Portal.

1. Selecteer **opslag configuratie** in het menu voor uw Azure time series Insights omgeving.

   [![Uw Azure Time Series Insights capaciteit configureren](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. Pas de schuif regelaar **capaciteit** aan om de capaciteit te selecteren die voldoet aan de vereisten voor uw ingangs tarieven en opslag capaciteit. Let op de **ingangs rente**, **opslag capaciteit** en **geschatte kosten** update dynamisch om de impact van de wijziging weer te geven.

   [![Configureer uw omgeving met behulp van de schuif regelaar capaciteit](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   U kunt ook het nummer van de capaciteits-multiplier in het tekstvak rechts van de schuif regelaar typen.

1. Selecteer **Opslaan** om de omgeving te schalen. De voortgangs indicator wordt weer gegeven totdat de wijziging wordt doorgevoerd.

1. Controleer of de nieuwe capaciteit [voldoende is om beperking te voor komen](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Volgende stappen

- Lees voor meer informatie [uitleg over retentie in azure time series Insights](time-series-insights-concepts-retention.md).

- Meer informatie over het [configureren van gegevens retentie in Azure Azure time series Insights](time-series-insights-how-to-configure-retention.md).

- Meer informatie over [het plannen van uw omgeving](time-series-insights-environment-planning.md).
