---
title: Een nieuwe update toevoegen | Microsoft Docs
description: How-To voor het toevoegen van een nieuwe update aan Apparaatupdate voor IoT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 4/19/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: ebfeee2828b3a36f9cf47891f8aea6d889db85bd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763573"
---
# <a name="add-an-update-to-device-update-for-iot-hub"></a>Een update toevoegen aan Apparaatupdate voor IoT Hub
Meer informatie over het toevoegen van een nieuwe update aan Apparaatupdate voor IoT Hub.

## <a name="prerequisites"></a>Vereisten

* [Toegang tot een IoT Hub met Apparaatupdate voor IoT Hub ingeschakeld.](create-device-update-account.md) 
* Een IoT-apparaat (of simulator) [dat is ingericht voor apparaatupdates](device-update-agent-provisioning.md) binnen IoT Hub.
* [PowerShell 5](/powershell/scripting/install/installing-powershell) of hoger (inclusief linux-, macOS- en Windows-installaties)
* Ondersteunde browsers:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> Sommige gegevens die naar deze service worden verzonden, kunnen worden verwerkt in een regio buiten de regio waarin dit exemplaar is gemaakt.

## <a name="obtain-an-update-for-your-devices"></a>Een update voor uw apparaten verkrijgen

Nu u Apparaatupdate hebt ingesteld en uw apparaten hebt ingericht, hebt u de updatebestand(en) nodig die u op deze apparaten gaat implementeren.

Als u apparaten hebt aangeschaft bij een OEM of oplossingsintegrator, zal die organisatie waarschijnlijk updatebestanden voor u leveren, zonder dat u de updates hoeft te maken. Neem contact op met de OEM of oplossingsintegrator om erachter te komen hoe deze updates beschikbaar maken.

