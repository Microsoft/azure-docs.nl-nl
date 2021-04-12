---
title: Bewaking en metrische gegevens configureren met behulp van Azure Monitor
titleSuffix: Azure Bastion
description: Meer informatie over Azure Bastion-bewaking en metrische gegevens met behulp van Azure Monitor, de oplossing voor metrische gegevens, waarschuwingen en Diagnostische logboeken in Azure.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/12/2021
ms.author: mialdrid
ms.openlocfilehash: c4e03318fae8d8d3a8b4d29538cad49f9ef39593
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259112"
---
# <a name="how-to-configure-monitoring-and-metrics-for-azure-bastion-using-azure-monitor"></a>Bewaking en metrische gegevens voor Azure Bastion configureren met behulp van Azure Monitor

Dit artikel helpt u bij het werken met bewaking en metrische gegevens voor Azure Bastion met behulp van Azure Monitor.

>[!NOTE]
>Het gebruik van **klassieke metrische gegevens** wordt niet aanbevolen.
>

## <a name="about-metrics"></a>Over metrische gegevens

Azure Bastion heeft diverse metrische gegevens die beschikbaar zijn. In de volgende tabel ziet u de categorie en dimensies voor elke beschik bare metriek.

|**Meting**|**Categorie**|**Dimensie (s)**|
| --- | --- | --- |
|Bastion-communicatie status * *|[Beschikbaarheid](#availability)|N.v.t.|
|Totaal geheugen|[Beschikbaarheid](#availability)|Exemplaar|
|Gebruikte CPU|[Verkeer](#traffic)|Exemplaar
|Gebruikt geheugen|[Verkeer](#traffic)|Exemplaar
|Aantal sessies|[Prestaties](#performance)|Exemplaar|

* * De Bastion-communicatie status is alleen van toepassing op Bastion-hosts die na november 2020 zijn geïmplementeerd.

### <a name="availability-metrics"></a><a name="availability"></a>Metrische beschikbaarheids gegevens

#### <a name="bastion-communication-status"></a><a name="communication-status"></a>Bastion-communicatie status

U kunt de communicatie status van Azure Bastion bekijken, geaggregeerd over alle exemplaren die de bastion-host vormen.

* Een waarde van **1** geeft aan dat de Bastion beschikbaar is.
* Een waarde van **0** geeft aan dat de Bastion-service niet beschikbaar is.

:::image type="content" source="./media/metrics-monitor-alert/communication-status.png" alt-text="Scherm opname met de communicatie status.":::

#### <a name="total-memory"></a><a name="total-memory"></a>Totaal geheugen

U kunt het totale geheugen van Azure Bastion bekijken, splitsen over elk Bastion-exemplaar.

:::image type="content" source="./media/metrics-monitor-alert/total-memory.png" alt-text="Scherm opname van het totale geheugen.":::

### <a name="traffic-metrics"></a><a name="traffic"></a>Metrische gegevens verkeer

#### <a name="used-cpu"></a><a name="used-cpu"></a>Gebruikte CPU

U kunt het CPU-gebruik van Azure Bastion bekijken, splitsen over elk Bastion-exemplaar. Door deze metrische gegevens te controleren, kunt u de beschik baarheid en de capaciteit van de exemplaren van Azure Bastion meten

:::image type="content" source="./media/metrics-monitor-alert/used-cpu.png" alt-text="Scherm opname van CPU-gebruik.":::

#### <a name="used-memory"></a><a name="used-memory"></a>Gebruikt geheugen

U kunt geheugen gebruik weer geven voor elk Bastion-exemplaar, splitsen over elke Bastion-instantie. Door deze metrische gegevens te bewaken, kunt u de beschik baarheid en capaciteit van de exemplaren van Azure Bastion meten.

:::image type="content" source="./media/metrics-monitor-alert/used-memory.png" alt-text="Scherm opname van het gebruikte geheugen.":::

### <a name="performance-metrics"></a><a name="performance"></a>Metrische prestatie gegevens

#### <a name="session-count"></a>Aantal sessies

U kunt het aantal actieve sessies per Bastion-exemplaar weer geven, geaggregeerd over elk sessie type (RDP en SSH). Elk Azure-Bastion kan een aantal actieve RDP-en SSH-sessies ondersteunen. Door deze metrische gegevens te controleren, kunt u zien of u het aantal exemplaren moet aanpassen dat de Bastion-service uitvoert. Raadpleeg de [Veelgestelde vragen over Azure Bastion](bastion-faq.md)voor meer informatie over het aantal sessies dat door Azure Bastion kan worden ondersteund.

De aanbevolen waarden voor deze metrische configuratie zijn:

* **Aggregatie:** Gemiddeld
* **Granulatie:** 5 of 15 minuten
* Splitsen op instanties wordt aanbevolen om een nauw keuriger aantal te bereiken

:::image type="content" source="./media/metrics-monitor-alert/session-count.png" alt-text="Scherm opname van aantal sessies.":::

## <a name="how-to-view-metrics"></a><a name="metrics"></a>Metrische gegevens weer geven

1. Als u metrische gegevens wilt weer geven, gaat u naar de bastion-host.
1. Selecteer **metrische gegevens** in de lijst **bewaking** .
1. Selecteer de parameters. Als er geen metrische gegevens zijn ingesteld, klikt u op **metrische gegevens toevoegen** en selecteert u vervolgens de para meters.

   * **Bereik:** De scope is standaard ingesteld op de bastion-host.
   * **Metrische naam ruimte:** Standaard metrische gegevens.
   * **Metriek:** Selecteer de metrische gegevens die u wilt weer geven.

1. Zodra een metriek is geselecteerd, wordt de standaard aggregatie toegepast. U kunt eventueel splitsen Toep assen, waardoor de metriek wordt weer gegeven met verschillende dimensies.

## <a name="next-steps"></a>Volgende stappen

Lees de [Veelgestelde vragen over Bastion](bastion-faq.md).
  
