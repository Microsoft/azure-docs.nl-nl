---
title: Overzicht van Azure IoT Device SDK opties
description: Informatie over welke Azure IoT Device SDK u moet gebruiken op basis van uw ontwikkelings-en taken.
author: elhorton
ms.author: elhorton
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: c9624e9a23d005185429c82199324ac570cbd63e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102607727"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Overzicht van de Sdk's van het Azure IoT-apparaat

De Sdk's van het Azure IoT-apparaat zijn een reeks client Bibliotheken, ontwikkel aars, voor beelden en documentatie van apparaten. De Sdk's van het apparaat helpen u bij het programmatisch verbinden van apparaten met Azure IoT-Services.

:::image type="content" border="false" source="media/about-iot-sdks/iot-sdk-diagram.png" alt-text="Diagram van verschillende Azure IoT-Sdk's":::

Zoals in het diagram wordt weer gegeven, zijn er diverse Sdk's voor apparaten beschikbaar die aansluiten op uw behoeften voor uw apparaat en programmeer taal. Richt lijnen voor het selecteren van de juiste SDK voor apparaten is beschikbaar in [welke SDK moet ik gebruiken](#which-sdk-should-i-use). Er zijn ook Sdk's voor Azure IoT service beschikbaar om uw Cloud toepassing te verbinden met Azure IoT-Services op de back-end. Dit artikel is gericht op de apparaat-Sdk's, maar u kunt [hier](#service-sdks)meer te weten komen over de Azure-service-sdk's.

## <a name="why-should-i-use-the-azure-iot-device-sdks"></a>Waarom zou ik de Azure IoT-apparaat-Sdk's moeten gebruiken?

Als u apparaten wilt verbinden met Azure IoT, kunt u een aangepaste beveiligingslaag maken of een Azure IoT-apparaat-Sdk's gebruiken. Er zijn verschillende voor delen van het gebruik van Azure IoT-apparaat-Sdk's:

| Ontwikkelings kosten &nbsp; &nbsp; &nbsp;&nbsp; | Aangepaste verbindingstime-laag | Sdk's van Azure IoT-apparaat |
| :-- | :------------------------------------------------ | :---------------------------------------- |
| Ondersteuning | Moet u een wille keurige versie van de bouw ondersteunen en documenteren | Toegang tot micro soft-ondersteuning (GitHub, micro soft Q&A, Microsoft Docs, klant ondersteunings teams) |
| Nieuwe functies | Nieuwe Azure-functies moeten worden toegevoegd aan de aangepaste middleware | Kan direct profiteren van nieuwe functies die micro soft voortdurend toevoegt aan de IoT-Sdk's |
| Investering | BEVEST honderden uren van geïntegreerde ontwikkeling voor het ontwerpen, bouwen, testen en onderhouden van een aangepaste versie | Kan profiteren van de gratis, open-source hulpprogram ma's. De enige kosten die zijn gekoppeld aan de Sdk's is de leer curve. |

## <a name="which-sdk-should-i-use"></a>Welke SDK moet ik gebruiken?

Sdk's voor Azure IoT-apparaten zijn beschikbaar in populaire programmeer talen, waaronder C, C#, Java, Node.js en python. Er zijn twee belang rijke overwegingen bij het kiezen van een SDK: apparaatfuncties en de vertrouwdheid van uw team met de programmeer taal.

### <a name="device-capabilities"></a>Apparaatfuncties

