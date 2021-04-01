---
title: Azure IoT Hub ontwikkelen voor beperkte apparaten met behulp van IoT Hub C SDK
description: Hand leiding voor ontwikkel aars-richt lijnen voor het ontwikkelen met Azure IoT Sdk's voor beperkte apparaten.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 5a43eb2537ebc09ffcb524a4426d7a8c9bec560b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96499999"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Ontwikkelen voor beperkte apparaten met behulp van de Azure IoT C-SDK

De Azure IoT Hub C SDK is geschreven in ANSI C (C99), waardoor het geschikt is voor het uitvoeren van verschillende platforms met kleine schijf-en geheugen ruimte. Het aanbevolen RAM-geheugen is ten minste 64 KB, maar de exacte geheugen capaciteit is afhankelijk van het gebruikte protocol, het aantal geopende verbindingen en het doel platform.
> [!NOTE]
> * De Azure IoT C SDK publiceert regel matig informatie over het verbruik van resources om te helpen bij het ontwikkelen.  Ga naar onze [github-opslag plaats](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md) en Bekijk de nieuwste benchmark versie.
>

C SDK is beschikbaar in pakket vorm van apt-get, NuGet en MBED. Als u beperkte apparaten wilt richten, kunt u de SDK lokaal maken voor uw doel platform. In deze documentatie ziet u hoe u bepaalde functies kunt verwijderen om de footprint van de C SDK te verkleinen met behulp van [cmake](https://cmake.org/). Daarnaast wordt in deze documentatie de best practice-programmeer modellen besproken voor het werken met beperkte apparaten.

## <a name="building-the-c-sdk-for-constrained-devices"></a>De C SDK bouwen voor beperkte apparaten

Bouw de C SDK voor beperkte apparaten.

> [!NOTE]
> De embedded C SDK is een alternatief voor beperkte apparaten die ondersteuning bieden voor de aanpak van uw eigen netwerk (BYON). IoT-ontwikkel aars hebben de vrijheid om de MQTT-client, TLS en socket van hun keuze te maken om een oplossing voor een apparaat te creëren. Meer [informatie over de embedded C SDK](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot).

### <a name="prerequisites"></a>Vereisten

Volg deze [C SDK-installatie handleiding](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) om uw ontwikkel omgeving voor te bereiden voor het bouwen van de C-SDK. Voordat u naar de stap voor het maken van cmake gaat, kunt u cmake-vlaggen aanroepen om ongebruikte onderdelen te verwijderen.

### <a name="remove-additional-protocol-libraries"></a>Extra protocol bibliotheken verwijderen

C SDK ondersteunt nu vijf protocollen: MQTT, MQTT via WebSocket, AMQPs, AMQP via WebSocket en HTTPS. De meeste scenario's vereisen een tot twee protocollen die worden uitgevoerd op een client. Daarom kunt u de Protocol bibliotheek die u niet gebruikt, verwijderen uit de SDK. Meer informatie over het kiezen van het juiste communicatie protocol voor uw scenario vindt u in [een IOT hub communicatie protocol kiezen](iot-hub-devguide-protocols.md). MQTT is bijvoorbeeld een licht gewicht protocol dat vaak beter geschikt is voor beperkte apparaten.

U kunt de AMQP-en HTTP-bibliotheken verwijderen met de volgende cmake-opdracht:

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>De SDK-logboek functie verwijderen

De C SDK voorziet in uitgebreide logboek registratie om u te helpen bij het opsporen van fouten. U kunt de logboek functie voor productie apparaten verwijderen met de volgende cmake-opdracht:

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Upload naar BLOB-mogelijkheid verwijderen

U kunt grote bestanden uploaden naar Azure Storage met behulp van de ingebouwde mogelijkheid van de SDK. Azure IoT Hub fungeert als een verzender naar een gekoppeld Azure Storage-account. U kunt deze functie gebruiken voor het verzenden van media bestanden, grote telemetriegegevens en Logboeken. U kunt meer informatie krijgen over het [uploaden van bestanden met IOT hub](iot-hub-devguide-file-upload.md). Als uw toepassing deze functionaliteit niet vereist, kunt u deze functie verwijderen met de volgende cmake-opdracht:

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Strip op Linux-omgeving wordt uitgevoerd

Als uw binaire bestanden op een Linux-systeem worden uitgevoerd, kunt u de [strook opdracht](https://en.wikipedia.org/wiki/Strip_(Unix)) gebruiken om de grootte van de uiteindelijke toepassing na het compileren te verkleinen.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>Programmeer modellen voor beperkte apparaten

Bekijk vervolgens de programmeer modellen voor beperkte apparaten.

### <a name="avoid-using-the-serializer"></a>Vermijd het gebruik van de Serialisatiefunctie

De C SDK bevat een optionele [C SDK serializer](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer), waarmee u declaratieve toewijzings tabellen kunt gebruiken om methoden en dubbele eigenschappen van het apparaat te definiëren. De serialisatiefunctie is ontworpen om de ontwikkeling te vereenvoudigen, maar voegt overhead toe, wat niet optimaal is voor beperkte apparaten. In dit geval kunt u overwegen primitieve client-Api's te gebruiken en JSON te parseren met behulp van een licht gewicht parser, zoals [Parson](https://github.com/kgabis/parson).

### <a name="use-the-lower-layer-_ll_"></a>De lagere laag (_ll_) gebruiken

De C SDK ondersteunt twee programmeer modellen. Eén set heeft Api's met een _ll_ infix, die staat voor een lagere laag. Deze set Api's is lichter gewicht en er worden geen worker-threads gespind, wat betekent dat de gebruiker de planning hand matig moet beheren. De _ll_ -api's kunnen bijvoorbeeld voor de apparaatclient worden gevonden in dit [header-bestand](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h). 

Een andere set Api's zonder de _ll_ -index heet de laag van het gemak, waarbij een werk thread automatisch wordt. De gebruiks gemak-Api's voor de apparaatclient kunnen bijvoorbeeld worden gevonden in het header- [bestand](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h)van de client voor IOT-apparaten. Voor beperkte apparaten waarbij elke extra thread een aanzienlijk percentage systeem bronnen kan hebben, kunt u het gebruik van _ll_ api's overwegen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de Azure IoT C SDK-architectuur:
-    [Azure IoT C SDK-bron code](https://github.com/Azure/azure-iot-sdk-c/)
-    [Azure IoT Device SDK voor C-Inleiding](iot-hub-device-sdk-c-intro.md)
