---
title: Azure IoT Edge Security Manager-Azure IoT Edge
description: Beheert de beveiligings koers van het IoT Edge apparaat en de integriteit van beveiligings Services.
services: iot-edge
keywords: beveiliging, beveiligd element, enclave, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 468f1b91d6c6157cd2af6de9599bad7f43c1ad8f
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492554"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge Security Manager

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge Security Manager is een zeer gebonden beveiligings kern voor het beveiligen van het IoT Edge apparaat en alle bijbehorende onderdelen door de Secure Silicon-hardware te abstractieen. Security Manager is het brand punt voor beveiligings beveiliging en biedt technologie-integratie punt aan Original Equipment Manufacturers (OEM).

![Azure IoT Edge Security Manager](media/edge-security-manager/iot-edge-security-manager.png)

IoT Edge Security Manager is gericht op het beschermen van de integriteit van het IoT Edge apparaat en alle inherente software bewerkingen. De Security Manager overgangen vertrouwt van de onderliggende hardware-basis van Trusted hardware (indien beschikbaar) om de IoT Edge runtime te Boots trapn en actieve bewerkingen te controleren.  De IoT Edge Security Manager is software die samen met Secure Silicon hardware (indien beschikbaar) werkt om de hoogst mogelijke beveiligings garanties te leveren.  

De verantwoordelijkheden van de IoT Edge Security Manager omvatten, maar zijn niet beperkt tot:

* Beveiligde en gemeten Boots trap van het Azure IoT Edge apparaat.
* Inrichting van de apparaat-id en overgang van de vertrouwens relatie, indien van toepassing.
* Host en beveiliging van de onderdelen van Cloud Services zoals Device Provisioning Service.
* Richt IoT Edge-modules veilig in met unieke identiteiten.
* Gate keeper to Device Hardware root of trust by notaris Services.
* Controleer de integriteit van IoT Edge-bewerkingen tijdens runtime.

IoT Edge Security Manager bevat drie onderdelen:

* IoT Edge Security daemon.
* Hardware Security module platform Abstraction Layer (HSM PAL).
* Optioneel maar sterk aanbevolen silicone root of Trust of HSM.

## <a name="the-iot-edge-security-daemon"></a>De IoT Edge Security daemon

De IoT Edge Security daemon is verantwoordelijk voor de logische bewerkingen van IoT Edge Security Manager. Dit vertegenwoordigt een aanzienlijk deel van de vertrouwde computing basis van het IoT Edge-apparaat.

### <a name="design-principles"></a>Ontwerpprincipes

De IoT Edge Security daemon volgt twee kern principes: maximaliseert de operationele integriteit en minimaliseert de grootten en het verloop.

#### <a name="maximize-operational-integrity"></a>Operationele integriteit maximaliseren

De IoT Edge Security daemon werkt met de hoogst mogelijke integriteit in de verdedigings functie van een bepaalde hoofdmap van Trust-hardware. Met de juiste integratie wordt de basis van het vertrouwen van hardware gemeten en wordt de beveiligings-daemon statisch en tijdens runtime bewaakt zodat er kan worden geknoeid.

Fysieke toegang is altijd een bedreiging voor IoT-apparaten. De hoofdmap van de vertrouwens relatie van de hardware speelt een belang rijke rol bij het beschermen van de integriteit van de IoT Edge Security daemon.  De hoofdmap van de vertrouwens relatie komt in twee varianten voor:

* Beveilig elementen voor de beveiliging van gevoelige informatie, zoals geheimen en cryptografische sleutels.
* Beveilig enclaves voor de beveiliging van geheimen zoals sleutels en gevoelige werk belastingen zoals meten en factureren.

Er zijn twee soorten uitvoerings omgevingen voor het gebruik van de hoofdmap van vertrouwen:

* De standaard of uitgebreide uitvoerings omgeving (REE) die afhankelijk is van het gebruik van beveiligde elementen om gevoelige informatie te beveiligen.
* De Trusted Execution Environment (TEE) die afhankelijk is van het gebruik van een beveiligde enclave-technologie om gevoelige informatie te beschermen en bescherming te bieden voor de uitvoering van software.

Voor apparaten die beveiligde enclaves gebruiken als de hoofdmap van de vertrouwens relatie, moet gevoelige logica binnen IoT Edge beveiligings-daemon zich in de enclave bevindt.  Niet-gevoelige delen van de beveiligings-daemon kunnen zich buiten het TEE bevinden.  In elk geval moeten Original Design Manufacturers (ODM) en Original Equipment Manufacturers (OEM) vertrouwen uitbreiden van de HSM om de integriteit van de IoT Edge Security daemon bij het opstarten en runtime te meten en te verdedigen.

