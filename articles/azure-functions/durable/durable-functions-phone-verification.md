---
title: Menselijke interactie en time-outs in Durable Functions-Azure
description: Meer informatie over het verwerken van menselijke interacties en time-outs in de Durable Functions-extensie voor Azure Functions.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: cba3cd0fd5d8727c4ffa4d1b42d7cd9250f21032
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028300"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Menselijke interactie in het voor beeld van een Durable Functions-telefoon verificatie

In dit voor beeld wordt gedemonstreerd hoe u een [Durable functions](durable-functions-overview.md) indeling bouwt waarbij menselijke interactie betrokken is. Wanneer een echte persoon betrokken is bij een geautomatiseerd proces, moet het proces meldingen kunnen verzenden naar de persoon en antwoorden asynchroon ontvangen. Het moet ook de mogelijkheid bieden dat de persoon niet beschikbaar is. (Dit laatste deel is waar time-outs belang rijk worden.)

Dit voor beeld implementeert een verificatie systeem op basis van een SMS-toestel. Deze soorten stromen worden vaak gebruikt bij het controleren van het telefoon nummer van een klant of voor multi-factor Authentication (MFA). Het is een krachtig voor beeld omdat de volledige implementatie wordt uitgevoerd met behulp van een paar kleine functies. Er is geen extern gegevens archief, zoals een Data Base, vereist.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Overzicht van scenario's

Verificatie via telefoon wordt gebruikt om te controleren of eind gebruikers van uw toepassing geen spammers zijn en dat ze zijn wie ze zeggen. Multi-factor Authentication is een veelvoorkomende use case voor het beveiligen van gebruikers accounts van hackers. De uitdaging bij het implementeren van uw eigen telefoon verificatie is dat er een **stateful interactie** is vereist met een menselijk. Een eind gebruiker krijgt meestal een code (bijvoorbeeld een nummer van vier cijfers) en moet **in een redelijke tijd** reageren.

Gewone Azure Functions zijn stateless (net als veel andere Cloud eindpunten op andere platforms), zodat deze typen interacties een expliciete status van extern beheer in een Data Base of een andere permanente Store moeten beheren. Daarnaast moet de interactie worden opgesplitst in meerdere functies die samen kunnen worden gecoördineerd. U hebt bijvoorbeeld ten minste één functie nodig om te beslissen over een code, ergens op te slaan en deze naar de telefoon van de gebruiker te verzenden. Daarnaast moet u ten minste één andere functie gebruiken om een reactie van de gebruiker te ontvangen en deze vervolgens weer te koppelen aan de oorspronkelijke functie aanroep om de code validatie uit te voeren. Een time-out is ook een belang rijk aspect om beveiliging te garanderen. Dit kan tamelijk complex zijn.

De complexiteit van dit scenario wordt sterk verkleind wanneer u Durable Functions gebruikt. Zoals u in dit voor beeld ziet, kan een Orchestrator-functie de stateful-interactie eenvoudig en zonder externe gegevens opslag beheren. Omdat Orchestrator-functies *duurzaam* zijn, zijn deze interactieve stromen ook zeer betrouwbaar.

## <a name="configuring-twilio-integration"></a>Twilio-integratie configureren

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>De functies

In dit artikel worden de volgende functies in de voor beeld-app behandeld:

* `E4_SmsPhoneVerification`: Een [Orchestrator-functie](durable-functions-bindings.md#orchestration-trigger) die het proces voor telefonische verificatie uitvoert, waaronder het beheren van time-outs en nieuwe pogingen.
* `E4_SendSmsChallenge`: Een [activiteit functie](durable-functions-bindings.md#activity-trigger) waarmee een code via een SMS-bericht wordt verzonden.

### <a name="e4_smsphoneverification-orchestrator-function"></a>E4_SmsPhoneVerification Orchestrator-functie

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=17-70)]

> [!NOTE]
> Het is mogelijk niet altijd duidelijk, maar deze Orchestrator schendt de [deterministische Orchestration-beperking](durable-functions-code-constraints.md)niet. Het is deterministisch omdat de `CurrentUtcDateTime` eigenschap wordt gebruikt om de verloop tijd van de timer te berekenen en retourneert dezelfde waarde bij elke herspeel op dit punt in de Orchestrator-code. Dit gedrag is belang rijk om ervoor te zorgen dat dezelfde `winner` resultaten van elke herhaalde aanroep naar `Task.WhenAny` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

De functie **E4_SmsPhoneVerification** maakt gebruik van de standaard *function.js* voor Orchestrator-functies.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/function.json)]

Hier volgt de code voor het implementeren van de functie:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

> [!NOTE]
> Het is mogelijk niet altijd duidelijk, maar deze Orchestrator schendt de [deterministische Orchestration-beperking](durable-functions-code-constraints.md)niet. Het is deterministisch omdat de `currentUtcDateTime` eigenschap wordt gebruikt om de verloop tijd van de timer te berekenen en retourneert dezelfde waarde bij elke herspeel op dit punt in de Orchestrator-code. Dit gedrag is belang rijk om ervoor te zorgen dat dezelfde `winner` resultaten van elke herhaalde aanroep naar `context.df.Task.any` .

# <a name="python"></a>[Python](#tab/python)

De functie **E4_SmsPhoneVerification** maakt gebruik van de standaard *function.js* voor Orchestrator-functies.

[!code-json[Main](~/samples-durable-functions-python/samples/human_interaction/E4_SmsPhoneVerification/function.json)]

