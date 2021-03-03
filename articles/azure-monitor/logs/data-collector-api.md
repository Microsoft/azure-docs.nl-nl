---
title: HTTP-gegevens verzamelaar-API Azure Monitor | Microsoft Docs
description: U kunt de Azure Monitor HTTP data collector API gebruiken om POST JSON-gegevens toe te voegen aan een Log Analytics-werk ruimte vanaf elke client die de REST API kan aanroepen. In dit artikel wordt beschreven hoe u de API gebruikt en bevat voor beelden van het publiceren van gegevens met behulp van verschillende programmeer talen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/14/2020
ms.openlocfilehash: 446e375cbbfc577dfc5bdff39e151cef5ae9e4c0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736998"
---
# <a name="send-log-data-to-azure-monitor-with-the-http-data-collector-api-public-preview"></a>Logboek gegevens naar Azure Monitor verzenden met de HTTP-gegevens verzamelaar-API (open bare preview)
In dit artikel leest u hoe u de HTTP data collector API kunt gebruiken om logboek gegevens te verzenden naar Azure Monitor van een REST API-client.  Hierin wordt beschreven hoe u gegevens opmaakt die worden verzameld door uw script of toepassing, deze toevoegen aan een aanvraag en die aanvraag hebben toegestaan door Azure Monitor.  Er zijn voor beelden van Power shell, C# en python.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> De Azure Monitor HTTP-gegevens verzamelaar-API bevindt zich in de open bare preview.

## <a name="concepts"></a>Concepten
U kunt de HTTP data collector API gebruiken om logboek gegevens te verzenden naar een Log Analytics-werk ruimte in Azure Monitor vanaf elke client die een REST API kan aanroepen.  Dit kan een runbook zijn in Azure Automation waarmee beheer gegevens worden verzameld van Azure of een andere Cloud, of een ander beheer systeem is dat gebruikmaakt van Azure Monitor om logboek gegevens te consolideren en analyseren.

Alle gegevens in de werk ruimte Log Analytics worden opgeslagen als een record met een bepaald record type.  U formatteert uw gegevens om deze te verzenden naar de HTTP data collector-API als meerdere records in JSON.  Wanneer u de gegevens verzendt, wordt er voor elke record in de aanvraag lading een afzonderlijke record in de opslag plaats gemaakt.


![Overzicht van HTTP-gegevens verzamelaar](media/data-collector-api/overview.png)



## <a name="create-a-request"></a>Een aanvraag maken
Als u de HTTP data collector API wilt gebruiken, maakt u een POST-aanvraag die de gegevens bevat die moeten worden verzonden in de JavaScript Object Notation (JSON).  De volgende drie tabellen geven een lijst van de kenmerken die zijn vereist voor elke aanvraag. Verderop in dit artikel wordt elk kenmerk uitvoeriger beschreven.

### <a name="request-uri"></a>Aanvraag-URI
| Kenmerk | Eigenschap |
|:--- |:--- |
| Methode |POST |
| URI |https:// \<CustomerId\> . ODS.opinsights.Azure.com/API/logs?API-Version=2016-04-01 |
| Inhoudstype |application/json |

### <a name="request-uri-parameters"></a>URI-para meters aanvragen
| Parameter | Beschrijving |
|:--- |:--- |
| CustomerID |De unieke id voor de Log Analytics-werk ruimte. |
| Resource |De naam van de API-resource:/API/logs. |
| API-versie |De versie van de API die moet worden gebruikt voor deze aanvraag. Momenteel is dit 2016-04-01. |

