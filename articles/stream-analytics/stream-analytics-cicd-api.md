---
title: REST-Api's gebruiken om CI/CD voor Azure Stream Analytics op IoT Edge uit te voeren
description: Meer informatie over het implementeren van een continue integratie-en implementatie pijplijn voor Azure Stream Analytics met behulp van REST Api's.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 3c3f776ad0996fa0b7422f0fca2d899a35e853d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016130"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>CI/CD voor Stream Analytics implementeren op IoT Edge met behulp van Api's

U kunt continue integratie en implementatie voor Azure Stream Analytics-taken inschakelen met behulp van REST-Api's. In dit artikel vindt u voor beelden van de Api's die moeten worden gebruikt en hoe u deze kunt gebruiken. REST-Api's worden niet ondersteund op Azure Cloud Shell.

## <a name="call-apis-from-different-environments"></a>Api's van verschillende omgevingen aanroepen

REST-Api's kunnen worden aangeroepen vanuit Linux en Windows. De volgende opdrachten tonen de juiste syntaxis voor de API-aanroep. Het specifieke API-gebruik wordt beschreven in latere secties van dit artikel.

### <a name="linux"></a>Linux

Voor Linux kunt u of- `Curl` `Wget` opdrachten gebruiken:

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" { <url> }   
```

```bash
wget -q -O- --{ <method> } -data="<request body>" --header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

Gebruik voor Windows Power shell: 

```powershell 
$user = "<username>" 
$pass = "<password>" 
$encodedCreds = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass))) 
$basicAuthValue = "Basic $encodedCreds" 
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("Content-Type", 'application/json') 
$headers.Add("Authorization", $basicAuthValue) 
$content = "<request body>" 
$response = Invoke-RestMethod <url> -Method <method> -Body $content -Headers $Headers 
echo $response 
```
 
## <a name="create-an-asa-job-on-edge"></a>Een ASA-taak maken aan de rand 
 
Als u Stream Analytics taak wilt maken, roept u de PUT-methode aan met behulp van de Stream Analytics-API.

|Methode|Aanvraag-URL|
|------|-----------|
|PUT|`https://management.azure.com/subscriptions/{\**subscription-id**}/resourcegroups/{**resource-group-name**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**job-name**}?api-version=2017-04-01-preview`|
 
Voor beeld van een opdracht met **krul**:

```curl
curl -u { <username:password> } -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
Voor beeld van een aanvraag tekst in JSON:

```json
{ 
  "location": "West US", 
  "tags": { "key": "value", "ms-suppressjobstatusmetrics": "true" }, 
  "sku": {  
      "name": "Standard" 
    }, 
  "properties": { 
    "sku": {  
      "name": "standard" 
    }, 
       "eventsLateArrivalMaxDelayInSeconds": 1, 
       "jobType": "edge", 
    "inputs": [ 
      { 
        "name": "{inputname}", 
        "properties": { 
         "type": "stream", 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ], 
    "transformation": { 
      "name": "{queryName}", 
      "properties": { 
        "query": "{query}" 
      } 
    }, 
    "package": { 
      "storageAccount" : { 
        "accountName": "{blobstorageaccountname}", 
        "accountKey": "{blobstorageaccountkey}" 
      }, 
      "container": "{blobcontaine}]" 
    }, 
    "outputs": [ 
      { 
        "name": "{outputname}", 
        "properties": { 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ] 
  } 
} 
```
 
Zie de [API-documentatie](/rest/api/streamanalytics/)voor meer informatie.  
 
## <a name="publish-edge-package"></a>Edge-pakket publiceren 
 
Als u een Stream Analytics-taak op IoT Edge wilt publiceren, roept u de POST-methode aan met de Edge package Publish API.

|Methode|Aanvraag-URL|
|------|-----------|
|POST|`https://management.azure.com/subscriptions/{\**subscriptionid**}/resourceGroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**jobname**}/publishedgepackage?api-version=2017-04-01-preview`|

Deze asynchrone bewerking retourneert de status 202 totdat de taak is gepubliceerd. De header van de locatie reactie bevat de URI die wordt gebruikt om de status van het proces op te halen. Terwijl het proces wordt uitgevoerd, retourneert een aanroep naar de URI in de locatie header de status 202. Wanneer het proces is voltooid, retourneert de URI in de locatie header de status 200. 

Voor beeld van een Edge-pakket publicatie gesprek met behulp van **krul**: 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
Na het aanroepen van de POST moet u een antwoord verwachten met een lege hoofd tekst. Zoek naar de URL die zich in het hoofd van het antwoord bevindt en noteer deze voor verder gebruik.
 
Voor beeld van de URL van het hoofd van de reactie:

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
Wacht één tot twee minuten voordat u de volgende opdracht uitvoert om een API-aanroep te maken met de URL die u hebt gevonden in het hoofd van het antwoord. Voer de opdracht opnieuw uit als u geen 200-antwoord ontvangt.
 
Voor beeld van het maken van een API-aanroep met de geretourneerde URL met **krul**:

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

Het antwoord bevat de informatie die u nodig hebt om toe te voegen aan het Edge-implementatie script. In de onderstaande voor beelden ziet u welke gegevens u moet verzamelen en waar u deze kunt toevoegen in het implementatie manifest.
 
De hoofd tekst van het voor beeld is nadat deze is gepubliceerd:

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["\],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}","PublishTimeStamp":"{publishtimestamp}"}}}}" 
  status : "Succeeded" 
} 
```

Voor beeld van implementatie manifest: 

```json
{ 
  "modulesContent": { 
    "$edgeAgent": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "runtime": { 
          "type": "docker", 
          "settings": { 
            "minDockerVersion": "v1.25", 
            "loggingOptions": "", 
            "registryCredentials": {} 
          } 
        }, 
        "systemModules": { 
          "edgeAgent": { 
            "type": "docker", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0", 
              "createOptions": "{}" 
            } 
          }, 
          "edgeHub": { 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0", 
              "createOptions": "{}" 
            } 
          } 
        }, 
        "modules": { 
          "<asajobname>": { 
            "version": "1.0", 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "<settings.image>", 
              "createOptions": "<settings.createOptions>" 
            } 
            "version": "<version>", 
             "env": { 
              "PlanId": { 
               "value": "stream-analytics-on-iot-edge" 
          } 
        } 
      } 
    }, 
    "$edgeHub": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "routes": { 
            "route": "FROM /* INTO $upstream" 
        }, 
        "storeAndForwardConfiguration": { 
          "timeToLiveSecs": 7200 
        } 
      } 
    }, 
    "<asajobname>": { 
      "properties.desired": {<twin.content.properties.desired>} 
    } 
  } 
} 
```

Na de configuratie van het implementatie manifest raadpleegt u [Azure IOT Edge-modules implementeren met Azure cli](../iot-edge/how-to-deploy-modules-cli.md) voor implementatie.


## <a name="next-steps"></a>Volgende stappen 
 
* [Azure Stream Analytics op IoT Edge](stream-analytics-edge.md)
* [Zelf studie voor IoT Edge van ASA](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Stream Analytics Edge-taken ontwikkelen met behulp van Visual Studio Tools](stream-analytics-tools-for-visual-studio-edge-jobs.md)