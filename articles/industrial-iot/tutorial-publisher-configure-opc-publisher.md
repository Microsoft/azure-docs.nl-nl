---
title: De micro soft OPC-Uitgever configureren
description: In deze zelf studie leert u hoe u de OPC-uitgever in de zelfstandige modus kunt configureren.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 4d4f9c90fd96365216480164f29f08fad92eb9d0
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787714"
---
# <a name="tutorial-configure-the-opc-publisher"></a>Zelf studie: de OPC-Uitgever configureren

In deze zelf studie bevat informatie over de configuratie van de OPC-Uitgever. Verschillende interfaces kunnen worden gebruikt om deze te configureren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De OPC-Uitgever configureren via het configuratie bestand
> * De OPC-Uitgever configureren via opdracht regel argumenten
> * De OPC-Uitgever configureren via IoT Hub directe methoden
> * De OPC-Publisher configureren via Cloud, REST micro service van de Companion

## <a name="configuring-security"></a>Beveiliging configureren

IoT Edge biedt OPC Publisher met de beveiligings configuratie voor het automatisch openen van IoT Hub. OPC Publisher kan ook worden uitgevoerd als een zelfstandige docker-container door een apparaat connection string op te geven voor toegang tot IoT Hub via de `dc` opdracht regel parameter. Een apparaat voor IoT Hub kan worden gemaakt en de connection string worden opgehaald via de Azure Portal.

Voor toegang tot OPC UA-ingeschakelde assets, X. 509-certificaten en de bijbehorende persoonlijke sleutels worden gebruikt door OPC UA. Dit heet OPC UA-toepassings verificatie en naast OPC UA-gebruikers verificatie. OPC Publisher gebruikt een op bestanden systeem gebaseerd certificaat archief om alle toepassings certificaten te beheren. Bij het opstarten controleert OPC Publisher of er een certificaat is dat kan worden gebruikt in dit certificaat archief en maakt een nieuw zelfondertekend certificaat en een nieuwe gekoppelde persoonlijke sleutel als er geen is. Zelfondertekende certificaten bieden zwakke verificatie, omdat ze niet zijn ondertekend door een vertrouwde certificerings instantie, maar ten minste de communicatie met de OPC UA-ingeschakelde Asset op deze manier kan worden versleuteld.

Beveiliging is ingeschakeld in het configuratie bestand via de `"UseSecurity": true,` vlag. Het veiligste eind punt dat beschikbaar is op de OPC UA-servers waarmee de OPC-Publisher verbinding moet maken, wordt automatisch geselecteerd.
OPC Publisher gebruikt standaard anonieme gebruikers verificatie (in aanvulling op de hierboven beschreven verificatie van de toepassing). OPC Publisher ondersteunt echter ook gebruikers verificatie met behulp van de gebruikers naam en het wacht woord. Dit kan worden opgegeven via de REST API configuratie-interface (hieronder beschreven) of het configuratie bestand als volgt:
```
"OpcAuthenticationMode": "UsernamePassword",
"OpcAuthenticationUsername": "usr",
"OpcAuthenticationPassword": "pwd",
```
Bovendien versleutelt OPC Publisher-versie 2,5 en lager de gebruikers naam en het wacht woord in het configuratie bestand. Versie 2,6 en hoger ondersteunt alleen de gebruikers naam en het wacht woord als Lees bare tekst. Dit wordt verbeterd in de volgende versie van de OPC-Uitgever.

Als u de beveiligings configuratie van de OPC-uitgever wilt behouden bij het opnieuw opstarten, moet het certificaat en de persoonlijke sleutel in de map van het certificaat archief worden toegewezen aan het bestands systeem van de IoT Edge host-besturings systeem. Zie ' Geef de opties voor het maken van containers op in het Azure Portal ' hierboven.

## <a name="configuration-via-configuration-file"></a>Configuratie via configuratie bestand

