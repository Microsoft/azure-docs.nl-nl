---
title: Naslag Gids voor functies in expressies
description: Naslag Gids voor functies in expressies voor Azure Logic Apps en energie automatisering
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: reference
ms.date: 03/12/2021
ms.openlocfilehash: 1414a7b0f17918caa16ccf854d70ea199fb42a47
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870191"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>Naslag Gids voor het gebruik van functies in expressies voor Azure Logic Apps en energie automatisering

Voor werk stroom definities in [Azure Logic apps](../logic-apps/logic-apps-overview.md) en het [automatiseren](/flow/getting-started)van de stroom, krijgen sommige [expressies](../logic-apps/logic-apps-workflow-definition-language.md#expressions) hun waarden van runtime-acties die mogelijk nog niet bestaan wanneer de werk stroom wordt gestart. Als u wilt verwijzen naar deze waarden of de waarden in deze expressies wilt verwerken, kunt u *functies* van de [werk stroom definitie taal](../logic-apps/logic-apps-workflow-definition-language.md)gebruiken.

> [!NOTE]
> Deze referentie pagina is van toepassing op zowel Azure Logic Apps als automatische energie, maar wordt weer gegeven in de Azure Logic Apps documentatie. Hoewel deze pagina specifiek verwijst naar Logic apps, werken deze functies voor zowel stromen als logische apps. Zie [expressies in voor waarden gebruiken](/flow/use-expressions-in-conditions)voor meer informatie over functies en expressies in automatische energie automatisering.

U kunt bijvoorbeeld waarden berekenen met behulp van wiskundige functies, zoals de functie [Add ()](../logic-apps/workflow-definition-language-functions-reference.md#add) , wanneer u de som van gehele getallen of zwevende tekens wilt gebruiken. Hier vindt u andere voorbeeld taken die u met functies kunt uitvoeren:

| Taak | Syntaxis van functie | Resultaat |
| ---- | --------------- | ------ |
| Retourneert een teken reeks met de indeling kleine letters. | toLower (' <*text*> ') <p>Bijvoorbeeld: toLower (' Hallo ') | Hello |
| Een Globally Unique Identifier (GUID) retour neren. | GUID () |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

Als u functies wilt vinden [op basis van hun algemene doel](#ordered-by-purpose), raadpleegt u de volgende tabellen. Of Zie de [Alfabetische lijst](#alphabetical-list)voor gedetailleerde informatie over elke functie.

## <a name="functions-in-expressions"></a>Functies in expressies

Dit voor beeld laat zien hoe u de waarde van de `customerName` para meter kunt ophalen en deze waarde aan de eigenschap moet toewijzen met `accountName` behulp van de functie [para meters ()](#parameters) in een expressie om te laten zien hoe u een functie in een expressie gebruikt:

```json
"accountName": "@parameters('customerName')"
```

Hier volgen enkele andere algemene manieren waarop u functies in expressies kunt gebruiken:

| Taak | Syntaxis van de functie in een expressie |
| ---- | -------------------------------- |
| Werk met een item uitvoeren door dit item door te geven aan een functie. | " \@ < *functie naam*> (<*item*>)" |
| 1. Haal de waarde van de *para meter* op met behulp van de geneste `parameters()` functie. </br>2. Voer het werk uit met het resultaat door deze waarde door te geven aan *functie naam*. | " \@ < *functie naam*> (para meters (' <*parameter* naam> '))" |
| 1. Haal het resultaat op uit de geneste Inner Function- *functie naam*. </br>2. het resultaat wordt door gegeven aan de buitenste functie *functionName2*. | " \@ < *functionName2*> (<*functie naam*> (<*item*>))" |
| 1. Haal het resultaat op uit de *functie naam*. </br>2. als het resultaat een object is met eigenschaps *eigenschapnaam*, haalt u de waarde van die eigenschap op. | " \@ < *functie naam*> (<*item*>). <*PropertyName*>" |
|||

De `concat()` functie kan bijvoorbeeld twee of meer teken reeks waarden als para meters hebben. Deze functie combineert deze teken reeksen in één teken reeks. U kunt letterlijke teken reeksen door geven, bijvoorbeeld ' Sophia ' en ' Owen ', zodat u een gecombineerde teken reeks, ' SophiaOwen ' krijgt:

```json
"customerName": "@concat('Sophia', 'Owen')"
```

U kunt ook teken reeks waarden van para meters ophalen. In dit voor beeld wordt de `parameters()` functie gebruikt in elke `concat()` para meter en de `firstName` `lastName` para meters en. U geeft de resulterende teken reeksen vervolgens door aan de `concat()` functie zodat u een gecombineerde teken reeks krijgt, bijvoorbeeld ' SophiaOwen ':

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

In beide gevallen wordt het resultaat door beide voor beelden aan de `customerName` eigenschap toegewezen.

Hier volgen enkele andere opmerkingen over functies in expressies:

* Functie parameters worden geëvalueerd van links naar rechts.

* In de syntaxis voor parameter definities is een vraag teken (?) die wordt weer gegeven na een para meter, de para meter optioneel. Zie bijvoorbeeld [getFutureTime ()](#getFutureTime).

In de volgende secties worden de functies ingedeeld op basis van hun algemene doel of kunt u deze functies in [alfabetische volg orde](#alphabetical-list)doorzoeken.

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Tekenreeksfuncties

Als u met teken reeksen wilt werken, kunt u deze teken reeks functies en ook bepaalde [verzamelings functies](#collection-functions)gebruiken. Teken reeks functies werken alleen voor teken reeksen.

| Teken reeks functie | Taak |
| --------------- | ---- |
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Combi neer twee of meer teken reeksen en retour neer de gecombineerde teken reeks. |
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Controleren of een teken reeks eindigt met de opgegeven subtekenreeks. |
| [formatNumber](../logic-apps/workflow-definition-language-functions-reference.md#formatNumber) | Een getal retour neren als een teken reeks op basis van de opgegeven indeling |
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Genereer een Globally Unique Identifier (GUID) als een teken reeks. |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Retourneert de begin positie van een subtekenreeks. |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Retourneert de begin positie voor het laatste exemplaar van een subtekenreeks. |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Retourneert het aantal items in een teken reeks of matrix. |
| [vervangen](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Vervang een subtekenreeks door de opgegeven teken reeks en retour neer de bijgewerkte teken reeks. |
| [delen](../logic-apps/workflow-definition-language-functions-reference.md#split) | Retourneert een matrix die subtekenreeksen bevat, gescheiden door komma's, van een grotere teken reeks op basis van een opgegeven scheidings teken in de oorspronkelijke teken reeks. |
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Controleren of een teken reeks begint met een specifieke subtekenreeks. |
| [subtekenreeks](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Retourneert tekens uit een teken reeks, te beginnen met de opgegeven positie. |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Retourneert een teken reeks met de indeling kleine letters. |
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Retourneert een teken reeks met een hoofd letter. |
| [interne](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Verwijder voor loop-en volg spaties uit een teken reeks en retour neer de bijgewerkte teken reeks. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Verzamelingsfuncties

Als u wilt werken met verzamelingen, meestal matrices, teken reeksen en soms, woorden lijsten, kunt u deze verzamelings functies gebruiken.

| Functie verzameling | Taak |
| ------------------- | ---- |
| [daarin](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Controleer of een verzameling een specifiek item heeft. |
| [leeg](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Controleer of een verzameling leeg is. |
| [instantie](../logic-apps/workflow-definition-language-functions-reference.md#first) | Het eerste item van een verzameling retour neren. |
| [Snij punt](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Een verzameling retour neren die *alleen* de gemeen schappelijke items in de opgegeven verzamelingen heeft. |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Wanneer een herhalende actie een matrix heeft, wordt het huidige item in de matrix geretourneerd tijdens de huidige iteratie van de actie. |
| [Jointypen](../logic-apps/workflow-definition-language-functions-reference.md#join) | Retourneert een teken reeks met *alle* items uit een matrix, gescheiden door het opgegeven teken. |
| [duren](../logic-apps/workflow-definition-language-functions-reference.md#last) | Het laatste item van een verzameling retour neren. |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Retourneert het aantal items in een teken reeks of matrix. |
| [verdergaan](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Verwijder items van de voor kant van een verzameling en retour neer *alle andere* items. |
| [Houd](../logic-apps/workflow-definition-language-functions-reference.md#take) | Items van de voor grond van een verzameling retour neren. |
| [Réunion](../logic-apps/workflow-definition-language-functions-reference.md#union) | Een verzameling retour neren die *alle* items uit de opgegeven verzamelingen bevat. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Logische vergelijkings functies

Als u met voor waarden wilt werken, vergelijkt u waarden en expressie resultaten of evalueert u verschillende soorten logica, kunt u deze logische vergelijkings functies gebruiken. Zie de [Alfabetische lijst](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)voor de volledige naslag informatie over elke functie.

> [!NOTE]
> Als u logische functies of voor waarden gebruikt om waarden te vergelijken, worden Null-waarden geconverteerd naar lege teken reeks ( `""` ) waarden. Het gedrag van voor waarden wijkt af wanneer u met een lege teken reeks vergelijkt in plaats van een null-waarde. Zie de [functie String ()](#string)voor meer informatie. 

| Logische vergelijkings functie | Taak |
| --------------------------- | ---- |
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) | Controleer of alle expressies waar zijn. |
| [gelijk is aan](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Controleer of beide waarden gelijk zijn. |
| [groter](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Controleer of de eerste waarde groter is dan de tweede waarde. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Controleer of de eerste waarde groter is dan of gelijk is aan de tweede waarde. |
| [If](../logic-apps/workflow-definition-language-functions-reference.md#if) | Controleer of een expressie True of False is. Retour neer een opgegeven waarde op basis van het resultaat. |
| [jonge](../logic-apps/workflow-definition-language-functions-reference.md#less) | Controleer of de eerste waarde lager is dan de tweede waarde. |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Controleer of de eerste waarde kleiner is dan of gelijk is aan de tweede waarde. |
| [ten](../logic-apps/workflow-definition-language-functions-reference.md#not) | Controleer of een expressie onwaar is. |
| [or](../logic-apps/workflow-definition-language-functions-reference.md#or) | Controleer of ten minste één expressie waar is. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Conversiefuncties

Als u het type of de indeling van een waarde wilt wijzigen, kunt u deze conversie functies gebruiken. U kunt bijvoorbeeld een waarde wijzigen van een Boolean in een geheel getal. Zie voor meer informatie over Logic Apps het afhandelen van inhouds typen tijdens de conversie [inhouds typen verwerken](../logic-apps/logic-apps-content-type.md). Zie de [Alfabetische lijst](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)voor de volledige naslag informatie over elke functie.

> [!NOTE]
> Azure Logic Apps converteert automatisch waarden tussen sommige gegevens typen, wat betekent dat u deze conversies niet hand matig hoeft uit te voeren. Als u dit wel doet, kunt u echter onverwachte weergave gedrag ondervinden die niet van invloed is op de daad werkelijke conversies, maar alleen op de manier waarop deze worden weer gegeven. Zie [impliciete gegevens type conversies](#implicit-data-conversions)voor meer informatie.

| Conversie functie | Taak |
| ------------------- | ---- |
| [matrix](../logic-apps/workflow-definition-language-functions-reference.md#array) | Een matrix retour neren van een enkele opgegeven invoer. Zie [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray)voor meerdere invoer. |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Retourneert de met base64 gecodeerde versie voor een teken reeks. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Retourneert de binaire versie voor een base64-gecodeerde teken reeks. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Retourneert de versie van de teken reeks voor een base64-gecodeerde teken reeks. |
| [waarde](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Retourneert de binaire versie voor een invoer waarde. |
| [booleaans](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Retourneert de Booleaanse versie van een invoer waarde. |
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Een matrix van meerdere invoer waarden retour neren. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | De gegevens-URI voor een invoer waarde Retour neren. |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Retourneert de binaire versie voor een gegevens-URI. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Retourneert de versie van de teken reeks voor een gegevens-URI. |
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Retourneert de versie van de teken reeks voor een base64-gecodeerde teken reeks. |
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Retourneert de binaire versie voor een gegevens-URI. |
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Retourneert een teken reeks waarmee escape tekens worden vervangen door gedecodeerde versies. |
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Retourneert een teken reeks waarmee URL-onveilige tekens worden vervangen door Escape-tekens. |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Retourneert een drijvende-komma waarde voor een invoer waarde. |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Retourneert de versie met gehele getallen voor een teken reeks. |
| [JSON](../logic-apps/workflow-definition-language-functions-reference.md#json) | De waarde of het object van het type JavaScript Object Notation (JSON) retour neren voor een teken reeks of XML. |
| [tekenreeks](../logic-apps/workflow-definition-language-functions-reference.md#string) | Retourneert de teken reeks versie voor een invoer waarde. |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | De versie van de URI-code ring retour neren voor een invoer waarde door onveilige URL-tekens te vervangen door Escape tekens. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Retourneert de binaire versie voor een teken reeks met URI-code ring. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Retourneert de versie van de teken reeks voor een teken reeks met URI-code ring. |
| [indeling](../logic-apps/workflow-definition-language-functions-reference.md#xml) | De XML-versie voor een teken reeks retour neren. |
|||

<a name="implicit-data-conversions"></a>

## <a name="implicit-data-type-conversions"></a>Impliciete gegevens type conversies

Azure Logic Apps automatisch of impliciet geconverteerd tussen sommige gegevens typen, zodat u deze typen niet hand matig hoeft te converteren. Als u bijvoorbeeld niet-teken reeks waarden gebruikt waarbij teken reeksen worden verwacht als invoer, worden Logic Apps de niet-teken reeks waarden automatisch geconverteerd naar teken reeksen.

Stel bijvoorbeeld dat een trigger een numerieke waarde retourneert als uitvoer:

`triggerBody()?['123']`

Als u deze numerieke uitvoer gebruikt en er wordt een teken reeks invoer verwacht, zoals een URL, Logic Apps zet de waarde automatisch om in een teken reeks met behulp van de notatie accolades ( `{}` ):

`@{triggerBody()?['123']}`

### <a name="base64-encoding-and-decoding"></a>Base64 coderen en decoderen

Logic Apps automatisch of impliciet base64-code ring of-decodering uitvoert, zodat u deze bewerkingen niet hand matig hoeft uit te voeren met behulp van de bijbehorende expressies:

* `base64(<value>)`
* `base64ToBinary(<value>)`
* `base64ToString(<value>)`
* `base64(decodeDataUri(<value>))`
* `concat('data:;base64,',<value>)`
* `concat('data:,',encodeUriComponent(<value>))`
* `decodeDataUri(<value>)`

> [!NOTE]
> Als u deze expressies hand matig toevoegt aan uw logische app, bijvoorbeeld met behulp van de expressie-editor, navigeert u naar de ontwerp functie voor logische apps en gaat u terug naar de ontwerp functie. de ontwerp functie toont alleen de parameter waarden. De expressies worden alleen in de code weergave bewaard als u de parameter waarden niet bewerkt. Anders verwijdert Logic Apps de expressies uit de code weergave, zodat alleen de parameter waarden worden weer gegeven. Dit gedrag heeft geen invloed op coderen en decoderen, alleen of de expressies worden weer gegeven.

<a name="math-functions"></a>

## <a name="math-functions"></a>Wiskundige functies

Als u met gehele getallen en zwevende tekens wilt werken, kunt u deze wiskundige functies gebruiken.
Zie de [Alfabetische lijst](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)voor de volledige naslag informatie over elke functie.

| Wiskundige functie | Taak |
| ------------- | ---- |
| [add](../logic-apps/workflow-definition-language-functions-reference.md#add) | Het resultaat van het toevoegen van twee getallen retour neren. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Retourneert het resultaat van het delen van twee getallen. |
| [aantal](../logic-apps/workflow-definition-language-functions-reference.md#max) | Retourneert de hoogste waarde van een reeks getallen of een matrix. |
| [min.](../logic-apps/workflow-definition-language-functions-reference.md#min) | Retourneert de laagste waarde van een reeks getallen of een matrix. |
| [mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Retourneert de rest van het delen van twee getallen. |
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Retourneert het product van het vermenigvuldigen van twee getallen. |
| [ASELECT](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Retourneert een wille keurig geheel getal uit een opgegeven bereik. |
| [bereik](../logic-apps/workflow-definition-language-functions-reference.md#range) | Retourneert een matrix met gehele getallen die begint met een opgegeven geheel getal. |
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Retourneert het resultaat van het aftrekken van het tweede getal uit het eerste getal. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Datum- en tijdfuncties

Als u met datums en tijden wilt werken, kunt u deze datum-en tijd functies gebruiken.
Zie de [Alfabetische lijst](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)voor de volledige naslag informatie over elke functie.

| De functie datum of tijd | Taak |
| --------------------- | ---- |
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Voeg een aantal dagen toe aan een tijds tempel. |
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Voeg een aantal uren toe aan een tijds tempel. |
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Voeg een aantal minuten toe aan een tijds tempel. |
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Voeg een aantal seconden toe aan een tijds tempel. |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Voeg een aantal tijds eenheden toe aan een tijds tempel. Zie ook [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). |
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Converteer een tijds tempel van Universal Time Coordinated (UTC) naar de doel tijdzone. |
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Converteer een tijds tempel van de bron tijdzone naar de doel tijdzone. |
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Een tijds tempel van de bron tijdzone converteren naar Universal Time Coordinated (UTC). |
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Retourneert de dag van het onderdeel month van een tijds tempel. |
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Retourneert de dag van de week component van een tijds tempel. |
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Retourneert de dag van het jaar component van een tijds tempel. |
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | De datum van een tijds tempel retour neren. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | De huidige tijds tempel en de opgegeven tijds eenheden retour neren. Zie ook [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Retourneert de huidige tijds tempel min de opgegeven tijds eenheden. Zie ook [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Retourneert het begin van de dag voor een tijds tempel. |
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Het begin van het uur retour neren voor een tijds tempel. |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Retourneert het begin van de maand voor een tijds tempel. |
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Een aantal tijds eenheden aftrekken van een tijds tempel. Zie ook [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). |
| [Ticks](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | De `ticks` eigenschaps waarde voor een opgegeven tijds tempel retour neren. |
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | De huidige tijds tempel retour neren als een teken reeks. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>Werk stroom functies

Deze werk stroom functies helpen u bij het volgende:

* Details over een werk stroom exemplaar tijdens runtime ophalen.
* Werk samen met de invoer die wordt gebruikt voor het instantiëren van logische apps of stromen.
* Raadpleeg de uitvoer van triggers en acties.

U kunt bijvoorbeeld naar de uitvoer van de ene actie verwijzen en die gegevens gebruiken in een latere actie.
Zie de [Alfabetische lijst](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)voor de volledige naslag informatie over elke functie.

| Werkstroomfunctie | Taak |
| ----------------- | ---- |
| [action](../logic-apps/workflow-definition-language-functions-reference.md#action) | De uitvoer van de huidige actie tijdens runtime retour neren of waarden van andere JSON-naam-en-waardeparen. Zie ook [acties](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | De uitvoer van een actie `body` tijdens runtime retour neren. Zie ook [hoofd tekst](../logic-apps/workflow-definition-language-functions-reference.md#body). |
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | De uitvoer van een actie tijdens runtime retour neren. Zie [uitvoer](../logic-apps/workflow-definition-language-functions-reference.md#outputs) en [acties](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [regelen](../logic-apps/workflow-definition-language-functions-reference.md#actions) | De uitvoer van een actie tijdens runtime retour neren of waarden van andere JSON-naam-en-waardeparen. Zie ook [actie](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [organen](#body) | De uitvoer van een actie `body` tijdens runtime retour neren. Zie ook [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Maak een matrix met de waarden die overeenkomen met een sleutel naam in uitvoer van *formulier gegevens* of *formulier-gecodeerde* acties. |
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Eén waarde Retour neren die overeenkomt met een sleutel naam in een actie *formulier gegevens* of *formulier-gecodeerde uitvoer*. |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Wanneer een herhalende actie een matrix heeft, wordt het huidige item in de matrix geretourneerd tijdens de huidige iteratie van de actie. |
| [vermeldingen](../logic-apps/workflow-definition-language-functions-reference.md#items) | In een foreach-of until-lus retourneert het huidige item van de opgegeven lus.|
| [iterationIndexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | Als de lus until is, retourneert u de index waarde voor de huidige iteratie. U kunt deze functie binnen nesten tot lussen gebruiken. |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | De call back-URL retour neren die een trigger of actie aanroept. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | De hoofd tekst van een specifiek deel in de uitvoer van een actie met meerdere delen retour neren. |
| [uitvoer](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | De uitvoer van een actie tijdens runtime retour neren. |
| [instellen](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Retourneert de waarde voor een para meter die wordt beschreven in uw werk stroom definitie. |
| [Daardoor](../logic-apps/workflow-definition-language-functions-reference.md#result) | De invoer en uitvoer retour neren van de acties op het hoogste niveau binnen de opgegeven actie met een bereik, zoals `For_each` , `Until` en `Scope` . |
| [activeren](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | De uitvoer van een trigger retour neren tijdens runtime of vanuit andere JSON-naam-en-waardeparen. Zie ook [triggerOutputs](#triggerOutputs) en [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Retour neer de uitvoer van een trigger `body` tijdens runtime. Zie [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Een enkele waarde Retour neren die overeenkomt met een sleutel naam in trigger uitvoer van *formulier gegevens* of *formulier codering* . |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | De hoofd tekst voor een specifiek deel in de meerdelige uitvoer van een trigger retour neren. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Maak een matrix waarvan de waarden overeenkomen met de naam van een sleutel in de trigger uitvoer van *formulier gegevens* of *formulier-gecodeerd* . |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Retourneert de uitvoer van een trigger tijdens runtime of waarden van andere JSON-naam-en-waardeparen. Zie [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [variabelen](../logic-apps/workflow-definition-language-functions-reference.md#variables) | De waarde voor een opgegeven variabele retour neren. |
| [workflowconfiguraties](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Alle gegevens over de werk stroom zelf retour neren tijdens de uitvoerings tijd. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>Functies voor het parseren van URI'S

Als u wilt werken met URI (Uniform Resource Identifiers) en verschillende eigenschaps waarden voor deze Uri's wilt ophalen, kunt u deze functies voor het parseren van uri's gebruiken.
Zie de [Alfabetische lijst](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)voor de volledige naslag informatie over elke functie.

| URI-parserings functie | Taak |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | De `host` waarde voor een Uniform Resource Identifier (URI) retour neren. |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | De `path` waarde voor een Uniform Resource Identifier (URI) retour neren. |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | De `path` waarden en `query` voor een Uniform Resource Identifier (URI) retour neren. |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | De `port` waarde voor een Uniform Resource Identifier (URI) retour neren. |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | De `query` waarde voor een Uniform Resource Identifier (URI) retour neren. |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | De `scheme` waarde voor een Uniform Resource Identifier (URI) retour neren. |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Manipulatie functies: JSON & XML

Als u wilt werken met JSON-objecten en XML-knoop punten, kunt u deze manipulatie functies gebruiken.
Zie de [Alfabetische lijst](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)voor de volledige naslag informatie over elke functie.

| Functie manipulatie | Taak |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Voeg een eigenschap en bijbehorende waarde, of naam/waarde-paar, toe aan een JSON-object en retour neer het bijgewerkte object. |
| [Voeg](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Retourneert de eerste waarde die niet null is van een of meer para meters. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Een eigenschap verwijderen uit een JSON-object en het bijgewerkte object retour neren. |
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Stel de waarde voor de eigenschap van een JSON-object in en retour neer het bijgewerkte object. |
| [XPath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Controleer XML voor knoop punten of waarden die overeenkomen met een XPath-expressie (XML-pad) en retour neer de overeenkomende knoop punten of waarden. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Alle functies-alfabetische lijst

In deze sectie worden alle beschik bare functies in alfabetische volg orde weer gegeven.

<a name="action"></a>

### <a name="action"></a>actie

De uitvoer van de *huidige* actie tijdens runtime retour neren of waarden van andere JSON-naam-en-waardeparen, die u kunt toewijzen aan een expressie.
Deze functie verwijst standaard naar het hele actie object, maar u kunt desgewenst een eigenschap opgeven waarvan u de waarde wilt bepalen.
Zie ook [acties ()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

U kunt de `action()` functie alleen op de volgende locaties gebruiken:

* De `unsubscribe` eigenschap voor een webhook-actie, zodat u het resultaat van de oorspronkelijke `subscribe` aanvraag kunt openen
* De `trackedProperties` eigenschap voor een actie
* De `do-until` lus-voor waarde voor een actie

```
action()
action().outputs.body.<property>
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*eigenschap*> | Nee | Tekenreeks | De naam van de eigenschap van het actie object waarvan u de waarde wilt: **naam**, **StartTime**, **EndTime**, **invoer**, **uitvoer**, **status**, **code**, **trackingId** en **clientTrackingId**. In de Azure Portal kunt u deze eigenschappen vinden door de details van een specifieke uitvoerings geschiedenis te bekijken. Zie [rest API-werk stroom acties uitvoeren](/rest/api/logic/workflowrunactions/get)voor meer informatie. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| <*actie-uitvoer*> | Tekenreeks | De uitvoer van de huidige actie of eigenschap |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

De uitvoer van een actie `body` tijdens runtime retour neren.
Steno voor `actions('<actionName>').outputs.body` .
Zie [hoofd tekst ()](#body) en [acties ()](#actions).

```
actionBody('<actionName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De naam voor de uitvoer van de actie `body` die u wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| <*actie-hoofd tekst-uitvoer*> | Tekenreeks | De `body` uitvoer van de opgegeven actie |
||||

*Voorbeeld*

In dit voor beeld wordt de `body` uitvoer van de Twitter-actie opgehaald `Get user` :

```
actionBody('Get_user')
```

En retourneert dit resultaat:

```json
"body": {
  "FullName": "Contoso Corporation",
  "Location": "Generic Town, USA",
  "Id": 283541717,
  "UserName": "ContosoInc",
  "FollowersCount": 172,
  "Description": "Leading the way in transforming the digital workplace.",
  "StatusesCount": 93,
  "FriendsCount": 126,
  "FavouritesCount": 46,
  "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="actionOutputs"></a>

### <a name="actionoutputs"></a>actionOutputs

De uitvoer van een actie tijdens runtime retour neren.  en is steno voor `actions('<actionName>').outputs` . Zie [acties ()](#actions). De `actionOutputs()` functie `outputs()` wordt omgezet in in de Logic app Designer. Overweeg daarom het gebruik van [uitvoer ()](#outputs)in plaats van `actionOutputs()` . Hoewel beide functies op dezelfde manier werken, verdient de `outputs()` voor keur.

```
actionOutputs('<actionName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De naam voor de uitvoer van de actie die u wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| <*uitvoer*> | Tekenreeks | De uitvoer van de opgegeven actie |
||||

*Voorbeeld*

In dit voor beeld wordt de uitvoer van de Twitter-actie opgehaald `Get user` :

```
actionOutputs('Get_user')
```

En retourneert dit resultaat:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="actions"></a>

### <a name="actions"></a>acties

De uitvoer van een actie tijdens runtime retour neren of waarden van andere JSON-naam-en-waardeparen, die u kunt toewijzen aan een expressie. De functie verwijst standaard naar het hele actie object, maar u kunt desgewenst een eigenschap opgeven waarvan u de waarde wilt bepalen.
Zie [actionBody ()](#actionBody), [actionOutputs ()](#actionOutputs)en [Body ()](#body)voor steno versies.
Zie [actie ()](#action)voor de huidige actie.

> [!TIP]
> De `actions()` functie retourneert uitvoer als een teken reeks. Als u wilt werken met een geretourneerde waarde als een JSON-object, moet u eerst de teken reeks waarde converteren. U kunt de teken reeks waarde omzetten in een JSON-object met behulp van de [actie JSON parseren](logic-apps-perform-data-operations.md#parse-json-action).

> [!NOTE]
> U kunt voorheen de `actions()` functie of het element gebruiken `conditions` Wanneer u opgeeft dat een actie is uitgevoerd op basis van de uitvoer van een andere actie. Als u echter expliciete afhankelijkheden wilt declareren tussen acties, moet u nu de eigenschap van de afhankelijke actie gebruiken `runAfter` .
> Zie voor meer informatie over de `runAfter` eigenschap [fouten met betrekking tot Catch en handle met de eigenschap runAfter](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De naam voor het actie object waarvan u de uitvoer wilt  |
| <*eigenschap*> | Nee | Tekenreeks | De naam van de eigenschap van het actie object waarvan u de waarde wilt: **naam**, **StartTime**, **EndTime**, **invoer**, **uitvoer**, **status**, **code**, **trackingId** en **clientTrackingId**. In de Azure Portal kunt u deze eigenschappen vinden door de details van een specifieke uitvoerings geschiedenis te bekijken. Zie [rest API-werk stroom acties uitvoeren](/rest/api/logic/workflowrunactions/get)voor meer informatie. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| <*actie-uitvoer*> | Tekenreeks | De uitvoer van de opgegeven actie of eigenschap |
||||

*Voorbeeld*

In dit voor beeld wordt de `status` eigenschaps waarde van de actie Twitter `Get user` tijdens runtime opgehaald:

```
actions('Get_user').outputs.body.status
```

En retourneert dit resultaat: `"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

Het resultaat van het toevoegen van twee getallen retour neren.

```
add(<summand_1>, <summand_2>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <<*summand_2* *summand_1*>> | Ja | Geheel getal, vlotter of gemengd | De toe te voegen getallen |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| <*resultaat-Sum*> | Geheel getal of zwevend | Het resultaat van het toevoegen van de opgegeven getallen |
||||

*Voorbeeld*

In dit voor beeld worden de opgegeven getallen toegevoegd:

```
add(1, 1.5)
```

En retourneert dit resultaat: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Voeg een aantal dagen toe aan een tijds tempel.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*resterende*> | Ja | Geheel getal | Het positieve of negatieve aantal dagen dat moet worden toegevoegd |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS. fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tijds tempel*> | Tekenreeks | De tijds tempel plus het opgegeven aantal dagen  |
||||

*Voorbeeld 1*

In dit voor beeld worden 10 dagen toegevoegd aan de opgegeven tijds tempel:

```
addDays('2018-03-15T00:00:00Z', 10)
```

En retourneert dit resultaat: `"2018-03-25T00:00:00.0000000Z"`

*Voorbeeld 2*

In dit voor beeld worden vijf dagen afgetrokken van de opgegeven tijds tempel:

```
addDays('2018-03-15T00:00:00Z', -5)
```

En retourneert dit resultaat: `"2018-03-10T00:00:00.0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Voeg een aantal uren toe aan een tijds tempel.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*loopt*> | Ja | Geheel getal | Het positieve of negatieve aantal uur dat moet worden toegevoegd |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS. fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tijds tempel*> | Tekenreeks | De tijds tempel plus het opgegeven aantal uren  |
||||

*Voorbeeld 1*

In dit voor beeld worden 10 uur toegevoegd aan de opgegeven tijds tempel:

```
addHours('2018-03-15T00:00:00Z', 10)
```

En retourneert dit resultaat: ' "2018-03-15T10:00:00.0000000 Z"

*Voorbeeld 2*

In dit voor beeld worden vijf uur afgetrokken van de opgegeven tijds tempel:

```
addHours('2018-03-15T15:00:00Z', -5)
```

En retourneert dit resultaat: `"2018-03-15T10:00:00.0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Voeg een aantal minuten toe aan een tijds tempel.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*wachten*> | Ja | Geheel getal | Het positieve of negatieve aantal minuten dat moet worden toegevoegd |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS. fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tijds tempel*> | Tekenreeks | De tijds tempel plus het opgegeven aantal minuten |
||||

*Voorbeeld 1*

In dit voor beeld wordt 10 minuten aan de opgegeven tijds tempel toegevoegd:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

En retourneert dit resultaat: `"2018-03-15T00:20:00.0000000Z"`

*Voorbeeld 2*

In dit voor beeld worden vijf minuten afgetrokken van de opgegeven tijds tempel:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

En retourneert dit resultaat: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Voeg een eigenschap en bijbehorende waarde, of naam/waarde-paar, toe aan een JSON-object en retour neer het bijgewerkte object. Als de eigenschap al bestaat tijdens runtime, mislukt de functie en wordt er een fout gegenereerd.

```
addProperty(<object>, '<property>', <value>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*object*> | Ja | Object | Het JSON-object waaraan u een eigenschap wilt toevoegen |
| <*eigenschap*> | Ja | Tekenreeks | De naam van de toe te voegen eigenschap |
| <*Value*> | Ja | Alle | De waarde voor de eigenschap |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-object*> | Object | Het bijgewerkte JSON-object met de opgegeven eigenschap |
||||

Als u een bovenliggende eigenschap aan een bestaande eigenschap wilt toevoegen, gebruikt u de `setProperty()` functie en niet de `addProperty()` functie. Anders retourneert de functie alleen het onderliggende object als uitvoer.

```
setProperty(<object>['<parent-property>'], '<parent-property>', addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*object*> | Ja | Object | Het JSON-object waaraan u een eigenschap wilt toevoegen |
| <*Parent-eigenschap*> | Ja | Tekenreeks | De naam van de bovenliggende eigenschap waaraan u de onderliggende eigenschap wilt toevoegen |
| <*Child-eigenschap*> | Ja | Tekenreeks | De naam van de onderliggende eigenschap die moet worden toegevoegd |
| <*Value*> | Ja | Alle | De waarde die moet worden ingesteld voor de opgegeven eigenschap |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-object*> | Object | Het bijgewerkte JSON-object waarvan u de eigenschap hebt ingesteld |
||||

*Voorbeeld 1*

In dit voor beeld wordt de `middleName` eigenschap toegevoegd aan een JSON-object, dat wordt geconverteerd van een teken reeks naar JSON met behulp van de [JSON ()](#json) -functie. Het object bevat al de `firstName` Eigenschappen en en `surName` . De functie wijst de opgegeven waarde toe aan de nieuwe eigenschap en retourneert het bijgewerkte object:

```
addProperty(json('{ "firstName": "Sophia", "lastName": "Owen" }'), 'middleName', 'Anne')
```

Hier is het huidige JSON-object:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Hier is het bijgewerkte JSON-object:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

*Voorbeeld 2*

In dit voor beeld wordt de `middleName` eigenschap Child toegevoegd aan de bestaande `customerName` eigenschap in een JSON-object, dat wordt geconverteerd van een teken reeks naar JSON met behulp van de [JSON ()](#json) -functie. De functie wijst de opgegeven waarde toe aan de nieuwe eigenschap en retourneert het bijgewerkte object:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', addProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'middleName', 'Anne'))
```

Hier is het huidige JSON-object:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

Hier is het bijgewerkte JSON-object:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Voeg een aantal seconden toe aan een tijds tempel.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*waarna*> | Ja | Geheel getal | Het positieve of negatieve aantal seconden dat moet worden toegevoegd |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS. fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tijds tempel*> | Tekenreeks | De tijds tempel plus het opgegeven aantal seconden  |
||||

*Voorbeeld 1*

In dit voor beeld wordt 10 seconden toegevoegd aan de opgegeven tijds tempel:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

En retourneert dit resultaat: `"2018-03-15T00:00:10.0000000Z"`

*Voorbeeld 2*

In dit voor beeld worden vijf seconden afgetrokken van de opgegeven tijds tempel:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

En retourneert dit resultaat: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Voeg een aantal tijds eenheden toe aan een tijds tempel.
Zie ook [getFutureTime ()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*bereik*> | Ja | Geheel getal | Het aantal opgegeven tijds eenheden dat moet worden toegevoegd |
| <*timeUnit*> | Ja | Tekenreeks | De tijds eenheid die moet worden gebruikt met het *interval*: ' seconde ', ' minuut ', ' uur ', ' dag ', ' week ', ' maand ', ' jaar ' |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS. fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tijds tempel*> | Tekenreeks | De tijds tempel plus het opgegeven aantal tijds eenheden  |
||||

*Voorbeeld 1*

In dit voor beeld wordt één dag toegevoegd aan de opgegeven tijds tempel:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

En retourneert dit resultaat: `"2018-01-02T00:00:00.0000000Z"`

*Voorbeeld 2*

In dit voor beeld wordt één dag toegevoegd aan de opgegeven tijds tempel:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

En retourneert het resultaat met de optionele D-indeling: `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>en

Controleer of alle expressies waar zijn.
Retourneert waar als alle expressies waar zijn, of retourneert onwaar als ten minste één expressie onwaar is.

```
and(<expression1>, <expression2>, ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*expressie1*>, <*Expressie2*>,... | Ja | Booleaans | De te controleren expressies |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| waar of onwaar | Booleaans | Retourneert waar als alle expressies waar zijn. Retourneert onwaar wanneer ten minste één expressie onwaar is. |
||||

*Voorbeeld 1*

In deze voor beelden wordt gecontroleerd of de opgegeven Booleaanse waarden allemaal waar zijn:

```
and(true, true)
and(false, true)
and(false, false)
```

En retourneert deze resultaten:

* Eerste voor beeld: beide expressies zijn waar, dus retourneert `true` .
* Tweede voor beeld: een expressie is onwaar, dus retourneert `false` .
* Derde voor beeld: beide expressies zijn onwaar, dus retourneert `false` .

*Voorbeeld 2*

In deze voor beelden wordt gecontroleerd of de opgegeven expressies allemaal waar zijn:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

En retourneert deze resultaten:

* Eerste voor beeld: beide expressies zijn waar, dus retourneert `true` .
* Tweede voor beeld: een expressie is onwaar, dus retourneert `false` .
* Derde voor beeld: beide expressies zijn onwaar, dus retourneert `false` .

<a name="array"></a>

### <a name="array"></a>matrix

Een matrix retour neren van een enkele opgegeven invoer.
Zie [createArray ()](#createArray)voor meerdere invoer.

```
array('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De teken reeks voor het maken van een matrix |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*waarde*>] | Matrix | Een matrix die de enkele opgegeven invoer bevat |
||||

*Voorbeeld*

In dit voor beeld wordt een matrix gemaakt op basis van de teken reeks ' Hallo ':

```
array('hello')
```

En retourneert dit resultaat: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Retourneert de met base64 gecodeerde versie voor een teken reeks.

> [!NOTE]
> Azure Logic Apps voert automatisch base64-code ring en-decodering uit, wat betekent dat u deze conversies niet hand matig hoeft uit te voeren. Als u dit wel doet, kunt u echter onverwachte weergave gedrag ondervinden die niet van invloed is op de daad werkelijke conversies, maar alleen op de manier waarop deze worden weer gegeven. Zie [impliciete gegevens type conversies](#implicit-data-conversions)voor meer informatie.

```
base64('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De invoer teken reeks |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*Base64-teken reeks*> | Tekenreeks | De met base64 gecodeerde versie voor de invoer teken reeks |
||||

*Voorbeeld*

In dit voor beeld wordt de teken reeks "Hallo" geconverteerd naar een base64-gecodeerde teken reeks:

```
base64('hello')
```

En retourneert dit resultaat: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Retourneert de binaire versie voor een base64-gecodeerde teken reeks.

> [!NOTE]
> Azure Logic Apps voert automatisch base64-code ring en-decodering uit, wat betekent dat u deze conversies niet hand matig hoeft uit te voeren. Als u dit wel doet, kunt u echter onverwachte weergave gedrag ondervinden die niet van invloed is op de daad werkelijke conversies, maar alleen op de manier waarop deze worden weer gegeven. Zie [impliciete gegevens type conversies](#implicit-data-conversions)voor meer informatie.

```
base64ToBinary('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De base64-gecodeerde teken reeks die moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*binary-for-base64-String*> | Tekenreeks | De binaire versie voor de met base64 gecodeerde teken reeks |
||||

*Voorbeeld*

In dit voor beeld wordt de met base64 gecodeerde teken reeks "aGVsbG8 =" geconverteerd naar een binaire teken reeks:

```
base64ToBinary('aGVsbG8=')
```

En retourneert dit resultaat:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Retourneert de teken reeks versie voor een base64-gecodeerde teken reeks, waardoor de base64-teken reeks effectief wordt gedecodeerd. Gebruik deze functie in plaats van [decodeBase64 ()](#decodeBase64), die is afgeschaft.

> [!NOTE]
> Azure Logic Apps voert automatisch base64-code ring en-decodering uit, wat betekent dat u deze conversies niet hand matig hoeft uit te voeren. Als u dit wel doet, kunt u echter onverwachte weergave gedrag ondervinden die niet van invloed is op de daad werkelijke conversies, maar alleen op de manier waarop deze worden weer gegeven. Zie [impliciete gegevens type conversies](#implicit-data-conversions)voor meer informatie.

```
base64ToString('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De met base64 gecodeerde teken reeks die moet worden gedecodeerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*gedecodeerd-base64-teken reeks*> | Tekenreeks | De teken reeks versie voor een base64-gecodeerde teken reeks |
||||

*Voorbeeld*

In dit voor beeld wordt de met base64 gecodeerde teken reeks "aGVsbG8 =" geconverteerd naar alleen een teken reeks:

```
base64ToString('aGVsbG8=')
```

En retourneert dit resultaat: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binair

Retourneert de binaire versie voor een teken reeks.

```
binary('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De teken reeks die moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*binary-for-input-waarde*> | Tekenreeks | De binaire versie voor de opgegeven teken reeks |
||||

*Voorbeeld*

In dit voor beeld wordt de teken reeks "Hallo" geconverteerd naar een binaire teken reeks:

```
binary('hello')
```

En retourneert dit resultaat:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>body

De uitvoer van een actie `body` tijdens runtime retour neren.
Steno voor `actions('<actionName>').outputs.body` .
Zie [actionBody ()](#actionBody) en [Actions ()](#actions).

```
body('<actionName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De naam voor de uitvoer van de actie `body` die u wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| <*actie-hoofd tekst-uitvoer*> | Tekenreeks | De `body` uitvoer van de opgegeven actie |
||||

*Voorbeeld*

In dit voor beeld wordt de `body` uitvoer van de `Get user` Twitter-actie opgehaald:

```
body('Get_user')
```

En retourneert dit resultaat:

```json
"body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="bool"></a>

### <a name="bool"></a>booleaans

Retourneert de Booleaanse versie van een waarde.

```
bool(<value>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Alle | De waarde die moet worden geconverteerd naar Booleaans. |
|||||

Als u `bool()` met een-object werkt, moet de waarde van het object een teken reeks of een geheel getal zijn dat kan worden geconverteerd naar een Boolean.

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| `true` of `false` | Booleaans | De Booleaanse versie van de opgegeven waarde. |
||||

*Uitvoerwaarden*

In deze voor beelden ziet u de verschillende typen invoer die worden ondersteund voor `bool()` :

| Invoerwaarde | Type | Retourwaarde |
| ----------- | ---------- | ---------------------- |
| `bool(1)` | Geheel getal | `true` |
| `bool(0)` | Geheel getal    | `false` |
| `bool(-1)` | Geheel getal | `true` |
| `bool('true')` | Tekenreeks | `true` |
| `bool('false')` | Tekenreeks | `false` |

<a name="coalesce"></a>

### <a name="coalesce"></a>Voeg

Retourneert de eerste waarde die niet null is van een of meer para meters.
Lege teken reeksen, lege matrices en lege objecten zijn niet null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>,... | Ja | Any, kan typen combi neren | Een of meer items die moeten worden gecontroleerd op null |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*eerste niet-null-item*> | Alle | Het eerste item of de waarde die niet null is. Als alle para meters null zijn, retourneert deze functie null. |
||||

*Voorbeeld*

In deze voor beelden wordt de eerste niet-null-waarde uit de opgegeven waarden geretourneerd, of NULL wanneer alle waarden null zijn:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

En retourneert deze resultaten:

* Eerste voor beeld: `true`
* Tweede voor beeld: `"hello"`
* Derde voor beeld: `null`

<a name="concat"></a>

### <a name="concat"></a>concat

Combi neer twee of meer teken reeksen en retour neer de gecombineerde teken reeks.

```
concat('<text1>', '<text2>', ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*Tekst2*>,... | Ja | Tekenreeks | Ten minste twee teken reeksen om te combi neren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*text1text2...*> | Tekenreeks | De teken reeks die is gemaakt op basis van de gecombineerde invoer teken reeksen |
||||

*Voorbeeld*

In dit voor beeld worden de teken reeksen "Hallo" en "wereld" gecombineerd:

```
concat('Hello', 'World')
```

En retourneert dit resultaat: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>bevat

Controleer of een verzameling een specifiek item heeft.
Retourneert waar wanneer het item is gevonden, of retourneert onwaar als het niet is gevonden.
Deze functie is hoofdletter gevoelig.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Deze functie werkt met name voor deze typen verzamelingen:

* Een *teken reeks* om een *subtekenreeks* te vinden
* Een *matrix* om een *waarde* te zoeken
* Een *woorden lijst* om een *sleutel* te vinden

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*innings*> | Ja | Teken reeks, matrix of woorden lijst | De verzameling die moet worden gecontroleerd |
| <*Value*> | Ja | Respectievelijk een teken reeks, matrix of woorden lijst | Het item dat u wilt zoeken |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | Retourneert waar wanneer het item is gevonden. Retourneert onwaar wanneer deze niet is gevonden. |
||||

*Voorbeeld 1*

In dit voor beeld wordt de teken reeks "Hallo wereld" voor de subtekenreeks "wereld" gecontroleerd en wordt waar geretourneerd:

```
contains('hello world', 'world')
```

*Voorbeeld 2*

In dit voor beeld wordt de teken reeks "Hallo wereld" voor de subtekenreeks "universum" gecontroleerd en wordt false geretourneerd:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Converteer een tijds tempel van Universal Time Coordinated (UTC) naar de doel tijdzone.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*destinationTimeZone*> | Ja | Tekenreeks | De naam voor de tijd zone van het doel. Zie de [micro soft time zone-index waarden](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)voor tijdzone namen, maar u moet mogelijk alle Lees tekens uit de naam van de tijd zone verwijderen. |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS. fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*geconverteerd: tijds tempel*> | Tekenreeks | De tijds tempel die is geconverteerd naar de doel tijdzone |
||||

*Voorbeeld 1*

In dit voor beeld wordt een tijds tempel geconverteerd naar de opgegeven tijd zone:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

En retourneert dit resultaat: `"2018-01-01T00:00:00.0000000"`

*Voorbeeld 2*

In dit voor beeld wordt een tijds tempel geconverteerd naar de opgegeven tijd zone en notatie:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

En retourneert dit resultaat: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Converteer een tijds tempel van de bron tijdzone naar de doel tijdzone.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*sourceTimeZone*> | Ja | Tekenreeks | De naam voor de bron tijdzone. Zie de [micro soft time zone-index waarden](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)voor tijdzone namen, maar u moet mogelijk alle Lees tekens uit de naam van de tijd zone verwijderen. |
| <*destinationTimeZone*> | Ja | Tekenreeks | De naam voor de tijd zone van het doel. Zie de [micro soft time zone-index waarden](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)voor tijdzone namen, maar u moet mogelijk alle Lees tekens uit de naam van de tijd zone verwijderen. |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS. fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*geconverteerd: tijds tempel*> | Tekenreeks | De tijds tempel die is geconverteerd naar de doel tijdzone |
||||

*Voorbeeld 1*

In dit voor beeld wordt de bron tijdzone geconverteerd naar de doel tijdzone:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

En retourneert dit resultaat: `"2018-01-01T00:00:00.0000000"`

*Voorbeeld 2*

In dit voor beeld wordt een tijd zone geconverteerd naar de opgegeven tijd zone en notatie:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

En retourneert dit resultaat: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Een tijds tempel van de bron tijdzone converteren naar Universal Time Coordinated (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*sourceTimeZone*> | Ja | Tekenreeks | De naam voor de bron tijdzone. Zie de [micro soft time zone-index waarden](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)voor tijdzone namen, maar u moet mogelijk alle Lees tekens uit de naam van de tijd zone verwijderen. |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS. fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*geconverteerd: tijds tempel*> | Tekenreeks | De tijds tempel die is geconverteerd naar UTC |
||||

*Voorbeeld 1*

In dit voor beeld wordt een tijds tempel geconverteerd naar UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

En retourneert dit resultaat: `"2018-01-01T08:00:00.0000000Z"`

*Voorbeeld 2*

In dit voor beeld wordt een tijds tempel geconverteerd naar UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

En retourneert dit resultaat: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Een matrix van meerdere invoer waarden retour neren.
Zie [matrix ()](#array)voor één invoer matrix.

```
createArray('<object1>', '<object2>', ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>,... | Ja | Any, maar niet gemengd | Ten minste twee items om de matrix te maken |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>,...] | Matrix | De matrix die is gemaakt op basis van alle invoer items |
||||

*Voorbeeld*

In dit voor beeld wordt een matrix gemaakt op basis van deze invoer:

```
createArray('h', 'e', 'l', 'l', 'o')
```

En retourneert dit resultaat: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Een gegevens-URI (Uniform Resource Identifier) retour neren voor een teken reeks.

```
dataUri('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De teken reeks die moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*gegevens-URI*> | Tekenreeks | De gegevens-URI voor de invoer teken reeks |
||||

*Voorbeeld*

In dit voor beeld wordt een gegevens-URI gemaakt voor de teken reeks "Hallo":

```
dataUri('hello')
```

En retourneert dit resultaat: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Retourneert de binaire versie voor een gegevens-URI (Uniform Resource Identifier).
Gebruik deze functie in plaats van [decodeDataUri ()](#decodeDataUri).
Hoewel beide functies op dezelfde manier werken, verdient de `dataUriBinary()` voor keur.

```
dataUriToBinary('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De gegevens-URI die moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*binary-for-data-URI*> | Tekenreeks | De binaire versie voor de gegevens-URI |
||||

*Voorbeeld*

In dit voor beeld wordt een binaire versie gemaakt voor deze gegevens-URI:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

En retourneert dit resultaat:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Retourneert de versie van de teken reeks voor een gegevens-URI (Uniform Resource Identifier).

```
dataUriToString('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De gegevens-URI die moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*string-for-data-URI*> | Tekenreeks | De teken reeks versie voor de gegevens-URI |
||||

*Voorbeeld*

In dit voor beeld wordt een teken reeks gemaakt voor deze gegevens-URI:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

En retourneert dit resultaat: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Retourneert de dag van de maand van een tijds tempel.

```
dayOfMonth('<timestamp>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*dag van de maand*> | Geheel getal | De dag van de maand van de opgegeven tijds tempel |
||||

*Voorbeeld*

In dit voor beeld wordt het getal voor de dag van de maand uit deze tijds tempel geretourneerd:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

En retourneert dit resultaat: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Retourneert de dag van de week van een tijds tempel.

```
dayOfWeek('<timestamp>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*dag van de week*> | Geheel getal | De dag van de week vanaf de opgegeven tijds tempel waarbij zondag 0 is, maandag 1, enzovoort |
||||

*Voorbeeld*

In dit voor beeld wordt het getal voor de dag van de week uit deze tijds tempel geretourneerd:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

En retourneert dit resultaat: `4`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Retourneert de dag van het jaar van een tijds tempel.

```
dayOfYear('<timestamp>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*dag van jaar*> | Geheel getal | De dag van het jaar van de opgegeven tijds tempel |
||||

*Voorbeeld*

In dit voor beeld wordt het nummer van de dag van het jaar uit deze tijds tempel geretourneerd:

```
dayOfYear('2018-03-15T13:27:36Z')
```

En retourneert dit resultaat: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64-deprecated"></a>decodeBase64 (afgeschaft)

Deze functie is afgeschaft. gebruik in plaats daarvan [base64ToString ()](#base64ToString) .

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Retourneert de binaire versie voor een gegevens-URI (Uniform Resource Identifier). Overweeg het gebruik van [dataUriToBinary ()](#dataUriToBinary)in plaats van `decodeDataUri()` . Hoewel beide functies op dezelfde manier werken, verdient de `dataUriToBinary()` voor keur.

> [!NOTE]
> Azure Logic Apps voert automatisch base64-code ring en-decodering uit, wat betekent dat u deze conversies niet hand matig hoeft uit te voeren. Als u dit wel doet, kunt u echter onverwachte weergave gedrag ondervinden die niet van invloed is op de daad werkelijke conversies, maar alleen op de manier waarop deze worden weer gegeven. Zie [impliciete gegevens type conversies](#implicit-data-conversions)voor meer informatie.

```
decodeDataUri('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De gegevens-URI-teken reeks die moet worden gedecodeerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*binary-for-data-URI*> | Tekenreeks | De binaire versie voor een gegevens-URI-teken reeks |
||||

*Voorbeeld*

In dit voor beeld wordt de binaire versie van deze gegevens-URI geretourneerd:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

En retourneert dit resultaat:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Retourneert een teken reeks waarmee escape tekens worden vervangen door gedecodeerde versies.

```
decodeUriComponent('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De teken reeks met de escape-tekens die moeten worden gedecodeerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*gedecodeerde URI*> | Tekenreeks | De bijgewerkte teken reeks met de gecodeerde escape tekens |
||||

*Voorbeeld*

In dit voor beeld worden de escape-tekens in deze teken reeks vervangen door gedecodeerde versies:

```
decodeUriComponent('https%3A%2F%2Fcontoso.com')
```

En retourneert dit resultaat: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Retourneert het resultaat van het delen van twee getallen. Zie [mod ()](#mod)om het rest resultaat op te halen.

```
div(<dividend>, <divisor>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | Ja | Geheel getal of zwevend | Het getal dat moet worden gedeeld door de *deler* |
| <*reken*> | Ja | Geheel getal of zwevend | Het getal dat het *deelt* deel, maar mag niet 0 zijn |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*quotiënt-resultaat*> | Geheel getal of zwevend | Het resultaat van het delen van het eerste getal met het tweede getal. Als het dividend of de deler een float-type heeft, heeft het resultaat float-type. <p><p>**Opmerking**: als u het float-resultaat wilt converteren naar een geheel getal, probeert u [een functie in azure te maken en](../logic-apps/logic-apps-azure-functions.md) aan te roepen vanuit uw logische app. |
||||

*Voorbeeld 1*

Beide voor beelden retour neren deze waarde met het type geheel getal: `2`

```
div(10,5)
div(11,5)
```

*Voorbeeld 2*

Beide voor beelden retour neren deze waarde met float-type: `2.2`

```
div(11,5.0)
div(11.0,5)
```

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Een gecodeerde URI-versie (Uniform Resource Identifier) retour neren voor een teken reeks door onveilige URL-tekens te vervangen door Escape tekens. Overweeg het gebruik van [uriComponent ()](#uriComponent)in plaats van `encodeUriComponent()` . Hoewel beide functies op dezelfde manier werken, verdient de `uriComponent()` voor keur.

> [!NOTE]
> Azure Logic Apps voert automatisch base64-code ring en-decodering uit, wat betekent dat u deze conversies niet hand matig hoeft uit te voeren. Als u dit wel doet, kunt u echter onverwachte weergave gedrag ondervinden die niet van invloed is op de daad werkelijke conversies, maar alleen op de manier waarop deze worden weer gegeven. Zie [impliciete gegevens type conversies](#implicit-data-conversions)voor meer informatie.

```
encodeUriComponent('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De teken reeks die moet worden geconverteerd naar een URI-gecodeerde indeling |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*gecodeerde URI*> | Tekenreeks | De teken reeks met URI-code ring met escape tekens |
||||

*Voorbeeld*

In dit voor beeld wordt een met URI gecodeerde versie gemaakt voor deze teken reeks:

```
encodeUriComponent('https://contoso.com')
```

En retourneert dit resultaat: `"https%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>leeg

Controleer of een verzameling leeg is.
Retourneert waar als de verzameling leeg is of retourneert onwaar als deze niet leeg is.

```
empty('<collection>')
empty([<collection>])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*innings*> | Ja | Teken reeks, matrix of object | De verzameling die moet worden gecontroleerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | Retourneert waar als de verzameling leeg is. Retourneert onwaar wanneer niet leeg is. |
||||

*Voorbeeld*

In deze voor beelden wordt gecontroleerd of de opgegeven verzamelingen leeg zijn:

```
empty('')
empty('abc')
```

En retourneert deze resultaten:

* Eerste voor beeld: Hiermee wordt een lege teken reeks door gegeven, waardoor de functie wordt geretourneerd `true` .
* Tweede voor beeld: de teken reeks "ABC" wordt door gegeven, dus de functie retourneert `false` .

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Controleren of een teken reeks eindigt met een specifieke subtekenreeks.
Retourneert waar als de subtekenreeks wordt gevonden, of retourneert False als het niet is gevonden.
Deze functie is niet hoofdletter gevoelig.

```
endsWith('<text>', '<searchText>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*SMS*> | Ja | Tekenreeks | De teken reeks die moet worden gecontroleerd |
| <*Tekst*> | Ja | Tekenreeks | De laatste subtekenreeks die moet worden gezocht |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar  | Booleaans | Retourneert waar als de laatste subtekenreeks wordt gevonden. Retourneert onwaar wanneer deze niet is gevonden. |
||||

*Voorbeeld 1*

In dit voor beeld wordt gecontroleerd of de teken reeks "Hallo wereld" eindigt op de "wereld"-teken reeks:

```
endsWith('hello world', 'world')
```

En retourneert dit resultaat: `true`

*Voorbeeld 2*

In dit voor beeld wordt gecontroleerd of de teken reeks ' Hallo wereld ' eindigt met de teken reeks ' universum ':

```
endsWith('hello world', 'universe')
```

En retourneert dit resultaat: `false`

<a name="equals"></a>

### <a name="equals"></a>is gelijk aan

Controleer of beide waarden, expressies of objecten gelijkwaardig zijn.
Retourneert waar als beide gelijkwaardig zijn, of retourneert onwaar als deze niet overeenkomen.

```
equals('<object1>', '<object2>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | Ja | Sommige | De waarden, expressies of objecten die u wilt vergelijken |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | Retourneert waar als beide gelijkwaardig zijn. Retourneert onwaar als dat niet het equivalent is. |
||||

*Voorbeeld*

In deze voor beelden wordt gecontroleerd of de opgegeven invoer gelijkwaardig zijn.

```
equals(true, 1)
equals('abc', 'abcd')
```

En retourneert deze resultaten:

* Eerste voor beeld: beide waarden zijn gelijkwaardig, waardoor de functie wordt geretourneerd `true` .
* Tweede voor beeld: beide waarden zijn niet gelijk aan, dus retourneert de functie `false` .

<a name="first"></a>

### <a name="first"></a>instantie

Het eerste item van een teken reeks of matrix retour neren.

```
first('<collection>')
first([<collection>])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*innings*> | Ja | Teken reeks of matrix | De verzameling waar het eerste item moet worden gevonden |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*eerste verzameling-item*> | Alle | Het eerste item in de verzameling |
||||

*Voorbeeld*

In deze voor beelden vindt u het eerste item in deze verzamelingen:

```
first('hello')
first(createArray(0, 1, 2))
```

En retour neren deze resultaten:

* Eerste voor beeld: `"h"`
* Tweede voor beeld: `0`

<a name="float"></a>

### <a name="float"></a>float

Een teken reeks versie voor een drijvende-komma getal converteren naar een werkelijk drijvende-komma getal.
U kunt deze functie alleen gebruiken bij het door geven van aangepaste para meters aan een app, bijvoorbeeld een logische app of stroom.

```
float('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De teken reeks met een geldig drijvende-komma getal dat moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*float-waarde*> | Float | Het drijvende-komma getal voor de opgegeven teken reeks |
||||

*Voorbeeld*

In dit voor beeld wordt een teken reeks versie gemaakt voor dit getal met drijvende komma:

```
float('10.333')
```

En retourneert dit resultaat: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Een tijds tempel retour neren in de opgegeven notatie.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS. fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*opnieuw geformatteerd-tijds tempel*> | Tekenreeks | De bijgewerkte tijds tempel in de opgegeven indeling |
||||

*Voorbeeld*

In dit voor beeld wordt een tijds tempel geconverteerd naar de opgegeven notatie:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

En retourneert dit resultaat: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

Retour neer een matrix met waarden die overeenkomen met een sleutel naam in een actie *formulier gegevens* of *formulier-gecodeerde* uitvoer.

```
formDataMultiValues('<actionName>', '<key>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De actie waarvan de uitvoer de gewenste sleutel waarde bevat |
| <*prestatie*> | Ja | Tekenreeks | De naam van de sleutel waarvan u de waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*matrix-met-sleutel waarden*>] | Matrix | Een matrix met alle waarden die overeenkomen met de opgegeven sleutel |
||||

*Voorbeeld*

In dit voor beeld wordt een matrix gemaakt op basis van de waarde van de sleutel subject in de opgegeven actie formulier gegevens of door formulieren gecodeerde uitvoer:

```
formDataMultiValues('Send_an_email', 'Subject')
```

En retourneert de onderwerps tekst in een matrix, bijvoorbeeld: `["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

Eén waarde Retour neren die overeenkomt met een sleutel naam in een actie *formulier gegevens* of *formulier-gecodeerde* uitvoer.
Als de functie meer dan één overeenkomst vindt, genereert de functie een fout.

```
formDataValue('<actionName>', '<key>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De actie waarvan de uitvoer de gewenste sleutel waarde bevat |
| <*prestatie*> | Ja | Tekenreeks | De naam van de sleutel waarvan u de waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*sleutel waarde*> | Tekenreeks | De waarde in de opgegeven sleutel  |
||||

*Voorbeeld*

In dit voor beeld wordt een teken reeks gemaakt op basis van de waarde van de sleutel subject in de opgegeven actie formulier gegevens of door formulieren gecodeerde uitvoer:

```
formDataValue('Send_an_email', 'Subject')
```

En retourneert de onderwerpnaam als een teken reeks, bijvoorbeeld: `"Hello world"`

<a name="formatNumber"></a>

### <a name="formatnumber"></a>formatNumber

Retourneert een getal als een teken reeks die is gebaseerd op de opgegeven indeling.

```text
formatNumber(<number>, <format>, <locale>?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Telwoord*> | Ja | Geheel getal of dubbele waarde | De waarde die u wilt opmaken. |
| <*Formatteer*> | Ja | Tekenreeks | Een samengestelde indelings teken reeks waarmee de indeling wordt opgegeven die u wilt gebruiken. Zie voor de ondersteunde teken reeksen voor numerieke notatie [standaard numerieke teken reeksen](/dotnet/standard/base-types/standard-numeric-format-strings), die worden ondersteund door `number.ToString(<format>, <locale>)` . |
| <*instelling*> | Nee | Tekenreeks | De land instelling die moet worden gebruikt als ondersteund door `number.ToString(<format>, <locale>)` . Als dat niet is opgegeven, is de standaard waarde `en-us` . |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*opgemaakt-nummer*> | Tekenreeks | Het opgegeven getal als een teken reeks in de indeling die u hebt opgegeven. U kunt deze retour waarde casten naar een `int` of `float` . |
||||

*Voorbeeld 1*

Stel dat u het nummer wilt opmaken `1234567890` . In dit voor beeld wordt dat getal opgemaakt als de teken reeks "1.234.567.890,00".

```
formatNumber(1234567890, '0,0.00', 'en-us')
```

* Voor beeld 2 "

Stel dat u het nummer wilt opmaken `1234567890` . In dit voor beeld wordt het getal opgemaakt als de teken reeks "1.234.567.890, 00".

```
formatNumber(1234567890, '0,0.00', 'is-is')
```

*Voorbeeld 3*

Stel dat u het nummer wilt opmaken `17.35` . In dit voor beeld wordt het getal opgemaakt op de teken reeks "$17,35".

```
formatNumber(17.35, 'C2')
```

*Voorbeeld 4*

Stel dat u het nummer wilt opmaken `17.35` . In dit voor beeld wordt het getal op de teken reeks "17, 35 KR" opgemaakt.

```
formatNumber(17.35, 'C2', 'is-is')
```

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

De huidige tijds tempel en de opgegeven tijds eenheden retour neren.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*bereik*> | Ja | Geheel getal | Het aantal opgegeven tijds eenheden dat moet worden toegevoegd |
| <*timeUnit*> | Ja | Tekenreeks | De tijds eenheid die moet worden gebruikt met het *interval*: ' seconde ', ' minuut ', ' uur ', ' dag ', ' week ', ' maand ', ' jaar ' |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS. fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tijds tempel*> | Tekenreeks | De huidige tijds tempel plus het opgegeven aantal tijds eenheden |
||||

*Voorbeeld 1*

Stel dat het huidige tijds tempel ' 2018-03-01T00:00:00.0000000 Z ' is.
In dit voor beeld worden vijf dagen toegevoegd aan die tijds tempel:

```
getFutureTime(5, 'Day')
```

En retourneert dit resultaat: `"2018-03-06T00:00:00.0000000Z"`

*Voorbeeld 2*

Stel dat het huidige tijds tempel ' 2018-03-01T00:00:00.0000000 Z ' is.
In dit voor beeld worden vijf dagen toegevoegd en wordt het resultaat geconverteerd naar D-indeling:

```
getFutureTime(5, 'Day', 'D')
```

En retourneert dit resultaat: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Retourneert de huidige tijds tempel min de opgegeven tijds eenheden.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*bereik*> | Ja | Geheel getal | Het aantal opgegeven tijds eenheden dat moet worden afgetrokken |
| <*timeUnit*> | Ja | Tekenreeks | De tijds eenheid die moet worden gebruikt met het *interval*: ' seconde ', ' minuut ', ' uur ', ' dag ', ' week ', ' maand ', ' jaar ' |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS. fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tijds tempel*> | Tekenreeks | De huidige tijds tempel min het opgegeven aantal tijds eenheden |
||||

*Voorbeeld 1*

Stel dat het huidige tijds tempel ' 2018-02-01T00:00:00.0000000 Z ' is.
In dit voor beeld worden vijf dagen afgetrokken van die tijds tempel:

```
getPastTime(5, 'Day')
```

En retourneert dit resultaat: `"2018-01-27T00:00:00.0000000Z"`

*Voorbeeld 2*

Stel dat het huidige tijds tempel ' 2018-02-01T00:00:00.0000000 Z ' is.
In dit voor beeld worden vijf dagen afgetrokken en wordt het resultaat geconverteerd naar D-indeling:

```
getPastTime(5, 'Day', 'D')
```

En retourneert dit resultaat: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Controleer of de eerste waarde groter is dan de tweede waarde.
Retourneert waar als de eerste waarde meer is of retourneert onwaar wanneer er minder wordt geretourneerd.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Geheel getal, float of teken reeks | De eerste waarde die moet worden gecontroleerd of deze hoger is dan de tweede waarde |
| <*compareTo*> | Ja | Respectievelijk geheel getal, float of teken reeks | De vergelijkings waarde |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | Retourneert waar als de eerste waarde groter is dan de tweede waarde. Retourneert onwaar als de eerste waarde gelijk is aan of kleiner is dan de tweede waarde. |
||||

*Voorbeeld*

Deze voor beelden controleren of de eerste waarde groter is dan de tweede waarde:

```
greater(10, 5)
greater('apple', 'banana')
```

En retour neren deze resultaten:

* Eerste voor beeld: `true`
* Tweede voor beeld: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Controleer of de eerste waarde groter is dan of gelijk is aan de tweede waarde.
Retourneert waar als de eerste waarde groter of gelijk is, of retourneert onwaar als de eerste waarde kleiner is.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Geheel getal, float of teken reeks | De eerste waarde om te controleren of deze groter dan of gelijk is aan de tweede waarde |
| <*compareTo*> | Ja | Respectievelijk geheel getal, float of teken reeks | De vergelijkings waarde |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | Retourneert waar als de eerste waarde groter is dan of gelijk is aan de tweede waarde. Retourneert onwaar als de eerste waarde lager is dan de tweede waarde. |
||||

*Voorbeeld*

In deze voor beelden wordt gecontroleerd of de eerste waarde groter is dan of gelijk is aan de tweede waarde:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

En retour neren deze resultaten:

* Eerste voor beeld: `true`
* Tweede voor beeld: `false`

<a name="guid"></a>

### <a name="guid"></a>guid

Genereer een Globally Unique Identifier (GUID) als een teken reeks, bijvoorbeeld ' c2ecc88d-88c8-4096-912c-d6f2e2b138ce ':

```
guid()
```

U kunt ook een andere notatie voor de GUID opgeven dan de standaard indeling, D, die bestaat uit 32 cijfers, gescheiden door afbreek streepjes.

```
guid('<format>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Formatteer*> | Nee | Tekenreeks | Een enkele [indelings aanduiding](/dotnet/api/system.guid.tostring#system_guid_tostring_system_string_) voor de geretourneerde GUID. De notatie is standaard ingesteld op D, maar u kunt N, D, B, P of X gebruiken ("nb"). |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*GUID-waarde*> | Tekenreeks | Een wille keurig gegenereerde GUID |
||||

*Voorbeeld*

In dit voor beeld wordt dezelfde GUID gegenereerd, maar als 32 cijfers, gescheiden door afbreek streepjes en tussen haakjes:

```
guid('P')
```

En retourneert dit resultaat: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Controleer of een expressie True of False is. Retour neer een opgegeven waarde op basis van het resultaat. Para meters worden van links naar rechts geëvalueerd.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*expressie*> | Ja | Booleaans | De expressie die moet worden gecontroleerd |
| <*valueIfTrue*> | Ja | Alle | De waarde die moet worden geretourneerd wanneer de expressie waar is |
| <*valueIfFalse*> | Ja | Alle | De waarde die moet worden geretourneerd wanneer de expressie onwaar is |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*opgegeven-retourneert-waarde*> | Alle | De opgegeven waarde die wordt geretourneerd op basis van het feit of de expressie waar of onwaar is |
||||

*Voorbeeld*

In dit voor beeld wordt geretourneerd `"yes"` omdat de opgegeven expressie True retourneert.
Anders wordt het volgende geretourneerd `"no"` :

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Retourneert de start positie of index waarde voor een subtekenreeks.
Deze functie is niet hoofdletter gevoelig en indexen beginnen met het cijfer 0.

```
indexOf('<text>', '<searchText>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*SMS*> | Ja | Tekenreeks | De teken reeks die de subtekenreeks bevat die moet worden gezocht |
| <*Tekst*> | Ja | Tekenreeks | De subtekenreeks die u wilt zoeken |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*index-waarde*>| Geheel getal | De start positie of index waarde voor de opgegeven subtekenreeks. <p>Als de teken reeks niet wordt gevonden, retourneert u het getal-1. |
||||

*Voorbeeld*

In dit voor beeld wordt gezocht naar de start index-waarde voor de subtekenreeks ' World ' in de teken reeks ' Hallo wereld ':

```
indexOf('hello world', 'world')
```

En retourneert dit resultaat: `6`

<a name="int"></a>

### <a name="int"></a>int

Retourneert de versie met gehele getallen voor een teken reeks.

```
int('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De teken reeks die moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*geheel getal-resultaat*> | Geheel getal | De versie van het gehele getal voor de opgegeven teken reeks |
||||

*Voorbeeld*

In dit voor beeld wordt een versie met gehele getallen gemaakt voor de teken reeks "10":

```
int('10')
```

En retourneert dit resultaat: `10`

<a name="item"></a>

### <a name="item"></a>item

Wanneer u een matrix gebruikt binnen een herhalende actie, retourneert u het huidige item in de matrix tijdens de huidige iteratie van de actie.
U kunt ook de waarden van de eigenschappen van dat item ophalen.

```
item()
```

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*huidig-matrix-item*> | Alle | Het huidige item in de matrix voor de huidige herhaling van de actie |
||||

*Voorbeeld*

In dit voor beeld wordt het `body` element van het huidige bericht opgehaald voor de actie ' Send_an_email ' binnen de huidige herhaling van elke lus:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>vermeldingen

Het huidige item van elke cyclus in een for-each-lus retour neren.
Gebruik deze functie binnen de for-each-lus.

```
items('<loopName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*lusinstructie*> | Ja | Tekenreeks | De naam voor de for-each-lus |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*item*> | Alle | Het item uit de huidige cyclus in de opgegeven for-each-lus |
||||

*Voorbeeld*

In dit voor beeld wordt het huidige item opgehaald van de opgegeven for-each-lus:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterationIndexes

Retourneert de index waarde voor de huidige iteratie binnen een lus until. U kunt deze functie binnen nesten tot lussen gebruiken. 

```
iterationIndexes('<loopName>')
```

| Parameter | Vereist | Type | Beschrijving | 
| --------- | -------- | ---- | ----------- | 
| <*lusinstructie*> | Ja | Tekenreeks | De naam voor de lus until | 
||||| 

| Retourwaarde | Type | Beschrijving | 
| ------------ | ---- | ----------- | 
| <*TabIndex*> | Geheel getal | De index waarde voor de huidige iteratie binnen de opgegeven lus until | 
|||| 

*Voorbeeld* 

In dit voor beeld wordt een item variabele gemaakt en deze variabele wordt tijdens elke iteratie in een lus until verhoogd tot de waarde van de teller vijf heeft bereikt. In het voor beeld wordt ook een variabele gemaakt die de huidige index voor elke herhaling bijhoudt. In de lus until, tijdens elke iteratie, wordt het prestatie meter item verhoogd en wordt vervolgens de item waarde toegewezen aan de huidige index waarde en wordt de teller vervolgens verhoogd. In dit voor beeld verwijst in de lus naar de huidige iteratie index met behulp van de `iterationIndexes` functie:

`iterationIndexes('Until_Max_Increment')`

```json
{
   "actions": {
      "Create_counter_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [ 
               {
                  "name": "myCounter",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {}
      },
      "Create_current_index_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [
               {
                  "name": "myCurrentLoopIndex",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {
            "Create_counter_variable": [ "Succeeded" ]
         }
      },
      "Until_Max_Increment": {
         "type": "Until",
         "actions": {
            "Assign_current_index_to_counter": {
               "type": "SetVariable",
               "inputs": {
                  "name": "myCurrentLoopIndex",
                  "value": "@variables('myCounter')"
               },
               "runAfter": {
                  "Increment_variable": [ "Succeeded" ]
               }
            },
            "Compose": {
               "inputs": "'Current index: ' @{iterationIndexes('Until_Max_Increment')}",
               "runAfter": {
                  "Assign_current_index_to_counter": [
                     "Succeeded"
                    ]
                },
                "type": "Compose"
            },           
            "Increment_variable": {
               "type": "IncrementVariable",
               "inputs": {
                  "name": "myCounter",
                  "value": 1
               },
               "runAfter": {}
            }
         },
         "expression": "@equals(variables('myCounter'), 5)",
         "limit": {
            "count": 60,
            "timeout": "PT1H"
         },
         "runAfter": {
            "Create_current_index_variable": [ "Succeeded" ]
         }
      }
   }
}
```

<a name="json"></a>

### <a name="json"></a>json

De waarde, het object of de matrix van objecten van het type JavaScript Object Notation (JSON) retour neren voor een teken reeks of XML.

```
json('<value>')
json(xml('value'))
```

> [!IMPORTANT]
> Zonder een XML-schema dat de structuur van de uitvoer definieert, kan de functie resultaten retour neren waarbij de structuur sterk verschilt van de verwachte indeling, afhankelijk van de invoer.
>  
> Dit gedrag zorgt ervoor dat deze functie niet geschikt is voor scenario's waarbij de uitvoer moet voldoen aan een goed gedefinieerd contract, bijvoorbeeld in essentiële bedrijfs systemen of oplossingen.

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Teken reeks of XML | De teken reeks of XML die moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*JSON-resultaat*> | Systeem eigen JSON-type,-object of-matrix | De waarde van het systeem eigen JSON-type, object of matrix van objecten uit de invoer teken reeks of XML. <p><p>-Als u een XML-bestand met één onderliggend element in het hoofd element doorgeeft, retourneert de functie één JSON-object voor dat onderliggende element. <p> -Als u een XML-bestand met meerdere onderliggende elementen in het hoofd element doorgeeft, retourneert de functie een matrix die JSON-objecten voor die onderliggende elementen bevat. <p>-Als de teken reeks null is, retourneert de functie een leeg object. |
||||

*Voorbeeld 1*

In dit voor beeld wordt deze teken reeks geconverteerd naar een JSON-waarde:

```
json('[1, 2, 3]')
```

En retourneert dit resultaat: `[1, 2, 3]`

*Voorbeeld 2*

In dit voor beeld wordt deze teken reeks geconverteerd naar JSON:

```
json('{"fullName": "Sophia Owen"}')
```

En retourneert dit resultaat:

```json
{
  "fullName": "Sophia Owen"
}
```

*Voorbeeld 3*

In dit voor beeld worden de `json()` functies en gebruikt `xml()` voor het converteren van XML met één onderliggend element in het hoofd element in een JSON-object met de naam `person` voor dat onderliggende element:

`json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))`

En retourneert dit resultaat:

```json
{
   "?xml": { 
      "@version": "1.0" 
   },
   "root": {
      "person": {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      }
   }
}
```

*Voorbeeld 4*

In dit voor beeld worden de `json()` functies en gebruikt `xml()` voor het converteren van XML met meerdere onderliggende elementen in het hoofd element in een matrix met de naam `person` die JSON-objecten voor die onderliggende elementen bevat:

`json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> <person id='2'> <name>John Doe</name> <occupation>Engineer</occupation> </person> </root>'))`

En retourneert dit resultaat:

```json
{
   "?xml": {
      "@version": "1.0"
   },
   "root": {
      "person": [
         {
            "@id": "1",
            "name": "Sophia Owen",
            "occupation": "Engineer"
         },
         {
            "@id": "2",
            "name": "John Doe",
            "occupation": "Engineer"
         }
      ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>Snij punt

Een verzameling retour neren die *alleen* de gemeen schappelijke items in de opgegeven verzamelingen heeft.
Om weer te geven in het resultaat moet een item worden weer gegeven in alle verzamelingen die aan deze functie worden door gegeven.
Als een of meer items dezelfde naam hebben, wordt het laatste item met die naam weer gegeven in het resultaat.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*verzameling1*>, <*Collection2*>,... | Ja | Matrix of object, maar niet beide | De verzamelingen van waaruit u *alleen* de algemene items wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*algemeen: items*> | Respectievelijk matrix of object | Een verzameling met alleen de gemeen schappelijke items in de opgegeven verzamelingen |
||||

*Voorbeeld*

In dit voor beeld vindt u de algemene items voor deze matrices:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

En retourneert alleen een matrix met *alleen* deze items: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Retourneert een teken reeks die alle items uit een matrix bevat en die elk teken gescheiden door een *scheidings* teken.

```
join([<collection>], '<delimiter>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*innings*> | Ja | Matrix | De matrix waarvan de items moeten worden toegevoegd |
| <*vorm*> | Ja | Tekenreeks | Het scheidings teken dat wordt weer gegeven tussen elk teken in de resulterende teken reeks |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*char1* >< *scheidings teken* >< *char2* >< *scheidings*>... | Tekenreeks | De resulterende teken reeks die is gemaakt op basis van alle items in de opgegeven matrix |
||||

*Voorbeeld*

In dit voor beeld wordt een teken reeks gemaakt van alle items in deze matrix met het opgegeven teken als scheidings teken:

```
join(createArray('a', 'b', 'c'), '.')
```

En retourneert dit resultaat: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>duren

Het laatste item van een verzameling retour neren.

```
last('<collection>')
last([<collection>])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*innings*> | Ja | Teken reeks of matrix | De verzameling waar het laatste item moet worden gevonden |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*laatste verzameling-item*> | Respectievelijk een teken reeks of matrix | Het laatste item in de verzameling |
||||

*Voorbeeld*

In deze voor beelden vindt u het laatste item in deze verzamelingen:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

En retourneert deze resultaten:

* Eerste voor beeld: `"d"`
* Tweede voor beeld: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

De start positie of index waarde Retour neren voor het laatste exemplaar van een subtekenreeks. Deze functie is niet hoofdletter gevoelig en indexen beginnen met het cijfer 0.

```json
lastIndexOf('<text>', '<searchText>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*SMS*> | Ja | Tekenreeks | De teken reeks die de subtekenreeks bevat die moet worden gezocht |
| <*Tekst*> | Ja | Tekenreeks | De subtekenreeks die u wilt zoeken |
|||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*eind index-waarde*> | Geheel getal | De start positie of index waarde voor het laatste exemplaar van de opgegeven subtekenreeks. |
|||

Als de waarde voor de teken reeks of subtekenreeks leeg is, gebeurt het volgende:

* Als alleen de teken reeks waarde leeg is, wordt de functie geretourneerd `-1` .

* Als de waarden voor de teken reeks en de subtekenreeks beide leeg zijn, retourneert de functie `0` .

* Als alleen de waarde van de subtekenreeks leeg is, retourneert de functie de teken reeks lengte min 1.

*Voorbeelden*

In dit voor beeld wordt gezocht naar de begin index waarde voor het laatste exemplaar van de subtekenreeks `world` in de teken reeks `hello world hello world` . Het geretourneerde resultaat is `18` :

```json
lastIndexOf('hello world hello world', 'world')
```

In dit voor beeld ontbreekt de subtekenreeks-para meter en retourneert een waarde van, `22` omdat de waarde van de invoer teken reeks ( `23` ) min 1 groter is dan 0.

```json
lastIndexOf('hello world hello world', '')
```

<a name="length"></a>

### <a name="length"></a>lengte

Retourneert het aantal items in een verzameling.

```
length('<collection>')
length([<collection>])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*innings*> | Ja | Teken reeks of matrix | De verzameling met de items die moeten worden geteld |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*lengte-of-aantal*> | Geheel getal | Het aantal items in de verzameling |
||||

*Voorbeeld*

In deze voor beelden wordt het aantal items in deze verzamelingen geteld:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

En retour neren dit resultaat: `4`

<a name="less"></a>

### <a name="less"></a>less

Controleer of de eerste waarde lager is dan de tweede waarde.
Retourneert waar wanneer de eerste waarde kleiner is, of retourneert False als de eerste waarde meer is.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Geheel getal, float of teken reeks | De eerste waarde die moet worden gecontroleerd of minder dan de tweede waarde |
| <*compareTo*> | Ja | Respectievelijk geheel getal, float of teken reeks | Het vergelijkings item |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | Retourneert waar als de eerste waarde lager is dan de tweede waarde. Retourneert onwaar als de eerste waarde gelijk is aan of groter is dan de tweede waarde. |
||||

*Voorbeeld*

In deze voor beelden wordt gecontroleerd of de eerste waarde lager is dan de tweede waarde.

```
less(5, 10)
less('banana', 'apple')
```

En retour neren deze resultaten:

* Eerste voor beeld: `true`
* Tweede voor beeld: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Controleer of de eerste waarde kleiner is dan of gelijk is aan de tweede waarde.
Retourneert waar als de eerste waarde kleiner is dan of gelijk is aan of retourneert onwaar als de eerste waarde meer is.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Geheel getal, float of teken reeks | De eerste waarde om te controleren of deze kleiner dan of gelijk is aan de tweede waarde |
| <*compareTo*> | Ja | Respectievelijk geheel getal, float of teken reeks | Het vergelijkings item |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar  | Booleaans | Retourneert waar als de eerste waarde kleiner is dan of gelijk is aan de tweede waarde. Retourneert onwaar als de eerste waarde groter is dan de tweede waarde. |
||||

*Voorbeeld*

In deze voor beelden wordt gecontroleerd of de eerste waarde kleiner of gelijk is aan de tweede waarde.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

En retour neren deze resultaten:

* Eerste voor beeld: `true`
* Tweede voor beeld: `false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

De call back-URL retour neren die een trigger of actie aanroept.
Deze functie werkt alleen met triggers en acties voor de **HttpWebhook** -en **ApiConnectionWebhook** -connector typen, maar niet de typen **hand matig**, **terugkeer patroon**, **http** en **APIConnection** .

```
listCallbackUrl()
```

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*call back-URL*> | Tekenreeks | De call back-URL voor een trigger of actie |
||||

*Voorbeeld*

In dit voor beeld ziet u een voor beeld van een call back-URL die door deze functie kan worden geretourneerd:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>max

Retourneert de hoogste waarde van een lijst of matrix met getallen die aan beide uiteinden zijn gekoppeld.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*getal1*>, <*getal2*>,... | Ja | Geheel getal, float of beide | De verzameling getallen waarvan u de hoogste waarde wilt |
| [<*getal1*>, <*getal2*>,...] | Ja | Matrix: geheel getal, float of beide | De matrix van getallen waarvan u de hoogste waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*Max-waarde*> | Geheel getal of zwevend | De hoogste waarde in de opgegeven matrix of set getallen |
||||

*Voorbeeld*

In deze voor beelden wordt de hoogste waarde uit de set getallen en de matrix opgehaald:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

En retour neren dit resultaat: `3`

<a name="min"></a>

### <a name="min"></a>min.

Retourneert de laagste waarde van een reeks getallen of een matrix.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*getal1*>, <*getal2*>,... | Ja | Geheel getal, float of beide | De verzameling getallen waarvan u de laagste waarde wilt |
| [<*getal1*>, <*getal2*>,...] | Ja | Matrix: geheel getal, float of beide | De matrix met getallen waarvan u de laagste waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*min-waarde*> | Geheel getal of zwevend | De laagste waarde in de opgegeven reeks getallen of de opgegeven matrix |
||||

*Voorbeeld*

In deze voor beelden wordt de laagste waarde in de set met getallen en de matrix opgehaald:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

En retour neren dit resultaat: `1`

<a name="mod"></a>

### <a name="mod"></a>mod

Retourneert de rest van het delen van twee getallen.
Zie [div ()](#div)om het resultaat van het gehele getal op te halen.

```
mod(<dividend>, <divisor>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | Ja | Geheel getal of zwevend | Het getal dat moet worden gedeeld door de *deler* |
| <*reken*> | Ja | Geheel getal of zwevend | Het getal dat het *deelt* deel, maar mag niet 0 zijn. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*modulo-resultaat*> | Geheel getal of zwevend | De rest van het delen van het eerste getal met het tweede getal |
||||

*Voorbeeld*

In het volgende voor beeld wordt het eerste getal in het tweede getal gedeeld:

```
mod(3, 2)
```

En retour neren dit resultaat: `1`

<a name="mul"></a>

### <a name="mul"></a>mul

Retourneert het product van het vermenigvuldigen van twee getallen.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Ja | Geheel getal of zwevend | Het getal waarmee u wilt vermenigvuldigen met *multiplicand2* |
| <*multiplicand2*> | Ja | Geheel getal of zwevend | Het getal dat meerdere *multiplicand1* |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*product-resultaat*> | Geheel getal of zwevend | Het product van het eerste getal vermenigvuldigen met het tweede getal |
||||

*Voorbeeld*

In deze voor beelden wordt het eerste getal vermenigvuldigd met het tweede getal:

```
mul(1, 2)
mul(1.5, 2)
```

En retour neren deze resultaten:

* Eerste voor beeld: `2`
* Tweede voor beeld `3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

De hoofd tekst van een specifiek deel in de uitvoer van een actie met meerdere delen retour neren.

```
multipartBody('<actionName>', <index>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De naam voor de actie met een uitvoer met meerdere delen |
| <*TabIndex*> | Ja | Geheel getal | De index waarde voor het onderdeel dat u wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*organen*> | Tekenreeks | De hoofd tekst voor het opgegeven deel |
||||

<a name="not"></a>

### <a name="not"></a>not

Controleer of een expressie onwaar is.
Retourneert waar als de expressie onwaar is, of retourneert False als ' True '.

```json
not(<expression>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*expressie*> | Ja | Booleaans | De expressie die moet worden gecontroleerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | Retourneert waar als de expressie onwaar is. Retourneert onwaar als de expressie waar is. |
||||

*Voorbeeld 1*

In deze voor beelden wordt gecontroleerd of de opgegeven expressies onwaar zijn:

```json
not(false)
not(true)
```

En retour neren deze resultaten:

* Eerste voor beeld: de expressie is onwaar, waardoor de functie wordt geretourneerd `true` .
* Tweede voor beeld: de expressie is waar, dus retourneert de functie `false` .

*Voorbeeld 2*

In deze voor beelden wordt gecontroleerd of de opgegeven expressies onwaar zijn:

```json
not(equals(1, 2))
not(equals(1, 1))
```

En retour neren deze resultaten:

* Eerste voor beeld: de expressie is onwaar, waardoor de functie wordt geretourneerd `true` .
* Tweede voor beeld: de expressie is waar, dus retourneert de functie `false` .

<a name="or"></a>

### <a name="or"></a>of

Controleer of ten minste één expressie waar is.
Retourneert waar als ten minste één expressie waar is, of retourneert onwaar als alle zijn ingesteld op ONWAAR.

```
or(<expression1>, <expression2>, ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*expressie1*>, <*Expressie2*>,... | Ja | Booleaans | De te controleren expressies |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | Retourneert waar als ten minste één expressie waar is. Retourneert onwaar als alle expressies onwaar zijn. |
||||

*Voorbeeld 1*

Deze voor beelden controleren of ten minste één expressie waar is:

```json
or(true, false)
or(false, false)
```

En retour neren deze resultaten:

* Eerste voor beeld: ten minste één expressie is waar, dus retourneert de functie `true` .
* Tweede voor beeld: beide expressies zijn onwaar, waardoor de functie wordt geretourneerd `false` .

*Voorbeeld 2*

Deze voor beelden controleren of ten minste één expressie waar is:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

En retour neren deze resultaten:

* Eerste voor beeld: ten minste één expressie is waar, dus retourneert de functie `true` .
* Tweede voor beeld: beide expressies zijn onwaar, waardoor de functie wordt geretourneerd `false` .

<a name="outputs"></a>

### <a name="outputs"></a>uitvoer

Retour neren van de uitvoer van een actie tijdens runtime. Gebruik deze functie in plaats van `actionOutputs()` , die wordt omgezet `outputs()` in in de Logic app Designer. Hoewel beide functies op dezelfde manier werken, verdient de `outputs()` voor keur.

```
outputs('<actionName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De naam voor de uitvoer van de actie die u wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| <*uitvoer*> | Tekenreeks | De uitvoer van de opgegeven actie |
||||

*Voorbeeld*

In dit voor beeld wordt de uitvoer van de Twitter-actie opgehaald `Get user` :

```
outputs('Get_user')
```

En retourneert dit resultaat:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="parameters"></a>

### <a name="parameters"></a>parameters

Retourneert de waarde voor een para meter die wordt beschreven in uw werk stroom definitie.

```
parameters('<parameterName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*parameterName*> | Ja | Tekenreeks | De naam voor de para meter waarvan u de waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*para meter-waarde*> | Alle | De waarde voor de opgegeven para meter |
||||

*Voorbeeld*

Stel dat u deze JSON-waarde hebt:

```json
{
  "fullName": "Sophia Owen"
}
```

In dit voor beeld wordt de waarde voor de opgegeven para meter opgehaald:

```
parameters('fullName')
```

En retourneert dit resultaat: `"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>ASELECT

Retourneert een wille keurig geheel getal uit een opgegeven bereik, dat alleen aan het begin einde is inbegrepen.

```
rand(<minValue>, <maxValue>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Ja | Geheel getal | Het kleinste gehele getal in het bereik |
| <*maxValue*> | Ja | Geheel getal | Het gehele getal dat volgt op het hoogste gehele getal in het bereik dat de functie kan retour neren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*wille keurig resultaat*> | Geheel getal | Het wille keurig geheel getal dat is geretourneerd uit het opgegeven bereik |
||||

*Voorbeeld*

In dit voor beeld wordt een wille keurig geheel getal opgehaald uit het opgegeven bereik, met uitzonde ring van de maximum waarde:

```
rand(1, 5)
```

En retourneert een van deze getallen als resultaat: `1` , `2` , `3` , of `4`

<a name="range"></a>

### <a name="range"></a>bereik

Retourneert een matrix met gehele getallen die begint met een opgegeven geheel getal.

```
range(<startIndex>, <count>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Start index*> | Ja | Geheel getal | Een geheel getal dat de matrix als het eerste item start |
| <*aantal*> | Ja | Geheel getal | Het aantal gehele getallen in de matrix |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*bereik-resultaat*>] | Matrix | De matrix met gehele getallen vanaf de opgegeven index |
||||

*Voorbeeld*

In dit voor beeld wordt een matrix met gehele getallen gemaakt die begint met de opgegeven index en het opgegeven aantal gehele getallen bevat:

```
range(1, 4)
```

En retourneert dit resultaat: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>vervangen

Vervang een subtekenreeks door de opgegeven teken reeks en retourneert de resultaat teken reeks. Deze functie is hoofdletter gevoelig.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*SMS*> | Ja | Tekenreeks | De teken reeks met de subtekenreeks die moet worden vervangen |
| <*oldText*> | Ja | Tekenreeks | De subtekenreeks die moet worden vervangen |
| <*newText*> | Ja | Tekenreeks | De vervangende teken reeks |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tekst*> | Tekenreeks | De bijgewerkte teken reeks na het vervangen van de subtekenreeks <p>Als de subtekenreeks niet wordt gevonden, retourneert u de oorspronkelijke teken reeks. |
||||

*Voorbeeld*

In dit voor beeld wordt de subtekenreeks ' old ' in ' Old string ' gezocht en vervangen door ' nieuw ':

```
replace('the old string', 'old', 'new')
```

En retourneert dit resultaat: `"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

Een eigenschap van een object verwijderen en het bijgewerkte object retour neren. Als de eigenschap die u probeert te verwijderen, niet bestaat, retourneert de functie het oorspronkelijke object.

```
removeProperty(<object>, '<property>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*object*> | Ja | Object | Het JSON-object waarvan u een eigenschap wilt verwijderen |
| <*eigenschap*> | Ja | Tekenreeks | De naam van de eigenschap die u wilt verwijderen |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-object*> | Object | Het bijgewerkte JSON-object zonder de opgegeven eigenschap |
||||

Als u een onderliggende eigenschap van een bestaande eigenschap wilt verwijderen, gebruikt u de volgende syntaxis:

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*object*> | Ja | Object | Het JSON-object waarvan u de eigenschap wilt verwijderen |
| <*Parent-eigenschap*> | Ja | Tekenreeks | De naam van de bovenliggende eigenschap met de onderliggende eigenschap die u wilt verwijderen |
| <*Child-eigenschap*> | Ja | Tekenreeks | De naam van de onderliggende eigenschap die u wilt verwijderen |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-object*> | Object | Het bijgewerkte JSON-object waarvan u de onderliggende eigenschap hebt verwijderd |
||||

*Voorbeeld 1*

In dit voor beeld wordt de `middleName` eigenschap van een JSON-object, dat wordt geconverteerd van een teken reeks naar JSON, verwijderd met behulp van de functie [JSON ()](#json) en wordt het bijgewerkte object geretourneerd:

```
removeProperty(json('{ "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" }'), 'middleName')
```

Hier is het huidige JSON-object:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

Hier is het bijgewerkte JSON-object:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

*Voorbeeld 2*

In dit voor beeld wordt de `middleName` onderliggende eigenschap van een `customerName` bovenliggende eigenschap in een JSON-object verwijderd, die wordt geconverteerd van een teken reeks naar JSON met behulp van de [JSON ()](#json) -functie, en het bijgewerkte object wordt geretourneerd:

```
removeProperty(json('{ "customerName": { "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" } }')['customerName'], 'middleName')
```

Hier is het huidige JSON-object:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

Hier is het bijgewerkte JSON-object:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

<a name="result"></a>

### <a name="result"></a>result

De resultaten retour neren van de acties op het hoogste niveau in de opgegeven actie met een bereik, zoals een `For_each` , `Until` of `Scope` actie. De `result()` functie accepteert één para meter, de naam van het bereik en retourneert een matrix die informatie bevat van de acties van het eerste niveau in dat bereik. Deze actie objecten bevatten dezelfde kenmerken als die worden geretourneerd door de `actions()` functie, zoals de begin tijd van de actie, de eind tijd, status, invoer, correlatie-id's en uitvoer.

> [!NOTE]
> Deze functie retourneert *alleen* informatie van de acties op het hoogste niveau in de actie binnen het bereik en niet van diep geneste acties zoals switch-of voorwaarde acties.

U kunt deze functie bijvoorbeeld gebruiken om de resultaten van mislukte acties op te halen, zodat u uitzonde ringen kunt vaststellen en verwerken. Zie [context en resultaten ophalen voor fouten](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures)voor meer informatie.

```
result('<scopedActionName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*scopedActionName*> | Ja | Tekenreeks | De naam van de bereik actie waarbij u de invoer en uitvoer wilt van de acties op het hoogste niveau binnen dat bereik |
||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*Matrix-object*> | Matrix object | Een matrix die matrices van invoer en uitvoer van elke actie op het hoogste niveau binnen het opgegeven bereik bevat |
||||

*Voorbeeld*

In dit voor beeld worden de invoer en uitvoer van elke herhaling van een HTTP-actie in een `For_each` lus geretourneerd met behulp van de `result()` functie in de `Compose` actie:

```json
{
   "actions": {
      "Compose": {
         "inputs": "@result('For_each')",
         "runAfter": {
            "For_each": [
               "Succeeded"
            ]
         },
         "type": "compose"
      },
      "For_each": {
         "actions": {
            "HTTP": {
               "inputs": {
                  "method": "GET",
                  "uri": "https://httpstat.us/200"
               },
               "runAfter": {},
               "type": "Http"
            }
         },
         "foreach": "@triggerBody()",
         "runAfter": {},
         "type": "Foreach"
      }
   }
}
```

Hier ziet u hoe de geretourneerde matrix eruit kan zien waar het buitenste `outputs` object de invoer en uitvoer bevat van elke herhaling van de acties binnen de `For_each` actie.

```json
[
   {
      "name": "HTTP",
      "outputs": [
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
               "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "6bad3015-0444-4ccd-a971-cbb0c99a7.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         },
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
            "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "9987e889-981b-41c5-aa27-f3e0e59bf69.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         }
      ]
   }
]
```

<a name="setProperty"></a>

### <a name="setproperty"></a>setProperty

Stel de waarde voor de eigenschap van het JSON-object in en retour neer het bijgewerkte object. Als de eigenschap die u probeert in te stellen niet bestaat, wordt de eigenschap toegevoegd aan het object. Als u een nieuwe eigenschap wilt toevoegen, gebruikt u de functie [addProperty ()](#addProperty) .

```
setProperty(<object>, '<property>', <value>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*object*> | Ja | Object | Het JSON-object waarvan u de eigenschap wilt instellen |
| <*eigenschap*> | Ja | Tekenreeks | De naam van de bestaande of nieuwe eigenschap die moet worden ingesteld |
| <*Value*> | Ja | Alle | De waarde die moet worden ingesteld voor de opgegeven eigenschap |
|||||

Als u de onderliggende eigenschap in een onderliggend object wilt instellen, gebruikt u `setProperty()` in plaats daarvan een genest aanroep. Anders retourneert de functie alleen het onderliggende object als uitvoer.

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*object*> | Ja | Object | Het JSON-object waarvan u de eigenschap wilt instellen |
| <*Parent-eigenschap*> | Ja | Tekenreeks | De naam van de bovenliggende eigenschap met de onderliggende eigenschap die u wilt instellen |
| <*Child-eigenschap*> | Ja | Tekenreeks | De naam van de onderliggende eigenschap die moet worden ingesteld |
| <*Value*> | Ja | Alle | De waarde die moet worden ingesteld voor de opgegeven eigenschap |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-object*> | Object | Het bijgewerkte JSON-object waarvan u de eigenschap hebt ingesteld |
||||

*Voorbeeld 1*

In dit voor beeld wordt de `surName` eigenschap in een JSON-object ingesteld, dat wordt geconverteerd van een teken reeks naar JSON met behulp van de [JSON ()](#json) -functie. De functie wijst de opgegeven waarde toe aan de eigenschap en retourneert het bijgewerkte object:

```
setProperty(json('{ "firstName": "Sophia", "surName": "Owen" }'), 'surName', 'Hartnett')
```

Hier is het huidige JSON-object:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Hier is het bijgewerkte JSON-object:

```json
{
   "firstName": "Sophia",
   "surName": "Hartnett"
}
```

*Voorbeeld 2*

In dit voor beeld wordt de `surName` onderliggende eigenschap voor de `customerName` bovenliggende eigenschap in een JSON-object ingesteld, die wordt geconverteerd van een teken reeks naar JSON met behulp van de [JSON ()-](#json) functie. De functie wijst de opgegeven waarde toe aan de eigenschap en retourneert het bijgewerkte object:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'surName', 'Hartnett'))
```

Hier is het huidige JSON-object:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Owen"
   }
}
```

Hier is het bijgewerkte JSON-object:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Hartnett"
   }
}
```

<a name="skip"></a>

### <a name="skip"></a>skip

Verwijder items van de voor kant van een verzameling en retour neer *alle andere* items.

```
skip([<collection>], <count>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*innings*> | Ja | Matrix | De verzameling waarvan u de items wilt verwijderen |
| <*aantal*> | Ja | Geheel getal | Een positief geheel getal voor het aantal items dat aan de voor grond moet worden verwijderd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*bijgewerkt-verzameling*>] | Matrix | De bijgewerkte verzameling na het verwijderen van de opgegeven items |
||||

*Voorbeeld*

In dit voor beeld wordt één item, het cijfer 0, van de voor kant van de opgegeven matrix verwijderd:

```
skip(createArray(0, 1, 2, 3), 1)
```

En retourneert deze matrix met de resterende items: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Retourneert een matrix die subtekenreeksen bevat, gescheiden door komma's, op basis van het opgegeven scheidings teken in de oorspronkelijke teken reeks.

```
split('<text>', '<delimiter>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*SMS*> | Ja | Tekenreeks | De teken reeks die moet worden gescheiden in subtekenreeksen op basis van het opgegeven scheidings teken in de oorspronkelijke teken reeks |
| <*vorm*> | Ja | Tekenreeks | Het teken in de oorspronkelijke teken reeks dat moet worden gebruikt als scheidings teken |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*subtekenreeks1*>, <*substring2*>,...] | Matrix | Een matrix die subtekenreeksen uit de oorspronkelijke teken reeks bevat, gescheiden door komma's |
||||

*Voorbeeld*

In dit voor beeld wordt een matrix gemaakt met subtekenreeksen uit de opgegeven teken reeks op basis van het opgegeven teken als scheidings tekens:

```
split('a_b_c', '_')
```

En retourneert deze matrix als het resultaat: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Retourneert het begin van de dag voor een tijds tempel.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS. fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tijds tempel*> | Tekenreeks | Het opgegeven tijds tempel, maar beginnend bij het lege uur voor de dag |
||||

*Voorbeeld*

In dit voor beeld wordt gezocht naar het begin van de dag voor deze tijds tempel:

```
startOfDay('2018-03-15T13:30:30Z')
```

En retourneert dit resultaat: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Het begin van het uur retour neren voor een tijds tempel.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS. fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tijds tempel*> | Tekenreeks | Het opgegeven tijds tempel, maar beginnend bij het nul-minuten teken voor het uur |
||||

*Voorbeeld*

In dit voor beeld vindt u het begin van het uur voor deze tijds tempel:

```
startOfHour('2018-03-15T13:30:30Z')
```

En retourneert dit resultaat: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Retourneert het begin van de maand voor een tijds tempel.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS. fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tijds tempel*> | Tekenreeks | Het opgegeven tijds tempel dat begint op de eerste dag van de maand met het nul-uur |
||||

*Voorbeeld 1*

In dit voor beeld wordt het begin van de maand voor deze tijds tempel geretourneerd:

```
startOfMonth('2018-03-15T13:30:30Z')
```

En retourneert dit resultaat: `"2018-03-01T00:00:00.0000000Z"`

*Voorbeeld 2*

In dit voor beeld wordt het begin van de maand in de opgegeven notatie voor deze tijds tempel geretourneerd:

```
startOfMonth('2018-03-15T13:30:30Z', 'yyyy-MM-dd')
```

En retourneert dit resultaat: `"2018-03-01"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Controleren of een teken reeks begint met een specifieke subtekenreeks.
Retourneert waar als de subtekenreeks wordt gevonden, of retourneert False als het niet is gevonden.
Deze functie is niet hoofdletter gevoelig.

```
startsWith('<text>', '<searchText>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*SMS*> | Ja | Tekenreeks | De teken reeks die moet worden gecontroleerd |
| <*Tekst*> | Ja | Tekenreeks | De begin teken reeks die moet worden gezocht |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar  | Booleaans | Retourneert waar als de eerste subtekenreeks wordt gevonden. Retourneert onwaar wanneer deze niet is gevonden. |
||||

*Voorbeeld 1*

In dit voor beeld wordt gecontroleerd of de teken reeks "Hallo wereld" begint met de subtekenreeks "Hallo":

```
startsWith('hello world', 'hello')
```

En retourneert dit resultaat: `true`

*Voorbeeld 2*

In dit voor beeld wordt gecontroleerd of de teken reeks "Hallo wereld" begint met de subtekenreeks "Greetings":

```
startsWith('hello world', 'greetings')
```

En retourneert dit resultaat: `false`

<a name="string"></a>

### <a name="string"></a>tekenreeks

Retourneert de teken reeks versie voor een waarde.

```
string(<value>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Alle | De waarde die moet worden geconverteerd. Als deze waarde Null is of is geëvalueerd als null, wordt de waarde geconverteerd naar een lege teken reeks ( `""` ) waarde. <p><p>Als u bijvoorbeeld een teken reeks variabele toewijst aan een niet-bestaande eigenschap die u kunt openen met de `?` operator, wordt de null-waarde omgezet in een lege teken reeks. Het vergelijken van een null-waarde is echter niet hetzelfde als het vergelijken van een lege teken reeks. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*teken reeks-waarde*> | Tekenreeks | De teken reeks versie voor de opgegeven waarde. Als de *waarde* para meter null is of resulteert in null, wordt deze waarde geretourneerd als een lege teken reeks ( `""` ) waarde. |
||||





*Voorbeeld 1*

In dit voor beeld wordt de teken reeks versie gemaakt voor dit nummer:

```
string(10)
```

En retourneert dit resultaat: `"10"`

*Voorbeeld 2*

In dit voor beeld wordt een teken reeks gemaakt voor het opgegeven JSON-object en wordt de back slash ( \\ ) gebruikt als escape teken voor het dubbele aanhalings tekens (").

```
string( { "name": "Sophie Owen" } )
```

En retourneert dit resultaat: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

Retourneert het resultaat van het aftrekken van het tweede getal uit het eerste getal.

```
sub(<minuend>, <subtrahend>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*aftrek getal*> | Ja | Geheel getal of zwevend | Het getal waaruit de *aftrekker* moet worden afgetrokken |
| <*aftrekker*> | Ja | Geheel getal of zwevend | Het getal dat moet worden afgetrokken van de *aftrek getal* |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*Daardoor*> | Geheel getal of zwevend | Het resultaat van het aftrekken van het tweede getal uit het eerste getal |
||||

*Voorbeeld*

In dit voor beeld wordt het tweede getal afgetrokken van het eerste getal:

```
sub(10.3, .3)
```

En retourneert dit resultaat: `10`

<a name="substring"></a>

### <a name="substring"></a>subtekenreeks

Retourneert tekens uit een teken reeks, beginnend bij de opgegeven positie of index. Index waarden beginnen met het cijfer 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*SMS*> | Ja | Tekenreeks | De teken reeks waarvan u de tekens wilt |
| <*Start index*> | Ja | Geheel getal | Een positief getal gelijk aan of groter dan 0 dat u wilt gebruiken als de begin positie of index waarde |
| <*lange*> | Nee | Geheel getal | Een positief aantal tekens dat u wilt in de subtekenreeks |
|||||

> [!NOTE]
> Zorg ervoor dat de som van de waarden van de para meter *Start index* en *lengte* kleiner is dan de lengte van de teken reeks die u opgeeft voor de *tekst* parameter.
> Anders wordt er een fout bericht weer gegeven, in tegens telling tot vergelijk bare functies in andere talen waarbij het resultaat de subtekenreeks is van de *Start index* tot het einde van de teken reeks. De *lengte* parameter is optioneel en als deze niet is ingevoerd, gebruikt de functie **subtekenreeks ()** alle tekens vanaf *Start index* tot het einde van de teken reeks.

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*subtekenreeks-resultaat*> | Tekenreeks | Een subtekenreeks met het opgegeven aantal tekens, beginnend bij de opgegeven index positie in de bron teken reeks |
||||

*Voorbeeld*

In dit voor beeld wordt een subtekenreeks van vijf tekens gemaakt op basis van de opgegeven teken reeks, beginnend bij de index waarde 6:

```
substring('hello world', 6, 5)
```

En retourneert dit resultaat: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Een aantal tijds eenheden aftrekken van een tijds tempel.
Zie ook [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks die de tijds tempel bevat |
| <*bereik*> | Ja | Geheel getal | Het aantal opgegeven tijds eenheden dat moet worden afgetrokken |
| <*timeUnit*> | Ja | Tekenreeks | De tijds eenheid die moet worden gebruikt met het *interval*: ' seconde ', ' minuut ', ' uur ', ' dag ', ' week ', ' maand ', ' jaar ' |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS. fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt-tijds tempel*> | Tekenreeks | De tijds tempel min het opgegeven aantal tijds eenheden |
||||

*Voorbeeld 1*

In dit voor beeld wordt één dag afgetrokken van deze tijds tempel:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

En retourneert dit resultaat: `"2018-01-01T00:00:00.0000000Z"`

*Voorbeeld 2*

In dit voor beeld wordt één dag afgetrokken van deze tijds tempel:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

En retourneert dit resultaat met de optionele D-indeling: `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take

Items van de voor grond van een verzameling retour neren.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*innings*> | Ja | Teken reeks of matrix | De verzameling waarvan u de items wilt |
| <*aantal*> | Ja | Geheel getal | Een positief geheel getal voor het aantal items dat u wilt van de voor grond |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*subset*> of [<*subset*>] | Respectievelijk een teken reeks of matrix | Een teken reeks of matrix met het opgegeven aantal items van de voor grond van de oorspronkelijke verzameling |
||||

*Voorbeeld*

In deze voor beelden wordt het opgegeven aantal items van de voor grond van deze verzamelingen opgehaald:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

En retour neren deze resultaten:

* Eerste voor beeld: `"abc"`
* Tweede voor beeld: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>Ticks

Retourneert het aantal maten, 100-nano seconden intervallen, sinds 1 januari 0001 12:00:00 middernacht (of DateTime. Ticks in C#) tot de opgegeven tijds tempel. Zie dit onderwerp: [DateTime. Ticks (systeem)](/dotnet/api/system.datetime.ticks)voor meer informatie.

```
ticks('<timestamp>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Neem*> | Ja | Tekenreeks | De teken reeks voor een tijds tempel |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*maten-nummer*> | Geheel getal | Het aantal maat streepjes sinds de opgegeven tijds tempel |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Retourneert een teken reeks met de indeling kleine letters. Als een teken in de teken reeks geen kleine versie heeft, blijft dat teken ongewijzigd in de geretourneerde teken reeks.

```
toLower('<text>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*SMS*> | Ja | Tekenreeks | De teken reeks die moet worden geretourneerd met een kleine letter notatie |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*kleine letters*> | Tekenreeks | De oorspronkelijke teken reeks in kleine letters |
||||

*Voorbeeld*

In dit voor beeld wordt deze teken reeks geconverteerd naar kleine letters:

```
toLower('Hello World')
```

En retourneert dit resultaat: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Retourneert een teken reeks met een hoofd letter. Als een teken in de teken reeks geen hoofd versie heeft, blijft dat teken ongewijzigd in de geretourneerde teken reeks.

```
toUpper('<text>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*SMS*> | Ja | Tekenreeks | De teken reeks die moet worden geretourneerd met een hoofd letter |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*hoofd letters: tekst*> | Tekenreeks | De oorspronkelijke teken reeks in hoofd letters |
||||

*Voorbeeld*

In dit voor beeld wordt deze teken reeks geconverteerd naar hoofd letters:

```
toUpper('Hello World')
```

En retourneert dit resultaat: `"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>activeren

Retourneert de uitvoer van een trigger tijdens runtime, of waarden van andere JSON-naam-en-waardeparen, die u kunt toewijzen aan een expressie.

* In de invoer van een trigger retourneert deze functie de uitvoer van de vorige uitvoering.

* Binnen de voor waarde van een trigger retourneert deze functie de uitvoer van de huidige uitvoering.

De functie verwijst standaard naar het volledige trigger object, maar u kunt desgewenst een eigenschap opgeven waarvan u de waarde wilt bepalen.
Deze functie heeft ook steno versies beschikbaar, Zie [triggerOutputs ()](#triggerOutputs) en [triggerBody ()](#triggerBody).

```
trigger()
```

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*trigger-uitvoer*> | Tekenreeks | De uitvoer van een trigger tijdens runtime |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

Retour neer de uitvoer van een trigger `body` tijdens runtime.
Steno voor `trigger().outputs.body` .
Zie [trigger ()](#trigger).

```
triggerBody()
```

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*trigger-hoofd tekst-uitvoer*> | Tekenreeks | De `body` uitvoer van de trigger |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Retour neer een matrix met waarden die overeenkomen met een sleutel naam in een trigger van de *formulier gegevens* of *formulier-gecodeerde* uitvoer.

```
triggerFormDataMultiValues('<key>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*prestatie*> | Ja | Tekenreeks | De naam van de sleutel waarvan u de waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*matrix-met-sleutel waarden*>] | Matrix | Een matrix met alle waarden die overeenkomen met de opgegeven sleutel |
||||

*Voorbeeld*

In dit voor beeld wordt een matrix gemaakt op basis van de sleutel waarde ' feedUrl ' in een RSS-trigger de formulier gegevens of de formulier-gecodeerde uitvoer:

```
triggerFormDataMultiValues('feedUrl')
```

En retourneert deze matrix als een voor beeld van een resultaat: `["https://feeds.a.dj.com/rss/RSSMarketsMain.xml"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Retourneert een teken reeks met één waarde die overeenkomt met een sleutel naam in een trigger van de *formulier gegevens* of *formulier-gecodeerde* uitvoer.
Als de functie meer dan één overeenkomst vindt, genereert de functie een fout.

```
triggerFormDataValue('<key>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*prestatie*> | Ja | Tekenreeks | De naam van de sleutel waarvan u de waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*sleutel waarde*> | Tekenreeks | De waarde in de opgegeven sleutel |
||||

*Voorbeeld*

In dit voor beeld wordt een teken reeks gemaakt op basis van de sleutel waarde ' feedUrl ' in een RSS-trigger de formulier gegevens of de formulier-gecodeerde uitvoer:

```
triggerFormDataValue('feedUrl')
```

En retourneert deze teken reeks als voorbeeld resultaat: `"https://feeds.a.dj.com/rss/RSSMarketsMain.xml"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

De hoofd tekst retour neren voor een specifiek deel in de uitvoer van een trigger met meerdere onderdelen.

```
triggerMultipartBody(<index>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*TabIndex*> | Ja | Geheel getal | De index waarde voor het onderdeel dat u wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*organen*> | Tekenreeks | De hoofd tekst voor het opgegeven deel in de meerdelige uitvoer van een trigger |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Retourneert de uitvoer van een trigger tijdens runtime of waarden van andere JSON-naam-en-waardeparen.
Steno voor `trigger().outputs` .
Zie [trigger ()](#trigger).

```
triggerOutputs()
```

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*trigger-uitvoer*> | Tekenreeks | De uitvoer van een trigger tijdens runtime  |
||||

<a name="trim"></a>

### <a name="trim"></a>interne

Verwijder voor loop-en volg spaties uit een teken reeks en retour neer de bijgewerkte teken reeks.

```
trim('<text>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*SMS*> | Ja | Tekenreeks | De teken reeks met de voor loop-en volg spaties die moeten worden verwijderd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updatedText*> | Tekenreeks | Een bijgewerkte versie van de oorspronkelijke teken reeks zonder voor loop-of volg spaties |
||||

*Voorbeeld*

In dit voor beeld worden de voor loop-en volg spaties uit de teken reeks "Hallo wereld" verwijderd:

```
trim(' Hello World  ')
```

En retourneert dit resultaat: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>Réunion

Een verzameling retour neren die *alle* items uit de opgegeven verzamelingen bevat.
Om weer te geven in het resultaat, kan een item worden weer gegeven in een verzameling die aan deze functie wordt door gegeven. Als een of meer items dezelfde naam hebben, wordt het laatste item met die naam weer gegeven in het resultaat.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*verzameling1*>, <*Collection2*>,...  | Ja | Matrix of object, maar niet beide | De verzamelingen van waaruit u wilt dat *alle* items |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | Respectievelijk matrix of object | Een verzameling met alle items uit de opgegeven verzamelingen-geen duplicaten |
||||

*Voorbeeld*

In dit voor beeld worden *alle* items van deze verzamelingen opgehaald:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

En retourneert dit resultaat: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Een gecodeerde URI-versie (Uniform Resource Identifier) retour neren voor een teken reeks door onveilige URL-tekens te vervangen door Escape tekens.
Gebruik deze functie in plaats van [encodeUriComponent ()](#encodeUriComponent).
Hoewel beide functies op dezelfde manier werken, verdient de `uriComponent()` voor keur.

```
uriComponent('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De teken reeks die moet worden geconverteerd naar een URI-gecodeerde indeling |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*gecodeerde URI*> | Tekenreeks | De teken reeks met URI-code ring met escape tekens |
||||

*Voorbeeld*

In dit voor beeld wordt een met URI gecodeerde versie gemaakt voor deze teken reeks:

```
uriComponent('https://contoso.com')
```

En retourneert dit resultaat: `"https%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Retourneert de binaire versie voor een onderdeel van een Uniform Resource Identifier (URI).

```
uriComponentToBinary('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De URI-gecodeerde teken reeks die moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*binair-voor-gecodeerde-URI*> | Tekenreeks | De binaire versie voor de teken reeks met URI-code ring. De binaire inhoud is base64-gecodeerd en vertegenwoordigd door `$content` . |
||||

*Voorbeeld*

In dit voor beeld wordt de binaire versie gemaakt voor deze teken reeks met URI-code ring:

```
uriComponentToBinary('https%3A%2F%2Fcontoso.com')
```

En retourneert dit resultaat:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Retourneert de teken reeks versie voor een gecodeerde URI-teken reeks (Uniform Resource Identifier), waardoor de gecodeerde URI-teken reeks effectief wordt gedecodeerd.

```
uriComponentToString('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De met URI gecodeerde teken reeks die moet worden gedecodeerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*gedecodeerde URI*> | Tekenreeks | De gedecodeerde versie voor de teken reeks met URI-code ring |
||||

*Voorbeeld*

In dit voor beeld wordt de gedecodeerde teken reeks versie gemaakt voor deze teken reeks met URI-code ring:

```
uriComponentToString('https%3A%2F%2Fcontoso.com')
```

En retourneert dit resultaat: `"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

De `host` waarde voor een Uniform Resource Identifier (URI) retour neren.

```
uriHost('<uri>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*URI*> | Ja | Tekenreeks | De URI waarvan `host` u de waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*Host-waarde*> | Tekenreeks | De `host` waarde voor de opgegeven URI |
||||

*Voorbeeld*

In dit voor beeld wordt de `host` waarde voor deze URI gezocht:

```
uriHost('https://www.localhost.com:8080')
```

En retourneert dit resultaat: `"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

De `path` waarde voor een Uniform Resource Identifier (URI) retour neren.

```
uriPath('<uri>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*URI*> | Ja | Tekenreeks | De URI waarvan `path` u de waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*pad-waarde*> | Tekenreeks | De `path` waarde voor de opgegeven URI. Als `path` er geen waarde is, retourneert het teken '/'. |
||||

*Voorbeeld*

In dit voor beeld wordt de `path` waarde voor deze URI gezocht:

```
uriPath('https://www.contoso.com/catalog/shownew.htm?date=today')
```

En retourneert dit resultaat: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

De `path` waarden en `query` voor een Uniform Resource Identifier (URI) retour neren.

```
uriPathAndQuery('<uri>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*URI*> | Ja | Tekenreeks | De URI waarvan `path` `query` u de waarden wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*pad-query-waarde*> | Tekenreeks | De `path` `query` waarden en voor de opgegeven URI. Als `path` er geen waarde wordt opgegeven, retourneert het teken/. |
||||

*Voorbeeld*

In dit voor beeld worden de `path` en- `query` waarden voor deze URI gezocht:

```
uriPathAndQuery('https://www.contoso.com/catalog/shownew.htm?date=today')
```

En retourneert dit resultaat: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

De `port` waarde voor een Uniform Resource Identifier (URI) retour neren.

```
uriPort('<uri>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*URI*> | Ja | Tekenreeks | De URI waarvan `port` u de waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*poort-waarde*> | Geheel getal | De `port` waarde voor de opgegeven URI. Als `port` er geen waarde wordt opgegeven, retourneert u de standaard poort voor het protocol. |
||||

*Voorbeeld*

In dit voor beeld wordt de `port` waarde voor deze URI geretourneerd:

```
uriPort('https://www.localhost:8080')
```

En retourneert dit resultaat: `8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

De `query` waarde voor een Uniform Resource Identifier (URI) retour neren.

```
uriQuery('<uri>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*URI*> | Ja | Tekenreeks | De URI waarvan `query` u de waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*query-waarde*> | Tekenreeks | De `query` waarde voor de opgegeven URI |
||||

*Voorbeeld*

In dit voor beeld wordt de `query` waarde voor deze URI geretourneerd:

```
uriQuery('https://www.contoso.com/catalog/shownew.htm?date=today')
```

En retourneert dit resultaat: `"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

De `scheme` waarde voor een Uniform Resource Identifier (URI) retour neren.

```
uriScheme('<uri>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*URI*> | Ja | Tekenreeks | De URI waarvan `scheme` u de waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*schema-waarde*> | Tekenreeks | De `scheme` waarde voor de opgegeven URI |
||||

*Voorbeeld*

In dit voor beeld wordt de `scheme` waarde voor deze URI geretourneerd:

```
uriScheme('https://www.contoso.com/catalog/shownew.htm?date=today')
```

En retourneert dit resultaat: `"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

De huidige tijds tempel retour neren.

```
utcNow('<format>')
```

U kunt desgewenst een andere indeling opgeven met de <*notatie*> para meter.


| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Formatteer*> | Nee | Tekenreeks | Een [enkele indelings aanduiding](/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepast opmaak patroon](/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaard notatie voor de tijds tempel is ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (jjjj-mm-ddTuu: mm: SS. fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijd zone gegevens behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*huidige-tijds tempel*> | Tekenreeks | De huidige datum en tijd |
||||

*Voorbeeld 1*

Stel dat vandaag 15 april 2018 om 1:00:00 uur.
In dit voor beeld wordt het huidige tijds tempel opgehaald:

```
utcNow()
```

En retourneert dit resultaat: `"2018-04-15T13:00:00.0000000Z"`

*Voorbeeld 2*

Stel dat vandaag 15 april 2018 om 1:00:00 uur.
In dit voor beeld wordt de huidige tijds tempel opgehaald met de optionele D-indeling:

```
utcNow('D')
```

En retourneert dit resultaat: `"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>variabelen

De waarde voor een opgegeven variabele retour neren.

```
variables('<variableName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*variableName*> | Ja | Tekenreeks | De naam voor de variabele waarvan u de waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*variabele-waarde*> | Alle | De waarde voor de opgegeven variabele |
||||

*Voorbeeld*

Stel dat de huidige waarde voor een variabele numItems is 20.
In dit voor beeld wordt de gehele waarde voor deze variabele opgehaald:

```
variables('numItems')
```

En retourneert dit resultaat: `20`

<a name="workflow"></a>

### <a name="workflow"></a>werkstroom

Alle gegevens over de werk stroom zelf retour neren tijdens de uitvoerings tijd.

```
workflow().<property>
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*eigenschap*> | Nee | Tekenreeks | De naam van de werk stroom eigenschap waarvan u de waarde wilt <p><p>Standaard heeft een werk stroom object de volgende eigenschappen: `name` , `type` , `id` , `location` , `run` en `tags` . <p><p>-De `run` waarde van de eigenschap is een JSON-object dat de volgende eigenschappen bevat: `name` , `type` en `id` . <p><p>-De `tags` eigenschap is een JSON-object dat [Tags bevat die zijn gekoppeld aan uw logische app in azure Logic apps of stroom in automatische energie automatisering](../azure-resource-manager/management/tag-resources.md) en de waarden voor die tags. Raadpleeg voor meer informatie over tags in azure-bronnen [Label resources, resource groepen en abonnementen voor logische organisatie in azure](../azure-resource-manager/management/tag-resources.md). <p><p>**Opmerking**: standaard bevat een logische app geen tags, maar een stroom automatiseren stroom heeft de `flowDisplayName` `environmentName` labels en. |
|||||

*Voorbeeld 1*

In dit voor beeld wordt de naam van de huidige uitvoering van een werk stroom geretourneerd:

`workflow().run.name`

*Voorbeeld 2*

Als u energie automatisering gebruikt, kunt u een `@workflow()` expressie maken die de `tags` uitvoer eigenschap gebruikt om de waarden van uw stroom of eigenschap op te halen `flowDisplayName` `environmentName` .

U kunt bijvoorbeeld aangepaste e-mail meldingen verzenden vanuit de stroom zelf die een koppeling naar uw stroom heeft. Deze meldingen kunnen een HTML-koppeling bevatten die de weergave naam van de stroom bevat in de titel van het e-mail bericht en volgt de volgende syntaxis:

`<a href=https://flow.microsoft.com/manage/environments/@{workflow()['tags']['environmentName']}/flows/@{workflow()['name']}/details>Open flow @{workflow()['tags']['flowDisplayName']}</a>`

<a name="xml"></a>

### <a name="xml"></a>xml

De XML-versie retour neren voor een teken reeks die een JSON-object bevat.

```
xml('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Value*> | Ja | Tekenreeks | De teken reeks met het JSON-object dat moet worden geconverteerd <p>Het JSON-object mag slechts één hoofd eigenschap hebben, die geen matrix kan zijn. <br>Gebruik de back slash ( \\ ) als escape-teken voor het dubbele aanhalings teken ("). |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*XML-versie*> | Object | De gecodeerde XML voor de opgegeven teken reeks of het JSON-object |
||||

*Voorbeeld 1*

In dit voor beeld wordt de XML-versie gemaakt voor deze teken reeks, die een JSON-object bevat:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

En retourneert deze XML-resultaten:

```xml
<name>Sophia Owen</name>
```

*Voorbeeld 2*

Stel dat u dit JSON-object hebt:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

In dit voor beeld wordt XML gemaakt voor een teken reeks die dit JSON-object bevat:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

En retourneert deze XML-resultaten:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>XPath

Controleer XML voor knoop punten of waarden die overeenkomen met een XPath-expressie (XML-pad) en retour neer de overeenkomende knoop punten of waarden. Een XPath-expressie of alleen XPath, helpt u bij het navigeren door een XML-document structuur, zodat u knoop punten of reken waarden kunt selecteren in de XML-inhoud.

```
xpath('<xml>', '<xpath>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*indeling*> | Ja | Alle | De XML-teken reeks die moet worden gezocht naar knoop punten of waarden die overeenkomen met een XPath-expressie waarde |
| <*XPath*> | Ja | Alle | De XPath-expressie die wordt gebruikt voor het zoeken van overeenkomende XML-knoop punten of-waarden |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*XML-knoop punt*> | XML | Een XML-knoop punt wanneer slechts één knoop punt overeenkomt met de opgegeven XPath-expressie |
| <*Value*> | Alle | De waarde van een XML-knoop punt wanneer er slechts één waarde overeenkomt met de opgegeven XPath-expressie |
| [<*XML-knooppunt1*>, <*xml-Knooppunt2*>,...] </br>-of- </br>[<*waarde1*>, <*Value2*>,...] | Matrix | Een matrix met XML-knoop punten of-waarden die overeenkomen met de opgegeven XPath-expressie |
||||

*Voorbeeld 1*

Stel dat u deze `'items'` XML-teken reeks hebt: 

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

In dit voor beeld worden de XPath-expressie door gegeven, `'/produce/item/name'` om te zoeken naar de knoop punten die overeenkomen met het `<name></name>` knoop punt in de `'items'` XML-teken reeks en wordt een matrix met deze knooppunt waarden geretourneerd:

`xpath(xml(parameters('items')), '/produce/item/name')`

In het voor beeld wordt ook de functie [para meters ()](#parameters) gebruikt om de XML-teken reeks op te halen uit `'items'` en de teken reeks te converteren naar XML-indeling met behulp van de functie [XML ()](#xml) .

Hier volgt de resultaat matrix met de knoop punten die overeenkomen `<name></name` :

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Voorbeeld 2*

In voor beeld 1 wordt in dit voor beeld de XPath-expressie door gegeven `'/produce/item/name[1]'` om het eerste `name` element te vinden dat de onderliggende van het `item` element is.

`xpath(xml(parameters('items')), '/produce/item/name[1]')`

Dit is het resultaat: `Gala`

*Voorbeeld 3*

In voor beeld 1 wordt in dit voor beeld de XPath-expressie door gegeven `'/produce/item/name[last()]'` om het laatste `name` element te vinden dat de onderliggende is van het `item` element.

`xpath(xml(parameters('items')), '/produce/item/name[last()]')`

Dit is het resultaat: `Honeycrisp`

*Voorbeeld 4*

In dit voor beeld ziet u dat uw `items` XML-teken reeks ook de kenmerken bevat `expired='true'` en `expired='false'` :

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name expired='true'>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name expired='false'>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

In dit voor beeld wordt de XPath-expressie door gegeven `'//name[@expired]'` om alle `name` elementen te zoeken die het `expired` kenmerk hebben:

`xpath(xml(parameters('items')), '//name[@expired]')`

Dit is het resultaat: `[ Gala, Honeycrisp ]`

*Voorbeeld 5*

Stel dat uw `items` XML-teken reeks in dit voor beeld alleen dit kenmerk bevat `expired = 'true'` :

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name expired='true'>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

In dit voor beeld wordt de XPath-expressie door gegeven `'//name[@expired = 'true']'` om alle `name` elementen te zoeken die het kenmerk hebben `expired = 'true'` :

`xpath(xml(parameters('items')), '//name[@expired = 'true']')`

Dit is het resultaat: `[ Gala ]`

*Voorbeeld 6*

In dit voor beeld bevat uw `items` XML-teken reeks ook deze kenmerken: 

* `expired='true' price='12'`
* `expired='false' price='40'`

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name expired='true' price='12'>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name expired='false' price='40'>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

In dit voor beeld wordt de XPath-expressie door gegeven `'//name[price>35]'` om alle `name` elementen te vinden die het `price > 35` volgende hebben:

`xpath(xml(parameters('items')), '//name[price>35]')`

Dit is het resultaat: `Honeycrisp`

*Voorbeeld 7*

In dit voor beeld is uw `items` XML-teken reeks hetzelfde als in voor beeld 1:

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

In dit voor beeld worden knoop punten gevonden die overeenkomen met het `<count></count>` knoop punt en worden deze knooppunt waarden toegevoegd met de `sum()` functie:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

Dit is het resultaat: `30`

*Voorbeeld 8*

In dit voor beeld geldt dat u deze XML-teken reeks hebt, die de XML-document naam ruimte bevat `xmlns="https://contoso.com"` :

```xml
<?xml version="1.0"?><file xmlns="https://contoso.com"><location>Paris</location></file>
```

Deze expressies gebruiken ofwel een XPath-expressie `/*[name()="file"]/*[name()="location"]` of `/*[local-name()="file" and namespace-uri()="https://contoso.com"]/*[local-name()="location"]` , om knoop punten te vinden die overeenkomen met het `<location></location>` knoop punt. In deze voor beelden ziet u de syntaxis die u gebruikt in de Logic app Designer of in de expressie-editor:

* `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`
* `xpath(xml(body('Http')), '/*[local-name()="file" and namespace-uri()="https://contoso.com"]/*[local-name()="location"]')`

Dit is het knoop punt resultaat dat overeenkomt met het `<location></location>` knoop punt: 

`<location xmlns="https://contoso.com">Paris</location>`

> [!IMPORTANT]
>
> Als u in de code weergave werkt, plaatst u het dubbele aanhalings teken (") met behulp van de back slash ( \\ ). 
> U moet bijvoorbeeld escape tekens gebruiken wanneer u een expressie serialiseren als een JSON-teken reeks. 
> Als u echter werkt in de Logic app Designer of de expressie-editor, hoeft u het dubbele aanhalings teken niet te escaperen omdat de back slash-waarde automatisch aan de onderliggende definitie wordt toegevoegd, bijvoorbeeld:
> 
> * Code weergave: `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`
>
> * Expressie-editor: `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`

*Voorbeeld 9*

In het volgende voor beeld 8 wordt in dit voor beeld de XPath-expressie gebruikt `'string(/*[name()="file"]/*[name()="location"])'` om de waarde in het `<location></location>` knoop punt te vinden:

`xpath(xml(body('Http')), 'string(/*[name()="file"]/*[name()="location"])')`

Dit is het resultaat: `Paris`

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [werk stroom definitie taal](../logic-apps/logic-apps-workflow-definition-language.md)
