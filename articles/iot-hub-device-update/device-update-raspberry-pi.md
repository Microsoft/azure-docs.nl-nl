---
title: Zelf studie over het bijwerken van apparaten voor Azure IoT Hub met behulp van de Raspberry Pi 3 B + Reference yocto image | Microsoft Docs
description: Ga aan de slag met het bijwerken van het apparaat voor Azure IoT Hub met behulp van de Raspberry Pi 3 B + Reference yocto image.
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: ca689df97e7268a5c0f7c0479e6514b98ffda9f2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443451"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-raspberry-pi-3-b-reference-image"></a>Zelf studie over het bijwerken van apparaten voor Azure IoT Hub met behulp van de Raspberry Pi 3 B +-referentie-afbeelding

Update van het apparaat voor IoT Hub ondersteunt twee soorten updates: op installatie kopie gebaseerd en op basis van een pakket.

Installatie kopieën bieden een hoger vertrouwens niveau in de eind toestand van het apparaat. Het is doorgaans eenvoudiger om de resultaten van een installatie kopie te repliceren: update tussen een pre-productie omgeving en een productie omgeving, omdat deze niet dezelfde uitdagingen als pakketten en hun afhankelijkheden vormt. Als gevolg van hun atoom karakter kan een model van een/B-failover ook eenvoudig worden aangenomen.

In deze zelf studie wordt u begeleid bij de stappen voor het volt ooien van een end-to-end update op basis van een installatie kopie met apparaat bijwerken voor IoT Hub. 

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Afbeelding downloaden
> * Een tag toevoegen aan uw IoT-apparaat
> * Een update importeren
> * Een apparaatgroep maken
> * Een update voor een installatie kopie implementeren
> * De update-implementatie controleren

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten
* Toegang tot een IoT Hub. U wordt aangeraden een S1-laag (Standard) of hoger te gebruiken.

## <a name="download-image"></a>Afbeelding downloaden

