---
title: Meer informatie over hoe modules logica uitvoeren op uw apparaten-Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge modules zijn container eenheden van logica die extern kunnen worden geïmplementeerd en beheerd, zodat u bedrijfs logica op IoT Edge apparaten kunt uitvoeren
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: a9b1ffb2dbcbd6e81856277f4b672cf876cc75f1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492367"
---
# <a name="understand-azure-iot-edge-modules"></a>Informatie over Azure IoT Edge-modules

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Met Azure IoT Edge kunt u bedrijfs logica op de rand in de vorm van *modules* implementeren en beheren. Azure IoT Edge modules zijn de kleinste reken eenheid die wordt beheerd door IoT Edge en kunnen Azure-Services (zoals Azure Stream Analytics) of uw eigen oplossings code bevatten. Als u wilt weten hoe modules worden ontwikkeld, geïmplementeerd en onderhouden, moet u rekening houden met de vier conceptuele elementen van een module:

* Een **module installatie kopie** is een pakket met de software die een module definieert.
* Een **module-exemplaar** is de specifieke reken eenheid die de module installatie kopie uitvoert op een IOT edge apparaat. Het module-exemplaar wordt gestart door de IoT Edge runtime.
* Een **module-identiteit** is een stukje informatie (inclusief beveiligings referenties) die is opgeslagen in IOT hub, dat is gekoppeld aan elk module-exemplaar.
* Een **module dubbele** is een JSON-document dat is opgeslagen in IOT hub, dat status informatie bevat voor een module-exemplaar, met inbegrip van meta gegevens, configuraties en voor waarden.

## <a name="module-images-and-instances"></a>Installatie kopieën en instanties van module

IoT Edge module installatie kopieën bevatten toepassingen die gebruikmaken van de functies voor beheer, beveiliging en communicatie van de IoT Edge runtime. U kunt uw eigen module-installatie kopieën ontwikkelen of een exporteren vanuit een ondersteunde Azure-service, zoals Azure Stream Analytics.
De afbeeldingen bevinden zich in de Cloud en kunnen worden bijgewerkt, gewijzigd en geïmplementeerd in verschillende oplossingen. Een module die bijvoorbeeld gebruikmaakt van machine learning om productie regel uitvoer te voors pellen, bestaat als een afzonderlijke installatie kopie dan een module die gebruikmaakt van computer vision om een drone te beheren.

Telkens wanneer een module installatie kopie wordt geïmplementeerd op een apparaat en wordt gestart door de IoT Edge runtime, wordt er een nieuw exemplaar van die module gemaakt. Twee apparaten in verschillende delen van de wereld kunnen dezelfde module installatie kopie gebruiken. Elk apparaat heeft echter een eigen module-exemplaar wanneer de module op het apparaat wordt gestart.

![Diagram-module installatie kopieën in Cloud, module-exemplaren op apparaten](./media/iot-edge-modules/image_instance.png)

In de implementatie bestaan modules installatie kopieën als container installatie kopie in een opslag plaats, en module-instanties zijn containers op apparaten.

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Module-identiteiten

Wanneer een nieuw module-exemplaar wordt gemaakt door de IoT Edge runtime, wordt er een bijbehorende module-id opgehaald. De module-identiteit wordt opgeslagen in IoT Hub en wordt gebruikt als het bereik van de adressen en de beveiliging voor alle lokale en Cloud communicatie voor dat module-exemplaar.

De identiteit die is gekoppeld aan een module-exemplaar is afhankelijk van de identiteit van het apparaat waarop het exemplaar wordt uitgevoerd en de naam die u in uw oplossing aan die module verstrekt. Als u bijvoorbeeld `insight` een module aanroept die gebruikmaakt van een Azure stream Analytics en u deze implementeert op een apparaat met `Hannover01` de naam, maakt de IOT Edge-runtime een bijbehorende module-ID met de naam `/devices/Hannover01/modules/insight` .

Als u één module installatie kopie meerdere keren op hetzelfde apparaat moet implementeren, kunt u de installatie kopie ook meerdere keren met verschillende namen implementeren.

![Diagram-module-identiteiten zijn uniek binnen apparaten en op verschillende apparaten](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Module apparaatdubbels

Elk module-exemplaar heeft ook een bijbehorende module, die u kunt gebruiken om het module-exemplaar te configureren. Het exemplaar en de twee zijn gekoppeld aan elkaar via de module-identiteit.

Een module twee is een JSON-document waarin module gegevens en configuratie-eigenschappen worden opgeslagen. In dit concept wordt het [apparaat-dubbele](../iot-hub/iot-hub-devguide-device-twins.md) concept van IOT hub parallel. De structuur van een module dubbele is dezelfde als die van een apparaat. De Api's die worden gebruikt voor interactie met beide typen apparaatdubbels zijn ook hetzelfde. Het enige verschil tussen de twee is de identiteit die wordt gebruikt voor het instantiëren van de client-SDK.

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a
// module since it is created with a module’s connection string instead
// of a device connection string.
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings);
await client.OpenAsync();

// Get the module twin
Twin twin = await client.GetTwinAsync();
```

## <a name="offline-capabilities"></a>Functionaliteiten offline

Azure IoT Edge modules kunnen voor onbepaalde tijd offline worden gebruikt na synchronisatie met IoT Hub ten minste één keer. Met IoT Edge apparaten kan deze offline-functionaliteit ook worden uitgebreid naar andere IoT-apparaten. Zie voor meer informatie [uitgebreide offline mogelijkheden voor IOT edge apparaten, modules en onderliggende apparaten begrijpen](offline-capabilities.md).

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over de vereisten en hulpprogram ma's voor het ontwikkelen van IoT Edge modules](module-development.md)
* [Inzicht in de runtime van Azure IoT Edge en de architectuur ervan](iot-edge-runtime.md)
