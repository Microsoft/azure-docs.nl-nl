---
title: Trans formatie afvlakken bij toewijzing van gegevens stroom
description: Hiërarchische gegevens opwaarderen met behulp van de afvlakkings transformatie
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: a0e75957a0ab49394dab56f2b7fb847dee4b43cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "81413682"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>Trans formatie afvlakken bij toewijzing van gegevens stroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gebruik de afvlakkings transformatie om matrix waarden binnen hiërarchische structuren te maken, zoals JSON en ze uit te vouwen in afzonderlijke rijen. Dit proces wordt ook wel denormaliseren genoemd.

## <a name="configuration"></a>Configuratie

De afvlakkings transformatie bevat de volgende configuratie-instellingen

![Instellingen voor afvlakking](media/data-flow/flatten1.png "Instellingen voor afvlakking")

### <a name="unroll-by"></a>Uitrollen

Selecteer een matrix om uit te vouwen. De uitvoer gegevens hebben één rij per item in elke matrix. Als de waarde voor de uitrollen in de invoer rij null of leeg is, wordt er één uitvoer rij met niet-gerollte waarden als null.

### <a name="unroll-root"></a>Hoofdmap uitrollen

De afvlakkings transformatie ontdraait standaard een matrix naar de bovenkant van de hiërarchie waarin deze zich bevindt. U kunt desgewenst een matrix selecteren als uw root uit de hoofdmap. De uitrollen root moet een matrix zijn met complexe objecten die de waarde van het element untotal by bevatten. Als u een hoofdmap uitrollen selecteert, bevatten de uitvoer gegevens ten minste één rij per items in de hoofdmap van het uitrollen. Als de invoer rij geen items bevat in de uitrollen root, wordt deze verwijderd uit de uitvoer gegevens. Als u een uitrollend element kiest, wordt altijd een kleiner of gelijk aantal rijen uitgevoerd dan het standaard gedrag.

### <a name="flatten-mapping"></a>Afvlakking koppelen

Net als bij de Select trans formatie kiest u de projectie van de nieuwe structuur van binnenkomende velden en de gedenormaliseerde matrix. Als een gedenormaliseerde matrix wordt toegewezen, wordt de uitvoer kolom hetzelfde gegevens type als de matrix. Als de deroll by-matrix een matrix is met complexe objecten die submatrixen bevat, wordt een item van die subarry een matrix te koppelen.

Raadpleeg het tabblad controleren en de voorbeeld gegevens om de toewijzings uitvoer te controleren.

## <a name="examples"></a>Voorbeelden

Raadpleeg het volgende JSON-object voor de onderstaande voor beelden van de afvlakkings transformatie

``` json
{
  "name":"MSFT","location":"Redmond", "satellites": ["Bay Area", "Shanghai"],
  "goods": {
    "trade":true, "customers":["government", "distributer", "retail"],
    "orders":[
        {"orderId":1,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":2}]}},
        {"orderId":2,"orderTotal":323.34,"shipped":{"orderItems":[{"itemName":"Mice","itemQty":2},{"itemName":"Keyboard","itemQty":1}]}}
    ]}}
{"name":"Company1","location":"Seattle", "satellites": ["New York"],
  "goods":{"trade":false, "customers":["store1", "store2"],
  "orders":[
      {"orderId":4,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":3}]}},
      {"orderId":5,"orderTotal":343.24,"shipped":{"orderItems":[{"itemName":"Chair","itemQty":4},{"itemName":"Lamp","itemQty":2}]}}
    ]}}
{"name": "Company2", "location": "Bellevue",
  "goods": {"trade": true, "customers":["Bank"], "orders": [{"orderId": 4, "orderTotal": 123.34}]}}
{"name": "Company3", "location": "Kirkland"}
```

### <a name="no-unroll-root-with-string-array"></a>Geen uitrollen basis met teken reeks matrix

| Uitrollen | Hoofdmap uitrollen | Projectie |
| --------- | ----------- | ---------- |
| goederen. klanten | Geen | naam <br> klant = goederen. klant |

#### <a name="output"></a>Uitvoer

```
{ 'MSFT', 'government'}
{ 'MSFT', 'distributer'}
{ 'MSFT', 'retail'}
{ 'Company1', 'store'}
{ 'Company1', 'store2'}
{ 'Company2', 'Bank'}
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>Geen uitrollen basis met complexe matrix

| Uitrollen | Hoofdmap uitrollen | Projectie |
| --------- | ----------- | ---------- |
| goederen. orders. verzonden. orderItems | Geen | naam <br> orderId = goederen. orders. orderId <br> ItemName = goederen. orders. verzonden. orderItems. ItemName <br> itemQty = goederen. orders. verzonden. orderItems. itemQty <br> locatie = locatie |

#### <a name="output"></a>Uitvoer

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>Zelfde basis als ondraai matrix

| Uitrollen | Hoofdmap uitrollen | Projectie |
| --------- | ----------- | ---------- |
| goederen. orders | goederen. orders | naam <br> goederen. orders. verzonden. orderItems. ItemName <br> goederen. klanten <br> location |

#### <a name="output"></a>Uitvoer

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>Root uitrollen met complexe matrix

| Uitrollen | Hoofdmap uitrollen | Projectie |
| --------- | ----------- | ---------- |
| goederen. orders. verzonden. orderItem | goederen. orders |naam <br> orderId = goederen. orders. orderId <br> ItemName = goederen. orders. verzonden. orderItems. ItemName <br> itemQty = goederen. orders. verzonden. orderItems. itemQty <br> locatie = locatie |

#### <a name="output"></a>Uitvoer

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
```

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntax

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>Voorbeeld

```
source foldDown(unroll(goods.orders.shipped.orderItems, goods.orders),
    mapColumn(
        name,
        orderId = goods.orders.orderId,
        itemName = goods.orders.shipped.orderItems.itemName,
        itemQty = goods.orders.shipped.orderItems.itemQty,
        location = location
    ),
    skipDuplicateMapInputs: false,
    skipDuplicateMapOutputs: false) 
```    

## <a name="next-steps"></a>Volgende stappen

* Gebruik de [draai transformatie](data-flow-pivot.md) om rijen naar kolommen te draaien.
* Gebruik de [draai tabel transformatie](data-flow-unpivot.md) om kolommen naar rijen te draaien.
