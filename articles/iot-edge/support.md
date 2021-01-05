---
title: Ondersteunde besturings systemen, container engines-Azure IoT Edge
description: Meer informatie over de besturings systemen die de Azure IoT Edge daemon en runtime kunnen uitvoeren en ondersteunde container engines voor uw productie apparaten
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 063ef6b76e031277d93297512e16d30a86aae45c
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97808518"
---
# <a name="azure-iot-edge-supported-systems"></a>Ondersteunde systemen Azure IoT Edge

In dit artikel vindt u informatie over welke systemen en onderdelen worden ondersteund door IoT Edge, of dat nu officieel of in de preview-versie is.

Als u problemen ondervindt bij het gebruik van de Azure IoT Edge-service, zijn er verschillende manieren om ondersteuning te zoeken. Probeer een van de volgende kanalen voor ondersteuning:

**Fouten rapporteren** : het meren deel van de ontwikkeling dat in het Azure IOT Edge product plaatsvindt, vindt plaats in het IOT Edge open-source project. U kunt fouten melden op de [pagina kwesties](https://github.com/azure/iotedge/issues) van het project. Oplossingen maken snel hun eigen werk van het project in op product updates.

**Klanten ondersteuning van micro soft** : gebruikers die een [ondersteunings plan](https://azure.microsoft.com/support/plans/) hebben, kunnen het micro soft Customer Support-team benaderen door rechtstreeks vanuit de [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)een ondersteunings ticket te maken.

**Functie aanvragen** – het Azure IOT Edge product traceert functie aanvragen via de [pagina gebruikers spraak](https://feedback.azure.com/forums/907045-azure-iot-edge)van het product.

## <a name="container-engines"></a>Container engines

Azure IoT Edge-modules worden geïmplementeerd als containers, zodat IoT Edge een container Engine nodig hebt om deze te starten. Micro soft biedt een container engine, Moby-engine, om aan deze vereiste te voldoen. Deze container engine is gebaseerd op het open-source project Moby. Docker CE en docker EE zijn andere populaire container engines. Ze zijn ook gebaseerd op het open-source project Moby en zijn compatibel met Azure IoT Edge. Micro soft biedt best mogelijke ondersteuning voor systemen die gebruikmaken van deze container motoren; Micro soft kan echter geen oplossingen verzenden voor problemen. Daarom raadt micro soft aan om Moby-engine te gebruiken op productie systemen.

<br>
<center>

![De Moby-engine als container runtime](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Besturingssystemen

Azure IoT Edge wordt uitgevoerd op de meeste besturings systemen waarop containers kunnen worden uitgevoerd. niet al deze systemen worden echter gelijkelijk ondersteund. Besturings systemen zijn gegroepeerd in lagen die het niveau van ondersteunings gebruikers vertegenwoordigen.

* Systemen met laag 1 worden ondersteund. Voor systemen met laag 1, micro soft:
  * heeft dit besturings systeem in geautomatiseerde tests
  * bevat installatie pakketten voor deze
* Systemen uit de laag 2 zijn compatibel met Azure IoT Edge en kunnen relatief eenvoudig worden gebruikt. Voor laag 2-systemen:
  * Micro soft heeft op de platformen informeel getest of weet of een partner Azure IoT Edge op het platform heeft uitgevoerd
  * Installatie pakketten voor andere platformen kunnen op deze platforms worden uitgevoerd

De familie van het hostbesturingssysteem moet altijd overeenkomen met de familie van het gast besturingssysteem dat in de container van een module wordt gebruikt. Met andere woorden, u kunt alleen Linux-containers gebruiken op Linux-en Windows-containers in Windows. Wanneer u Windows gebruikt, worden alleen geïsoleerde containers verwerken ondersteund, niet geïsoleerde Hyper-V-containers.  

<br>
<center>

![Host OS komt overeen met gast besturingssysteem](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Tier 1

De systemen die worden vermeld in de volgende tabel worden ondersteund door micro soft, algemeen beschikbaar of in open bare preview, en worden getest met elke nieuwe release. 

| Besturingssysteem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry Pi OS Stretch |  | ![Raspberry Pi OS stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu-Server 16,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Open bare preview  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu-Server 18,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Open bare preview |
| [Windows 10 IOT Enter prise](/windows/iot-core/windows-iot-enterprise), build 17763 | ![Windows 10 IoT Enter prise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IOT core](/windows/iot-core/windows-iot-core), build 17763 | ![Windows IoT core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](/windows-server/get-started-19/rel-notes-19), build 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](/windows/iot-core/windows-server), build 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |

De Windows-besturings systemen die hierboven worden vermeld, zijn de vereisten voor apparaten met Windows-containers in Windows. Dit is de enige ondersteunde configuratie voor productie. Met de Azure IoT Edge-installatie pakketten voor Windows is het gebruik van Linux-containers in Windows toegestaan. deze configuratie is echter alleen voor ontwikkelings-en test doeleinden. 

### <a name="tier-2"></a>Tier 2

De systemen die in de volgende tabel worden vermeld, worden beschouwd als compatibel met Azure IoT Edge, maar worden niet actief getest of onderhouden door micro soft.

| Besturingssysteem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Inge sloten Linux Flex-besturings systeem voor docenten](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Met begeleiding embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Met begeleiding embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Het universeel-besturings systeem van mentor embedded Linux](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor embedded Linux universeel OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor embedded Linux universeel OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind stroom 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspberry Pi OS Buster <sup>1</sup> |  | ![Raspberry Pi OS Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspberry Pi OS Buster + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20,04 <sup>2</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20,04 + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian 10-systemen, waaronder RASPBERRY Pi OS Buster, gebruiken een versie van OpenSSL die IOT Edge niet ondersteunt. Gebruik de volgende opdracht om een eerdere versie te installeren voordat u IoT Edge installeert:

```bash
sudo apt-get install libssl1.1
```

<sup>2</sup> de Debian 9-pakketten van de [Azure IOT Edge releases opslag plaats](https://github.com/Azure/azure-iotedge/releases) moeten van het vak werken met Ubuntu 20,04.

## <a name="releases"></a>Releases

IoT Edge release-assets en release opmerkingen zijn beschikbaar op de pagina [met Azure iotedge-releases](https://github.com/Azure/azure-iotedge/releases) . Deze sectie bevat informatie uit deze release opmerkingen waarmee u de onderdelen van elke versie gemakkelijker kunt visualiseren.

IoT Edge onderdelen kunnen afzonderlijk worden geïnstalleerd of bijgewerkt, en zijn achterwaarts compatibel met onderdelen van oudere versies. De volgende tabel bevat de onderdelen die zijn opgenomen in elke versie:

| Release | Beveiligings-daemon | Edge hub<br>Edge-agent | Libiothsm | Moby |
|--|--|--|--|--|
| **1.0.10** | 1.0.10 | 1.0.10 | 1.0.10 |  |
| **1.0.9** | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 |  |
| **1.0.8** | 1.0.8 | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl, CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

IoT Edge maakt gebruik van de micro soft. Azure. devices. client-SDK. Zie de [Azure IOT C# SDK github opslag plaats](https://github.com/Azure/azure-iot-sdk-csharp) of de [Azure SDK voor .net-referentie-inhoud](/dotnet/api/overview/azure/iot/client)voor meer informatie. De volgende lijst bevat de versie van de client-SDK waarvoor elke release wordt getest:

* **IOT Edge 1.0.10**: Client SDK 1.28.0
* **IOT Edge 1.0.9**: Client SDK 1.21.1
* **IOT Edge 1.0.8**: Client SDK 1.20.3
* **IOT Edge 1.0.7**: Client SDK 1.20.1
* **IOT Edge 1.0.6**: Client SDK 1.17.1
* **IOT Edge 1.0.5**: Client SDK 1.17.1

## <a name="virtual-machines"></a>Virtual Machines

Azure IoT Edge kunnen worden uitgevoerd op virtuele machines. Het gebruik van een virtuele machine als een IoT Edge apparaat is gebruikelijk wanneer klanten bestaande infra structuur willen uitbreiden met Edge Intelligence. De serie van het VM-besturings systeem van de host moet overeenkomen met de familie van het gast besturingssysteem dat in de container van een module wordt gebruikt. Deze vereiste is hetzelfde als wanneer Azure IoT Edge rechtstreeks op een apparaat wordt uitgevoerd. Azure IoT Edge is neutraal van de onderliggende technologie en werkt in Vm's op basis van platforms, zoals Hyper-V en vSphere.

<br>
<center>

![Azure IoT Edge in een VM](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Minimale systeem vereisten

Azure IoT Edge is uitstekend geschikt voor apparaten met een Raspberry-Pi3 op server kwaliteit. Het kiezen van de juiste hardware voor uw scenario is afhankelijk van de werk belastingen die u wilt uitvoeren. Het maken van de definitieve beslissing van het apparaat kan gecompliceerd zijn. u kunt echter eenvoudig beginnen met het prototypen van een oplossing op traditionele laptops of Bureau bladen.

Ervaring tijdens het prototypen helpt bij het selecteren van de uiteindelijke selectie van apparaten. Vragen die u moet overwegen:

* Hoeveel modules bevinden zich in uw werk belasting?
* Hoeveel lagen delen de containers van de modules?
* In welke taal zijn uw modules geschreven?
* Hoeveel gegevens worden er door uw modules verwerkt?
* Hebben uw modules gespecialiseerde hardware nodig om de werk belastingen te versnellen?
* Wat zijn de gewenste prestatie kenmerken van uw oplossing?
* Wat is uw hardware-budget?