Als uw organisatie al software maakt voor de apparaten die u gebruikt, wordt diezelfde groep gebruikt om de updates voor die software te maken. Wanneer u een update maakt die moet worden geïmplementeerd met apparaatupdates voor IoT Hub, begint u met de op afbeeldingen of pakketten gebaseerde [benadering,](understand-device-update.md#support-for-a-wide-range-of-update-artifacts) afhankelijk van uw scenario. Opmerking: als u uw eigen updates wilt maken, maar net begint, is GitHub een uitstekende optie om uw ontwikkeling te beheren. U kunt uw broncode opslaan en beheren, en Continue integratie (CI) en continue implementatie (CD) uitvoeren met [behulp van GitHub Actions](https://docs.github.com/en/actions/guides/about-continuous-integration).

## <a name="create-a-device-update-import-manifest"></a>Een importmanifest voor apparaatupdates maken

Als u dit nog niet hebt gedaan, zorg er dan voor dat u vertrouwd bent met de [basisconcepten voor importeren.](import-concepts.md)

1. Zorg ervoor dat uw updatebestand(en) zich in een map bevinden die toegankelijk is vanuit PowerShell.

2. Maak een tekstbestand met de **naam AduUpdate.psm1** in de map waarin uw update-afbeeldingsbestand of APT-manifestbestand zich bevindt. Open vervolgens de PowerShell-cmdlet [AduUpdate.psm1,](https://github.com/Azure/iot-hub-device-update/tree/main/tools/AduCmdlets) kopieer de inhoud naar het tekstbestand en sla het tekstbestand op.

3. Navigeer in PowerShell naar de map waar u de PowerShell-cmdlet uit stap 2 hebt gemaakt. Gebruik de optie Kopiëren hieronder en plak deze in PowerShell om de opdrachten uit te voeren:

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
    Import-Module .\AduUpdate.psm1
    ```

4. Voer de volgende opdrachten uit door de voorbeeldparameterwaarden te vervangen om een importmanifest te genereren, een JSON-bestand dat de update beschrijft:
    ```powershell
    $compat = New-AduUpdateCompatibility -DeviceManufacturer 'deviceManufacturer' -DeviceModel 'deviceModel'

    $importManifest = New-AduImportManifest -Provider 'updateProvider' -Name 'updateName' -Version 'updateVersion' `
                                            -UpdateType 'updateType' -InstalledCriteria 'installedCriteria' `
                                            -Compatibility $compat -Files 'updateFilePath(s)'

    $importManifest | Out-File '.\importManifest.json' -Encoding UTF8
    ```

    Hier volgen enkele voorbeeldwaarden voor de bovenstaande parameters. U kunt ook het volledige [importmanifestschema weergeven](import-schema.md) voor meer informatie.

    | Parameter | Beschrijving |
    | --------- | ----------- |
    | deviceManufacturer | De fabrikant van het apparaat met de update is compatibel met bijvoorbeeld Contoso. Moet overeenkomen met _de apparaat-eigenschap_ [van de fabrikant.](./device-update-plug-and-play.md#device-properties)
    | deviceModel | Het model van het apparaat waar de update compatibel mee is, bijvoorbeeld Broodrooster. Moet overeenkomen met de [apparaat-eigenschap van het](./device-update-plug-and-play.md#device-properties) _model._
    | updateProvider | Entiteit die maakt of rechtstreeks verantwoordelijk is voor de update. Het is vaak een bedrijfsnaam.
    | updateName | Id voor een klasse updates. De klasse kan alles zijn wat u kiest. Dit is vaak een apparaat- of modelnaam.
    | updateVersion | Versienummer dat deze update onderscheidt van andere die dezelfde provider en naam hebben. Komt niet overeen met een versie van een afzonderlijk softwareonderdeel op het apparaat (maar wel als u kiest).
    | updateType | <ul><li>Opgeven `microsoft/swupdate:1` voor update van afbeelding</li><li>Opgeven `microsoft/apt:1` voor pakketupdate</li></ul>
    | installedCriteria | <ul><li>Geef de waarde van SWVersion op voor `microsoft/swupdate:1` het updatetype</li><li>Geef **name-version op,** waarbij _name_ de naam is van het APT-manifest en _versie_ de versie van het APT-manifest is. Bijvoorbeeld contoso-iot-edge-1.0.0.0.
    | updateFilePath(s) | Pad naar de updatebestand(en) op uw computer


## <a name="review-the-generated-import-manifest"></a>Het gegenereerde importmanifest controleren

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

## <a name="import-an-update"></a>Een update importeren

> [!NOTE]
> In de onderstaande instructies wordt beschreven hoe u een update importeert via de Azure Portal ui. U kunt ook de Apparaatupdate [voor IoT Hub API's gebruiken om](https://github.com/Azure/iot-hub-device-update/tree/main/docs/publish-api-reference) een update te importeren. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en navigeer naar IoT Hub apparaatupdate.

2. Selecteer aan de linkerkant van de pagina Apparaatupdates onder Automatisch apparaatbeheer.

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="Updates importeren" lightbox="media/import-update/import-updates-3.png":::

3. Boven aan het scherm ziet u verschillende tabbladen. Selecteer het tabblad Updates.

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="Updates" lightbox="media/import-update/updates-tab.png":::

4. Selecteer +Nieuwe update importeren onder de header Gereed voor implementatie.

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="Nieuwe update importeren" lightbox="media/import-update/import-new-update-2.png":::

5. Selecteer het mappictogram of het tekstvak onder Een manifestbestand importeren selecteren. U ziet een dialoogvenster voor het kiezen van bestanden. Selecteer het importmanifest dat u eerder hebt gemaakt met behulp van de PowerShell-cmdlet. Selecteer vervolgens het mappictogram of het tekstvak onder Een of meer updatebestanden selecteren. U ziet een dialoogvenster voor het kiezen van bestanden. Selecteer uw updatebestand(en).

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Bestanden bijwerken selecteren" lightbox="media/import-update/select-update-files.png":::

6. Selecteer het mappictogram of het tekstvak onder Een opslagcontainer selecteren. Selecteer vervolgens het juiste opslagaccount. De opslagcontainer wordt gebruikt om de updatebestanden tijdelijk te faseren.

   :::image type="content" source="media/import-update/storage-account.png" alt-text="Opslagaccount" lightbox="media/import-update/storage-account.png":::

7. Als u al een container hebt gemaakt, kunt u deze opnieuw gebruiken. (Selecteer anders +Container om een nieuwe opslagcontainer voor updates te maken.)  Selecteer de container die u wilt gebruiken en klik op Selecteren.

   :::image type="content" source="media/import-update/container.png" alt-text="Selecteer Container" lightbox="media/import-update/container.png":::

8. Selecteer Verzenden om het importproces te starten.

   :::image type="content" source="media/import-update/publish-update.png" alt-text="Update publiceren" lightbox="media/import-update/publish-update.png":::

9. Het importproces begint en het scherm schakelt over naar de sectie Importgeschiedenis. Selecteer Vernieuwen om de voortgang te bekijken totdat het importproces is voltooid (afhankelijk van de grootte van de update kan dit binnen enkele minuten worden voltooid, maar kan het langer duren).

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Sequencing voor importeren bijwerken" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Wanneer in de kolom Status wordt aangegeven dat het importeren is geslaagd, selecteert u de header Gereed voor implementatie. De geïmporteerde update wordt nu weergegeven in de lijst.

   :::image type="content" source="media/import-update/update-ready.png" alt-text="Taakstatus" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>Volgende stappen

[Groepen maken](create-update-group.md)

[Meer informatie over importconcepten](import-concepts.md)