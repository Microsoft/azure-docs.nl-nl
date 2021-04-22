---
title: Client maken voor een model dat is geïmplementeerd als webservice
titleSuffix: Azure Machine Learning
description: Meer informatie over het aanroepen van een webservice-eindpunt dat is gegenereerd toen een model werd geïmplementeerd vanuit Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 10/12/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-csharp
ms.openlocfilehash: 8cab9331cdd4c15ab76b7c33f956be15ec2259ef
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861189"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Een Azure Machine Learning-model gebruiken dat als een webservice is geïmplementeerd


Als u een Azure Machine Learning-model implementeert als webservice, wordt een REST API-eindpunt gemaakt. U kunt gegevens naar dit eindpunt verzenden en de voorspelling ontvangen die door het model wordt geretourneerd. In dit document leert u hoe u clients maakt voor de webservice met behulp van C#, Go, Java en Python.

U maakt een webservice wanneer u een model implementeert in uw lokale omgeving, Azure Container Instances, Azure Kubernetes Service of field-programmable gate arrays (FPGA). U haalt de URI op die wordt gebruikt voor toegang tot de webservice met behulp van [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro). Als verificatie is ingeschakeld, kunt u ook de SDK gebruiken om de verificatiesleutels of tokens op te halen.

De algemene werkstroom voor het maken van een client die gebruikmaakt van machine learning webservice is:

1. Gebruik de SDK om de verbindingsgegevens op te halen.
1. Bepaal het type aanvraaggegevens dat door het model wordt gebruikt.
1. Maak een toepassing die de webservice aanroept.

> [!TIP]
> De voorbeelden in dit document worden handmatig gemaakt zonder het gebruik van OpenAPI-specificaties (Swagger). Als u een OpenAPI-specificatie voor uw implementatie hebt ingeschakeld, kunt u hulpprogramma's zoals [swagger-codegen](https://github.com/swagger-api/swagger-codegen) gebruiken om clientbibliotheken voor uw service te maken.

## <a name="connection-information"></a>Verbindingsgegevens

> [!NOTE]
> Gebruik de Azure Machine Learning SDK om de webservicegegevens op te halen. Dit is een Python SDK. U kunt elke taal gebruiken om een client voor de service te maken.

De [klasse azureml.core.Webservice](/python/api/azureml-core/azureml.core.webservice%28class%29) biedt de informatie die u nodig hebt om een client te maken. De volgende `Webservice` eigenschappen zijn handig voor het maken van een clienttoepassing:

* `auth_enabled`- Als sleutelverificatie is ingeschakeld, `True` ; anders . `False`
* `token_auth_enabled`- Als tokenverificatie is ingeschakeld, `True` ; anders . `False`
* `scoring_uri` - Het REST API adres.
* `swagger_uri` - Het adres van de OpenAPI-specificatie. Deze URI is beschikbaar als u automatische schemageneratie hebt ingeschakeld. Zie Modellen implementeren met [Azure Machine Learning voor meer Azure Machine Learning.](how-to-deploy-and-where.md)

Er zijn verschillende manieren om deze informatie op te halen voor geïmplementeerde webservices:

# <a name="python"></a>[Python](#tab/python)

