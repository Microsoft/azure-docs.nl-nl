---
title: Aangepaste query specificatie
description: Meer informatie over het gebruik van aangepaste query's voor het programmatisch ophalen van gegevens uit analyse tabellen voor uw aanbiedingen in micro soft Commercial Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4be063342a6c46d73c86f2d9dff1da5395328389
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583831"
---
# <a name="custom-query-specification"></a>Aangepaste query specificatie

Partners kunnen deze query specificatie gebruiken om eenvoudig aangepaste query's te formuleren voor het extra heren van gegevens uit analyse tabellen. De query's kunnen worden gebruikt om alleen de gewenste kolommen en metrische gegevens te selecteren die overeenkomen met een bepaald criterium. De kern van de taal specificatie is de definitie van de gegevensset waarop een aangepaste query kan worden geschreven.

## <a name="datasets"></a>Gegevenssets

Op dezelfde manier als sommige query's worden uitgevoerd op een Data Base met tabellen en kolommen, kan een aangepaste query worden gebruikt voor gegevens sets met kolommen en metrische gegevens. De volledige lijst met beschik bare gegevens sets voor het formuleren van een query kan worden verkregen met behulp van de data sets-API.

Hier volgt een voor beeld van een gegevensset die wordt weer gegeven als een JSON-bestand.

```json
{
     "datasetName": "ISVUsage",
     "selectableColumns": [
       "MarketplaceSubscriptionId",
       "OfferName",
            "CustomerId",
     "MonthStartDate",
     "SKU"
    ],
    "availableMetrics": [
     "NormalizedUsage", 
     "RawUsage"
        "EstimatedExtendedChargeCC"
    ],
    "availableDateRanges": [
     "LAST_MONTH",
     "LAST_3_MONTHS",
     "LAST_6_MONTHS",
     "LIFETIME"
    ]
}
```

### <a name="parts-of-a-dataset"></a>Onderdelen van een gegevensset

- De naam van een gegevensset is net als de naam van een database tabel. Bijvoorbeeld ISVUsage. Een gegevensset bevat een lijst met kolommen die kunnen worden geselecteerd, zoals MarketplaceSubscriptionId.
- Een gegevensset bevat ook metrische gegevens, zoals aggregatie functies in een Data Base. Bijvoorbeeld NormalizedUsage.
- Er zijn vaste tijds duren waarover gegevens kunnen worden geëxporteerd.

### <a name="formulating-a-query-on-a-dataset"></a>Een query voor een gegevensset formuleren

Dit zijn enkele voor beelden van query's die laten zien hoe verschillende typen gegevens moeten worden geëxtraheerd.

| Query | Beschrijving |
| ------------ | ------------- |
| **Selecteer** MarketplaceSubscriptionId, CustomerId **van** ISVUsage **span LAST_MONTH** | Deze query krijgt elke unieke `MarketplaceSubscriptionId` en bijbehorende overeenkomende `CustomerId` in de afgelopen maand. |
| **Selecteer** MarketplaceSubscriptionId, EstimatedExtendedChargeCC **van** ISVUSAGE **order by** EstimatedExtendedChargeCC **limiet** 10 | Met deze query worden de Top 10-abonnementen in aflopende volg orde opgehaald van het aantal licenties dat is verkocht onder elk abonnement. |
| **Selecteer** KlantId, NormalizedUsage, RawUsage **van** ISVUSAGE **order by** NormalizedUsage **waarbij** NormalizedUsage > 100000 **order by** NormalizedUsage **span** LAST_6_MONTHS | Met deze query worden de NormalizedUsage en RawUsage opgehaald van alle klanten die groter zijn dan 100.000. |
| **Selecteer** MarketplaceSubscriptionId, MonthStartDate, NormalizedUsage **van** ISVUsage **waarbij** KlantId in (' 2a31c234-1f4e-4c60-909e-76d234f93161 ', ' 80780748-3f9a-11eb-b378-0242ac130002 ') | Met deze query worden de `MarketplaceSubscriptionId` en de gegenereerde omzet voor elke maand door de twee `CustomerId` waarden opgehaald: `2a31c234-1f4e-4c60-909e-76d234f93161` en `80780748-3f9a-11eb-b378-0242ac130002` . |
|||

## <a name="query-specification"></a>Query specificatie

In deze sectie worden de definitie en structuur van de query beschreven.

### <a name="grammar-reference"></a>Naslag informatie voor de grammatica

In deze tabel worden de symbolen beschreven die in query's worden gebruikt.

| Symbool | Betekenis |
| ------------ | ------------- |
| ? | Optioneel |
| * | Nul of meer |
| + | Een of meer |
| &#124; | Of/een van de lijst |
| | |

### <a name="query-definition"></a>Query definitie

De query-instructie bevat de volgende componenten: SelectClause, FromClause, WhereClause?, OrderClause?, LimitClause? en time span?.

- **SelectClause**: **Select** ColumOrMetricName (, ColumOrMetricName) *
    - **ColumOrMetricName**: kolommen en metrische gegevens die in de gegevensset zijn gedefinieerd
- **FromClause**: **van** datasetnaam
    - **Datasetnaam**: naam van gegevensset gedefinieerd binnen de gegevensset
