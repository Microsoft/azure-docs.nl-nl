---
title: Begrijpen hoe Apparaatupdate voor IoT Hub IoT-Plug en Play | Microsoft Docs
description: Apparaatupdate voor IoT Hub voor het ontdekken en beheren van apparaten die over-the-air kunnen worden bijgewerkt.
author: ValOlson
ms.author: valls
ms.date: 2/14/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: fbc3502952e11830ef9abb06cb709fcc60288343
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739526"
---
# <a name="device-update-for-iot-hub-and-iot-plug-and-play"></a>Apparaatupdate voor IoT Hub en IoT-Plug en Play

Apparaatupdate voor IoT Hub maakt gebruik van [IoT Plug en Play](../iot-pnp/index.yml) om apparaten te ontdekken en beheren die over-the-air kunnen worden bijgewerkt. De apparaatupdateservice verzendt en ontvangt eigenschappen en berichten naar en van apparaten met behulp van PnP-interfaces. Apparaatupdate voor IoT Hub vereist dat IoT-apparaten de volgende interfaces en model-id implementeren, zoals hieronder wordt beschreven.

## <a name="adu-core-interface"></a>ADU Core Interface

De interface 'ADUCoreInterface' wordt gebruikt voor het verzenden van updateacties en metagegevens naar apparaten en het ontvangen van de updatestatus van apparaten. De interface 'ADU Core' is gesplitst in twee objecteigenschappen.

De verwachte onderdeelnaam in uw model is **'azureDeviceUpdateAgent'** bij het implementeren van deze interface. [Meer informatie over Azure IoT PnP-onderdelen](../iot-pnp/concepts-components.md)

### <a name="agent-metadata"></a>Metagegevens van agent

Metagegevens van agent bevatten velden die het apparaat of de Apparaatupdate-agent gebruikt om informatie en status te verzenden naar Apparaatupdate-services.

|Name|Schema|Richting|Beschrijving|Voorbeeld|
|----|------|---------|-----------|-----------|
|resultCode|geheel getal|apparaat naar cloud|Een code die informatie bevat over het resultaat van de laatste updateactie. Kan worden ingevuld voor geslaagd of mislukt en moet voldoen aan de [http-statuscodespecificatie](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).|500|
|extendedResultCode|geheel getal|apparaat naar cloud|Een code die aanvullende informatie over het resultaat bevat. Kan worden ingevuld voor geslaagd of mislukt.|0x80004005|
|staat|geheel getal|apparaat naar cloud|Het is een geheel getal dat de huidige status van de Agent voor apparaatupdates aangeeft. Zie hieronder voor meer informatie |Niet-actief|
|installedUpdateId|tekenreeks|apparaat naar cloud|Een id van de update die momenteel is geïnstalleerd (via Apparaatupdate). Deze waarde is null voor een apparaat dat nooit een update heeft ondernomen via Apparaatupdate.|Null|
|`deviceProperties`|Kaart|apparaat naar cloud|De set eigenschappen die de fabrikant en het model bevatten.|Zie hieronder voor meer informatie

#### <a name="state"></a>Staat

