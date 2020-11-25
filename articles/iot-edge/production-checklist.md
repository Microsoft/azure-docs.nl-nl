---
title: Bereid u voor op het implementeren van uw oplossing in productie-Azure IoT Edge
description: Meer informatie over hoe u uw Azure IoT Edge oplossing van ontwikkeling tot productie kunt nemen, inclusief het instellen van uw apparaten met de juiste certificaten en het maken van een implementatie plan voor toekomstige code-updates.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 7cabae837656611813d44017ce2e1112f06066ef
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013289"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>De implementatie van uw IoT Edge oplossing in productie voorbereiden

Wanneer u klaar bent om uw IoT Edge oplossing van ontwikkeling naar productie te nemen, moet u ervoor zorgen dat deze is geconfigureerd voor voortdurende prestaties.

De informatie in dit artikel is niet allemaal gelijk. Om u te helpen prioriteiten te geven, wordt elke sectie gestart met lijsten die het werk in twee secties verdelen: **belang rijk** om te volt ooien voordat u naar productie gaat, of **handig** te weten.

## <a name="device-configuration"></a>Apparaatconfiguratie

IoT Edge-apparaten kunnen van een Raspberry pi van een laptop naar een virtuele machine die wordt uitgevoerd op een server. U hebt mogelijk fysiek of via een virtuele verbinding toegang tot het apparaat, of het kan gedurende lange tijd worden geïsoleerd. In beide gevallen moet u er zeker van zijn dat deze zo zijn geconfigureerd dat ze correct werken.

* **Belangrijk**
  * Productiecertificaten installeren
  * Een plan voor Apparaatbeheer hebben
  * Moby gebruiken als de container engine

* **Zinvol**
  * Upstream-protocol kiezen

### <a name="install-production-certificates"></a>Productiecertificaten installeren

Voor elk IoT Edge apparaat in productie moet een certificaat van een apparaat voor een certificerings instantie (CA) worden geïnstalleerd. Dat CA-certificaat wordt vervolgens gedeclareerd voor de IoT Edge runtime in het bestand config. yaml. Voor ontwikkelings-en test scenario's maakt de IoT Edge-runtime tijdelijke certificaten als er geen certificaten worden gedefinieerd in het bestand config. yaml. Deze tijdelijke certificaten verlopen echter na drie maanden en zijn niet veilig voor productie scenario's. Voor productie scenario's moet u uw eigen CA-certificaat voor uw apparaat opgeven, hetzij van een zelfondertekende certificerings instantie, hetzij zijn gekocht bij een commerciële certificerings instantie.

> [!NOTE]
> Op dit moment wordt een beperking in libiothsm voor komen dat certificaten worden gebruikt die op of na 1 januari 2038 verlopen.

Zie [How Azure IOT Edge certificaten gebruikt](iot-edge-certs.md)voor meer informatie over de rol van het CA-certificaat van het apparaat.

Zie [certificaat beheren op een IOT edge apparaat](how-to-manage-device-certificates.md)voor meer informatie over het installeren van certificaten op een IOT edge apparaat en om ernaar te verwijzen vanuit het bestand config. yaml.

### <a name="have-a-device-management-plan"></a>Een plan voor Apparaatbeheer hebben

Voordat u een apparaat in productie plaatst, moet u weten hoe u toekomstige updates gaat beheren. Voor een IoT Edge apparaat kan de lijst met onderdelen die moeten worden bijgewerkt, het volgende omvatten:

* Firmware van apparaat
* Bibliotheken van het besturings systeem
* Container engine, zoals Moby
* IoT Edge-daemon
* CA-certificaten

Zie [de IOT Edge runtime bijwerken](how-to-update-iot-edge.md)voor meer informatie. Voor de huidige methoden voor het bijwerken van de IoT Edge-daemon is fysieke of SSH-toegang tot het IoT Edge apparaat vereist. Als u veel apparaten wilt bijwerken, kunt u overwegen om de update stappen toe te voegen aan een script of een automatiserings programma zoals Ansible te gebruiken.

### <a name="use-moby-as-the-container-engine"></a>Moby gebruiken als de container engine

