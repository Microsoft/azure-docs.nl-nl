---
title: Querytaal
titleSuffix: Azure Digital Twins
description: Meer informatie over de basis principes van de Azure Digital Apparaatdubbels-query taal.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 9549e6ea30be0cd9eb1a8c200a5af4a4721793a6
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034673"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Over de query taal voor Azure Digital Apparaatdubbels

Het midden van Azure Digital Apparaatdubbels is het [dubbele diagram](concepts-twins-graph.md), dat is geconstrueerd op basis van digitale apparaatdubbels en relaties. 

Deze grafiek kan worden opgevraagd om informatie te krijgen over de digitale apparaatdubbels en de relaties hierin. Deze query's worden geschreven in een aangepaste SQL-achtige querytaal, die de **Azure Digital Twins-querytaal** wordt genoemd. Dit is vergelijkbaar met de [IOT hub query taal](../iot-hub/iot-hub-devguide-query-language.md) met veel vergelijk bare functies.

In dit artikel worden de basis principes van de query taal en de mogelijkheden ervan beschreven. Zie [*How-to: query the dubbele Graph*](how-to-query-graph.md)(Engelstalig) voor meer gedetailleerde voor beelden van query-syntaxis en het uitvoeren van query aanvragen.

## <a name="about-the-queries"></a>Over de query's

U kunt de Azure Digital Apparaatdubbels-query taal gebruiken om digitale apparaatdubbels op te halen volgens hun...
* eigenschappen (inclusief [Label eigenschappen](how-to-use-tags.md))
* modellen
* relaties
  - eigenschappen van de relaties

Als u een query wilt verzenden naar de service vanuit een client-app, gebruikt u de Azure Digital Apparaatdubbels- [**query-API**](/rest/api/digital-twins/dataplane/query). Eén manier om de API te gebruiken is via een van de [sdk's](how-to-use-apis-sdks.md#overview-data-plane-apis) voor Azure Digital apparaatdubbels.

### <a name="considerations-for-querying"></a>Overwegingen voor het uitvoeren van query's

Bij het schrijven van query's voor Azure Digital Apparaatdubbels moet u rekening houden met de volgende overwegingen:
* **Hoofdletter gevoeligheid onthouden**: alle Azure Digital apparaatdubbels-query bewerkingen zijn hoofdletter gevoelig, dus let erop dat u de exacte namen gebruikt die in de modellen zijn gedefinieerd. Als de namen van eigenschappen verkeerd zijn gespeld of onjuist zijn, is de resultatenset leeg zonder dat er fouten worden geretourneerd.
* **Enkele aanhalings tekens** sluiten: als de query tekst één aanhalings teken in de gegevens bevat, moet de prijs opgave met het teken worden voorafgegaan `\` . Hier volgt een voor beeld dat een eigenschaps waarde van *D'Souza* behandelt:

  :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="EscapedSingleQuote":::

## <a name="reference-expressions-and-conditions"></a>Verwijzing: expressies en voor waarden

In deze sectie worden de Opera tors en functies beschreven die beschikbaar zijn voor het schrijven van Azure Digital Apparaatdubbels-query's. Zie [*How-to: query uitvoeren op de dubbele grafiek*](how-to-query-graph.md)voor voor beelden van query's die het gebruik van deze functies illustreren.

### <a name="operators"></a>Operators

De volgende Opera tors worden ondersteund:

| Familie | Operators |
| --- | --- |
| Logisch |`AND`, `OR`, `NOT` |
| Vergelijking | `=`, `!=`, `<`, `>`, `<=`, `>=` |
| Contains | `IN`, `NIN` |

### <a name="functions"></a>Functions

De volgende typen functies voor controleren en casting worden ondersteund:

| Functie | Beschrijving |
| -------- | ----------- |
| `IS_DEFINED` | Retourneert een Booleaanse waarde die aangeeft of aan de eigenschap een waarde is toegewezen. Dit wordt alleen ondersteund als de waarde een primitief type is. Primitieve typen bevatten een teken reeks, een Booleaanse waarde of een getal `null` . `DateTime`, object typen en matrices worden niet ondersteund. |
| `IS_OF_MODEL` | Retourneert een Booleaanse waarde die aangeeft of het opgegeven dubbele overeenkomt met het opgegeven model type |
| `IS_BOOL` | Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een Boolean is. |
| `IS_NUMBER` | Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een getal is. |
| `IS_STRING` | Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een teken reeks is. |
| `IS_NULL` | Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie Null is. |
| `IS_PRIMITIVE` | Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een primitieve is (teken reeks, Booleaans, numeriek of `null` ). |
| `IS_OBJECT` | Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een JSON-object is. |

De volgende teken reeks functies worden ondersteund:

| Functie | Beschrijving |
| -------- | ----------- |
| `STARTSWITH(x, y)` | Retourneert een Booleaanse waarde die aangeeft of de eerste teken reeks expressie begint met de tweede. |
| `ENDSWITH(x, y)` | Retourneert een Booleaanse waarde die aangeeft of de eerste teken reeks expressie eindigt op de tweede. |

## <a name="query-limitations"></a>Query beperkingen

In deze sectie worden de beperkingen van de query taal beschreven.

* Timing: er kan een vertraging van Maxi maal tien seconden zijn voordat wijzigingen in uw exemplaar worden weer gegeven in query's. Als u bijvoorbeeld een bewerking voltooit zoals het maken of verwijderen van apparaatdubbels met de DigitalTwins-API, wordt het resultaat mogelijk niet direct weer gegeven in de query-API-aanvragen. Het is voldoende om te wachten op een korte periode.
* Er worden geen subquery's ondersteund in de `FROM` instructie.
* `OUTER JOIN` semantische waarden worden niet ondersteund, wat betekent dat als de relatie een positie van nul heeft, de hele rij wordt verwijderd uit de uitvoer resultaatset.
* De diepte van de grafiek Traversal is beperkt tot vijf `JOIN` niveaus per query.
* De bron voor `JOIN` bewerkingen is beperkt: de query moet de apparaatdubbels declareren waar de query wordt gestart.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het schrijven van query's en het bekijken van voor beelden van client code in [*How-to: Query's uitvoeren op de dubbele grafiek*](how-to-query-graph.md).