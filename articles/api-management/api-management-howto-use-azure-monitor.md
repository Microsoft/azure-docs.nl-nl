---
title: "Zelfstudie: in Azure API Management gepubliceerde API's bewaken | Microsoft Docs"
description: Volg de stappen in deze zelfstudie voor meer informatie over het gebruik van metrische gegevens, waarschuwingen, activiteitenlogboeken en resourcelogboeken om uw API's te controleren in Azure API Management.
services: api-management
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: 2317e61111c3ad328e8f112e7d9567f3f5d47990
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379340"
---
# <a name="tutorial-monitor-published-apis"></a>Zelfstudie: Gepubliceerde API's bewaken

Met Azure Monitor kunt u visualiseren, query's uitvoeren, routeren, archiveren en actie ondernemen op basis van de metrische gegevens en logboeken vanuit uw Azure API Management-service.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Metrische gegevens van uw API weergeven 
> * Een waarschuwingsregel instellen 
> * Activiteitenlogboeken bekijken
> * Resourcelogboeken inschakelen en weergeven

## <a name="prerequisites"></a>Vereisten

+ Informatie over de [terminologie van Azure API Management](api-management-terminology.md).
+ Voltooi de volgende snelstartgids: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md).
+ Voltooi ook de volgende zelfstudie: [Uw eerste API importeren en publiceren](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>Metrische gegevens van uw API's weergeven

API Management geeft elke minuut [metrische gegevens](../azure-monitor/platform/data-platform-metrics.md) vrij, waardoor u in vrijwel realtime inzicht hebt in de status van uw API's. Hierna staan de twee meestgebruikte metrische gegevens. Zie [ondersteunde metrische gegevens](../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice) voor een lijst met alle beschikbare metrische gegevens.

* **Capaciteit**: hiermee kunt u beslissingen nemen over het uitvoeren van een up- of downgrade van uw APIM-services. Dit gegeven komt elke minuut beschikbaar en is een weerspiegeling van de capaciteit van de gateway ten tijde van de export. Het gegevensbereik loopt van 0 tot 100 en wordt berekend op basis van gateway-resources als CPU- en geheugengebruik.
* **Aanvragen**: helpt u bij het analyseren van API-verkeer via uw API Management-services. De meetwaarde wordt iedere minuut verzonden en rapporteert het aantal gateway-aanvragen met dimensies, inclusief responscodes, locatie, hostnaam en fouten. 

> [!IMPORTANT]
> De volgende metrische gegevens zijn per mei 2019 afgeschaft en zullen in augustus 2023 buiten gebruik worden gesteld: Totaal aantal gateway-aanvragen, geslaagde gateway-aanvragen, niet-gemachtigde gateway-aanvragen, mislukte gateway-aanvragen, andere gateway-aanvragen. Migreer naar de metrische gegevens voor aanvragen met gelijkwaardige functionaliteit.

:::image type="content" source="media/api-management-howto-use-azure-monitor/apim-monitor-metrics.png" alt-text="Schermopname van metrische gegevens in API Management-overzicht":::

Metrische gegevens openen:

1. Blader in [Azure Portal](https://portal.azure.com) naar uw API Management-exemplaar. Bekijk op de pagina **Overzicht** de belangrijkste metrische gegevens voor uw API's.
1. Als u de metrische gegevens grondig wilt onderzoeken, selecteert u **Metrische gegevens** in het menu onderaan de pagina.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-metrics-blade.png" alt-text="Schermopname van het item Metrische gegevens in het menu Bewaking":::

1. Selecteer in de vervolgkeuzelijst de gewenste metrische gegevens. Bijvoorbeeld **Aanvragen**. 
1. De grafiek toont het totale aantal API-aanroepen.
1. De grafiek kan worden gefilterd met behulp van de dimensies van de meetwaarde **Aanvragen**. Selecteer bijvoorbeeld **Filter toevoegen**, selecteer **Categorie Antwoordstatus van back-end** en voer 500 in als de waarde. De grafiek toont nu het aantal aanvragen dat is mislukt in de API-back-end.   

## <a name="set-up-an-alert-rule"></a>Een waarschuwingsregel instellen 

U kunt [waarschuwingen](../azure-monitor/platform/alerts-metric-overview.md) ontvangt op basis van metrische gegevens en activiteitenlogboeken. Met Azure Monitor kunt u een [waarschuwing configureren](../azure-monitor/platform/alerts-metric.md) om het volgende te doen bij activering:

* Een e-mailmelding verzenden
* Een webhook aanroepen
* Een logische Azure-app aanroepen

Een voorbeeld van een waarschuwingsregel configureren op basis van een aangevraagd metrisch gegeven:

1. Blader in [Azure Portal](https://portal.azure.com) naar uw API Management-exemplaar.
1. Selecteer **Waarschuwingen** in de menubalk onderaan de pagina.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/alert-menu-item.png" alt-text="Schermopname van de optie Waarschuwingen in het menu Bewaking":::

1. Selecteer **+ Nieuwe waarschuwingsregel**.
1. In het venster **Waarschuwingsregel maken** **Voorwaarde selecteren**.
1. In het venster **Signaallogica configureren**:
    1. Selecteer in **Signaaltype** **Metrische gegevens**.
    1. Selecteer in **Signaalnaam** **Aanvragen**.
    1. Selecteer in **Splitsen op dimensies** in **Dimensienaam** **Categorie antwoordcode van gateway**.
    1. Selecteer in **Dimensiewaarden** **4xx** voor clientfouten, zoals niet-geautoriseerde of ongeldige aanvragen.
    1. Geef in **Waarschuwingslogica** een drempelwaarde op waarna de waarschuwing moet worden geactiveerd en selecteer **Gereed**.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/threshold.png" alt-text="Schermopname van het venster Signaallogica configureren":::

1. Selecteer een bestaande actiegroep of maak een nieuwe groep. In het volgende voorbeeld wordt een nieuwe actiegroep gemaakt. Er wordt een e-mailmelding verzonden naar admin@contoso.com. 

    :::image type="content" source="media/api-management-howto-use-azure-monitor/action-details.png" alt-text="Schermopname van meldingen voor nieuwe actiegroep":::

1. Voer een naam en beschrijving van de waarschuwingsregel in en selecteer het ernstniveau. 
1. Selecteer **Waarschuwingsregel maken**.
1. Test nu de waarschuwingsregel door de Conference API aan te roepen zonder een API-sleutel. Bijvoorbeeld:

    ```bash
    curl GET https://apim-hello-world.azure-api.net/conference/speakers HTTP/1.1 
    ```

    Er wordt een waarschuwing geactiveerd op basis van de evaluatieperiode en er wordt een e-mail verzonden naar admin@contoso.com. 

    Waarschuwingen worden ook weergegeven op de pagina **Waarschuwingen** voor het API Management-exemplaar.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/portal-alerts.png" alt-text="Schermopname van waarschuwingen in de portal":::

## <a name="activity-logs"></a>Activiteitenlogboeken

Activiteitenlogboeken bieden inzicht in de bewerkingen die zijn uitgevoerd voor uw API Management-services. Met activiteitenlogboeken kunt u het 'wat, wie en wanneer' bepalen voor schrijfbewerkingen (PUT, POST, DELETE) die voor uw API Management-services worden uitgevoerd.

> [!NOTE]
> Activiteitenlogboeken bevatten geen lees-bewerkingen (GET) of bewerkingen die zijn uitgevoerd in de Azure-portal of met behulp van de oorspronkelijke beheer-API's.

U kunt activiteitenlogboeken in uw API Management-service openen. U kunt alle logboeken van al uw Azure-resources in Azure Monitor openen. 

:::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs.png" alt-text="Schermopname van activiteitenlogboek in portal":::

Het activiteitenlogboek weergeven:

1. Blader in [Azure Portal](https://portal.azure.com) naar uw API Management-exemplaar.

1. Selecteer **Activiteitenlogboek**.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs-blade.png" alt-text="Schermopname van het item Activiteitenlogboek in het menu Bewaking":::
1. Selecteer het gewenste filterbereik en vervolgens **Toepassen**.

## <a name="resource-logs"></a>Resourcelogboeken

Resourcelogboeken bieden uitgebreide informatie over bewerkingen en fouten die belangrijk zijn voor zowel controles als het oplossen van problemen. Resourcelogboeken verschillen van activiteitenlogboeken. Het activiteitenlogboek biedt inzicht in de bewerkingen die zijn uitgevoerd op uw Azure-resources. Resourcelogboeken bieden inzicht in bewerkingen die door de resources zelf zijn uitgevoerd.

Ga als volgt te werk om resourcelogboeken te configureren:

1. Blader in [Azure Portal](https://portal.azure.com) naar uw API Management-exemplaar.
2. Selecteer **Diagnostische instellingen**.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-diagnostic-logs-blade.png" alt-text="Schermopname van het item Diagnostische instellingen in het menu Bewaking":::

1. Selecteer **+ Diagnostische instelling toevoegen**.
1. Selecteer de logboeken of metrische gegevens die u wilt verzamelen.

   U kunt resourcelogboeken samen met metrische gegevens naar een opslagaccount archiveren, ze naar een Event Hub streamen of ze naar een Log Analytics-werkruimte verzenden. 

Raadpleeg [Diagnostische instellingen maken om logboeken en metrische gegevens van het platform te verzenden naar verschillende bestemmingen](../azure-monitor/platform/diagnostic-settings.md) voor meer informatie.

## <a name="view-diagnostic-data-in-azure-monitor"></a>Diagnostische gegevens weergeven in Azure Monitor

Als u het verzamelen van GatewayLogs of metrische gegevens in een Log Analytics-werkruimte inschakelt, kan het enkele minuten duren voordat gegevens worden weergegeven in Azure Monitor. De gegevens weergeven:

1. Blader in [Azure Portal](https://portal.azure.com) naar uw API Management-exemplaar.
1. Selecteer **Logboeken** in het menu onderaan de pagina.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/logs-menu-item.png" alt-text="Schermopname van het item Logboeken in het menu Bewaking":::

Voer query's uit om de gegevens weer te geven. Er worden verschillende [voorbeeldquery's](../azure-monitor/log-query/saved-queries.md) gegeven of u kunt uw eigen query's uitvoeren. Met de volgende query wordt bijvoorbeeld de meest recente 24 uur aan gegevens opgehaald uit de GatewayLogs-tabel:

```kusto
ApiManagementGatewayLogs
| where TimeGenerated > ago(1d) 
```

Raadpleeg voor meer informatie over het gebruik van resourcelogboeken voor API Management:

* [Aan de slag met Azure Monitor Log Analytics](../azure-monitor/log-query/get-started-portal.md) of probeer de [Log Analytics-testomgeving](https://portal.loganalytics.io/demo).

* [Overzicht van logboekquery’s in Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

De volgende JSON geeft een voorbeeldvermelding in GatewayLogs aan voor een succesvolle API-aanvraag. Raadpleeg de [naslaginformatie over schema's](gateway-log-schema-reference.md) voor meer informatie. 

```json
{
    "Level": 4,
    "isRequestSuccess": true,
    "time": "2020-10-14T17:xx:xx.xx",
    "operationName": "Microsoft.ApiManagement/GatewayLogs",
    "category": "GatewayLogs",
    "durationMs": 152,
    "callerIpAddress": "xx.xx.xxx.xx",
    "correlationId": "3f06647e-xxxx-xxxx-xxxx-530eb9f15261",
    "location": "East US",
    "properties": {
        "method": "GET",
        "url": "https://apim-hello-world.azure-api.net/conference/speakers",
        "backendResponseCode": 200,
        "responseCode": 200,
        "responseSize": 41583,
        "cache": "none",
        "backendTime": 87,
        "requestSize": 526,
        "apiId": "demo-conference-api",
        "operationId": "GetSpeakers",
        "apimSubscriptionId": "master",
        "clientTime": 65,
        "clientProtocol": "HTTP/1.1",
        "backendProtocol": "HTTP/1.1",
        "apiRevision": "1",
        "clientTlsVersion": "1.2",
        "backendMethod": "GET",
        "backendUrl": "https://conferenceapi.azurewebsites.net/speakers"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/APIM-HELLO-WORLD"
}
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Metrische gegevens van uw API weergeven
> * Een waarschuwingsregel instellen 
> * Activiteitenlogboeken bekijken
> * Resourcelogboeken inschakelen en weergeven


Ga door naar de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Oproepen traceren](api-management-howto-api-inspector.md)