Een container engine is een vereiste voor een IoT Edge apparaat. Alleen Moby-engine wordt in productie ondersteund. Andere container engines, zoals docker, werken samen met IoT Edge. het is dan ook mogelijk om deze engines te gebruiken voor ontwikkeling. De Moby-engine kan opnieuw worden gedistribueerd wanneer deze wordt gebruikt in combi natie met Azure IoT Edge, en micro soft biedt onderhoud voor deze engine.

### <a name="choose-upstream-protocol"></a>Upstream-protocol kiezen

U kunt het protocol configureren (waarmee wordt bepaald welke poort wordt gebruikt) voor de communicatie IoT Hub tussen de IoT Edge agent en de IoT Edge hub. Het standaard protocol is AMQP, maar u kunt deze wijzigen, afhankelijk van de instellingen van uw netwerk.

De twee runtime modules hebben beide een omgevings variabele **UpstreamProtocol** . De geldige waarden voor de variabele zijn:

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Configureer de variabele UpstreamProtocol voor de IoT Edge agent in het bestand config. yaml op het apparaat zelf. Als uw IoT Edge-apparaat bijvoorbeeld zich achter een proxy server bevindt die AMQP poorten blokkeert, moet u mogelijk de IoT Edge agent configureren voor het gebruik van AMQP via WebSocket (AMQPWS) om de eerste verbinding met IoT Hub tot stand te brengen.

