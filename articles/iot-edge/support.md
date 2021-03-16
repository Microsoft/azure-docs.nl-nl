---
title: Ondersteunde besturings systemen, container engines-Azure IoT Edge
description: Meer informatie over de besturings systemen die de Azure IoT Edge daemon en runtime kunnen uitvoeren en ondersteunde container engines voor uw productie apparaten
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a3656d6dd81132a7fd10103fc0199d55d9288df3
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489606"
---
# <a name="azure-iot-edge-supported-systems"></a>Ondersteunde systemen Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

In dit artikel vindt u informatie over welke systemen en onderdelen worden ondersteund door IoT Edge, of dat nu officieel of in de preview-versie is.

## <a name="get-support"></a>Ondersteuning krijgen

Als u problemen ondervindt bij het gebruik van de Azure IoT Edge-service, zijn er verschillende manieren om ondersteuning te zoeken. Probeer een van de volgende kanalen voor ondersteuning:

**Fouten rapporteren** : het meren deel van de ontwikkeling dat in het Azure IOT Edge product plaatsvindt, vindt plaats in het IOT Edge open-source project. U kunt fouten melden op de [pagina kwesties](https://github.com/azure/iotedge/issues) van het project. Fouten gerelateerd aan Azure IoT Edge voor Linux op Windows kunnen worden gerapporteerd op de [pagina met iotedge-eflow-problemen](https://github.com/azure/iotedge-eflow/issues). Oplossingen maken snel hun eigen werk van de projecten in op product updates.

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

IoT Edge voor Linux op Windows gebruikt IoT Edge op een virtuele Linux-machine die wordt uitgevoerd op een Windows-host. Op deze manier kunt u linux-modules uitvoeren op een Windows-apparaat.

### <a name="tier-1"></a>Tier 1

De systemen die worden vermeld in de volgende tabellen worden ondersteund door micro soft, algemeen beschikbaar of in open bare preview, en worden getest met elke nieuwe release.

Azure IoT Edge ondersteunt modules die zijn gebouwd als Linux-of Windows-containers. Linux-containers kunnen worden geïmplementeerd op Linux-apparaten of worden geïmplementeerd op Windows-apparaten met behulp van IoT Edge voor Linux in Windows. Windows-containers kunnen alleen worden geïmplementeerd op Windows-apparaten.

#### <a name="linux-containers"></a>Linux-containers

Modules die zijn gebouwd als Linux-containers kunnen worden geïmplementeerd op Linux-of Windows-apparaten. Voor Linux-apparaten wordt de IoT Edge runtime rechtstreeks op het hostapparaat geïnstalleerd. Voor Windows-apparaten is een virtuele Linux-machine die is gebouwd met de IoT Edge runtime uitgevoerd op het hostapparaat.

[IOT Edge voor Linux in Windows](iot-edge-for-linux-on-windows.md) is momenteel beschikbaar als open bare preview, maar is de aanbevolen manier om IOT Edge uit te voeren op Windows-apparaten.

| Besturingssysteem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry Pi OS Stretch |  | ![Raspberry Pi OS stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu-Server 18,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Open bare preview |
| Windows 10 Pro | Open bare preview |  |  |
| Windows 10 Enterprise | Open bare preview |  |  |
| Windows 10 IoT Enterprise | Open bare preview |  |  |
| Windows Server 2019 | Open bare preview |  |  |

Alle Windows-besturings systemen moeten versie 1809 (build 17763) of hoger zijn.

>[!NOTE]
>Ondersteuning voor Ubuntu Server 16,04 is gestopt met de release van IoT Edge versie 1,1.

#### <a name="windows-containers"></a>Windows-containers

>[!IMPORTANT]
>IoT Edge 1,1 LTS is het laatste release kanaal dat ondersteuning biedt voor Windows-containers. Vanaf versie 1,2 worden Windows-containers niet ondersteund. Overweeg het gebruik of verplaatsen van [IOT Edge voor Linux in Windows](iot-edge-for-linux-on-windows.md) om IOT Edge op Windows-apparaten uit te voeren.

Modules die zijn gebouwd als Windows-containers kunnen alleen worden geïmplementeerd op Windows-apparaten.

| Besturingssysteem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Windows 10 IoT Enterprise | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server 2019  | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server IoT 2019 | ![check1](./media/tutorial-c-module/green-check.png) |  |  |

Alle Windows-besturings systemen moeten versie 1809 (build 17763) hebben. De specifieke build van Windows is vereist voor IoT Edge in Windows omdat de versie van de Windows-containers precies moet overeenkomen met de versie van het Windows-host-apparaat. Windows-containers maken momenteel alleen gebruik van build 17763.

>[!NOTE]
>Windows 10 IoT Core-ondersteuning is gestopt met de release van IoT Edge versie 1,1.

### <a name="tier-2"></a>Tier 2

De systemen die in de volgende tabel worden vermeld, worden beschouwd als compatibel met Azure IoT Edge, maar worden niet actief getest of onderhouden door micro soft.

| Besturingssysteem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20,04 <sup>1</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Inge sloten Linux Flex-besturings systeem voor docenten](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Met begeleiding embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Met begeleiding embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Het universeel-besturings systeem van mentor embedded Linux](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor embedded Linux universeel OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor embedded Linux universeel OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind stroom 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspberry Pi OS Buster |  | ![Raspberry Pi OS Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspberry Pi OS Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> de installatie stappen voor Ubuntu Server 18,04 in [Install of uninstall Azure IOT Edge voor Linux](how-to-install-iot-edge.md) werken zonder wijzigingen op Ubuntu 20,04.

## <a name="releases"></a>Releases

IoT Edge release-assets en release opmerkingen zijn beschikbaar op de pagina [met Azure iotedge-releases](https://github.com/Azure/azure-iotedge/releases) . Deze sectie bevat informatie uit deze release opmerkingen waarmee u de onderdelen van elke versie gemakkelijker kunt visualiseren.

IoT Edge onderdelen kunnen afzonderlijk worden geïnstalleerd of bijgewerkt, en zijn achterwaarts compatibel met onderdelen van oudere versies. De volgende tabel bevat de onderdelen die zijn opgenomen in elke versie:

| Release | Beveiligings-daemon | Edge hub<br>Edge-agent | Libiothsm | Moby |
|--|--|--|--|--|
| **1.1.0 LTS**<sup>1</sup> | 1.1.0 | 1.1.0 | 1.1.0 |   |
| **1.0.10** | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br>1.0.10.3<br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 |  |
| **1.0.9** | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 |  |
| **1.0.8** | 1.0.8 | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl, CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

<sup>1</sup> IoT Edge 1,1 is het eerste LTS-release kanaal (lange termijn ondersteuning). Deze versie heeft geen nieuwe functies geïntroduceerd, maar ontvangt fout oplossingen en beveiligings patches. IoT Edge 1,1 LTS maakt gebruik van .NET Core 3,1 en wordt tot 3 december 2022 ondersteund om te voldoen aan de [versie levenscyclus van .net core en .net 5](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

>[!IMPORTANT]
>Met de release van een ondersteunings kanaal voor de lange termijn raden wij aan alle huidige klanten met 1.0. x hun apparaten bij te werken naar 1.1. x om voortdurende ondersteuning te ontvangen.

IoT Edge maakt gebruik van de micro soft. Azure. devices. client-SDK. Zie de [Azure IOT C# SDK github opslag plaats](https://github.com/Azure/azure-iot-sdk-csharp) of de [Azure SDK voor .net-referentie-inhoud](/dotnet/api/overview/azure/iot/client)voor meer informatie. De volgende lijst bevat de versie van de client-SDK waarvoor elke release wordt getest:

| IoT Edge-versie | Micro soft. Azure. devices. client-SDK-versie |
|------------------|--------------------------------------------|
| 1.1.0 (LTS)      | 1.28.0                                     |
| 1.0.10           | 1.28.0                                     |
| 1.0.9            | 1.21.1                                     |
| 1.0.8            | 1.20.3                                     |
| 1.0.7            | 1.20.1                                     |
| 1.0.6            | 1.17.1                                     |
| 1.0.5            | 1.17.1                                     |

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
