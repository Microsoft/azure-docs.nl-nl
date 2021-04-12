---
title: Logboeken voor Azure Web Application firewall bewaken
description: Meer informatie over het inschakelen en beheren van Logboeken en voor Azure Web Application firewall
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 116c12900f015c849c9492ed67bc11d116286c43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102611926"
---
# <a name="resource-logs-for-azure-web-application-firewall"></a>Resource logboeken voor Azure Web Application firewall

U kunt Web Application firewall-bronnen bewaken met behulp van Logboeken. U kunt prestaties, toegang en andere gegevens opslaan of van een resource gebruiken voor bewakings doeleinden.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="diagnostic-logs"></a>Diagnostische logboeken

U kunt verschillende typen logboeken in azure gebruiken om toepassings gateways te beheren en op te lossen. Via de portal kunt u toegang verkrijgen tot sommige van deze logboeken. Alle logboeken kunnen worden geëxtraheerd uit Azure Blob-opslag en worden weer gegeven in verschillende hulpprogram ma's, zoals [Azure monitor logboeken](../../azure-monitor/insights/azure-networking-analytics.md), Excel en Power bi. Meer informatie over de verschillende typen logboeken vindt u in de volgende lijst:

* **Activiteiten logboek**: u kunt [Azure-activiteiten logboeken](../../azure-resource-manager/management/view-activity-logs.md) gebruiken om alle bewerkingen weer te geven die zijn verzonden naar uw Azure-abonnement en hun status. Activiteitenlogboekitems worden standaard verzameld en kunnen in de Azure-portal worden bekeken.
* **Toegang tot resource logboek**: u kunt dit logboek gebruiken om Application Gateway toegangs patronen weer te geven en belang rijke informatie te analyseren. Dit geldt ook voor de IP van de beller, de aangevraagde URL, reactie latentie, retour code en bytes in en uit. Er wordt elke 300 seconden een toegangs logboek verzameld. Dit logboek bevat één record per exemplaar van Application Gateway. Het Application Gateway exemplaar wordt geïdentificeerd door de eigenschap instanceId.
* **Prestatie bronnen logboek**: u kunt dit logboek gebruiken om te zien hoe Application Gateway exemplaren worden uitgevoerd. In dit logboek worden de prestatie gegevens voor elk exemplaar vastgelegd, met inbegrip van het totale aantal geleverde aanvragen, de door Voer in bytes, het totale aantal geleverde aanvragen, de telling van mislukte aanvragen en een gezonde en slechte back-end-instantie. Er wordt elke 60 seconden een prestatie logboek verzameld. Het prestatie logboek is alleen beschikbaar voor de V1-SKU. Voor de v2-SKU gebruikt u [metrische](../../application-gateway/application-gateway-metrics.md) gegevens voor prestaties.
* **Firewall bron logboek**: u kunt dit logboek gebruiken om de aanvragen weer te geven die zijn geregistreerd via de detectie-of preventie modus van een toepassings gateway die is geconfigureerd met de Web Application firewall.

> [!NOTE]
> Logboeken zijn alleen beschikbaar voor resources die zijn geïmplementeerd in het Azure Resource Manager-implementatie model. U kunt geen Logboeken gebruiken voor bronnen in het klassieke implementatie model. Zie het artikel wat is de [implementatie van Resource Manager en de klassieke implementatie](../../azure-resource-manager/management/deployment-models.md) voor een beter inzicht in de twee modellen.

U hebt drie opties voor het opslaan van uw logboeken:

* **Opslagaccount**: opslagaccounts kunnen het best worden gebruikt wanneer logboeken voor een langere periode worden opgeslagen en moeten kunnen worden bekeken wanneer dat nodig is.
* **Event hubs**: Event hubs zijn een uitstekende optie voor het integreren met andere Siem-hulpprogram ma's (Security Information and Event Management) om waarschuwingen over uw resources te krijgen.
* **Azure monitor-logboeken**: Azure monitor-logboeken worden het beste gebruikt voor algemene realtime-bewaking van uw toepassing of voor het bekijken van trends.

