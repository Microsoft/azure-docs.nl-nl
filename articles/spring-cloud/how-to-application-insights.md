---
title: Java-agent Application Insights In-Process in Azure Spring Cloud
description: Apps en microservices bewaken met behulp van Application Insights Java In-Process Agent in Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: c7083cb6669d7bc779a8e69babfef38988819f8c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483770"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Application Insights Java In-Process Agent in Azure Spring Cloud (preview)

In dit document wordt uitgelegd hoe u apps en microservices bewaakt met behulp van Application Insights Java-agent in Azure Spring Cloud. 

Met deze functie kunt u het volgende doen:

* Traceringsgegevens zoeken met verschillende filters.
* Afhankelijkheidskaart van microservices weergeven.
* Controleer de prestaties van aanvragen.
* Realtime live metrische gegevens bewaken.
* Controleer mislukte aanvragen.
* Controleer de metrische gegevens van de toepassing.

Application Insights bieden veel waarneembare perspectieven, waaronder:

* Overzicht van de toepassing
* Prestaties
* Fouten
* Metrische gegevens
* Live Metrics
* Beschikbaarheid

## <a name="enable-java-in-process-agent-for-application-insights"></a>Java In-Process-agent inschakelen voor Application Insights

Schakel de previewIn-Process functie java-agent in met behulp van de volgende procedure.

1. Ga naar de overzichtspagina van uw service-exemplaar.
2. Klik **Application Insights** op de blade Bewaking.
3. Klik **op de Application Insights** inschakelen om de integratie **Application Insights** inschakelen.
4. Selecteer een bestaand exemplaar van Application Insights of maak een nieuw exemplaar.
5. Enable **Java in-process agent to** enable preview Java in-process agent feature. Hier kunt u ook de samplingfrequentie van 0 tot 100 aanpassen.
6.  Klik op **Opslaan** om de wijziging op te slaan.

## <a name="portal"></a>Portal

1. Ga naar de **service | Overzichtspagina** en selecteer **Application Insights** in de **sectie** Bewaking. 
2. Klik **op Application Insights** inschakelen om Application Insights in Azure Spring Cloud.
3. Klik **op Enable Java in-process agent om** de Java IPA preview-functie in te schakelen. Wanneer een IPA-preview-functie is ingeschakeld, kunt u één optionele steekproeffrequentie configureren (standaard 10,0%).

  [![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>De functie Application Insights gebruiken

Wanneer de **Application Insights** functie is ingeschakeld, kunt u het volgende doen:

Klik in het linkernavigatiedeelvenster **op Application Insights** naar de **pagina** Overzicht van Application Insights. 

* Klik **op Toepassingskaart** om de status van aanroepen tussen toepassingen te bekijken.

  [![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* Klik op de koppeling tussen klanten-service en `petclinic` voor meer informatie, zoals een query van SQL.

* Klik in het linkernavigatiedeelvenster op **Prestaties** om de prestatiegegevens te zien van alle toepassingen, evenals afhankelijkheden en rollen.

  [![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* Klik in het linkernavigatiedeelvenster op **Fouten om** te zien of er iets onverwachts is voor uw toepassingen.

  [![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* Klik in het linkernavigatiedeelvenster op Metrische gegevens en selecteer de naamruimte. U ziet zowel Spring Boot metrische gegevens als aangepaste metrische gegevens, indien van mij. 

  [![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* Klik in het linkernavigatiedeelvenster op **Live Metrics om** de realtime metrische gegevens voor verschillende dimensies te bekijken.

  [![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* Klik in het navigatiedeelvenster links op **Beschikbaarheid om** de beschikbaarheid en reactiesnelheid van web-apps te controleren door [Beschikbaarheidstests te maken in Application Insights](../azure-monitor/app/monitor-web-app-availability.md).

  [![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>ARM-sjabloon
Als u de sjabloon Azure Resource Manager, kopieert u de volgende inhoud naar `azuredeploy.json` .

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
Pas de ARM-sjabloon toe met de CLI-opdracht:

* Voor een bestaand Azure Spring Cloud-exemplaar:

```azurecli
az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate] "samplingRate" â€“name "assignedName" â€“resource-group "resourceGroupName"
```
* Voor een nieuw gemaakt Azure Spring Cloud-exemplaar:

```azurecli
az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName" --disable-app-insights false --enable-java-agent true --name "assignedName" â€“resource-group "resourceGroupName"
```
* App-inzicht uitschakelen:

```azurecli
az spring-cloud app-insights update --disable â€“name "assignedName" â€“resource-group "resourceGroupName"

```

## <a name="see-also"></a>Zie ook
* [Gedistribueerde tracering gebruiken met Azure Spring Cloud](spring-cloud-howto-distributed-tracing.md)
* [Logboeken en metrische gegevens analyseren](diagnostic-services.md)
* [Logboeken in real-time streamen](spring-cloud-howto-log-streaming.md)
