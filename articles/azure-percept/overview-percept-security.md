---
title: Overzicht van Azure percept-beveiliging
description: Meer informatie over Azure percept-beveiliging
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: template-concept
ms.openlocfilehash: 93884fb87f87651054ffff0a04c4910de634a5eb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567641"
---
# <a name="azure-percept-security-overview"></a>Overzicht van Azure percept-beveiliging

Azure percept-apparaten zijn ontworpen met de hardware-basis van vertrouwen. Deze ingebouwde beveiliging helpt bij het beveiligen van de inschakeling van gegevens en privacy gevoelige Sens oren, zoals camera's en microfoons, en schakelt verificatie en autorisatie van apparaten in voor Azure percept Studio Services.

> [!NOTE]
> Azure percept DK wordt alleen in licentie gegeven voor gebruik in ontwikkel-en test omgevingen.

## <a name="devices"></a>Apparaten

### <a name="azure-percept-dk"></a>Azure percept DK

Azure percept DK bevat een Trusted Platform Module (TPM) versie 2,0, die kan worden gebruikt om het apparaat te verbinden met Azure Device Provisioning Services (DPS) met extra beveiliging. TPM is een branchespecifieke, ISO-standaard van de Trusted Computing Group. Bekijk de [Trusted Computing Group-website](https://trustedcomputinggroup.org/resource/tpm-library-specification/) voor meer informatie over de volledige TPM 2,0 spec of de ISO/IEC 11889 spec. Zie [Azure IOT hub Device Provisioning Service-TPM-Attestation](../iot-dps/concepts-tpm-attestation.md)(Engelstalig) voor meer informatie over hoe DPS apparaten op een veilige manier kan inrichten.

### <a name="azure-percept-system-on-modules-soms"></a>Azure percept System-on-modules (SoMs)

Het Azure percept Vision System-on-module (SoM) en het percept-audio-SoM bevatten beide een microcontroller-eenheid (MCU) voor het beveiligen van de toegang tot de Inge sloten AI-Sens oren. Bij elke keer dat de MCU-firmware wordt gebruikt, wordt de AI-Accelerator geverifieerd en geautoriseerd met Azure percept Studio Services met behulp van de architectuur van de apparaat-id compositie engine (dobbel stenen). Dobbel stenen werken door het opstarten in lagen op te splitsen en unieke apparaat-geheimen (UDS) te maken voor elke laag en configuratie. Als verschillende code of configuratie op een wille keurig punt in de keten wordt opgestart, verschillen de geheimen. Meer informatie over de dobbel stenen vindt u in de specificaties van de [dobbel stenen](https://trustedcomputinggroup.org/work-groups/dice-architectures/). Zie het artikel over het [configureren van firewalls voor Azure PERCEPT DK](concept-security-configuration.md)voor meer informatie over het configureren van toegang tot Azure percept Studio en de vereiste services.

Azure percept-apparaten gebruiken de hardwarefabrikant om firmware te beveiligen. De opstart-ROM zorgt voor de integriteit van de firmware tussen het laad programma van de ROM en het besturings systeem (OS), dat op zijn beurt de integriteit van de andere software onderdelen garandeert, waardoor er een vertrouwens keten wordt gemaakt.

## <a name="services"></a>Services

### <a name="iot-edge"></a>IoT Edge

Azure percept DK maakt verbinding met Azure percept Studio met extra beveiliging en andere Azure-Services die gebruikmaken van Transport Layer Security (TLS)-protocol. Azure percept DK is een Azure IoT Edge apparaat. IoT Edge runtime is een verzameling Program ma's die een apparaat in een IoT Edge apparaat omzetten. De IoT Edge-runtime-onderdelen maken het samen IoT Edge apparaten de mogelijkheid code te ontvangen die aan de rand worden uitgevoerd en de resultaten te communiceren. Azure percept DK maakt gebruik van docker-containers voor het isoleren van IoT Edge workloads van het hostbesturingssysteem en toepassingen met rand mogelijkheden. Meer informatie over het Azure IoT Edge Security Framework vindt u in de [IOT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

### <a name="device-update-for-iot-hub"></a>Update van het apparaat voor IoT Hub

Update van het apparaat voor IoT Hub zorgt voor meer beveiliging, schaal baarheid en betrouw bare over-the-Air update waarmee de beveiliging van Azure percept-apparaten wordt verlengd. Het biedt uitgebreide beheer controles en update compatibiliteit via inzichten. Azure percept DK bevat een vooraf geïntegreerde oplossing voor het bijwerken van het apparaat, die een flexibele update (A/B) levert van firmware tot besturingssysteem lagen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Firewall configuraties en aanbevelingen voor beveiliging](concept-security-configuration.md)

> [!div class="nextstepaction"]
> [Een Azure percept DK kopen via de online winkel van micro soft](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
