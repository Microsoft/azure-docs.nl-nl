---
title: Azure IoT Hub Device Provisioning Service-X. 509-certificaat Attestation
description: Hierin worden de concepten beschreven die specifiek zijn voor het gebruik van X. 509-certificaat attest met de Device Provisioning Service (DPS) en IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 9eee315aac28847710662b463add7d6e68d8d505
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94967292"
---
# <a name="x509-certificate-attestation"></a>X.509-certificaatattestatie

Dit artikel bevat een overzicht van de concepten van de Device Provisioning Service (DPS) bij het inrichten van apparaten met behulp van X. 509-certificaat Attestation. Dit artikel is relevant voor alle personen bij het ophalen van een apparaat dat klaar is voor implementatie.

X. 509-certificaten kunnen worden opgeslagen in een HSM van de Hardware Security-module.

> [!TIP]
> We raden u ten zeerste aan een HSM te gebruiken met apparaten om veilig geheimen op te slaan, zoals het X. 509-certificaat, op uw apparaten in productie.


## <a name="x509-certificates"></a>X. 509-certificaten

Het gebruik van X. 509-certificaten als Attestation-mechanisme is een uitstekende manier om productie te schalen en het inrichten van apparaten te vereenvoudigen. X. 509-certificaten worden meestal gerangschikt in een vertrouwens keten waarbij elk certificaat in de keten wordt ondertekend door de persoonlijke sleutel van het volgende hogere certificaat, enzovoort, wordt beëindigd in een zelfondertekend basis certificaat. Deze regeling brengt een overgedragen vertrouwens keten tot stand van het basis certificaat dat door een vertrouwde basis certificerings instantie (CA) wordt gegenereerd via elke tussenliggende CA naar het eind entiteit blad-certificaat dat op een apparaat is geïnstalleerd. Zie voor meer informatie [apparaat-verificatie met behulp van X. 509 CA-certificaten](../iot-hub/iot-hub-x509ca-overview.md). 

Vaak vertegenwoordigt de certificaat keten een logische of fysieke hiërarchie die is gekoppeld aan apparaten. Een fabrikant kan bijvoorbeeld:
- een zelfondertekend basis-CA-certificaat uitgeven
- het basis certificaat gebruiken voor het genereren van een uniek tussenliggend CA-certificaat voor elke fabriek
- elk fabrieks certificaat gebruiken voor het genereren van een uniek tussenliggend CA-certificaat voor elke productie lijn in het bedrijf
- en ten slotte het certificaat van de productie lijn gebruiken om een uniek apparaat (eind entiteit) te genereren voor elk apparaat dat op de regel wordt geproduceerd. 

Zie voor meer informatie [conceptuele kennis van X. 509 CA-certificaten in de IOT-industrie](../iot-hub/iot-hub-x509ca-concept.md). 

### <a name="root-certificate"></a>Basis certificaat

