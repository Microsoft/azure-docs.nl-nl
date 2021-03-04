---
title: Aanbevolen procedures voor het ontwikkelen van apparaten in azure IoT Central | Microsoft Docs
description: In dit artikel vindt u een overzicht van de aanbevolen procedures voor de connectiviteit van apparaten in azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 03/03/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
ms.openlocfilehash: e8ae8b0173e53c0a46ded1a2690175e367997c9f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054562"
---
# <a name="best-practices-for-device-development"></a>Aanbevolen procedures voor het ontwikkelen van apparaten

*Dit artikel is bedoeld voor ontwikkelaars van apparaten.*

Deze aanbevelingen laten zien hoe u apparaten implementeert om te profiteren van de ingebouwde herstel na nood gevallen en automatisch schalen in IoT Central.

In de volgende lijst wordt de stroom op hoog niveau weer gegeven wanneer een apparaat verbinding maakt met IoT Central:

1. Gebruik DPS om het apparaat in te richten en een apparaat connection string op te halen.

1. Gebruik de connection string om verbinding te maken met het interne IoT Hub-eind punt van IoT Central. Gegevens verzenden naar en ontvangen van uw IoT Central-toepassing.

1. Als het apparaat verbindings fouten krijgt, moet u, afhankelijk van het type fout, de verbinding opnieuw proberen of het apparaat opnieuw inrichten.

## <a name="use-dps-to-provision-the-device"></a>DPS gebruiken om het apparaat in te richten

Als u een apparaat wilt inrichten met DPS, gebruikt u de scope-ID, de referenties en de apparaat-ID van uw IoT Central-toepassing. Zie voor meer informatie over de referentie typen [X. 509 groep registratie](concepts-get-connected.md#x509-group-enrollment) en [SAS-groeps inschrijving](concepts-get-connected.md#sas-group-enrollment). Zie [apparaatregistratie](concepts-get-connected.md#device-registration)voor meer informatie over apparaat-id's.

Bij voltooiing retourneert DPS een connection string het apparaat kan gebruiken om verbinding te maken met uw IoT Central-toepassing. Zie [de inrichtings status van uw apparaat controleren om de](troubleshoot-connection.md#check-the-provisioning-status-of-your-device)inrichtings fouten op te lossen.

Het apparaat kan de connection string in de cache opslaan voor gebruik in latere verbindingen. Het apparaat moet echter worden voor bereid voor het [afhandelen van verbindings fouten](#handle-connection-failures).

## <a name="connect-to-iot-central"></a>Verbinding maken met IoT Central

Gebruik de connection string om verbinding te maken met het interne IoT Hub-eind punt van IoT Central. Met de verbinding kunt u telemetrie naar uw IoT Central-toepassing verzenden, eigenschaps waarden synchroniseren met uw IoT Central toepassing en reageren op opdrachten die door uw IoT Central-toepassing worden verzonden.

## <a name="handle-connection-failures"></a>Verbindings fouten verwerken

Voor schalen of herstel na nood gevallen kan IoT Central de onderliggende IoT-hub bijwerken. Als u de connectiviteit wilt behouden, moet uw apparaatcode specifieke verbindings fouten verwerken door verbinding te maken met het nieuwe IoT Hub-eind punt.

Als het apparaat een van de volgende fouten ontvangt wanneer de verbinding tot stand wordt gebracht, wordt de inrichtings stap opnieuw met DPS opnieuw uitvoeren om een nieuwe connection string te krijgen. Deze fouten betekenen de connection string het apparaat niet meer geldig is:

- Onbereikbaar IoT Hub-eind punt.
- Verlopen beveiligings token.
- Het apparaat is uitgeschakeld in IoT Hub.

Als het apparaat een van de volgende fouten ontvangt wanneer er verbinding mee wordt gemaakt, moet het een back-up-strategie gebruiken om de verbinding opnieuw te proberen. Deze fouten betekenen de connection string het apparaat nog steeds geldig is, maar de tijdelijke omstandigheden stoppen het apparaat om verbinding te maken:

- Door operator geblokkeerd apparaat.
- Interne fout 500 van de service.

Zie [problemen met apparaattoewijzing oplossen](troubleshoot-connection.md)voor meer informatie over fout codes voor apparaten.

## <a name="next-steps"></a>Volgende stappen

Als u een ontwikkelaar van een apparaat bent, kunt u het volgende doen:

- Bekijk een voor beeld van een code die laat zien hoe u SAS-tokens gebruikt in [de zelf studie: een client toepassing maken en verbinden met uw Azure IOT Central-toepassing](tutorial-connect-device.md)
- Meer informatie over het [verbinden van apparaten met X. 509-certificaten met behulp van Node.js apparaat-SDK voor IOT Central toepassing](how-to-connect-devices-x509.md)
- Meer informatie over het [controleren van de connectiviteit van apparaten met behulp van Azure cli](./howto-monitor-devices-azure-cli.md)
- Meer informatie over het [definiëren van een nieuw IOT-apparaattype in uw Azure IOT Central-toepassing](./howto-set-up-template.md)
- Meer informatie over [Azure IOT edge apparaten en Azure IOT Central](./concepts-iot-edge.md)
