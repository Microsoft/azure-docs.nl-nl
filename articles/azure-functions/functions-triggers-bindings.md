---
title: Triggers en bindingen in Azure Functions
description: Leer hoe u triggers en bindingen kunt gebruiken om uw Azure-functie te verbinden met onlinegebeurtenissen en cloudservices.
author: craigshoemaker
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 8648a52c58929983070b9a89c8fe946b89d37385
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739400"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions triggers en bindingsconcepten

In dit artikel leert u de algemene concepten rondom functietriggers en -bindingen.

Triggers zorgen ervoor dat een functie wordt uitgevoerd. Een trigger definieert hoe een functie wordt aangeroepen en een functie precies één trigger moet hebben. Aan triggers zijn gegevens gekoppeld. Meestal is dit de nettolading waarmee de functie is geactiveerd. 

Binding met een functie is een manier om een andere resource declaratief te verbinden met de functie; bindingen kunnen worden verbonden als *invoerbindingen,* *uitvoerbindingen* of beide. Gegevens van bindingen worden als parameters doorgegeven aan de functie.

U kunt verschillende bindingen mixen en matchen om aan uw behoeften te voldoen. Bindingen zijn optioneel en een functie kan een of meerdere invoer- en/of uitvoerbindingen hebben.

Met triggers en bindingen kunt u hardcoding van toegang tot andere services vermijden. Uw functie ontvangt gegevens (bijvoorbeeld de inhoud van een wachtrijbericht) in functieparameters. U verzendt gegevens (bijvoorbeeld om een ​​wachtrijbericht te maken) door de retourwaarde van de functie te gebruiken. 

Kijk eens naar de volgende voorbeelden van hoe u verschillende functies kunt implementeren.

| Voorbeeldscenario | Trigger | Invoerbinding | Uitvoerbinding |
|-------------|---------|---------------|----------------|
| Er wordt een nieuw wachtrijbericht ontvangen waarin een functie wordt uitgevoerd om naar een andere wachtrij te schrijven. | Wachtrij<sup>*</sup> | *Geen* | Wachtrij<sup>*</sup> |
|Een geplande taak leest Blob Storage inhoud en maakt een nieuw Cosmos DB document. | Timer | Blob Storage | Cosmos DB |
|De Event Grid wordt gebruikt om een afbeelding te lezen uit Blob Storage en een document van Cosmos DB om een e-mail te verzenden. | Event Grid | Blob Storage en Cosmos DB | SendGrid |
| Een webhook die gebruikmaakt van Microsoft Graph om een Excel-werkblad bij te werken. | HTTP | *Geen* | Microsoft Graph |

<sup>\*</sup> Vertegenwoordigt verschillende wachtrijen

Deze voorbeelden zijn niet volledig bedoeld, maar zijn bedoeld om te illustreren hoe u triggers en bindingen samen kunt gebruiken.

###  <a name="trigger-and-binding-definitions"></a>Trigger- en bindingsdefinities

Triggers en bindingen worden verschillend gedefinieerd, afhankelijk van de ontwikkelingstaal.

