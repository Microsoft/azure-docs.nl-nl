---
title: Bewakings Media Services
description: Begin hier om te leren hoe u Media Services kunt bewaken
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femilia
ms.topic: how-to
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 03/17/2021
ms.openlocfilehash: 90ca92dc19c588d0b19adf009301cf844e0cdbde
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105609039"
---
# <a name="monitor-media-services"></a>Media Services bewaken

Wanneer u belang rijke toepassingen en bedrijfs processen hebt die afhankelijk zijn van Azure-resources, wilt u deze resources controleren op hun Beschik baarheid, prestaties en werking. In dit artikel worden de bewakings gegevens beschreven die worden gegenereerd door Media Services en hoe u de functies van Azure Monitor kunt gebruiken om deze gegevens te analyseren en te waarschuwen.

## <a name="metrics-are-useful"></a>Metrische gegevens zijn handig

Hier volgen enkele voor beelden van de manier waarop bewaking Media Services metrische gegevens u kan helpen inzicht te krijgen in hoe uw apps worden uitgevoerd. Enkele vragen die kunnen worden behandeld met Media Services metrische gegevens zijn:

- Hoe kan ik het standaard streaming-eind punt te controleren om te weten wanneer de limieten zijn overschreden?
- Hoe kan ik weet of ik voldoende Premium streaming-eindpunt schaal eenheden heb?
- Hoe kan ik een waarschuwing instellen om te weten wanneer mijn streaming-eind punten moeten worden geschaald?
- Hoe kan ik een waarschuwing instellen om te weten wanneer het maximale aantal uitgangen dat is geconfigureerd voor het account is bereikt?
- Hoe kan ik de uitsplitsing van mislukte aanvragen zien en wat de oorzaak van de fout is?
- Hoe kan ik zien hoeveel HLS of STREEPJES aanvragen worden opgehaald uit de packager?
- Hoe kan ik een waarschuwing instellen om te weten wanneer ik de drempel waarde van mislukte aanvragen heb bereikt?

<!--THIS DOESN'T BELONG HERE Concurrency becomes a concern for the number of Streaming Endpoints used in a single account over time. You need to keep in mind the relationship between the number of concurrent streams with complex publishing parameters like dynamic packaging to multiple protocols, multiple DRM encryptions etc. Each additional published live stream adds to the CPU and output bandwidth on the Streaming Endpoint. With that in mind, you should use Azure Monitor to closely watch the Streaming Endpoint's utilization (CPU and Egress capacity) to make certain that you are scaling it appropriately (or split traffic out between multiple Streaming Endpoints if you are getting into very high concurrency).-->

<!-- Optional diagram showing monitoring for your service. If you need help creating one, contact 
robb@microsoft.com -->

## <a name="what-is-azure-monitor"></a>Wat is Azure Monitor?

Media Services maakt bewakings gegevens met behulp van [Azure monitor](../../../azure-monitor/overview.md). Dit is een volledige stack bewakings service in azure met een volledige set functies voor het bewaken van uw Azure-resources naast bronnen in andere Clouds en on-premises.

Begin met het lezen van het artikel [Azure-resources controleren met Azure monitor](../../../azure-monitor/essentials/monitor-azure-resource.md), waarin de volgende concepten worden beschreven:

- Wat is Azure Monitor?
- Kosten die zijn gekoppeld aan bewaking
- Bewaken van gegevens die zijn verzameld in azure
- Gegevens verzameling configureren
- Standaard Programma's in azure voor het analyseren en waarschuwen van bewakings gegevens

## <a name="monitoring-data"></a>Bewakingsgegevens

Media Services worden dezelfde soorten bewakings gegevens verzameld als andere Azure-resources die worden beschreven in [gegevens van Azure-resources bewaken](../../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data).

Alle gegevens die worden verzameld door Azure Monitor kunnen in twee fundamentele typen worden ingedeeld: metrische gegevens en logboeken. Met deze twee typen kunt u:

- Visualiseer en analyseer de metrische gegevens met behulp van Metrics Explorer.
- Bewaak Media Services Diagnostische logboeken en maak waarschuwingen en meldingen voor hen.
- Met Logboeken kunt u het volgende doen:
  - Deze naar Azure Storage verzenden
  - Ze streamen naar Azure Event Hubs
  - Exporteren naar Log Analytics
  - Services van derden gebruiken