Wanneer u een SDK kiest, moet u rekening houden met de limieten van de apparaten die u gebruikt. Een beperkt apparaat heeft één micro controller (MCU) en een beperkt geheugen. Als u een beperkt apparaat gebruikt, raden we u aan de [Embedded C SDK](#embedded-c-sdk)te gebruiken. Deze SDK is ontworpen om de bare minimale set mogelijkheden te bieden om verbinding te maken met Azure IoT. U kunt ook onderdelen (MQTT-client, TLS en socket-bibliotheken) selecteren die het meest zijn geoptimaliseerd voor uw Inge sloten apparaat. Als uw beperkte apparaat ook Azure RTO'S uitvoert, kunt u de Azure RTO'S-middleware gebruiken om verbinding te maken met Azure IoT. De Azure RTO'S-middleware pakt de Inge sloten C SDK in met extra functionaliteit om uw Azure RTO'S-apparaat te koppelen aan de Cloud.

Een apparaat met een onbeperkte capaciteit is een krachtigere CPU, waarmee een besturings systeem kan worden uitgevoerd voor de ondersteuning van een taal runtime, zoals .NET of python. Als u een niet-beperkt apparaat gebruikt, is de belangrijkste overweging vertrouwd met de taal.

### <a name="your-teams-familiarity-with-the-programming-language"></a>De vertrouwdheid van uw team met de programmeer taal

Sdk's voor Azure IoT-apparaten worden geïmplementeerd in meerdere talen, zodat u de gewenste taal kunt kiezen. De Sdk's van het apparaat kunnen ook worden geïntegreerd met andere vertrouwde, taalspecifieke hulpprogram ma's. U kunt met een vertrouwde ontwikkel taal en hulpprogram ma's werken, zodat uw team de ontwikkelings cyclus van onderzoek, prototypen, product ontwikkeling en voortdurend onderhoud kan optimaliseren.

Als dat mogelijk is, selecteert u een SDK die vertrouwd is met uw ontwikkel team. Alle Azure IoT-Sdk's zijn open source en er zijn verschillende voor beelden beschikbaar die uw team nodig heeft om te evalueren en testen voordat een specifieke SDK wordt doorgevoerd.

## <a name="how-can-i-get-started"></a>Hoe ga ik aan de slag?

De plek waar u begint, is om de GitHub-opslag plaatsen van de Azure apparaat-Sdk's te verkennen. U kunt ook een [Snelstartgids](quickstart-send-telemetry-python.md) uitproberen die laat zien hoe u snel een SDK kunt gebruiken om telemetrie te verzenden naar Azure IOT.

De opties om aan de slag te gaan, zijn afhankelijk van het type apparaat dat u hebt:
- Gebruik de [Embedded C SDK](#embedded-c-sdk)voor beperkte apparaten. 
- Voor apparaten waarop Azure RTO'S wordt uitgevoerd, kunt u ontwikkelen met de [Azure rto's-middleware](#azure-rtos-middleware). 
- Voor apparaten die onbeperkt zijn, kunt u [een SDK kiezen](#unconstrained-device-sdks) in een door u gewenste taal. 

### <a name="constrained-device-sdks"></a>Congebonden apparaat-Sdk's
Deze Sdk's zijn gespecialiseerd om te worden uitgevoerd op apparaten met beperkte reken-of geheugen bronnen. Zie [overzicht van de typen Azure IOT-apparaten](concepts-iot-device-types.md)voor meer informatie over algemene apparaattypen.

#### <a name="embedded-c-sdk"></a>Embedded C SDK
* [GitHub-opslag plaats](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)
* [Voorbeelden](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md)
* [Referentie documentatie](https://azure.github.io/azure-sdk-for-c/)
* [De embedded C SDK bouwen](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Grootte grafiek voor beperkte apparaten](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart)

#### <a name="azure-rtos-middleware"></a>Azure RTO'S-middleware

* [GitHub-opslag plaats](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot)
* [Aan de slag-hand leidingen](https://github.com/azure-rtos/getting-started) en [meer voor beelden](https://github.com/azure-rtos/samples)
* [Referentie documentatie](/azure/rtos/threadx/)

### <a name="unconstrained-device-sdks"></a>Niet-beperkende apparaat-Sdk's
Deze Sdk's kunnen worden uitgevoerd op elk apparaat dat een meertalige taal versie kan ondersteunen. Dit omvat apparaten zoals Pc's, Raspberry Pis en smartphones. Ze worden voornamelijk per taal onderscheiden, zodat u kunt kiezen welke bibliotheek het beste past bij uw team en scenario.

#### <a name="c-device-sdk"></a>C-SDK voor apparaten
* [GitHub-opslag plaats](https://github.com/Azure/azure-iot-sdk-c)
* [Voorbeelden](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)
* [Pakketten](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Referentie documentatie](/azure/iot-hub/iot-c-sdk-ref/)
* [Naslag documentatie voor Edge-modules](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [De SDK voor C-apparaten compileren](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Poort van de C SDK naar andere platforms](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Documentatie voor ontwikkel aars](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) voor meer informatie over meerdere compilaties en aan de slag met verschillende platformen
* [Informatie over het gebruik van Azure IoT Hub C SDK-resources](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

#### <a name="c-device-sdk"></a>C#-apparaat-SDK

* [GitHub-opslag plaats](https://github.com/Azure/azure-iot-sdk-csharp)
* [Voorbeelden](https://github.com/Azure/azure-iot-sdk-csharp#samples)
* [Pakket](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Referentie documentatie](/dotnet/api/microsoft.azure.devices)
* [Naslag documentatie voor Edge-modules](/dotnet/api/microsoft.azure.devices.client.moduleclient)

#### <a name="java-device-sdk"></a>Java-apparaat-SDK

* [GitHub-opslag plaats](https://github.com/Azure/azure-iot-sdk-java)
* [Voorbeelden](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)
* [Pakket](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Referentie documentatie](/java/api/com.microsoft.azure.sdk.iot.device)
* [Naslag documentatie voor Edge-modules](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)

#### <a name="nodejs-device-sdk"></a>SDK van Node.js apparaat

* [GitHub-opslag plaats](https://github.com/Azure/azure-iot-sdk-node)
* [Voorbeelden](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)
* [Pakket](https://www.npmjs.com/package/azure-iot-device)
* [Referentie documentatie](/javascript/api/azure-iot-device/)
* [Naslag documentatie voor Edge-modules](/javascript/api/azure-iot-device/moduleclient)

#### <a name="python-device-sdk"></a>Python-apparaat-SDK

* [GitHub-opslag plaats](https://github.com/Azure/azure-iot-sdk-python)
* [Voorbeelden](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)
* [Pakket](https://pypi.org/project/azure-iot-device/)
* [Referentie documentatie](/python/api/azure-iot-device)
* [Naslag documentatie voor Edge-modules](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)

### <a name="service-sdks"></a>Service-SDK's
Azure IoT biedt ook service-Sdk's waarmee u toepassingen aan de oplossings zijde kunt bouwen om apparaten te beheren, inzichten te verkrijgen, gegevens te visualiseren en meer. Deze Sdk's zijn specifiek voor elke Azure IoT-service en zijn beschikbaar in C#, Java, java script en python om uw ontwikkel ervaring te vereenvoudigen. 

#### <a name="iot-hub"></a>IoT Hub

Met de IoT Hub service-Sdk's kunt u toepassingen bouwen die eenvoudig met uw IoT Hub kunnen communiceren om apparaten en beveiliging te beheren. U kunt deze Sdk's gebruiken om Cloud-naar-apparaat-berichten te verzenden, direct methoden op uw apparaten aan te roepen, apparaateigenschappen bij te werken, en meer.

[**Meer informatie over IOT hub**](https://azure.microsoft.com/services/iot-hub/)  |  [ **Beheer van een apparaat proberen**](../iot-hub/quickstart-control-device-python.md)

Naslag documentatie voor voor beelden van **C# IOT hub Service-SDK**: [github opslagplaats](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service)  |  [pakket](https://www.nuget.org/packages/Microsoft.Azure.Devices/)  |  [](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service/samples)  |  [](/dotnet/api/microsoft.azure.devices)

Naslag documentatie voor de **Java IOT hub Service-SDK**: [github opslagplaats](https://github.com/Azure/azure-iot-sdk-java/tree/master/service)  |  [pakket](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)voor  |  [beelden](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples)  |  [](/java/api/com.microsoft.azure.sdk.iot.service)

Naslag documentatie voor voor beelden van **Java script IOT hub Service-SDK**: [github opslagplaats](https://github.com/Azure/azure-iot-sdk-node/tree/master/service)  |  [pakket](https://www.npmjs.com/package/azure-iothub)  |  [](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples)  |  [](/javascript/api/azure-iothub/)

GitHub van **python IOT hub Service-SDK**: [](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub)  |  [](https://pypi.python.org/pypi/azure-iot-hub/)  |  [](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples)  |  [referentie documentatie](/python/api/azure-iot-hub) voor voor beelden van opslag plaats pakket

#### <a name="azure-digital-twins"></a>Azure Digital Twins

Azure Digital Twins is een PaaS-aanbieding (Platform as a Service) dat het maken van infografieken op basis van digitale modellen van hele omgevingen mogelijk maakt. Deze omgevingen kunnen gebouwen, fabrieken, boerderijen, energienetwerken, spoorwegen, stadiums en meer zijn, zelfs steden. Deze digitale modellen kunnen worden gebruikt om inzichten te verkrijgen die betere producten, geoptimaliseerde bewerkingen, lagere kosten en baanbrekende klantervaringen opleveren. Azure IoT biedt service-Sdk's waarmee u eenvoudig toepassingen kunt bouwen die gebruikmaken van de kracht van Azure Digital Apparaatdubbels.

[**Meer informatie over Azure Digital apparaatdubbels**](https://azure.microsoft.com/services/digital-twins/)  |  [ **Een ADT-toepassing coderen**](../digital-twins/tutorial-code.md)

Naslag documentatie voor voor beelden van **C# ADT Service SDK**: [github-opslag plaats](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)  |  [pakket](https://www.nuget.org/packages/Azure.DigitalTwins.Core)  |  [](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)  |  [](/dotnet/api/overview/azure/digitaltwins/client)

Naslag documentatie voor voor beelden van de **Java ADT Service SDK**: [github-opslag plaats](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [pakket](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)  |  [](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core/src/samples)  |  [](/java/api/overview/azure/digitaltwins/client)

Naslag documentatie voor **Node.js ADT Service SDK**: [github-opslag plaats](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)  |  [pakket](https://www.npmjs.com/package/@azure/digital-twins-core)  |  [voorbeelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core/samples)  |  [](/javascript/api/@azure/digital-twins-core/)

**ADT van python-Service-SDK**: github-naslag documentatie voor [opslag plaats](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [pakket](https://pypi.org/project/azure-digitaltwins-core/)  |  [voorbeelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core/samples)  |  [](/python/api/azure-digitaltwins-core/azure.digitaltwins.core)

#### <a name="device-provisioning-service"></a>Device Provisioning Service

IoT Hub Device Provisioning Service (DPS) is een helper-service die zero-touch mogelijk maakt, het Just-In-Time inrichten naar de juiste IoT Hub zonder tussenkomst van de gebruiker. Met DPS kunnen miljoenen apparaten op een veilige en schaal bare manier worden ingericht. Met de Sdk's van de DPS-service kunt u toepassingen bouwen die uw apparaten veilig kunnen beheren door registratie groepen te maken en bulk bewerkingen uit te voeren.

[**Meer informatie over Device Provisioning Service**](../iot-dps/index.yml)  |  [ **Probeer een groeps registratie te maken voor X. 509-apparaten**](../iot-dps/quick-enroll-device-x509-csharp.md)

**SDK voor C# Device Provisioning Service**: [github opslag plaats](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service)  |  [pakket](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/)voor  |  [beelden](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)  |  [referentie documentatie](/dotnet/api/microsoft.azure.devices.provisioning.service)

Naslag documentatie voor de **Java Device Provisioning Service SDK**: [github opslagplaats](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-service-client/src)  |  [pakket](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client)voor  |  [beelden](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples#provisioning-service-client)  |  [](/java/api/com.microsoft.azure.sdk.iot.provisioning.service)

**Node.js Device Provisioning Service SDK**: [](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)  |  [](https://www.npmjs.com/package/azure-iot-provisioning-service)  |  [](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)  |  [naslag documentatie](/javascript/api/azure-iot-provisioning-service) voor voor beelden van github-opslagplaats pakket

## <a name="next-steps"></a>Volgende stappen

* [Quick Start: een apparaat verbinden met IoT Central (python)](quickstart-send-telemetry-python.md)
* [Quick Start: een apparaat verbinden met IoT Hub (python)](quickstart-send-telemetry-cli-python.md)
* [Aan de slag met Inge sloten ontwikkeling](quickstart-device-development.md)
* Meer informatie over de [voor delen van het ontwikkelen met Azure IOT sdk's](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)