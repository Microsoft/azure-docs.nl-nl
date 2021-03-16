---
title: Vertrouwelijke toepassingen als Azure IoT Edge modules
description: Gebruik de open enclave-SDK en API om vertrouwelijke toepassingen te schrijven en deze als IoT Edge modules voor vertrouwelijke computing te implementeren
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kgremban
ms.openlocfilehash: f9dff1b4c6b2489edd3cd685e3546618961d9757
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103487714"
---
# <a name="confidential-computing-at-the-edge"></a>Vertrouwelijk Computing aan de rand

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge ondersteunt vertrouwelijke toepassingen die worden uitgevoerd binnen beveiligde enclaves op het apparaat. Versleuteling biedt beveiliging voor gegevens tijdens de overdracht of in rust, maar enclaves bieden beveiliging voor gegevens en workloads die in gebruik zijn. IoT Edge ondersteunt open enclave als standaard voor het ontwikkelen van vertrouwelijke toepassingen.

Beveiliging is altijd een belang rijke rol van de Internet of Things (IoT), omdat vaak IoT-apparaten vaak op de wereld worden gebruikt in plaats van te worden beveiligd binnen een persoonlijke faciliteit. Deze bloot stelling vormt een risico voor manipulatie en vervalsing van apparaten omdat deze fysiek toegankelijk zijn voor ongeldige actors. IoT Edge-apparaten hebben nog steeds vertrouwen en integriteit nodig, omdat gevoelige werk belastingen aan de rand kunnen worden uitgevoerd. In tegens telling tot algemene Sens oren en actuators zijn deze intelligente apparaten mogelijk gevoelige werk belastingen die voorheen alleen worden uitgevoerd binnen beveiligde Cloud-of on-premises omgevingen.

Het [IOT Edge Security Manager](iot-edge-security-manager.md) behandelt een deel van de Challenge van het vertrouwelijke computer gebruik. De Security Manager gebruikt een HSM (Hardware Security module) om de identiteits-en doorlopende processen van een IoT Edge apparaat te beschermen.

Een ander aspect van vertrouwelijke computing is het beschermen van de gegevens die worden gebruikt aan de rand. Een *Trusted Execution Environment* (Tee) is een veilige, geïsoleerde omgeving op een processor en wordt ook wel een *enclave* genoemd. Een *vertrouwelijke toepassing* is een toepassing die wordt uitgevoerd in een enclave. Vanwege de aard van enclaves worden vertrouwelijke toepassingen beschermd tegen andere apps die worden uitgevoerd in de hoofd processor of in de TEE.

## <a name="confidential-applications-on-iot-edge"></a>Vertrouwelijke toepassingen op IoT Edge

Vertrouwelijke toepassingen worden tijdens de overdracht en op rest versleuteld en alleen ontsleuteld om te worden uitgevoerd binnen een vertrouwde uitvoerings omgeving. Deze standaard geldt voor vertrouwelijke toepassingen die als IoT Edge modules worden geïmplementeerd.

De ontwikkelaar maakt de vertrouwelijke toepassing en pakt deze als een IoT Edge module toe. De toepassing wordt versleuteld voordat deze naar het container register wordt gepusht. De toepassing blijft versleuteld gedurende het IoT Edge-implementatie proces totdat de module op het IoT Edge apparaat wordt gestart. Zodra de vertrouwelijke toepassing zich in de TEE van het apparaat bevindt, wordt deze ontsleuteld en kan de uitvoering worden uitgevoerd.

![Diagram-vertrouwelijke toepassingen worden versleuteld binnen IoT Edge modules tot ze zijn geïmplementeerd in de beveiligde enclave](./media/deploy-confidential-applications/confidential-applications-encrypted.png)

Vertrouwelijke toepassingen op IoT Edge zijn een logische uitbrei ding van [Azure vertrouwelijk computing](../confidential-computing/overview.md). Workloads die worden uitgevoerd binnen beveiligde enclaves in de Cloud, kunnen ook worden geïmplementeerd om te worden uitgevoerd binnen beveiligde enclaves aan de rand.

## <a name="open-enclave"></a>Open Enclave

De [Open ENCLAVE SDK](https://openenclave.io/sdk/) is een open-source project dat ontwikkel aars helpt om vertrouwelijke toepassingen te maken voor meerdere platforms en omgevingen. De open enclave SDK werkt in de vertrouwde uitvoerings omgeving van een apparaat, terwijl de open enclave-API fungeert als een interface tussen de TEE en de niet-TEE-verwerkings omgeving.

Open enclave ondersteunt meerdere hardwareplatforms. Voor de ondersteuning van IoT Edge voor enclaves is momenteel het open Portable-TEE-besturings systeem vereist (OP-TEE OS). Zie voor meer informatie [ENCLAVE SDK openen voor op-t-besturings systeem](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/OP-TEE/Introduction.md).

De open enclave-opslag plaats bevat ook voor beelden om ontwikkel aars te helpen aan de slag te gaan. Kies een van de inleidende artikelen voor meer informatie:

* [Open enclave SDK-voor beelden bouwen op Linux](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md)
* [Open enclave SDK-voor beelden bouwen op Windows](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesWindows.md)

## <a name="hardware"></a>Hardware

Momenteel is [TrustBox by Scalys](https://scalys.com/trustbox-industrial/) het enige apparaat dat wordt ondersteund door de fabrikant-service overeenkomsten voor het implementeren van vertrouwelijke toepassingen als IOT Edge modules. De TrustBox is gebaseerd op de TrustBox Edge en TrustBox EdgeXL-apparaten zijn beide vooraf geladen met de open enclave SDK en Azure IoT Edge.

Zie [aan de slag met open enclave voor de Scalys-TrustBox](https://aka.ms/scalys-trustbox-edge-get-started)voor meer informatie.

## <a name="develop-and-deploy"></a>Ontwikkelen en implementeren

Wanneer u klaar bent om uw vertrouwelijke toepassing te ontwikkelen en implementeren, kan de [micro soft open enclave](https://marketplace.visualstudio.com/items?itemName=ms-iot.msiot-vscode-openenclave) -extensie voor Visual Studio code helpen. U kunt Linux of Windows gebruiken als uw ontwikkel computer om modules voor de TrustBox te ontwikkelen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het ontwikkelen van vertrouwelijke toepassingen als IoT Edge modules met de [Open enclave-extensie voor Visual Studio code](https://github.com/openenclave/openenclave/tree/master/devex/vscode-extension)
