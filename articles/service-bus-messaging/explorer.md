---
title: Azure Service Bus Explorer gebruiken om gegevens bewerkingen op Service Bus uit te voeren (preview)
description: Dit artikel bevat informatie over het gebruik van de portal Azure Service Bus Explorer voor toegang tot Azure Service Bus gegevens.
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: eb6610945ba4b09543308ab351d3a36ec7e587dd
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684838"
---
# <a name="use-service-bus-explorer-to-perform-data-operations-on-service-bus-preview"></a>Service Bus Explorer gebruiken om gegevens bewerkingen op Service Bus uit te voeren (preview)

## <a name="overview"></a>Overzicht

Met Azure Service Bus kunnen client toepassingen van verzenders en ontvangers hun bedrijfs logica loskoppelen met het gebruik van vertrouwde Point-to-Point (wachtrij) en publiceren/abonneren (topic)-semantiek.

Bewerkingen die worden uitgevoerd op een Azure Service Bus naam ruimte zijn twee soorten 
   * Beheer bewerkingen: maken, bijwerken, verwijderen van Service Bus naam ruimte, wacht rijen, onderwerpen en abonnementen.
   * Gegevens bewerkingen: berichten verzenden naar en ontvangen van wacht rijen, onderwerpen en abonnementen.

De Azure Service Bus Explorer breidt de functionaliteit van de portal uit tot de beheer bewerkingen voor de ondersteuning van gegevens bewerkingen (verzenden, ontvangen, bekijken) voor de wacht rijen, onderwerpen en abonnementen (en hun onbestelbare letter subentiteiten), rechts van de Azure Portal.

> [!NOTE]
> Dit artikel geeft een overzicht van de functionaliteit van de Azure Service Bus Explorer die in de Azure Portal.
>
> Het hulp programma Azure Service Bus Explorer is ***niet** _ het OSS-hulp programma van community [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).
>

## <a name="prerequisites"></a>Vereisten

Als u het hulp programma Service Bus Explorer wilt gebruiken, moet u de volgende taken uitvoeren: 

- Richt een Azure Service Bus naam ruimte in.
- Een wachtrij maken voor het verzenden en ontvangen van een bericht van of een onderwerp met een abonnement om de functionaliteit te testen. Raadpleeg de volgende artikelen voor meer informatie over het maken van wacht rijen, onderwerpen en abonnementen: 
    - [Snelstartgids-wacht rijen maken](service-bus-quickstart-portal.md)
    - [Snelstartgids: onderwerpen maken](service-bus-quickstart-topics-subscriptions-portal.md)
