---
title: Door gebruiker gedefinieerde JavaScript-functies in Azure Stream Analytics
description: Dit artikel is een inleiding tot door de gebruiker gedefinieerde JavaScript-functies in Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc, devx-track-js
ms.date: 12/15/2020
ms.openlocfilehash: 70015ef24039694789ce96a6c4853221fe2377c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020380"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>Door gebruiker gedefinieerde JavaScript-functies in Stream Analytics
 
Azure Stream Analytics ondersteunt door de gebruiker gedefinieerde functies die zijn geschreven in JavaScript. Met de uitgebreide set van **String**-, **RegExp**-, **Math**-, **Array**- en **Date**-methoden van JavaScript kunt u gemakkelijker complexe gegevenstransformaties maken met Stream Analytics-taken.

## <a name="overview"></a>Overzicht

Door de gebruiker gedefinieerde JavaScript-functies ondersteunen staatloze, scalaire rekenfuncties die geen externe verbinding nodig hebben. De resultaatwaarde van een functie mag alleen een scalaire (enkelvoudige) waarde zijn. Nadat u een door de gebruiker gedefinieerde JavaScript-functie aan een taak hebt toegevoegd, kunt u de functie overal in de query als een ingebouwde scalaire functie gebruiken.

Hier volgen enkele scenario's waarin door de gebruiker gedefinieerde JavaScript-functies mogelijk interessant kunnen zijn:
* Het parseren en manipuleren van tekenreeksen die functies met reguliere expressies bevatten, bijvoorbeeld **Regexp_Replace()** en **Regexp_Extract()**
* Het (de)coderen van gegevens, bijvoorbeeld bij een conversie van binair naar hexadecimaal
* Het maken van rekenkundige berekeningen met **Math**-functies van JavaScript
* Het maken van matrixbewerkingen zoals sorteren, samenvoegen, zoeken en vullen

Hier volgen enkele dingen die u met een door de gebruiker gedefinieerde JavaScript-functie niet kunt doen in Stream Analytics:
* Externe REST-eindpunten aanroepen, bijvoorbeeld, het maken van een reverse IP-lookup of het ophalen van referentiegegevens uit een externe bron
* Serialisatie of deserialisatie van gebeurtenissen met aangepaste indeling uitvoeren voor invoer/uitvoer
* Aangepaste combinaties maken

Hoewel functies als **Date.GetDate()** of **Math.random()** niet zijn geblokkeerd in de definitie van functies, kunt u ze beter niet gebruiken. Deze functies resulteren **niet** telkens wanneer u ze aanroept hetzelfde resultaat. Bovendien houdt de Azure Stream Analytics-service geen logboek bij van functieaanroepen en de geretourneerde resultaten. Als een functie verschillende resultaten voor dezelfde gebeurtenissen retourneert, wordt herhaalbaarheid niet gegarandeerd wanneer een taak door u of door de Stream Analytics-service opnieuw wordt gestart.

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>Een door een JavaScript-gebruiker gedefinieerde functie aan uw taak toevoegen