### <a name="enable-logging-through-powershell"></a>Logboek registratie via Power shell inschakelen

Activiteitenlogboekregistratie is automatisch ingeschakeld voor elke Resource Manager-resource. U moet logboek registratie van toegang en prestaties inschakelen om te beginnen met het verzamelen van de gegevens die beschikbaar zijn via deze logboeken. Als u logboek registratie wilt inschakelen, gebruikt u de volgende stappen:

1. Noteer de resource-ID van uw opslagaccount waar de logboekgegevens worden opgeslagen. Deze waarde is van het formulier:/Subscriptions/ \<subscriptionId\> /ResourceGroups/ \<resource group name\> /providers/Microsoft.Storage/storageAccounts/ \<storage account name\> . U kunt elk opslagaccount in uw abonnement gebruiken. U kunt de Azure-portal gebruiken om deze informatie te vinden.

    ![Portal: Resource-ID voor opslag account](../media/web-application-firewall-logs/diagnostics1.png)

2. Noteer de resource-ID van uw toepassings gateway waarvoor logboek registratie is ingeschakeld. Deze waarde is van het formulier:/Subscriptions/ \<subscriptionId\> /ResourceGroups/ \<resource group name\> /providers/Microsoft.Network/applicationGateways/ \<application gateway name\> . U kunt de portal gebruiken om deze informatie te vinden.

    ![Portal: Resource-ID voor toepassings gateway](../media/web-application-firewall-logs/diagnostics2.png)

3. Schakel bron logboek registratie in met behulp van de volgende Power shell-cmdlet:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```

> [!TIP]
>Voor activiteiten Logboeken is geen afzonderlijk opslag account vereist. Voor het gebruik van opslag voor toegangs- en prestatielogboeken worden servicekosten in rekening gebracht.

### <a name="enable-logging-through-the-azure-portal"></a>Logboekregistratie inschakelen via de Azure-portal

1. Zoek in het Azure Portal uw resource en selecteer **Diagnostische instellingen**.

   Voor Application Gateway zijn drie logboeken beschikbaar:

   * Access-logboek
   * Prestatie logboek
   * Firewall logboek

2. Selecteer **diagnostiek inschakelen** om gegevens te verzamelen.

   ![Diagnostische gegevens inschakelen][1]

3. De pagina **Diagnostische instellingen** bevat de instellingen voor de resource Logboeken. In dit voor beeld slaat Log Analytics de logboeken op. U kunt ook Event hubs en een opslag account gebruiken om de bron logboeken op te slaan.

   ![Het configuratieproces starten][2]

5. Typ een naam voor de instellingen, bevestig de instellingen en selecteer **Opslaan**.

### <a name="activity-log"></a>Activiteitenlogboek

Azure genereert standaard het activiteiten logboek. De logboeken blijven 90 dagen bewaard in de Azure gebeurtenis logboeken Store. Lees het artikel [gebeurtenissen en activiteiten logboek weer geven](../../azure-resource-manager/management/view-activity-logs.md) voor meer informatie over deze logboeken.

### <a name="access-log"></a>Access-logboek

Het toegangs logboek wordt alleen gegenereerd als u het hebt ingeschakeld op elk Application Gateway-exemplaar, zoals in de voor gaande stappen wordt beschreven. De gegevens worden opgeslagen in het opslag account dat u hebt opgegeven tijdens het inschakelen van de logboek registratie. Elke toegang van Application Gateway wordt geregistreerd in JSON-indeling, zoals wordt weer gegeven in het volgende voor beeld voor v1:

|Waarde  |Beschrijving  |
|---------|---------|
|instanceId     | Application Gateway exemplaar dat de aanvraag heeft verzonden.        |
|Client     | Oorspronkelijk IP-adres voor de aanvraag.        |
|clientPort     | Bron poort voor de aanvraag.       |
|httpMethod     | De HTTP-methode die door de aanvraag wordt gebruikt.       |
|requestUri     | De URI van de ontvangen aanvraag.        |
|RequestQuery     | Door de **server gerouteerd**: het exemplaar van de back-end-pool dat de aanvraag heeft verzonden.</br>**X-AzureApplicationGateway-log-id**: correlatie-id die voor de aanvraag wordt gebruikt. Het kan worden gebruikt om problemen met verkeer op de back-endservers op te lossen. </br>**Server-status**: http-antwoord code die Application Gateway ontvangen van de back-end.       |
|User agent     | Gebruikers agent van de header van de HTTP-aanvraag.        |
|Http status     | Er is een HTTP-status code geretourneerd naar de client van Application Gateway.       |
|httpVersion     | HTTP-versie van de aanvraag.        |
|receivedBytes     | Grootte van ontvangen pakket, in bytes.        |
|sentBytes| Grootte van het verzonden pakket, in bytes.|
|timeTaken| De tijds duur (in milliseconden) die nodig is voor het verwerken van een aanvraag en het antwoord op verzen ding. Dit wordt berekend als het interval van de tijd dat Application Gateway de eerste byte van een HTTP-aanvraag ontvangt naar het tijdstip waarop de bewerking voor het verzenden van het antwoord is voltooid. Het is belang rijk te weten dat het veld Time-Taken meestal de tijd bevat dat de aanvraag-en antwoord pakketten via het netwerk onderweg zijn. |
|sslEnabled| Of communicatie met de back-endservers gebruikte TLS/SSL. Geldige waarden zijn in-en uitgeschakeld.|
|host| De hostnaam waarmee de aanvraag is verzonden naar de back-endserver. Als de hostnaam van de back-end wordt overschreven, wordt deze naam weer gegeven.|
|originalHost| De hostnaam waarmee de aanvraag is ontvangen door de Application Gateway van de client.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "timestamp": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "host": "www.contoso.com",
        "originalHost": "www.contoso.com"
    }
}
```
Voor Application Gateway en WAF v2 bevatten de logboeken nog iets meer informatie:

