---
title: Wat is Azure IoT Edge voor Linux in Windows | Microsoft Docs
description: Overzicht van hoe u Linux-modules kunt IoT Edge op Windows 10 apparaten
author: kgremban
manager: philmea
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 3c7fd6c842d465dd5af5257628044666f10f2ece
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538204"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows-preview"></a>Wat is Azure IoT Edge voor Linux op Windows (preview)?

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge voor Linux in Windows kunt u in containers geplaatste Linux-workloads uitvoeren naast Windows-toepassingen in Windows IoT-implementaties. Bedrijven die afhankelijk zijn van Windows IoT om hun edge-apparaten aan te kunnen, kunnen nu profiteren van de cloudeigen analyseoplossingen die in Linux worden gebouwd.

IoT Edge linux in Windows werkt door een virtuele Linux-machine op een Windows-apparaat uit te werken. De virtuele Linux-machine wordt vooraf geïnstalleerd met de IoT Edge runtime. Alle IoT Edge modules die op het apparaat zijn geïmplementeerd, worden uitgevoerd binnen de virtuele machine. Ondertussen kunnen Windows-toepassingen die worden uitgevoerd op het Windows-hostapparaat communiceren met de modules die worden uitgevoerd op de virtuele Linux-machine.

[Ga vandaag nog](how-to-install-iot-edge-on-windows.md) aan de slag met de preview.

>[!NOTE]
>Overweeg onze [productenquête om](https://aka.ms/AzEFLOW-Registration) ons te helpen de Azure IoT Edge voor Linux op Windows te verbeteren op basis van IoT Edge achtergrond en doelstellingen. U kunt deze enquête ook gebruiken om u te registreren voor toekomstige Azure IoT Edge linux op Windows-aankondigingen.

## <a name="components"></a>Onderdelen

IoT Edge voor Linux in Windows maakt gebruik van de volgende onderdelen, zodat Linux- en Windows-workloads naast elkaar kunnen worden uitgevoerd en naadloos kunnen communiceren:

* **Een virtuele Linux-machine** met Azure IoT Edge: een virtuele Linux-machine, gebaseerd op het first party [Operatingr-besturingssysteem](https://github.com/microsoft/CBL-Mariner) van Microsoft, wordt gebouwd met de IoT Edge-runtime en gevalideerd als een laag 1-ondersteunde omgeving voor IoT Edge-workloads.

* **Windows Admin Center:** een IoT Edge-extensie voor Windows Admin Center vereen mogelijk installatie, configuratie en diagnostische gegevens van IoT Edge op de virtuele Linux-machine. Windows Admin Center kunt een IoT Edge voor Linux in Windows implementeren op het lokale apparaat of verbinding maken met doelapparaten en deze extern beheren.

* **Microsoft Update:** Integratie met Microsoft Update zorgt ervoor dat de Windows-runtimeonderdelen, de Linux-VM en IoT Edge up-to-date blijven.

![Windows en de Linux-VM worden parallel uitgevoerd, terwijl de Windows Admin Center beide onderdelen beheert](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

Bi-directionele communicatie tussen het Windows-proces en de virtuele Linux-machine betekent dat Windows-processen gebruikersinterfaces of hardware-proxies kunnen bieden voor werkbelastingen die worden uitgevoerd in de Linux-containers.

## <a name="samples"></a>Voorbeelden

IoT Edge linux onder Windows ligt de nadruk op de interoperabiliteit tussen de Linux- en Windows-onderdelen.

Zie EFLOW & Windows 10 [IoT](https://aka.ms/AzEFLOW-Samples)Samples voor voorbeelden die communicatie tussen Windows-toepassingen en IoT Edge modules demonstreren.

## <a name="public-preview"></a>Openbare preview

IoT Edge voor Linux in Windows is momenteel beschikbaar als [openbare preview.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Installatie- en beheerprocessen kunnen anders zijn dan voor algemeen beschikbare functies.

## <a name="support"></a>Ondersteuning

Gebruik de IoT Edge-ondersteunings- en feedbackkanalen om hulp te krijgen met IoT Edge linux in Windows.

**Fouten melden:** er kunnen fouten worden gerapporteerd op [de pagina](https://github.com/azure/iotedge/issues) problemen van IoT Edge opensource-project. Fouten met betrekking tot Azure IoT Edge voor Linux in Windows kunnen worden gerapporteerd op de pagina [iotedge-eflow issues](https://aka.ms/AzEFLOW-Issues).

**Klantondersteuningsteam van Microsoft:** gebruikers met een ondersteuningsplan kunnen contact opnemen met het klantondersteuningsteam van Microsoft door rechtstreeks vanuit de [Azure Portal.](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac) [](https://azure.microsoft.com/support/plans/)

**Functieaanvragen:** het Azure IoT Edge product houdt functieaanvragen bij via de [user voice-pagina van het product.](https://feedback.azure.com/forums/907045-azure-iot-edge)

## <a name="next-steps"></a>Volgende stappen

Bekijk [IoT Edge voor Linux op Windows 10 IoT Enterprise](https://aka.ms/EFLOWPPC9) voor meer informatie en een voorbeeld in actie.

Volg de stappen in [Install and provision Azure IoT Edge for Linux on a Windows device](how-to-install-iot-edge-on-windows.md) (Linux installeren en inrichten op een Windows-apparaat) om een apparaat in te stellen met IoT Edge voor Linux in Windows.
