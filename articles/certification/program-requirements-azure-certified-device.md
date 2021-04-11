---
title: Vereisten voor Azure Certified Device
description: Vereisten voor het Azure Certified Device-programma
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Azure Certified Device Certification Requirements
ms.service: certification
ms.openlocfilehash: 948fe25da8468e887693fe8c9f75f675dfbea858
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969186"
---
# <a name="azure-certified-device-requirements"></a>Vereisten voor Azure Certified Device 
(voorheen bekend als IoT Hub)

Dit document bevat een overzicht van de apparaatspecifieke mogelijkheden die worden weer gegeven in de Azure Certified Device Catalog. Een mogelijkheid is een enkel kenmerk van het apparaat dat software-implementatie of combi natie van software-en hardware-implementaties kan zijn. 

## <a name="program-purpose"></a>Programma doel

Micro soft vereenvoudigt IoT en de certificering van Azure Certified-apparaten is basis certificerings programma om ervoor te zorgen dat alle apparaattypen veilig IoT Hub worden ingericht.

Belofte van certificering van Azure Certified-apparaten:

1. Het apparaat ondersteunt telemetrie die werkt met IoT Hub
2.  Ondersteunings IoT Hub Device Provisioning Service voor apparaten (DPS) voor een veilige inrichting van Azure IoT Hub
3.  Het apparaat ondersteunt eenvoudige invoer van de scope-overdracht van de doel-DPS-ID zonder dat de gebruiker Inge sloten code opnieuw moet compileren.
4.  Verifieer eventueel andere elementen, zoals Cloud-naar-apparaat-berichten, directe methoden en dubbele apparaten 

## <a name="requirements"></a>Vereisten

**Lang Apparaat naar Cloud: het doel van de test is om ervoor te zorgen dat apparaten die telemetrie gebruiken met IoT Hub worden verzonden**

