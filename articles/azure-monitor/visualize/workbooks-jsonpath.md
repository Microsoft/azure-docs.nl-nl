---
title: 'Azure Monitor werkmappen: JSON-gegevens transformeren met JSONPath'
description: Hoe u JSONPath in Azure Monitor werkmappen kunt gebruiken om de resultaten van JSON-gegevens die door een query-eind punt zijn geretourneerd, te transformeren naar de gewenste indeling.
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: 142853f0d5ed787d0b7aaee1bb118a001f144227
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101722922"
---
# <a name="how-to-use-jsonpath-to-transform-json-data-in-workbooks"></a>JSONPath gebruiken om JSON-gegevens in werkmappen te transformeren

Werkmappen kan gegevens uit een groot aantal bronnen opvragen. Sommige eind punten, zoals [Azure Resource Manager](../../azure-resource-manager/management/overview.md) of aangepast eind punt, kunnen resultaten in JSON retour neren. Als de JSON-gegevens die door het aangevraagde eind punt worden geretourneerd, niet zijn geconfigureerd in een indeling die u wenst, kan JSONPath worden gebruikt om de resultaten te transformeren.

JSONPath is een query taal voor JSON die vergelijkbaar is met XPath voor XML. Net als XPath biedt JSONPath de mogelijkheid om gegevens uit de JSON-structuur te extra heren en te filteren.

Met behulp van JSONPath-trans formatie kunnen schrijvers van werkmappen JSON omzetten in een tabel structuur. De tabel kan vervolgens worden gebruikt voor het uitzetten van [werkmap visualisaties](./workbooks-overview.md#visualizations).

## <a name="using-jsonpath"></a>JSONPath gebruiken

1. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht *bewerken* .
2. Gebruik de koppeling *add*  >  *query* toevoegen om een besturings element query toe te voegen aan de werkmap.
3. Selecteer de gegevens bron als *JSON*.
4. De JSON-editor gebruiken om het volgende JSON-fragment in te voeren
    ```json
    { "store": {
        "books": [ 
          { "category": "reference",
            "author": "Nigel Rees",
            "title": "Sayings of the Century",
            "price": 8.95
          },
          { "category": "fiction",
            "author": "Evelyn Waugh",
            "title": "Sword of Honour",
            "price": 12.99
          },
          { "category": "fiction",
            "author": "Herman Melville",
            "title": "Moby Dick",
            "isbn": "0-553-21311-3",
            "price": 8.99
          },
          { "category": "fiction",
            "author": "J. R. R. Tolkien",
            "title": "The Lord of the Rings",
            "isbn": "0-395-19395-8",
            "price": 22.99
          }
        ],
        "bicycle": {
          "color": "red",
          "price": 19.95
        }
      }
    }
    ```  

We gaan ervan uit dat we het bovenstaande JSON-object hebben gezien als een representatie van de inventaris van een winkel. Onze taak is het maken van een tabel van de beschik bare boeken van de winkel door hun titels, auteurs en prijzen weer te geven.

1. Selecteer het tabblad *resultaten instellingen* en schakel de resultaat indeling naar *JSON-pad*.
2. Pas de volgende instellingen voor het JSON-pad toe:

    Tabel JSON-pad: `$.store.books` . Dit veld vertegenwoordigt het pad naar de hoofdmap van de tabel. In dit geval zorgen we voor de inventaris van de winkel. Het pad naar de tabel filtert de JSON met de boek gegevens.

   | Kolom-Id's | Kolom-JSON-pad |
   |:-----------|:-----------------|
   | Titel      | `$.title`        |
   | Auteur     | `$.author`       |
   | Prijs      | `$.price`        |

    Kolom-Id's worden de kolom koppen. Kolom JSON-paden velden vertegenwoordigen het pad van de hoofdmap van de tabel naar de kolom waarde.

1. Pas de bovenstaande instellingen toe door te klikken op *query uitvoeren*

![ Query-item bewerken met JSON-gegevens bron en resultaat indeling van JSON-pad](./media/workbooks-jsonpath/query-jsonpath.png)

## <a name="next-steps"></a>Volgende stappen
- [Overzicht van werkmappen](./workbooks-overview.md)
- [Groepen in Azure Monitor werkmappen](workbooks-groups.md)