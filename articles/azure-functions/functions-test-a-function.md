---
title: Azure Functions testen
description: Automatische tests voor een C#-functie in Visual Studio en Java script-functie in VS code maken
author: craigshoemaker
ms.topic: conceptual
ms.custom: devx-track-csharp, devx-track-js
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: f75f42f3879f551a945bdeb2d88450ae3b9d6106
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98674147"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategieën voor het testen van uw code in Azure Functions

In dit artikel wordt beschreven hoe u automatische tests voor Azure Functions maakt.

Testen van alle code wordt aanbevolen, maar u kunt wel de beste resultaten krijgen door de logica van een functie op te geven en tests buiten de functie te maken. Met abstractisatie logica worden de regels van code van een functie beperkt en is het mogelijk dat de functie alleen verantwoordelijk is voor het aanroepen van andere klassen of modules. In dit artikel wordt echter gedemonstreerd hoe u automatische tests kunt maken op basis van een HTTP-en door een timer geactiveerde functie.

De volgende inhoud is onderverdeeld in twee verschillende secties die bedoeld zijn om verschillende talen en omgevingen te bereiken. Meer informatie over het bouwen van tests vindt u in:

- [C# in Visual Studio met xUnit](#c-in-visual-studio)
- [Java script in VS code met jest](#javascript-in-vs-code)

De voor beeld-opslag plaats is beschikbaar op [github](https://github.com/Azure-Samples/azure-functions-tests).

## <a name="c-in-visual-studio"></a>C# in Visual Studio

In het volgende voor beeld wordt beschreven hoe u een C#-functie-app maakt in Visual Studio en voert en test met [xUnit](https://github.com/xunit/xunit).

![Azure Functions testen met C# in Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Instellen

Als u uw omgeving wilt instellen, maakt u een functie-en test-app. De volgende stappen helpen u bij het maken van de apps en functies die nodig zijn voor de ondersteuning van de tests:

1. [Een nieuwe functions-app maken](./functions-get-started.md) en **hieraan** een naam toe te voegen
2. [Maak een HTTP-functie op basis van de sjabloon](./functions-get-started.md) en geef deze de naam **MyHttpTrigger**.
3. [Maak een timer functie op basis van de sjabloon](./functions-create-scheduled-function.md) en noem deze **MyTimerTrigger**.
4. [Maak een xUnit-test-app](https://xunit.net/docs/getting-started/netcore/cmdline) in de oplossing en geef deze de naam **Function. tests**.
5. Gebruik NuGet om een verwijzing toe te voegen vanuit de test-app naar [micro soft. AspNetCore. MVC](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. [Raadpleeg de *functions* -app](/visualstudio/ide/managing-references-in-a-project?view=vs-2017) vanuit *functions. app tests* .

### <a name="create-test-classes"></a>Test klassen maken

Nu de projecten zijn gemaakt, kunt u de klassen maken die worden gebruikt om de geautomatiseerde tests uit te voeren.

Voor elke functie wordt een instantie van [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) gebruikt voor het afhandelen van bericht logboek registratie. Sommige tests registreren geen berichten of hebben geen betrekking op hoe logboek registratie wordt geïmplementeerd. Andere tests moeten de geregistreerde berichten evalueren om te bepalen of een test wordt door gegeven.

U maakt een nieuwe klasse met de naam `ListLogger` die een interne lijst met berichten bevat die tijdens het testen moet worden geëvalueerd. Voor het implementeren van de vereiste `ILogger` interface moet de klasse een bereik hebben. De volgende klasse geeft een bereik aan voor de test cases die moeten worden door gegeven aan de `ListLogger` klasse.

Maak een nieuwe klasse in *functions. test* project met de naam **NullScope. cs** en voer de volgende code in:

```csharp
using System;

namespace Functions.Tests
{
    public class NullScope : IDisposable
    {
        public static NullScope Instance { get; } = new NullScope();

        private NullScope() { }

        public void Dispose() { }
    }
}
```

Maak vervolgens een nieuwe klasse in *functions. test* project met de naam **ListLogger. cs** en voer de volgende code in:

```csharp
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.Text;

namespace Functions.Tests
{
    public class ListLogger : ILogger
    {
        public IList<string> Logs;

        public IDisposable BeginScope<TState>(TState state) => NullScope.Instance;

        public bool IsEnabled(LogLevel logLevel) => false;

        public ListLogger()
        {
            this.Logs = new List<string>();
        }

        public void Log<TState>(LogLevel logLevel,
                                EventId eventId,
                                TState state,
                                Exception exception,
                                Func<TState, Exception, string> formatter)
        {
            string message = formatter(state, exception);
            this.Logs.Add(message);
        }
    }
}
```

De `ListLogger` klasse implementeert de volgende leden als een contract van de `ILogger` Interface:

- **BeginScope**: bereiken voegen context toe aan uw logboek registratie. In dit geval wijst de test alleen naar het statische exemplaar op de `NullScope` klasse om de test te laten functioneren.

- **IsEnabled**: er wordt een standaard waarde van `false` gegeven.

- **Log**: deze methode gebruikt de geleverde `formatter` functie om het bericht op te maken en vervolgens de resulterende tekst aan de verzameling toe te voegen `Logs` .

De `Logs` verzameling is een exemplaar van `List<string>` en wordt geïnitialiseerd in de constructor.

Maak vervolgens een nieuw bestand in *functions. test* project met de naam **LoggerTypes. cs** en voer de volgende code in:

```csharp
namespace Functions.Tests
{
    public enum LoggerTypes
    {
        Null,
        List
    }
}
```

Met deze opsomming geeft u het type logboek registratie op dat wordt gebruikt door de tests.

Maak nu een nieuwe klasse in *functions. test* project met de naam **TestFactory. cs** en voer de volgende code in:

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Http.Internal;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.Abstractions;
using Microsoft.Extensions.Primitives;
using System.Collections.Generic;

namespace Functions.Tests
{
    public class TestFactory
    {
        public static IEnumerable<object[]> Data()
        {
            return new List<object[]>
            {
                new object[] { "name", "Bill" },
                new object[] { "name", "Paul" },
                new object[] { "name", "Steve" }

            };
        }

        private static Dictionary<string, StringValues> CreateDictionary(string key, string value)
        {
            var qs = new Dictionary<string, StringValues>
            {
                { key, value }
            };
            return qs;
        }

        public static HttpRequest CreateHttpRequest(string queryStringKey, string queryStringValue)
        {
            var context = new DefaultHttpContext();
            var request = context.Request;
            request.Query = new QueryCollection(CreateDictionary(queryStringKey, queryStringValue));
            return request;
        }

        public static ILogger CreateLogger(LoggerTypes type = LoggerTypes.Null)
        {
            ILogger logger;

            if (type == LoggerTypes.List)
            {
                logger = new ListLogger();
            }
            else
            {
                logger = NullLoggerFactory.Instance.CreateLogger("Null Logger");
            }

            return logger;
        }
    }
}
```

De `TestFactory` klasse implementeert de volgende leden:

- **Gegevens**: deze eigenschap retourneert een [IEnumerable](/dotnet/api/system.collections.ienumerable) -verzameling van voorbeeld gegevens. De sleutel-waardeparen vertegenwoordigen waarden die worden door gegeven aan een query reeks.

- **CreateDictionary**: deze methode accepteert een sleutel/waarde-paar als argumenten en retourneert een nieuwe `Dictionary` die wordt gebruikt om `QueryCollection` query teken reeks waarden weer te geven.

- **CreateHttpRequest**: met deze methode maakt u een HTTP-aanvraag die is geïnitialiseerd met de opgegeven query reeks parameters.

- **CreateLogger**: op basis van het logboek type retourneert deze methode een logger klasse die wordt gebruikt voor het testen. Hiermee worden `ListLogger` vastgelegde berichten bijgehouden die beschikbaar zijn voor evaluatie in tests.

Maak ten slotte een nieuwe klasse in *functions. test* project met de naam **FunctionsTests. cs** en voer de volgende code in:

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Xunit;

namespace Functions.Tests
{
    public class FunctionsTests
    {
        private readonly ILogger logger = TestFactory.CreateLogger();

        [Fact]
        public async void Http_trigger_should_return_known_string()
        {
            var request = TestFactory.CreateHttpRequest("name", "Bill");
            var response = (OkObjectResult)await MyHttpTrigger.Run(request, logger);
            Assert.Equal("Hello, Bill. This HTTP triggered function executed successfully.", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await MyHttpTrigger.Run(request, logger);
            Assert.Equal($"Hello, {queryStringValue}. This HTTP triggered function executed successfully.", response.Value);
        }

        [Fact]
        public void Timer_should_log_message()
        {
            var logger = (ListLogger)TestFactory.CreateLogger(LoggerTypes.List);
            MyTimerTrigger.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```

De leden die in deze klasse worden geïmplementeerd zijn:

- **Http_trigger_should_return_known_string**: met deze test wordt een aanvraag met de query teken reeks waarden van `name=Bill` naar een http-functie gemaakt en wordt gecontroleerd of de verwachte reactie wordt geretourneerd.

- **Http_trigger_should_return_string_from_member_data**: deze test maakt gebruik van xUnit-kenmerken om voorbeeld gegevens aan de http-functie toe te voegen.

- **Timer_should_log_message**: met deze test wordt een exemplaar van gemaakt `ListLogger` en door gegeven aan een timer-functie. Zodra de functie is uitgevoerd, wordt het logboek gecontroleerd om te controleren of het verwachte bericht aanwezig is.

Als u toegang wilt krijgen tot toepassings instellingen in uw tests, kunt [](./functions-dotnet-dependency-injection.md) u een `IConfiguration` instantie met gemodelde omgevings variabele waarden in uw functie injecteren.

### <a name="run-tests"></a>Tests uitvoeren

Als u de tests wilt uitvoeren, gaat u naar de **test Verkenner** en klikt u op **alles uitvoeren**.

![Azure Functions testen met C# in Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Debug-tests

Als u fouten wilt opsporen in de tests, stelt u een onderbrekings punt in op een test, gaat u naar de **test Verkenner** en klikt u op **uitvoeren > fout opsporing laatst uitgevoerd**.

## <a name="javascript-in-vs-code"></a>Java script in VS code

In het volgende voor beeld wordt beschreven hoe u een Java script-functie-app maakt in VS code en voert en test met [jest](https://jestjs.io). Deze procedure maakt gebruik van de [extensie VS code functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) om Azure functions te maken.

![Azure Functions testen met Java script in VS code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Instellen

Als u uw omgeving wilt instellen, initialiseert u een nieuwe Node.js-app in een lege map door uit te voeren `npm init` .

```bash
npm init -y
```

Installeer vervolgens jest door de volgende opdracht uit te voeren:

```bash
npm i jest
```

Werk _package.js_ nu bij om de bestaande test opdracht te vervangen door de volgende opdracht:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Test modules maken

Wanneer het project is geïnitialiseerd, kunt u de modules maken die worden gebruikt om de geautomatiseerde tests uit te voeren. Maak eerst een nieuwe map met de naam *tests* om de ondersteunings modules op te slaan.

Voeg in de map *testen* een nieuw bestand toe, geef het de naam **defaultContext.js** en voeg de volgende code toe:

```javascript
module.exports = {
    log: jest.fn()
};
```

In deze module wordt de *logboek* functie voor de standaard uitvoerings context weer gegeven.

Voeg vervolgens een nieuw bestand toe, geef het de naam **defaultTimer.js** en voeg de volgende code toe:

```javascript
module.exports = {
    IsPastDue: false
};
```

Deze module implementeert de `IsPastDue` eigenschap op standaard als een valse Timer-instantie. Timer configuraties zoals NCRONTAB-expressies zijn hier niet vereist omdat de test harnas de functie rechtstreeks aanroept om het resultaat te testen.

Gebruik vervolgens de extensie VS code functions om [een nieuwe Java script HTTP-functie te maken](/azure/developer/javascript/tutorial-vscode-serverless-node-01) en deze *http trigger* te noemen. Nadat de functie is gemaakt, voegt u een nieuw bestand toe aan dezelfde map met de naam **index.test.js** en voegt u de volgende code toe:

```javascript
const httpFunction = require('./index');
const context = require('../testing/defaultContext')

test('Http trigger should return known text', async () => {

    const request = {
        query: { name: 'Bill' }
    };

    await httpFunction(context, request);

    expect(context.log.mock.calls.length).toBe(1);
    expect(context.res.body).toEqual('Hello Bill');
});
```

De HTTP-functie van de sjabloon retourneert een teken reeks ' Hello ' die is samengevoegd met de naam die is opgenomen in de query reeks. Met deze test wordt een vervalst exemplaar van een aanvraag gemaakt en door gegeven aan de HTTP-functie. De test controleert of de *logboek* methode eenmaal wordt aangeroepen en de geretourneerde tekst is gelijk aan "Hello Bill".

Gebruik vervolgens de extensie VS code functions om een nieuwe Java Script-timer functie te maken en geef deze de naam *Timer trigger*. Nadat de functie is gemaakt, voegt u een nieuw bestand toe aan dezelfde map met de naam **index.test.js** en voegt u de volgende code toe:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```

De timer functie van de sjabloon registreert een bericht aan het einde van de hoofd tekst van de functie. Deze test zorgt ervoor dat de *logboek* functie eenmaal wordt genoemd.

### <a name="run-tests"></a>Tests uitvoeren

Als u de tests wilt uitvoeren, drukt u op **CTRL + ~** om het opdracht venster te openen en voert u het `npm test` volgende uit:

```bash
npm test
```

![Azure Functions testen met Java script in VS code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Debug-tests

Als u fouten wilt opsporen in uw tests, voegt u de volgende configuratie toe aan uw *launch.jsin* het bestand:

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "disableOptimisticBPs": true,
  "program": "${workspaceRoot}/node_modules/jest/bin/jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

Stel vervolgens een onderbrekings punt in uw test in en druk op **F5**.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u automatische tests voor uw functies schrijft, kunt u door gaan met deze resources:

- [Handmatig een niet door HTTP geactiveerde functie uitvoeren](./functions-manually-run-non-http.md)
- [Fout afhandeling Azure Functions](./functions-bindings-error-pages.md)
- [Lokaal opsporen van fouten in Azure-functies voor Event Grid Trigger](./functions-debug-event-grid-trigger-local.md)