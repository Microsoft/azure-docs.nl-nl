---
title: 'Zelfstudie: Query uitvoeren vanuit Power Apps'
titleSuffix: Azure Cognitive Search
description: Stapsgewijze richtlijnen over het bouwen van een Power App die verbinding maakt met een Azure Cognitive Search-index, query's verzendt en resultaten weergeeft.
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: tutorial
ms.date: 11/17/2020
ms.openlocfilehash: e8c16f02cf6b77fa54d2a19abac48e9914aa99bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008562"
---
# <a name="tutorial-query-a-cognitive-search-index-from-power-apps"></a>Zelfstudie: Een query uitvoeren op een Cognitive Search-index via Power Apps

Gebruik de Rapid Application Development-omgeving van Power Apps om een aangepaste app te maken voor uw doorzoekbare inhoud in Azure Cognitive Search.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Verbinding maken met Azure Cognitive Search
> * Een queryaanvraag instellen
> * Resultaten visualiseren in een canvas-app

Als u geen abonnement op Azure hebt, opent u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* [Power Apps-account](https://make.powerapps.com)

* [Hotels-sample-index](search-get-started-portal.md) gehost in uw zoekservice

* [API-sleutel voor query's](search-security-api-keys.md#find-existing-keys)

## <a name="1---create-a-custom-connector"></a>1\. Een aangepaste connector maken

Een connector in Power Apps is een gegevensbronverbinding. In deze stap maakt u een aangepaste connector om verbinding te maken met een zoekindex in de cloud.

1. Meld u aan bij [Power Apps](https://make.powerapps.com).

1. Vouw aan de linkerkant **Gegevens** > **Aangepaste connectors** uit.
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="Het menu Aangepaste connector" border="true":::

1. Selecteer **+ Nieuwe aangepaste connector** en selecteer vervolgens **Leeg item maken**.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="Maken op basis van een leeg menu" border="true":::

1. Geef uw aangepaste connector een naam (bijvoorbeeld *AzureSearchQuery*) en klik vervolgens op **Doorgaan**.

1. Voer op de pagina Algemeen informatie in:

   * Achtergrondkleur van pictogram (bijvoorbeeld #007ee5)
   * Beschrijving (bijvoorbeeld 'Een connector voor Azure Cognitive Search')
   * Bij Host moet u de zoekservice-URL opgeven (zoals `<yourservicename>.search.windows.net`)
   * Als Basis-URL voert u simpelweg '/' in

    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="Het dialoogvenster Algemene informatie" border="true":::

1. Stel op de pagina Beveiliging *API-sleutel* als het **Verificatietype** en stel zowel het parameterlabel als de parameternaam in op *api-key*. Als **Parameterlocatie** selecteert u *Header* (zie onderstaande afbeelding).

    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="Verificatietype-optie" border="true":::

1. Op de pagina Definities selecteert u **+ Nieuwe actie** om een actie te maken waarmee een query op de index wordt uitgevoerd. Voer de waarde Query in voor de samenvatting en de naam van de bewerkings-id. Voer een beschrijving in, zoals *Voert een query op de zoekindex uit*.

    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="Nieuwe actieopties" border="true":::

1. Schuif omlaag. Bij Aanvragen selecteert u de knop **+ Importeren uit voorbeeld** om een queryaanvraag op uw zoekservice te configureren:

   * Selecteer de bewerking `GET`

   * Als URL voert u een voorbeeldquery in voor uw zoekindex (met `search=*` worden alle documenten geretourneerd, met `$select=` kunt u velden kiezen). De API-versie is verplicht. Een volledig opgegeven URL ziet er als volgt uit: `https://mydemo.search.windows.net/indexes/hotels-sample-index/docs?search=*&$select=HotelName,Description,Address/City&api-version=2020-06-30`

   * Als Headers typt u `Content-Type`. In een latere stap stelt u de waarde in op `application/json`.

     **Power Apps** maakt gebruik van de syntaxis in de URL om parameters te extraheren uit de query. Parameters voor zoeken, selecteren en API-versies worden configureerbaar tijdens uw voortgang door de wizard.

       :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="Importeren vanuit voorbeeld" border="true":::

1. Klik op **Importeren** om de aanvraag automatisch in te vullen. Voltooi het instellen van de parametermetagegevens door op het symbool **...** naast elke parameter te klikken. Klik op **Terug** om na elke parameterupdate terug te keren naar de pagina Aanvraag.

   :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="Het dialoogvenster Importeren uit voorbeeld" border="true":::

1. Voor *zoeken*: Stel `*` in als de **standaardwaarde**, stel **vereist** in op *False* en stel **zichtbaarheid** in op *geen*. 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="Metagegevens van zoekparameter" border="true":::

1. Voor *selecteren*: Stel `HotelName,Description,Address/City` in als de **standaardwaarde**, stel **vereist** in op *False* en stel **zichtbaarheid** in op *geen*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-4-parameter-metadata-select.png" alt-text="Metagegevens van selectieparameter" border="true":::

1. Voor *api-version*: Stel `2020-06-30` in als de **standaardwaarde**, stel **vereist** in op *True* en stel **zichtbaarheid** in op *intern*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="Metagegevens van de versieparameter" border="true":::

1. Voor *Content-Type*: Ingesteld op `application/json`.

1. Nadat u deze wijzigingen hebt aangebracht, schakelt u over naar de **Swagger Editor**-weergave. In de sectie Parameters ziet u, als het goed is, de volgende configuratie:

    ```JSON
    parameters:
      - {name: search, in: query, required: false, type: string, default: '*'}
      - {name: $select, in: query, required: false, type: string, default: 'HotelName,Description,Address/City'}
      - {name: api-version, in: query, required: true, type: string, default: '2020-06-30',
        x-ms-visibility: internal}
      - {name: Content-Type, in: header, required: false, type: string}
    ```

1. Schakel terug naar de wizard en ga terug naar stap **3. Aanvragen**. Schuif omlaag naar de sectie Antwoord. Klik op **Standaardantwoord toevoegen**. Dit is essentieel omdat Power Apps hiermee het schema van het antwoord kan leren kennen. 

1. Plak een voorbeeldantwoord. U kunt gemakkelijk een voorbeeldantwoord vastleggen via de Search Explorer in Azure Portal. Voer in Search Explorer dezelfde query in als u voor de aanvraag hebt gedaan, maar voeg **$top=2** toe om het aantal resultaten tot slechts twee documenten te beperken: : `search=*&$select=HotelName,Description,Address/City&$top=2`. 

   Power Apps heeft maar een paar resultaten nodig om een schema te kunnen detecteren. U kunt het volgende voorbeeld nu kopiëren in de wizard, ervan uitgaande dat u de hotels-sample-index gebruikt.

    ```JSON
    {
        "@odata.context": "https://mydemo.search.windows.net/indexes('hotels-sample-index')/$metadata#docs(*)",
        "value": [
            {
                "@search.score": 1,
                "HotelName": "Arcadia Resort & Restaurant",
                "Description": "The largest year-round resort in the area offering more of everything for your vacation – at the best value!  What can you enjoy while at the resort, aside from the mile-long sandy beaches of the lake? Check out our activities sure to excite both young and young-at-heart guests. We have it all, including being named “Property of the Year” and a “Top Ten Resort” by top publications.",
                "Address": {
                    "City": "Seattle"
                }
            },
            {
                "@search.score": 1,
                "HotelName": "Travel Resort",
                "Description": "The Best Gaming Resort in the area.  With elegant rooms & suites, pool, cabanas, spa, brewery & world-class gaming.  This is the best place to play, stay & dine.",
                "Address": {
                    "City": "Albuquerque"
                }
            }
        ]
    }
    ```

    > [!TIP] 
    > Het aantal tekens dat u voor het JSON-antwoord kunt invoeren is beperkt, dus wellicht is het handig om de JSON te vereenvoudigen voordat u deze plakt. Het schema en de indeling van het antwoord is belangrijker dan de waarden zelf. Het veld Beschrijving kan bijvoorbeeld worden vereenvoudigd tot alleen de eerste zin.

1. Klik op **Importeren** om het standaardantwoord toe te voegen.

1. Klik rechtsboven op **Connector maken** om de definitie op te slaan.

1. Klik op **Sluiten** om de connector te sluiten.

## <a name="2---test-the-connection"></a>2\. De verbinding testen

Wanneer de connector voor het eerst wordt gemaakt, moet u deze opnieuw openen vanuit de lijst met aangepaste connectors om de nieuwe connector te testen. Als u later aanvullende updates invoert, kunt u de connector vanuit de wizard testen.

Voor deze taak hebt u een [API-sleutel voor query's](search-security-api-keys.md#find-existing-keys) nodig. Steeds wanneer er een verbinding wordt gemaakt, of dit nu voor een testuitvoering is of een verbinding die in een app wordt opgenomen, heeft de connector de API-sleutel voor query's nodig die is gebruikt om verbinding te maken met Azure Cognitive Search.

1. Klik uiterst links op **Aangepaste connectors**.

1. Zoek de connector in de lijst (in deze zelfstudie is dit AzureSearchQuery).

1. Selecteer de connector, vouw de lijst met acties uit en selecteer **Eigenschappen weergeven**.

    :::image type="content" source="./media/search-howto-powerapps/1-11-1-test-connector.png" alt-text="Eigenschappen weergeven" border="true":::

1. Selecteer rechtsboven de optie **Bewerken**.

1. Selecteer **4. Testen** om de testpagina te openen.

1. Klik bij Testbewerking op **+ Nieuwe verbinding**.

1. Voer een API-sleutel voor query's in. Dit is een Azure Cognitive Search-query voor alleen-lezen toegang tot een index. U [vindt de sleutel](search-security-api-keys.md#find-existing-keys) in Azure Portal. 

1. Klik bij Bewerkingen op de knop **Testbewerking**. Als dit is gelukt, ziet u de status 200, en in de hoofdtekst van het antwoord ziet u als het goed is de JSON waarmee de zoekresultaten worden beschreven.

    :::image type="content" source="./media/search-howto-powerapps/1-11-2-test-connector.png" alt-text="JSON-antwoord" border="true":::

## <a name="3---visualize-results"></a>3\. Resultaten visualiseren

In deze stap maakt u een Power App met een zoekvak, een zoekknop en een weergavegebied voor de resultaten. De Power App maakt verbinding met de onlangs gemaakt aangepaste connector om de gegevens van Azure Search op te halen.

1. Vouw aan de linkerkant **Apps** >  **+ Nieuwe app** > **Canvas** uit.

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="Een canvas-app maken" border="true":::

1. Selecteer het type toepassing. Maak voor deze zelfstudie een **Lege app** met de **Telefoonindeling**. De **Power Apps Studio** wordt weergegeven.

1. Zodra u zich in de studio bevindt, selecteert u het tabblad **Gegevensbronnen** en klikt u op de nieuwe connector die u zojuist hebt gemaakt. In ons geval is dit *AzureSearchQuery*. Klik op **Een verbinding toevoegen**.

   Voer de API-sleutel voor query's in.

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="connector verbinden" border="true":::

    Nu is *AzureSearchQuery* een gegevensbron die beschikbaar is voor gebruik vanuit uw toepassing.

1. Voeg op het tabblad **Invoegen** een aantal besturingselementen toe aan het canvas.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="Besturingselementen invoegen" border="true":::

1. Voeg de volgende elementen in:

   * Een tekstlabel met de waarde Query:
   * Een tekstinvoerelement (noem dit *txtQuery*, standaardwaarde: *)
   * Een knop met de tekst Zoeken 
   * Een verticale galerie met de naam *galleryResults*

    Het canvas ziet er ongeveer als volgt uit:

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="Indeling van besturingselementen" border="true":::

1. Als u ervoor wilt zorgen dat een query wordt gestart via de **zoekknop**, plakt u de volgende actie in **OnSelect**:

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Query({search: txtQuery.Text}).value))
    ```

   In de volgende schermopname ziet u de formulebalk voor de actie **OnSelect**.

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="De knop OnSelect" border="true":::

   Met deze actie wordt een nieuwe verzameling met de naam *azResult* bijgewerkt met het resultaat van de zoekquery als er op de knop wordt gedrukt, met behulp van de tekst in het tekstvak *txtQuery* als de queryterm.

   > [!NOTE]
   > Probeer dit als u de volgende formulesyntaxisfout krijgt: 'De functie ClearCollect bevat een aantal ongeldige functies':
   > 
   > * Controleer eerst of de verwijzing naar de connector juist is. Wis de connectornaam en typ de naam van uw connector. Intellisense stelt de juiste connector en bewerking voor.
   > 
   > * Verwijder de connector en maak deze opnieuw als de fout zich blijft voordoen. Als er meerdere exemplaren van een connector zijn, gebruikt de app mogelijk het verkeerde exemplaar.
   > 

1. Koppel het besturingselement Verticale galerie aan de *azResult*-verzameling die is gemaakt bij het voltooien van de vorige stap. 

   Selecteer het galeriebesturingselement en voer de volgende acties uit in het deelvenster Eigenschappen.

   * Stel **DataSource** in op *azResult*.
   * Selecteer een **Indeling** die voor u werkt op basis van het gegevenstype in uw index. In dit geval hebben we de indeling *Titel, ondertitel en hoofdtekst* gebruikt.
   * **Bewerk velden** en selecteer de velden die u wilt visualiseren.

    Omdat we een voorbeeldresultaat hebben gegeven toen we de connector hebben gedefinieerd, is de app zich bewust van de velden die in uw index beschikbaar zijn.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="Galerievelden" border="true":::   
 
1. Druk op **F5** om een voorbeeld van de app weer te geven.  

    :::image type="content" source="./media/search-howto-powerapps/2-8-3-final.png" alt-text="Uiteindelijke app" border="true":::    

<!--     Remember that the fields can be set to calculated values.

    For the example, setting using the *"Image, Title and Subtitle"* layout and specifying the *Image* function as the concatenation of the root path for the data and the file name (for instance, `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`) will produce the result below.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="Final app" border="true":::         -->

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling **Alle resources** of **Resourcegroepen** in het navigatiedeelvenster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, indexeerfuncties en gegevensbronnen. U kunt afzonderlijke items in de portal verwijderen om onder de limiet te blijven.

## <a name="next-steps"></a>Volgende stappen

Met Power Apps is snelle toepassingsontwikkeling van aangepaste apps mogelijk. Nu u weet hoe u een verbinding met een zoekindex maakt, kunt u meer leren over het maken van een uitgebreide visualisatie-ervaring in een aangepaste Power App.

> [!div class="nextstepaction"]
> [Studiecatalogus voor Power Apps](/powerapps/learning-catalog/get-started)