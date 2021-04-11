---
title: Security Framework-Azure IoT Edge | Microsoft Docs
description: Meer informatie over de beveiligings-, verificatie-en autorisatie standaarden die zijn gebruikt voor het ontwikkelen van Azure IoT Edge en die u moet overwegen bij het ontwerpen van uw oplossing
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9ff154eee6c6174a8f1a3aa7bea37ef62273bb1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103489499"
---
# <a name="security-standards-for-azure-iot-edge"></a>Beveiligings standaarden voor Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge worden de Risico's opgelost die inherent zijn bij het verplaatsen van uw gegevens en analyses naar de intelligente rand. Het IoT Edge Security Standards-saldo is flexibel voor verschillende implementatie scenario's met de beveiliging die u van alle Azure-Services verwacht.

IoT Edge worden uitgevoerd op verschillende mogelijkheden en modellen van hardware, ondersteunt verschillende besturings systemen en is van toepassing op verschillende implementatie scenario's. In plaats van concrete oplossingen voor specifieke scenario's te bieden, is IoT Edge een uitbreidbaar beveiligings raamwerk dat is gebaseerd op goed gegronde principes die zijn ontworpen voor schaal. Het risico van een implementatie scenario is afhankelijk van een groot aantal factoren, waaronder:

* Eigendom van oplossing
* Geografie voor implementatie
* Gegevens gevoeligheid
* Privacy
* Verticale toepassing
* Wettelijke vereisten

