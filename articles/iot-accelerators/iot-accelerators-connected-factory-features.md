---
title: Functies van de oplossing Connected Factory-Azure | Microsoft Docs
description: In dit artikel wordt een overzicht gegeven van de functies van de vooraf geconfigureerde oplossing Connected Factory, zoals Cloud dashboard, regels en waarschuwingen.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: e492ebf70f69c985691852a1c1f2351d1e09578e
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646125"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Wat is de verbonden Factory IoT-oplossings versneller?

> [!IMPORTANT]
> Zie [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) voor de meest recente inhoud terwijl we dit artikel bijwerken.

Connected Factory is een implementatie van de Azure Industrial IoT-referentie architectuur van micro soft, verpakt als een open-source oplossing. U kunt deze gebruiken als uitgangs punt voor een commercieel product. U kunt een vooraf ontwikkelde versie van de oplossing Connected Factory implementeren in uw Azure-abonnement van [Azure IOT-oplossings Accelerators](https://www.azureiotsolutions.com/#solutions/types/CF).

![Dash board Connected Factory-oplossing](./media/iot-accelerators-connected-factory-features/dashboard.png)

De Connected Factory Solution Accelerator [code is beschikbaar op github](https://github.com/Azure/azure-iot-connected-factory).

Verbonden Factory bevat de volgende functies:

## <a name="industrial-device-interoperability"></a>Interoperabiliteit van industrieel apparaat

- Maak verbinding met industriële assets met een OPC UA-interface.
- Gebruik de gesimuleerde productie lijnen (actieve OPC UA-servers in docker-containers) om live-telemetrie hiervan te bekijken.
- Blader door het OPC UA-informatie model van de OPC UA-servers vanuit een Cloud dashboard.

## <a name="remote-management"></a>Extern beheer

- Configureer uw OPC UA-assets vanuit het Cloud dashboard (oproep methoden, lezen en schrijven van gegevens).
- Telemetrie-gegevens uit uw OPC UA-assets publiceren en ongedaan maken vanuit een Cloud dashboard.

## <a name="cloud-dashboard"></a>Cloud dashboard

- Bekijk de voor beelden van telemetrie rechtstreeks in een Cloud dashboard.
- Bekijk trends in telemetriegegevens en maak correlaties met behulp van het dash board Time Series Insights Explorer.
- Zie berekende algemene efficiëntie van apparatuur (OEE) en Key Performance Indica tors (Kpi's) van een Cloud dashboard.
- Bekijk de hiërarchieën van industriële activa in een boom topologie en op een interactieve kaart.
- Waarschuwingen weer geven, erkennen en sluiten vanuit een Cloud dashboard.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure time series Insights](../time-series-insights/time-series-insights-overview.md) is gemaakt voor het opslaan, visualiseren en opvragen van grote hoeveel heden gegevens in de tijd reeks. Connected Factory maakt gebruik van deze service.
- Connected Factory integreert met deze service, zodat u de gegevens van uw apparaten dieper kunt analyseren.

## <a name="rules-and-alerts"></a>Regels en waarschuwingen

[Regels op basis van drempel waarden configureren voor waarschuwingen](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>End-to-end beveiliging

- Configureer beveiligings machtigingen voor gebruikers die gebruikmaken van op rollen gebaseerd toegangs beheer (RBAC).
- End-to-end-versleuteling wordt geïmplementeerd met behulp van de OPC UA-verificatie (met behulp van X. 509-certificaten) en beveiligings tokens.

## <a name="customizability"></a>Aanpassings mogelijkheden

- Pas de oplossing aan om te voldoen aan specifieke bedrijfs vereisten.
- Volledige bron code van de oplossing die beschikbaar is op GitHub. Zie de [vooraf geconfigureerde](https://github.com/Azure/azure-iot-connected-factory) opslag plaats voor oplossingen van de verbonden Factory.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over de Connected Factory Solution Accelerator raadpleegt u de Quick [Start een Cloud oplossing voor het beheren van mijn industriële IOT-apparaten](quickstart-connected-factory-deploy.md).