Zie het artikel [bewaking Media Services gegevens referentie](monitor-media-services-data-reference.md) voor gedetailleerde informatie over de metrische gegevens en logboek gegevens die zijn gemaakt door Media Services.

## <a name="collection-and-routing"></a>Verzameling en route ring

De *metrische gegevens* van het platform en het *activiteiten logboek* worden automatisch verzameld en opgeslagen, maar kunnen worden doorgestuurd naar andere locaties met behulp van een diagnostische instelling.  

*Bron logboeken* worden pas verzameld en opgeslagen als u een diagnostische **instelling hebt gemaakt** en deze naar een of meer locaties wilt door sturen.

Zie het artikel [Diagnostische instelling maken voor het verzamelen van platform logboeken en metrische gegevens in azure](../../../azure-monitor/essentials/diagnostic-settings.md) voor een gedetailleerd proces van het maken van een diagnostische instelling met behulp van de Azure Portal, CLI of Power shell.

Wanneer u een diagnostische instelling maakt, geeft u op welke categorieën logboeken u wilt verzamelen. De categorieën voor Media Services worden vermeld in [Media Services bewakings gegevens referentie](monitor-media-services-data-reference.md).

## <a name="analyzing-metrics"></a>Metrische gegevens analyseren

U kunt metrische gegevens voor Media Services met metrische gegevens uit andere Azure-Services analyseren door **metrische gegevens** te openen in het menu **Azure monitor** . Zie [aan de slag met Azure Metrics Explorer](../../../azure-monitor/essentials/metrics-getting-started.md) voor meer informatie over het gebruik van dit hulp programma.

Zie [Media Services Data Reference controleren](monitor-media-services-data-reference.md)voor een lijst met de metrische gegevens die worden verzameld voor Media Services.

## <a name="analyzing-logs"></a>Logboeken analyseren

Gegevens in Azure Monitor logboeken worden opgeslagen in tabellen waarin elke tabel een eigen set unieke eigenschappen heeft.  

Alle resource Logboeken in Azure Monitor hebben dezelfde velden die worden gevolgd door servicespecifieke velden. Het algemene schema wordt beschreven in [Azure monitor resource-logboek schema](../../../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema).

Het schema voor Media Services resource Logboeken vindt u in [bewaking Media Services gegevens verwijzing](monitor-media-services-data-reference.md).

Het [activiteiten logboek](../../../azure-monitor/essentials/activity-log.md) is een platform logboek in azure dat inzicht biedt in gebeurtenissen op abonnements niveau. U kunt deze onafhankelijk bekijken of door sturen naar Azure Monitor-logboeken, waar u veel complexere query's kunt uitvoeren met behulp van Log Analytics.

Zie [Media Services gegevens referentie bewaken](monitor-media-services-data-reference.md)voor een lijst met de typen bron logboeken die zijn verzameld voor Media Services.

### <a name="why-would-i-want-to-use-diagnostic-logs"></a>Waarom zou ik Diagnostische logboeken willen gebruiken?

Enkele dingen die u kunt onderzoeken met Diagnostische logboeken zijn:

- Het aantal licenties dat door het DRM-type is geleverd.
- Het aantal licenties dat door het beleid is afgeleverd.
- Fouten door DRM of beleids type.
- Het aantal niet-geautoriseerde licentie aanvragen van clients.

## <a name="alerts"></a>Waarschuwingen

Azure Monitor waarschuwingen geven u proactief op de hoogte wanneer er belang rijke voor waarden worden gevonden in uw bewakings gegevens. Hiermee kunt u problemen in uw systeem identificeren en oplossen voordat uw klanten ze opmerken. U kunt waarschuwingen instellen voor [metrische gegevens](../../../azure-monitor/alerts/alerts-metric-overview.md), [Logboeken](../../../azure-monitor/alerts/alerts-unified-log.md)en het [activiteiten logboek](../../../azure-monitor/alerts/activity-log-alerts.md).

Media Services metrische gegevens worden met regel matige tussen pozen verzameld, ongeacht of de waarde wordt gewijzigd. Ze zijn handig voor waarschuwingen omdat ze regel matig kunnen worden gesampled. Een waarschuwing kan snel worden geactiveerd met relatief eenvoudige logica.

<!--
The following table lists common and recommended alert rules for Media Services.

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable
**PLACEHOLDER** table

| Alert type | Condition | Description  |
|:---|:---|:---|
| | | |
| | | |
-->

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
