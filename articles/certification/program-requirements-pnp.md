---
title: Vereisten voor IoT Plug en Play-certificering
description: Vereisten voor IoT Plug en Play-certificerings programma
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: IoT Plug and Play Certification Requirements
ms.service: certification
ms.openlocfilehash: b26fab6f8b92e3cb996f545f1f6201d32b1eaced
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310509"
---
# <a name="iot-plug-and-play-certification-requirements"></a>Vereisten voor IoT Plug en Play-certificering

Dit document bevat een overzicht van de specifieke mogelijkheden van het apparaat dat wordt weer gegeven in de Azure IoT-apparaatinstantie. Een mogelijkheid is een enkel kenmerk van het apparaat dat software-implementatie of combi natie van software-en hardware-implementaties kan zijn.

## <a name="program-purpose"></a>Programma doel

Met IoT Plug en Play Preview kunnen ontwikkelaars slimme apparaten in hun oplossingen integreren zonder handmatige configuratie. De kern van IoT Plug en Play omvat een apparaatmodel dat kan worden gebruikt voor een apparaat om de mogelijkheden van het apparaat aan te kondigen bij een IoT Plug en Play-toepassing. Dit model is gestructureerd als een set elementen: telemetrie, eigenschappen en opdrachten.

Promise van IoT Plug en Play-certificering zijn:

