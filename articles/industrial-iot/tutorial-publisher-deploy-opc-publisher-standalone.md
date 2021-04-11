---
title: De micro soft OPC-Uitgever implementeren
description: In deze zelf studie leert u hoe u de OPC-Publisher kunt implementeren in de zelfstandige modus.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: c82d15541459b5b482e427fc707b92755aa02c6c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787690"
---
# <a name="tutorial-deploy-the-opc-publisher"></a>Zelf studie: de OPC-Uitgever implementeren

OPC Publisher is een volledig ondersteund micro soft-product dat is ontwikkeld in de open, waardoor de kloof tussen industriële activa en de Microsoft Azure Cloud wordt overbrugd. Dit doet u door verbinding te maken met OPC UA-ingeschakelde assets of industriële connectiviteits software en om telemetriegegevens te publiceren naar [Azure IOT hub](https://azure.microsoft.com/services/iot-hub/) in verschillende indelingen, waaronder IEC62541 OPC UA PubSub Standard-indeling (vanaf versie 2,6 en hoger).

Het wordt uitgevoerd op [Azure IOT Edge](https://azure.microsoft.com/services/iot-edge/) als een module of op een gewone docker als container. Omdat het gebruikmaakt van [.net platformoverschrijdende runtime](https://docs.microsoft.com/dotnet/core/introduction), wordt het ook systeem eigen uitgevoerd op Linux en Windows 10.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De OPC-Uitgever implementeren
> * De meest recente versie van de OPC-Uitgever uitvoeren als een container
> * Geef de opties voor het maken van containers op in de Azure Portal

Als u geen Azure-abonnement hebt, maakt u een gratis proef account

## <a name="prerequisites"></a>Vereisten

- Er moet een IoT Hub worden gemaakt
- Een IoT Edge apparaat moet worden gemaakt

## <a name="deploy-the-opc-publisher-from-the-azure-marketplace"></a>De OPC-Uitgever implementeren vanuit Azure Marketplace

1. Kies het Azure-abonnement dat u wilt gebruiken. Als er geen Azure-abonnement beschikbaar is, moet er een worden gemaakt.
2. Kies de IoT Hub de OPC-uitgever moet gegevens verzenden naar. Als er geen IoT Hub beschikbaar is, moet er een worden gemaakt.
3. Kies het IoT Edge apparaat waarop de OPC-uitgever moet worden uitgevoerd (of voer een naam in voor een nieuw IoT Edge apparaat dat moet worden gemaakt).
4. Klik op Maken. De pagina ' modules op apparaat instellen ' voor het geselecteerde IoT Edge apparaat wordt geopend.
5. Klik op ' OPCPublisher ' om de pagina met de OPC-Uitgever ' update IoT Edge module ' te openen en selecteer vervolgens ' container maken opties '.
6. Geef extra container opties op op basis van uw gebruik van OPC Publisher. Zie de volgende sectie hieronder.


### <a name="accessing-the-microsoft-container-registry-docker-containers-for-opc-publisher-manually"></a>Hand matig toegang tot de micro soft Container Registry docker-containers voor OPC Publisher

De meest recente versie van de OPC-uitgever kan hand matig worden uitgevoerd via:

```
docker run mcr.microsoft.com/iotedge/opc-publisher:latest <name>
```

Waarbij "naam" de naam is van de container.

## <a name="specifying-container-create-options-in-the-azure-portal"></a>Opties voor het maken van containers opgeven in de Azure Portal
Wanneer u OPC Publisher implementeert via de Azure Portal, kunt u opties voor het maken van containers opgeven op de pagina IoT Edge module bijwerken van de OPC-Uitgever. Deze Create-opties moeten de JSON-indeling hebben. De OPC Publisher opdracht regel argumenten kunnen worden opgegeven via de toets cmd, bijvoorbeeld:
```
"Cmd": [
    "--pf=./pn.json",
    "--aa"
],
```

Een typische set opties voor het maken van IoT Edge module container voor OPC Publisher is:
```
{
    "Hostname": "opcpublisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "/iiotedge:/appdata"
        ]
    }
}
```

Als u deze opties hebt opgegeven, wordt in OPC Publisher het configuratie bestand gelezen `./pn.json` . De OPC-Uitgever van de Publisher is ingesteld op `/appdata` bij het opstarten. de functie OPC Publisher leest het bestand `/appdata/pn.json` in de docker-container. OPC-logboek bestand van de Publisher wordt geschreven naar `/appdata` en de `CertificateStores` map (gebruikt voor OPC UA-certificaten) wordt ook in deze map gemaakt. Als u deze bestanden beschikbaar wilt maken in het bestands systeem van de IoT Edge-host, is voor de container configuratie een bindings koppel volume vereist. Met de `/iiotedge:/appdata` binding wordt de map toegewezen `/appdata` aan de host `/iiotedge` -map (die wordt gemaakt door de IOT Edge runtime als deze niet bestaat).
**Zonder dit volume voor bindings koppeling worden alle OPC Publisher-configuratie bestanden verwijderd wanneer de container opnieuw wordt gestart.**

Een verbinding met een OPC UA-server die gebruikmaakt van de hostnaam zonder een DNS-server die op het netwerk is geconfigureerd, kan worden bereikt door een vermelding toe te voegen `ExtraHosts` aan de `HostConfig` sectie:

```
"HostConfig": {
    "ExtraHosts": [
        "opctestsvr:192.168.178.26"
    ]
}
```

## <a name="next-steps"></a>Volgende stappen 
Nu u de OPC Publisher Edge-module hebt geïmplementeerd, is de volgende stap het configureren van de sjabloon:

> [!div class="nextstepaction"]
> [De OPC-Uitgever configureren](tutorial-publisher-configure-opc-publisher.md)