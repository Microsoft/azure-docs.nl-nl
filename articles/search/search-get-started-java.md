---
title: "Quickstart: Een zoekindex maken in Java met behulp van REST API's"
titleSuffix: Azure Cognitive Search
description: In deze Java-quickstart leert u hoe u een index maakt, gegevens laadt en query's uitvoert met behulp van de REST API's van Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: java
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: devx-track-java
ms.openlocfilehash: 8c688b1ba80050c49b9e2a36696ed7a2fb863e3f
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089390"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-java-using-rest-apis"></a>Quickstart: Een Azure Cognitive Search-index maken in Java met behulp van REST API's
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-javascript.md)
> * [C#](search-get-started-dotnet.md)
> * [Java](search-get-started-java.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](./search-get-started-powershell.md)
> * [Python](search-get-started-python.md)
> * [REST](search-get-started-rest.md)

Maak een Java-consoletoepassing waarmee een zoekindex wordt gemaakt en geladen en daarop een query wordt uitgevoerd met behulp van [IntelliJ](https://www.jetbrains.com/idea/), [Java 11 SDK](/java/azure/jdk/) en de [REST API van Azure Cognitive Search](/rest/api/searchservice/). In dit artikel vindt u stapsgewijze instructies voor het maken van de toepassing. U kunt ook [de complete toepassing downloaden en uitvoeren](https://developers.google.com/sheets/api/quickstart/java).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

De volgende software en services zijn gebruikt om deze quickstart te maken en te testen:

+ [IntelliJ IDEA](https://www.jetbrains.com/idea/)

+ [SDK voor Java 11](/java/azure/jdk/)

+ [Maak een Azure Cognitive Search-service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt een gratis service voor deze quickstart gebruiken.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

Voor aanroepen naar de service zijn voor elke aanvraag een URL-eindpunt en een toegangssleutel vereist. Een zoekservice wordt gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, volgt u deze stappen om de benodigde informatie op te halen:

1. [Meld u aan bij Azure Portal](https://portal.azure.com/) en haal op de pagina **Overzicht** van uw zoekservice de URL op. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

2. Haal onder **Instellingen** > **Sleutels** een beheersleutel op voor volledige rechten op de service. Er zijn twee uitwisselbare beheersleutels die voor bedrijfscontinuïteit worden verstrekt voor het geval u een moet overschakelen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   Maak ook een querysleutel. Het is een aanbevolen procedure voor het uitgeven van queryaanvragen met alleen-lezen-toegang.

:::image type="content" source="media/search-get-started-javascript/service-name-and-keys.png" alt-text="De naam van de service en de querysleutels voor beheer ophalen" border="false":::

Voor elke aanvraag die naar uw service is verzonden, hebt u een API-sleutel nodig. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="set-up-your-environment"></a>Uw omgeving instellen

Open eerst IntelliJ IDEA en stel een nieuw project in.

### <a name="create-the-project"></a>Het project maken

1. Open IntelliJ IDEA en selecteer **Nieuw project maken**.
1. Selecteer **Maven**.
1. Selecteer de Java 11 SDK in de **Project SDK**-lijst.

    :::image type="content" source="media/search-get-started-java/java-quickstart-create-new-maven-project.png" alt-text="Een Maven-project maken" border="false":::

1. Voer voor **GroupId** en **ArtifactId** `AzureSearchQuickstart` in.
1. Accepteer de resterende standaarden om het project te openen.

### <a name="specify-maven-dependencies"></a>Maven-afhankelijkheden opgeven

1. Selecteer **Bestand** > **Instellingen**.
1. Selecteer in het venster **Instellingen** **Bouwen, uitvoeren, implementeren** > **Hulpprogramma's voor bouwen** > **Maven** > **Importeren**.
1. Schakel het selectievakje **Maven-projecten automatisch importeren** in en klik op **OK** om het venster te sluiten. Maven-invoegtoepassingen en andere afhankelijkheden worden nu automatisch gesynchroniseerd wanneer u het bestand pom.xml bijwerkt in de volgende stap.

    :::image type="content" source="media/search-get-started-java/java-quickstart-settings-import-maven-auto.png" alt-text="Maven-importeeropties in IntelliJ-instellingen" border="false":::

1. Open het bestand pom.xml en vervang de inhoud door de volgende Maven-configuratiedetails. Dit is inclusief verwijzingen naar de [Exec Maven-invoegtoepassing](https://www.mojohaus.org/exec-maven-plugin/) en een [JSON interface-API](https://javadoc.io/doc/org.glassfish/javax.json/1.0.2)

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
    
        <groupId>AzureSearchQuickstart</groupId>
        <artifactId>AzureSearchQuickstart</artifactId>
        <version>1.0-SNAPSHOT</version>
        <build>
            <sourceDirectory>src</sourceDirectory>
            <plugins>
                <plugin>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.1</version>
                    <configuration>
                        <source>11</source>
                        <target>11</target>
                    </configuration>
                </plugin>
                <plugin>
                    <groupId>org.codehaus.mojo</groupId>
                    <artifactId>exec-maven-plugin</artifactId>
                    <version>1.6.0</version>
                    <executions>
                        <execution>
                            <goals>
                                <goal>exec</goal>
                            </goals>
                        </execution>
                    </executions>
                    <configuration>
                        <mainClass>main.java.app.App</mainClass>
                        <cleanupDaemonThreads>false</cleanupDaemonThreads>
                    </configuration>
                </plugin>
            </plugins>
        </build>
        <dependencies>
            <dependency>
                <groupId>org.glassfish</groupId>
                <artifactId>javax.json</artifactId>
                <version>1.0.2</version>
            </dependency>
        </dependencies>   
    </project>
    ```

### <a name="set-up-the-project-structure"></a>De projectstructuur instellen

1. Selecteer **Bestand** > **Projectstructuur**.
1. Selecteer **Modules** en vouw de bronstructuur uit voor toegang tot de inhoud van de map `src` >  `main`.
1. Voeg in de map `src` >  `main` > `java` de mappen `app` en `service` toe. Selecteer hiervoor de map `java`, druk op Alt + Insert en voer vervolgens de mapnaam in.
1. Voeg in de map `src` >  `main` >`resources` de mappen `app` en `service` toe.

    Als u klaar bent, moet de projectstructuur er als in de volgende afbeelding uitzien.

    :::image type="content" source="media/search-get-started-java/java-quickstart-basic-code-tree.png" alt-text="De structuur van de projectmap" border="false":::

1. Klik op **OK** om het venster te sluiten.

### <a name="add-azure-cognitive-search-service-information"></a>Informatie over de Azure Cognitive Search-service toevoegen

1. Vouw in het venster **Project** de bronstructuur uit voor toegang tot de map `src` >  `main` >`resources` > `app` en voeg een `config.properties`-bestand toe. Selecteer hiervoor de map `app`, druk op Alt + Insert, selecteer **Bestand** en voer vervolgens de bestandsnaam in.

1. Kopieer de volgende instellingen naar het nieuwe bestand en vervang `<YOUR-SEARCH-SERVICE-NAME>`, `<YOUR-ADMIN-KEY>` en `<YOUR-QUERY-KEY>` door uw servicenaam en sleutels. Als uw service-eindpunt `https://mydemo.search.windows.net` is, wordt de servicenaam `"mydemo"`.

    ```java
        SearchServiceName=<YOUR-SEARCH-SERVICE-NAME>
        SearchServiceAdminKey=<YOUR-ADMIN-KEY>
        SearchServiceQueryKey=<YOUR-QUERY-KEY>
        IndexName=hotels-quickstart
        ApiVersion=2020-06-30
    ```

### <a name="add-the-main-method"></a>De hoofdmethode toevoegen

1. Voeg in de map `src` >  `main` > `java` > `app` een `App`-klasse toe. Selecteer hiervoor de `app`-map, druk op Alt + Insert, selecteer **Java-klasse** en voer vervolgens de naam van de klasse in.
1. Open de `App`-klasse en vervang de inhoud door de volgende code. Deze code bevat de `main`-methode. 

    De code, zonder opmerkingen, leest de parameters van de zoekservice en gebruikt deze vervolgens om een instantie van de zoekserviceclient te maken. De zoekserviceclientcode wordt in de volgende sectie toegevoegd.

    In een latere sectie van deze quickstart worden de opmerkingen bij deze code zonder commentaar in deze klasse verwijderd.

    ```java
    package main.java.app;
    
    import main.java.service.SearchServiceClient;
    import java.io.IOException;
    import java.util.Properties;
    
    public class App {
    
        private static Properties loadPropertiesFromResource(String resourcePath) throws IOException {
            var inputStream = App.class.getResourceAsStream(resourcePath);
            var configProperties = new Properties();
            configProperties.load(inputStream);
            return configProperties;
        }
    
        public static void main(String[] args) {
            try {
                var config = loadPropertiesFromResource("/app/config.properties");
                var client = new SearchServiceClient(
                        config.getProperty("SearchServiceName"),
                        config.getProperty("SearchServiceAdminKey"),
                        config.getProperty("SearchServiceQueryKey"),
                        config.getProperty("ApiVersion"),
                        config.getProperty("IndexName")
                );
    
    
    //Uncomment the next 3 lines in the 1 - Create Index section of the quickstart
    //            if(client.indexExists()){ client.deleteIndex();}
    //            client.createIndex("/service/index.json");
    //            Thread.sleep(1000L); // wait a second to create the index
    
    //Uncomment the next 2 lines in the 2 - Load Documents section of the quickstart
    //            client.uploadDocuments("/service/hotels.json");
    //            Thread.sleep(2000L); // wait 2 seconds for data to upload
    
    //Uncomment the following 5 search queries in the 3 - Search an index section of the quickstart
    //            // Query 1
    //            client.logMessage("\n*QUERY 1****************************************************************");
    //            client.logMessage("Search for: Atlanta'");
    //            client.logMessage("Return: All fields'");
    //            client.searchPlus("Atlanta");
    //
    //            // Query 2
    //            client.logMessage("\n*QUERY 2****************************************************************");
    //            client.logMessage("Search for: Atlanta");
    //            client.logMessage("Return: HotelName, Tags, Address");
    //            SearchServiceClient.SearchOptions options2 = client.createSearchOptions();
    //            options2.select = "HotelName,Tags,Address";
    //            client.searchPlus("Atlanta", options2);
    //
    //            //Query 3
    //            client.logMessage("\n*QUERY 3****************************************************************");
    //            client.logMessage("Search for: wifi & restaurant");
    //            client.logMessage("Return: HotelName, Description, Tags");
    //            SearchServiceClient.SearchOptions options3 = client.createSearchOptions();
    //            options3.select = "HotelName,Description,Tags";
    //            client.searchPlus("wifi,restaurant", options3);
    //
    //            // Query 4 -filtered query
    //            client.logMessage("\n*QUERY 4****************************************************************");
    //            client.logMessage("Search for: all");
    //            client.logMessage("Filter: Ratings greater than 4");
    //            client.logMessage("Return: HotelName, Rating");
    //            SearchServiceClient.SearchOptions options4 = client.createSearchOptions();
    //            options4.filter="Rating%20gt%204";
    //            options4.select = "HotelName,Rating";
    //            client.searchPlus("*",options4);
    //
    //            // Query 5 - top 2 results, ordered by
    //            client.logMessage("\n*QUERY 5****************************************************************");
    //            client.logMessage("Search for: boutique");
    //            client.logMessage("Get: Top 2 results");
    //            client.logMessage("Order by: Rating in descending order");
    //            client.logMessage("Return: HotelId, HotelName, Category, Rating");
    //            SearchServiceClient.SearchOptions options5 = client.createSearchOptions();
    //            options5.top=2;
    //            options5.orderby = "Rating%20desc";
    //            options5.select = "HotelId,HotelName,Category,Rating";
    //            client.searchPlus("boutique", options5);
    
            } catch (Exception e) {
                System.err.println("Exception:" + e.getMessage());
                e.printStackTrace();
            }
        }
    }
    ```

### <a name="add-the-http-operations"></a>De HTTP-bewerkingen toevoegen

1. Voeg in de map `src` >  `main` > `java` > `service` een `SearchServiceClient`-klasse toe. Selecteer hiervoor de `service`-map, druk op Alt + Insert, selecteer **Java-klasse** en voer vervolgens de naam van de klasse in.
1. Open de `SearchServiceClient`-klasse en vervang de inhoud door de volgende code. Deze code biedt de HTTP-bewerkingen die zijn vereist voor het gebruik van de REST API van Azure Cognitive Search. In een latere sectie worden aanvullende methoden toegevoegd voor het maken van een index, het uploaden van documenten en het uitvoeren van een query op de index.

    ```java
    package main.java.service;

    import javax.json.Json;
    import javax.net.ssl.HttpsURLConnection;
    import java.io.IOException;
    import java.io.StringReader;
    import java.net.HttpURLConnection;
    import java.net.URI;
    import java.net.http.HttpClient;
    import java.net.http.HttpRequest;
    import java.net.http.HttpResponse;
    import java.nio.charset.StandardCharsets;
    import java.util.Formatter;
    import java.util.function.Consumer;
    
        /* This class is responsible for implementing HTTP operations for creating the index, uploading documents and searching the data*/
        public class SearchServiceClient {
            private final String _adminKey;
            private final String _queryKey;
            private final String _apiVersion;
            private final String _serviceName;
            private final String _indexName;
            private final static HttpClient client = HttpClient.newHttpClient();
    
        public SearchServiceClient(String serviceName, String adminKey, String queryKey, String apiVersion, String indexName) {
            this._serviceName = serviceName;
            this._adminKey = adminKey;
            this._queryKey = queryKey;
            this._apiVersion = apiVersion;
            this._indexName = indexName;
        }

        private static HttpResponse<String> sendRequest(HttpRequest request) throws IOException, InterruptedException {
            logMessage(String.format("%s: %s", request.method(), request.uri()));
            return client.send(request, HttpResponse.BodyHandlers.ofString());
        }

        private static URI buildURI(Consumer<Formatter> fmtFn)
                {
                    Formatter strFormatter = new Formatter();
                    fmtFn.accept(strFormatter);
                    String url = strFormatter.out().toString();
                    strFormatter.close();
                    return URI.create(url);
        }
    
        public static void logMessage(String message) {
            System.out.println(message);
        }
    
        public static boolean isSuccessResponse(HttpResponse<String> response) {
            try {
                int responseCode = response.statusCode();
    
                logMessage("\n Response code = " + responseCode);
    
                if (responseCode == HttpURLConnection.HTTP_OK || responseCode == HttpURLConnection.HTTP_ACCEPTED
                        || responseCode == HttpURLConnection.HTTP_NO_CONTENT || responseCode == HttpsURLConnection.HTTP_CREATED) {
                    return true;
                }
    
                // We got an error
                var msg = response.body();
                if (msg != null) {
                    logMessage(String.format("\n MESSAGE: %s", msg));
                }
    
            } catch (Exception e) {
                e.printStackTrace();
            }
    
            return false;
        }
    
        public static HttpRequest httpRequest(URI uri, String key, String method, String contents) {
            contents = contents == null ? "" : contents;
            var builder = HttpRequest.newBuilder();
            builder.uri(uri);
            builder.setHeader("content-type", "application/json");
            builder.setHeader("api-key", key);
    
            switch (method) {
                case "GET":
                    builder = builder.GET();
                    break;
                case "HEAD":
                    builder = builder.GET();
                    break;
                case "DELETE":
                    builder = builder.DELETE();
                    break;
                case "PUT":
                    builder = builder.PUT(HttpRequest.BodyPublishers.ofString(contents));
                    break;
                case "POST":
                    builder = builder.POST(HttpRequest.BodyPublishers.ofString(contents));
                    break;
                default:
                    throw new IllegalArgumentException(String.format("Can't create request for method '%s'", method));
            }
            return builder.build();
        }
    }
    
    ```

### <a name="build-the-project"></a>Het project bouwen

1. Controleer of uw project de volgende structuur heeft.

    :::image type="content" source="media/search-get-started-java/java-quickstart-basic-code-tree-plus-classes.png" alt-text="De projectmapstructuur en klassen" border="false":::

1. Open het venster van het **Maven**-hulpprogramma en voer deze Maven-doelstelling uit: `verify exec:java`
:::image type="content" source="media/search-get-started-java/java-quickstart-execute-maven-goal.png" alt-text="Execute maven goal: verify exec:java" border="false":::

Wanneer de verwerking is voltooid, zoekt u het bericht BUILD SUCCESS, gevolgd door de afsluitcode nul (0).

## <a name="1---create-index"></a>1 - Index maken

De definitie van de hotelsindex bevat eenvoudige velden en één complex veld. Voorbeelden van een eenvoudige veld zijn HotelName of Description. Het veld Address is een complex veld omdat dit veld subvelden bevat, zoals Adres en Woonplaats. In deze quickstart wordt de indexdefinitie opgegeven met behulp van JSON.

1. Vouw in het venster **Project** de bronstructuur uit voor toegang tot de map `src` >  `main` >`resources` > `service` en voeg een `index.json`-bestand toe. Selecteer hiervoor de map `app`, druk op Alt + Insert, selecteer **Bestand** en voer vervolgens de bestandsnaam in.

1. Open het bestand `index.json` en voeg de volgende indexdefinitie in.

    ```json
    {
      "name": "hotels-quickstart",
      "fields": [
        {
          "name": "HotelId",
          "type": "Edm.String",
          "key": true,
          "filterable": true
        },
        {
          "name": "HotelName",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": true,
          "facetable": false
        },
        {
          "name": "Description",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": false,
          "facetable": false,
          "analyzer": "en.lucene"
        },
        {
          "name": "Description_fr",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": false,
          "facetable": false,
          "analyzer": "fr.lucene"
        },
        {
          "name": "Category",
          "type": "Edm.String",
          "searchable": true,
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Tags",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "filterable": true,
          "sortable": false,
          "facetable": true
        },
        {
          "name": "ParkingIncluded",
          "type": "Edm.Boolean",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "LastRenovationDate",
          "type": "Edm.DateTimeOffset",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Rating",
          "type": "Edm.Double",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Address",
          "type": "Edm.ComplexType",
          "fields": [
            {
              "name": "StreetAddress",
              "type": "Edm.String",
              "filterable": false,
              "sortable": false,
              "facetable": false,
              "searchable": true
            },
            {
              "name": "City",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "StateProvince",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "PostalCode",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "Country",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            }
          ]
        }
      ]
    }
    ```

    De indexnaam is hotels-quickstart. Kenmerken in de indexvelden bepalen hoe de geïndexeerde gegevens kunnen worden gezocht in een toepassing. Het kenmerk `IsSearchable` moet bijvoorbeeld worden toegewezen aan elk veld dat moet worden opgenomen in een zoekopdracht naar volledige tekst. Zie [Verzameling van velden en veldkenmerken](search-what-is-an-index.md#fields-collection) voor meer informatie over kenmerken.
    
    Voor het `Description`-veld in deze index wordt de optionele `analyzer`-eigenschap gebruikt om de standaard Lucene-taalanalysefuncties te overschrijven. Voor het veld `Description_fr` wordt de Franse Lucene-analyse `fr.lucene` gebruikt, omdat hierin Franse tekst wordt opgeslagen. Voor `Description` wordt de optionele Microsoft-taalanalysefunctie en.lucene gebruikt. Zie [Analysefuncties voor tekstverwerking in Azure Cognitive Search](search-analyzers.md) voor meer informatie over analysefuncties.

1. Voeg de volgende code toe aan de klasse `SearchServiceClient`. Met behulp van deze methoden worden Azure Cognitive Search REST-service-URL's gebouwd om een index te maken en te verwijderen en om te bepalen of er een index bestaat. Met deze methoden wordt ook de HTTP-aanvraag gemaakt.

    ```java
    public boolean indexExists() throws IOException, InterruptedException {
        logMessage("\n Checking if index exists...");
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s/docs?api-version=%s&search=*",
                _serviceName,_indexName,_apiVersion));
        var request = httpRequest(uri, _adminKey, "HEAD", "");
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    
    public boolean deleteIndex() throws IOException, InterruptedException {
        logMessage("\n Deleting index...");
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        var request = httpRequest(uri, _adminKey, "DELETE", "*");
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    
    
    public boolean createIndex(String indexDefinitionFile) throws IOException, InterruptedException {
        logMessage("\n Creating index...");
        //Build the search service URL
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        //Read in index definition file
        var inputStream = SearchServiceClient.class.getResourceAsStream(indexDefinitionFile);
        var indexDef = new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        //Send HTTP PUT request to create the index in the search service
        var request = httpRequest(uri, _adminKey, "PUT", indexDef);
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    ```

1. Verwijder opmerkingen uit de volgende code in de `App`-klasse. Met deze code wordt de index hotels-quickstart (als deze bestaat) verwijderd en wordt een nieuwe index gemaakt op basis van de indexdefinitie in het bestand index.json. 

    Na de aanvraag voor het maken van de index wordt een pauze van één seconde ingevoegd. Door deze pauze zorgt u ervoor dat de index wordt gemaakt voordat u documenten uploadt.

    ```java
        if (client.indexExists()) { client.deleteIndex();}
          client.createIndex("/service/index.json");
          Thread.sleep(1000L); // wait a second to create the index
    ```

1. Open het venster van het **Maven**-hulpprogramma en voer deze Maven-doelstelling uit: `verify exec:java`

    Wanneer de code wordt uitgevoerd, zoekt u het bericht Index wordt gemaakt, gevolgd door een 201-antwoordcode. Deze antwoordcode bevestigt dat de index is gemaakt. De uitvoering moet eindigen met het bericht BUILD SUCCESS en de afsluitcode nul (0).
    
## <a name="2---load-documents"></a>2 - Documenten laden

1. Vouw in het venster **Project** de bronstructuur uit voor toegang tot de map `src` >  `main` >`resources` > `service` en voeg een `hotels.json`-bestand toe. Selecteer hiervoor de map `app`, druk op Alt + Insert, selecteer **Bestand** en voer vervolgens de bestandsnaam in.
1. Voeg de volgende hoteldocumenten toe in het bestand.

    ```json
    {
      "value": [
        {
          "@search.action": "upload",
          "HotelId": "1",
          "HotelName": "Secret Point Motel",
          "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
          "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
          "Category": "Boutique",
          "Tags": [ "pool", "air conditioning", "concierge" ],
          "ParkingIncluded": "false",
          "LastRenovationDate": "1970-01-18T00:00:00Z",
          "Rating": 3.60,
          "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "2",
          "HotelName": "Twin Dome Motel",
          "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
          "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
          "Category": "Boutique",
          "Tags": [ "pool", "free wifi", "concierge" ],
          "ParkingIncluded": "false",
          "LastRenovationDate": "1979-02-18T00:00:00Z",
          "Rating": 3.60,
          "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "3",
          "HotelName": "Triple Landscape Hotel",
          "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
          "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
          "Category": "Resort and Spa",
          "Tags": [ "air conditioning", "bar", "continental breakfast" ],
          "ParkingIncluded": "true",
          "LastRenovationDate": "2015-09-20T00:00:00Z",
          "Rating": 4.80,
          "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "4",
          "HotelName": "Sublime Cliff Hotel",
          "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
          "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
          "Category": "Boutique",
          "Tags": [ "concierge", "view", "24-hour front desk service" ],
          "ParkingIncluded": "true",
          "LastRenovationDate": "1960-02-06T00:00:00Z",
          "Rating": 4.60,
          "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
          }
        }
      ]
    }
    ```

1. Voeg de volgende code toe aan de `SearchServiceClient`-klasse. Met deze code wordt de REST-service-URL gebouwd voor het uploaden van de hoteldocumenten naar de index, en vervolgens wordt de HTTP POST-aanvraag gemaakt.

    ```java
    public boolean uploadDocuments(String documentsFile) throws IOException, InterruptedException {
        logMessage("\n Uploading documents...");
        //Build the search service URL
        var endpoint = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s/docs/index?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        //Read in the data to index
        var inputStream = SearchServiceClient.class.getResourceAsStream(documentsFile);
        var documents = new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        //Send HTTP POST request to upload and index the data
        var request = httpRequest(endpoint, _adminKey, "POST", documents);
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    ```

1. Verwijder opmerkingen uit de volgende code in de `App`-klasse. Met deze code worden de documenten in hotels.json geüpload naar de index.

    ```java
    client.uploadDocuments("/service/hotels.json");
    Thread.sleep(2000L); // wait 2 seconds for data to upload
    ```

    Na de uploadaanvraag wordt een pauze van twee seconden ingevoegd om ervoor te zorgen dat het proces voor het laden van documenten wordt voltooid voordat u een query op de index uitvoert.

1. Open het venster van het **Maven**-hulpprogramma en voer deze Maven-doelstelling uit: `verify exec:java`

    Omdat u in de vorige stap een hotels-quickstart-index hebt gemaakt, wordt deze nu door de code verwijderd en wordt deze index opnieuw gemaakt voordat de hoteldocumenten worden geladen.

    Wanneer de code wordt uitgevoerd, zoekt u het bericht Documenten worden geüpload, gevolgd door een 200-antwoordcode. Deze antwoordcode bevestigt dat de documenten naar de index zijn geüpload. De uitvoering moet eindigen met het bericht BUILD SUCCESS en de afsluitcode nul (0).

## <a name="3---search-an-index"></a>3 - Een index doorzoeken

Nu u de hoteldocumenten hebt geladen, kunt u zoekquery's maken om toegang te krijgen tot de hotelgegevens.

1. Voeg de volgende code toe aan de klasse `SearchServiceClient`. Met deze code worden Azure Cognitive Search REST-service-URL's gebouwd om de geïndexeerde gegevens te doorzoeken en worden de zoekresultaten afgedrukt.

    Met behulp van de `SearchOptions`-klasse en `createSearchOptions`-methode kunt u een subset van de beschikbare query-opties voor de REST API van Azure Cognitive Search opgeven. Zie [Zoeken in documenten (REST API van Azure Cognitive Search)](/rest/api/searchservice/search-documents) voor meer informatie over de query-opties voor REST API's.

    Met de `SearchPlus`-methode worden de zoekquery-URL en de zoekaanvraag gemaakt en worden vervolgens de resultaten afgedrukt op de console. 

    ```java
    public SearchOptions createSearchOptions() { return new SearchOptions();}

    //Defines available search parameters that can be set
    public static class SearchOptions {

        public String select = "";
        public String filter = "";
        public int top = 0;
        public String orderby= "";
    }

    //Concatenates search parameters to append to the search request
    private String createOptionsString(SearchOptions options)
    {
        String optionsString = "";
        if (options != null) {
            if (options.select != "")
                optionsString = optionsString + "&$select=" + options.select;
            if (options.filter != "")
                optionsString = optionsString + "&$filter=" + options.filter;
            if (options.top != 0)
                optionsString = optionsString + "&$top=" + options.top;
            if (options.orderby != "")
                optionsString = optionsString + "&$orderby=" +options.orderby;
        }
        return optionsString;
    }
    
    public void searchPlus(String queryString)
    {
        searchPlus( queryString, null);
    }
    
    public void searchPlus(String queryString, SearchOptions options) {
    
        try {
            String optionsString = createOptionsString(options);
            var uri = buildURI(strFormatter -> strFormatter.format(
                    "https://%s.search.windows.net/indexes/%s/docs?api-version=%s&search=%s%s",
                    _serviceName, _indexName, _apiVersion, queryString, optionsString));
            var request = httpRequest(uri, _queryKey, "GET", null);
            var response = sendRequest(request);
            var jsonReader = Json.createReader(new StringReader(response.body()));
            var jsonArray = jsonReader.readObject().getJsonArray("value");
            var resultsCount = jsonArray.size();
            logMessage("Results:\nCount: " + resultsCount);
            for (int i = 0; i <= resultsCount - 1; i++) {
                logMessage(jsonArray.get(i).toString());
            }
    
            jsonReader.close();
    
        }
        catch (Exception e) {
            e.printStackTrace();
        }
    
    }
    ```

1. Verwijder in de `App`-klasse de opmerkingen uit de volgende code. Met deze code worden vijf verschillende query's ingesteld, waaronder de zoektekst, queryparameters en gegevensvelden die moeten worden geretourneerd. 

    ```java
    // Query 1
    client.logMessage("\n*QUERY 1****************************************************************");
    client.logMessage("Search for: Atlanta");
    client.logMessage("Return: All fields'");
    client.searchPlus("Atlanta");

    // Query 2
    client.logMessage("\n*QUERY 2****************************************************************");
    client.logMessage("Search for: Atlanta");
    client.logMessage("Return: HotelName, Tags, Address");
    SearchServiceClient.SearchOptions options2 = client.createSearchOptions();
    options2.select = "HotelName,Tags,Address";
    client.searchPlus("Atlanta", options2);

    //Query 3
    client.logMessage("\n*QUERY 3****************************************************************");
    client.logMessage("Search for: wifi & restaurant");
    client.logMessage("Return: HotelName, Description, Tags");
    SearchServiceClient.SearchOptions options3 = client.createSearchOptions();
    options3.select = "HotelName,Description,Tags";
    client.searchPlus("wifi,restaurant", options3);

    // Query 4 -filtered query
    client.logMessage("\n*QUERY 4****************************************************************");
    client.logMessage("Search for: all");
    client.logMessage("Filter: Ratings greater than 4");
    client.logMessage("Return: HotelName, Rating");
    SearchServiceClient.SearchOptions options4 = client.createSearchOptions();
    options4.filter="Rating%20gt%204";
    options4.select = "HotelName,Rating";
    client.searchPlus("*",options4);

    // Query 5 - top 2 results, ordered by
    client.logMessage("\n*QUERY 5****************************************************************");
    client.logMessage("Search for: boutique");
    client.logMessage("Get: Top 2 results");
    client.logMessage("Order by: Rating in descending order");
    client.logMessage("Return: HotelId, HotelName, Category, Rating");
    SearchServiceClient.SearchOptions options5 = client.createSearchOptions();
    options5.top=2;
    options5.orderby = "Rating%20desc";
    options5.select = "HotelId,HotelName,Category,Rating";
    client.searchPlus("boutique", options5);
    ```



    Er zijn twee [manieren om termen in een query te vergelijken](search-query-overview.md#types-of-queries): zoeken op volledige tekst en filters. Een query naar volledige tekst zoekt naar een of meer termen in `IsSearchable`-velden in de index. Een filter is een Booleaanse uitdrukking die wordt geëvalueerd over `IsFilterable`-velden in een index. U kunt zoeken op volledige tekst en filters samen of afzonderlijk gebruiken.

1. Open het venster van het **Maven**-hulpprogramma en voer deze Maven-doelstelling uit: `verify exec:java`

    Zoek een samenvatting van elke query en de bijbehorende resultaten. De uitvoering moet worden voltooid met het bericht BUILD SUCCESS en de afsluitcode nul (0).

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt en of u deze moet verwijderen. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling **Alle resources** of **Resourcegroepen** in het navigatiedeelvenster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, indexeerfuncties en gegevensbronnen. U kunt afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

In deze Java-quickstart hebt u een reeks taken uitgevoerd om een index te maken, hier documenten in te laden en query's uit te voeren. Als u vertrouwd bent met de basisconcepten, wordt het volgende artikel aanbevolen, waarin de bewerkingen van de indexeerfunctie in REST worden vermeld.

> [!div class="nextstepaction"]
> [Bewerkingen van de indexeerfunctie](/rest/api/searchservice/indexer-operations)