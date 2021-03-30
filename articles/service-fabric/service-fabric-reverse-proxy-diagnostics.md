---
title: Diagnostische gegevens van Azure Service Fabric reverse-proxy
description: Meer informatie over het bewaken en diagnosticeren van aanvraag verwerking bij de omgekeerde proxy voor een Azure Service Fabric-toepassing.
author: kavyako
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: bbc1fe5a76ecb5720bc49e0a082d5e9151b403d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75645460"
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>Verwerking van aanvragen bewaken en diagnoses uitvoeren bij de omgekeerde proxy

Vanaf de 5,7-release van Service Fabric zijn omgekeerde proxy gebeurtenissen beschikbaar voor de verzameling. De gebeurtenissen zijn beschikbaar in twee kanalen, één met alleen fout gebeurtenissen met betrekking tot het verwerken van aanvragen bij de omgekeerde proxy en het tweede kanaal met uitgebreide gebeurtenissen met vermeldingen voor zowel geslaagde als mislukte aanvragen.

Raadpleeg [reverse proxy-gebeurtenissen verzamelen](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) om het verzamelen van gebeurtenissen van deze kanalen in lokale en Azure service Fabric-clusters mogelijk te maken.

## <a name="troubleshoot-using-diagnostics-logs"></a>Problemen oplossen met Diagnostische logboeken
Hier volgen enkele voor beelden van het interpreteren van de veelvoorkomende fout logboeken die zich kunnen voordoen:

1. Reverse proxy retourneert antwoord status code 504 (time-out).

    Een reden kan zijn dat de service niet reageert binnen de time-outperiode van de aanvraag.
   De eerste gebeurtenis hieronder registreert de details van de aanvraag die bij de omgekeerde proxy is ontvangen. 
   De tweede gebeurtenis geeft aan dat de aanvraag is mislukt tijdens het door sturen naar de service vanwege "interne fout = ERROR_WINHTTP_TIMEOUT" 

    De payload omvat:

   * **traceId**: deze GUID kan worden gebruikt om alle gebeurtenissen te correleren die overeenkomen met één aanvraag. In de onderstaande twee gebeurtenissen is het traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271** dat ze tot dezelfde aanvraag behoren.
   * **requestUrl**: de URL (reverse proxy-URL) waarnaar de aanvraag is verzonden.
   * **Verb**: HTTP-term.
   * **remoteAddress**: het adres van de client die de aanvraag verzendt.
   * **resolvedServiceUrl**: de URL van het service-eind punt waarnaar de binnenkomende aanvraag is opgelost. 
   * **Error Details**: aanvullende informatie over de fout.

     ```
     {
     "Timestamp": "2017-07-20T15:57:59.9871163-07:00",
     "ProviderName": "Microsoft-ServiceFabric",
     "Id": 51477,
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Request url = https://localhost:19081/LocationApp/LocationFEService?zipcode=98052, verb = GET, remote (client) address = ::1, resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052, request processing start time =     15:58:00.074114 (745,608.196 MSec) ",
     "ProcessId": 57696,
     "Level": "Informational",
     "Keywords": "0x1000000000000021",
     "EventName": "ReverseProxy",
     "ActivityID": null,
     "RelatedActivityID": null,
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?zipcode=98052",
      "verb": "GET",
      "remoteAddress": "::1",
      "resolvedServiceUrl": "Https://localhost:8491/LocationApp/?zipcode=98052",
      "requestStartTime": "2017-07-20T15:58:00.0741142-07:00"
     }
     }

     {
     "Timestamp": "2017-07-20T16:00:01.3173605-07:00",
     ...
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Error while forwarding request to service: response status code = 504, description = Reverse proxy Timeout, phase = FinishSendRequest, internal error = ERROR_WINHTTP_TIMEOUT ",
     ...
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "statusCode": 504,
      "description": "Reverse Proxy Timeout",
      "sendRequestPhase": "FinishSendRequest",
      "errorDetails": "internal error = ERROR_WINHTTP_TIMEOUT"
     }
     }
     ```

