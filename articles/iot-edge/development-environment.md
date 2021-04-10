---
title: Azure IoT Edge ontwikkel omgeving | Microsoft Docs
description: Meer informatie over de ondersteunde systemen en ontwikkel hulpprogramma's van de eerste partij waarmee u IoT Edge modules kunt maken
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 517bcdab375db819b0942306df82e7285b9473b1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103487697"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Bereid uw ontwikkel-en test omgeving voor op IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge verplaatst uw bestaande bedrijfs logica naar apparaten die aan de rand worden uitgevoerd. Als u uw toepassingen en werk belastingen wilt voorbereiden om te worden uitgevoerd als [IOT Edge modules](iot-edge-modules.md), moet u deze als containers bouwen. Dit artikel bevat richt lijnen over het configureren van uw ontwikkel omgeving, zodat u een IoT Edge oplossing kunt maken. Zodra u uw ontwikkel omgeving hebt ingesteld, kunt u leren hoe u [uw eigen IOT Edge-modules ontwikkelt](module-development.md).

In een IoT Edge oplossing zijn er ten minste twee computers die u kunt overwegen. Een is de IoT Edge apparaat (of apparaten) zelf, waarmee de IoT Edge-module wordt uitgevoerd. De andere is de ontwikkel computer die u gebruikt om modules te bouwen, te testen en te implementeren. Dit artikel is voornamelijk gericht op de ontwikkel machine. De twee computers kunnen worden gebruikt voor test doeleinden. U kunt IoT Edge op uw ontwikkel computer uitvoeren en er modules op implementeren.

## <a name="operating-system"></a>Besturingssysteem

Azure IoT Edge wordt uitgevoerd op een specifieke set [ondersteunde besturings systemen](support.md). Voor het ontwikkelen van IoT Edge kunt u de meeste besturings systemen gebruiken die een container Engine kunnen uitvoeren. De container engine is een vereiste op de ontwikkel machine om uw modules als containers te bouwen en deze naar een container register te pushen.