Dit is de status die wordt gerapporteerd door de Agent voor apparaatupdates nadat deze een actie van de Device Update Service heeft ontvangen. `State` wordt gerapporteerd als reactie op een (zie hieronder) verzonden naar de Agent voor `Action` apparaatupdates van de `Actions` Apparaatupdateservice. Zie de [overzichtswerkstroom](understand-device-update.md#device-update-agent) voor aanvragen die tussen de Device Update Service en de Device Update Agent stromen.

|Name|Waarde|Beschrijving|
|---------|-----|-----------|
|Niet-actief|0|Het apparaat is klaar om een actie te ontvangen van de Device Update Service. Na een geslaagde update wordt de status teruggegeven aan de `Idle` status .|
|DownloadSucceeded|2|Een geslaagde download.|
|InstallSucceeded|4|Een geslaagde installatie.|
|Mislukt|255|Er is een fout opgetreden tijdens het bijwerken.|

#### <a name="device-properties"></a>Apparaateigenschappen

Het is de set eigenschappen die de fabrikant en het model bevatten.

|Name|Schema|Richting|Description|
|----|------|---------|-----------|
|manufacturer|tekenreeks|apparaat naar cloud|De fabrikant van het apparaat, gerapporteerd via `deviceProperties` . Deze eigenschap wordt gelezen op een van de twee plaatsen. De interface 'AzureDeviceUpdateCore' probeert eerst de waarde 'aduc_manufacturer' uit het configuratiebestand [te](device-update-configuration-file.md) lezen.  Als de waarde niet is ingevuld in het configuratiebestand, wordt standaard de compileertijddefinitie voor de ADUC_DEVICEPROPERTIES_MANUFACTURER. Deze eigenschap wordt alleen gerapporteerd tijdens het opstarten.|
|model|tekenreeks|apparaat naar cloud|Het apparaatmodel van het apparaat, gerapporteerd via `deviceProperties` . Deze eigenschap wordt gelezen op een van de twee plaatsen. De Interface AzureDeviceUpdateCore probeert eerst de waarde 'aduc_model' uit het configuratiebestand [te](device-update-configuration-file.md) lezen.  Als de waarde niet is ingevuld in het configuratiebestand, wordt standaard de compileertijddefinitie voor de ADUC_DEVICEPROPERTIES_MODEL. Deze eigenschap wordt alleen gerapporteerd tijdens het opstarten.|
|aduVer|tekenreeks|apparaat naar cloud|Versie van de Agent voor apparaatupdates die op het apparaat wordt uitgevoerd. Deze waarde wordt alleen uit de build gelezen als tijdens het compileren ENABLE_ADU_TELEMETRY_REPORTING is ingesteld op 1 (true). Klanten kunnen ervoor kiezen om versierapportage af te melden door de waarde in te stellen op 0 (onwaar). [Eigenschappen van de Agent voor apparaatupdates aanpassen.](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md)|
|Dover|tekenreeks|apparaat naar cloud|Versie van de Delivery Optimization agent die wordt uitgevoerd op het apparaat. De waarde wordt alleen uit de build gelezen als tijdens het compileren ENABLE_ADU_TELEMETRY_REPORTING is ingesteld op 1 (true). Klanten kunnen ervoor kiezen om af te melden voor versierapportage door de waarde in te stellen op 0 (onwaar). [De eigenschappen van Delivery Optimization agent aanpassen.](https://github.com/microsoft/do-client/blob/main/README.md#building-do-client-components)|

### <a name="service-metadata"></a>Servicemetagegevens

Servicemetagegevens bevatten velden die de Apparaatupdate-services gebruiken om acties en gegevens te communiceren met de Agent voor apparaatupdates.

|Name|Schema|Richting|Description|
|----|------|---------|-----------|
|action|geheel getal|cloud naar apparaat|Het is een geheel getal dat overeenkomt met een actie die de agent moet uitvoeren. Waarden die hieronder worden vermeld.|
|updateManifest|tekenreeks|cloud naar apparaat|Wordt gebruikt om de inhoud van een update te beschrijven. Gegenereerd op basis van [het importmanifest](import-update.md#create-a-device-update-import-manifest)|
|updateManifestSignature|JSON-object|cloud naar apparaat|Een JSON Web Signature (JWS) met JSON-websleutels die worden gebruikt voor bronverificatie.|
|fileUrls|Kaart|cloud naar apparaat|Wijs toe `FileHash` aan `DownloadUri` . Vertelt de agent welke bestanden moeten worden gedownload en welke hash moet worden gebruikt om te controleren of de bestanden correct zijn gedownload.|

#### <a name="action"></a>Bewerking

`Actions` hieronder worden de acties weergegeven die door de Agent voor apparaatupdates worden ondernomen volgens de instructies van de Device Update Service. De Agent voor apparaatupdates rapporteert een `State` (zie `State` sectie hierboven) voor het verwerken van de `Action` ontvangen. Zie de [overzichtswerkstroom](understand-device-update.md#device-update-agent) voor aanvragen die tussen de Device Update Service en de Device Update Agent stromen.

|Name|Waarde|Beschrijving|
|---------|-----|-----------|
|Downloaden|0|Gepubliceerde inhoud downloaden of bijwerken en andere benodigde inhoud|
|Installeren|1|Installeer de inhoud of update. Dit betekent meestal het aanroepen van het installatieprogramma voor de inhoud of update.|
|Toepassen|2|Vereenf de update. Het geeft aan dat het systeem zo nodig opnieuw moet worden opgestart.|
|Annuleren|255|Stop de verwerking van de huidige actie en ga terug naar `Idle` . Wordt ook gebruikt om de agent in de status te vertellen `Failed` om terug te gaan naar `Idle` .|

## <a name="device-information-interface"></a>Interface voor apparaatgegevens

De Device Information Interface is een concept dat wordt gebruikt in [IoT Plug en Play architectuur](../iot-pnp/overview-iot-plug-and-play.md). Het bevat apparaat-naar-cloudeigenschappen die informatie bieden over de hardware en het besturingssysteem van het apparaat. Apparaatupdate voor IoT Hub gebruikt de eigenschappen DeviceInformation.manufacturer en DeviceInformation.model voor telemetrie en diagnostische gegevens. Zie dit voorbeeld voor meer informatie over de Interface voor [apparaatgegevens.](https://devicemodels.azure.com/dtmi/azure/devicemanagement/deviceinformation-1.json)

De verwachte onderdeelnaam in uw model is **deviceInformation** bij het implementeren van deze interface. [Meer informatie over Azure IoT PnP-onderdelen](../iot-pnp/concepts-components.md)

|Naam|Type|Schema|Richting|Beschrijving|Voorbeeld|
|----|----|------|---------|-----------|-----------|
|manufacturer|Eigenschap|tekenreeks|apparaat naar cloud|Bedrijfsnaam van de fabrikant van het apparaat. Dit kan dezelfde zijn als de naam van de original equipment manufacturer (OEM).|Contoso|
|model|Eigenschap|tekenreeks|apparaat naar cloud|Naam of id van apparaatmodel.|IoT Edge apparaat|
|swVersion|Eigenschap|tekenreeks|apparaat naar cloud|Versie van de software op uw apparaat. swVersion kan de versie van uw firmware zijn.|4.15.0-122|
|osName|Eigenschap|tekenreeks|apparaat naar cloud|Naam van het besturingssysteem op het apparaat.|Ubuntu Server 18.04|
|processorArchitecture|Eigenschap|tekenreeks|apparaat naar cloud|Architectuur van de processor op het apparaat.|ARM64|
|processorManufacturer|Eigenschap|tekenreeks|apparaat naar cloud|Naam van de fabrikant van de processor op het apparaat.|Microsoft|
|totalStorage|Eigenschap|tekenreeks|apparaat naar cloud|Totale beschikbare opslag op het apparaat in kilobytes.|2048|
|totalMemory|Eigenschap|tekenreeks|apparaat naar cloud|Totaal beschikbaar geheugen op het apparaat in kilobytes.|256|

## <a name="model-id"></a>Model-id 

Model-id is hoe slimme apparaten hun mogelijkheden adverteren naar Azure IoT-toepassingen met IoT Plug en Play.To voor meer informatie over het bouwen van slimme apparaten om hun mogelijkheden aan Azure IoT-toepassingen te adverteren. Ga naar de ontwikkelaarshandleiding voor [IoT Plug en Play-apparaten.](../iot-pnp/concepts-developer-guide-device.md)

Apparaatupdate voor IoT Hub vereist dat het slimme IoT Plug en Play-apparaat een model-id aankondeelt met de waarde **'dtmi:AzureDeviceUpdate;1'** als onderdeel van de apparaatverbinding. [Meer informatie over het aankondigen van een model-id.](../iot-pnp/concepts-developer-guide-device.md#model-id-announcement)