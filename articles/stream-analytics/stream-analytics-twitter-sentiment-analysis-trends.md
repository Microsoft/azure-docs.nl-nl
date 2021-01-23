---
title: Realtime Twitter-sentiment analyse met Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u Stream Analytics gebruikt voor realtime Twitter-sentiment analyse. Stapsgewijze richt lijnen voor het genereren van gebeurtenissen naar gegevens op een live dash board.
services: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: how-to
ms.date: 02/10/2020
ms.openlocfilehash: 6a461ad906f7611c8a13e2ee495f4d2f62fedd53
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734832"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analyse van realtime Twitter-gevoel in Azure Stream Analytics

In dit artikel leert u hoe u een sentiment-analyse oplossing voor sociale media bouwt door real-time Twitter-gebeurtenissen in te brengen in azure Event Hubs. U schrijft een Azure Stream Analytics query om de gegevens te analyseren en de resultaten op te slaan voor later gebruik of om een [Power bi](https://powerbi.com/) -dash board te maken om inzicht in realtime te bieden.

Hulpprogram ma's voor sociale media analyse helpen organisaties bij het begrijpen van trending-onderwerpen. Trending onderwerpen zijn onderwerpen en gedragingen die een groot aantal posts op sociale media hebben. Sentiment analyse, ook wel *opinie* analyse genoemd, maakt gebruik van hulpprogram ma's voor de analyse van sociale media om houding te bepalen voor een product of een idee. 

Realtime-trend analyse van Twitter is een goed voor beeld van een analyse programma omdat u met het hashtag-abonnements model kunt Luis teren naar specifieke sleutel woorden (Hashtags) en sentiment analyse van de feed ontwikkelt.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scenario: analyse van sociale media-sentiment in realtime

Een bedrijf met een website voor nieuws media is geïnteresseerd in het verkrijgen van een voor deel van zijn concurrenten met behulp van site-inhoud die direct relevant is voor de lezers. Het bedrijf maakt gebruik van sociale media-analyses op onderwerpen die relevant zijn voor lezers door real-time sentiment analyse van Twitter-gegevens uit te voeren.

Voor het identificeren van trending onderwerpen in realtime op Twitter, heeft het bedrijf een real-time analyse nodig over het tweet-volume en de sentiment voor belangrijkste onderwerpen.

## <a name="prerequisites"></a>Vereisten

In deze hand leiding kunt u een client toepassing gebruiken die verbinding maakt met Twitter en zoekt naar tweets met bepaalde Hashtags (die u kunt instellen). Als u de toepassing wilt uitvoeren en de tweets wilt analyseren met behulp van Azure streaming Analytics, hebt u het volgende nodig:

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) aan.

