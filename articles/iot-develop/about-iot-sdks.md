---
title: Overzicht van SDK-opties voor Azure IoT-apparaten
description: Meer informatie over welke Azure IoT-apparaat-SDK u kunt gebruiken op basis van uw ontwikkelingsrol en taken.
author: philmea
ms.author: philmea
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: c35a9045bf809c03630fbb7c57f9d31e7b143422
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876453"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Overzicht van Azure IoT Device SDK's

De Apparaat-SDK's van Azure IoT zijn een set clientbibliotheken voor apparaten, ontwikkelaarshandleidingen, voorbeelden en documentatie. Met de apparaat-SDK's kunt u apparaten programmatisch verbinden met Azure IoT-services.

:::image type="content" border="false" source="media/about-iot-sdks/iot-sdk-diagram.png" alt-text="Diagram met verschillende Azure IoT SDK's":::

Zoals in het diagram wordt weergegeven, zijn er verschillende apparaat-SDK's beschikbaar die aansluiten op de behoeften van uw apparaat en programmeertaal. Richtlijnen voor het selecteren van de juiste apparaat-SDK zijn beschikbaar in [Welke SDK moet ik gebruiken?](#which-sdk-should-i-use) Er zijn ook Azure IoT-service-SDK's beschikbaar om uw cloudtoepassing te verbinden met Azure IoT-services op de back-end. Dit artikel is gericht op de apparaat-SDK's, [](#service-sdks)maar hier vindt u meer informatie over Azure-service-SDK's.

## <a name="why-should-i-use-the-azure-iot-device-sdks"></a>Waarom moet ik de Azure IoT Device SDK's gebruiken?

Als u apparaten wilt verbinden met Azure IoT, kunt u een aangepaste verbindingslaag bouwen of Azure IoT Device SDK's gebruiken. Het gebruik van Azure IoT Device SDK's biedt verschillende voordelen:

| Ontwikkelingskosten &nbsp; &nbsp; &nbsp;&nbsp; | Aangepaste verbindingslaag | Azure IoT Device SDK's |
| :-- | :------------------------------------------------ | :---------------------------------------- |
| Ondersteuning | Ondersteuning en document moeten bieden voor alles wat u bouwt | Toegang hebben tot Microsoft-ondersteuning (GitHub, Microsoft Q&A, Microsoft Docs, customer support-teams) |
| Nieuwe functies | Nieuwe Azure-functies moeten worden toegevoegd aan aangepaste middleware | Kan onmiddellijk profiteren van nieuwe functies die Microsoft voortdurend toevoegt aan de IoT SDK's |
| Investering | Honderden uren aan ingesloten ontwikkeling investeren in het ontwerpen, bouwen, testen en onderhouden van een aangepaste versie | U kunt profiteren van gratis opensource-hulpprogramma's. De enige kosten die aan de SDK's zijn gekoppeld, zijn de leercurve. |

## <a name="which-sdk-should-i-use"></a>Welke SDK moet ik gebruiken?

Apparaat-SDK's van Azure IoT zijn beschikbaar in populaire programmeertalen, waaronder C, C#, Java, Node.js en Python. Er zijn twee belangrijke overwegingen bij het kiezen van een SDK: apparaatmogelijkheden en de bekendheid van uw team met de programmeertaal.

### <a name="device-capabilities"></a>Apparaatmogelijkheden

