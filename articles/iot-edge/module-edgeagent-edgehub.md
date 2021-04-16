---
title: Eigenschappen van de agent en hubmodule-tweelingen - Azure IoT Edge
description: Bekijk de specifieke eigenschappen en hun waarden voor de edgeAgent- en edgeHub-module-tweelingen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/16/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 29ec958764f4a464d51f29f4b9c8223d5d7a1760
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576003"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Eigenschappen van de IoT Edge agent en IoT Edge hubmodule-tweelingen

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

De IoT Edge agent en IoT Edge hub zijn twee modules die de IoT Edge runtime. Zie Inzicht in de Azure IoT Edge runtime en de architectuur voor meer informatie over de verantwoordelijkheden [van elke runtimemodule.](iot-edge-runtime.md)

Dit artikel bevat de gewenste eigenschappen en gerapporteerde eigenschappen van de runtimemodule-tweelingen. Zie Voor meer informatie over het implementeren van modules op IoT Edge apparaten Informatie over het implementeren van modules en het maken van [routes in IoT Edge.](module-composition.md)

Een module dubbel bevat:

* **Gewenste eigenschappen.** De back-end van de oplossing kan gewenste eigenschappen instellen en de module kan deze lezen. De module kan ook meldingen ontvangen over wijzigingen in de gewenste eigenschappen. Gewenste eigenschappen worden samen met gerapporteerde eigenschappen gebruikt om moduleconfiguratie of -voorwaarden te synchroniseren.

* **Gerapporteerde eigenschappen**. De module kan gerapporteerde eigenschappen instellen en de back-end van de oplossing kan deze lezen en er query's op uitvoeren. Gerapporteerde eigenschappen worden samen met de gewenste eigenschappen gebruikt om moduleconfiguratie of -voorwaarden te synchroniseren.

## <a name="edgeagent-desired-properties"></a>Gewenste eigenschappen van EdgeAgent

De module dubbel voor de IoT Edge agent wordt aangeroepen en coördineert de communicatie tussen de IoT Edge agent die wordt uitgevoerd op een `$edgeAgent` apparaat en IoT Hub. De gewenste eigenschappen worden ingesteld bij het toepassen van een implementatiemanifest op een specifiek apparaat als onderdeel van een implementatie met één apparaat of op schaal.

