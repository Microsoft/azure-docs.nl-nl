---
title: Bewerkingen, gebeurtenissen en prestatie meter items bewaken voor open bare basis Load Balancer
titleSuffix: Azure Load Balancer
description: Meer informatie over het inschakelen van waarschuwings gebeurtenissen en het controleren van de status logboek registratie voor open bare basis Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: f24ab2c646757f0241748336243b0d5f977d081c
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698322"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Azure Monitor-logboeken voor openbare Basic Azure Load Balancer

U kunt verschillende typen logboeken in azure gebruiken om Basic load balancers te beheren en problemen op te lossen. Sommige van deze logboeken kunnen worden geopend via de portal. Logboeken kunnen worden gestreamd naar een Event Hub-of Log Analytics-werk ruimte. Alle logboeken kunnen worden geëxtraheerd uit Azure Blob-opslag en worden weer gegeven in verschillende hulpprogram ma's, zoals Excel en Power BI.  In de onderstaande lijst vindt u meer informatie over de verschillende typen logboeken.

* **Activiteiten logboeken:** U kunt [activiteiten logboeken weer geven gebruiken om acties op resources te controleren](../azure-resource-manager/management/view-activity-logs.md) om alle activiteiten weer te geven die worden verzonden naar uw Azure-abonnement (en) en hun status. Activiteiten logboeken zijn standaard ingeschakeld en kunnen worden weer gegeven in de Azure Portal.
* **Gebeurtenis logboeken voor waarschuwingen:** U kunt dit logboek gebruiken om waarschuwingen weer te geven die zijn gegenereerd door de load balancer. De status voor de load balancer wordt elke vijf minuten verzameld. Dit logboek wordt alleen geschreven als er een load balancer waarschuwings gebeurtenis optreedt.
* **Health probe-logboeken:** U kunt dit logboek gebruiken om problemen weer te geven die zijn gedetecteerd door uw Health Probe, zoals het aantal exemplaren in uw back-end-pool die geen aanvragen ontvangen van de load balancer als gevolg van fouten in de status test. In dit logboek wordt geschreven wanneer er een wijziging is in de status van de status test.

