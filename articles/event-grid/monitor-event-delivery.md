---
title: Azure Event Grid metrische gegevens weer geven en waarschuwingen instellen
description: In dit artikel wordt beschreven hoe u de Azure Portal gebruikt om metrische gegevens weer te geven voor Azure Event Grid onderwerpen en abonnementen, en hierop waarschuwingen te maken.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 518d34d39e6fbecc408fe9a44d899fe4745d60d0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008953"
---
# <a name="monitor-event-grid-message-delivery"></a>Bezorging van Event Grid bericht bewaken 
In dit artikel wordt beschreven hoe u de portal kunt gebruiken om de metrische gegevens voor Event Grid-onderwerpen en-abonnementen te bekijken en om waarschuwingen te maken. 

## <a name="metrics"></a>Metrische gegevens

In de portal worden metrische gegevens weer gegeven voor de status van het leveren van gebeurtenis berichten.

Hier volgen enkele metrische gegevens voor onderwerpen:

* **Publiceren is voltooid**: de gebeurtenis is verzonden naar het onderwerp en verwerkt met een 2xx-antwoord.
* **Publiceren is mislukt**: de gebeurtenis is verzonden naar het onderwerp, maar er is een fout code afgewezen.
* Niet- **overeenkomende**: de gebeurtenis is gepubliceerd naar het onderwerp, maar is niet gekoppeld aan een gebeurtenis abonnement. De gebeurtenis is verwijderd.

Hier volgen enkele metrische gegevens voor abonnementen:

* De **levering is voltooid**: de gebeurtenis is bezorgd bij het eind punt van het abonnement en er is een 2xx-antwoord ontvangen.
* De **Bezorging is mislukt**: elke keer dat de service probeert te leveren en de gebeurtenis-handler retourneert geen succes 2xx-code, wordt het prestatie meter item dat **kan worden bezorgd** verhoogd. Als we proberen dezelfde gebeurtenis meerdere keren te leveren en mislukken, wordt het **prestatie meter** item voor elke fout verhoogd.
* **Verlopen gebeurtenissen**: de gebeurtenis is niet bezorgd en alle nieuwe pogingen zijn verzonden. De gebeurtenis is verwijderd.
* **Overeenkomende gebeurtenissen**: de gebeurtenis in het onderwerp is afgestemd op het gebeurtenis abonnement.

    > [!NOTE]
    > Zie [metrische gegevens die worden ondersteund door Azure Event grid](metrics.md)voor een volledige lijst met metrische gegevens.

## <a name="view-custom-topic-metrics"></a>Metrische gegevens over aangepaste onderwerpen weer geven

Als u een aangepast onderwerp hebt gepubliceerd, kunt u de metrische gegevens weer geven. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Typ in de zoek balk van het onderwerp **Event grid onderwerpen** en selecteer vervolgens **Event grid onderwerpen** in de vervolg keuzelijst. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Event Grid-onderwerpen zoeken en selecteren":::
3. Selecteer uw aangepaste onderwerp in de lijst met onderwerpen. 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="Uw aangepaste onderwerp selecteren":::
4. Bekijk de metrische gegevens voor het onderwerp van de aangepaste gebeurtenis op de pagina met het **Event grid onderwerp** . In de volgende afbeelding is de sectie **Essentials** waarin de resource groep, het abonnement etc. wordt weer gegeven, geminimaliseerd. 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="Metrische gebeurtenis gegevens weer geven":::

U kunt grafieken met ondersteunde metrische gegevens maken met behulp van het tabblad **metrische gegevens** van de pagina met het **Event grid onderwerp** .

:::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="Pagina met metrische gegevens over het onderwerp":::

Zie [metrische gegevens in azure monitor](../azure-monitor/platform/data-platform-metrics.md) voor meer informatie over metrische gegevens.

Zie bijvoorbeeld het grafiek met metrische gegevens voor de metrische gegevens van **gepubliceerde gebeurtenissen** .

:::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="Metrische gegevens van gepubliceerde gebeurtenissen":::


## <a name="view-subscription-metrics"></a>Metrische gegevens van abonnement weer geven
1. Ga naar de pagina met het **Event grid-onderwerp** door de stappen uit de vorige sectie te volgen. 
2. Selecteer het abonnement in het onderste deel venster, zoals in het volgende voor beeld wordt weer gegeven. 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="Selecteer gebeurtenisabonnement":::    

    U kunt ook zoeken naar **Event grid-abonnementen** in de zoek balk van de Azure Portal, **het type onderwerp**, het **abonnement** en de **locatie** selecteren om een gebeurtenis abonnement te zien. 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="Selecteer een gebeurtenis abonnement op de pagina Event Grid abonnementen":::        

    Voor aangepaste onderwerpen selecteert u **Event grid onderwerpen** als **onderwerps type**. Selecteer voor systeem onderwerpen het type van de Azure-resource, bijvoorbeeld **opslag accounts (BLOB, GPv2)**. 
3. Bekijk de metrische gegevens voor het abonnement op de start pagina van het abonnement in een grafiek. U kunt **algemene**, **fout**-, **latentie**-en **onbestelbare** metrische gegevens weer geven voor afgelopen 1 uur, 6 uur, 12 uur, 1 dag, 7 dagen of 30 dagen. 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="Metrische gegevens op de start pagina van het abonnement":::    

## <a name="view-system-topic-metrics"></a>Metrische gegevens over het systeem onderwerp weer geven

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Typ in de zoek balk van het onderwerp **Event grid systeem onderwerpen** en selecteer vervolgens **Event grid systeem onderwerpen** in de vervolg keuzelijst. 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="Event Grid systeem onderwerpen zoeken en selecteren":::
3. Selecteer uw systeem onderwerp in de lijst met onderwerpen. 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="Selecteer uw systeem onderwerp":::
4. Bekijk de metrische gegevens voor het onderwerp systeem op de pagina **Event grid systeem onderwerp** . In de volgende afbeelding is de sectie **Essentials** waarin de resource groep, het abonnement etc. wordt weer gegeven, geminimaliseerd. 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="Metrische gegevens over het systeem onderwerp weer geven op de pagina overzicht":::

U kunt grafieken met ondersteunde metrische gegevens maken met behulp van het tabblad **metrische gegevens** van de pagina met het **Event grid onderwerp** .

:::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="Systeem onderwerp-metrische gegevens pagina":::

Zie [metrische gegevens in azure monitor](../azure-monitor/platform/data-platform-metrics.md) voor meer informatie over metrische gegevens.


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- Zie [waarschuwingen instellen](set-alerts.md)voor meer informatie over het maken van waarschuwingen voor metrische gegevens en activiteiten logboek bewerkingen.
- [Event grid aflevering van berichten en probeer het opnieuw](delivery-and-retry.md).