1.  Gedefinieerde modellen en interfaces voor apparaten zijn compatibel met de  [Digital-taal voor dubbele definitie](https://github.com/Azure/opendigitaltwins-dtdl)  
2.  Veilige inrichting en eenvoudige overdracht van eigendom van ID-bereik in Device Provisioning Services
3.  Eenvoudige integratie met Azure IoT-oplossingen met behulp van de [digitale dubbele api's](../iot-pnp/concepts-digital-twin.md)  : Azure IOT hub en Azure IOT Central
4.  Gevalideerde product waarheid op gecertificeerde apparaten

## <a name="requirements"></a>Vereisten

**Lang Apparaat naar Cloud: het doel van de test is om ervoor te zorgen dat apparaten die telemetrie gebruiken met IoT Hub worden verzonden**

| **Naam**                | IoTPnP. D2C                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Beschik baarheid doel** | Nu verkrijgbaar                                                |
| **Van toepassing op**          | Leaf-apparaat/rand apparaat                                      |
| **Besturingssysteem**                  | Agnostisch                                                     |
| **Validatie type**     | Geautomatiseerd                                                    |
| **Validatie**          | Het apparaat moet een telemetrie-schema naar IoT Hub verzenden. Micro soft biedt de [Portal werk stroom](https://certify.azure.com) voor het uitvoeren van de tests. Apparaat naar Cloud (vereist): **1.** Hiermee wordt gevalideerd of het apparaat een bericht kan verzenden naar AICS beheerd IoT Hub **2.** De gebruiker moet het aantal en de frequentie van berichten opgeven. **3.** AICS valideert of de telemetrie is ontvangen door het hub-exemplaar |
| **Bronnen**           | [Certificerings stappen](./overview.md) (heeft alle aanvullende bronnen) |

**Lang DPS: het doel van de test is om te controleren of het apparaat wordt geïmplementeerd en ondersteunt IoT Hub Device Provisioning Service met een van de drie Attestation-methoden**

| **Naam**                | IoTPnP. DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Beschik baarheid doel** | Nu verkrijgbaar                                                |
| **Van toepassing op**          | Elk apparaat                                                   |
| **Besturingssysteem**                  | Agnostisch                                                     |
| **Validatie type**     | Geautomatiseerd                                                    |
| **Validatie**          | Het apparaat moet een eenvoudige overdracht van de identiteit van het DPS-ID-bereik implementeren zonder dat de Inge sloten code opnieuw moet worden gecompileerd. Micro soft biedt de [Portal werk stroom](https://certify.azure.com) voor het uitvoeren van de tests om te valideren dat het apparaat DPS **1 ondersteunt.** De gebruiker moet een van de Attestation-methoden (X. 509, TPM en SAS-sleutel) **2 selecteren.** Afhankelijk van de Attestation-methode moet de gebruiker overeenkomstige actie ondernemen, zoals **een)** upload X. 509-certificaat naar AICS beheerd DPS **-Scope b)** Implementeer SAS-sleutel of goedkeurings sleutel in het apparaat |
| **Bronnen**           | **a)** [Device Provisioning Service-overzicht](../iot-dps/about-iot-dps.md), **b)** [voor beeld van een configuratie bestand voor DPS id-overdracht](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview-pnp/serializer/samples/devicetwin_simplesample) |

**Lang DTDL v2: het doel van de test om te controleren of gedefinieerde modellen en interfaces voor het apparaat compatibel zijn met de Digital Apparaatdubbels Definition Language v2.**                                                              

| **Naam**                | IoTPnP.DTDL                                                  |
| ----------------------- | ------------------------------------------------------------ |
| **Beschik baarheid doel** | Nu verkrijgbaar                                                |
| **Van toepassing op**          | Elk apparaat                                                   |
| **Besturingssysteem**                  | Agnostisch                                                     |
| **Validatie type**     | Geautomatiseerd                                                    |
| **Validatie**          | De [werk stroom](https://certify.azure.com) van de portal valideert: **1.** De model-ID-aankondiging en zorg ervoor dat het apparaat is verbonden met behulp van de MQTT-of MQTT via websockets protocol **2.** Modellen zijn compatibel met de DTDL v2 **3.** Telemetrie, eigenschappen en opdrachten zijn op de juiste wijze geïmplementeerd en werken tussen IoT Hub digitale en dubbele apparaten op het apparaat |
| **Bronnen**           | [Updates voor open bare Preview-versies vernieuwen](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |

**Lang Modellen voor apparaten worden gepubliceerd in de open bare model opslagplaats**

| **Naam**                | IoTPnP.ModelRepo                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Beschik baarheid doel** | Nu verkrijgbaar                                                |
| **Van toepassing op**          | Elk apparaat                                                   |
| **Besturingssysteem**                  | Agnostisch                                                     |
| **Validatie type**     | Geautomatiseerd                                                    |
| **Validatie**          | Alle apparaatprofielen moeten worden gepubliceerd in de open bare opslag plaats. Modellen voor apparaten worden omgezet via modellen die beschikbaar zijn in de open bare opslag plaats **1.** De gebruiker moet de modellen hand matig publiceren naar de open bare opslag plaats voordat deze wordt verzonden voor de certificering. **2.** Houd er rekening mee dat het publiceren van de modellen onveranderbaar is. We raden u ten zeerste aan om alleen te publiceren wanneer de modellen en de code van het Inge sloten apparaat zijn voltooid. * 1 * 1 gebruiker moet contact opnemen met micro soft ondersteuning om de modellen in te trekken bij de publicatie van de model opslagplaats **3.** Met de [Portal werk stroom](https://certify.azure.com) wordt gecontroleerd of de modellen aanwezig zijn in de open bare opslag plaats wanneer het apparaat is verbonden met de certificerings service |
| **Bronnen**           | [Modelopslagplaats](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |

**Lang Validatie van het fysieke apparaat met behulp van de GSG**

| **Naam**                                  | IoTPnP.Physicaldevice                                      |
| ----------------------------------------- | ------------------------------------------------------------ |
| **Beschik baarheid doel**                   | Nu verkrijgbaar                                                |
| **Van toepassing op**                            | Elk apparaat                                                   |
| **Besturingssysteem**                                    | Agnostisch                                                     |
| **Validatie type**                       | Handmatig                                                       |
| **Validatie**                            | Partners moeten samen werken met micro soft contact ( [iotcert@microsoft.com](mailto:iotcert@microsoft.com) ) om afspraken te maken voor het uitvoeren van extra validaties op het fysieke apparaat. Vanwege COVID-19-situatie verkennen we verschillende manieren om de validatie van het fysieke apparaat uit te voeren zonder het apparaat naar micro soft te verzenden. |
| **Bronnen**                             | Details zijn later beschikbaar                                 |
| **Aanbevolen door Azure**       | N.v.t.    |

**[Indien geïmplementeerd] Interface voor apparaatgegevens: het doel van de test is het valideren van de interface voor apparaatgegevens is op de juiste wijze geïmplementeerd in de apparaatcode**

| **Naam**                | IoTPnP.DeviceInfoInterface                                   |
| ----------------------- | ------------------------------------------------------------ |
| **Beschik baarheid doel** | Nu verkrijgbaar                                                |
| **Van toepassing op**          | Elk apparaat                                                   |
| **Besturingssysteem**                  | Agnostisch                                                     |
| **Validatie type**     | Geautomatiseerd                                                    |
| **Validatie**          | Met de [Portal werk stroom](https://certify.azure.com) wordt gecontroleerd of de apparaatcode de apparaatgegevens Interface 1 implementeert **.** Controleert of de waarden worden verzonden door de apparaatcode naar IoT Hub **2.** Controleert of de interface is geïmplementeerd in de DCM (deze implementatie wordt gewijzigd in DTDL v2) **3.** De controle-eigenschappen kunnen niet worden geschreven (alleen-lezen) **4.** Hiermee wordt gecontroleerd of het schema type teken reeks is en/of lang en niet Null |
| **Bronnen**           | [Door micro soft gedefinieerde interface](../iot-pnp/overview-iot-plug-and-play-preview-updates.md) |
| **Aanbevolen door Azure**  | N.v.t.                                                          |

**[Indien geïmplementeerd] Cloud naar apparaat: het doel van de test is te controleren of berichten vanuit de Cloud naar apparaten kunnen worden verzonden**

| **Naam**                | IoTPnP. C2D                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Beschik baarheid doel** | Nu verkrijgbaar                                                |
| **Van toepassing op**          | Leaf-apparaat/rand apparaat                                      |
| **Besturingssysteem**                  | Agnostisch                                                     |
| **Validatie type**     | Geautomatiseerd                                                    |
| **Validatie**          | Het apparaat moet in staat zijn om berichten van IoT Hub in de cloud te laten staan. Micro soft biedt de [werk stroom](https://certify.azure.com) van de portal om deze tests uit te voeren. Cloud naar apparaat (indien geïmplementeerd): **1.** Hiermee wordt gecontroleerd of het apparaat bericht kan ontvangen van IoT Hub **2.** AICS verzendt wille keurig bericht en valideert dit via bericht ACK van het apparaat |
| **Bronnen**           | **1.** [certificerings stappen](./overview.md) (heeft alle extra resources), **2.** [Cloud naar apparaat berichten verzenden vanuit een IoT Hub](../iot-hub/iot-hub-devguide-messages-c2d.md) |

**[Indien geïmplementeerd] Directe methoden: het doel van de test is om ervoor te zorgen dat apparaten met IoT Hub werken en rechtstreekse methoden ondersteunen**

| **Naam**                | IoTPnP.DirectMethods                                     |
| ----------------------- | ------------------------------------------------------------ |
| **Beschik baarheid doel** | Nu verkrijgbaar                                                |
| **Van toepassing op**          | Leaf-apparaat/rand apparaat                                      |
| **Besturingssysteem**                  | Agnostisch                                                     |
| **Validatie type**     | Geautomatiseerd                                                    |
| **Validatie**          | Het apparaat moet opdrachten aanvragen van IoT Hub kunnen ontvangen en beantwoorden. Micro soft biedt de [Portal werk stroom](https://certify.azure.com) voor het uitvoeren van de tests. Directe methoden (indien geïmplementeerd): **1.** De gebruiker moet de methode lading van de directe methode opgeven. **2.** AICS valideert of de opgegeven Payload-aanvraag wordt verzonden vanuit de hub en het bevestigings bericht dat is ontvangen door het apparaat |
| **Bronnen**           | **1.** [certificerings stappen](./overview.md) (heeft alle extra resources), **2.** [Directe methoden van IoT Hub begrijpen](../iot-hub/iot-hub-devguide-direct-methods.md) |

**[Indien geïmplementeerd] Device dubbele eigenschap: het doel van de test is om ervoor te zorgen dat apparaten die telemetrie gebruiken met IoT Hub, een aantal van de IoT Hub mogelijkheden ondersteunt, zoals directe methoden en een dubbele eigenschap van het apparaat**

| **Naam**                | IoTPnP.DeviceTwin                                        |
| ----------------------- | ------------------------------------------------------------ |
| **Beschik baarheid doel** | Nu verkrijgbaar                                                |
| **Van toepassing op**          | Leaf-apparaat/rand apparaat                                      |
| **Besturingssysteem**                  | Agnostisch                                                     |
| **Validatie type**     | Geautomatiseerd                                                    |
| **Validatie**          | Het apparaat moet een telemetrie-schema naar IoT Hub verzenden. Micro soft biedt de [Portal werk stroom](https://certify.azure.com) voor het uitvoeren van de tests. Dubbele eigenschap van apparaat (indien geïmplementeerd): **1.** AICS valideert de eigenschap lezen/schrijven in het apparaat dubbele JSON **2.** Gebruiker moet de JSON-nettolading opgeven die moet worden gewijzigd **3.** AICS valideert de opgegeven gewenste eigenschappen van IoT Hub en ACK-bericht dat is ontvangen door het apparaat |
| **Bronnen**           | **1.** [certificerings stappen](./overview.md) (heeft alle extra resources), **2.** [Apparaat-apparaatdubbels met IoT Hub gebruiken](../iot-hub/iot-hub-devguide-device-twins.md) |
