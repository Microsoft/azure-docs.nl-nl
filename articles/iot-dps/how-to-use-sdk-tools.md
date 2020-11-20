---
title: Hulpprogram ma's voor Azure IoT Hub Device Provisioning Service Sdk's gebruiken
description: In dit document worden de hulpprogram ma's van Azure IoT Hub Device Provisioning Service (DPS) Sdk's voor ontwikkeling beschreven
author: wesmc7777
ms.author: wesmc
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 984d38752df93f233c6d87458e3c9ba713696177
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967241"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>De hulpprogram ma's in de Sdk's gebruiken om de ontwikkeling van het inrichten te vereenvoudigen
De IoT Hub Device Provisioning Service vereenvoudigt het inrichtings proces met Zero-Touch [inrichten](about-iot-dps.md#provisioning-process) , op een veilige en schaal bare manier.  Er is een beveiligings attest in de vorm van X. 509-certificaat of Trusted Platform Module (TPM) vereist.  Micro soft is ook in samen werking met [andere partners voor beveiligingshardware](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) om het vertrouwen in de beveiliging van IOT-implementatie te verbeteren. Meer informatie over de vereiste voor hardware-beveiliging kan voor ontwikkel aars behoorlijk lastig zijn. Er wordt een set met Sdk's voor Azure IoT Provisioning Service gegeven, zodat ontwikkel aars een laag kunnen gebruiken voor het schrijven van clients die met de inrichtings service communiceren. De Sdk's bieden ook voor beelden voor veelvoorkomende scenario's en een set hulpprogram ma's om beveiligings attesten in ontwikkeling te vereenvoudigen.

## <a name="trusted-platform-module-tpm-simulator"></a>Trusted Platform Module (TPM) Simulator
[TPM](./concepts-service.md#attestation-mechanism) kan verwijzen naar een standaard voor het veilig opslaan van sleutels om het platform te verifiëren, of kan verwijzen naar de I/O-interface die wordt gebruikt om te communiceren met de modules die de standaard implementeren. Tpm's kan bestaan als afzonderlijke hardware, geïntegreerde hardware, op firmware gebaseerd of op software gebaseerd.  TPM bevindt zich in productie op het apparaat, hetzij als discrete hardware, geïntegreerde hardware of op basis van de firmware. In de test fase wordt een op software gebaseerde TPM-Simulator aan ontwikkel aars verschaft.  Deze simulator is nu alleen beschikbaar voor het ontwikkelen op het Windows-platform.

De stappen voor het gebruik van de TPM-Simulator zijn:
1. [Bereid de ontwikkel omgeving](./quick-enroll-device-x509-java.md) voor en kloon de GitHub-opslag plaats:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Navigeer naar de map TPM simulator onder ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/``` .
3. Voer Simulator.exe uit voordat u een client toepassing voor inrichtings apparaat uitvoert.
4. Laat de Simulator tijdens het inrichtings proces op de achtergrond uitvoeren om registratie-ID en goedkeurings sleutel te verkrijgen.  Beide waarden zijn alleen geldig voor één exemplaar van de uitvoering.

## <a name="x509-certificate-generator"></a>X. 509-certificaat generator
[X. 509-certificaten](./concepts-x509-attestation.md#x509-certificates) kunnen worden gebruikt als Attestation-mechanisme voor het schalen van productie en het vereenvoudigen van het inrichten van apparaten.  Er zijn [verschillende manieren](../iot-hub/iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) om een X. 509-certificaat te verkrijgen:
* Voor productie omgeving raden we u aan om een X. 509 CA-certificaat te kopen bij een open bare basis certificerings instantie.
* Voor test omgevingen kunt u een X. 509-basis certificaat of een X. 509-certificaat keten genereren met behulp van:
    * OpenSSL: u kunt scripts gebruiken voor het genereren van certificaten:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [Power shell of bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Engine voor het samen stellen van de apparaat-id (DOBBEL ring): dobbel stenen kunnen worden gebruikt voor cryptografische apparaat-id en-Attestation op basis van TLS-protocol en X. 509-client certificaten.  Meer [informatie](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) over apparaat-ID'S met dobbel stenen.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>X. 509-certificaat generator met de-dobbel stenen-emulator gebruiken
De Sdk's bieden een X. 509-certificaat generator met een DOBBELer-emulator, die zich in de [Java-SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator)bevindt.  Deze generator werkt op verschillende platforms.  Het gegenereerde certificaat kan worden gebruikt voor ontwikkeling in andere talen.

De dobbel stenen emulator voert momenteel een basis certificaat, een tussenliggend certificaat, een blad certificaat en de bijbehorende persoonlijke sleutel uit.  Het basis certificaat of het tussenliggende certificaat kan echter niet worden gebruikt voor het ondertekenen van een afzonderlijk blad certificaat.  Als u een scenario voor groeps inschrijving wilt testen waarbij één handtekening certificaat wordt gebruikt voor het ondertekenen van de blad certificaten van meerdere apparaten, kunt u OpenSSL gebruiken om een keten van certificaten te maken.

Het X. 509-certificaat genereren met behulp van deze generator:
1. [Bereid de ontwikkel omgeving](./quick-enroll-device-x509-java.md) voor en kloon de GitHub-opslag plaats:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Wijzig de hoofdmap in azure-IOT-SDK-java.
3. Voer uit ```mvn install -DskipTests=true``` om alle vereiste pakketten te downloaden en de SDK te compileren
4. Navigeer naar de hoofdmap voor X. 509-certificaat generator in ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator``` .
5. Bouwen met ```mvn clean install```
6. Voer het hulpprogramma uit met behulp van de volgende opdrachten:
   ```
   cd target
   java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
   ```
7. Wanneer u hierom wordt gevraagd, kunt u ook een _algemene naam_ invoeren voor de certificaten.
8. Het hulp programma genereert lokaal een **client certificaat**, de **persoonlijke sleutel van het client certificaat**, het **tussenliggende certificaat** en het **basis certificaat**.

**Client certificaat** is het Leaf-certificaat op het apparaat.  Het **client certificaat** en de bijbehorende **persoonlijke sleutel van het client certificaat** zijn vereist in de apparaatclient. Afhankelijk van de taal die u kiest, kan het mechanisme dat u wilt opnemen in de client toepassing anders zijn.  Zie voor meer informatie de [Quick](./quick-create-simulated-device-x509.md) starts op gesimuleerd apparaat maken met X. 509 voor meer informatie.

Het basis certificaat of tussenliggende kan worden gebruikt voor het maken van een inschrijvings groep of een individuele inschrijving [via een programma](./how-to-manage-enrollments-sdks.md) of via de [Portal](./how-to-manage-enrollments.md).

## <a name="next-steps"></a>Volgende stappen
* Ontwikkelen met behulp van de [Azure IOT SDK]( https://github.com/Azure/azure-iot-sdks) voor Azure IOT hub en Azure IOT hub Device Provisioning Service