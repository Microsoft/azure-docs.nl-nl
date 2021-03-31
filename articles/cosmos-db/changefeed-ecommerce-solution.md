---
title: Azure Cosmos DB Change feed gebruiken om real-time gegevens analyse te visualiseren
description: In dit artikel wordt beschreven hoe wijzigings invoer door een retail bedrijf kan worden gebruikt om gebruikers patronen te begrijpen, analyse en visualisatie in realtime-gegevens uit te voeren
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 05/28/2019
ms.author: sngun
ms.custom: devx-track-java
ms.openlocfilehash: e7b75c71d64054e38630677ecd38f8e3e2483c12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97606331"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Azure Cosmos DB Change feed gebruiken om real-time gegevens analyse te visualiseren
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

De Azure Cosmos DB wijzigings feed is een mechanisme voor het verkrijgen van een doorlopende en incrementele feed van records uit een Azure Cosmos-container wanneer die records worden gemaakt of gewijzigd. De ondersteuning voor feeds kan worden gewijzigd door naar de container te Luis teren. Als output verschijnt er vervolgens een gesorteerde lijst met gewijzigde documenten op volgorde van wijziging. Zie [werken met het artikel Change feed](change-feed.md) voor meer informatie over wijzigings invoer. 

In dit artikel wordt beschreven hoe wijzigings invoer door een e-commerce bedrijf kan worden gebruikt om gebruikers patronen te begrijpen, analyse en visualisatie in realtime-gegevens uit te voeren. U gaat gebeurtenissen analyseren zoals een gebruiker die een item bekijkt, een item toevoegt aan hun winkel wagen of een item koopt. Wanneer een van deze gebeurtenissen zich voordoet, wordt er een nieuwe record gemaakt en wordt die record door de wijzigings feed vastgelegd. Wijzigings feed vervolgens wordt een reeks stappen geactiveerd die resulteren in een visualisatie van metrische gegevens die de prestaties en activiteit van het bedrijf analyseren. Voor beelden van metrische gegevens die u kunt visualiseren, zijn inkomsten, unieke site bezoekers, populairste items en de gemiddelde prijs van de items die worden weer gegeven versus die zijn toegevoegd aan een winkel wagen en de aankoop. Deze metrische voorbeeld gegevens kunnen bijdragen aan een e-commerce bedrijf om de populariteit van de site te evalueren, hun reclame-en prijs strategieën te ontwikkelen en beslissingen te nemen over de inventaris om in te investeren in.

Bekijk de volgende video voor meer informatie over het bekijken van een video over de oplossing voordat u aan de slag gaat:

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>Oplossingsonderdelen
Het volgende diagram geeft de gegevens stroom en onderdelen die bij de oplossing horen:

:::image type="content" source="./media/changefeed-ecommerce-solution/project-visual.png" alt-text="Project-visueel element" border="false":::
 
1. **Gegevens genereren:** Data Simulator wordt gebruikt voor het genereren van Retail gegevens die gebeurtenissen vertegenwoordigen, zoals een gebruiker die een item bekijkt, een item toevoegt aan hun mandje en een item koopt. U kunt een grote set voorbeeld gegevens genereren met behulp van de gegevens generator. De gegenereerde voorbeeld gegevens bevatten documenten met de volgende indeling:
   
    ```json
    {
      "CartID": 2486,
      "Action": "Viewed",
      "Item": "Women's Denim Jacket",
      "Price": 31.99
    }
    ```

2. **Cosmos DB:** De gegenereerde gegevens worden opgeslagen in een Azure Cosmos-container.  

3. **Wijzigings feed:** De wijzigings feed wordt geluisterd naar wijzigingen in de Azure Cosmos-container. Telkens wanneer er een nieuw document wordt toegevoegd aan de verzameling (dat wil zeggen wanneer een gebeurtenis zich voordoet als een gebruiker die een item bekijkt, een item toevoegt aan het wagentje of een item aanschaft), wordt een [Azure-functie](../azure-functions/functions-overview.md)geactiveerd door de wijzigings feed.  

4. **Azure-functie:** Met de functie Azure worden de nieuwe gegevens verwerkt en verzonden naar een [Azure Event hub](../event-hubs/event-hubs-about.md).  

5. **Event hub:** Deze gebeurtenissen worden opgeslagen in de Azure Event hub en verzonden naar [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) om verdere analyses uit te voeren.  