### <a name="request-headers"></a>Aanvraagheaders
| Header | Beschrijving |
|:--- |:--- |
| Autorisatie |De autorisatie handtekening. Verderop in dit artikel vindt u meer informatie over het maken van een HMAC-SHA256-header. |
| Log-Type |Geef het record type op van de gegevens die worden verzonden. Mag alleen letters, cijfers en onderstrepings tekens (_) bevatten en mag niet langer zijn dan 100. |
| x-MS-date |De datum waarop de aanvraag is verwerkt, in RFC 1123-indeling. |
| x-MS-AzureResourceId | Resource-ID van de Azure-resource waaraan de gegevens moeten worden gekoppeld. Hiermee wordt de eigenschap [_ResourceId](./log-standard-columns.md#_resourceid) gevuld en kunnen de gegevens worden opgenomen in [resource-context](./design-logs-deployment.md#access-mode) query's. Als dit veld niet wordt opgegeven, worden de gegevens niet opgenomen in resource-context query's. |
| gegenereerde tijd-veld | De naam van een veld in de gegevens die de tijds tempel van het gegevens item bevat. Als u een veld opgeeft, wordt de inhoud ervan gebruikt voor **TimeGenerated**. Als dit veld niet is opgegeven, is de standaard waarde voor **TimeGenerated** het tijdstip waarop het bericht wordt opgenomen. De inhoud van het veld bericht moet de ISO 8601-notatie JJJJ-MM-DDTuu: mm: ssZ hebben. |

## <a name="authorization"></a>Autorisatie
Elke aanvraag voor de Azure Monitor HTTP-gegevens verzamelaar-API moet een autorisatie-header bevatten. Als u een aanvraag wilt verifiëren, moet u de aanvraag ondertekenen met de primaire of secundaire sleutel voor de werk ruimte die de aanvraag maakt. Geef vervolgens die hand tekening door als onderdeel van de aanvraag.   

Dit is de indeling voor de autorisatie-header:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* is de unieke id voor de log Analytics-werk ruimte. *Hand tekening* is een [op hash gebaseerde Message Authentication Code (HMAC)](/dotnet/api/system.security.cryptography.hmacsha256) die is opgebouwd op basis van de aanvraag en vervolgens wordt berekend met behulp van de [sha256-algoritme](/dotnet/api/system.security.cryptography.sha256). Vervolgens versleutelt u het met base64-code ring.

Gebruik deze indeling om de teken reeks voor de **SharedKey** -hand tekening te coderen:

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

Hier volgt een voor beeld van een teken reeks voor hand tekeningen:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Wanneer u de teken reeks voor hand tekeningen hebt, versleutelt u deze met behulp van het HMAC-SHA256-algoritme voor de teken reeks met UTF-8-code ring en versleutelt u het resultaat als base64. Gebruik deze indeling:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

De voor beelden in de volgende secties bevatten voorbeeld code om u te helpen bij het maken van een autorisatie-header.

## <a name="request-body"></a>Aanvraagbody
De hoofd tekst van het bericht moet JSON zijn. Het moet een of meer records bevatten met de eigenschaps naam en waardeparen in de volgende notatie. De naam van de eigenschap mag alleen letters, cijfers en onderstrepings tekens (_) bevatten.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

U kunt meerdere records samen voegen in één aanvraag met behulp van de volgende indeling. Alle records moeten hetzelfde record type zijn.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    },
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

## <a name="record-type-and-properties"></a>Record type en eigenschappen
U definieert een aangepast record type wanneer u gegevens verzendt via de Azure Monitor HTTP-gegevens verzamelaar-API. Op dit moment kunt u geen gegevens schrijven naar bestaande record typen die zijn gemaakt door andere gegevens typen en oplossingen. Azure Monitor leest de inkomende gegevens en maakt vervolgens eigenschappen die overeenkomen met de gegevens typen van de waarden die u invoert.

Elke aanvraag voor de Data Collector-API moet een header van het **logboek type** bevatten met de naam van het record type. Het achtervoegsel **_CL** wordt automatisch toegevoegd aan de naam die u invoert om deze te onderscheiden van andere logboek typen als aangepast logboek. Als u bijvoorbeeld de naam **MyNewRecordType** opgeeft, maakt Azure monitor een record met het type **MyNewRecordType_CL**. Dit zorgt ervoor dat er geen conflicten zijn tussen door de gebruiker gemaakte type namen en die worden geleverd in huidige of toekomstige micro soft-oplossingen.

