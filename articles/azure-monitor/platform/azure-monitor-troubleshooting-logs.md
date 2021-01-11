---
title: Azure Monitor probleemoplossings Logboeken (preview-versie)
description: Gebruik Azure Monitor om snel of periodiek problemen te onderzoeken, problemen met code-of configuratie problemen op te lossen of om ondersteunings aanvragen te behandelen, die vaak afhankelijk zijn van het zoeken in grote hoeveel heden gegevens voor specifieke inzichten.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/29/2020
ms.openlocfilehash: 816cdddc1f3d0a9bc9ebc3f277bc223a688cba31
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98067362"
---
# <a name="azure-monitor-troubleshooting-logs-preview"></a>Azure Monitor probleemoplossings Logboeken (preview-versie)
Gebruik Azure Monitor om snel en/of periodiek problemen te onderzoeken, problemen met code of configuratie op te lossen of service-ondersteunings cases, die vaak afhankelijk zijn van het zoeken in grote hoeveel heden gegevens voor specifieke inzichten.

>[!NOTE]
> * Probleemoplossings logboeken bevindt zich in de preview-modus.
>* Neem contact op met het [log Analytics team](mailto:orens@microsoft.com) met eventuele vragen of om de functie toe te passen.
## <a name="troubleshoot-and-query-your-code-or-configuration-issues"></a>Problemen oplossen en query's uitvoeren op uw code of configuratie problemen
Gebruik Azure Monitor probleemoplossings Logboeken om uw records op te halen en problemen en problemen op een eenvoudige en goedkopere manier te onderzoeken met behulp van KQL.
Met Logboeken voor probleem oplossing worden uw kosten in rekening gebracht door basis mogelijkheden te bieden voor het oplossen van problemen.

> [!NOTE]
>* De beslissing voor de probleemoplossings modus kan worden geconfigureerd.
>* Logboeken voor probleem oplossing kunnen worden toegepast op specifieke tabellen, op dit moment in de tabellen container logs en app-traceringen.
>* Er is een gratis Bewaar periode van 4 dagen, waardoor de kosten kunnen worden verlengd.
>* Standaard neemt de tabellen de Bewaar periode van de werk ruimte over. Om extra kosten te voor komen, is het raadzaam deze tabellen retentie te wijzigen. [Klik hier voor meer informatie over het wijzigen van de Bewaar periode van tabellen](https://docs.microsoft.com//azure/azure-monitor/platform/manage-cost-storage).

## <a name="turn-on-troubleshooting-logs-on-your-tables"></a>Schakel logboeken voor probleem oplossing in de tabellen in

Als u Logboeken voor het oplossen van problemen in uw werk ruimte wilt inschakelen, moet u de volgende API-aanroep gebruiken.
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
      }
```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-a-given-table"></a>Controleer of de functie Logboeken voor probleem oplossing is ingeschakeld voor een bepaalde tabel
Als u wilt controleren of het logboek voor probleem oplossing is ingeschakeld voor een bepaalde tabel, kunt u de volgende API-aanroep gebruiken.

```http
GET https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

Response: 
"properties": {
          "retentionInDays": 30,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true,
          "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": " {tableName}"

```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-all-of-the-tables-in-a-workspace"></a>Controleer of de functie Logboeken voor probleem oplossing is ingeschakeld voor alle tabellen in een werk ruimte
Als u wilt controleren voor welke tabellen het logboek voor probleem oplossing is ingeschakeld, kunt u de volgende API-aanroep gebruiken.

```http
GET "https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables"

Response: 
{
          "properties": {
            "retentionInDays": 30,
            "isTroubleshootingAllowed": true,
            "isTroubleshootEnabled": true,
            "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table1",
          "name": "table1"
        },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": true
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table2",
          "name": "table2"
        },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": false
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table3",
          "name": "table3"
        }
```
## <a name="turn-off-troubleshooting-logs-on-your-tables"></a>Probleemoplossings Logboeken in uw tabellen uitschakelen

Als u Logboeken voor probleem oplossing in uw werk ruimte wilt uitschakelen, moet u de volgende API-aanroep gebruiken.
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": false
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
      }
```
>[!TIP]
>* U kunt elk REST API-hulp programma gebruiken om de opdrachten uit te voeren. [Meer informatie](https://docs.microsoft.com/rest/api/azure/)
>* U moet het Bearer-token gebruiken voor verificatie. [Meer informatie](https://social.technet.microsoft.com/wiki/contents/articles/51140.azure-rest-management-api-the-quickest-way-to-get-your-bearer-token.aspx)

>[!NOTE]
>* De vlag ' isTroubleshootingAllowed ' – beschrijft of de tabel is toegestaan in de service
>* De ' isTroubleshootEnabled ' geeft aan of de functie is ingeschakeld voor de tabel-kan worden in-of uitgeschakeld (waar of onwaar)
>* Als u de vlag ' isTroubleshootEnabled ' voor een specifieke tabel uitschakelt, is het mogelijk dat er slechts één week na de vorige Schakel datum weer wordt ingeschakeld.
>* Dit wordt momenteel alleen ondersteund voor tabellen onder (andere Sku's worden ook in de toekomst ondersteund): [meer informatie over prijzen](https://docs.microsoft.com/services-hub/health/azure_pricing).

## <a name="query-limitations-for-troubleshooting"></a>Query beperkingen voor het oplossen van problemen
Er gelden enkele beperkingen voor een tabel die is gemarkeerd als ' logboeken voor probleem oplossing ':
*   Krijgt minder verwerkings resources en is daarom niet geschikt voor grote Dash boards, complexe analyses of veel gelijktijdige API-aanroepen.
*   Query's zijn beperkt tot een tijds bereik van twee dagen.
* het opschonen werkt niet – [Lees meer over leegmaken](https://docs.microsoft.com/rest/api/loganalytics/workspacepurge/purge).
* Waarschuwingen worden niet ondersteund via deze service.
## <a name="next-steps"></a>Volgende stappen
* [Query's schrijven](https://docs.microsoft.com/azure/data-explorer/write-queries)


