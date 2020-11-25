---
title: SIM-gegevens integreren in de oplossing voor externe controle-Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Telefónica SIM-gegevens integreert in de oplossing voor externe controle.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: 21d84bea808ba5832b81d415001a3fc25a7e1630
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006760"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>SIM-gegevens integreren in de oplossing voor controle op afstand

IoT-apparaten maken vaak verbinding met de Cloud met behulp van een SIM-kaart waarmee gegevens stromen vanaf elke locatie kunnen worden verzonden. De Azure IoT-oplossing voor externe controle maakt het mogelijk om met IoT beheerde connectiviteits gegevens te integreren, zodat Opera tors ook de status van het apparaat kunnen volgen via de gegevens van de IoT-SIMKAART.

Externe bewaking biedt de Telefónica IoT-connectiviteit buiten de box-integratie, waardoor klanten die gebruikmaken van het IoT-connectiviteits platform hun apparaat-SIMs-connectiviteits gegevens synchroniseren met hun oplossingen. Deze oplossing kan worden uitgebreid ter ondersteuning van andere IoT-connectiviteits providers via de GitHub- [opslag plaats](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).

In deze zelfstudie leert u het volgende:

* Telefónica IoT SIM-gegevens integreren in de oplossing voor bewaking op afstand
* Telemetrie in realtime weergeven
* SIM-gegevens weer geven

## <a name="telefnica-iot-integration-setup"></a>Setup van Telefónica IoT-integratie

### <a name="prerequisites"></a>Vereisten

Deze extra functie voor externe controle is momenteel beschikbaar als preview-versie. Voer de volgende stappen uit om uw verbindings gegevens te synchroniseren met de oplossing voor externe controle in Azure:

1. Vul een aanvraag op de [Telefónica's-site](https://iot.telefonica.com/contact)in, selecteer de optie **Azure externe controle**, inclusief uw contact gegevens.
2. Telefónica activeert uw account.
3. Als u nog geen Telefónica-client bent en u wilt profiteren van deze of andere IoT-connectiviteit Cloud Ready-Services, gaat u naar de [Telefónica's-site](https://iot.telefonica.com/) en selecteert u de optie **connectiviteit**.

### <a name="telefnica-sim-setup"></a>Telefónica SIM instellen
De Telefónica-SIMKAART & Azure-ID-koppeling voor het dubbele apparaat is gebaseerd op de eigenschap alias van Telefónica IoT SIM. 

Ga naar de [Telefónica IOT-connectiviteits platform Portal](https://m2m-movistar-es.telefonica.com/) > SIM-inventaris > Selecteer uw SIM en werk elke SIM-alias bij met de gewenste dubbele DeviceID. Deze taak kan ook in bulk modus worden uitgevoerd (Raadpleeg de gebruikers handleidingen van het Telefónica IoT-connectiviteits platform).

Deze taak kan ook in bulk modus worden uitgevoerd (Raadpleeg de gebruikers handleidingen van het Telefónica IoT-connectiviteits platform)

![Telefónica-update](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Als u uw apparaat wilt verbinden met de externe bewaking, kunt u deze zelf studies volgen met [C](iot-accelerators-connecting-devices-linux.md) of [knoop punt](iot-accelerators-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Eigenschappen van telemetrie en SIM van apparaten weer geven

Als uw Telefónica-account correct is geconfigureerd en uw apparaat is verbonden, kunt u de details van het apparaat en de SIM-gegevens bekijken.

De volgende connectiviteits parameters zijn gepubliceerd:

* ICCID
* IP
* Netwerk aanwezigheid
* Status van SIM
* Locatie op het netwerk
* Verbruikte gegevens verkeer

![Scherm afbeelding van het Device Explorer-venster in het Azure IoT-dash board voor externe controle. Er wordt een rij met WeatherStation apparaatgegevens gemarkeerd.](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht hebt van het integreren van SIM-gegevens in azure IoT-externe controle, vindt u hier een voorgestelde volgende stappen voor oplossingen Accelerators:

* [De Azure IoT-oplossing voor externe controle uitvoeren](quickstart-remote-monitoring-deploy.md)
* [Geavanceerde bewaking uitvoeren](iot-accelerators-remote-monitoring-monitor.md)
* [Uw apparaten beheren](iot-accelerators-remote-monitoring-manage.md)
* [Problemen met het apparaat oplossen](iot-accelerators-remote-monitoring-maintain.md)

