---
title: Updates voor IoT Hub-schema en andere informatie importeren in de update van het apparaat | Microsoft Docs
description: Schema en andere verwante informatie (inclusief objecten) die wordt gebruikt bij het importeren van updates voor de update van het apparaat voor IoT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/25/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 13044b8f087b403f83516a32a490d2dee8db700f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054604"
---
# <a name="importing-updates-into-device-update-for-iot-hub---schema-and-other-information"></a>Updates voor IoT Hub-schema en andere informatie importeren in de update van het apparaat
Als u een update wilt importeren in update voor het apparaat voor IoT Hub, moet u ervoor zorgen dat u de [concepten](import-concepts.md) en [hand leiding](import-update.md) eerst hebt gecontroleerd. Als u geïnteresseerd bent in de details van het schema dat wordt gebruikt bij het maken van een import manifest en informatie over verwante objecten, zie hieronder.

## <a name="import-manifest-schema"></a>Manifest schema importeren

| Naam | Type | Beschrijving | Beperkingen |
| --------- | --------- | --------- | --------- |
| UpdateId | `UpdateId` object | Identiteit bijwerken. |
| UpdateType | tekenreeks | Type update: <br/><br/> * Geef `microsoft/apt:1` op wanneer u een update op basis van een pakket uitvoert met behulp van referentie agent.<br/> * Geef `microsoft/swupdate:1` op wanneer u een op een installatie kopie gebaseerde update uitvoert met behulp van referentie agent.<br/> * Geef op `microsoft/simulator:1` Wanneer u voorbeeld agent Simulator gebruikt.<br/> * Geef een aangepast type op bij het ontwikkelen van een aangepaste agent. | Indeling: <br/> `{provider}/{type}:{typeVersion}`<br/><br/> Maxi maal 32 tekens in totaal |
| InstalledCriteria | tekenreeks | De teken reeks die door de agent wordt geïnterpreteerd om te bepalen of de update is toegepast:  <br/> * Geef de **waarde** van SWVersion voor het update type op `microsoft/swupdate:1` .<br/> * Geef `{name}-{version}` op voor het update type `microsoft/apt:1` , waarvan de naam en versie worden opgehaald uit het apt-bestand.<br/> * Geef de hash van het update bestand voor het update type op `microsoft/simulator:1` .<br/> * Geef een aangepaste teken reeks op als u een aangepaste Agent ontwikkelt.<br/> | Maxi maal 64 tekens |
| Compatibiliteit | Matrix van `CompatibilityInfo` objecten | Compatibiliteits informatie van een apparaat dat compatibel is met deze update. | Maxi maal 10 items |
| CreatedDateTime | datum en tijd | De datum en tijd waarop de update is gemaakt. | Gescheiden ISO 8601-datum-en tijd notatie, in UTC |
| ManifestVersion | tekenreeks | Versie van manifest schema importeren. Geef `2.0` op dat compatibel is met `urn:azureiot:AzureDeviceUpdateCore:1` interface en `urn:azureiot:AzureDeviceUpdateCore:4` interface. | Moet `2.0` |
| Bestanden | Matrix van `File` objecten | Payload-bestanden bijwerken | Maxi maal 5 bestanden |

## <a name="updateid-object"></a>UpdateId-object

| Naam | Type | Beschrijving | Beperkingen |
| --------- | --------- | --------- | --------- |
| Provider | tekenreeks | Provider onderdeel van de update-identiteit. | 1-64 tekens, alfanumeriek, punt en streepje. |
| Name | tekenreeks | Noem een deel van de update-identiteit. | 1-64 tekens, alfanumeriek, punt en streepje. |
| Versie | versie | Versie gedeelte van de update-identiteit. | 2 tot 4 onderdelen, punt-gescheiden versie nummer tussen 0 en 2147483647. Voorloop nullen worden verwijderd. |

## <a name="file-object"></a>Bestands object

| Naam | Type | Beschrijving | Beperkingen |
| --------- | --------- | --------- | --------- |
| Bestands | tekenreeks | Naam van bestand | Moet uniek zijn binnen een update |
| SizeInBytes | Int64 | Grootte van het bestand in bytes. | Maxi maal 800 MB per afzonderlijk bestand of 800 MB gezamenlijk per update |
| Hashes | `Hashes` object | JSON-object met hash (sen) van het bestand |

## <a name="compatibilityinfo-object"></a>CompatibilityInfo-object

| Naam | Type | Beschrijving | Beperkingen |
| --- | --- | --- | --- |
| DeviceManufacturer | tekenreeks | Fabrikant van het apparaat waarmee de update compatibel is. | 1-64 tekens, alfanumeriek, punt en streepje. |
| DeviceModel | tekenreeks | Model van het apparaat waarmee de update compatibel is. | 1-64 tekens, alfanumeriek, punt en streepje. |

## <a name="hashes-object"></a>Hashes-object

| Name | Vereist | Type | Beschrijving |
| --------- | --------- | --------- | --------- |
| Sha256 | True | tekenreeks | Met base64 gecodeerde hash van het bestand met behulp van het algoritme SHA-256. |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [importeren van concepten](./import-concepts.md).

Als u klaar bent, kunt u de [hand leiding voor het importeren How-To](./import-update.md)uitproberen. hier vindt u stapsgewijze instructies voor het importeren van het proces.
