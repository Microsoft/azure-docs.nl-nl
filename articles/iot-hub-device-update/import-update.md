---
title: Een nieuwe update importeren | Microsoft Docs
description: How-To hand leiding voor het importeren van een nieuwe update in IoT Hub apparaat bijwerken voor IoT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: ede0d279b8769f49afcdae1cb9352c1b47fb59b5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932400"
---
# <a name="import-new-update"></a>Nieuwe update importeren
Meer informatie over het importeren van een nieuwe update voor het bijwerken van apparaten in IoT Hub. Als u dit nog niet hebt gedaan, moet u vertrouwd zijn met de basis concepten voor het [importeren](import-concepts.md).

## <a name="prerequisites"></a>Vereisten

* [Toegang tot een IOT hub met het bijwerken van het apparaat voor IOT hub ingeschakeld](create-device-update-account.md). 
* Een IoT-apparaat (of simulator) dat is ingericht voor het bijwerken van het apparaat binnen IoT Hub.
   * Als u een echt apparaat gebruikt, hebt u een update-installatie kopie bestand voor het bijwerken van de installatie kopie of het [apt-manifest bestand](device-update-apt-manifest.md) voor pakket updates nodig.
* [Power shell 5](/powershell/scripting/install/installing-powershell) of hoger (inclusief Linux-, macOS-en Windows-installaties)
* Ondersteunde browsers:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> Sommige gegevens die naar deze service worden verzonden, kunnen worden verwerkt in een regio buiten de regio waarin deze instantie is gemaakt.

## <a name="create-device-update-import-manifest"></a>Manifest voor het importeren van het apparaat maken

1. Zorg ervoor dat het bestand met de update-afbeelding of het APT-manifest bestand zich bevindt in een map die toegankelijk is vanuit Power shell.

