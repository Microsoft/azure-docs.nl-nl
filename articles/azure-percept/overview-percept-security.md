---
title: Overzicht van Azure percept-beveiliging
description: Meer informatie over Azure percept-beveiliging
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 6a3049709c6c094f722a8132ee4c4b2051e24d95
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102616686"
---
# <a name="azure-percept-security-overview"></a>Overzicht van Azure percept-beveiliging

Azure percept DK-apparaten zijn ontworpen met een hardwarefabrikant: extra ingebouwde beveiliging op elk apparaat. Het helpt u bij het beveiligen van gevoelige Sens oren, zoals camera's en microfoons, het afhandelen van gegevens en het inschakelen van verificatie en autorisatie van apparaten voor Azure percept Studio Services.

> [!NOTE]
> Azure percept DK wordt alleen in licentie gegeven voor gebruik in ontwikkel-en test omgevingen.

## <a name="devices"></a>Apparaten

### <a name="azure-percept-dk"></a>Azure percept DK

Azure percept DK bevat een Trusted Platform Module (TPM) versie 2,0 die kan worden gebruikt om het apparaat te verbinden met Azure Device Provisioning Services met extra beveiliging. TPM is een branchespecifieke, ISO-standaard van de Trusted Computing Group en u kunt meer lezen over TPM bij de [volledige tpm 2,0 spec](https://trustedcomputinggroup.org/resource/tpm-library-specification/) of de ISO/IEC 11889 spec. Zie [Azure IOT hub Device Provisioning Service-TPM-Attestation (Engelstalig)](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation)voor meer informatie over hoe DPS apparaten op een veilige manier kan inrichten.

### <a name="azure-percept-system-on-module-som"></a>Azure percept System on module (SOM)

Azure percept DK-systeem op module (SOM) en het percept audio-accessoire SOM bevatten beide een micro controller-eenheid (MCU) voor het beveiligen van de toegang tot de Inge sloten AI-Sens oren. Bij elke keer dat de MCU-firmware wordt gebruikt, wordt de AI-Accelerator geverifieerd en geautoriseerd met Azure percept Studio Services met behulp van de architectuur van de apparaat-id compositie engine (dobbel stenen). Dobbel stenen worden gesplitst in lagen en maken geheimen uniek voor elke laag en configuratie op basis van een uniek apparaat geheim (UDS). Als er op elk moment in de keten een andere code of configuratie wordt opgestart, zijn de geheimen verschillend. Meer informatie over de dobbel stenen vindt u in de specificaties van de [dobbel stenen](https://trustedcomputinggroup.org/work-groups/dice-architectures/). Zie voor meer informatie over het configureren van toegang tot Azure percept Studio en de vereiste services de volgende **firewalls configureren voor Azure percept** .

Azure percept-apparaten gebruiken het vertrouwen van de hoofdmap van de hardware om firmware te beveiligen. De opstart-ROM zorgt voor de integriteit van de firmware tussen de lader van het ROM-station en het besturings systeem, die op zijn beurt de integriteit van de andere software onderdelen die een vertrouwens keten maken, garandeert.

## <a name="services"></a>Services

### <a name="iot-edge"></a>IoT Edge

Azure percept DK maakt verbinding met Azure percept Studio met extra beveiliging en andere Azure-Services die gebruikmaken van Transport Layer Security (TLS)-protocol. Azure percept DK is een Azure IoT Edge ingeschakeld apparaat. IoT Edge runtime is een verzameling Program ma's die een apparaat in een IoT Edge apparaat omzetten. De IoT Edge-runtime-onderdelen maken het samen IoT Edge apparaten de mogelijkheid code te ontvangen die aan de rand worden uitgevoerd en de resultaten te communiceren. Azure percept DK maakt gebruik van docker-containers voor het isoleren van IoT Edge workloads van het hostbesturingssysteem en de Edge-toepassingen. Meer informatie over het Azure IoT Edge Security Framework vindt u in de [IOT Edge Security Manager](https://docs.microsoft.com/azure/iot-edge/iot-edge-security-manager).

### <a name="device-update-for-iot-hub"></a>Update van het apparaat voor IoT Hub

Update van het apparaat voor IoT Hub zorgt voor meer beveiliging, schaal baarheid en betrouw bare over-the-Air update waarmee de beveiliging van Azure percept-apparaten wordt verlengd. Het biedt uitgebreide beheer controles en update compatibiliteit via inzichten. Azure percept DK bevat een vooraf geïntegreerde oplossing voor het bijwerken van het apparaat, die een flexibele update (A/B) levert van firmware tot besturingssysteem lagen.

<!---I think the below topics need to be somewhere else, (i.e. not on the main page)
--->

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Firewalls configureren voor Azure percept DK

Als uw netwerk installatie vereist dat u verbindingen die zijn gemaakt met Azure percept DK-apparaten expliciet toestaat, raadpleegt u de volgende lijst met onderdelen.

Deze controle lijst is een start punt voor firewall regels:

|URL (* = Joker teken) |Uitgaande TCP-poorten|    Gebruik|
|-------------------|------------------|---------|
|*. auth.azureperceptdk.azure.net|   443|    SOM van Azure DK-verificatie en-autorisatie|
|*. auth.projectsantacruz.azure.net| 443|    SOM van Azure DK-verificatie en-autorisatie|

Bekijk ook de lijst met [verbindingen die door Azure IOT Edge worden gebruikt](https://docs.microsoft.com/azure/iot-edge/production-checklist#allow-connections-from-iot-edge-devices).

<!---
## Additional Recommendations for Deployment to Production

Azure Percept DK offers a great variety of security capabilities out of the box. In addition to those powerful security features included in the current release, Microsoft also suggests the following guidelines when considering production deployments:

- Strong physical protection of the device itself
- Ensuring data at rest encryption is enabled
- Continuously monitoring the device posture and quickly responding to alerts
- Limiting the number of administrators who have access to the device
--->


## <a name="next-steps"></a>Volgende stappen

Meer informatie over de beschik bare [Azure PERCEPT AI-modellen](./overview-ai-models.md).
