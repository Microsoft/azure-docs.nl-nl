---
title: Programma voor Azure Certified Device | Microsoft Docs
description: Meer informatie over het programma Azure Certified device.
author: koichih
ms.author: koichih
ms.date: 09/25/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1fcfc7a9e632e5db1fb809dba7a938c8641c9ddc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92048097"
---
# <a name="what-is-the-azure-certified-device-program"></a>Wat is het Azure Certified Device-programma?

Met het Azure Certified Device-programma kunnen klant oplossingen naadloos samen werken met uw Azure-Services. Het programma gebruikt hulpprogram ma's, services en een Marketplace om de kennis van de branche en de best practices te delen met een community van apparaten en oplossingen bouwers.

Dit programma is ontworpen om:

- **Geef klanten het vertrouwen:** Klanten kunnen apparaten die door micro soft zijn gecertificeerd, kopen voor gebruik met Azure-Services.

- **Help klanten om de juiste apparaten te vinden voor hun oplossing:** Apparaat-Builders kunnen de unieke mogelijkheden van hun apparaten publiceren als onderdeel van het certificerings proces. Klanten kunnen de producten gemakkelijk vinden die aan hun behoeften voldoen.

- **Gecertificeerde apparaten promoten:** Device Builders krijgen meer inzicht, nemen contact met klanten en het gebruik van het logo van het Azure-gecertificeerde apparaat.

In dit artikel wordt beschreven hoe u:

- Onboarding van uw bedrijf naar het Azure Certified Device-programma.
- Bepaal welke certificering van toepassing is op uw apparaat.
- Zoek programma vereisten en andere bronnen om aan de slag te gaan met testen.
- Gebruik de Azure Certified Device Portal om uw apparaat te valideren.

## <a name="onboarding"></a>Onboarding

