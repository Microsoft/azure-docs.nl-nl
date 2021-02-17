---
title: Aan de slag met document vertalingen
description: Een document Vertaal service maken met behulp van C#-, go-, Java-, Node.js-of python-programmeer talen en-platforms
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: f6b68ea756253a30f006983c214e287c75637278
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642374"
---
# <a name="get-started-with-document-translation-preview"></a>Aan de slag met document vertalingen (preview-versie)

 In dit artikel leert u hoe u document vertalingen met HTTP-REST API-methoden gebruikt. Document vertalingen is een Cloud functie van de [Azure Translator](../translator-info-overview.md) -service.  Met de API voor document vertalingen kan de vertaling van hele documenten worden omgezet met behoud van de structuur en tekst opmaak van de bron documenten.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een actief [**Azure-account**](https://azure.microsoft.com/free/cognitive-services/).  Als u er nog geen hebt, kunt u [**een gratis account maken**](https://azure.microsoft.com/free/).

* Een [**Translator**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) -service resource (**niet** een Cognitive Services resource). 

* Een [**Azure Blob-opslag account**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Alle toegang tot Azure Storage vindt plaats via een opslagaccount.

> [!NOTE]
> Document vertalingen worden momenteel alleen ondersteund in de Translator-resource (single-service), **niet** de Cognitive Services resource (meerdere services).

## <a name="get-your-custom-domain-name-and-subscription-key"></a>Uw aangepaste domein naam en abonnements sleutel ophalen

> [!IMPORTANT]
>
> * U kunt het eind punt dat u hebt gevonden op uw Azure Portal bron _sleutels en de eindpunt_ pagina of het globale Translator-eind punt niet gebruiken `api.cognitive.microsofttranslator.com` om HTTP-aanvragen voor document omzetting te maken.
> * **Voor alle API-aanvragen voor de document Vertaal service is een aangepast domein eindpunt vereist**.

### <a name="what-is-the-custom-domain-endpoint"></a>Wat is het aangepaste domein eindpunt? 

Het aangepaste domein-eind punt is een URL die is opgemaakt met uw resource naam, hostnaam en Translator-submappen:

```http
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1
```

### <a name="find-your-custom-domain-name"></a>Uw aangepaste domein naam zoeken

De para meter **naam-of-your-resource** (ook wel *aangepaste domein naam* genoemd) is de waarde die u hebt ingevoerd in het veld **naam** bij het maken van uw Translator-resource.

:::image type="content" source="../media/instance-details.png" alt-text="Afbeelding van de Azure Portal, resource maken, Details van het veld naam.":::

### <a name="get-your-subscription-key"></a>Uw abonnements sleutel ophalen

Aanvragen voor de Translator-service vereisen een alleen-lezen sleutel voor het verifiëren van de toegang.

1. Als u een nieuwe resource hebt gemaakt nadat u deze hebt geïmplementeerd, selecteert u **Ga naar resource**. Als u een bestaande resource voor document vertalingen hebt, gaat u rechtstreeks naar de resource pagina.
1. Selecteer in de rails onder *resource management* de optie **sleutels en eind punt**.
1. Kopieer en plak uw abonnements sleutel op een handige locatie, zoals *micro soft Klad blok*.
1. Plak deze in de onderstaande code om uw aanvraag te verifiëren bij de service voor document vertalingen.

:::image type="content" source="../media/translator-keys.png" alt-text="Afbeelding van het veld uw abonnements sleutel ophalen in Azure Portal.":::

## <a name="create-your-azure-blob-storage-containers"></a>Uw Azure Blob-opslag containers maken

U moet in uw [**Azure Blob Storage-account**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) [**containers maken**](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container) voor bron-, doel-en optionele woordenlijst bestanden.

* **Bron container**. In deze container uploadt u uw bestanden voor vertaling (vereist).
* **Doel container**. In deze container worden uw vertaalde bestanden opgeslagen (vereist).  
* **Container voor verklarende woorden lijst**. In deze container kunt u uw woordenlijst bestanden uploaden (optioneel).  

*Zie* **SAS-toegangs tokens maken voor document vertalingen**

De `sourceUrl` , `targetUrl` en optioneel `glossaryUrl`  moeten een Shared Access Signature-token (SAS) bevatten, toegevoegd als een query reeks. Het token kan worden toegewezen aan uw container of specifieke blobs.

* Uw **bron** container of BLOB moet de aangewezen  **Lees** -en **lijst** toegang hebben.
* Uw **doel** container of BLOB moet  **Schrijf** -en **lijst** toegang hebben.
* De container of BLOB van de **woorden lijst** moet de aangewezen  **Lees** -en **lijst** toegang hebben.

> [!TIP]
>
> * Als u **meerdere** bestanden (blobs) in een bewerking vertaalt, **delegeert u de SAS-toegang op het niveau van de container**.  
> * Als u **één** bestand (BLOB) vertaalt in een bewerking, **delegeert u SAS-toegang op het niveau van de BLOB**.  
>

## <a name="set-up-your-coding-platform"></a>Uw coderings platform instellen

### <a name="c"></a>[C#](#tab/csharp)

* Een nieuw project maken.
* Vervang Program.cs door de hieronder weergegeven C#-code.
* Stel uw eind punt in. abonnements sleutel en container-URL-waarden in Program.cs.
* Als u JSON-gegevens wilt verwerken, voegt [ uNewtonsoft.Jstoe aan het pakket met behulp van .net cli](https://www.nuget.org/packages/Newtonsoft.Json/).
* Voer het programma uit vanuit de projectmap.

### <a name="nodejs"></a>[Node.js](#tab/javascript)

* Een nieuw Node.js project maken.
* Installeer de Axios-bibliotheek met `npm i axios` .
* Kopieer de onderstaande code in uw project.
* Stel de waarden van uw eind punt, abonnements sleutel en container-URL in.
* Voer het programma uit.

### <a name="python"></a>[Python](#tab/python)  

* Een nieuw project maken.
* Kopieer en plak de code van een van de voor beelden in uw project.
* Stel de waarden van uw eind punt, abonnements sleutel en container-URL in.
* Voer het programma uit. Bijvoorbeeld: `python translate.py`.
  
### <a name="java"></a>[Java](#tab/java)

* Maak een werkmap voor uw project. Bijvoorbeeld:

```powershell
mkdir sample-project
```

* Maak in de projectmap de volgende mapstructuur:  

  src</br>
&emsp; └ Main</br>
&emsp;&emsp;&emsp;└ Java

```powershell
mkdir -p src/main/java/
```

* Java-bron bestanden (bijvoorbeeld _voorbeeld. java_) Live in src/main/**Java**.

* Initialiseer uw project met Gradle in uw hoofdmap (bijvoorbeeld *voorbeeld project*):

```powershell
gradle init --type basic
```

* Wanneer u wordt gevraagd om een **DSL** te kiezen, selecteert u **Kotlin**.
* Werk het `build.gradle.kts`  bestand bij. Houd er rekening mee dat u het volgende moet bijwerken `mainClassName` , afhankelijk van het voor beeld:

  ```java
  plugins {
    java
    application
  }
  application {
    mainClassName = "{NAME OF YOUR CLASS}"
  }
  repositories {
    mavenCentral()
  }
  dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
  }
  ```

* Maak een Java-bestand in de **Java** -map en kopieer/plak de code uit het voor beeld. Vergeet niet om uw abonnements sleutel en eind punt toe te voegen.
**Bouw en voer het voor beeld uit vanuit de hoofdmap**:

```powershell
gradle build
gradle run
```

### <a name="go"></a>[Go](#tab/go)  

* Maak een nieuw go-project.
* Voeg de onderstaande code toe.
* Stel de waarden van uw eind punt, abonnements sleutel en container-URL in.
* Sla het bestand op met de extensie .go.
* Open een opdrachtprompt op een computer waarop Go is geïnstalleerd.
* Compileer het bestand. Bijvoorbeeld: go build example-code.go.
* Voer het bestand uit, bijvoorbeeld: voorbeeldcode.

---

## <a name="make-document-translation-requests"></a>Aanvragen voor het vertalen van documenten maken

Er wordt een aanvraag voor een batch-document vertaling verzonden naar het Vertaal Service-eind punt via een POST-aanvraag. Als dit lukt, retourneert de POST-methode een `202 Accepted`  respons code en wordt de batch aanvraag gemaakt door de service.

### <a name="http-headers"></a>HTTP-headers

De volgende headers zijn opgenomen in elke document Translator API-aanvraag:

|HTTP-header|Description|
|---|--|
|Ocp-Apim-Subscription-Key|**Vereist**: de waarde is de Azure-abonnements sleutel voor uw Translator of Cognitive Services resource.|
|Content-Type|**Vereist**: Hiermee geeft u het inhouds type van de payload op. Geaccepteerde waarden zijn application/json of charset = UTF-8.|
|Content-Length|**Vereist**: de lengte van de hoofd tekst van de aanvraag.|

### <a name="post-request-body-properties"></a>Eigenschappen van hoofd tekst van POST-aanvraag

* De POST-aanvraag tekst is een JSON-object met de naam `inputs` .
* Het `inputs` object bevat zowel  `sourceURL` `targetURL`  containers als container adressen voor uw bron-en doel taal paren en kan optioneel een `glossaryURL` container adres bevatten.
* De `prefix` `suffix` velden en (optioneel) worden gebruikt voor het filteren van documenten in de container, inclusief mappen.
* Er wordt een waarde voor het  `glossaries`  veld (optioneel) toegepast wanneer het document wordt vertaald.
* De `targetUrl` voor elke doel taal moet uniek zijn.

>[!NOTE]
> Als er al een bestand met dezelfde naam in het doel bestaat, wordt dit overschreven.

### <a name="post-a-translation-request"></a>Een Vertaal aanvraag plaatsen

> [!IMPORTANT]
>
> * Voor de voorbeeld code moet u mogelijk de volgende velden bijwerken, afhankelijk van de bewerking:

>> [!div class="checklist"]
>>
>> * `endpoint`
>> * `subscriptionKey`
>> * `sourceURL`
>> * `targetURL`
>> * `glossaryURL`
>> * `id`  (taak-ID)
>>
> * U vindt de taak `id`  in de URL-waarde van de antwoord header van de methode post `Operation-Location`  . De laatste para meter van de URL is de taak van de bewerking **`id`** .  
> * U kunt ook een aanvraag voor het ophalen van taken gebruiken om de taak `id`  voor een document Vertaal bewerking op te halen.
> * In de onderstaande voor beelden kunt u uw sleutel en het eind punt op de aangegeven wijze in een code vastleggen. Vergeet niet om de sleutel uit uw code te verwijderen wanneer u klaar bent en deze nooit openbaar te plaatsen.  
>
> Zie [Azure Cognitive Services Security](/azure/cognitive-services/cognitive-services-security?tabs=command-line%2Ccsharp) voor manieren om uw referenties veilig op te slaan en te openen.

<!-- markdownlint-disable MD024 -->
### <a name="post-request-body-without-optional-glossaryurl"></a>POST-aanvraag hoofdtekst zonder optionele glossaryURL

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "<https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS>",
                "storageSource": "AzureBlob",
                "filter": {
                    "prefix": "News",
                    "suffix": ".txt"
                },
                "language": "en"
            },
            "targets": [
                {
                    "targetUrl": "<https://YOUR-SOURCE-URL-WITH-WRITE-LIST-ACCESS-SAS>",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "de"
                }
            ]
        }
    ]
}
```

### <a name="post-request-body-with-optional-glossaryurl"></a>POST-aanvraag tekst met optionele glossaryURL

```json
{
  "inputs":[
    {
      "source":{
        "sourceUrl":"<https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS>",
        "storageSource":"AzureBlob",
        "filter":{
          "prefix":"News",
          "suffix":".txt"
        },
        "language":"en"
      },
      "targets":[
        {
          "targetUrl":"<https://YOUR-SOURCE-URL-WITH-WRITE-LIST-ACCESS-SAS>",
          "storageSource":"AzureBlob",
          "category":"general",
          "language":"de",
          "glossaries":[
            {
              "glossaryUrl":"<https://YOUR-GLOSSARY-URL-WITH-READ-LIST-ACCESS-SAS>",
              "format":"xliff",
              "version":"1.2"
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="_post-document-translation_-request-code-samples"></a>Code voorbeelden van de aanvraag voor _document vertaling verzenden_

Een conversie aanvraag voor een batch-document verzenden naar de Vertaal service.

### <a name="c"></a>[C#](#tab/csharp)

```csharp

    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Text;
    

    class Program
    {

        static readonly string route = "/batches";

        private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

        private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

        static readonly string json = ("{\"inputs\": [{\"source\": {\"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",\"storageSource\": \"AzureBlob\",\"language\": \"en\",\"filter\":{\"prefix\": \"Demo_1/\"} }, \"targets\": [{\"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\"storageSource\": \"AzureBlob\",\"category\": \"general\",\"language\": \"es\"}]}]}");
        
        static async Task Main(string[] args)
        {
            using HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
            
                StringContent content = new StringContent(json, Encoding.UTF8, "application/json");

                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                request.Content = content;
                
                HttpResponseMessage  response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;
                if (response.IsSuccessStatusCode)
                {
                    Console.WriteLine($"Status code: {response.StatusCode}");
                    Console.WriteLine();
                    Console.WriteLine($"Response Headers:"); 
                    Console.WriteLine(response.Headers);
                }
                else
                    Console.Write("Error");

            }

        }

    }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios').default;

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let route = '/batches';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';

let data = JSON.stringify({"inputs": [
  {
      "source": {
          "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
          "storageSource": "AzureBlob",
          "language": "en",
          "filter":{
              "prefix": "Demo_1/"
          }
      },
      "targets": [
          {
              "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
              "storageSource": "AzureBlob",
              "category": "general",
              "language": "es"}]}]});

let config = {
  method: 'post',
  baseURL: endpoint,
  url: route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-Type': 'application/json'
  },
  data: data
};

axios(config)
.then(function (response) {
  let result = { statusText: response.statusText, statusCode: response.status, headers: response.headers };
  console.log()
  console.log(JSON.stringify(result));
})
.catch(function (error) {
  console.log(error);
});
```

### <a name="python"></a>[Python](#tab/python)

```python

import requests

endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
path = '/batches'
constructed_url = endpoint + path

payload= {
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
                "storageSource": "AzureBlob",
                "language": "en",
                "filter":{
                    "prefix": "Demo_1/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "es"
                }
            ]
        }
    ]
}
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey,
  'Content-Type': 'application/json'
}

response = requests.post(constructed_url, headers=headers, json=payload)

print(f'response status code: {response.status_code}\nresponse status: {response.reason}\nresponse headers: {response.headers}')
```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DocumentTranslation {
    String subscriptionKey = "'<YOUR-SUBSCRIPTION-KEY>'";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String path = endpoint + "/batches";

    OkHttpClient client = new OkHttpClient();

    public void post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,  "{\n \"inputs\": [\n {\n \"source\": {\n \"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS\",\n \"filter\": {\n  \"prefix\": \"Demo_1\"\n  },\n  \"language\": \"en\",\n \"storageSource\": \"AzureBlob\"\n  },\n \"targets\": [\n {\n \"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\n \"category\": \"general\",\n\"language\": \"fr\",\n\"storageSource\": \"AzureBlob\"\n }\n ],\n \"storageType\": \"Folder\"\n }\n  ]\n}");
        Request request = new Request.Builder()
                .url(path).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        System.out.println(response.code());
        System.out.println(response.headers());
    }

  public static void main(String[] args) {
        try {
            DocumentTranslation sampleRequest = new DocumentTranslation();
            sampleRequest.post();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
 "bytes"
 "fmt"
"net/http"
)

func main() {
endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
uri := endpoint + "/batches"
method := "POST"

var jsonStr = []byte(`{"inputs":[{"source":{"sourceUrl":"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS","storageSource":"AzureBlob","language":"en","filter":{"prefix":"Demo_1/"}},"targets":[{"targetUrl":"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS","storageSource":"AzureBlob","category":"general","language":"es"}]}]}`)

req, err := http.NewRequest(method, endpoint, bytes.NewBuffer(jsonStr))
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

client := &http.Client{}

if err != nil {
    fmt.Println(err)
    return
  }
  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()
  fmt.Println("response status:",  res.Status)
  fmt.Println("response headers",  res.Header)
}
```

---

## <a name="_get-file-formats_-code-samples"></a>Code voorbeelden voor _Bestands indelingen ophalen_

Een lijst met ondersteunde bestands indelingen ophalen. Als deze methode is geslaagd, wordt een `200 OK` antwoord code geretourneerd.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/documents/formats";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/documents/formats';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetFileFormats {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/documents/formats";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/documents/formats'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/documents/formats"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_get-job-status_-code-samples"></a>Voor beelden van _taak status code ophalen_

De huidige status ophalen voor één taak en een samen vatting van alle taken in een aanvraag voor document vertaling. Als deze methode is geslaagd, wordt een `200 OK` antwoord code geretourneerd.
<!-- markdownlint-disable MD024 -->

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetJobStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_get-document-status_-code-samples"></a>Voor beelden van _document status code ophalen_

### <a name="brief-overview"></a>Beknopt overzicht

De status van een specifiek document in een aanvraag voor document vertalingen ophalen. Als deze methode is geslaagd, wordt een `200 OK` antwoord code geretourneerd.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/{id}/document/{documentId}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/{id}/document/{documentId}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetDocumentStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/{id}/document/{documentId}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/{id}/document/{documentId}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/{id}/document/{documentId}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_delete-job_-code-samples"></a>_Taak_ code voorbeelden verwijderen

### <a name="brief-overview"></a>Beknopt overzicht

De huidige verwerking of taak in de wachtrij annuleren. Alleen documenten waarvan de vertaling nog niet is gestart, worden geannuleerd.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Delete;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'delete',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DeleteJob {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("DELETE", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("DELETE", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "DELETE"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="content-limits"></a>Inhouds beperkingen

De volgende tabel bevat de limieten voor gegevens die u naar document vertalingen verzendt.

|Kenmerk | Limiet|
|---|---|
|Document grootte| ≤ 40 MB |
|Totaal aantal bestanden.|≤ 1000 |
|Totale inhouds grootte in een batch | ≤ 250 MB|
|Aantal doel talen in een batch| ≤ 10 |
|Grootte van het Vertaal geheugen bestand| ≤ 10 MB|

> [!NOTE]
> De bovenstaande inhouds limieten zijn onderhevig aan wijzigingen vóór de open bare versie.

## <a name="learn-more"></a>Lees meer

* [Translator v3 API-verwijzing](../reference/v3-0-reference.md)
* [Taalondersteuning](../language-support.md)
* [Abonnementen in Azure API Management](/azure/api-management/api-management-subscriptions).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een aangepast taal systeem maken met aangepaste vertaler](../custom-translator/overview.md)
>
>
