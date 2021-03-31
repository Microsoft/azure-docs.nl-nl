---
title: Azure-IoT Hub Device Provisioning Service-TPM-Attestation
description: Dit artikel bevat een conceptueel overzicht van de TPM-attest stroom met behulp van IoT Device Provisioning Service (DPS).
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 12860629d78391ed271306daba29a51aeb326c1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90531590"
---
# <a name="tpm-attestation"></a>TPM-attestation

IoT Hub Device Provisioning Service is een Helper-service voor IoT Hub die u gebruikt om het inrichten van Zero-Touch-apparaten te configureren voor een opgegeven IoT-hub. Met de Device Provisioning Service kunt u miljoenen apparaten op een veilige manier inrichten.

In dit artikel wordt het identiteits attest proces voor het gebruik van een Trusted Platform Module (TPM) beschreven. Een TPM is een type hardware Security module (HSM). In dit artikel wordt ervan uitgegaan dat u een afzonderlijke, firmware of geïntegreerde TPM gebruikt. Software geëmuleerde Tpm's zijn geschikt voor het maken van prototypen of tests, maar bieden geen hetzelfde beveiligings niveau als discrete, firmware of geïntegreerde Tpm's. Het is niet raadzaam om software Tpm's in productie te gebruiken. Zie [een korte inleiding tot TPM](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf)voor meer informatie over de typen tpm's.

