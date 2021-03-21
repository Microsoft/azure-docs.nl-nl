---
title: 'Zelfstudie: Een grafiek met metrische gegevens maken in Azure Monitor'
description: Meer informatie over het maken van een grafiek met metrische gegevens met Metrics Explorer in Azure.
author: bwren
ms.author: bwren
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 9923bfcbd439ef5e20e44e9c397199fe06f40b43
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102032905"
---
# <a name="tutorial-create-a-metrics-chart-in-azure-monitor"></a>Zelfstudie: Een grafiek met metrische gegevens maken in Azure Monitor
Metrics Explorer is een functie van Azure Monitor in Azure Portal waarmee u grafieken kunt maken op basis van metrische waarden, trends visueel kunt correleren en pieken en dalen in metrische waarden kunt onderzoeken. Gebruik Metrics Explorer om de status en het gebruik van uw Azure-resources te onderzoeken of grafieken te tekenen op basis van aangepaste metrische gegevens. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een metrische waarde selecteren waarvoor u een grafiek wilt tekenen
> * Verschillende aggregaties van metrische waarden uitvoeren
> * Het tijdsbereik en de granulatie van de grafiek wijzigen

De onderstaande video betreft een uitgebreider scenario dan de procedure die wordt beschreven in dit artikel. Als u niet bekend bent met metrische gegevens, wordt u aangeraden dit artikel eerst te lezen en vervolgens de video te bekijken voor gedetailleerde informatie. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4qO59]

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u een Azure-resource nodig die kan worden gecontroleerd. U kunt hiervoor elke resource in uw Azure-abonnement gebruiken die ondersteuning biedt voor metrische gegevens. Dit kunt u vaststellen door in Azure Portal naar het menu van de resource te gaan en te kijken of er een optie **Metrische gegevens** staat in de sectie **Controle** van het menu.


## <a name="log-in-to-azure"></a>Meld u aan bij Azure.
Meld u aan bij de Azure-portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="open-metrics-explorer-and-select-a-scope"></a>De verkenner voor metrische gegevens openen en een bereik selecteren
U kunt de verkenner voor metrische gegevens openen vanuit het menu van Azure Monitor of vanuit het menu van een resource in Azure Portal. De metrische gegevens van alle resources zijn beschikbaar, ongeacht de gekozen optie. 

1. Selecteer **Metrische gegevens** in het menu van **Azure Monitor** of in de sectie **Controle** van het menu van een resource.

1. Selecteer **Bereik**. Dit is de resource waarvoor u de metrische gegevens wilt weergeven. Het bereik is al ingevuld als u de verkenner voor metrische gegevens hebt geopend vanuit het menu van een resource. Ga naar [dit artikel](../essentials/metrics-charts.md#resource-scope-picker) voor meer informatie over de diverse mogelijkheden van de resourcebereikkiezer.

    ![Een bereik selecteren](media/tutorial-metrics-explorer/scope-picker.png)

2. Selecteer een **naamruimte** als het bereik er meer dan één omvat. De naamruimte is slechts een manier om metrische gegevens te organiseren, zodat u ze gemakkelijk kunt vinden. Zo hebben opslagaccounts afzonderlijke naamruimten voor het opslaan van bestanden, tabellen, blobs en wachtrijen. Veel resourcetypen hebben slechts één naamruimte.

3. Selecteer een metrische waarde in een lijst met beschikbare metrische gegevens voor het geselecteerde bereik en de geselecteerde naamruimte.

    ![Een metrische waarde selecteren](media/tutorial-metrics-explorer/metric-picker.png)

4. Wijzig desgewenst de metrische waarde **Aggregatie**. Hiermee definieert u hoe de metrische waarden worden geaggregeerd over de tijdgranulatie voor de grafiek. Als de tijdgranulatie bijvoorbeeld is ingesteld op 15 minuten en de aggregatie is ingesteld op Som, wordt elk punt in de grafiek de som van alle waarden die zijn verzameld in een segment van 15 minuten.

    ![Schermopname van een grafiek met de titel Sum ingress voor contosoretailweb.](media/tutorial-metrics-explorer/chart.png)

5. Gebruik de knop **Metrische waarde toevoegen** en herhaal deze stappen als u meerdere metrische waarden wilt zien die in dezelfde grafiek zijn getekend. Als u meerdere grafieken in één weergave wilt, selecteert u de knop **Nieuwe grafiek**.

## <a name="select-a-time-range-and-granularity"></a>Een tijdsbereik en granulatie selecteren

In de grafiek worden standaard de meest recente 24 uur aan metrische gegevens weergegeven. Gebruik de tijdkiezer als u het **Tijdsbereik** voor de grafiek of de **Tijdgranulatie** waarmee het tijdsbereik voor elk gegevenspunt wordt gedefinieerd, wilt wijzigen. In de grafiek wordt de opgegeven aggregatie gebruikt voor het berekenen van alle steekproefwaarden ten opzichte van de opgegeven tijdgranulatie.

![Venster voor het wijzigen van het tijdsbereik](media/tutorial-metrics-explorer/time-picker.png)


Gebruik **Time Brush** om een opvallend gebied van de grafiek nader te onderzoeken, zoals een piek of een dal. Plaats de muisaanwijzer aan het begin van het gebied, klik erop en houd de linkermuis ingedrukt, sleep naar de andere kant van het gebied en laat de knop los. In de grafiek wordt ingezoomd op dat tijdsbereik. 

![Time Brush](media/tutorial-metrics-explorer/time-brush.png)

## <a name="apply-dimension-filters-and-splitting"></a>Dimensies filteren en splitsen
Raadpleeg de volgende naslaginformatie voor geavanceerde functies waarmee u extra analyses kunt uitvoeren op uw metrische gegevens en mogelijke uitbijters in uw gegevens kunt identificeren.

- Met [Filteren](../essentials/metrics-charts.md#filters) kunt u kiezen welke dimensiewaarden in de grafiek worden opgenomen. Bijvoorbeeld: u wilt alleen geslaagde aanvragen weergeven voor het meten van de *reactietijd van een server*. 

- Met [Splitsen ](../essentials/metrics-charts.md#apply-splitting) bepaalt u of in het diagram elke waarde van een dimensie wordt weergegeven op een afzonderlijke regel of dat de waarden worden geaggregeerd op één regel. Bijvoorbeeld: u wilt één regel zien met de gemiddelde reactietijd van alle servers of u wilt afzonderlijke regels voor elke server. 

Zie [voorbeelden van grafieken](../essentials/metric-chart-samples.md) waarin een filter of splitsing is toegepast.

## <a name="advanced-chart-settings"></a>Geavanceerde instellingen voor grafieken

U kunt de stijl, titel en geavanceerde instellingen van de grafiek aanpassen. Wanneer u klaar bent met uw aanpassingen, kunt u deze vastmaken aan een dashboard om uw werk op te slaan. U kunt ook waarschuwingen voor metrische gegevens configureren. Zie de [geavanceerde functies van Metrics Explorer](../essentials/metrics-charts.md#locking the-range-of-the-y-axis) voor meer informatie over deze en andere geavanceerde functies van Metrics Explorer in Azure Monitor.


## <a name="next-steps"></a>Volgende stappen
Nu u hebt geleerd hoe u met metrische gegevens in Azure Monitor kunt werken, kunt u leren hoe u metrische gegevens kunt gebruiken om proactieve waarschuwingen te verzenden.

> [!div class="nextstepaction"]
> [Metrische waarschuwing maken, bekijken en beheren met Azure Monitor](../essentials/metrics-charts.md#alert-rules)

