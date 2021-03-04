---
title: Een Azure-resource bewaken met Azure Monitor
description: Krijg meer informatie over het verzamelen en analyseren van gegevens voor een Azure-resource in Azure Monitor.
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: 0bbc08ccf02f572fefd1c676e2b1d73157e169c9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033228"
---
# <a name="quickstart-monitor-an-azure-resource-with-azure-monitor"></a>Quickstart: Een Azure-resource bewaken met Azure Monitor
[Azure Monitor](../overview.md) begint met het verzamelen van gegevens van Azure-resources op het moment dat ze worden gemaakt. In deze quickstart krijgt u een beknopt overzicht van de gegevens die automatisch worden verzameld voor een resource en ziet u hoe u deze kunt weergeven in Azure Portal. U kunt later een configuratie toevoegen voor het verzamelen van aanvullende gegevens. Via het menu van Azure Monitor kunt u ook dezelfde hulpprogramma's gebruiken voor toegang tot gegevens die zijn verzameld voor alle resources in uw abonnement.

Zie [Azure-resources bewaken met Azure Monitor](../essentials/monitor-azure-resource.md) voor meer gedetailleerde beschrijvingen van het bewaken van gegevens die zijn verzameld uit Azure-resources.


## <a name="sign-in-to-azure-portal"></a>Meld u aan bij Azure Portal

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com). 


## <a name="overview-page"></a>Overzichtspagina
Veel services bevatten bewakingsgegevens op de pagina **Overzicht** als een snelle weergave van hun werking. Dit is doorgaans gebaseerd op een subset van metrische gegevens van het platform die zijn opgeslagen in de metrische gegevens van Azure Monitor.

1. Zoek een Azure-resource in uw abonnement.
2. Ga naar de pagina **Overzicht** en kijk of er prestatiegegevens worden weergegeven. Deze gegevens worden verstrekt door Azure Monitor. Het onderstaande voorbeeld is de pagina **Overzicht** pagina voor een Azure-opslagaccount en u kunt zien dat er meerdere metrische gegevens worden weergegeven.

    ![Overzichtspagina](media/quick-monitor-azure-resource/overview.png)

3. U kunt op een van de grafieken klikken om de gegevens in Metrics Explorer te openen, die hieronder worden beschreven.

## <a name="view-the-activity-log"></a>Het activiteitenlogboek weergeven
Het activiteitenlogboek biedt inzicht in de bewerkingen op elke Azure-resource in het abonnement. Dit omvat informatie zoals wanneer een bron wordt gemaakt of gewijzigd, wanneer een taak wordt gestart of wanneer een bepaalde bewerking wordt uitgevoerd.

1. Selecteer bovenaan het menu voor uw resource **Activiteitenlogboek**.
2. Het huidige filter is ingesteld op gebeurtenissen met betrekking tot uw resource. Als er geen gebeurtenissen worden weergeven, wijzigt u de **Periode** om het tijdsbereik te vergroten.

    ![Schermopname van een activiteitenlogboek voor resources in Azure Portal met een lijst met bewerkingen voor de resource die is gegenereerd met de standaardfiltercriteria.](media/quick-monitor-azure-resource/activity-log-resource.png)

4. Als u gebeurtenissen van andere resources in uw abonnement wilt weergeven, wijzigt u de criteria in het filter of verwijdert u zelfs filtereigenschappen.

    ![Schermopname van een activiteitenlogboek voor resources in Azure Portal met een lijst met bewerkingen voor de resource die is gegenereerd met gewijzigde filtercriteria.](media/quick-monitor-azure-resource/activity-log-all.png)



## <a name="view-metrics"></a>Metrische gegevens bekijken
Metrische gegevens zijn numerieke waarden waarmee een bepaald aspect van uw resource op een bepaald tijdstip wordt beschreven. Azure Monitor verzamelt automatisch metrische platformgegevens met intervallen van één minuut uit alle Azure-resources. U kunt deze metrische gegevens weergeven met Metrics Explorer.

1. Selecteer in de sectie **Controle** van uw resourcemenu de optie **Metrische gegevens**. Hiermee opent u Metrics Explorer met het bereik om uw resource in te stellen.
2. Klik op **Metrische waarde toevoegen** om een metrische waarde aan de grafiek toe te voegen.
   
   ![Schermopname van Metrics Explorer in Azure Portal, met de metrische gegevens voor een resource. De vervolgkeuzelijst Metrisch is geopend voor het toevoegen van nieuwe metrische gegevens.](media/quick-monitor-azure-resource/metrics-explorer-01.png)
   
4. Selecteer een **metrische waarde** in de vervolgkeuzelijst en vervolgens een **aggregatie**. Hiermee definieert u hoe een steekproef van de verzamelde waarden voor elk tijdsinterval wordt genomen.

    ![Schermopname van Metrics Explorer in Azure Portal, met de metrische gegevens voor een resource. De vervolgkeuzelijst Aggregatie is geopend en Gem. is geselecteerd.](media/quick-monitor-azure-resource/metrics-explorer-02.png)

5. Klik op **Metrische waarde toevoegen** om extra metrische en aggregatiecombinaties aan de grafiek toe te voegen.

    ![Schermopname van Metrics Explorer in Azure Portal, met de metrische gegevens voor een resource.](media/quick-monitor-azure-resource/metrics-explorer-03.png)



## <a name="next-steps"></a>Volgende stappen
In deze quickstart hebt u het activiteitenlogboek en de metrische gegevens weergegeven voor een Azure-resource die automatisch worden verzameld door Azure Monitor. Ga door naar de volgende quickstart waarin wordt uitgelegd hoe u het activiteitenlogboek kunt verzamelen in een Log Analytics-werkruimte, waar de gegevens kunnen worden geanalyseerd met behulp van [logboekquery's](../logs/log-query-overview.md).

> [!div class="nextstepaction"]
> [Azure-activiteitenlogboek naar Log Analytics-werkruimte verzenden](./quick-collect-activity-log-portal.md)