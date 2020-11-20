---
title: Metrische gegevens en waarschuwingen-Azure DNS
description: Met dit leer traject gaat u aan de slag met Azure DNS metrische gegevens en waarschuwingen.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: rohink
ms.openlocfilehash: 85b681cbeb1b06784529e321c9e907ab2bdecb73
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954644"
---
# <a name="azure-dns-metrics-and-alerts"></a>Metrische gegevens en waarschuwingen Azure DNS
Azure DNS is een hosting service voor DNS-domeinen die naam omzetting biedt met behulp van de Microsoft Azure-infra structuur. In dit artikel worden metrische gegevens en waarschuwingen voor de Azure DNS-service beschreven.

## <a name="azure-dns-metrics"></a>Azure DNS metrische gegevens

Azure DNS voorziet in metrische gegevens voor klanten om ze in staat te stellen specifieke aspecten te bewaken van de DNS-zones die worden gehost in de service. Daarnaast kunt u met Azure DNS metrische gegevens waarschuwingen configureren en ontvangen op basis van de voor waarden die van belang zijn. De metrische gegevens worden via de [Azure Monitor-service](../azure-monitor/index.yml)verschaft. Azure DNS biedt de volgende metrische gegevens via Azure Monitor voor uw DNS-zones:

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

U kunt ook de [definitie van deze metrische gegevens](../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) zien op de pagina Azure monitor documentatie.
>[!NOTE]
> Op dit moment zijn deze metrische gegevens alleen beschikbaar voor open bare DNS-zones die worden gehost in Azure DNS. Als u privé zones hebt die worden gehost in Azure DNS, worden deze metrische gegevens niet verstrekt voor deze zones. Daarnaast wordt de functie voor metrische gegevens en waarschuwingen alleen ondersteund in de open bare Azure-Cloud. De ondersteuning voor soevereine Clouds wordt op een later tijdstip gevolgd. 

Het meest gedetailleerde element waarvan u de metrische gegevens kunt zien, is een DNS-zone. U kunt op dit moment geen metrische gegevens weer geven voor afzonderlijke bron records in een zone.

### <a name="query-volume"></a>Query volume

In de metrische gegevens van het *query volume* in azure DNS wordt het volume DNS-query's (query verkeer) weer gegeven dat is ontvangen door Azure DNS voor uw DNS-zone. De meet eenheid is aantal en de aggregatie is het totaal van alle query's die gedurende een bepaalde periode zijn ontvangen. 

Als u deze metriek wilt weer geven, selecteert u op het tabblad Monitor in het Azure Portal metrische gegevens (preview) Verkenner. Selecteer de DNS-zone in de vervolg keuzelijst bron, selecteer het query volume metriek en selecteer som als aggregatie. Onder scherm afbeelding ziet u een voor beeld.  Zie [Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-charts.md)voor meer informatie over de metrics Explorere ervaring en grafieken.

![Query volume](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Afbeelding: metrische gegevens van het volume Azure DNS opvragen*

### <a name="record-set-count"></a>Aantal record sets
Met de metrische gegevens voor het *aantal records sets* wordt het aantal record sets in azure DNS voor uw DNS-zone weer gegeven. Alle record sets die in uw zone zijn gedefinieerd, worden geteld. De maat eenheid is aantal en de aggregatie is het maximum van alle record sets. Als u deze metriek wilt weer geven, selecteert u op het tabblad **monitor** in het Azure Portal **metrische gegevens (preview)** Verkenner. Selecteer de DNS-zone in de vervolg keuzelijst **bron** , selecteer de waarde voor het **aantal records sets** en selecteer vervolgens **Max** als **aggregatie**. Zie [Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-charts.md)voor meer informatie over de metrics Explorere ervaring en grafieken. 

![Aantal record sets](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Afbeelding: metrische gegevens over het aantal Azure DNS records instellen*


### <a name="record-set-capacity-utilization"></a>Capaciteits gebruik van record sets
De metrische gegevens voor het *capaciteits gebruik van de record sets* in azure DNS tonen het percentage van het gebruik van de capaciteit van de record sets voor een DNS-zone. Elke DNS-zone in Azure DNS is onderhevig aan een recordset-limiet die het maximum aantal record sets definieert dat is toegestaan voor de zone (Zie [DNS-limieten](dns-zones-records.md#limits)). In deze metrische gegevens ziet u dus hoe dicht u de limiet van de recordset kunt aangaan. Als u bijvoorbeeld 500-record sets hebt geconfigureerd voor uw DNS-zone en de zone de standaard limiet voor de recordset van 5000 heeft, wordt de waarde van de RecordSetCapacityUtilization weer gegeven als 10% (wordt verkregen door het delen van 500 door 5000). De meet eenheid is **percentage** en het **aggregatie** type is **maximum**. Als u deze metriek wilt weer geven, selecteert u op het tabblad Monitor in het Azure Portal metrische gegevens (preview) Verkenner. Selecteer de DNS-zone in de vervolg keuzelijst bron, selecteer de metrische gegevens voor het capaciteits gebruik van de record instellen en selecteer maximum als de aggregatie. Onder scherm afbeelding ziet u een voor beeld. Zie [Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-charts.md)voor meer informatie over de metrics Explorere ervaring en grafieken. 

![Scherm afbeelding van een voor beeld van het weer geven van metrische gegevens.](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Afbeelding: de metrische gegevens over het capaciteits gebruik Azure DNS record sets instellen*

## <a name="alerts-in-azure-dns"></a>Waarschuwingen in Azure DNS
Azure Monitor biedt de mogelijkheid om een waarschuwing te ontvangen voor de beschik bare meet waarden. De DNS-metrische gegevens zijn beschikbaar in de nieuwe waarschuwing configuratie-ervaring. Zoals beschreven in details in de [documentatie over Azure monitor waarschuwingen](../azure-monitor/platform/alerts-metric.md), kunt u DNS-zone als resource selecteren, het metrische signaal type kiezen en de waarschuwings logica en andere para meters, zoals **periode** en **frequentie**, configureren. U kunt een [actie groep](../azure-monitor/platform/action-groups.md) verder definiëren wanneer aan de voor waarde van de waarschuwing wordt voldaan, waardoor de waarschuwing wordt geleverd via de gekozen acties. Zie [waarschuwingen maken, weer geven en beheren met Azure monitor](../azure-monitor/platform/alerts-metric.md)voor meer informatie over het configureren van waarschuwingen voor Azure monitor metrische gegevens. 

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Azure DNS](dns-overview.md).