Een basis certificaat is een zelfondertekend X. 509-certificaat dat een certificerings instantie (CA) vertegenwoordigt. Het is het ondersteuningabonnementen of vertrouwens anker van de certificaat keten. Basis certificaten kunnen zelf worden uitgegeven door een organisatie of worden aangeschaft bij een basis certificerings instantie. Zie [Get X. 509 ca certificates](../iot-hub/iot-hub-security-x509-get-started.md#get-x509-ca-certificates)(Engelstalig) voor meer informatie. Het basis certificaat kan ook worden aangeduid als een basis-CA-certificaat.

### <a name="intermediate-certificate"></a>Tussenliggend certificaat

Een tussenliggend certificaat is een X. 509-certificaat dat is ondertekend door het basis certificaat (of door een ander tussenliggend certificaat met het basis certificaat in de keten). Het laatste tussenliggende certificaat in een keten wordt gebruikt voor het ondertekenen van het blad certificaat. Een tussenliggend certificaat kan ook worden aangeduid als een tussenliggend CA-certificaat.

##### <a name="why-are-intermediate-certs-useful"></a>Waarom zijn tussenliggende certificaten nuttig?
Tussenliggende certificaten worden op verschillende manieren gebruikt. Tussenliggende certificaten kunnen bijvoorbeeld worden gebruikt voor het groeperen van apparaten op product lijnen, klanten die apparaten, bedrijfs divisies of fabrieken kopen. 

Stel dat contoso een grote onderneming is met een eigen open bare-sleutel infrastructuur (PKI) met behulp van het basis certificaat met de naam *ContosoRootCert*. Elke dochter onderneming van Contoso heeft hun eigen tussenliggende certificaat dat is ondertekend door *ContosoRootCert*. Elke dochter onderneming zal vervolgens hun tussenliggende certificaat gebruiken om hun blad certificaten voor elk apparaat te ondertekenen. In dit scenario kan contoso één DPS-exemplaar gebruiken waarbij *ContosoRootCert* is geverifieerd met het [bewijs van zijn bezit](./how-to-verify-certificates.md). Ze kunnen een registratie groep voor elke dochter maatschappij hebben. Op deze manier hoeft elke afzonderlijke dochter onderneming zich geen zorgen te maken over het verifiëren van certificaten.


### <a name="end-entity-leaf-certificate"></a>Leaf-certificaat van eind entiteit

Het blad certificaat of het certificaat van de eind entiteit identificeert de certificaat houder. Het bevat het basis certificaat in de certificaat keten en nul of meer tussenliggende certificaten. Het blad certificaat wordt niet gebruikt om andere certificaten te ondertekenen. Het apparaat wordt uniek geïdentificeerd voor de inrichtings service en wordt ook wel het certificaat van het apparaat genoemd. Tijdens de verificatie gebruikt het apparaat de persoonlijke sleutel die is gekoppeld aan dit certificaat om te reageren op de vraag naar een bewijs van de eigendom van de service.

Bij blad certificaten die worden gebruikt met een [afzonderlijke inschrijvings](./concepts-service.md#individual-enrollment) vermelding moet de **onderwerpnaam** worden ingesteld op de registratie-id van de afzonderlijke inschrijvings vermelding. Bij blad certificaten die worden gebruikt met een vermelding voor een [registratie groep](./concepts-service.md#enrollment-group) moet de **onderwerpnaam** worden ingesteld op de gewenste apparaat-id die wordt weer gegeven in de **registratie records** voor het geverifieerde apparaat in de registratie groep.

Zie [apparaten verifiëren die zijn ondertekend met X. 509 CA-certificaten](../iot-hub/iot-hub-x509ca-overview.md#authenticating-devices-signed-with-x509-ca-certificates)voor meer informatie.

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Toegang van apparaten tot de inrichtings service beheren met X. 509-certificaten

De inrichtings service beschrijft twee inschrijvings typen die u kunt gebruiken om de toegang tot het apparaat te beheren met het mechanisme X. 509 Attestation:  

- [Afzonderlijke registratie](./concepts-service.md#individual-enrollment) vermeldingen worden geconfigureerd met het certificaat van het apparaat dat is gekoppeld aan een specifiek apparaat. Met deze vermeldingen worden inschrijvingen voor specifieke apparaten beheerd.
- Vermeldingen in de [registratie groep](./concepts-service.md#enrollment-group) zijn gekoppeld aan een specifiek tussenliggend of basis-CA-certificaat. Deze vermeldingen bepalen registraties voor alle apparaten die dat tussenliggende of basis certificaat in hun certificaat keten hebben. 

#### <a name="dps-device-chain-requirements"></a>Vereisten voor de service keten van DPS

Wanneer een apparaat probeert registratie via DPS te maken met behulp van een registratie groep, moet het apparaat de certificaat keten van het blad certificaat verzenden naar een certificaat dat is geverifieerd met een [bewijs van eigendom](how-to-verify-certificates.md). Anders mislukt de verificatie.

Als alleen het basis certificaat wordt gecontroleerd en een tussenliggend certificaat wordt geüpload naar de registratie groep, moet het apparaat de certificaat keten van het blad certificaat helemaal naar het geverifieerde basis certificaat presen teren. Deze certificaat keten bevat tussenliggende certificaten. Verificatie mislukt als DPS de certificaat keten niet kan passeren voor een geverifieerd certificaat.

Denk bijvoorbeeld aan een bedrijf met behulp van de volgende keten voor een apparaat.

![Voor beeld van certificaat keten van apparaat](./media/concepts-x509-attestation/example-device-cert-chain.png) 

Alleen het basis certificaat wordt gecontroleerd en het *intermediate2* -certificaat is geüpload voor de registratie groep.

![Voor beeld van basis verificatie](./media/concepts-x509-attestation/example-root-verified.png) 

Als het apparaat tijdens de inrichting alleen de volgende keten van apparaten verzendt, mislukt de verificatie. Omdat DPS geen verificatie kan uitvoeren, wordt aangenomen dat de geldigheid van het certificaat *intermediate1*

![Voor beeld van een mislukte certificaat keten](./media/concepts-x509-attestation/example-fail-cert-chain.png) 

Als tijdens de inrichting de volledige keten van het apparaat wordt verzonden, kan DPS verificatie van het apparaat proberen.

![Voor beeld van certificaat keten van apparaat](./media/concepts-x509-attestation/example-device-cert-chain.png) 




> [!NOTE]
> Tussenliggende certificaten kunnen ook worden geverifieerd met behulp [van de bewijzen](how-to-verify-certificates.md).


#### <a name="dps-order-of-operations-with-certificates"></a>DPS-volg orde van bewerkingen met certificaten
Wanneer een apparaat verbinding maakt met de inrichtings service, wordt met de service prioriteit gegeven aan meer specifieke inschrijvings vermeldingen voor minder specifieke inschrijvings vermeldingen. Dat wil zeggen, als er een afzonderlijke inschrijving voor het apparaat bestaat, de inrichtings service die vermelding toepast. Als er geen individuele inschrijving voor het apparaat is en een registratie groep voor het eerste tussenliggende certificaat in de certificaat keten van het apparaat bestaat, wordt die vermelding door de service toegepast, enzovoort, in de keten tot aan de hoofdmap. De service past de eerste toepasselijke vermelding toe die wordt gevonden, bijvoorbeeld:

- Als de eerste inschrijvings vermelding is ingeschakeld, wordt het apparaat door de service ingericht.
- Als de eerste inschrijvings vermelding is uitgeschakeld, wordt het apparaat niet door de service ingericht.  
- Als er geen inschrijvings vermelding is gevonden voor de certificaten in de certificaat keten van het apparaat, wordt het apparaat niet door de service ingericht. 

Dit mechanisme en de hiërarchische structuur van certificaat ketens biedt krachtige flexibiliteit bij het beheren van de toegang voor afzonderlijke apparaten en voor groepen apparaten. Stel bijvoorbeeld vijf apparaten met de volgende certificaat ketens: 

- *Apparaat 1*: basis certificaat-> certificaat A-> apparaat 1 certificaat
- *Apparaat 2*: basis certificaat-> certificaat A-> apparaat 2 certificaat
- *Apparaat 3*: basis certificaat-> certificaat A-> apparaat 3
- *Apparaat 4*: basis certificaat-> certificaat B-> apparaat 4-certificaat
- *Apparaat 5*: basis certificaat-> certificaat B-> apparaat 5 certificaat

In eerste instantie kunt u een eenmalige registratie vermelding voor het basis certificaat maken om toegang voor alle vijf apparaten in te scha kelen. Als certificaat B later wordt aangetast, kunt u een uitgeschakelde registratie groep voor certificaat B maken om te voor komen dat *apparaat 4* en *apparaat 5* worden inge schreven. Als er nog steeds een nieuwer *apparaat 3* wordt aangetast, kunt u een uitgeschakelde individuele inschrijvings vermelding voor het certificaat maken. Hiermee wordt de toegang voor *apparaat 3* ingetrokken, maar kan *apparaat 1* en *apparaat 2* worden inge schreven.