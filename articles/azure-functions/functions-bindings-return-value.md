---
title: Retour waarde van een Azure-functie gebruiken
description: Meer informatie over het beheren van retour waarden voor Azure Functions
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 58ee9b682bc97dc4044d811392cf4ff5b51a69fd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94491355"
---
# <a name="using-the-azure-function-return-value"></a>De functie retour waarde van Azure gebruiken

In dit artikel wordt uitgelegd hoe resultaat waarden binnen een functie werken.

In talen die een retour waarde hebben, kunt u een functie- [uitvoer binding](./functions-triggers-bindings.md#binding-direction) binden aan de retour waarde:

* Pas in een C#-klassebibliotheek het kenmerk uitvoer binding toe op de retour waarde van de methode.
* Pas in Java de annotatie van de uitvoer binding toe op de functie methode.
* In andere talen stelt u de `name` eigenschap in *function.jsin op* aan `$return` .

Als er meerdere uitvoer bindingen zijn, gebruikt u de retour waarde voor slechts één van beide.

In C#-en C#-script, alternatieve manieren om gegevens te verzenden naar een uitvoer binding zijn `out` para meters en [Collector-objecten](functions-reference-csharp.md#writing-multiple-output-values).

# <a name="c"></a>[C#](#tab/csharp)

Dit is de C#-code die gebruikmaakt van de retour waarde voor een uitvoer binding, gevolgd door een async-voor beeld:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Dit is de uitvoer binding in de *function.jsvoor* het volgende bestand:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Hier volgt de C#-script code, gevolgd door een async-voor beeld:

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="f"></a>[F#](#tab/fsharp)

Dit is de uitvoer binding in de *function.jsvoor* het volgende bestand:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Dit is de F #-code:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Dit is de uitvoer binding in de *function.jsvoor* het volgende bestand:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

In Java script komt de geretourneerde waarde voor in de tweede para meter voor `context.done` :

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```
# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Dit is de uitvoer binding in de *function.jsvoor* het volgende bestand:

```json
{
    "name": "Response",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{blobname}"
}
```

Hier volgt de Power shell-code die gebruikmaakt van de retour waarde voor een HTTP-uitvoer binding:

```powershell
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body = $blobname
    })
```

# <a name="python"></a>[Python](#tab/python)

Dit is de uitvoer binding in de *function.jsvoor* het volgende bestand:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Dit is de Python-code:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

# <a name="java"></a>[Java](#tab/java)

Dit is een Java-code die gebruikmaakt van de retour waarde voor een uitvoer binding:

```java
@FunctionName("QueueTrigger")
@StorageAccount("AzureWebJobsStorage")
@BlobOutput(name = "output", path = "output-container/{id}")
public static String run(
  @QueueTrigger(name = "input", queueName = "inputqueue") WorkItem input,
  final ExecutionContext context
) {
  String json = String.format("{ \"id\": \"%s\" }", input.id);
  context.getLogger().info("Java processed queue message. Item=" + json);
  return json;
}
```

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure Functions bindings fouten verwerken](./functions-bindings-errors.md)
