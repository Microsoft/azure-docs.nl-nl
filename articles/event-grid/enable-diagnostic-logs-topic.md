---
title: Azure Event Grid-Diagnostische logboeken voor onderwerpen of domeinen inschakelen
description: In dit artikel vindt u stapsgewijze instructies voor het inschakelen van Diagnostische logboeken voor een Azure Event grid-onderwerp.
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: ff00c1438c49cbc9f9e67eba0cf0acef7991a5a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576448"
---
#  <a name="enable-diagnostic-logs-for-azure-event-grid-topics-or-domains"></a>Diagnostische logboeken inschakelen voor Azure Event grid-onderwerpen of-domeinen
In dit artikel vindt u stapsgewijze instructies voor het inschakelen van diagnostische instellingen voor Event Grid onderwerpen of domeinen.  Met deze instellingen kunt u **publicatie-en leverings fout** logboeken vastleggen en weer geven. 

## <a name="prerequisites"></a>Vereisten

- Een onderwerp over een ingericht gebeurtenis raster
- Een ingerichte bestemming voor het vastleggen van Diagnostische logboeken. Dit kan een van de volgende bestemmingen zijn op dezelfde locatie als het event grid-onderwerp:
    - Azure Storage-account
    - Event Hub
    - Log Analytics-werkruimte

## <a name="enable-diagnostic-logs-for-a-custom-topic"></a>Diagnostische logboeken inschakelen voor een aangepast onderwerp

> [!NOTE]
> De volgende procedure bevat stapsgewijze instructies voor het inschakelen van Diagnostische logboeken voor een onderwerp. Stappen voor het inschakelen van Diagnostische logboeken voor een domein zijn vergelijkbaar. In stap 2 gaat u naar het gebeurtenis raster **domein** in de Azure Portal.  

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Ga naar het event grid-onderwerp waarvoor u de instellingen voor Diagnostische logboeken wilt inschakelen. 
    1. Zoek in de zoek balk aan de bovenkant naar **Event grid onderwerpen**. 
    
        ![Zoeken naar aangepaste onderwerpen](./media/enable-diagnostic-logs-topic/search-custom-topics.png)
    1. Selecteer het **onderwerp** in de lijst waarvoor u de diagnostische instellingen wilt configureren. 
