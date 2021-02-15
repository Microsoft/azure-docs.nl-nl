---
title: Gegevens persistentie en-serialisatie in Durable Functions-Azure
description: Meer informatie over de uitbrei ding van de Durable Functions voor Azure Functions gegevens persistent maken
author: ConnorMcMahon
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: azfuncdf
ms.openlocfilehash: 34845afe0efd0131e0c568eac1169ddc10be5d47
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100521062"
---
# <a name="data-persistence-and-serialization-in-durable-functions-azure-functions"></a>Gegevens persistentie en-serialisatie in Durable Functions (Azure Functions)

Met Durable Functions worden functie parameters, retour waarden en andere status automatisch persistent gemaakt aan een duurzame back-end om betrouw bare uitvoering te kunnen bieden. De hoeveelheid gegevens en de frequentie waarmee duurzame opslag wordt bewaard, kan echter invloed hebben op de prestaties van de toepassing en de kosten voor opslag transacties. Afhankelijk van het type gegevens dat door uw toepassing wordt opgeslagen, kan het nodig zijn om het bewaren van gegevens en het privacybeleid te overwegen.

## <a name="azure-storage"></a>Azure Storage

Durable Functions worden standaard gegevens opgeslagen in wacht rijen, tabellen en blobs in een [Azure Storage](https://azure.microsoft.com/services/storage/) account dat u opgeeft.

### <a name="queues"></a>Wachtrijen

Durable Functions gebruikt Azure Storage wacht rijen voor het betrouwbaar plannen van alle functies. Deze wachtrij berichten bevatten functie-invoer of uitvoer, afhankelijk van het feit of het bericht wordt gebruikt om een uitvoering te plannen of een waarde terug te retour neren naar een aanroepende functie. Deze wachtrij berichten bevatten ook aanvullende meta gegevens die Durable Functions gebruikt voor interne doel einden, zoals route ring en end-to-end correlatie. Nadat een functie is uitgevoerd als reactie op een ontvangen bericht, wordt dat bericht verwijderd en kan het resultaat van de uitvoering ook blijven bestaan in Azure Storage tabellen of Azure Storage blobs.

In één [Task hub](durable-functions-task-hubs.md)worden met Durable functions berichten gemaakt en toegevoegd aan een *werk* wachtrij met de naam `<taskhub>-workitem` voor het plannen van activiteit functies en een of meer *controle wachtrijen* met de naam `<taskhub>-control-##` om Orchestrator en entiteits functies te plannen of hervatten. Het aantal controle wachtrijen is gelijk aan het aantal partities dat voor uw toepassing is geconfigureerd. Zie de [documentatie over prestaties en schaal baarheid](durable-functions-perf-and-scale.md)voor meer informatie over wacht rijen en partities.

### <a name="tables"></a>Tabellen

Zodra de gegevens zijn verwerkt, worden de resulterende acties bewaard in de *geschiedenis* tabel met de naam `<taskhub>History` . Orchestration-invoer, uitvoer en aangepaste status gegevens worden ook opgeslagen in de tabel *instances* met de naam `<taskhub>Instances` .

### <a name="blobs"></a>Blobs

In de meeste gevallen gebruikt Durable Functions Azure Storage blobs niet om gegevens te behouden. Wacht rijen en tabellen hebben echter [grootte limieten](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-queue-storage-limits) die kunnen voor komen dat Durable functions alle vereiste gegevens in een rij-of wachtrij bericht van de opslag blijven. Wanneer bijvoorbeeld een stukje gegevens dat persistent moet worden gemaakt voor een wachtrij groter is dan 45 KB, worden de gegevens door Durable Functions gecomprimeerd en in plaats daarvan opgeslagen in een blob. Bij het persistent maken van gegevens naar Blob-opslag op deze manier slaat een duurzame functie een verwijzing op naar die Blob in de tabelrij of wachtrij bericht. Als Durable Functions de gegevens moet ophalen, wordt deze automatisch opgehaald uit de blob. Deze blobs worden opgeslagen in de BLOB-container `<taskhub>-largemessages` .

> [!NOTE]
> De stappen voor de extra compressie en BLOB-bewerkingen voor grote berichten kunnen kostbaar zijn voor de kosten van CPU en I/O-latentie. Daarnaast moeten Durable Functions persistente gegevens in het geheugen laden en kan dit dus voor veel verschillende functies tegelijk worden uitgevoerd. Als gevolg hiervan kan het persistent maken van grote gegevens ladingen ook leiden tot hoog geheugen gebruik. Als u de overhead van het geheugen wilt minimaliseren, kunt u overwegen om grote hoeveel heden hand matig te blijven gebruiken (bijvoorbeeld in Blob Storage). in plaats daarvan worden verwijzingen naar deze gegevens omzeild. Op deze manier kan uw code de gegevens alleen laden wanneer deze nodig zijn om redundante belasting te voor komen tijdens het [herhalen van Orchestrator-functies](durable-functions-orchestrations.md#reliability). Het opslaan van payloads op schijf wordt echter *niet* aanbevolen, omdat de status van de schijf niet gegarandeerd beschikbaar is omdat functies op verschillende vm's gedurende hun levens duur kunnen worden uitgevoerd.

### <a name="types-of-data-that-is-serialized-and-persisted"></a>Typen gegevens die worden geserialiseerd en bewaard
Hier volgt een lijst met de verschillende typen gegevens die worden geserialiseerd en blijven behouden wanneer u functies van Durable Functions gebruikt:

- Alle invoer en uitvoer van orchestrator, activiteiten en entiteits functies, inclusief alle Id's en niet-verwerkte uitzonde ringen
- Functie namen van orchestrator, activiteit en entiteit
- Externe gebeurtenis namen en nettoladingen
- Nettoladingen van de aangepaste indelings status
- Insluitings berichten van Orchestrator
- Duurzame nettoladingen voor timer
- Duurzame HTTP-aanvraag-en antwoord-Url's, kopteksten en nettoladingen
- Nettoladingen van entiteits-en signaal toestanden
- Nettoladingen van entiteits status

### <a name="working-with-sensitive-data"></a>Werken met gevoelige gegevens
Wanneer u Azure Storage gebruikt, worden alle gegevens automatisch versleuteld op rest. Iedereen met toegang tot het opslag account kan echter de gegevens in de niet-versleutelde vorm lezen. Als u betere beveiliging voor gevoelige gegevens nodig hebt, kunt u eerst de gegevens versleutelen met behulp van uw eigen coderings sleutels zodat Durable Functions de gegevens in een vooraf versleutelde vorm persistent maakt.

Daarnaast hebben .NET-gebruikers de mogelijkheid om aangepaste serialisatie providers te implementeren die automatische versleuteling bieden. Een voor beeld van aangepaste serialisatie met versleuteling vindt u in [Dit github](https://github.com/charleszipp/azure-durable-entities-encryption)-voor beeld.

> [!NOTE]
> Als u besluit versleuteling op toepassings niveau te implementeren, moet u er rekening mee houden dat Orchestrations en entiteiten voor onbepaalde tijd kunnen bestaan. Dit is belang rijk wanneer het tijd is voor het draaien van de versleutelings sleutels omdat een indeling of entiteiten langer kunnen worden uitgevoerd dan het beleid voor sleutel rotatie. Als er een belang rijke draaiing plaatsvindt, is de sleutel die wordt gebruikt om uw gegevens te versleutelen mogelijk niet langer beschikbaar om deze te ontsleutelen wanneer uw indeling of entiteit de volgende keer wordt uitgevoerd. Versleuteling van klanten wordt daarom alleen aanbevolen wanneer wordt verwacht dat er relatief korte Peri Oden worden uitgevoerd.

## <a name="customizing-serialization-and-deserialization"></a>Serialisatie en deserialisatie aanpassen

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default-serialization-logic"></a>Standaard logica voor serialisatie

Durable Functions intern gebruikt [JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm) om indelings-en entiteits gegevens te serialiseren naar JSON. De standaard instellingen Durable Functions gebruikt voor Json.NET zijn:

**Invoer, uitvoer en status:**

```csharp
JsonSerializerSettings
{
    TypeNameHandling = TypeNameHandling.None,
    DateParseHandling = DateParseHandling.None,
}
```

**Uitzonderingen**

```csharp
JsonSerializerSettings
{
    ContractResolver = new ExceptionResolver(),
    TypeNameHandling = TypeNameHandling.Objects,
    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
}
```

Lees hier meer gedetailleerde documentatie `JsonSerializerSettings` [](https://www.newtonsoft.com/json/help/html/SerializationSettings.htm).

## <a name="customizing-serialization-with-net-attributes"></a>Serialisatie aanpassen met .NET-kenmerken

Bij het serialiseren van gegevens zoekt Json.NET naar [verschillende kenmerken](https://www.newtonsoft.com/json/help/html/SerializationAttributes.htm) voor klassen en eigenschappen die bepalen hoe de gegevens worden geserialiseerd en GEDESERIALISEERD van JSON. Als u eigenaar bent van de bron code voor het gegevens type dat is door gegeven aan Durable Functions Api's, kunt u deze kenmerken toevoegen aan het type om serialisatie en deserialisatie aan te passen.

## <a name="customizing-serialization-with-dependency-injection"></a>Serialisatie aanpassen met afhankelijkheids injectie

Functie-apps die .NET als doel hebben en die worden uitgevoerd op de functions v3 runtime, kunnen gebruikmaken van [afhankelijkheids injectie (di)](../functions-dotnet-dependency-injection.md) om de manier waarop gegevens en uitzonde ringen worden geserialiseerd, aan te passen. De voorbeeld code hieronder laat zien hoe u DI gebruikt voor het overschrijven van de standaard instellingen voor Json.NET-serialisatie met aangepaste implementaties van de `IMessageSerializerSettingsFactory` and- `IErrorSerializerSettingsFactory` Service interfaces.

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Azure.WebJobs.Extensions.DurableTask;
using Microsoft.Extensions.DependencyInjection;
using Newtonsoft.Json;
using System.Collections.Generic;

[assembly: FunctionsStartup(typeof(MyApplication.Startup))]
namespace MyApplication
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddSingleton<IMessageSerializerSettingsFactory, CustomMessageSerializerSettingFactory>();
            builder.Services.AddSingleton<IErrorSerializerSettingsFactory, CustomErrorSerializerSettingsFactory>();
        }

        /// <summary>
        /// A factory that provides the serialization for all inputs and outputs for activities and
        /// orchestrations, as well as entity state.
        /// </summary>
        internal class CustomMessageSerializerSettingsFactory : IMessageSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }

        /// <summary>
        /// A factory that provides the serialization for all exceptions thrown by activities
        /// and orchestrations
        /// </summary>
        internal class CustomErrorSerializerSettingsFactory : IErrorSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="serialization-and-deserialization-logic"></a>Logica voor serialisatie en deserialisatie

Azure Functions knooppunt toepassingen worden gebruikt [ `JSON.stringify()` voor serialisatie](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) en [ `JSON.Parse()` voor deserialisatie](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse). De meeste typen moeten naadloos worden geserialiseerd en gedeserialiseerd. In gevallen waarin de standaard logica onvoldoende is, `toJSON()` zal het definiëren van een methode op het object de serialisatie-logica overnemen. Er bestaat echter geen enkel analoog voor het deserialiseren van objecten.

Voor een volledige aanpassing van de pijp lijn voor serialisatie/deserialisatie kunt u overwegen om de serialisatie en deserialisatie te verwerken met uw eigen code en om gegevens als teken reeksen door te geven.


# <a name="python"></a>[Python](#tab/python)

### <a name="serialization-and-deserialization-logic"></a>Logica voor serialisatie en deserialisatie

Het wordt sterk aanbevolen om type aantekeningen te gebruiken om ervoor te zorgen dat uw gegevens op de juiste wijze worden geserializd door Durable Functions. Hoewel veel ingebouwde typen automatisch worden verwerkt, moeten bij sommige ingebouwde gegevens typen annotaties van het type tijdens deserialisatie behouden blijven.

Voor aangepaste gegevens typen moet u de JSON-serialisatie en deserialisatie van een gegevens type definiëren door een statische `to_json` en een `from_json` methode vanuit uw klasse te exporteren.

---
