---
title: Data Collector-API gebruiken om een gegevens pijplijn te maken
description: U kunt de Azure Monitor HTTP data collector API gebruiken om POST JSON-gegevens toe te voegen aan de Log Analytics-werk ruimte vanaf elke client die de REST API kan aanroepen. In dit artikel wordt beschreven hoe u gegevens die zijn opgeslagen in bestanden op een geautomatiseerde manier uploadt.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/09/2018
ms.openlocfilehash: ab2e9c691f17b8f0891ecbc82ff42cd3529a1328
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031188"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>Een gegevens pijplijn maken met de Data Collector-API

Met de [Azure Monitor Data Collector-API](data-collector-api.md) kunt u aangepaste logboek gegevens importeren in een log Analytics werkruimte in azure monitor. De enige vereisten zijn dat de gegevens in JSON-indeling zijn ingedeeld en worden gesplitst in 30 MB of minder segmenten. Dit is een volledig flexibel mechanisme dat op verschillende manieren kan worden aangesloten: van gegevens die rechtstreeks vanuit uw toepassing worden verzonden naar eenmalige adhoc-uploads. In dit artikel vindt u een overzicht van enkele begin punten voor een veelvoorkomend scenario: de nood zaak om gegevens die zijn opgeslagen in bestanden, regel matig en automatisch te uploaden. Hoewel de pijp lijn die hier wordt weer gegeven niet het meest presteert of anderszins geoptimaliseerd is, is het bedoeld om als uitgangs punt te fungeren voor het bouwen van een productie pijplijn van uw eigen product.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="example-problem"></a>Voorbeeld probleem
In de rest van dit artikel gaan we pagina weergave gegevens bekijken in Application Insights. In het hypothetische scenario willen we de geografische gegevens die standaard door de Application Insights SDK worden verzameld, correleren aan aangepaste gegevens die de populatie van elk land/regio in de wereld bevatten, met het doel van het identificeren van de meeste marketing dollars. 

