---
title: Overzicht van Azure Firewall logboeken en metrische gegevens
description: U kunt Azure Firewall bewaken met behulp van firewall-logboeken. U kunt ook activiteitenlogboeken gebruiken om bewerkingen in Azure Firewall-resources te controleren.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 8922e6e0d5137a3a900e0f57f685d449c08b3f47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100596959"
---
# <a name="azure-firewall-logs-and-metrics"></a>Azure Firewall-logboeken en metrische gegevens

U kunt Azure Firewall bewaken met behulp van firewall-logboeken. U kunt ook activiteitenlogboeken gebruiken om bewerkingen in Azure Firewall-resources te controleren.

Via de portal kunt u toegang verkrijgen tot sommige van deze logboeken. Logboeken kunnen worden verzonden naar [Azure monitor logboeken](../azure-monitor/insights/azure-networking-analytics.md), opslag en Event hubs en worden geanalyseerd in azure monitor Logboeken of door verschillende hulpprogram Ma's zoals Excel en Power bi.

Metrische gegevens zijn licht gewicht en kunnen bijna realtime-scenario's ondersteunen, waardoor ze nuttig zijn voor waarschuwingen en snelle detectie van problemen.

## <a name="diagnostic-logs"></a>Diagnostische logboeken

 De volgende diagnostische logboeken zijn beschikbaar voor Azure Firewall:

* **Logboek voor toepassingsregels**

   Het toepassings regel logboek wordt opgeslagen in een opslag account, gestreamd naar Event hubs en/of verzonden naar Azure Monitor Logboeken alleen als u het hebt ingeschakeld voor elke Azure Firewall. Elke nieuwe verbinding die overeenkomt met een van uw geconfigureerde toepassingsregels, resulteert in een logboek voor de geaccepteerde/geweigerde verbinding. De gegevens worden vastgelegd in de JSON-indeling, zoals wordt weer gegeven in de volgende voor beelden:

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

   ```json
   {
     "category": "AzureFirewallApplicationRule",
     "time": "2018-04-16T23:45:04.8295030Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallApplicationRuleLog",
     "properties": {
         "msg": "HTTPS request from 10.11.2.4:53344 to www.bing.com:443. Action: Allow. Rule Collection: ExampleRuleCollection. Rule: ExampleRule. Web Category: SearchEnginesAndPortals"
     }
   }
   ```

* **Logboek voor netwerkregels**

   Het netwerk regel logboek wordt opgeslagen in een opslag account, gestreamd naar Event hubs en/of verzonden naar Azure Monitor Logboeken alleen als u het hebt ingeschakeld voor elke Azure Firewall. Elke nieuwe verbinding die overeenkomt met een van uw geconfigureerde netwerkregels, resulteert in een logboek voor de geaccepteerde/geweigerde verbinding. De gegevens worden geregistreerd in JSON-indeling, zoals weergegeven in het volgende voorbeeld:

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

