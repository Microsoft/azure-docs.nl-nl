---
title: 'How to: search the Data Catalog'
description: In dit artikel wordt beschreven hoe u een gegevenscatalogus kunt doorzoeken.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 178604335968c3664bde51c144759c1c040c359d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564910"
---
# <a name="search-the-azure-purview-data-catalog"></a>Zoeken in de Azure Purview-gegevenscatalogus

Gegevensdetectie is de eerste stap voor een workload voor gegevensanalyse of gegevensbeheer voor gegevensverbruikers. Gegevensdetectie kan tijdrovend zijn omdat u mogelijk niet weet waar u de persoonsgegevens kunt vinden. Zelfs na het vinden van de gegevens hebt u mogelijk twijfels over of u de gegevens kunt vertrouwen en er afhankelijk van kunt worden.

Het doel van het zoeken in Azure Purview is om het proces van gegevensdetectie te versnellen om snel de belangrijke gegevens te vinden. In dit artikel wordt beschreven hoe u de Azure Purview-gegevenscatalogus kunt doorzoeken om snel de gegevens te vinden die u zoekt.

## <a name="search-the-catalog-for-assets"></a>Zoeken in de catalogus naar assets

In Azure Purview bevindt de zoekbalk zich bovenaan de UX van Purview Studio.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Schermopname van de locatie van de Azure Purview-zoekbalk" border="true":::

Wanneer u op de zoekbalk klikt, ziet u uw recente zoekgeschiedenis en onlangs toegang tot assets. Selecteer 'Alles weergeven' om alle onlangs bekeken assets te bekijken.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="Schermopname van de zoekbalk voordat er trefwoorden zijn ingevoerd" border="true":::

Voer trefwoorden in die u helpen bij het identificeren van uw asset, zoals de naam, het gegevenstype, classificaties en woordenlijsttermen. Bij het invoeren van trefwoorden met betrekking tot uw gewenste asset geeft Azure Purview suggesties weer over wat er moet worden gezocht en mogelijke overeenkomsten met activa. Als u uw zoekopdracht wilt voltooien, klikt u op Zoekresultaten weergeven of drukt u op Enter.

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="Schermopname van de zoekbalk wanneer een gebruiker trefwoorden invoert" border="true":::

Op de pagina met zoekresultaten ziet u een lijst met assets die overeenkomen met de trefwoorden die zijn opgegeven op volgorde van relevantie. Er zijn verschillende factoren die van invloed kunnen zijn op de relevantiescore van een asset. U kunt de lijst verder filteren door specifieke gegevensopslag, classificaties, contactpersonen, labels en woordenlijsttermen te selecteren die van toepassing zijn op de asset die u zoekt.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Schermopname van de resultaten van een zoekopdracht" border="true":::

 Klik op de gewenste asset om de pagina met assetdetails weer te geven waar u eigenschappen kunt bekijken, waaronder schema,gegevens en eigenaren van activa.

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="Schermopname van de pagina met assetdetails" border="true":::

## <a name="search-query-syntax"></a>Querysyntaxis zoeken

Alle zoekquery's bestaan uit trefwoorden en operators. Een trefwoord is iets dat deel uitmaakt van de eigenschappen van een asset. Mogelijke trefwoorden kunnen een classificatie, woordenlijstterm, assetbeschrijving of een assetnaam zijn. Een trefwoord kan slechts een deel zijn van de eigenschap die u wilt gebruiken. Gebruik trefwoorden en de operators die hieronder worden vermeld om ervoor te zorgen dat Azure Purview de assets retourneert die u zoekt. 

Hieronder vindt u de operators die kunnen worden gebruikt om een zoekquery samen te stellen. Operators kunnen zo vaak worden gecombineerd als nodig is in één query.

| Operator | Definitie | Voorbeeld |
| -------- | ---------- | ------- |
| OF | Hiermee geeft u op dat een asset ten minste één van de twee trefwoorden moet hebben. Moet een hoofdletter hebben. Een witruimte is ook een OR-operator.  | De query `hive OR database` retourneert assets die 'hive' of 'database' of beide bevatten. |
| AND | Hiermee geeft u op dat een asset beide trefwoorden moet hebben. Moet een hoofdletter hebben | De query `hive AND database` retourneert assets die zowel 'hive' als 'database' bevatten. |
| NOT | Hiermee geeft u op dat een asset niet het trefwoord rechts van de NOT-component kan bevatten | De query `hive NOT database` retourneert assets die 'hive' bevatten, maar niet 'database'. |
| () | Groepeert een set trefwoorden en operators. Bij het combineren van meerdere operators geeft u met haakjes de volgorde van de bewerkingen op. | De query `hive AND (database OR warehouse)` retourneert assets die 'hive' en 'database' of 'warehouse' of beide bevatten. |
| "" | Hiermee geeft u de exacte inhoud op in een woordgroep waar de query op moet overeenkomen. | De query `"hive database"` retourneert assets die de zin 'hive-database' in hun eigenschappen bevatten |
| * | Een jokerteken dat overeenkomt met één tot veel tekens. Kan niet het eerste teken in een trefwoord zijn. | De query `dat*` retourneert assets die eigenschappen hebben die beginnen met 'dat', zoals 'gegevens' of 'database'. |
| ? | Een jokerteken dat overeenkomt met één teken. Kan niet het eerste teken in een trefwoord zijn | De query retourneert assets met eigenschappen die beginnen met 'dat' en die vier letters zijn, zoals `dat?` 'datum' of 'gegevens'. |

> [!Note]
> Geef altijd Booleaanse operators (**EN**, **OF**, **NOT**) op in hoofdletters. Anders maakt case niet uit en zijn er geen extra spaties.

## <a name="next-steps"></a>Volgende stappen

- [Verklarende woordenlijsten maken, importeren en exporteren](how-to-create-import-export-glossary.md)
- [Termsjablonen beheren voor zakelijke woordenlijst](how-to-manage-term-templates.md)
