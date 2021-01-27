---
title: Application Insights Java In-Process-agent gebruiken in azure lente-Cloud
description: Apps en micro services bewaken met Application Insights Java In-Process agent in azure lente Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: c4871c3de8028eec1b6184c1d03ac2180b50f57d
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881347"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Application Insights Java In-Process-agent in azure lente Cloud (preview-versie)

In dit document wordt uitgelegd hoe u apps en micro services bewaken met behulp van de Application Insights Java-agent in azure lente Cloud. 

Met deze functie kunt u het volgende doen:

* Traceringsgegevens zoeken met verschillende filters.
* Afhankelijkheids toewijzing van micro services weer geven.
* Controleer de prestaties van de aanvraag.
* Real-time dynamische metrische gegevens bewaken.
* Controleren op mislukte aanvragen.
* Controleer de metrische gegevens van de toepassing.

Application Insights bieden een groot aantal waarneem bare perspectieven, waaronder:

* Overzicht van de toepassing
* Prestaties
* Fouten
* Metrische gegevens
* Live Metrics
* Beschikbaarheid

## <a name="enable-java-in-process-agent-for-application-insights"></a>Java In-Process-agent voor Application Insights inschakelen

Gebruik de volgende procedure om de preview-functie van Java In-Process agent in te scha kelen.

1. Ga naar de pagina service overzicht van uw service-exemplaar.
2. Klik op **Application Insights** item onder bewaking Blade.
3. Klik op **inschakelen Application Insights** knop om **Application Insights** integratie in te scha kelen.
4. Selecteer een bestaande instantie van Application Insights of maak een nieuwe.
5. Kuiken **schakelt java in-process agent** in om preview-functie java in-process in te scha kelen. Hier kunt u ook de sampling frequentie aanpassen van 0 tot 100.
6.  Klik op **Opslaan** om de wijziging op te slaan.

## <a name="portal"></a>Portal

1. Ga naar de **service | Overzicht** pagina en selecteer **Application Insights** in het gedeelte **bewaking** . 
2. Klik op **inschakelen Application Insights** om Application Insights in te scha kelen in azure lente-Cloud.
3. Klik op **Java inschakelen in process-agent** om de preview-functie van Java IPA in te scha kelen. Wanneer een IPA preview-functie is ingeschakeld, kunt u één optionele sampling frequentie (standaard 10,0%) configureren.

  [![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>De functie Application Insights gebruiken

Wanneer de **Application Insights** functie is ingeschakeld, kunt u het volgende doen:

Klik in het navigatie deel venster links op **Application Insights** om naar de pagina **overzicht** van Application Insights te gaan. 

* Klik op **toepassings toewijzing** om de status van aanroepen tussen toepassingen weer te geven.

  [![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* Klik op de koppeling tussen klanten-service en `petclinic` om meer details weer te geven, zoals een query van SQL.

* Klik in het navigatie deel venster links op **prestaties** om de prestatie gegevens van alle toepassings bewerkingen te bekijken, evenals afhankelijkheden en rollen.

  [![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* Klik in het navigatie deel venster links op **fouten** om te zien of er iets onverwacht van uw toepassingen is.

  [![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* Klik in het navigatie deel venster links op **metrische gegevens** en selecteer de naam ruimte. u ziet dan de standaard waarden voor veer boot en aangepaste metrische gegevens, indien van toepassing.

  [![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* Klik in het navigatie deel venster aan de linkerkant op **Live metrische** gegevens om de metrische gegevens voor de realtime voor verschillende dimensies te bekijken.

  [![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* Klik in het navigatie deel venster links op **Beschik baarheid** om de beschik baarheid en reactie snelheid van web-apps te bewaken door [beschikbaarheids tests te maken in Application Insights](../azure-monitor/app/monitor-web-app-availability.md).

  [![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>ARM-sjabloon
Als u de sjabloon Azure Resource Manager wilt gebruiken, kopieert u de volgende inhoud naar `azuredeploy.json` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.AppPlatform/Spring",
            "name": "customize this",
            "apiVersion": "2020-07-01",
            "location": "[resourceGroup().location]",
            "resources": [
                {
                    "type": "monitoringSettings",
                    "apiVersion": "2020-11-01-preview",
                    "name": "default",
                    "properties": {
                        "appInsightsInstrumentationKey": "00000000-0000-0000-0000-000000000000",
                        "appInsightsSamplingRate": 88.0
                    },
                    "dependsOn": [
                        "[resourceId('Microsoft.AppPlatform/Spring', 'customize this')]"
                    ]
                }
            ],
            "properties": {}
        }
    ]
}
```

## <a name="cli"></a>CLI
ARM-sjabloon Toep assen met de CLI-opdracht:

* Voor een bestaande Azure veer Cloud-instantie:

```azurecli
az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate] "samplingRate" –name "assignedName" –resource-group "resourceGroupName"
```
* Voor een nieuw Azure lente Cloud-exemplaar:

```azurecli
az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName" --disable-app-insights false --enable-java-agent true --name "assignedName" –resource-group "resourceGroupName"
```
* App-inzicht uitschakelen:

```azurecli
az spring-cloud app-insights update --disable –name "assignedName" –resource-group "resourceGroupName"

```

## <a name="see-also"></a>Zie ook
* [Gedistribueerde tracering gebruiken met Azure Spring Cloud](spring-cloud-tutorial-distributed-tracing.md)
* [Logboeken en metrische gegevens analyseren](diagnostic-services.md)
* [Logboeken in real-time streamen](spring-cloud-howto-log-streaming.md)