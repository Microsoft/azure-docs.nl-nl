---
title: Zelf studie over het bijwerken van apparaten voor Azure IoT Hub met behulp van de Ubuntu (18,04 x64) Simulator-referentie agent | Microsoft Docs
description: Ga aan de slag met het bijwerken van het apparaat voor Azure IoT Hub met behulp van de Ubuntu (18,04 x64) Simulator-referentie agent.
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 8b2a8ae76c79e4d3ff151334defe7f966c60f032
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559244"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-1804-x64-simulator-reference-agent"></a>Zelf studie over het bijwerken van apparaten voor Azure IoT Hub met behulp van de Ubuntu (18,04 x64) Simulator-referentie agent

Update van het apparaat voor IoT Hub ondersteunt twee soorten updates: op installatie kopie gebaseerd en op basis van een pakket.

Installatie kopieën bieden een hoger vertrouwens niveau in de eind toestand van het apparaat. Het is doorgaans eenvoudiger om de resultaten van een installatie kopie te repliceren: update tussen een pre-productie omgeving en een productie omgeving, omdat deze niet dezelfde uitdagingen als pakketten en hun afhankelijkheden vormt. Als gevolg van hun atoom karakter kan een model van een/B-failover ook eenvoudig worden aangenomen.

In deze zelf studie wordt u begeleid bij de stappen voor het volt ooien van een end-to-end update op basis van een installatie kopie met apparaat bijwerken voor IoT Hub. 

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Installatie kopie downloaden en installeren
> * Een tag toevoegen aan uw IoT-apparaat
> * Een update importeren
> * Een apparaatgroep maken
> * Een update voor een installatie kopie implementeren
> * De update-implementatie controleren

## <a name="prerequisites"></a>Vereisten
* Als u dit nog niet hebt gedaan, maakt u een [Update account en-exemplaar](create-device-update-account.md)voor het apparaat, met inbegrip van het configureren van een IOT hub.

### <a name="download-and-install"></a>Downloaden en installeren

* AZ (Azure CLI)-cmdlets voor Power shell:
  * Open Power shell > Azure CLI (' Y ' installeren voor vragen om te installeren vanaf een niet-vertrouwde bron)

```powershell
PS> Install-Module Az -Scope CurrentUser
```

### <a name="enable-wsl-on-your-windows-device-windows-subsystem-for-linux"></a>WSL inschakelen op uw Windows-apparaat (Windows-subsysteem voor Linux)

1. Open Power shell als Administrator op uw computer en voer de volgende opdracht uit (u wordt mogelijk na elke stap opnieuw opgestart):

```powershell
PS> Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
PS> Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

(*U wordt mogelijk gevraagd om opnieuw op te starten na deze stap*)

2. Ga naar de Microsoft Store op internet en Installeer [Ubuntu 18,04 LTS](https://www.microsoft.com/p/ubuntu-1804-lts/9n9tngvndl3q?activetab=pivot:overviewtab`).

3. Start ' Ubuntu 18,04 LTS ' en installeer.

4. Wanneer u hebt geïnstalleerd, wordt u gevraagd om een basis naam (gebruikers naam) en wacht woord in te stellen. Zorg ervoor dat u het wacht woord voor het onthouden van een basis naam gebruikt.

5. Voer in Power shell de volgende opdracht uit om Ubuntu in te stellen als de standaard distributie voor Linux:

```powershell
PS> wsl --setdefault Ubuntu-18.04
```

6. Alle Linux-distributies weer geven, moet u ervoor zorgen dat Ubuntu de standaard versie is.

```powershell
PS> wsl --list
```

7. U ziet het volgende: **Ubuntu-18,04 (standaard)**

## <a name="download-device-update-ubuntu-1804-x64-simulator-reference-agent"></a>Update Ubuntu voor het apparaat downloaden (18,04 x64) Simulator referentie agent

