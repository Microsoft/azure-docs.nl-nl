---
title: Implementatie met een nul-uitval tijd voor Durable Functions
description: Meer informatie over het inschakelen van uw Durable Functions-indeling voor implementaties met een nul-uitval tijd.
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.custom: fasttrack-edit
ms.openlocfilehash: 707d624c47c536e00e98910a8902772703733515
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558760"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Implementatie met een nul-uitval tijd voor Durable Functions

Het [betrouw bare uitvoerings model](./durable-functions-orchestrations.md) van Durable functions vereist dat indelingen deterministisch zijn, wat een extra uitdaging vormt om te overwegen wanneer u updates implementeert. Wanneer een implementatie wijzigingen bevat in de hand tekeningen of Orchestrator-logica van de activiteit functie, mislukken in vlucht indelings instanties. Deze situatie is met name een probleem voor exemplaren van langlopende orchestrator, die uren of werk dagen kunnen Voorst Ellen.

Om te voor komen dat deze fouten zich voordoen, hebt u twee opties: 
- Stel uw implementatie uit totdat alle actieve Orchestrator-exemplaren zijn voltooid.
- Zorg ervoor dat alle actieve Orchestrator-instanties de bestaande versies van uw functies gebruiken. 

In het volgende diagram worden de drie belangrijkste strategieën vergeleken om een implementatie met een nul-uitval tijd voor Durable Functions te krijgen: 