- **WhereClause**: **waarbij** FilterCondition (**en** FilterCondition) *
    - **FilterCondition**: ColumOrMetricName-operator waarde
        - **Operator**: = | > | < | >= | <= |! = | ZOALS | NIET ALS | IN | NIET IN
        - **Waarde**: nummer | StringLiteral | MultiNumberList | MultiStringList 
            - **Nummer**:-? [0-9] + (. [0-9] [0-9] *)?
            - **StringLiteral**: ' [a-Za-z0-9_] * '
            - **MultiNumberList**: (getal (, getal) *)
            - **MultiStringList**: (StringLiteral (, StringLiteral) *)
- **OrderClause**: **order by** OrderCondition (, OrderCondition) *
    - **OrderCondition**: ColumOrMetricName (**ASC**  |  **DESC**) *
    - **LimitClause**: **limiet** [0-9] +
    - **Time span**: **time span** (vandaag | GISTEREN | LAST_7_DAYS | LAST_14_DAYS | LAST_30_DAYS | LAST_90_DAYS | LAST_180_DAYS | LAST_365_DAYS | LAST_MONTH | LAST_3_MONTHS | LAST_6_MONTHS | LAST_1_YEAR | DOOD

### <a name="query-structure"></a>Query structuur

Een rapport query bestaat uit meerdere onderdelen:

- SELECT
- FROM
- WHERE
- ORDER BY
- ONDERGRENS
- DUUR

Elk onderdeel wordt hieronder beschreven.

#### <a name="select"></a>SELECT

In dit deel van de query worden de kolommen opgegeven die worden geëxporteerd. De kolommen die kunnen worden geselecteerd, zijn de velden die worden weer gegeven in `selectableColumns` en `availableMetrics` secties van een gegevensset. De laatste geëxporteerde rijen bevatten altijd afzonderlijke waarden in de geselecteerde kolommen. Er zijn bijvoorbeeld geen dubbele rijen in het geëxporteerde bestand. Metrische gegevens worden berekend voor elke afzonderlijke combi natie van de geselecteerde kolommen.

**Voorbeeld**:
- **Selecteren** `OfferName` , `NormalizedUsage`

#### <a name="from"></a>FROM

Dit deel van de query geeft de gegevensset aan waaruit gegevens moeten worden geëxporteerd. De naam van de gegevensset die u hier opgeeft, moet een geldige dataset-naam zijn die wordt geretourneerd door de data sets-API.

**Voorbeeld**:
- Van `ISVUsage`
- Van `ISVOrder`

#### <a name="where"></a>WHERE

Dit deel van de query wordt gebruikt om filter voorwaarden op de gegevensset op te geven. Alleen rijen die overeenkomen met alle voor waarden in deze component, worden weer gegeven in het geëxporteerde bestand. De filter voorwaarde kan zich bevinden op een van de kolommen in `selectableColumns` en `availableMetrics` . De waarden die zijn opgegeven in de filter voorwaarde, kunnen alleen een lijst met getallen of een lijst met teken reeksen zijn als de operator `IN` of is `NOT IN` . De waarden kunnen altijd worden opgegeven als een letterlijke teken reeks en ze worden geconverteerd naar de systeem eigen typen kolommen. Meerdere filter voorwaarden moeten worden gescheiden met een `AND` bewerking.

**Voorbeeld**:

- MarketplaceSubscriptionId = ' 868368da-957d-4959-8992-3c12dc7e6260 '
- Customername **zoals** % Contosso%
- KlantId **niet in** (1000, 1001, 1002)
- OrderQuantity = 100
- OrderQuantity = ' 100 '
    - MarketplaceSubscriptionId = ' 7b487ac0-ce12-b732-dcd6-91a1e4e74a50 ' en CustomerId = ' 0f8b7fa0-eb83-a183-1225-ca153ef807aa '

#### <a name="order-by"></a>ORDER BY

In dit deel van de query worden de sorteer criteria voor de geëxporteerde rijen opgegeven. De kolommen waarvan de volg orde kan worden gedefinieerd, moeten afkomstig zijn van de `selectableColumns` en `availableMetrics` van de gegevensset. Als er geen sorteer richting is opgegeven, wordt deze standaard ingesteld `DESC` op de kolom. Volg orde kan worden gedefinieerd voor meerdere kolommen door de criteria te scheiden met een komma.

**Voorbeeld**:

- sorteren **op** NormalizedUsage **ASC**, ESTIMATEDEXTENDEDCHARGE (CC) **DESC**
- sorteren **op** Customername **ASC**, NormalizedUsage

#### <a name="limit"></a>ONDERGRENS

In dit deel van de query wordt het aantal rijen aangegeven dat moet worden geëxporteerd. Het getal dat u opgeeft, moet een positief geheel getal zijn dat niet gelijk is aan nul.

#### <a name="timespan"></a>DUUR

In dit deel van de query wordt de tijds duur opgegeven waarvoor de gegevens moeten worden geëxporteerd. De mogelijke waarden moeten afkomstig zijn uit het `availableDateRanges` veld in de definitie van de gegevensset.

### <a name="case-sensitivity-in-query-specification"></a>Hoofdletter gevoeligheid in query specificatie

De specificatie is volledig niet hoofdletter gevoelig. Vooraf gedefinieerde tref woorden, kolom namen en waarden kunnen worden opgegeven met een hoofd letter of kleine letters.

## <a name="see-also"></a>Zie ook

- [Lijst met systeem query's](analytics-system-queries.md)
