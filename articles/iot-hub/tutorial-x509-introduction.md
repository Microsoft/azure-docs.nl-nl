---
title: Zelf studie-informatie over crypto grafie en X. 509-certificaten voor Azure IoT Hub | Microsoft Docs
description: Zelf studie-begrijp crypto grafie en X. 509 PKI voor Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: 88f5803e1d4348b79c7675d627a541ff47700dc0
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630692"
---
# <a name="tutorial-understanding-public-key-cryptography-and-x509-public-key-infrastructure"></a>Zelf studie: informatie over open bare-sleutel cryptografie en X. 509-infra structuur voor open bare sleutels

U kunt X. 509-certificaten gebruiken om apparaten te verifiëren bij een Azure-IoT Hub. Een certificaat is een digitaal document dat de open bare sleutel van het apparaat bevat en kan worden gebruikt om te controleren of het apparaat een claim is. X. 509-certificaten en certificaatintrekkingslijsten (Crl's) worden gedocumenteerd door [RFC 5280](https://tools.ietf.org/html/rfc5280). Certificaten zijn slechts één onderdeel van een X. 509 open bare-sleutel infrastructuur (PKI). Voor een beter begrip van X. 509 PKI moet u de cryptografische algoritmen, cryptografische sleutels, certificaten en certificerings instanties (Ca's) begrijpen:

* Met **algoritmen** definieert u hoe originele Lees bare gegevens worden omgezet in gecodeerde tekst en terug naar Lees bare tekst.
* **Sleutels** zijn wille keurige of Pseudorandom gegevens reeksen die worden gebruikt als invoer voor een algoritme.
* **Certificaten** zijn digitale documenten die de open bare sleutel van een entiteit bevatten en waarmee u kunt bepalen of het onderwerp van het certificaat is wie of wat de claim is.
* **Certificerings instanties** verklaren de echtheid van certificaat houders.

U kunt een certificaat kopen bij een certificerings instantie (CA). U kunt ook voor testen en ontwikkeling of als u in een zelfstandige omgeving werkt, een zelfondertekende basis-CA maken. Als u bijvoorbeeld eigenaar bent van een of meer apparaten en IoT hub-verificatie wilt testen, kunt u uw basis-CA zelf ondertekenen en gebruiken om de certificaten van apparaten uit te geven. U kunt ook zelfondertekende apparaten uitgeven. Dit wordt in de volgende artikelen besproken.

Voordat u met behulp van X. 509-certificaten meer gedetailleerde informatie over het verifiëren van apparaten bij een IoT Hub, bespreken we de crypto grafie waarop certificaten zijn gebaseerd.

## <a name="cryptography"></a>Cryptografie

Crypto grafie wordt gebruikt voor het beveiligen van informatie en communicatie. Dit wordt doorgaans gedaan door gebruik te maken van cryptografische technieken voor het versleutelen van tekst zonder opmaak (normale teken) in gecodeerde tekst (gecodeerd) en weer terug. Dit proces wordt versleuteling genoemd. Het omgekeerde proces heet ontsleutelen. Crypto grafie is betrokken bij de volgende doel stellingen:

* **Vertrouwelijkheid**: de informatie kan alleen worden geïnterpreteerd door de beoogde doel groep.
* **Integriteit**: de informatie kan niet worden gewijzigd in opslag of onderweg.
* **Niet-afwijzing**: de maker van de informatie kan het maken van de gegevens later niet opzeggen.
* **Verificatie**: de afzender en de ontvanger kunnen de identiteit van elk ander verifiëren.

## <a name="encryption"></a>Versleuteling

Het versleutelings proces vereist een algoritme en een sleutel. Met het algoritme wordt gedefinieerd hoe gegevens worden getransformeerd van tekst zonder opmaak naar een gecodeerde tekst en terug naar de Lees bare sleutel. Een sleutel is een wille keurige teken reeks die wordt gebruikt als invoer voor de algoritme. Alle beveiliging van het proces bevindt zich in de sleutel. Daarom moet de sleutel veilig worden opgeslagen. De details van de populairste algoritmen zijn echter openbaar beschikbaar.

Er zijn twee soorten versleuteling. Symmetrische versleuteling gebruikt dezelfde sleutel voor versleuteling en ontsleuteling. Asymmetrische versleuteling gebruikt verschillende, wiskundige gerelateerde sleutels voor het uitvoeren van versleuteling en ontsleuteling.

