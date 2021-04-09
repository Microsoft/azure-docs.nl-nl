---
title: Azure Advisor waarschuwingen maken voor nieuwe aanbevelingen met behulp van Azure Portal
description: Azure Advisor-waarschuwingen maken voor nieuwe aanbeveling
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: 3c51479821914ef34edcd13d8708344169f17aae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100590114"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations-using-the-azure-portal"></a>Maak Azure Advisor waarschuwingen met betrekking tot nieuwe aanbevelingen met behulp van de Azure Portal 

In dit artikel wordt beschreven hoe u een waarschuwing instelt voor nieuwe aanbevelingen van Azure Advisor met behulp van de Azure Portal. 

Wanneer Azure Advisor een nieuwe aanbeveling voor een van uw resources detecteert, wordt een gebeurtenis opgeslagen in het [Azure-activiteitenlogboek](../azure-monitor/essentials/platform-logs-overview.md). U kunt waarschuwingen instellen voor deze gebeurtenissen van Azure Advisor met behulp van een functie voor het maken van aanbevelingsgerichte waarschuwingen. U kunt een abonnement en optioneel een resourcegroep selecteren om de resources op te geven waarvoor u waarschuwingen wilt ontvangen. 

U kunt ook de typen aanbevelingen vaststellen met behulp van de volgende eigenschappen:

* Categorie
* Impactniveau
* Aanbevelingstype

U kunt ook de actie configureren die wordt uitgevoerd wanneer een waarschuwing wordt geactiveerd door:  

* Een bestaande actiegroep te selecteren
* Een nieuwe actiegroep te maken

Raadpleeg [Actiegroepen maken en beheren](../azure-monitor/alerts/action-groups.md) voor meer informatie over actiegroepen.

> [!NOTE] 
> Advisor-waarschuwingen zijn momenteel alleen beschikbaar voor aanbevelingen voor hoge beschikbaarheid, prestaties en kosten. Aanbevelingen voor beveiliging worden niet ondersteund. 

## <a name="create-alert-rule"></a>Waarschuwingsregel maken
1. Selecteer in de **portal** **Azure Advisor**.

    ![Azure Advisor in Portal](./media/advisor-alerts/create1.png)

2. Selecteer in de sectie **bewaking** van het linkermenu **waarschuwingen**. 

    ![Waarschuwingen in Advisor](./media/advisor-alerts/create2.png)

3. Selecteer **nieuwe Advisor-waarschuwing**.

    ![Nieuwe Advisor-waarschuwing](./media/advisor-alerts/create3.png)

4. Selecteer in de sectie **bereik** het abonnement en optioneel de resource groep waarop u wilt worden gewaarschuwd. 

    ![Advisor-waarschuwings bereik](./media/advisor-alerts/create4.png)

5. Selecteer in de sectie **voor waarde** de methode die u wilt gebruiken voor het configureren van de waarschuwing. Als u een waarschuwing wilt ontvangen voor alle aanbevelingen voor een bepaalde categorie en/of een impact niveau, selecteert u **categorie en impact niveau**. Als u een waarschuwing wilt ontvangen voor alle aanbevelingen van een bepaald type, selecteert u **aanbevelings type**.

    ![Azure Advisor waarschuwings voorwaarde](./media/advisor-alerts/create5.png)

6. Afhankelijk van de optie configureren op die u selecteert, kunt u de criteria opgeven. Als u alle aanbevelingen wilt, laat u de resterende velden leeg. 

    ![Actie groep voor Advisor-waarschuwingen](./media/advisor-alerts/create6.png)

7. Selecteer in de sectie **actie groepen** de optie **bestaande toevoegen** om een actie groep te gebruiken die u al hebt gemaakt of selecteer **nieuwe maken** om een nieuwe [actie groep](../azure-monitor/alerts/action-groups.md)in te stellen. 

    ![Advisor-waarschuwing bestaande toevoegen](./media/advisor-alerts/create7.png)

8. Geef in de sectie waarschuwings Details een naam en een korte beschrijving voor de waarschuwing op. Als u wilt dat uw waarschuwing wordt ingeschakeld, verlaat u **regel inschakelen bij** selectie instellen op **Ja**. Selecteer vervolgens de resource groep waarin u de waarschuwing wilt opslaan. Dit heeft geen invloed op het doel bereik van de aanbeveling. 

    :::image type="content" source="./media/advisor-alerts/create8.png" alt-text="Scherm afbeelding van de sectie waarschuwings Details.":::


## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>Aanbevelings waarschuwingen configureren voor het gebruik van een webhook
In deze sectie wordt beschreven hoe u Azure Advisor waarschuwingen configureert om aanbevelings gegevens via webhooks naar uw bestaande systemen te verzenden. 

U kunt waarschuwingen instellen om een melding te ontvangen wanneer u een nieuwe adviseur-aanbeveling hebt voor een van uw resources. Deze waarschuwingen kunnen u verwittigen via e-mail of SMS, maar ze kunnen ook worden gebruikt om te integreren met uw bestaande systemen via een webhook. 


### <a name="using-the-advisor-recommendation-alert-payload"></a>De nettolading aanbeveling van Advisor-waarschuwing gebruiken
Als u Advisor-waarschuwingen wilt integreren in uw eigen systemen met een webhook, moet u de JSON-nettolading parseren die vanuit de melding wordt verzonden. 

