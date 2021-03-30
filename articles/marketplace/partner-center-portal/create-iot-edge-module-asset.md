---
title: De technische activa van de IoT Edge module voorbereiden-Azure Marketplace
description: Meer informatie over de technische en configuratie vereisten uw Internet of Things (IoT)-rand module technische activa moeten voldoen voordat u deze naar Azure Marketplace kunt publiceren.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/03/2020
ms.openlocfilehash: aca27b89a3b92b410fa560c8b4bd7eb3d4e0a935
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93346771"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>De technische activa van uw IoT Edge module voorbereiden

In dit artikel worden de vereisten beschreven waaraan de technische activa van uw Internet of Things (IoT) Edge-module moeten voldoen voordat ze worden gepubliceerd in azure Marketplace.

## <a name="get-started"></a>Aan de slag

Een IoT Edge module is een docker-compatibele container die wordt uitgevoerd op een IoT Edge apparaat.

- Zie [Azure IOT Edge modules begrijpen](../../iot-edge/iot-edge-modules.md)voor meer informatie over IOT Edge-modules.
- Zie [uw eigen IOT Edge modules ontwikkelen](../../iot-edge/module-development.md)om aan de slag te gaan met de ontwikkeling van uw IOT Edge-module.

## <a name="technical-requirements"></a>Technische vereisten

Uw IoT Edge-module moet voldoen aan de volgende technische vereisten om te worden gecertificeerd en gepubliceerd in azure Marketplace.

### <a name="platform-support"></a>Platformondersteuning

De module IoT Edge moet een van de volgende platform opties ondersteunen:

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Platforms van laag 1 die door IoT Edge worden ondersteund

Uw module moet ondersteuning bieden voor alle platforms van laag 1 die door IoT Edge worden ondersteund (zoals vastgelegd in [Azure IOT EDGE-ondersteuning](../../iot-edge/support.md)). We raden u aan deze optie te kiezen omdat deze een betere klant ervaring biedt. De modules die aan deze criteria voldoen, worden weer gepresenteerd. Een module die gebruikmaakt van deze platform optie moet:

- Geef een van de nieuwste Tags en een versie label (bijvoorbeeld 1.0.1) op die zijn gemaakt met het [manifest hulp programma github](https://github.com/estesp/manifest-tool).

- Gebruik het tabblad aanbiedings vermelding in het [partner centrum](https://partner.microsoft.com/dashboard/commercial-marketplace) om een koppeling toe te voegen aan de sectie **handige koppelingen** naar de [Azure IOT Edge gecertificeerde-stuurprogrammacatalogus](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}/).

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Een subset van laag 1-platforms die door IoT Edge worden ondersteund

Uw module moet ondersteuning bieden voor een subset (ten minste één) van laag 1-platformen die door IoT Edge worden ondersteund (zoals vastgelegd in [Azure IOT EDGE-ondersteuning](../../iot-edge/support.md)). Een module die gebruikmaakt van deze platform optie moet:

- Geef een laatste tag en een versie label (bijvoorbeeld 1.0.1) op die zijn gemaakt met het GitHub [-manifest hulp programma](https://github.com/estesp/manifest-tool) als meer dan één platform wordt ondersteund. Manifest Tags zijn alleen optioneel wanneer één platform wordt ondersteund.
- Gebruik het tabblad aanbiedings vermelding in het [partner centrum](https://partner.microsoft.com/dashboard/commercial-marketplace) om een koppeling toe te voegen onder de sectie **handige koppelingen** naar ten minste één IOT edge apparaat uit de [catalogus met Azure IOT Edge-gecertificeerde apparaten](https://catalog.azureiotsolutions.com/).

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="Dit is een afbeelding van de aanbiedings vermelding in het partner centrum":::

### <a name="device-dimensions"></a>Afmetingen van apparaat

IoT Edge module dimensies (zoals CPU, RAM, opslag en GPU) op doel IoT Edge apparaten moeten voldoen aan de volgende vereisten:

- De module moet samen werken met ten minste één IoT Edge apparaat uit de [catalogus met Azure IOT Edge-gecertificeerde apparaten](https://catalog.azureiotsolutions.com/).

- De minimale hardwarevereisten moeten worden gedocumenteerd als de laatste alinea in de beschrijving van de aanbieding (op het tabblad aanbiedings vermelding in het [partner centrum](https://partner.microsoft.com/dashboard/commercial-marketplace)). U kunt eventueel ook de aanbevolen hardwarevereisten weer geven als ze aanzienlijk verschillen. Voeg bijvoorbeeld de volgende sectie toe aan het einde van de beschrijving van uw aanbieding:

Kopieer deze HTML-tekst of gebruik de bijbehorende RTF-functies in het bewerkings venster.

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>Configuratie

De module moet standaard configuratie-instellingen bevatten om de implementatie zo eenvoudig mogelijk op een IoT Edge apparaat te kunnen uitvoeren. Deze informatie kan worden verstrekt op de pagina **technische configuratie** voor het plan in [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace). De container kan ook de SDK van IoT Edge module bevatten om communicatie met de Edge hub en IoT Hub in te scha kelen.

#### <a name="default-configuration"></a>Standaardconfiguratie

IoT Edge modules moeten kunnen beginnen met de standaard instellingen op de pagina **technische configuratie** voor het plan in [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace). De volgende standaard instellingen zijn beschikbaar:

- Standaard **routes**
- Standaard **module dubbele gewenste eigenschappen**
- Standaard **omgevings variabelen**
- Standaard **Opties** voor het maken van een container

In een scenario waarbij een para meter die is vereist voor een standaard waarde niet logisch is (bijvoorbeeld het IP-adres van de server van een klant), voegt u een para meter toe als de standaard waarde. Deze waarde is hoofd letters en tussen haakjes. Voor dit voor beeld stelt u de volgende standaard omgevings variabele in:

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Configuratie documentatie

Alle configuratie-instellingen van een IoT Edge module moeten duidelijk zijn gedocumenteerd. U moet bijvoorbeeld documenteren hoe de routes, dubbele gewenste eigenschappen, omgevings variabelen, createOptions, enzovoort, worden gebruikt. U moet een koppeling naar uw documentatie opgeven of een deel van uw aanbieding of plan beschrijving maken. U kunt deze informatie opgeven **op de pagina aanbieding** en **aanbieding plannen** in [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace).

#### <a name="tags-and-versioning"></a>Tags en versie beheer

Klanten moeten eenvoudig een module kunnen implementeren en automatisch updates ophalen van de Marketplace (in een scenario voor ontwikkel aars). Ze moeten ook een exacte versie kunnen gebruiken en blok keren die ze hebben getest (in een productie scenario).

IoT Edge modules moeten voldoen aan de volgende vereisten om aan deze klant verwachtingen te voldoen en op Marketplace te worden gepubliceerd

- Neem een manifest van de meest recente code op die naar de meest recente versie verwijst op alle ondersteunde platforms.
- Maak versie Tags in de vorm X. Y. Z, waarbij X, Y en Z gehele getallen zijn.
- Neem een version-tag op, zoals 1.0.1, die verwijst naar een specifieke versie op alle ondersteunde platforms.
- U kunt geen versie Tags bijwerken, zoals 1.0.1, omdat ze niet mogen worden gewijzigd.

> [!NOTE]
> Versie beheer kan optioneel de Tags "roulerende versie" bevatten, zoals 2,0 en 1,0. Dit biedt ondersteuning voor het tegelijkertijd onderhouden van meerdere primaire versies.

### <a name="telemetry"></a>Telemetrie

Modules die gebruikmaken van de IoT-module-SDK, moeten de unieke module-id instellen op PublisherId. OfferId. SkuId voor telemetrie-doel einden. Een unieke id helpt Azure Marketplace bij het bepalen van het aantal module-exemplaren dat wordt uitgevoerd.

Gebruik een van de volgende methoden van de IoT-module Sdk's om de product info in te stellen op deze id:

- [C#](/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](/java/api/com.microsoft.azure.sdk.iot.device.productinfo)

Voor modules die niet gebruikmaken van de IoT-module-SDK, is minder nauw keurige inzichten beschikbaar via partner centrum, zoals het aantal down loads.

### <a name="security"></a>Beveiliging

IoT Edge modules moeten [geprivilegieerde modules](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)vermijden. Vraag in plaats daarvan de minst bevoegde toegang tot de host mogelijk te stellen.

### <a name="module-iot-sdk"></a>Module IoT SDK

Met inbegrip van de IoT-module SDK is er geen vereiste voor certificering. Met inbegrip van de IoT-module-SDK kunt u echter betere gebruikers ervaring bieden. Bijvoorbeeld voor het ondersteunen van route ring of het verzenden van berichten naar de Cloud.

De IoT-module SDK is vereist voor het ophalen van telemetriegegevens over het aantal module-exemplaren dat wordt uitgevoerd.

## <a name="recertification-process"></a>Opnieuw certificerings proces

Partners worden op de hoogte gesteld wanneer er een belang rijke wijziging is die van invloed is op de modules, zoals:

- Ondersteunings matrix voor het besturings systeem/de boog op laag 1 wordt ondersteund door IoT Edge
- IoT-module-SDK
- IoT Edge-runtime
- Richt lijnen voor de certificering van IoT Edge-modules

Partners moeten hun aanbiedingen bijwerken en opnieuw certificeren door ze opnieuw te publiceren in het [partner centrum](https://partner.microsoft.com/dashboard/commercial-marketplace).

Uw aanbieding wordt ook opnieuw gecertificeerd als u deze bijwerkt, bijvoorbeeld door nieuwe afbeeldings tags toe te voegen.

## <a name="host-module-in-azure-container-registry"></a>Host-module in Azure Container Registry

Als u uw IoT Edge-module naar Azure Marketplace wilt uploaden, moet u deze eerst hosten in een [Azure container Registry](https://azure.microsoft.com/services/container-registry/) (ACR). De module moet alle labels bevatten die u wilt publiceren, met inbegrip van de afbeeldings Tags waarnaar wordt verwezen door een manifest-tag. Zie voor meer informatie de zelf studie [een Azure container Registry maken en een container installatie kopie pushen](../../container-instances/container-instances-tutorial-prepare-acr.md).

## <a name="next-steps"></a>Volgende stappen

- [Aanbieding voor IoT Edge-module maken](azure-iot-edge-module-creation.md)