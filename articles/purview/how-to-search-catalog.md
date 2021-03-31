---
title: 'Procedure: zoeken in de Data Catalog'
description: Dit artikel bevat een overzicht van het doorzoeken van een gegevens catalogus.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 7799266bf9cece1ed789d6fab64ec970a09fbfcb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588434"
---
# <a name="search-the-azure-purview-data-catalog"></a>Zoeken in de Azure Purview-gegevenscatalogus

Gegevens detectie is de eerste stap voor een gegevens analyse of Data Governance-werk belasting voor gegevens gebruikers. Gegevens detectie kan tijdrovend zijn, omdat u mogelijk niet weet waar u de gewenste gegevens kunt vinden. Zelfs nadat u de gegevens hebt gevonden, weet u mogelijk zeker of u de gegevens kunt vertrouwen en er afhankelijk van wilt maken.

Het doel van zoeken in azure controle sfeer liggen is het proces van gegevens detectie te versnellen, zodat u snel de gegevens kunt vinden die van belang zijn. In dit artikel wordt beschreven hoe u in de Azure controle sfeer liggen Data Catalog kunt zoeken naar de gegevens die u zoekt.

## <a name="search-the-catalog-for-assets"></a>De catalogus doorzoeken op assets

De zoek balk bevindt zich boven aan de controle sfeer liggen Studio UX in azure controle sfeer liggen.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Scherm afbeelding van de locatie van de Azure controle sfeer liggen-zoek balk" border="true":::

Wanneer u op de zoek balk klikt, kunt u uw recente Zoek geschiedenis en onlangs gebruikte assets bekijken. Selecteer alles weer geven om alle recent bekeken assets te bekijken.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="Scherm opname van de zoek balk voordat tref woorden zijn ingevoerd" border="true":::

Voer in tref woorden in om uw asset te identificeren, zoals de naam, het gegevens type, de classificaties en de termen van de woorden lijst. Wanneer u tref woorden voor de gewenste activa invoert, worden in azure controle sfeer liggen suggesties weer gegeven over wat u zoekt en mogelijke activa overeenkomsten. Klik op zoek resultaten weer geven om uw zoek opdracht te volt ooien of druk op ENTER.

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="Scherm opname van de zoek balk wanneer een gebruiker in tref woorden invoert" border="true":::

De pagina met zoek resultaten bevat een lijst met assets die overeenkomen met de tref woorden die zijn opgegeven in volg orde van relevantie. Er zijn verschillende factoren die van invloed kunnen zijn op de relevantie Score van een Asset. U kunt de lijst verder filteren door specifieke gegevens archieven, classificaties, contact personen, labels en termen te selecteren die van toepassing zijn op de activa die u zoekt.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Scherm opname van de resultaten van een zoek opdracht" border="true":::

 Klik op het gewenste activum om de pagina met activa gegevens weer te geven, waar u eigenschappen kunt bekijken, zoals schema, afkomst en eigen aren van activa.

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="Scherm opname van de pagina met activa Details" border="true":::

## <a name="search-query-syntax"></a>Zoek query syntaxis

Alle zoek query's bestaan uit tref woorden en Opera tors. Een sleutel woord is een iets dat deel uitmaakt van de eigenschappen van een activum. Mogelijke sleutel woorden kunnen een classificatie, een woordenlijst term, een beschrijving van een activum of een Asset name zijn. Een tref woord kan slechts een deel zijn van de eigenschap die u wilt zoeken. Gebruik tref woorden en de hieronder vermelde Opera tors om ervoor te zorgen dat Azure controle sfeer liggen de assets retourneert die u zoekt. 

Hieronder ziet u de Opera tors die kunnen worden gebruikt voor het samen stellen van een zoek query. Opera tors kunnen zo vaak als nodig worden gecombineerd in één query.

| Operator | Definitie | Voorbeeld |
| -------- | ---------- | ------- |
| OF | Hiermee geeft u op dat een activum ten minste één van de twee sleutel woorden moet bevatten. Moet in alle hoofd letters zijn. Een spatie is ook een OR-operator.  | De query `hive OR database` retourneert assets die ' hive ' of ' data base ' of beide bevatten. |
| AND | Hiermee geeft u op dat een activum beide sleutel woorden moet bevatten. Moet in hoofd letters zijn | De query `hive AND database` retourneert assets die zowel ' hive ' als ' data base ' bevatten. |
| NOT | Hiermee geeft u op dat een Asset het sleutel woord niet mag bevatten rechts van de component NOT | De query `hive NOT database` retourneert assets die ' hive ' bevatten, maar niet ' data base '. |
| () | Groepeert een set tref woorden en Opera tors samen. Bij het combi neren van meerdere opera Tors, geeft haakjes de volg orde van de bewerkingen op. | De query `hive AND (database OR warehouse)` retourneert assets die ' hive ' en ' data base ' of ' Warehouse ' bevatten, of beide. |
| "" | Hiermee geeft u de exacte inhoud op van een woord groep waarmee de query moet overeenkomen. | De query `"hive database"` retourneert assets die de zin ' hive-data base ' bevatten in hun eigenschappen |
| * | Een Joker teken dat overeenkomt met een tot veel tekens. Kan niet het eerste teken in een tref woord zijn. | De query `hiv\` * retourneert assets die eigenschappen hebben die beginnen met ' HIV ', zoals ' hive ' of ' hive-Table '. |
| ? | Een Joker teken dat overeenkomt met één teken. Kan niet het eerste teken in een tref woord zijn | De query `hiv?` retourneert assets die eigenschappen hebben die beginnen met ' HIV ' en vier letters zijn, zoals ' hive ' of ' HIVA '. |

> [!Note]
> Geef altijd Booleaanse Opera tors (**en**, **of**, **niet**) op in hoofd letters. Als dat niet het geval is, maakt u geen gebruik van extra spaties.

## <a name="next-steps"></a>Volgende stappen

- [Termen van een woorden lijst maken, importeren en exporteren](how-to-create-import-export-glossary.md)
- [Term sjablonen voor de woorden lijst voor bedrijven beheren](how-to-manage-term-templates.md)