Dit artikel is alleen van toepassing op apparaten met behulp van TPM 2,0 met HMAC-sleutel ondersteuning en de bijbehorende goedkeurings sleutels. Het is niet voor apparaten met X. 509-certificaten voor verificatie. TPM is een branchespecifieke, ISO-standaard van de Trusted Computing Group en u kunt meer lezen over TPM bij de [volledige tpm 2,0 spec](https://trustedcomputinggroup.org/tpm-library-specification/) of de [ISO/IEC 11889 spec](https://www.iso.org/standard/66510.html). In dit artikel wordt ervan uitgegaan dat u bekend bent met open bare en persoonlijke sleutel paren en hoe deze worden gebruikt voor versleuteling.

De Sdk's van het Device Provisioning Service-apparaat verwerken alles wat in dit artikel wordt beschreven voor u. U hoeft verder niets te doen als u de Sdk's op uw apparaten gebruikt. Dit artikel helpt u inzicht te krijgen in wat er gebeurt met uw TPM-beveiligings chip wanneer uw apparaat wordt ingericht en waarom dit zo is beveiligd.

## <a name="overview"></a>Overzicht

Tpm's gebruiken iets de goedkeurings sleutel (EK) als veilige basis voor vertrouwens relaties. De EK is uniek voor de TPM en het wijzigen van het apparaat wordt in feite gewijzigd in een nieuw.

Er is een ander type sleutel dat Tpm's de opslag basis sleutel (SRK) heeft. Een SRK kan worden gegenereerd door de eigenaar van de TPM nadat het eigendom van de TPM is overgenomen. Het eigendom van de TPM is de TPM-specifieke manier om te zeggen dat iemand een wacht woord instelt voor de HSM. Als een TPM-apparaat wordt verkocht aan een nieuwe eigenaar, kan de nieuwe eigenaar eigenaar worden van de TPM om een nieuwe SRK te genereren. De nieuwe SRK-generatie zorgt ervoor dat de vorige eigenaar de TPM niet kan gebruiken. Omdat de SRK uniek is voor de eigenaar van de TPM, kan de SRK worden gebruikt voor het verzegelen van gegevens in de TPM zelf voor die eigenaar. De SRK biedt een sandbox voor de eigenaar om de sleutels op te slaan en biedt toegang tot revocability als het apparaat of de TPM wordt verkocht. Het is een goed uitgangs punt in een nieuw huis: als u eigenaar bent, worden de vergren delingen op de deuren gewijzigd en worden alle meubelen verwijderd door de voor gaande eigen aren (SRK), maar u kunt het adres van het huis (EK) niet wijzigen.

Zodra een apparaat is ingesteld en klaar is voor gebruik, heeft het een EK en een SRK beschikbaar voor gebruik.

![Eigenaar worden van een TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

Een opmerking over het maken van eigenaar van de TPM: het eigendom van een TPM is afhankelijk van veel dingen, waaronder TPM-fabrikant, de set TPM-hulpprogram ma's die worden gebruikt en het besturings systeem van het apparaat. Volg de instructies die relevant zijn voor uw systeem om eigenaar te worden.

De Device Provisioning Service maakt gebruik van het open bare deel van de EK (EK_pub) om apparaten te identificeren en in te schrijven. De leverancier van het apparaat kan de EK_pub lezen tijdens de productie of het laatste testen en de EK_pub uploaden naar de inrichtings service, zodat het apparaat wordt herkend wanneer het verbinding maakt met inrichten. De Device Provisioning service controleert de SRK of eigenaar niet, dus als u de TPM wist, worden klant gegevens gewist, maar worden de EK (en andere leveranciers gegevens) bewaard en wordt het apparaat nog steeds herkend door de Device Provisioning Service wanneer het verbinding maakt met inrichten.

## <a name="detailed-attestation-process"></a>Gedetailleerd Attestation-proces

Wanneer een apparaat met een TPM voor het eerst verbinding maakt met Device Provisioning Service, controleert de service eerst de geleverde EK_pub op de EK_pub die is opgeslagen in de registratie lijst. Als de EK_pubs niet overeenkomen, mag het apparaat niet worden ingericht. Als de EK_pubs overeenkomen, vereist de service dat het apparaat het eigendom bewijst van het privé gedeelte van de EK via een nonce-uitdaging. Dit is een veilige uitdaging die wordt gebruikt om identiteit te bewijzen. De Device Provisioning Service genereert een nonce en versleutelt deze vervolgens met de SRK en vervolgens de EK_pub, die beide door het apparaat worden geleverd tijdens de eerste registratie aanroep. De TPM houdt altijd het persoonlijke deel van de EK veilig. Dit voor komt vervalsing en zorgt ervoor dat SAS-tokens veilig worden ingericht op geautoriseerde apparaten.

Laten we het Attestation-proces gedetailleerd door lopen.

### <a name="device-requests-an-iot-hub-assignment"></a>Apparaat vraagt een IoT Hub toewijzing aan

Eerst maakt het apparaat verbinding met de Device Provisioning Service en de aanvragen om in te richten. Hierbij levert het apparaat de registratie-ID, een ID-bereik en de EK_pub en SRK_pub van de TPM. De versleutelde nonce wordt teruggestuurd naar het apparaat en vraagt het apparaat om de nonce te ontsleutelen en gebruikt om een SAS-token te ondertekenen om opnieuw verbinding te maken en de inrichting te volt ooien.

![Apparaat-aanvragen inrichten](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Nonce-uitdaging

Het apparaat neemt de nonce en maakt gebruik van de privé delen van de EK en SRK om de nonce te ontsleutelen naar de TPM. de volg orde van de nonce-versleuteling wordt vertrouwd van de EK, die onveranderbaar is, naar de SRK, die kan veranderen als een nieuwe eigenaar eigenaar wordt van de TPM.

![De nonce ontsleutelen](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>De nonce-en ontvangst referenties valideren

Het apparaat kan vervolgens een SAS-token ondertekenen met behulp van de ontsleutelde nonce en vervolgens een verbinding met de Device Provisioning Service tot stand brengen met behulp van het ondertekende SAS-token. Als de nonce-uitdaging is voltooid, kan het apparaat door de service worden ingericht.

![Het apparaat brengt een verbinding tot stand met Device Provisioning Service om EK-eigendom te valideren](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Volgende stappen

Het apparaat maakt nu verbinding met IoT Hub en u beveiligt in de kennis dat de sleutels van uw apparaten veilig zijn opgeslagen. Nu u weet hoe de Device Provisioning-Service een apparaat-id veilig verifieert met behulp van TPM, raadpleegt u de volgende artikelen voor meer informatie:

* [Meer informatie over de concepten van het inrichten](about-iot-dps.md#provisioning-process)
* [Ga aan de slag met het automatisch inrichten](./quick-setup-auto-provision.md) met de sdk's om de stroom uit te voeren.