Wanneer u uw actie groep voor deze waarschuwing instelt, selecteert u of u het algemene waarschuwings schema wilt gebruiken. Als u het algemene schema voor waarschuwingen selecteert, ziet uw nettolading er als volgt uit: 

```json
{  
   "schemaId":"azureMonitorCommonAlertSchema",
   "data":{  
      "essentials":{  
         "alertId":"/subscriptions/<subid>/providers/Microsoft.AlertsManagement/alerts/<alerted>",
         "alertRule":"Webhhook-test",
         "severity":"Sev4",
         "signalType":"Activity Log",
         "monitorCondition":"Fired",
         "monitoringService":"Activity Log - Recommendation",
         "alertTargetIDs":[  
            "/subscriptions/<subid>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>"
         ],
         "originAlertId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "firedDateTime":"2019-07-17T23:00:57.3858656Z",
         "description":"A new recommendation is available.",
         "essentialsVersion":"1.0",
         "alertContextVersion":"1.0"
      },
      "alertContext":{  
         "channels":"Operation",
         "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
         "caller":"Microsoft.Advisor",
         "correlationId":"8554b847-2a72-48ef-9776-600aca3c3aab",
         "eventSource":"Recommendation",
         "eventTimestamp":"2019-07-17T22:28:54.1566942+00:00",
         "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
         "eventDataId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "level":"Informational",
         "operationName":"Microsoft.Advisor/recommendations/available/action",
         "properties":{  
            "recommendationSchemaVersion":"1.0",
            "recommendationCategory":"Performance",
            "recommendationImpact":"Medium",
            "recommendationName":"Increase the MariaDB server vCores",
            "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%<subscription id>%2FresourceGroups%2<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
            "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
         },
         "status":"Active",
         "subStatus":"",
         "submissionTimestamp":"2019-07-17T22:28:54.1566942+00:00"
      }
   }
}
  ```

Als u het algemene schema niet gebruikt, ziet uw nettolading er als volgt uit: 

```json
{  
   "schemaId":"Microsoft.Insights/activityLogs",
   "data":{  
      "status":"Activated",
      "context":{  
         "activityLog":{  
            "channels":"Operation",
            "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
            "caller":"Microsoft.Advisor",
            "correlationId":"3ea7320f-c002-4062-adb8-96d3bd92a5f4",
            "description":"A new recommendation is available.",
            "eventSource":"Recommendation",
            "eventTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
            "eventDataId":"a12b8e59-0b1d-4003-bfdc-3d8152922e59",
            "level":"Informational",
            "operationName":"Microsoft.Advisor/recommendations/available/action",
            "properties":{  
               "recommendationSchemaVersion":"1.0",
               "recommendationCategory":"Performance",
               "recommendationImpact":"Medium",
               "recommendationName":"Increase the MariaDB server vCores",
               "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%2F<subscription id>%2FresourceGroups%2F<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
               "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
            },
            "resourceId":"/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>",
            "resourceGroupName":"<resource group name>",
            "resourceProviderName":"MICROSOFT.DBFORMARIADB",
            "status":"Active",
            "subStatus":"",
            "subscriptionId":"<subscription id>",
            "submissionTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "resourceType":"MICROSOFT.DBFORMARIADB/SERVERS"
         }
      },
      "properties":{  
 
      }
   }
}
```

In een van beide schema's kunt u aanbevelingen voor Advisor identificeren door te kijken naar **Event source** is `Recommendation` en **operationname** `Microsoft.Advisor/recommendations/available/action` .

Enkele van de andere belang rijke velden die u wilt gebruiken, zijn: 

* *alertTargetIDs* (in het algemene schema) of *resourceId* (verouderd schema)
* *recommendationType*
* *aanbeveling*
* *recommendationCategory*
* *recommendationImpact*
* *recommendationResourceLink*


## <a name="manage-your-alerts"></a>De waarschuwingen beheren 

Vanuit Azure Advisor kunt u waarschuwingen voor uw aanbevelingen bewerken, verwijderen of uitschakelen en inschakelen. 

1. Selecteer in de **portal** **Azure Advisor**.

    :::image type="content" source="./media/advisor-alerts/create1.png" alt-text="Scherm opname van het Azure Portal menu waarin Azure Advisor is geselecteerd.":::

2. Selecteer in de sectie **bewaking** van het linkermenu **waarschuwingen**.

    :::image type="content" source="./media/advisor-alerts/create2.png" alt-text="Scherm opname van het menu Azure Portal waarin de geselecteerde waarschuwingen worden weer gegeven.":::

3. Als u een waarschuwing wilt bewerken, klikt u op de naam van de waarschuwing om de waarschuwing te openen en bewerkt u de velden die u wilt bewerken.

4. Als u een waarschuwing wilt verwijderen, inschakelen of uitschakelen, klikt u op de ovaal aan het einde van de rij en selecteert u vervolgens de actie die u wilt uitvoeren.
 

## <a name="next-steps"></a>Volgende stappen
- Een [overzicht van waarschuwingen voor het activiteitenlogboek](../azure-monitor/alerts/alerts-overview.md) en meer informatie over het ontvangen van waarschuwingen.
- Meer informatie over [actiegroepen](../azure-monitor/alerts/action-groups.md).