We gebruiken een open bare gegevens bron, zoals de [kandidaten voor Onwerelde populaties](https://esa.un.org/unpd/wpp/) voor dit doel. De gegevens hebben het volgende eenvoudige schema:

![Voor beeld eenvoudig schema](./media/create-pipeline-datacollector-api/example-simple-schema-01.png)

In ons voor beeld gaan we ervan uit dat we een nieuw bestand uploaden met de gegevens van het laatste jaar zodra deze beschikbaar zijn.

## <a name="general-design"></a>Algemeen ontwerp
We gebruiken een klassieke ETL-type logica om onze pijp lijn te ontwerpen. De architectuur ziet er als volgt uit:

![Pijplijn architectuur voor gegevens verzameling](./media/create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

In dit artikel wordt niet beschreven hoe u gegevens maakt of [uploadt naar een Azure Blob Storage-account](../../storage/blobs/storage-upload-process-images.md). In plaats daarvan kiezen we de stroom zodra een nieuw bestand wordt geüpload naar de blob. Vanaf hier:

1. Er wordt door een proces gedetecteerd dat er nieuwe gegevens zijn geüpload.  In ons voor beeld wordt een [Azure Logic-app](../../logic-apps/logic-apps-overview.md)gebruikt, die een trigger beschikbaar heeft voor het detecteren van nieuwe gegevens die worden geüpload naar een blob.

2. Een processor leest deze nieuwe gegevens en converteert deze naar JSON. de indeling die in dit voor beeld is vereist voor Azure Monitor, gebruiken we een [Azure-functie](../../azure-functions/functions-overview.md) als een licht gewicht, rendabele manier om onze verwerkings code uit te voeren. De functie wordt gestart door de logische app die we hebben gebruikt voor het detecteren van de nieuwe gegevens.

3. Ten slotte, zodra het JSON-object beschikbaar is, wordt het naar Azure Monitor verzonden. De logische app verzendt de gegevens naar Azure Monitor met behulp van de ingebouwde Log Analytics Data Collector-activiteit.

Hoewel de gedetailleerde installatie van de Blob-opslag, de logische app of de Azure-functie niet in dit artikel wordt beschreven, zijn gedetailleerde instructies beschikbaar op de pagina's van de specifieke producten.

Om deze pijp lijn te bewaken, gebruiken we Application Insights om onze Azure Function- [gegevens hier](../../azure-functions/functions-monitoring.md)te bewaken en Azure monitor om de details van de Logic-app [hier](../../logic-apps/monitor-logic-apps-log-analytics.md)te bewaken. 

## <a name="setting-up-the-pipeline"></a>De pijp lijn instellen
Als u de pijp lijn wilt instellen, moet u eerst controleren of u de BLOB-container hebt gemaakt en geconfigureerd. Zorg er ook voor dat de Log Analytics werk ruimte waar u de gegevens naartoe wilt verzenden, is gemaakt.

## <a name="ingesting-json-data"></a>JSON-gegevens opnemen
Het opnemen van JSON-gegevens is lastig met Logic Apps en omdat er geen trans formatie hoeft te worden uitgevoerd, kunnen we de volledige pijp lijn in één logische app omsluiten. Zodra de BLOB-container en de Log Analytics-werk ruimte zijn geconfigureerd, maakt u een nieuwe logische app en configureert u deze als volgt:

![Werk stroom voor logische apps-voor beeld](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

Sla de logische app op en ga door met testen.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>XML, CSV of andere gegevens indelingen opnemen
Logic Apps vandaag heeft geen ingebouwde mogelijkheden voor het eenvoudig transformeren van XML-, CSV-of andere typen in JSON-indeling. Daarom moeten we een andere manier gebruiken om deze trans formatie te volt ooien. Voor dit artikel gebruiken we de serverloze reken mogelijkheden van Azure Functions als een zeer lichte en rendabele manier om dit te doen. 

In dit voor beeld parseren we een CSV-bestand, maar elk ander bestands type kunnen op dezelfde manier worden verwerkt. Wijzig het deserialisatie gedeelte van de Azure-functie om de juiste logica voor uw specifieke gegevens type weer te geven.

1.  Maak een nieuwe Azure-functie met behulp van de functie runtime v1 en het verbruik, wanneer u hierom wordt gevraagd.  Selecteer de **http-trigger** sjabloon waarop C# is gericht als uitgangs punt waarmee uw bindingen worden geconfigureerd. 
2.  Maak een nieuw bestand met de naam **project.jsop** het tabblad **bestanden weer geven** in het rechterdeel venster en plak de volgende code in NuGet-pakketten die worden gebruikt:

    ![Azure Functions voorbeeld project](./media/create-pipeline-datacollector-api/functions-example-project-01.png)
    
    ``` JSON
    {
      "frameworks": {
        "net46":{
          "dependencies": {
            "CsvHelper": "7.1.1",
            "Newtonsoft.Json": "11.0.2"
          }  
        }  
       }  
     }  
    ```

3. Schakel over naar **Run. CSX** in het rechterdeel venster en vervang de standaard code door het volgende. 

    >[!NOTE]
    >Voor uw project moet u het record model (de klasse ' PopulationRecord ') vervangen door uw eigen gegevens schema.
    >

    ```   
    using System.Net;
    using Newtonsoft.Json;
    using CsvHelper;
    
    class PopulationRecord
    {
        public String Location { get; set; }
        public int Time { get; set; }
        public long Population { get; set; }
    }

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        string filePath = await req.Content.ReadAsStringAsync(); //get the CSV URI being passed from Logic App
        string response = "";

        //get a stream from blob
        WebClient wc = new WebClient();
        Stream s = wc.OpenRead(filePath);         

        //read the stream
        using (var sr = new StreamReader(s))
        {
            var csvReader = new CsvReader(sr);
    
            var records = csvReader.GetRecords<PopulationRecord>(); //deserialize the CSV stream as an IEnumerable
    
            response = JsonConvert.SerializeObject(records); //serialize the IEnumerable back into JSON
        }    

        return response == null
            ? req.CreateResponse(HttpStatusCode.BadRequest, "There was an issue getting data")
            : req.CreateResponse(HttpStatusCode.OK, response);
     }  
    ```

4. Sla de functie op.
5. Test de functie om te controleren of de code goed werkt. Ga naar het tabblad **testen** in het rechterdeel venster en configureer de test als volgt. Plaats een koppeling naar een blob met voorbeeld gegevens in het tekstvak **hoofd tekst van aanvraag** . Nadat u op **uitvoeren** hebt geklikt, ziet u JSON-uitvoer in het vak **uitvoer** :

    ![Test code functie-apps](./media/create-pipeline-datacollector-api/functions-test-01.png)

Nu moet u teruggaan en de logische app wijzigen die u eerder hebt gemaakt om de gegevens op te nemen die zijn opgenomen en geconverteerd naar de JSON-indeling.  Met behulp van View Designer configureert u als volgt en slaat u vervolgens uw logische app op:

![Voor beeld van Logic Apps werk stroom voltooid](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>De pijp lijn testen
U kunt nu een nieuw bestand uploaden naar de blob die u eerder hebt geconfigureerd en deze laten controleren door uw logische app. Binnenkort ziet u een nieuw exemplaar van de logische app-start, neemt u contact op met uw Azure-functie en verzendt u de gegevens naar Azure Monitor. 

>[!NOTE]
>Het kan tot 30 minuten duren voordat de gegevens worden weer gegeven in Azure Monitor de eerste keer dat u een nieuw gegevens type verzendt.


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>Correleren met andere gegevens in Log Analytics en Application Insights
Als u het doel van het correleren van Application Insights pagina weergave gegevens wilt volt ooien met de populatie gegevens uit onze aangepaste gegevens bron, voert u de volgende query uit vanuit uw Application Insights Analytics-venster of Log Analytics-werk ruimte:

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

In de uitvoer ziet u dat de twee gegevens bronnen nu zijn gekoppeld.  

![Een voor beeld van het samen voegen van gegevens in een Zoek resultaat](./media/create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>Voorgestelde verbeteringen voor een productie pijplijn
In dit artikel wordt een werkend model gepresenteerd, de logica die kan worden toegepast op een echte oplossing voor productie kwaliteit. Voor een dergelijke oplossing van productie kwaliteit worden de volgende verbeteringen aanbevolen:

* Voeg fout afhandeling toe en voer de logica opnieuw uit in uw logische app en functie.
* Voeg logica toe om ervoor te zorgen dat de aanroep limiet van de 30MB/single Log Analytics opname-API niet wordt overschreden. Splits de gegevens zo nodig op in kleinere segmenten.
* Stel een opschoon beleid in voor de Blob-opslag. Als het verzenden naar de Log Analytics-werk ruimte is voltooid, tenzij u de onbewerkte gegevens beschikbaar wilt houden voor archiverings doeleinden, is er geen reden om deze te blijven opslaan. 
* Controleer of de controle is ingeschakeld in de volledige pijp lijn en voeg indien nodig tracerings punten en waarschuwingen toe.
* Gebruik broncode beheer voor het beheren van de code voor uw functie en logische app.
* Zorg ervoor dat het juiste beleid voor wijzigings beheer wordt gevolgd, zodat de functie en de Logic Apps dienovereenkomstig worden gewijzigd als het schema wordt gewijzigd.
* Als u meerdere verschillende gegevens typen uploadt, kunt u ze scheiden in afzonderlijke mappen binnen de BLOB-container en logica maken om de logica uit te waaieren op basis van het gegevens type. 


## <a name="next-steps"></a>Volgende stappen
Meer informatie over de  [Data Collector-API](data-collector-api.md) voor het schrijven van gegevens naar log Analytics werk ruimte vanuit een wille keurige rest API-client.
