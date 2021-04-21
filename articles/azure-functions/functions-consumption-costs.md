---
title: Kosten van verbruiksplannen schatten in Azure Functions
description: Meer informatie over het beter inschatten van de kosten die u kunt maken bij het uitvoeren van uw functie-app in een verbruiksplan in Azure.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 648be6325cce5bad36795b113c8bbccb3e21d37b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773999"
---
# <a name="estimating-consumption-plan-costs"></a>Kosten schatten voor verbruiksplan

Er zijn momenteel drie typen hostingplannen voor een app die wordt uitgevoerd in Azure Functions, met elk abonnement een eigen prijsmodel: 

| Plannen | Description |
| ---- | ----------- |
| [**Verbruik**](consumption-plan.md) | Er worden alleen kosten in rekening gebracht voor de tijd dat uw functie-app wordt uitgevoerd. Dit abonnement bevat een pagina met[gratis toekenningsprijzen] per abonnement. []|
| [**Premium**](functions-premium-plan.md) | Biedt u dezelfde functies en schaalmechanisme als het verbruiksplan, maar met verbeterde prestaties en VNET-toegang. De kosten zijn gebaseerd op de gekozen prijscategorie. Zie Premium-abonnement [Azure Functions voor meer informatie.](functions-premium-plan.md) |
| [**Toegewezen (App Service)**](dedicated-plan.md) <br/>(Basic-laag of hoger) | Wanneer u wilt uitvoeren in toegewezen VM's of geïsoleerd, gebruikt u aangepaste afbeeldingen of wilt u uw overtollige capaciteit van App Service gebruiken. Maakt [gebruik van App Service abonnement.](https://azure.microsoft.com/pricing/details/app-service/) De kosten zijn gebaseerd op de gekozen prijscategorie.|

U hebt het plan gekozen dat het beste ondersteuning biedt voor uw functieprestaties en kostenvereisten. Zie Schalen en [hosten Azure Functions voor meer informatie.](functions-scale.md)

In dit artikel wordt alleen het verbruiksplan beschreven, omdat dit plan resulteert in variabele kosten. In dit artikel wordt het artikel Veelgestelde vragen over facturering van [verbruiksplannen](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ) over kosten overschreven.

Durable Functions kunnen ook worden uitgevoerd in een verbruiksplan. Zie facturering voor meer informatie over de kostenoverwegingen bij Durable Functions [gebruik van Durable Functions facturering.](./durable/durable-functions-billing.md)

## <a name="consumption-plan-costs"></a>Kosten van verbruiksabonnement

De *uitvoeringskosten* van een uitvoering van één functie worden gemeten in *GB-seconden.* De uitvoeringskosten worden berekend door het geheugengebruik te combineren met de uitvoeringstijd. Een functie die langer wordt uitgevoerd, kost meer, net als een functie die meer geheugen verbruikt. 

Denk bijvoorbeeld aan een geval waarin de hoeveelheid geheugen die wordt gebruikt door de functie constant blijft. In dit geval is het berekenen van de kosten een eenvoudige vermenigvuldiging. Stel bijvoorbeeld dat uw functie 0,5 GB heeft verbruikt voor 3 seconden. Vervolgens zijn de uitvoeringskosten `0.5GB * 3s = 1.5 GB-seconds` . 

Omdat het geheugengebruik na een periode verandert, is de berekening in feite het integraal onderdeel van het geheugengebruik in de tijd.  Het systeem doet deze berekening door regelmatig een steekproef te nemen van het geheugengebruik van het proces (samen met onderliggende processen). Zoals vermeld op de [pagina met prijzen,]wordt het geheugengebruik afgerond naar de dichtstbijzijnde bucket van 128 MB. Wanneer uw proces 160 MB gebruikt, worden er kosten in rekening gebracht voor 256 MB. Bij de berekening wordt rekening gehouden met gelijktijdigheid. Dit zijn meerdere gelijktijdige functie-uitvoeringen in hetzelfde proces.

> [!NOTE]
> Hoewel HET CPU-gebruik niet rechtstreeks wordt overwogen in de uitvoeringskosten, kan dit van invloed zijn op de kosten wanneer dit van invloed is op de uitvoeringstijd van de functie.

Wanneer er voor een http-geactiveerde functie een fout optreedt voordat uw functiecode wordt uitgevoerd, worden er geen kosten in rekening gebracht voor een uitvoering. Dit betekent dat 401-reacties van het platform vanwege de validatie van de API-sleutel of de functie App Service-verificatie/autorisatie niet meetellen voor de uitvoeringskosten. Op dezelfde manier worden reacties van 5xx-statuscodes niet geteld wanneer ze plaatsvinden op het platform voordat een functie de aanvraag verwerkt. Een 5xx-antwoord dat door het platform wordt gegenereerd nadat uw functiecode is gestart om uit te voeren, wordt nog steeds geteld als een uitvoering, zelfs als de fout niet wordt gegenereerd door uw functiecode.

