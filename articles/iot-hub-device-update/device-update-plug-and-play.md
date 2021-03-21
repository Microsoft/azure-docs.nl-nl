---
title: Meer informatie over het bijwerken van apparaten met updates voor IoT Hub IoT Plug en Play | Microsoft Docs
description: Met update van het apparaat voor IoT Hub worden apparaten gedetecteerd en beheerd die over-the-Air-update geschikt zijn.
author: ValOlson
ms.author: valls
ms.date: 2/14/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 227488f165aaad2f204c647eed17467a4ef561a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101662556"
---
# <a name="device-update-for-iot-hub-and-iot-plug-and-play"></a>Update van het apparaat voor IoT Hub en IoT Plug en Play

Update van het apparaat voor IoT Hub maakt gebruik van [IoT Plug en Play](https://docs.microsoft.com/azure/iot-pnp/) om apparaten te detecteren en te beheren die via de lucht kunnen worden bijgewerkt. De Device Update-service verzendt en ontvangt eigenschappen en berichten van en naar apparaten met behulp van PnP-interfaces. Apparaat bijwerken voor IoT Hub vereist IoT-apparaten om de volgende interfaces en model-id te implementeren, zoals hieronder wordt beschreven.

## <a name="adu-core-interface"></a>ADU-kern interface

De ADUCoreInterface-interface wordt gebruikt om Update-acties en meta gegevens naar apparaten te verzenden en de update status van apparaten te ontvangen. De ADU core-interface is in twee object eigenschappen gesplitst.

De verwachte onderdeel naam in uw model is **"azureDeviceUpdateAgent"** wanneer u deze interface implementeert. [Meer informatie over Azure IoT PnP-onderdelen](https://docs.microsoft.com/azure/iot-pnp/concepts-components)

### <a name="agent-metadata"></a>Meta gegevens van agent

De meta gegevens van de agent bevatten velden die de Update-Agent van het apparaat gebruikt om informatie en status te verzenden naar updates van apparaten.

|Name|Schema|Richting|Beschrijving|Voorbeeld|
|----|------|---------|-----------|-----------|
|resultCode|geheel getal|apparaat naar Cloud|Een code die informatie bevat over het resultaat van de laatste update-actie. Kan worden ingevuld voor het slagen of mislukken en moet de specificatie van de [HTTP-status code](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)volgen.|500|
|extendedResultCode|geheel getal|apparaat naar Cloud|Een code die aanvullende informatie over het resultaat bevat. Kan worden ingevuld voor het slagen of mislukken.|0x80004005|
|staat|geheel getal|apparaat naar Cloud|Dit is een geheel getal dat de huidige status van de Update-Agent van het apparaat aangeeft. Zie hieronder voor meer informatie |Niet-actief|
|installedUpdateId|tekenreeks|apparaat naar Cloud|Een ID van de update die momenteel is geïnstalleerd (via update van het apparaat). Deze waarde is null voor een apparaat dat nooit een update heeft uitgevoerd via een apparaat bijwerken.|Null|
|`deviceProperties`|Kaart|apparaat naar Cloud|De set eigenschappen die de fabrikant en het model bevatten.|Zie hieronder voor meer informatie

#### <a name="state"></a>Staat

Het is de status die door de Update Agent van het apparaat wordt gerapporteerd na ontvangst van een actie van de Device Update service. `State` wordt gerapporteerd als reactie op een `Action` (Zie `Actions` hieronder), verzonden naar de Update Agent van het apparaat van de Device Update-service. Zie de [werk stroom overzicht](understand-device-update.md#device-update-agent) voor aanvragen die tussen de update service voor het apparaat en de Update-Agent van het apparaat stromen.

|Name|Waarde|Beschrijving|
|---------|-----|-----------|
|Niet-actief|0|Het apparaat is klaar om een actie te ontvangen van de update service voor het apparaat. Nadat een update is voltooid, wordt de status geretourneerd naar de `Idle` status.|
|DownloadSucceeded|2|Een geslaagde down load.|
|InstallSucceeded|4|Een geslaagde installatie.|
|Mislukt|255|Er is een fout opgetreden tijdens het bijwerken.|

#### <a name="device-properties"></a>Apparaateigenschappen

Het is de set eigenschappen die de fabrikant en het model bevatten.

|Name|Schema|Richting|Beschrijving|
|----|------|---------|-----------|
|manufacturer|tekenreeks|apparaat naar Cloud|De fabrikant van het apparaat van het apparaat, gerapporteerd via `deviceProperties` . Deze eigenschap wordt van een van de twee locaties gelezen: de interface ' AzureDeviceUpdateCore ' probeert eerst de waarde ' aduc_manufacturer ' te lezen uit het bestand met het [configuratie bestand](device-update-configuration-file.md) .  Als de waarde niet wordt ingevuld in het configuratie bestand, wordt standaard de definitie van de compileer tijd voor ADUC_DEVICEPROPERTIES_MANUFACTURER gerapporteerd. Deze eigenschap wordt alleen gerapporteerd tijdens het opstarten.|
|model|tekenreeks|apparaat naar Cloud|Het model van het apparaat van het apparaat, gerapporteerd via `deviceProperties` . Deze eigenschap wordt van een van de twee locaties gelezen: de AzureDeviceUpdateCore-interface probeert eerst de waarde aduc_model te lezen uit het bestand met het [configuratie bestand](device-update-configuration-file.md) .  Als de waarde niet wordt ingevuld in het configuratie bestand, wordt standaard de definitie van de compileer tijd voor ADUC_DEVICEPROPERTIES_MODEL gerapporteerd. Deze eigenschap wordt alleen gerapporteerd tijdens het opstarten.|
|aduVer|tekenreeks|apparaat naar Cloud|Versie van de apparaat-Update agent die op het apparaat wordt uitgevoerd. Deze waarde wordt alleen gelezen uit de build als tijdens de compilatie ENABLE_ADU_TELEMETRY_REPORTING is ingesteld op 1 (waar). Klanten kunnen kiezen voor het afmelden van versie rapportage door de waarde in te stellen op 0 (false). De eigenschappen van de [Update-Agent voor het apparaat aanpassen](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md).|
|doVer|tekenreeks|apparaat naar Cloud|Versie van de Delivery Optimization agent die wordt uitgevoerd op het apparaat. De waarde wordt alleen uit de build gelezen als tijdens de compilatie ENABLE_ADU_TELEMETRY_REPORTING is ingesteld op 1 (waar). Klanten kunnen kiezen voor deelname aan de versie rapportage door de waarde in te stellen op 0 (false). De eigenschappen van de [Delivery Optimization-Agent aanpassen](https://github.com/microsoft/do-client/blob/main/README.md#building-do-client-components).|

### <a name="service-metadata"></a>Meta gegevens van service

De meta gegevens van de service bevatten velden die door de Update Services van het apparaat worden gebruikt voor het communiceren van acties en gegevens naar de Update Agent van het apparaat.

|Name|Schema|Richting|Beschrijving|
|----|------|---------|-----------|
|actie|geheel getal|Cloud naar apparaat|Het is een geheel getal dat overeenkomt met een actie die de agent moet uitvoeren. Onderstaande waarden.|
|updateManifest|tekenreeks|Cloud naar apparaat|Wordt gebruikt om de inhoud van een update te beschrijven. Gegenereerd vanuit het [import manifest](import-update.md#create-device-update-import-manifest)|
|updateManifestSignature|JSON-object|Cloud naar apparaat|Een JSON-websignature (JWS) met JSON-websleutels die worden gebruikt voor de verificatie van de bron.|
|fileUrls|Kaart|Cloud naar apparaat|Toewijzing van `FileHash` tot `DownloadUri` . Hiermee wordt aan de agent door gegeven welke bestanden moeten worden gedownload en welke hash moet worden gebruikt om te controleren of de bestanden correct zijn gedownload.|

#### <a name="action"></a>Bewerking

`Actions` Hieronder staan de acties die door de Update Agent van het apparaat worden uitgevoerd, zoals wordt aangegeven door de service voor het bijwerken van het apparaat. De Update Agent van het apparaat rapporteert een `State` (Zie `State` sectie hierboven) waarbij de ontvangen wordt verwerkt `Action` . Zie de [werk stroom overzicht](understand-device-update.md#device-update-agent) voor aanvragen die tussen de update service voor het apparaat en de Update-Agent van het apparaat stromen.

|Name|Waarde|Beschrijving|
|---------|-----|-----------|
|Downloaden|0|Gepubliceerde inhoud of update en andere benodigde inhoud downloaden|
|Installeren|1|Installeer de inhoud of update. Dit betekent meestal dat het installatie programma wordt aangeroepen voor de inhoud of update.|
|Toepassen|2|Voltooi de update. Het systeem wordt zo nodig gesignaleerd om de computer opnieuw op te starten.|
|Annuleren|255|De verwerking van de huidige actie stoppen en teruggaan naar `Idle` . Wordt ook gebruikt om de agent in de status te laten terugkeren naar `Failed` `Idle` .|

## <a name="device-information-interface"></a>Interface voor apparaatgegevens

De apparaatgegevens interface is een concept dat in [IOT-Plug en Play architectuur](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play)wordt gebruikt. Het bevat apparaat-naar-Cloud-eigenschappen die informatie geven over de hardware en het besturings systeem van het apparaat. Bij het bijwerken van het apparaat voor IoT Hub worden de eigenschappen DeviceInformation. Manufacturer en DeviceInformation. model gebruikt voor telemetrie en diagnostische gegevens. Zie dit [voor beeld](https://devicemodels.azure.com/dtmi/azure/devicemanagement/deviceinformation-1.json)voor meer informatie over de interface met informatie over apparaten.

De verwachte onderdeel naam in uw model is **deviceInformation** wanneer u deze interface implementeert. [Meer informatie over Azure IoT PnP-onderdelen](https://docs.microsoft.com/azure/iot-pnp/concepts-components)

|Naam|Type|Schema|Richting|Beschrijving|Voorbeeld|
|----|----|------|---------|-----------|-----------|
|manufacturer|Eigenschap|tekenreeks|apparaat naar Cloud|Bedrijfs naam van de fabrikant van het apparaat. Dit kan hetzelfde zijn als de naam van de OEM (Original Equipment Manufacturer).|Contoso|
|model|Eigenschap|tekenreeks|apparaat naar Cloud|Naam of ID van het apparaats model.|IoT Edge apparaat|
|swVersion|Eigenschap|tekenreeks|apparaat naar Cloud|De versie van de software op uw apparaat. swVersion kan de versie van uw firmware zijn.|4.15.0-122|
|osName|Eigenschap|tekenreeks|apparaat naar Cloud|De naam van het besturings systeem op het apparaat.|Ubuntu Server 18.04|
|processorArchitecture|Eigenschap|tekenreeks|apparaat naar Cloud|De architectuur van de processor op het apparaat.|ARM64|
|processorManufacturer|Eigenschap|tekenreeks|apparaat naar Cloud|De naam van de fabrikant van de processor op het apparaat.|Microsoft|
|totalStorage|Eigenschap|tekenreeks|apparaat naar Cloud|Totale beschik bare opslag ruimte op het apparaat in kilo bytes.|2048|
|totalMemory|Eigenschap|tekenreeks|apparaat naar Cloud|Het totale beschik bare geheugen op het apparaat in kilo bytes.|256|

## <a name="model-id"></a>Model-id 

Model-ID is hoe slimme apparaten hun mogelijkheden adverteren voor Azure IoT-toepassingen met IoT-Plug en Play.To meer informatie over het bouwen van smart-apparaten voor het adverteren van hun mogelijkheden voor Azure IoT-toepassingen vindt u de [ontwikkel handleiding voor IoT Plug en Play-apparaat](https://docs.microsoft.com/azure/iot-pnp/concepts-developer-guide-device-c).

Voor het bijwerken van apparaten voor IoT Hub moet de IoT-Plug en Play Smart Device een model-ID met de waarde **' dtmi: AzureDeviceUpdate; 1 '** aankondigen als onderdeel van de verbinding met het apparaat. [Meer informatie over het aankondigen van een model-id](https://docs.microsoft.com/azure/iot-pnp/concepts-developer-guide-device-c#model-id-announcement).
