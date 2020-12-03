---
title: 'Procedure: zoeken in de Data Catalog'
description: Dit artikel bevat een overzicht van het doorzoeken van een gegevens catalogus.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/15/2020
ms.openlocfilehash: 87ca3c49d6a96f083cc50f01f5b9a4a739d688a1
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552737"
---
# <a name="search-the-azure-purview-data-catalog"></a>Zoek in de Azure controle sfeer liggen-Data Catalog

In dit artikel wordt beschreven hoe u de verschillende zoek functies in azure controle sfeer liggen Data Catalog gebruikt.

## <a name="search-the-catalog-for-assets"></a>De catalogus doorzoeken op assets

De stappen voor het uitvoeren van een zoek actie op activa zijn:

1. [Open het dialoog venster voor het zoeken van assets](#open-the-asset-search-dialog) door **Zoek catalogus** te selecteren.
1. [Voer zoek termen](#enter-search-terms) in om activa te vinden met kenmerken die overeenkomen met de voor waarden.
1. [Stel snelle filters](#set-quick-filters) in om de zoek opdracht te verfijnen.
1. [Start de zoek opdracht](#start-the-search) en ga naar de zoek resultaten.

Het maakt niet uit of u snel filters instelt vóór of na het invoeren van zoek termen.

Als er geen zoek termen en geen filters zijn, bevatten de zoek resultaten alle activa.

### <a name="open-the-asset-search-dialog"></a>Het dialoog venster voor het zoeken van assets openen

Open het dialoog venster voor het zoeken van assets door **Zoek catalogus** te selecteren.

:::image type="content" source="./media/how-to-search-catalog/search-catalog.png" alt-text="Onder zoek catalogus bevindt zich een linkerdeel venster met zoek filters en een rechterdeel venster met recente Zoek opdrachten." border="true":::

Het dialoog venster zoeken bevat snelle filters, een zoek geschiedenis en een lijst met onlangs geopende assets.

:::image type="content" source="./media/how-to-search-catalog/asset-search-dialog.png" alt-text="De zoek lijst bevindt zich in het rechterdeel venster onder zoek catalogus." border="true":::

### <a name="enter-search-terms"></a>Zoek termen invoeren

Voer een of meer zoek termen in de **Zoek catalogus** in. Terwijl u typt, worden overeenkomende zoek termen van recente Zoek opdrachten weer gegeven in **uw recente Zoek opdrachten**, worden voorgestelde zoek termen weer gegeven in **Zoek suggesties** en worden overeenkomende **gegevensassets in de activa suggesties** vermeld.

:::image type="content" source="./media/how-to-search-catalog/enter-search-terms.png" alt-text="Scherm opname van de resultaten van een zoek opdracht die wordt ingevoerd in het vak Zoek catalogus":::

Zoek resultaten bevatten alleen assets met een of meer kenmerken die overeenkomen met de zoek termen. Deze kenmerken zijn onder andere Asset name, Asset type, classificaties en contact personen.

#### <a name="types-of-search-criteria"></a>Typen zoek criteria

Azure controle sfeer liggen ondersteunt de volgende typen zoek criteria.

> [!Note]
> Geef altijd Booleaanse Opera tors (**en**, **of**, **niet**) op in hoofd letters. Als dat niet het geval is, maakt u geen gebruik van extra spaties.

- **Hive**: documenten zoeken die **Hive** bevatten.
- **Hive-data base**: documenten zoeken die precies een **Hive-data base** bevatten.
- **Hive of data base**: documenten zoeken die **onderdeel** of **Data Base** bevatten, of beide.
- **Hive en data base**, **Hive && data base**: documenten zoeken die zowel **Hive** als **Data Base** bevatten.
- **Hive en (Data Base of Warehouse)**: documenten zoeken die een **Hive** bevatten, of een **Data Base** of een **magazijn**, of beide.
- **Hive niet data base**: documenten zoeken die een **Hive** bevatten, maar geen **Data Base**.
- **HIV**: documenten zoeken die een woord bevatten dat begint met **HIV**. Bijvoorbeeld, **HIV**, **Hive**, **hivbar** (* is een Joker teken dat overeenkomt met een wille keurig aantal tekens).

### <a name="set-quick-filters"></a>Snelle filters instellen

De lijst met zoek resultaten is gebaseerd op de zoek termen die u in de **Zoek catalogus** invoert en op de waarden die u voor de snelle filters selecteert.

Met een snel filter wordt de lijst met zoek resultaten beperkt tot assets die een geselecteerde waarde van een kenmerk hebben. Het filter heeft een vervolg keuzelijst en een tekstvak. De vervolg keuzelijst bevat waarden van het kenmerk dat zich in de *huidige* Zoek resultaten bevindt. Naast elke waarde in de lijst is het aantal assets in de huidige Zoek resultaten met die waarde. Als u een waarde in de lijst selecteert, worden de zoek resultaten beperkt tot assets die die waarde hebben. U kunt slechts één waarde selecteren.

De huidige Zoek resultaten die worden gebruikt voor het maken van de vervolg keuzelijst, worden bepaald door:

- De zoek termen die zijn ingevoerd in de **Zoek catalogus**. 
- De waarden die zijn geselecteerd in de snelle filters.

Hier volgt een voor beeld van het snelle filter ' Asset type '.

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="Het voor beeld van een snel filter voor het activa type." border="true":::

U kunt tekst in het tekstvak invoeren om de waarden in de vervolg keuzelijst te beperken tot waarden die overeenkomen met of gedeeltelijk overeenkomen met de tekst. Voor voor beelden van het gebruik van het tekstvak, Zie [Snelzoeken filter: filteren op activa type](#search-quick-filter-filter-by-asset-type)en [Zoek snel filter: filteren op classificatie](#search-quick-filter-filter-by-classification).

#### <a name="search-quick-filter-filter-by-asset-type"></a>Snel filter voor zoeken: filteren op activa type

Als u wilt filteren op activa type, gebruikt u het filter voor het snel filteren van **activa** . In de vervolg keuzelijst worden de activa typen weer gegeven die in de huidige Zoek resultaten zijn gevonden, zoals wordt bepaald door de zoek termen en de snelle filters. Voor elk type wordt het aantal assets van dat type weer gegeven.

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="De snelle filters voor het type activum worden gemarkeerd. Hierin worden de activa typen en het aantal voor elk weer gegeven." border="true":::

Selecteer een activum type om de zoek resultaten te beperken tot activa van dat type. U kunt slechts één type selecteren.

Als u alleen de activa typen wilt weer geven waarvan de namen overeenkomen met een teken reeks, voert u de teken reeks in het tekstvak in. Als u bijvoorbeeld alleen activa typen met **SQL** in hun naam wilt weer geven, voert u **SQL** in.

:::image type="content" source="./media/how-to-search-catalog/filter-asset-types.png" alt-text="Het deel venster snelle filters bevat SQL voor ' Asset type '. In de lijst met assets die SQL bevat, worden drie vermeldingen weer gegeven." border="true":::

Selecteer een activum type om de zoek resultaten te beperken tot activa van dat type. U kunt slechts één type selecteren.

#### <a name="search-quick-filter-filter-by-classification"></a>Snel filter voor zoeken: filteren op classificatie

Als u wilt filteren op activa classificatie, gebruikt u het snelle filter **classificatie** . De vervolg keuzelijst bevat de classificaties die zijn toegewezen aan een of meer assets in de huidige Zoek resultaten, zoals wordt bepaald door de zoek termen en de snelle filters. Voor elke classificatie wordt het aantal assets weer gegeven dat is toegewezen aan deze classificatie.

:::image type="content" source="./media/how-to-search-catalog/classification-quick-filter.png" alt-text="De snelle filters voor classificatie zijn gemarkeerd." border="true":::

Selecteer een classificatie om de zoek resultaten te beperken tot activa die aan deze classificatie zijn toegewezen. U kunt slechts één classificatie selecteren.

Als u alleen classificaties wilt weer geven waarvan de namen overeenkomen met een teken reeks, voert u de teken reeks in het tekstvak in. Als u bijvoorbeeld alleen classificaties met een **nummer** in hun namen wilt weer geven, voert u **getal** in.

:::image type="content" source="./media/how-to-search-catalog/filter-classifications.png" alt-text="In het deel venster snelle filters is classificatie ' Bank ' en de classificaties die allemaal die waarde bevatten." border="true":::

Selecteer een classificatie om de zoek resultaten te beperken tot activa waaraan deze classificatie is toegewezen. U kunt niet meer dan één classificatie selecteren.

#### <a name="search-quick-filter-filter-by-contacts"></a>Snel filter voor zoeken: filteren op contact personen

Een *contact* persoon is een persoon die is toegewezen aan een activum als eigenaar of deskundige. Wanneer u activa details bekijkt, worden contact personen weer gegeven op het tabblad **contact personen** .

Er zijn twee manieren om te zoeken naar activa waaraan een bepaalde contact persoon is toegewezen.

- Voer de volledige of gedeeltelijke naam van de contact persoon in de **Zoek catalogus** in en voer een zoek opdracht uit. De zoek resultaten bevatten activa met contact personen waarvan de namen overeenkomen met uw zoek termen.
- Selecteer de contact persoon van belang in het snelle filter voor **contact personen** en voer een zoek opdracht uit.

:::image type="content" source="./media/how-to-search-catalog/contact-quick-filter.png" alt-text="De waarde van persoon in het deel venster snelle filters is ' Darren '. Het deel venster met suggesties bevat drie suggesties." border="true":::

## <a name="search-example"></a>Zoek voorbeeld

Laten we eens kijken naar een hypothetisch voor beeld om te zien hoe de zoek termen en snelle filters communiceren om de zoek resultaten te bepalen. In het bijzonder volgen we het aantal van het activa type **Azure-Blob Storage**.

- De eerste zoek opdracht zonder zoek termen is opgegeven en er zijn geen waarden geselecteerd in de snelle filters. De zoek opdracht vindt alle assets in de catalogus. De lijst met zoek resultaten en het snelle filter voor het **type activum** geven het volgende weer:

    - De lijst met zoek resultaten bevat 164.230 assets. Dit zijn alle assets in de catalogus.
    - De vervolg keuzelijst **type activum** bevat 43 vermeldingen. Dit zijn alle typen assets in de catalogus. Omdat elk activum van één en slechts één type is, is de som van de aantallen van de 43-activa typen 164.230.
    - Het type **Azure Blob Storage** -activum is het eerste item in de vervolg keuzelijst van het snelle filter van het **type activum** . De waarden worden geordend op basis van aantal, grootste eerst, zodat **Azure Blob Storage** het meest voorkomende type activum is. Het aantal is 118.174.

- We voeren nu **Parquet** in in **Zoek catalogus** en voeren een andere zoek opdracht uit. De zoek resultaten bevatten alleen assets met kenmerken die overeenkomen met **Parquet**. Dit vermindert het aantal aantallen, als volgt:

    - De lijst met zoek resultaten bevat 493 assets. Slechts 493 van de 164.230-assets in de catalogus hebben kenmerken die overeenkomen met ' Parquet '.
    - De vervolg keuzelijst **type activum** bevat 15 vermeldingen. Elk van de 493-assets is van een van deze 15 typen en de som van de aantallen van de 15 typen is 493.
    - Er zijn 456 assets van het type **Azure Blob Storage**. De andere 37 (493 min 456) activa zijn van een van de andere 14 typen.

- We kijken nu naar de vervolg keuzelijst van een ander snel filter, **classificatie**:

    - Er zijn 12 classificaties voor de 493-assets in de lijst met zoek resultaten. De aantallen voor de 493-assets zijn niet van toepassing op 493, omdat een wille keurig aantal classificaties kan worden toegewezen aan een Asset.
    - De **naam classificatie van de persoon** is toegewezen aan 36 activa, meer dan enige andere classificatie.

- We selecteren de **naam classificatie van de persoon** . De lijst met zoek resultaten valt 36 assets, zoals verwacht, omdat het aantal van de **persoons naam** 36 is. Geen van deze resultaten is van het type **Azure Blob Storage**.

We kunnen concluderen dat er geen Asset is met het type **Azure Blob Storage** dat overeenkomt met **Parquet** en dat een classificatie van **persoons naam** heeft.

## <a name="start-the-search"></a>De zoek opdracht starten

Wanneer u zoekt, worden de zoek termen die u in de **Zoek catalogus** invoert, vergeleken met de eigenschappen van de activa. Deze kenmerken bevatten naam, type, classificatie en contact personen. De activa met overeenkomende kenmerken worden weer gegeven in de lijst met zoek resultaten, tenzij wordt uitgesloten door een snel filter.

Nadat u de zoek termen hebt ingevoerd en de snelle filters hebt ingesteld, start u de zoek opdracht op een van de volgende manieren:

- Als u wilt zoeken op basis van de voor waarden die u hebt ingevoerd, selecteert u het zoek pictogram ( :::image type="icon" source="./media/how-to-search-catalog/search-icon.png"::: ), drukt u op **Enter** of selecteert u **Zoek resultaten weer geven**.
- Als u met voor waarden uit een vorige Zoek opdracht wilt zoeken, selecteert u deze in **uw recente Zoek opdrachten**.
- Als u wilt zoeken met voorgestelde voor waarden, selecteert u deze in **Zoek suggesties**.

Selecteer een activum uit **activa suggesties** om rechtstreeks naar de detail pagina voor de Asset te gaan. Er is geen zoek opdracht uitgevoerd.

De lijst met resultaten voor suggesties en zoek opdrachten voor gebruikers kan enigszins verschillen. De resultaten in de lijst met suggesties zijn gebaseerd op fuzzy overeenkomsten, terwijl door de gebruiker geïnitieerde Zoek resultaten worden gebaseerd op exacte overeenkomsten.

Wanneer u zoekt, wordt de pagina **Zoek resultaten** weer gegeven en worden de assets vermeld die zijn gevonden door de zoek opdracht.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Scherm afbeelding met de zoek resultaten voor een zoek waarde van contoso.":::

Als u activa details wilt bekijken, selecteert u de naam van een Asset.

Gebruik de besturings elementen aan de onderkant van de pagina met zoek resultaten om naar andere pagina's met zoek resultaten te navigeren.

:::image type="content" source="./media/how-to-search-catalog/page-navigation.png" alt-text="Scherm afbeelding die laat zien hoe u door de pagina's met zoek resultaten navigeert.":::

### <a name="sort-search-results"></a>Zoek resultaten sorteren

Gebruik **sorteren op** om de zoek resultaten te sorteren op **relevantie** of **naam**.

:::image type="content" source="./media/how-to-search-catalog/sort-by.png" alt-text="Scherm afbeelding die laat zien hoe de zoek resultaten worden gesorteerd. Voor dit voor beeld is de lijst sorteren op vervolg keuzelijst ingesteld op relevantie.":::

### <a name="search-results-dynamic-filters"></a>Zoek resultaten dynamische filters

Het **filter** deel venster op de pagina **Zoek resultaten** bevat filters die dynamische filters bieden voor de assets in de lijst met zoek resultaten. Het filteren is dynamisch, omdat er extra filters kunnen worden weer gegeven op basis van filters electies.

De dynamische filters hebben een selectie vakje voor elke waarde in de vervolg keuzelijst. Gebruik deze selectie vakjes om te filteren op zoveel waarden als u wilt.

#### <a name="search-results-dynamic-filter-filter-by-asset-type"></a>Zoek resultaten dynamisch filter: filteren op activa type

Als u een activa type selecteert in de vervolg keuzelijst **Asset type** , worden er dynamische filters weer gegeven die u extra manieren bieden om uw zoek resultaten te verfijnen. De filters variëren afhankelijk van het geselecteerde activum type. Als u bijvoorbeeld **Azure SQL database** selecteert, worden er dynamische filters weer gegeven voor de server, de data base en het schema. De waarden in deze filters zijn afkomstig uit de assets in de zoek resultaten van het geselecteerde type.

:::image type="content" source="./media/how-to-search-catalog/asset-type-dynamic-filter.png" alt-text="Het Azure SQL Database-filter item is het enige item van het type activum dat is geselecteerd. Er wordt ook een Zoek resultaat van het type activum gemarkeerd." border="true":::

#### <a name="search-results-dynamic-filter-filter-by-classification"></a>Zoek resultaten dynamisch filter: filteren op classificatie

Elke classificatie in de **classificatie** lijst is van toepassing op ten minste één item in de lijst met zoek resultaten. Selecteer een of meer classificaties om uw zoek resultaten te beperken tot assets van de geselecteerde classificaties.

:::image type="content" source="./media/how-to-search-catalog/classification-dynamic-filter.png" alt-text="Het classificatie filter van de zoek resultaten is gemarkeerd." border="true":::

#### <a name="edit-and-delete-search-results-filters"></a>Filters voor zoek resultaten bewerken en verwijderen

Als u een filter wilt verwijderen, schakelt u het selectie vakje naast de naam van het filter uit.

### <a name="recently-accessed-assets"></a>Recent geopende assets

De sectie **onlangs geopend** in het uitgebreide zoekvak bevat de meest recent gebruikte assets, indien van toepassing.

- Selecteer **alles weer geven** in de sectie **onlangs geopend** om de volledige lijst met recent geopende assets te bekijken.

   :::image type="content" source="./media/how-to-search-catalog/get-to-recent-view.png" alt-text="Scherm opname van de sectie onlangs geopend in het uitgebreide zoek venster.":::

   Er wordt een lijst met onlangs geopende assets weer gegeven.

   :::image type="content" source="./media/how-to-search-catalog/recent.png" alt-text="Scherm afbeelding met een lijst met recent gebruikte assets.":::

- Als u wilt filteren op naam, voert u een zoek reeks in bij **filteren op naam**.

- Als u items uit de lijst wilt verwijderen, selecteert u deze met hun selectie vakjes en selecteert u vervolgens **verwijderen**.

   :::image type="content" source="./media/how-to-search-catalog/remove-from-recent-view.png" alt-text="Scherm afbeelding die laat zien hoe u items uit een lijst met onlangs geopende assets kunt verwijderen.":::

- Als u de hele lijst wilt wissen, selecteert u **wissen**.

   :::image type="content" source="./media/how-to-search-catalog/clear-recent-view-selections.png" alt-text="Scherm opname van het wissen van een lijst met onlangs geopende assets":::

### <a name="search-assets"></a>Assets zoeken

Veel pagina's, met uitzonde ring van **Home** , hebben bovenaan het vak **Zoek elementen** . Hier ziet u bijvoorbeeld de pagina Asset Details, met **Zoek elementen** gemarkeerd.

:::image type="content" source="./media/how-to-search-catalog/search-assets.png" alt-text="Scherm opname van een pagina met activa Details waarvoor Zoek elementen zijn gemarkeerd":::

Selecteer **Zoek assets** om een zoekvak te starten, zoals het vak dat u uit de **Zoek catalogus** op het **huis** haalt, met dezelfde mogelijkheden.

:::image type="content" source="./media/how-to-search-catalog/search-assets-dialog.png" alt-text="Scherm opname van het vak uitgebreide zoek assets.":::

## <a name="next-steps"></a>Volgende stappen

- [Termen van een woorden lijst maken, importeren en exporteren](how-to-create-import-export-glossary.md)
- [Term sjablonen voor de woorden lijst voor bedrijven beheren](how-to-manage-term-templates.md)
