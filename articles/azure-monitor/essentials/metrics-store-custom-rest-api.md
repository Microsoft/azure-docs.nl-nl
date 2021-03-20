---
title: Metrische gegevens naar de data base van de Azure Monitor metriek verzenden met behulp van REST API
description: Aangepaste metrische gegevens voor een Azure-resource verzenden naar het Azure Monitor metrische archief met behulp van een REST API
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.openlocfilehash: 8acb3c31a41521eace46e065c06f04e69d97a2e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037214"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>Aangepaste metrische gegevens voor een Azure-resource verzenden naar het Azure Monitor metrische archief met behulp van een REST API

Dit artikel laat u zien hoe u aangepaste metrische gegevens voor Azure-resources kunt verzenden naar de Azure Monitor metrische gegevens opslag via een REST API. Nadat de metrische gegevens zijn Azure Monitor, kunt u alle dingen doen met de metrieken met standaard metrische gegevens. Voor beelden hiervan zijn grafieken, waarschuwingen en route ring naar andere externe hulpprogram ma's.  

>[!NOTE]  
>Met de REST API is het verzenden van aangepaste metrische gegevens voor Azure-resources alleen toegestaan. Als u metrische gegevens voor resources in verschillende omgevingen of on-premises wilt verzenden, kunt u [Application Insights](../app/api-custom-events-metrics.md)gebruiken.    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Een service-principal maken en autoriseren voor het verzenden van metrische gegevens 

Maak een Service-Principal in uw Azure Active Directory-Tenant met behulp van de instructies op het [maken van een Service-Principal](../../active-directory/develop/howto-create-service-principal-portal.md). 

Houd rekening met het volgende wanneer u dit proces door lopen: 

- U kunt elke URL voor de aanmeldings-URL invoeren.  
- Maak een nieuw client geheim voor deze app.  
- Sla de sleutel en de client-ID op voor gebruik in latere stappen.  

Geef de app gemaakt als onderdeel van stap 1, bewaken van de uitgever van metrische gegevens, machtigingen voor de resource waarvoor u metrische gegevens wilt verzenden. Als u van plan bent om de app te gebruiken voor het verzenden van aangepaste metrische gegevens voor een groot aantal resources, kunt u deze machtigingen verlenen aan de resource groep of het abonnements niveau. 

## <a name="get-an-authorization-token"></a>Een autorisatie Token ophalen
Open een opdracht prompt en voer de volgende opdracht uit:

```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step>" -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Sla het toegangs token op uit het antwoord.

![Toegangs token](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>De metriek verzenden via de REST API 

1. Plak de volgende JSON in een bestand en sla het op als **custommetric.jsop** de lokale computer. Werk de tijd parameter in het JSON-bestand bij: 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20", 
        "data": { 
            "baseData": { 
                "metric": "QueueDepth", 
                "namespace": "QueueProcessing", 
                "dimNames": [ 
                  "QueueName", 
                  "MessageType" 
                ], 
                "series": [ 
                  { 
                    "dimValues": [ 
                      "ImagesToProcess", 
                      "JPEG" 
                    ], 
                    "min": 3, 
                    "max": 20, 
                    "sum": 28, 
                    "count": 3 
                  } 
                ] 
            } 
        } 
    } 
    ``` 

1. In het opdracht prompt venster plaatst u de metrische gegevens: 
   - **azureregio**. Moet overeenkomen met de implementatie regio van de resource waarvoor u de metrische gegevens wilt verzenden. 
   - **resourceID**.  De resource-ID van de Azure-resource waarmee u de metrische gegevens wilt bijhouden.  
   - **AccessToken**. Plak het token dat u eerder hebt verkregen.

     ```Shell 
     curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
     ```
1. Wijzig de tijds tempel en waarden in het JSON-bestand. 
1. Herhaal de vorige twee stappen een paar keer, zodat u enkele minuten gegevens hebt.

## <a name="troubleshooting"></a>Problemen oplossen 
Als er een fout bericht wordt weer gegeven met een deel van het proces, kunt u de volgende informatie over het oplossen van problemen overwegen:

1. U kunt geen metrische gegevens uitgeven voor een abonnement of resource groep als uw Azure-resource. 
1. U kunt geen metrische gegevens in de Store plaatsen die meer dan 20 minuten oud zijn. De metrische opslag is geoptimaliseerd voor waarschuwingen en realtime grafieken. 
2. Het aantal dimensie namen moet overeenkomen met de waarden en vice versa. Controleer de waarden. 
2. U kunt metrische gegevens verzenden naar een regio die geen aangepaste metrische gegevens ondersteunt. Zie [ondersteunde regio's](./metrics-custom-overview.md#supported-regions). 



## <a name="view-your-metrics"></a>Uw metrische gegevens weer geven 

1. Meld u aan bij Azure Portal. 

1. Selecteer in het menu links de optie **monitor**. 

1. Selecteer **metrische gegevens** op de pagina **monitor** . 

   ![Metrische gegevens selecteren](./media/metrics-store-custom-rest-api/metrics.png) 

1. Wijzig de aggregatie periode in de **laatste 30 minuten**.  

1. Selecteer in de vervolg keuzelijst **resource** de resource waarmee u de metrische gegevens hebt verzonden.  

1. Selecteer in de vervolg keuzelijst **naam ruimten** de optie **QueueProcessing**. 

1. Selecteer in de vervolg keuzelijst **metrische gegevens** de optie **QueueDepth**.  

 
## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [aangepaste metrische gegevens](./metrics-custom-overview.md).

