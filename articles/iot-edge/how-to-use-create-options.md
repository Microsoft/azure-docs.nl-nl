---
title: CreateOptions schrijven voor modules-Azure IoT Edge | Microsoft Docs
description: CreateOptions gebruiken in het implementatie manifest om modules te configureren tijdens runtime
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9ee5536562eb3f2008908a36ff296ef2cfa337ea
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200606"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>Opties voor het maken van containers configureren voor IoT Edge modules

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Met de para meter **createOptions** in het implementatie manifest kunt u de module containers tijdens runtime configureren. Met deze para meter wordt uw controle over de modules uitgebreid en kunnen taken zoals het toestaan of beperken van de module toegang tot de resources van het hostapparaat of het configureren van netwerken.

IoT Edge-modules worden geïmplementeerd als docker-compatibele containers op uw IoT Edge apparaat. Docker biedt veel opties voor het maken van containers en deze opties zijn ook van toepassing op IoT Edge modules. Zie voor meer informatie [docker-container maken opties](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="format-create-options"></a>Opties voor het maken van opmaak

Het IoT Edge-implementatie manifest accepteert Create-opties die zijn opgemaakt als JSON. Neem bijvoorbeeld de Create-opties die automatisch zijn opgenomen voor elke edgeHub-module:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "5671/tcp": [
        {
          "HostPort": "5671"
        }
      ],
      "8883/tcp": [
        {
          "HostPort": "8883"
        }
      ],
      "443/tcp": [
        {
          "HostPort": "443"
        }
      ]
    }
  }
}
```

In dit edgeHub-voor beeld wordt de para meter **HostConfig. PortBindings** gebruikt om weer gegeven poorten in de container toe te wijzen aan een poort op het hostapparaat.

Als u de Azure IoT-Hulpprogram Ma's uitbrei dingen voor Visual Studio of Visual Studio code gebruikt, kunt u de Create-opties in JSON-indeling schrijven in de **deployment.template.jsin** het bestand. Wanneer u de extensie gebruikt om de IoT Edge oplossing te bouwen of het implementatie manifest te genereren, wordt de JSON voor u stringify in de indeling die de IoT Edge runtime verwacht. Bijvoorbeeld:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

Een tip voor het maken van schrijf opties is het gebruik van de `docker inspect` opdracht. Als onderdeel van uw ontwikkelings proces voert u de module lokaal uit met behulp van `docker run <container name>` . Wanneer de module op de gewenste manier werkt, voert u uit `docker inspect <container name>` . Met deze opdracht worden de module details in JSON-indeling uitgevoerd. Zoek de para meters die u hebt geconfigureerd en kopieer de JSON. Bijvoorbeeld:

[![Resultaten van docker inspecte edgeHub](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png)](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>Algemene scenario's

De opties voor het maken van een container bieden veel scenario's, maar hier zijn er een aantal die het meest voor komt bij het bouwen van IoT Edge oplossingen:

* [Modules toegang geven tot de opslag van de host](how-to-access-host-storage-from-module.md)
* [Host-poort toewijzen aan module poort](#map-host-port-to-module-port)
* [Module geheugen en CPU-gebruik beperken](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>Host-poort toewijzen aan module poort

Als uw module moet communiceren met een service buiten de IoT Edge-oplossing, en hiervoor geen bericht routering gebruikt, moet u een host poort toewijzen aan een module poort.

>[!TIP]
>Deze poort toewijzing is niet vereist voor de communicatie van module-naar-module op hetzelfde apparaat. Als module A een query moet uitvoeren op een API die wordt gehost op module B, kan deze zonder poort toewijzing worden uitgevoerd. Module B moet een poort beschikbaar maken in de dockerfile, bijvoorbeeld: `EXPOSE 8080` . Vervolgens kan module A een query uitvoeren op de API met behulp van de naam van module B, bijvoorbeeld: `http://ModuleB:8080/api` .

Zorg er eerst voor dat een poort in de module wordt weer gegeven om te Luis teren naar verbindingen. U kunt dit doen met behulp van een [openbare](https://docs.docker.com/engine/reference/builder/#expose) instructie in de dockerfile. Bijvoorbeeld `EXPOSE 8080`. De standaard instelling voor het weer geven van het TCP-protocol is niet opgegeven, of u kunt UDP opgeven.

Vervolgens gebruikt u de instelling **PortBindings** in de groep **HostConfig** van de [docker-container maken](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) om de weer gegeven poort in de module toe te wijzen aan een poort op het hostapparaat. Als u bijvoorbeeld poort 8080 in de module hebt weer gegeven en wilt toewijzen aan poort 80 van het hostapparaat, zien de opties in de template.jsin het bestand er als volgt uit:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "8080/tcp": [
        {
          "HostPort": "80"
        }
      ]
    }
  }
}
```

Nadat stringified voor het implementatie manifest is uitgevoerd, ziet dezelfde configuratie er als in het volgende voor beeld:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>Module geheugen en CPU-gebruik beperken

U kunt declareren welk deel van de host-resources een module kan gebruiken. Dit besturings element is handig om ervoor te zorgen dat een module niet te veel geheugen of CPU-gebruik kan verbruiken en dat andere processen niet op het apparaat kunnen worden uitgevoerd. U kunt deze instellingen beheren met [docker-container maken opties](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) in de groep **HostConfig** , met inbegrip van:

* **Geheugen**: geheugen limiet in bytes. Bijvoorbeeld 268435456 bytes = 256 MB.
* **Memory**: totale geheugen limiet (geheugen + wisseling). Bijvoorbeeld 536870912 bytes = 512 MB
* **CpuPeriod**: de lengte van een CPU-periode in micro seconden. De standaard waarde is 100000, dus als een waarde van 25000, wordt een container beperkt tot 25% van de CPU-resources.

In de notatie template.jsop worden deze waarden weer geven als in het volgende voor beeld:

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

Zodra stringified voor het definitieve implementatie manifest, worden deze waarden weer geven als in het volgende voor beeld:

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>Volgende stappen

Voor meer voor beelden van het maken van opties in actie raadpleegt u de volgende IoT Edge-voor beelden:

* [Custom Vision en Azure IoT Edge op een Raspberry Pi 3](https://github.com/Azure-Samples/custom-vision-service-iot-edge-raspberry-pi)
* [Voor beeld van Azure IoT Edge Blob-opslag](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