Als u het gegevens type van een eigenschap wilt identificeren, voegt Azure Monitor een achtervoegsel toe aan de naam van de eigenschap. Als een eigenschap een null-waarde bevat, wordt de eigenschap niet in die record opgenomen. Deze tabel bevat het gegevens type van de eigenschap en het bijbehorende achtervoegsel:

| Eigenschaps gegevens type | Achtervoegsel |
|:--- |:--- |
| Tekenreeks |_s |
| Booleaans |_b |
| Dubbel |_d |
| Datum/tijd |_t |
| GUID (opgeslagen als een teken reeks) |_g |

> [!NOTE]
> Teken reeks waarden die worden weer gegeven als GUID'S, krijgen het _g achtervoegsel en zijn opgemaakt als een GUID, zelfs als de inkomende waarde geen streepjes bevat. Bijvoorbeeld: zowel "8145d822-13a7-44ad-859c-36f31a84f6dd" als "8145d82213a744ad859c36f31a84f6dd" worden opgeslagen als "8145d822-13a7-44ad-859c-36f31a84f6dd". De enige verschillen tussen deze en een andere teken reeks zijn de _g in de naam en het invoegen van streepjes als deze niet zijn opgegeven in de invoer. 

Het gegevens type dat Azure Monitor gebruikt voor elke eigenschap is afhankelijk van het feit of het record type voor de nieuwe record al bestaat.

* Als het record type niet bestaat, wordt door Azure Monitor een nieuwe gemaakt met behulp van het JSON-type deinterferentie om het gegevens type voor elke eigenschap voor de nieuwe record te bepalen.
* Als het record type bestaat, probeert Azure Monitor een nieuwe record te maken op basis van bestaande eigenschappen. Als het gegevens type voor een eigenschap in de nieuwe record niet overeenkomt met en niet kan worden geconverteerd naar het bestaande type, of als de record een eigenschap bevat die niet bestaat, maakt Azure Monitor een nieuwe eigenschap met het relevante achtervoegsel.

Met deze verzend vermelding maakt u bijvoorbeeld een record met drie eigenschappen, **number_d**, **boolean_b** en **string_s**:

![Voorbeeld record 1](media/data-collector-api/record-01.png)

Als u deze volgende vermelding vervolgens hebt verzonden met alle waarden die zijn opgemaakt als teken reeksen, worden de eigenschappen niet gewijzigd. Deze waarden kunnen worden geconverteerd naar bestaande gegevens typen:

![Voorbeeld record 2](media/data-collector-api/record-02.png)

Maar als u deze volgende verzen ding hebt gemaakt, Azure Monitor de nieuwe eigenschappen **boolean_d** en **string_d** maken. Deze waarden kunnen niet worden geconverteerd:

![Voorbeeld record 3](media/data-collector-api/record-03.png)

Als u de volgende vermelding vervolgens hebt verzonden voordat het record type werd gemaakt, maakt Azure Monitor een record met drie eigenschappen, **number_s**, **boolean_s** en **string_s**. In deze vermelding wordt elk van de oorspronkelijke waarden opgemaakt als een teken reeks:

![Voorbeeld record 4](media/data-collector-api/record-04.png)

## <a name="reserved-properties"></a>Gereserveerde eigenschappen
De volgende eigenschappen zijn gereserveerd en mogen niet worden gebruikt in een aangepast record type. U krijgt een fout melding als uw payload een van deze eigenschaps namen bevat.

- tenant

## <a name="data-limits"></a>Gegevenslimieten
Er zijn enkele beperkingen rond de gegevens die worden gepost naar de Azure Monitor Data Collection-API.