Dit artikel bevat een overzicht van het IoT Edge Security-Framework. Zie [intelligent Edge beveiligen](https://azure.microsoft.com/blog/securing-the-intelligent-edge/)voor meer informatie.

## <a name="standards"></a>Standaarden

Standaarden bevorderen het gemak en het gemak van de implementatie, beide hallmarks van beveiliging. Een beveiligings oplossing zou zichzelf kunnen onderbouwen op basis van evaluatie om een vertrouwens relatie tot stand te brengen, en mag geen obstakel zijn voor de implementatie. Het ontwerp van het framework om Azure IoT Edge te beveiligen, is gebaseerd op tijdgeteste en door de industrie bewezen beveiligings protocollen voor vertrouwdheid en hergebruik.

## <a name="authentication"></a>Verificatie

Wanneer u een IoT-oplossing implementeert, moet u weten dat alleen vertrouwde actors, apparaten en modules toegang hebben tot uw oplossing. Verificatie op basis van certificaten is het primaire mechanisme voor verificatie voor het Azure IoT Edge-platform. Dit mechanisme is afgeleid van een reeks standaarden die zijn gebaseerd op de open bare-sleutel infrastructuur (PKiX) door de IETF (Internet Engineering Task Force).

Alle apparaten, modules en actors die communiceren met het Azure IoT Edge apparaat moeten unieke certificaat identiteiten hebben. Deze richt lijnen zijn van toepassing of de interacties fysiek of via een netwerk verbinding zijn. Niet elk scenario of onderdeel kan zichzelf onderlenen aan verificatie op basis van certificaten, waardoor de uitbreid baarheid van het beveiligings raamwerk veilige alternatieven biedt.

Zie [Azure IOT Edge certificaat gebruik](iot-edge-certs.md)voor meer informatie.

## <a name="authorization"></a>Autorisatie

Het principe van minimale bevoegdheden geeft aan dat gebruikers en onderdelen van een systeem alleen toegang moeten hebben tot de minimale set resources en gegevens die nodig zijn om hun rollen uit te voeren. Apparaten, modules en actors moeten alleen toegang hebben tot de bronnen en gegevens binnen hun machtigings bereik, en alleen wanneer de architectuur is toegestaan. Sommige machtigingen kunnen worden geconfigureerd met voldoende bevoegdheden en andere worden op architectuur afgedwongen. Sommige modules kunnen bijvoorbeeld worden gemachtigd om verbinding te maken met Azure IoT Hub. Er is echter geen reden waarom een module in één IoT Edge apparaat toegang moet hebben tot de twee modules in een ander IoT Edge apparaat.

Andere autorisatie schema's zijn onder andere machtigingen voor certificaat ondertekening en op rollen gebaseerd toegangs beheer (RBAC).

## <a name="attestation"></a>Attestation

Attestation zorgt voor de integriteit van software-bits, die belang rijk is voor het detecteren en voor komen van malware. Het Azure IoT Edge Security Framework classificeert Attestation onder drie hoofd categorieën:

* Statische Attestation
* Runtime Attestation
* Software Attestation

### <a name="static-attestation"></a>Statische Attestation

Bij statische Attestation wordt de integriteit van alle software op een apparaat gecontroleerd tijdens het opstarten, met inbegrip van het besturings systeem, alle Runtimes en configuratie-informatie. Omdat er tijdens het opstarten statische attestatie plaatsvindt, wordt vaak beveiligd opstarten genoemd. Het beveiligings raamwerk voor IoT Edge-apparaten wordt uitgebreid naar fabrikanten en beschikt over veilige hardware-mogelijkheden die een statische Attestation-proces garanderen. Deze processen zijn onder andere beveiligd opstarten en beveiligde firmware-upgrade. Als u werkt in nauwe samen werking met silicium leveranciers, elimineert u overbodige firmware lagen, waardoor het bedreigings oppervlak wordt geminimaliseerd.

### <a name="runtime-attestation"></a>Runtime Attestation

Wanneer een systeem een beveiligd opstart proces heeft voltooid, moeten goed ontworpen systemen pogingen voor het invoeren van malware en de juiste tegen maatregelen worden gedetecteerd. Aanvallen van schadelijke software zijn mogelijk gericht op de poorten en interfaces van het systeem. Als kwaad aardige actoren fysieke toegang tot een apparaat hebben, kunnen ze met het apparaat zelf knoeien of gebruikmaken van Side-Channel-aanvallen om toegang te krijgen. Dergelijke Malcontent, ongeacht of er malware of niet-geautoriseerde configuratie wijzigingen zijn, kunnen niet worden gedetecteerd door een statische Attestation omdat deze wordt ingevoegd na het opstart proces. Tegen maatregelen die worden geboden of afgedwongen door de hardware van het apparaat, kunnen deze bedreigingen ook buiten. Het beveiligings raamwerk voor IoT Edge expliciete uitbrei dingen aanroept voor het bestrijden van runtime bedreigingen.  

### <a name="software-attestation"></a>Software Attestation

Alle gezonde systemen, waaronder intelligente systemen, hebben patches en upgrades nodig. Beveiliging is belang rijk voor update processen, anders kunnen ze potentiële bedreigings vectoren zijn. Het beveiligings raamwerk voor IoT Edge-aanroepen voor updates via gemeten en ondertekende pakketten om de integriteit van de pakketten te waarborgen en te verifiëren. Deze standaard is van toepassing op alle besturings systemen en toepassings software-bits.

## <a name="hardware-root-of-trust"></a>De hoofdmap van de vertrouwens relatie

Voor veel intelligente apparaten, met name apparaten die fysiek kunnen worden gebruikt door potentiële schadelijke actors, is Hardware Security de laatste verdedigings linie. Moeilijk te vervalsen hardware is van cruciaal belang voor dergelijke implementaties. Azure IoT Edge moedigt productleveranciers van siliconen aan verschillende soorten vertrouwens basis van de hardware te bieden voor verschillende risico profielen en implementatie scenario's. Het is mogelijk dat de hardware-vertrouwens relatie afkomstig is van algemene beveiligings protocol standaarden zoals Trusted Platform Module (ISO/IEC 11889) en de apparaat-id compositie-engine (dobbel stenen) van Trusted Computing Group. Beveiligde enclave-technologieën zoals TrustZones en software Guard Extensions (SGX) bieden ook een hardware-vertrouwens relatie.

## <a name="certification"></a>Certificering

Om klanten te helpen weloverwogen beslissingen te nemen bij het inkopen van Azure IoT Edge-apparaten voor hun implementatie, bevat het IoT Edge-Framework certificerings vereisten. Basis voor deze vereisten zijn certificeringen die betrekking hebben op beveiligings claims en certificeringen die betrekking hebben op de validatie van de beveiligings implementatie. Een beveiligings claim geeft bijvoorbeeld aan dat het IoT Edge apparaat gebruikmaakt van beveiligde hardware waarmee opstart aanvallen kunnen worden voors tand. Een validatie certificering betekent dat de beveiligde hardware op de juiste wijze is geïmplementeerd om deze waarde in het apparaat aan te bieden. In overeenstemming met het beginsel van de eenvoud, probeert het Framework de belasting van de certificering mini maal te houden.

## <a name="extensibility"></a>Uitbreidbaarheid

Met IoT-technologie die verschillende soorten trans formaties verstuurt, moet beveiliging parallel worden ontwikkeld om nieuwe scenario's te verhelpen. Het Azure IoT Edge Security Framework begint met een solide basis waarop de uitbreid baarheid in verschillende dimensies wordt gebouwd om het volgende te omvatten:

* Security Services van de eerste partij, zoals de Device Provisioning Service voor Azure IoT Hub.
* Services van derden zoals beheerde beveiligings Services voor verschillende verticale toepassingen (zoals industriële of gezondheids zorg) of technologie focus (zoals beveiligings bewaking in netnetwerken of Silicon hardware Attestation Services) via een uitgebreid netwerk van partners.
* Verouderde systemen om retrofitting te omvatten met alternatieve beveiligings strategieën, zoals het gebruik van beveiligde technologie dan certificaten voor verificatie en identiteits beheer.
* Veilige hardware voor de invoering van opkomende technologieën voor veilige hardware en bijdragen aan silicium partners.

In het eind is het beveiligen van de intelligente Edge vereist voor gezamenlijke bijdragen van een open Community door de gemeen schappelijke interesse in het beveiligen van IoT. Deze bijdragen kunnen de vorm hebben van beveiligde technologieën of services. Het Azure IoT Edge Security Framework biedt een solide basis voor beveiliging die uitbreidbaar is voor de maximale dekking om hetzelfde vertrouwens niveau en dezelfde integriteit te bieden in de intelligente Edge, net als bij Azure-Cloud.  

## <a name="next-steps"></a>Volgende stappen

Lees meer over de manier waarop Azure IoT Edge [de intelligente Edge beveiligt](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).
