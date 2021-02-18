---
title: Meerdere Azure Monitor Application Insights resources samen voegen | Microsoft Docs
description: In dit artikel vindt u informatie over het gebruik van een functie in Azure Monitor logboeken voor het opvragen van meerdere Application Insights resources en het visualiseren van die gegevens.
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 7f49bd23347b53f9a5287973c6b2aba82da50ca7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100611982"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Meerdere Azure Monitor Application Insights resources samen voegen 
In dit artikel wordt beschreven hoe u al uw Application Insights logboek gegevens op één plek kunt opvragen en weer geven, zelfs wanneer ze zich in verschillende Azure-abonnementen bevinden, als vervanging voor de afschaffing van de Application Insights-connector. Het aantal Application Insights resources dat u in één query kunt toevoegen, is beperkt tot 100.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Aanbevolen benadering voor het opvragen van meerdere Application Insights resources 
Het weer geven van meerdere Application Insights resources in een query kan lastig en moeilijk te onderhouden zijn. In plaats daarvan kunt u gebruikmaken van de functie voor het scheiden van de query logica van de scopes van de toepassing.  

In dit voor beeld ziet u hoe u meerdere Application Insights resources kunt bewaken en het aantal mislukte aanvragen op toepassings naam visualiseren.

Maak een functie met behulp van de operator Union met de lijst met toepassingen en sla de query vervolgens op in uw werk ruimte als functie met de alias *applicationsScoping*. 

U kunt de vermelde toepassingen op elk gewenst moment in de portal wijzigen door naar query Verkenner in uw werk ruimte te gaan en de functie te selecteren die u wilt bewerken en vervolgens op te slaan, of met de `SavedSearch` Power shell-cmdlet. 

>[!NOTE]
>Deze methode kan niet worden gebruikt met logboek waarschuwingen omdat de toegangs validatie van de resources van de waarschuwings regel, inclusief werk ruimten en toepassingen, wordt uitgevoerd tijdens het maken van de waarschuwing. Het toevoegen van nieuwe resources aan de functie nadat het maken van de waarschuwing niet wordt ondersteund. Als u de functie voor het bereik van resources in logboek waarschuwingen wilt gebruiken, moet u de waarschuwings regel in de portal of met een resource manager-sjabloon bewerken om de resources binnen het bereik bij te werken. U kunt ook de lijst met resources opnemen in de waarschuwings query voor Logboeken.

De `withsource= SourceApp` opdracht voegt een kolom toe aan de resultaten die de toepassing aanduidt die het logboek heeft verzonden. De operator parse is optioneel in dit voor beeld en wordt gebruikt om de toepassings naam op te halen uit de eigenschap SourceApp. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

U bent nu klaar om de functie applicationsScoping in de cross-resource query te gebruiken:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

De query maakt gebruik van Application Insights schema, hoewel de query wordt uitgevoerd in de werk ruimte, omdat de functie applicationsScoping de Application Insights gegevens structuur retourneert. De functie alias retourneert de samen voeging van de aanvragen van alle gedefinieerde toepassingen. De query filtert vervolgens op mislukte aanvragen en visualiseert de trends op basis van de toepassing.

![Voor beeld van resultaten van cross-query](media/unify-app-resource-data/app-insights-query-results.png)

>[!NOTE]
>[Query's voor meerdere resources](../logs/cross-workspace-query.md) in logboek waarschuwingen worden alleen ondersteund in de huidige [scheduledQueryRules-API](/rest/api/monitor/scheduledqueryrules). Als u de API legacy Log Analytics Alerts gebruikt, moet u [overschakelen naar de huidige API](../alerts/alerts-log-api-switch.md). [Zie voorbeeld sjablonen](../alerts/alerts-log-create-templates.md).

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Verschillen in schema Application Insights en Log Analytics van werk ruimten
In de volgende tabel ziet u de schema verschillen tussen Log Analytics en Application Insights.  

| Eigenschappen van Log Analytics werk ruimte| Eigenschappen van Application Insights-bron|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationID | appId|
| ApplicationName | Toepassings|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duur |
| AvailabilityMessage | message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | naam |
| AvailabilityTimestamp | tijdstempel |
| Browser | client_browser |
| Plaats | client_city |
| ClientIP | client_IP |
| Computer | cloud_RoleInstance | 
| Land/regio | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | naam | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| ExceptionType | type |
| OperationID | operation_id |
| OperationName | operation_Name | 
| Besturingssysteem | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duur | 
| PageViewName | naam | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duur | 
| RequestID | id | 
| Aanvraagnaam | naam | 
| RequestSuccess | voltooid | 
| ResponseCode | resultCode | 
| Rol | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| URL | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Volgende stappen

Gebruik [Zoeken in Logboeken](../logs/log-query-overview.md) om gedetailleerde informatie weer te geven voor uw Application Insights-apps.