Er zijn drie installatie kopieën beschikbaar als onderdeel van de ' assets ' in een bepaalde [Update github-versie](https://github.com/Azure/iot-hub-device-update/releases)van het apparaat. De basis installatie kopie (Adu-base-image) en één update-installatie kopie (Adu-update-image) worden meegeleverd zodat u implementaties kunt uitproberen naar verschillende versies zonder dat de SD-kaart op het apparaat hoeft te worden Flash. Hiervoor moet u de update-installatie kopieën uploaden naar de update van het apparaat voor IoT Hub service, als onderdeel van het importeren.

## <a name="flash-sd-card-with-image"></a>Flash SD-kaart met afbeelding

Installeer de update basis installatie kopie van het apparaat (Adu-base-image) op de SD-kaart die wordt gebruikt in het Raspberry Pi 3 B +-apparaat om uw favoriete hulp programma voor het flashen van besturings systemen te gebruiken.

### <a name="using-bmaptool-to-flash-sd-card"></a>Bmaptool naar Flash SD-kaart gebruiken

1. Als u dit nog niet hebt gedaan, installeert u het `bmaptool` hulp programma.

   ```shell
   sudo apt-get install bmap-tools
   ```

2. Zoek het pad naar de SD-kaart in `/dev` . Het pad moet er ongeveer `/dev/sd*` of zijn `/dev/mmcblk*` . U kunt het `dmesg` hulp programma gebruiken om het juiste pad te vinden.

3. U moet alle gekoppelde partities ontkoppelen voordat u kunt flashen.

   ```shell
   sudo umount /dev/<device>
   ```

4. Zorg ervoor dat u schrijf machtigingen hebt voor het apparaat.

   ```shell
   sudo chmod a+rw /dev/<device>
   ```

5. Optioneel. Voor een snellere flits downloadt u het bimap-bestand samen met het installatie kopie bestand en plaatst u deze in dezelfde map.

6. Knip de SD-kaart.

   ```shell
   sudo bmaptool copy <path to image> /dev/<device>
   ```
   
Voor het bijwerken van apparaten voor Azure IoT Hub-software gelden de volgende licentie voorwaarden:
   * [Update van het apparaat voor IoT Hub licentie](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Delivery Optimization-client licentie](https://github.com/microsoft/do-client/blob/main/LICENSE.md)
   
Lees de licentie voorwaarden voordat u de agent gebruikt. Uw installatie en gebruik zijn uw acceptatie van deze voor waarden. Als u niet akkoord gaat met de licentie voorwaarden, gebruik dan niet de update van het apparaat voor IoT Hub agent.

## <a name="create-device-in-iot-hub-and-get-connection-string"></a>Apparaat maken in IoT Hub en connection string ophalen

Het apparaat moet nu worden toegevoegd aan de Azure-IoT Hub.  In azure IoT Hub wordt een connection string gegenereerd voor het apparaat.

1. Start de update voor het apparaat vanuit het Azure Portal IoT Hub.
2. Maak een nieuw apparaat.
3. Navigeer aan de linkerkant van de pagina naar ' Explorers ' > IoT-apparaten > Selecteer ' nieuw '.
4. Geef een naam op voor het apparaat onder apparaat-ID--zorg ervoor dat het selectie vakje sleutels automatisch genereren is geselecteerd.
5. Selecteer opslaan.
6. Nu keert u terug naar de pagina apparaten en het apparaat dat u hebt gemaakt, moet zich in de lijst bevindt. Selecteer dat apparaat.
7. Selecteer in de weer gave apparaat het pictogram kopiëren naast primaire verbindings reeks.
8. Plak de gekopieerde tekens ergens anders in de onderstaande stappen.
   **Deze gekopieerde teken reeks is uw apparaat Connection String**.

## <a name="provision-connection-string-on-sd-card"></a>connection string inrichten op SD-kaart

1. Zorg ervoor dat de Raspberry Pi3 is verbonden met het netwerk.
2. In Power shell gebruikt u de onderstaande opdracht om SSH te gebruiken in het apparaat
   ```markdown
   ssh raspberrypi3 -l root
      ```
4. Voer de aanmelding in als root en wacht woord moet leeg blijven.
5. Voer de onderstaande opdrachten uit nadat u een SSH-verbinding hebt gemaakt met het apparaat
 
Vervangen `<device connection string>` door uw Connection String
 ```markdown
    echo "connection_string=<device connection string>" > adu-conf.txt  
    echo "aduc_manufacturer=ADUTeam" >> adu-conf.txt
    echo "aduc_model=RefDevice" >> adu-conf.txt
   ```

## <a name="connect-the-device-in-device-update-iot-hub"></a>Het apparaat verbinden in IoT Hub voor het bijwerken van apparaten

1. Selecteer aan de linkerkant van de pagina ' IoT-apparaten ' onder ' Explorers '.
2. Selecteer de koppeling met de naam van uw apparaat.
3. Selecteer aan de bovenkant van de pagina ' apparaat dubbele '.
4. Zoek in de sectie ' gerapporteerd ' van de dubbele eigenschappen van het apparaat naar de versie van de Linux-kernel.
Voor een nieuw apparaat, dat geen update heeft ontvangen van het bijwerken van het apparaat, vertegenwoordigt de waarde [DeviceManagement: DeviceInformation: 1. swVersion](device-update-plug-and-play.md) de firmware versie die op het apparaat wordt uitgevoerd.  Zodra een update is toegepast op een apparaat, gebruikt de apparaat-update de eigenschaps waarde [AzureDeviceUpdateCore: ClientMetadata: 4. installedUpdateId](device-update-plug-and-play.md) om de firmware versie weer te geven die op het apparaat wordt uitgevoerd.
5. De basis-en update-afbeeldings bestanden hebben een versie nummer in de bestands naam.

   ```markdown
   adu-<image type>-image-<machine>-<version number>.<extension>
   ```

Gebruik dat versie nummer in de stap voor het importeren van de update.

## <a name="add-a-tag-to-your-device"></a>Een tag toevoegen aan uw apparaat

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en navigeer naar de IOT hub.

2. Zoek in het navigatie deel venster van IoT-apparaten of IoT Edge in het linkernavigatievenster uw IoT-apparaat op en navigeer naar het dubbele apparaat.

3. Verwijder in het dubbele apparaat alle bestaande waarde voor het bijwerken van het apparaat door ze in te stellen op null.

4. Voeg een nieuwe waarde voor het update label van het apparaat toe, zoals hieronder wordt weer gegeven.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>Update importeren

1. Maak een import manifest volgens deze [instructies](import-update.md).
2. Selecteer de optie apparaat bijwerken onder Automatische Apparaatbeheer in de navigatie balk aan de linkerkant.
3. Selecteer het tabblad updates.
4. Selecteer + nieuwe update importeren.
5. Selecteer het mappictogram of het tekstvak onder ' Selecteer een manifest bestand voor importeren '. U ziet een dialoog venster voor het kiezen van een bestand. Selecteer het import manifest dat u hierboven hebt gemaakt.  Selecteer vervolgens het mappictogram of het tekstvak onder ' Selecteer een of meer update bestanden '. U ziet een dialoog venster voor het kiezen van een bestand. Selecteer het update bestand dat u wilt implementeren op uw IoT-apparaten.
   
   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Scherm opname van de selectie van update bestanden." lightbox="media/import-update/select-update-files.png":::

5. Selecteer het mappictogram of tekstvak onder "een opslag container selecteren". Selecteer vervolgens het gewenste opslag account.

6. Als u al een container hebt gemaakt, kunt u deze opnieuw gebruiken. (Selecteer anders "+ container" om een nieuwe opslag container voor updates te maken.).  Selecteer de container die u wilt gebruiken en klik op selecteren.
  
  :::image type="content" source="media/import-update/container.png" alt-text="Scherm opname van container selectie." lightbox="media/import-update/container.png":::

7. Selecteer verzenden om het import proces te starten.

8. Het import proces wordt gestart en het scherm wordt gewijzigd in de sectie import geschiedenis. Selecteer vernieuwen om de voortgang weer te geven totdat het import proces is voltooid. Afhankelijk van de grootte van de update, kan dit binnen enkele minuten worden voltooid, maar dit kan langer duren.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Scherm opname met update-import volgorde." lightbox="media/import-update/update-publishing-sequence-2.png":::

9. Wanneer de status kolom aangeeft dat het importeren is geslaagd, selecteert u de header gereed om te implementeren. U ziet nu de geïmporteerde update in de lijst.

[Meer informatie](import-update.md) over het importeren van updates.

## <a name="create-update-group"></a>Update groep maken

1. Ga naar de IoT Hub die u eerder hebt verbonden met het update-exemplaar van uw apparaat.

2. Selecteer de optie apparaat bijwerken onder Automatische Apparaatbeheer in de navigatie balk aan de linkerkant.

3. Selecteer het tabblad groepen boven aan de pagina. 

4. Selecteer de knop toevoegen om een nieuwe groep te maken.

5. Selecteer in de lijst het IoT Hub label dat u in de vorige stap hebt gemaakt. Selecteer update groep maken.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Scherm opname van label selectie." lightbox="media/create-update-group/select-tag.PNG":::

[Meer informatie](create-update-group.md) over het toevoegen van tags en het maken van update groepen


## <a name="deploy-update"></a>Update implementeren

1. Zodra de groep is gemaakt, ziet u een nieuwe update die beschikbaar is voor uw apparaatgroep, met een koppeling naar de update onder in behandeling zijnde updates. Mogelijk moet u één keer vernieuwen. 

2. Klik op de beschik bare update.

3. Bevestig dat de juiste groep is geselecteerd als de doel groep. Plan uw implementatie en selecteer vervolgens update implementeren.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Update selecteren" lightbox="media/deploy-update/select-update.png":::

4. De compliance-grafiek weer geven. U ziet dat de update nu wordt uitgevoerd. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="De update wordt uitgevoerd" lightbox="media/deploy-update/update-in-progress.png":::

5. Nadat het apparaat is bijgewerkt, ziet u dat uw nalevings diagram en de update van de implementatie gegevens overeenkomen. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Update geslaagd" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Een update-implementatie bewaken

1. Selecteer het tabblad implementaties boven aan de pagina.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Tabblad implementaties" lightbox="media/deploy-update/deployments-tab.png":::

2. Selecteer de implementatie die u hebt gemaakt om de details van de implementatie weer te geven.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Implementatiedetails" lightbox="media/deploy-update/deployment-details.png":::

3. Selecteer vernieuwen om de meest recente status gegevens weer te geven. Herhaal dit proces totdat de status is gewijzigd in geslaagd.

U hebt nu een voltooide end-to-end update van de installatie kopie voltooid met update voor het bijwerken van het apparaat voor IoT Hub op een Raspberry Pi 3 B +-apparaat. 

## <a name="clean-up-resources"></a>Resources opschonen

Als u het apparaat niet meer nodig hebt, moet u de update account, het exemplaar, het IoT Hub en IoT-apparaat opschonen. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Simulator-referentie agent](device-update-simulator.md)