## <a name="other-related-costs"></a>Overige gerelateerde kosten

Bij het schatten van de totale kosten van het uitvoeren van uw functies in een plan, moet u er rekening mee houden dat de Functions-runtime gebruikmaakt van verschillende andere Azure-services, die elk afzonderlijk worden gefactureerd. Wanneer u de prijzen voor functie-apps berekent, moet u voor alle triggers en bindingen die u hebt die zijn geïntegreerd met andere Azure-services, die extra services maken en betalen. 

Voor functies die worden uitgevoerd in een verbruiksplan, zijn de totale kosten de uitvoeringskosten van uw functies, plus de kosten van bandbreedte en aanvullende services. 

Gebruik de Azure-prijscalculator bij het schatten van de totale kosten van uw functie-app en [gerelateerde services.](https://azure.microsoft.com/pricing/calculator/?service=functions) 

| Gerelateerde kosten | Beschrijving |
| ------------ | ----------- |
| **Opslagaccount** | Elke functie-app vereist dat u een gekoppeld Algemeen [Azure Storage account](../storage/common/storage-introduction.md#types-of-storage-accounts)hebt, dat [afzonderlijk wordt gefactureerd.](https://azure.microsoft.com/pricing/details/storage/) Dit account wordt intern gebruikt door de Functions-runtime, maar u kunt het ook gebruiken voor Storage-triggers en -bindingen. Als u geen opslagaccount hebt, wordt er een voor u gemaakt wanneer de functie-app wordt gemaakt. Zie Vereisten voor [opslagaccounts voor meer informatie.](storage-considerations.md#storage-account-requirements)|
| **Application Insights** | Functions is afhankelijk van [Application Insights](../azure-monitor/app/app-insights-overview.md) een krachtige bewakingservaring te bieden voor uw functie-apps. Hoewel dit niet vereist is, moet u [de Application Insights inschakelen.](configure-monitoring.md#enable-application-insights-integration) Elke maand wordt een gratis toekenning van telemetriegegevens opgenomen. Zie de pagina met prijzen [Azure Monitor meer informatie.](https://azure.microsoft.com/pricing/details/monitor/) |
| **Netwerkbandbreedte** | U betaalt niet voor gegevensoverdracht tussen Azure-services in dezelfde regio. U kunt echter kosten in rekening brengen voor uitgaande gegevensoverdrachten naar een andere regio of buiten Azure. Zie Prijsinformatie voor bandbreedte [voor meer informatie.](https://azure.microsoft.com/pricing/details/bandwidth/) |

## <a name="behaviors-affecting-execution-time"></a>Gedrag dat de uitvoeringstijd beïnvloedt

Het volgende gedrag van uw functies kan van invloed zijn op de uitvoeringstijd:

+ **Triggers en bindingen:** de tijd die nodig [is](functions-triggers-bindings.md) om invoer van en schrijfuitvoer naar uw functiebindingen te lezen, wordt geteld als uitvoeringstijd. Wanneer uw functie bijvoorbeeld een uitvoerbinding gebruikt om een bericht naar een Azure-opslagwachtrij te schrijven, bevat de uitvoeringstijd de tijd die nodig is om het bericht naar de wachtrij te schrijven. Dit is opgenomen in de berekening van de functiekosten. 

+ **Asynchrone uitvoering:** de tijd dat uw functie wacht op de resultaten van een asynchrone aanvraag ( `await` in C#) wordt geteld als uitvoeringstijd. De berekening van GB-seconde is gebaseerd op de begin- en eindtijd van de functie en het geheugengebruik in die periode. Wat er in die periode gebeurt in termen van CPU-activiteit, wordt niet meegenomen in de berekening. Mogelijk kunt u de kosten verlagen tijdens asynchrone bewerkingen met behulp van [Durable Functions](durable/durable-functions-overview.md). U wordt niet gefactureerd voor de tijd die is besteed aan awaits in orchestrator-functies.

## <a name="viewing-cost-related-data"></a>Kostengerelateerde gegevens weergeven

In [uw factuur](../cost-management-billing/understand/download-azure-invoice.md)kunt u de kostengerelateerde gegevens bekijken van Totaal aantal uitvoeringen **-** Functies en Uitvoeringstijd - **Functies**, samen met de werkelijke gefactureerde kosten. Deze factuurgegevens zijn echter een maandelijkse statistische functie voor een eerdere factuurperiode. 

### <a name="function-app-level-metrics"></a>Metrische gegevens op app-niveau van functie

Als u meer inzicht wilt krijgen in de kostenimpact van uw functies, kunt u Azure Monitor om kostengerelateerde metrische gegevens weer te geven die momenteel worden gegenereerd door uw functie-apps. U kunt deze gegevens [Azure Monitor metrics explorer](../azure-monitor/essentials/metrics-getting-started.md) in de [Azure Portal] of REST API's.

#### <a name="monitor-metrics-explorer"></a>Metrics Explorer bewaken

Gebruik [Azure Monitor Metrics Explorer om](../azure-monitor/essentials/metrics-getting-started.md) kostengerelateerde gegevens voor uw verbruiksplanfunctie-apps in een grafische indeling weer te geven. 

1. Bovenaan de Azure Portal [zoeken] naar **services, resources** en documenten zoeken en controleren selecteren `monitor` onder **Services.** 

1. Selecteer aan de linkerkant  >  **Metrische gegevens Selecteer een resource** en gebruik vervolgens de instellingen onder de afbeelding om uw functie-app te kiezen.

    ![Uw functie-app-resource selecteren](media/functions-consumption-costing/select-a-resource.png)

      
    |Instelling  |Voorgestelde waarde  |Beschrijving  |
    |---------|---------|---------|
    | Abonnement    |  Uw abonnement  | Het abonnement met uw functie-app.  |
    | Resourcegroep     | Uw resourcegroep  | De resourcegroep die uw functie-app bevat.   |
    | Resourcetype     |  App Services | Functie-apps worden weergegeven als App Services in Monitor. |
    | Resource     |  Uw functie-app  | De functie-app die moet worden bewaakt.        |

1. Selecteer **Toepassen om** uw functie-app te kiezen als de resource die u wilt bewaken.

1. Kies **in Metrische** gegevens **Het aantal uitvoeringen van functies** en **Som** **voor Aggregatie**. Hiermee wordt de som van het aantal uitvoeringen tijdens de gekozen periode toegevoegd aan de grafiek.

    ![Metrische gegevens voor een functie-app definiëren om toe te voegen aan de grafiek](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. Selecteer **Metrische waarde toevoegen** en herhaal stap 2-4 om functie-uitvoeringseenheden aan **de** grafiek toe te voegen. 

De resulterende grafiek bevat de totalen voor beide metrische uitvoeringsgegevens in het gekozen tijdsbereik, in dit geval twee uur.

![Grafiek van het aantal uitvoeringen van functies en uitvoeringseenheden](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Omdat het aantal uitvoeringseenheden zoveel groter is dan het aantal uitvoeringen, toont de grafiek alleen uitvoeringseenheden.

In dit diagram ziet u in totaal 1,11 miljard verbruikt in een periode van twee uur, gemeten `Function Execution Units` in MB milliseconden. Als u wilt converteren naar GB-seconden, deelt u door 1024000. In dit voorbeeld heeft de functie-app `1110000000 / 1024000 = 1083.98` GB-seconden verbruikt. U kunt deze waarde nemen en vermenigvuldigen met de huidige prijs van de uitvoeringstijd op de pagina met prijzen voor [Functions,][]waar u de kosten van deze twee uur krijgt, ervan uitgaande dat u al gratis toekenningen van uitvoeringstijd hebt gebruikt. 

#### <a name="azure-cli"></a>Azure CLI

De [Azure CLI heeft](/cli/azure/) opdrachten voor het ophalen van metrische gegevens. U kunt de CLI gebruiken vanuit een lokale opdrachtomgeving of rechtstreeks vanuit de portal met behulp [van Azure Cloud Shell](../cloud-shell/overview.md). De volgende opdracht [az monitor metrics list retourneert](/cli/azure/monitor/metrics#az_monitor_metrics_list) bijvoorbeeld elk uur gegevens over dezelfde periode die eerder is gebruikt.

Zorg ervoor dat u vervangt `<AZURE_SUBSCRIPTON_ID>` door de id van uw Azure-abonnement met de opdracht .

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

Deze opdracht retourneert een JSON-nettolading die lijkt op het volgende voorbeeld:

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
Dit specifieke antwoord laat zien dat de app van tot `2019-09-11T21:46` `2019-09-11T23:18` 11100000000 MB heeft verbruikt (1083,98 GB seconden).

### <a name="function-level-metrics"></a>Metrische gegevens op functieniveau

Functie-uitvoeringseenheden zijn een combinatie van uitvoeringstijd en uw geheugengebruik, waardoor het lastig is om inzicht te krijgen in het geheugengebruik. Geheugengegevens zijn momenteel geen metrische gegevens die beschikbaar zijn via Azure Monitor. Als u echter het geheugengebruik van uw app wilt optimaliseren, kunt u de prestatiemetergegevens gebruiken die door de Application Insights.  

Als u dit nog niet hebt gedaan, kunt [u Application Insights in uw functie-app inschakelen.](configure-monitoring.md#enable-application-insights-integration) Als deze integratie is ingeschakeld, kunt u [deze telemetriegegevens opvragen in de portal](analyze-telemetry-data.md#query-telemetry-data). 

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over functie-apps bewaken](functions-monitoring.md)

[pagina met prijzen]:https://azure.microsoft.com/pricing/details/functions/
[Azure-portal]: https://portal.azure.com
