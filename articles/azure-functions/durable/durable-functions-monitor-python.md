---
title: Monitors in Durable Functions (python)-Azure
description: Meer informatie over het implementeren van een status monitor met de extensie Durable Functions voor Azure Functions (python).
author: davidmrdavid
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: azfuncdf
ms.openlocfilehash: 62b3c9bb1c6fd53d9f11227a9d7e774d56859d04
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434760"
---
# <a name="monitor-scenario-in-durable-functions---github-issue-monitoring-sample"></a>Het scenario bewaken in het Durable Functions-GitHub-probleem bewakings voorbeeld

Het monitor patroon verwijst naar een flexibel terugkerend proces in een werk stroom, bijvoorbeeld polling totdat aan bepaalde voor waarden wordt voldaan. In dit artikel vindt u een voor beeld waarin Durable Functions wordt gebruikt voor het implementeren van bewaking.

## <a name="prerequisites"></a>Vereisten

* [Het artikel Snelstartgids volt ooien](quickstart-python-vscode.md)
* [Kloon of down load het project met voor beelden van GitHub](https://github.com/Azure/azure-functions-durable-python/tree/main/samples/)


## <a name="scenario-overview"></a>Overzicht van scenario's

In dit voor beeld wordt het aantal problemen in een GitHub-opslag plaats gecontroleerd en wordt de gebruiker gewaarschuwd als er meer dan drie openstaande problemen zijn. U kunt een normale, door timer geactiveerde functie gebruiken om het geopende aantal problemen met regel matige intervallen aan te vragen. Maar een probleem met deze aanpak is het **beheer van de levens duur**. Als er slechts één waarschuwing moet worden verzonden, moet de monitor zichzelf uitschakelen nadat er drie of meer problemen zijn gedetecteerd. Het bewakings patroon kan zijn eigen uitvoering kunnen beëindigen, onder andere voor delen:

* Bewaakte uitvoeringen op intervallen, niet schema's: een timer trigger wordt elk uur *uitgevoerd* ; een monitor *wacht* één uur tussen de acties. De acties van een monitor hebben geen overlap, tenzij opgegeven, wat belang rijk kan zijn voor langlopende taken.
* Monitors kunnen dynamische intervallen hebben: de wacht tijd kan worden gewijzigd op basis van een voor waarde.
* Monitors kunnen worden beëindigd als aan een bepaalde voor waarde is voldaan of door een ander proces wordt beëindigd.
* Monitors kunnen para meters hebben. In het voor beeld ziet u hoe hetzelfde opslag plaats-bewakings proces kan worden toegepast op alle aangevraagde open bare GitHub opslag plaats en het telefoon nummer.
* Monitors zijn schaalbaar. Omdat elke monitor een indelings exemplaar is, kunnen er meerdere monitors worden gemaakt zonder nieuwe functies te maken of meer code te definiëren.
* Monitors kunnen eenvoudig worden geïntegreerd in grotere werk stromen. Een monitor kan één sectie van een complexere Orchestration-functie of een subindeling [zijn.](durable-functions-sub-orchestrations.md)

## <a name="configuration"></a>Configuratie

### <a name="configuring-twilio-integration"></a>Twilio-integratie configureren

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>De functies

In dit artikel worden de volgende functies in de voor beeld-app uitgelegd:

* `E3_Monitor`: Een [Orchestrator-functie](durable-functions-bindings.md#orchestration-trigger) die `E3_TooManyOpenIssues` regel matig aanroept. Hiermee wordt `E3_SendAlert` de retour waarde van `E3_TooManyOpenIssues` is aangeroepen `True` .
* `E3_TooManyOpenIssues`: Een [activiteit functie](durable-functions-bindings.md#activity-trigger) waarmee wordt gecontroleerd of er te veel openstaande problemen zijn met een opslag plaats. Voor demonstratie doeleinden is het belang rijk dat er meer dan drie openstaande problemen zijn.
* `E3_SendAlert`: Een activiteit functie die een SMS-bericht verzendt via Twilio.

### <a name="e3_monitor-orchestrator-function"></a>E3_Monitor Orchestrator-functie


De functie **E3_Monitor** maakt gebruik van de standaard *function.js* voor Orchestrator-functies.

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/function.json)]

Hier volgt de code voor het implementeren van de functie:

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/\_\_init\_\_.py)]


Deze Orchestrator-functie voert de volgende acties uit:

1. Hiermee haalt u de *opslag plaats* op die moet worden bewaakt en het *telefoon nummer* waarnaar de SMS-melding moet worden verzonden.
2. Bepaalt de verloop tijd van de monitor. In het voor beeld wordt een in code vastgelegde waarde gebruikt voor de boog.
3. Roept **E3_TooManyOpenIssues** aan om te bepalen of er te veel openstaande problemen zijn op de aangevraagde opslag plaats.
4. Als er te veel problemen zijn, roept **E3_SendAlert** om een SMS-bericht naar het gewenste telefoon nummer te verzenden.
5. Hiermee maakt u een duurzame timer om de indeling te hervatten bij het volgende polling-interval. In het voor beeld wordt een in code vastgelegde waarde gebruikt voor de boog.
6. Blijft actief totdat de huidige UTC-tijd de verloop tijd van de monitor doorgeeft of een SMS-waarschuwing wordt verzonden.