| Taal | Triggers en bindingen worden geconfigureerd door... |
|-------------|--------------------------------------------|
| C#-klassebibliotheek | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;methoden en parameters voor het gebruik van parameters met C#-kenmerken |
| Java | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;methoden en parameters voor het gebruik van parameters met Java-aantekeningen  | 
| JavaScript/PowerShell/Python/TypeScript | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;bijwerken [function.jsin](./functions-reference.md) ([schema](http://json.schemastore.org/function)) |

Voor talen die afhankelijk zijn van function.js, biedt de portal een gebruikersinterface voor het toevoegen van bindingen op het **tabblad** Integratie. U kunt het bestand ook rechtstreeks in de portal bewerken op het **tabblad Code + testen** van uw functie. Visual Studio code kunt u eenvoudig een binding toevoegen aan een [function.jsbestand](functions-develop-vs-code.md?tabs=nodejs#add-a-function-to-your-project) door een handige set prompts te volgen. 

In .NET en Java definieert het parametertype het gegevenstype voor invoergegevens. Gebruik bijvoorbeeld om te binden aan de tekst van een wachtrijtrigger, een bytematrix om als binair te lezen en een aangepast type om de serialiseren naar een `string` object te deser serialiseren. Omdat .NET-klassebibliotheekfuncties en Java-functies niet afhankelijk zijn van *function.js* voor bindingsdefinities, kunnen ze niet worden gemaakt en bewerkt in de portal. C#-portalbewerking is gebaseerd op C#-script, dat gebruikmaakt van *function.jsin* plaats van kenmerken.

Zie Functies verbinden met Azure-services met behulp van bindingen voor meer informatie over het toevoegen van [bindingen aan bestaande functies.](add-bindings-existing-function.md)

Voor talen die dynamisch worden getypt, zoals JavaScript, gebruikt u de eigenschap `dataType` in hetfunction.jsin *het* bestand. Als u bijvoorbeeld de inhoud van een HTTP-aanvraag in binaire indeling wilt lezen, stelt u in `dataType` op `binary` :

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Andere opties voor `dataType` zijn `stream` en `string` .

## <a name="binding-direction"></a>Bindingsrichting

Alle triggers en bindingen hebben een `direction` eigenschap in hetfunction.js[bestand:](./functions-reference.md)

- Voor triggers is de richting altijd `in`
- Invoer- en uitvoerbindingen gebruiken `in` en `out`
- Sommige bindingen ondersteunen een speciale `inout` richting. Als u `inout` gebruikt, is alleen de **geavanceerde editor** beschikbaar via het tabblad **Integreren** in de portal.

Wanneer u kenmerken [in een](functions-dotnet-class-library.md) klassebibliotheek gebruikt om triggers en bindingen te configureren, wordt de richting opgegeven in een kenmerkcon constructor of afgeleid van het parametertype.

## <a name="add-bindings-to-a-function"></a>Bindingen toevoegen aan een functie

U kunt uw functie verbinden met andere services met behulp van invoer- of uitvoerbindingen. Voeg een binding toe door de specifieke definities toe te voegen aan uw functie. Zie Bindingen toevoegen [aan een](add-bindings-existing-function.md)bestaande functie in Azure Functions .  

## <a name="supported-bindings"></a>Ondersteunde bindingen

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Zie Ondersteunde talen voor meer informatie over welke bindingen in preview zijn of goedgekeurd zijn voor [productiegebruik.](supported-languages.md)

## <a name="bindings-code-examples"></a>Voorbeelden van bindingscode

Gebruik de volgende tabel om voorbeelden te vinden van specifieke bindingstypen die laten zien hoe u met bindingen in uw functies kunt werken. Kies eerst het taaltabblad dat overeenkomt met uw project. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="custom-bindings"></a>Aangepaste bindingen

U kunt aangepaste invoer- en uitvoerbindingen maken. Bindingen moeten worden geschreven in .NET, maar kunnen worden gebruikt vanuit elke ondersteunde taal. Zie Aangepaste invoer- en uitvoerbindingen maken voor meer informatie over het maken [van aangepaste bindingen.](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings)

## <a name="resources"></a>Resources
- [Bindingexpressie en -patronen](./functions-bindings-expressions-patterns.md)
- [De retourwaarde van de Azure-functie gebruiken](./functions-bindings-return-value.md)
- [Een bindingexpressie registreren](./functions-bindings-register.md)
- Testing:
  - [Strategieën voor het testen van uw code in Azure Functions](functions-test-a-function.md)
  - [Handmatig een niet door HTTP geactiveerde functie uitvoeren](functions-manually-run-non-http.md)
- [Bindingsfouten afhandelen](./functions-bindings-errors.md)

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Bindingsextensies Azure Functions registreren](./functions-bindings-register.md)
