---
title: Query uitvoeren op de tweelinggrafiek
titleSuffix: Azure Digital Twins
description: Zie een query uitvoeren op de Azure Digital Apparaatdubbels dubbele grafiek voor informatie.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 3fd504ec36abae3f00cd2a7eb4e1f7b639be0cea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462674"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Query's uitvoeren op de Azure Digital Apparaatdubbels dubbele grafiek

In dit artikel vindt u voor beelden van query's en gedetailleerde instructies voor het gebruik van de **Azure Digital apparaatdubbels-query taal** voor het uitvoeren van een query op uw [dubbele grafiek](concepts-twins-graph.md) voor informatie. (Voor een inleiding tot de query taal en een volledige lijst met functies raadpleegt u [*concepten: query taal*](concepts-query-language.md).)

Dit artikel begint met voorbeeld query's die de query taal structuur en algemene query bewerkingen voor digitale apparaatdubbels illustreren. Vervolgens wordt beschreven hoe u uw query's uitvoert nadat u ze hebt geschreven, met behulp van de Azure Digital Apparaatdubbels- [query-API](/rest/api/digital-twins/dataplane/query) of een [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis).

> [!NOTE]
> Als u de onderstaande voorbeeld query's uitvoert met een API of SDK-aanroep, moet u de query tekst in één regel verkleinen.

## <a name="show-all-digital-twins"></a>Alle digitale apparaatdubbels weer geven

Dit is de basis query waarmee een lijst met alle digitale apparaatdubbels in het exemplaar wordt geretourneerd:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

## <a name="query-by-property"></a>Query op eigenschap

Digitale apparaatdubbels ophalen op basis van **Eigenschappen** (waaronder id en meta gegevens):

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty1":::

Zoals in de bovenstaande query wordt weer gegeven, wordt er met behulp van het veld meta gegevens een query uitgevoerd voor de ID van een digitaal twee `$dtId` .

>[!TIP]
> Als u Cloud Shell gebruikt om een query uit te voeren met meta gegevens velden die beginnen met `$` , moet u de `$` with a-apostroffen om te laten Cloud shell weten dat het geen variabele is en moet worden gebruikt als een letterlijke waarde in de query tekst.

U kunt ook apparaatdubbels ophalen op basis van **het feit of een bepaalde eigenschap is gedefinieerd**. Hier volgt een query waarmee apparaatdubbels worden opgehaald die een gedefinieerde *locatie* -eigenschap hebben:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty2":::

Dit kan u helpen apparaatdubbels te verkrijgen op basis van de *code* -eigenschappen, zoals beschreven in [Tags toevoegen aan digitale apparaatdubbels](how-to-use-tags.md). Hier volgt een query waarmee alle apparaatdubbels met *rood* worden opgehaald:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

U kunt ook apparaatdubbels ophalen op basis van het **type van een eigenschap**. Dit is een query die apparaatdubbels ophaalt waarvan de eigenschap *Tempe ratuur* een getal is:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty3":::

>[!TIP]
> Als een eigenschap van het type is `Map` , kunt u de sleutels en waarden van de kaart rechtstreeks in de query gebruiken, zoals:
> :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty4":::

## <a name="query-by-model"></a>Query op model

De `IS_OF_MODEL` operator kan worden gebruikt om te filteren op basis van het dubbele [**model**](concepts-models.md).

