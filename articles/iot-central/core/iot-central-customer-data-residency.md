---
title: Locatie van klant gegevens in azure IoT Central | Microsoft Docs
description: In dit artikel worden klant gegevens locatie in azure IoT Central-toepassingen beschreven.
author: dominicbetts
ms.author: dobett
ms.date: 11/02/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9cf803ddd5a3c9329cadefffc35ae6d6999323a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93280687"
---
# <a name="azure-iot-central-customer-data-residency"></a>Azure IoT Central klant gegevens locatie

Met IoT Central worden klant gegevens niet opgeslagen buiten de opgegeven geografische locatie van de klant, met uitzonde ring van de volgende scenario's:

- Wanneer een nieuwe gebruiker wordt toegevoegd aan een bestaande IoT Central toepassing, kan de e-mail-ID van de gebruiker buiten de geografie worden opgeslagen totdat de uitgenodigde gebruiker de toepassing voor de eerste keer opent.

- IoT Central dash board-kaart tegels gebruiken [Azure Maps](../../azure-maps/about-azure-maps.md). Wanneer u een kaart tegel aan een bestaande IoT Central-toepassing toevoegt, kunnen de locatie gegevens worden verwerkt of opgeslagen in overeenstemming met de geolocatie regels van de Azure Maps-service.
