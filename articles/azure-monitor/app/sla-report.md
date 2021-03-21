---
title: Downtime, SLA en werk stroom voor de werk stroom-Application Insights
description: SLA het voor webtests op en meld u aan met een enkel glas venster over uw Application Insights resources en Azure-abonnementen.
ms.topic: conceptual
ms.date: 02/8/2021
ms.openlocfilehash: d225627a27bffd9088956e5aee37ca543e528d4a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714048"
---
# <a name="downtime-sla-and-outages-workbook"></a>De werkmap downtime, SLA en uitval

Introductie van een eenvoudige manier om SLA (Service-Level Agreement) te berekenen en rapporteren voor webtests via één deel venster van glas over uw Application Insights resources en Azure-abonnementen. Het rapport downtime en uitval biedt krachtige vooraf gebouwde query's en gegevens visualisaties om uw ervaring met de connectiviteit van uw klant, een typische reactie tijd van de toepassing en een ervaren periode te verbeteren.

U hebt toegang tot de SLA-werkmap sjabloon via de werkmap galerie in uw Application Insights resource of het tabblad Beschik baarheid door **Sla-rapporten** bovenaan te selecteren.
:::image type="content" source="./media/sla-report/availability.png" alt-text="Scherm afbeelding van het tabblad Beschik baarheid met SLA-rapporten gemarkeerd." lightbox="./media/sla-report/availability.png":::

:::image type="content" source="./media/sla-report/workbook-gallery.png" alt-text="Scherm opname van de werkmap galerie met downtime en uitval van de werkmap gemarkeerd." lightbox ="./media/sla-report/workbook-gallery.png":::

## <a name="parameter-flexibility"></a>Parameter flexibiliteit

De para meters die in de werkmap zijn ingesteld, hebben invloed op de rest van uw rapport.

:::image type="content" source="./media/sla-report/outages.png" alt-text=" Scherm opname van de para meters voor onderbrekingen/onderhoud in de werk stroom downtime en uitval." lightbox ="./media/sla-report/outages.png":::

`Subscriptions`, `App Insights Resources` en `Web Test` de para meters bepalen uw resource opties op hoog niveau. Deze para meters zijn gebaseerd op Log Analytics-query's en worden gebruikt in elke rapport query.

`Failure Threshold` en `Outage Window` u kunt uw eigen criteria voor een service storing bepalen, bijvoorbeeld de criteria voor een waarschuwing voor de beschik baarheid van app Insights op basis van een mislukte locatie over een gekozen periode. De gemiddelde drempel waarde is drie locaties gedurende een periode van vijf minuten.

`Maintenance Period` Hiermee kunt u uw normale onderhouds frequentie selecteren en `Maintenance Window` is een datetime-selector voor een voor beeld van een onderhouds periode. Alle gegevens die plaatsvinden tijdens de geïdentificeerde periode, worden genegeerd in uw resultaten.

`Availability Target 9s` Hiermee geeft u de doel-9 's doel stelling van twee 9 's tot vijf 9 's.

## <a name="overview-page"></a>Overzichtspagina

De pagina overzicht bevat informatie op hoog niveau over uw totale SLA (exclusief onderhouds perioden, indien gedefinieerd), end-to-end-uitval instanties en uitval tijd van toepassingen. Uitval instanties worden gedefinieerd door wanneer een test wordt gestart, totdat deze is gelukt op basis van uw uitval parameters. Als een test wordt gestart om 8:00 uur en het opnieuw slaagt om 10:00 uur, wordt de hele periode van gegevens beschouwd als dezelfde storing.

:::image type="content" source="./media/sla-report/overview.gif" alt-text=" GIF van overzichts pagina waarin de overzichts tabel per test wordt weer gegeven." lightbox="./media/sla-report/overview.gif":::

U kunt ook de langste storing onderzoeken die zijn opgetreden tijdens de rapportage periode.

Sommige tests zijn gekoppeld terug naar hun Application Insights resource voor verdere onderzoek, maar dit is alleen mogelijk in de [Application Insights resource op basis van werk ruimte](create-workspace-resource.md).

## <a name="downtime-outages-and-failures"></a>Uitval tijd, storingen en fouten

Het tabblad storingen **en downtime** bevat informatie over het totale aantal malen dat de gegevens zijn gereduceerd en de totale uitval tijd, gesplitst per test. Het tabblad **fouten op locatie** bevat een geokaart van mislukte test locaties om mogelijke probleem verbindings gebieden te identificeren.

:::image type="content" source="./media/sla-report/outages-failures.gif" alt-text=" De GIF van het tabblad storingen en uitval per locatie in de werkmap downtime en uitval." lightbox="./media/sla-report/outages-failures.gif":::

## <a name="edit-the-report"></a>Het rapport bewerken

U kunt het rapport bewerken, net als andere [Azure monitor werkmap](../visualize/workbooks-overview.md). U kunt de query's of visualisaties aanpassen op basis van de behoeften van uw team.

:::image type="content" source="./media/sla-report/edit.gif" alt-text=" GIF van het selecteren van de knop bewerken om de visualisatie te wijzigen in een cirkel diagram." lightbox="./media/sla-report/edit.gif":::

### <a name="log-analytics"></a>Log Analytics

De query's kunnen allemaal worden uitgevoerd in [log Analytics](../logs/log-analytics-overview.md) en worden gebruikt in andere rapporten of Dash boards. Verwijder de parameter beperking en gebruik de kern query opnieuw.

:::image type="content" source="./media/sla-report/logs.gif" alt-text=" GIF-bestand van logboek query." lightbox="./media/sla-report/logs.gif":::

## <a name="access-and-sharing"></a>Toegang en delen

Het rapport kan worden gedeeld met uw teams, leiderschap of vastgemaakt aan een dash board voor verder gebruik. De gebruiker moet Lees machtigingen hebben voor toegang tot de resource van de sollicitatie Insights waarin de werkmap wordt opgeslagen.

:::image type="content" source="./media/sla-report/share.png" alt-text=" Scherm opname van deze sjabloon delen." lightbox= "./media/sla-report/share.png":::

## <a name="next-steps"></a>Volgende stappen

- [Tips voor het optimaliseren van query's log Analytics](../logs/query-optimization.md).
- Meer informatie over het [maken van een grafiek in werkmappen](../visualize/workbooks-chart-visualizations.md).
- Meer informatie over het bewaken van uw website met [beschikbaarheids tests](monitor-web-app-availability.md).