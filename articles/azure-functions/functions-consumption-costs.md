---
title: De kosten van het verbruiks abonnement in Azure Functions schatten
description: Meer informatie over hoe u de kosten die u kunt doen bij het uitvoeren van uw functie-app in een verbruiks abonnement in azure, beter kunt schatten.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 4967e0ff79a638891da4f784cf2f5f1ca4ddfe51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578565"
---
# <a name="estimating-consumption-plan-costs"></a>Kosten schatten voor verbruiksplan

Er zijn momenteel drie typen hosting plannen voor een app die wordt uitgevoerd in Azure Functions, waarbij elk abonnement een eigen prijs model heeft: 

| Plannen | Description |
| ---- | ----------- |
| [**Verbruik**](consumption-plan.md) | Er worden alleen kosten in rekening gebracht voor de tijd dat uw functie-app wordt uitgevoerd. Dit abonnement bevat een [gratis][prijs pagina] voor granting per abonnement.|
| [**Premium**](functions-premium-plan.md) | Biedt u dezelfde functies en schaal methode als het verbruiks abonnement, maar met verbeterde prestaties en VNET-toegang. De kosten zijn gebaseerd op de gekozen prijs categorie. Zie [Azure functions Premium-abonnement](functions-premium-plan.md)voor meer informatie. |
| [**Toegewezen (App Service)**](dedicated-plan.md) <br/>(Basic-laag of hoger) | Wanneer u moet worden uitgevoerd in specifieke Vm's of in isolatie, gebruikt u aangepaste installatie kopieën of wilt u uw overtollige App Service plan capaciteit gebruiken. Maakt gebruik van de facturering van het [normale app service plan](https://azure.microsoft.com/pricing/details/app-service/). De kosten zijn gebaseerd op de gekozen prijs categorie.|

U hebt het abonnement gekozen dat het beste past bij de prestaties en kosten vereisten van uw functie. Zie [Azure functions schalen en hosten](functions-scale.md)voor meer informatie.

Dit artikel behandelt alleen het verbruiks abonnement, omdat dit plan de variabele kosten als resultaat heeft. In dit artikel vervangt u het artikel [Veelgestelde vragen over het gebruik van kosten voor het verbruiks plan](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ) .

Durable Functions kan ook worden uitgevoerd in een verbruiks abonnement. Zie [Durable functions facturering](./durable/durable-functions-billing.md)voor meer informatie over de kosten overwegingen bij het gebruik van Durable functions.

## <a name="consumption-plan-costs"></a>Kosten van verbruiksabonnement

De uitvoerings *kosten* van het uitvoeren van één functie worden gemeten in *GB seconden*. De uitvoerings kosten worden berekend door het geheugen gebruik te combi neren met de uitvoerings tijd. Een functie waarmee meer kosten worden uitgevoerd, zoals een functie die meer geheugen gebruikt. 

Bekijk een geval waarbij de hoeveelheid geheugen die wordt gebruikt door de functie constant blijft. In dit geval is het berekenen van de kosten eenvoudige vermenigvuldiging. Stel bijvoorbeeld dat uw functie gedurende drie seconden 0,5 GB verbruikt. De uitvoerings kosten zijn dan `0.5GB * 3s = 1.5 GB-seconds` . 

Omdat het geheugen gebruik na verloop van tijd verandert, is de berekening in feite de integraal van het geheugen gebruik in de loop van de tijd.  Het systeem voert deze berekening uit door het geheugen gebruik van het proces (samen met de onderliggende processen) met regel matige tussen pozen te bemonsteren. Zoals vermeld op de [pagina met prijzen], wordt het geheugen gebruik naar boven afgerond naar de dichtstbijzijnde Bucket van 128 MB. Wanneer uw proces 160 MB gebruikt, worden er kosten in rekening gebracht voor 256 MB. De berekening houdt rekening met gelijktijdigheid, wat meerdere gelijktijdige uitvoeringen van functies in hetzelfde proces is.

> [!NOTE]
> Hoewel het CPU-gebruik niet rechtstreeks wordt overwogen tijdens de uitvoerings kosten, kan dit gevolgen hebben voor de kosten wanneer dit van invloed is op de uitvoerings tijd van de functie.

Als er een fout optreedt voordat de functie code wordt uitgevoerd, wordt er geen kosten in rekening gebracht voor een uitvoering van een door HTTP geactiveerde functie. Dit betekent dat 401 reacties van het platform als gevolg van de validatie van de API-sleutel of de App Service verificatie/autorisatie-functie niet meegerekend voor uw uitvoerings kosten. Evenzo worden 5xx-status code reacties niet meegeteld wanneer ze optreden in het platform voordat een functie de aanvraag verwerkt. Een 5xx-antwoord dat door het platform wordt gegenereerd nadat de functie code is gestart, wordt nog steeds beschouwd als een uitvoering, zelfs als de fout niet wordt veroorzaakt door uw functie code.

