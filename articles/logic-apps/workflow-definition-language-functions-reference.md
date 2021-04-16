---
title: Naslaghandleiding voor functies in expressies
description: Naslaghandleiding voor functies in expressies voor Azure Logic Apps en Power Automate
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: reference
ms.date: 03/30/2021
ms.openlocfilehash: d2ea08551299d66edd919a828877c134c84ef938
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477771"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>Naslaghandleiding voor het gebruik van functies in expressies voor Azure Logic Apps en Power Automate

Voor werkstroomdefinities [in](../logic-apps/logic-apps-overview.md) Azure Logic Apps en [Power Automate](/flow/getting-started)krijgen sommige [expressies](../logic-apps/logic-apps-workflow-definition-language.md#expressions) hun waarden van runtime-acties die mogelijk nog niet bestaan wanneer uw werkstroom wordt uitgevoerd. Als u wilt verwijzen naar deze waarden of de waarden in deze expressies wilt verwerken, kunt u *functies* gebruiken die worden geleverd door [de definitietaal van de werkstroom.](../logic-apps/logic-apps-workflow-definition-language.md)

> [!NOTE]
> Deze referentiepagina is van toepassing op Azure Logic Apps en Power Automate, maar wordt weergegeven in de Azure Logic Apps documentatie. Hoewel deze pagina specifiek naar logische apps verwijst, werken deze functies voor zowel stromen als logische apps. Zie Voor meer informatie over functies en expressies in Power Automate, [zie Expressies gebruiken in voorwaarden](/flow/use-expressions-in-conditions).

U kunt bijvoorbeeld waarden berekenen met behulp van wiskundige functies, zoals de [functie add(),](../logic-apps/workflow-definition-language-functions-reference.md#add) wanneer u de som van gehele getallen of floats wilt berekenen. Hier volgen andere voorbeeldtaken die u met functies kunt uitvoeren:

| Taak | Functiesyntaxis | Resultaat |
| ---- | --------------- | ------ |
| Een tekenreeks retourneren in kleine letters. | toLower('<*text*>') <p>Bijvoorbeeld: toLower('Hello') | "Hallo" |
| Retourneren van een globally unique identifier (GUID). | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

Als u functies wilt [zoeken op basis van hun algemene doeleinden,](#ordered-by-purpose)bekijkt u de volgende tabellen. Of zie de alfabetische lijst voor gedetailleerde informatie over [elke functie.](#alphabetical-list)

## <a name="functions-in-expressions"></a>Functies in expressies

Om te laten zien hoe u een functie in een expressie gebruikt, laat dit voorbeeld zien hoe u de waarde van de parameter kunt krijgen en die waarde kunt toewijzen aan de eigenschap met behulp van de `customerName` `accountName` functie [parameters()](#parameters) in een expressie:

```json
"accountName": "@parameters('customerName')"
```

Hier zijn enkele andere algemene manieren waarop u functies in expressies kunt gebruiken:

| Taak | Functiesyntaxis in een expressie |
| ---- | -------------------------------- |
| Werk met een item uit door dat item door te geven aan een functie. | " \@ < *functionName*>(<*item*>)" |
| 1. Haal de *waarde van parameterName* op met behulp van de geneste `parameters()` functie. </br>2. Voer werk uit met het resultaat door die waarde door te geven aan *functionName.* | " \@ < *functionName*>(parameters('<*parameterName*>'))" |
| 1. Haal het resultaat op van de geneste functie *functionName*. </br>2. Geef het resultaat door aan de buitenste *functie functionName2.* | " \@ < *functionName2*>(<*functionName*>(<*item*>))" |
| 1. Haal het resultaat op uit *functionName*. </br>2. Gezien het feit dat het resultaat een object met eigenschap *propertyName* is, krijgt u de waarde van die eigenschap. | " \@ < *functionName*>(<*item*>).<*propertyName*>" |
|||

De functie kan `concat()` bijvoorbeeld twee of meer tekenreekswaarden als parameters gebruiken. Met deze functie worden deze tekenreeksen gecombineerd tot één tekenreeks. U kunt letterlijke tekenreeksen doorgeven, bijvoorbeeld 'Hebteen', zodat u een gecombineerde tekenreeks, 'Hebtowen', krijgt:

```json
"customerName": "@concat('Sophia', 'Owen')"
```

U kunt ook tekenreekswaarden uit parameters op halen. In dit voorbeeld wordt de `parameters()` functie in elke parameter en de parameters en `concat()` `firstName` `lastName` gebruikt. Vervolgens geeft u de resulterende tekenreeksen door aan de functie, zodat u een gecombineerde tekenreeks krijgt, bijvoorbeeld `concat()` 'MoetOwen':

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

In beide gevallen wijzen beide voorbeelden het resultaat toe aan de `customerName` eigenschap .

Hier vindt u enkele andere opmerkingen over functies in expressies:

* Functieparameters worden van links naar rechts geëvalueerd.

* In de syntaxis voor parameterdefinities betekent een vraagteken (?) dat wordt weergegeven na een parameter dat de parameter optioneel is. Zie bijvoorbeeld [getFutureTime()](#getFutureTime).

In de volgende secties worden functies georganiseerd op basis van het algemene doel of kunt u in alfabetische volgorde door deze [functies bladeren.](#alphabetical-list)

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Tekenreeksfuncties

Als u wilt werken met tekenreeksen, kunt u deze tekenreeksfuncties en ook enkele [verzamelingsfuncties gebruiken.](#collection-functions) Tekenreeksfuncties werken alleen voor tekenreeksen.

| Tekenreeksfunctie | Taak |
| --------------- | ---- |
| [Concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Combineer twee of meer tekenreeksen en retourner de gecombineerde tekenreeks. |
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Controleer of een tekenreeks eindigt met de opgegeven subtekenreeks. |
| [formatNumber](../logic-apps/workflow-definition-language-functions-reference.md#formatNumber) | Een getal retourneren als een tekenreeks op basis van de opgegeven indeling |
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Genereer een globally unique identifier (GUID) als een tekenreeks. |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Retourneert de beginpositie voor een subtekenreeks. |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Retourneert de beginpositie voor het laatste exemplaar van een subtekenreeks. |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Het aantal items in een tekenreeks of matrix retourneren. |
| [Vervangen](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Vervang een subtekenreeks door de opgegeven tekenreeks en retourneert de bijgewerkte tekenreeks. |
| [Split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Retourneert een matrix die subtekenreeksen bevat, gescheiden door komma's, van een grotere tekenreeks op basis van een opgegeven scheidingsteken in de oorspronkelijke tekenreeks. |
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Controleer of een tekenreeks begint met een specifieke subtekenreeks. |
| [Subtekenreeks](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Retourneert tekens uit een tekenreeks, beginnend vanaf de opgegeven positie. |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Een tekenreeks retourneren in kleine letters. |
| [Toupper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Een tekenreeks retourneren in hoofdletters. |
| [Trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Verwijder vooraan en achter elkaar witruimte uit een tekenreeks en retourneert de bijgewerkte tekenreeks. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Verzamelingsfuncties

Als u wilt werken met verzamelingen, over het algemeen matrices, tekenreeksen en soms woordenlijsten, kunt u deze verzamelingsfuncties gebruiken.

| Verzamelingsfunctie | Taak |
| ------------------- | ---- |
| [Bevat](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Controleer of een verzameling een specifiek item heeft. |
| [leeg](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Controleer of een verzameling leeg is. |
| [Eerste](../logic-apps/workflow-definition-language-functions-reference.md#first) | Het eerste item uit een verzameling retourneren. |
| [Snijpunt](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Retourneert een verzameling die *alleen de* algemene items in de opgegeven verzamelingen bevat. |
| [Item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Wanneer u binnen een herhalende actie over een matrix bent, retourneert u het huidige item in de matrix tijdens de huidige iteratie van de actie. |
| [Join](../logic-apps/workflow-definition-language-functions-reference.md#join) | Retourneert een tekenreeks *met alle* items uit een matrix, gescheiden door het opgegeven teken. |
| [Laatste](../logic-apps/workflow-definition-language-functions-reference.md#last) | Het laatste item uit een verzameling retourneren. |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Het aantal items in een tekenreeks of matrix retourneren. |
| [Overslaan](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Items vooraan in een verzameling verwijderen en alle *andere* items retourneren. |
| [Nemen](../logic-apps/workflow-definition-language-functions-reference.md#take) | Items retourneren vanaf het voorste deel van een verzameling. |
| [Unie](../logic-apps/workflow-definition-language-functions-reference.md#union) | Retourneert een verzameling die *alle* items uit de opgegeven verzamelingen bevat. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Logische vergelijkingsfuncties

Als u wilt werken met voorwaarden, waarden en expressieresultaten wilt vergelijken of verschillende soorten logica wilt evalueren, kunt u deze logische vergelijkingsfuncties gebruiken. Zie de alfabetische lijst voor de volledige naslag over [elke functie.](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)

> [!NOTE]
> Als u logische functies of voorwaarden gebruikt om waarden te vergelijken, worden null-waarden geconverteerd naar lege tekenreekswaarden `""` (). Het gedrag van voorwaarden verschilt wanneer u een lege tekenreeks vergelijkt in plaats van een null-waarde. Zie de functie [string() voor meer informatie.](#string) 

| Logische vergelijkingsfunctie | Taak |
| --------------------------- | ---- |
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) | Controleer of alle expressies waar zijn. |
| [is gelijk aan](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Controleer of beide waarden gelijkwaardig zijn. |
| [Groter](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Controleer of de eerste waarde groter is dan de tweede waarde. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Controleer of de eerste waarde groter is dan of gelijk is aan de tweede waarde. |
| [Als](../logic-apps/workflow-definition-language-functions-reference.md#if) | Controleer of een expressie waar of onwaar is. Retourneert op basis van het resultaat een opgegeven waarde. |
| [Minder](../logic-apps/workflow-definition-language-functions-reference.md#less) | Controleer of de eerste waarde kleiner is dan de tweede waarde. |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Controleer of de eerste waarde kleiner is dan of gelijk is aan de tweede waarde. |
| [Niet](../logic-apps/workflow-definition-language-functions-reference.md#not) | Controleer of een expressie onwaar is. |
| [or](../logic-apps/workflow-definition-language-functions-reference.md#or) | Controleer of ten minste één expressie waar is. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Conversiefuncties

Als u het type of de indeling van een waarde wilt wijzigen, kunt u deze conversiefuncties gebruiken. U kunt bijvoorbeeld een waarde van een Booleaanse waarde wijzigen in een geheel getal. Zie Inhoudstypen verwerken Logic Apps meer informatie over hoe inhoudstypen tijdens de conversie [worden verwerkt.](../logic-apps/logic-apps-content-type.md) Zie de alfabetische lijst voor de volledige naslag over [elke functie.](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)

> [!NOTE]
> Azure Logic Apps voert automatisch of impliciet base64-codering en -decoderen uit, zodat u deze conversies niet handmatig hoeft uit te voeren met behulp van de coderings- en decodeerfuncties. Als u deze functies echter toch in de ontwerpfunctie gebruikt, kan er onverwacht renderinggedrag in de ontwerpfunctie worden ervaren. Dit gedrag is alleen van invloed op de zichtbaarheid van de functies en niet op het effect ervan, tenzij u de parameterwaarden van de functies bewerkt, waardoor de functies en hun effecten uit uw code worden verwijderd. Zie Impliciete [gegevenstypeconversies voor meer informatie.](#implicit-data-conversions)

| Conversiefunctie | Taak |
| ------------------- | ---- |
| [matrix](../logic-apps/workflow-definition-language-functions-reference.md#array) | Retourneert een matrix van één opgegeven invoer. Zie [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray)voor meerdere invoer. |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Retourneerde de met base64 gecodeerde versie voor een tekenreeks. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Retourneerde de binaire versie voor een tekenreeks met base64-codering. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Retourneerde de tekenreeksversie voor een tekenreeks met base64-codering. |
| [Binaire](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Retourneert de binaire versie voor een invoerwaarde. |
| [booleaans](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Retourneert de Booleaanse versie voor een invoerwaarde. |
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Retourneert een matrix van meerdere invoer. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Retourneert de gegevens-URI voor een invoerwaarde. |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Retourneren van de binaire versie voor een gegevens-URI. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Retourneren van de tekenreeksversie voor een gegevens-URI. |
| [decoderenBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Retourneerde de tekenreeksversie voor een tekenreeks met base64-codering. |
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Retourneren van de binaire versie voor een gegevens-URI. |
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Retourneert een tekenreeks die escape-tekens vervangt door gedecodeerde versies. |
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Retourneert een tekenreeks die URL-onveilige tekens vervangt door escape-tekens. |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Retourneert een drijvende-puntnummer voor een invoerwaarde. |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Retourner de geheel getal-versie voor een tekenreeks. |
| [Json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Retourneert JavaScript Object Notation (JSON)-typewaarde of -object voor een tekenreeks of XML. |
| [tekenreeks](../logic-apps/workflow-definition-language-functions-reference.md#string) | Retourneert de tekenreeksversie voor een invoerwaarde. |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Retourneert de met URI gecodeerde versie voor een invoerwaarde door URL-onveilige tekens te vervangen door escape-tekens. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Retourneren van de binaire versie voor een tekenreeks met URI-codering. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Retourneren van de tekenreeksversie voor een tekenreeks met URI-codering. |
| [Xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Retourneert de XML-versie voor een tekenreeks. |
|||

<a name="implicit-data-conversions"></a>

## <a name="implicit-data-type-conversions"></a>Impliciete gegevenstypeconversies

Azure Logic Apps automatisch of impliciet converteert tussen bepaalde gegevenstypen, zodat u deze conversies niet handmatig hoeft uit te voeren. Als u bijvoorbeeld niet-tekenreekswaarden gebruikt waarbij tekenreeksen worden verwacht als invoer, worden Logic Apps niet-tekenreekswaarden automatisch ge converteert naar tekenreeksen.

Stel bijvoorbeeld dat een trigger een numerieke waarde retourneert als uitvoer:

`triggerBody()?['123']`

Als u deze numerieke uitvoer gebruikt waarbij tekenreeksinvoer wordt verwacht, zoals een URL, converteert Logic Apps de waarde automatisch naar een tekenreeks met behulp van de notatie accolades ( `{}` ) :

`@{triggerBody()?['123']}`

<a name="base64-encoding-decoding"></a>

### <a name="base64-encoding-and-decoding"></a>Base64-codering en -decoderen

Logic Apps base64-codering of -decoderen automatisch of impliciet uitvoert, zodat u deze conversies niet handmatig hoeft uit te voeren met behulp van de bijbehorende functies:

* `base64(<value>)`
* `base64ToBinary(<value>)`
* `base64ToString(<value>)`
* `base64(decodeDataUri(<value>))`
* `concat('data:;base64,',<value>)`
* `concat('data:,',encodeUriComponent(<value>))`
* `decodeDataUri(<value>)`

> [!NOTE]
> Als u handmatig een van deze functies aan uw werkstroom toevoegt via de ontwerper van logische apps, bijvoorbeeld door de expressie-editor te gebruiken, weg te navigeren van de ontwerpfunctie en terug te keren naar de ontwerpfunctie, verdwijnt de functie uit de ontwerpfunctie, waardoor alleen de parameterwaarden worden achter gelaten. Dit gedrag teert ook als u een trigger of actie selecteert die gebruikmaakt van deze functie zonder de parameterwaarden van de functie te bewerken. Dit resultaat is alleen van invloed op de zichtbaarheid van de functie en niet op het effect. In de codeweergave wordt de functie niet beïnvloed. Als u echter de parameterwaarden van de functie bewerkt, worden de functie en het effect ervan beide verwijderd uit de codeweergave, waardoor alleen de parameterwaarden van de functie achterblijven.

<a name="math-functions"></a>

## <a name="math-functions"></a>Wiskundige functies

Als u met gehele getallen en floats wilt werken, kunt u deze wiskundige functies gebruiken.
Zie de alfabetische lijst voor de volledige naslag over [elke functie.](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)

| Wiskundige functie | Taak |
| ------------- | ---- |
| [add](../logic-apps/workflow-definition-language-functions-reference.md#add) | Het resultaat van het toevoegen van twee getallen retourneren. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Retourneert het resultaat van het delen van twee getallen. |
| [Max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Retourneert de hoogste waarde van een set getallen of een matrix. |
| [min.](../logic-apps/workflow-definition-language-functions-reference.md#min) | Retourneert de laagste waarde van een set getallen of een matrix. |
| [Mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Retourneert het restant van het delen van twee getallen. |
| [Mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Retourneert het product door twee getallen te vermenigvuldigen. |
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Retourneert een willekeurig geheel getal uit een opgegeven bereik. |
| [Bereik](../logic-apps/workflow-definition-language-functions-reference.md#range) | Retourneert een matrix met gehele getallen die begint met een opgegeven geheel getal. |
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Retourneert het resultaat van het aftrekken van het tweede getal van het eerste getal. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Datum- en tijdfuncties

Als u wilt werken met datums en tijden, kunt u deze datum- en tijdfuncties gebruiken.
Zie de alfabetische lijst voor de volledige naslag over [elke functie.](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)

| Datum- of tijdfunctie | Taak |
| --------------------- | ---- |
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Voeg een aantal dagen toe aan een tijdstempel. |
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Voeg een aantal uren toe aan een tijdstempel. |
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Voeg een aantal minuten toe aan een tijdstempel. |
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Voeg een aantal seconden toe aan een tijdstempel. |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Voeg een aantal tijdseenheden toe aan een tijdstempel. Zie ook [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). |
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Converteert een tijdstempel van Universal Time Coordinated (UTC) naar de doeltijdzone. |
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Converteert een tijdstempel van de brontijdzone naar de doeltijdzone. |
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Convert a timestamp from the source time zone to Universal Time Coordinated (UTC). |
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Retourneerde de dag van het maandonderdeel uit een tijdstempel. |
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Retourneerde de dag van het weekonderdeel uit een tijdstempel. |
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Retourneerde de dag van het jaar-onderdeel van een tijdstempel. |
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | De datum van een tijdstempel retourneren. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Retourneert de huidige tijdstempel plus de opgegeven tijdeenheden. Zie ook [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Retourneert de huidige tijdstempel min de opgegeven tijdseenheden. Zie ook [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Retourneerde het begin van de dag voor een tijdstempel. |
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Retourneerde het begin van het uur voor een tijdstempel. |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Retourneerde het begin van de maand voor een tijdstempel. |
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Een aantal tijdseenheden van een tijdstempel aftrekken. Zie ook [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). |
| [Teken](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | `ticks`Retourneert de eigenschapswaarde voor een opgegeven tijdstempel. |
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Retourneerde de huidige tijdstempel als een tekenreeks. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>Werkstroomfuncties

Deze werkstroomfuncties kunnen u helpen:

* Meer informatie over een werkstroom-exemplaar tijdens run time.
* Werk met de invoer die wordt gebruikt voor het instantiëren van logische apps of stromen.
* Verwijs naar de uitvoer van triggers en acties.

U kunt bijvoorbeeld verwijzen naar de uitvoer van één actie en die gegevens gebruiken in een latere actie.
Zie de alfabetische lijst voor de volledige naslag over [elke functie.](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)

| Werkstroomfunctie | Taak |
| ----------------- | ---- |
| [action](../logic-apps/workflow-definition-language-functions-reference.md#action) | Retourneert de uitvoer van de huidige actie tijdens runtime of waarden van andere JSON-naam-en-waardeparen. Zie ook [acties](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | De uitvoer van een actie `body` retourneren tijdens runtime. Zie ook [de body](../logic-apps/workflow-definition-language-functions-reference.md#body). |
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | De uitvoer van een actie retourneren tijdens runtime. Zie [uitvoer en](../logic-apps/workflow-definition-language-functions-reference.md#outputs) [acties.](../logic-apps/workflow-definition-language-functions-reference.md#actions) |
| [Acties](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Retourneert de uitvoer van een actie tijdens runtime of waarden van andere JSON-naam-en-waardeparen. Zie ook [actie](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [Lichaam](#body) | De uitvoer van een actie `body` retourneren tijdens runtime. Zie ook [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Maak een matrix met de waarden die overeenkomen met een sleutelnaam in de uitvoer van de actie *form-data* of *form-encoded.* |
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Retourneert één waarde die overeenkomt met een sleutelnaam in de uitvoer van een actie met *form-data* of *form-encoded*. |
| [Item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Wanneer u binnen een herhalende actie over een matrix bent, retourneert u het huidige item in de matrix tijdens de huidige iteratie van de actie. |
| [Items](../logic-apps/workflow-definition-language-functions-reference.md#items) | Wanneer u binnen een Foreach- of Until-lus bent, retourneert u het huidige item uit de opgegeven lus.|
| [iterationIndexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | In een Until-lus retourneert u de indexwaarde voor de huidige iteratie. U kunt deze functie gebruiken binnen geneste Until-lussen. |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Retourneren de 'callback-URL' die een trigger of actie aanroept. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | De body retourneren voor een specifiek onderdeel in de uitvoer van een actie die meerdere delen heeft. |
| [Uitgangen](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | De uitvoer van een actie retourneren tijdens runtime. |
| [Parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Retourneert de waarde voor een parameter die wordt beschreven in uw werkstroomdefinitie. |
| [Resultaat](../logic-apps/workflow-definition-language-functions-reference.md#result) | Retourneert de invoer en uitvoer van de acties op het hoogste niveau binnen de opgegeven scoped actie, zoals `For_each` `Until` , en `Scope` . |
| [activeren](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Retourneert de uitvoer van een trigger tijdens runtime of van andere JSON-naam-en-waardeparen. Zie ook [triggerOutputs](#triggerOutputs) en [triggerBody.](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | De uitvoer van een trigger `body` retourneren tijdens runtime. Zie [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Retourneert één waarde die overeenkomt met een sleutelnaam in uitvoer van *formuliergegevens* of met formulier *gecodeerde* triggers. |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | De body retourneren voor een specifiek onderdeel in de uitvoer van een trigger met meerdere onderdelen. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Maak een matrix waarvan de waarden overeenkomen met een sleutelnaam in *de* uitvoer van een *trigger* met formuliergegevens of formuliercode. |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Retourneert de uitvoer van een trigger tijdens runtime of waarden van andere JSON-naam-en-waardeparen. Zie [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [Variabelen](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Retourneert de waarde voor een opgegeven variabele. |
| [Workflow](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Retourneert alle details over de werkstroom zelf tijdens de run time. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>URI-parseringsfuncties

Als u wilt werken met URI's (Uniform Resource Identifiers) en verschillende eigenschapswaarden voor deze URI's wilt krijgen, kunt u deze URI-parseringsfuncties gebruiken.
Zie de alfabetische lijst voor de volledige naslag over [elke functie.](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)

| URI-parseringsfunctie | Taak |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | `host`Retourneert de waarde voor een uniform resource identifier (URI). |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | `path`Retourneert de waarde voor een uniform resource identifier (URI). |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | `path`Retourneert de waarden en voor een uniform resource identifier `query` (URI). |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | `port`Retourneert de waarde voor een uniform resource identifier (URI). |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | `query`Retourneert de waarde voor een uniform resource identifier (URI). |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | `scheme`Retourneert de waarde voor een uniform resource identifier (URI). |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Manipulatiefuncties: JSON & XML

Als u wilt werken met JSON-objecten en XML-knooppunten, kunt u deze manipulatiefuncties gebruiken.
Zie de alfabetische lijst voor de volledige naslag over [elke functie.](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)

| Manipulatiefunctie | Taak |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Voeg een eigenschap en de waarde ervan of naam-waardepaar toe aan een JSON-object en retourneert het bijgewerkte object. |
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Retourneert de eerste niet-null-waarde van een of meer parameters. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Verwijder een eigenschap uit een JSON-object en retourneert het bijgewerkte object. |
| [Eigenschapinstellen](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Stel de waarde voor de eigenschap van een JSON-object in en retourneert het bijgewerkte object. |
| [Xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Controleer XML op knooppunten of waarden die overeenkomen met een XPath-expressie (XML Path Language) en retourneert de overeenkomende knooppunten of waarden. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Alle functies - alfabetische lijst

In deze sectie worden alle beschikbare functies in alfabetische volgorde vermeld.

<a name="action"></a>

### <a name="action"></a>action

*Retourneert de* uitvoer van de huidige actie tijdens runtime, of waarden van andere JSON-naam-en-waardeparen, die u kunt toewijzen aan een expressie.
Deze functie verwijst standaard naar het hele actieobject, maar u kunt eventueel een eigenschap opgeven waarvan u de waarde wilt.
Zie ook [actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

U kunt de `action()` functie alleen op deze plaatsen gebruiken:

* De `unsubscribe` eigenschap voor een webhookactie, zodat u het resultaat van de oorspronkelijke aanvraag kunt `subscribe` openen
* De `trackedProperties` eigenschap voor een actie
* De `do-until` lusvoorwaarde voor een actie

```
action()
action().outputs.body.<property>
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Eigenschap*> | Nee | Tekenreeks | De naam voor de eigenschap van het actieobject waarvan u wilt **dat:** naam , **startTime,** **endTime** **,** **invoert**, uitvoer , **status**, **code,** **trackingId** en **clientTrackingId.** In de Azure Portal kunt u deze eigenschappen vinden door de details van een specifieke run history te bekijken. Zie voor meer informatie REST API [- Werkstroomrunacties.](/rest/api/logic/workflowrunactions/get) |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| <*action-output*> | Tekenreeks | De uitvoer van de huidige actie of eigenschap |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

De uitvoer van een actie `body` retourneren tijdens runtime.
Steno voor `actions('<actionName>').outputs.body` .
Zie [body()](#body) en [actions()](#actions).

```
actionBody('<actionName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De naam voor de uitvoer van `body` de actie die u wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| <*action-body-output*> | Tekenreeks | De `body` uitvoer van de opgegeven actie |
||||

*Voorbeeld*

In dit voorbeeld wordt de `body` uitvoer van de Twitter-actie : `Get user`

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

De uitvoer van een actie retourneren tijdens runtime.  en is steno voor `actions('<actionName>').outputs` . Zie [actions()](#actions). De functie wordt in de ontwerpfunctie van de logische app opgelost, dus overweeg het `actionOutputs()` gebruik van `outputs()` [outputs()](#outputs)in plaats van `actionOutputs()` . Hoewel beide functies op dezelfde manier werken, `outputs()` heeft de voorkeur.

```
actionOutputs('<actionName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De naam voor de uitvoer van de actie die u wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| <*Output*> | Tekenreeks | De uitvoer van de opgegeven actie |
||||

*Voorbeeld*

In dit voorbeeld wordt de uitvoer van de Twitter-actie : `Get user`

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

Retourneert de uitvoer van een actie tijdens runtime, of waarden van andere JSON-naam-en-waardeparen, die u kunt toewijzen aan een expressie. De functie verwijst standaard naar het hele actie-object, maar u kunt eventueel een eigenschap opgeven waarvan u de waarde wilt.
Zie [actionBody()](#actionBody), [actionOutputs()](#actionOutputs)en [body() voor verkorte versies.](#body)
Zie [action() voor de huidige actie.](#action)

> [!TIP]
> De `actions()` functie retourneert uitvoer als een tekenreeks. Als u met een geretourneerde waarde als JSON-object wilt werken, moet u eerst de tekenreekswaarde converteren. U kunt de tekenreekswaarde omzetten in een JSON-object met behulp van [de actie JSON parseren.](logic-apps-perform-data-operations.md#parse-json-action)

> [!NOTE]
> Voorheen kon u de functie of het element gebruiken bij het opgeven dat een actie werd uitgevoerd op basis `actions()` van de uitvoer van een andere `conditions` actie. Als u echter expliciet afhankelijkheden tussen acties wilt declaren, moet u nu de eigenschap van de afhankelijke actie `runAfter` gebruiken.
> Zie Catch and handle failures with the runAfter property (Fouten ondervangen en afhandelen met de `runAfter` [eigenschap runAfter)](../logic-apps/logic-apps-workflow-definition-language.md)voor meer informatie over de eigenschap .

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De naam voor het actieobject waarvan u de uitvoer wilt gebruiken  |
| <*Eigenschap*> | Nee | Tekenreeks | De naam voor de eigenschap van het actieobject waarvan u wilt **dat:** naam , **startTime,** **endTime** **,** **invoert**, uitvoer , **status**, **code,** **trackingId** en **clientTrackingId.** In de Azure Portal kunt u deze eigenschappen vinden door de details van een specifieke run history te bekijken. Zie voor meer informatie REST API [- Werkstroomrunacties.](/rest/api/logic/workflowrunactions/get) |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| <*action-output*> | Tekenreeks | De uitvoer van de opgegeven actie of eigenschap |
||||

*Voorbeeld*

In dit voorbeeld wordt de `status` eigenschapswaarde van de Twitter-actie tijdens `Get user` runtime:

```
actions('Get_user').outputs.body.status
```

En retourneert dit resultaat: `"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

Het resultaat retourneren van het toevoegen van twee getallen.

```
add(<summand_1>, <summand_2>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*summand_1*> <*summand_2*> | Ja | Geheel getal, Float of gemengd | De toe te voegen getallen |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| <*result-sum*> | Geheel getal of float | Het resultaat van het toevoegen van de opgegeven getallen |
||||

*Voorbeeld*

In dit voorbeeld worden de opgegeven getallen toegevoegd:

```
add(1, 1.5)
```

En retourneert dit resultaat: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Voeg een aantal dagen toe aan een tijdstempel.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die het tijdstempel bevat |
| <*Dagen*> | Ja | Geheel getal | Het positieve of negatieve aantal dagen dat moet worden toevoegen |
| <*Formaat*> | Nee | Tekenreeks | Eén [opmaakopwijzer of](/dotnet/standard/base-types/standard-date-and-time-format-strings) een [patroon voor aangepaste notatie.](/dotnet/standard/base-types/custom-date-and-time-format-strings) De standaardindeling voor de tijdstempel is ['o'](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Tekenreeks | Het tijdstempel plus het opgegeven aantal dagen  |
||||

*Voorbeeld 1*

In dit voorbeeld worden 10 dagen toegevoegd aan het opgegeven tijdstempel:

```
addDays('2018-03-15T00:00:00Z', 10)
```

En retourneert dit resultaat: `"2018-03-25T00:00:00.0000000Z"`

*Voorbeeld 2*

In dit voorbeeld worden vijf dagen afgetrokken van de opgegeven tijdstempel:

```
addDays('2018-03-15T00:00:00Z', -5)
```

En retourneert dit resultaat: `"2018-03-10T00:00:00.0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Voeg een aantal uren toe aan een tijdstempel.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die het tijdstempel bevat |
| <*Uur*> | Ja | Geheel getal | Het positieve of negatieve aantal uren dat moet worden toevoegen |
| <*Formaat*> | Nee | Tekenreeks | Eén [opmaakopwijzer of](/dotnet/standard/base-types/standard-date-and-time-format-strings) een [patroon voor aangepaste notatie.](/dotnet/standard/base-types/custom-date-and-time-format-strings) De standaardindeling voor de tijdstempel is ['o'](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Tekenreeks | Het tijdstempel plus het opgegeven aantal uren  |
||||

*Voorbeeld 1*

In dit voorbeeld wordt 10 uur toegevoegd aan het opgegeven tijdstempel:

```
addHours('2018-03-15T00:00:00Z', 10)
```

En retourneert dit resultaat: '"2018-03-15T10:00:00.0000000Z"

*Voorbeeld 2*

In dit voorbeeld worden vijf uur afgetrokken van de opgegeven tijdstempel:

```
addHours('2018-03-15T15:00:00Z', -5)
```

En retourneert dit resultaat: `"2018-03-15T10:00:00.0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Voeg een aantal minuten toe aan een tijdstempel.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die het tijdstempel bevat |
| <*Minuten*> | Ja | Geheel getal | Het positieve of negatieve aantal minuten dat moet worden toevoegen |
| <*Formaat*> | Nee | Tekenreeks | Eén [opmaakopwijzer of](/dotnet/standard/base-types/standard-date-and-time-format-strings) een [patroon voor aangepaste notatie.](/dotnet/standard/base-types/custom-date-and-time-format-strings) De standaardindeling voor de tijdstempel is ['o'](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Tekenreeks | Het tijdstempel plus het opgegeven aantal minuten |
||||

*Voorbeeld 1*

In dit voorbeeld wordt 10 minuten toegevoegd aan het opgegeven tijdstempel:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

En retourneert dit resultaat: `"2018-03-15T00:20:00.0000000Z"`

*Voorbeeld 2*

In dit voorbeeld worden vijf minuten afgetrokken van de opgegeven tijdstempel:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

En retourneert dit resultaat: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Voeg een eigenschap en de waarde ervan of naam-waardepaar toe aan een JSON-object en retourneert het bijgewerkte object. Als de eigenschap al bestaat tijdens runtime, mislukt de functie en wordt er een foutmelding weergegeven.

```
addProperty(<object>, '<property>', <value>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Object*> | Ja | Object | Het JSON-object waar u een eigenschap wilt toevoegen |
| <*Eigenschap*> | Ja | Tekenreeks | De naam voor de eigenschap die moet worden toevoegen |
| <*Waarde*> | Ja | Alle | De waarde voor de eigenschap |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | Het bijgewerkte JSON-object met de opgegeven eigenschap |
||||

Als u een bovenliggende eigenschap wilt toevoegen aan een bestaande eigenschap, gebruikt u de `setProperty()` functie , niet de functie `addProperty()` . Anders retourneert de functie alleen het onderliggende object als uitvoer.

```
setProperty(<object>['<parent-property>'], '<parent-property>', addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Object*> | Ja | Object | Het JSON-object waar u een eigenschap wilt toevoegen |
| <*bovenliggende eigenschap*> | Ja | Tekenreeks | De naam voor de bovenliggende eigenschap waar u de onderliggende eigenschap wilt toevoegen |
| <*onderliggende eigenschap*> | Ja | Tekenreeks | De naam voor de onderliggende eigenschap die moet worden toevoegen |
| <*Waarde*> | Ja | Alle | De waarde die moet worden ingesteld voor de opgegeven eigenschap |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | Het bijgewerkte JSON-object waarvan u de eigenschap hebt ingesteld |
||||

*Voorbeeld 1*

In dit voorbeeld wordt de eigenschap toegevoegd aan een JSON-object, dat wordt geconverteerd van een tekenreeks naar JSON met behulp van de `middleName` [functie JSON().](#json) Het object bevat al de `firstName` eigenschappen `surName` en . De functie wijst de opgegeven waarde toe aan de nieuwe eigenschap en retourneert het bijgewerkte object:

```
addProperty(json('{ "firstName": "Sophia", "lastName": "Owen" }'), 'middleName', 'Anne')
```

Dit is het huidige JSON-object:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Dit is het bijgewerkte JSON-object:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

*Voorbeeld 2*

In dit voorbeeld wordt de onderliggende eigenschap toegevoegd aan de bestaande eigenschap in een JSON-object, dat wordt geconverteerd van een tekenreeks naar JSON met behulp van de `middleName` `customerName` functie [JSON().](#json) De functie wijst de opgegeven waarde toe aan de nieuwe eigenschap en retourneert het bijgewerkte object:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', addProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'middleName', 'Anne'))
```

Dit is het huidige JSON-object:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

Dit is het bijgewerkte JSON-object:

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

Voeg een aantal seconden toe aan een tijdstempel.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die het tijdstempel bevat |
| <*Seconden*> | Ja | Geheel getal | Het positieve of negatieve aantal seconden dat moet worden toevoegen |
| <*Formaat*> | Nee | Tekenreeks | Eén [opmaakopwijzer of](/dotnet/standard/base-types/standard-date-and-time-format-strings) een [patroon voor aangepaste notatie.](/dotnet/standard/base-types/custom-date-and-time-format-strings) De standaardindeling voor de tijdstempel is ['o'](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Tekenreeks | Het tijdstempel plus het opgegeven aantal seconden  |
||||

*Voorbeeld 1*

In dit voorbeeld wordt 10 seconden toegevoegd aan het opgegeven tijdstempel:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

En retourneert dit resultaat: `"2018-03-15T00:00:10.0000000Z"`

*Voorbeeld 2*

In dit voorbeeld wordt vijf seconden afgetrokken van de opgegeven tijdstempel:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

En retourneert dit resultaat: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Voeg een aantal tijdseenheden toe aan een tijdstempel.
Zie ook [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die het tijdstempel bevat |
| <*Interval*> | Ja | Geheel getal | Het aantal opgegeven tijdeenheden dat moet worden toevoegen |
| <*timeUnit*> | Ja | Tekenreeks | De tijdseenheid die moet worden gebruikt met *interval:*"Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*Formaat*> | Nee | Tekenreeks | Eén [opmaakopwijzer of](/dotnet/standard/base-types/standard-date-and-time-format-strings) een [patroon voor aangepaste notatie.](/dotnet/standard/base-types/custom-date-and-time-format-strings) De standaardindeling voor de tijdstempel is ['o'](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Tekenreeks | Het tijdstempel plus het opgegeven aantal tijdeenheden  |
||||

*Voorbeeld 1*

In dit voorbeeld wordt één dag toegevoegd aan de opgegeven tijdstempel:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

En retourneert dit resultaat: `"2018-01-02T00:00:00.0000000Z"`

*Voorbeeld 2*

In dit voorbeeld wordt één dag toegevoegd aan het opgegeven tijdstempel:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

En retourneert het resultaat met de optionele indeling 'D': `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>en

Controleer of alle expressies waar zijn.
Retourneert true wanneer alle expressies true zijn, of retourneert false wanneer ten minste één expressie onwaar is.

```
and(<expression1>, <expression2>, ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | Ja | Booleaans | De expressies die moeten worden controleren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| waar of onwaar | Booleaans | Retourneert true wanneer alle expressies waar zijn. Retourneren onwaar wanneer ten minste één expressie onwaar is. |
||||

*Voorbeeld 1*

Deze voorbeelden controleren of de opgegeven Booleaanse waarden allemaal waar zijn:

```
and(true, true)
and(false, true)
and(false, false)
```

En retourneert deze resultaten:

* Eerste voorbeeld: Beide expressies zijn true, dus retourneert `true` .
* Tweede voorbeeld: Eén expressie is onwaar, dus retourneert `false` .
* Derde voorbeeld: Beide expressies zijn onwaar, dus retourneert `false` .

*Voorbeeld 2*

Deze voorbeelden controleren of de opgegeven expressies allemaal waar zijn:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

En retourneert deze resultaten:

* Eerste voorbeeld: Beide expressies zijn true, dus retourneert `true` .
* Tweede voorbeeld: Eén expressie is onwaar, dus retourneert `false` .
* Derde voorbeeld: Beide expressies zijn onwaar, dus retourneert `false` .

<a name="array"></a>

### <a name="array"></a>matrix

Retourneert een matrix op basis van één opgegeven invoer.
Zie [createArray() voor meerdere invoer.](#createArray)

```
array('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De tekenreeks voor het maken van een matrix |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*waarde*>] | Matrix | Een matrix die de opgegeven invoer bevat |
||||

*Voorbeeld*

In dit voorbeeld wordt een matrix gemaakt van de 'hallo'-tekenreeks:

```
array('hello')
```

En retourneert dit resultaat: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Retourneerde de met base64 gecodeerde versie voor een tekenreeks.

> [!NOTE]
> Azure Logic Apps voert automatisch of impliciet base64-codering en -decoderen uit, zodat u deze conversies niet handmatig hoeft uit te voeren met behulp van de coderings- en decodeerfuncties. Als u deze functies echter toch gebruikt, kunt u onverwacht renderinggedrag ervaren in de ontwerpfunctie. Dit gedrag is alleen van invloed op de zichtbaarheid van de functies en niet op het effect ervan, tenzij u de parameterwaarden van de functies bewerkt, waardoor de functies en hun effecten uit uw code worden verwijderd. Zie [Base64-codering en -decoderen](#base64-encoding-decoding)voor meer informatie.

```
base64('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De invoerreeks |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*base64-tekenreeks*> | Tekenreeks | De met base64 gecodeerde versie voor de invoerreeks |
||||

*Voorbeeld*

In dit voorbeeld wordt de hello-tekenreeks ge converteerd naar een tekenreeks met base64-codering:

```
base64('hello')
```

En retourneert dit resultaat: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Retourneerde de binaire versie voor een tekenreeks met base64-codering.

> [!NOTE]
> Azure Logic Apps wordt base64-codering en -decoderen automatisch of impliciet uitgevoerd, zodat u deze conversies niet handmatig hoeft uit te voeren met behulp van de coderings- en decodeerfuncties. Als u deze functies echter toch in de ontwerpfunctie gebruikt, kan er onverwacht renderinggedrag in de ontwerpfunctie worden ervaren. Dit gedrag is alleen van invloed op de zichtbaarheid van de functies en niet op het effect ervan, tenzij u de parameterwaarden van de functies bewerkt, waardoor de functies en hun effecten uit uw code worden verwijderd. Zie [Base64-codering en -decoderen voor meer informatie.](#base64-encoding-decoding)

```
base64ToBinary('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De met base64 gecodeerde tekenreeks om te converteren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*binary-for-base64-string*> | Tekenreeks | De binaire versie voor de met base64 gecodeerde tekenreeks |
||||

*Voorbeeld*

In dit voorbeeld wordt de met base64 gecodeerde tekenreeks 'aGVsbG8=' ge converteerd naar een binaire tekenreeks:

```
base64ToBinary('aGVsbG8=')
```

En retourneert dit resultaat:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Retourneert de tekenreeksversie voor een met base64 gecodeerde tekenreeks, en decodeert de base64-tekenreeks effectief. Gebruik deze functie in plaats [van decoderenBase64(),](#decodeBase64)die is afgeschaft.

> [!NOTE]
> Azure Logic Apps wordt base64-codering en -decoderen automatisch of impliciet uitgevoerd, zodat u deze conversies niet handmatig hoeft uit te voeren met behulp van de coderings- en decodeerfuncties. Als u deze functies echter toch in de ontwerpfunctie gebruikt, kan er onverwacht renderinggedrag in de ontwerpfunctie worden ervaren. Dit gedrag is alleen van invloed op de zichtbaarheid van de functies en niet op het effect ervan, tenzij u de parameterwaarden van de functies bewerkt, waardoor de functies en hun effecten uit uw code worden verwijderd. Zie [Base64-codering en -decoderen voor meer informatie.](#base64-encoding-decoding)

```
base64ToString('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De met base64 gecodeerde tekenreeks die moet worden gedecodeerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | Tekenreeks | De tekenreeksversie voor een tekenreeks met base64-codering |
||||

*Voorbeeld*

In dit voorbeeld wordt de met base64 gecodeerde tekenreeks 'aGVsbG8=' ge converteerd naar slechts een tekenreeks:

```
base64ToString('aGVsbG8=')
```

En retourneert dit resultaat: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binair

De binaire versie voor een tekenreeks retourneren.

```
binary('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De tekenreeks die u wilt converteren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*binary-for-input-value*> | Tekenreeks | De binaire versie voor de opgegeven tekenreeks |
||||

*Voorbeeld*

In dit voorbeeld wordt de 'hallo'-tekenreeks ge converteert naar een binaire tekenreeks:

```
binary('hello')
```

En retourneert dit resultaat:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>body

De uitvoer van een actie `body` retourneren tijdens runtime. Steno voor `actions('<actionName>').outputs.body` . Zie [actionBody()](#actionBody) en [actions()](#actions).

```
body('<actionName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De naam voor de uitvoer van `body` de actie die u wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| <*action-body-output*> | Tekenreeks | De `body` uitvoer van de opgegeven actie |
||||

*Voorbeeld*

In dit voorbeeld wordt de `body` uitvoer van de `Get user` Twitter-actie:

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
| <*Waarde*> | Ja | Alle | De waarde die moet worden ge converteert naar Booleaanse waarde. |
|||||

Als u gebruikt met een -object, moet de waarde van het object een tekenreeks of geheel getal zijn dat `bool()` kan worden geconverteerd naar booleaanse waarde.

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| `true` of `false` | Booleaans | De Booleaanse versie van de opgegeven waarde. |
||||

*Uitvoerwaarden*

In deze voorbeelden worden de verschillende ondersteunde typen invoer voor `bool()` :

| Invoerwaarde | Type | Retourwaarde |
| ----------- | ---------- | ---------------------- |
| `bool(1)` | Geheel getal | `true` |
| `bool(0)` | Geheel getal    | `false` |
| `bool(-1)` | Geheel getal | `true` |
| `bool('true')` | Tekenreeks | `true` |
| `bool('false')` | Tekenreeks | `false` |

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

Retourneert de eerste niet-null-waarde van een of meer parameters.
Lege tekenreeksen, lege matrices en lege objecten zijn niet null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>, ... | Ja | Elke, kan typen combineren | Een of meer items om te controleren op null |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*first-non-null-item*> | Alle | Het eerste item of de waarde die niet null is. Als alle parameters null zijn, retourneert deze functie null. |
||||

*Voorbeeld*

Deze voorbeelden retourneren de eerste niet-null-waarde van de opgegeven waarden, of null wanneer alle waarden null zijn:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

En retourneert deze resultaten:

* Eerste voorbeeld: `true`
* Tweede voorbeeld: `"hello"`
* Derde voorbeeld: `null`

<a name="concat"></a>

### <a name="concat"></a>Concat

Combineer twee of meer tekenreeksen en retourner de gecombineerde tekenreeks.

> [!NOTE]
> Azure Logic Apps voert automatisch of impliciet base64-codering en -decoderen uit, zodat u deze conversies niet handmatig hoeft uit te voeren wanneer u de functie gebruikt met gegevens die moeten worden gecodeerd of `concat()` gedecodeerd:
> 
> * `concat('data:;base64,',<value>)`
> * `concat('data:,',encodeUriComponent(<value>))`
> 
> Als u deze functie echter toch in de ontwerpfunctie gebruikt, kan er onverwacht renderinggedrag in de ontwerpfunctie worden ervaren. Dit gedrag is alleen van invloed op de zichtbaarheid van de functie en niet op het effect, tenzij u de parameterwaarden van de functie bewerkt, waardoor de functie en het effect uit uw code worden verwijderd. 
> Zie [Base64-codering en -decoderen](#base64-encoding-decoding)voor meer informatie.

```
concat('<text1>', '<text2>', ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | Ja | Tekenreeks | Ten minste twee tekenreeksen om te combineren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*text1text2...*> | Tekenreeks | De tekenreeks die is gemaakt op basis van de gecombineerde invoerreeksen |
||||

*Voorbeeld*

In dit voorbeeld worden de tekenreeksen 'Hallo' en 'Wereld' gecombineerd:

```
concat('Hello', 'World')
```

En retourneert dit resultaat: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>bevat

Controleer of een verzameling een specifiek item heeft. Retourner true wanneer het item wordt gevonden of retourner false wanneer het niet wordt gevonden. Deze functie is casegevoelig.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Deze functie werkt specifiek voor deze verzamelingstypen:

* Een *tekenreeks* om een *subtekenreeks te vinden*
* Een *matrix om* een waarde te *vinden*
* Een *woordenlijst om* een sleutel te *vinden*

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Collectie*> | Ja | Tekenreeks, Matrix of Woordenlijst | De te controleren verzameling |
| <*Waarde*> | Ja | Respectievelijk tekenreeks, matrix of woordenlijst | Het item dat moet worden gevonden |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | Retourneren waar wanneer het item wordt gevonden. Retourneren onwaar wanneer deze niet wordt gevonden. |
||||

*Voorbeeld 1*

In dit voorbeeld wordt de tekenreeks 'hallo wereld' gecontroleerd voor de subtekenreeks 'world' en wordt true (waar) retourneert:

```
contains('hello world', 'world')
```

*Voorbeeld 2*

In dit voorbeeld wordt de tekenreeks 'hallo wereld' gecontroleerd op de subtekenreeks 'universe' en wordt false (onwaar) retourneert:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Convert a timestamp from Universal Time Coordinated (UTC) to the target time zone.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die het tijdstempel bevat |
| <*destinationTimeZone*> | Ja | Tekenreeks | De naam voor de doeltijdzone. Zie Microsoft Windows [Default Time Zones (Standaard](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones)tijdzones van Microsoft Windows) voor meer informatie over tijdzonenamen, maar mogelijk moet u eventuele interpunctie verwijderen uit de naam van de tijdzone. |
| <*Formaat*> | Nee | Tekenreeks | Eén [opmaakopwijzer of](/dotnet/standard/base-types/standard-date-and-time-format-strings) een [patroon voor aangepaste notatie.](/dotnet/standard/base-types/custom-date-and-time-format-strings) De standaardindeling voor de tijdstempel is ['o'](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | Tekenreeks | Het tijdstempel geconverteerd naar de doeltijdzone |
||||

*Voorbeeld 1*

In dit voorbeeld wordt een tijdstempel ge converteerd naar de opgegeven tijdzone:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

En retourneert dit resultaat: `"2018-01-01T00:00:00.0000000"`

*Voorbeeld 2*

In dit voorbeeld wordt een tijdstempel ge converteerd naar de opgegeven tijdzone en indeling:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

En retourneert dit resultaat: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Converteert een tijdstempel van de brontijdzone naar de doeltijdzone.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die het tijdstempel bevat |
| <*sourceTimeZone*> | Ja | Tekenreeks | De naam voor de brontijdzone. Zie Microsoft Windows [Default Time Zones (Standaard](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones)tijdzones van Microsoft Windows) voor meer informatie over tijdzonenamen, maar mogelijk moet u eventuele interpunctie verwijderen uit de naam van de tijdzone. |
| <*destinationTimeZone*> | Ja | Tekenreeks | De naam voor de doeltijdzone. Zie Microsoft Windows [Default Time Zones (Standaard](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones)tijdzones van Microsoft Windows) voor meer informatie over tijdzonenamen, maar mogelijk moet u eventuele interpunctie verwijderen uit de naam van de tijdzone. |
| <*Formaat*> | Nee | Tekenreeks | Eén [opmaakopwijzer of](/dotnet/standard/base-types/standard-date-and-time-format-strings) een [patroon voor aangepaste notatie.](/dotnet/standard/base-types/custom-date-and-time-format-strings) De standaardindeling voor de tijdstempel is ['o'](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | Tekenreeks | De tijdstempel geconverteerd naar de doeltijdzone |
||||

*Voorbeeld 1*

In dit voorbeeld wordt de brontijdzone ge converteert naar de doeltijdzone:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

En retourneert dit resultaat: `"2018-01-01T00:00:00.0000000"`

*Voorbeeld 2*

In dit voorbeeld wordt een tijdzone ge converteerd naar de opgegeven tijdzone en indeling:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

En retourneert dit resultaat: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Convert a timestamp from the source time zone to Universal Time Coordinated (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die het tijdstempel bevat |
| <*sourceTimeZone*> | Ja | Tekenreeks | De naam voor de brontijdzone. Zie Microsoft Windows [Default Time Zones (Standaard](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones)tijdzones van Microsoft Windows) voor meer informatie over tijdzonenamen, maar mogelijk moet u eventuele interpunctie verwijderen uit de naam van de tijdzone. |
| <*Formaat*> | Nee | Tekenreeks | Eén [opmaakopwijzer of](/dotnet/standard/base-types/standard-date-and-time-format-strings) een [patroon voor aangepaste notatie.](/dotnet/standard/base-types/custom-date-and-time-format-strings) De standaardindeling voor de tijdstempel is ['o'](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | Tekenreeks | Het tijdstempel geconverteerd naar UTC |
||||

*Voorbeeld 1*

In dit voorbeeld wordt een tijdstempel ge converteert naar UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

En retourneert dit resultaat: `"2018-01-01T08:00:00.0000000Z"`

*Voorbeeld 2*

In dit voorbeeld wordt een tijdstempel ge converteert naar UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

En retourneert dit resultaat: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Retourneert een matrix op basis van meerdere invoer.
Zie array() voor enkele invoer [matrices.](#array)

```
createArray('<object1>', '<object2>', ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>, ... | Ja | Alle, maar niet gemengd | Ten minste twee items om de matrix te maken |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>, ...] | Matrix | De matrix die is gemaakt op basis van alle invoeritems |
||||

*Voorbeeld*

In dit voorbeeld wordt een matrix gemaakt op basis van deze invoer:

```
createArray('h', 'e', 'l', 'l', 'o')
```

En retourneert dit resultaat: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Een URI (Data Uniform Resource Identifier) voor een tekenreeks retourneren.

```
dataUri('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De tekenreeks die u wilt converteren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*data-uri*> | Tekenreeks | De gegevens-URI voor de invoerreeks |
||||

*Voorbeeld*

In dit voorbeeld wordt een gegevens-URI gemaakt voor de hello-tekenreeks:

```
dataUri('hello')
```

En retourneert dit resultaat: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

De binaire versie voor een URI (Data Uniform Resource Identifier) retourneren.
Gebruik deze functie in plaats [van decodeDataUri()](#decodeDataUri).
Hoewel beide functies op dezelfde manier werken, `dataUriBinary()` heeft de voorkeur.

```
dataUriToBinary('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De gegevens-URI die moet worden ge converteren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | Tekenreeks | De binaire versie voor de gegevens-URI |
||||

*Voorbeeld*

In dit voorbeeld wordt een binaire versie voor deze gegevens-URI gemaakt:

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

Retourneren van de tekenreeksversie voor een URI (Data Uniform Resource Identifier).

```
dataUriToString('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De gegevens-URI die moet worden ge converteren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | Tekenreeks | De tekenreeksversie voor de gegevens-URI |
||||

*Voorbeeld*

In dit voorbeeld wordt een tekenreeks voor deze gegevens-URI gemaakt:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

En retourneert dit resultaat: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Retourneerde de dag van de maand uit een tijdstempel.

```
dayOfMonth('<timestamp>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die het tijdstempel bevat |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*dag van de maand*> | Geheel getal | De dag van de maand vanaf het opgegeven tijdstempel |
||||

*Voorbeeld*

In dit voorbeeld wordt het getal voor de dag van de maand uit deze tijdstempel:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

En retourneert dit resultaat: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Retourneerde de dag van de week uit een tijdstempel.

```
dayOfWeek('<timestamp>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die het tijdstempel bevat |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*dag van de week*> | Geheel getal | De dag van de week van het opgegeven tijdstempel waarbij zondag 0 is, maandag 1, en meer |
||||

*Voorbeeld*

In dit voorbeeld wordt het getal voor de dag van de week van dit tijdstempel:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

En retourneert dit resultaat: `4`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Retourneerde de dag van het jaar uit een tijdstempel.

```
dayOfYear('<timestamp>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die het tijdstempel bevat |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*dag van het jaar*> | Geheel getal | De dag van het jaar vanaf het opgegeven tijdstempel |
||||

*Voorbeeld*

In dit voorbeeld wordt het aantal dag van het jaar uit dit tijdstempel:

```
dayOfYear('2018-03-15T13:27:36Z')
```

En retourneert dit resultaat: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64-deprecated"></a>decodeBase64 (afgeschaft)

Deze functie is afgeschaft, dus gebruik in plaats daarvan [base64ToString().](#base64ToString)

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

De binaire versie voor een URI (Data Uniform Resource Identifier) retourneren. Overweeg het [gebruik van dataUriToBinary()](#dataUriToBinary)in plaats van `decodeDataUri()` . Hoewel beide functies op dezelfde manier werken, `dataUriToBinary()` heeft de voorkeur.

> [!NOTE]
> Azure Logic Apps voert automatisch of impliciet base64-codering en -decoderen uit, zodat u deze conversies niet handmatig hoeft uit te voeren met behulp van de coderings- en decodeerfuncties. Als u deze functies echter toch in de ontwerpfunctie gebruikt, kan er onverwacht renderinggedrag in de ontwerpfunctie worden ervaren. Dit gedrag is alleen van invloed op de zichtbaarheid van de functies en niet op het effect ervan, tenzij u de parameterwaarden van de functies bewerkt, waardoor de functies en hun effecten uit uw code worden verwijderd. Zie [Base64-codering en -decoderen voor meer informatie.](#base64-encoding-decoding)

```
decodeDataUri('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De gegevens-URI-tekenreeks die moet worden gedecodeerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | Tekenreeks | De binaire versie voor een gegevens-URI-tekenreeks |
||||

*Voorbeeld*

In dit voorbeeld wordt de binaire versie voor deze gegevens-URI retourneert:

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

Retourneert een tekenreeks die escape-tekens vervangt door gedecodeerde versies.

```
decodeUriComponent('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De tekenreeks met de escapetekens die moeten worden gedecodeerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | Tekenreeks | De bijgewerkte tekenreeks met de gedecodeerde escapetekens |
||||

*Voorbeeld*

In dit voorbeeld worden de escape-tekens in deze tekenreeks vervangen door gedecodeerde versies:

```
decodeUriComponent('https%3A%2F%2Fcontoso.com')
```

En retourneert dit resultaat: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Retourneert het resultaat van het delen van twee getallen. Zie [mod()](#mod)om het restresultaat op te halen.

```
div(<dividend>, <divisor>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Dividend*> | Ja | Geheel getal of float | Het getal dat door de *deler moet worden verdeeld* |
| <*Deler*> | Ja | Geheel getal of float | Het getal dat het *deeltal deelt,* maar niet 0 mag zijn |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*quotient-result*> | Geheel getal of float | Het resultaat van het delen van het eerste getal door het tweede getal. Als het deeltal of deler het Float-type heeft, heeft het resultaat het Float-type. <p><p>**Opmerking:** als u het float-resultaat wilt converteren naar een geheel getal, probeert u een functie [in Azure](../logic-apps/logic-apps-azure-functions.md) te maken en aan te roepen vanuit uw logische app. |
||||

*Voorbeeld 1*

Beide voorbeelden retourneren deze waarde met het type Geheel getal: `2`

```
div(10,5)
div(11,5)
```

*Voorbeeld 2*

Beide voorbeelden retourneren deze waarde met floattype: `2.2`

```
div(11,5.0)
div(11.0,5)
```

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Retourneert een met uniform resource identifier (URI) gecodeerde versie voor een tekenreeks door URL-onveilige tekens te vervangen door escape-tekens. Overweeg het [gebruik van uriComponent()](#uriComponent)in plaats van `encodeUriComponent()` . Hoewel beide functies op dezelfde manier werken, `uriComponent()` heeft de voorkeur.

> [!NOTE]
> Azure Logic Apps voert automatisch of impliciet base64-codering en -decoderen uit, zodat u deze conversies niet handmatig hoeft uit te voeren met behulp van de coderings- en decodeerfuncties. Als u deze functies echter toch in de ontwerpfunctie gebruikt, kan er onverwacht renderinggedrag in de ontwerpfunctie worden ervaren. Dit gedrag is alleen van invloed op de zichtbaarheid van de functies en niet op het effect ervan, tenzij u de parameterwaarden van de functies bewerkt, waardoor de functies en hun effecten uit uw code worden verwijderd. Zie [Base64-codering en -decoderen](#base64-encoding-decoding)voor meer informatie.

```
encodeUriComponent('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De tekenreeks die moet worden ge converteren naar een met URI gecodeerde indeling |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*encoded-URI*> | Tekenreeks | De met URI gecodeerde tekenreeks met escape-tekens |
||||

*Voorbeeld*

In dit voorbeeld wordt een met URI gecodeerde versie voor deze tekenreeks gemaakt:

```
encodeUriComponent('https://contoso.com')
```

En retourneert dit resultaat: `"https%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>leeg

Controleer of een verzameling leeg is.
Retourner true wanneer de verzameling leeg is of onwaar als deze niet leeg is.

```
empty('<collection>')
empty([<collection>])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Collectie*> | Ja | Tekenreeks, Matrix of Object | De te controleren verzameling |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | Retourner true wanneer de verzameling leeg is. Retourner false wanneer deze niet leeg is. |
||||

*Voorbeeld*

Deze voorbeelden controleren of de opgegeven verzamelingen leeg zijn:

```
empty('')
empty('abc')
```

En retourneert deze resultaten:

* Eerste voorbeeld: geeft een lege tekenreeks door, zodat de functie `true` retourneert.
* Tweede voorbeeld: geeft de tekenreeks 'abc' door, zodat de functie `false` retourneert.

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Controleer of een tekenreeks eindigt met een specifieke subtekenreeks.
Retourneert true wanneer de subtekenreeks wordt gevonden of retourneert false wanneer deze niet is gevonden.
Deze functie is niet casegevoelig.

```
endsWith('<text>', '<searchText>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Ja | Tekenreeks | De tekenreeks die u wilt controleren |
| <*searchText*> | Ja | Tekenreeks | De eindsubtekenreeks die moet worden gevonden |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar  | Booleaans | Retourneert true wanneer de eindsubtekenreeks wordt gevonden. Onwaar retourneren wanneer deze niet is gevonden. |
||||

*Voorbeeld 1*

In dit voorbeeld wordt gecontroleerd of de 'hallo wereld'-tekenreeks eindigt met de 'wereld'-tekenreeks:

```
endsWith('hello world', 'world')
```

En retourneert dit resultaat: `true`

*Voorbeeld 2*

In dit voorbeeld wordt gecontroleerd of de 'hallo wereld'-tekenreeks eindigt met de tekenreeks 'universum':

```
endsWith('hello world', 'universe')
```

En retourneert dit resultaat: `false`

<a name="equals"></a>

### <a name="equals"></a>is gelijk aan

Controleer of beide waarden, expressies of objecten gelijkwaardig zijn.
True retourneren wanneer beide gelijkwaardig zijn, of onwaar retourneren wanneer ze niet gelijkwaardig zijn.

```
equals('<object1>', '<object2>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | Ja | Verschillende | De waarden, expressies of objecten die moeten worden vergeleken |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | True retourneren wanneer beide gelijkwaardig zijn. Onwaar retourneren wanneer dit niet gelijkwaardig is. |
||||

*Voorbeeld*

Deze voorbeelden controleren of de opgegeven invoer gelijk is.

```
equals(true, 1)
equals('abc', 'abcd')
```

En retourneert deze resultaten:

* Eerste voorbeeld: Beide waarden zijn gelijkwaardig, dus de functie retourneert `true` .
* Tweede voorbeeld: Beide waarden zijn niet equivalent, dus retourneert de functie `false` .

<a name="first"></a>

### <a name="first"></a>Eerste

Het eerste item uit een tekenreeks of matrix retourneren.

```
first('<collection>')
first([<collection>])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Collectie*> | Ja | Tekenreeks of matrix | De verzameling waar u het eerste item kunt vinden |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*first-collection-item*> | Alle | Het eerste item in de verzameling |
||||

*Voorbeeld*

In deze voorbeelden vindt u het eerste item in deze verzamelingen:

```
first('hello')
first(createArray(0, 1, 2))
```

En deze resultaten retourneren:

* Eerste voorbeeld: `"h"`
* Tweede voorbeeld: `0`

<a name="float"></a>

### <a name="float"></a>float

Converteert een tekenreeksversie voor een drijvende-puntnummer naar een daadwerkelijk drijvende-puntnummer.
U kunt deze functie alleen gebruiken bij het doorgeven van aangepaste parameters aan een app, bijvoorbeeld een logische app of stroom.

```
float('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De tekenreeks met een geldig drijvende-puntnummer om te converteren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*float-value*> | Float | Het drijvende-puntnummer voor de opgegeven tekenreeks |
||||

*Voorbeeld*

In dit voorbeeld wordt een tekenreeksversie voor dit drijvende-puntnummer gemaakt:

```
float('10.333')
```

En retourneert dit resultaat: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Retourneert een tijdstempel in de opgegeven indeling.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die het tijdstempel bevat |
| <*Formaat*> | Nee | Tekenreeks | Eén [opmaakopwijzer of](/dotnet/standard/base-types/standard-date-and-time-format-strings) een [patroon voor aangepaste notatie.](/dotnet/standard/base-types/custom-date-and-time-format-strings) De standaardindeling voor de tijdstempel is ['o'](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*reformatted-timestamp*> | Tekenreeks | Het bijgewerkte tijdstempel in de opgegeven indeling |
||||

*Voorbeeld*

In dit voorbeeld wordt een tijdstempel ge converteerd naar de opgegeven indeling:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

En retourneert dit resultaat: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

Retourneert een matrix met waarden die overeenkomen met een sleutelnaam in de uitvoer van een actie met *form-data* of *form-encoded.*

```
formDataMultiValues('<actionName>', '<key>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De actie waarvan de uitvoer de sleutelwaarde heeft die u wilt |
| <*Sleutel*> | Ja | Tekenreeks | De naam van de sleutel waarvan u de waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*array-with-key-values>]* | Matrix | Een matrix met alle waarden die overeenkomen met de opgegeven sleutel |
||||

*Voorbeeld*

In dit voorbeeld wordt een matrix gemaakt op basis van de waarde van de onderwerpsleutel in de uitvoer van de opgegeven actie op basis van formuliergegevens of met formulier gecodeerde gegevens:

```
formDataMultiValues('Send_an_email', 'Subject')
```

En retourneert de onderwerptekst in een matrix, bijvoorbeeld: `["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

Retourneert één waarde die overeenkomt met een sleutelnaam in de uitvoer van een actie met *form-data* of *form-encoded.*
Als de functie meer dan één overeenkomst vindt, wordt door de functie een foutmelding weergegeven.

```
formDataValue('<actionName>', '<key>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De actie waarvan de uitvoer de sleutelwaarde heeft die u wilt |
| <*Sleutel*> | Ja | Tekenreeks | De naam van de sleutel waarvan u de waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*sleutelwaarde*> | Tekenreeks | De waarde in de opgegeven sleutel  |
||||

*Voorbeeld*

In dit voorbeeld wordt een tekenreeks gemaakt op basis van de waarde van de onderwerpsleutel in de uitvoer van de opgegeven actie met form-data of form-encoded:

```
formDataValue('Send_an_email', 'Subject')
```

En retourneert de onderwerptekst als een tekenreeks, bijvoorbeeld: `"Hello world"`

<a name="formatNumber"></a>

### <a name="formatnumber"></a>formatNumber

Retourneert een getal als een tekenreeks die is gebaseerd op de opgegeven indeling.

```text
formatNumber(<number>, <format>, <locale>?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Nummer*> | Ja | Geheel getal of dubbel | De waarde die u wilt opmaken. |
| <*Formaat*> | Ja | Tekenreeks | Een samengestelde notatiereeks die de indeling aangeeft die u wilt gebruiken. Zie Standaardreeksen met numerieke [](/dotnet/standard/base-types/standard-numeric-format-strings)notatie voor de ondersteunde tekenreeksen met numerieke notatie, die worden ondersteund door `number.ToString(<format>, <locale>)` . |
| <*Locale*> | Nee | Tekenreeks | De te gebruiken locale als ondersteund door `number.ToString(<format>, <locale>)` . Als dit niet is opgegeven, is de standaardwaarde `en-us` . |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*formatted-number*> | Tekenreeks | Het opgegeven getal als een tekenreeks in de indeling die u hebt opgegeven. U kunt deze retourwaarde casten naar een `int` of `float` . |
||||

*Voorbeeld 1*

Stel dat u het getal wilt `1234567890` opmaken. In dit voorbeeld wordt dat getal opgemaakt als de tekenreeks '1.234.567.890.00'.

```
formatNumber(1234567890, '0,0.00', 'en-us')
```

*Voorbeeld 2"

Stel dat u het getal wilt `1234567890` opmaken. In dit voorbeeld wordt het getal opgemaakt in de tekenreeks '1.234.567.890,00'.

```
formatNumber(1234567890, '0,0.00', 'is-is')
```

*Voorbeeld 3*

Stel dat u het getal wilt `17.35` opmaken. In dit voorbeeld wordt het getal opgemaakt in de tekenreeks $17,35.

```
formatNumber(17.35, 'C2')
```

*Voorbeeld 4*

Stel dat u het getal wilt `17.35` opmaken. In dit voorbeeld wordt het getal opgemaakt in de tekenreeks '17,35 kr'.

```
formatNumber(17.35, 'C2', 'is-is')
```

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Retourneert de huidige tijdstempel plus de opgegeven tijdeenheden.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Interval*> | Ja | Geheel getal | Het aantal opgegeven tijdeenheden dat moet worden toevoegen |
| <*timeUnit*> | Ja | Tekenreeks | De tijdseenheid die moet worden gebruikt met *interval:*"Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*Formaat*> | Nee | Tekenreeks | Eén [opmaakopwijzer of](/dotnet/standard/base-types/standard-date-and-time-format-strings) een [patroon voor aangepaste notatie.](/dotnet/standard/base-types/custom-date-and-time-format-strings) De standaardindeling voor de tijdstempel is ['o'](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Tekenreeks | Het huidige tijdstempel plus het opgegeven aantal tijdeenheden |
||||

*Voorbeeld 1*

Stel dat de huidige tijdstempel '2018-03-01T00:00:00.00000000Z' is.
In dit voorbeeld worden vijf dagen aan die tijdstempel toegevoegd:

```
getFutureTime(5, 'Day')
```

En retourneert dit resultaat: `"2018-03-06T00:00:00.0000000Z"`

*Voorbeeld 2*

Stel dat de huidige tijdstempel '2018-03-01T00:00:00.00000000Z' is.
In dit voorbeeld worden vijf dagen toegevoegd en wordt het resultaat ge converteert naar de indeling D:

```
getFutureTime(5, 'Day', 'D')
```

En retourneert dit resultaat: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Retourneert de huidige tijdstempel min de opgegeven tijdseenheden.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Interval*> | Ja | Geheel getal | Het aantal opgegeven tijdeenheden dat moet worden afgetrokken |
| <*timeUnit*> | Ja | Tekenreeks | De tijdseenheid die moet worden gebruikt met *interval:*'Second', 'Minute', 'Hour', 'Day', 'Week', 'Month', 'Year' |
| <*Formaat*> | Nee | Tekenreeks | Eén [opmaakopwijzer of](/dotnet/standard/base-types/standard-date-and-time-format-strings) een [patroon voor aangepaste notatie.](/dotnet/standard/base-types/custom-date-and-time-format-strings) De standaardindeling voor de tijdstempel is ['o'](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Tekenreeks | Het huidige tijdstempel min het opgegeven aantal tijdeenheden |
||||

*Voorbeeld 1*

Stel dat de huidige tijdstempel '2018-02-01T00:00:00.0000000Z' is.
In dit voorbeeld worden vijf dagen van die tijdstempel afgetrokken:

```
getPastTime(5, 'Day')
```

En retourneert dit resultaat: `"2018-01-27T00:00:00.0000000Z"`

*Voorbeeld 2*

Stel dat de huidige tijdstempel '2018-02-01T00:00:00.0000000Z' is.
In dit voorbeeld worden vijf dagen afgetrokken en wordt het resultaat ge converteert naar de indeling D:

```
getPastTime(5, 'Day', 'D')
```

En retourneert dit resultaat: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Controleer of de eerste waarde groter is dan de tweede waarde.
Retourneert true wanneer de eerste waarde meer is, of retourneert false wanneer deze kleiner is.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Geheel getal, float of tekenreeks | De eerste waarde om te controleren of deze groter is dan de tweede waarde |
| <*Compareto*> | Ja | Respectievelijk geheel getal, float of tekenreeks | De vergelijkingswaarde |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | Retourneert true wanneer de eerste waarde groter is dan de tweede waarde. Retourneert false wanneer de eerste waarde gelijk is aan of kleiner is dan de tweede waarde. |
||||

*Voorbeeld*

Deze voorbeelden controleren of de eerste waarde groter is dan de tweede waarde:

```
greater(10, 5)
greater('apple', 'banana')
```

En deze resultaten retourneren:

* Eerste voorbeeld: `true`
* Tweede voorbeeld: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Controleer of de eerste waarde groter is dan of gelijk is aan de tweede waarde.
Retourneert true wanneer de eerste waarde groter of gelijk is of false retourneert wanneer de eerste waarde kleiner is.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Geheel getal, Float of Tekenreeks | De eerste waarde om te controleren of groter is dan of gelijk is aan de tweede waarde |
| <*Compareto*> | Ja | Respectievelijk geheel getal, float of tekenreeks | De vergelijkingswaarde |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | Retourneert true wanneer de eerste waarde groter is dan of gelijk is aan de tweede waarde. Retourneert onwaar wanneer de eerste waarde kleiner is dan de tweede waarde. |
||||

*Voorbeeld*

Deze voorbeelden controleren of de eerste waarde groter of gelijk is aan de tweede waarde:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

En deze resultaten retourneren:

* Eerste voorbeeld: `true`
* Tweede voorbeeld: `false`

<a name="guid"></a>

### <a name="guid"></a>guid

Genereer een globally unique identifier (GUID) als een tekenreeks, bijvoorbeeld 'c2ecc88d-88c8-4096-912c-d6f2e2b138ce':

```
guid()
```

U kunt ook een andere indeling voor de GUID opgeven dan de standaardindeling D, die bestaat uit 32 cijfers gescheiden door afbreekstreetes.

```
guid('<format>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Formaat*> | Nee | Tekenreeks | Eén [opmaakopmaak voor](/dotnet/api/system.guid.tostring#system_guid_tostring_system_string_) de geretourneerde GUID. De indeling is standaard 'D', maar u kunt 'N', 'D', 'B', 'P' of 'X' gebruiken. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*GUID-waarde*> | Tekenreeks | Een willekeurig gegenereerde GUID |
||||

*Voorbeeld*

In dit voorbeeld wordt dezelfde GUID gegenereerd, maar als 32 cijfers, gescheiden door afbreekstreeën, en tussen haakjes:

```
guid('P')
```

En retourneert dit resultaat: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Controleer of een expressie waar of onwaar is. Retourneert op basis van het resultaat een opgegeven waarde. Parameters worden van links naar rechts geëvalueerd.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Expressie*> | Ja | Booleaans | De expressie die moet worden controleren |
| <*valueIfTrue*> | Ja | Alle | De waarde die moet worden retourneert wanneer de expressie waar is |
| <*valueIfFalse*> | Ja | Alle | De waarde die moet worden retourneert wanneer de expressie onwaar is |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*specified-return-value*> | Alle | De opgegeven waarde die retourneert op basis van of de expressie waar of onwaar is |
||||

*Voorbeeld*

Dit voorbeeld retourneert `"yes"` omdat de opgegeven expressie true retourneert.
Anders retourneert het voorbeeld `"no"` :

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Retourneert de beginpositie of indexwaarde voor een subtekenreeks.
Deze functie is niet hoofdgevoelig en indexen beginnen met het getal 0.

```
indexOf('<text>', '<searchText>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Ja | Tekenreeks | De tekenreeks met de subtekenreeks die u kunt vinden |
| <*searchText*> | Ja | Tekenreeks | De subtekenreeks die moet worden gevonden |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*index-value*>| Geheel getal | De beginpositie of indexwaarde voor de opgegeven subtekenreeks. <p>Als de tekenreeks niet wordt gevonden, retournt u het getal -1. |
||||

*Voorbeeld*

In dit voorbeeld wordt de beginindexwaarde voor de subtekenreeks 'world' in de 'hallo wereld'-tekenreeks gevonden:

```
indexOf('hello world', 'world')
```

En retourneert dit resultaat: `6`

<a name="int"></a>

### <a name="int"></a>int

Retourner de geheel getal-versie voor een tekenreeks.

```
int('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De tekenreeks die u wilt converteren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*integer-result*> | Geheel getal | De geheel getal-versie voor de opgegeven tekenreeks |
||||

*Voorbeeld*

In dit voorbeeld wordt een geheel getal gemaakt voor de tekenreeks '10':

```
int('10')
```

En retourneert dit resultaat: `10`

<a name="item"></a>

### <a name="item"></a>item

Wanneer u in een herhalende actie over een matrix gebruikt, retourneert u het huidige item in de matrix tijdens de huidige iteratie van de actie.
U kunt ook de waarden uit de eigenschappen van dat item op halen.

```
item()
```

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*current-array-item*> | Alle | Het huidige item in de matrix voor de huidige iteratie van de actie |
||||

*Voorbeeld*

In dit voorbeeld wordt het element uit het huidige bericht voor de actie 'Send_an_email' binnen de huidige iteratie van een `body` for-each-lus opgevraagd:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>Items

Het huidige item van elke cyclus retourneren in een for-each-lus.
Gebruik deze functie in de for-each-lus.

```
items('<loopName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*loopName*> | Ja | Tekenreeks | De naam voor de for-each-lus |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*Item*> | Alle | Het item uit de huidige cyclus in de opgegeven for-each-lus |
||||

*Voorbeeld*

In dit voorbeeld wordt het huidige item uit de opgegeven for-each-lus opgeslagen:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterationIndexes

Retourneert de indexwaarde voor de huidige iteratie binnen een Until-lus. U kunt deze functie gebruiken binnen geneste Until-lussen. 

```
iterationIndexes('<loopName>')
```

| Parameter | Vereist | Type | Beschrijving | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | Ja | Tekenreeks | De naam voor de until-lus | 
||||| 

| Retourwaarde | Type | Beschrijving | 
| ------------ | ---- | ----------- | 
| <*Index*> | Geheel getal | De indexwaarde voor de huidige iteratie binnen de opgegeven Until-lus | 
|||| 

*Voorbeeld* 

In dit voorbeeld wordt een tellervariabele gemaakt en wordt die variabele tijdens elke iteratie in een Until-lus met één verhoogd totdat de tellerwaarde vijf bereikt. In het voorbeeld wordt ook een variabele gemaakt die de huidige index voor elke iteratie bij houdt. In de lus Until wordt tijdens elke iteratie het teller in het voorbeeld verhoogd en wordt vervolgens de tellerwaarde toegewezen aan de huidige indexwaarde en vervolgens de teller verhoogd. In de lus verwijst dit voorbeeld naar de huidige iteratie-index met behulp van de `iterationIndexes` functie :

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

Retourneert JavaScript Object Notation (JSON)-typewaarde, object of matrix met objecten voor een tekenreeks of XML.

```
json('<value>')
json(xml('value'))
```

> [!IMPORTANT]
> Zonder een XML-schema dat de structuur van de uitvoer definieert, kan de functie resultaten retourneren waarbij de structuur sterk verschilt van de verwachte indeling, afhankelijk van de invoer.
>  
> Door dit gedrag is deze functie niet geschikt voor scenario's waarin de uitvoer moet voldoen aan een goed gedefinieerd contract, bijvoorbeeld in kritieke bedrijfssystemen of oplossingen.

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks of XML | De tekenreeks of XML die u wilt converteren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*JSON-resultaat*> | Native JSON-type, -object of -matrix | De waarde van het native JSON-type, het object of de matrix met objecten uit de invoerreeks of XML. <p><p>- Als u XML met één onderliggend element in het hoofdelement door geeft, retourneert de functie één JSON-object voor dat onderliggende element. <p> - Als u XML met meerdere onderliggende elementen in het hoofdelement door geeft, retourneert de functie een matrix die JSON-objecten voor die onderliggende elementen bevat. <p>- Als de tekenreeks null is, retourneert de functie een leeg object. |
||||

*Voorbeeld 1*

In dit voorbeeld wordt deze tekenreeks ge converteert naar een JSON-waarde:

```
json('[1, 2, 3]')
```

En retourneert dit resultaat: `[1, 2, 3]`

*Voorbeeld 2*

In dit voorbeeld wordt deze tekenreeks ge converteert naar JSON:

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

In dit voorbeeld worden de functies en gebruikt om XML met één onderliggend element in het hoofdelement te converteren naar een JSON-object met de naam `json()` `xml()` voor dat `person` onderliggende element:

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

In dit voorbeeld worden de functies en gebruikt om XML met meerdere onderliggende elementen in het hoofdelement te converteren naar een matrix met de naam `json()` `xml()` die JSON-objecten voor die onderliggende `person` elementen bevat:

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

### <a name="intersection"></a>Snijpunt

Retourneert een verzameling die *alleen de* algemene items in de opgegeven verzamelingen bevat.
Als u wilt worden weergegeven in het resultaat, moet een item worden weergegeven in alle verzamelingen die aan deze functie worden doorgegeven.
Als een of meer items dezelfde naam hebben, wordt het laatste item met die naam weergegeven in het resultaat.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ... | Ja | Matrix of object, maar niet beide | De verzamelingen van waar u alleen *de algemene* items wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*common-items*> | Matrix of object, respectievelijk | Een verzameling met alleen de algemene items in de opgegeven verzamelingen |
||||

*Voorbeeld*

In dit voorbeeld worden de algemene items in deze matrices gevonden:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

En retourneert een matrix met *alleen* deze items: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Retourneren van een tekenreeks met alle items uit een matrix en elk teken gescheiden door *een scheidingsteken*.

```
join([<collection>], '<delimiter>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Collectie*> | Ja | Matrix | De matrix met de items die moeten worden lid |
| <*Scheidingsteken*> | Ja | Tekenreeks | Het scheidingsteken dat wordt weergegeven tussen elk teken in de resulterende tekenreeks |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*char1* >< *scheidingsteken* >< *char2* >< *scheidingstekens*>... | Tekenreeks | De resulterende tekenreeks die is gemaakt van alle items in de opgegeven matrix |
||||

*Voorbeeld*

In dit voorbeeld wordt een tekenreeks gemaakt van alle items in deze matrix met het opgegeven teken als scheidingsteken:

```
join(createArray('a', 'b', 'c'), '.')
```

En retourneert dit resultaat: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>Laatste

Het laatste item uit een verzameling retourneren.

```
last('<collection>')
last([<collection>])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Collectie*> | Ja | Tekenreeks of matrix | De verzameling waar het laatste item te vinden is |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*last-collection-item*> | Respectievelijk tekenreeks of matrix | Het laatste item in de verzameling |
||||

*Voorbeeld*

In deze voorbeelden vindt u het laatste item in deze verzamelingen:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

En retourneert deze resultaten:

* Eerste voorbeeld: `"d"`
* Tweede voorbeeld: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Retourneert de beginpositie of indexwaarde voor het laatste exemplaar van een subtekenreeks. Deze functie is niet hoofdgevoelig en indexen beginnen met het getal 0.

```json
lastIndexOf('<text>', '<searchText>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Ja | Tekenreeks | De tekenreeks met de subtekenreeks die u kunt vinden |
| <*searchText*> | Ja | Tekenreeks | De subtekenreeks die moet worden gevonden |
|||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*ending-index-value*> | Geheel getal | De beginpositie of indexwaarde voor het laatste exemplaar van de opgegeven subtekenreeks. |
|||

Als de tekenreeks of subtekenreekswaarde leeg is, gebeurt het volgende:

* Als alleen de tekenreekswaarde leeg is, retourneert de functie `-1` .

* Als de tekenreeks- en subtekenreekswaarden beide leeg zijn, retourneert de functie `0` .

* Als alleen de subtekenreekswaarde leeg is, retourneert de functie de tekenreekslengte min 1.

*Voorbeelden*

In dit voorbeeld wordt de beginindexwaarde gevonden voor het laatste exemplaar van de subtekenreeks `world` in de tekenreeks `hello world hello world` . Het geretourneerde resultaat is `18` :

```json
lastIndexOf('hello world hello world', 'world')
```

In dit voorbeeld ontbreekt de subtekenreeksparameter en wordt een waarde van retourneert omdat de waarde van de invoertekenreeks `22` ( `23` ) min 1 groter is dan 0.

```json
lastIndexOf('hello world hello world', '')
```

<a name="length"></a>

### <a name="length"></a>lengte

Het aantal items in een verzameling retourneren.

```
length('<collection>')
length([<collection>])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Collectie*> | Ja | Tekenreeks of matrix | De verzameling met de items die moeten worden geteld |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*length-or-count*> | Geheel getal | Het aantal items in de verzameling |
||||

*Voorbeeld*

In deze voorbeelden wordt het aantal items in deze verzamelingen geteld:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

En dit resultaat retourneren: `4`

<a name="less"></a>

### <a name="less"></a>less

Controleer of de eerste waarde kleiner is dan de tweede waarde.
Retourneert true wanneer de eerste waarde kleiner is, of retourneert false wanneer de eerste waarde meer is.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Geheel getal, Float of Tekenreeks | De eerste waarde om te controleren of deze kleiner is dan de tweede waarde |
| <*Compareto*> | Ja | Respectievelijk geheel getal, float of tekenreeks | Het vergelijkingsitem |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | Retourneert true wanneer de eerste waarde kleiner is dan de tweede waarde. Retourneert false wanneer de eerste waarde gelijk is aan of groter is dan de tweede waarde. |
||||

*Voorbeeld*

Deze voorbeelden controleren of de eerste waarde kleiner is dan de tweede waarde.

```
less(5, 10)
less('banana', 'apple')
```

En deze resultaten retourneren:

* Eerste voorbeeld: `true`
* Tweede voorbeeld: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Controleer of de eerste waarde kleiner is dan of gelijk is aan de tweede waarde.
Retourneert true wanneer de eerste waarde kleiner is dan of gelijk is, of false retourneert wanneer de eerste waarde meer is.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Geheel getal, Float of Tekenreeks | De eerste waarde om te controleren of kleiner dan of gelijk aan de tweede waarde |
| <*Compareto*> | Ja | Respectievelijk geheel getal, float of tekenreeks | Het vergelijkingsitem |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar  | Booleaans | Retourneert true wanneer de eerste waarde kleiner is dan of gelijk is aan de tweede waarde. Retourneert false wanneer de eerste waarde groter is dan de tweede waarde. |
||||

*Voorbeeld*

Deze voorbeelden controleren of de eerste waarde kleiner of gelijk is aan de tweede waarde.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

En deze resultaten retourneren:

* Eerste voorbeeld: `true`
* Tweede voorbeeld: `false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

Retourneren van de 'callback-URL' die een trigger of actie aanroept.
Deze functie werkt alleen met triggers en acties voor de connectortypen **HttpWebhook** en **ApiConnectionWebhook,** maar niet de typen **Handmatig,** **Terugkeerpatroon,** **HTTP** en **APIConnection.**

```
listCallbackUrl()
```

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*callback-URL*> | Tekenreeks | De callback-URL voor een trigger of actie |
||||

*Voorbeeld*

In dit voorbeeld ziet u een voorbeeld van een callback-URL die deze functie kan retourneren:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>max

Retourneert de hoogste waarde van een lijst of matrix met getallen die aan beide uiteinden zijn opgenomen.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | Ja | Geheel getal, Float of beide | De set getallen waarvan u de hoogste waarde wilt |
| [<*number1*>, <*number2*>, ...] | Ja | Matrix: geheel getal, float of beide | De matrix met getallen waarvan u de hoogste waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*max-value*> | Geheel getal of float | De hoogste waarde in de opgegeven matrix of set getallen |
||||

*Voorbeeld*

Deze voorbeelden krijgen de hoogste waarde uit de set getallen en de matrix:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

En dit resultaat retourneren: `3`

<a name="min"></a>

### <a name="min"></a>min.

Retourneert de laagste waarde van een set getallen of een matrix.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | Ja | Geheel getal, Float of beide | De set getallen van waaruit u de laagste waarde wilt |
| [<*number1*>, <*number2*>, ...] | Ja | Matrix: geheel getal, float of beide | De matrix met getallen waarvan u de laagste waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*min-value*> | Geheel getal of float | De laagste waarde in de opgegeven set getallen of opgegeven matrix |
||||

*Voorbeeld*

Deze voorbeelden krijgen de laagste waarde in de set getallen en de matrix:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

En dit resultaat retourneren: `1`

<a name="mod"></a>

### <a name="mod"></a>Mod

Retourneert het restant van het delen van twee getallen.
Zie [div()](#div)om het resultaat van een geheel getal op te halen.

```
mod(<dividend>, <divisor>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Dividend*> | Ja | Geheel getal of float | Het getal dat door de *deler moet worden verdeeld* |
| <*Deler*> | Ja | Geheel getal of float | Het getal dat het *deeltal deelt,* maar niet 0 mag zijn. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*modulo-result*> | Geheel getal of float | De rest van het delen van het eerste getal door het tweede getal |
||||

*Voorbeeld*

In dit voorbeeld wordt het eerste getal door het tweede getal verdeeld:

```
mod(3, 2)
```

En dit resultaat retourneren: `1`

<a name="mul"></a>

### <a name="mul"></a>Mul

Retourneert het product door twee getallen te vermenigvuldigen.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Ja | Geheel getal of float | Het getal dat moet worden vermenigvuldigd *met vermenigvuldigd2* |
| <*multiplicand2*> | Ja | Geheel getal of float | Het getal dat *vermenigvuldigt vermenigvuldigen1* |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*productresultaat*> | Geheel getal of float | Het product van het vermenigvuldigen van het eerste getal met het tweede getal |
||||

*Voorbeeld*

In deze voorbeelden wordt het eerste getal met het tweede getal meervoudig:

```
mul(1, 2)
mul(1.5, 2)
```

En deze resultaten retourneren:

* Eerste voorbeeld: `2`
* Tweede voorbeeld `3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

De body retourneren voor een specifiek onderdeel in de uitvoer van een actie die meerdere delen heeft.

```
multipartBody('<actionName>', <index>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De naam voor de actie met uitvoer met meerdere onderdelen |
| <*Index*> | Ja | Geheel getal | De indexwaarde voor het onderdeel dat u wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*Lichaam*> | Tekenreeks | De body voor het opgegeven onderdeel |
||||

<a name="not"></a>

### <a name="not"></a>not

Controleer of een expressie onwaar is.
Retourner true wanneer de expressie onwaar is, of retourner onwaar wanneer waar.

```json
not(<expression>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Expressie*> | Ja | Booleaans | De expressie die moet worden controleren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | Retourneren waar wanneer de expressie onwaar is. Retourner onwaar wanneer de expressie waar is. |
||||

*Voorbeeld 1*

Deze voorbeelden controleren of de opgegeven expressies onwaar zijn:

```json
not(false)
not(true)
```

En deze resultaten retourneren:

* Eerste voorbeeld: De expressie is onwaar, dus de functie retourneert `true` .
* Tweede voorbeeld: De expressie is true, dus de functie retourneert `false` .

*Voorbeeld 2*

Deze voorbeelden controleren of de opgegeven expressies onwaar zijn:

```json
not(equals(1, 2))
not(equals(1, 1))
```

En deze resultaten retourneren:

* Eerste voorbeeld: De expressie is onwaar, dus de functie retourneert `true` .
* Tweede voorbeeld: De expressie is true, dus de functie retourneert `false` .

<a name="or"></a>

### <a name="or"></a>of

Controleer of ten minste één expressie waar is.
Retourner waar wanneer ten minste één expressie waar is of als false wordt retourneren wanneer alle onwaar zijn.

```
or(<expression1>, <expression2>, ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | Ja | Booleaans | De expressies die moeten worden controleren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | True retourneren wanneer ten minste één expressie waar is. Retourneert onwaar wanneer alle expressies onwaar zijn. |
||||

*Voorbeeld 1*

Deze voorbeelden controleren of ten minste één expressie waar is:

```json
or(true, false)
or(false, false)
```

En deze resultaten retourneren:

* Eerste voorbeeld: Ten minste één expressie is true, dus de functie retourneert `true` .
* Tweede voorbeeld: Beide expressies zijn onwaar, dus de functie retourneert `false` .

*Voorbeeld 2*

Deze voorbeelden controleren of ten minste één expressie waar is:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

En deze resultaten retourneren:

* Eerste voorbeeld: Ten minste één expressie is true, dus de functie retourneert `true` .
* Tweede voorbeeld: Beide expressies zijn onwaar, dus de functie retourneert `false` .

<a name="outputs"></a>

### <a name="outputs"></a>Uitgangen

Retourneert de uitvoer van een actie tijdens runtime. Gebruik deze functie in plaats van `actionOutputs()` , die wordt opgelost in de `outputs()` ontwerpfunctie voor logische apps. Hoewel beide functies op dezelfde manier werken, `outputs()` heeft de voorkeur.

```
outputs('<actionName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De naam voor de uitvoer van de actie die u wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| <*Output*> | Tekenreeks | De uitvoer van de opgegeven actie |
||||

*Voorbeeld*

In dit voorbeeld wordt de uitvoer van de Twitter-actie : `Get user`

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

Retourneert de waarde voor een parameter die wordt beschreven in uw werkstroomdefinitie.

```
parameters('<parameterName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*parameterName*> | Ja | Tekenreeks | De naam voor de parameter waarvan u de wantwaarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*parameter-value*> | Alle | De waarde voor de opgegeven parameter |
||||

*Voorbeeld*

Stel dat u deze JSON-waarde hebt:

```json
{
  "fullName": "Sophia Owen"
}
```

In dit voorbeeld wordt de waarde voor de opgegeven parameter:

```
parameters('fullName')
```

En retourneert dit resultaat: `"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>rand

Retourneert een willekeurig geheel getal uit een opgegeven bereik, dat alleen aan het begin is inbegrepen.

```
rand(<minValue>, <maxValue>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Ja | Geheel getal | Het laagste gehele getal in het bereik |
| <*maxValue*> | Ja | Geheel getal | Het gehele getal dat het hoogste gehele getal in het bereik volgt dat de functie kan retourneren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*willekeurig resultaat*> | Geheel getal | Het willekeurige gehele getal dat wordt geretourneerd uit het opgegeven bereik |
||||

*Voorbeeld*

In dit voorbeeld wordt een willekeurig geheel getal uit het opgegeven bereik, met uitzondering van de maximumwaarde:

```
rand(1, 5)
```

En retourneert een van deze getallen als resultaat: `1` `2` , , `3` of `4`

<a name="range"></a>

### <a name="range"></a>Bereik

Retourneert een matrix met gehele getallen die begint met een opgegeven geheel getal.

```
range(<startIndex>, <count>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Startindex*> | Ja | Geheel getal | Een geheel getal dat de matrix als eerste item start |
| <*Tellen*> | Ja | Geheel getal | Het aantal gehele getallen in de matrix |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*bereik-resultaat>]* | Matrix | De matrix met gehele getallen die beginnen met de opgegeven index |
||||

*Voorbeeld*

In dit voorbeeld wordt een matrix met gehele getallen gemaakt die begint met de opgegeven index en het opgegeven aantal gehele getallen heeft:

```
range(1, 4)
```

En retourneert dit resultaat: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>vervangen

Vervang een subtekenreeks door de opgegeven tekenreeks en retourneert de resultaattekenreeks. Deze functie is casegevoelig.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Ja | Tekenreeks | De tekenreeks met de subtekenreeks die moet worden vervangen |
| <*oldText*> | Ja | Tekenreeks | De subtekenreeks die moet worden vervangen |
| <*newText*> | Ja | Tekenreeks | De vervangende tekenreeks |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-text*> | Tekenreeks | De bijgewerkte tekenreeks na het vervangen van de subtekenreeks <p>Als de subtekenreeks niet wordt gevonden, retourneert u de oorspronkelijke tekenreeks. |
||||

*Voorbeeld*

In dit voorbeeld vindt u de 'oude' subtekenreeks in 'de oude tekenreeks' en vervangt u 'oud' door 'nieuw':

```
replace('the old string', 'old', 'new')
```

En retourneert dit resultaat: `"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

Verwijder een eigenschap uit een object en retourneren het bijgewerkte object. Als de eigenschap die u probeert te verwijderen niet bestaat, retourneert de functie het oorspronkelijke object.

```
removeProperty(<object>, '<property>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Object*> | Ja | Object | Het JSON-object van waar u een eigenschap wilt verwijderen |
| <*Eigenschap*> | Ja | Tekenreeks | De naam voor de eigenschap die moet worden verwijderd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | Het bijgewerkte JSON-object zonder de opgegeven eigenschap |
||||

Als u een onderliggende eigenschap uit een bestaande eigenschap wilt verwijderen, gebruikt u deze syntaxis:

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Object*> | Ja | Object | Het JSON-object waarvan u de eigenschap wilt verwijderen |
| <*bovenliggende eigenschap*> | Ja | Tekenreeks | De naam van de bovenliggende eigenschap met de onderliggende eigenschap die u wilt verwijderen |
| <*onderliggende eigenschap*> | Ja | Tekenreeks | De naam voor de onderliggende eigenschap die moet worden verwijderd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | Het bijgewerkte JSON-object waarvan u de onderliggende eigenschap hebt verwijderd |
||||

*Voorbeeld 1*

In dit voorbeeld wordt de eigenschap verwijderd uit een JSON-object, dat wordt geconverteerd van een tekenreeks naar JSON met behulp van de `middleName` [functie JSON()](#json) en het bijgewerkte object retourneert:

```
removeProperty(json('{ "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" }'), 'middleName')
```

Dit is het huidige JSON-object:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

Dit is het bijgewerkte JSON-object:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

*Voorbeeld 2*

In dit voorbeeld wordt de onderliggende eigenschap verwijderd uit een bovenliggende eigenschap in een JSON-object, dat wordt geconverteerd van een tekenreeks naar JSON met behulp van de `middleName` `customerName` functie [JSON()](#json) en het bijgewerkte object retourneert:

```
removeProperty(json('{ "customerName": { "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" } }')['customerName'], 'middleName')
```

Dit is het huidige JSON-object:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

Dit is het bijgewerkte JSON-object:

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

Retourneert de resultaten van de acties op het hoogste niveau in de opgegeven scoped actie, zoals een `For_each` `Until` actie , of `Scope` . De functie accepteert één parameter, de naam van het bereik, en retourneert een matrix die informatie bevat van de acties op het eerste niveau `result()` in dat bereik. Deze actieobjecten bevatten dezelfde kenmerken als die worden geretourneerd door de functie, zoals de `actions()` begintijd, eindtijd, status, invoer, correlatie-ID's en uitvoer van de actie.

> [!NOTE]
> Deze functie retourneert *alleen* informatie van de acties op het eerste niveau in de bereikactie en niet van dieper geneste acties, zoals switch- of voorwaardeacties.

U kunt deze functie bijvoorbeeld gebruiken om de resultaten van mislukte acties op te halen, zodat u uitzonderingen kunt diagnosticeren en afhandelen. Zie Get [context and results for failures (Context en resultaten voor fouten verkrijgen) voor meer informatie.](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures)

```
result('<scopedActionName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*scopedActionName*> | Ja | Tekenreeks | De naam van de actie binnen het bereik waar u de invoer en uitvoer van de acties op het hoogste niveau binnen dat bereik wilt |
||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*array-object*> | Matrixobject | Een matrix met matrices met invoer en uitvoer van elke actie op het hoogste niveau binnen het opgegeven bereik |
||||

*Voorbeeld*

In dit voorbeeld worden de in- en uitvoer van elke iteratie van een HTTP-actie in een lus retourneert met behulp van de functie `For_each` `result()` in de `Compose` actie:

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

Hier ziet u hoe de voorbeeld geretourneerde matrix eruit kan zien waar het buitenste object de invoer en uitvoer van elke iteratie van de acties in de `outputs` actie `For_each` bevat.

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

### <a name="setproperty"></a>Eigenschapinstellen

Stel de waarde voor de eigenschap van het JSON-object in en retourneert het bijgewerkte object. Als de eigenschap die u probeert in te stellen niet bestaat, wordt de eigenschap toegevoegd aan het -object. Als u een nieuwe eigenschap wilt toevoegen, gebruikt [u de functie addProperty().](#addProperty)

```
setProperty(<object>, '<property>', <value>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Object*> | Ja | Object | Het JSON-object waarvan u de eigenschap wilt instellen |
| <*Eigenschap*> | Ja | Tekenreeks | De naam voor de bestaande of nieuwe eigenschap die moet worden ingesteld |
| <*Waarde*> | Ja | Alle | De waarde die moet worden ingesteld voor de opgegeven eigenschap |
|||||

Als u de onderliggende eigenschap in een onderliggend object wilt instellen, gebruikt u in plaats daarvan een geneste `setProperty()` aanroep. Anders retourneert de functie alleen het onderliggende object als uitvoer.

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Object*> | Ja | Object | Het JSON-object waarvan u de eigenschap wilt instellen |
| <*bovenliggende eigenschap*> | Ja | Tekenreeks | De naam van de bovenliggende eigenschap met de onderliggende eigenschap die u wilt instellen |
| <*onderliggende eigenschap*> | Ja | Tekenreeks | De naam voor de onderliggende eigenschap die moet worden ingesteld |
| <*Waarde*> | Ja | Alle | De waarde die moet worden ingesteld voor de opgegeven eigenschap |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | Het bijgewerkte JSON-object waarvan u de eigenschap hebt ingesteld |
||||

*Voorbeeld 1*

In dit voorbeeld stelt u `surName` de eigenschap in een JSON-object in, dat wordt geconverteerd van een tekenreeks naar JSON met behulp van de functie [JSON().](#json) De functie wijst de opgegeven waarde toe aan de eigenschap en retourneert het bijgewerkte object:

```
setProperty(json('{ "firstName": "Sophia", "surName": "Owen" }'), 'surName', 'Hartnett')
```

Dit is het huidige JSON-object:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Dit is het bijgewerkte JSON-object:

```json
{
   "firstName": "Sophia",
   "surName": "Hartnett"
}
```

*Voorbeeld 2*

In dit voorbeeld wordt de onderliggende eigenschap voor de bovenliggende eigenschap in een JSON-object, die wordt geconverteerd van een tekenreeks naar JSON met behulp van de `surName` `customerName` functie [JSON().](#json) De functie wijst de opgegeven waarde toe aan de eigenschap en retourneert het bijgewerkte object:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'surName', 'Hartnett'))
```

Dit is het huidige JSON-object:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Owen"
   }
}
```

Dit is het bijgewerkte JSON-object:

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

Items vooraan in een verzameling verwijderen en alle *andere* items retourneren.

```
skip([<collection>], <count>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Collectie*> | Ja | Matrix | De verzameling waarvan u de items wilt verwijderen |
| <*Tellen*> | Ja | Geheel getal | Een positief geheel getal voor het aantal items dat aan de voorzijde moet worden verwijderd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*bijgewerkte verzameling>]* | Matrix | De bijgewerkte verzameling na het verwijderen van de opgegeven items |
||||

*Voorbeeld*

In dit voorbeeld wordt één item, het getal 0, van de voorkant van de opgegeven matrix verwijderd:

```
skip(createArray(0, 1, 2, 3), 1)
```

En retourneert deze matrix met de resterende items: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Retourneert een matrix die subtekenreeksen bevat, gescheiden door komma's, op basis van het opgegeven scheidingsteken in de oorspronkelijke tekenreeks.

```
split('<text>', '<delimiter>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Ja | Tekenreeks | De tekenreeks die moet worden gescheiden in subtekenreeksen op basis van het opgegeven scheidingsteken in de oorspronkelijke tekenreeks |
| <*Scheidingsteken*> | Ja | Tekenreeks | Het teken in de oorspronkelijke tekenreeks dat moet worden gebruikt als scheidingsteken |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*substring1*>,<*substring2*>,...] | Matrix | Een matrix met subtekenreeksen uit de oorspronkelijke tekenreeks, gescheiden door komma's |
||||

*Voorbeeld*

In dit voorbeeld wordt een matrix gemaakt met subtekenreeksen van de opgegeven tekenreeks op basis van het opgegeven teken als scheidingsteken:

```
split('a_b_c', '_')
```

En retourneert deze matrix als het resultaat: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Retourneerde het begin van de dag voor een tijdstempel.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die het tijdstempel bevat |
| <*Formaat*> | Nee | Tekenreeks | Eén [opmaakopwijzer of](/dotnet/standard/base-types/standard-date-and-time-format-strings) een [patroon voor aangepaste notatie.](/dotnet/standard/base-types/custom-date-and-time-format-strings) De standaardindeling voor de tijdstempel is ['o'](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Tekenreeks | Het opgegeven tijdstempel, maar beginnend bij de markering van nul uur voor de dag |
||||

*Voorbeeld*

In dit voorbeeld wordt het begin van de dag voor deze tijdstempel gevonden:

```
startOfDay('2018-03-15T13:30:30Z')
```

En retourneert dit resultaat: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Retourneerde het begin van het uur voor een tijdstempel.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die het tijdstempel bevat |
| <*Formaat*> | Nee | Tekenreeks | Eén [opmaakopwijzer of](/dotnet/standard/base-types/standard-date-and-time-format-strings) een [patroon voor aangepaste notatie.](/dotnet/standard/base-types/custom-date-and-time-format-strings) De standaardindeling voor de tijdstempel is ['o'](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Tekenreeks | Het opgegeven tijdstempel, maar beginnend bij de markering van nul minuten voor het uur |
||||

*Voorbeeld*

In dit voorbeeld wordt het begin van het uur voor dit tijdstempel gevonden:

```
startOfHour('2018-03-15T13:30:30Z')
```

En retourneert dit resultaat: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Retourneerde het begin van de maand voor een tijdstempel.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die het tijdstempel bevat |
| <*Formaat*> | Nee | Tekenreeks | Eén [opmaakopwijzer of](/dotnet/standard/base-types/standard-date-and-time-format-strings) een [patroon voor aangepaste notatie.](/dotnet/standard/base-types/custom-date-and-time-format-strings) De standaardindeling voor de tijdstempel is ['o'](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Tekenreeks | Het opgegeven tijdstempel, maar beginnend op de eerste dag van de maand bij de markering van nul uur |
||||

*Voorbeeld 1*

Dit voorbeeld retourneert het begin van de maand voor deze tijdstempel:

```
startOfMonth('2018-03-15T13:30:30Z')
```

En retourneert dit resultaat: `"2018-03-01T00:00:00.0000000Z"`

*Voorbeeld 2*

In dit voorbeeld wordt het begin van de maand in de opgegeven notatie voor deze tijdstempel:

```
startOfMonth('2018-03-15T13:30:30Z', 'yyyy-MM-dd')
```

En retourneert dit resultaat: `"2018-03-01"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Controleer of een tekenreeks begint met een specifieke subtekenreeks.
Retourneert true wanneer de subtekenreeks wordt gevonden of retourneert false wanneer deze niet is gevonden.
Deze functie is niet casegevoelig.

```
startsWith('<text>', '<searchText>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Ja | Tekenreeks | De tekenreeks die u wilt controleren |
| <*searchText*> | Ja | Tekenreeks | De beginreeks die moet worden gevonden |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar  | Booleaans | Retourneert true wanneer de beginsubtekenreeks wordt gevonden. Retourneren onwaar wanneer deze niet wordt gevonden. |
||||

*Voorbeeld 1*

In dit voorbeeld wordt gecontroleerd of de 'hallo wereld'-tekenreeks begint met de subtekenreeks 'hello':

```
startsWith('hello world', 'hello')
```

En retourneert dit resultaat: `true`

*Voorbeeld 2*

In dit voorbeeld wordt gecontroleerd of de 'hallo wereld'-tekenreeks begint met de subtekenreeks 'greetings':

```
startsWith('hello world', 'greetings')
```

En retourneert dit resultaat: `false`

<a name="string"></a>

### <a name="string"></a>tekenreeks

Retourneert de tekenreeksversie voor een waarde.

```
string(<value>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Alle | De waarde die moet worden ge converteert. Als deze waarde null is of als null wordt geëvalueerd, wordt de waarde geconverteerd naar een lege tekenreekswaarde ( `""` ). <p><p>Als u bijvoorbeeld een tekenreeksvariabele toewijst aan een niet-bestaande eigenschap, die u kunt openen met de operator , wordt de null-waarde geconverteerd naar `?` een lege tekenreeks. Het vergelijken van een null-waarde is echter niet hetzelfde als het vergelijken van een lege tekenreeks. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*string-value*> | Tekenreeks | De tekenreeksversie voor de opgegeven waarde. Als de *waardeparameter* null is of als null wordt geëvalueerd, wordt deze waarde geretourneerd als een lege tekenreekswaarde ( `""` ). |
||||





*Voorbeeld 1*

In dit voorbeeld wordt de tekenreeksversie voor dit nummer gemaakt:

```
string(10)
```

En retourneert dit resultaat: `"10"`

*Voorbeeld 2*

In dit voorbeeld wordt een tekenreeks gemaakt voor het opgegeven JSON-object en wordt het backslash-teken () gebruikt als een escape-teken voor het dubbele aanhalingsteken \\ ().

```
string( { "name": "Sophie Owen" } )
```

En retourneert dit resultaat: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

Retourneert het resultaat van het aftrekken van het tweede getal van het eerste getal.

```
sub(<minuend>, <subtrahend>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | Ja | Geheel getal of float | Het getal waarvan de afgetrokken waarde *moet worden afgetrokken* |
| <*onderbegrip*> | Ja | Geheel getal of float | Het getal dat moet worden afgetrokken van *het minuend* |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*Resultaat*> | Geheel getal of float | Het resultaat van het aftrekken van het tweede getal van het eerste getal |
||||

*Voorbeeld*

In dit voorbeeld wordt het tweede getal afgetrokken van het eerste getal:

```
sub(10.3, .3)
```

En retourneert dit resultaat: `10`

<a name="substring"></a>

### <a name="substring"></a>Subtekenreeks

Retourneert tekens uit een tekenreeks, beginnend vanaf de opgegeven positie of index. Indexwaarden beginnen met het getal 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Ja | Tekenreeks | De tekenreeks waarvan u de tekens wilt |
| <*Startindex*> | Ja | Geheel getal | Een positief getal dat gelijk is aan of groter is dan 0 dat u wilt gebruiken als beginpositie of indexwaarde |
| <*Lengte*> | Nee | Geheel getal | Een positief aantal tekens dat u wilt gebruiken in de subtekenreeks |
|||||

> [!NOTE]
> Zorg ervoor dat de som van het toevoegen van de parameterwaarden *startIndex* en *length* kleiner is dan de lengte van de tekenreeks die u opgeeft voor de *tekstparameter.*
> Anders krijgt u een foutmelding, in tegenstelling tot vergelijkbare functies in andere talen, waarbij het resultaat de subtekenreeks is van *startIndex* tot het einde van de tekenreeks. De *lengteparameter* is optioneel en indien niet opgegeven, neemt de **functie substring()** alle tekens die beginnen *vanaf startIndex* tot het einde van de tekenreeks.

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*subtekenreeksresultaat*> | Tekenreeks | Een subtekenreeks met het opgegeven aantal tekens, beginnend bij de opgegeven indexpositie in de brontekenreeks |
||||

*Voorbeeld*

In dit voorbeeld wordt een subtekenreeks van vijf tekens gemaakt op basis van de opgegeven tekenreeks, beginnend bij de indexwaarde 6:

```
substring('hello world', 6, 5)
```

En retourneert dit resultaat: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Een aantal tijdseenheden van een tijdstempel aftrekken.
Zie ook [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die het tijdstempel bevat |
| <*Interval*> | Ja | Geheel getal | Het aantal opgegeven tijdeenheden dat moet worden afgetrokken |
| <*timeUnit*> | Ja | Tekenreeks | De tijdseenheid die moet worden gebruikt met *interval:*"Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*Formaat*> | Nee | Tekenreeks | Eén [opmaakopwijzer of](/dotnet/standard/base-types/standard-date-and-time-format-strings) een [patroon voor aangepaste notatie.](/dotnet/standard/base-types/custom-date-and-time-format-strings) De standaardindeling voor de tijdstempel is ['o'](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Tekenreeks | Het tijdstempel min het opgegeven aantal tijdeenheden |
||||

*Voorbeeld 1*

In dit voorbeeld wordt één dag afgetrokken van dit tijdstempel:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

En retourneert dit resultaat: `"2018-01-01T00:00:00.0000000Z"`

*Voorbeeld 2*

In dit voorbeeld wordt één dag van deze tijdstempel afgetrokken:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

En retourneert dit resultaat met de optionele indeling 'D': `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take

Items retourneren vanaf het voorste deel van een verzameling.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Collectie*> | Ja | Tekenreeks of matrix | De verzameling waarvan u de want items wilt |
| <*Tellen*> | Ja | Geheel getal | Een positief geheel getal voor het aantal items dat u van voor af aan wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*subset*> of [<*subset*>] | Respectievelijk tekenreeks of matrix | Een tekenreeks of matrix met het opgegeven aantal items dat is overgenomen van de voorkant van de oorspronkelijke verzameling |
||||

*Voorbeeld*

In deze voorbeelden wordt het opgegeven aantal items aan de voorzijde van deze verzamelingen opgeslagen:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

En deze resultaten retourneren:

* Eerste voorbeeld: `"abc"`
* Tweede voorbeeld: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>Teken

Retourneert het aantal tikken( intervallen van 100 nanoseconden), sinds 1 januari 0001 12:00:00 middernacht (of DateTime.Ticks in C#) tot aan het opgegeven tijdstempel. Zie voor meer informatie dit onderwerp: [DateTime.Ticks Property (System)](/dotnet/api/system.datetime.ticks).

```
ticks('<timestamp>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks voor een tijdstempel |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*ticks-number*> | Geheel getal | Het aantal tikken sinds de opgegeven tijdstempel |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Een tekenreeks retourneren in kleine letters. Als een teken in de tekenreeks geen kleine letter heeft, blijft dat teken ongewijzigd in de geretourneerde tekenreeks.

```
toLower('<text>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Ja | Tekenreeks | De tekenreeks die moet worden retourneren in kleine letters |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*kleine letters*> | Tekenreeks | De oorspronkelijke tekenreeks in kleine letters |
||||

*Voorbeeld*

In dit voorbeeld wordt deze tekenreeks ge converteert naar kleine letters:

```
toLower('Hello World')
```

En retourneert dit resultaat: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>Toupper

Een tekenreeks retourneren in hoofdletters. Als een teken in de tekenreeks geen hoofdletterversie heeft, blijft dat teken ongewijzigd in de geretourneerde tekenreeks.

```
toUpper('<text>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Ja | Tekenreeks | De tekenreeks die moet worden retourneren in hoofdletters |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*hoofdletters en tekst*> | Tekenreeks | De oorspronkelijke tekenreeks in hoofdletters |
||||

*Voorbeeld*

In dit voorbeeld wordt deze tekenreeks ge converteert naar hoofdletters:

```
toUpper('Hello World')
```

En retourneert dit resultaat: `"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>activeren

Retourneert de uitvoer van een trigger tijdens runtime, of waarden van andere JSON-naam-en-waardeparen, die u aan een expressie kunt toewijzen.

* Binnen de invoer van een trigger retourneert deze functie de uitvoer van de vorige uitvoering.

* Binnen de voorwaarde van een trigger retourneert deze functie de uitvoer van de huidige uitvoering.

De functie verwijst standaard naar het hele triggerobject, maar u kunt eventueel een eigenschap opgeven waarvan u de wantwaarde wilt.
Voor deze functie zijn ook verkorte versies beschikbaar. Zie [triggerOutputs()](#triggerOutputs) en [triggerBody()](#triggerBody).

```
trigger()
```

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*trigger-output*> | Tekenreeks | De uitvoer van een trigger tijdens runtime |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

De uitvoer van een trigger `body` retourneren tijdens runtime.
Steno voor `trigger().outputs.body` .
Zie [trigger()](#trigger).

```
triggerBody()
```

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*trigger-body-output*> | Tekenreeks | De `body` uitvoer van de trigger |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Retourneert een matrix met waarden die overeenkomen met een sleutelnaam in de uitvoer van een trigger met *form-data* of *form-encoded.*

```
triggerFormDataMultiValues('<key>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Sleutel*> | Ja | Tekenreeks | De naam van de sleutel waarvan u de waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*array-with-key-values>]* | Matrix | Een matrix met alle waarden die overeenkomen met de opgegeven sleutel |
||||

*Voorbeeld*

In dit voorbeeld wordt een matrix gemaakt op basis van de sleutelwaarde 'feedUrl' in de uitvoer van een RSS-trigger op basis van formuliergegevens of formuliercode:

```
triggerFormDataMultiValues('feedUrl')
```

En retourneert deze matrix als voorbeeld: `["https://feeds.a.dj.com/rss/RSSMarketsMain.xml"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Retourneert een tekenreeks met één waarde die overeenkomt met een sleutelnaam in de uitvoer van een trigger met *form-data* of *form-encoded.*
Als de functie meer dan één overeenkomst vindt, wordt er een foutmelding weergegeven.

```
triggerFormDataValue('<key>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Sleutel*> | Ja | Tekenreeks | De naam van de sleutel waarvan u de waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*sleutelwaarde*> | Tekenreeks | De waarde in de opgegeven sleutel |
||||

*Voorbeeld*

In dit voorbeeld wordt een tekenreeks gemaakt van de sleutelwaarde 'feedUrl' in de uitvoer van een RSS-trigger met formuliergegevens of met formulier gecodeerd:

```
triggerFormDataValue('feedUrl')
```

En retourneert deze tekenreeks als voorbeeld: `"https://feeds.a.dj.com/rss/RSSMarketsMain.xml"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

De body retourneren voor een specifiek onderdeel in de uitvoer van een trigger die meerdere delen heeft.

```
triggerMultipartBody(<index>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Index*> | Ja | Geheel getal | De indexwaarde voor het onderdeel dat u wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*Lichaam*> | Tekenreeks | De body voor het opgegeven deel in de uitvoer van een trigger met meerdere onderdelen |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Retourneert de uitvoer van een trigger tijdens runtime of waarden van andere JSON-naam-en-waardeparen.
Steno voor `trigger().outputs` .
Zie [trigger()](#trigger).

```
triggerOutputs()
```

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*trigger-output*> | Tekenreeks | De uitvoer van een trigger tijdens runtime  |
||||

<a name="trim"></a>

### <a name="trim"></a>Trim

Verwijder vooraan en achter elkaar witruimte uit een tekenreeks en retourneert de bijgewerkte tekenreeks.

```
trim('<text>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Ja | Tekenreeks | De tekenreeks met de vooraanstaande en aaneen volgende witruimte die moet worden verwijderd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updatedText*> | Tekenreeks | Een bijgewerkte versie voor de oorspronkelijke tekenreeks zonder voor- of achteraan lopende witruimte |
||||

*Voorbeeld*

In dit voorbeeld wordt de vooraanstaande en aaneen volgende witruimte verwijderd uit de tekenreeks " Hallo wereld ":

```
trim(' Hello World  ')
```

En retourneert dit resultaat: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>Unie

Retourneert een verzameling die *alle* items uit de opgegeven verzamelingen bevat.
Om in het resultaat te worden weergegeven, kan een item worden weergegeven in elke verzameling die aan deze functie wordt doorgegeven. Als een of meer items dezelfde naam hebben, wordt het laatste item met die naam in het resultaat weergegeven.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ...  | Ja | Matrix of object, maar niet beide | De verzamelingen van waar u *alle* items wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | Matrix of object, respectievelijk | Een verzameling met alle items uit de opgegeven verzamelingen - geen duplicaten |
||||

*Voorbeeld*

In dit voorbeeld worden *alle* items uit deze verzamelingen ophalen:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

En retourneert dit resultaat: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Retourneert een met uniform resource identifier (URI) gecodeerde versie voor een tekenreeks door URL-onveilige tekens te vervangen door escape-tekens.
Gebruik deze functie in plaats [van encodeUriComponent()](#encodeUriComponent).
Hoewel beide functies op dezelfde manier werken, `uriComponent()` heeft de voorkeur.

```
uriComponent('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De tekenreeks die moet worden ge converteerd naar URI-gecodeerde indeling |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | Tekenreeks | De met URI gecodeerde tekenreeks met escapetekens |
||||

*Voorbeeld*

In dit voorbeeld wordt een met URI gecodeerde versie voor deze tekenreeks gemaakt:

```
uriComponent('https://contoso.com')
```

En retourneert dit resultaat: `"https%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Retourneren van de binaire versie voor een URI-onderdeel (Uniform Resource Identifier).

```
uriComponentToBinary('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De met URI gecodeerde tekenreeks die moet worden ge converteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*binary-for-encoded-uri*> | Tekenreeks | De binaire versie voor de tekenreeks met URI-codering. De binaire inhoud wordt base64-gecodeerd en vertegenwoordigd door `$content` . |
||||

*Voorbeeld*

In dit voorbeeld wordt de binaire versie voor deze met URI gecodeerde tekenreeks gemaakt:

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

Retourneert de tekenreeksversie voor een met uniform resource identifier (URI) gecodeerde tekenreeks, en decodeert de met URI gecodeerde tekenreeks effectief.

```
uriComponentToString('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De met URI gecodeerde tekenreeks die moet worden gedecodeerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | Tekenreeks | De gedecodeerde versie voor de tekenreeks met URI-codering |
||||

*Voorbeeld*

In dit voorbeeld wordt de gedecodeerde tekenreeksversie voor deze tekenreeks met URI-codering gemaakt:

```
uriComponentToString('https%3A%2F%2Fcontoso.com')
```

En retourneert dit resultaat: `"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

`host`Retourneert de waarde voor een uniform resource identifier (URI).

```
uriHost('<uri>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Tekenreeks | De URI waarvan `host` u de wantwaarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*hostwaarde*> | Tekenreeks | De `host` waarde voor de opgegeven URI |
||||

*Voorbeeld*

In dit voorbeeld wordt de `host` waarde voor deze URI gevonden:

```
uriHost('https://www.localhost.com:8080')
```

En retourneert dit resultaat: `"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

`path`Retourneert de waarde voor een uniform resource identifier (URI).

```
uriPath('<uri>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Tekenreeks | De URI waarvan `path` u de wantwaarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*path-value*> | Tekenreeks | De `path` waarde voor de opgegeven URI. Als `path` geen waarde heeft, retourneert u het teken /. |
||||

*Voorbeeld*

In dit voorbeeld wordt de `path` waarde voor deze URI gevonden:

```
uriPath('https://www.contoso.com/catalog/shownew.htm?date=today')
```

En retourneert dit resultaat: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

`path`Retourneert de waarden en voor een uniform resource identifier `query` (URI).

```
uriPathAndQuery('<uri>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Tekenreeks | De URI waarvan `path` en `query` waarden u wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*path-query-value*> | Tekenreeks | De `path` waarden en voor de opgegeven `query` URI. Als `path` geen waarde opgeeft, retourneert u het teken /. |
||||

*Voorbeeld*

In dit voorbeeld worden de `path` waarden en voor deze `query` URI gevonden:

```
uriPathAndQuery('https://www.contoso.com/catalog/shownew.htm?date=today')
```

En retourneert dit resultaat: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

`port`Retourneert de waarde voor een uniform resource identifier (URI).

```
uriPort('<uri>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Tekenreeks | De URI waarvan `port` u de wantwaarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*poortwaarde*> | Geheel getal | De `port` waarde voor de opgegeven URI. Als `port` geen waarde opgeeft, retourneert u de standaardpoort voor het protocol. |
||||

*Voorbeeld*

In dit voorbeeld wordt de `port` waarde voor deze URI retourneert:

```
uriPort('https://www.localhost:8080')
```

En retourneert dit resultaat: `8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

`query`Retourneert de waarde voor een uniform resource identifier (URI).

```
uriQuery('<uri>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Tekenreeks | De URI waarvan `query` u de wantwaarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*query-value*> | Tekenreeks | De `query` waarde voor de opgegeven URI |
||||

*Voorbeeld*

In dit voorbeeld wordt de `query` waarde voor deze URI retourneert:

```
uriQuery('https://www.contoso.com/catalog/shownew.htm?date=today')
```

En retourneert dit resultaat: `"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

`scheme`Retourneert de waarde voor een uniform resource identifier (URI).

```
uriScheme('<uri>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Tekenreeks | De URI waarvan `scheme` u de wantwaarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*scheme-value*> | Tekenreeks | De `scheme` waarde voor de opgegeven URI |
||||

*Voorbeeld*

In dit voorbeeld wordt de `scheme` waarde voor deze URI retourneert:

```
uriScheme('https://www.contoso.com/catalog/shownew.htm?date=today')
```

En retourneert dit resultaat: `"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Retourneerde de huidige tijdstempel.

```
utcNow('<format>')
```

U kunt eventueel een andere indeling opgeven met de *<-indeling>* parameter.


| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Formaat*> | Nee | Tekenreeks | Eén [opmaakopwijzer of](/dotnet/standard/base-types/standard-date-and-time-format-strings) een [patroon voor aangepaste notatie.](/dotnet/standard/base-types/custom-date-and-time-format-strings) De standaardindeling voor de tijdstempel is ['o'](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), die voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*current-timestamp*> | Tekenreeks | De huidige datum en tijd |
||||

*Voorbeeld 1*

Stel dat het vandaag 15 april 2018 is om 13:00:00 uur.
In dit voorbeeld wordt het huidige tijdstempel:

```
utcNow()
```

En retourneert dit resultaat: `"2018-04-15T13:00:00.0000000Z"`

*Voorbeeld 2*

Stel dat het vandaag 15 april 2018 is om 13:00:00 uur.
In dit voorbeeld wordt de huidige tijdstempel op basis van de optionele indeling 'D' gebruikt:

```
utcNow('D')
```

En retourneert dit resultaat: `"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>Variabelen

Retourneert de waarde voor een opgegeven variabele.

```
variables('<variableName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*variableName*> | Ja | Tekenreeks | De naam van de variabele waarvan u de waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*variable-value*> | Alle | De waarde voor de opgegeven variabele |
||||

*Voorbeeld*

Stel dat de huidige waarde voor een variabele 'numItems' 20 is.
In dit voorbeeld wordt de waarde voor een geheel getal voor deze variabele:

```
variables('numItems')
```

En retourneert dit resultaat: `20`

<a name="workflow"></a>

### <a name="workflow"></a>werkstroom

Retourneert alle details over de werkstroom zelf tijdens de run time.

```
workflow().<property>
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Eigenschap*> | Nee | Tekenreeks | De naam voor de werkstroom-eigenschap waarvan u de wantwaarde wilt <p><p>Een werkstroomobject heeft standaard de volgende eigenschappen: `name` , , , , en `type` `id` `location` `run` `tags` . <p><p>- De `run` eigenschapswaarde is een JSON-object dat de volgende eigenschappen bevat: `name` , en `type` `id` . <p><p>- De eigenschap is een JSON-object dat tags bevat die zijn gekoppeld aan uw logische app in Azure Logic Apps of stroom in Power Automate en de waarden `tags` voor deze tags. [](../azure-resource-manager/management/tag-resources.md) Voor meer informatie over tags in Azure-resources, bekijkt u [Resources, resourcegroepen](../azure-resource-manager/management/tag-resources.md)en abonnementen taggen voor logische organisatie in Azure. <p><p>**Opmerking:** een logische app heeft standaard geen tags, maar een Power Automate stroom heeft de `flowDisplayName` `environmentName` tags en . |
|||||

*Voorbeeld 1*

In dit voorbeeld wordt de naam van de huidige run van een werkstroom retourneert:

`workflow().run.name`

*Voorbeeld 2*

Als u Power Automate, kunt u een expressie maken die gebruikmaakt van de uitvoer-eigenschap om de waarden op te halen uit de eigenschap `@workflow()` `tags` of van uw `flowDisplayName` `environmentName` stroom.

U kunt bijvoorbeeld aangepaste e-mailmeldingen verzenden vanuit de stroom zelf die een koppeling naar uw stroom maken. Deze meldingen kunnen een HTML-koppeling bevatten die de weergavenaam van de stroom in de e-mailtitel bevat en de volgende syntaxis volgt:

`<a href=https://flow.microsoft.com/manage/environments/@{workflow()['tags']['environmentName']}/flows/@{workflow()['name']}/details>Open flow @{workflow()['tags']['flowDisplayName']}</a>`

<a name="xml"></a>

### <a name="xml"></a>xml

Retourneert de XML-versie voor een tekenreeks die een JSON-object bevat.

```
xml('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De tekenreeks met het JSON-object dat u wilt converteren <p>Het JSON-object moet slechts één hoofd eigenschap hebben, wat geen matrix mag zijn. <br>Gebruik het backslash-teken ( \\ ) als een escape-teken voor het dubbele aanhalingsteken (). |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*xml-version*> | Object | De gecodeerde XML voor de opgegeven tekenreeks of het JSON-object |
||||

*Voorbeeld 1*

In dit voorbeeld wordt de XML-versie voor deze tekenreeks gemaakt, die een JSON-object bevat:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

En retourneert deze resultaat-XML:

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

In dit voorbeeld wordt XML gemaakt voor een tekenreeks die dit JSON-object bevat:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

En retourneert deze resultaat-XML:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>Xpath

Controleer XML op knooppunten of waarden die overeenkomen met een XPath-expressie (XML Path Language) en retourneert de overeenkomende knooppunten of waarden. Met een XPath-expressie, of gewoon 'XPath', kunt u door een XML-documentstructuur navigeren, zodat u knooppunten of rekenwaarden in de XML-inhoud kunt selecteren.

```
xpath('<xml>', '<xpath>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Xml*> | Ja | Alle | De XML-tekenreeks om te zoeken naar knooppunten of waarden die overeenkomen met een XPath-expressiewaarde |
| <*Xpath*> | Ja | Alle | De XPath-expressie die wordt gebruikt om overeenkomende XML-knooppunten of -waarden te vinden |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*xml-node*> | XML | Een XML-knooppunt wanneer slechts één knooppunt overeenkomt met de opgegeven XPath-expressie |
| <*Waarde*> | Alle | De waarde van een XML-knooppunt wanneer slechts één waarde overeenkomt met de opgegeven XPath-expressie |
| [<*xml-node1*>, <*xml-node2*>, ...] </br>-of- </br>[<*value1*>, <*value2*>, ...] | Matrix | Een matrix met XML-knooppunten of -waarden die overeenkomen met de opgegeven XPath-expressie |
||||

*Voorbeeld 1*

Stel dat u deze `'items'` XML-tekenreeks hebt: 

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

In dit voorbeeld wordt de XPath-expressie, , gebruikt om de knooppunten te vinden die overeenkomen met het knooppunt in de XML-tekenreeks en wordt een matrix met deze `'/produce/item/name'` `<name></name>` `'items'` knooppuntwaarden retourneert:

`xpath(xml(parameters('items')), '/produce/item/name')`

In het voorbeeld wordt ook de [functie parameters()](#parameters) gebruikt om de XML-tekenreeks op te halen uit en de tekenreeks te converteren naar XML-indeling met behulp `'items'` van de functie [xml().](#xml)

Hier is de resultaat matrix met de knooppunten die overeenkomen `<name></name` met :

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Voorbeeld 2*

In voorbeeld 1 wordt in dit voorbeeld de XPath-expressie, , door gegeven om het eerste element te vinden dat het onderliggende element `'/produce/item/name[1]'` `name` van het element `item` is.

`xpath(xml(parameters('items')), '/produce/item/name[1]')`

Dit is het resultaat: `Gala`

*Voorbeeld 3*

In voorbeeld 1 wordt in dit voorbeeld de XPath-expressie, , door gegeven om het laatste element te vinden dat het onderliggende element `'/produce/item/name[last()]'` `name` van het element `item` is.

`xpath(xml(parameters('items')), '/produce/item/name[last()]')`

Dit is het resultaat: `Honeycrisp`

*Voorbeeld 4*

Stel in dit voorbeeld dat uw `items` XML-tekenreeks ook de kenmerken bevat, `expired='true'` en `expired='false'` :

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

In dit voorbeeld wordt de XPath-expressie, , gebruikt `'//name[@expired]'` om alle elementen te vinden die het kenmerk `name` `expired` hebben:

`xpath(xml(parameters('items')), '//name[@expired]')`

Dit is het resultaat: `[ Gala, Honeycrisp ]`

*Voorbeeld 5*

Stel in dit voorbeeld dat uw `items` XML-tekenreeks alleen dit kenmerk bevat: `expired = 'true'` :

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

In dit voorbeeld wordt de XPath-expressie, , gebruikt om alle elementen te vinden `'//name[@expired = 'true']'` `name` die het kenmerk hebben: `expired = 'true'`

`xpath(xml(parameters('items')), '//name[@expired = 'true']')`

Dit is het resultaat: `[ Gala ]`

*Voorbeeld 6*

Stel in dit voorbeeld dat uw `items` XML-tekenreeks ook de volgende kenmerken bevat: 

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

In dit voorbeeld wordt de XPath-expressie, , gebruikt `'//name[price>35]'` om alle elementen met te `name` `price > 35` zoeken:

`xpath(xml(parameters('items')), '//name[price>35]')`

Dit is het resultaat: `Honeycrisp`

*Voorbeeld 7*

Stel in dit voorbeeld dat uw `items` XML-tekenreeks hetzelfde is als in voorbeeld 1:

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

In dit voorbeeld worden knooppunten gevonden die overeenkomen met het knooppunt en `<count></count>` worden deze knooppuntwaarden toegevoegd met de `sum()` functie :

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

Dit is het resultaat: `30`

*Voorbeeld 8*

Stel dat u in dit voorbeeld deze XML-tekenreeks hebt, die de XML-documentnaamruimte bevat, `xmlns="https://contoso.com"` :

```xml
<?xml version="1.0"?><file xmlns="https://contoso.com"><location>Paris</location></file>
```

Deze expressies gebruiken de XPath-expressie `/*[name()="file"]/*[name()="location"]` of `/*[local-name()="file" and namespace-uri()="https://contoso.com"]/*[local-name()="location"]` om knooppunten te vinden die overeenkomen met het `<location></location>` knooppunt. In deze voorbeelden ziet u de syntaxis die u gebruikt in de ontwerper van logische apps of in de expressie-editor:

* `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`
* `xpath(xml(body('Http')), '/*[local-name()="file" and namespace-uri()="https://contoso.com"]/*[local-name()="location"]')`

Dit is het resultaat-knooppunt dat overeenkomt met het `<location></location>` knooppunt: 

`<location xmlns="https://contoso.com">Paris</location>`

> [!IMPORTANT]
>
> Als u in de codeweergave werkt, kunt u het dubbele aanhalingsteken (") escapen met behulp van het backslash-teken ( \\ ). 
> U moet bijvoorbeeld escapetekens gebruiken wanneer u een expressie serialiseert als een JSON-tekenreeks. 
> Als u echter in de logic appontwerper of expressie-editor werkt, hoeft u het dubbele aanhalingsteken niet te escapen omdat het backslash-teken automatisch wordt toegevoegd aan de onderliggende definitie, bijvoorbeeld:
> 
> * Codeweergave: `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`
>
> * Expressie-editor: `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`

*Voorbeeld 9*

In voorbeeld 8 wordt in dit voorbeeld gebruikgemaakt van de XPath-expressie, , om de waarde in het `'string(/*[name()="file"]/*[name()="location"])'` knooppunt `<location></location>` te vinden:

`xpath(xml(body('Http')), 'string(/*[name()="file"]/*[name()="location"])')`

Dit is het resultaat: `Paris`

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [definitietaal van de werkstroom](../logic-apps/logic-apps-workflow-definition-language.md)