2. Reverse proxy retourneert antwoord status code 404 (niet gevonden). 
    
    Hier volgt een voorbeeld gebeurtenis waarbij reverse proxy 404 retourneert omdat het overeenkomende service-eind punt niet is gevonden.
    De nettolading van de volgende belang rijke items zijn:
   * **processRequestPhase**: geeft de fase aan tijdens de verwerking van aanvragen wanneer de fout is opgetreden, **_TryGetEndpoint_** dat wil zeggen tijdens een poging om het service-eind punt op te halen om door te gaan. 
   * **Error Details**: geeft een lijst van de zoek criteria voor het eind punt. Hier kunt u zien dat de listenerName opgegeven = **FrontEndListener** , terwijl de lijst met replica-eind punten alleen een listener bevat met de naam **OldListener**.
    
     ```
     {
     ...
     "Message": "c1cca3b7-f85d-4fef-a162-88af23604343 Error while processing request, cannot forward to service: request url = https://localhost:19081/LocationApp/LocationFEService?ListenerName=FrontEndListener&zipcode=98052, verb = GET, remote (client) address = ::1, request processing start time = 16:43:02.686271 (3,448,220.353 MSec), error = FABRIC_E_ENDPOINT_NOT_FOUND, message = , phase = TryGetEndoint, SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"\":\"Https:\/\/localhost:8491\/LocationApp\/\"}} ",
     "ProcessId": 57696,
     "Level": "Warning",
     "EventName": "ReverseProxy",
     "Payload": {
      "traceId": "c1cca3b7-f85d-4fef-a162-88af23604343",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?ListenerName=NewListener&zipcode=98052",
      ...
      "processRequestPhase": "TryGetEndoint",
      "errorDetails": "SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Https:\/\/localhost:8491\/LocationApp\/\"}}"
     }
     }
     ```
     Een ander voor beeld waarbij omgekeerde proxy 404 niet kan worden gevonden is: ApplicationGateway\Http Configuration para meter **SecureOnlyMode** is ingesteld op True met de reverse proxy luistert op **https**, maar niet alle replica-eind punten zijn onveilig (Luis teren op http).
     Omgekeerde proxy retourneert 404 omdat er geen eind punt luistert op HTTPS om de aanvraag door te sturen. Het analyseren van de para meters in de gebeurtenis lading helpt het probleem te verfijnen:
    
     ```
      "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
     ```

3. De aanvraag voor de omgekeerde proxy mislukt met een time-outfout. 
    De gebeurtenis logboeken bevatten een gebeurtenis met de ontvangen aanvraag Details (deze worden hier niet weer gegeven).
    De volgende gebeurtenis geeft aan dat de service heeft gereageerd met een 404-status code en een omgekeerde proxy initieert een nieuwe oplossing. 

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Request to service returned: status code = 404, status description = , Reresolving ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "statusCode": 404,
        "statusDescription": ""
      }
    }
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Re-resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052 ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "requestUrl": "Https://localhost:8491/LocationApp/?zipcode=98052"
      }
    }
    ```
    Bij het verzamelen van alle gebeurtenissen ziet u een Train van gebeurtenissen die elke keer oplossen en door sturen worden weer gegeven.
    De laatste gebeurtenis in de reeks toont de verwerking van de aanvraag is mislukt met een time-out, samen met het aantal geslaagde verholpen pogingen.
    
    > [!NOTE]
    > Het is raadzaam om de uitgebreide kanaal gebeurtenis verzameling standaard uitgeschakeld te laten en deze in te scha kelen voor probleem oplossing op basis van behoefte.

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Error while processing request: number of successful resolve attempts = 12, error = FABRIC_E_TIMEOUT, message = , phase = ResolveServicePartition,  ",
      "EventName": "ReverseProxy",
      ...
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "resolveCount": 12,
        "errorval": -2147017729,
        "errorMessage": "",
        "processRequestPhase": "ResolveServicePartition",
        "errorDetails": ""
      }
    }
    ```
    
    Als de verzameling is ingeschakeld voor kritieke/fout gebeurtenissen, ziet u één gebeurtenis met details over de time-out en het aantal pogingen tot oplossen. 
    
    Services die van plan zijn een 404-status code terug te sturen naar de gebruiker, moet een ' X-ServiceFabric-header in het antwoord toevoegen. Nadat de header aan het antwoord is toegevoegd, stuurt de reverse-proxy de status code terug naar de client.  

4. Gevallen waarin de client de verbinding met de aanvraag heeft verbroken.

    De volgende gebeurtenis wordt vastgelegd wanneer de omgekeerde proxy de reactie doorstuurt naar de client, maar de client verbreekt de verbinding:

    ```
    {
      ...
      "Message": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8 Unable to send response to client: phase = SendResponseHeaders, error = -805306367, internal error = ERROR_SUCCESS ",
      "ProcessId": 57696,
      "Level": "Warning",
      ...
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8",
        "sendResponsePhase": "SendResponseHeaders",
        "errorval": -805306367,
        "winHttpError": "ERROR_SUCCESS"
      }
    }
    ```
5. Reverse proxy retourneert 404 FABRIC_E_SERVICE_DOES_NOT_EXIST

    FABRIC_E_SERVICE_DOES_NOT_EXIST fout wordt geretourneerd als het URI-schema niet is opgegeven voor het service-eind punt in het service manifest.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    Om het probleem op te lossen, geeft u het URI-schema op in het manifest.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> Gebeurtenissen met betrekking tot de verwerking van WebSocket-aanvragen zijn momenteel niet geregistreerd. Deze wordt toegevoegd aan de volgende release.

## <a name="next-steps"></a>Volgende stappen
* [Gebeurtenis aggregatie en verzameling met behulp van Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) voor het inschakelen van logboek verzameling in azure-clusters.
* Zie voor het weer geven van Service Fabric gebeurtenissen in Visual Studio [lokaal controleren en diagnoses](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)uitvoeren.
* Raadpleeg [reverse proxy configureren om verbinding te maken met beveiligde services](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services) voor voor beelden van Azure Resource Manager-sjablonen voor het configureren van beveiligde omgekeerde proxy met de verschillende validatie opties voor service certificaten.
* Lees [service Fabric omgekeerde proxy](service-fabric-reverseproxy.md) voor meer informatie.