Als uw ontwikkel computer Azure IoT Edge niet kan uitvoeren, gaat u verder in dit artikel voor meer informatie over het [testen van hulpprogram ma's](#testing-tools) waarmee u lokaal kunt testen en fouten opsporen.

Het besturings systeem van uw ontwikkel machine hoeft niet overeen te komen met het besturings systeem van uw IoT Edge-apparaat. Het besturings systeem van de container moet echter consistent zijn tussen de ontwikkel machine en het IoT Edge apparaat. U kunt bijvoorbeeld modules op een Windows-computer ontwikkelen en deze implementeren op een Linux-apparaat. De Windows-machine moet Linux-containers uitvoeren om de modules voor het Linux-apparaat te bouwen.

## <a name="container-engine"></a>Container engine

Het centrale concept van IoT Edge is dat u uw bedrijfs-en Cloud logica op afstand kunt implementeren op apparaten door deze te verpakken in containers. Als u containers wilt maken, hebt u een container Engine nodig op uw ontwikkel machine.

De enige ondersteunde container engine voor IoT Edge apparaten in productie is Moby. Alle container engines die compatibel zijn met het open container initiatief, zoals docker, kunnen echter wel IoT Edge module-installatie kopieën bouwen.

## <a name="development-tools"></a>Ontwikkelhulpprogramma’s

Visual Studio en Visual Studio code hebben extra uitbrei dingen om IoT Edge oplossingen te ontwikkelen. Deze uitbrei dingen bieden taalspecifieke sjablonen die u helpen bij het maken en implementeren van nieuwe IoT Edge scenario's. Met de Azure IoT Edge-uitbrei dingen voor Visual Studio en Visual Studio code kunt u uw IoT Edge oplossingen incoderen, bouwen, implementeren en fouten opsporen. U kunt een volledige IoT Edge-oplossing maken die meerdere modules bevat, en de uitbrei dingen updaten automatisch een sjabloon implementatie manifest bij elke nieuwe module toevoeging. Met de uitbrei dingen kunt u ook uw IoT-apparaten beheren vanuit Visual Studio of Visual Studio code. Modules implementeren op een apparaat, de status bewaken en berichten weer geven die op IoT Hub arriveren. Beide uitbrei dingen maken gebruik van het [hulp programma IOT EdgeHub dev](#iot-edgehub-dev-tool) om het lokale uitvoeren en debuggen van modules op uw ontwikkel computer ook in te scha kelen.

Als u liever met andere editors of vanuit de CLI wilt ontwikkelen, biedt het Azure IoT Edge dev tool opdrachten waarmee u vanaf de opdracht regel kunt ontwikkelen en testen. U kunt nieuwe IoT Edge scenario's maken, module-installatie kopieën bouwen, modules uitvoeren in een simulator en berichten bewaken die worden verzonden naar IoT Hub.

### <a name="visual-studio-code-extension"></a>Visual Studio Code-extensie

De Azure IoT Edge-extensie voor Visual Studio code biedt IoT Edge module sjablonen die zijn gebaseerd op programmeer talen, waaronder C, C#, Java, Node.js en Python, evenals Azure functions in C#.

Zie [Azure IOT-Hulpprogram ma's voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)voor meer informatie en om te downloaden.

Naast de uitbrei dingen van IoT Edge, is het handig om extra uitbrei dingen voor ontwikkelen te installeren. U kunt bijvoorbeeld [docker-ondersteuning voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) gebruiken voor het beheren van uw installatie kopieën, containers en registers. Daarnaast hebben alle primaire ondersteunde talen extensies voor Visual Studio-code die u kan helpen bij het ontwikkelen van modules.

#### <a name="prerequisites"></a>Vereisten

De module sjablonen voor sommige talen en services hebben vereisten die nodig zijn voor het bouwen van de project mappen op uw ontwikkel computer met Visual Studio code.

| Modulesjabloon | Vereiste |
| --------------- | ------------ |
| Azure Functions | [.NET Core 2,1 SDK](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [.NET Core 2,1 SDK](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Java SE Development Kit 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) <li> [De variabele JAVA_HOME omgeving instellen](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Generator van Azure IoT Edge Node.js-module](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Visual Studio 2017/2019-extensie

De Azure IoT Edge-hulpprogram ma's voor Visual Studio bieden een IoT Edge module sjabloon die is gebaseerd op C# en C.

Zie [Azure IOT Edge-Hulpprogram ma's voor Visual studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) of [Azure IOT Edge-Hulpprogram Ma's voor Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)voor meer informatie en om te downloaden.

### <a name="iot-edge-dev-tool"></a>Hulp programma IoT Edge dev

Het hulp programma Azure IoT Edge dev vereenvoudigt IoT Edge ontwikkeling met opdracht regel mogelijkheden. Dit hulp programma biedt CLI-opdrachten voor het ontwikkelen, opsporen en testen van modules. Het hulp programma IoT Edge dev werkt samen met uw ontwikkel systeem, of u de afhankelijkheden op uw computer hand matig hebt geïnstalleerd of gebruikmaken van de IoT Edge dev-container.

Zie [IOT Edge-wiki voor ontwikkel aars](https://github.com/Azure/iotedgedev/wiki)voor meer informatie en om aan de slag te gaan.

## <a name="testing-tools"></a>Testhulpprogramma's

Er zijn verschillende hulpprogram ma's voor testen waarmee u IoT Edge apparaten of modules voor fout opsporing efficiënter kunt simuleren. De volgende tabel bevat een vergelijking op hoog niveau tussen de hulpprogram ma's, en in de afzonderlijke secties wordt elk hulp programma specifiek beschreven.

Alleen de IoT Edge runtime wordt ondersteund voor productie-implementaties, maar met de volgende hulpprogram ma's kunt u IoT Edge-apparaten simuleren of eenvoudig maken voor ontwikkelings-en test doeleinden. Deze hulpprogram ma's sluiten elkaar niet uit, maar kunnen samen werken voor een volledige ontwikkel ervaring.

| Hulpprogramma | Ook wel bekend als | Ondersteunde platforms | Ideaal voor |
| ---- | ------------- | ------------------- | --------- |
| IoT EdgeHub dev tool  | iotedgehubdev | Windows, Linux, macOS | Een apparaat simuleren voor fout opsporing in modules. |
| IoT Edge dev-container | iotedgedev | Windows, Linux, macOS | Ontwikkelen zonder afhankelijkheden te installeren. |
| IoT Edge runtime in een container | iotedgec | Windows, Linux, macOS, ARM | Testen op een apparaat dat de runtime mogelijk niet ondersteunt. |
| IoT Edge-container | toolboc/Azure-IOT-Edge-Device-container | Windows, Linux, macOS, ARM | Testen van een scenario met veel IoT Edge apparaten op schaal. |

### <a name="iot-edgehub-dev-tool"></a>IoT EdgeHub dev tool

Het hulp programma Azure IoT EdgeHub dev biedt een lokale ontwikkel-en debug-ervaring. Het hulp programma helpt IoT Edge modules te starten zonder de IoT Edge-runtime, zodat u lokaal IoT Edge modules en oplossingen kunt maken, ontwikkelen, testen, uitvoeren en fouten opsporen. U hoeft geen installatie kopieën naar een container register te pushen en deze op een apparaat te implementeren om te testen.

Het hulp programma IoT EdgeHub dev is ontworpen voor samen werking met de Visual Studio en Visual Studio code Extensions, evenals met het hulp programma IoT Edge dev. Het ondersteunt de ontwikkeling binnen de loop van een lus en het testen van de buitenste lus, dus integreert ook met de DevOps-hulpprogram ma's.

Zie [Azure IOT EdgeHub dev tool](https://pypi.org/project/iotedgehubdev/)voor meer informatie en om te installeren.

### <a name="iot-edge-dev-container"></a>IoT Edge dev-container

De Azure IoT Edge dev-container is een docker-container met alle afhankelijkheden die u nodig hebt om IoT Edge te ontwikkelen. Met deze container kunt u gemakkelijk aan de slag gaan met de taal die u wilt ontwikkelen in, waaronder C#, Python, Node.js en Java. U hoeft alleen maar een container engine te installeren, zoals docker of Moby, om de container naar uw ontwikkel machine te halen.

Zie [Azure IOT Edge dev container](https://github.com/Azure/iotedgedev/wiki/quickstart-with-iot-edge-dev-container)(Engelstalig) voor meer informatie.

### <a name="iot-edge-runtime-in-a-container"></a>IoT Edge runtime in een container

De IoT Edge runtime in een container biedt een volledige runtime waarmee uw apparaat connection string als een omgevings variabele. Met deze container kunt u IoT Edge-modules en-scenario's testen op een systeem dat de runtime mogelijk niet systeem eigen ondersteunt, zoals macOS. Modules die u implementeert, worden buiten de runtime-container gestart. Als u wilt dat de runtime en eventuele geïmplementeerde modules in dezelfde container bestaan, kunt u het beste de IoT Edge-container van het apparaat gebruiken.

Zie [Azure IOT Edge uitvoeren in een container](https://github.com/Azure/iotedgedev/tree/master/docker/runtime)voor meer informatie.

### <a name="iot-edge-device-container"></a>IoT Edge-container

De IoT Edge-container is een volledig IoT Edge apparaat dat kan worden gestart op elke machine met een container engine. De container van het apparaat bevat de IoT Edge runtime en een container engine zelf. Elke instantie van de container is een volledig functionele self-provisioning IoT Edge apparaat. De container ondersteunt fout opsporing op afstand van modules, zolang er een netwerk route naar de module is. De container is handig voor het snel maken van grote aantallen IoT Edge apparaten om scenario's of Azure-pijp lijnen te testen. Het biedt ook ondersteuning voor implementatie naar kubernetes via helm.

Zie [Azure IOT Edge Device container](https://github.com/toolboc/azure-iot-edge-device-container)voor meer informatie.

## <a name="devops-tools"></a>DevOps-hulpprogram ma's

Wanneer u klaar bent om oplossingen te ontwikkelen voor uitgebreide productie scenario's, Profiteer dan van moderne DevOps-principes, waaronder automatisering, bewaking en gestroomlijnde software engineering processen. IoT Edge heeft uitbrei dingen voor de ondersteuning van DevOps-hulpprogram ma's, waaronder Azure DevOps, Azure DevOps Projects en Jenkins. Als u een bestaande pijp lijn wilt aanpassen of een ander DevOps-hulp programma wilt gebruiken, zoals CircleCI of TravisCI, kunt u dit doen met de CLI-functies die zijn opgenomen in het IoT Edge dev tool.

Zie de volgende pagina's voor meer informatie, richt lijnen en voor beelden:

* [Continue integratie en continue implementatie naar Azure IoT Edge](how-to-continuous-integration-continuous-deployment.md)
* [Een CI/CD-pijp lijn maken voor IoT Edge met Azure DevOps starter](how-to-devops-starter.md)
* [Azure IoT Edge Jenkins-invoeg toepassing](https://plugins.jenkins.io/azure-iot-edge)
* [IoT Edge DevOps GitHub opslag plaats](https://github.com/toolboc/IoTEdge-DevOps)