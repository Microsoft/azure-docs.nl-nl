---
title: Triggers en bindingen in Azure Functions
description: Meer informatie over het gebruik van triggers en bindingen om uw Azure-functie te koppelen aan online gebeurtenissen en Cloud Services.
author: craigshoemaker
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: aa0d78d52ec13c91b82e6a8d10720269076f59a1
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353541"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Concepten van Azure Functions triggers en bindingen

In dit artikel vindt u informatie over de concepten van het hoge niveau rond functies triggers en bindingen.

Triggers zijn de oorzaak dat een functie wordt uitgevoerd. Een trigger definieert hoe een functie wordt aangeroepen en een functie moet precies één trigger hebben. Aan triggers zijn gegevens gekoppeld. Meestal is dit de nettolading waarmee de functie is geactiveerd. 

Een binding met een functie is een manier om een andere resource declaratief aan de functie te koppelen. bindingen kunnen worden verbonden als *invoer bindingen*, *uitvoer bindingen* of beide. Gegevens van bindingen worden als parameters doorgegeven aan de functie.

U kunt verschillende bindingen mixen en matchen om aan uw behoeften te voldoen. Bindingen zijn optioneel en een functie kan een of meerdere invoer- en/of uitvoerbindingen hebben.

Met triggers en bindingen kunt u hardcoding toegang tot andere services voor komen. Uw functie ontvangt gegevens (bijvoorbeeld de inhoud van een wachtrijbericht) in functieparameters. U verzendt gegevens (bijvoorbeeld om een ​​wachtrijbericht te maken) door de retourwaarde van de functie te gebruiken. 

Bekijk de volgende voor beelden van hoe u verschillende functies kunt implementeren.

| Voorbeeldscenario | Trigger | Invoer binding | Uitvoer binding |
|-------------|---------|---------------|----------------|
| Er wordt een nieuw wachtrij bericht ontvangen waarmee een functie wordt uitgevoerd om naar een andere wachtrij te schrijven. | Wachtrij<sup>*</sup> | *Geen* | Wachtrij<sup>*</sup> |
|Met een geplande taak wordt Blob Storage inhoud gelezen en wordt er een nieuw Cosmos DB document gemaakt. | Timer | Blob Storage | Cosmos DB |
|De Event Grid wordt gebruikt voor het lezen van een afbeelding van Blob Storage en een document van Cosmos DB om een e-mail bericht te verzenden. | Event Grid | Blob Storage en Cosmos DB | SendGrid |
| Een webhook die gebruikmaakt van Microsoft Graph om een Excel-werk blad bij te werken. | HTTP | *Geen* | Microsoft Graph |

<sup>\*</sup> Vertegenwoordigt verschillende wacht rijen

Deze voor beelden zijn niet volledig, maar zijn bedoeld om te laten zien hoe u triggers en bindingen samen kunt gebruiken.

###  <a name="trigger-and-binding-definitions"></a>Definities van triggers en bindingen

Triggers en bindingen worden op verschillende manieren gedefinieerd, afhankelijk van de ontwikkelings benadering.

| Platform | Triggers en bindingen worden geconfigureerd door... |
|-------------|--------------------------------------------|
| C#-klassebibliotheek | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;de methoden en para meters voor C#-kenmerken |
| Alle andere (inclusief Azure Portal) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[function.jsbijwerken op](./functions-reference.md) ([schema](http://json.schemastore.org/function)) |

De portal biedt een gebruikers interface voor deze configuratie, maar u kunt het bestand rechtstreeks bewerken door de **Geavanceerde editor** te openen die beschikbaar is via het tabblad **integreren** van uw functie.

In .NET definieert het parameter type het gegevens type voor invoer gegevens. Gebruik bijvoorbeeld `string` om een binding te maken met de tekst van een wachtrij trigger, een byte matrix die als binair moet worden gelezen en een aangepast type om te deserialiseren naar een object.

Voor talen die dynamisch worden getypt, zoals Java script, gebruikt u de `dataType` eigenschap in de *function.jsin* het bestand. Als u bijvoorbeeld de inhoud van een HTTP-aanvraag in binaire indeling wilt lezen, stelt u het volgende in `dataType` `binary` :

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Andere opties voor `dataType` zijn `stream` en `string` .

## <a name="binding-direction"></a>Bindings richting

Alle triggers en bindingen hebben een `direction` eigenschap in de [function.jsvoor](./functions-reference.md) het volgende bestand:

- Voor triggers is de richting altijd `in`
- Invoer-en uitvoer bindingen gebruiken `in` en `out`
- Sommige bindingen bieden ondersteuning voor een speciale richting `inout` . Als u gebruikt `inout` , is alleen de **Geavanceerde editor** beschikbaar via het tabblad **integreren** in de portal.

Wanneer u [kenmerken in een klassen bibliotheek](functions-dotnet-class-library.md) gebruikt om triggers en bindingen te configureren, wordt de richting in een kenmerk-constructor of afgeleid van het parameter type gegeven.

## <a name="add-bindings-to-a-function"></a>Bindingen aan een functie toevoegen

U kunt uw functie verbinden met andere services met behulp van invoer-of uitvoer bindingen. Voeg een binding toe door de specifieke definities aan uw functie toe te voegen. Zie [bindingen toevoegen aan een bestaande functie in azure functions](add-bindings-existing-function.md)voor meer informatie.  

## <a name="supported-bindings"></a>Ondersteunde bindingen

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Zie [ondersteunde talen](supported-languages.md)voor informatie over welke bindingen in Preview zijn of die zijn goedgekeurd voor productie gebruik.

## <a name="bindings-code-examples"></a>Code voorbeelden voor bindingen

Gebruik de volgende tabel om voor beelden te vinden van specifieke bindings typen die laten zien hoe u met bindingen kunt werken in uw-functies. Kies eerst het tabblad taal dat overeenkomt met uw project. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="custom-bindings"></a>Aangepaste bindingen

U kunt aangepaste invoer-en uitvoer bindingen maken. Bindingen moeten zijn geschreven in .NET, maar kunnen worden gebruikt vanuit elke ondersteunde taal. Zie [aangepaste invoer-en uitvoer bindingen maken](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings)voor meer informatie over het maken van aangepaste bindingen.

## <a name="resources"></a>Resources
- [Expressies en patronen binden](./functions-bindings-expressions-patterns.md)
- [De functie retour waarde van Azure gebruiken](./functions-bindings-return-value.md)
- [Een bindings expressie registreren](./functions-bindings-register.md)
- Testen
  - [Strategieën voor het testen van uw code in Azure Functions](functions-test-a-function.md)
  - [Handmatig een niet door HTTP geactiveerde functie uitvoeren](functions-manually-run-non-http.md)
- [Bindings fouten verwerken](./functions-bindings-errors.md)

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Azure Functions bindings uitbreidingen registreren](./functions-bindings-register.md)