|Waarde  |Beschrijving  |
|---------|---------|
|instanceId     | Application Gateway exemplaar dat de aanvraag heeft verzonden.        |
|Client     | Oorspronkelijk IP-adres voor de aanvraag.        |
|clientPort     | Bron poort voor de aanvraag.       |
|httpMethod     | De HTTP-methode die door de aanvraag wordt gebruikt.       |
|requestUri     | De URI van de ontvangen aanvraag.        |
|User agent     | Gebruikers agent van de header van de HTTP-aanvraag.        |
|Http status     | Er is een HTTP-status code geretourneerd naar de client van Application Gateway.       |
|httpVersion     | HTTP-versie van de aanvraag.        |
|receivedBytes     | Grootte van ontvangen pakket, in bytes.        |
|sentBytes| Grootte van het verzonden pakket, in bytes.|
|timeTaken| De tijds duur (in milliseconden) die nodig is voor het verwerken van een aanvraag en het antwoord op verzen ding. Dit wordt berekend als het interval van de tijd dat Application Gateway de eerste byte van een HTTP-aanvraag ontvangt naar het tijdstip waarop de bewerking voor het verzenden van het antwoord is voltooid. Het is belang rijk te weten dat het veld Time-Taken meestal de tijd bevat dat de aanvraag-en antwoord pakketten via het netwerk onderweg zijn. |
|sslEnabled| Hiermee wordt aangegeven of communicatie met de back-endservers die TLS gebruiken. Geldige waarden zijn in-en uitgeschakeld.|
|sslCipher| Coderings suite wordt gebruikt voor TLS-communicatie (als TLS is ingeschakeld).|
|sslProtocol| Gebruikt TLS-protocol (als TLS is ingeschakeld).|
|serverRouted| De back-endserver die Application Gateway naar verzendt.|
|serverStatus| HTTP-status code van de back-endserver.|
|serverResponseLatency| Latentie van het antwoord van de back-endserver.|
|host| Het adres dat wordt vermeld in de host-header van de aanvraag.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "appgw_1",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "www.contoso.com",
    }
}
```

### <a name="performance-log"></a>Prestatie logboek

Het prestatie logboek wordt alleen gegenereerd als u het hebt ingeschakeld op elk Application Gateway-exemplaar, zoals in de voor gaande stappen wordt beschreven. De gegevens worden opgeslagen in het opslag account dat u hebt opgegeven tijdens het inschakelen van de logboek registratie. De gegevens van het prestatie logboek worden gegenereerd in intervallen van 1 minuten. Het is alleen beschikbaar voor de V1-SKU. Voor de v2-SKU gebruikt u [metrische](../../application-gateway/application-gateway-metrics.md) gegevens voor prestaties. De volgende gegevens worden geregistreerd:


|Waarde  |Beschrijving  |
|---------|---------|
|instanceId     |  Application Gateway exemplaar waarvoor prestatie gegevens worden gegenereerd. Voor een toepassings gateway met meerdere instanties is er één rij per exemplaar.        |
|healthyHostCount     | Aantal gezonde hosts in de back-end-pool.        |
|unHealthyHostCount     | Aantal beschadigde hosts in de back-end-pool.        |
|requestCount     | Aantal geleverde aanvragen.        |
|latentie | De gemiddelde latentie (in milliseconden) van aanvragen van het exemplaar naar de back-end die de aanvragen verzendt. |
|failedRequestCount| Aantal mislukte aanvragen.|
|doorvoer| Gemiddelde door Voer sinds het laatste logboek, gemeten in bytes per seconde.|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> De latentie wordt berekend op basis van de tijd waarop de eerste byte van de HTTP-aanvraag wordt ontvangen op het moment dat de laatste byte van het HTTP-antwoord wordt verzonden. Het is de som van de verwerkings tijd van de Application Gateway plus de netwerk kosten voor de back-end, plus de tijd die de back-end nodig heeft om de aanvraag te verwerken.

### <a name="firewall-log"></a>Firewall logboek

Het firewall logboek wordt alleen gegenereerd als u het hebt ingeschakeld voor elke toepassings gateway, zoals in de voor gaande stappen wordt beschreven. Het logboek vereist ook dat de Web Application Firewall is geconfigureerd op een toepassings gateway. De gegevens worden opgeslagen in het opslag account dat u hebt opgegeven tijdens het inschakelen van de logboek registratie. De volgende gegevens worden geregistreerd:


|Waarde  |Beschrijving  |
|---------|---------|
|instanceId     | Application Gateway exemplaar waarvoor de firewall gegevens worden gegenereerd. Voor een toepassings gateway met meerdere instanties is er één rij per exemplaar.         |
|Client     |   Oorspronkelijk IP-adres voor de aanvraag.      |
|clientPort     |  Bron poort voor de aanvraag.       |
|requestUri     | De URL van de ontvangen aanvraag.       |
|ruleSetType     | Type regel instellingen. De beschik bare waarde is OWASP.        |
|ruleSetVersion     | Gebruikte versie van regel instellingen. Beschik bare waarden zijn 2.2.9 en 3,0.     |
|ruleId     | De regel-ID van de trigger gebeurtenis.        |
|message     | Gebruikers vriendelijk bericht voor de activerings gebeurtenis. Meer informatie vindt u in de sectie Details.        |
|actie     |  De actie die voor de aanvraag is uitgevoerd. Beschik bare waarden worden geblokkeerd en toegestaan (voor aangepaste regels), overeenkomend (als een regel overeenkomt met een deel van de aanvraag) en gedetecteerd en geblokkeerd (dit zijn beide voor verplichte regels, afhankelijk van of de WAF zich in de detectie-of preventie modus bevindt).      |
|site     | De site waarvoor het logboek is gegenereerd. Momenteel wordt alleen globaal weer gegeven omdat regels globaal zijn.|
|nadere     | Details van de trigger gebeurtenis.        |
|Details. bericht     | Beschrijving van de regel.        |
|Details. gegevens     | Specifieke gegevens gevonden in de aanvraag die overeenkomen met de regel.         |
|Details. bestand     | Configuratie bestand waarin de regel is opgenomen.        |
|Details. regel     | Regel nummer in het configuratie bestand dat de gebeurtenis heeft geactiveerd.       |
|hostnaam   | De hostnaam of het IP-adres van de Application Gateway.    |
|transactionId  | De unieke ID voor een bepaalde trans actie waarmee meerdere regel schendingen kunnen worden gegroepeerd die binnen dezelfde aanvraag zijn opgetreden.   |
|policyId   | De unieke ID van het firewall beleid dat is gekoppeld aan de Application Gateway, listener of het pad.   |
|policyScope    | De locatie van de beleids waarden kan "globaal", "listener" of "locatie" zijn.   |
|policyScopeName   | De naam van het object waarop het beleid wordt toegepast.    |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.147",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343",
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/perListener",
      "policyScope": "Listener",
      "policyScopeName": "httpListener1"
    }
  }
}

```

