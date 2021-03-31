---
title: Eigenschappen van de agent en de hub-module apparaatdubbels-Azure IoT Edge
description: Controleer de specifieke eigenschappen en hun waarden voor de edgeAgent-en edgeHub-module apparaatdubbels
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/31/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 951111b217b7ace3f12676edf6febfa7266094df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103489945"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Eigenschappen van de IoT Edge agent en IoT Edge hub-module apparaatdubbels

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

De IoT Edge-agent en IoT Edge-hub zijn twee modules die de IoT Edge runtime vormen. Zie voor meer informatie over de verantwoordelijkheden van elke runtime module [inzicht in de Azure IOT Edge runtime en de bijbehorende architectuur](iot-edge-runtime.md).

Dit artikel bevat de gewenste eigenschappen en gerapporteerde eigenschappen van de runtime module apparaatdubbels. Zie informatie over het [implementeren van modules en het tot stand brengen van routes in IOT Edge](module-composition.md)voor meer informatie over het implementeren van modules op IOT edge apparaten.

Een module met dubbele inhoud:

* **Gewenste eigenschappen**. De back-end van de oplossing kan gewenste eigenschappen instellen en de module kan deze lezen. De module kan ook meldingen ontvangen over wijzigingen in de gewenste eigenschappen. Gewenste eigenschappen worden samen met gerapporteerde eigenschappen gebruikt voor het synchroniseren van module configuratie of-voor waarden.

* **Gerapporteerde eigenschappen**. De module kan gerapporteerde eigenschappen instellen en de back-end van de oplossing kan deze lezen en er query's op uitvoeren. Gerapporteerde eigenschappen worden samen met de gewenste eigenschappen gebruikt voor het synchroniseren van module configuratie of-voor waarden.

## <a name="edgeagent-desired-properties"></a>Gewenste eigenschappen EdgeAgent

De module voor de IoT Edge-agent wordt aangeroepen `$edgeAgent` en coördineert de communicatie tussen de IOT Edge agent die op een apparaat wordt uitgevoerd en IOT hub. De gewenste eigenschappen worden ingesteld wanneer een implementatie manifest op een specifiek apparaat wordt toegepast als onderdeel van een implementatie met één apparaat of schaal.