* Maxi maal 30 MB per post naar Azure Monitor Data Collector-API. Dit is een maximale grootte voor één bericht. Als de gegevens van één post die meer dan 30 MB overschrijden, moet u de gegevens opsplitsen naar kleinere segmenten en deze gelijktijdig verzenden.
* Maximum limiet van 32 kB voor veldwaarden. Als een veldwaarde groter is dan 32 kB, worden de gegevens afgekapt.
* Het aanbevolen aantal velden voor elk type is 50. Dit is een praktische limiet vanuit het oogpunt van bruikbaarheid en zoekervaring.  
* Een tabel in een Log Analytics-werk ruimte ondersteunt Maxi maal 500 kolommen (ook wel een veld genoemd in dit artikel). 
* Het maximum aantal tekens voor een kolomnaam is 500.

## <a name="return-codes"></a>Retour codes
De HTTP-status code 200 betekent dat de aanvraag is ontvangen voor verwerking. Dit geeft aan dat de bewerking is voltooid.

Deze tabel bevat de volledige set met status codes die de service kan retour neren:

| Code | Status | Foutcode | Beschrijving |
|:--- |:--- |:--- |:--- |
| 200 |OK | |De aanvraag is geaccepteerd. |
| 400 |Ongeldige aanvraag |InactiveCustomer |De werk ruimte is gesloten. |
| 400 |Ongeldige aanvraag |InvalidApiVersion |De API-versie die u hebt opgegeven, wordt niet herkend door de service. |
| 400 |Ongeldige aanvraag |InvalidCustomerId |De opgegeven werk ruimte-ID is ongeldig. |
| 400 |Ongeldige aanvraag |InvalidDataFormat |Ongeldige JSON verzonden. De antwoord tekst bevat mogelijk meer informatie over het oplossen van de fout. |
| 400 |Ongeldige aanvraag |InvalidLogType |Het opgegeven logboek type bevat speciale tekens of cijfers. |
| 400 |Ongeldige aanvraag |MissingApiVersion |De API-versie is niet opgegeven. |
| 400 |Ongeldige aanvraag |MissingContentType |Het inhouds type is niet opgegeven. |
| 400 |Ongeldige aanvraag |MissingLogType |Het vereiste logboek type voor de waarde is niet opgegeven. |
| 400 |Ongeldige aanvraag |UnsupportedContentType |Het inhouds type is niet ingesteld op **Application/JSON**. |
| 403 |Verboden |InvalidAuthorization |De service kan de aanvraag niet verifiëren. Controleer of de werk ruimte-ID en de verbindings sleutel geldig zijn. |
| 404 |Niet gevonden | | De gegeven URL is onjuist of de aanvraag is te groot. |
| 429 |Te veel aanvragen | | De service ondervindt een groot aantal gegevens van uw account. Voer de aanvraag later opnieuw uit. |
| 500 |Interne server fout |UnspecifiedError |Er is een interne fout opgetreden in de service. Voer de aanvraag opnieuw uit. |
| 503 |Service niet beschikbaar |ServiceUnavailable |De service is momenteel niet beschikbaar voor het ontvangen van aanvragen. Probeer de aanvraag opnieuw uit te voeren. |

## <a name="query-data"></a>Querygegevens
Als u query's wilt uitvoeren op gegevens die zijn verzonden door de Azure Monitor HTTP-gegevens verzamelaar-API, zoekt u naar records met een **type** dat gelijk is aan de **LogType** -waarde die u hebt opgegeven, toegevoegd met **_CL**. Als u bijvoorbeeld **MyCustomLog** hebt gebruikt, geeft u alle records met op `MyCustomLog_CL` .

## <a name="sample-requests"></a>Voorbeeld aanvragen
In de volgende secties vindt u voor beelden van het verzenden van gegevens naar de Azure Monitor HTTP-gegevens verzamelaar-API met behulp van verschillende programmeer talen.

Voer voor elk voor beeld de volgende stappen uit om de variabelen in te stellen voor de autorisatie-header:

1. Zoek in de Azure Portal de Log Analytics-werk ruimte.
2. Selecteer **agents beheren**.
2. Selecteer het Kopieer pictogram rechts van **werk ruimte-id** en plak de id als de waarde van de variabele voor de **klant-id** .
3. Rechts van **primaire sleutel** selecteert u het Kopieer pictogram en plakt u de id als de waarde van de **gedeelde sleutel** variabele.

U kunt ook de variabelen voor het logboek type en JSON-gegevens wijzigen.

### <a name="powershell-sample"></a>Voorbeeld van PowerShell
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
$TimeStampField = ""


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>C#-voorbeeld
```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Azure Monitor
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(json);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-2-sample"></a>Python 2-voor beeld
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```

### <a name="python-3-sample"></a>Python 3-voor beeld
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash, encoding="utf-8")  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest()).decode()
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print('Accepted')
    else:
        print("Response code: {}".format(response.status_code))

post_data(customer_id, shared_key, body, log_type)
```


### <a name="java-sample"></a>Java-voor beeld

```java

import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.springframework.http.MediaType;

import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.text.SimpleDateFormat;
import java.util.Base64;
import java.util.Calendar;
import java.util.TimeZone;

import static org.springframework.http.HttpHeaders.CONTENT_TYPE;

public class ApiExample {

  private static final String workspaceId = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
  private static final String sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";
  private static final String logName = "DemoExample";
  /*
  You can use an optional field to specify the timestamp from the data. If the time field is not specified,
  Azure Monitor assumes the time is the message ingestion time
   */
  private static final String timestamp = "";
  private static final String json = "{\"name\": \"test\",\n" + "  \"id\": 1\n" + "}";
  private static final String RFC_1123_DATE = "EEE, dd MMM yyyy HH:mm:ss z";

  public static void main(String[] args) throws IOException, NoSuchAlgorithmException, InvalidKeyException {
    String dateString = getServerTime();
    String httpMethod = "POST";
    String contentType = "application/json";
    String xmsDate = "x-ms-date:" + dateString;
    String resource = "/api/logs";
    String stringToHash = String
        .join("\n", httpMethod, String.valueOf(json.getBytes(StandardCharsets.UTF_8).length), contentType,
            xmsDate , resource);
    String hashedString = getHMAC254(stringToHash, sharedKey);
    String signature = "SharedKey " + workspaceId + ":" + hashedString;

    postData(signature, dateString, json);
  }

  private static String getServerTime() {
    Calendar calendar = Calendar.getInstance();
    SimpleDateFormat dateFormat = new SimpleDateFormat(RFC_1123_DATE);
    dateFormat.setTimeZone(TimeZone.getTimeZone("GMT"));
    return dateFormat.format(calendar.getTime());
  }

  private static void postData(String signature, String dateString, String json) throws IOException {
    String url = "https://" + workspaceId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";
    HttpPost httpPost = new HttpPost(url);
    httpPost.setHeader("Authorization", signature);
    httpPost.setHeader(CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE);
    httpPost.setHeader("Log-Type", logName);
    httpPost.setHeader("x-ms-date", dateString);
    httpPost.setHeader("time-generated-field", timestamp);
    httpPost.setEntity(new StringEntity(json));
    try(CloseableHttpClient httpClient = HttpClients.createDefault()){
      HttpResponse response = httpClient.execute(httpPost);
      int statusCode = response.getStatusLine().getStatusCode();
      System.out.println("Status code: " + statusCode);
    }
  }

  private static String getHMAC254(String input, String key) throws InvalidKeyException, NoSuchAlgorithmException {
    String hash;
    Mac sha254HMAC = Mac.getInstance("HmacSHA256");
    Base64.Decoder decoder = Base64.getDecoder();
    SecretKeySpec secretKey = new SecretKeySpec(decoder.decode(key.getBytes(StandardCharsets.UTF_8)), "HmacSHA256");
    sha254HMAC.init(secretKey);
    Base64.Encoder encoder = Base64.getEncoder();
    hash = new String(encoder.encode(sha254HMAC.doFinal(input.getBytes(StandardCharsets.UTF_8))));
    return hash;
  }

}