#### <a name="minimize-bloat-and-churn"></a>Grootten en verloop minimaliseren

Een ander kern principe voor de IoT Edge Security daemon is om het verloop te minimaliseren.  Voor het hoogste vertrouwens niveau kan de IoT Edge-beveiligings-daemon nauw keurig zijn gekoppeld aan de apparaatstatus van de hardware van vertrouwen en als systeem eigen code worden gebruikt.  Het is gebruikelijk voor deze typen realisaties om de daemon-software bij te werken via de beveiligde update paden van de hoofdmap van de vertrouwens relatie (in plaats van de door het besturings systeem meegeleverde update mechanismen), wat in sommige scenario's lastig kan zijn.  Hoewel het vernieuwen van de beveiliging wordt aanbevolen voor IoT-apparaten, kunnen er grote update vereisten of nettoladingen met veel updates worden uitgebreid op veel manieren.  Voor beelden zijn het overs laan van updates om de operationele Beschik baarheid te maximaliseren of de hoofdmap van de vertrouw bare hardware te beperken voor het verwerken van grote update payloads.  Daarom is het ontwerp van IoT Edge Security daemon beknopt om de footprint en het vertrouwen van het vertrouwde computer gebruik te houden en om de update vereisten te minimaliseren.

### <a name="architecture-of-iot-edge-security-daemon"></a>Architectuur van IoT Edge Security daemon

![Azure IoT Edge Security daemon](media/edge-security-manager/iot-edge-security-daemon.png)

De IoT Edge Security daemon maakt gebruik van de beschik bare hardware-root van vertrouwens technologie voor beveiligings beveiliging.  Het biedt ook een gesplitste wereld wijde werking tussen een standaard/krachtige uitvoerings omgeving (REE) en een Trusted Execution Environment (t) wanneer de hardware-technologieën vertrouwde uitvoerings omgevingen bieden. Met roldefinities kunnen de belangrijkste onderdelen van IoT Edge de integriteit van het IoT Edge apparaat en de bijbehorende bewerkingen garanderen.

#### <a name="cloud-interface"></a>Cloud interface

Met de Cloud interface kan de IoT Edge Security daemon toegang krijgen tot Cloud Services, zoals Cloud complimenten, zoals beveiligings vernieuwing.  Zo gebruikt de IoT Edge Security daemon momenteel deze interface om toegang te krijgen tot de Azure IoT Hub [Device Provisioning Service](../iot-dps/index.yml) voor levenscyclus beheer van de apparaat-id.  

#### <a name="management-api"></a>Beheer-API

IoT Edge Security daemon biedt een beheer-API, die wordt aangeroepen door de IoT Edge agent bij het maken/starten/stoppen/verwijderen van een IoT Edge module. De Security daemon slaat registraties op voor alle actieve modules. Deze registraties wijzen de identiteit van een module toe aan bepaalde eigenschappen van de module. Voor beelden zijn deze module-eigenschappen bevatten de proces-id (PID) van het proces dat wordt uitgevoerd in de container en de hash van de inhoud van de docker-container.

Deze eigenschappen worden gebruikt door de API voor workload (hieronder beschreven) om te controleren of de aanroeper is geautoriseerd voor een actie.

De beheer-API is een geautoriseerde API die alleen kan worden aangeroepen vanuit de IoT Edge-agent.  Sinds de IoT Edge beveiligings-daemon Boots traps en start de IoT Edge-agent, wordt gecontroleerd of er niet is geknoeid met de IoT Edge agent en kan hij een impliciete registratie voor de IoT Edge agent maken. Hetzelfde Attestation-proces dat door de workload API wordt gebruikt, beperkt ook de toegang tot de beheer-API tot alleen de IoT Edge-agent.

#### <a name="container-api"></a>Container-API

De container-API communiceert met het container systeem dat in gebruik is voor module beheer, zoals Moby of docker.

#### <a name="workload-api"></a>API voor workload

De workload API is toegankelijk voor alle modules. Het biedt bewijs van identiteit, hetzij als een met HSM geroot ondertekend token of een x509-certificaat, en de bijbehorende vertrouwens bundel aan een module. De vertrouwens bundel bevat CA-certificaten voor alle andere servers die door de modules moeten worden vertrouwd.

