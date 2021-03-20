---
title: Problemen met Azure IoT Hub 500xxx interne fouten oplossen
description: Meer informatie over het oplossen van 500xxx interne fouten
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1333a135f3e123757e268513f73e8329537e630b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92538201"
---
# <a name="500xxx-internal-errors"></a>500xxx Internal errors

In dit artikel worden de oorzaken en oplossingen voor **500Xxx interne fouten** beschreven.

## <a name="symptoms"></a>Symptomen

Uw aanvraag voor IoT Hub mislukt met een fout die begint met 500 en/of een sortering van ' server fout '. Enkele mogelijkheden zijn:

* **500001 server error**: er is IOT hub een probleem aan de server zijde opgetreden.

* **500008 GenericTimeout**: IOT hub kan de verbindings aanvraag niet volt ooien voordat de time-out is opgelopen.

* **ServiceUnavailable (geen fout code)**: er is een interne fout opgetreden in IOT hub.

* **InternalServerError (geen fout code)**: er is een interne fout opgetreden in IOT hub.

## <a name="cause"></a>Oorzaak

Er kunnen een aantal oorzaken zijn voor een 500xxx-fout melding. In alle gevallen is het probleem waarschijnlijk tijdelijk. Hoewel het IoT Hub-team moeilijk werkt om [de sla](https://azure.microsoft.com/support/legal/sla/iot-hub/)te onderhouden, kunnen kleine subsets van IOT hub knoop punten af en toe tijdelijke fouten ondervinden. Wanneer het apparaat probeert verbinding te maken met een knoop punt dat problemen ondervindt, wordt deze fout weer gegeven.

## <a name="solution"></a>Oplossing

Als u 500xxx-fouten wilt verhelpen, geeft u een nieuwe poging vanaf het apparaat. Als u [nieuwe pogingen automatisch wilt beheren](./iot-hub-reliability-features-in-sdks.md#connection-and-retry), moet u ervoor zorgen dat u de meest recente versie van de [Azure IOT sdk's](./iot-hub-devguide-sdks.md)gebruikt. Zie [tijdelijke fout afhandeling](/azure/architecture/best-practices/transient-faults)voor Best Practice van tijdelijke fout afhandeling en nieuwe pogingen.  Als het probleem zich blijft voordoen, controleert u [resource Health](./iot-hub-azure-service-health-integration.md#check-health-of-an-iot-hub-with-azure-resource-health) en de [status van Azure](https://status.azure.com/) om na te gaan of IOT hub een bekend probleem heeft. U kunt ook de [functie hand matige failover](./tutorial-manual-failover.md)gebruiken. Als er geen bekende problemen zijn en het probleem blijft bestaan, [neemt u contact op met de ondersteuning](https://azure.microsoft.com/support/options/) voor verdere onderzoek.