| Eigenschap | Beschrijving | Vereist |
| -------- | ----------- | -------- |
| schemaVersion | '1.0' of '1.1'. Versie 1.1 is geïntroduceerd IoT Edge versie 1.0.10 en wordt aanbevolen. | Yes |
| runtime.type | Moet 'docker' zijn | Yes |
| runtime.settings.minDockerVersion | Ingesteld op de minimale Docker-versie die vereist is voor dit implementatiemanifest | Yes |
| runtime.settings.loggingOptions | Een JSON met tekenreeksen die de logboekregistratieopties voor de IoT Edge agentcontainer bevat. [Opties voor Docker-logboekregistratie](https://docs.docker.com/engine/admin/logging/overview/) | No |
| runtime.settings.registryCredentials<br>. {registryId}.username | De gebruikersnaam van het containerregister. Voor Azure Container Registry is de gebruikersnaam meestal de registernaam.<br><br> Registerreferenties zijn nodig voor alle persoonlijke module-afbeeldingen. | No |
| runtime.settings.registryCredentials<br>. {registryId}.password | Het wachtwoord voor het containerregister. | No |
| runtime.settings.registryCredentials<br>. {registryId}.address | Het adres van het containerregister. Voor Azure Container Registry is het adres meestal *{registernaam}.azurecr.io.* | No |  
| systemModules.edgeAgent.type | Moet 'docker' zijn | Yes |
| systemModules.edgeAgent.settings.image | De URI van de afbeelding van de IoT Edge agent. Op dit moment kan IoT Edge-agent zichzelf niet bijwerken. | Yes |
| systemModules.edgeAgent.settings<br>.createOptions | Een JSON met tekenreeksen die de opties bevat voor het maken van de IoT Edge agentcontainer. [Opties voor docker maken](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeAgent.configuration.id | De id van de implementatie die deze module heeft geïmplementeerd. | IoT Hub deze eigenschap in wanneer het manifest wordt toegepast met behulp van een implementatie. Maakt geen deel uit van een implementatiemanifest. |
| systemModules.edgeHub.type | Moet 'docker' zijn | Yes |
| systemModules.edgeHub.status | Moet 'actief' zijn | Yes |
| systemModules.edgeHub.restartPolicy | Moet 'altijd' zijn | Yes |
| systemModules.edgeHub.startupOrder | Een geheel getal waarvoor een module in de opstartorder staat. 0 is het eerste en het maximum gehele getal (4294967295) is de laatste. Als er geen waarde is opgegeven, is de standaardwaarde max. integer.  | No |
| systemModules.edgeHub.settings.image | De URI van de afbeelding van de IoT Edge hub. | Yes |
| systemModules.edgeHub.settings<br>.createOptions | Een JSON met tekenreeksen die de opties bevat voor het maken van de IoT Edge hubcontainer. [Docker-opties maken](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeHub.configuration.id | De id van de implementatie die deze module heeft geïmplementeerd. | IoT Hub deze eigenschap in wanneer het manifest wordt toegepast met behulp van een implementatie. Maakt geen deel uit van een implementatiemanifest. |
| Modules. {moduleId}.version | Een door de gebruiker gedefinieerde tekenreeks die de versie van deze module vertegenwoordigt. | Yes |
| Modules. {moduleId}.type | Moet 'docker' zijn | Yes |
| Modules. {moduleId}.status | {"running" \| "gestopt"} | Yes |
| Modules. {moduleId}.restartPolicy | {"nooit" \| "on-failure" \| "on-unhealthy" \| "always"} | Yes |
| Modules. {moduleId}.startupOrder | Een geheel getalwaarde waarvoor een module in de opstartorder staat. 0 is het eerste en het maximum gehele getal (4294967295) is de laatste. Als er geen waarde is opgegeven, is de standaardwaarde max integer.  | No |
| Modules. {moduleId}.imagePullPolicy | {"on-create" \| "nooit"} | No |
| Modules. {moduleId}.env | Een lijst met omgevingsvariabelen die aan de module moeten worden doorgeven. Neemt de indeling `"<name>": {"value": "<value>"}` | No |
| Modules. {moduleId}.settings.image | De URI naar de moduleafbeelding. | Yes |
| Modules. {moduleId}.settings.createOptions | Een JSON met tekenreeksen die de opties bevat voor het maken van de modulecontainer. [Opties voor docker maken](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| Modules. {moduleId}.configuration.id | De id van de implementatie die deze module heeft geïmplementeerd. | IoT Hub deze eigenschap in wanneer het manifest wordt toegepast met behulp van een implementatie. Maakt geen deel uit van een implementatiemanifest. |

## <a name="edgeagent-reported-properties"></a>Gerapporteerde eigenschappen van EdgeAgent

De IoT Edge gerapporteerde eigenschappen van de agent bevatten drie belangrijke gegevens:

1. De status van de toepassing van de laatst geziene gewenste eigenschappen;
2. De status van de modules die momenteel op het apparaat worden uitgevoerd, zoals gerapporteerd door de IoT Edge agent; En
3. Een kopie van de gewenste eigenschappen die momenteel op het apparaat worden uitgevoerd.

De kopie van de huidige gewenste eigenschappen is handig om te zien of het apparaat de meest recente implementatie heeft toegepast of nog steeds een eerder implementatiemanifest heeft uitgevoerd.

> [!NOTE]
> De gerapporteerde eigenschappen van de IoT Edge-agent zijn handig omdat ze kunnen worden opgevraagd met de [IoT Hub-querytaal](../iot-hub/iot-hub-devguide-query-language.md) om de status van implementaties op schaal te onderzoeken. Zie Understand [IoT Edge deployments for single devices or at scale](module-deployment-monitoring.md)(Inzicht in implementaties voor één apparaat of op schaal) voor meer informatie over het gebruik van de eigenschappen IoT Edge agent voor status.

De volgende tabel bevat niet de informatie die is gekopieerd uit de gewenste eigenschappen.

| Eigenschap | Beschrijving |
| -------- | ----------- |
| lastDesiredVersion | Dit gehele getal verwijst naar de laatste versie van de gewenste eigenschappen die door de IoT Edge verwerkt. |
| lastDesiredStatus.code | Deze statuscode verwijst naar de laatst gewenste eigenschappen die worden gezien door de IoT Edge agent. Toegestane `200` waarden: Geslaagd, `400` Ongeldige configuratie, `412` Ongeldige schemaversie, `417` de gewenste eigenschappen zijn leeg, `500` Mislukt |
| lastDesiredStatus.description | Tekstbeschrijving van de status |
| configurationHealth. {deploymentId}.health | `healthy` als de runtimestatus van alle modules die zijn ingesteld door de implementatie {deploymentId} of `running` `stopped` is, `unhealthy` anders |
| runtime.platform.OS | Het besturingssysteem rapporteren dat op het apparaat wordt uitgevoerd |
| runtime.platform.architecture | De architectuur van de CPU op het apparaat rapporteren |
| systemModules.edgeAgent.runtimeStatus | De gerapporteerde status van IoT Edge agent: {"running" \| "unhealthy"} |
| systemModules.edgeAgent.statusDescription | Tekstbeschrijving van de gerapporteerde status van de IoT Edge agent. |
| systemModules.edgeHub.runtimeStatus | Status van IoT Edge hub: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | Tekstbeschrijving van de status van IoT Edge hub als deze niet in orde is. |
| systemModules.edgeHub.exitCode | De exit-code die wordt gerapporteerd door IoT Edge hubcontainer als de container wordt afgesloten |
| systemModules.edgeHub.startTimeUtc | Tijdstip waarop IoT Edge hub voor het laatst is gestart |
| systemModules.edgeHub.lastExitTimeUtc | Tijdstip waarop IoT Edge hub voor het laatst is afgesloten |
| systemModules.edgeHub.lastRestartTimeUtc | Tijdstip waarop IoT Edge hub voor het laatst opnieuw is opgestart |
| systemModules.edgeHub.restartCount | Het aantal keren dat deze module opnieuw is opgestart als onderdeel van het beleid voor opnieuw opstarten. |
| Modules. {moduleId}.runtimeStatus | Status van de module: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| Modules. {moduleId}.statusDescription | Tekstbeschrijving van de status van de module als deze niet in orde is. |
| Modules. {moduleId}.exitCode | De exitcode die door de modulecontainer wordt gerapporteerd als de container wordt afgesloten |
| Modules. {moduleId}.startTimeUtc | Tijdstip waarop de module voor het laatst is gestart |
| Modules. {moduleId}.lastExitTimeUtc | Tijdstip waarop de module voor het laatst is afgesloten |
| Modules. {moduleId}.lastRestartTimeUtc | Tijdstip waarop de module voor het laatst opnieuw is opgestart |
| Modules. {moduleId}.restartCount | Het aantal keren dat deze module opnieuw is opgestart als onderdeel van het beleid voor opnieuw opstarten. |

## <a name="edgehub-desired-properties"></a>Gewenste eigenschappen van EdgeHub

De module dubbel voor de IoT Edge hub wordt aangeroepen en coördineert de communicatie tussen de IoT Edge hub die wordt uitgevoerd op een `$edgeHub` apparaat en IoT Hub. De gewenste eigenschappen worden ingesteld bij het toepassen van een implementatiemanifest op een specifiek apparaat als onderdeel van een implementatie met één apparaat of op schaal.

| Eigenschap | Beschrijving | Vereist in het implementatiemanifest |
| -------- | ----------- | -------- |
| schemaVersion | '1.0' of '1.1'. Versie 1.1 is geïntroduceerd IoT Edge versie 1.0.10 en wordt aanbevolen. | Yes |
| Routes. {routeName} | Een tekenreeks die een IoT Edge hubroute vertegenwoordigt. Zie Routes declareer voor [meer informatie.](module-composition.md#declare-routes) | Het `routes` element kan aanwezig zijn, maar leeg zijn. |
| storeAndForwardConfiguration.timeToLiveSecs | De tijd in seconden dat IoT Edge hub berichten bewaart als ze zijn losgekoppeld van routerings-eindpunten, ongeacht of IoT Hub of een lokale module. De waarde kan elk positief geheel getal zijn. | Yes |

## <a name="edgehub-reported-properties"></a>Gerapporteerde eigenschappen van EdgeHub

| Eigenschap | Beschrijving |
| -------- | ----------- |
| lastDesiredVersion | Dit gehele getal verwijst naar de laatste versie van de gewenste eigenschappen die worden verwerkt door de IoT Edge hub. |
| lastDesiredStatus.code | De statuscode die verwijst naar de laatste gewenste eigenschappen die worden gezien door de IoT Edge hub. Toegestane waarden: `200` Geslaagd, `400` Ongeldige configuratie, `500` Mislukt |
| lastDesiredStatus.description | Tekstbeschrijving van de status. |
| Clients. {apparaat of moduleId}.status | De connectiviteitsstatus van dit apparaat of deze module. Mogelijke waarden {"connected" \| "disconnected"}. Alleen module-id's kunnen niet-verbonden zijn. Downstreamapparaten die verbinding maken met IoT Edge hub worden alleen weergegeven wanneer ze zijn verbonden. |
| Clients. {apparaat of moduleId}.lastConnectTime | De laatste keer dat het apparaat of de module is verbonden. |
| Clients. {apparaat of moduleId}.lastDisconnectTime | De laatste keer dat de verbinding met het apparaat of de module is verbroken. |

## <a name="next-steps"></a>Volgende stappen

Zie Understand how IoT Edge how IoT Edge can be used, configured, and reused (Begrijpen hoe u modules kunt gebruiken, configureren en hergebruiken) voor meer informatie over het gebruik van deze eigenschappen voor het bouwen [van implementatiemanifests.](module-composition.md)
