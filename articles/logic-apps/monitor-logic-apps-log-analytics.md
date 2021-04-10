---
title: Logische apps bewaken met behulp van Azure Monitor-logboeken
description: Problemen met logische apps oplossen door Azure Monitor Logboeken in te stellen en diagnostische gegevens te verzamelen voor Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 09/24/2020
ms.openlocfilehash: d1c095be1d0525cc843c04a3b83e14bb4211b175
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101721256"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>Azure Monitor logboeken instellen en diagnostische gegevens verzamelen voor Azure Logic Apps

Voor meer informatie over het opsporen van fouten in uw Logic apps tijdens runtime kunt u [Azure monitor logboeken](../azure-monitor/logs/data-platform-logs.md) instellen en gebruiken voor het vastleggen en opslaan van informatie over runtime gegevens en gebeurtenissen, zoals trigger gebeurtenissen, uitvoer gebeurtenissen en actie gebeurtenissen in een [log Analytics-werk ruimte](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). [Azure monitor](../azure-monitor/overview.md) helpt u bij het bewaken van uw Cloud-en on-premises omgevingen, zodat u hun Beschik baarheid en prestaties gemakkelijker kunt onderhouden. Met Azure Monitor-Logboeken kunt u [logboek query's](../azure-monitor/logs/log-query-overview.md) maken die u helpen bij het verzamelen en controleren van deze gegevens. U kunt [deze diagnostische gegevens ook gebruiken met andere Azure-Services](#extend-data), zoals Azure Storage en Azure Event hubs.

Als u logboek registratie wilt instellen voor uw logische app, kunt u [log Analytics inschakelen wanneer u uw logische app maakt](#logging-for-new-logic-apps), of u kunt [de Logic apps-beheer oplossing](#install-management-solution) in uw log Analytics-werk ruimte installeren voor bestaande Logic apps. Deze oplossing biedt geaggregeerde gegevens voor uw logische app-uitvoeringen en bevat specifieke details zoals status, uitvoerings tijd, status van opnieuw verzenden en correlatie-Id's. [Stel vervolgens Azure monitor-logboeken](#set-up-resource-logs)in om logboek registratie in te scha kelen en query's te maken voor deze informatie.

In dit artikel wordt beschreven hoe u Log Analytics kunt inschakelen voor nieuwe Logic apps en bestaande Logic apps, hoe u de Logic Apps beheer oplossing kunt installeren en instellen, en hoe u query's kunt instellen en maken voor Azure Monitor Logboeken.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, hebt u een [log Analytics-werk ruimte](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)nodig. Als u geen werk ruimte hebt, leert u [hoe u een log Analytics-werk ruimte maakt](../azure-monitor/logs/quick-create-workspace.md).

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>Log Analytics inschakelen voor nieuwe Logic apps

U kunt Log Analytics inschakelen wanneer u uw logische app maakt.

1. Ga als volgt te werk in het [Azure Portal](https://portal.azure.com)in het deel venster waar u de gegevens voor de logische app hebt gemaakt:

   1. Selecteer **aan** onder **log Analytics**.

   1. Selecteer in de lijst **log Analytics-werk ruimte** de werk ruimte waar u de gegevens van de logische app-uitvoeringen wilt verzenden.

      ![Informatie over logische app opgeven](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      Nadat u deze stap hebt voltooid, maakt Azure uw logische app, die nu aan uw Log Analytics-werk ruimte is gekoppeld. Met deze stap wordt ook de oplossing voor Logic Apps beheer automatisch geïnstalleerd in uw werk ruimte.

1. Selecteer **Maken** als u klaar bent.

1. Nadat u uw logische app hebt uitgevoerd, [gaat u verder met deze stappen](#view-logic-app-runs)om uw logische app-uitvoeringen weer te geven.

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>Logic Apps-beheer oplossing installeren

Als u Log Analytics hebt ingeschakeld tijdens het maken van uw logische app, slaat u deze stap over. U hebt de Logic Apps-beheer oplossing al geïnstalleerd in uw Log Analytics-werk ruimte.

1. Voer in het zoekvak van de [Azure Portal](https://portal.azure.com)in `log analytics workspaces` en selecteer vervolgens **log Analytics werk ruimten**.

   ![Selecteer ' Log Analytics werk ruimten '](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. Selecteer onder **log Analytics werk ruimten** uw werk ruimte.

   ![Uw Log Analytics-werk ruimte selecteren](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. Selecteer in het deel venster **overzicht** onder **aan de slag met log Analytics**  >  **bewakings oplossingen configureren** de optie **oplossingen weer geven**.

   ![Selecteer in het deel venster Overzicht de optie oplossingen weer geven.](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. Selecteer onder **overzicht** de optie **toevoegen**.

   ![Voeg in het deel venster Overzicht een nieuwe oplossing toe](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. Nadat de **Marketplace** is geopend, voert u in het zoekvak in `logic apps management` en selecteert u **Logic apps beheer**.

   ![Selecteer ' Logic Apps-beheer ' in Marketplace](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. Selecteer **maken** in het deel venster beschrijving van oplossing.

   ![Selecteer ' maken ' om de oplossing ' Logic Apps-beheer ' toe te voegen](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. Controleer en bevestig de Log Analytics-werk ruimte waar u de oplossing wilt installeren en selecteer opnieuw **maken** .

   ![Selecteer ' maken ' voor ' Logic Apps-beheer '](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   Nadat Azure de oplossing heeft geïmplementeerd voor de Azure-resource groep met uw Log Analytics-werk ruimte, wordt de oplossing weer gegeven in het deel venster samen vatting van de werk ruimte.

   ![Deel venster werkruimte overzicht](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Azure Monitor-logboeken instellen

Wanneer u gegevens over runtime-gebeurtenissen en-gegevens in [Azure monitor logboeken](../azure-monitor/logs/data-platform-logs.md)opslaat, kunt u [logboek query's](../azure-monitor/logs/log-query-overview.md) maken waarmee u deze informatie vindt en kunt controleren.

> [!NOTE]
> Nadat u de diagnostische instellingen hebt ingeschakeld, kunnen de diagnostische gegevens niet Maxi maal 30 minuten in de logboeken op het opgegeven doel worden geplaatst, zoals Log Analytics, Event Hub of opslag account. Deze vertraging betekent dat er mogelijk geen diagnostische gegevens uit deze tijds periode bestaan die u kunt controleren. Voltooide gebeurtenissen en [bijgehouden eigenschappen](#extend-data) worden mogelijk gedurende 10-15 minuten niet in uw log Analytics-werk ruimte weer gegeven.

1. Zoek en selecteer uw logische app in de [Azure Portal](https://portal.azure.com).

1. Klik in het menu van de logische app onder **bewaking** op **Diagnostische instellingen**  >  **Diagnostische instelling toevoegen**.

   ![Onder bewaking selecteert u Diagnostische instellingen > diagnostische instelling toevoegen.](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. Voer de volgende stappen uit om de instelling te maken:

   1. Geef een naam op voor de instelling.

   1. Selecteer **verzenden naar log Analytics**.

   1. Selecteer bij **abonnement** het Azure-abonnement dat is gekoppeld aan uw log Analytics-werk ruimte.

   1. Voor **log Analytics werk ruimte** selecteert u de werk ruimte die u wilt gebruiken.

   1. Selecteer onder **logboek** de categorie **WorkflowRuntime** , waarmee de gebeurtenis categorie wordt opgegeven die u wilt opnemen.

   1. Als u alle metrische gegevens wilt selecteren, selecteert u onder **metrische gegevens** **AllMetrics**.

   1. Selecteer **Opslaan** als u klaar bent.

   Bijvoorbeeld:

   ![Log Analytics werk ruimte en gegevens selecteren voor logboek registratie](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>Uitvoerings status van de logische app weer geven

Wanneer de logische app wordt uitgevoerd, kunt u de gegevens over die uitvoeringen bekijken in uw Log Analytics-werk ruimte.

1. Zoek in de [Azure Portal](https://portal.azure.com)uw log Analytics-werk ruimte en open deze.

1. Selecteer in het menu van de werk ruimte de optie **samen vatting van werk ruimte**  >  **Logic apps beheer**.

   ![Uitvoerings status en aantal van logische app](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > Als de tegel Logic Apps beheer niet meteen resultaten weergeeft na een uitvoering, selecteert u **vernieuwen** of wacht u een korte tijd voordat u het opnieuw probeert.

   Hier worden de logische app-uitvoeringen gegroepeerd op naam of op uitvoerings status. Deze pagina bevat ook details over fouten in acties of triggers voor uitvoeringen van de logische app.

   ![Status samenvatting voor de uitvoeringen van logische apps](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. Als u alle uitvoeringen voor een specifieke logische app of status wilt weer geven, selecteert u de rij voor die logische app of status.

   Hier volgt een voor beeld waarin alle uitvoeringen voor een specifieke logische app worden weer gegeven:

   ![Uitvoeringen en status van logische apps weer geven](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   Voor acties waarbij u [bijgehouden eigenschappen instelt](#extend-data), kunt u deze eigenschappen ook weer geven door **weer gave** te selecteren in de kolom **bijgehouden eigenschappen** . Gebruik het kolom filter om de bijgehouden eigenschappen te doorzoeken.

   ![Bijgehouden eigenschappen voor een logische app weer geven](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

1. Als u uw resultaten wilt filteren, kunt u zowel client-side als server-side filters uitvoeren.

   * **Filter aan client zijde**: Selecteer voor elke kolom de gewenste filters, bijvoorbeeld:

     ![Voor beeld van kolom filters](./media/monitor-logic-apps-log-analytics/filters.png)

   * **Filter aan de server zijde**: als u een bepaald tijd venster wilt selecteren of als u het aantal uitvoeringen wilt beperken dat wordt weer gegeven, gebruikt u het bereik besturings element boven aan de pagina. Standaard worden slechts 1.000 records per keer weer gegeven.

     ![Het tijd venster wijzigen](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. Als u alle acties en de bijbehorende Details voor een specifieke uitvoering wilt weer geven, selecteert u de rij voor de uitvoering van een logische app.

   Hier volgt een voor beeld waarin alle acties en triggers worden weer gegeven voor het uitvoeren van een specifieke logische app:

   ![Acties voor het uitvoeren van een logische app weer geven](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>Diagnostische gegevens verzenden naar Azure Storage en Azure Event Hubs

Naast Azure Monitor-Logboeken kunt u uitbreiden hoe u de diagnostische gegevens van uw logische app gebruikt met andere Azure-Services, bijvoorbeeld:

* [Azure-resourcelogboeken archiveren naar opslagaccount](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)
* [Azure-platform logboeken streamen naar Azure Event Hubs](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

U kunt vervolgens realtime-bewaking krijgen door telemetrie en analyses uit andere services te gebruiken, zoals [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) en [Power bi](../azure-monitor/visualize/powerbi.md). Bijvoorbeeld:

* [Gegevens streamen van Event Hubs naar Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analyseer streaminggegevens met Stream Analytics en maak een real-time analyse dashboard in Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Zorg ervoor dat u eerst [een Azure-opslag account maakt](../storage/common/storage-account-create.md) of [een Azure-Event hub maakt](../event-hubs/event-hubs-create.md)op basis van de locaties waar u Diagnostische gegevens wilt verzenden. U kunt vervolgens de doelen selecteren waarnaar u de gegevens wilt verzenden. Bewaar perioden zijn alleen van toepassing wanneer u een opslag account gebruikt.

![Gegevens verzenden naar een Azure Storage-account of Event Hub](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Diagnostische gebeurtenissen Azure Monitor

Elke diagnostische gebeurtenis heeft details over uw logische app en die gebeurtenis, bijvoorbeeld de status, start tijd, eind tijd, enzovoort. Voor het programmatisch instellen van bewaking, tracering en logboek registratie kunt u deze informatie gebruiken met de [rest API voor Azure Logic apps](/rest/api/logic) en de [rest API voor Azure monitor](../azure-monitor/essentials/metrics-supported.md#microsoftlogicworkflows). U kunt ook de `clientTrackingId` Eigenschappen en gebruiken `trackedProperties` , die worden weer gegeven in 

* `clientTrackingId`: Als u dit niet opgeeft, wordt deze ID automatisch door Azure gegenereerd en worden gebeurtenissen gecorreleerd in een logische app-uitvoering, inclusief alle geneste werk stromen die vanuit de logische app worden aangeroepen. U kunt deze ID hand matig opgeven in een trigger door een `x-ms-client-tracking-id` header met uw aangepaste ID-waarde in de trigger aanvraag door te geven. U kunt een trigger voor een aanvraag, een HTTP-trigger of een webhook gebruiken.

* `trackedProperties`: Voor het volgen van invoer of uitvoer in diagnostische gegevens kunt u een `trackedProperties` sectie aan een actie toevoegen met behulp van de Logic app Designer of rechtstreeks in de JSON-definitie van uw logische app. In bijgehouden eigenschappen kunnen alleen de invoer en uitvoer van één actie worden bijgehouden, maar u kunt de `correlation` Eigenschappen van gebeurtenissen gebruiken om te correleren over acties in een uitvoering. Als u meer dan één eigenschap wilt bijhouden, voegt u een of meer eigenschappen toe `trackedProperties` aan de sectie en de eigenschappen die u aan de actie definitie wilt toevoegen.

  Hier volgt een voor beeld waarin wordt getoond hoe de definitie van de variabele-actie voor **initialisatie** bijgehouden eigenschappen bevat van de invoer van de actie, waarbij de invoer een matrix is, niet een record.

  ``` json
  {
     "Initialize_variable": {
        "type": "InitializeVariable",
        "inputs": {
           "variables": [
              {
                 "name": "ConnectorName", 
                 "type": "String", 
                 "value": "SFTP-SSH" 
              }
           ]
        },
        "runAfter": {},
        "trackedProperties": { 
           "myTrackedPropertyName": "@action().inputs.variables[0].value"
        }
     }
  }
  ```

  In dit voor beeld worden meerdere bijgehouden eigenschappen weer gegeven:

  ``` json
  "HTTP": {
     "type": "Http",
     "inputs": {
        "body": "@triggerBody()",
        "headers": {
           "Content-Type": "application/json"
        },
        "method": "POST",
        "uri": "http://store.fabrikam.com",
     },
     "runAfter": {},
     "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
     }
  }
  ```

In dit voor beeld ziet u hoe de `ActionCompleted` gebeurtenis de `clientTrackingId` kenmerken en bevat `trackedProperties` :

```json
{
   "time": "2016-07-09T17:09:54.4773148Z",
   "workflowId": "/subscriptions/XXXXXXXXXXXXXXX/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp",
   "resourceId": "/subscriptions/<subscription-ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp/runs/<run-ID>/actions/Http",
   "category": "WorkflowRuntime",
   "level": "Information",
   "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
   "properties": {
      "$schema": "2016-06-01",
      "startTime": "2016-07-09T17:09:53.4336305Z",
      "endTime": "2016-07-09T17:09:53.5430281Z",
      "status": "Succeeded",
      "code": "OK",
      "resource": {
         "subscriptionId": "<subscription-ID>",
         "resourceGroupName": "MyResourceGroup",
         "workflowId": "<logic-app-workflow-ID>",
         "workflowName": "MyLogicApp",
         "runId": "08587361146922712057",
         "location": "westus",
         "actionName": "Http"
      },
      "correlation": {
         "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
         "clientTrackingId": "<my-custom-tracking-ID>"
      },
      "trackedProperties": {
         "myTrackedPropertyName": "<value>"
      }
   }
}
```

## <a name="next-steps"></a>Volgende stappen

* [Bewakings- en traceringsquery's maken](../logic-apps/create-monitoring-tracking-queries.md)
* [B2B-berichten bewaken met Azure Monitor-logboeken](../logic-apps/monitor-b2b-messages-log-analytics.md)
