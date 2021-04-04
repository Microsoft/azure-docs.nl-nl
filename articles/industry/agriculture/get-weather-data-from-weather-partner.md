---
title: Weer gegevens ophalen van weer partners
description: In dit artikel wordt beschreven hoe u weer gegevens van partners kunt ophalen.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: bb28c517e353af6b8c1ee0cad788ff41b971918c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460879"
---
# <a name="get-weather-data-from-weather-partners"></a>Weer gegevens ophalen van weer partners

Met Azure FarmBeats kunt u weer gegevens van uw weer gegevens providers halen met behulp van een connector raamwerk op basis van docker. Met behulp van dit framework implementeren weer gegevens providers een docker die kan worden geïntegreerd met FarmBeats. Op dit moment wordt de volgende weer gegeven gegevens provider ondersteund.

  ![FarmBeats-partners](./media/get-sensor-data-from-sensor-partner/dtn-logo.png)
  
   [DTN](https://www.dtn.com/dtn-content-integration/)

De weer gegevens kunnen worden gebruikt voor het genereren van met actie bare inzichten en het bouwen van AI-of ML-modellen in FarmBeats.

## <a name="before-you-start"></a>Voordat u begint

Zorg ervoor dat u [FarmBeats hebt geïnstalleerd](./install-azure-farmbeats.md)om weer gegevens op te halen. De weers integratie wordt ondersteund in versies 1.2.11 en hoger. 

## <a name="enable-weather-integration-with-farmbeats"></a>Weers integratie met FarmBeats inschakelen

Als u wilt beginnen met het ophalen van weer gegevens op uw FarmBeats-Datahub:

1. Ga naar uw FarmBeats Datahub Swagger `https://farmbeatswebsite-api.azurewebsites.net/swagger` .

2. Ga naar de/partner-API en maak een POST-aanvraag. Gebruik de volgende invoer lading:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   Gebruik bijvoorbeeld de volgende Payload om weer gegevens van DTN op te halen. U kunt de naam en beschrijving wijzigen op basis van uw voor keur.

   > [!NOTE]
   > Voor de volgende stap is een API-sleutel vereist. Als u een sleutel voor uw DTN-abonnement wilt ophalen, neemt u contact op met DTN.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "dtnweather/dtn-farm-beats",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerCredentials": {
      "apikey": "<API key from DTN>"
      },
     "partnerType": "Weather",
     "name": "dtn-weather",
     "description": "DTN registered as a Weather Partner in FarmBeats"
   }  
   ```

   > [!NOTE]
   > Zie de [bijlage](get-weather-data-from-weather-partner.md#appendix) in dit artikel voor meer informatie over het partner object.

   In de voor gaande stap worden de resources ingericht zodat docker kan worden uitgevoerd in de FarmBeats omgeving van de klant.  

   Het duurt ongeveer 10 tot 15 minuten om de resources in te richten.

3. Controleer de status van het/partner-object dat u in de vorige stap hebt gemaakt. Als u de status wilt controleren, maakt u een GET-aanvraag in de/partner-API en controleert u de status van het partner object. Nadat FarmBeats de partner met succes heeft ingericht, wordt de status ingesteld op **actief**.

4. Maak een GET-aanvraag in de/JobType-API. Controleer op de weers taken die u eerder hebt gemaakt, in het proces voor het toevoegen van partners. In de weers-taken heeft het veld **pipelineName** de volgende indeling: **partner-name_partner-type_job-naam**.

      Uw FarmBeats-instantie heeft nu een actieve weer gegevens partner. U kunt taken uitvoeren om weer gegevens voor een bepaalde locatie (breedte graad en lengte graad) en een datum bereik op te vragen. De taak typen bevatten details over welke para meters zijn vereist voor het uitvoeren van weer taken.

      Voor DTN worden bijvoorbeeld de volgende taak typen gemaakt:
   
      - **get_dtn_daily_observations**: dagelijks waarnemingen voor een locatie en tijds periode ophalen.
      - **get_dtn_daily_forecasts**: dagelijkse prognoses voor een locatie en een tijds periode ophalen.
      - **get_dtn_hourly_observations**: er worden waarnemingen per uur opgehaald voor een locatie en tijds periode.
      - **get_dtn_hourly_forecasts**: Ontvang uurprognoses voor een locatie en een periode.

6. Noteer de ID en de para meters van de taak typen.

7. Ga naar de/Jobs-API en maak een POST-aanvraag op/jobs. Gebruik de volgende invoer lading:

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   Als u bijvoorbeeld **get_dtn_daily_observations** wilt uitvoeren, gebruikt u de volgende Payload:

   ```json
   { 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "days": 5
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. In de voor gaande stap worden de weer taken uitgevoerd zoals gedefinieerd in de partner docker en worden er weer gegevens opgenomen in FarmBeats. U kunt de status van de taak controleren door een GET-aanvraag in te stellen op/jobs. Zoek naar **currentState** in het antwoord. Wanneer u klaar bent, wordt **currentState** ingesteld op **geslaagd**.

## <a name="query-ingested-weather-data"></a>Query's opgenomen weer gegeven gegevens

Nadat de weers taken zijn voltooid, kunt u een query uitvoeren op opgenomen weer gegevens om modellen of met actie bare inzichten te maken met behulp van FarmBeats Datahub REST-Api's.

Om weer gegevens op te vragen met behulp van een FarmBeats REST API:

1. In uw FarmBeats Datahub [Swagger](https://yourdatahub.azurewebsites.net/swagger)gaat u naar de/WEATHERDATALOCATION-API en maakt u een GET-aanvraag. Het antwoord bevat/WeatherDataLocation-objecten die zijn gemaakt voor de locatie (breedte graad en lengte graad) die de taak heeft opgegeven. Noteer de **id** en de **weatherDataModelId** van de objecten.

2. Maak een GET/{id}-aanvraag voor de/WeatherDataModel-API voor de **weatherDataModelId** zoals u dat eerder hebt gedaan. Het weer gegeven gegevens model toont alle meta gegevens en Details over de opgenomen weer gegevens. In het object weer gegeven gegevens model ziet u bijvoorbeeld hoe de weers informatie wordt ondersteund en in welke typen en eenheden. Bijvoorbeeld:

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   Noteer de reactie van de aanroep GET/{id} voor het weer gegevens model.

3. Ga naar de telemetrie-API en maak een POST-aanvraag. Gebruik de volgende invoer lading:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

    Het antwoord toont de weer gegevens die beschikbaar zijn voor het opgegeven tijds bereik:

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

In het vorige voor beeld toont het antwoord gegevens voor twee tijds tempels. Het bevat ook de naam van de meting (Tempe ratuur) en de waarden van de gerapporteerde weer gegevens in de twee tijds tempels. Raadpleeg het bijbehorende weer gegevens model om het type en de eenheid van de gerapporteerde waarden te interpreteren.

## <a name="troubleshoot-job-failures"></a>Problemen met taak fouten oplossen

[Controleer de taak logboeken](troubleshoot-azure-farmbeats.md#weather-data-job-failures)om problemen met de taak op te lossen.


## <a name="appendix"></a>Bijlage

|        Partner   |  Details   |
| ------- | -------             |
|     DockerDetails-afbeeldings         |          Naam van docker-installatie kopie. Bijvoorbeeld docker.io/mydockerimage (image in hub.docker.com) of myazureacr.azurecr.io/mydockerimage (afbeelding in Azure Container Registry), enzovoort. Als er geen REGI ster wordt gegeven, wordt de standaard hub.docker.com.      |
|          DockerDetails - imageTag             |         De label naam van de docker-installatie kopie. De standaard waarde is "meest recent".     |
|  DockerDetails-referenties      |  Referenties voor toegang tot de privé-docker. De partner biedt de referenties.   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch VM-SKU. Zie [alle beschik bare virtuele Linux-machines](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor meer informatie. <BR> <BR> Geldige waarden zijn ' Small ', ' ExtraLarge ', ' large ', ' A8 ', ' A9 ', ' medium ', ' A5 ', ' A6 ', ' A7 ', ' STANDARD_D1 ', ' STANDARD_D2 ', ' STANDARD_D3 ', ' STANDARD_D4 ', ' STANDARD_D11 ', ' STANDARD_D12 ', ' STANDARD_D13 ', ' STANDARD_D14 ', ' A10 ', ' A11 ', ' STANDARD_D1_V2 ', ' STANDARD_D2_V2 ', ' STANDARD_D3_V2 ', ' STANDARD_D4_V2 ', ' STANDARD_D11_V2 ', ' STANDARD_D12_V2 ', ' STANDARD_D13_V2 ', ' STANDARD_D14_V2 ', ' STANDARD_G1 ', ' STANDARD_G2 ' , ' STANDARD_G5 ', ' STANDARD_D5_V2 ', ' BASIC_A1 ', ' BASIC_A2 ', ' BASIC_A3 ', ' BASIC_A4 ', ' STANDARD_A1 ', ' STANDARD_A2 ', ' STANDARD_A3 ', ' STANDARD_A4 ', ' STANDARD_A5 ', ' STANDARD_A6 ', ' STANDARD_A7 ', ' STANDARD_A8 ', ' STANDARD_A9 ', ' STANDARD_A10 ', ' STANDARD_A11 ', ' STANDARD_D15_V2 ', ' STANDARD_F1 ', ' STANDARD_F2 ', ' STANDARD_F4 ', ' STANDARD_F8 ', ' STANDARD_F16 ', ' STANDARD_NV6 ', ' STANDARD_NV12 ', ' STANDARD_NV24 ', ' STANDARD_NC6 ', ' STANDARD_NC12 ' , ' STANDARD_H8 ', ' STANDARD_H8m ', ' STANDARD_H16 ', ' STANDARD_H16m ', ' STANDARD_H16mr ', ' STANDARD_H16r ', ' STANDARD_A1_V2 ', ' STANDARD_A2_V2 ', ' STANDARD_A4_V2 ', ' STANDARD_A8_V2 ', ' STANDARD_A2m_V2 ', ' STANDARD_A4m_V2 ', ' STANDARD_A8m_V2 ' en ' STANDARD_M64ms '. *De standaard waarde is STANDARD_D2_V2.*  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  Aantal toegewezen computer knooppunten per batch-pool. De standaardwaarde is 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    SKU-ID van Azure Batch node-agent. Op dit moment wordt alleen de batch-knooppunt agent batch. node. Ubuntu 18,04 ondersteund.    |
| DockerDetails - partnerCredentials | Referenties voor het aanroepen van de partner-API in docker. De partner verstrekt deze informatie op basis van het ondersteunde autorisatie mechanisme; bijvoorbeeld gebruikers naam en wacht woord, of API-sleutels. |
| partnerType | "Weer". Andere partner typen in FarmBeats zijn "sensor" en "installatie kopie".  |
|  naam   |   Gewenste naam van de partner in het FarmBeats systeem.   |
|  beschrijving |  Beschrijving   |

## <a name="next-steps"></a>Volgende stappen

Nu u een query hebt uitgevoerd op sensor gegevens uit uw Azure FarmBeats-exemplaar, leert u hoe u kaarten voor uw Farms kunt [genereren](generate-maps-in-azure-farmbeats.md#generate-maps) .