- Zorg ervoor dat u lid bent van een van deze rollen op de naam ruimte: 
    - [Gegevens eigenaar Service Bus](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) 
    - [Inzender](../role-based-access-control/built-in-roles.md#contributor) 
    - [Eigenaar](/role-based-access-control/built-in-roles.md#owner)


## <a name="using-the-service-bus-explorer"></a>De Service Bus Explorer gebruiken

Als u de Azure Service Bus Explorer wilt gebruiken, moet u naar de Service Bus naam ruimte gaan waarop u de bewerkingen voor verzenden, bekijken en ontvangen wilt uitvoeren.

Als u bewerkingen wilt uitvoeren voor een wachtrij, kiest u _ *' wacht rijen '** in het navigatie menu. Als u bewerkingen wilt uitvoeren op een onderwerp (en gerelateerde abonnementen), kiest u **onderwerpen**. 

:::image type="content" source="./media/service-bus-explorer/queue-topics-left-navigation.png"alt-text="Entiteit selecteren":::

Nadat u de **' wacht rijen** ' of **' onderwerpen '** hebt geselecteerd, kiest u de specifieke wachtrij of het onderwerp.

Selecteer de **Service Bus Explorer (preview)** in het navigatie menu links

:::image type="content" source="./media/service-bus-explorer/left-navigation-menu-selected.png" alt-text="Menu Linkernavigatie van SB Explorer":::

### <a name="sending-a-message-to-a-queue-or-topic"></a>Een bericht verzenden naar een wachtrij of onderwerp

Als u een bericht wilt verzenden naar een **wachtrij** of **onderwerp**, klikt u op het tabblad **_verzenden_* _ van de service bus Explorer.

Hier kunt u een bericht opstellen: 

1. Kies _ *inhouds type** als ' text/Plains ', ' Application/XML ' of ' application/json '.
2. Voeg de bericht **inhoud** toe. Zorg ervoor dat deze overeenkomt met het eerder ingestelde **inhouds type** .
3. Stel de **Geavanceerde eigenschappen** (optioneel) in. Dit zijn onder andere correlatie-id, bericht-id, label, ReplyTo, time to Live (TTL) en geplande tijd in wachtrij (voor geplande berichten).
4. De **aangepaste eigenschappen** instellen: dit kunnen alle gebruikers eigenschappen zijn die zijn ingesteld op basis van een woordenlijst sleutel.

Zodra het bericht is samengesteld, klikt u op verzenden.

:::image type="content" source="./media/service-bus-explorer/send-experience.png" alt-text="Bericht opstellen":::

Wanneer de verzend bewerking is voltooid, 

* Bij verzen ding naar de wachtrij worden de tellers voor metrische gegevens van **actieve berichten** verhoogd.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

* Bij verzen ding naar het onderwerp worden de tellers voor metrische gegevens van **actieve berichten** verhoogd op het abonnement waarnaar het bericht is doorgestuurd.

    :::image type="content" source="./media/service-bus-explorer/topic-after-send-metrics.png" alt-text="TopicAfterSendMetrics":::

### <a name="receiving-a-message-from-a-queue"></a>Een bericht ontvangen van een wachtrij

Met de functie receive in de Service Bus Explorer kan één bericht tegelijk worden ontvangen. De receive-bewerking wordt uitgevoerd met behulp van de **ReceiveAndDelete** -modus.

> [!IMPORTANT]
> Houd er rekening mee dat de ontvangst bewerking die door Service Bus Explorer wordt uitgevoerd, een **_destructief ontvangen_* _ is, dat wil zeggen het bericht wordt uit de wachtrij verwijderd wanneer het wordt weer gegeven in het hulp programma service bus Explorer.
>
> Als u wilt bladeren in berichten zonder ze uit de wachtrij te verwijderen, kunt u overwegen de functie _*_Peek_*_ te gebruiken.
>

Een bericht van een wachtrij (of de bijbehorende deadletter subwachtrij) ontvangen 

1. Klik op het tabblad _*_ontvangen_*_ in de service bus Explorer.
2. Controleer de metrische gegevens om te zien of er _ *actieve berichten** of **onbestelbare berichten** moeten worden ontvangen.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. Kies tussen de **_wachtrij_* _ of de _*_Deadletter_*_ -subwachtrij.

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadletter":::

4. Klik op de knop _*_ontvangen_*_ , gevolgd door _*_Ja_*_ om de bewerking ' ontvangen en verwijderen ' te bevestigen.


Wanneer de ontvangst bewerking is gelukt, worden de gegevens van het bericht als hieronder weer gegeven in het raster. U kunt het bericht selecteren in het raster om de details ervan weer te geven.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-queue-2.png" alt-text="Scherm opname van het venster wacht rijen in de Azure Service Bus Explorer met bericht details die worden weer gegeven voor een actief bericht dat in de wachtrij is geselecteerd.":::


### <a name="peeking-a-message-from-a-queue"></a>Een bericht uit een wachtrij bekijken

Met de functie Peek kunt u de Service Bus Explorer gebruiken om de belangrijkste 32-berichten in een wachtrij of de deadletter-wachtrij weer te geven.

1. Als u het bericht in een wachtrij wilt bekijken, klikt u op het tabblad _*_Peek_*_ op de service bus Explorer.

    :::image type="content" source="./media/service-bus-explorer/peek-tab-selected.png" alt-text="PeekTab":::

2. Controleer de metrische gegevens om te zien of er _ *actieve berichten** of **onbestelbare berichten** moeten worden weer gegeven.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. Kies vervolgens tussen de **_wachtrij_* _ of de _*_Deadletter_*_ -subwachtrij.

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadletter":::

4. Klik op de knop _*_Peek_*_ . 

Zodra de Peek-bewerking is voltooid, worden Maxi maal 32 berichten weer gegeven in het raster. Als u de details van een bepaald bericht wilt weer geven, selecteert u het in het raster. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-queue-2.png" alt-text="PeekMessageFromQueue":::

> [!NOTE]
>
> Omdat Peek geen destructieve bewerking is, wordt het bericht _ *niet** uit de wachtrij verwijderd.
>

### <a name="receiving-a-message-from-a-subscription"></a>Een bericht ontvangen van een abonnement

Net als bij een wachtrij kan de **_Receive_*-bewerking van de * worden uitgevoerd op basis van een abonnement (of de deadletter-entiteit). Omdat een abonnement echter binnen de context van het onderwerp valt, wordt de ontvangst bewerking uitgevoerd door te navigeren naar de Service Bus Explorer voor een bepaald onderwerp.

> [!IMPORTANT]
> Houd er rekening mee dat de ontvangst bewerking die door Service Bus Explorer wordt uitgevoerd, een _*_destructieve ontvangst_*_ is. het bericht wordt uit de wachtrij verwijderd wanneer het wordt weer gegeven in het hulp programma service bus Explorer.
>
> Als u wilt bladeren in berichten zonder ze uit de wachtrij te verwijderen, kunt u overwegen de functie _*_Peek_*_ te gebruiken.
>

1. Klik op het tabblad _*_ontvangen_*_ en selecteer het specifieke _*_abonnement_*_ in de kiezer voor vervolg keuzelijst.

    :::image type="content" source="./media/service-bus-explorer/receive-subscription-tab-selected.png" alt-text="ReceiveTabSelected":::

2. Kiezen tussen het _*_abonnement_*_ of de _*_DeadLetter_*_ -subentiteit.

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. Klik op de knop _*_ontvangen_*_ , gevolgd door _*_Ja_*_ om de bewerking ' ontvangen en verwijderen ' te bevestigen.

Wanneer de ontvangst bewerking is gelukt, wordt het ontvangen bericht als volgt weer gegeven in het raster. Klik op het bericht om de details van het bericht weer te geven.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-subscription.png" alt-text="Scherm afbeelding van het tabblad ontvangen in de Azure Service Bus Explorer met de bericht gegevens die worden weer gegeven voor een actief bericht dat is ontvangen.":::

### <a name="peeking-a-message-from-a-subscription"></a>Een bericht van een abonnement bekijken

Als u alleen door de berichten in een abonnement of de deadletter-subentiteit wilt bladeren, kan de functie _*_Peek_*_ ook worden gebruikt voor het abonnement.

1. Klik op het tabblad _*_Peek_*_ en selecteer het specifieke _*_abonnement_*_ in de kiezer voor de vervolg keuzelijst.

    :::image type="content" source="./media/service-bus-explorer/peek-subscription-tab-selected.png" alt-text="PeekTabSelected":::

2. Kiezen tussen het _*_abonnement_*_ of de _*_DeadLetter_*_ -subentiteit.

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. Klik op de knop _*_Peek_*_ .

Zodra de Peek-bewerking is voltooid, worden Maxi maal 32 berichten weer gegeven in het raster. Als u de details van een bepaald bericht wilt weer geven, selecteert u het in het raster. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-subscription.png" alt-text="PeekMessageFromSubscription":::

> [!NOTE]
>
> Omdat Peek geen destructieve bewerking is, wordt het bericht _ *niet** uit de wachtrij verwijderd.
>

## <a name="next-steps"></a>Volgende stappen

   * Meer informatie over Service Bus- [wacht rijen](service-bus-queues-topics-subscriptions.md#queues) en- [onderwerpen](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)
   * Meer informatie over het maken van [Service Bus wachtrijen via de Azure Portal](service-bus-quickstart-portal.md)
   * Meer informatie over het maken van [Service Bus-onderwerpen en-abonnementen via de Azure Portal](service-bus-quickstart-topics-subscriptions-portal.md)