> [!NOTE]
> Deze stappen werken aan de Stream Analytics-taken die zijn geconfigureerd om te worden uitgevoerd in de cloud. Als uw Stream Analytics-taak is geconfigureerd om te worden uitgevoerd op Azure IoT Edge, gebruikt u in plaats daarvan Visual Studio en [schrijft u de door de gebruiker gedefinieerde functie met C#](stream-analytics-edge-csharp-udf.md).

Als u een door de gebruiker gedefinieerde JavaScript-functie in uw Stream Analytics-taak wilt maken, selecteert u **Functies** onder **Taaktopologie**. Selecteer vervolgens **JavaScript UDF** in het vervolgkeuzemenu **+ Toevoegen**. 

![JavaScript UDF toevoegen](./media/javascript/stream-analytics-jsudf-add.png)

Vervolgens moet u de volgende eigenschappen opgeven en **Opslaan** selecteren.

|Eigenschap|Beschrijving|
|--------|-----------|
|Functiealias|Voer een naam in om de functie in uw query aan te roepen.|
|Uitvoertype|Type dat wordt geretourneerd met behulp van door de gebruiker gedefinieerde JavaScript-functie naar uw Stream Analytics-query.|
|Functiedefinitie|Implementatie van uw JavaScript-functie die wordt uitgevoerd wanneer uw UDF wordt aangeroepen vanuit uw query.|

## <a name="test-and-troubleshoot-javascript-udfs"></a>JavaScript-UDF’s testen en fouten oplossen 

U kunt uw JavaScript UDF-logica testen en fouten opsporen in elke browser. Fouten opsporen en testen van de logica van deze door de gebruiker gedefinieerde functies wordt momenteel niet ondersteund in de Stream Analytics-portal. Zodra de functie werkt zoals verwacht, kunt u deze toevoegen aan de Stream Analytics-taak zoals hierboven wordt vermeld en vervolgens rechtstreeks vanuit uw query aanroepen. U kunt uw query-logica testen met JavaScript UDF met [Stream Analytics-hulpprogramma's voor Visual Studio](./stream-analytics-tools-for-visual-studio-install.md).

JavaScript-runtime-fouten worden beschouwd als onherstelbaar en worden weergegeven via het activiteitenlogboek. U haalt het logboek op door in Azure Portal naar uw project te gaan en **Activiteitenlogboek** te selecteren.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Een door de gebruiker gedefinieerde JavaScript-functie in een query aanroepen

U kunt uw JavaScript-functie in uw query eenvoudig aanroepen met behulp van de functiealias voorafgegaan door **udf**. Hier volgt een voorbeeld van een JavaScript UDF waarmee hexadecimale waarden worden geconverteerd naar een geheel getal dat wordt aangeroepen in een Stream Analytics-query.

```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
```

## <a name="supported-javascript-objects"></a>Ondersteunde JavaScript-objecten

Door de gebruiker gedefinieerde JavaScript-functies in Azure Stream Analytics ondersteunen standaard ingebouwde JavaScript-objecten. Zie [Algemene objecten](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects) voor een lijst van deze objecten.

### <a name="stream-analytics-and-javascript-type-conversion"></a>Typeconversie van Stream Analytics en JavaScript

Er zijn verschillen in de typen die de querytaal van Stream Analytics en JavaScript ondersteunen. Deze tabel bevat de conversietoewijzingen tussen de twee:

Stream Analytics | Javascript
--- | ---
bigint | Number (In JavaScript kunnen alleen gehele getallen tot exact 2^53 worden weergegeven)
DateTime | Date (JavaScript ondersteunt alleen milliseconden)
double | Aantal
nvarchar(MAX) | Tekenreeks
Record | Object
Matrix | Matrix
NULL | Null

Hier volgen JavaScript-naar-Stream Analytics-conversies:

Javascript | Stream Analytics
--- | ---
Aantal | Bigint (als het een rond getal tussen long.MinValue en long.MaxValue is; anders is het double)
Date | DateTime
Tekenreeks | nvarchar(MAX)
Object | Record
Matrix | Matrix
Null, niet gedefinieerd | NULL
Elk ander type (bijvoorbeeld een functie of fout) | Niet ondersteund (resulteert in een runtime-fout)

JavaScript-computertaal is hoofdlettergevoelig en hoofdlettergebruik in de objectvelden in JavaScript-code moeten overeenkomen met hoofdlettergebruik in de velden in de binnenkomende gegevens. Met taken met compatibiliteitsniveau 1,0 worden velden van SQL SELECT-instructie geconverteerd naar kleine letters. Onder compatibiliteitsniveau 1,1 en hoger, hebben velden uit de SELECT-instructie hetzelfde hoofdlettergebruik dat is opgegeven in de SQL-query.

## <a name="other-javascript-user-defined-function-patterns"></a>Andere door de gebruiker gedefinieerde JavaScript-functiepatronen

### <a name="write-nested-json-to-output"></a>Geneste JSON naar uitvoer schrijven

Als u een follow-up verwerkingsstap hebt die de uitvoer van een Stream Analytics-taak gebruikt als invoer, als die invoer een JSON-indeling vereist, kunt u een JSON-tekenreeks naar uitvoer schrijven. In het volgende voorbeeld wordt de functie **JSON.stringify()** aangeroepen om alle naam-/waarde-paren van de invoer te verpakken en als één tekenreekswaarde in de uitvoer te schrijven.

**Definitie van een door de gebruiker gedefinieerde JavaScript-functie:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Voorbeeldquery:**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.jsonstringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

### <a name="cast-string-to-json-object-to-process"></a>Te verwerken tekenreeks naar JSON-object converteren

Als u een tekenreeksveld hebt in JSON en dit wilt converteren naar een JSON-object om in een JavaScript-UDF te verwerken, kunt u de functie **JSON.parse()** gebruiken om een JSON-object te maken dat vervolgens kan worden gebruikt.

**Definitie van een door de gebruiker gedefinieerde JavaScript-functie:**

```javascript
function main(x) {
var person = JSON.parse(x);  
return person.name;
}
```

**Voorbeeldquery:**
```SQL
SELECT
    UDF.getName(input) AS Name
INTO
    output
FROM
    input
```

### <a name="use-trycatch-for-error-handling"></a>Try/catch gebruiken voor foutafhandeling

Try/catch-blokken kunnen u helpen bij het identificeren van problemen met onjuiste invoergegevens die worden doorgegeven aan een JavaScript-UDF.

**Definitie van een door de gebruiker gedefinieerde JavaScript-functie:**

```javascript
function main(input, x) {
    var obj = null;

    try{
        obj = JSON.parse(x);
    }catch(error){
        throw input;
    }
    
    return obj.Value;
}
```

**Voorbeeldquery: Geef een volledige record door als eerste parameter, zodat deze kan worden geretourneerd als er een fout optreedt.**
```SQL
SELECT
    A.context.company AS Company,
    udf.getValue(A, A.context.value) as Value
INTO
    output
FROM
    input A
```

### <a name="tolocalestring"></a>toLocaleString()
De **toLocaleString**-methode in JavaScript kan worden gebruikt om een taalgevoelige tekenreeks te retourneren die de datum- en tijdgegevens vertegenwoordigt vanwaaruit deze methode wordt aangeroepen.
Hoewel Azure Stream Analytics alleen UTC-datums en -tijden accepteert als tijdstempel van het systeem, kan deze methode worden gebruikt om de systeemtijdstempel te converteren naar een andere landinstelling en tijdzone.
Deze methode volgt hetzelfde implementatiegedrag als in Internet Explorer.

**Definitie van een door de gebruiker gedefinieerde JavaScript-functie:**

```javascript
function main(datetime){
    const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
    return event.toLocaleDateString('de-DE', options);
}
```

**Voorbeeldquery: Een datum/tijd als invoerwaarde doorgeven**
```SQL
SELECT
    udf.toLocaleString(input.datetime) as localeString
INTO
    output
FROM
    input
```

De uitvoer van deze query is de invoer-datum/tijd in **de-DE** met de opgegeven opties.
```
Samstag, 28. Dezember 2019
```

## <a name="user-logging"></a>Logboekregistratie voor gebruikers
Met het mechanisme voor logboekregistratie kunt u aangepaste gegevens vastleggen terwijl een taak wordt uitgevoerd. U kunt logboekgegevens gebruiken om fouten op te sporen of om de juistheid van de aangepaste code in realtime te evalueren. Dit mechanisme is beschikbaar via de methode Console.Log().

```javascript
console.log('my error message');
```

U kunt logboekberichten openen via de [Diagnostische logboeken](data-errors.md).
## <a name="next-steps"></a>Volgende stappen

* [UDF van Machine Learning](./machine-learning-udf.md)
* [C#-UDF](./stream-analytics-edge-csharp-udf-methods.md)
