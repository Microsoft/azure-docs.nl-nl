---
title: Ondersteunde besturingssystemen, containeren engines - Azure IoT Edge
description: Ontdek welke besturingssystemen de daemon en runtime Azure IoT Edge kunnen uitvoeren, en welke containeren engines voor uw productieapparaten worden ondersteund
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/16/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 67532fce2cac0ec9d05b4caa069e63014b813bd8
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576343"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge ondersteunde systemen

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Dit artikel bevat informatie over welke systemen en onderdelen worden ondersteund door IoT Edge, officieel of in preview.

## <a name="get-support"></a>Ondersteuning krijgen

Als u problemen ervaart tijdens het gebruik Azure IoT Edge service, zijn er verschillende manieren om ondersteuning te zoeken. Probeer een van de volgende kanalen voor ondersteuning:

**Fouten melden:** het merendeel van de ontwikkeling die in het Azure IoT Edge product wordt gebruikt, vindt plaats in IoT Edge opensource-project. Er kunnen fouten worden gerapporteerd op de [pagina Problemen](https://github.com/azure/iotedge/issues) van het project. Fouten met betrekking tot Azure IoT Edge voor Linux in Windows kunnen worden gerapporteerd op de pagina [problemen met iotedge-eflow.](https://github.com/azure/iotedge-eflow/issues) Oplossingen vinden snel hun weg van de projecten naar productupdates.

**Klantondersteuningsteam van Microsoft:** gebruikers met een ondersteuningsplan kunnen contact opnemen met het klantondersteuningsteam van Microsoft door rechtstreeks vanuit de [Azure Portal.](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac) [](https://azure.microsoft.com/support/plans/)

**Functieaanvragen:** het Azure IoT Edge product houdt functieaanvragen bij via de [user voice-pagina van het product.](https://feedback.azure.com/forums/907045-azure-iot-edge)

## <a name="container-engines"></a>Container-engines

Azure IoT Edge modules worden geïmplementeerd als containers, dus IoT Edge een container-engine nodig om ze te starten. Microsoft biedt een container-engine, moby-engine, om aan deze vereiste te voldoen. Deze container-engine is gebaseerd op het opensource-project Moby. Docker CE en Docker EE zijn andere populaire containeren engines. Ze zijn ook gebaseerd op het opensource-project moby en zijn compatibel met Azure IoT Edge. Microsoft biedt best effort ondersteuning voor systemen die deze containeren engines gebruiken; Microsoft kan echter geen oplossingen verzenden voor problemen in deze oplossingen. Daarom raadt Microsoft aan om moby-engine te gebruiken op productiesystemen.

<br>
<center>

![De Moby-engine als containerruntime](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Besturingssystemen

Azure IoT Edge worden uitgevoerd op de meeste besturingssystemen die containers kunnen uitvoeren; Niet al deze systemen worden echter even ondersteund. Besturingssystemen worden gegroepeerd in lagen die het ondersteuningsniveau vertegenwoordigen dat gebruikers kunnen verwachten.

* Laag 1-systemen worden ondersteund. Voor systemen op laag 1, Microsoft:
  * heeft dit besturingssysteem in geautomatiseerde tests
  * voorziet in installatiepakketten voor deze pakketten
* Systemen op laag 2 zijn compatibel met Azure IoT Edge en kunnen relatief eenvoudig worden gebruikt. Voor systemen op laag 2:
  * Microsoft heeft informele tests uitgevoerd op de platforms of weet van een partner die Azure IoT Edge op het platform
  * Installatiepakketten voor andere platforms kunnen op deze platforms werken

De familie van het host-besturingssysteem moet altijd overeenkomen met de familie van het gast-besturingssysteem dat in de container van een module wordt gebruikt.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Met andere woorden, u kunt alleen Linux-containers in Linux- en Windows-containers in Windows gebruiken. Wanneer u Windows-containers gebruikt, worden alleen geïsoleerde procescontainers ondersteund, niet geïsoleerde Hyper-V-containers.  

IoT Edge linux in Windows gebruikt IoT Edge in een virtuele Linux-machine die wordt uitgevoerd op een Windows-host. Op deze manier kunt u Linux-modules uitvoeren op een Windows-apparaat.
:::moniker-end
<!-- end 1.1 -->

### <a name="tier-1"></a>Tier 1

De systemen die in de volgende tabellen worden vermeld, worden ondersteund door Microsoft, algemeen beschikbaar of in openbare preview, en worden getest met elke nieuwe release.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Azure IoT Edge ondersteunt modules die zijn gebouwd als Linux- of Windows-containers. Linux-containers kunnen worden geïmplementeerd op Linux-apparaten of op Windows-apparaten met behulp van IoT Edge voor Linux in Windows. Windows-containers kunnen alleen worden geïmplementeerd op Windows-apparaten.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Azure IoT Edge versie 1.2 ondersteunt alleen modules die zijn gebouwd als Linux-containers.

Er is momenteel geen ondersteunde manier om versie IoT Edge 1.2 uit te voeren op Windows-apparaten. [IoT Edge linux](iot-edge-for-linux-on-windows.md) op Windows is de aanbevolen manier om IoT Edge op Windows-apparaten uit te voeren, maar wordt momenteel alleen uitgevoerd IoT Edge 1.1. Raadpleeg voor meer informatie de [IoT Edge versie 1.1](?view=iotedge-2018-06&preserve-view=true) van dit artikel.

:::moniker-end
<!-- end 1.2 -->

#### <a name="linux-containers"></a>Linux-containers

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Modules die zijn gebouwd als Linux-containers kunnen worden geïmplementeerd op Linux- of Windows-apparaten. Voor Linux-apparaten wordt IoT Edge runtime rechtstreeks op het hostapparaat geïnstalleerd. Voor Windows-apparaten wordt een virtuele Linux-machine die vooraf is gebouwd met de IoT Edge runtime uitgevoerd op het hostapparaat.

[IoT Edge voor Linux in Windows](iot-edge-for-linux-on-windows.md) is momenteel beschikbaar als openbare preview, maar is de aanbevolen manier om een IoT Edge op Windows-apparaten.

| Besturingssysteem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry Pi OS Stretch |  | ![Raspberry Pi OS Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Openbare preview |
| Windows 10 Pro | Openbare preview |  |  |
| Windows 10 Enterprise | Openbare preview |  |  |
| Windows 10 IoT Enterprise | Openbare preview |  |  |
| Windows Server 2019 | Openbare preview |  |  |

Alle Windows-besturingssystemen moeten versie 1809 (build 17763) of hoger zijn.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

| Besturingssysteem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry Pi OS Stretch |  | ![Raspberry Pi OS Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Openbare preview |

:::moniker-end
<!-- end 1.2 -->

>[!NOTE]
>Ondersteuning voor Ubuntu Server 16.04 is beëindigd met de release van IoT Edge versie 1.1.

#### <a name="windows-containers"></a>Windows-containers

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
>[!IMPORTANT]
>IoT Edge 1.1 LTS is het laatste releasekanaal dat Ondersteuning biedt voor Windows-containers. Vanaf versie 1.2 worden Windows-containers niet ondersteund. Overweeg het gebruik of de overstap [naar IoT Edge linux in Windows om](iot-edge-for-linux-on-windows.md) deze IoT Edge Windows-apparaten uit te voeren.

Modules die zijn gebouwd als Windows-containers kunnen alleen worden geïmplementeerd op Windows-apparaten.

| Besturingssysteem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Windows 10 IoT Enterprise | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server 2019  | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server IoT 2019 | ![check1](./media/tutorial-c-module/green-check.png) |  |  |

Alle Windows-besturingssystemen moeten versie 1809 (build 17763) zijn. De specifieke build van Windows is vereist voor windows IoT Edge omdat de versie van de Windows-containers exact moet overeenkomen met de versie van het Windows-hostapparaat. Windows-containers gebruiken momenteel alleen build 17763.

>[!NOTE]
>Windows 10 IoT Core ondersteuning beëindigd met de release van IoT Edge versie 1.1.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
IoT Edge 1.1 LTS is het laatste releasekanaal dat ondersteuning biedt voor Windows-containers. Vanaf versie 1.2 worden Windows-containers niet ondersteund.

Raadpleeg de IoT Edge [1.1](?view=iotedge-2018-06&preserve-view=true) van dit artikel voor informatie over ondersteunde besturingssystemen voor Windows-containers.

:::moniker-end
<!-- end 1.2 -->

### <a name="tier-2"></a>Tier 2

De systemen in de volgende tabel worden beschouwd als compatibel met Azure IoT Edge, maar worden niet actief getest of onderhouden door Microsoft.

| Besturingssysteem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20.04 <sup>1</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Begeleiding Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Begeleiding Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Begeleiding Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Begeleiding Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Begeleiding Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Begeleiding Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Begeleiding Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspberry Pi OS Sturingssystemen |  | ![Raspberry Pi OS Sturingssystemen + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspberry Pi OS Sturingssystemen + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> De installatiestappen voor Ubuntu Server 18.04 in Azure IoT Edge installeren of verwijderen voor [Linux](how-to-install-iot-edge.md) moeten zonder wijzigingen in Ubuntu 20.04 werken.

## <a name="releases"></a>Releases

IoT Edge release-assets en release-opmerkingen zijn beschikbaar op de [releasepagina azure-iotedge.](https://github.com/Azure/azure-iotedge/releases) Deze sectie bevat informatie uit deze release-opmerkingen om u te helpen de onderdelen van elke versie gemakkelijker te visualiseren.

De volgende tabel bevat de onderdelen die zijn opgenomen in elke release vanaf 1.2.0. De onderdelen in deze tabel kunnen afzonderlijk worden geïnstalleerd of bijgewerkt en zijn achterwaarts compatibel met oudere versies.

| Release | aziot-edge | edgeHub<br>edgeAgent | aziot-identity-service |
| ------- | ---------- | -------------------- | ---------------------- |
| **1.2** | 1.2.0      | 1.2.0                | 1.2.0                  |

De volgende tabel bevat de onderdelen die zijn opgenomen in elke release tot aan de 1.1 LTS-release. De onderdelen in deze tabel kunnen afzonderlijk worden geïnstalleerd of bijgewerkt en zijn achterwaarts compatibel met oudere versies.

| Release | iotedge | edgeHub<br>edgeAgent | libiothsm | Moby |
|--|--|--|--|--|
| **1.1 LTS**<sup>1</sup> | 1.1.0<br>1.1.1<br><br> | 1.1.0<br>1.1.1<br>1.1.2 | 1.1.0<br>1.1.1<br><br> |   |
| **1.0.10** | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br>1.0.10.3<br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 |  |
| **1.0.9** | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 |  |
| **1.0.8** | 1.0.8 | 1.0.8<br>1.0.8.1<br>1.0.8.2<br>1.0.8.3<br>1.0.8.4<br>1.0.8.5 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 3.0.4 (ARMv7hl, CentOS)<br>3.0.5 |
| **1.0.6** | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

<sup>1</sup> IoT Edge 1.1 is het eerste LTS-releasekanaal (long-term support). Deze versie heeft geen nieuwe functies geïntroduceerd, maar ontvangt beveiligingsupdates en oplossingen voor regressies. IoT Edge 1.1 LTS maakt gebruik van .NET Core 3.1 en wordt ondersteund tot en met 3 december 2022 om overeen te komen met de levenscyclus van [de .NET Core- en .NET 5-release.](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)

>[!IMPORTANT]
>Met de release van een ondersteuningskanaal voor de lange termijn wordt u aangeraden dat alle huidige klanten met 1.0.x hun apparaten upgraden naar 1.1.x om doorlopende ondersteuning te ontvangen.

IoT Edge maakt gebruik van de SDK Microsoft.Azure.Devices.Client. Zie de [GitHub-opslagplaats van de Azure IoT C# SDK](https://github.com/Azure/azure-iot-sdk-csharp) of de [Azure SDK voor .NET-referentie-inhoud voor meer informatie.](/dotnet/api/overview/azure/iot/client) In de volgende lijst ziet u de versie van de client-SDK die voor elke release is getest:

| IoT Edge-versie | Microsoft.Azure.Devices.Client SDK-versie |
|------------------|--------------------------------------------|
| 1.2.0            | 1.33.4-NestedEdge
| 1.1 (LTS)        | 1.28.0                                     |
| 1.0.10           | 1.28.0                                     |
| 1.0.9            | 1.21.1                                     |
| 1.0.8            | 1.20.3                                     |
| 1.0.7            | 1.20.1                                     |
| 1.0.6            | 1.17.1                                     |
| 1.0.5            | 1.17.1                                     |

## <a name="virtual-machines"></a>Virtual Machines

Azure IoT Edge kunnen worden uitgevoerd op virtuele machines. Het gebruik van een virtuele machine IoT Edge is gebruikelijk wanneer klanten de bestaande infrastructuur willen verbeteren met edge intelligence. De familie van het host-VM-besturingssysteem moet overeenkomen met de familie van het gast-besturingssysteem dat in de container van een module wordt gebruikt. Deze vereiste is hetzelfde als wanneer Azure IoT Edge rechtstreeks op een apparaat wordt uitgevoerd. Azure IoT Edge is onafhankelijk van de onderliggende virtualisatietechnologie en werkt in VM's powered by platformen zoals Hyper-V en vSphere.

<br>

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

<center>

![Azure IoT Edge in een VM](./media/support/edge-on-vm-with-windows.png)

</center>

::: moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

<center>

![Azure IoT Edge in een VM](./media/support/edge-on-vm.png)

</center>

:::moniker-end

## <a name="minimum-system-requirements"></a>Minimale systeemvereisten

Azure IoT Edge kan heel goed worden uitgevoerd op apparaten van zo klein als een Raspberry Pi3 tot hardware op serverkwaliteit. De keuze van de juiste hardware voor uw scenario is afhankelijk van de workloads die u wilt uitvoeren. Het maken van de uiteindelijke apparaatbeslissing kan ingewikkeld zijn; U kunt echter eenvoudig beginnen met het maken van prototypen van een oplossing op traditionele laptops of desktops.

Ervaring met het maken van prototypen helpt u bij het selecteren van uw uiteindelijke apparaat. Vragen die u moet overwegen, zijn onder andere:

* Hoeveel modules zijn er in uw workload?
* Hoeveel lagen delen de containers van uw modules?
* In welke taal zijn uw modules geschreven?
* Hoeveel gegevens worden door uw modules verwerkt?
* Hebben uw modules speciale hardware nodig om hun workloads te versnellen?
* Wat zijn de gewenste prestatiekenmerken van uw oplossing?
* Wat is uw hardwarebudget?