Wanneer u een SDK kiest, moet u rekening houden met de limieten van de apparaten die u gebruikt. Een beperkt apparaat heeft één microcontroller (MCU) en beperkt geheugen. Als u een beperkt apparaat gebruikt, raden we u aan de [Embedded C SDK te gebruiken.](#embedded-c-sdk) Deze SDK is ontworpen om de minimale set mogelijkheden te bieden om verbinding te maken met Azure IoT. U kunt ook onderdelen (MQTT-client, TLS en socketbibliotheken) selecteren die het meest zijn geoptimaliseerd voor uw ingesloten apparaat. Als uw beperkte apparaat ook wordt uitgevoerd Azure RTOS, kunt u de Azure RTOS-middleware gebruiken om verbinding te maken met Azure IoT. De Azure RTOS-middleware verpakt de Embedded C SDK met extra functionaliteit om het verbinden van Azure RTOS apparaat met de cloud te vereenvoudigen.

Een niet-getraind apparaat heeft een krachtigere CPU, waarmee een besturingssysteem kan worden uitgevoerd ter ondersteuning van een taalruntime zoals .NET of Python. Als u een niet-getraind apparaat gebruikt, is het belangrijkste aandachtspunt kennis van de taal.

### <a name="your-teams-familiarity-with-the-programming-language"></a>De bekendheid van uw team met de programmeertaal

Apparaat-SDK's van Azure IoT worden in meerdere talen geïmplementeerd, zodat u de voorkeurstaal kunt kiezen. De apparaat-SDK's kunnen ook worden geïntegreerd met andere vertrouwde, taalspecifieke hulpprogramma's. Door te kunnen werken met een vertrouwde ontwikkelingstaal en hulpprogramma's, kan uw team de ontwikkelingscyclus van onderzoek, prototypen, productontwikkeling en doorlopend onderhoud optimaliseren.

Selecteer indien mogelijk een SDK die vertrouwd lijkt bij uw ontwikkelteam. Alle Azure IoT SDK's zijn open source en er zijn verschillende voorbeelden beschikbaar voor uw team om te evalueren en te testen voordat u zich verbindt met een specifieke SDK.

## <a name="how-can-i-get-started"></a>Hoe ga ik aan de slag?

U kunt beginnen met het verkennen van de GitHub-opslagplaatsen van de Azure Device SDK's. U kunt ook een [quickstart proberen](quickstart-send-telemetry-python.md) die laat zien hoe u snel een SDK kunt gebruiken om telemetrie te verzenden naar Azure IoT.

Uw opties om aan de slag te gaan, zijn afhankelijk van het type apparaat dat u hebt:
- Gebruik voor beperkte apparaten de [Embedded C SDK.](#embedded-c-sdk) 
- Voor apparaten die worden uitgevoerd op Azure RTOS, kunt u ontwikkelen met de [Azure RTOS middleware](#azure-rtos-middleware). 
- Voor apparaten die niet zijn getraind, kunt u een [SDK kiezen](#unconstrained-device-sdks) in een taal van uw keuze. 

### <a name="constrained-device-sdks"></a>Beperkte apparaat-SDK's
Deze SDK's zijn speciaal voor gebruik op apparaten met beperkte reken- of geheugenbronnen. Zie Overzicht van Azure IoT-apparaattypen voor meer informatie over [algemene apparaattypen.](concepts-iot-device-types.md)

#### <a name="embedded-c-sdk"></a>Ingesloten C SDK
* [GitHub-opslagplaats](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)
* [Voorbeelden](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md)
* [Referentiedocumentatie](https://azure.github.io/azure-sdk-for-c/)
* [De Embedded C SDK bouwen](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Groottegrafiek voor beperkte apparaten](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart)

#### <a name="azure-rtos-middleware"></a>Azure RTOS Middleware

* [GitHub-opslagplaats](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot)
* [Aan de slag en](https://github.com/azure-rtos/getting-started) meer [voorbeelden](https://github.com/azure-rtos/samples)
* [Referentiedocumentatie](/azure/rtos/threadx/)

### <a name="unconstrained-device-sdks"></a>Niet-getrainde apparaat-SDK's
Deze SDK's kunnen worden uitgevoerd op elk apparaat dat een runtime met een hogere taal kan ondersteunen. Dit omvat apparaten zoals pc's, Raspberry Pis en smartphones. Ze worden voornamelijk gedifferentieerd op taal, zodat u kunt kiezen welke bibliotheek het beste bij uw team en scenario past.

#### <a name="c-device-sdk"></a>Apparaat-SDK voor C
* [GitHub-opslagplaats](https://github.com/Azure/azure-iot-sdk-c)
* [Voorbeelden](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)
* [Pakketten](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Referentiedocumentatie](/azure/iot-hub/iot-c-sdk-ref/)
* [Referentiedocumentatie voor Edge-module](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [De C Device SDK compileren](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [De C-SDK over te brengen naar andere platforms](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Documentatie voor ontwikkelaars](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) voor informatie over cross-compiling en aan de slag op verschillende platforms
* [Azure IoT Hub C SDK-resourceverbruiksgegevens](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

#### <a name="c-device-sdk"></a>Apparaat-SDK voor C#

* [GitHub-opslagplaats](https://github.com/Azure/azure-iot-sdk-csharp)
* [Voorbeelden](https://github.com/Azure/azure-iot-sdk-csharp#samples)
* [Pakket](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Referentiedocumentatie](/dotnet/api/microsoft.azure.devices)
* [Referentiedocumentatie voor Edge-module](/dotnet/api/microsoft.azure.devices.client.moduleclient)

#### <a name="java-device-sdk"></a>Java Device SDK

* [GitHub-opslagplaats](https://github.com/Azure/azure-iot-sdk-java)
* [Voorbeelden](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)
* [Pakket](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Referentiedocumentatie](/java/api/com.microsoft.azure.sdk.iot.device)
* [Referentiedocumentatie voor Edge-module](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)

#### <a name="nodejs-device-sdk"></a>Node.js Device SDK

* [GitHub-opslagplaats](https://github.com/Azure/azure-iot-sdk-node)
* [Voorbeelden](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)
* [Pakket](https://www.npmjs.com/package/azure-iot-device)
* [Referentiedocumentatie](/javascript/api/azure-iot-device/)
* [Referentiedocumentatie voor Edge-module](/javascript/api/azure-iot-device/moduleclient)

#### <a name="python-device-sdk"></a>Apparaat-SDK voor Python

* [GitHub-opslagplaats](https://github.com/Azure/azure-iot-sdk-python)
* [Voorbeelden](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)
* [Pakket](https://pypi.org/project/azure-iot-device/)
* [Referentiedocumentatie](/python/api/azure-iot-device)
* [Referentiedocumentatie voor Edge-module](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)

### <a name="service-sdks"></a>Service-SDK's
Azure IoT biedt ook service-SDK's waarmee u toepassingen aan de oplossingszijde kunt bouwen om apparaten te beheren, inzichten te verkrijgen, gegevens te visualiseren en meer. Deze SDK's zijn specifiek voor elke Azure IoT-service en zijn beschikbaar in C#, Java, JavaScript en Python om uw ontwikkelervaring te vereenvoudigen. 

#### <a name="iot-hub"></a>IoT Hub

Met IoT Hub service-SDK's kunt u toepassingen bouwen die eenvoudig kunnen communiceren met uw IoT Hub om apparaten en beveiliging te beheren. U kunt deze SDK's gebruiken om cloud-naar-apparaat-berichten te verzenden, directe methoden op uw apparaten aan te roepen, apparaateigenschappen bij te werken en meer.

[**Meer informatie over IoT Hub**](https://azure.microsoft.com/services/iot-hub/)  |  [ **Een apparaat beheren**](../iot-hub/quickstart-control-device-python.md)

**C# IoT Hub Service SDK:** [Referentiedocumentatie voor GitHub-opslagplaatspakketvoorbeelden](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service)  |  [](https://www.nuget.org/packages/Microsoft.Azure.Devices/)  |  [](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service/samples)  |  [](/dotnet/api/microsoft.azure.devices)

**Naslagdocumentatie voor Java IoT Hub Service SDK:** [GitHub-opslagplaatspakketvoorbeelden](https://github.com/Azure/azure-iot-sdk-java/tree/master/service)  |  [](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)  |  [](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples)  |  [](/java/api/com.microsoft.azure.sdk.iot.service)

**Naslagdocumentatie voor JavaScript IoT Hub Service SDK:** [](https://github.com/Azure/azure-iot-sdk-node/tree/master/service)  |  [](https://www.npmjs.com/package/azure-iothub)  |  [](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples)GitHub-opslagplaatspakketvoorbeelden  |  [](/javascript/api/azure-iothub/)

**Python IoT Hub Service SDK:** referentiedocumentatie [voor github-opslagplaatspakketvoorbeelden](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub)  |  [](https://pypi.python.org/pypi/azure-iot-hub/)  |  [](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples)  |  [](/python/api/azure-iot-hub)

#### <a name="azure-digital-twins"></a>Azure Digital Twins

Azure Digital Twins is een PaaS-aanbieding (Platform as a Service) dat het maken van infografieken op basis van digitale modellen van hele omgevingen mogelijk maakt. Deze omgevingen kunnen gebouwen, fabrieken, boerderijen, energienetwerken, spoorwegen, stadiums en meer zijn, zelfs steden. Deze digitale modellen kunnen worden gebruikt om inzichten te verkrijgen die betere producten, geoptimaliseerde bewerkingen, lagere kosten en baanbrekende klantervaringen opleveren. Azure IoT biedt service-SDK's om eenvoudig toepassingen te bouwen die gebruikmaken van de kracht van Azure Digital Twins.

[**Meer informatie over Azure Digital Twins**](https://azure.microsoft.com/services/digital-twins/)  |  [ **Een ADT-toepassing coden**](../digital-twins/tutorial-code.md)

**C# ADT Service SDK:** [Referentiedocumentatie voor GitHub-opslagplaatspakketvoorbeelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)  |  [](https://www.nuget.org/packages/Azure.DigitalTwins.Core)  |  [](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)  |  [](/dotnet/api/overview/azure/digitaltwins/client)

**Java ADT Service SDK:** [Referentiedocumentatie voor GitHub-opslagplaatspakketvoorbeelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)  |  [](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core/src/samples)  |  [](/java/api/overview/azure/digitaltwins/client)

**Node.js ADT-service-SDK:** [Referentiedocumentatie voor GitHub-opslagplaatspakketten](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)  |  [](https://www.npmjs.com/package/@azure/digital-twins-core)  |  [](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core/samples)  |  [](/javascript/api/@azure/digital-twins-core/)

**Python ADT Service SDK:** [Referentiedocumentatie voor gitHub-opslagplaatspakketvoorbeelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [](https://pypi.org/project/azure-digitaltwins-core/)  |  [](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core/samples)  |  [](/python/api/azure-digitaltwins-core/azure.digitaltwins.core)

#### <a name="device-provisioning-service"></a>Device Provisioning Service

IoT Hub Device Provisioning Service (DPS) is een helper-service die zero-touch mogelijk maakt, het Just-In-Time inrichten naar de juiste IoT Hub zonder tussenkomst van de gebruiker. Met DPS kunnen miljoenen apparaten veilig en schaalbaar worden ingericht. Met de DPS Service SDK's kunt u toepassingen bouwen die uw apparaten veilig kunnen beheren door registratiegroepen te maken en bulkbewerkingen uit te voeren.

[**Meer informatie over Device Provisioning Service**](../iot-dps/index.yml)  |  [ **Probeer een groepsinschrijving te maken voor X.509-apparaten**](../iot-dps/quick-enroll-device-x509-csharp.md)

**C# Device Provisioning Service SDK:** [Referentiedocumentatie voor gitHub-opslagplaatspakketvoorbeelden](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service)  |  [](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/)  |  [](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)  |  [](/dotnet/api/microsoft.azure.devices.provisioning.service)

**Java Device Provisioning Service SDK:** [referentiedocumentatie voor GitHub-opslagplaatspakketvoorbeelden](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-service-client/src)  |  [](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client)  |  [](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples#provisioning-service-client)  |  [](/java/api/com.microsoft.azure.sdk.iot.provisioning.service)

**Node.js Device Provisioning Service SDK:** referentiedocumentatie voor [github-opslagplaatspakketten](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)  |  [](https://www.npmjs.com/package/azure-iot-provisioning-service)  |  [](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)  |  [](/javascript/api/azure-iot-provisioning-service)

## <a name="next-steps"></a>Volgende stappen

* [Quickstart: Een apparaat verbinden met IoT Central (Python)](quickstart-send-telemetry-python.md)
* [Quickstart: Een apparaat verbinden met IoT Hub (Python)](quickstart-send-telemetry-cli-python.md)
* [Aan de slag met ingesloten ontwikkeling](quickstart-device-development.md)
* Meer informatie over de voordelen [van het ontwikkelen met behulp van Azure IoT SDK's](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)