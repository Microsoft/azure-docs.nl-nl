---
title: Activering van complexe acties met Azure Monitor waarschuwingen
description: Meer informatie over het maken van een actie voor een logische app voor het verwerken van Azure Monitor-waarschuwingen.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: f1e81dca6926ae9f57e428eb1cef761c588a78b6
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029842"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Complexe acties met Azure Monitor waarschuwingen activeren

Dit artikel laat u zien hoe u een logische app kunt instellen en activeren om een conversatie te maken in micro soft teams wanneer een waarschuwing wordt geactiveerd.

## <a name="overview"></a>Overzicht

Wanneer een Azure Monitor waarschuwing wordt geactiveerd, wordt een [actie groep](./action-groups.md)aangeroepen. Met actie groepen kunt u een of meer acties activeren om anderen op de hoogte te stellen van een waarschuwing en deze ook te herstellen.

Het algemene proces is:

-   Maak de logische app voor het respectieve waarschuwings type.

-   Importeer een nettolading voor een steek proef voor het betreffende waarschuwings type in de logische app.

-   Definieer het gedrag van de logische app.

-   Kopieer het HTTP-eind punt van de logische app naar een Azure-actie groep.

Het proces is vergelijkbaar als u wilt dat de logische app een andere actie uitvoert.

## <a name="create-an-activity-log-alert-administrative"></a>Een waarschuwing voor een activiteiten logboek maken: beheer

1. [Maak een logische app](~/articles/logic-apps/quickstart-create-first-logic-app-workflow.md).

1.  Selecteer de trigger: **Wanneer een HTTP-aanvraag wordt ontvangen**.

1. Selecteer in het dialoogvenster **Wanneer een HTTP-aanvraag wordt ontvangen** de optie **Voorbeeldpayload gebruiken om schema te genereren**.

    ![Scherm opname van het dialoog venster wanneer een H T/m P-aanvraag wordt weer gegeven en de optie voor beeld-Payload gebruiken om een schema te genereren is geselecteerd. ](~/articles/app-service/media/tutorial-send-email/generate-schema-with-payload.png)

1.  Kopieer en plak de volgende voor beeld-nettolading in het dialoog venster:

    ```json
        {
            "schemaId&quot;: &quot;Microsoft.Insights/activityLogs&quot;,
            &quot;data&quot;: {
                &quot;status&quot;: &quot;Activated&quot;,
                &quot;context&quot;: {
                &quot;activityLog&quot;: {
                    &quot;authorization&quot;: {
                    &quot;action&quot;: &quot;microsoft.insights/activityLogAlerts/write&quot;,
                    &quot;scope&quot;: &quot;/subscriptions/…&quot;
                    },
                    &quot;channels&quot;: &quot;Operation&quot;,
                    &quot;claims&quot;: &quot;…&quot;,
                    &quot;caller&quot;: &quot;logicappdemo@contoso.com&quot;,
                    &quot;correlationId&quot;: &quot;91ad2bac-1afa-4932-a2ce-2f8efd6765a3&quot;,
                    &quot;description&quot;: &quot;&quot;,
                    &quot;eventSource&quot;: &quot;Administrative&quot;,
                    &quot;eventTimestamp&quot;: &quot;2018-04-03T22:33:11.762469+00:00&quot;,
                    &quot;eventDataId&quot;: &quot;ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0&quot;,
                    &quot;level&quot;: &quot;Informational&quot;,
                    &quot;operationName&quot;: &quot;microsoft.insights/activityLogAlerts/write&quot;,
                    &quot;operationId&quot;: &quot;61f59fc8-1442-4c74-9f5f-937392a9723c&quot;,
                    &quot;resourceId&quot;: &quot;/subscriptions/…&quot;,
                    &quot;resourceGroupName&quot;: &quot;LOGICAPP-DEMO&quot;,
                    &quot;resourceProviderName&quot;: &quot;microsoft.insights&quot;,
                    &quot;status&quot;: &quot;Succeeded&quot;,
                    &quot;subStatus&quot;: &quot;&quot;,
                    &quot;subscriptionId&quot;: &quot;…&quot;,
                    &quot;submissionTimestamp&quot;: &quot;2018-04-03T22:33:36.1068742+00:00&quot;,
                    &quot;resourceType&quot;: &quot;microsoft.insights/activityLogAlerts&quot;
                }
                },
                &quot;properties&quot;: {}
            }
        }
    ```