## <a name="other-related-costs"></a>Andere gerelateerde kosten

Wanneer u de totale kosten van het uitvoeren van uw functies in een plan wilt schatten, moet u er rekening mee houden dat de functions-runtime gebruikmaakt van verschillende andere Azure-Services, die elk afzonderlijk worden gefactureerd. Bij het berekenen van de prijzen voor functie-apps, moet u de triggers en bindingen die u met andere Azure-Services integreert, maken en betalen voor deze extra services. 

Voor functies die in een verbruiks plan worden uitgevoerd, zijn de totale kosten de uitvoerings kosten van uw functies plus de kosten van band breedte en extra services. 

Gebruik de [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/?service=functions)bij het schatten van de totale kosten van uw functie-app en gerelateerde services. 

| Gerelateerde kosten | Beschrijving |
| ------------ | ----------- |
| **Opslagaccount** | Voor elke functie-app moet u een gekoppeld Algemeen [Azure Storage account](../storage/common/storage-introduction.md#types-of-storage-accounts)hebben, dat [afzonderlijk wordt gefactureerd](https://azure.microsoft.com/pricing/details/storage/). Dit account wordt intern gebruikt door de functions-runtime, maar u kunt het ook gebruiken voor opslag triggers en bindingen. Als u geen opslag account hebt, wordt er één voor u gemaakt wanneer de functie-app wordt gemaakt. Zie [vereisten voor opslag accounts](storage-considerations.md#storage-account-requirements)voor meer informatie.|
| **Application Insights** | Functies zijn afhankelijk van [Application Insights](../azure-monitor/app/app-insights-overview.md) om een krachtige bewakings ervaring te bieden voor uw functie-apps. Hoewel dit niet vereist is, moet u [Application Insights-integratie inschakelen](configure-monitoring.md#enable-application-insights-integration). Er wordt elke maand een gratis toekenning van telemetriegegevens opgenomen. Zie [de pagina met prijzen voor Azure monitor](https://azure.microsoft.com/pricing/details/monitor/)voor meer informatie. |
| **Netwerkbandbreedte** | U betaalt niet voor gegevens overdracht tussen Azure-Services in dezelfde regio. U kunt echter kosten in rekening brengen voor uitgaande gegevens overdracht naar een andere regio of buiten Azure. Zie [prijs informatie voor band breedte](https://azure.microsoft.com/pricing/details/bandwidth/)voor meer informatie. |

## <a name="behaviors-affecting-execution-time"></a>Gedrag van invloed op uitvoerings tijd

Het volgende gedrag van uw functies kan de uitvoerings tijd beïnvloeden:

+ **Triggers en bindingen**: de tijd die nodig is voor het lezen van invoer van en het schrijven van uitvoer naar uw [functie bindingen](functions-triggers-bindings.md) , wordt als uitvoerings tijd beschouwd. Als uw functie bijvoorbeeld gebruikmaakt van een uitvoer binding voor het schrijven van een bericht naar een Azure Storage-wachtrij, bevat uw uitvoerings tijd de tijd die nodig is om het bericht naar de wachtrij te schrijven, dat is opgenomen in de berekening van de functie kosten. 

+ **Asynchrone uitvoering**: de tijd die uw functie wacht op de resultaten van een async-aanvraag ( `await` in C#) als uitvoerings tijd. De berekening van de GB-seconde is gebaseerd op de begin-en eind tijd van de functie en het geheugen gebruik in die periode. Wat er gebeurt over die tijd in termen van CPU-activiteit wordt niet in de berekening gefactoreerd. U kunt kosten besparen tijdens asynchrone bewerkingen door gebruik te maken van [Durable functions](durable/durable-functions-overview.md). Er worden geen kosten in rekening gebracht voor de tijd die in de Orchestrator-functies wordt gebruikt om te wachten.

## <a name="viewing-cost-related-data"></a>Kosten-gerelateerde gegevens bekijken

In [uw factuur](../cost-management-billing/understand/download-azure-invoice.md)kunt u de kosten gerelateerde gegevens van het **totale aantal uitvoeringen** bekijken-functies en **uitvoerings tijd-functies**, samen met de werkelijke gefactureerde kosten. Deze factuur gegevens zijn echter een maandelijkse statistische functie voor een eerdere factuur periode. 

### <a name="function-app-level-metrics"></a>Metrische gegevens op app-niveau functie

Voor een beter begrip van de kosten impact van uw functies kunt u Azure Monitor gebruiken om de metrische gegevens weer te geven die momenteel worden gegenereerd door uw functie-apps. U kunt in de [Azure Portal] -of rest-api's een [Azure monitor Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md) gebruiken om deze gegevens op te halen.

#### <a name="monitor-metrics-explorer"></a>Metrics Explorer bewaken

Gebruik [Azure monitor Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md) om kostengerelateerde gegevens weer te geven voor de functie-apps van uw verbruiks plan in een grafische indeling. 

1. Klik boven aan de [Azure Portal] in **Zoek Services, resources en docs**  zoeken naar `monitor` en selecteer **monitor** onder **Services**.

1. Selecteer aan de linkerkant **metrische gegevens**  >  en **Selecteer een resource** en gebruik vervolgens de instellingen onder de afbeelding om uw functie-app te kiezen.

    ![De resource voor de functie-app selecteren](media/functions-consumption-costing/select-a-resource.png)

      
    |Instelling  |Voorgestelde waarde  |Beschrijving  |
    |---------|---------|---------|
    | Abonnement    |  Uw abonnement  | Het abonnement met uw functie-app.  |
    | Resourcegroep     | De resource groep  | De resource groep die de functie-app bevat.   |
    | Resourcetype     |  App Services | Functie-apps worden weer gegeven als App Services instanties in monitor. |
    | Resource     |  Uw functie-app  | De functie-app die moet worden bewaakt.        |

1. Selecteer **Toep assen** om uw functie-app te kiezen als de te controleren bron.

1. Kies bij **metriek** het **aantal functies** en de **som** voor **aggregatie**. Hiermee voegt u de som van de uitvoerings aantallen tijdens de gekozen periode toe aan de grafiek.

    ![Definieer de metrische gegevens van een functie-app die u aan de grafiek wilt toevoegen](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. Selecteer **metrische gegevens toevoegen** en herhaal stap 2-4 om **eenheden voor functie-uitvoering** toe te voegen aan de grafiek. 

De resulterende grafiek bevat de totalen voor zowel uitvoerings metrieken in het gekozen tijds bereik, in dit geval twee uur.

![Grafiek van het aantal functie-en uitvoerings eenheden](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Naarmate het aantal uitvoerings eenheden zoveel groter is dan het aantal uitvoeringen, toont de grafiek alleen uitvoerings eenheden.

Dit diagram toont een totaal van 1.110.000.000 `Function Execution Units` verbruikt in een periode van twee uur, gemeten in MB milliseconden. Als u wilt converteren naar GB-seconden, deelt u door 1024000. In dit voor beeld verbruikt de functie `1110000000 / 1024000 = 1083.98` -app GB seconden. U kunt deze waarde nemen en vermenigvuldigen met de huidige prijs van de uitvoerings tijd op de pagina met prijs informatie voor de [pagina met prijzen][, waarmee]u rekening moet houden met de kosten van deze twee uur, ervan uitgaande dat u al een gratis toekenning van de uitvoerings tijd hebt gebruikt. 

#### <a name="azure-cli"></a>Azure CLI

De [Azure cli](/cli/azure/) bevat opdrachten voor het ophalen van metrische gegevens. U kunt de CLI vanuit een lokale opdracht omgeving of rechtstreeks vanuit de portal gebruiken met behulp van [Azure Cloud shell](../cloud-shell/overview.md). De volgende opdracht [AZ monitor Metrics List](/cli/azure/monitor/metrics#az-monitor-metrics-list) retourneert bijvoorbeeld elk uur gegevens over dezelfde tijds periode die eerder is gebruikt.

Zorg ervoor dat u vervangt door de `<AZURE_SUBSCRIPTON_ID>` id van uw Azure-abonnement met de opdracht.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

Met deze opdracht wordt een JSON-nettolading geretourneerd die eruitziet als in het volgende voor beeld:

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
Dit specifieke antwoord laat zien dat van `2019-09-11T21:46` tot `2019-09-11T23:18` , dat de app 1110000000 MB milliseconden (1083,98 GB-seconden) verbruikt.

### <a name="function-level-metrics"></a>Metrische functie niveaus

Eenheden voor het uitvoeren van functies zijn een combi natie van uitvoerings tijd en het geheugen gebruik. Dit maakt het een lastige meet waarde voor het geheugen gebruik. Geheugen gegevens zijn momenteel niet beschikbaar via Azure Monitor. Als u echter het geheugen gebruik van uw app wilt optimaliseren, kunt u de gegevens van de prestatie meter items die worden verzameld door Application Insights gebruiken.  

Als u dit nog niet hebt gedaan, [schakelt u Application Insights in uw functie-app in](configure-monitoring.md#enable-application-insights-integration). Als deze integratie is ingeschakeld, kunt u [een query uitvoeren op deze telemetriegegevens in de portal](analyze-telemetry-data.md#query-telemetry-data). 

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het bewaken van functie-apps](functions-monitoring.md)

[pagina met prijzen]:https://azure.microsoft.com/pricing/details/functions/
[Azure-portal]: https://portal.azure.com
