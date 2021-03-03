---
title: Zelf studie over het bijwerken van apparaten voor Azure IoT Hub met behulp van de Ubuntu Server 18,04 x64-pakket agent | Microsoft Docs
description: Ga aan de slag met het bijwerken van het apparaat voor Azure IoT Hub met behulp van de Ubuntu Server 18,04 x64 package agent.
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 19be3b141fb663ea0f4f11d811bf6ffc33252504
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664697"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-server-1804-x64-package-agent"></a>Zelf studie over het bijwerken van apparaten voor Azure IoT Hub met behulp van de Ubuntu Server 18,04 x64-pakket agent

Update van het apparaat voor IoT Hub ondersteunt twee soorten updates: op installatie kopie gebaseerd en op basis van een pakket.

Updates op basis van pakketten zijn gerichte updates waarmee alleen een specifiek onderdeel of een specifieke toepassing op het apparaat wordt gewijzigd. Dit leidt tot een lager verbruik van de band breedte en vermindert de tijd voor het downloaden en installeren van de update. Pakket updates bieden doorgaans minder downtime van apparaten bij het Toep assen van een update en vermijden de overhead van het maken van installatie kopieën.

In deze zelf studie wordt u begeleid bij het volt ooien van een end-to-end update op basis van een pakket via apparaat bijwerken voor IoT Hub. Voor deze zelf studie wordt een voor beeld van een pakket agent voor Ubuntu Server 18,04 x64 gebruikt. Zelfs als u van plan bent een andere besturingssysteem platform configuratie te gebruiken, is deze zelf studie nog steeds nuttig voor meer informatie over de hulpprogram ma's en concepten in de update van het apparaat voor IoT Hub. Voltooi deze inleiding tot een end-to-end update proces en kies vervolgens uw voorkeurs formulier voor het bijwerken en het besturings systeem om de details te bekijken. Met deze zelf studie kunt u een update van een Azure IoT-of Azure IoT Edge-apparaat met behulp van een update voor IoT Hub van het apparaat gebruiken. 

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Opslag plaats voor Device update pakket configureren
> * Update Agent en de bijbehorende afhankelijkheden van het apparaat downloaden en installeren
> * Een tag toevoegen aan uw IoT-apparaat
> * Een update importeren
> * Een apparaatgroep maken
> * Een pakket update implementeren
> * De update-implementatie controleren

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten
* Toegang tot een IoT Hub. U wordt aangeraden een S1-laag (Standard) of hoger te gebruiken.
* Een Azure IoT-of Azure IoT Edge-apparaat met Ubuntu Server 18,04 x64, verbonden met IoT Hub.
   * Als u een Azure IoT Edge apparaat gebruikt, zorg er dan voor dat het zich op v 1.2.0 van de Edge-runtime of hoger bevindt 