De Ubuntu-update afbeelding kan worden gedownload uit de sectie *assets* van de [opmerkingen bij](https://github.com/Azure/iot-hub-device-update/releases)de release.

Er zijn twee versies van de agent. Als u een scenario op basis van een installatie kopie wilt maken, gebruikt u AducIotAgentSim-micro soft-swupdate en als u een pakket op basis van pakketten uitoefent, gebruikt u AducIotAgentSim-micro soft-apt.

## <a name="install-device-update-agent-simulator"></a>Update Agent Simulator voor apparaat installeren

1. Start Ubuntu WSL en voer de volgende opdracht in (Houd er rekening mee dat er extra ruimte en punt aan het einde).

  ```shell
  explorer.exe .
  ```

2. Kopieer AducIotAgentSim-micro soft-swupdate (of AducIotAgentSim-micro soft-apt) van uw lokale map waar u deze hebt gedownload onder/mnt naar uw basismap in WSL.

3. Voer de volgende opdracht uit om de binaire bestanden van het uitvoer bare bestand te maken.

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-swupdate
  ```

  of

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-apt
  ```
Voor het bijwerken van apparaten voor Azure IoT Hub-software gelden de volgende licentie voorwaarden:
   * [Update van het apparaat voor IoT Hub licentie](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Delivery Optimization-client licentie](https://github.com/microsoft/do-client/blob/main/LICENSE.md)
   
Lees de licentie voorwaarden voordat u de agent gebruikt. Uw installatie en gebruik zijn uw acceptatie van deze voor waarden. Als u niet akkoord gaat met de licentie voorwaarden, gebruik dan niet de update van het apparaat voor IoT Hub agent.

## <a name="add-device-to-azure-iot-hub"></a>Apparaat toevoegen aan Azure IoT Hub

Zodra de Update Agent van het apparaat wordt uitgevoerd op een IoT-apparaat, moet het apparaat worden toegevoegd aan de Azure-IoT Hub.  In azure IoT Hub wordt een connection string gegenereerd voor een bepaald apparaat.

1. Start de update voor het apparaat vanuit het Azure Portal IoT Hub.
2. Maak een nieuw apparaat.
3. Navigeer aan de linkerkant van de pagina naar ' Explorers ' > IoT-apparaten > Selecteer ' nieuw '.
4. Geef een naam op voor het apparaat onder apparaat-ID--zorg ervoor dat het selectie vakje sleutels automatisch genereren is geselecteerd.
5. Selecteer opslaan.
6. Nu keert u terug naar de pagina apparaten en het apparaat dat u hebt gemaakt, moet zich in de lijst bevindt. Selecteer dat apparaat.
7. Selecteer in de weer gave apparaat het pictogram kopiëren naast primaire verbindings reeks.
8. Plak de gekopieerde tekens ergens anders in de onderstaande stappen. **Deze gekopieerde teken reeks is uw apparaat Connection String**.

## <a name="add-connection-string-to-simulator"></a>connection string toevoegen aan de Simulator

Start de Update Agent van het apparaat op uw nieuwe software apparaten.

1. Ubuntu starten.
2. Voer de Update Agent voor het apparaat uit en geef het apparaat connection string van de vorige sectie verpakt met apostrofs:

Vervangen `<device connection string>` door uw Connection String
```shell
./AducIotAgentSim-microsoft-swupdate -c '<device connection string>'
```

of

```shell
./AducIotAgentSim-microsoft-apt -c '<device connection string>'
```

3. Schuif omhoog en zoek naar de teken reeks die aangeeft dat het apparaat de status niet-actief heeft. De status ' inactief ' geeft aan dat het apparaat gereed is voor service opdrachten:

```markdown
Agent running. [main]
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

1. Maak een import manifest volgens deze [instructies](import-update.md).
2. Selecteer de optie apparaat bijwerken onder Automatische Apparaatbeheer in de navigatie balk aan de linkerkant.

3. Selecteer het tabblad updates.

4. Selecteer + nieuwe update importeren.

5. Selecteer het mappictogram of het tekstvak onder ' Selecteer een manifest bestand voor importeren '. U ziet een dialoog venster voor het kiezen van een bestand. Selecteer het import manifest dat u hierboven hebt gemaakt.  Selecteer vervolgens het mappictogram of het tekstvak onder ' Selecteer een of meer update bestanden '. U ziet een dialoog venster voor het kiezen van een bestand. Selecteer de Ubuntu-update kopie die u eerder hebt gedownload. 

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

U hebt nu een voltooide end-to-end update van de installatie kopie voltooid met behulp van de update voor het bijwerken van het apparaat voor IoT Hub met de Ubuntu (18,04 x64) Simulator-referentie agent.

## <a name="clean-up-resources"></a>Resources opschonen

Als u het apparaat niet meer nodig hebt, moet u de update account, het exemplaar, het IoT Hub en IoT-apparaat opschonen. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Problemen oplossen](troubleshoot-device-update.md)

