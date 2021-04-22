---
title: Azure IoT Edge-ontwikkelomgeving | Microsoft Docs
description: Meer informatie over de ondersteunde systemen en ontwikkelhulpprogramma's van derden die u helpen bij het maken IoT Edge modules
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1070a5ddf298ad88100e7803635e970f9a314e52
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869577"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Uw ontwikkel- en testomgeving voorbereiden voor IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge uw bestaande bedrijfslogica verplaatst naar apparaten die aan de rand worden gebruikt. Als u uw toepassingen en workloads wilt voorbereiden voor gebruik [als IoT Edge modules,](iot-edge-modules.md)moet u ze bouwen als containers. Dit artikel bevat richtlijnen voor het configureren van uw ontwikkelomgeving, zodat u een IoT Edge maken. Zodra u uw ontwikkelomgeving hebt ingesteld, kunt u leren hoe u uw eigen [modules IoT Edge ontwikkelen.](module-development.md)

In elke IoT Edge zijn er ten minste twee machines om rekening mee te houden. Een is het IoT Edge apparaat zelf, waarop de module IoT Edge uitgevoerd. De andere is de ontwikkelmachine die u gebruikt voor het bouwen, testen en implementeren van modules. Dit artikel richt zich voornamelijk op de ontwikkelmachine. Voor testdoeleinden kunnen de twee computers hetzelfde zijn. U kunt deze IoT Edge op uw ontwikkelmachine en er modules in implementeren.

## <a name="operating-system"></a>Besturingssysteem

Azure IoT Edge wordt uitgevoerd op een specifieke set [ondersteunde besturingssystemen.](support.md) Voor het ontwikkelen IoT Edge, kunt u de meeste besturingssystemen gebruiken die een container-engine kunnen uitvoeren. De container-engine is een vereiste op de ontwikkelmachine om uw modules als containers te bouwen en deze naar een containerregister te pushen.