| Strategie |  Wanneer gebruikt u dit? | Voordelen | Nadelen |
| -------- | ------------ | ---- | ---- |
| [Versiebeheer](#versioning) |  Toepassingen die geen regel matige [Afbrekings wijzigingen](durable-functions-versioning.md) ondervinden. | Eenvoudig te implementeren. |  Verbeterde grootte van de functie-app in het geheugen en het aantal functies.<br/>Code duplicatie. |
| [Status controle met sleuf](#status-check-with-slot) | Een systeem dat geen langlopende Orchestrations heeft die meer dan 24 uur of veelvuldig overlappende integraties bevat. | Eenvoudige code basis.<br/>Vereist geen extra functie-app-beheer. | Vereist extra opslag account of taak hub-beheer.<br/>Er is een tijds periode vereist wanneer er geen Orchestrations worden uitgevoerd. |
| [Toepassings routering](#application-routing) | Een systeem dat geen tijd punten heeft wanneer er sprake is van niet-actieve orchestrator, zoals Peri Oden met een indeling die meer dan 24 uur of met regel matig overlappende integraties bevat. | Behandelt nieuwe versies van systemen met doorlopende integraties die wijzigingen hebben onderlopen. | Vereist een intelligente toepassings router.<br/>Kan Maxi maal het aantal functie-apps dat is toegestaan door uw abonnement. De standaardwaarde is 100. |

## <a name="versioning"></a>Versiebeheer

Definieer nieuwe versies van uw functies en verlaat de oude versies in uw functie-app. Zoals u kunt zien in het diagram, wordt de versie van een functie onderdeel van de naam. Omdat eerdere versies van functies behouden blijven, kunnen in-Flight-indelings instanties blijven verwijzen. Ondertussen worden aanvragen voor nieuwe Orchestrator-instanties aangeroepen voor de meest recente versie, die de functie Orchestration client kan verwijzen van een app-instelling.

![Strategie voor versie beheer](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

In deze strategie moet elke functie worden gekopieerd en zijn verwijzingen naar andere functies moeten worden bijgewerkt. U kunt dit vereenvoudigen door een script te schrijven. Hier volgt een voor beeld van een [project](https://github.com/TsuyoshiUshio/DurableVersioning) met een migratie script.

>[!NOTE]
>Deze strategie maakt gebruik van implementatie sleuven om uitval tijd tijdens de implementatie te voor komen. Zie [Azure functions implementatie sleuven](../functions-deployment-slots.md)voor meer informatie over het maken en gebruiken van nieuwe implementatie sleuven.

## <a name="status-check-with-slot"></a>Status controle met sleuf

Terwijl de huidige versie van de functie-app wordt uitgevoerd in uw productie sleuf, implementeert u de nieuwe versie van uw functie-app in uw staging-sleuf. Controleer voordat u uw productie-en staging-sleuven verwisselt of er exemplaren van de indeling worden uitgevoerd. Nadat alle indelings instanties zijn voltooid, kunt u de swap uitvoeren. Deze strategie werkt wanneer u voorspel bare Peri Oden hebt wanneer er geen indelings instanties in vlucht zijn. Dit is de beste benadering wanneer uw Orchestrations niet lang worden uitgevoerd en wanneer uw Orchestration-uitvoeringen niet vaak overlappen.

### <a name="function-app-configuration"></a>Configuratie van functie-app

Gebruik de volgende procedure om dit scenario in te stellen.

1. [Voeg implementatie sleuven](../functions-deployment-slots.md#add-a-slot) toe aan uw functie-app voor fase ring en productie.

1. Stel voor elke sleuf de [toepassings instelling AzureWebJobsStorage](../functions-app-settings.md#azurewebjobsstorage) in op de Connection String van een gedeeld opslag account. Dit opslag account connection string wordt gebruikt door de Azure Functions-runtime. Dit account wordt gebruikt door de Azure Functions runtime en beheert de sleutels van de functie.

1. Maak voor elke sleuf een nieuwe app-instelling, bijvoorbeeld `DurableManagementStorage` . Stel de waarde ervan in op de connection string van verschillende opslag accounts. Deze opslag accounts worden gebruikt door de extensie Durable Functions voor [betrouw bare uitvoering](./durable-functions-orchestrations.md). Gebruik een afzonderlijk opslag account voor elke sleuf. Markeer deze instelling niet als een implementatie site-instelling.

1. In dehost.jsvan de functie-app [ in de sectie durableTask van het bestand](durable-functions-bindings.md#hostjson-settings), geeft `connectionStringName` u (duurzaam 2. x) of `azureStorageConnectionStringName` (duurzaam 1. x) op als de naam van de app-instelling die u in stap 3 hebt gemaakt.

In het volgende diagram ziet u de beschreven configuratie van implementatie sleuven en opslag accounts. In dit mogelijke scenario voor voor implementatie wordt versie 2 van een functie-app uitgevoerd in de productie omgeving, terwijl versie 1 in de faserings sleuf blijft.

![Implementatie sleuven en opslag accounts](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples&quot;></a>host.jsop voor beelden

De volgende JSON-fragmenten zijn voor beelden van de connection string-instelling in de *host.jsvoor* het bestand.

#### <a name=&quot;functions-20&quot;></a>Functies 2,0

```json
{
  &quot;version&quot;: 2.0,
  &quot;extensions&quot;: {
    &quot;durableTask&quot;: {
      &quot;hubName&quot;: &quot;MyTaskHub&quot;,
      &quot;storageProvider&quot;: {
        &quot;connectionStringName&quot;: &quot;DurableManagementStorage&quot;
      }
    }
  }
}
```

#### <a name=&quot;functions-1x&quot;></a>Functions 1.x

```json
{
  &quot;durableTask&quot;: {
    &quot;azureStorageConnectionStringName&quot;: &quot;DurableManagementStorage&quot;
  }
}
```

### <a name=&quot;cicd-pipeline-configuration&quot;></a>Configuratie van CI/CD-pijp lijn

Configureer uw CI/CD-pijp lijn zodanig dat deze alleen kan worden geïmplementeerd als uw functie-app geen in behandeling zijnde of actieve Orchestrator-exemplaren bevat. Wanneer u Azure-pijp lijnen gebruikt, kunt u een functie maken die controleert op deze voor waarden, zoals in het volgende voor beeld:

```csharp
[FunctionName(&quot;StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var result = await client.ListInstancesAsync(new OrchestrationStatusQueryCondition() { RuntimeStatus = runtimeStatus }, CancellationToken.None);
    return (ActionResult)new OkObjectResult(new { HasRunning = result.DurableOrchestrationState.Any() });
}
```

Configureer vervolgens de staging-Gate zodat er wordt gewacht tot er geen Orchestrations worden uitgevoerd. Zie [release Deployment Control using Gates](/azure/devops/pipelines/release/approvals/gates) (Engelstalig) voor meer informatie.

![Implementatiepoort](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure-pijp lijnen controleert uw functie-app voor het uitvoeren van Orchestrator-instanties voordat de implementatie wordt gestart.

![Implementatie poort (wordt uitgevoerd)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

Nu moet de nieuwe versie van de functie-app worden geïmplementeerd in de faserings sleuf.

![Faserings sleuf](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

Ten slotte wisselen de sleuven. 

Toepassings instellingen die niet zijn gemarkeerd als implementatie site-instellingen, worden ook omgewisseld, zodat de versie 2-app de verwijzing naar het opslag account A bewaart. Omdat de indelings status wordt bijgehouden in het opslag account, blijven de door de toepassing van versie 2 uitgevoerde integraties in de nieuwe sleuf zonder onderbreking worden uitgevoerd.

![Implementatiesite](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

Als u hetzelfde opslag account voor beide sleuven wilt gebruiken, kunt u de namen van uw taak hubs wijzigen. In dit geval moet u de status van uw sleuven en de HubName-instellingen van uw app beheren. Zie [taak hubs in Durable functions](durable-functions-task-hubs.md)voor meer informatie.

## <a name="application-routing"></a>Toepassings routering

Deze strategie is het meest complexe. Het kan echter worden gebruikt voor functie-apps waarvoor geen tijd is tussen het uitvoeren van Orchestrations.

Voor deze strategie moet u een *toepassings router* vóór uw Durable functions maken. Deze router kan worden geïmplementeerd met Durable Functions. De router is verantwoordelijk voor het volgende:

* Implementeer de functie-app.
* De versie van Durable Functions beheren. 
* Routerings aanvragen voor functie-apps.

De eerste keer dat een Orchestrator-aanvraag wordt ontvangen, voert de router de volgende taken uit:

1. Hiermee maakt u een nieuwe functie-app in Azure.
2. Hiermee implementeert u de code van uw functie-app in de nieuwe functie-app in Azure.
3. Stuurt de Orchestration-aanvraag door naar de nieuwe app.

De router beheert de status van de versie van de code van uw app die wordt geïmplementeerd in welke functie-app in Azure.

![Toepassings routering (de eerste keer)](media/durable-functions-zero-downtime-deployment/application-routing.png)

De router stuurt implementatie-en indelings aanvragen naar de juiste functie-app op basis van de versie die met de aanvraag is verzonden. De patch versie wordt genegeerd.

Wanneer u een nieuwe versie van uw app implementeert zonder een belang rijke wijziging, kunt u de patch versie verhogen. De router wordt geïmplementeerd in uw bestaande functie-app en verzendt aanvragen voor de oude en nieuwe versies van de code die naar dezelfde functie-app worden doorgestuurd.

![Sollicitatie routering (geen breuk wijzigen)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

Wanneer u een nieuwe versie van uw app implementeert met een belang rijke wijziging, kunt u de primaire of secundaire versie verhogen. Vervolgens maakt de toepassings router een nieuwe functie-app in azure, implementeert deze en worden aanvragen voor de nieuwe versie van uw app hiernaar gerouteerd. In het volgende diagram wordt het uitvoeren van Orchestrations op de 1.0.1-versie van de app actief blijven, maar aanvragen voor de 1.1.0-versie worden doorgestuurd naar de nieuwe functie-app.

![Sollicitatie routering (breuk wijzigen)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

De router controleert de status van de integraties op de 1.0.1-versie en verwijdert apps nadat alle Orchestrations zijn voltooid. 

### <a name="tracking-store-settings"></a>Opslag instellingen bijhouden

Elke functie-app moet afzonderlijke plannings wachtrijen gebruiken, mogelijk in afzonderlijke opslag accounts. Als u in alle versies van uw toepassing alle indelingen van exemplaren wilt opvragen, kunt u exemplaar-en geschiedenis tabellen delen in uw functie-apps. U kunt tabellen delen door de `trackingStoreConnectionStringName` instellingen en te configureren `trackingStoreNamePrefix` in de [host.jsop instellingen](durable-functions-bindings.md#host-json) bestand, zodat ze allemaal dezelfde waarden gebruiken.

Zie [instanties beheren in Durable functions in azure](durable-functions-instance-management.md)voor meer informatie.

![Opslag instellingen bijhouden](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Versie beheer Durable Functions](durable-functions-versioning.md)