* Een [Twitter](https://twitter.com) -account.

* De TwitterClientCore-toepassing, die de Twitter-feed leest. Down load [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore)om deze toepassing op te halen.

* Installeer de [.net core SLI](/dotnet/core/tools/?tabs=netcore2x) versie 2.1.0.

Hieronder ziet u de architectuur van de oplossing die u gaat implementeren.

   ![Een diagram van verschillende services en toepassingen die worden gebruikt voor het bouwen van de oplossing.](./media/stream-analytics-twitter-sentiment-analysis-trends/solution-diagram.png "Oplossings diagram")

## <a name="create-an-event-hub-for-streaming-input"></a>Een Event Hub maken voor streaming-invoer

De voorbeeld toepassing genereert gebeurtenissen en duwt deze naar een Azure-Event Hub. Azure Event Hubs zijn de voorkeurs methode voor het opnemen van gebeurtenissen voor Stream Analytics. Zie de [documentatie van Azure Event hubs](../event-hubs/event-hubs-about.md)voor meer informatie.

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Een Event Hub naam ruimte en Event Hub maken
In deze sectie maakt u een Event Hub naam ruimte en voegt u een Event Hub toe aan die naam ruimte. Event hub-naam ruimten worden gebruikt om gerelateerde exemplaren van de gebeurtenis-bus logisch te groeperen. 

1. Meld u aan bij de Azure Portal en selecteer **een resource maken**. Kies. Zoek naar **Event hubs** en selecteer **maken**.

2. Voer op de pagina **naam ruimte maken** een naam in voor de naam ruimte. U kunt een wille keurige naam voor de naam ruimte gebruiken, maar de naam moet geldig zijn voor een URL en moet uniek zijn binnen Azure. 
    
3. Selecteer een prijs categorie en een abonnement en maak of kies een resource groep. Kies vervolgens een locatie en selecteer **maken**. 
 
4. Wanneer de implementatie van de naam ruimte is voltooid, gaat u naar de resource groep en zoekt u de Event Hub naam ruimte in de lijst met Azure-resources. 

5. Selecteer in de nieuwe naam ruimte **+ &nbsp; Event hub**. 

6. Geef de nieuwe Event Hub *socialtwitter-eh*. U kunt ook een andere naam gebruiken. Als u dat wel doet, noteert u deze, omdat u de naam later nodig hebt. U hoeft geen andere opties voor de Event Hub in te stellen.
 
7. Selecteer **Maken**.

### <a name="grant-access-to-the-event-hub"></a>Toegang verlenen tot de Event Hub

Voordat een proces gegevens naar een Event Hub kan verzenden, heeft de Event Hub een beleid nodig waarmee toegang is toegestaan. Het toegangsbeleid genereert een verbindingsreeks die autorisatiegegevens bevat.

1.  Selecteer in de navigatie balk aan de linkerkant van uw event hubs-naam ruimte de optie **Event hubs**. Deze bevindt zich in de sectie **entities** . Selecteer vervolgens de Event Hub die u zojuist hebt gemaakt.

2.  Selecteer in de navigatie balk aan de linkerkant de optie **beleid voor gedeelde toegang** onder **instellingen**.

    >[!NOTE]
    >Er is een optie voor gedeeld toegangs beleid onder voor de naam ruimte Event Hub en voor de Event Hub. Zorg ervoor dat u werkt in de context van uw Event Hub en niet de algemene Event Hub naam ruimte.

3.  Selecteer op de pagina toegangs beleid **+ toevoegen**. Voer vervolgens *socialtwitter-Access* in voor de naam van het **beleid** en schakel het selectie vakje **beheren** in.
 
4.  Selecteer **Maken**.

5.  Nadat het beleid is geïmplementeerd, selecteert u het beleid in de lijst met beleid voor gedeelde toegang.

6.  Zoek het vak **primaire sleutel van de verbindings reeks** op en selecteer de knop kopiëren naast het Connection String.
 
7.  Plak de verbindingsreeks in een teksteditor. U hebt deze connection string nodig voor de volgende sectie nadat u enkele kleine wijzigingen hebt aangebracht.

   De connection string ziet er als volgt uit:
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   U ziet dat de Connection String meerdere sleutel-waardeparen bevat, gescheiden door punt komma's: `Endpoint` ,, en `SharedAccessKeyName` `SharedAccessKey` `EntityPath` .  

   > [!NOTE]
   > Voor de beveiliging zijn delen van de connection string in het voor beeld verwijderd.

## <a name="configure-and-start-the-twitter-client-application"></a>De Twitter-client toepassing configureren en starten

De client toepassing ontvangt Tweet-gebeurtenissen rechtstreeks van Twitter. Om dit te doen, heeft het toestemming nodig om de Twitter streaming Api's aan te roepen. Als u deze machtiging wilt configureren, maakt u een toepassing in Twitter, waarmee unieke referenties worden gegenereerd (zoals een OAuth-token). U kunt vervolgens de client toepassing configureren voor het gebruik van deze referenties bij het maken van API-aanroepen. 

### <a name="create-a-twitter-application"></a>Een Twitter-toepassing maken
Als u nog geen Twitter-toepassing hebt die u voor deze hand leiding kunt gebruiken, kunt u er een maken. U moet al een Twitter-account hebben.

> [!NOTE]
> Het exacte proces in Twitter voor het maken van een toepassing en het ophalen van de sleutels, geheimen en token kan veranderen. Als deze instructies niet overeenkomen met wat u ziet op de Twitter-site, raadpleegt u de Twitter-ontwikkelaars documentatie.

1. Ga in een webbrowser naar [Twitter voor ontwikkel aars](https://developer.twitter.com/en/apps), maak een ontwikkelaars account en selecteer **een app maken**. Mogelijk wordt er een bericht weergegeven met de mededeling dat u een Twitter-ontwikkelaarsaccount moet aanvragen. U kunt dit doen en nadat uw toepassing is goedgekeurd, ziet u een bevestigings-e-mail. Het kan enkele dagen duren voordat de aanvraag voor een ontwikkelaarsaccount wordt goedgekeurd.

   ![Scherm afbeelding toont de knop een app maken.](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Details van Twitter-toepassing")

2. Voer op de pagina **Create an application** de gegevens voor de nieuwe app in en selecteer **Create your Twitter application**.

   ![Scherm afbeelding toont het deel venster app-Details waarin u waarden voor uw app kunt invoeren.](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Details van Twitter-toepassing")

3. Selecteer op de toepassingspagina het tabblad **Keys and Tokens** en kopieer de waarden voor **Consumer API Key** en **Consumer API Secret Key**. Selecteer ook **Create** onder **Access Token and Access Token Secret** om de toegangstokens te genereren. Kopieer de waarden voor **Access Token** en **Access Token Secret**.

   Sla de waarden op die u hebt opgehaald voor de Twitter-toepassing. U hebt de waarden later nodig.

> [!NOTE]
> De sleutels en geheimen voor de Twitter-toepassing bieden toegang tot uw Twitter-account. Deze informatie wordt als vertrouwelijk beschouwd, op dezelfde manier als uw Twitter-wacht woord. U kunt deze informatie bijvoorbeeld niet insluiten in een toepassing die u aan anderen verstrekt. 

### <a name="configure-the-client-application"></a>De client toepassing configureren

We hebben een client toepassing gemaakt die verbinding maakt met Twitter-gegevens met behulp [van de streaming-api's van Twitter](https://dev.twitter.com/streaming/overview) voor het verzamelen van Tweet-gebeurtenissen over een specifieke set met onderwerpen.

Voordat de toepassing wordt uitgevoerd, hebt u bepaalde gegevens van u nodig, zoals de Twitter-sleutels en de Event Hub connection string.

1. Zorg ervoor dat u de [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore) -toepassing hebt gedownload, zoals vermeld in de vereisten.

2. Gebruik een tekst editor om het *App.config* bestand te openen. Breng de volgende wijzigingen aan in het `<appSettings>` element:

   * Ingesteld `oauth_consumer_key` op de Twitter-consument sleutel (API-sleutel). 
   * Ingesteld `oauth_consumer_secret` op de Twitter-consument geheim (geheime API-sleutel).
   * Stel `oauth_token` in op het Twitter-toegangs token.
   * Stel `oauth_token_secret` in op het geheim voor het Twitter-toegangs token.
   * Ingesteld `EventHubNameConnectionString` op de Connection String.
   * Stel `EventHubName` in op de naam van de Event hub (dat is de waarde van het pad van de entiteit).

3. Open de opdracht regel en navigeer naar de map waarin uw TwitterClientCore-app zich bevindt. Gebruik de opdracht `dotnet build` om het project te bouwen. Gebruik vervolgens de opdracht `dotnet run` om de app uit te voeren. De app verzendt tweets naar uw event hub.

## <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken

Nu Tweet-gebeurtenissen in realtime worden gestreamd vanuit Twitter, kunt u een Stream Analytics taak instellen om deze gebeurtenissen in realtime te analyseren.

1. Navigeer in het Azure Portal naar de resource groep en selecteer **+ toevoegen**. Zoek vervolgens naar **Stream Analytics-taak** en selecteer **maken**.

2. Noem de taak `socialtwitter-sa-job` en geef een abonnement, resource groep en locatie op.

    Het is een goed idee om de taak en de Event Hub in dezelfde regio te plaatsen voor de beste prestaties en om ervoor te zorgen dat u niet betaalt voor het overdragen van gegevens tussen regio's.

3. Selecteer **Maken**. Ga vervolgens naar uw taak wanneer de implementatie is voltooid.

## <a name="specify-the-job-input"></a>De taak invoer opgeven

1. Selecteer in uw Stream Analytics-taak de optie **invoer** in het menu links onder **taak topologie**.

2. Selecteer **+ &nbsp; add Stream Input**  >  **Event hub**. Vul het **nieuwe invoer** formulier in met de volgende informatie:

   |**Instelling**  |**Voorgestelde waarde**  |**Beschrijving**  |
   |---------|---------|---------|
   |Invoeralias| *TwitterStream* | Voer een alias in voor de invoer. |
   |Abonnement  | \<Your subscription\> |  Selecteer het Azure-abonnement dat u wilt gebruiken. |
   |Event hub-naamruimte | *ASA-Twitter-eventhub* |
   |Naam van de Event Hub | *socialtwitter-eh* | Kies *bestaande gebruiken*. Selecteer vervolgens de Event hub die u hebt gemaakt.|
   |Gebeurteniscompressietype| GZip | Het type gegevens compressie.|

   Wijzig de resterende standaard waarden en selecteer **Opslaan**.

## <a name="specify-the-job-query"></a>De taak query opgeven

Stream Analytics ondersteunt een eenvoudig, declaratief query model dat trans formaties beschrijft. Raadpleeg [Verwijzing voor Azure Stream Analytics-querytaal](/stream-analytics-query/stream-analytics-query-language-reference) voor meer informatie over de taal. Deze hand leiding helpt u bij het ontwerpen en testen van verschillende query's via Twitter-gegevens.

Als u het aantal vermeldingen tussen de onderwerpen wilt vergelijken, kunt u een [tumblingvenstertriggers-venster](/stream-analytics-query/tumbling-window-azure-stream-analytics) gebruiken om elke vijf seconden het aantal vermeldingen per onderwerp op te halen.

1. In uw taak **overzicht** selecteert u **query bewerken** in de rechter bovenhoek van het query-vak. Azure vermeldt de invoer en uitvoer die voor de taak zijn geconfigureerd. Hiermee kunt u een query maken om de invoer stroom te transformeren wanneer deze naar de uitvoer wordt verzonden.

2. Wijzig de query in de query-editor in het volgende:

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. Gebeurtenis gegevens uit de berichten moeten worden weer gegeven in het venster **invoer voorbeeld** onder uw query. Zorg ervoor dat de **weer gave** is ingesteld op **JSON**. Als u geen gegevens ziet, zorg er dan voor dat uw gegevens Generator gebeurtenissen naar uw Event Hub verzendt en dat u **gzip** hebt geselecteerd als het compressie type voor de invoer.

4. Selecteer **test query** en Bekijk de resultaten in het venster **test resultaten** onder uw query.

5. Wijzig de query in de code-editor naar de volgende en selecteer **test query**:

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. Deze query retourneert alle tweets die het sleutel woord *Azure* bevatten.

## <a name="create-an-output-sink"></a>Een uitvoer Sink maken

U hebt nu een gebeurtenis stroom gedefinieerd, een Event Hub invoer voor opname gebeurtenissen en een query voor het uitvoeren van een trans formatie over de stroom. De laatste stap is het definiëren van een uitvoer filter voor de taak.  

In deze hand leiding schrijft u de geaggregeerde Tweet-gebeurtenissen van de taak query naar Azure Blob-opslag.  Afhankelijk van de behoeften van uw toepassing kunt u uw resultaten ook naar Azure SQL Database, Azure-tabel opslag, Event Hubs of Power BI pushen.

## <a name="specify-the-job-output"></a>De taak uitvoer opgeven

1. Selecteer in het gedeelte **taak topologie** in het navigatie menu links de optie **uitvoer**. 

2. Klik op de pagina **uitvoer** op **+ &nbsp; toevoegen** en **Blob Storage/Data Lake Storage Gen2**:

   * **Uitvoer alias**: gebruik de naam `TwitterStream-Output` . 
   * **Opties voor importeren**: Selecteer **opslag selecteren bij uw abonnementen**.
   * **Opslag account**. Selecteer uw opslagaccount.
   * **Container**. Selecteer **nieuwe maken** en invoeren `socialtwitter` .
   
4. Selecteer **Opslaan**.   

## <a name="start-the-job"></a>Taak starten

Er zijn een invoer-, query-en uitvoer taak opgegeven. U bent klaar om de Stream Analytics-taak te starten.

1. Zorg ervoor dat de TwitterClientCore-toepassing wordt uitgevoerd. 

2. Selecteer **Start** in het taak overzicht.

3. Selecteer op de pagina **taak starten** voor de **Start tijd van de taak uitvoer** de optie **nu** en selecteer vervolgens **starten**.

## <a name="get-support"></a>Ondersteuning krijgen
Probeer voor meer hulp onze [micro soft Q&een vraag pagina voor Azure stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](/rest/api/streamanalytics/)