Zodra uw IoT Edge apparaat verbinding maakt, moet u de UpstreamProtocol-variabele voor beide runtime modules in toekomstige implementaties blijven configureren. Een voor beeld van dit proces vindt [u in een IOT edge apparaat configureren om te communiceren via een proxy server](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Implementatie

* **Zinvol**
  * Consistent zijn met upstream-Protocol
  * Host Storage instellen voor systeem modules
  * De geheugen ruimte beperken die wordt gebruikt door de IoT Edge hub
  * Geen debug-versies van module-installatie kopieën gebruiken

### <a name="be-consistent-with-upstream-protocol"></a>Consistent zijn met upstream-Protocol

Als u de IoT Edge-agent op uw IoT Edge apparaat zodanig hebt geconfigureerd dat er een ander protocol dan de standaard AMQP wordt gebruikt, moet u hetzelfde protocol in alle toekomstige implementaties declareren. Als uw IoT Edge-apparaat bijvoorbeeld zich achter een proxy server bevindt die AMQP poorten blokkeert, hebt u waarschijnlijk het apparaat geconfigureerd om verbinding te maken via AMQP via WebSocket (AMQPWS). Wanneer u modules op het apparaat implementeert, configureert u hetzelfde AMQPWS-protocol voor de IoT Edge agent en IoT Edge hub, anders worden de instellingen door de standaard-AMQP overschreven en wordt voor komen dat u weer verbinding maakt.

U hoeft alleen de omgevings variabele UpstreamProtocol te configureren voor de IoT Edge agent en IoT Edge hub-modules. Eventuele extra modules nemen elk protocol op in de runtime modules.

Een voor beeld van dit proces vindt [u in een IOT edge apparaat configureren om te communiceren via een proxy server](how-to-configure-proxy-support.md).

### <a name="set-up-host-storage-for-system-modules"></a>Host Storage instellen voor systeem modules

De modules IoT Edge hub en agent gebruiken lokale opslag voor het onderhouden van de status en het inschakelen van berichten tussen modules, apparaten en de Cloud. Voor betere betrouw baarheid en prestaties moet u de systeem modules configureren voor het gebruik van opslag op het host-bestands systeem.

Zie [opslag van hosts voor systeem modules](how-to-access-host-storage-from-module.md)voor meer informatie.

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>De geheugen ruimte die wordt gebruikt door IoT Edge hub beperken

Als u beperkte apparaten implementeert waarvoor beperkt geheugen beschikbaar is, kunt u IoT Edge hub configureren zodat deze wordt uitgevoerd in een meer gestroomlijnde capaciteit en minder schijf ruimte gebruiken. Deze configuraties begrenzen echter de prestaties van de IoT Edge hub, dus zoek het juiste saldo dat geschikt is voor uw oplossing.

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Niet optimaliseren voor prestaties op beperkte apparaten

De IoT Edge hub is standaard geoptimaliseerd voor prestaties, zodat er grote delen van het geheugen worden toegewezen. Deze configuratie kan stabiliteits problemen veroorzaken op kleinere apparaten, zoals de Raspberry pi. Als u apparaten met beperkte resources implementeert, kunt u de omgevings variabele **OptimizeForPerformance** instellen op **false** in de IOT Edge hub.

Wanneer **OptimizeForPerformance** is ingesteld op **True**, gebruikt de MQTT-protocol kop de PooledByteBufferAllocator, die betere prestaties heeft, maar meer geheugen toewijst. De allocator werkt niet goed op 32-bits besturings systemen of op apparaten met weinig geheugen. Daarnaast wijst RocksDb, wanneer deze is geoptimaliseerd voor prestaties, meer geheugen toe voor de rol van de lokale opslag provider.

Zie [stabiliteits problemen op kleinere apparaten](troubleshoot-common-errors.md#stability-issues-on-smaller-devices)voor meer informatie.

#### <a name="disable-unused-protocols"></a>Ongebruikte protocollen uitschakelen

Een andere manier om de prestaties van de IoT Edge hub te optimaliseren en het geheugen gebruik te verminderen, is om de protocol koppen uit te scha kelen voor protocollen die u niet gebruikt in uw oplossing.

Protocol koppen worden geconfigureerd door Boole-omgevings variabelen in te stellen voor de module IoT Edge hub in uw implementatie manifesten. De drie variabelen zijn:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Alle drie de variabelen hebben *twee onderstrepings tekens* en kunnen worden ingesteld op waar of onwaar.

#### <a name="reduce-storage-time-for-messages"></a>De opslag tijd voor berichten beperken

De IoT Edge hub-module slaat berichten tijdelijk op als deze om een of andere reden niet aan IoT Hub worden geleverd. U kunt configureren hoe lang de IoT Edge hub op niet-bezorgde berichten vasthoudt voordat ze verlopen. Als er geheugen problemen zijn op uw apparaat, kunt u de **timeToLiveSecs** -waarde verlagen in de IOT Edge hub-module dubbele.

De standaard waarde van de para meter timeToLiveSecs is 7200 seconden, die twee uur bedraagt.

### <a name="do-not-use-debug-versions-of-module-images"></a>Geen debug-versies van module-installatie kopieën gebruiken

Wanneer u overstapt van test scenario's naar productie scenario's, moet u de configuraties voor fout opsporing verwijderen uit implementatie manifesten. Controleer of geen enkele module installatie kopieën in de implementatie manifesten het achtervoegsel **\. debug** hebben. Als u opties voor het maken hebt toegevoegd om poorten in de modules voor fout opsporing weer te geven, moet u deze opties voor maken ook verwijderen.

## <a name="container-management"></a>Containerbeheer

* **Belangrijk**
  * Toegang tot uw container register beheren
  * Tags gebruiken om versies te beheren
* **Zinvol**
  * Runtime-containers opslaan in uw persoonlijke REGI ster

### <a name="manage-access-to-your-container-registry"></a>Toegang tot uw container register beheren

Voordat u modules implementeert voor productie IoT Edge-apparaten, moet u ervoor zorgen dat u de toegang tot uw container register zodanig beheert dat buitensta anders geen toegang krijgen tot of wijzigingen aanbrengen in uw container installatie kopieën. Gebruik een persoonlijk, niet openbaar, container register voor het beheren van container installatie kopieën.

In de zelf studies en andere documentatie wordt u geadviseerd om dezelfde container register referenties op uw IoT Edge-apparaat te gebruiken wanneer u op de ontwikkel computer gebruikt. Deze instructies zijn alleen bedoeld om u te helpen bij het eenvoudig instellen van test-en ontwikkelings omgevingen en mogen niet worden gevolgd in een productie scenario.

Voor een veiligere toegang tot het REGI ster hebt u de keuze uit [verificatie opties](../container-registry/container-registry-authentication.md). Een populaire en aanbevolen verificatie is het gebruik van een Active Directory service-principal die geschikt is voor toepassingen of services om container installatie kopieën te halen in een geautomatiseerd of op een andere manier zonder toezicht (headless), als IoT Edge apparaten doen.

Als u een Service-Principal wilt maken, voert u de twee scripts uit zoals beschreven in [Create a Service Principal](../container-registry/container-registry-auth-service-principal.md#create-a-service-principal). Deze scripts doen de volgende taken:

* Met het eerste script wordt de service-principal gemaakt. De Service-Principal-ID en het Service-Principal-wacht woord worden uitgevoerd. Sla deze waarden veilig op in uw records.

* Met het tweede script worden roltoewijzingen gemaakt om te verlenen aan de Service-Principal, die vervolgens indien nodig kan worden uitgevoerd. We raden u aan de gebruikersrol **acrPull** voor de `role` para meter toe te passen. Zie [Azure container Registry rollen en machtigingen](../container-registry/container-registry-roles.md)voor een lijst met rollen.

Als u wilt verifiëren met een Service-Principal, geeft u de Service-Principal-ID en het wacht woord op die u hebt verkregen van het eerste script. Geef deze referenties op in het implementatie manifest.

* Voor de gebruikers naam of client-ID geeft u de Service-Principal-ID op.

* Geef voor het wacht woord of client geheim het wacht woord voor de Service-Principal op.

> [!NOTE]
> Nadat u een verbeterde beveiligings verificatie hebt geïmplementeerd, schakelt u de **gebruikers instelling beheerder** uit zodat de standaard gebruikers naam en het wacht woord niet meer beschikbaar zijn. Selecteer in het container register in de Azure Portal in het linkerdeel venster onder **instellingen** de optie **toegangs sleutels**.

### <a name="use-tags-to-manage-versions"></a>Tags gebruiken om versies te beheren

Een tag is een docker-concept dat u kunt gebruiken om onderscheid te maken tussen versies van docker-containers. Tags zijn achtervoegsels als **1,0** die aan het einde van een container opslagplaats gaan. Bijvoorbeeld **MCR.Microsoft.com/azureiotedge-agent:1.0**. Tags zijn onveranderbaar en kunnen worden gewijzigd om op elk gewenst moment naar een andere container te gaan, zodat uw team moet overeenkomen met een Conventie bij het bijwerken van de installatie kopieën van uw module.

Met tags kunt u ook updates op uw IoT Edge-apparaten afdwingen. Wanneer u een bijgewerkte versie van een module naar het container register pusht, verhoogt u de code. Vervolgens kunt u een nieuwe implementatie naar uw apparaten pushen met het label dat wordt verhoogd. De container engine herkent de oplopende code als een nieuwe versie en haalt de meest recente module versie naar uw apparaat.

Voor een voor beeld van een label Conventie raadpleegt u [de IOT Edge runtime bijwerken](how-to-update-iot-edge.md#understand-iot-edge-tags) om te zien hoe IOT Edge gebruik maakt van roulerende Tags en specifieke tags om versies bij te houden.

### <a name="store-runtime-containers-in-your-private-registry"></a>Runtime-containers opslaan in uw persoonlijke REGI ster

U weet hoe u uw container installatie kopieën opslaat voor aangepaste code modules in uw persoonlijke Azure-REGI ster, maar u kunt deze ook gebruiken om open bare container installatie kopieën op te slaan, zoals voor de edgeAgent-en edgHub-runtime modules. Dit kan nodig zijn als u zeer nauw firewall beperkingen hebt, omdat deze runtime-containers worden opgeslagen in de micro soft-Container Registry (MCR).

Haal de installatie kopieën op met de docker-pull-opdracht die u in uw privé register wilt plaatsen. Houd er rekening mee dat u de installatie kopieën moet bijwerken met elke nieuwe versie van IoT Edge runtime.

| IoT Edge runtime-container | Docker-pull-opdracht |
| --- | --- |
| [Azure IoT Edge-agent](https://hub.docker.com/_/microsoft-azureiotedge-agent) | `docker pull mcr.microsoft.com/azureiotedge-agent` |
| [Azure IoT Edge hub](https://hub.docker.com/_/microsoft-azureiotedge-hub) | `docker pull mcr.microsoft.com/azureiotedge-hub` |

Zorg er vervolgens voor dat u de afbeeldings verwijzingen in het deployment.template.jsbestand voor de systeem modules edgeAgent en edgeHub bijwerkt. Vervang door `mcr.microsoft.com` de naam en server van uw REGI ster voor beide modules.

* edgeAgent:

    `"image": "<registry name and server>/azureiotedge-agent:1.0",`

* edgeHub:

    `"image": "<registry name and server>/azureiotedge-hub:1.0",`

## <a name="networking"></a>Netwerken

* **Zinvol**
  * Uitgaande/binnenkomende configuratie controleren
  * Verbindingen van IoT Edge apparaten toestaan
  * Communicatie via een proxy configureren

### <a name="review-outboundinbound-configuration"></a>Uitgaande/binnenkomende configuratie controleren

Communicatie kanalen tussen Azure IoT Hub en IoT Edge zijn altijd geconfigureerd als uitgaand verkeer. Voor de meeste IoT Edge scenario's zijn slechts drie verbindingen nodig. De container-engine moet verbinding maken met het container register (of registers) waarin de module installatie kopieën worden bewaard. De IoT Edge runtime moet verbinding maken met IoT Hub om configuratie-informatie over het apparaat op te halen en om berichten en telemetrie te verzenden. En als u automatische inrichting gebruikt, moet de IoT Edge-daemon verbinding maken met de Device Provisioning Service. Zie [firewall-en poort configuratie regels](troubleshoot.md#check-your-firewall-and-port-configuration-rules)voor meer informatie.

### <a name="allow-connections-from-iot-edge-devices"></a>Verbindingen van IoT Edge apparaten toestaan

Als uw netwerk installatie vereist dat u verbindingen die zijn gemaakt vanaf IoT Edge apparaten expliciet toestaat, raadpleegt u de volgende lijst met IoT Edge onderdelen:

* **IOT Edge agent** opent een permanente AMQP/MQTT-verbinding met IOT hub, mogelijk via websockets.
* **IOT Edge hub** opent één permanente AMQP-verbinding of meerdere MQTT-verbindingen met IOT hub, mogelijk via websockets.
* **IOT Edge-daemon** maakt periodieke https-aanroepen naar IOT hub.

In alle drie de gevallen komt de DNS-naam overeen met het patroon \* . Azure-devices.net.

Daarnaast maakt de **container-engine** aanroepen van container registers via https. De DNS-naam is mcr.microsoft.com om de installatie kopieën van de IoT Edge-runtime op te halen. De container-Engine maakt verbinding met andere registers zoals geconfigureerd in de implementatie.

Deze controle lijst is een start punt voor firewall regels:

   | URL ( \* = Joker teken) | Uitgaande TCP-poorten | Gebruik |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Micro soft Container Registry |
   | global.azure-devices-provisioning.net  | 443 | DPS-toegang (optioneel) |
   | \*. azurecr.io | 443 | Persoonlijke en container registers van derden |
   | \*.blob.core.windows.net | 443 | Azure Container Registry afbeeldings verschillen van Blob-opslag downloaden |
   | \*. azure-devices.net | 5671, 8883, 443 | Toegang IoT Hub |
   | \*. docker.io  | 443 | Toegang voor docker hub (optioneel) |

Sommige van deze firewall regels worden overgenomen van Azure Container Registry. Zie [regels configureren voor toegang tot een Azure container Registry achter een firewall](../container-registry/container-registry-firewall-access-rules.md)voor meer informatie.

> [!NOTE]
> Om een consistente FQDN-waarde tussen de REST-en data-eind punten te bieden, vanaf **15 juni 2020** wordt het gegevens eindpunt van micro soft container Registry gewijzigd van `*.cdn.mscr.io` in `*.data.mcr.microsoft.com`  
> Zie [configuratie van firewall regels voor micro soft container Registry-client](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md) voor meer informatie

Als u uw firewall niet wilt configureren om toegang tot open bare container registers toe te staan, kunt u installatie kopieën opslaan in uw persoonlijke container register, zoals beschreven in [runtime-containers voor opslag in uw persoonlijke REGI ster](#store-runtime-containers-in-your-private-registry).

### <a name="configure-communication-through-a-proxy"></a>Communicatie via een proxy configureren

Als uw apparaten worden geïmplementeerd in een netwerk dat gebruikmaakt van een proxy server, moeten ze kunnen communiceren via de proxy om IoT Hub en container registers te bereiken. Zie [een IOT edge apparaat configureren om te communiceren via een proxy server](how-to-configure-proxy-support.md)voor meer informatie.

## <a name="solution-management"></a>Oplossings beheer

* **Zinvol**
  * Logboeken en diagnostische gegevens instellen
  * Testen en CI/CD-pijp lijnen overwegen

### <a name="set-up-logs-and-diagnostics"></a>Logboeken en diagnostische gegevens instellen

In Linux gebruikt de IoT Edge-daemon journalen als het standaard stuur programma voor logboek registratie. U kunt het opdracht regel programma gebruiken `journalctl` om de daemon-logboeken te doorzoeken. In Windows gebruikt de IoT Edge-daemon Power shell-diagnose. Gebruiken `Get-IoTEdgeLog` om logboeken vanuit de daemon te doorzoeken. IoT Edge-modules gebruiken het JSON-stuur programma voor logboek registratie. Dit is de standaard instelling.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Wanneer u een IoT Edge-implementatie test, kunt u normaal gesp roken toegang krijgen tot uw apparaten om logboeken op te halen en problemen op te lossen. In een implementatie scenario hebt u deze optie mogelijk niet. Denk na over hoe u informatie over uw apparaten in productie gaat verzamelen. Een optie is het gebruik van een logboek module voor het verzamelen van gegevens uit de andere modules en deze naar de Cloud verzendt. Een voor beeld van een logboek module is [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics)of u kunt uw eigen ontwerp ontwerpen.

### <a name="place-limits-on-log-size"></a>Limieten voor logboek grootte

De Moby-container engine stelt standaard de limieten voor de container logboek grootte niet in. Na verloop van tijd kan dit ertoe leiden dat het apparaat wordt opgevuld met Logboeken en er onvoldoende schijf ruimte beschikbaar is. Houd rekening met de volgende opties om dit te voor komen:

#### <a name="option-set-global-limits-that-apply-to-all-container-modules"></a>Optie: globale limieten instellen die van toepassing zijn op alle container modules

U kunt de grootte van alle container logboeken beperken in de logboek opties van de container-engine. In het volgende voor beeld wordt het logboek stuur programma ingesteld op `json-file` (aanbevolen) met limieten voor grootte en aantal bestanden:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Voeg deze gegevens toe aan een bestand met `daemon.json` de naam en plaats het op de juiste locatie voor het platform van uw apparaat.

| Platform | Locatie |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

De container engine moet opnieuw worden gestart om de wijzigingen van kracht te laten worden.

#### <a name="option-adjust-log-settings-for-each-container-module"></a>Optie: logboek instellingen voor elke container module aanpassen

U kunt dit doen in de **createOptions** van elke module. Bijvoorbeeld:

```yml
"createOptions": {
    "HostConfig": {
        "LogConfig": {
            "Type": "json-file",
            "Config": {
                "max-size": "10m",
                "max-file": "3"
            }
        }
    }
}
```

#### <a name="additional-options-on-linux-systems"></a>Aanvullende opties op Linux-systemen

* Configureer de container-engine voor het verzenden van logboeken naar `systemd` het [logboek](https://docs.docker.com/config/containers/logging/journald/) door in te stellen `journald` als het standaard stuur programma voor logboek registratie.

* Verwijder regel matig oude logboeken van uw apparaat door een logrotate-hulp programma te installeren. Gebruik de volgende bestands specificatie:

   ```txt
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>Testen en CI/CD-pijp lijnen overwegen

Voor het meest efficiënte IoT Edge implementatie scenario kunt u uw productie-implementatie integreren in uw test-en CI/CD-pijp lijnen. Azure IoT Edge ondersteunt meerdere CI/CD-platformen, waaronder Azure DevOps. Zie [continue integratie en continue implementatie voor Azure IOT Edge](how-to-continuous-integration-continuous-deployment.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [IOT Edge automatische implementatie](module-deployment-monitoring.md).
* Zie hoe IoT Edge ondersteuning biedt voor [continue integratie en continue implementatie](how-to-continuous-integration-continuous-deployment.md).
