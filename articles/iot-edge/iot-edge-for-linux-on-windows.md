---
title: Wat is Azure IoT Edge voor Linux op Windows | Microsoft Docs
description: Overzicht van u kunt Linux IoT Edge-modules uitvoeren op Windows 10-apparaten
author: kgremban
manager: philmea
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 330eaf5c12372347917e9f3a4aeafb6a2088c592
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492571"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows-preview"></a>Wat is Azure IoT Edge voor Linux op Windows (preview)?

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Met Azure IoT Edge voor Linux in Windows kunt u in containers geplaatste Linux-workloads uitvoeren naast Windows-toepassingen in Windows IoT-implementaties. Bedrijven die vertrouwen op Windows IoT om hun rand apparaten te laten werken, kunnen nu profiteren van de Cloud-systeem eigen analyse oplossingen die in Linux zijn ingebouwd.

IoT Edge voor Linux in Windows werkt door een virtuele Linux-machine uit te voeren op een Windows-apparaat. De virtuele Linux-machine wordt vooraf geïnstalleerd met de IoT Edge runtime. Alle IoT Edge modules die op het apparaat zijn geïmplementeerd, worden uitgevoerd in de virtuele machine. Ondertussen kunnen Windows-toepassingen die worden uitgevoerd op het Windows-host-apparaat communiceren met de modules die worden uitgevoerd op de virtuele Linux-machine.

[Ga](how-to-install-iot-edge-on-windows.md) vandaag nog aan de slag met de preview-versie.

>[!NOTE]
>Overweeg onze [product enquête](https://aka.ms/AzEFLOW-Registration) te nemen om ons te helpen Azure IOT Edge voor Linux op Windows te verbeteren op basis van uw IOT Edge achtergrond en doel stellingen. U kunt deze enquête ook gebruiken om u te registreren voor toekomstige Azure IoT Edge voor Linux op Windows-aankondigingen.

## <a name="components"></a>Onderdelen

IoT Edge voor Linux in Windows maakt gebruik van de volgende onderdelen om Linux-en Windows-workloads naast elkaar uit te voeren en probleemloos te communiceren:

* **Een virtuele Linux-machine met Azure IOT Edge**: een virtuele Linux-machine, gebaseerd op het [Mariner](https://github.com/microsoft/CBL-Mariner) -besturings systeem van micro soft, is gebouwd met de IOT Edge runtime en gevalideerd als een omgeving die wordt ondersteund als laag 1 voor IOT Edge workloads.

* **Windows-beheer centrum**: een IOT Edge extensie voor Windows-beheer centrum vereenvoudigt de installatie, configuratie en diagnose van IOT Edge op de virtuele Linux-machine. Windows-beheer centrum kan IoT Edge voor Linux op het lokale apparaat implementeren of verbinding maken met doel apparaten en ze op afstand beheren.

* **Microsoft Update**: integratie met Microsoft Update houdt de Windows runtime-onderdelen, de Mariner Linux-VM en IOT Edge up-to-date.

![Windows en de virtuele Linux-machine worden parallel uitgevoerd, terwijl het Windows-beheer centrum beide onderdelen beheert](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

Bidirectionele communicatie tussen Windows-processen en de virtuele Linux-machine betekent dat Windows-processen gebruikers interfaces of hardware-proxy's kunnen bieden voor werk belastingen die worden uitgevoerd in de Linux-containers.

## <a name="samples"></a>Voorbeelden

IoT Edge voor Linux op Windows is een nadruk op de interoperabiliteit tussen de Linux-en Windows-onderdelen.

Zie [Windows 10 IOT](https://github.com/microsoft/Windows-IoT-Samples)-voor beelden voor steek proeven die de communicatie tussen Windows-toepassingen en IOT Edge modules demonstreren.

## <a name="public-preview"></a>Open bare preview

IoT Edge voor Linux in Windows is momenteel beschikbaar als [open bare preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). De installatie-en beheer processen kunnen afwijken van de algemene beschik bare functies.

## <a name="support"></a>Ondersteuning

Gebruik de IoT Edge ondersteuning en feedback kanalen om hulp te krijgen bij IoT Edge voor Linux in Windows.

**Fouten rapporteren** : fouten kunnen worden gerapporteerd op de [pagina kwesties](https://github.com/azure/iotedge/issues) van het open-source project IOT Edge. Fouten gerelateerd aan Azure IoT Edge voor Linux op Windows kunnen worden gerapporteerd op de [pagina met iotedge-eflow-problemen](https://github.com/azure/iotedge-eflow/issues).

**Klanten ondersteuning van micro soft** : gebruikers die een [ondersteunings plan](https://azure.microsoft.com/support/plans/) hebben, kunnen het micro soft Customer Support-team benaderen door rechtstreeks vanuit de [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)een ondersteunings ticket te maken.

**Functie aanvragen** – het Azure IOT Edge product traceert functie aanvragen via de [pagina gebruikers spraak](https://feedback.azure.com/forums/907045-azure-iot-edge)van het product.

## <a name="next-steps"></a>Volgende stappen

Bekijk [IOT Edge voor Linux op Windows 10 IOT Enter prise](https://aka.ms/EFLOWPPC9) voor meer informatie en een voor beeld van actie.

Volg de stappen in [installeren en inrichten Azure IOT Edge voor Linux op een Windows-apparaat](how-to-install-iot-edge-on-windows.md) om een apparaat in te stellen met IOT Edge voor Linux in Windows.