### <a name="symmetric-encryption"></a>Symmetrische versleuteling

Symmetrische versleuteling maakt gebruik van dezelfde sleutel voor het versleutelen van tekst zonder opmaak in gecodeerde tekst en ontsleuteling van gecodeerde sleutels in de De vereiste lengte van de sleutel, uitgedrukt in aantal bits, wordt bepaald door de algoritme. Nadat de sleutel is gebruikt voor het versleutelen van de Lees bare tekst, wordt het versleutelde bericht verzonden naar de ontvanger die vervolgens de gecodeerde sleutel decodeert. De symmetrische sleutel moet veilig worden verzonden naar de ontvanger. Het verzenden van de sleutel is het grootste beveiligings risico wanneer u een symmetrisch algoritme gebruikt.

![Voor beeld van symmetrische versleuteling](media/tutorial-x509-introduction/symmetric-keys.png)

### <a name="asymmetric-encryption"></a>Asymmetrische versleuteling

Als er alleen symmetrische versleuteling wordt gebruikt, is het probleem dat alle partijen bij de communicatie over de persoonlijke sleutel beschikken. Het is echter mogelijk dat niet-geautoriseerde derden de sleutel kunnen vastleggen tijdens de verzen ding naar geautoriseerde gebruikers. U kunt dit probleem oplossen door in plaats daarvan asymmetrische of open bare-sleutel cryptografie te gebruiken.

Bij asymmetrische crypto grafie heeft elke gebruiker twee wiskundige gerelateerde sleutels die een sleutel paar worden genoemd. Een sleutel is openbaar en de andere sleutel is privé. Het sleutel paar zorgt ervoor dat alleen de ontvanger toegang heeft tot de persoonlijke sleutel die nodig is om de gegevens te ontsleutelen. In de volgende afbeelding ziet u een overzicht van het asymmetrische versleutelings proces.

![Voor beeld van asymmetrische versleuteling](media/tutorial-x509-introduction/asymmetric-keys.png)

1. De ontvanger maakt een openbaar-persoonlijk sleutel paar en stuurt de open bare sleutel naar een certificerings instantie. De CA verpakt de open bare sleutel in een X. 509-certificaat.

1. De afzender verkrijgt de open bare sleutel van de ontvanger van de certificerings instantie.

1. De afzender versleutelt gegevens zonder opmaak met behulp van een versleutelings algoritme. De open bare sleutel van de ontvanger wordt gebruikt om versleuteling uit te voeren.

1. De afzender verzendt de gecodeerde tekst naar de ontvanger. Het is niet nodig om de sleutel te verzenden omdat de ontvanger de persoonlijke sleutel al nodig heeft om de gecodeerde tekst te ontsleutelen.

1. De ontvanger ontsleutelt de gecodeerde tekst met behulp van het opgegeven asymmetrische algoritme en de persoonlijke sleutel.

### <a name="combining-symmetric-and-asymmetric-encryption"></a>Combi neren van symmetrische en asymmetrische versleuteling

Symmetrische en asymmetrische versleuteling kunnen worden gecombineerd om te profiteren van hun relatieve kracht. Symmetrische versleuteling is veel sneller dan asymmetrisch, maar vanwege de nood zaak om persoonlijke sleutels naar andere partijen te verzenden, is niet zo veilig. Als u de twee typen samen wilt combi neren, kan symmetrische versleuteling worden gebruikt voor het converteren van tekst zonder opmaak naar een gecodeerde sleutel. Asymmetrische versleuteling wordt gebruikt voor het uitwisselen van de symmetrische sleutel. Dit wordt getoond in het volgende diagram.

![Symmetrische en Assymetric-versleuteling](media/tutorial-x509-introduction/symmetric-asymmetric-encryption.png)

1. De afzender haalt de open bare sleutel van de ontvanger op.

1. De afzender genereert een symmetrische sleutel en gebruikt deze om de oorspronkelijke gegevens te versleutelen.

1. De afzender gebruikt de open bare sleutel van de ontvanger voor het versleutelen van de symmetrische sleutel.

1. De verzender verzendt de versleutelde symmetrische sleutel en de gecodeerde tekst naar de bedoelde ontvanger.

1. De ontvanger gebruikt de persoonlijke sleutel die overeenkomt met de open bare sleutel van de ontvanger voor het ontsleutelen van de symmetrische sleutel van de afzender.

