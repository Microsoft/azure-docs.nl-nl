---
title: Para meters voor de vervolg keuzelijst van Azure Monitor werkmap
description: Vereenvoudig complexe rapportage met vooraf samengestelde en aangepaste werkmappen met para meters die vervolg parameters bevatten
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: d6fb4954ff616bccd9b237aedb3001b0a8d592dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101717193"
---
# <a name="workbook-drop-down-parameters"></a>Para meters voor werkmap vervolg keuzelijst

Met de vervolg keuzelijst kunnen gebruikers een of meer invoer waarden uit een bekende set verzamelen (Selecteer bijvoorbeeld een van de aanvragen van uw app). De vervolg keuzelijsten bieden een gebruiks vriendelijke manier om wille keurige invoer van gebruikers te verzamelen. Vervolg keuzelijsten zijn vooral nuttig bij het inschakelen van filteren in uw interactieve rapporten. 

De eenvoudigste manier om een vervolg keuzelijst op te geven, is door een statische lijst te bieden in de instelling voor de para meter. Een interessante manier is om de lijst dynamisch op te halen via een KQL-query. Met parameter instellingen kunt u ook opgeven of het een enkelvoudige of meervoudige selectie is, en als het een meervoudige selectie is, hoe de resultatenset moet worden opgemaakt (scheidings teken, offerte, enzovoort).

## <a name="creating-a-static-drop-down-parameter"></a>Een statische vervolg keuzelijst maken

1. Beginnen met een lege werkmap in de bewerkings modus.
2. Kies _para meters toevoegen_ uit de koppelingen in de werkmap.
3. Klik op de knop Blue _para meter toevoegen_ .
4. Typ in het deel venster nieuwe para meters dat verschijnt:
    1. Parameter naam: `Environment`
    2. Parameter type: `Drop down`
    3. Vereist: `checked`
    4. Toestaan `multiple selection` : `unchecked`
    5. Gegevens ophalen uit: `JSON`
5. Plaats in het JSON-invoer tekst blok dit JSON-fragment:
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. Druk op de knop met het blauwetje `Update` .
7. Kies opslaan op de werk balk om de para meter te maken.
8. De omgevings parameter is een vervolg keuzelijst met de drie waarden.

    ![Afbeelding van het maken van een statische intervallen omlaag](./media/workbooks-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>Een statische vervolg keuzelijst maken met groepen items

Als uw query resultaat/JSON een groeps veld bevat, worden in de vervolg keuzelijst groepen waarden weer gegeven. Volg het bovenstaande voor beeld, maar gebruik in plaats daarvan de volgende JSON:

```json
[
    { "value":"dev", "label":"Development", "group":"Development" },
    { "value":"dev-cloud", "label":"Development (Cloud)", "group":"Development" },
    { "value":"ppe", "label":"Pre-production", "group":"Test" },
    { "value":"ppe-test", "label":"Pre-production (Test)", "group":"Test" },
    { "value":"prod1", "label":"Prod 1", "selected":true, "group":"Production" },
    { "value":"prod2", "label":"Prod 2", "group":"Production" }
]
```

![Afbeelding van een voor beeld van een gegroepeerde vervolg keuzelijst](./media/workbooks-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>Een dynamische vervolg keuzelijst maken
1. Beginnen met een lege werkmap in de bewerkings modus.
2. Kies _para meters toevoegen_ uit de koppelingen in de werkmap.
3. Klik op de knop Blue _para meter toevoegen_ .
4. Typ in het deel venster nieuwe para meters dat verschijnt:
    1. Parameter naam: `RequestName`
    2. Parameter type: `Drop down`
    3. Vereist: `checked`
    4. Toestaan `multiple selection` : `unchecked`
    5. Gegevens ophalen uit: `Query`
5. Plaats in het JSON-invoer tekst blok dit JSON-fragment:

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. Druk op de knop met het blauwetje `Run Query` .
2. Kies opslaan op de werk balk om de para meter te maken.
3. De para meter van de aanvrager is een vervolg keuzelijst van de namen van alle aanvragen in de app.

    ![Afbeelding van het maken van een dynamische vervolg keuzelijst](./media/workbooks-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>Verwijzing naar vervolg keuzelijst

### <a name="in-kql"></a>In KQL
1. Voeg een besturings element query toe aan de werkmap en selecteer een Application Insights resource.
2. Voer in de KQL-editor dit fragment in

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. Hiermee wordt de evaluatie tijd van de query uitgebreid naar:

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. Voer de query uit om de resultaten te bekijken. U kunt deze ook als grafiek weer geven.

    ![Afbeelding met een vervolg keuzelijst waarnaar wordt verwezen in KQL](./media/workbooks-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>Parameter waarde, label, selectie en groep
De query die wordt gebruikt in de para meter dynamische vervolg keuzelijst retourneert alleen een lijst met waarden die in de vervolg keuzelijst in orde worden weer gegeven. Maar wat als u een andere weergave naam wilde of een van deze wilt selecteren? Met para meters voor vervolg keuzelijst kunt u dit doen via de kolommen waarde, label, selectie en groep.

In het voor beeld hieronder ziet u hoe u een lijst kunt ophalen met Application Insights afhankelijkheden waarvan de weergave namen worden opgemaakt met een emoji, het eerste item dat is geselecteerd en dat is gegroepeerd op bewerkings namen.

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```

![Afbeelding van een vervolg keuzelijst met waarden, labels, selectie en groeps opties](./media/workbooks-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>Opties voor vervolg keuzelijst
| Parameter | Uitleg | Voorbeeld |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | De geselecteerde waarde | Fabrikamaccount ophalen |
| `{DependencyName:label}` | Het geselecteerde label | fabrikamaccount ophalen 🌐 |
| `{DependencyName:value}` | De geselecteerde waarde | Fabrikamaccount ophalen |

## <a name="multiple-selection"></a>Meerdere selecties
In de voor beelden is de para meter zo ver ingesteld dat er slechts één waarde in de vervolg keuzelijst wordt geselecteerd. Para meters voor vervolg keuzelijst ondersteunen ook `multiple selection` het inschakelen van dit is net zo eenvoudig als het controleren van de `Allow multiple selection` optie. 

De gebruiker heeft ook de optie om de indeling van de resultatenset op te geven via de `delimiter` `quote with` instellingen en. De standaard waarde retourneert alleen de waarden als een verzameling in dit formulier: ' a ', ' b ', ' c '. Ze hebben ook de mogelijkheid om het aantal selecties te beperken.

De KQL die verwijst naar de para meter moet worden gewijzigd om te kunnen werken met de indeling van het resultaat. De meest voorkomende manier om deze in te scha kelen, is via de `in` operator.

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

Hier volgt een voor beeld van de vervolg keuzelijst multi-select op het werk:

![Afbeelding van een vervolg keuzelijst met meervoudige selectie](./media/workbooks-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>Volgende stappen

* [Ga](./workbooks-overview.md#visualizations) voor meer informatie over werkmappen veel uitgebreide visualisaties opties.
* De toegang tot uw werkmap resources [beheren](./workbooks-access-control.md) en delen.