* Wanneer u een model implementeert, `Webservice` wordt een -object geretourneerd met informatie over de service:

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* U kunt gebruiken `Webservice.list` om een lijst met geïmplementeerde webservices voor modellen in uw werkruimte op te halen. U kunt filters toevoegen om de lijst met geretourneerde gegevens te beperken. Zie de naslagdocumentatie voor [Webservice.list](/python/api/azureml-core/azureml.core.webservice.webservice.webservice) voor meer informatie over waar op kan worden gefilterd.

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* Als u de naam van de geïmplementeerde service weet, kunt u een nieuw exemplaar van maken en de werkruimte en `Webservice` servicenaam opgeven als parameters. Het nieuwe object bevat informatie over de geïmplementeerde service.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de naam van de geïmplementeerde service weet, gebruikt u de [opdracht az ml service show:](/cli/azure/ml/service#az_ml_service_show)

```azurecli
az ml service show -n <service-name>
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecteer Azure Machine Learning-studio __eindpunten,__ __realtime-eindpunten__ en vervolgens de naam van het eindpunt. In details voor het eindpunt bevat het __veld REST-eindpunt__ de scoring-URI. De __Swagger-URI__ bevat de swagger-URI.

---

In de volgende tabel ziet u hoe deze URI's eruit zien:

| URI-type | Voorbeeld |
| ----- | ----- |
| Scoring-URI | `http://104.214.29.152:80/api/v1/service/<service-name>/score` |
| Swagger-URI | `http://104.214.29.152/api/v1/service/<service-name>/swagger.json` |

> [!TIP]
> Het IP-adres is anders voor uw implementatie. Elk AKS-cluster heeft een eigen IP-adres dat wordt gedeeld door implementaties naar dat cluster.

### <a name="secured-web-service"></a>Beveiligde webservice

Als u de geïmplementeerde webservice hebt beveiligd met een TLS/SSL-certificaat, kunt u [HTTPS](https://en.wikipedia.org/wiki/HTTPS) gebruiken om verbinding te maken met de service met behulp van de scoring- of swagger-URI. HTTPS helpt de communicatie tussen een client en een webservice te beveiligen door communicatie tussen de twee te versleutelen. Voor [versleuteling Transport Layer Security (TLS) gebruikt.](https://en.wikipedia.org/wiki/Transport_Layer_Security) TLS wordt soms nog steeds aangeduid *als Secure Sockets Layer* (SSL), de voorloper van TLS.

> [!IMPORTANT]
> Webservices die zijn geïmplementeerd door Azure Machine Learning bieden alleen ondersteuning voor TLS-versie 1.2. Wanneer u een clienttoepassing maakt, moet u ervoor zorgen dat deze versie wordt ondersteund.

Zie Use TLS to secure a web service through Azure Machine Learning (TLS gebruiken om een webservice te [beveiligen via Azure Machine Learning).](how-to-secure-web-service.md)

### <a name="authentication-for-services"></a>Verificatie voor services

Azure Machine Learning biedt twee manieren om de toegang tot uw webservices te controleren.

|Verificatiemethode|Aci|AKS|
|---|---|---|
|Sleutel|Standaard uitgeschakeld| Standaard ingeschakeld|
|Token| Niet beschikbaar| Standaard uitgeschakeld |

Bij het verzenden van een aanvraag naar een service die  is beveiligd met een sleutel of token, gebruikt u de autorisatieheader om de sleutel of het token door te geven. De sleutel of token moet worden opgemaakt als , waarbij de waarde `Bearer <key-or-token>` van uw sleutel of `<key-or-token>` token is.

Het belangrijkste verschil tussen sleutels en tokens is dat sleutels statisch zijn en handmatig opnieuw kunnen worden ge regenereerd **en** dat tokens na verloop van verloop moeten **worden vernieuwd.** Op sleutels gebaseerde auth wordt ondersteund voor Azure Container Instance en Azure Kubernetes Service geïmplementeerde webservices, en op token gebaseerde auth **is** alleen beschikbaar voor Azure Kubernetes Service implementaties. Zie Verificatie configureren voor modellen die zijn geïmplementeerd als webservices voor meer informatie over het [configureren van verificatie.](how-to-authenticate-web-service.md)


#### <a name="authentication-with-keys"></a>Verificatie met sleutels

Wanneer u verificatie inschakelen voor een implementatie, maakt u automatisch verificatiesleutels.

* Verificatie is standaard ingeschakeld wanneer u implementeert in Azure Kubernetes Service.
* Verificatie is standaard uitgeschakeld wanneer u implementeert in Azure Container Instances.

Als u verificatie wilt controleren, gebruikt u `auth_enabled` de parameter bij het maken of bijwerken van een implementatie.

Als verificatie is ingeschakeld, kunt u de methode gebruiken om een primaire en `get_keys` secundaire verificatiesleutel op te halen:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Als u een sleutel opnieuw wilt maken, gebruikt u [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29) .

#### <a name="authentication-with-tokens"></a>Verificatie met tokens

Wanneer u tokenverificatie voor een webservice inschakelen, moet een gebruiker een Azure Machine Learning JWT-token aan de webservice om toegang te krijgen. 

* Tokenverificatie is standaard uitgeschakeld wanneer u implementeert in Azure Kubernetes Service.
* Tokenverificatie wordt niet ondersteund wanneer u implementeert in Azure Container Instances.

Als u tokenverificatie wilt controleren, gebruikt u `token_auth_enabled` de parameter bij het maken of bijwerken van een implementatie.

Als tokenverificatie is ingeschakeld, kunt u de methode gebruiken om een Bearer-token en de verlooptijd van `get_token` tokens op te halen:

```python
token, refresh_by = service.get_token()
print(token)
```

Als u de Azure CLI en de extensie [machine learning hebt,](reference-azure-machine-learning-cli.md)kunt u de volgende opdracht gebruiken om een token op te halen:

```azurecli
az ml service get-access-token -n <service-name>
```

> [!IMPORTANT]
> Momenteel kunt u het token alleen ophalen met behulp van de Azure Machine Learning-SDK of de Azure CLI machine learning extensie.

U moet na de tijd van het token een nieuw token `refresh_by` aanvragen. 

## <a name="request-data"></a>Gegevens aanvragen

De REST API verwacht dat de body van de aanvraag een JSON-document is met de volgende structuur:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> De structuur van de gegevens moet overeenkomen met wat het scorescript en model in de service verwachten. Het scorescript kan de gegevens wijzigen voordat deze aan het model worden doorgeven.

### <a name="binary-data"></a>Binaire gegevens

Zie Binaire gegevens voor meer informatie over het inschakelen van ondersteuning voor binaire gegevens in [uw service.](how-to-deploy-advanced-entry-script.md#binary-data)

> [!TIP]
> Het inschakelen van ondersteuning voor binaire gegevens vindt plaats in score.py bestand dat wordt gebruikt door het geïmplementeerde model. Gebruik vanuit de client de HTTP-functionaliteit van uw programmeertaal. Het volgende fragment verzendt bijvoorbeeld de inhoud van een JPG-bestand naar een webservice:
>
> ```python
> import requests
> # Load image data
> data = open('example.jpg', 'rb').read()
> # Post raw data to scoring URI
> res = request.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/> octet-stream'})
> ```

### <a name="cross-origin-resource-sharing-cors"></a>CORS (Cross-Origin Resource Sharing, cross-origin-resource delen)

Zie [Cross-origin resource sharing](how-to-deploy-advanced-entry-script.md#cors)voor meer informatie over het inschakelen van CORS-ondersteuning in uw service.

## <a name="call-the-service-c"></a>De service aanroepen (C#)

In dit voorbeeld wordt gedemonstreerd hoe u C# gebruikt om de webservice aan te roepen die is gemaakt vanuit het voorbeeld [Trainen in notebook:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb)

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key or token
            string scoringUri = "<your web service URI>";
            string authKey = "<your key or token>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

De geretourneerde resultaten zijn vergelijkbaar met het volgende JSON-document:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>De service aanroepen (Go)

In dit voorbeeld wordt gedemonstreerd hoe u Go gebruikt om de webservice aan te roepen die is gemaakt vanuit het voorbeeld [Trainen in notebook:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key or token (if any) for your service
var authKey string = "<your key or token>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

De geretourneerde resultaten zijn vergelijkbaar met het volgende JSON-document:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>De service aanroepen (Java)

In dit voorbeeld wordt gedemonstreerd hoe u Java gebruikt om de webservice aan te roepen die is gemaakt vanuit het voorbeeld [Trainen in notebook:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb)

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key or token
        String key = "<your key or token>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

De geretourneerde resultaten zijn vergelijkbaar met het volgende JSON-document:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>De service aanroepen (Python)

In dit voorbeeld ziet u hoe u Python gebruikt voor het aanroepen van de webservice die is gemaakt vanuit het voorbeeld [Trainen in notebook:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb)

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key or token
key = '<your key or token>'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [
                0.0199132141783263,
                0.0506801187398187,
                0.104808689473925,
                0.0700725447072635,
                -0.0359677812752396,
                -0.0266789028311707,
                -0.0249926566315915,
                -0.00259226199818282,
                0.00371173823343597,
                0.0403433716478807
            ],
            [
                -0.0127796318808497,
                -0.044641636506989,
                0.0606183944448076,
                0.0528581912385822,
                0.0479653430750293,
                0.0293746718291555,
                -0.0176293810234174,
                0.0343088588777263,
                0.0702112981933102,
                0.00720651632920303]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}
# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

De geretourneerde resultaten zijn vergelijkbaar met het volgende JSON-document:

```JSON
[217.67978776218715, 224.78937091757172]
```


## <a name="web-service-schema-openapi-specification"></a>Webserviceschema (OpenAPI-specificatie)

Als u automatische schemageneratie hebt gebruikt voor uw implementatie, kunt u het adres van de OpenAPI-specificatie voor de service op halen met behulp van [swagger_uri eigenschap](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#swagger-uri). `print(service.swagger_uri)`(Bijvoorbeeld.) Gebruik een GET-aanvraag of open de URI in een browser om de specificatie op te halen.

Het volgende JSON-document is een voorbeeld van een schema (OpenAPI-specificatie) dat is gegenereerd voor een implementatie:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

Zie [OpenAPI-specificatie voor meer informatie.](https://swagger.io/specification/)

Zie [swagger-codegen](https://github.com/swagger-api/swagger-codegen)voor een hulpprogramma dat clientbibliotheken op de specificatie kan maken.


> [!TIP]
> U kunt het JSON-document voor het schema ophalen nadat u de service hebt geïmplementeerd. Gebruik de [swagger_uri van](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#swagger-uri) de geïmplementeerde webservice (bijvoorbeeld ) om de URI naar het Swagger-bestand van de lokale `service.swagger_uri` webservice op te halen.

## <a name="consume-the-service-from-power-bi"></a>De service van Power BI

Power BI ondersteunt het gebruik van Azure Machine Learning webservices om de gegevens in de Power BI te verrijken met voorspellingen. 

Als u een webservice wilt genereren die wordt ondersteund voor gebruik in Power BI, moet het schema de indeling ondersteunen die is vereist door Power BI. [Informatie over het maken van een Power BI ondersteund schema](./how-to-deploy-advanced-entry-script.md#power-bi-compatible-endpoint).

Zodra de webservice is geïmplementeerd, kan deze worden gebruikt vanuit Power BI-gegevensstromen. [Een Azure Machine Learning-webservice gebruiken vanuit Power BI](/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>Volgende stappen

Als u een referentiearchitectuur wilt weergeven voor het in realtime scoren van Python- en Deep Learning-modellen, gaat u naar het [Azure Architecture Center.](/azure/architecture/reference-architectures/ai/realtime-scoring-python)