Meerdere Orchestrator-exemplaren kunnen tegelijkertijd worden uitgevoerd door de Orchestrator-functie meerdere keren aan te roepen. De opslag plaats die moet worden bewaakt en het telefoon nummer waarnaar een SMS-bericht moet worden verzonden, kan worden opgegeven. Ten slotte moet u er rekening mee houden dat de Orchestrator-functie *niet* wordt uitgevoerd tijdens het wachten op de timer, zodat er geen kosten in rekening worden gebracht.


### <a name="e3_toomanyopenissues-activity-function"></a>E3_TooManyOpenIssues-activiteit functie

Net als bij andere voor beelden zijn de functies van de Help-activiteit reguliere functies die gebruikmaken van de `activityTrigger` trigger binding. De functie **E3_TooManyOpenIssues** haalt een lijst met momenteel openstaande problemen op de opslag plaats op en bepaalt of er te veel items zijn: meer dan 3 van ons voor beeld.


De *function.jsop* wordt als volgt gedefinieerd:

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

En dit is de implementatie.

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/\_\_init\_\_.py)]


### <a name="e3_sendalert-activity-function"></a>E3_SendAlert-activiteit functie

De functie **E3_SendAlert** maakt gebruik van de Twilio-binding voor het verzenden van een SMS-bericht met de mede deling dat er ten minste drie openstaande problemen met een oplossing in de eind gebruiker zijn.


De *function.js* is eenvoudig:

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

En dit is de code die het SMS-bericht verzendt:

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_SendAlert/\_\_init\_\_.py)]


## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

U hebt een [github](https://github.com/) -account nodig. Hiermee maakt u een tijdelijke open bare opslag plaats waarmee u problemen kunt openen.

Met de met HTTP geactiveerde functies die in het voor beeld zijn opgenomen, kunt u de indeling starten door de volgende HTTP POST-aanvraag te verzenden:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "repo": "<your github handle>/<a new github repo under your user>", "phone": "+1425XXXXXXX" }
```

Als uw GitHub-gebruikers naam bijvoorbeeld is `foo` en uw opslag plaats, `bar` moet uw waarde voor `"repo"` zijn `"foo/bar"` .

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

Het **E3_Monitor** exemplaar wordt gestart en vraagt de gegeven opslag plaats om openstaande problemen op te lossen. Als er ten minste 3 openstaande problemen worden gevonden, wordt een activiteit functie aangeroepen om een waarschuwing te verzenden. anders wordt een timer ingesteld. Wanneer de timer is verlopen, wordt de indeling hervat.

U kunt de activiteit van de Orchestration bekijken door te kijken naar de functie Logboeken in de Azure Functions Portal.

```
[2020-12-04T18:24:30.007Z] Executing 'Functions.HttpStart' (Reason='This function was programmatically 
called via the host APIs.', Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6)
[2020-12-04T18:24:30.769Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=058e656e-bcb1-418c-95b3-49afcd07bd08)
[2020-12-04T18:24:30.847Z] Started orchestration with ID = '788420bb31754c50acbbc46e12ef4f9c'.
[2020-12-04T18:24:30.932Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=058e656e-bcb1-418c-95b3-49afcd07bd08, Duration=174ms)
[2020-12-04T18:24:30.955Z] Executed 'Functions.HttpStart' (Succeeded, Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6, Duration=1028ms)
[2020-12-04T18:24:31.229Z] Executing 'Functions.E3_TooManyOpenIssues' (Reason='(null)', Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3)
[2020-12-04T18:24:31.772Z] Executed 'Functions.E3_TooManyOpenIssues' (Succeeded, Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3, Duration=555ms)
[2020-12-04T18:24:40.754Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=23915e4c-ddbf-46f9-b3f0-53289ed66082)
[2020-12-04T18:24:40.789Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=23915e4c-ddbf-46f9-b3f0-53289ed66082, Duration=38ms)
(...trimmed...)
```

De indeling wordt voltooid zodra de time-out is bereikt of er meer dan drie openstaande problemen zijn gedetecteerd. U kunt ook de `terminate` API in een andere functie gebruiken of de **TERMINATEPOSTURI** http post-webhook aanroepen waarnaar wordt verwezen in het 202-antwoord hierboven. Als u de webhook wilt gebruiken, vervangt u door de `{text}` reden voor de vroege beëindiging. De HTTP POST-URL ziet er ongeveer als volgt uit:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>Volgende stappen

In dit voor beeld wordt gedemonstreerd hoe u Durable Functions kunt gebruiken om de status van een externe bron te bewaken met behulp van [duurzame timers](durable-functions-timers.md) en voorwaardelijke logica. In het volgende voor beeld ziet u hoe u externe gebeurtenissen en [duurzame timers](durable-functions-timers.md) gebruikt om menselijke interactie te verwerken.

> [!div class="nextstepaction"]
> [Het voor beeld voor menselijke interactie uitvoeren](durable-functions-phone-verification.md)