Het gaat over [overname](concepts-models.md#model-inheritance) en model [versie beheer](how-to-manage-model.md#update-models)en resulteert in waar voor een bepaalde dubbele **waarde** als de dubbele aan een van deze voor waarden voldoet:

* Het dubbele implementeert het door gegeven model `IS_OF_MODEL()` en het versie nummer van het model op de dubbele waarde is *groter dan of gelijk aan* het versie nummer van het gegeven model
* Het dubbele implementeert een model dat het model *uitbreidt* `IS_OF_MODEL()` , en het uitgebreide model versie nummer van het type is *groter dan of gelijk aan* het versie nummer van het gegeven model

Als u bijvoorbeeld een query uitvoert voor apparaatdubbels van het model `dtmi:example:widget;4` , retourneert de query alle apparaatdubbels op basis van **versie 4 of hoger** van het **widget** model en ook apparaatdubbels op basis van versie **4 of hoger** van alle **modellen die van de widget overnemen**.

`IS_OF_MODEL` verschillende para meters kunnen worden uitgevoerd en de rest van deze sectie is toegewezen aan de verschillende overbelasting opties.

Het eenvoudigste gebruik van `IS_OF_MODEL` heeft alleen een `twinTypeName` para meter: `IS_OF_MODEL(twinTypeName)` .
Hier volgt een query voorbeeld waarin een waarde in deze para meter wordt door gegeven:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel1":::

Als u een dubbele verzameling wilt opgeven wanneer er meer dan één moet worden gezocht `JOIN` , voegt u de `twinCollection` para meter: toe als u er meer dan een wilt zoeken `IS_OF_MODEL(twinCollection, twinTypeName)` .
Hier volgt een query voorbeeld waarmee een waarde voor deze para meter wordt toegevoegd:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel2":::

Als u een exacte overeenkomst wilt, voegt u de `exact` para meter: toe `IS_OF_MODEL(twinTypeName, exact)` .
Hier volgt een query voorbeeld waarmee een waarde voor deze para meter wordt toegevoegd:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel3":::

U kunt ook alle drie de argumenten tegelijk door geven: `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
Hier volgt een query voorbeeld waarin een waarde voor alle drie de para meters wordt opgegeven:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel4":::

## <a name="query-by-relationship"></a>Query's uitvoeren op relatie

Bij het uitvoeren van query's op basis van de **relaties** van Digital apparaatdubbels heeft de Azure Digital apparaatdubbels-query taal een speciale syntaxis.

Relaties worden in het querybereik gehaald in de `FROM`-component. Een belang rijk verschil van ' klassiek ' SQL-type talen is dat elke expressie in deze `FROM` component geen tabel is. in plaats daarvan wordt in de `FROM` component een kruis entiteits relatie door lopen en is deze geschreven met een Azure Digital apparaatdubbels-versie van `JOIN` .

Voor de mogelijkheden van het Azure Digital Apparaatdubbels [model](concepts-models.md) zijn relaties niet onafhankelijk van apparaatdubbels. Dit betekent dat de `JOIN` van de Azure Digital Twins-querytaal iets verschilt van de algemene SQL-`JOIN`, omdat de relaties hier niet onafhankelijk van elkaar kunnen worden opgevraagd en aan een dubbel moeten worden gekoppeld.
Voor het inbouwen van dit verschil wordt het trefwoord `RELATED` gebruikt in de `JOIN`-component om te verwijzen naar een reeks relaties van een dubbel.

In de volgende sectie vindt u enkele voor beelden van hoe dit eruitziet.

> [!TIP]
> Deze functie imiteert de document gerichte functionaliteit van CosmosDB, waar deze `JOIN` kan worden uitgevoerd op onderliggende objecten in een document. CosmosDB maakt gebruik `IN` van het sleutel woord om aan te geven `JOIN` dat het is bedoeld om matrix elementen in het huidige context document te herhalen.

### <a name="relationship-based-query-examples"></a>Voor beelden van query's op basis van een relatie

Als u een gegevensset wilt ophalen die relaties bevat, gebruikt u één `FROM` instructie gevolgd door N `JOIN` -instructies, waarbij de `JOIN` instructies Express-relaties hebben met betrekking tot het resultaat van een voor gaande `FROM` of- `JOIN` instructie.

Hier volgt een voor beeld van een op relaties gebaseerde query. Dit code fragment selecteert alle digitale-apparaatdubbels met de *id-* eigenschap ' ABC ' en alle digitale apparaatdubbels met betrekking tot deze digitale apparaatdubbels via een *contains* -relatie.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship1":::

> [!NOTE]
> De ontwikkelaar hoeft dit niet te correleren `JOIN` met een sleutel waarde in de `WHERE` -component (of geef een sleutel waarde op in de `JOIN` definitie). Deze correlatie wordt automatisch berekend door het systeem, omdat de relatie-eigenschappen zelf de doelentiteit aanduiden.

### <a name="query-the-properties-of-a-relationship"></a>Een query uitvoeren op de eigenschappen van een relatie

Net zoals digitale dubbels over eigenschappen beschikken die via DTDL zijn beschreven, kunnen relaties ook over eigenschappen beschikken. U kunt apparaatdubbels query's uitvoeren op **basis van de eigenschappen van hun relaties**.
Met de Azure Digital Apparaatdubbels-query taal kunnen relaties worden gefilterd en geprojectied door een alias toe te wijzen aan de relatie binnen de `JOIN` component.

Denk bijvoorbeeld aan een *servicedBy* -relatie die een *reportedCondition* -eigenschap heeft. In de onderstaande query krijgt deze relatie een alias van ' R ' om te kunnen verwijzen naar de eigenschap.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship2":::

In het bovenstaande voor beeld ziet u hoe *reportedCondition* een eigenschap is van de *servicedBy* -relatie zelf (niet van een digitale dubbele verbinding met een *servicedBy* -relatie).

### <a name="query-with-multiple-joins"></a>Query met meerdere samen voegingen

Maxi maal vijf `JOIN` s worden ondersteund in één query. Zo kunt u meerdere niveaus van relaties tegelijk door lopen.

Hier volgt een voor beeld van een meervoudige samenvoeg query, waarmee alle gloei lampen in de licht panelen in de ruimten 1 en 2 worden opgehaald.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship3":::

## <a name="count-items"></a>Items tellen

U kunt het aantal items in een resultatenset tellen met behulp van de- `Select COUNT` component:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount1":::

Voeg een- `WHERE` component toe om het aantal items te tellen dat aan een bepaald criterium voldoet. Hier volgen enkele voor beelden van tellingen met een toegepast filter op basis van het type dubbele model (Zie voor meer informatie over deze syntaxis [*query per model*](#query-by-model) ):

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount2":::

U kunt ook `COUNT` samen met de- `JOIN` component gebruiken. Hier volgt een query waarmee alle gloei lampen in de lichte deel Vensters van de kamers 1 en 2 worden geteld:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount3":::

## <a name="filter-results-select-top-items"></a>Resultaten filteren: bovenste items selecteren

U kunt de verschillende ' top ' items in een query selecteren met behulp van de- `Select TOP` component.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectTop":::

## <a name="filter-results-specify-return-set-with-projections"></a>Resultaten filteren: retour sets met projecties opgeven

Door projecties in de `SELECT` -instructie te gebruiken, kunt u kiezen welke kolommen een query moet retour neren.

>[!NOTE]
>Op dit moment worden complexe eigenschappen niet ondersteund. Combi neer de projecties met een controle om te controleren of de projectie-eigenschappen geldig zijn `IS_PRIMITIVE` .

Hier volgt een voor beeld van een query die projectie gebruikt voor het retour neren van apparaatdubbels en relaties. Met de volgende query worden de *Consumer*, de *fabriek* en de *rand* van een scenario waarin een *Factory* met de id *ABC* is gerelateerd aan de *consument* gekoppeld via een relatie van *Factory. Customer*, en die relatie wordt weer gegeven als de *rand*.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections1":::

U kunt projectie ook gebruiken om een eigenschap van een dubbele waarde te retour neren. Met de volgende query wordt de eigenschap *name* van de *consumenten* projecten die zijn gerelateerd aan de *fabriek* met een id van *ABC* door middel van een relatie van *Factory. Customer*.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections2":::

U kunt ook projectie gebruiken om een eigenschap van een relatie te retour neren. Net als in het vorige voor beeld, met de volgende query worden de eigenschap *name* van de *consumenten* met betrekking tot de *fabriek* met een id van *ABC* door middel van een relatie van *Factory. Customer*; het resultaat is nu ook twee eigenschappen van die relatie, *prop1* en *prop2*. Dit doet u door de relatie *rand* te benoemen en de eigenschappen ervan te verzamelen.  

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections3":::

U kunt ook aliassen gebruiken om query's te vereenvoudigen met projectie.

Met de volgende query worden dezelfde bewerkingen uitgevoerd als in het vorige voor beeld, maar er wordt een alias voor de eigenschaps namen toegepast op `consumerName` , `first` , en `second` `factoryArea` .

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections4":::

Hier volgt een soort gelijke query die de bovenstaande set doorzoekt, maar alleen projecten de eigenschap *Consumer.name* als `consumerName` en projecteert de volledige *fabriek* als een twee.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections5":::

## <a name="build-efficient-queries-with-the-in-operator"></a>Efficiënte query's bouwen met de IN-operator

U kunt het aantal query's dat u nodig hebt aanzienlijk beperken door een matrix met apparaatdubbels te bouwen en query's met de operator te maken `IN` . 

Denk bijvoorbeeld aan een scenario waarin *gebouwen* *vloeren* en *vloeren* *bevatten.* Als u wilt zoeken naar kamers binnen een gebouw dat warm is, kunt u de volgende stappen uitvoeren.

1. Zoek in het gebouw vloeren op basis van de `contains` relatie.

    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="INOperatorWithout":::

2. Als u wilt zoeken naar ruimten, in plaats van de vloeren één voor één te overwegen en een `JOIN` query uit te voeren om de kamers voor elke plaats te vinden, kunt u een query uitvoeren op een verzameling van de vloeren in het gebouw (met de naam *vloer* in de onderstaande query).

    In client-app:
    
    ```csharp
    var floors = "['floor1','floor2', ..'floorn']"; 
    ```
    
    In query:
    
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="INOperatorWith":::

## <a name="other-compound-query-examples"></a>Andere samengestelde query voorbeelden

U kunt elk van de bovenstaande typen query's **combi neren** met behulp van combi Naties om meer details in één query op te halen. Hier volgen enkele aanvullende voor beelden van samengestelde query's waarmee een query wordt uitgevoerd voor meer dan één type dubbele descriptor tegelijk.

* Van de apparaten die *ruimte 123* heeft, retour neren ze de MxChip-apparaten die de rol van operator gebruiken
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples1":::
* Apparaatdubbels ophalen die een relatie met de naam *contains bevat* met een andere dubbele id en een *id1*
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples2":::
* Alle kamers van dit room-model ophalen die zijn opgenomen in *floor11*
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples3":::

## <a name="run-queries-with-the-api"></a>Query's uitvoeren met de API

Zodra u een query reeks hebt gekozen, voert u deze uit door een aanroep naar de [**query-API**](/rest/api/digital-twins/dataplane/query)te maken.

U kunt de API rechtstreeks aanroepen of een van de [sdk's](how-to-use-apis-sdks.md#overview-data-plane-apis) gebruiken die beschikbaar zijn voor Azure Digital apparaatdubbels.

Het volgende code fragment illustreert de [.net (C#) SDK-](/dotnet/api/overview/azure/digitaltwins/client) aanroep van een client-app:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="RunQuery":::

De query die wordt gebruikt in deze aanroep retourneert een lijst met digitale apparaatdubbels, die het bovenstaande voor beeld vertegenwoordigt met [BasicDigitalTwin](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin) -objecten. Het retour type van uw gegevens voor elke query is afhankelijk van de voor waarden die u opgeeft met de `SELECT` instructie:
* Query's die beginnen met bevatten `SELECT * FROM ...` , retour neren een lijst met digitale apparaatdubbels (die kunnen worden geserialiseerd als `BasicDigitalTwin` objecten of andere aangepaste, digitale twee typen die u mogelijk hebt gemaakt).
* Query's die in de notatie beginnen, `SELECT <A>, <B>, <C> FROM ...` retour neren een woorden lijst met sleutels `<A>` , `<B>` en `<C>` .
* Andere indelingen van- `SELECT` instructies kunnen worden ontworpen om aangepaste gegevens te retour neren. U kunt overwegen uw eigen klassen te maken voor het verwerken van zeer aangepaste resultaten sets. 

### <a name="query-with-paging"></a>Query met paginering

Query aanroepen ondersteunen paginering. Hier volgt een volledig voor beeld `BasicDigitalTwin` dat wordt gebruikt als query resultaat type met fout afhandeling en paging:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Azure Digital Apparaatdubbels api's en sdk's](how-to-use-apis-sdks.md), met inbegrip van de query-API die wordt gebruikt om de query's uit dit artikel uit te voeren.