6. **Azure stream Analytics:** Azure Stream Analytics definieert query's om de gebeurtenissen te verwerken en realtime gegevens analyse uit te voeren. Deze gegevens worden vervolgens naar [micro soft power bi](/power-bi/desktop-what-is-desktop)verzonden.  

7. **Power BI:** Power BI wordt gebruikt voor het visualiseren van de gegevens die worden verzonden door Azure Stream Analytics. U kunt een dash board maken om te zien hoe de metrische gegevens in realtime worden gewijzigd.  

## <a name="prerequisites"></a>Vereisten

* Microsoft .NET Framework 4.7.1 of hoger

* Microsoft .NET Core 2,1 (of hoger)

* Visual Studio met Universeel Windows-platform ontwikkeling, .NET Desktop Development en ASP.NET-en Web Development-workloads

* Microsoft Azure abonnement

* Micro soft Power BI-account

* Down load het [Azure Cosmos DB Change feed Lab](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample) van github. 

## <a name="create-azure-resources"></a>Azure-resources maken 

Maak de Azure-resources-Azure Cosmos DB,-opslag account, Event hub Stream Analytics vereist door de oplossing. U kunt deze resources implementeren via een Azure Resource Manager sjabloon. Gebruik de volgende stappen om deze bronnen te implementeren: 

1. Stel het Windows Power shell-uitvoerings beleid in op **onbeperkt**. Als u dit wilt doen, opent u **Windows Power shell als beheerder** en voert u de volgende opdrachten uit:

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. Ga vanuit de GitHub-opslag plaats die u in de vorige stap hebt gedownload naar de map **Azure Resource Manager** en open het bestand met de naam **parameters.jsin** het bestand.  

3. Geef waarden op voor de cosmosdbaccount_name, eventhubnamespace_name, storageaccount_name, para meters zoals aangegeven in **parameters.jsin** het bestand. U moet de namen die u aan uw resources verstrekt, later gebruiken.  

4. Ga vanuit **Windows Power shell** naar de map **Azure Resource Manager** en voer de volgende opdracht uit:

   ```powershell
   .\deploy.ps1
   ```
5. Voer desgevraagd uw Azure **-abonnements-id** in, **changefeedlab** voor de naam van de resource groep en **RUN1** voor de implementatie naam. Zodra de resources zijn geïmplementeerd, kan het tot tien minuten duren voordat deze is voltooid.

## <a name="create-a-database-and-the-collection"></a>Een Data Base en de verzameling maken

U gaat nu een verzameling maken voor het opslaan van e-commerce-site gebeurtenissen. Wanneer een gebruiker een item bekijkt, een item toevoegt aan hun winkel wagen of een item koopt, ontvangt de verzameling een record met de actie (' bekeken ', ' toegevoegd ' of ' aangeschaft '), de naam van het betrokken item, de prijs van het betrokken item en het ID-nummer van de betrokken gebruikers wagen.