1. In de **Logic apps Designer** wordt een pop-upvenster weer gegeven om u eraan te herinneren dat de aanvraag die naar de logische app wordt verzonden **, de content-type-** header moet instellen op **Application/JSON**. Sluit het pop-upvenster. De Azure Monitor waarschuwing stelt de koptekst in.

    ![De content-type-header instellen](media/action-groups-logic-app/content-type-header.png &quot;De content-type-header instellen")

1. Selecteer **+** **nieuwe stap** en kies vervolgens **een actie toevoegen**.

    ![Een actie toevoegen](media/action-groups-logic-app/add-action.png "Een actie toevoegen")

1. Zoek en selecteer de micro soft teams-connector. Kies de actie **micro soft teams-post bericht** .

    ![Micro soft teams-acties](media/action-groups-logic-app/microsoft-teams-actions.png "Micro soft teams-acties")

1. Configureer de micro soft teams-actie. De **Logic apps Designer** vraagt u om te verifiëren bij uw werk-of school account. Kies de **Team-ID** en de **kanaal-id** waarnaar het bericht moet worden verzonden.

13. Configureer het bericht met een combi natie van statische tekst en verwijzingen naar de \<fields\> in de dynamische inhoud. Kopieer en plak de volgende tekst in het **bericht** veld:

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Zoek en vervang vervolgens de \<fields\> met dynamische inhouds Tags met dezelfde naam.

    > [!NOTE]
    > Er zijn twee dynamische velden met de naam **status**. Voeg beide velden toe aan het bericht. Gebruik het veld in de **activityLog** -eigenschappen verzameling en verwijder het andere veld. Beweeg de muis aanwijzer over het veld **status** om de volledig gekwalificeerde veld verwijzing te zien, zoals wordt weer gegeven in de volgende scherm afbeelding:

    ![Micro soft teams-actie: een bericht plaatsen](media/action-groups-logic-app/teams-action-post-message.png "Micro soft teams-actie: een bericht plaatsen")

1. Selecteer op de pagina **Logic apps Designer** de optie **Opslaan** om uw logische app op te slaan.

1. Open uw bestaande actie groep en voeg een actie toe om te verwijzen naar de logische app. Als u geen bestaande actie groep hebt, raadpleegt u [actie groepen maken en beheren in de Azure Portal](./action-groups.md) om er een te maken. Vergeet niet om uw wijzigingen op te slaan.

    ![De actie groep bijwerken](media/action-groups-logic-app/update-action-group.png "De actie groep bijwerken")

De volgende keer dat een waarschuwing uw actie groep aanroept, wordt uw logische app aangeroepen.

## <a name="create-a-service-health-alert"></a>Een service status waarschuwing maken

Azure Service Health vermeldingen maken deel uit van het activiteiten logboek. Het proces voor het maken van de waarschuwing is vergelijkbaar met het [maken van een waarschuwing voor een activiteiten logboek](#create-an-activity-log-alert-administrative), maar met een paar wijzigingen:

- De stappen 1 tot en met 3 zijn hetzelfde.
- Gebruik voor stap 4 de volgende voor beeld-nettolading voor de HTTP-aanvraag trigger:

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "...",
                        "service": "...",
                        "region": "Global",
                        "communication": "...",
                        "incidentType": "Incident",
                        "trackingId": "...",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{\"ImpactedRegions\"}]",
                        "defaultLanguageTitle": "...",
                        "defaultLanguageContent": "...",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "...",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  Stap 5 en 6 zijn hetzelfde.
-  Gebruik het volgende proces voor stap 7 tot en met 11:

   1. Selecteer **+** **nieuwe stap** en kies vervolgens **een voor waarde toevoegen**. Stel de volgende voor waarden in zodat de logische app alleen wordt uitgevoerd wanneer de invoer gegevens overeenkomen met de waarden hieronder.  Wanneer u de versie waarde in het tekstvak invoert, plaatst u een aanhalings teken ("0.1.1") om ervoor te zorgen dat deze wordt geëvalueerd als een reeks en niet als een numeriek type.  Het systeem geeft de aanhalings tekens niet weer als u terugkeert naar de pagina, maar de onderliggende code blijft het teken reeks type behouden.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      ![' Service Health Payload-voor waarde '](media/action-groups-logic-app/service-health-payload-condition.png "Service Health Payload-voor waarde")

   1. In de voor waarde **Indien waar** , volg de instructies in stap 11 tot en met 13 in [een waarschuwing voor het maken van een activiteiten logboek](#create-an-activity-log-alert-administrative) om de micro soft teams-actie toe te voegen.

   1. Definieer het bericht met behulp van een combi natie van HTML en dynamische inhoud. Kopieer en plak de volgende inhoud in het veld **bericht** . Vervang de `[incidentType]` `[trackingID]` velden,, en door de `[title]` `[communication]` labels van de dynamische inhoud met dezelfde naam:

       ```html
       <p>
       <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
       <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
       <strong>Title:</strong>&nbsp;[title]</p>
       <p>
       <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.</a>
       </p>
       <p>[communication]</p>
       ```

       !["Service Health bewerking voor het na ' True '](media/action-groups-logic-app/service-health-true-condition-post-action.png "Post actie voor Service Health waar")

   1. Geef voor de voor waarde **indien onwaar** een nuttig bericht op:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       ![De actie ' Service Health False voor waarde na '](media/action-groups-logic-app/service-health-false-condition-post-action.png "Post actie Service Health False-voor waarde")

- Stap 15 is hetzelfde. Volg de instructies om uw logische app op te slaan en uw actie groep bij te werken.

## <a name="create-a-metric-alert"></a>Een waarschuwing voor metrische gegevens maken

Het proces voor het maken van een metrische waarschuwing is vergelijkbaar met het [maken van een waarschuwing voor een activiteiten logboek](#create-an-activity-log-alert-administrative), maar met een paar wijzigingen:

- De stappen 1 tot en met 3 zijn hetzelfde.
- Gebruik voor stap 4 de volgende voor beeld-nettolading voor de HTTP-aanvraag trigger:

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "...",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "...",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "...",
        "portalLink": "..."
        },
        "properties": {}
    }
    }
    ```

- Stap 5 en 6 zijn hetzelfde.
- Gebruik het volgende proces voor stap 7 tot en met 11:

  1. Selecteer **+** **nieuwe stap** en kies vervolgens **een voor waarde toevoegen**. Stel de volgende voor waarden in zodat de logische app alleen wordt uitgevoerd wanneer de invoer gegevens overeenkomen met deze waarden. Wanneer u de versie waarde in het tekstvak invoert, plaatst u aanhalings tekens ("2,0") om ervoor te zorgen dat deze wordt geëvalueerd als een teken reeks en niet als numeriek type.  Het systeem geeft de aanhalings tekens niet weer als u terugkeert naar de pagina, maar de onderliggende code blijft het teken reeks type behouden. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       ![Voor waarde voor nettolading van metrische waarschuwing](media/action-groups-logic-app/metric-alert-payload-condition.png "Toestand van metrische waarschuwing nettolading")

  1. Voeg in de voor waarde **Indien waar** , een **voor elke** lus en de micro soft teams-actie toe. Definieer het bericht met behulp van een combi natie van HTML en dynamische inhoud.

      !["Metrische waarschuwing True voor waarde post actie"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "Post actie metrische waarschuwing True voor waarde")

  1. In de voor waarde **indien onwaar** definieert u een micro soft teams-actie om te communiceren dat de metrische waarschuwing niet overeenkomt met de verwachtingen van de logische app. De JSON-nettolading bevatten. U ziet hoe u kunt verwijzen naar de `triggerBody` dynamische inhoud in de `json()` expressie.

      !["Metrische waarschuwing ONWAAR voor waarde na actie bericht"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "Post actie waarschuwing metrische waarde ONWAAR")

- Stap 15 is hetzelfde. Volg de instructies om uw logische app op te slaan en uw actie groep bij te werken.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Andere toepassingen aanroepen behalve micro soft teams
Logic Apps heeft een aantal verschillende connectors waarmee u acties kunt activeren in een breed scala aan toepassingen en data bases. Toegestane vertraging, SQL Server, Oracle, Sales Force, zijn slechts enkele voor beelden. Zie [Logic app-connectors](../../connectors/apis-list.md)voor meer informatie over connectors.  

## <a name="next-steps"></a>Volgende stappen
* Bekijk een [overzicht van de waarschuwingen voor Azure-activiteiten logboeken](./alerts-overview.md) en lees hoe u waarschuwingen kunt ontvangen.  
* Meer informatie over het [configureren van waarschuwingen wanneer een Azure service Health-melding wordt geplaatst](../../service-health/alerts-activity-log-service-notifications-portal.md).
* Meer informatie over [actiegroepen](./action-groups.md).
