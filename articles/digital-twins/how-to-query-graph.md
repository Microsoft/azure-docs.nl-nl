---
title: Query uitvoeren op de tweelinggrafiek
titleSuffix: Azure Digital Twins
description: Zie een query uitvoeren op de Azure Digital Apparaatdubbels dubbele grafiek voor informatie.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperfq2
ms.openlocfilehash: 45b177bd35af9748ff80ecc38f2d1c803c10546e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452816"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Query's uitvoeren op de Azure Digital Apparaatdubbels dubbele grafiek

In dit artikel vindt u voor beelden van query's en gedetailleerde instructies voor het gebruik van de **Azure Digital apparaatdubbels-query taal** voor het uitvoeren van een query op uw [dubbele grafiek](concepts-twins-graph.md) voor informatie. (Voor een inleiding tot de query taal en een volledige lijst met functies raadpleegt u [*concepten: query taal*](concepts-query-language.md).)

Dit artikel begint met voorbeeld query's die de query taal structuur en algemene query bewerkingen voor digitale apparaatdubbels illustreren. Vervolgens wordt beschreven hoe u uw query's uitvoert nadat u ze hebt geschreven, met behulp van de Azure Digital Apparaatdubbels- [query-API](/rest/api/digital-twins/dataplane/query) of een [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis).

> [!TIP]
> Als u de onderstaande voorbeeld query's uitvoert met een API of SDK-aanroep, moet u de query tekst in één regel verkleinen.

## <a name="show-all-digital-twins"></a>Alle digitale apparaatdubbels weer geven

Dit is de basis query waarmee een lijst met alle digitale apparaatdubbels in het exemplaar wordt geretourneerd:

```sql
SELECT *
FROM DIGITALTWINS
```

## <a name="query-by-property"></a>Query op eigenschap

Digitale apparaatdubbels ophalen op basis van **Eigenschappen** (waaronder id en meta gegevens):

```sql
SELECT  *
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

> [!TIP]
> De ID van een digitale dubbele query wordt uitgevoerd met behulp van het veld meta gegevens `$dtId` .

U kunt ook apparaatdubbels ophalen op basis van **het feit of een bepaalde eigenschap is gedefinieerd**. Hier volgt een query waarmee apparaatdubbels worden opgehaald die een gedefinieerde *locatie* -eigenschap hebben:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_DEFINED(Location)
```

Dit kan u helpen apparaatdubbels te verkrijgen op basis van de *code* -eigenschappen, zoals beschreven in [Tags toevoegen aan digitale apparaatdubbels](how-to-use-tags.md). Hier volgt een query waarmee alle apparaatdubbels met *rood* worden opgehaald:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_DEFINED(tags.red)
```

U kunt ook apparaatdubbels ophalen op basis van het **type van een eigenschap**. Dit is een query die apparaatdubbels ophaalt waarvan de eigenschap *Tempe ratuur* een getal is:

```sql
SELECT * FROM DIGITALTWINS T WHERE IS_NUMBER(T.Temperature)
```

## <a name="query-by-model"></a>Query op model

De `IS_OF_MODEL` operator kan worden gebruikt om te filteren op basis van het dubbele [**model**](concepts-models.md).

Het gaat over [overname](concepts-models.md#model-inheritance) en model [versie beheer](how-to-manage-model.md#update-models)en resulteert in waar voor een bepaalde dubbele **waarde** als de dubbele aan een van deze voor waarden voldoet:

* Het dubbele implementeert het door gegeven model `IS_OF_MODEL()` en het versie nummer van het model op de dubbele waarde is *groter dan of gelijk aan* het versie nummer van het gegeven model
* Het dubbele implementeert een model dat het model *uitbreidt* `IS_OF_MODEL()` , en het uitgebreide model versie nummer van het type is *groter dan of gelijk aan* het versie nummer van het gegeven model

Als u bijvoorbeeld een query uitvoert voor apparaatdubbels van het model `dtmi:example:widget;4` , retourneert de query alle apparaatdubbels op basis van **versie 4 of hoger** van het **widget** model en ook apparaatdubbels op basis van versie **4 of hoger** van alle **modellen die van de widget overnemen**.

`IS_OF_MODEL` verschillende para meters kunnen worden uitgevoerd en de rest van deze sectie is toegewezen aan de verschillende overbelasting opties.

Het eenvoudigste gebruik van `IS_OF_MODEL` heeft alleen een `twinTypeName` para meter: `IS_OF_MODEL(twinTypeName)` .
Hier volgt een query voorbeeld waarin een waarde in deze para meter wordt door gegeven:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:example:thing;1')
```