* **DNS-proxy logboek**

   Het DNS-proxy logboek wordt opgeslagen in een opslag account, gestreamd naar Event hubs en/of verzonden naar Azure Monitor Logboeken alleen als u het hebt ingeschakeld voor elke Azure Firewall. In dit logboek worden DNS-berichten bijgehouden voor een DNS-server die is geconfigureerd met behulp van DNS-proxy. De gegevens worden vastgelegd in de JSON-indeling, zoals wordt weer gegeven in de volgende voor beelden:


   ```
   Category: DNS proxy logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   Geleverd
   ```json
   {
     "category": "AzureFirewallDnsProxy",
     "time": "2020-09-02T19:12:33.751Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallDnsProxyLog",
     "properties": {
         "msg": "DNS Request: 11.5.0.7:48197 – 15676 AAA IN md-l1l1pg5lcmkq.blob.core.windows.net. udp 55 false 512 NOERROR - 0 2.000301956s"
     }
   }
   ```

   Is mislukt

   ```json
   {
     "category": "AzureFirewallDnsProxy",
     "time": "2020-09-02T19:12:33.751Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallDnsProxyLog",
     "properties": {
         "msg": " Error: 2 time.windows.com.reddog.microsoft.com. A: read udp 10.0.1.5:49126->168.63.129.160:53: i/o timeout”
     }
   }
   ```

   msg-indeling:

   `[client’s IP address]:[client’s port] – [query ID] [type of the request] [class of the request] [name of the request] [protocol used] [request size in bytes] [EDNS0 DO (DNSSEC OK) bit set in the query] [EDNS0 buffer size advertised in the query] [response CODE] [response flags] [response size] [response duration]`

U hebt drie opties voor het opslaan van uw logboeken:

* **Opslagaccount**: opslagaccounts kunnen het best worden gebruikt wanneer logboeken voor een langere periode worden opgeslagen en moeten kunnen worden bekeken wanneer dat nodig is.
* **Event Hubs**: Event Hubs zijn een geweldige optie voor integratie met andere SIEM-hulpprogramma’s (Security Information and Event Management) om waarschuwingen over uw resources te krijgen.
* **Azure monitor-logboeken**: Azure monitor-logboeken worden het beste gebruikt voor algemene realtime-bewaking van uw toepassing of voor het bekijken van trends.

## <a name="activity-logs"></a>Activiteitenlogboeken

   Activiteitenlogboekitems worden standaard verzameld en kunnen in de Azure-portal worden bekeken.

   U kunt [Azure-activiteiten logboeken](../azure-resource-manager/management/view-activity-logs.md) (voorheen bekend als operationele logboeken en audit Logboeken) gebruiken om alle bewerkingen weer te geven die zijn verzonden naar uw Azure-abonnement.

## <a name="metrics"></a>Metrische gegevens

Metrische gegevens in Azure Monitor zijn numerieke waarden die een aspect van een systeem op een bepaald moment beschrijven. Metrische gegevens worden elke minuut verzameld en zijn nuttig voor waarschuwingen omdat ze regel matig kunnen worden gesampled. Een waarschuwing kan snel worden geactiveerd met relatief eenvoudige logica.

De volgende metrische gegevens zijn beschikbaar voor Azure Firewall:

- **Aantal op toepassings regels gebaseerde treffers** -het aantal keren dat een toepassings regel is bereikt.

    Eenheid: aantal

- **Aantal netwerk regels treffers** -het aantal keren dat een netwerk regel is bereikt.

    Eenheid: aantal

- **Verwerkte gegevens** : som van de gegevens die de firewall in een bepaald tijd venster passeren.

    Eenheid: bytes

- **Doorvoer** snelheid van de gegevens die de firewall per seconde passeren.

    Eenheid: bits per seconde

- Status van **firewall** : geeft de status van de firewall aan op basis van de beschik baarheid van de SNAT-poort.

    Eenheid: percentage

   Deze metrische waarde heeft twee dimensies:
  - Status: mogelijke waarden zijn *in orde*, *gedegradeerd*, *beschadigd.*
  - Reden: geeft de reden aan van de bijbehorende status van de firewall. 

     Als er een SNAT-poort wordt gebruikt > 95%, worden deze als uitgeput beschouwd en is de status 50% met het **cijfer = verslechterd** en reden =**SNAT-poort**. De firewall blijft verkeer verwerken en bestaande verbindingen worden niet beïnvloed. Het is echter mogelijk dat het niet altijd lukt om nieuwe verbindingen tot stand te brengen.

     Als er SNAT-poorten worden gebruikt < 95%, wordt de firewall als gezond beschouwd en wordt de status weer gegeven als 100%.

     Als er geen gebruik van SNAT-poorten wordt gerapporteerd, wordt de status weergegeven als 0%. 

- **SNAT-poort gebruik** : het percentage SNAT-poorten dat door de firewall is gebruikt.

    Eenheid: percentage

   Wanneer u meer openbare IP-adressen aan uw firewall toevoegt, zijn er meer SNAT-poorten beschikbaar, waardoor het gebruik van de SNAT-poorten afneemt. Als de firewall om wat voor reden dan ook wordt uitgeschaald (bijvoorbeeld CPU of doorvoer), komen er ook extra SNAT-poorten beschikbaar. Een bepaald percentage van het gebruik van de SNAT-poorten kan dan ook worden vervolgd zonder dat u open bare IP-adressen toevoegt, alleen omdat de service is uitgeschaald. U kunt het aantal open bare IP-adressen dat beschikbaar is voor het verg Roten van de beschik bare poorten op uw firewall, direct beheren. Maar u kunt het schalen van de firewall niet rechtstreeks beheren.


## <a name="next-steps"></a>Volgende stappen

- Zie [zelf studie: Azure firewall logboeken controleren](./firewall-diagnostics.md)voor meer informatie over het bewaken van Azure firewall logboeken en metrische gegevens.

- Zie [metrische gegevens in azure monitor](../azure-monitor/essentials/data-platform-metrics.md)voor meer informatie over metrische gegevens in azure monitor.