| Eigenschap | Beschrijving | Vereist |
| -------- | ----------- | -------- |
| schemaVersion | "1,0" of "1,1". Versie 1,1 is geïntroduceerd in IoT Edge versie 1.0.10 en wordt aanbevolen. | Yes |
| runtime. type | Moet ' docker ' zijn | Yes |
| runtime. settings. minDockerVersion | Ingesteld op de minimale docker-versie die is vereist voor dit implementatie manifest | Yes |
| runtime. settings. loggingOptions | Een stringified-JSON met de registratie opties voor de container van de IoT Edge agent. [Opties voor docker-logboek registratie](https://docs.docker.com/engine/admin/logging/overview/) | No |
| runtime. settings. registryCredentials<br>. {registryId}. gebruikers naam | De gebruikers naam van het container register. Voor Azure Container Registry is de gebruikers naam meestal de register naam.<br><br> Register referenties zijn nodig voor installatie kopieën van een persoonlijke module. | No |
| runtime. settings. registryCredentials<br>. {registryId}. wacht woord | Het wacht woord voor het container register. | No |
| runtime. settings. registryCredentials<br>. {registryId}. adres | Het adres van het container register. Voor Azure Container Registry is het adres doorgaans *{Registry name}. azurecr. io*. | No |  
| systemModules. edgeAgent. type | Moet ' docker ' zijn | Yes |
| systemModules. edgeAgent. settings. image | De URI van de afbeelding van de IoT Edge agent. De IoT Edge agent kan momenteel niet worden bijgewerkt. | Yes |
| systemModules. edgeAgent. settings<br>.createOptions | Een stringified-JSON met de opties voor het maken van de IoT Edge agent-container. [Opties voor het maken van docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeAgent.configuration.id | De ID van de implementatie die deze module heeft geïmplementeerd. | IoT Hub stelt deze eigenschap in wanneer het manifest wordt toegepast met behulp van een implementatie. Maakt geen deel uit van een implementatie manifest. |
| systemModules. edgeHub. type | Moet ' docker ' zijn | Yes |
| systemModules. edgeHub. status | Moet worden uitgevoerd | Yes |
| systemModules.edgeHub.restartPolicy | Moet ' Always ' zijn | Yes |
| systemModules.edgeHub.startupOrder | Een geheel getal dat een module in de opstart volgorde heeft. 0 is het eerste en maximum geheel getal (4294967295) is de laatste waarde. Als er geen waarde wordt gegeven, is de standaard instelling een maximum geheel getal.  | No |
| systemModules. edgeHub. settings. image | De URI van de afbeelding van de IoT Edge hub. | Yes |
| systemModules. edgeHub. settings<br>.createOptions | Een stringified-JSON met de opties voor het maken van de IoT Edge hub-container. [Opties voor het maken van docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeHub.configuration.id | De ID van de implementatie die deze module heeft geïmplementeerd. | IoT Hub stelt deze eigenschap in wanneer het manifest wordt toegepast met behulp van een implementatie. Maakt geen deel uit van een implementatie manifest. |
| modules. {moduleId}. versie | Een door de gebruiker gedefinieerde teken reeks die de versie van deze module voor stelt. | Yes |
| modules. {moduleId}. type | Moet ' docker ' zijn | Yes |
| modules. {moduleId}. status | {"uitvoeren" \| "gestopt"} | Yes |
| modules. {moduleId}. restartPolicy | {"Never" \| "on-failure" \| " \| " altijd "} | Yes |
| modules. {moduleId}. startupOrder | Een geheel getal dat een module in de opstart volgorde heeft. 0 is het eerste en maximum geheel getal (4294967295) is de laatste waarde. Als er geen waarde wordt gegeven, is de standaard instelling een maximum geheel getal.  | No |
| modules. {moduleId}. imagePullPolicy | {"on-Create" \| ' Never '} | No |
| modules. {moduleId}. env | Een lijst met omgevings variabelen die moeten worden door gegeven aan de module. Neemt de indeling `"<name>": {"value": "<value>"}` | No |
| modules. {moduleId}. settings. image | De URI naar de module afbeelding. | Yes |
| modules. {moduleId}. settings. createOptions | Een stringified-JSON met de opties voor het maken van de module container. [Opties voor het maken van docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| modules. {moduleId}. configuratie.-id | De ID van de implementatie die deze module heeft geïmplementeerd. | IoT Hub stelt deze eigenschap in wanneer het manifest wordt toegepast met behulp van een implementatie. Maakt geen deel uit van een implementatie manifest. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent gerapporteerde eigenschappen

De gerapporteerde eigenschappen van de IoT Edge agent bevatten drie belang rijke informatie:

1. De status van de toepassing van de laatst weer gegeven gewenste eigenschappen;
2. De status van de modules die momenteel worden uitgevoerd op het apparaat, zoals gerapporteerd door de IoT Edge-agent; maar
3. Een kopie van de gewenste eigenschappen die momenteel worden uitgevoerd op het apparaat.

De kopie van de huidige gewenste eigenschappen is handig om te weten of het apparaat de meest recente implementatie heeft toegepast of nog steeds een eerder implementatie manifest uitvoert.

> [!NOTE]
> De gerapporteerde eigenschappen van de IoT Edge-agent zijn nuttig, omdat er query's kunnen worden uitgevoerd met de [IOT hub query taal](../iot-hub/iot-hub-devguide-query-language.md) om de status van implementaties op schaal te onderzoeken. Zie voor meer informatie over het gebruik van de IoT Edge agent-eigenschappen voor de status [IOT Edge implementaties voor één apparaat of op schaal begrijpen](module-deployment-monitoring.md).

De volgende tabel bevat niet de informatie die uit de gewenste eigenschappen is gekopieerd.

| Eigenschap | Beschrijving |
| -------- | ----------- |
| lastDesiredVersion | Dit geheel getal verwijst naar de laatste versie van de gewenste eigenschappen die worden verwerkt door de IoT Edge agent. |
| lastDesiredStatus. code | Deze status code verwijst naar de laatste gewenste eigenschappen die worden weer gegeven door de IoT Edge agent. Toegestane waarden: `200` geslaagd, `400` Ongeldige configuratie, `412` Ongeldige schema versie, `417` de gewenste eigenschappen zijn leeg, `500` mislukt |
| lastDesiredStatus. Description | Tekst beschrijving van de status |
| deviceHealth | `healthy` Als de runtime status van alle modules ofwel `running` of `stopped` `unhealthy` anders is |
| configurationHealth. {deploymentId}. status | `healthy` Als de runtime status van alle modules die zijn ingesteld door de implementatie {deploymentId} `running` `stopped` , of `unhealthy` anders |
| runtime. platform. OS | Melden van het besturings systeem dat op het apparaat wordt uitgevoerd |
| runtime. platform. Architecture | Rapportage van de architectuur van de CPU op het apparaat |
| systemModules.edgeAgent.runtimeStatus | De gerapporteerde status van de IoT Edge-agent: {"wordt uitgevoerd" \| "is beschadigd"} |
| systemModules.edgeAgent.statusDescription | De tekst beschrijving van de gerapporteerde status van de IoT Edge agent. |
| systemModules.edgeHub.runtimeStatus | Status van IoT Edge hub: {"uitvoeren gestopt" "" \| \| uitstel "" Failed " \| \| } |
| systemModules.edgeHub.statusDescription | De tekst beschrijving van de status van IoT Edge hub als deze beschadigd is. |
| systemModules. edgeHub. exitCode | De afsluit code die wordt gerapporteerd door de IoT Edge hub-container als de container wordt afgesloten |
| systemModules.edgeHub.startTimeUtc | Tijdstip waarop IoT Edge hub voor het laatst is gestart |
| systemModules.edgeHub.lastExitTimeUtc | Tijdstip waarop IoT Edge hub voor het laatst is afgesloten |
| systemModules.edgeHub.lastRestartTimeUtc | Tijdstip waarop IoT Edge hub voor het laatst opnieuw is opgestart |
| systemModules.edgeHub.restartCount | Aantal keer dat deze module opnieuw is opgestart als onderdeel van het beleid voor opnieuw opstarten. |
| modules. {moduleId}. runtimeStatus | Status van de module: {"" wordt \| gestopt "" \| is mislukt "" \| uitstel "" is \| beschadigd "} |
| modules. {moduleId}. statusDescription | De tekst beschrijving van de status van de module indien beschadigd. |
| modules. {moduleId}. exitCode | De afsluit code die wordt gerapporteerd door de module container als de container wordt afgesloten |
| modules. {moduleId}. startTimeUtc | Tijdstip waarop de module voor het laatst is gestart |
| modules. {moduleId}. lastExitTimeUtc | Tijdstip waarop de module de laatste keer is afgesloten |
| modules. {moduleId}. lastRestartTimeUtc | Tijdstip waarop de module voor het laatst opnieuw is gestart |
| modules. {moduleId}. restartCount | Aantal keer dat deze module opnieuw is opgestart als onderdeel van het beleid voor opnieuw opstarten. |

## <a name="edgehub-desired-properties"></a>Gewenste eigenschappen EdgeHub

De module voor de IoT Edge hub wordt aangeroepen `$edgeHub` en coördineert de communicatie tussen de IOT Edge hub die op een apparaat wordt uitgevoerd en IOT hub. De gewenste eigenschappen worden ingesteld wanneer een implementatie manifest op een specifiek apparaat wordt toegepast als onderdeel van een implementatie met één apparaat of schaal.

| Eigenschap | Beschrijving | Vereist in het implementatie manifest |
| -------- | ----------- | -------- |
| schemaVersion | "1,0" of "1,1". Versie 1,1 is geïntroduceerd in IoT Edge versie 1.0.10 en wordt aanbevolen. | Yes |
| stuurt. RouteName | Een teken reeks die een IoT Edge hub-route vertegenwoordigt. Zie voor meer informatie [routes declareren](module-composition.md#declare-routes). | Het `routes` element kan aanwezig zijn, maar leeg zijn. |
| storeAndForwardConfiguration.timeToLiveSecs | De tijd in seconden dat IoT Edge hub berichten houdt als de verbinding met de routerings eindpunten is verbroken, of IoT Hub of een lokale module. De waarde kan elk positief geheel getal zijn. | Yes |

## <a name="edgehub-reported-properties"></a>EdgeHub gerapporteerde eigenschappen

| Eigenschap | Beschrijving |
| -------- | ----------- |
| lastDesiredVersion | Dit geheel getal verwijst naar de laatste versie van de gewenste eigenschappen die worden verwerkt door de IoT Edge hub. |
| lastDesiredStatus. code | De status code verwijst naar de laatste gewenste eigenschappen die worden weer gegeven door de IoT Edge hub. Toegestane waarden: `200` geslaagd, `400` Ongeldige configuratie, `500` mislukt |
| lastDesiredStatus. Description | De tekst beschrijving van de status. |
| clients. {apparaat of moduleId}. status | De connectiviteits status van dit apparaat of deze module. Mogelijke waarden {"Connected" \| "Connected"}. Alleen module-identiteiten kunnen de status verbroken hebben. Downstream-apparaten die verbinding maken met IoT Edge hub worden alleen weer gegeven wanneer ze zijn verbonden. |
| clients. {apparaat of moduleId}. lastConnectTime | De laatste keer dat het apparaat of de module is verbonden. |
| clients. {apparaat of moduleId}. lastDisconnectTime | De laatste keer dat het apparaat of de module is losgekoppeld. |

## <a name="next-steps"></a>Volgende stappen

Zie [begrijpen hoe IOT Edge modules kunnen worden gebruikt, geconfigureerd en opnieuw worden gebruikt](module-composition.md)voor meer informatie over het gebruik van deze eigenschappen voor het samen stellen van implementatie manifesten.