De eenvoudigste manier om OPC Publisher te configureren via een configuratie bestand. Er wordt een voorbeeld configuratie bestand en documentatie over de indeling geleverd via het bestand [`publishednodes.json`](https://raw.githubusercontent.com/Azure/iot-edge-opc-publisher/master/opcpublisher/publishednodes.json) in deze opslag plaats.
De syntaxis van het configuratie bestand is gewijzigd na verloop van tijd en OPC Publisher kan nog steeds oude indelingen lezen, maar ze worden omgezet in de meest recente indeling wanneer de configuratie persistent wordt gemaakt.

Een basis configuratie bestand ziet er als volgt uit:
```
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

OPC UA-assets optimaliseren netwerk bandbreedte door alleen gegevens wijzigingen naar OPC Publisher te verzenden wanneer de gegevens zijn gewijzigd. Als de gegevens wijzigingen vaker of met regel matige tussen pozen moeten worden gepubliceerd, ondersteunt OPC Publisher een ' heartbeat ' voor elk geconfigureerd gegevens item dat kan worden ingeschakeld door ook de HeartbeatInterval-sleutel op te geven in de configuratie van het gegevens item. Het interval wordt opgegeven in seconden:
```
 "HeartbeatInterval": 3600,
```

Een OPC UA-Asset verzendt altijd de huidige waarde van een gegevens item wanneer OPC Publisher er eerst verbinding mee maakt. Om te voor komen dat deze gegevens naar IoT Hub worden gepubliceerd, kan de SkipFirst-sleutel ook worden opgegeven in de configuratie van het gegevens item:
```
 "SkipFirst": true,
```

Beide instellingen kunnen ook globaal worden ingeschakeld via opdracht regel opties.

## <a name="configuration-via-command-line-arguments"></a>Configuratie via opdracht regel argumenten

Er zijn verschillende opdracht regel argumenten die kunnen worden gebruikt om algemene instellingen voor OPC-Publisher in te stellen. Deze worden [hier](reference-command-line-arguments.md)beschreven.


## <a name="configuration-via-the-built-in-opc-ua-server-interface"></a>Configuratie via de ingebouwde OPC UA-Server Interface

>[!NOTE] 
> Deze functie is alleen beschikbaar in versie 2,5 en lager van de OPC-Uitgever. * *

OPC Publisher heeft een ingebouwde OPC UA-server die wordt uitgevoerd op poort 62222. Het implementeert drie OPC UA-methoden:

  - PublishNode
  - UnpublishNode
  - GetPublishedNodes

Deze interface kan worden geopend met een OPC UA-client toepassing, bijvoorbeeld [ua expert](https://www.unified-automation.com/products/development-tools/uaexpert.html).

## <a name="configuration-via-iot-hub-direct-methods"></a>Configuratie via IoT Hub directe methoden

>[!NOTE] 
> Deze functie is alleen beschikbaar in versie 2,5 en lager van de OPC-Uitgever. * *

OPC Publisher implementeert de volgende [IOT hub directe methoden](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-direct-methods), die kunnen worden aangeroepen vanuit een toepassing (vanaf elke locatie ter wereld) met [behulp van de IOT hub apparaat-SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks):

  - PublishNodes
  - UnpublishNodes
  - UnpublishAllNodes
  - GetConfiguredEndpoints
  - GetConfiguredNodesOnEndpoint
  - GetDiagnosticInfo
  - GetDiagnosticLog
  - GetDiagnosticStartupLog
  - ExitApplication
  - GetInfo

We hebben een voor [beeld van een configuratie toepassing](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) en een [toepassing voor het lezen van diagnostische gegevens](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics) van OPC Publisher open-source, met deze interface.

## <a name="configuration-via-cloud-based-companion-rest-microservice"></a>Configuratie via Cloud, REST micro service van de Companion

>[!NOTE] 
> Deze functie is alleen beschikbaar in versie 2,6 en hoger van de OPC-Uitgever.

Een op de cloud gebaseerde, aanvullende micro service met een REST-interface wordt [hier](https://github.com/Azure/Industrial-IoT/blob/master/docs/services/publisher.md)beschreven en beschikbaar. Het kan worden gebruikt om OPC-Publisher te configureren via een OpenAPI-compatibele interface, bijvoorbeeld via Swagger.

## <a name="configuration-of-the-simple-json-telemetry-format-via-separate-configuration-file"></a>Configuratie van de eenvoudige JSON-telemetrie-indeling via een afzonderlijk configuratie bestand

>[!NOTE] 
> Deze functie is alleen beschikbaar in versie 2,5 en lager van de OPC-Uitgever.

OPC Publisher maakt het mogelijk om de onderdelen van de niet-gestandaardiseerde, eenvoudige telemetrie-indeling te filteren via een afzonderlijk configuratie bestand, dat kan worden opgegeven via de TC-opdracht regel optie. Als er geen configuratie bestand is opgegeven, wordt de volledige indeling van de JSON-telemetrie naar IoT Hub verzonden. De indeling van het afzonderlijke telemetrie-configuratie bestand wordt [hier](reference-opc-publisher-telemetry-format.md#opc-publisher-telemetry-configuration-file-format)beschreven.

## <a name="next-steps"></a>Volgende stappen
Nu u de OPC-Publisher hebt geconfigureerd, is de volgende stap informatie over het afstemmen van de prestaties en het geheugen van de module Edge:

> [!div class="nextstepaction"]
> [Prestaties en geheugen afstemmen](tutorial-publisher-performance-memory-tuning-opc-publisher.md)