Als u uw apparaten wilt certificeren op de [Azure Certified Device Portal](https://aka.ms/acdp), moet u de volgende stappen uitvoeren:

1. Zorg ervoor dat uw bedrijf een Azure Active Directory account heeft met behulp van een werk-of school Tenant.
2. Word lid van de [Microsoft Partner Network (MPN)](https://partner.microsoft.com/) met behulp van uw account.
3. Meld u aan bij de [Azure Certified Device Portal](https://aka.ms/acdp) nadat u de MPN hebt toegevoegd.
4. De [programma overeenkomst](https://aka.ms/acdagreement) controleren en ondertekenen op de pagina Bedrijfs profiel

### <a name="company-profile"></a>Bedrijfs profiel

Als u het profiel van uw bedrijf wilt beheren in de Azure Certified Device Portal, selecteert u **bedrijfs profiel**. Het bedrijfs profiel bevat de bedrijfs-URL, het e-mail adres en het logo. Ga akkoord met de programma overeenkomst op deze pagina voordat u doorgaat met de certificerings bewerkingen.

Op de pagina apparaatbeschrijving in de Azure Certified-apparaatinstantie wordt gebruikgemaakt van de bedrijfs profiel informatie.

## <a name="choose-the-certification"></a>De certificering kiezen

Er zijn drie verschillende certificeringen, die allemaal gericht zijn op het leveren van een andere klant waarde. Afhankelijk van het type apparaat dat u bouwt en uw doel publiek, kunt u kiezen welke certificering of certificeringen het meest van toepassing zijn:

### <a name="azure-certified-device"></a>Azure-gecertificeerd apparaat

_Certificering van Azure Certified-apparaten_ valideert dat een apparaat verbinding kan maken met Azure IOT hub en veilig kan inrichten via de Device Provisioning Service (DPS). Deze certificering weerspiegelt de functionaliteit en interoperabiliteit van een apparaat dat een vereiste basis lijn voor geavanceerdere certificeringen is.

- Zie de [certificerings vereisten](https://aka.ms/acdrequirements)voor meer informatie.
- Zie [overzicht van apparaten inrichten](../iot-dps/about-iot-dps.md)voor meer informatie over het gebruik van DPS om uw apparaat te verbinden met Azure IOT hub.

### <a name="iot-plug-and-play"></a>IoT Plug and Play

_IoT Plug en Play-certificering_, een incrementele certificering aan de certificering van het Azure Certified-apparaat, vereenvoudigt het proces van het bouwen van apparaten zonder aangepaste apparaatcode. Met IoT Plug en Play kunnen hardware-partners apparaten bouwen die eenvoudig kunnen worden geïntegreerd met cloud oplossingen op basis van Azure IoT Central en oplossingen van derden.

- Zie de [certificerings vereisten](https://aka.ms/acdiotpnprequirements)voor meer informatie.
- Zie voor meer informatie over het voorbereiden van een apparaat voor IoT-Plug en Play tests [hoe u iot Plug en Play-apparaten kunt certificeren](howto-certify-device.md).

### <a name="edge-managed"></a>Edge beheerd

_Edge Managed Certification_, een incrementele certificering aan de certificering van het Azure Certified-apparaat, richt zich op de standaarden voor Apparaatbeheer voor Azure IOT-apparaten met Windows, Linux of rto's. Op dit moment is dit programma certificering gericht op compatibiliteit met Edge-runtime voor module-implementatie en-beheer.

> [!TIP]
> Dit programma was voorheen bekend als het _IOT Edge-certificerings programma_.

- Zie de [certificerings vereisten](https://aka.ms/acdedgemanagedrequirements)voor meer informatie.
- Zie [IOT Edge Overview](../iot-edge/about-iot-edge.md)voor meer informatie over IOT Edge.
- Zie [IOT Edge ondersteunde systemen](../iot-edge/support.md)voor meer informatie over ondersteunde besturings systemen en containers.

## <a name="use-the-azure-certified-device-portal"></a>De Azure Certified Device Portal gebruiken

In deze sectie wordt een overzicht gegeven van het gebruik van de [Azure Certified Device Portal](https://certify.azure.com). Zie de [hand leiding aan de slag met de portal](https://aka.ms/acdhelp)voor meer informatie.

Als u een apparaat wilt certificeren in het Azure Certified Device-programma, voert u de volgende vier stappen uit:

1. De details van het apparaat opgeven
2. Het apparaat testen
3. De beoordeling verzenden en volt ooien
4. Publiceren naar de catalogus met Azure Certified-apparaten (optioneel)

### <a name="provide-device-details"></a>Details van apparaat opgeven

Voor elk apparaat dat u wilt certificeren, gebruikt u de formulieren in de certificerings Portal om details vast te leggen over de hardware van het apparaat, de installatie-instructies en het marketing materiaal:

- **Apparaatgegevens:** Verzamelt informatie over het apparaat, zoals de naam, beschrijving, hardwaregegevens en het besturings systeem.
- **Aan de slag-hand leiding**: een PDF-document dat een klant kan gebruiken om snel uw product te gebruiken. Er zijn [voorbeeld sjablonen](https://aka.ms/GSTemplate) beschikbaar.
- **Marketing Details:** Bied marketing gegevens van de klant voor uw apparaat, zoals een foto en informatie over de distributeur.
- **Aanvullende branche certificeringen:** Een optionele sectie waarmee u informatie kunt geven over andere certificeringen die het apparaat heeft.

### <a name="test-the-device"></a>Het apparaat testen

Deze fase communiceert met uw apparaat en voert een reeks tests uit nadat het apparaat gebruikmaakt van DPS om verbinding te maken met IoT Hub. Bij voltooiing kunt u een set logboek bestanden weer geven met de test resultaten van uw apparaat.

De certificerings Portal bevat instructies over het maken van verbinding met het IoT Hub-exemplaar dat wordt gebruikt voor het testen. U kunt de DPS-verbinding tot stand brengen via een van de [ondersteunde Attestation-methoden](../iot-dps/concepts-service.md#attestation-mechanism).

Het Azure Certified Device team kan contact opnemen met de opbouw functie voor apparaten voor verdere hand matige validatie van het apparaat.

### <a name="submit-and-publish"></a>Verzenden en publiceren

De laatste vereiste fase is het indienen van het project voor controle. In deze stap wordt een lid van een Azure Certified-teamlid geïnformeerd over het volt ooien van het project, met inbegrip van de details van het apparaat en de marketing, en de aan de slag-hand leiding. Een teamlid kan contact met u opnemen in het e-mail adres van het bedrijf dat eerder is meegeleverd met vragen of aanvragen voor goed keuring heeft bewerkt.

Als uw apparaat verdere hand matige validatie als onderdeel van de certificering vereist, ontvangt u op dit moment een kennisgeving.

Wanneer een apparaat is gecertificeerd, kunt u ervoor kiezen om uw product gegevens te publiceren naar de Azure Certified-apparaatinstantie met behulp van de functie **publiceren op catalogus** op de pagina product overzicht.

## <a name="if-you-have-questions"></a>Als u vragen hebt

Neem contact op met het team [iotcert@microsoft.com](mailto:iotcert@microsoft.com?subject=Azure%20Certified%20Device%20question) Als u vragen hebt over de certificerings Programma's, de certificerings portal of de Azure Certified-stuurprogrammacatalogus.

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht hebt van het Azure Certified Device-programma, is een voorgestelde volgende stap om [te leren hoe u IoT Plug en Play-apparaten kunt certificeren](howto-certify-device.md).