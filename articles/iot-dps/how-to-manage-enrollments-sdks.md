---
title: Apparaat-inschrijvingen beheren met Azure DPS Sdk's
description: Registratie van apparaten beheren in de IoT Hub Device Provisioning Service (DPS) met behulp van de service-Sdk's
author: robinsh
ms.author: robinsh
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
ms.custom: fasttrack-edit, iot
services: iot-dps
ms.openlocfilehash: 45a2b7a64006ab6963290be3ac86a3a5d1e4916d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959880"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Registratie van apparaten beheren met de Sdk's van Azure Device Provisioning Service
Een *apparaatregistratie* maakt een record van één apparaat of een groep apparaten die op een bepaald moment bij de Device Provisioning-Service kunnen worden geregistreerd. De registratie record bevat de eerste gewenste configuratie voor de apparaten die deel uitmaken van deze inschrijving, inclusief de gewenste IoT-hub. In dit artikel leest u hoe u de registratie van apparaten voor uw inrichtings service programmatisch beheert met de Azure IoT Provisioning Service Sdk's.  De Sdk's zijn beschikbaar op GitHub in dezelfde opslag plaats als Azure IoT-Sdk's.

## <a name="prerequisites"></a>Vereisten
* Verkrijg de connection string van uw Device Provisioning service-exemplaar.
* De beveiligings artefacten van het apparaat verkrijgen voor het [Attestation-mechanisme](concepts-service.md#attestation-mechanism) dat wordt gebruikt:
    * [**Trusted Platform Module (TPM)**](./concepts-tpm-attestation.md):
        * Afzonderlijke inschrijving: registratie-ID en TPM-goedkeurings sleutel van een fysiek apparaat of van TPM-Simulator.
        * De registratie groep is niet van toepassing op TPM-Attestation.
    * [**X. 509**](./concepts-service.md#attestation-mechanism):
        * Individuele inschrijving: het [Leaf-certificaat](./concepts-service.md#attestation-mechanism) van een fysiek apparaat of van de SDK- [codec](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) -emulator.
        * Registratie groep: het [CA/basis certificaat](./concepts-x509-attestation.md#root-certificate) of het [tussenliggende certificaat](./concepts-x509-attestation.md#intermediate-certificate), dat wordt gebruikt om een apparaat certificaat te maken op een fysiek apparaat.  Het kan ook worden gegenereerd met de SDK-codec-emulator.
* Exacte API-aanroepen kunnen verschillen vanwege taal verschillen. Bekijk de voor beelden die worden weer gegeven op GitHub voor meer informatie:
   * [Voor beelden van Java Provisioning Service-client](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [ Voor beelden vanNode.js inrichten service-clients](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [Voor beelden van .NET Provisioning Service-client](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Een apparaatinschrijving maken
Er zijn twee manieren waarop u uw apparaten kunt inschrijven bij de inrichtings service:

* Een **registratie groep** is een vermelding voor een groep apparaten die een gemeen schappelijk Attestation-mechanisme van X. 509-certificaten delen, ondertekend door het [basis certificaat](./concepts-x509-attestation.md#root-certificate) of het [tussenliggende certificaat](./concepts-x509-attestation.md#intermediate-certificate). U kunt het beste een registratie groep gebruiken voor een groot aantal apparaten die een gewenste initiële configuratie delen, of voor apparaten die allemaal naar dezelfde Tenant gaan. Houd er rekening mee dat u alleen apparaten kunt inschrijven die gebruikmaken van het 509 Attestation-mechanisme van *X.* 

    U kunt een registratie groep maken met de Sdk's die volgen op deze werk stroom:

    1. Voor de registratie groep gebruikt het Attestation-mechanisme X. 509-basis certificaat.  Roep Service SDK API ```X509Attestation.createFromRootCertificate``` met basis certificaat om Attestation voor registratie te maken.  X. 509-basis certificaat is opgenomen in een PEM-bestand of als een teken reeks.
    1. Maak een nieuwe ```EnrollmentGroup``` variabele met behulp van de maakt ```attestation``` en een unieke ```enrollmentGroupId``` .  U kunt eventueel ook para meters instellen ```IoTHubHostName``` , zoals, ```ProvisioningStatus``` .
    2. Roep Service SDK API aan ```createOrUpdateEnrollmentGroup``` in uw back-end-toepassing met ```EnrollmentGroup``` om een registratie groep te maken.

* Een **afzonderlijke inschrijving** is een vermelding voor één apparaat dat kan worden geregistreerd. Individuele inschrijvingen kunnen X. 509-certificaten of SAS-tokens (van een fysieke of virtuele TPM) gebruiken als Attestation-mechanismen. We raden u aan om afzonderlijke inschrijvingen te gebruiken voor apparaten die unieke initiële configuraties vereisen, of voor apparaten die alleen SAS-tokens via TPM of virtuele TPM kunnen gebruiken als Attestation-mechanisme. Afzonderlijke inschrijvingen hebben mogelijk de gewenste apparaat-id voor IoT Hub die is opgegeven.

    U kunt een afzonderlijke inschrijving maken met de Sdk's die volgen op deze werk stroom:
    
    1. Kies uw ```attestation``` mechanisme. Dit kan TPM of X. 509 zijn.
        1. **TPM**: met behulp van de goedkeurings sleutel van een fysiek apparaat of van TPM Simulator als invoer, kunt u de Service SDK API aanroepen ```TpmAttestation``` om Attestation voor registratie te maken. 
        2. **X. 509**: als u het client certificaat gebruikt als invoer, kunt u de Service SDK API aanroepen ```X509Attestation.createFromClientCertificate``` om Attestation voor registratie te maken.
    2. Maak een nieuwe ```IndividualEnrollment``` variabele met behulp van het ```attestation``` gemaakte en unieke ```registrationId``` als-invoer, dat zich op uw apparaat bevindt of dat is gegenereerd op basis van de TPM-Simulator.  U kunt eventueel ook para meters instellen, zoals ```Device ID``` , ```IoTHubHostName``` , ```ProvisioningStatus``` .
    3. Roep Service SDK API aan ```createOrUpdateIndividualEnrollment``` in uw back-end-toepassing met ```IndividualEnrollment``` om een afzonderlijke inschrijving te maken.

Nadat u een inschrijving hebt gemaakt, retourneert de Device Provisioning-Service een registratie resultaat. Deze werk stroom wordt gedemonstreerd in de [eerder genoemde](#prerequisites)voor beelden.

## <a name="update-an-enrollment-entry"></a>Een inschrijvings vermelding bijwerken

Nadat u een inschrijvings vermelding hebt gemaakt, wilt u de registratie wellicht bijwerken.  Mogelijke scenario's zijn onder andere het bijwerken van de gewenste eigenschap, het bijwerken van de Attestation-methode of het intrekken van de toegang tot het apparaat.  Er zijn verschillende Api's voor afzonderlijke inschrijving en groeps registratie, maar geen onderscheid voor Attestation-mechanisme.

U kunt een inschrijvings vermelding die volgt op deze werk stroom bijwerken:
* **Afzonderlijke inschrijving**:
    1. Ontvang eerst de meest recente inschrijving van de inrichtings service met de Service SDK API ```getIndividualEnrollment``` .
    2. Wijzig indien nodig de para meter van de meest recente registratie. 
    3. Gebruik de laatste inschrijvings Service SDK API ```createOrUpdateIndividualEnrollment``` om uw inschrijvings vermelding bij te werken.
* **Groeps registratie**:
    1. Ontvang eerst de meest recente inschrijving van de inrichtings service met de Service SDK API ```getEnrollmentGroup``` .
    2. Wijzig indien nodig de para meter van de meest recente registratie.
    3. Gebruik de laatste inschrijvings Service SDK API ```createOrUpdateEnrollmentGroup``` om uw inschrijvings vermelding bij te werken.

Deze werk stroom wordt gedemonstreerd in de [eerder genoemde](#prerequisites)voor beelden.

## <a name="remove-an-enrollment-entry"></a>Een inschrijvings vermelding verwijderen

* **Individuele inschrijving** kan worden verwijderd door de Service SDK API te roepen ```deleteIndividualEnrollment``` met behulp van ```registrationId``` .
* **Groeps registratie** kan worden verwijderd door de Service SDK API te roepen ```deleteEnrollmentGroup``` met behulp van ```enrollmentGroupId``` .

Deze werk stroom wordt gedemonstreerd in de [eerder genoemde](#prerequisites)voor beelden.

## <a name="bulk-operation-on-individual-enrollments"></a>Bulk bewerking voor afzonderlijke inschrijvingen

U kunt een bulk bewerking uitvoeren om meerdere afzonderlijke inschrijvingen te maken, bij te werken of te verwijderen volgens deze werk stroom:

1. Maak een variabele die meerdere bevat ```IndividualEnrollment``` .  De implementatie van deze variabele verschilt voor elke taal.  Bekijk het voor beeld van bulk bewerking op GitHub voor meer informatie.
2. Roep Service SDK API ```runBulkOperation``` met een ```BulkOperationMode``` gewenste bewerking en uw variabele voor afzonderlijke registraties. Vier modi worden ondersteund: Create, update, updateIfMatchEtag en DELETE.

Nadat u een bewerking hebt uitgevoerd, retourneert de Device Provisioning-Service een resultaat van een bulk bewerking.

Deze werk stroom wordt gedemonstreerd in de [eerder genoemde](#prerequisites)voor beelden.