| **Naam**                | AzureCertified. D2C                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Beschik baarheid doel** | Nu verkrijgbaar                                                |
| **Van toepassing op**          | Leaf-apparaat/rand apparaat                                      |
| **Besturingssysteem**                  | Agnostisch                                                     |
| **Validatie type**     | Geautomatiseerd                                                    |
| **Validatie**          | Het apparaat moet een telemetrie-schema naar IoT Hub verzenden. Micro soft biedt de [Portal werk stroom](https://certify.azure.come) voor het uitvoeren van de tests. Apparaat naar Cloud (vereist): **1.** Hiermee wordt gevalideerd of het apparaat een bericht kan verzenden naar AICS beheerd IoT Hub **2.** De gebruiker moet het aantal en de frequentie van berichten opgeven. **3.** AICS valideert of de telemetrie is ontvangen door het hub-exemplaar |
| **Bronnen**           | [Certificerings stappen](./overview.md) (heeft alle aanvullende bronnen) |

**Lang DPS: het doel van de test is om te controleren of het apparaat wordt geïmplementeerd en ondersteunt IoT Hub Device Provisioning Service met een van de drie Attestation-methoden**

| **Naam**                | AzureCertified. DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Beschik baarheid doel** | Nieuw                                                          |
| **Van toepassing op**          | Elk apparaat                                                   |
| **Besturingssysteem**                  | Agnostisch                                                     |
| **Validatie type**     | Geautomatiseerd                                                    |
| **Validatie**          | Het apparaat ondersteunt een eenvoudige invoer van het eigendom van het doel voor de DPS-ID-Scope zonder dat de Inge sloten code opnieuw moet worden gecompileerd. Micro soft biedt de [Portal werk stroom](https://certify.azure.com) voor het uitvoeren van de tests om te valideren dat het apparaat DPS **1 ondersteunt.** De gebruiker moet een van de Attestation-methoden (X. 509, TPM en SAS-sleutel) **2 selecteren.** Afhankelijk van de Attestation-methode moet de gebruiker overeenkomstige actie ondernemen, zoals **een)** upload X. 509-certificaat naar AICS beheerd DPS **-Scope b)** Implementeer SAS-sleutel of goedkeurings sleutel in het apparaat |
| **Bronnen**           | [Overzicht van Device Provisioning Service](../iot-dps/about-iot-dps.md) |

**[Indien geïmplementeerd] Cloud naar apparaat: het doel van de test is te controleren of berichten vanuit de Cloud naar apparaten kunnen worden verzonden**                                                              

| **Naam**                | AzureCertified. C2D                                                  |
| ----------------------- | ------------------------------------------------------------ |
| **Beschik baarheid doel** | Nu verkrijgbaar                                            |
| **Van toepassing op**          | Leaf-apparaat/rand apparaat                                                   |
| **Besturingssysteem**                  | Agnostisch                                                     |
| **Validatie type**     | Geautomatiseerd                                                    |
| **Validatie**          | Het apparaat moet in staat zijn om berichten van IoT Hub in de cloud te laten staan. Micro soft biedt de [werk stroom](https://certify.azure.com) van de portal om deze tests uit te voeren. Cloud naar apparaat (indien geïmplementeerd): **1.** Hiermee wordt gecontroleerd of het apparaat bericht kan ontvangen van IoT Hub **2.** AICS verzendt wille keurig bericht en valideert dit via bericht ACK van het apparaat  |
| **Bronnen**           | **a) de** [certificerings stappen](./overview.md) (alle aanvullende bronnen) **b)** [een cloud naar een apparaat verzenden vanuit een IOT hub](../iot-hub/iot-hub-devguide-messages-c2d.md) |

**[Indien geïmplementeerd] Directe methoden: het doel van de test is om ervoor te zorgen dat apparaten met IoT Hub werken en rechtstreekse methoden ondersteunen**

| **Naam**                | AzureCertified.DirectMethods                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Beschik baarheid doel** | Nu verkrijgbaar                                            |
| **Van toepassing op**          | Leaf-apparaat/rand apparaat                                                   |
| **Besturingssysteem**                  | Agnostisch                                                     |
| **Validatie type**     | Geautomatiseerd                                                    |
| **Validatie**          | Het apparaat moet opdrachten aanvragen van IoT Hub kunnen ontvangen en beantwoorden. Micro soft biedt de [Portal werk stroom](https://certify.azure.com) voor het uitvoeren van de tests. Directe methoden (indien geïmplementeerd) **1.** De gebruiker moet de methode lading van de directe methode opgeven. **2.** AICS valideert of de opgegeven Payload-aanvraag wordt verzonden vanuit de hub en het bevestigings bericht dat is ontvangen door het apparaat |
| **Bronnen**           | **a) de** [certificerings stappen](./overview.md) (alle aanvullende bronnen) **b)** [begrijpen directe methoden van IOT hub](../iot-hub/iot-hub-devguide-direct-methods.md) |

**[Indien geïmplementeerd] Device dubbele eigenschap: het doel van de test is om ervoor te zorgen dat apparaten die telemetrie gebruiken met IoT Hub, een aantal van de IoT Hub mogelijkheden ondersteunt, zoals directe methoden en een dubbele eigenschap van het apparaat**

| **Naam**                                  | AzureCertified.DeviceTwin                                      |
| ----------------------------------------- | ------------------------------------------------------------ |
| **Beschik baarheid doel**                   | Nu verkrijgbaar                                            |
| **Van toepassing op**                            | Leaf-apparaat/rand apparaat                                                   |
| **Besturingssysteem**                                    | Agnostisch                                                     |
| **Validatie type**                       | Geautomatiseerd                                                       |
| **Validatie**                            | Het apparaat moet een telemetrie-schema naar IoT Hub verzenden. Micro soft biedt de [Portal werk stroom](https://certify.azure.com) voor het uitvoeren van de tests. Dubbele eigenschap van apparaat (indien geïmplementeerd) **1.** AICS valideert de eigenschap lezen/schrijven in het apparaat dubbele JSON **2.** Gebruiker moet de JSON-nettolading opgeven die moet worden gewijzigd **3.** AICS valideert de opgegeven gewenste eigenschappen van IoT Hub en ACK-bericht dat is ontvangen door het apparaat |
| **Bronnen**                             | **a) de** [certificerings stappen](./overview.md) (alle aanvullende bronnen) **b)** [apparaatdubbels apparaat gebruiken met IOT hub](../iot-hub/iot-hub-devguide-device-twins.md) |