2. Maak een tekst bestand met de naam **AduUpdate. psm1** in de map waarin het bestand met de update-installatie kopie of het apt-manifest bestand zich bevindt. Open vervolgens de Power shell-cmdlet [AduUpdate. psm1](https://github.com/Azure/iot-hub-device-update/tree/main/tools/AduCmdlets) , kopieer de inhoud naar het tekst bestand en sla het tekst bestand op.

3. Ga in Power shell naar de map waarin u de Power shell-cmdlet van stap 2 hebt gemaakt. Gebruik de Kopieer optie hieronder en plak vervolgens in Power shell om de opdrachten uit te voeren:

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
    Import-Module .\AduUpdate.psm1
    ```

4. Voer de volgende opdrachten uit door de voorbeeld parameter waarden te vervangen om een import manifest te genereren, een JSON-bestand dat de update beschrijft:
    ```powershell
    $compat = New-AduUpdateCompatibility -DeviceManufacturer 'deviceManufacturer' -DeviceModel 'deviceModel'

    $importManifest = New-AduImportManifest -Provider 'updateProvider' -Name 'updateName' -Version 'updateVersion' `
                                            -UpdateType 'updateType' -InstalledCriteria 'installedCriteria' `
                                            -Compatibility $compat -Files 'updateFilePath(s)'

    $importManifest | Out-File '.\importManifest.json' -Encoding UTF8
    ```

    Hier volgen enkele voorbeeld waarden voor de bovenstaande para meters. U kunt ook het volledige schema voor het importeren van het [manifest](import-schema.md) bekijken voor meer informatie.

    | Parameter | Beschrijving |
    | --------- | ----------- |
    | deviceManufacturer | Fabrikant van het apparaat waarmee de update compatibel is, bijvoorbeeld contoso. De eigenschap van het [apparaat](./device-update-plug-and-play.md#device-properties)van de _fabrikant_ moet overeenkomen.
    | deviceModel | Model van het apparaat waarmee de update compatibel is, bijvoorbeeld pop-up. De eigenschap van het _model_ [apparaat](./device-update-plug-and-play.md#device-properties)moet overeenkomen.
    | updateProvider | Entiteit die de update maakt of rechtstreeks verantwoordelijk is. Het is vaak een bedrijfs naam.
    | updatenaam | Id voor een klasse van updates. De klasse kan alles zijn wat u kiest. Het is vaak een apparaat-of model naam.
    | updateVersion | Het versie nummer waarmee deze update wordt onderscheiden van anderen die dezelfde provider en naam hebben. Komt niet overeen met een versie van een afzonderlijk software onderdeel op het apparaat (maar kan desgewenst worden gekozen).
    | updateType | <ul><li>Opgeven `microsoft/swupdate:1` voor bijwerken van installatie kopie</li><li>Opgeven `microsoft/apt:1` voor pakket update</li></ul>
    | installedCriteria | <ul><li>Geef de waarde van SWVersion voor het `microsoft/swupdate:1` Update type op</li><li>Geef de naam **-versie** op, waarbij _naam_ de naam is van het apt-manifest en _versie_ de versie van het apt-manifest. Bijvoorbeeld contoso-IOT-Edge-1.0.0.0.
    | updateFilePath (s) | Pad naar de update bestand (en) op uw computer


## <a name="review-generated-import-manifest"></a>Gegenereerd import manifest controleren

Voorbeeld:
```json
{
  "updateId": {
    "provider": "Microsoft",
    "name": "Toaster",
    "version": "2.0"
  },
  "updateType": "microsoft/swupdate:1",
  "installedCriteria": "5",
  "compatibility": [
    {
      "deviceManufacturer": "Fabrikam",
      "deviceModel": "Toaster"
    },
    {
      "deviceManufacturer": "Contoso",
      "deviceModel": "Toaster"
    }
  ],
  "files": [
    {
      "filename": "file1.json",
      "sizeInBytes": 7,
      "hashes": {
        "sha256": "K2mn97qWmKSaSaM9SFdhC0QIEJ/wluXV7CoTlM8zMUo="
      }
    },
    {
      "filename": "file2.zip",
      "sizeInBytes": 11,
      "hashes": {
        "sha256": "gbG9pxCr9RMH2Pv57vBxKjm89uhUstD06wvQSioLMgU="
      }
    }
  ],
  "createdDateTime": "2020-10-08T03:32:52.477Z",
  "manifestVersion": "2.0"
}
```

## <a name="import-update"></a>Update importeren

[!NOTE]
De volgende instructies laten zien hoe u een update importeert via de gebruikers interface van Azure Portal. U kunt ook de [Update van het apparaat voor IOT hub-api's](https://github.com/Azure/iot-hub-device-update/tree/main/docs/publish-api-reference) gebruiken om een update te importeren. 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw IOT hub met het bijwerken van het apparaat.

2. Selecteer aan de linkerkant van de pagina ' apparaat bijwerken ' onder Automatische Apparaatbeheer.

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="Updates importeren" lightbox="media/import-update/import-updates-3.png":::

3. Boven aan het scherm worden verschillende tabbladen weer gegeven. Selecteer het tabblad updates.

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="Updates" lightbox="media/import-update/updates-tab.png":::

4. Selecteer ' + nieuwe update importeren ' onder de header gereed om te implementeren.

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="Nieuwe update importeren" lightbox="media/import-update/import-new-update-2.png":::

5. Selecteer het mappictogram of het tekstvak onder ' Selecteer een manifest bestand voor importeren '. U ziet een dialoog venster voor het kiezen van een bestand. Selecteer het import manifest dat u eerder hebt gemaakt met behulp van de Power shell-cmdlet. Selecteer vervolgens het mappictogram of het tekstvak onder ' Selecteer een of meer update bestanden '. U ziet een dialoog venster voor het kiezen van een bestand. Selecteer uw update bestand (en).

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Update bestanden selecteren" lightbox="media/import-update/select-update-files.png":::

6. Selecteer het mappictogram of tekstvak onder "een opslag container selecteren". Selecteer vervolgens het gewenste opslag account. De opslag container wordt gebruikt om de update bestanden tijdelijk te stage.

   :::image type="content" source="media/import-update/storage-account.png" alt-text="Opslagaccount" lightbox="media/import-update/storage-account.png":::

7. Als u al een container hebt gemaakt, kunt u deze opnieuw gebruiken. (Selecteer anders "+ container" om een nieuwe opslag container voor updates te maken.).  Selecteer de container die u wilt gebruiken en klik op selecteren.

   :::image type="content" source="media/import-update/container.png" alt-text="Container selecteren" lightbox="media/import-update/container.png":::

8. Selecteer verzenden om het import proces te starten.

   :::image type="content" source="media/import-update/publish-update.png" alt-text="Update publiceren" lightbox="media/import-update/publish-update.png":::

9. Het import proces wordt gestart en het scherm wordt overgeschakeld naar de sectie Geschiedenis importeren. Selecteer vernieuwen om de voortgang weer te geven totdat het import proces is voltooid (afhankelijk van de grootte van de update, dit kan binnen een paar minuten worden voltooid, maar kan langer duren).

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Sequentiëren van import bewerking bijwerken" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Wanneer de status kolom aangeeft dat het importeren is geslaagd, selecteert u de header gereed om te implementeren. U ziet nu de geïmporteerde update in de lijst.

   :::image type="content" source="media/import-update/update-ready.png" alt-text="Taak status" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>Volgende stappen

[Groepen maken](create-update-group.md)

[Meer informatie over het importeren van concepten](import-concepts.md)