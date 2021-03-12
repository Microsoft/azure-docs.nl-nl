---
title: bestand opnemen
description: bestand opnemen
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: ee7ccb126eb04e168ccc213f0429d41a76a8bc2f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103021138"
---
**Volume gegevensverzameling en gegevensretentie** 

| Laag | Limiet per dag | Bewaartijd voor gegevens | Opmerking |
|:---|:---|:---|:---|
| Huidig Prijscategorie per GB<br>(geïntroduceerd in april 2018) | Geen limiet | 30 - 730 dagen | Gegevensretentie na meer dan 31 dagen is tegen extra kosten mogelijk. Meer informatie over Azure Monitor-prijzen. |
| Verouderd Gratis lagen<br>(geïntroduceerd in april 2016) | 500 MB | 7 dagen | Als uw werkruimte de limiet van 500 MB per dag bereikt, wordt de gegevensopname gestopt en aan het begin van de volgende dag hervat. Een dag is gebaseerd op UTC. Houd er rekening mee dat de gegevens die worden verzameld door Azure Security Center, niet zijn opgenomen in de limiet van 500 MB per dag en nog steeds worden verzameld als de limiet is overschreden.  |
| Verouderd Zelfstandig Laag per GB<br>(geïntroduceerd in april 2016) | Geen limiet | 30 tot 730 dagen | Gegevensretentie na meer dan 31 dagen is tegen extra kosten mogelijk. Meer informatie over Azure Monitor-prijzen. |
| Verouderd Per knooppunt (OMS)<br>(geïntroduceerd in april 2016) | Geen limiet | 30 tot 730 dagen | Gegevensretentie na meer dan 31 dagen is tegen extra kosten mogelijk. Meer informatie over Azure Monitor-prijzen. |
| Verouderd Standaardlaag | Geen limiet | 30 dagen  | Retentie kan niet worden aangepast |
| Verouderd Premiumlaag | Geen limiet | 365 dagen  | Retentie kan niet worden aangepast |

**Aantal werkruimten per abonnement.**

| Prijscategorie    | Werkruimtelimiet | Opmerkingen
|:---|:---|:---|
| Gratis laag  | 10 | Deze limiet kan niet worden verhoogd. |
| Alle andere servicelagen | Geen limiet | Er gelden limieten voor het aantal resources binnen een resourcegroep en voor het aantal resourcegroepen per abonnement. |

**Azure Portal**

| Categorie | Limiet | Opmerkingen |
|:---|:---|:---|
| Maximum aantal records geretourneerd door logboekquery | 10.000 | Verminder de hoeveelheid resultaten met behulp van het querybereik, het tijdsbereik en de filters in de query. |


**API gegevensverzamelaar**

| Categorie | Limiet | Opmerkingen |
|:---|:---|:---|
| Maximumgrootte voor een enkel bericht | 30 MB | Grotere volumes splitsen in meerdere berichten. |
| Maximumgrootte voor veldwaarden  | 32 kB | Velden die langer zijn dan 32 KB worden afgebroken. |

**Search API**

| Categorie | Limiet | Opmerkingen |
|:---|:---|:---|
| Maximum aantal records dat wordt geretourneerd in één query | 500.000 | |
| Maximale grootte van geretourneerde gegevens | 64.000.000 bytes (~ 61 MiB)| |
| Maximale uitvoeringstijd van query | 10 minuten | Zie [Time-outs](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) voor meer informatie.  |
| Maximale aanvraagsnelheid | 200 aanvragen per 30 seconden per IP-adres van Azure AD-gebruikers of client | Zie [Snelheidslimieten](https://dev.loganalytics.io/documentation/Using-the-API/Limits) voor meer informatie. |

**Connector voor Azure Monitor-logboeken**

| Categorie | Limiet | Opmerkingen |
|:---|:---|:---|
| Maximum aantal records | 500.000 | |
| Maximale querytime-out | 110 seconde | |
| Grafieken | | Visualisatie op de pagina logboeken en de connector gebruiken verschillende grafiek bibliotheken en sommige functies zijn momenteel niet beschikbaar in de connector. |

**Algemene limieten voor werkruimten**

| Categorie | Limiet | Opmerkingen |
|:---|:---|:---|
| Maximum aantal kolommen in een tabel         | 500 | |
| Maximum aantal tekens voor de kolomnaam | 500 | |

**<a name="data-ingestion-volume-rate">Gegevensopnamevolume</a>**

Azure Monitor is een grootschalige gegevensservice die elke maand een groeiend aantal terabytes aan gegevens van duizenden klanten verwerkt. De limiet voor het opnamevolume moet de klanten van Azure Monitor isoleren tegen plotselinge opnamepieken in een multitenancy-omgeving. Er is een standaarddrempel van 500 MB (gecomprimeerd) voor het opnamevolume gedefenieerd in werkruimtes. Dit staat gelijk aan een niet-gecomprimeerd volume van ongeveer **6 GB/min**. De werkelijke grootte kan per gegevenstype variëren afhankelijk van de logboeklengte en de compressieratio ervan. De limiet voor volumesnelheid is van toepassing op gegevens die worden opgenomen vanuit Azure-resource via [Diagnostische instellingen](../articles/azure-monitor/essentials/diagnostic-settings.md). Wanneer het volumelimiet is bereikt, probeert een mechanisme voor nieuwe pogingen de gegevens vier keer op te nemen in een periode van 30 minuten. Als dat niet lukt, mislukt de bewerking. De limiet is niet van toepassingen op gegevens van [agents](../articles/azure-monitor/agents/agents-overview.md) of de [Data Collector-API](../articles/azure-monitor/logs/data-collector-api.md).

Als naar een werkruimte verzonden gegevens een volumesnelheid heeft die hoger is dan 80 % van de drempel die in uw werkruimte is geconfigureerd, wordt er om de zes uur een gebeurtenis verzonden naar de *bewerkingstabel* in uw werkruimte, zolang de drempel nog steeds wordt overschreden. Als het opnamevolume hoger is dan de drempel, worden sommige gegevens verwijderd en wordt er om de zes uur een gebeurtenis verzonden naar de *bewerkingstabel* in uw werkruimte, zolang de drempel wordt overschreden. Als uw opnamevolume de drempel blijft overschrijden of als u verwacht de drempel binnenkort te bereiken, kunt u een verhoging aanvragen door een ondersteuningsaanvraag te openen. 

Zie [De status van de Log Analytics-werkruimte in Azure Monitor bewaken](../articles/azure-monitor/logs/monitor-workspace.md) voor het maken van waarschuwingsregels om proactief te worden gewaarschuwd wanneer u eventuele opnamelimieten bereikt.

>[!NOTE]
>Afhankelijk van hoe lang u logboekanalyse hebt gebruikt, hebt u mogelijk toegang tot verouderde prijscategorieën. Meer informatie over [verouderde prijscategorieën van logboekanalyse](../articles/azure-monitor/logs/manage-cost-storage.md#legacy-pricing-tiers).