```


## <a name="alternatives-and-considerations"></a>Alternatieven en overwegingen
Terwijl de Data Collector-API het meren deel van uw behoeften voor het verzamelen van vrije-vorm gegevens in azure-logboeken moet omvatten, zijn er exemplaren die mogelijk vereist zijn om bepaalde beperkingen van de API te overwinnen. U kunt kiezen uit de volgende belang rijke overwegingen:

| Vervangen | Beschrijving | Geschikt voor |
|---|---|---|
| [Aangepaste gebeurtenissen](../app/api-custom-events-metrics.md?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties): systeem eigen op SDK gebaseerde opname in Application Insights | Application Insights, meestal door middel van een SDK binnen uw toepassing, biedt u de mogelijkheid om aangepaste gegevens via aangepaste gebeurtenissen te verzenden. | <ul><li> Gegevens die in uw toepassing worden gegenereerd, maar niet door de SDK worden opgehaald via een van de standaard gegevens typen (aanvragen, afhankelijkheden, uitzonde ringen, enzovoort).</li><li> Gegevens die het meest worden gecorreleerd aan andere toepassings gegevens in Application Insights </li></ul> |
| Data Collector-API in Azure Monitor-logboeken | De Data Collector-API in Azure Monitor Logboeken is een volledig open manier om gegevens op te nemen. Gegevens die in een JSON-object zijn ingedeeld, kunnen hier worden verzonden. Zodra de gegevens zijn verzonden, worden deze verwerkt en in logboeken weer gegeven om te worden gecorreleerd met andere vermeldingen in Logboeken of met andere Application Insights gegevens. <br/><br/> Het is tamelijk eenvoudig om de gegevens als bestanden naar een Azure Blob-Blob te uploaden, vanaf waar deze bestanden worden verwerkt en geüpload naar Log Analytics. Raadpleeg [Dit](./create-pipeline-datacollector-api.md) artikel voor een voor beeld van de implementatie van een dergelijke pijp lijn. | <ul><li> Gegevens die niet noodzakelijkerwijs worden gegenereerd binnen een toepassings instrument in Application Insights.</li><li> Voor beelden zijn onder andere lookup-en feiten tabellen, referentie gegevens, vooraf geaggregeerde statistieken, enzovoort. </li><li> Bedoeld voor gegevens waarnaar wordt verwezen met andere Azure Monitor gegevens (Application Insights, andere gegevens typen van Logboeken, Security Center, container inzichten/Vm's, enzovoort). </li></ul> |
| [Azure Data Explorer](/azure/data-explorer/ingest-data-overview) | Azure Data Explorer (ADX) is het gegevens platform dat Application Insights Analytics-en Azure Monitor-logboeken aanstuurt. Nu algemeen beschikbaar ("GA"), met behulp van het gegevens platform in het onbewerkte formulier, hebt u de flexibiliteit om te volt ooien (maar de overhead van het beheer vereisen) via het cluster (Kubernetes RBAC, retentie frequentie, schema, enzovoort). ADX biedt veel [opname opties](/azure/data-explorer/ingest-data-overview#ingestion-methods) , waaronder [CSV-, TSV-en JSON-](/azure/kusto/management/mappings?branch=master) bestanden. | <ul><li> Gegevens die niet met andere gegevens onder Application Insights of Logboeken worden gecorreleerd. </li><li> Gegevens waarvoor geavanceerde opname-of verwerkings mogelijkheden zijn vereist, die momenteel niet beschikbaar zijn in Azure Monitor Logboeken. </li></ul> |


## <a name="next-steps"></a>Volgende stappen
- Gebruik de [API voor zoeken in Logboeken](./log-query-overview.md) om gegevens op te halen uit de log Analytics-werk ruimte.

- Meer informatie over hoe u [een gegevens pijplijn maakt met de Data Collector-API](create-pipeline-datacollector-api.md) met behulp van Logic apps werk stroom tot Azure monitor.