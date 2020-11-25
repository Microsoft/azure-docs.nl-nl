---
title: Terminologie die wordt gebruikt met Azure IoT Hub Device Provisioning Service | Microsoft Docs
description: Hierin worden veelvoorkomende terminologie beschreven die wordt gebruikt bij de Device Provisioning Service (DPS) en IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: b9fc37c6589cdd0bc6a5cdce7b7ebebe2c6e9a85
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019443"
---
# <a name="iot-hub-device-provisioning-service-dps-terminology"></a>Terminologie van IoT Hub Device Provisioning Service (DPS)

IoT Hub Device Provisioning Service is een Helper-service voor IoT Hub die u gebruikt om het inrichten van Zero-Touch-apparaten te configureren voor een opgegeven IoT-hub. Met de Device Provisioning Service kunt u miljoenen apparaten op een veilige en schaal bare manier [inrichten](about-iot-dps.md#provisioning-process) .

Het inrichten van apparaten is een proces dat uit twee delen bestaat. In het eerste deel wordt de eerste verbinding tot stand gebracht tussen het apparaat en de IoT-oplossing door het apparaat te *registreren* . Het tweede deel past de juiste *configuratie* op het apparaat toe op basis van de specifieke vereisten van de oplossing. Wanneer beide stappen zijn uitgevoerd, is het apparaat volledig *ingericht*. De Device Provisioning Service automatiseert beide stappen en biedt zo een naadloze ervaring voor de inrichting van het apparaat.

Dit artikel bevat een overzicht van de belangrijkste concepten die van toepassing zijn op het beheer van de *service*. Dit artikel is het meest relevant voor personen die deel uitmaken van de stap voor de installatie van de [Cloud](about-iot-dps.md#cloud-setup-step) van het ophalen van een apparaat dat gereed is voor implementatie.

## <a name="service-operations-endpoint"></a>Service-eind punt

Het eind punt van de service bewerkingen is het eind punt voor het beheren van de service-instellingen en het onderhouden van de registratie lijst. Dit eind punt wordt alleen gebruikt door de service beheerder. het wordt niet gebruikt door apparaten.

## <a name="device-provisioning-endpoint"></a>Device Provisioning-eind punt

Het apparaat voor het inrichten van apparaten is het enige eind punt dat alle apparaten gebruiken voor automatische inrichting. De URL is hetzelfde voor alle inrichtings service-instanties, om te voor komen dat apparaten opnieuw moeten worden geflashen met nieuwe verbindings gegevens in de toeleverings keten scenario's. Het ID-bereik zorgt voor isolatie van de Tenant.

## <a name="linked-iot-hubs"></a>Gekoppelde IoT-hubs

De Device Provisioning Service kan alleen apparaten inrichten voor IoT-hubs die eraan zijn gekoppeld. Als u een IoT-hub koppelt aan een exemplaar van de Device Provisioning Service, worden de machtigingen lezen/schrijven van de service voor het Device-REGI ster van de IoT-hub geboden. met de koppeling kan een Device Provisioning-Service een apparaat-ID registreren en de eerste configuratie in het apparaat configureren. Gekoppelde IoT-hubs kunnen zich in elke Azure-regio bevinden. U kunt hubs in andere abonnementen koppelen aan uw inrichtings service.


## <a name="allocation-policy"></a>Toewijzings beleid

De instelling op service niveau die bepaalt hoe Device Provisioning Service apparaten toewijst aan een IoT-hub. Er worden drie soorten toewijzingsbeleid ondersteund:

* **Evenredig gewogen distributie**: gekoppelde IOT-hubs zijn even waarschijnlijk dat apparaten worden ingericht. De standaard instelling. Als u apparaten voor slechts één IoT-hub inricht, kunt u deze instelling bewaren.

* **Laagste latentie**: apparaten worden ingericht naar een IOT-hub met de laagste latentie voor het apparaat. Als meerdere gekoppelde IoT-hubs dezelfde laagste latentie geven, wordt de inrichtings service hashes van apparaten via deze hubs

* **Statische configuratie via de registratie lijst**: specificatie van de gewenste IOT-hub in de registratie lijst heeft voor rang op het toewijzings beleid op service niveau.

* **Aangepast (Azure function gebruiken): met** een aangepast toewijzings beleid hebt u meer controle over hoe apparaten worden toegewezen aan een IOT-hub. Dit wordt bereikt door gebruik te maken van aangepaste code in een Azure-functie om apparaten toe te wijzen aan een IoT-hub. De Device Provisioning Service roept de code van uw Azure-functie aan om alle relevante informatie over het apparaat en de inschrijving van uw code op te geven. De functiecode wordt uitgevoerd en retourneert de gegevens van de IoT-hub die worden gebruikt om het apparaat in te richten.

## <a name="enrollment"></a>Inschrijving

Een inschrijving is de record met apparaten of groepen apparaten die door automatische inrichting kunnen worden geregistreerd. De registratie record bevat informatie over het apparaat of de groep apparaten, waaronder:
- het [Attestation-mechanisme](#attestation-mechanism) dat wordt gebruikt door het apparaat
- de optionele eerste gewenste configuratie
- gewenste IoT-hub
- de gewenste apparaat-ID

Er worden twee soorten inschrijvingen ondersteund door de Device Provisioning Service:

### <a name="enrollment-group"></a>Registratie groep

Een registratie groep is een groep apparaten die een specifiek Attestation-mechanisme delen. Registratie groepen ondersteunen zowel X. 509 als symmetrisch. Alle apparaten in de X. 509-registratie groep bevatten X. 509-certificaten die zijn ondertekend door dezelfde basis of tussenliggende certificerings instantie (CA). Elk apparaat in de groep voor de symmetrische sleutel registratie bevat SAS-tokens die zijn afgeleid van de groeps symmetrische sleutel. De naam van de registratie groep en de certificaat naam moeten alfanumerieke tekens, kleine letters en afbreek streepjes bevatten.

> [!TIP]
> U kunt het beste een registratie groep gebruiken voor een groot aantal apparaten die een gewenste initiële configuratie delen, of voor apparaten die allemaal naar dezelfde Tenant gaan.

### <a name="individual-enrollment"></a>Individuele inschrijving

Een afzonderlijke inschrijving is een vermelding voor één apparaat dat kan worden geregistreerd. Afzonderlijke inschrijvingen kunnen X. 509 Leaf-certificaten of SAS-tokens (van een fysieke of virtuele TPM) gebruiken als Attestation-mechanismen. De registratie-ID in een afzonderlijke inschrijving is alfanumeriek, in kleine letters en kan afbreek streepjes bevatten. Afzonderlijke inschrijvingen hebben mogelijk de gewenste apparaat-id voor IoT Hub die is opgegeven.

> [!TIP]
> We raden u aan om afzonderlijke inschrijvingen te gebruiken voor apparaten die unieke initiële configuraties vereisen, of voor apparaten die alleen kunnen worden geverifieerd met SAS-tokens via TPM-Attestation.


## <a name="attestation-mechanism"></a>Attestation-mechanisme

Een Attestation-mechanisme is de methode die wordt gebruikt om de identiteit van een apparaat te bevestigen. Het Attestation-mechanisme wordt geconfigureerd voor een inschrijvings vermelding en vertelt de inrichtings service welke methode moet worden gebruikt bij het controleren van de identiteit van een apparaat tijdens de registratie.

> [!NOTE]
> IoT Hub gebruikt verificatie schema voor een vergelijkbaar concept in die service.

De Device Provisioning Service ondersteunt de volgende vormen van Attestation:
* **X. 509-certificaten** op basis van de standaard x. 509 certificaat verificatie stroom. Zie [X. 509 Attestation](concepts-x509-attestation.md)(Engelstalig) voor meer informatie.
* **Trusted Platform Module (TPM)** op basis van een nonce-Challenge, met behulp van de TPM-standaard voor sleutels om een ondertekend Shared Access Signature-token (SAS) aan te bieden. Hiervoor is geen fysieke TPM vereist op het apparaat, maar de service verwacht een verklaring te maken met behulp van de goedkeurings sleutel per [TPM-specificatie](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/). Zie [TPM-Attestation](concepts-tpm-attestation.md)(Engelstalig) voor meer informatie.
* **Symmetrische sleutel** op basis van [beveiligings tokens](../iot-hub/iot-hub-devguide-security.md#security-tokens)voor Shared Access Signature (SAS), die een gehashte hand tekening en een Inge sloten verloop bevatten. Zie [symmetrische sleutel Attestation](concepts-symmetric-key-attestation.md)(Engelstalig) voor meer informatie.


## <a name="hardware-security-module"></a>Hardware Security module

De Hardware Security module, of HSM, wordt gebruikt voor veilige, op hardware gebaseerde opslag van geheimen van apparaten en is de veiligste vorm van geheime opslag. Zowel X. 509-certificaten als SAS-tokens kunnen worden opgeslagen in de HSM. Hsm's kan worden gebruikt met beide Attestation-mechanismen die door de inrichtings service worden ondersteund.

> [!TIP]
> We raden u ten zeerste aan een HSM te gebruiken met apparaten om veilig geheimen op uw apparaten op te slaan.

Er kunnen ook geheimen voor apparaten worden opgeslagen in software (geheugen), maar dit is een minder veilige opslag ruimte dan een HSM.



## <a name="id-scope"></a>ID-bereik

Het ID-bereik wordt toegewezen aan een Device Provisioning Service wanneer deze door de gebruiker wordt gemaakt en wordt gebruikt om de specifieke inrichtings service te identificeren die door het apparaat wordt geregistreerd. Het ID-bereik wordt gegenereerd door de service en is onveranderbaar, waardoor uniekheid wordt gegarandeerd.

> [!NOTE]
> Uniekheid is belang rijk voor langlopende implementatie bewerkingen en fusie-en verwervings scenario's.


## <a name="registration"></a>Registratie

Een registratie is de record van een apparaat dat is geregistreerd/ingericht naar een IoT Hub via de Device Provisioning Service. Registratie records worden automatisch gemaakt. ze kunnen worden verwijderd, maar kunnen niet worden bijgewerkt.


## <a name="registration-id"></a>Registratie-ID

De registratie-ID wordt gebruikt voor het identificeren van een apparaatregistratie met de Device Provisioning Service. De apparaat-ID moet uniek zijn in het bereik van de service [-id](#id-scope)voor de inrichting. Elk apparaat moet een registratie-ID hebben. De registratie-ID is alfanumeriek, niet hoofdletter gevoelig en kan speciale tekens bevatten, waaronder dubbele punt, punt, onderstrepings teken en afbreek streepje.

* In het geval van TPM wordt de registratie-ID door de TPM zelf verschaft.
* In het geval van X. 509 Attestation wordt de registratie-ID gegeven als de onderwerpnaam van het certificaat.

## <a name="device-id"></a>Apparaat-ID

De apparaat-ID is de ID zoals deze wordt weer gegeven in IoT Hub. De gewenste apparaat-ID kan in de inschrijvings vermelding worden ingesteld, maar deze hoeft niet te worden ingesteld. Het instellen van de gewenste apparaat-ID wordt alleen ondersteund in afzonderlijke inschrijvingen. Als er geen gewenste apparaat-ID is opgegeven in de registratie lijst, wordt de registratie-ID gebruikt als de apparaat-ID bij het registreren van het apparaat. Meer informatie over [apparaat-id's in IOT hub](../iot-hub/iot-hub-devguide-identity-registry.md).



## <a name="operations"></a>Operations

Bewerkingen zijn de facturerings eenheid van de Device Provisioning Service. Eén bewerking is het volt ooien van een instructie naar de service. Bewerkingen omvatten apparaatregistratie en nieuwe registraties; bewerkingen omvatten ook wijzigingen aan de service zijde, zoals het toevoegen van inschrijvings lijst vermeldingen en het bijwerken van inschrijvings lijst vermeldingen.
