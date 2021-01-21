---
title: Apparaatinformatie importeren
description: Defender voor IoT Sens oren bewaken en analyseren gespiegeld verkeer. In dergelijke gevallen wilt u mogelijk gegevens importeren naar verrijke informatie op apparaten die al zijn gedetecteerd.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 7cb805f60ba9feb0ae2d1483b2ab2df4e03639d8
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625411"
---
# <a name="import-device-information-to-a-sensor"></a>Apparaatgegevens importeren in een sensor

Een Azure Defender voor IoT-sensor bewaakt en analyseert gespiegeld verkeer. In sommige gevallen, vanwege een netwerkconfiguratiebeleid van een organisatie, worden bepaalde gegevens mogelijk niet verzonden.

In dergelijke gevallen wilt u mogelijk gegevens importeren naar verrijke informatie over apparaten die al zijn gedetecteerd. Er zijn twee opties beschikbaar voor het importeren van informatie over Sens oren:

- **Importeren van de kaart**: werk de apparaatnaam, het type, de groep of de Purdue-laag bij naar de kaart.

- **Importeren uit import instellingen**: besturings systeem van het apparaat, het IP-adres, het patch niveau of de autorisatie status importeren.

## <a name="import-from-the-map"></a>Importeren uit de kaart

In deze sectie wordt beschreven hoe u apparaatnamen, typen, groepen of Purdue lagen kunt importeren in de apparaattoewijzing. U kunt dit doen vanuit de kaart.

Dit zijn de import vereisten:

- **Namen**: mogen Maxi maal 30 tekens bevatten.

-  Purdue- **laag**: gebruik de opties die worden weer gegeven in het dialoog venster **Apparaateigenschappen** . (Klik met de rechter muisknop op het apparaat en selecteer **Eigenschappen weer geven**.)

- **Apparaatgroep**: een nieuwe groep van Maxi maal 30 tekens maken. 

> [!NOTE]
> Importeer de gegevens die u hebt geëxporteerd van de ene sensor naar een andere sensor om conflicten te voor komen.

Importeren:

1. Selecteer **apparaten** in het menu aan de zijkant.

2. Selecteer in de rechter bovenhoek van het venster **apparaten** :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="Scherm opname van het venster met apparaten.":::

3. Selecteer **apparaten exporteren**. Er wordt een uitgebreid scala aan gegevens weer gegeven in het geëxporteerde bestand. Deze informatie omvat protocollen die het apparaat gebruikt en de autorisatie status van het apparaat.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="De informatie in het geëxporteerde bestand.":::

4. Wijzig in het CSV-bestand alleen de apparaatnaam, het type, de groep en de Purdue-laag. Sla het bestand op. 

   Gebruik de kapitalisatie standaarden die worden weer gegeven in het geëxporteerde bestand. Gebruik bijvoorbeeld voor de laag Purdue alle eerste letters.

5. Selecteer in de vervolg keuzelijst **importeren/exporteren** in het venster **apparaat** de optie **apparaten importeren**.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Apparaten importeren via het venster apparaat.":::

6. Selecteer **apparaten importeren** en selecteer het CSV-bestand dat u wilt importeren. De import status berichten worden weer gegeven op het scherm totdat het dialoog venster **apparaten importeren** wordt gesloten.

## <a name="import-from-import-settings"></a>Importeren uit import instellingen

In deze sectie wordt beschreven hoe u het IP-adres, het besturings systeem, het patch niveau of de autorisatie status van het apparaat in de apparaattoewijzing kunt importeren. U doet dit in het dialoog venster **Instellingen importeren** .

Het IP-adres, het besturings systeem en het patch niveau importeren:

1. Down load het bestand [devices_info_2.2.8 en up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) van het [Help Center](https://cyberx-labs.zendesk.com/hc/en-us) en voer de volgende gegevens in:

   - **IP-adres**: Voer het IP-adres van het apparaat in.

   - **Besturings systeem**: Selecteer een optie in de vervolg keuzelijst.

   - **Laatste update**: gebruik de notatie JJJJ-MM-DD.

     :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="Het scherm opties.":::

2. Selecteer in het menu aan de zijkant **Instellingen importeren**.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Importeer uw instellingen.":::

3. Als u de vereiste configuratie wilt uploaden, selecteert u in de sectie **apparaatgegevens** de optie **toevoegen** en UPLOADt u het CSV-bestand dat u hebt voor bereid.

De autorisatie status importeren:

1. Down load het [authorized_devices.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) -bestand en sla het op in het Help Center van Defender voor IOT. Controleer of u het bestand hebt opgeslagen als CSV.

2. Voer de gegevens in als:

   - **IP-adres**: het IP-adres van het apparaat.

   - **Naam**: de naam van de geautoriseerde apparaat. Zorg ervoor dat de namen juist zijn. Namen die aan de apparaten in de geïmporteerde lijst worden gegeven, overschrijven de namen die worden weer gegeven in de apparaattoewijzing.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="Excel-bestanden met een lijst met geïmporteerde apparaten.":::

3. Selecteer in het menu aan de zijkant **Instellingen importeren**.

4. Selecteer in de sectie **geautoriseerde apparaten** de optie **toevoegen** en upload het CSV-bestand dat u hebt opgeslagen.

Wanneer de gegevens worden geïmporteerd, ontvangt u waarschuwingen over niet-geautoriseerde apparaten voor alle apparaten die niet in deze lijst worden weer gegeven.

## <a name="import-device-information-to-the-sensor"></a>Apparaatgegevens importeren naar de sensor

De sensor bewaakt en analyseert gespiegeld verkeer. In sommige gevallen, vanwege een netwerkconfiguratiebeleid van een organisatie, worden bepaalde gegevens mogelijk niet verzonden.

In dergelijke gevallen wilt u mogelijk gegevens importeren naar verrijke apparaatgegevens op apparaten die al zijn gedetecteerd. Er zijn twee opties beschikbaar voor het importeren van informatie over Sens oren:

- **Importeren van de kaart**: werk de apparaatnaam, het type, de groep of de Purdue-laag bij naar de kaart.

- **Importeren uit import instellingen**: besturings systeem van het apparaat, het IP-adres, het patch niveau of de autorisatie status importeren.

### <a name="import-from-the-map"></a>Importeren uit de kaart

In deze sectie wordt beschreven hoe u apparaatnamen, typen, groepen of Purdue lagen kunt importeren in de apparaattoewijzing. U kunt dit doen vanuit de kaart.

Dit zijn de import vereisten:

- **Namen**: mogen Maxi maal 30 tekens bevatten.

-  Purdue- **laag**: gebruik de opties die worden weer gegeven in het dialoog venster **Apparaateigenschappen** . (Klik met de rechter muisknop op het apparaat en selecteer **Eigenschappen weer geven**.)

- **Apparaatgroep**: een nieuwe groep van Maxi maal 30 tekens maken. 

> [!NOTE]
> Importeer de gegevens die u hebt geëxporteerd van de ene sensor naar een andere sensor om conflicten te voor komen.

Importeren:

1. Selecteer **apparaten** in het menu aan de zijkant.

2. Selecteer in de rechter bovenhoek van het venster **apparaten** :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="Het venster waaruit u uw apparaat wilt kiezen.":::

3. Selecteer **apparaten exporteren**. Er wordt een uitgebreid scala aan gegevens weer gegeven in het geëxporteerde bestand. Voor beelden zijn protocollen die het apparaat gebruikt en de autorisatie status van het apparaat.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="De informatie in het geëxporteerde bestand.":::

4. Wijzig in het CSV-bestand alleen de apparaatnaam, het type, de groep en de Purdue-laag. Sla het bestand op. 

   Gebruik de kapitalisatie standaarden die worden weer gegeven in het geëxporteerde bestand. Gebruik bijvoorbeeld voor de laag Purdue alle eerste letters.

5. Selecteer in de vervolg keuzelijst **importeren/exporteren** in het venster **apparaat** de optie **apparaten importeren**.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Importeer uw apparaten.":::

6. Selecteer **apparaten importeren** en selecteer het CSV-bestand dat u wilt importeren. De import status berichten worden weer gegeven op het scherm totdat het dialoog venster **apparaten importeren** wordt gesloten.

### <a name="import-from-import-settings"></a>Importeren uit import instellingen 

In deze sectie wordt beschreven hoe u het IP-adres, het besturings systeem, het patch niveau of de autorisatie status van het apparaat in de apparaattoewijzing kunt importeren. U doet dit in het dialoog venster **Instellingen importeren** .

Het IP-adres, het besturings systeem en het patch niveau importeren:

1. Down load het bestand [devices_info_2.2.8 en up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) van het [Help Center](https://cyberx-labs.zendesk.com/hc/en-us) en voer de volgende gegevens in:

   - **IP-adres**: het IP-adres van het apparaat.

   - **Besturings systeem**: Selecteer een optie in de vervolg keuzelijst.

   - **Laatste update**: gebruik de notatie JJJJ-MM-DD.

    :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="De inhoud van het scherm.":::

2. Selecteer in het menu aan de zijkant **Instellingen importeren**.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Vul het scherm instellingen voor importeren in.":::

3. Als u de vereiste configuratie wilt uploaden, selecteert u in de sectie **apparaatgegevens** de optie **toevoegen** en UPLOADt u het CSV-bestand dat u hebt voor bereid.

De autorisatie status importeren:

1. Down load het [authorized_devices.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) -bestand en sla het op in het Help Center van Defender voor IOT. Controleer of u het bestand hebt opgeslagen als CSV.

2. Voer de gegevens in als:

   - **IP-adres**: het IP-adres van het apparaat.

   - **Naam**: de naam van de geautoriseerde apparaat. Controleer of de namen juist zijn. Namen die aan de apparaten in de geïmporteerde lijst worden gegeven, overschrijven de namen die worden weer gegeven in de apparaattoewijzing.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="De lijst importeren naar de apparaattoewijzing.":::

3. Selecteer in het menu aan de zijkant **Instellingen importeren**.

4. Selecteer in de sectie **geautoriseerde apparaten** de optie **toevoegen** en upload het CSV-bestand dat u hebt opgeslagen.

Wanneer de gegevens worden geïmporteerd, ontvangt u waarschuwingen over niet-geautoriseerde apparaten voor alle apparaten die niet in deze lijst worden weer gegeven.

## <a name="see-also"></a>Zie ook

[Controleren welk verkeer wordt bewaakt](how-to-control-what-traffic-is-monitored.md)

[Alle sensordetecties in een apparaatinventaris onderzoeken](how-to-investigate-sensor-detections-in-a-device-inventory.md)