1. Ga naar [Azure Portal](https://portal.azure.com/) en zoek het **Azure Cosmos DB account** dat is gemaakt door de sjabloon implementatie.  

2. Selecteer in het deel venster **Data Explorer** de optie **nieuwe verzameling** en vul het formulier in met de volgende details:  

   * Selecteer **nieuwe maken** in het veld **Data Base-id** en voer vervolgens **changefeedlabdatabase** in. Zorg ervoor dat het vakje **doorvoer database inrichten** niet is ingeschakeld.  
   * Voer voor het veld **verzamelings** -id **changefeedlabcollection** in.  
   * Voer **/item** in bij het veld **partitie sleutel** . Dit is hoofdletter gevoelig. Zorg er dus voor dat u deze correct invoert.  
   * Voer **10000** in voor het veld **door Voer** .  
   * Selecteer de knop **OK**.  

3. Maak vervolgens een andere verzameling met de naam **leases** voor wijziging van de feed. De leases-verzameling coördineert de verwerking van de wijzigings feed voor meerdere werk nemers. Er wordt een afzonderlijke verzameling gebruikt voor het opslaan van leases met één lease per partitie.  

4. Ga terug naar het **Data Explorer** venster en selecteer **nieuwe verzameling** en vul het formulier in met de volgende details:

   * Selecteer in het veld **Data Base-id** de optie **bestaande gebruiken** en voer vervolgens **changefeedlabdatabase** in.  
   * Voer **leases** in bij het veld **verzamelings-id** .  
   * Selecteer **vast** bij **opslag capaciteit**.  
   * Zorg ervoor dat het veld **door Voer** is ingesteld op de standaard waarde.  
   * Selecteer de knop **OK**.

## <a name="get-the-connection-string-and-keys"></a>De connection string en sleutels ophalen

### <a name="get-the-azure-cosmos-db-connection-string"></a>De Azure Cosmos DB ophalen connection string

1. Ga naar [Azure Portal](https://portal.azure.com/) en zoek het **Azure Cosmos DB account** dat is gemaakt door de sjabloon implementatie.  

2. Navigeer naar het deel venster **sleutels** , kopieer de primaire verbindings reeks en kopieer deze naar een Klad blok of een ander document waartoe u toegang hebt tot het hele Lab. U moet de label **Cosmos DB de verbindings reeks**. U moet de teken reeks later naar uw code kopiëren. u kunt dus een notitie maken en onthouden waar u deze opslaat.

### <a name="get-the-storage-account-key-and-connection-string"></a>De sleutel voor het opslag account en het connection string ophalen

Met Azure Storage accounts kunnen gebruikers gegevens opslaan. In dit Lab gebruikt u een opslag account om gegevens op te slaan die worden gebruikt door de Azure-functie. De functie Azure wordt geactiveerd wanneer een wijziging wordt aangebracht in de verzameling.

1. Ga terug naar de resource groep en open het opslag account dat u eerder hebt gemaakt  

2. Selecteer **toegangs sleutels** in het menu aan de linkerkant.  

3. Kopieer de waarden onder **sleutel 1** naar een Klad blok of een ander document waarvan u toegang hebt tot het hele Lab. Voorzie de **sleutel** als de **opslag sleutel** en de **verbindings reeks** als **verbindings reeks voor opslag**. U moet deze teken reeksen later naar uw code kopiëren. u kunt dus een notitie maken en onthouden waar u ze opslaat.  

### <a name="get-the-event-hub-namespace-connection-string"></a>De naam ruimte van de Event Hub ophalen connection string

Een Azure Event hub ontvangt de gegevens van de gebeurtenis, opslaat, verwerkt en stuurt deze door. In dit Lab ontvangt de Azure Event hub een document telkens wanneer er een nieuwe gebeurtenis plaatsvindt (dat wil zeggen dat een item wordt bekeken door een gebruiker, wordt toegevoegd aan de winkel wagen van een gebruiker of door een gebruiker wordt gekocht). vervolgens wordt het document doorgestuurd naar Azure Stream Analytics.

1. Ga terug naar de resource groep en open de **Event hub-naam ruimte** die u hebt gemaakt en benoemd in het prelab.  

2. Selecteer **beleid voor gedeelde toegang** in het menu aan de linkerkant.  

3. selecteer **RootManageSharedAccessKey**. Kopieer de **verbindings reeks-primaire sleutel** naar een Klad blok of een ander document waarvan u toegang hebt tot het hele Lab. U moet de naam ruimte van de IT **Event Hub** Connection String labelen. U moet de teken reeks later naar uw code kopiëren. u kunt dus een notitie maken en onthouden waar u deze opslaat.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>Azure function instellen om de wijzigings feed te lezen

Wanneer een nieuw document wordt gemaakt of een huidig document is gewijzigd in een Cosmos-container, voegt de wijzigings feed het gewijzigde document automatisch toe aan de geschiedenis van verzamelings wijzigingen. U gaat nu een Azure-functie bouwen en uitvoeren waarmee de wijzigings feed wordt verwerkt. Wanneer een document wordt gemaakt of gewijzigd in de verzameling die u hebt gemaakt, wordt de Azure-functie geactiveerd door de wijzigings feed. Vervolgens verzendt de Azure-functie het gewijzigde document naar de Event hub.

1. Ga terug naar de opslag plaats die u hebt gekloond op het apparaat.  

2. Klik met de rechter muisknop op het bestand met de naam **ChangeFeedLabSolution. SLN** en selecteer **openen met Visual Studio**.  

3. Ga in Visual Studio naar **local.settings.js** . Gebruik vervolgens de waarden die u eerder hebt vastgelegd om de lege velden in te vullen.  

4. Navigeer naar **ChangeFeedProcessor. cs**. Voer de volgende acties uit in de para meters voor de functie **Run** :  

   * Vervang de tekst van **uw verzamelings naam hier** door de naam van uw verzameling. Als u eerdere instructies hebt gevolgd, is de naam van uw verzameling changefeedlabcollection.  
   * Vervang de naam van **uw leases-verzameling** door de naam van uw leases-verzameling. Als u eerdere instructies hebt gevolgd, is de naam van uw leases-verzameling **leases**.  
   * Zorg ervoor dat boven aan Visual Studio het vak Startup-project aan de linkerkant van de groene pijl de tekst **ChangeFeedFunction**.  
   * Selecteer **Start**  boven aan de pagina om het programma uit te voeren  
   * U kunt controleren of de functie wordt uitgevoerd wanneer de console-App "job host is gestart" aangeeft.

## <a name="insert-data-into-azure-cosmos-db"></a>Gegevens invoegen in Azure Cosmos DB 

Als u wilt zien hoe wijzigingen in de feed nieuwe acties op een e-commerce-site worden verwerkt, moet u gegevens simuleren die gebruikers weer geven van items uit de product catalogus, het toevoegen van die items aan hun winkel wagen en het kopen van de items in hun wagen. Deze gegevens zijn wille keurig en voor het repliceren van de gegevens op een site van Commerce.

1. Ga terug naar de opslag plaats in Verkenner en klik met de rechter muisknop op **ChangeFeedFunction. SLN** om het opnieuw te openen in een nieuw Visual Studio-venster.  

2. Navigeer naar het **App.config** -bestand. Voeg binnen het `<appSettings>` blok het eind punt en de unieke **primaire sleutel** toe van uw Azure Cosmos DB account dat u eerder hebt opgehaald.  

3. Voeg de namen van de **verzameling** en de **Data Base** toe. (Deze namen moeten **changefeedlabcollection** en **changefeedlabdatabase** zijn, tenzij u een andere naam wilt opgeven.)

   :::image type="content" source="./media/changefeed-ecommerce-solution/update-connection-string.png" alt-text="Verbindings reeksen bijwerken":::
 
4. Sla de wijzigingen op in alle bestanden die zijn bewerkt.  

5. Zorg ervoor dat boven aan Visual Studio het vak Startup- **project** aan de linkerkant van de groene pijl de tekst **DataGenerator**. Selecteer vervolgens **beginnen** bovenaan de pagina om het programma uit te voeren.  
 
6. Wacht totdat het programma is uitgevoerd. Het sterretje betekent dat de gegevens beschikbaar zijn. Laat het programma actief. het is belang rijk dat er veel gegevens worden verzameld.  

7. Als u naar [Azure Portal](https://portal.azure.com/) navigeert, vervolgens naar het Cosmos DB-account in de resource groep en vervolgens naar **Data Explorer**, ziet u de wille keurige gegevens die in uw **changefeedlabcollection** zijn geïmporteerd.
 
   :::image type="content" source="./media/changefeed-ecommerce-solution/data-generated-in-portal.png" alt-text="Gegevens die in de portal worden gegenereerd":::

## <a name="set-up-a-stream-analytics-job"></a>Een stream Analytics-taak instellen

Azure Stream Analytics is een volledig beheerde Cloud service voor realtime-verwerking van streaming-gegevens. In dit Lab gebruikt u Stream Analytics om nieuwe gebeurtenissen van de Event hub te verwerken (dat wil zeggen wanneer een item wordt weer gegeven, wordt toegevoegd aan een winkel wagen of is gekocht), neemt u deze gebeurtenissen op in realtime gegevens analyse en verzendt u deze naar Power BI voor visualisatie.

1. Navigeer vanuit het [Azure Portal](https://portal.azure.com/)naar de resource groep en vervolgens naar **streamjob1** (de stream Analytics-taak die u hebt gemaakt in de prelab).  

2. Selecteer de **invoer** zoals hieronder wordt geïllustreerd.  

   :::image type="content" source="./media/changefeed-ecommerce-solution/create-input.png" alt-text="Invoer maken":::

3. Selecteer **+ stroom invoer toevoegen**. Selecteer vervolgens **Event hub** in de vervolg keuzelijst.  

4. Vul het nieuwe invoer formulier in met de volgende details:

   * Voer in het veld **invoer** alias de **invoer** in.  
   * Selecteer de optie **Event hub selecteren bij uw abonnementen**.  
   * Stel het veld **abonnement** in op uw abonnement.  
   * Voer in het veld **naam ruimte van de Event hub** de naam in van de Event hub-naam ruimte die u hebt gemaakt tijdens de prelab.  
   * Selecteer in het veld naam van de **Event hub** de optie voor het **gebruik van bestaande** en kies **gebeurtenis-hub1** in de vervolg keuzelijst.  
   * Zorg ervoor dat het veld naam van **beleids regel voor Event hub** is ingesteld op de standaard waarde.  
   * De **serialisatie-indeling** van de gebeurtenis verlaten als **JSON**.  
   * Zorg ervoor dat het **veld encoding** is ingesteld op **UTF-8**.  
   * Vul het veld **compressie type voor gebeurtenis** in op **geen**.  
   * Selecteer de knop **Opslaan**.

5. Ga terug naar de pagina stream Analytics-taak en selecteer **uitvoer**.  

6. Selecteer **+ Toevoegen**. Selecteer vervolgens **Power bi** in de vervolg keuzelijst.  

7. Voer de volgende acties uit om een nieuwe Power BI uitvoer te maken om de gemiddelde prijs te visualiseren:

   * Voer in het veld **uitvoer alias** **averagePriceOutput** in.  
   * Zorg ervoor dat het veld **groeps werkruimte** is ingesteld om **verbinding te autoriseren om werk ruimten te laden**.  
   * Typ **averagePrice** in het veld **naam van gegevensset** .  
   * Voer in het veld **tabel naam** **averagePrice** in.  
   * Selecteer de knop **autoriseren** en volg de instructies om de verbinding met Power bi te autoriseren.  
   * Selecteer de knop **Opslaan**.  

8. Ga vervolgens terug naar **streamjob1** en selecteer **query bewerken**.

   :::image type="content" source="./media/changefeed-ecommerce-solution/edit-query.png" alt-text="Query bewerken":::
 
9. Plak de volgende query in het query venster. De **gemiddelde prijs** query berekent de gemiddelde prijs van alle items die worden weer gegeven door gebruikers, de gemiddelde prijs van alle items die worden toegevoegd aan de manden van gebruikers en de gemiddelde prijs van alle items die door gebruikers worden gekocht. Met deze metriek kunnen e-commerce-bedrijven helpen bij het bepalen van de prijzen voor het verkopen van artikelen op en de inventarisatie in. Als de gemiddelde prijs van items die worden weer gegeven, bijvoorbeeld veel hoger is dan de gemiddelde prijs van de gekochte artikelen, kan een bedrijf ervoor kiezen minder dure artikelen toe te voegen aan de inventaris.

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. Selecteer vervolgens **Opslaan** in de linkerbovenhoek.  

11. Ga nu terug naar **streamjob1** en selecteer de knop **Start** boven aan de pagina. Het kan een paar minuten duren voordat Azure Stream Analytics worden opgestart, maar u ziet uiteindelijk dat de wijziging wordt doorgevoerd van ' starten ' in ' wordt uitgevoerd '.

## <a name="connect-to-power-bi"></a>Verbinding maken met Power BI

Power BI is een pakket business analytics-hulpprogramma's waarmee u gegevens kunt analyseren en inzichten kunt delen. Het is een goed voor beeld van hoe u de geanalyseerde gegevens strategisch kunt visualiseren.

1. Meld u aan bij Power BI en navigeer naar **mijn werk ruimte** door het menu aan de linkerkant van de pagina te openen.  

2. Selecteer **+ maken** in de rechter bovenhoek en selecteer vervolgens **dash board** om een dash board te maken.  

3. Selecteer **+ tegel toevoegen** in de rechter bovenhoek.  

4. Selecteer **aangepaste streaminggegevens** en selecteer vervolgens de knop **volgende** .  
 
5. Selecteer **averagePrice** in **de gegevens sets** en selecteer vervolgens **volgende**.  

6. Kies in het veld **type visualisatie** de optie **gegroepeerd staaf diagram** in de vervolg keuzelijst. Voeg onder **as** actie toe. **Legenda** overs laan zonder iets toe te voegen. Voeg vervolgens in de volgende sectie de naam **Value** toe: **Gem**. Selecteer **volgende**, geef een titel op voor de grafiek en selecteer **Toep assen**. Er wordt nu een nieuwe grafiek op het dash board weer geven.  

7. Als u nu meer metrische gegevens wilt visualiseren, kunt u teruggaan naar **streamjob1** en drie meer uitvoer maken met de volgende velden.

   a. **Uitvoer alias:** incomingRevenueOutput, naam gegevensset: IncomingRevenue, tabel naam: incomingRevenue  
   b. **Uitvoer alias:** top5Output, naam gegevensset: Top5, tabel naam: top5  
   c. **Uitvoer alias:** uniqueVisitorCountOutput, naam gegevensset: UniqueVisitorCount, tabel naam: uniqueVisitorCount

   Selecteer vervolgens **query bewerken** en plak de volgende query's die u **hierboven** hebt geschreven.

   ```sql
    /*TOP 5*/
    WITH Counter AS
    (
    SELECT Item, Price, Action, COUNT(*) AS countEvents
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY Item, Price, Action, TumblingWindow(second,30)
    ), 
    top5 AS
    (
    SELECT DISTINCT
    CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
    FROM Counter
    GROUP BY TumblingWindow(second,30)
    ), 
    arrayselect AS 
    (
    SELECT arrayElement.ArrayValue
    FROM top5
    CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
    ) 
    SELECT arrayvalue.value.item, arrayvalue.value.price,   arrayvalue.value.countEvents
    INTO top5Output
    FROM arrayselect

    /*REVENUE*/
    SELECT System.TimeStamp AS Time, SUM(Price)
    INTO incomingRevenueOutput
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY TumblingWindow(hour, 1)

    /*UNIQUE VISITORS*/
    SELECT System.TimeStamp AS Time, COUNT(DISTINCT CartID) as uniqueVisitors
    INTO uniqueVisitorCountOutput
    FROM input
    GROUP BY TumblingWindow(second, 5)
   ```
   
   De TOP 5 query berekent de bovenste 5 items, gerangschikt op het aantal keren dat ze zijn gekocht. Deze metrische gegevens kunnen e-commerce bedrijven helpen om te beoordelen welke items het meest populair zijn en kunnen van invloed zijn op de reclame-, prijs-en voorraad beslissingen van het bedrijf.

   De omzet query berekent de omzet door de prijzen bij elkaar op te tellen van alle items die elke minuut zijn gekocht. Deze metrische gegevens kunnen e-commerce bedrijven helpen om financiële prestaties te beoordelen en ook te begrijpen welke tijden van de dag bijdragen aan de meeste omzet. Dit kan invloed hebben op de algehele bedrijfs strategie, in het bijzonder in de handel.

   De unieke bezoekers query berekent hoeveel unieke bezoekers elke vijf seconden op de site zijn door de unieke ID van de winkel wagen te detecteren. Met deze metrische gegevens kunnen e-commerce bedrijven hun site activiteiten evalueren en bij hoe meer klanten kunnen worden aangeschaft.

8. U kunt nu ook tegels voor deze gegevens sets toevoegen.

   * Voor de top 5 zou het zinvol zijn om een gegroepeerd kolom diagram te maken met de items als de as en het aantal als de waarde.  
   * Voor omzet zou het zinvol zijn om een lijn diagram te maken met tijd als de as en de som van de prijzen als waarde. Het tijd venster dat moet worden weer gegeven, is het grootst mogelijk om zoveel mogelijk informatie te leveren.  
   * Voor unieke bezoekers zou het zinvol zijn om een kaart visualisatie te maken met het aantal unieke bezoekers als waarde.

   Zo ziet een voorbeeld dashboard eruit met deze grafieken:

   :::image type="content" source="./media/changefeed-ecommerce-solution/visualizations.png" alt-text="Scherm afbeelding toont een voor beeld van een dash board met de naam gemiddelde prijs van items op actie, unieke bezoekers, omzet en top 5 van items die zijn gekocht.":::

## <a name="optional-visualize-with-an-e-commerce-site"></a>Optioneel: visualiseren met een E-commerce-site

U ziet nu hoe u het nieuwe hulp programma voor gegevens analyse kunt gebruiken om verbinding te maken met een echte e-commerce-site. Als u de e-commerce-site wilt maken, gebruikt u een Azure Cosmos-data base om de lijst met product categorieën (vrouwen, heren, unisex), de product catalogus en een lijst met veelgebruikte items op te slaan.

1. Ga terug naar de [Azure Portal](https://portal.azure.com/), vervolgens naar uw **Cosmos DB account** en vervolgens naar **Data Explorer**.  

   Voeg twee verzamelingen onder **changefeedlabdatabase**-  -  **producten** en- **Categorieën** met vaste opslag capaciteit toe.

   Voeg een andere verzameling toe onder **changefeedlabdatabase** met de naam **topItems** en **/item** als de partitie sleutel.

2. Selecteer de **topItems** -verzameling en stel onder **schaal en instellingen** de **time to Live** in op **30 seconden** zodat topItems elke 30 seconden wordt bijgewerkt.

   :::image type="content" source="./media/changefeed-ecommerce-solution/time-to-live.png" alt-text="Time To Live":::

3. Als u de **topItems** verzameling met de meest gekochte items wilt vullen, gaat u terug naar **streamjob1** en voegt u een nieuwe **uitvoer** toe. Selecteer **Cosmos DB**.

4. Vul de vereiste velden in zoals hieronder wordt beschreven.

   :::image type="content" source="./media/changefeed-ecommerce-solution/cosmos-output.png" alt-text="Cosmos uitvoer":::
 
5. Als u de optionele TOP 5-query in het vorige deel van het Lab hebt toegevoegd, gaat u verder met deel 5a. Als dat niet het geval is, gaat u verder met stap 5.

   5a. Selecteer in **streamjob1** de optie **query bewerken** en plak de volgende query in de query-editor van Azure stream Analytics onder de top 5 query, maar boven de overige query's.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. Selecteer in **streamjob1** de optie **query bewerken** en plak de volgende query in de query-editor van Azure stream Analytics boven alle andere query's.

   ```sql
   /*TOP 5*/
   WITH Counter AS
   (
   SELECT Item, Price, Action, COUNT(*) AS countEvents
   FROM input
   WHERE Action = 'Purchased'
   GROUP BY Item, Price, Action, TumblingWindow(second,30)
   ), 
   top5 AS
   (
   SELECT DISTINCT
   CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
   FROM Counter
   GROUP BY TumblingWindow(second,30)
   ), 
   arrayselect AS 
   (
   SELECT arrayElement.ArrayValue
   FROM top5
   CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
   ) 
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```

6. Open **EcommerceWebApp. SLN** en navigeer naar het **Web.config** bestand in de **Solution Explorer**.  

7. Voeg binnen het `<appSettings>` blok de **URI** en **primaire sleutel** toe die u eerder hebt opgeslagen, waarbij **uw URI hier** en **uw primaire sleutel** worden vermeld. Voeg vervolgens de naam van uw **Data Base** en **verzamelings naam** toe zoals aangegeven. (Deze namen moeten **changefeedlabdatabase** en **changefeedlabcollection** zijn, tenzij u ervoor hebt gekozen om een andere naam te noemen.)

   Vul de naam van uw **producten verzameling**, de **verzamelings naam** van de categorie en de **verzamelings naam van de bovenste items** in zoals aangegeven. (Deze namen moeten **producten, categorieën en topItems** zijn, tenzij u een andere naam hebt gekozen.)  

8. Navigeer naar en open de **map Checkout** in **EcommerceWebApp. SLN.** Open vervolgens het **Web.config** -bestand in die map.  

9. Voeg binnen het `<appSettings>` blok de **URI** en **primaire sleutel** toe die u eerder hebt opgeslagen, waarbij u hebt opgegeven. Voeg vervolgens de naam van uw **Data Base** en **verzamelings naam** toe zoals aangegeven. (Deze namen moeten **changefeedlabdatabase** en **changefeedlabcollection** zijn, tenzij u ervoor hebt gekozen om een andere naam te noemen.)  

10. Klik boven aan de pagina op **Start** om het programma uit te voeren.  

11. U kunt nu op de e-commerce-site spelen. Wanneer u een item bekijkt, een item toevoegt aan uw winkel wagen, de hoeveelheid van een item in uw winkel wagen wijzigt of een item aanschaft, worden deze gebeurtenissen door gegeven via de Cosmos DB Change feed to Event hub, ASA en vervolgens Power BI. We raden u aan om DataGenerator uit te voeren voor het genereren van belang rijke gegevens over webverkeer en biedt een realistische set ' Hot Products ' op de e-commerce-site.

## <a name="delete-the-resources"></a>De resources verwijderen

Als u de resources wilt verwijderen die u hebt gemaakt tijdens dit lab, gaat u naar de resource groep op [Azure Portal](https://portal.azure.com/)en selecteert u **resource groep verwijderen** in het menu boven aan de pagina en volgt u de instructies die worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen 
  
* Zie [werken met ondersteuning voor wijzigings feeds in azure Cosmos DB](change-feed.md) voor meer informatie over wijzigings invoer.