* Als u geen Azure IoT Edge apparaat gebruikt, [installeert u het meest recente `aziot-identity-service` pakket (preview) op uw IOT-apparaat](https://github.com/Azure/iot-identity-service/actions/runs/575919358) 
* [Het update account van het apparaat en het exemplaar zijn gekoppeld aan dezelfde IoT Hub als hierboven.](create-device-update-account.md)

## <a name="configure-device-update-package-repository"></a>Opslag plaats voor Device update pakket configureren

1. Installeer de configuratie van de opslagplaats die overeenkomt met het besturingssysteem van uw apparaat. Voor deze zelf studie is dit Ubuntu Server 18,04. 
   
   ```shell
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

2. Kopieer de gegenereerde lijst naar de map sources.list.d.

   ```shell
      sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```
   
3. Installeer de openbare Microsoft GPG-sleutel.

   ```shell
      curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
      sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

## <a name="install-device-update-deb-agent-packages"></a>Updates voor de update van een deb-agent voor het apparaat installeren

1. Pakket lijsten bijwerken op uw apparaat

   ```shell
      sudo apt-get update
   ```

2. Het pakket met de deviceupdate-agent en de bijbehorende afhankelijkheden installeren

   ```shell
      sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
   ```

Voor het bijwerken van apparaten voor Azure IoT Hub-software pakketten gelden de volgende licentie voorwaarden:
   * [Update van het apparaat voor IoT Hub licentie](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Delivery Optimization-client licentie](https://github.com/microsoft/do-client/blob/main/LICENSE.md)
   
Lees de licentie voorwaarden voordat u een pakket gebruikt. Uw installatie en het gebruik van een pakket zijn uw acceptatie van deze voor waarden. Als u niet akkoord gaat met de licentie voorwaarden, mag u dat pakket niet gebruiken.

## <a name="configure-device-update-agent-using-azure-iot-identity-service-preview"></a>Device Update Agent configureren met behulp van de Azure IoT-identiteits service (preview)

Zodra de vereiste pakketten zijn geïnstalleerd, moet u het apparaat inrichten met de Cloud identiteit en verificatie gegevens.

1. Het configuratie bestand openen

   ```shell
      sudo nano /etc/aziot/config.toml
   ```

2. Zoek de sectie inrichtings configuratie van het bestand. Verwijder de sectie hand matig inrichten met connection string. Werk de waarde van de connection_string bij met de connection string voor uw IoT-apparaat (Edge). Zorg ervoor dat alle andere inrichtings secties van commentaar zijn.


   ```toml
      # Manual provisioning configuration using a connection string
      [provisioning]
      source = "manual"
      iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
      device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
      dynamic_reprovisioning = false 
   ```

3. Sla het bestand op en sluit het met CTRL + X, Y

4. Pas de configuratie toe. 

   Als u een IoT Edge apparaat gebruikt, gebruikt u de volgende opdracht. 
   
   ```shell
      sudo iotedge config apply
   ```
   
   Als u een IoT-apparaat gebruikt, waarop het `aziot-identity-service` pakket is geïnstalleerd, gebruikt u de volgende opdracht. 
      
   ```shell
      sudo aziotctl config apply
   ```

5. U kunt ook controleren of de services worden uitgevoerd door

    ```shell
       sudo systemctl list-units --type=service | grep 'adu-agent\.service\|deliveryoptimization-agent\.service'
    ```

    De uitvoer moet het volgende lezen:

    ```markdown
       adu-agent.service                   loaded active running Device Update for IoT Hub Agent daemon.

       deliveryoptimization-agent.service               loaded active running deliveryoptimization-agent.service: Performs content delivery optimization tasks   `
    ```

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

1. Down load het volgende [apt-manifest bestand](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-apt-manifest.json) en [Importeer het manifest bestand](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.1-importManifest.json). In dit apt-manifest wordt de nieuwste beschik bare versie van `libcurl4-doc package` op uw IOT-apparaat geïnstalleerd. 

   U kunt dit [apt-manifest bestand](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-7.58-apt-manifest.json) ook downloaden en het [manifest bestand importeren](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-2-2.0.1-importManifest.json). Hiermee wordt een specifieke versie v-7.58.0 van de `libcurl4-doc package` naar uw IOT-apparaat geïnstalleerd. 

2. Selecteer in Azure Portal de optie apparaat bijwerken onder Automatische Apparaatbeheer in de navigatie balk aan de linkerkant van uw IoT Hub.

3. Selecteer het tabblad updates.

4. Selecteer + nieuwe update importeren.

5. Selecteer het mappictogram of het tekstvak onder ' Selecteer een manifest bestand voor importeren '. U ziet een dialoog venster voor het kiezen van een bestand. Selecteer het import manifest dat u eerder hebt gedownload. Selecteer vervolgens het mappictogram of het tekstvak onder ' Selecteer een of meer update bestanden '. U ziet een dialoog venster voor het kiezen van een bestand. Selecteer het apt-manifest update bestand dat u eerder hebt gedownload.
   
   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Scherm opname van de selectie van update bestanden." lightbox="media/import-update/select-update-files.png":::

6. Selecteer het mappictogram of tekstvak onder "een opslag container selecteren". Selecteer vervolgens het gewenste opslag account.

7. Als u al een container hebt gemaakt, kunt u deze opnieuw gebruiken. (Selecteer anders "+ container" om een nieuwe opslag container voor updates te maken.).  Selecteer de container die u wilt gebruiken en klik op selecteren.

   :::image type="content" source="media/import-update/container.png" alt-text="Scherm opname van container selectie." lightbox="media/import-update/container.png":::

8. Selecteer verzenden om het import proces te starten.

9. Het import proces wordt gestart en het scherm wordt gewijzigd in de sectie import geschiedenis. Selecteer vernieuwen om de voortgang weer te geven totdat het import proces is voltooid. Afhankelijk van de grootte van de update, kan dit binnen enkele minuten worden voltooid, maar dit kan langer duren.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Scherm opname met update-import volgorde." lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Wanneer de status kolom aangeeft dat het importeren is geslaagd, selecteert u de header gereed om te implementeren. U ziet nu de geïmporteerde update in de lijst.

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

U hebt nu een voltooide end-to-end pakket update voltooid met update voor het bijwerken van het apparaat voor IoT Hub op een Ubuntu Server 18,04 x64-apparaat. 

## <a name="bonus-steps"></a>Bonus stappen

1. Down load het volgende [apt-manifest bestand](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-remove-apt-manifest.json) en [Importeer het manifest bestand](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.2-importManifest.json). Met dit apt-manifest wordt de geïnstalleerde `libcurl4-doc package` van uw IOT-apparaat verwijderd. 

2. De secties ' update importeren ' en ' update implementeren ' herhalen

## <a name="clean-up-resources"></a>Resources opschonen

Als u het apparaat niet meer nodig hebt, moet u de update account, het exemplaar, het IoT Hub en IoT-apparaat opschonen. U kunt dit doen door naar elke afzonderlijke resource te gaan en verwijderen te selecteren. Houd er rekening mee dat u een update-exemplaar voor het apparaat moet opschonen voordat u het update account voor het apparaat opschoont. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Image update op Raspberry Pi 3 B + zelf studie](device-update-raspberry-pi.md)