Hier volgt de code voor het implementeren van de functie:

[!code-python[Main](~/samples-durable-functions-python/samples/human_interaction/E4_SmsPhoneVerification/\_\_init\_\_.py)]

> [!NOTE]
> Het is mogelijk niet altijd duidelijk, maar deze Orchestrator schendt de [deterministische Orchestration-beperking](durable-functions-code-constraints.md)niet. Het is deterministisch omdat de `currentUtcDateTime` eigenschap wordt gebruikt om de verloop tijd van de timer te berekenen en retourneert dezelfde waarde bij elke herspeel op dit punt in de Orchestrator-code. Dit gedrag is belang rijk om ervoor te zorgen dat dezelfde `winner` resultaten van elke herhaalde aanroep naar `context.df.Task.any` .

---

Zodra deze functie is gestart, doet deze Orchestrator het volgende:

1. Hiermee wordt een telefoon nummer opgehaald waarnaar de SMS-melding wordt *verzonden* .
2. Roept **E4_SendSmsChallenge** op om een SMS-bericht naar de gebruiker te verzenden en retourneert de verwachte vraag code van 4 cijfers terug.
3. Maakt een duurzame timer die 90 seconden vanaf de huidige tijd activeert.
4. Parallel met de timer, wacht op een **SmsChallengeResponse** -gebeurtenis van de gebruiker.

De gebruiker ontvangt een SMS-bericht met een code van vier cijfers. Ze hebben 90 seconden nodig om deze code van vier cijfers terug te sturen naar het Orchestrator-functie exemplaar om het verificatie proces te volt ooien. Als ze de verkeerde code verzenden, krijgen ze een extra drie pogingen om het recht te krijgen (binnen hetzelfde 90-Second-venster).

> [!WARNING]
> Het is belang rijk [timers te annuleren](durable-functions-timers.md) als u deze niet langer nodig hebt om te verlopen, zoals in het bovenstaande voor beeld wanneer een antwoord op een Challenge wordt geaccepteerd.

## <a name="e4_sendsmschallenge-activity-function"></a>E4_SendSmsChallenge-activiteit functie

De functie **E4_SendSmsChallenge** maakt gebruik van de Twilio-binding voor het verzenden van het SMS-bericht met de code van vier cijfers naar de eind gebruiker.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=72-89)]

> [!NOTE]
> U moet het `Microsoft.Azure.WebJobs.Extensions.Twilio` Nuget-pakket installeren om de voorbeeld code uit te voeren.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

De *function.jsop* wordt als volgt gedefinieerd:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/function.json)]

En dit is de code waarmee de code van vier cijfers wordt gegenereerd en het SMS-bericht wordt verzonden:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

# <a name="python"></a>[Python](#tab/python)

De *function.jsop* wordt als volgt gedefinieerd:

[!code-json[Main](~/samples-durable-functions-python/samples/human_interaction/SendSMSChallenge/function.json)]

En dit is de code waarmee de code van vier cijfers wordt gegenereerd en het SMS-bericht wordt verzonden:

[!code-python[Main](~/samples-durable-functions-python/samples/human_interaction/SendSMSChallenge/\_\_init\_\_.py)]

---

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Met de met HTTP geactiveerde functies die in het voor beeld zijn opgenomen, kunt u de indeling starten door de volgende HTTP POST-aanvraag te verzenden:

```
POST http://{host}/orchestrators/E4_SmsPhoneVerification
Content-Length: 14
Content-Type: application/json

"+1425XXXXXXX"
```

```
HTTP/1.1 202 Accepted
Content-Length: 695
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

De Orchestrator-functie ontvangt het opgegeven telefoon nummer en verzendt dit onmiddellijk een SMS-bericht met een wille keurig gegenereerde verificatie code van 4 cijfers bijvoorbeeld &mdash; *2168*. De functie wacht vervolgens 90 seconden op een reactie.

Als u wilt antwoorden met de code, kunt u [ `RaiseEventAsync` (.net) of `raiseEvent` (Java script)](durable-functions-instance-management.md) in een andere functie gebruiken of de **sendEventUrl** http post-webhook aanroepen waarnaar wordt verwezen in het 202-antwoord hierboven, waarbij u vervangt `{eventName}` door de naam van de gebeurtenis `SmsChallengeResponse` :

```
POST http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Als u dit verzendt voordat de timer verloopt, wordt de indeling voltooid en `output` wordt het veld ingesteld op `true` , wat aangeeft dat de verificatie is geslaagd.

```
GET http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Als u de timer laat verlopen, of als u de verkeerde code vier keer opgeeft, kunt u een query voor de status uitvoeren en een `false` functie-uitvoer van Orchestration gebruiken om aan te geven dat de telefoon verificatie is mislukt.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="next-steps"></a>Volgende stappen

In dit voor beeld hebben we enkele van de geavanceerde mogelijkheden van Durable Functions, met name en api's, gedemonstreerd `WaitForExternalEvent` `CreateTimer` . U hebt gezien hoe deze kunnen worden gecombineerd met `Task.WaitAny` om een betrouw bare time-outsysteem te implementeren, wat vaak nuttig is voor interactie met echte mensen. U vindt meer informatie over het gebruik van Durable Functions door een reeks artikelen te lezen die een gedetailleerde dekking van specifieke onderwerpen bieden.

> [!div class="nextstepaction"]
> [Ga naar het eerste artikel in de reeks](durable-functions-bindings.md)