Als uw ontwikkelmachine niet kan worden uitgevoerd Azure IoT Edge, gaat [](#testing-tools) u verder met dit artikel voor meer informatie over testhulpprogramma's waarmee u lokaal kunt testen en fouten kunt opsporen.

Het besturingssysteem van uw ontwikkelmachine hoeft niet overeen te komen met het besturingssysteem van uw IoT Edge apparaat. Het besturingssysteem van de container moet echter consistent zijn tussen de ontwikkelmachine en IoT Edge apparaat. U kunt bijvoorbeeld modules ontwikkelen op een Windows-computer en deze implementeren op een Linux-apparaat. Op de Windows-computer moeten Linux-containers worden uitgevoerd om de modules voor het Linux-apparaat te bouwen.

## <a name="container-engine"></a>Container-engine

Het centrale concept van IoT Edge is dat u uw bedrijf- en cloudlogica op afstand op apparaten kunt implementeren door deze in containers te verpakken. Als u containers wilt bouwen, hebt u een container-engine op uw ontwikkelmachine nodig.

De enige ondersteunde container-engine voor IoT Edge in productie is Moby. Elke containerent engine die compatibel is met Het Open Container Initiative, zoals Docker, kan echter IoT Edge module-afbeeldingen.

## <a name="development-tools"></a>Ontwikkelhulpprogramma’s

Zowel Visual Studio als Visual Studio Code hebben invoegextensies om u te helpen bij het ontwikkelen IoT Edge oplossingen. Deze extensies bieden taalspecifieke sjablonen voor het maken en implementeren van nieuwe IoT Edge scenario's. De Azure IoT Edge-extensies voor Visual Studio en Visual Studio Code helpen u bij het coden, bouwen, implementeren en foutopsporing van uw IoT Edge oplossingen. U kunt een volledige IoT Edge oplossing maken die meerdere modules bevat, en de extensies werken automatisch een implementatiemanifestsjabloon bij met elke nieuwe module-toevoeging. Met de extensies kunt u uw IoT-apparaten ook beheren vanuit Visual Studio of Visual Studio Code. Modules implementeren op een apparaat, de status bewaken en berichten weergeven wanneer ze bij IoT Hub. Beide extensies gebruiken het [ontwikkelhulpprogramma IoT EdgeHub](#iot-edgehub-dev-tool) om lokaal uitvoeren en debuggen van modules op uw ontwikkelmachine mogelijk te maken.

Als u liever ontwikkelt met andere editors of vanuit de CLI, biedt het hulpprogramma Azure IoT Edge dev opdrachten, zodat u vanaf de opdrachtregel kunt ontwikkelen en testen. U kunt nieuwe scenario'IoT Edge maken, module-afbeeldingen maken, modules uitvoeren in een simulator en berichten bewaken die naar de IoT Hub.

### <a name="visual-studio-code-extension"></a>Visual Studio Code-extensie

De Azure IoT Edge-extensie voor Visual Studio Code biedt IoT Edge-modulesjablonen die zijn gebouwd op programmeertalen, waaronder C, C#, Java, Node.js en Python, evenals Azure-functies in C#.

Zie voor meer informatie en om te downloaden [Azure IoT Tools voor Visual Studio Code.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

Naast de extensies IoT Edge, kan het handig zijn om extra extensies te installeren voor het ontwikkelen. U kunt bijvoorbeeld [Docker-ondersteuning voor Visual Studio Code gebruiken](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) om uw afbeeldingen, containers en registers te beheren. Bovendien hebben alle belangrijkste ondersteunde talen extensies voor Visual Studio Code die u kunnen helpen bij het ontwikkelen van modules.

#### <a name="prerequisites"></a>Vereisten

De modulesjablonen voor sommige talen en services hebben vereisten die nodig zijn om de projectmappen op uw ontwikkelmachine te bouwen met Visual Studio Code.

| Modulesjabloon | Vereiste |
| --------------- | ------------ |
| Azure Functions | [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1) |
| C | [Git](https://git-scm.com/) |
| C# | [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1) |
| Java | <ul><li>[Java SE Development Kit 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) <li> [De omgevingsvariabele JAVA_HOME instellen](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Azure IoT Edge Node.js modulegenerator](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Visual Studio-extensie van 2017/2019

De Azure IoT Edge voor Visual Studio bieden een IoT Edge modulesjabloon die is gebouwd op C# en C.

Zie voor meer informatie en om te downloaden Azure IoT Edge Tools voor [Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) of [Azure IoT Edge Tools voor Visual Studio 2019.](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)

### <a name="iot-edge-dev-tool"></a>IoT Edge v-hulpprogramma

Het Azure IoT Edge ontwikkelhulpprogramma vereenvoudigt IoT Edge ontwikkeling met opdrachtregelmogelijkheden. Dit hulpprogramma biedt CLI-opdrachten om modules te ontwikkelen, fouten op te sporen en te testen. Het IoT Edge ontwikkelhulpprogramma werkt met uw ontwikkelsysteem, ongeacht of u de afhankelijkheden handmatig op uw computer hebt geïnstalleerd of de IoT Edge dev-container.

Zie voor meer informatie en om aan de slag [te IoT Edge wiki over dev-hulpprogramma's.](https://github.com/Azure/iotedgedev/wiki)

## <a name="testing-tools"></a>Testhulpprogramma's

Er bestaan verschillende testhulpprogramma's om u te helpen IoT Edge of foutopsporingsmodules efficiënter te simuleren. In de volgende tabel ziet u een vergelijking op hoog niveau tussen de hulpprogramma's en vervolgens worden elk hulpprogramma in afzonderlijke secties specifieker beschreven.

Alleen de IoT Edge runtime wordt ondersteund voor productie-implementaties, maar met de volgende hulpprogramma's kunt u eenvoudig IoT Edge maken voor ontwikkelings- en testdoeleinden. Deze hulpprogramma's sluiten elkaar niet uit, maar kunnen samenwerken voor een volledige ontwikkelervaring.

| Hulpprogramma | Ook wel bekend als | Ondersteunde platforms | Ideaal voor |
| ---- | ------------- | ------------------- | --------- |
| Hulpprogramma voor IoT EdgeHub-dev  | iotedgehubdev | Windows, Linux, macOS | Een apparaat simuleren om fouten in modules op te sporen. |
| IoT Edge v-container | iotedgedev | Windows, Linux, macOS | Ontwikkelen zonder afhankelijkheden te installeren. |
| IoT Edge runtime in een container | iotedgec | Windows, Linux, macOS, ARM | Testen op een apparaat dat de runtime mogelijk niet ondersteunt. |
| IoT Edge apparaatcontainer maken | toolboc/azure-iot-edge-device-container | Windows, Linux, macOS, ARM | Een scenario testen met veel IoT Edge apparaten op schaal. |

### <a name="iot-edgehub-dev-tool"></a>Hulpprogramma voor IoT EdgeHub-dev

Het ontwikkelhulpprogramma Azure IoT EdgeHub biedt een lokale ontwikkel- en foutopsporingservaring. Met het hulpprogramma kunt u IoT Edge modules starten zonder de IoT Edge-runtime, zodat u lokaal modules en oplossingen kunt maken, ontwikkelen, testen, uitvoeren en fouten kunt opsporen IoT Edge modules en oplossingen. U hoeft geen afbeeldingen naar een containerregister te pushen en deze te implementeren op een apparaat om te testen.

Het hulpprogramma IoT EdgeHub-dev is ontworpen om samen met de extensies Visual Studio en Visual Studio Code te werken, evenals met het hulpprogramma IoT Edge dev. Het biedt ondersteuning voor ontwikkeling van interne lussen en het testen van buitenste lussen, dus kan ook worden geïntegreerd met de DevOps-hulpprogramma's.

Zie Azure [IoT EdgeHub dev tool](https://pypi.org/project/iotedgehubdev/)voor meer informatie en om te installeren.

### <a name="iot-edge-dev-container"></a>IoT Edge v-container

De Azure IoT Edge v-container is een Docker-container met alle afhankelijkheden die u nodig hebt voor IoT Edge ontwikkeling. Met deze container kunt u eenvoudig aan de slag met de taal waarin u wilt ontwikkelen, waaronder C#, Python, Node.js en Java. U hoeft alleen maar een container-engine te installeren, zoals Docker of Moby, om de container naar uw ontwikkelmachine te halen.

Zie voor meer informatie [Azure IoT Edge dev-container](https://github.com/Azure/iotedgedev/wiki/quickstart-with-iot-edge-dev-container).

### <a name="iot-edge-runtime-in-a-container"></a>IoT Edge runtime in een container

De IoT Edge runtime in een container biedt een volledige runtime die uw apparaat connection string als een omgevingsvariabele. Met deze container kunt u de IoT Edge en scenario's testen op een systeem dat de runtime mogelijk niet systeemeigen ondersteunt, zoals macOS. Alle modules die u implementeert, worden buiten de runtimecontainer gestart. Als u wilt dat de runtime en alle geïmplementeerde modules binnen dezelfde container bestaan, kunt u in plaats daarvan de IoT Edge apparaatcontainer overwegen.

Zie Running Azure IoT Edge in a container (Een Azure IoT Edge [uitvoeren in een container) voor meer informatie.](https://github.com/Azure/iotedgedev/tree/master/docker/runtime)

### <a name="iot-edge-device-container"></a>IoT Edge apparaatcontainer maken

De IoT Edge-apparaatcontainer is een volledig IoT Edge, klaar om te worden gestart op elke computer met een container-engine. De apparaatcontainer bevat de IoT Edge runtime en een container-engine zelf. Elk exemplaar van de container is een volledig functioneel zelfinrichtingsapparaat IoT Edge ingericht. De apparaatcontainer ondersteunt externe debugging van modules, zolang er een netwerkroute naar de module is. De apparaatcontainer is goed voor het snel maken van grote aantallen IoT Edge om scenario's op schaal of Azure Pipelines te testen. Het biedt ook ondersteuning voor implementatie naar kubernetes via Helm.

Zie apparaatcontainer Azure IoT Edge [meer informatie.](https://github.com/toolboc/azure-iot-edge-device-container)

## <a name="devops-tools"></a>DevOps-hulpprogramma's

Wanneer u klaar bent om op schaal oplossingen te ontwikkelen voor uitgebreide productiescenario's, kunt u profiteren van moderne DevOps-principes, waaronder automatisering, bewaking en gestroomlijnde processen voor software-engineering. IoT Edge heeft extensies ter ondersteuning van DevOps-hulpprogramma's, waaronder Azure DevOps, Azure DevOps Projects en Jenkins. Als u een bestaande pijplijn wilt aanpassen of een ander DevOps-hulpprogramma wilt gebruiken, zoals CircleCI of CircleCI, kunt u dit doen met de CLI-functies die zijn opgenomen in het hulpprogramma IoT Edge dev.

Zie de volgende pagina's voor meer informatie, richtlijnen en voorbeelden:

* [Continue integratie en continue implementatie naar Azure IoT Edge](how-to-continuous-integration-continuous-deployment.md)
* [Een CI/CD-pijplijn voor IoT Edge maken met Azure DevOps Starter](how-to-devops-starter.md)
* [IoT Edge DevOps GitHub-opslagplaats](https://github.com/toolboc/IoTEdge-DevOps)