---
title: Problemen met Azure IoT Hub-fout 401003 IoTHubUnauthorized
description: Meer informatie over het oplossen van fout 401003 IoTHubUnauthorized
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 8fb891d5a47203c9905a7def9d04199d24327f70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94357246"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

In dit artikel worden de oorzaken en oplossingen voor **401003 IoTHubUnauthorized** -fouten beschreven.

## <a name="symptoms"></a>Symptomen

### <a name="symptom-1"></a>Symptoom 1

In Logboeken ziet u een patroon van apparaten waarmee de verbinding met **401003 IoTHubUnauthorized** wordt verbroken, gevolgd door **404104 DeviceConnectionClosedRemotely** en binnenkort verbinding maken.

### <a name="symptom-2"></a>Symptoom 2

Aanvragen voor IoT Hub mislukken met een van de volgende fout berichten:

* Autorisatie header ontbreekt
* IotHub ' \* ' bevat het opgegeven apparaat ' ' niet \*
* Autorisatie regel ' \* ' staat geen toegang toe voor ' \* '
* Verificatie is mislukt voor dit apparaat, Token of certificaat vernieuwen en opnieuw verbinding maken
* Vinger afdruk komt niet overeen met configuratie: vinger afdruk: SHA1Hash = \* , SHA2Hash = \* ; Configuratie: PrimaryThumbprint = \* , SecondaryThumbprint =\*

## <a name="cause"></a>Oorzaak

### <a name="cause-1"></a>Oorzaak 1

Voor MQTT zijn sommige Sdk's afhankelijk van IoT Hub om de verbinding te verbreken wanneer het SAS-token verloopt om te weten wanneer het moet worden vernieuwd. Dus

1. Het SAS-token verloopt
1. IoT Hub het verlopen van het apparaat en het verbreken van de verbinding met **401003 IoTHubUnauthorized**
1. Het apparaat is de verbinding met **404104 DeviceConnectionClosedRemotely** voltooid
1. De IoT SDK genereert een nieuw SAS-token
1. Er wordt opnieuw verbinding gemaakt met het apparaat met IoT Hub

### <a name="cause-2"></a>Oorzaak 2

IoT Hub kan de Authentication Header, regel of sleutel niet verifiëren. Dit kan worden veroorzaakt door een van de redenen die in de symptomen worden genoemd.

## <a name="solution"></a>Oplossing

### <a name="solution-1"></a>Oplossing 1

Er is geen actie nodig als u IoT SDK gebruikt voor verbinding met behulp van het apparaat connection string. IoT SDK genereert het nieuwe token opnieuw om opnieuw verbinding te maken met de verval datum van SAS-tokens.

De standaard levens duur van tokens is 60 minuten voor de Sdk's; voor sommige Sdk's kan echter de levens duur van het token en de drempel waarde voor het vernieuwen van tokens worden geconfigureerd. Bovendien verschillen de fouten die worden gegenereerd wanneer een apparaat wordt losgekoppeld en opnieuw verbinding maakt met het vernieuwen van tokens voor elke SDK. Zie MQTT voor meer informatie over hoe u kunt bepalen welke SDK uw apparaat in Logboeken gebruikt, Raadpleeg het [gedrag voor het verbreken van apparaten met Azure IOT sdk's](iot-hub-troubleshoot-connectivity.md#mqtt-device-disconnect-behavior-with-azure-iot-sdks).

Als het fouten volume een probleem is, kunt u voor ontwikkel aars van het apparaat overschakelen naar de C SDK, waarmee het SAS-token wordt vernieuwd voordat het verloopt. Voor AMQP kan het SAS-token worden vernieuwd zonder de verbinding te verbreken.

### <a name="solution-2"></a>Oplossing 2

In het algemeen wordt het fout bericht weer gegeven waarin wordt uitgelegd hoe de fout moet worden opgelost. Als u om een of andere reden geen toegang hebt tot de details van het fout bericht, controleert u het volgende:

- De SAS of een ander beveiligings token dat u gebruikt, is niet verlopen.
- Voor X. 509-certificaat authenticatie is het certificaat van het apparaat of het CA-certificaat dat is gekoppeld aan het apparaat, niet verlopen. Zie [x. 509-beveiliging instellen in uw Azure IOT hub](iot-hub-security-x509-get-started.md)voor meer informatie over het registreren van x. 509 CA-certificaten met IOT hub.
- Voor X. 509 certificaat vingerafdruk verificatie wordt de vinger afdruk van het certificaat van het apparaat geregistreerd bij IoT Hub.
- De autorisatie referentie is goed gevormd voor het protocol dat u gebruikt. Zie [toegang tot IOT hub beheren](iot-hub-devguide-security.md)voor meer informatie.
- De autorisatie regel die wordt gebruikt, heeft de machtiging voor de aangevraagde bewerking.

## <a name="next-steps"></a>Volgende stappen

- Als u het verifiëren van IoT Hub eenvoudiger wilt maken, raden wij u aan [Azure IOT sdk's](iot-hub-devguide-sdks.md)te gebruiken.
- Zie [toegang tot IOT hub beheren](iot-hub-devguide-security.md)voor meer informatie over verificatie met IOT hub.