1. De ontvanger gebruikt de symmetrische sleutel om de gecodeerde tekst te ontsleutelen.

### <a name="asymmetric-signing"></a>Asymmetrische ondertekening

Asymmetrische algoritmen kunnen worden gebruikt om gegevens te beveiligen tegen wijzigingen en de identiteit van de gegevens Maker te bewijzen. In de volgende afbeelding ziet u hoe asymmetrisch ondertekenen u helpt de identiteit van de afzender te bewijzen.

![Voor beeld van asymmetrische ondertekening](media/tutorial-x509-introduction/asymmetric-signing.png)

1. De afzender geeft tekst zonder opmaak door middel van een asymmetrische versleutelings algoritme, met behulp van de persoonlijke sleutel voor versleuteling. U ziet dat in dit scenario het gebruik van de persoonlijke en open bare sleutels die in de voor gaande sectie worden beschreven, worden teruggedraaid. Dit is gedetailleerde asymmetrische versleuteling.

1. De resulterende gecodeerde tekst wordt verzonden naar de ontvanger.

1. De ontvanger haalt de open bare sleutel van de maker op uit een map.

1. De ontvanger ontsleutelt de gecodeerde tekst met behulp van de open bare sleutel van de maker. De resulterende Lees bare tekst bewijst de identiteit van de maker, omdat alleen de maker toegang heeft tot de persoonlijke sleutel die de oorspronkelijke tekst oorspronkelijk versleutelde.

## <a name="signing"></a>Ondertekenen

Digitale ondertekening kan worden gebruikt om te bepalen of de gegevens zijn gewijzigd tijdens de overdracht of in rust. De gegevens worden door gegeven via een hash-algoritme, een eenrichtings functie die een wiskundig resultaat van het gegeven bericht produceert. Het resultaat wordt een *hash-waarde*, een *Message Digest*, een samen *vatting*, een *hand tekening* of een *vinger afdruk* genoemd. Een hash-waarde kan niet worden omgekeerd om het oorspronkelijke bericht te verkrijgen. Omdat een kleine wijziging in het bericht resulteert in een belang rijke wijziging in de *vinger afdruk*, kan de hash-waarde worden gebruikt om te bepalen of een bericht is gewijzigd. In de volgende afbeelding ziet u hoe asymmetrische versleuteling en hash-algoritmen kunnen worden gebruikt om te controleren of een bericht niet is gewijzigd.

![Voor beeld van ondertekening](media/tutorial-x509-introduction/signing.png)

1. De afzender maakt een bericht met een lees bare tekst.

1. De afzender hasheert het Lees bare bericht om een Message Digest te maken.

1. De afzender versleutelt de samen vatting met een persoonlijke sleutel.

1. De afzender verzendt het Lees bare bericht en de versleutelde samen vatting naar de beoogde ontvanger.

1. De ontvanger ontsleutelt de Digest met de open bare sleutel van de afzender.

1. De ontvanger voert hetzelfde hash-algoritme uit dat de afzender via het bericht heeft gebruikt.

1. De ontvanger vergelijkt de resulterende hand tekening met de ontsleutelde hand tekening. Als de samen vattingen hetzelfde zijn, is het bericht niet gewijzigd tijdens de verzen ding.

## <a name="next-steps"></a>Volgende stappen

Zie informatie over de [open bare-sleutel certificaten van X. 509](tutorial-x509-certificates.md)voor meer informatie over de velden waaruit een certificaat is opgebouwd.

Als u al een groot aantal 509-certificaten kent en u test versies wilt genereren die u kunt gebruiken om uw IoT Hub te verifiëren, raadpleegt u de volgende onderwerpen:

* [Microsoft-Supplied-scripts gebruiken om test certificaten te maken](tutorial-x509-scripts.md)
* [OpenSSL gebruiken om test certificaten te maken](tutorial-x509-openssl.md)
* [Self-Signed test certificaten maken met behulp van OpenSSL](tutorial-x509-self-sign.md)

Als u een certificaat van een certificerings instantie of een onderliggend CA-certificaat hebt en u het wilt uploaden naar uw IoT-hub en wilt bewijzen dat u de eigenaar bent, raadpleegt u het [bewijs van een CA-certificaat](tutorial-x509-prove-possession.md).
