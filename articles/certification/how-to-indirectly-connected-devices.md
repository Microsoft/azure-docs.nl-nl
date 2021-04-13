---
title: Certifing-apparaat bundels en indirect verbonden apparaten
titleSuffix: Azure Certified
description: Zie een indirect verbonden apparaat verzenden voor certificering.
author: cbroad
ms.author: cbroad
ms.date: 02/23/2021
ms.topic: how-to
ms.service: certification
ms.openlocfilehash: 3a4fd2838c0ddf6d7d03d68f105fc59471b77dea
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304474"
---
# <a name="device-bundles-and-indirectly-connected-devices"></a>Apparaat-en indirect verbonden apparaten

Om apparaten te ondersteunen die met Azure communiceren via een apparaat, SaaS-of PaaS-aanbieding, onze inrichtings Portal ( https://certify.azure.com/) , en de catalogus met apparaten), kunt u https://devicecatalog.azure.com) de combi natie van bundels en afhankelijkheden gebruiken om deze apparaten te promoten en in te scha kelen voor het programma Azure Certified device.

Afhankelijk van de aangeboden product lijn en services, is voor uw situatie een combi natie van de volgende stappen vereist:


![Project afhankelijkheden maken](./media/indirect-connected-device/picture-1.png )
## <a name="sensors-and-indirect-devices"></a>Sens oren en indirecte apparaten
Veel Sens oren vereisen een apparaat om verbinding te maken met Azure. Daarnaast hebt u mogelijk meerdere compatibele apparaten die met het sensor apparaat werken. **Als u deze scenario's wilt ondersteunen, moet u eerst de apparaten certificeren voordat u de sensor certificeert waarmee informatie wordt door gegeven.**

Voor beeld van een matrix voor het verzenden van combi Naties van verzend combinaties ![](./media/indirect-connected-device/picture-2.png )

Voor het certificeren van uw sensor, waarvoor een afzonderlijk apparaat is vereist:
1.  Certificeer eerst [het apparaat](https://certify.azure.com) en publiceer het naar de Azure Certified-apparaatinstantie
    - Als u meerdere, compatibele passthrough-apparaten (zoals in het bovenstaande voor beeld) hebt, moet u deze afzonderlijk voor certificering verzenden en publiceren naar de catalogus
2.  Wanneer de sensor is verbonden via het apparaat, dient u de sensor voor certificering in
    * Stel op het tabblad ' afhankelijkheden ' van de sectie ' Details van apparaat ' de volgende waarden in
        * Afhankelijkheids type = "hardware-gateway"
        * URL van afhankelijkheid = "URL-koppeling naar het apparaat op de catalogus"
        * Wordt gebruikt tijdens het testen = "Yes"
        * Voeg eventuele klant gerichte opmerkingen toe aan een gebruiker die de product beschrijving in de catalogus van het apparaat ziet. (voor beeld: ' Series 100-apparaten zijn vereist voor Sens oren om verbinding te maken met Azure ')

3.  Als u meer apparaten hebt die u wilt toevoegen als optioneel voor dit apparaat, kunt u + extra afhankelijkheid toevoegen selecteren. Volg dezelfde richt lijnen en houd er rekening mee dat deze niet is gebruikt tijdens het testen. Zorg er in de klant gerichte opmerkingen voor dat er andere apparaten aan deze sensor zijn gekoppeld (als alternatief voor het apparaat dat is gebruikt tijdens de test).

![Alternatieve tekst](./media/indirect-connected-device/picture-3.png "Type hardware-afhankelijkheid")

## <a name="paas-and-saas-offerings"></a>PaaS-en SaaS-aanbiedingen
Als onderdeel van uw product portfolio hebt u mogelijk apparaten die u certificeert, maar uw apparaat vereist ook andere services van uw bedrijf of andere bedrijven van derden. Voer de volgende stappen uit om deze afhankelijkheid toe te voegen:
1. Het verzend proces voor uw apparaat starten
2. Stel op het tabblad ' afhankelijkheden ' de volgende waarden in
    - Afhankelijkheids type = "software service"
    - Service naam = "[uw product naam]"
    - Dependency URL = "URL-koppeling naar een product pagina waarin de service wordt beschreven"
    - Voeg eventuele opmerkingen van klanten toe die moeten worden toegevoegd aan een gebruiker die de product beschrijving in de Azure Certified-Stuurprogrammacatalogus ziet
3. Als u andere software, services of hardware-afhankelijkheden hebt toegevoegd die u als optioneel wilt toevoegen voor dit apparaat, kunt u ' + extra afhankelijkheid toevoegen ' selecteren en dezelfde richt lijnen volgen.

![Type software-afhankelijkheid](./media/indirect-connected-device/picture-4.png )

## <a name="bundled-products"></a>Gebundelde producten
Gebundelde product lijsten zijn gewoon de geslaagde certificering van een apparaat met andere onderdelen die als onderdeel van de bundel worden verkocht in één product lijst. U hebt de mogelijkheid om een apparaat met extra onderdelen, zoals een temperatuur sensor en een camera #1, te verzenden, of u kunt een aanraak sensor verzenden die een passthrough-apparaat (#2) bevat. U hebt de mogelijkheid om meerdere onderdelen toe te voegen aan uw vermelding met behulp van de functie ' onderdeel '.

Als u dit wilt doen, formatteert u de installatie kopie van het product om aan te geven dat dit product wordt geleverd met andere onderdelen.  Als voor uw bundel extra services zijn vereist om te certificeren, moet u bovendien deze identificeren via de service afhankelijkheid.
Voor beeld van matrix van gebundelde producten

![Voor beeld van bundel verzenden](./media/indirect-connected-device/picture-5.png )

Zie de [Help-documentatie](./how-to-using-the-components-feature.md)voor een gedetailleerde beschrijving van het gebruik van de functionaliteit van het onderdeel in de Azure Certified Device Portal. 

Als een apparaat een passthrough-apparaat met een afzonderlijke sensor in hetzelfde product is, maakt u één onderdeel om het passthrough-apparaat weer te geven en een ander onderdeel om de sensor weer te geven. Onderdelen kunnen worden toegevoegd aan het project op het tabblad product details van de sectie Details van apparaat:

![Onderdelen toevoegen](./media/indirect-connected-device/picture-6.png )

Voor het passthrough-apparaat stelt u het onderdeel type in als een product dat klaar is voor de klant en vult u de overige velden in die relevant zijn voor uw product. Voorbeeld:

![Onderdeel Details](./media/indirect-connected-device/picture-7.png )

Voeg voor de sensor een tweede onderdeel toe, waarbij u het onderdeel type instelt als een afzonderlijke rand-en bijlage methode. Voorbeeld:

![Details van tweede onderdeel](./media/indirect-connected-device/picture-8.png )

Nadat het sensor onderdeel is gemaakt, bewerkt u de details, gaat u naar het tabblad Sens oren en voegt u vervolgens de sensor gegevens toe. Voorbeeld:

![Sensor Details](./media/indirect-connected-device/picture-9.png )

Voltooi de details van uw projecten en dien uw apparaat in als normaal certificaat.

