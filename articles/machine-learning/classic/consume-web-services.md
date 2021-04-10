---
title: 'ML Studio (klassiek): webservices gebruiken-Azure'
description: Zodra een machine learning-service is geïmplementeerd vanuit Azure Machine Learning Studio (klassiek), kan de REST-webservice worden gebruikt als een real-time aanvraag-antwoord service of als een batch-uitvoerings service.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-python, devx-track-js, devx-track-csharp
ms.date: 05/29/2020
ms.openlocfilehash: 0dc49265c0ea799e194e4ac7004b558d8a9d4dd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519266"
---
# <a name="how-to-consume-a-machine-learning-studio-classic-web-service"></a>Een Machine Learning Studio-webservice (klassiek) gebruiken

**VAN TOEPASSING OP:**  ![Dit is een vinkje, wat betekent dat dit artikel van toepassing is op Machine Learning Studio (klassiek).](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassiek)   ![dit is een X, wat betekent dat dit artikel van toepassing is op Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Nadat u een Azure Machine Learning Studio (klassiek) voorspellend model als een webservice hebt geïmplementeerd, kunt u een REST API gebruiken om gegevens te verzenden en voor spellingen te ontvangen. U kunt de gegevens in realtime of in batch modus verzenden.

U vindt hier meer informatie over het maken en implementeren van een Machine Learning-webservice met behulp van Machine Learning Studio (klassiek):

* Zie [uw eerste experiment maken](create-experiment.md)voor een zelf studie over het maken van een experiment in machine learning Studio (klassiek).
* Zie [Deploy a machine learning web service](deploy-a-machine-learning-web-service.md)(Engelstalig) voor meer informatie over het implementeren van een webservice.
* Ga voor meer informatie over Machine Learning in het algemeen naar het [documentatie centrum van machine learning](https://azure.microsoft.com/documentation/services/machine-learning/).



## <a name="overview"></a>Overzicht
Met de Azure Machine Learning-webservice communiceert een externe toepassing met een Machine Learning werk stroom score model in realtime. Een aanroep van een Machine Learning-webservice retourneert Voorspellings resultaten naar een externe toepassing. Als u een aanroep van een Machine Learning-webservice wilt maken, geeft u een API-sleutel door die wordt gemaakt wanneer u een voor spelling implementeert. De Machine Learning-webservice is gebaseerd op REST, een populaire architectuur keuze voor webprogrammeer projecten.

Azure Machine Learning Studio (klassiek) heeft twee soorten services:

* Request-Response service (RR'S): een zeer schaal bare service met lage latentie die een interface biedt voor de stateless modellen die zijn gemaakt en geïmplementeerd via de Machine Learning Studio (klassiek).
* Batch Execution Service (BES): een asynchrone service die een batch voor gegevens records verlaagt.

Zie [Deploy a machine learning web service](deploy-a-machine-learning-web-service.md)(Engelstalig) voor meer informatie over machine learning Web Services.

## <a name="get-an-authorization-key"></a>Een autorisatie sleutel ophalen
Wanneer u uw experiment implementeert, worden de API-sleutels voor de webservice gegenereerd. U kunt de sleutels van verschillende locaties ophalen.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Van de Microsoft Azure Machine Learning webservices-Portal
Meld u aan bij de [Microsoft Azure machine learning Web Services](https://services.azureml.net) -Portal.

De API-sleutel voor een nieuwe Machine Learning-webservice ophalen:

1. Klik in het Azure Machine Learning Web Services-portal op **Web Services** in het bovenste menu.
2. Klik op de webservice waarvoor u de sleutel wilt ophalen.
3. Klik in het menu bovenaan op **verbruikt**.
4. Kopieer de **primaire sleutel** en sla deze op.

De API-sleutel voor een klassieke Machine Learning-webservice ophalen:

1. Klik in het Azure Machine Learning Web Services-portal op **klassieke webservices** in het bovenste menu.
2. Klik op de webservice waarmee u werkt.
3. Klik op het eind punt waarvoor u de sleutel wilt ophalen.
4. Klik in het menu bovenaan op **verbruikt**.
5. Kopieer de **primaire sleutel** en sla deze op.

### <a name="classic-web-service"></a>Klassieke webservice
 U kunt ook een sleutel voor een klassieke webservice ophalen van Machine Learning Studio (klassiek).

#### <a name="machine-learning-studio-classic"></a>Machine Learning Studio (klassiek)
1. Klik in Machine Learning Studio (klassiek) op **Web Services** aan de linkerkant.
2. Klik op een webservice. De **API-sleutel** bevindt zich op het tabblad **dash board** .

## <a name="connect-to-a-machine-learning-web-service"></a><a id="connect"></a>Verbinding maken met een Machine Learning-webservice
U kunt verbinding maken met een Machine Learning-webservice met elke programmeer taal die HTTP-aanvragen en-antwoorden ondersteunt. U kunt voor beelden in C#, python en R bekijken via de Help-pagina van een Machine Learning-webservice.

**Help bij machine learning-API** Machine Learning API Help wordt gemaakt wanneer u een webservice implementeert. Zie [zelf studie 3: een model voor het credit risico implementeren](tutorial-part3-credit-risk-deploy.md).
De Help van de Machine Learning-API bevat details over een Voorspellings webservice.

1. Klik op de webservice waarmee u werkt.
2. Klik op het eind punt waarvoor u de API Help-pagina wilt weer geven.
3. Klik in het menu bovenaan op **verbruikt**.
4. Klik op de **Help-pagina** van de API onder de Request-Response of batch Execution-eind punten.

**De Help van Machine Learning-API voor een nieuwe webservice weer geven**

In de [Azure machine learning Web Services-portal](https://services.azureml.net/):

1. Klik op **Web Services** in het bovenste menu.
2. Klik op de webservice waarvoor u de sleutel wilt ophalen.

Klik op **webservice gebruiken** om de uri's voor de Request-Response en batch Execution Services en voorbeeld code in C#, R en python op te halen.

Klik op **SWAGGER API** om op Swagger gebaseerde documentatie op te halen voor de api's die worden aangeroepen vanuit de opgegeven uri's.

### <a name="c-sample"></a>C#-voor beeld
Als u verbinding wilt maken met een Machine Learning-webservice, gebruikt u een **httpclient maakt** door gegeven ScoreData. ScoreData bevat een FeatureVector, een n-dimensionale vector van de numerieke functies waarmee de ScoreData wordt aangeduid. U bent geverifieerd bij de Machine Learning-service met een API-sleutel.

Als u verbinding wilt maken met een Machine Learning-webservice, moet u het NuGet-pakket **micro soft. AspNet. WebApi. client** installeren.

**Micro soft. AspNet. WebApi. client NuGet installeren in Visual Studio**

1. Publiceer de gegevensset voor downloaden van UCI: provider van de verzamelings gegevens van de volwassen 2-klasse.
2. Klik op **extra**  >  **NuGet package manager**  >  **Package Manager-console**.
3. Kies **install-package micro soft. AspNet. WebApi. client**.

**Het code voorbeeld uitvoeren**

1. Publiceren "voor beeld 1: gegevensset downloaden van UCI: volwassene 2 Class gegevensset" experiment, deel van de verzameling Machine Learning-voor beelden.
2. Wijs apiKey toe aan de sleutel van een webservice. Zie hierboven **een autorisatie sleutel ophalen** .
3. Wijs serviceUri toe met de aanvraag-URI.

**Hier ziet u hoe een volledige aanvraag eruitziet.**
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Formatting;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

namespace CallRequestResponseService
{
    class Program
    {
        static void Main(string[] args)
        {
            InvokeRequestResponseService().Wait();
        }

        static async Task InvokeRequestResponseService()
        {
            using (var client = new HttpClient())
            {
                var scoreRequest = new
                {
                    Inputs = new Dictionary<string, List<Dictionary<string, string>>> () {
                        {
                            "input1",
                            // Replace columns labels with those used in your dataset
                            new List<Dictionary<string, string>>(){new Dictionary<string, string>(){
                                    {
                                        "column1", "value1"
                                    },
                                    {
                                        "column2", "value2"
                                    },
                                    {
                                        "column3", "value3"
                                    }
                                }
                            }
                        },
                    },
                    GlobalParameters = new Dictionary<string, string>() {}
                };

                // Replace these values with your API key and URI found on https://services.azureml.net/
                const string apiKey = "<your-api-key>"; 
                const string apiUri = "<your-api-uri>";
                
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
                client.BaseAddress = new Uri(apiUri);

                // WARNING: The 'await' statement below can result in a deadlock
                // if you are calling this code from the UI thread of an ASP.NET application.
                // One way to address this would be to call ConfigureAwait(false)
                // so that the execution does not attempt to resume on the original context.
                // For instance, replace code such as:
                //      result = await DoSomeTask()
                // with the following:
                //      result = await DoSomeTask().ConfigureAwait(false)

                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

                if (response.IsSuccessStatusCode)
                {
                    string result = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("Result: {0}", result);
                }
                else
                {
                    Console.WriteLine(string.Format("The request failed with status code: {0}", response.StatusCode));

                    // Print the headers - they include the request ID and the timestamp,
                    // which are useful for debugging the failure
                    Console.WriteLine(response.Headers.ToString());

                    string responseContent = await response.Content.ReadAsStringAsync();
                    Console.WriteLine(responseContent);
                }
            }
        }
    }
}
```

### <a name="python-sample"></a>Python-voor beeld
Als u verbinding wilt maken met een Machine Learning-webservice, gebruikt u de **urllib2** -bibliotheek voor python 2. x en **urllib. Request** -bibliotheek voor python 3. x. U stuurt ScoreData, die een FeatureVector bevat, een n-dimensionale vector van de numerieke functies die de ScoreData vertegenwoordigt. U bent geverifieerd bij de Machine Learning-service met een API-sleutel.

**Het code voorbeeld uitvoeren**

1. Implementeren "voor beeld 1: gegevensset downloaden van UCI: volwassene 2 Class gegevensset" experiment, deel van de verzameling Machine Learning-voor beelden.
2. Wijs apiKey toe aan de sleutel van een webservice. Zie de sectie **een autorisatie sleutel ophalen aan** het begin van dit artikel.
3. Wijs serviceUri toe met de aanvraag-URI.

**Hier ziet u hoe een volledige aanvraag eruitziet.**
```python
import urllib2 # urllib.request and urllib.error for Python 3.X
import json

data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",   
                'column2': "value2",   
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters":  {}
}

body = str.encode(json.dumps(data))

# Replace this with the URI and API Key for your web service
url = '<your-api-uri>'
api_key = '<your-api-key>'
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

# "urllib.request.Request(url, body, headers)" for Python 3.X
req = urllib2.Request(url, body, headers)

try:
    # "urllib.request.urlopen(req)" for Python 3.X
    response = urllib2.urlopen(req)

    result = response.read()
    print(result)
# "urllib.error.HTTPError as error" for Python 3.X
except urllib2.HTTPError, error: 
    print("The request failed with status code: " + str(error.code))

    # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>R-voor beeld

Als u verbinding wilt maken met een Machine Learning-webservice, gebruikt u de **RCurl** -en **rjson** -bibliotheken om de aanvraag te maken en de geretourneerde JSON-reactie te verwerken. U stuurt ScoreData, die een FeatureVector bevat, een n-dimensionale vector van de numerieke functies die de ScoreData vertegenwoordigt. U bent geverifieerd bij de Machine Learning-service met een API-sleutel.

**Hier ziet u hoe een volledige aanvraag eruitziet.**
```r
library("curl")
library("httr")
library("rjson")

requestFailed = function(response) {
    return (response$status_code >= 400)
}

printHttpResult = function(response, result) {
    if (requestFailed(response)) {
        print(paste("The request failed with status code:", response$status_code, sep=" "))
    
        # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
        print(response$headers)
    }
    
    print("Result:") 
    print(fromJSON(result))  
}

req = list(
        Inputs = list( 
            "input1" = list(
                "ColumnNames" = list("Col1", "Col2", "Col3"),
                "Values" = list( list( "0", "value", "0" ),  list( "0", "value", "0" )  )
            )                ),
        GlobalParameters = setNames(fromJSON('{}'), character(0))
)

body = enc2utf8(toJSON(req))
api_key = "abc123" # Replace this with the API key for the web service
authz_hdr = paste('Bearer', api_key, sep=' ')

response = POST(url= "<your-api-uri>",
        add_headers("Content-Type" = "application/json", "Authorization" = authz_hdr),
        body = body)

result = content(response, type="text", encoding="UTF-8")

printHttpResult(response, result)
```

### <a name="javascript-sample"></a>Java script-voor beeld

Als u verbinding wilt maken met een Machine Learning-webservice, gebruikt u het NPM-pakket van de **aanvraag** in uw project. U gebruikt het object ook `JSON` om uw invoer op te maken en het resultaat te parseren. Installeer met `npm install request --save` of Voeg `"request": "*"` aan uw package.jstoe onder `dependencies` en voer uit `npm install` .

**Hier ziet u hoe een volledige aanvraag eruitziet.**
```js
let req = require("request");

const uri = "<your-api-uri>";
const apiKey = "<your-api-key>";

let data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",
                'column2': "value2",
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters": {}
}

const options = {
    uri: uri,
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + apiKey,
    },
    body: JSON.stringify(data)
}

req(options, (err, res, body) => {
    if (!err && res.statusCode == 200) {
        console.log(body);
    } else {
        console.log("The request failed with status code: " + res.statusCode);
    }
});
```