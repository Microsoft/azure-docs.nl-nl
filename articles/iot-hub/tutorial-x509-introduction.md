---
title: 'Zelfstudie: Cryptografie- en X.509-certificaten voor Azure IoT Hub | Microsoft Docs'
description: 'Zelfstudie: cryptografie en X.509 PKI voor Azure IoT Hub'
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
ms.openlocfilehash: 2c375a02f534572826e1ebd6b8549e59f6e83640
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378377"
---
# <a name="tutorial-understanding-public-key-cryptography-and-x509-public-key-infrastructure"></a>Zelfstudie: Informatie over openbare-sleutelcryptografie en infrastructuur met openbare X.509-sleutels

U kunt X.509-certificaten gebruiken om apparaten te verifiëren bij een Azure IoT Hub. Een certificaat is een digitaal document dat de openbare sleutel van het apparaat bevat en kan worden gebruikt om te controleren of het apparaat is wat het claimt te zijn. X.509-certificaten en certificaat intrekken lijsten (CRL's) worden gedocumenteerd door [RFC 5280.](https://tools.ietf.org/html/rfc5280) Certificaten zijn slechts één onderdeel van een PKI (Public Key Infrastructure) van X.509. Als u inzicht wilt krijgen in de X.509 PKI, moet u inzicht hebben in cryptografische algoritmen, cryptografische sleutels, certificaten en certificeringsinstanties (CERTIFICERINGsinstanties):

* Algoritmen definiëren hoe oorspronkelijke platte-tekstgegevens worden omgezet in **coderingstekst** en terug naar tekst zonder tekst.
* **Sleutels** zijn willekeurige of pseudorandomgegevensreeksen die worden gebruikt als invoer voor een algoritme.
* **Certificaten zijn** digitale documenten die de openbare sleutel van een entiteit bevatten en waarmee u kunt bepalen of het onderwerp van het certificaat is wie of wat het claimt te zijn.
* **Certificeringsinstanties** bevestigen de echtheid van certificaatonderwerpen.

U kunt een certificaat aanschaffen bij een certificeringsinstantie (CA). U kunt ook, voor testen en ontwikkeling, of als u in een zelfstandige omgeving werkt, een zelf-ondertekende basis-CA maken. Als u bijvoorbeeld eigenaar bent van een of meer apparaten en IoT Hub-verificatie test, kunt u uw basis-CA zelf ondertekenen en deze gebruiken om apparaatcertificaten uit te geven. U kunt ook zelf-ondertekende apparaatcertificaten uitgeven. Dit wordt besproken in volgende artikelen.

Voordat we X.509-certificaten gedetailleerder bespreken en gebruiken om apparaten te verifiëren bij een IoT Hub, bespreken we de cryptografie waarop certificaten zijn gebaseerd.

## <a name="cryptography"></a>Cryptografie

Cryptografie wordt gebruikt om informatie en communicatie te beveiligen. Dit wordt meestal gedaan met behulp van cryptografische technieken om tekst zonder tekst (gewone tekst) om te zetten in coderingstekst (gecodeerde tekst) en weer terug te keren. Dit versleutelingsproces wordt versleuteling genoemd. Het omgekeerde proces wordt ontsleuteling genoemd. Cryptografie heeft betrekking op de volgende doelstellingen:

* **Vertrouwelijkheid:** de informatie kan alleen worden begrepen door de beoogde doelgroep.
* **Integriteit:** de informatie kan niet worden gewijzigd in opslag of tijdens overdracht.
* **Niet-weerlegbaarheid:** de maker van informatie kan het maken van de gegevens later niet weigeren.
* **Verificatie:** de afzender en ontvanger kunnen elkaars identiteit bevestigen.

## <a name="encryption"></a>Versleuteling

Voor het versleutelingsproces zijn een algoritme en een sleutel vereist. Het algoritme definieert hoe gegevens worden getransformeerd van platte tekst naar coderingstekst en terug naar tekst zonder tekst. Een sleutel is een willekeurige reeks gegevens die wordt gebruikt als invoer voor het algoritme. Alle beveiliging van het proces is opgenomen in de sleutel. Daarom moet de sleutel veilig worden opgeslagen. De details van de populairste algoritmen zijn echter openbaar beschikbaar.

Er zijn twee soorten versleuteling. Symmetrische versleuteling maakt gebruik van dezelfde sleutel voor zowel versleuteling als ontsleuteling. Asymmetrische versleuteling maakt gebruik van andere, maar wiskundig gerelateerde sleutels om versleuteling en ontsleuteling uit te voeren.

### <a name="symmetric-encryption"></a>Symmetrische versleuteling

Symmetrische versleuteling maakt gebruik van dezelfde sleutel om ongecodeerde tekst te versleutelen in ciphertext en coderingstekst weer te ontsleutelen in niet-versleutelde tekst. De benodigde lengte van de sleutel, uitgedrukt in aantal bits, wordt bepaald door het algoritme. Nadat de sleutel is gebruikt om leesbare tekst te versleutelen, wordt het versleutelde bericht verzonden naar de ontvanger die vervolgens de coderingstekst ontsleutelt. De symmetrische sleutel moet veilig worden verzonden naar de ontvanger. Het verzenden van de sleutel is het grootste beveiligingsrisico wanneer u een symmetrisch algoritme gebruikt.

![Voorbeeld van symmetrische versleuteling](media/tutorial-x509-introduction/symmetric-keys.png)

### <a name="asymmetric-encryption"></a>Asymmetrische versleuteling

Als alleen symmetrische versleuteling wordt gebruikt, is het probleem dat alle partijen in de communicatie over de persoonlijke sleutel moeten beschikken. Het is echter mogelijk dat niet-geautoriseerde derden de sleutel kunnen vastleggen tijdens de overdracht naar gemachtigde gebruikers. U kunt dit probleem oplossen door in plaats daarvan asymmetrische of openbare-sleutelcryptografie te gebruiken.

In asymmetrische cryptografie heeft elke gebruiker twee wiskundig gerelateerde sleutels die een sleutelpaar worden genoemd. De ene sleutel is openbaar en de andere is privé. Het sleutelpaar zorgt ervoor dat alleen de ontvanger toegang heeft tot de persoonlijke sleutel die nodig is om de gegevens te ontsleutelen. In de volgende afbeelding wordt het asymmetrische versleutelingsproces samengevat.

![Voorbeeld van asymmetrische versleuteling](media/tutorial-x509-introduction/asymmetric-keys.png)

1. De ontvanger maakt een openbaar-persoonlijk sleutelpaar en verzendt de openbare sleutel naar een CA. De CA verpakt de openbare sleutel in een X.509-certificaat.

1. De verzendende partij verkrijgt de openbare sleutel van de ontvanger van de CA.

1. De afzender versleutelt niet-versleutelde gegevens met behulp van een versleutelingsalgoritme. De openbare sleutel van de ontvanger wordt gebruikt om versleuteling uit te voeren.

1. De afzender verzendt de coderingstekst naar de ontvanger. Het is niet nodig om de sleutel te verzenden omdat de ontvanger al beschikt over de persoonlijke sleutel die nodig is om de coderingscode te ontsleutelen.

1. De ontvanger ontsleutelt de coderingstekst met behulp van het opgegeven asymmetrische algoritme en de persoonlijke sleutel.

### <a name="combining-symmetric-and-asymmetric-encryption"></a>Symmetrische en asymmetrische versleuteling combineren

Symmetrische en asymmetrische versleuteling kunnen worden gecombineerd om te profiteren van hun relatieve sterke punten. Symmetrische versleuteling is veel sneller dan asymmetrische, maar vanwege de noodzaak van het verzenden van persoonlijke sleutels naar andere partijen is niet zo veilig. Om de twee typen te combineren, kan symmetrische versleuteling worden gebruikt om niet-versleutelde tekst te converteren naar coderingstekst. Asymmetrische versleuteling wordt gebruikt om de symmetrische sleutel uit te wisselen. Dit wordt gedemonstreerd in het volgende diagram.

![Symmetrische en assymetrische versleuteling](media/tutorial-x509-introduction/symmetric-asymmetric-encryption.png)

1. De afzender haalt de openbare sleutel van de ontvanger op.

1. De afzender genereert een symmetrische sleutel en gebruikt deze om de oorspronkelijke gegevens te versleutelen.

1. De afzender gebruikt de openbare sleutel van de ontvanger om de symmetrische sleutel te versleutelen.

1. De afzender verzendt de versleutelde symmetrische sleutel en de coderingstekst naar de beoogde ontvanger.

1. De ontvanger gebruikt de persoonlijke sleutel die overeenkomt met de openbare sleutel van de ontvanger om de symmetrische sleutel van de afzender te ontsleutelen.

1. De ontvanger gebruikt de symmetrische sleutel om de coderingstekst te ontsleutelen.

### <a name="asymmetric-signing"></a>Asymmetrische ondertekening

Asymmetrische algoritmen kunnen worden gebruikt om gegevens te beschermen tegen wijzigingen en om de identiteit van de maker van de gegevens te bewijzen. In de volgende afbeelding ziet u hoe asymmetrische ondertekening helpt om de identiteit van de afzender te bewijzen.

![Voorbeeld van asymmetrische ondertekening](media/tutorial-x509-introduction/asymmetric-signing.png)

1. De afzender geeft niet-versleutelde gegevens door aan een asymmetrisch versleutelingsalgoritme, met behulp van de persoonlijke sleutel voor versleuteling. U ziet dat in dit scenario het gebruik van de persoonlijke en openbare sleutels die in de vorige sectie zijn beschreven, wordt omgekeerd.

1. De resulterende coderingstekst wordt verzonden naar de ontvanger.

1. De ontvanger verkrijgt de openbare sleutel van de oorspronkelijke gebruiker uit een directory.

1. De ontvanger ontsleutelt de coderingstekst met behulp van de openbare sleutel van de oorspronkelijke gebruiker. De resulterende tekst zonder tekst toont de identiteit van de oorspronkelijke persoon aan, omdat alleen de oorspronkelijke persoon toegang heeft tot de persoonlijke sleutel die de oorspronkelijke tekst in eerste instantie heeft versleuteld.

## <a name="signing"></a>Ondertekenen

Digitale ondertekening kan worden gebruikt om te bepalen of de gegevens tijdens de overdracht of at-rest zijn gewijzigd. De gegevens worden doorgegeven via een hash-algoritme, een functie in één manier die een wiskundig resultaat van het opgegeven bericht produceert. Het resultaat wordt een *hash-waarde,* *message digest,* *digest,* *handtekening* of *vingerafdruk genoemd.* Een hash-waarde kan niet worden omgekeerd om het oorspronkelijke bericht te verkrijgen. Omdat een kleine wijziging in het bericht resulteert in een aanzienlijke wijziging in de *vingerafdruk,* kan de hash-waarde worden gebruikt om te bepalen of een bericht is gewijzigd. In de volgende afbeelding ziet u hoe asymmetrische versleuteling en hash-algoritmen kunnen worden gebruikt om te controleren of een bericht niet is gewijzigd.

![Voorbeeld van ondertekening](media/tutorial-x509-introduction/signing.png)

1. De afzender maakt een bericht met tekst zonder tekst.

1. De afzender hasht het bericht zonder tekst om een samenvatting van het bericht te maken.

1. De afzender versleutelt de samenvatting met behulp van een persoonlijke sleutel.

1. De afzender verzendt het bericht met platte tekst en de versleutelde samenvatting naar de beoogde ontvanger.

1. De ontvanger ontsleutelt de samenvatting met behulp van de openbare sleutel van de afzender.

1. De ontvanger voert hetzelfde hash-algoritme uit dat de afzender voor het bericht heeft gebruikt.

1. De ontvanger vergelijkt de resulterende handtekening met de ontsleutelde handtekening. Als de samenvattingen hetzelfde zijn, is het bericht niet gewijzigd tijdens de verzending.

## <a name="next-steps"></a>Volgende stappen

Zie Understanding [X.509 Public Key Certificates (Openbare-sleutelcertificaten voor X.509)](tutorial-x509-certificates.md)voor meer informatie over de velden waar een certificaat uit is.

Zie de volgende onderwerpen als u al veel weet over X.509-certificaten en u testversies wilt genereren die u kunt gebruiken voor verificatie bij uw IoT Hub:

* [Testcertificaten maken met behulp van Microsoft-Supplied Scripts](tutorial-x509-scripts.md)
* [OpenSSL gebruiken om testcertificaten te maken](tutorial-x509-openssl.md)
* [OpenSSL gebruiken om certificaten te Self-Signed testen](tutorial-x509-self-sign.md)

Als u een ca-certificaat (certificeringsinstantie) of een onderliggend CA-certificaat hebt en u het wilt uploaden naar uw IoT-hub en wilt bewijzen dat u de eigenaar bent, gaat u naar Het bezit van een [CA-certificaat](tutorial-x509-prove-possession.md)bewijzen.