### <a name="view-and-analyze-the-activity-log"></a>Het activiteitenlogboek bekijken en analyseren

U kunt activiteitenlogboekgegevens bekijken en analyseren via een van de volgende methoden:

* **Azure-hulpprogramma’s**: Haal informatie uit het activiteitenlogboek op via Azure PowerShell, de Azure CLI, de Azure REST-API of de Azure-portal. In het artikel [Activiteitsbewerkingen met Resource Manager](../../azure-resource-manager/management/view-activity-logs.md) staan stapsgewijze instructies voor elke methode.
* **Power BI**: Als u nog geen [Power BI](https://powerbi.microsoft.com/pricing)-account hebt, kunt u het gratis uitproberen. Met behulp van de [Power bi sjabloon-apps](/power-bi/service-template-apps-overview)kunt u uw gegevens analyseren.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>De logboeken voor toegang, prestaties en firewalls weer geven en analyseren

[Azure monitor logboeken](../../azure-monitor/insights/azure-networking-analytics.md) kunnen de item-en gebeurtenis logboek bestanden van uw Blob Storage-account verzamelen. Het omvat visualisaties en krachtige zoekmogelijkheden om uw logboeken te analyseren.

U kunt ook verbinding maken met uw opslagaccount en de JSON-logboekitems voor toegangs- en prestatielogboeken ophalen. Nadat u de JSON-bestanden hebt gedownload, kunt u ze naar de CSV-indeling converteren en in Excel, Power BI of een ander hulpprogramma voor gegevensvisualisatie bekijken.

> [!TIP]
> Als u bekend bent met Visual Studio en de basisconcepten van het wijzigen van waarden voor constanten en variabelen in C#, kunt u de [logboekconversieprogramma’s](https://github.com/Azure-Samples/networking-dotnet-log-converter) gebruiken die beschikbaar zijn in GitHub.
>
>

#### <a name="analyzing-access-logs-through-goaccess"></a>Access-logboeken analyseren via GoAccess

We hebben een resource manager-sjabloon gepubliceerd waarmee de populaire [GoAccess](https://goaccess.io/) log analyzer voor Application Gateway Access-Logboeken wordt geïnstalleerd en uitgevoerd. GoAccess biedt waardevolle statistieken voor HTTP-verkeer, zoals unieke bezoekers, aangevraagde bestanden, hosts, besturings systemen, browsers, HTTP-status codes en meer. Raadpleeg het [Leesmij-bestand in de map Resource Manager-sjabloon in github](https://aka.ms/appgwgoaccessreadme)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Visualiseer teller-en gebeurtenis logboeken met behulp van [Azure monitor-logboeken](../../azure-monitor/insights/azure-networking-analytics.md).
* [Visualiseer uw Azure-activiteiten logboek met Power bi](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) blog bericht.
* [Bekijk en analyseer activiteiten logboeken van Azure in Power bi en meer](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blog berichten.

[1]: ../media/web-application-firewall-logs/figure1.png
[2]: ../media/web-application-firewall-logs/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