> [!IMPORTANT]
> **Gebeurtenis logboeken met status controle zijn momenteel niet functioneel en worden weer gegeven in de [bekende problemen voor de Azure Load Balancer](whats-new.md#known-issues).** Logboeken zijn alleen beschikbaar voor resources die zijn geïmplementeerd in het Resource Manager-implementatie model. U kunt geen Logboeken gebruiken voor bronnen in het klassieke implementatie model. Zie [Resource Manager-implementatie en klassieke implementatie](../azure-resource-manager/management/deployment-models.md)voor meer informatie over de implementatie modellen.

## <a name="enable-logging"></a>Logboekregistratie inschakelen

Activiteitenlogboekregistratie is automatisch ingeschakeld voor elke Resource Manager-resource. Schakel logboek registratie voor gebeurtenis-en status controle in om te beginnen met het verzamelen van de gegevens die beschikbaar zijn via deze logboeken. Gebruik de volgende stappen om logboek registratie in te scha kelen.

Meld u aan bij de [Azure-portal](https://portal.azure.com). Als u nog geen load balancer hebt, [maakt u een Load Balancer](./quickstart-load-balancer-standard-public-portal.md) voordat u doorgaat.

1. Klik in de portal op **resource groepen**.
2. Selecteer **\<resource-group-name>** waar uw Load Balancer is.
3. Selecteer uw load balancer.
4. Selecteer **Activity log**  >  **Diagnostische instellingen** voor activiteiten logboek.
5. Selecteer in het deel venster **Diagnostische instellingen** onder **Diagnostische instellingen** de optie **+ Diagnostische instelling toevoegen**.
6. Voer in het deel venster **Diagnostische instellingen** maken **myLBDiagnostics** in het veld **naam** in.
7. Er zijn drie opties voor de **Diagnostische instellingen**.  U kunt een, twee of alle drie kiezen en elk configureren voor uw vereisten:
   * **Archiveren naar een opslag account**
   * **Streamen naar een Event Hub**
   * **Verzenden naar Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>Archiveren naar een opslagaccount
    U hebt een opslag account nodig dat al is gemaakt voor dit proces.  Zie [een opslag account maken](../storage/common/storage-account-create.md?tabs=azure-portal) voor meer informatie over het maken van een opslag account.

    1. Schakel het selectie vakje in naast **archiveren naar een opslag account**.
    2. Selecteer **configureren** om het deel venster **een opslag account selecteren** te openen.
    3. Selecteer het **abonnement** waarin uw opslag account is gemaakt in de vervolg keuzelijst.
    4. Selecteer de naam van uw opslag account onder **opslag account** in de vervolg keuzelijst.
    5. Selecteer OK.

    ### <a name="stream-to-an-event-hub"></a>Streamen naar een Event Hub
    U hebt een Event Hub nodig dat voor dit proces al is gemaakt.  Als u een Event Hub wilt maken, raadpleegt u [Quick Start: een event hub maken met behulp van Azure Portal](../event-hubs/event-hubs-create.md)

    1. Schakel het selectie vakje **in naast streamen naar een event hub**
    2. Selecteer **configureren** om het deel venster **Event hub selecteren** te openen.
    3. Selecteer het **abonnement** waarin uw event hub is gemaakt in de vervolg keuzelijst.
    4. **Selecteer Event hub naam ruimte** in de vervolg keuzelijst.
    5. **Selecteer Event hub-beleids naam** in de vervolg keuzelijst.
    6. Selecteer OK.

    ### <a name="send-to-log-analytics"></a>Verzenden naar Log Analytics
    U moet al een log Analytics-werk ruimte hebben gemaakt en geconfigureerd voor dit proces.  Zie [een log Analytics-werk ruimte maken in de Azure Portal](../azure-monitor/learn/quick-create-workspace.md) om een log Analytics-werk ruimte te maken.

    1. Schakel het selectie vakje in naast **verzenden naar log Analytics**.
    2. Selecteer het **abonnement** waarin uw log Analytics werk ruimte zich in de vervolg keuzelijst bevindt.
    3. Selecteer de **log Analytics werk ruimte** in de vervolg keuzelijst.


8. Schakel onder de sectie **logboek** in het deel venster **Diagnostische instellingen** het selectie vakje in naast beide:
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  Schakel onder de sectie **metric** in het deel venster **Diagnostische instellingen** het selectie vakje in naast:
   * **AllMetrics**

11. Controleer of alles goed lijkt en klik boven aan het deel venster **Diagnostische instellingen** maken op **Opslaan** .

## <a name="activity-log"></a>Activiteitenlogboek

Het activiteiten logboek wordt standaard gegenereerd. De logboeken blijven 90 dagen bewaard in de gebeurtenis logboeken van Azure. Lees voor meer informatie over deze logboeken de [activiteiten logboeken weer geven om acties op het artikel resources te controleren](../azure-resource-manager/management/view-activity-logs.md) .

## <a name="archive-to-storage-account-logs"></a>Logboeken voor archiveren naar een opslagaccount

### <a name="alert-event-log"></a>Gebeurtenis logboek voor waarschuwingen

Dit logboek wordt alleen gegenereerd als u het hebt ingeschakeld op basis van een per load balancer. De gebeurtenissen worden vastgelegd in de JSON-indeling en opgeslagen in het opslag account dat u hebt opgegeven tijdens het inschakelen van de logboek registratie. Het volgende voor beeld is een gebeurtenis.

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

De JSON-uitvoer toont de eigenschap *eventname* , waarmee wordt beschreven waarom de Load Balancer een waarschuwing heeft gemaakt. In dit geval wordt de gegenereerde waarschuwing veroorzaakt door de TCP-poort ontstaat vanwege IP NAT-limieten (SNAT) van de bron.

### <a name="health-probe-log"></a>Status test logboek

Dit logboek wordt alleen gegenereerd als u het hebt ingeschakeld op een per load balancer, zoals hierboven is beschreven. De gegevens worden opgeslagen in het opslag account dat u hebt opgegeven tijdens het inschakelen van de logboek registratie. Er wordt een container met de naam ' Insights-logs-loadbalancerprobehealthstatus ' gemaakt en de volgende gegevens worden geregistreerd:

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

De JSON-uitvoer wordt weer gegeven in het veld eigenschappen de basis informatie voor de test status. De eigenschap *dipDownCount* toont het totale aantal exemplaren op de back-end, die geen netwerk verkeer ontvangen vanwege mislukte test reacties.

### <a name="view-and-analyze-the-activity-log"></a>Het activiteitenlogboek bekijken en analyseren

U kunt activiteiten logboek gegevens weer geven en analyseren met een van de volgende methoden:

* **Azure-hulpprogram ma's:** Gegevens ophalen uit het activiteiten logboek via Azure PowerShell, de Azure-opdracht regel interface (CLI), de Azure-REST API of de Azure Portal. Stapsgewijze instructies voor elke methode worden beschreven in het artikel [controle bewerkingen met Resource Manager](../azure-resource-manager/management/view-activity-logs.md) .
* **Power BI:** Als u nog geen [Power bi](https:// .microsoft.com/pricing) account hebt, kunt u het gratis uitproberen. Met het [Azure audit logs-inhouds pakket voor Power bi](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs)kunt u uw gegevens analyseren met vooraf geconfigureerde Dash boards, of u kunt weer gaven aanpassen aan uw vereisten.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>De status test en het gebeurtenis logboek weer geven en analyseren

Maak verbinding met uw opslag account en haal de JSON-logboek vermeldingen op voor gebeurtenis-en Health probe Logboeken. Wanneer u de JSON-bestanden hebt gedownload, kunt u deze converteren naar CSV en weer geven in Excel, Power BI of een ander hulp programma voor gegevens visualisatie.

> [!TIP]
> Als u bekend bent met Visual Studio en de basisconcepten van het wijzigen van waarden voor constanten en variabelen in C#, kunt u de [logboekconversieprogramma’s](https://github.com/Azure-Samples/networking-dotnet-log-converter) gebruiken die beschikbaar zijn in GitHub.

## <a name="stream-to-an-event-hub"></a>Streamen naar een Event Hub
Wanneer diagnostische gegevens worden gestreamd naar een Event Hub, kan deze worden gebruikt voor gecentraliseerde logboek analyse in een SIEM-hulp programma van derden met Azure Monitor-integratie. Zie [Azure-bewakings gegevens streamen naar een event hub](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration) voor meer informatie

## <a name="send-to-log-analytics"></a>Verzenden naar Log Analytics
De diagnostische gegevens van resources in azure kunnen rechtstreeks naar een Log Analytics-werk ruimte worden verzonden, waar complexe query's kunnen worden uitgevoerd op basis van de informatie voor probleem oplossing en analyse.  Zie [Azure-resource logboeken verzamelen in log Analytics werk ruimte in azure monitor](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

[Load Balancer-testen](load-balancer-custom-probe-overview.md)