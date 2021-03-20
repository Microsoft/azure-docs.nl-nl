---
title: Power BI zelf studie voor Azure Cosmos DB-connector
description: Gebruik deze Power BI zelf studie voor het importeren van JSON, het maken van imgezichts rapporten en het visualiseren van gegevens met behulp van de Azure Cosmos DB en Power BI connector.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: b27bab9ea3029264143caaacf094f0a799894356
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359860"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Azure Cosmos DB-gegevens visualiseren met behulp van de Power BI-connector 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

[Power bi](https://powerbi.microsoft.com/) is een online service waar u Dash boards en rapporten kunt maken en delen. Power BI Desktop is een bewerkingstool voor rapporten, waarmee u gegevens uit verschillende gegevensbronnen kunt ophalen. Azure Cosmos DB is een van de gegevensbronnen die u kunt gebruiken met Power BI Desktop. U kunt Power BI Desktop met het Azure Cosmos DB-account verbinden door middel van de Azure Cosmos DB Power BI-connector.  Nadat u de Azure Cosmos DB-gegevens naar Power BI hebt geïmporteerd, kun u deze transformeren, rapporten maken en de rapporten publiceren naar Power BI.   

Dit artikel beschrijft de stappen die nodig zijn om een Azure Cosmos DB-account te verbinden met Power BI Desktop. Nadat u verbinding hebt gemaakt, extraheert u de gegevens, zet u de JSON-gegevens om in tabelvorm en publiceert u een rapport naar Power BI.

> [!NOTE]
> De Power BI connector voor Azure Cosmos DB maakt verbinding met Power BI Desktop. Rapporten die zijn gemaakt in Power BI Desktop kunnen worden gepubliceerd op PowerBI.com. Directe extractie van Azure Cosmos DB gegevens kan niet worden uitgevoerd vanuit PowerBI.com. 

> [!NOTE]
> Verbinding maken met Azure Cosmos DB met de Power BI-connector wordt momenteel alleen ondersteund voor Azure Cosmos DB SQL API-en Gremlin-API-accounts.

## <a name="prerequisites"></a>Vereisten
Voordat u de instructies in deze Power BI zelf studie volgt, moet u ervoor zorgen dat u toegang hebt tot de volgende bronnen:

* [Down load de nieuwste versie van Power bi Desktop](https://powerbi.microsoft.com/desktop).

* Down load de [voorbeeld gegevens van Volcano](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/main/SampleData/VolcanoData.json) van github.

* [Maak een Azure Cosmos-database account](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) en importeer de Volcano-gegevens met behulp van het [hulp programma voor gegevens migratie Azure Cosmos DB](import-data.md). Bij het importeren van gegevens moet u rekening houden met de volgende instellingen voor de bron en de doelen in het hulp programma voor gegevens migratie:

   * **Bron parameters** 

       * **Importeren uit:** JSON-bestand (en)

   * **Doel parameters** 

      * **Verbindings reeks:**`AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **Partitie sleutel:** /Country 

      * **Door Voer van verzameling:** 1000 

Als u uw rapporten wilt delen in PowerBI.com, moet u een account hebben in PowerBI.com.  Zie voor meer informatie over Power BI en Power BI Pro [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing) .

## <a name="lets-get-started"></a>Laten we aan de slag
In deze zelf studie stel u voor dat u een geologiste Volcanoes over de hele wereld bent. De Volcano-gegevens worden opgeslagen in een Azure Cosmos DB-account en de JSON-document indeling is als volgt:

```json
{
    "Volcano Name": "Rainier",
        "Country": "United States",
        "Region": "US-Washington",
        "Location": {
          "type": "Point",
          "coordinates": [
            -121.758,
            46.87
          ]
        },
        "Elevation": 4392,
        "Type": "Stratovolcano",
        "Status": "Dendrochronology",
        "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

U haalt de Volcano-gegevens op uit het Azure Cosmos DB-account en visualiseert gegevens in een interactief Power BI rapport.

1. Voer Power BI Desktop uit.

2. U kunt **gegevens ophalen**, **recente bronnen** weer geven of **andere rapporten** rechtstreeks vanuit het welkomst scherm openen. Selecteer de ' X ' in de rechter bovenhoek om het scherm te sluiten. De **rapport** weergave van Power bi Desktop wordt weer gegeven.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview.png" alt-text="Power BI Desktop rapport weergave-Power BI-connector":::

3. Selecteer het lint **Start** en klik vervolgens op **gegevens ophalen**.  Het venster **gegevens ophalen** wordt weer gegeven.

4. Klik op **Azure**, selecteer **Azure Cosmos DB (bèta)** en klik vervolgens op **verbinding maken**. 

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbigetdata.png" alt-text="Power BI Desktop gegevens ophalen-Power BI-connector":::

5. Klik op de pagina **voor beeld** van de connector op **door gaan**. Het venster **Azure Cosmos DB** wordt weer gegeven.

6. Geef de eind punt-URL op van het Azure Cosmos DB-account waaruit u de gegevens wilt ophalen, zoals hieronder wordt weer gegeven, en klik vervolgens op **OK**. Als u uw eigen account wilt gebruiken, kunt u de URL ophalen uit het vak URI op de Blade **sleutels** van de Azure Portal. U kunt desgewenst de naam van de data base opgeven, de naam van de verzameling of de Navigator gebruiken om de data base en verzameling te selecteren om te bepalen waar de gegevens vandaan komen.
   
7. Als u voor de eerste keer verbinding maakt met dit eind punt, wordt u gevraagd om de account sleutel op te vragen. Voor uw eigen account haalt u de sleutel op uit het vak **primaire sleutel** op de Blade met **alleen-lezen sleutels** van de Azure Portal. Voer de juiste sleutel in en klik vervolgens op **verbinding maken**.
   
   U wordt aangeraden de alleen-lezen sleutel te gebruiken bij het maken van rapporten. Dit voor komt onnodige bloot stelling van de primaire sleutel tot mogelijke beveiligings Risico's. De alleen-lezen sleutel is beschikbaar op de Blade **sleutels** van de Azure Portal. 
    
8. Wanneer de verbinding met het account is gemaakt, wordt het deel venster **Navigator** weer gegeven. In de **Navigator** ziet u een lijst met data bases onder het account.

9. Klik op de data base waaruit de gegevens voor het rapport afkomstig zijn en vouw deze uit. Selecteer **volcanodb** (de naam van uw data base kan anders zijn).   

10. Selecteer nu een verzameling die de gegevens bevat die moeten worden opgehaald, selecteer **volcano1** (de naam van uw verzameling kan anders zijn).
    
    In het voorbeeld venster ziet u een lijst met **record** items.  Een document wordt weer gegeven als een **record** type in Power bi. Op dezelfde manier is een genest JSON-blok in een document ook een **record**.
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbinavigator.png" alt-text="Power BI zelf studie voor Azure Cosmos DB Power BI connector-Navigator venster":::

12. Klik op **bewerken** om de query-editor in een nieuw venster te openen om de gegevens te transformeren.

## <a name="flattening-and-transforming-json-documents"></a>Het afvlakken en transformeren van JSON-documenten
1. Schakel over naar het venster Power BI query-editor, waarbij de kolom **document** in het middelste deel venster wordt.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png" alt-text="Query-editor in Power BI Desktop":::

1. Klik op de uitbreidings module aan de rechter kant van de kolomkop **document** .  Het context menu met een lijst met velden wordt weer gegeven.  Selecteer de velden die u nodig hebt voor het rapport, bijvoorbeeld Volcano naam, land, regio, locatie, uitbrei ding, type, status en eind afbreken. Schakel het selectie vakje **oorspronkelijke kolom naam gebruiken als voor voegsel** uit en klik vervolgens op **OK**.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png" alt-text="Power BI zelf studie voor Azure Cosmos DB Power BI connector-documenten uitbreiden":::

1. In het middelste deel venster wordt een voor beeld weer gegeven van het resultaat met de geselecteerde velden.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png" alt-text="Power BI zelf studie voor Azure Cosmos DB Power BI connector-afvlakking van resultaten":::

1. In ons voor beeld is de locatie-eigenschap een geojson-blok in een document.  Zoals u kunt zien, wordt de locatie weer gegeven als een **record** type in Power bi Desktop.  

1. Klik op de uitbreidings module aan de rechter kant van de kolomkop document. locatie.  Het context menu met de velden type en coördinaten wordt weer gegeven.  Laten we het veld coördinaten selecteren, zorg ervoor dat de **oorspronkelijke kolom naam gebruiken als voor voegsel** niet is geselecteerd en klik op **OK**.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png" alt-text="Power BI zelf studie voor Azure Cosmos DB Power BI connector-locatie record":::

1. In het middelste deel venster wordt nu een kolom coördinaten van het **lijst** type weer gegeven.  Zoals aan het begin van de zelf studie wordt weer gegeven, is de geojson-gegevens in deze zelf studie van punt type met de breedte-en lengte waarden die zijn vastgelegd in de coördinaten matrix.
   
   Het element coördinaten [0] vertegenwoordigt een lengte graad terwijl coördinaten [1] de breedte graad aangeeft.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png" alt-text="Power BI zelf studie voor Azure Cosmos DB Power BI connector-coördinaten lijst":::

1. Als u de coördinaten matrix wilt samen voegen, maakt u een **aangepaste kolom** met de naam LatLong.  Selecteer het lint **kolom toevoegen** en klik op **aangepaste kolom**.  Het venster **aangepaste kolom** wordt weer gegeven.

1. Geef een naam op voor de nieuwe kolom, bijvoorbeeld LatLong.

1. Geef vervolgens de aangepaste formule voor de nieuwe kolom op.  In ons voor beeld voegen we de breedte-en lengte waarden, gescheiden door een komma, toe, zoals hieronder wordt weer gegeven, met behulp van de volgende formule: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})` . Klik op **OK**.
   
   Raadpleeg voor meer informatie over Dax (Data Analysis expressions), waaronder DAX-functies, de [basis beginselen van Dax in Power bi Desktop](/power-bi/desktop-quickstart-learn-dax-basics).
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png" alt-text="Power BI zelf studie voor Azure Cosmos DB Power BI-connector-aangepaste kolom toevoegen":::

1. In het middelste deel venster ziet u nu de nieuwe LatLong kolommen gevuld met de waarden.
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png" alt-text="Power BI zelf studie voor Azure Cosmos DB Power BI connector-kolom aangepaste LatLong":::
    
    Als er een fout in de nieuwe kolom wordt weer gegeven, moet u ervoor zorgen dat de toegepaste stappen onder query-instellingen overeenkomen met de volgende afbeelding:
    
    :::image type="content" source="./media/powerbi-visualize/power-bi-applied-steps.png" alt-text="Toegepaste stappen moeten bron, navigatie, uitgevouwen document, uitgevouwen document. locatie, aangepast zijn toegevoegd":::
    
    Als uw stappen verschillen, verwijdert u de extra stappen en probeert u de aangepaste kolom opnieuw toe te voegen. 

1. Klik op **sluiten en Toep assen** om het gegevens model op te slaan.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicloseapply.png" alt-text="Power BI zelf studie voor Azure Cosmos DB Power BI connector-sluiten & Toep assen":::

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>De rapporten maken

In Power BI Desktop rapport weergave kunt u beginnen met het maken van rapporten om gegevens te visualiseren.  U kunt rapporten maken door velden naar het **rapport** doek te slepen en neer te zetten.

:::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview2.png" alt-text="Power BI Desktop rapport weergave: de vereiste velden slepen en neerzetten":::

In de rapport weergave vindt u het volgende:

1. In het deel venster **velden** ziet u een lijst met gegevens modellen met velden die u voor uw rapporten kunt gebruiken.
1. Het deel venster **Visualisaties** . Een rapport kan één of meer visualisaties bevatten.  Kies de gewenste visuele typen in het deel venster **Visualisaties** .
1. De **rapport** canvas, dit is de locatie waar u de visuele elementen voor uw rapport bouwt.
1. De **rapport** pagina. U kunt meerdere rapport pagina's toevoegen in Power BI Desktop.

Hieronder ziet u de basis stappen voor het maken van een eenvoudig interactief kaart weergave rapport.

1. In ons voor beeld maken we een kaart weergave waarin de locatie van elk Volcano wordt weer gegeven.  Klik in het deel venster **Visualisaties** op het visuele type kaart dat is gemarkeerd in de bovenstaande scherm afbeelding.  U ziet dat het type kaart visueel is getekend op het canvas van het **rapport** .  In het deel venster **Visualisaties** moet ook een set eigenschappen worden weer gegeven die betrekking hebben op het visuele type van de kaart.
1. Sleep het veld LatLong nu van het deel venster **velden** naar de eigenschap **locatie** in het deel venster **Visualisaties** .
1. Vervolgens sleept u het veld Volcano name naar de eigenschap **Legend** .  
1. Sleep vervolgens het veld met de kanteling naar de eigenschap **Size** .  
1. U ziet nu de kaart Visual met een set met bellen die de locatie van elk Volcano aangeven met de grootte van de bel met de uitbrei ding van de Volcano.
1. U hebt nu een basis rapport gemaakt.  U kunt het rapport verder aanpassen door meer visualisaties toe te voegen.  In ons geval hebben we een slicer van het type Volcano toegevoegd om het rapport interactief te maken.  
   
1. Klik in het menu bestand op **Opslaan** en sla het bestand op als PowerBITutorial. pbix.

## <a name="publish-and-share-your-report"></a>Uw rapport publiceren en delen
Als u uw rapport wilt delen, moet u een account hebben in PowerBI.com.

1. Klik in de Power BI Desktop op het lint **Start** .
1. Klik op **Publish**.  U wordt gevraagd om de gebruikers naam en het wacht woord voor uw PowerBI.com-account in te voeren.
1. Zodra de referentie is geverifieerd, wordt het rapport gepubliceerd naar uw bestemming die u hebt geselecteerd.
1. Klik op **PowerBITutorial. Pbix openen in Power bi** om uw rapport te bekijken en te delen op PowerBI.com.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png" alt-text="Publiceren naar Power BI slagen. Open zelf studie in Power BI":::

## <a name="create-a-dashboard-in-powerbicom"></a>Een dashboard maken op PowerBI.com
Nu u een rapport hebt, kunt u dit delen op PowerBI.com

Wanneer u uw rapport publiceert van Power BI Desktop naar PowerBI.com, genereert het een **rapport** en een **gegevensset** in uw PowerBI.com-Tenant. Nadat u bijvoorbeeld een rapport met de naam **PowerBITutorial** hebt gepubliceerd naar PowerBI.com, ziet u PowerBITutorial in de secties **rapporten** en **gegevens sets** op PowerBI.com.

   :::image type="content" source="./media/powerbi-visualize/powerbi-reports-datasets.png" alt-text="Scherm afbeelding van het nieuwe rapport en de gegevensset in PowerBI.com":::

Als u een deelbaar dash board wilt maken, klikt u op de knop **Live-pagina vastzetten** in uw PowerBI.com-rapport.

   :::image type="content" source="./media/powerbi-visualize/power-bi-pin-live-tile.png" alt-text="Scherm afbeelding van het vastmaken van een rapport aan PowerBI.com":::

Volg vervolgens de instructies in [een tegel van een rapport](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) vastmaken om een nieuw dash board te creëren. 

U kunt ook ad hoc wijzigingen aanbrengen in het rapport voordat u een dash board maakt. Het is echter raadzaam om Power BI Desktop te gebruiken voor het uitvoeren van de wijzigingen en het rapport opnieuw te publiceren op PowerBI.com.

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

:::image type="content" source="./media/powerbi-visualize/power-bi-refresh-now.png" alt-text="Screenshot of Refresh Now in PowerBI.com":::

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    :::image type="content" source="./media/powerbi-visualize/power-bi-schedule-refresh.png" alt-text="Screenshot of the Schedule Refresh in PowerBI.com":::
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>Volgende stappen
* Zie aan de [slag met Power bi](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)voor meer informatie over Power bi.
* Voor meer informatie over Azure Cosmos DB, raadpleegt u de [pagina land Azure Cosmos DB documentatie](https://azure.microsoft.com/documentation/services/cosmos-db/).