De IoT Edge Security daemon maakt gebruik van een Attestation-proces om deze API te beveiligen. Wanneer een module deze API aanroept, probeert de beveiligings-daemon een registratie voor de identiteit te vinden. Als dit lukt, worden de eigenschappen van de registratie gebruikt om de module te meten. Als het resultaat van het meting proces overeenkomt met de registratie, wordt er een nieuw identiteits bewijs gegenereerd. De bijbehorende CA-certificaten (vertrouwens bundel) worden geretourneerd naar de module.  De module gebruikt dit certificaat om verbinding te maken met IoT Hub, andere modules of een server te starten. Wanneer het ondertekende token of certificaat bijna is verlopen, is het de verantwoordelijkheid van de module om een nieuw certificaat aan te vragen.

### <a name="integration-and-maintenance"></a>Integratie en onderhoud

Micro soft onderhoudt de belangrijkste code basis voor de [IOT Edge Security daemon op github](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Installatie en updates

Installatie en updates van de IoT Edge Security daemon worden beheerd via de pakketbeheersysteem van het besturings systeem. IoT Edge apparaten met de hoofdmap van het vertrouwens niveau van de hardware moeten extra beveiliging bieden voor de integriteit van de daemon door de levens cyclus te beheren via de beheer systemen beveiligd opstarten en bijwerken. De makers van apparaten moeten deze mogelijkheden verkennen op basis van hun respectieve apparaatfuncties.

#### <a name="versioning"></a>Versiebeheer

Met de IoT Edge runtime wordt de versie van de IoT Edge-beveiligings-daemon getraceerd en gerapporteerd. De versie wordt gerapporteerd als *runtime. platform. version* -kenmerk van de gerapporteerde eigenschap van de IOT Edge Agent module.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Hardware Security module platform Abstraction Layer (HSM PAL)

De HSM PAL bevindt zich in de hoofdmap van Trusted hardware om de ontwikkelaar of de gebruiker van IoT Edge uit hun ingewikkelde eigenschappen te isoleren.  Het bevat een combi natie van Application Programming Interface (API) en transdomein communicatie procedures, bijvoorbeeld communicatie tussen een standaard uitvoerings omgeving en een beveiligde enclave.  De daad werkelijke implementatie van de HSM PAL is afhankelijk van de specifieke beveiligde hardware die in gebruik is. Het bestaan hiervan maakt het gebruik van vrijwel elk veilig siliconen mogelijk.

## <a name="secure-silicon-root-of-trust-hardware"></a>Beveiligde Silicon-hoofdmap van Trust-hardware

Beveiligd silicium is nodig om de vertrouwens relatie binnen de hardware van IoT Edge apparaat te verankeren.  Beveiligd silicium is in het geruimde niveau inbegrepen om Trusted Platform Module (TPM), Inge sloten beveiligd element (eSE), ARM TrustZone, Intel SGX en aangepaste, beveiligde silicium technologieën te omvatten.  Het gebruik van Secure Silicon root of Trust in apparaten wordt aanbevolen, omdat de bedreigingen zijn gekoppeld aan fysieke toegankelijkheid van IoT-apparaten.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Integratie en onderhoud van beveiligings beheer IoT Edge

Het IoT Edge Security Manager streeft ernaar de onderdelen te identificeren en isoleren die de beveiliging en integriteit van het Azure IoT Edge platform voor aangepaste beveiliging beschermen. Derden, zoals Device makers, moeten gebruikmaken van aangepaste beveiligings functies die beschikbaar zijn met de hardware van het apparaat.  Zie de sectie volgende stappen voor koppelingen die laten zien hoe u de beveiliging van Azure IoT Security Manager kunt beveiligen met de Trusted Platform Module (TPM) op Linux-en Windows-platforms. Deze voor beelden gebruiken software of virtuele-Tpm's, maar zijn rechtstreeks van toepassing op het gebruik van afzonderlijke TPM-apparaten.  

## <a name="next-steps"></a>Volgende stappen

Lees de blog over [het beveiligen van de intelligente rand](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Een IoT Edge apparaat maken en inrichten [met een virtuele TPM op een virtuele Linux-machine](how-to-auto-provision-simulated-device-linux.md).

Een IoT Edge apparaat maken en inrichten [met een gesimuleerde TPM in Windows](how-to-auto-provision-simulated-device-windows.md).