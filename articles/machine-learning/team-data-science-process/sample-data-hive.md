---
title: Voorbeeld gegevens in azure HDInsight Hive-tabellen-team data Science process
description: Voor beelden van gegevens die zijn opgeslagen in azure HDInsight Hive-tabellen met hive-query's om de gegevens te beperken tot een grotere beheerbaar voor analyse.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6a015da77cb7c0ba54be1dd5e729a9ee8a848c9d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321889"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Voorbeeldgegevens in Hive-tabellen in Azure HDInsight
In dit artikel wordt beschreven hoe u voor beelden van gegevens die zijn opgeslagen in azure HDInsight-Hive-tabellen met hive-query's, kunt u deze beperken tot een grootte die meer kan worden beheerd voor analyse. Dit omvat drie veelgebruikte steekproef methoden:

* Uniforme aselecte steek proef
* Wille keurige steek proeven per groep
* Stratified-steek proeven

**Waarom een voor beeld van uw gegevens?**
Als de gegevensset die u wilt analyseren groot is, is het doorgaans een goed idee om de gegevens te verlagen om deze te verminderen tot een kleinere, maar representatieve en meer beheersbare grootte. Down sampling vereenvoudigt het leren van gegevens, het verkennen en functie-engineering. De rol van het team data Science proces is om snel een prototype van de functies voor gegevens verwerking en machine learning modellen mogelijk te maken.

Deze steekproef taak is een stap in het [team data Science process (TDSP)](./index.yml).

## <a name="how-to-submit-hive-queries"></a>Hive-query's verzenden
Hive-query's kunnen worden verzonden vanuit de Hadoop Command-Line-console op het hoofd knooppunt van het Hadoop-cluster.  Meld u aan bij het hoofd knooppunt van het Hadoop-cluster, open de Hadoop Command-Line-console en verzend de Hive-query's. Zie [Hive-Query's verzenden](move-hive-tables.md#submit)voor instructies over het verzenden van Hive-query's in de Hadoop Command-Line-console.

## <a name="uniform-random-sampling"></a><a name="uniform"></a> Uniforme aselecte steek proef
Uniforme wille keurige steek proeven betekenen dat elke rij in de gegevensset een gelijke kans heeft om te worden steek proeven. Het kan worden geïmplementeerd door een extra veld ASELECT () toe te voegen aan de gegevensset in de binnenste ' Select '-query en in de buitenste ' Select ' query die voor waarde voor dat wille keurig veld.

Hier volgt een voorbeeld van een query:

```python
SET sampleRate=<sample rate, 0-1>;
select
    field1, field2, …, fieldN
from
    (
    select
        field1, field2, …, fieldN, rand() as samplekey
    from <hive table name>
    )a
where samplekey<='${hiveconf:sampleRate}'
```

Hier `<sample rate, 0-1>` geeft u het aandeel van records op waarmee de gebruikers willen steek proeven.

## <a name="random-sampling-by-groups"></a><a name="group"></a> Wille keurige steek proeven per groep
Bij het bemonsteren van categorische-gegevens wilt u mogelijk alle instanties opnemen of uitsluiten voor een bepaalde waarde van de variabele categorische. Deze soort steek proef wordt ' steek proef op groep ' genoemd. Als u bijvoorbeeld een categorische-variabele '*State*' hebt, die waarden bevat zoals NY, ma, CA, NJ en PA, wilt u dat records uit elke staat samen komen, ongeacht of ze worden bemonsterd of niet.

Hier volgt een voor beeld van een query die voor beelden per groep bevat:

```python
SET sampleRate=<sample rate, 0-1>;
select
    b.field1, b.field2, …, b.catfield, …, b.fieldN
from
    (
    select
        field1, field2, …, catfield, …, fieldN
    from <table name>
    )b
join
    (
    select
        catfield
    from
        (
        select
            catfield, rand() as samplekey
        from <table name>
        group by catfield
        )a
    where samplekey<='${hiveconf:sampleRate}'
    )c
on b.catfield=c.catfield
```

## <a name="stratified-sampling"></a><a name="stratified"></a>Stratified-steek proeven
Wille keurige steek proeven zijn stratified ten opzichte van een categorische-variabele wanneer de voor beelden die zijn verkregen categorische waarden bevatten die aanwezig zijn in dezelfde verhouding als die in de bovenliggende populatie. Als u hetzelfde voor beeld gebruikt als hierboven, hebben uw gegevens de volgende waarnemingen per status: NJ heeft 100 waarnemingen, NY heeft 60 waarnemingen en WA heeft 300 waarnemingen. Als u het aantal stratified-steek proeven opgeeft om 0,5 te zijn, moet het verkregen voor beeld ongeveer 50, 30 en 150 waarnemingen van NJ, NY en WA bevatten.

Hier volgt een voorbeeld van een query:

```hiveql
SET sampleRate=<sample rate, 0-1>;
select
    field1, field2, field3, ..., fieldN, state
from
    (
    select
        field1, field2, field3, ..., fieldN, state,
        count(*) over (partition by state) as state_cnt,
          rank() over (partition by state order by rand()) as state_rank
      from <table name>
    ) a
where state_rank <= state_cnt*'${hiveconf:sampleRate}'
```

Zie [LanguageManual sampling](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling)(Engelstalig) voor meer informatie over geavanceerde bemonsterings methoden die beschikbaar zijn in Hive.