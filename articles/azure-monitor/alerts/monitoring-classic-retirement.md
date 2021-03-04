---
title: Update van klassieke waarschuwingen & bewaking in Azure Monitor
description: Beschrijving van het buiten gebruik stellen van klassieke bewakings Services en-functionaliteit, eerder weer gegeven in Azure Portal onder waarschuwingen (klassiek).
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
ms.openlocfilehash: bfa92a2fc58d479edd328dba9bf02d57ec66c0c9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041090"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Unified Alerting-& bewaking in Azure Monitor vervangt klassieke waarschuwingen & bewaking

Azure Monitor is een uniforme bewakings stack die ondersteuning biedt voor één metrieke en één waarschuwing in azure-resources. Meer informatie vindt u in dit [blog bericht](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). De nieuwe Azure-bewakings-en waarschuwings platformen zijn gebouwd om sneller, slimmer en uitbreidbaar te zijn, met de groeiende Expanse van Cloud Computing en online met micro soft intelligent Cloud filosofie.

Wanneer het nieuwe Azure-bewakings-en waarschuwings platform aanwezig is, worden klassieke waarschuwingen in Azure Monitor buiten gebruik gesteld voor open bare Cloud gebruikers, maar in beperkte mate in tot **31 mei 2021**. Klassieke waarschuwingen voor Azure Government Cloud en Azure China 21Vianet worden op **29 februari 2024** buiten gebruik gesteld.

 ![Klassieke waarschuwing in Azure Portal](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

We raden u aan om aan de slag te gaan en uw waarschuwingen opnieuw te maken op het nieuwe platform.

> [!IMPORTANT]
> Klassieke waarschuwings regels die zijn gemaakt in het activiteiten logboek, worden niet gedeprecieerd of gemigreerd. Alle klassieke waarschuwings regels die zijn gemaakt in het activiteiten logboek, zijn toegankelijk en worden gebruikt door de nieuwe Azure Monitor-waarschuwingen. Zie [waarschuwingen voor activiteiten logboek maken, weer geven en beheren met Azure monitor](./alerts-activity-log.md)voor meer informatie. Waarschuwingen op Service Health zijn ook toegankelijk en kunnen worden gebruikt als-afkomstig uit de sectie nieuw Service Health. Zie [waarschuwingen over service status meldingen](../../service-health/alerts-activity-log-service-notifications-portal.md)voor meer informatie.

## <a name="unified-metrics-and-alerts-for-azure-resources"></a>Uniforme metrische gegevens en waarschuwingen voor Azure-resources

In maart 2018 hebben we de volgende generatie waarschuwingen uitgebracht voor Azure-resources. Het nieuwere meet platform en waarschuwingen worden sneller uitgevoerd en biedt meer granulatie met behulp van dimensies. Met dimensies kunt u de combi natie van specifieke waarden, voor waarde of bewerking segmenteren en filteren. De nieuwere metrische waarschuwingen gebruiken actie groepen om meer meldingen en acties voor automatisering toe te staan. Zie voor meer informatie over het [beheren van metrische waarschuwingen met behulp van Azure monitor](./alerts-metric.md).

Nieuwe metrische gegevens voor Azure-resources zijn beschikbaar als:

- **Azure monitor standaard platform metrieken** : Dit biedt populaire vooraf gevulde metrische gegevens van verschillende Azure-Services en-producten. Zie dit artikel over [ondersteunde metrische gegevens op Azure monitor](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported) en [ondersteuning voor metrische waarschuwingen op Azure monitor](./alerts-metric-overview.md#supported-resource-types-for-metric-alerts)voor meer informatie.
- **Azure monitor aangepaste metrische gegevens** , die metrische gegevens van door de gebruiker gestuurde bronnen bevat, waaronder de Azure Diagnostics-agent. Zie dit artikel over [aangepaste metrische gegevens in azure monitor](../essentials/metrics-custom-overview.md)voor meer informatie. Met aangepaste metrische gegevens kunt u ook metrische gegevens die zijn verzameld door [Windows Azure Diagnostics agent](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md) en [InfluxData-telegrafe-agent](../essentials/collect-custom-metrics-linux-telegraf.md), publiceren.

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Buiten gebruik stellen van klassiek bewakings-en waarschuwings platform

Zoals eerder aangegeven, zijn oudere klassieke bewakings-en waarschuwings functies buiten gebruik gesteld voor open bare Cloud gebruikers. De oplossing omvat de sluiting van gerelateerde Api's, Azure Portal-interface en services, maar is nog steeds in beperkt gebruik tot en met **31 mei 2021**. Klassieke waarschuwingen voor Azure Government Cloud en Azure China 21Vianet worden op **29 februari 2024** buiten gebruik gesteld.

Het pensioen bereik is met name bedoeld voor de klassieke metrische gegevens die momenteel beschikbaar zijn in het [gedeelte waarschuwingen (klassiek)](./alerts-classic.overview.md) van Azure Portal en toegankelijk zijn als [micro soft. Insights/alertrules](/rest/api/monitor/alertrules) resources.

Dit betekent:

- De klassieke bewakings-en waarschuwings service wordt buiten gebruik gesteld en is niet meer beschikbaar voor het maken van nieuwe waarschuwings regels.
- Alle waarschuwings regels die blijven bestaan in waarschuwingen (klassiek) blijven worden uitgevoerd en meldingen worden gestart.
- De meeste klassieke waarschuwings regels worden gemigreerd. Het proces is naadloos zonder uitval tijd en klanten hebben geen verlies van bewakings dekking.
- Geactiveerde meldingen bevatten een nieuwe Payload-structuur. Doel moet worden aangepast om met de nieuwe structuur te werken.
- Sommige [klassieke waarschuwings regels die niet automatisch kunnen worden gemigreerd](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts) en waarvoor gebruikers hand matige actie moeten ondernemen, moeten ze blijven uitvoeren.

> [!IMPORTANT]
> Azure Monitor heeft een [hulp programma geïmplementeerd om](alerts-using-migration-tool.md) de klassieke waarschuwings regels vrijwillig te migreren naar het nieuwe platform. De resterende regels worden automatisch gemigreerd zodra de service buiten gebruik wordt gesteld. Klanten moeten ervoor zorgen dat de nettolading van de klassieke waarschuwings regel wordt gebruikt voor het verwerken van de nieuwe payload van [uniforme metrische gegevens en waarschuwingen voor andere Azure-resources](#unified-metrics-and-alerts-for-azure-resources), na de migratie van de klassieke waarschuwings regels. Zie voor meer informatie [voor bereiding voor de migratie van de klassieke waarschuwings regel](alerts-prepare-migration.md).

## <a name="pricing-for-migrated-alert-rules"></a>Prijzen voor gemigreerde waarschuwings regels

We implementeren een hulp programma voor migratie om u te helpen bij het migreren van uw Azure Monitor [klassieke waarschuwingen](./alerts-classic.overview.md) naar de nieuwe waarschuwings ervaring. De gemigreerde waarschuwings regels en de bijbehorende gemigreerde actie groepen (e-mail, webhook of LogicApp) blijven kosteloos. De functionaliteit die u had met klassieke waarschuwingen met inbegrip van de mogelijkheid om de drempel waarde, het aggregatie type te bewerken en de aggregatie granulatie blijft gratis beschikbaar voor uw gemigreerde waarschuwings regel. Als u echter de gemigreerde waarschuwings regel bewerkt om een van de nieuwe functies, meldingen of actie typen van het waarschuwings platform te gebruiken, worden er kosten in rekening gebracht. Zie voor meer informatie over de prijzen voor waarschuwings regels en meldingen [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/).

Hieronder ziet u enkele voor beelden van gevallen waarin u kosten in rekening brengt voor uw waarschuwings regel:

- Er is een nieuwe (niet-gemigreerde) waarschuwings regel gemaakt die groter is dan de beschik bare eenheden op het nieuwe Azure Monitor-platform
- Alle gegevens die zijn opgenomen en bewaard buiten de beschik bare eenheden van Azure Monitor
- Alle webtests met meerdere tests die zijn uitgevoerd door Application Insights
- Aangepaste metrische gegevens die zijn opgeslagen buiten de beschik bare eenheden die zijn opgenomen in Azure Monitor
- Alle gemigreerde waarschuwings regels die worden bewerkt om nieuwere metrische waarschuwings functies te gebruiken, zoals frequentie, meerdere resources/dimensies, [Dynamische drempel waarden](../alerts/alerts-dynamic-thresholds.md), het wijzigen van de bron/het signaal, enzovoort.
- Gemigreerde actie groepen die zijn bewerkt voor het gebruik van nieuwere meldingen of actie typen zoals SMS, spraak oproep en/of ITSM-integratie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [nieuwe geïntegreerde Azure monitor](../overview.md).
* Meer informatie over de nieuwe [Azure-waarschuwingen](./alerts-overview.md).