Als u een dubbele verzameling wilt opgeven wanneer er meer dan één moet worden gezocht `JOIN` , voegt u de `twinCollection` para meter: toe als u er meer dan een wilt zoeken `IS_OF_MODEL(twinCollection, twinTypeName)` .
Hier volgt een query voorbeeld waarmee een waarde voor deze para meter wordt toegevoegd:

```sql
SELECT * FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:example:thing;1')
```

Als u een exacte overeenkomst wilt, voegt u de `exact` para meter: toe `IS_OF_MODEL(twinTypeName, exact)` .
Hier volgt een query voorbeeld waarmee een waarde voor deze para meter wordt toegevoegd:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:example:thing;1', exact)
```

U kunt ook alle drie de argumenten tegelijk door geven: `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
Hier volgt een query voorbeeld waarin een waarde voor alle drie de para meters wordt opgegeven:

```sql
SELECT ROOM FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:example:thing;1', exact)
```

## <a name="query-by-relationship"></a>Query's uitvoeren op relatie

Bij het uitvoeren van query's op basis van de **relaties** van Digital apparaatdubbels heeft de Azure Digital apparaatdubbels-query taal een speciale syntaxis.

Relaties worden opgehaald in het query bereik in de- `FROM` component. Een belang rijk verschil van ' klassiek ' SQL-type talen is dat elke expressie in deze `FROM` component geen tabel is. in plaats daarvan wordt in de `FROM` component een kruis entiteits relatie door lopen en is deze geschreven met een Azure Digital apparaatdubbels-versie van `JOIN` .

Voor de mogelijkheden van het Azure Digital Apparaatdubbels [model](concepts-models.md) zijn relaties niet onafhankelijk van apparaatdubbels. Dit betekent dat de Azure Digital Apparaatdubbels-query taal `JOIN` iets anders is dan de algemene SQL `JOIN` , omdat hier geen query's kunnen worden uitgevoerd en moeten ze zijn gekoppeld aan een dubbele.
Om dit verschil op te nemen, wordt het sleutel woord `RELATED` in de- `JOIN` component gebruikt om te verwijzen naar een dubbele set relaties.

In de volgende sectie vindt u enkele voor beelden van hoe dit eruitziet.

> [!TIP]
> Deze functie imiteert de document gerichte functionaliteit van CosmosDB, waar deze `JOIN` kan worden uitgevoerd op onderliggende objecten in een document. CosmosDB maakt gebruik `IN` van het sleutel woord om aan te geven `JOIN` dat het is bedoeld om matrix elementen in het huidige context document te herhalen.

### <a name="relationship-based-query-examples"></a>Voor beelden van query's op basis van een relatie

Als u een gegevensset wilt ophalen die relaties bevat, gebruikt u één `FROM` instructie gevolgd door N `JOIN` -instructies, waarbij de `JOIN` instructies Express-relaties hebben met betrekking tot het resultaat van een voor gaande `FROM` of- `JOIN` instructie.

Hier volgt een voor beeld van een op relaties gebaseerde query. Dit code fragment selecteert alle digitale-apparaatdubbels met de *id-* eigenschap ' ABC ' en alle digitale apparaatdubbels met betrekking tot deze digitale apparaatdubbels via een *contains* -relatie.

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC'
```

> [!NOTE]
> De ontwikkelaar hoeft dit niet te correleren `JOIN` met een sleutel waarde in de `WHERE` -component (of geef een sleutel waarde op in de `JOIN` definitie). Deze correlatie wordt automatisch berekend door het systeem, omdat de relatie-eigenschappen zelf de doel entiteit identificeren.

### <a name="query-the-properties-of-a-relationship"></a>De eigenschappen van een relatie opvragen

Net zoals bij digitale apparaatdubbels eigenschappen beschreven via DTDL, kunnen relaties ook eigenschappen hebben. U kunt apparaatdubbels query's uitvoeren op **basis van de eigenschappen van hun relaties**.
Met de Azure Digital Apparaatdubbels-query taal kunnen relaties worden gefilterd en geprojectied door een alias toe te wijzen aan de relatie binnen de `JOIN` component.

Denk bijvoorbeeld aan een *servicedBy* -relatie die een *reportedCondition* -eigenschap heeft. In de onderstaande query krijgt deze relatie een alias van ' R ' om te kunnen verwijzen naar de eigenschap.

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC'
AND R.reportedCondition = 'clean'
```

In het bovenstaande voor beeld ziet u hoe *reportedCondition* een eigenschap is van de *servicedBy* -relatie zelf (niet van een digitale dubbele verbinding met een *servicedBy* -relatie).

### <a name="query-with-multiple-joins"></a>Query met meerdere samen voegingen

Maxi maal vijf `JOIN` s worden ondersteund in één query. Zo kunt u meerdere niveaus van relaties tegelijk door lopen.

Hier volgt een voor beeld van een meervoudige samenvoeg query, waarmee alle gloei lampen in de licht panelen in de ruimten 1 en 2 worden opgehaald.