1. Selecteer **Diagnostische instellingen** onder **bewaking** in het menu links.
1. Selecteer op de pagina **Diagnostische instellingen** de optie **nieuwe diagnostische instelling toevoegen**. 
    
    ![Knop diagnostische instelling toevoegen](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Geef een **naam** op voor de diagnostische instelling. 
6. Selecteer de opties **DeliveryFailures** en **PublishFailures** in de sectie **logboek** . 
    ![De fouten selecteren](./media/enable-diagnostic-logs-topic/log-failures.png)
7. Schakel een of meer van de vastleg doelen in voor de logboeken en configureer ze vervolgens door een eerder gemaakte Capture-bron te selecteren. 
    - Als u **archiveren naar een opslag account** selecteert, selecteert u **opslag account-configureren** en selecteert u vervolgens het opslag account in uw Azure-abonnement. 

        ![Scherm opname van de pagina Diagnostische instellingen met ' archiveren naar een Azure-opslag account ' ingeschakeld en een opslag account geselecteerd.](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Als u **Stream naar een event hub** selecteert, selecteert u **Event hub-configure** en selecteert u vervolgens de Event Hubs naam ruimte, Event hub en het toegangs beleid. 
        ![Scherm opname van de pagina Diagnostische instellingen met ' stream to a Event Hub ' ingeschakeld.](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Als u **verzenden naar log Analytics** selecteert, selecteert u de werk ruimte log Analytics.
        ![Scherm opname van de pagina Diagnostische instellingen met de optie Send to Log Analytics.](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. Selecteer **Opslaan**. Selecteer vervolgens **X** in de rechter bovenhoek om de pagina te sluiten. 
9. Ga nu terug naar de pagina **Diagnostische instellingen** en controleer of er een nieuwe vermelding wordt weer gegeven in de tabel **Diagnostische instellingen** . 
    ![Scherm opname van de pagina Diagnostische instellingen met een nieuwe vermelding die is gemarkeerd in de tabel Diagnostische instellingen.](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     U kunt ook het verzamelen van alle metrische gegevens inschakelen voor het onderwerp. 

## <a name="enable-diagnostic-logs-for-a-system-topic"></a>Diagnostische logboeken inschakelen voor een systeem onderwerp

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Ga naar het event grid-onderwerp waarvoor u de instellingen voor Diagnostische logboeken wilt inschakelen. 
    1. Zoek in de zoek balk aan de bovenkant naar **Event grid systeem onderwerpen**. 
    
        ![Systeem onderwerpen zoeken](./media/enable-diagnostic-logs-topic/search-system-topics.png)
    1. Selecteer het **onderwerp** van het systeem waarvoor u de diagnostische instellingen wilt configureren. 
    
        ![Systeem onderwerp selecteren](./media/enable-diagnostic-logs-topic/select-system-topic.png)
3. Selecteer **Diagnostische instellingen** onder **bewaking** in het menu links en selecteer vervolgens **Diagnostische instelling toevoegen**. 

    ![Diagnostische instellingen toevoegen-knop](./media/enable-diagnostic-logs-topic/system-topic-add-diagnostic-settings-button.png)
4. Geef een **naam** op voor de diagnostische instelling. 
7. Selecteer de **DeliveryFailures** in het gedeelte **logboek** . 
    ![Leverings fouten selecteren](./media/enable-diagnostic-logs-topic/system-topic-select-delivery-failures.png)
6. Schakel een of meer van de vastleg doelen in voor de logboeken en configureer ze vervolgens door een eerder gemaakte Capture-bron te selecteren. 
    - Als u **verzenden naar log Analytics** selecteert, selecteert u de werk ruimte log Analytics.
        ![Verzenden naar Log Analytics](./media/enable-diagnostic-logs-topic/system-topic-select-log-workspace.png) 
    - Als u **archiveren naar een opslag account** selecteert, selecteert u **opslag account-configureren** en selecteert u vervolgens het opslag account in uw Azure-abonnement. 

        ![Archiveren naar een Azure Storage-account](./media/enable-diagnostic-logs-topic/system-topic-select-storage-account.png)
    - Als u **Stream naar een event hub** selecteert, selecteert u **Event hub-configure** en selecteert u vervolgens de Event Hubs naam ruimte, Event hub en het toegangs beleid. 
        ![Streamen naar een Event Hub](./media/enable-diagnostic-logs-topic/system-topic-select-event-hub.png)
8. Selecteer **Opslaan**. Selecteer vervolgens **X** in de rechter bovenhoek om de pagina te sluiten. 
9. Ga nu terug naar de pagina **Diagnostische instellingen** en controleer of er een nieuwe vermelding wordt weer gegeven in de tabel **Diagnostische instellingen** . 
    ![Diagnostische instelling in de lijst](./media/enable-diagnostic-logs-topic/system-topic-diagnostic-settings-targets.png)

     U kunt ook het verzamelen van alle **metrische gegevens** voor het onderwerp systeem inschakelen.

    ![Systeem onderwerp: alle metrische gegevens inschakelen](./media/enable-diagnostic-logs-topic/system-topics-metrics.png)

## <a name="view-diagnostic-logs-in-azure-storage"></a>Diagnostische logboeken in Azure Storage weer geven 

1. Zodra u een opslag account als een vastleg doel hebt ingeschakeld, wordt Event Grid het verzenden van Diagnostische logboeken gestart. U ziet nieuwe containers met de naam **Insights-logs-deliveryfailures** en **Insights-logs-publishfailures** in het opslag account. 

    ![Opslag containers voor Diagnostische logboeken](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. Wanneer u door een van de containers navigeert, wordt er een BLOB in JSON-indeling in de vorm van een blobopslag. Het bestand bevat logboek vermeldingen voor een leverings fout of een fout bij het publiceren. Het navigatiepad vertegenwoordigt de **ResourceID** van het gebeurtenis grid-onderwerp en de tijds tempel (minuut niveau) waar de logboek vermeldingen werden gegenereerd. Het BLOB/JSON-bestand, dat kan worden gedownload, voldoet aan het schema dat in de volgende sectie wordt beschreven. 

    [![JSON-bestand in de opslag ](./media/enable-diagnostic-logs-topic/select-json.png)](./media/enable-diagnostic-logs-topic/select-json.png)
3. U ziet nu inhoud in het JSON-bestand zoals in het volgende voor beeld: 

    ```json
    {
        "time": "2019-11-01T00:17:13.4389048Z",
        "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
        "eventSubscriptionName": "SAMPLEDESTINATION",
        "category": "DeliveryFailures",
        "operationName": "Deliver",
        "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
    }
    ```
## <a name="next-steps"></a>Volgende stappen
Zie [Diagnostische logboeken](diagnostic-logs.md)voor het logboek schema en andere conceptuele informatie over Diagnostische logboeken voor onderwerpen of domeinen.