```sql
SELECT LightBulb
FROM DIGITALTWINS Room
JOIN LightPanel RELATED Room.contains
JOIN LightBulb RELATED LightPanel.contains
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')
AND Room.$dtId IN ['room1', 'room2']
```

## <a name="count-items"></a>Items tellen

U kunt het aantal items in een resultatenset tellen met behulp van de- `Select COUNT` component:

```sql
SELECT COUNT()
FROM DIGITALTWINS
```

Voeg een- `WHERE` component toe om het aantal items te tellen dat aan een bepaald criterium voldoet. Hier volgen enkele voor beelden van tellingen met een toegepast filter op basis van het type dubbele model (Zie voor meer informatie over deze syntaxis [*query per model*](#query-by-model) ):

```sql
SELECT COUNT()
FROM DIGITALTWINS
WHERE IS_OF_MODEL('dtmi:sample:Room;1')

SELECT COUNT()
FROM DIGITALTWINS c
WHERE IS_OF_MODEL('dtmi:sample:Room;1') AND c.Capacity > 20
```

U kunt ook `COUNT` samen met de- `JOIN` component gebruiken. Hier volgt een query waarmee alle gloei lampen in de lichte deel Vensters van de kamers 1 en 2 worden geteld:

```sql
SELECT COUNT()  
FROM DIGITALTWINS Room  
JOIN LightPanel RELATED Room.contains  
JOIN LightBulb RELATED LightPanel.contains  
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')  
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')  
AND Room.$dtId IN ['room1', 'room2']
```

## <a name="filter-results-select-top-items"></a>Resultaten filteren: bovenste items selecteren

U kunt de verschillende ' top ' items in een query selecteren met behulp van de- `Select TOP` component.

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE ...
```

## <a name="filter-results-specify-return-set-with-projections"></a>Resultaten filteren: retour sets met projecties opgeven

Door projecties in de `SELECT` -instructie te gebruiken, kunt u kiezen welke kolommen een query moet retour neren.

>[!NOTE]
>Op dit moment worden complexe eigenschappen niet ondersteund. Combi neer de projecties met een controle om te controleren of de projectie-eigenschappen geldig zijn `IS_PRIMITIVE` .

Hier volgt een voor beeld van een query die projectie gebruikt voor het retour neren van apparaatdubbels en relaties. Met de volgende query worden de *Consumer*, de *fabriek* en de *rand* van een scenario waarin een *Factory* met de id *ABC* is gerelateerd aan de *consument* gekoppeld via een relatie van *Factory. Customer*, en die relatie wordt weer gegeven als de *rand*.

```sql
SELECT Consumer, Factory, Edge
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
```

U kunt projectie ook gebruiken om een eigenschap van een dubbele waarde te retour neren. Met de volgende query wordt de eigenschap *name* van de *consumenten* projecten die zijn gerelateerd aan de *fabriek* met een id van *ABC* door middel van een relatie van *Factory. Customer*.

```sql
SELECT Consumer.name
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Consumer.name)
```

U kunt ook projectie gebruiken om een eigenschap van een relatie te retour neren. Net als in het vorige voor beeld, met de volgende query worden de eigenschap *name* van de *consumenten* met betrekking tot de *fabriek* met een id van *ABC* door middel van een relatie van *Factory. Customer*; het resultaat is nu ook twee eigenschappen van die relatie, *prop1* en *prop2*. Dit doet u door de relatie *rand* te benoemen en de eigenschappen ervan te verzamelen.  

```sql
SELECT Consumer.name, Edge.prop1, Edge.prop2, Factory.area
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)
```

U kunt ook aliassen gebruiken om query's te vereenvoudigen met projectie.

Met de volgende query worden dezelfde bewerkingen uitgevoerd als in het vorige voor beeld, maar er wordt een alias voor de eigenschaps namen toegepast op `consumerName` , `first` , en `second` `factoryArea` .

```sql
SELECT Consumer.name AS consumerName, Edge.prop1 AS first, Edge.prop2 AS second, Factory.area AS factoryArea
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)
```

Hier volgt een soort gelijke query die de bovenstaande set doorzoekt, maar alleen projecten de eigenschap *Consumer.name* als `consumerName` en projecteert de volledige *fabriek* als een twee.

```sql
SELECT Consumer.name AS consumerName, Factory
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name)
```

## <a name="build-efficient-queries-with-the-in-operator"></a>Efficiënte query's bouwen met de IN-operator

U kunt het aantal query's dat u nodig hebt aanzienlijk beperken door een matrix met apparaatdubbels te bouwen en query's met de operator te maken `IN` . 

Denk bijvoorbeeld aan een scenario waarin *gebouwen* *vloeren* en *vloeren* *bevatten.* Als u wilt zoeken naar kamers binnen een gebouw dat warm is, kunt u de volgende stappen uitvoeren.

1. Zoek in het gebouw vloeren op basis van de `contains` relatie.

    ```sql
    SELECT Floor
    FROM DIGITALTWINS Building
    JOIN Floor RELATED Building.contains
    WHERE Building.$dtId = @buildingId
    ```

2. Als u wilt zoeken naar ruimten, in plaats van de vloeren één voor één te overwegen en een `JOIN` query uit te voeren om de kamers voor elke plaats te vinden, kunt u een query uitvoeren op een verzameling van de vloeren in het gebouw (met de naam *vloer* in de onderstaande query).

    In client-app:
    
    ```csharp
    var floors = "['floor1','floor2', ..'floorn']"; 
    ```
    
    In query:
    
    ```sql
    
    SELECT Room
    FROM DIGITALTWINS Floor
    JOIN Room RELATED Floor.contains
    WHERE Floor.$dtId IN ['floor1','floor2', ..'floorn']
    AND Room. Temperature > 72
    AND IS_OF_MODEL(Room, 'dtmi:com:contoso:Room;1')
    
    ```

## <a name="other-compound-query-examples"></a>Andere samengestelde query voorbeelden

U kunt elk van de bovenstaande typen query's **combi neren** met behulp van combi Naties om meer details in één query op te halen. Hier volgen enkele aanvullende voor beelden van samengestelde query's waarmee een query wordt uitgevoerd voor meer dan één type dubbele descriptor tegelijk.

| Beschrijving | Query’s uitvoeren |
| --- | --- |
| Van de apparaten die *ruimte 123* heeft, retour neren ze de MxChip-apparaten die de rol van operator gebruiken | `SELECT device`<br>`FROM DigitalTwins space`<br>`JOIN device RELATED space.has`<br>`WHERE space.$dtid = 'Room 123'`<br>`AND device.$metadata.model = 'dtmi:contoso:com:DigitalTwins:MxChip:3'`<br>`AND has.role = 'Operator'` |
| Apparaatdubbels ophalen die een relatie met de naam *contains bevat* met een andere dubbele id en een *id1* | `SELECT Room`<br>`FROM DIGITALTWINS Room`<br>`JOIN Thermostat RELATED Room.Contains`<br>`WHERE Thermostat.$dtId = 'id1'` |
| Alle kamers van dit room-model ophalen die zijn opgenomen in *floor11* | `SELECT Room`<br>`FROM DIGITALTWINS Floor`<br>`JOIN Room RELATED Floor.Contains`<br>`WHERE Floor.$dtId = 'floor11'`<br>`AND IS_OF_MODEL(Room, 'dtmi:contoso:com:DigitalTwins:Room;1')` |

## <a name="run-queries-with-the-api"></a>Query's uitvoeren met de API

Zodra u een query reeks hebt gekozen, voert u deze uit door een aanroep naar de [**query-API**](/rest/api/digital-twins/dataplane/query)te maken.

U kunt de API rechtstreeks aanroepen of een van de [sdk's](how-to-use-apis-sdks.md#overview-data-plane-apis) gebruiken die beschikbaar zijn voor Azure Digital apparaatdubbels.

Het volgende code fragment illustreert de [.net (C#) SDK-](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) aanroep van een client-app:

```csharp
    string adtInstanceEndpoint = "https://<your-instance-hostname>";

    var credential = new DefaultAzureCredential();
    DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceEndpoint), credential);

    // Run a query for all twins   
    string query = "SELECT * FROM DIGITALTWINS";
    AsyncPageable<BasicDigitalTwin> result = client.QueryAsync<BasicDigitalTwin>(query);
```

Deze aanroep retourneert query resultaten in de vorm van een [BasicDigitalTwin](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin?view=azure-dotnet&preserve-view=true) -object.

Query aanroepen ondersteunen paginering. Hier volgt een volledig voor beeld `BasicDigitalTwin` dat wordt gebruikt als query resultaat type met fout afhandeling en paging:

```csharp
try
{
    await foreach(BasicDigitalTwin twin in result)
        {
            // You can include your own logic to print the result
            // The logic below prints the twin's ID and contents
            Console.WriteLine($"Twin ID: {twin.Id} \nTwin data");
            IDictionary<string, object> contents = twin.Contents;
            foreach (KeyValuePair<string, object> kvp in contents)
            {
                Console.WriteLine($"{kvp.Key}  {kvp.Value}");
            }
        }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"Error {e.Status}: {e.Message}");
    throw;
}
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Azure Digital Apparaatdubbels api's en sdk's](how-to-use-apis-sdks.md), met inbegrip van de query-API die wordt gebruikt om de query's uit dit artikel uit te voeren.
