---
title: Importeren/exporteren van Azure IoT Hub apparaat-id's | Microsoft Docs
description: De Azure IoT Service SDK gebruiken om bulk bewerkingen uit te voeren op het identiteits register om apparaat-id's te importeren en exporteren. Met import bewerkingen kunt u de apparaat-id's in bulk maken, bijwerken en verwijderen.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: robinsh
ms.custom: devx-track-csharp
ms.openlocfilehash: 8e7a725b78fa828ce1286e212ee7de0205968156
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92536076"
---
# <a name="import-and-export-iot-hub-device-identities-in-bulk"></a>Id's van IoT Hub-apparaten bulksgewijs importeren en exporteren

Elke IoT-hub beschikt over een id-REGI ster dat u kunt gebruiken voor het maken van resources per apparaat in de service. Met het id-REGI ster kunt u ook de toegang tot de eind punten van het apparaat beheren. In dit artikel wordt beschreven hoe u apparaat-id's in bulk kunt importeren en exporteren naar een identiteits register. Zie [een IOT hub klonen](iot-hub-how-to-clone.md)om een werkend voor beeld in C# te bekijken en te leren hoe u deze mogelijkheid kunt gebruiken bij het klonen van een hub naar een andere regio.

> [!NOTE]
> IoT Hub heeft onlangs ondersteuning voor het virtuele netwerk in een beperkt aantal regio's toegevoegd. Deze functie beveiligt import-en export bewerkingen en elimineert de nood zaak om sleutels voor verificatie uit te voeren.  De ondersteuning voor het virtuele netwerk is in eerste instantie alleen beschikbaar in deze regio's: *WestUS2*, *eastus* en *SouthCentralUS*. Zie [IOT hub-ondersteuning voor virtuele netwerken](virtual-network-support.md)voor meer informatie over ondersteuning voor virtuele netwerken en de API-aanroepen om deze te implementeren.

Import-en export bewerkingen worden uitgevoerd in de context van *taken* waarmee u bulksgewijze service bewerkingen kunt uitvoeren op een IOT-hub.

De **RegistryManager** -klasse bevat de methoden **ExportDevicesAsync** en **ImportDevicesAsync** die gebruikmaken van het **project** Framework. Met deze methoden kunt u het geheel van een IoT hub-identiteits register exporteren, importeren en synchroniseren.

In dit onderwerp wordt beschreven hoe u de **RegistryManager** -klasse en het **taak** systeem gebruikt voor het uitvoeren van bulksgewijs importeren en exporteren van apparaten naar en van het identiteits register van een IOT-hub. U kunt ook de Azure IoT Hub Device Provisioning Service gebruiken voor het inschakelen van Zero-Touch, just-in-time inrichting naar een of meer IoT-hubs zonder menselijke tussen komst. Zie de documentatie van de [inrichtings service](../iot-dps/index.yml)voor meer informatie.

## <a name="what-are-jobs"></a>Wat zijn taken?

Identiteits register bewerkingen gebruiken het **taak** systeem wanneer de bewerking:

* Heeft een mogelijk lange uitvoerings tijd ten opzichte van de standaard runtime bewerkingen.

* Retourneert een grote hoeveelheid gegevens naar de gebruiker.

In plaats van één API-aanroep wacht op het resultaat van de bewerking, maakt de bewerking asynchroon een **taak** voor die IOT-hub. De bewerking retourneert vervolgens onmiddellijk een **JobProperties** -object.

In het volgende code fragment van C# ziet u hoe u een export taak maakt:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await 
  registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Als u de **RegistryManager** -klasse in uw C#-code wilt gebruiken, voegt u het NuGet-pakket **micro soft. Azure. devices** toe aan uw project. De **RegistryManager** -klasse bevindt zich in de naam ruimte **micro soft. Azure. devices** .

U kunt de **RegistryManager** -klasse gebruiken om de status van de **taak** op te vragen met de geretourneerde **JobProperties** -meta gegevens. Als u een exemplaar van de **RegistryManager** -klasse wilt maken, gebruikt u de methode **API createfromconnectionstring** .

```csharp
RegistryManager registryManager =
  RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

Als u de connection string voor uw IoT-hub wilt vinden, gaat u in het Azure Portal naar:

- Ga naar uw IoT-hub.

- Selecteer **Beleid voor gedeelde toegang**.

- Selecteer een beleid, waarbij rekening wordt gehouden met de machtigingen die u nodig hebt.

- Kopieer de Connections Tring van het deel venster aan de rechter kant van het scherm.

Het volgende C#-code fragment laat zien hoe u elke vijf seconden kunt pollen om te zien of de taak is voltooid:

```csharp
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

> [!NOTE]
> Als uw opslag account firewall configuraties heeft die de connectiviteit van IoT Hub beperken, kunt u overwegen [micro soft betrouw bare eerste partij uitzondering](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) te gebruiken (beschikbaar in geselecteerde regio's voor IOT-hubs met een beheerde service-identiteit).


## <a name="device-importexport-job-limits"></a>Taak limieten voor importeren/exporteren van apparaten

Voor alle IoT Hub lagen is slechts één actieve apparaat-import of-export taak toegestaan. IoT Hub heeft ook limieten voor de frequentie van taken. Zie [referentie-IOT hub quota's en beperking](iot-hub-devguide-quotas-throttling.md)voor meer informatie.

## <a name="export-devices"></a>Apparaten exporteren

Gebruik de methode **ExportDevicesAsync** om het volledig van een IOT hub-identiteits register te exporteren naar een Azure Storage BLOB-container met behulp van een Shared Access Signature (SAS). Zie voor meer informatie over gedeelde toegangs handtekeningen [beperkte toegang verlenen tot Azure storage-resources met behulp van Shared Access signatures (SAS)](../storage/common/storage-sas-overview.md).

Met deze methode kunt u betrouw bare back-ups maken van de apparaatgegevens in een BLOB-container die u beheert.

De methode **ExportDevicesAsync** vereist twee para meters:

* Een *teken reeks* die een URI bevat van een BLOB-container. Deze URI moet een SAS-token bevatten dat schrijf toegang verleent aan de container. Met de taak wordt een blok-Blob in deze container gemaakt voor het opslaan van de geserialiseerde gegevens van het export apparaat. Het SAS-token moet deze machtigingen bevatten:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

* Een *Booleaanse waarde* die aangeeft of u verificatie sleutels uit uw export gegevens wilt uitsluiten. Als deze eigenschap **Onwaar** is, worden verificatie sleutels opgenomen in de export uitvoer. Anders worden sleutels geëxporteerd als **Null**.

In het volgende code fragment van C# ziet u hoe u een export taak initieert die stuur programma-verificatie sleutels bevat in de export gegevens en vervolgens pollen voor voltooiing:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
  await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

De uitvoer van de taak wordt in de gegeven BLOB-container opgeslagen als een blok-blob met de naam **devices.txt**. De uitvoer gegevens bestaan uit JSON serialized Device Data, met één apparaat per regel.

In het volgende voor beeld ziet u de uitvoer gegevens:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Als een apparaat dubbele gegevens heeft, worden de dubbele gegevens ook samen met de gegevens van het apparaat geëxporteerd. In het volgende voor beeld ziet u deze notatie. Alle gegevens van de regel ' twinETag ' tot het einde is twee ledige gegevens.

```json
{
   "id":"export-6d84f075-0",
   "eTag":"MQ==",
   "status":"enabled",
   "statusReason":"firstUpdate",
   "authentication":null,
   "twinETag":"AAAAAAAAAAI=",
   "tags":{
      "Location":"LivingRoom"
   },
   "properties":{
      "desired":{
         "Thermostat":{
            "Temperature":75.1,
            "Unit":"F"
         },
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
            "$lastUpdatedVersion":2,
            "Thermostat":{
               "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
               "$lastUpdatedVersion":2,
               "Temperature":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               },
               "Unit":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               }
            }
         },
         "$version":2
      },
      "reported":{
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:51.1309437Z"
         },
         "$version":1
      }
   }
}
```

Als u toegang tot deze gegevens in code nodig hebt, kunt u deze gegevens eenvoudig deserialiseren met behulp van de **ExportImportDevice** -klasse. In het volgende code fragment van C# ziet u hoe u de apparaatgegevens leest die eerder zijn geëxporteerd naar een blok-blob:

```csharp
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), null, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

## <a name="import-devices"></a>Apparaten importeren

Met de methode **ImportDevicesAsync** in de klasse **RegistryManager** kunt u bulksgewijze import-en synchronisatie bewerkingen uitvoeren in een IOT hub-identiteits register. Net als de methode **ExportDevicesAsync** gebruikt de methode **ImportDevicesAsync** het **taak** raamwerk.

Wees voorzichtig met het gebruik van de methode **ImportDevicesAsync** omdat er naast het inrichten van nieuwe apparaten in uw identiteits register ook bestaande apparaten kunnen worden bijgewerkt en verwijderd.

> [!WARNING]
> Een import bewerking kan niet ongedaan worden gemaakt. Maak altijd een back-up van uw bestaande gegevens met behulp van de methode **ExportDevicesAsync** naar een andere blob-container voordat u bulksgewijs wijzigingen aanbrengt in uw identiteits register.

De methode **ImportDevicesAsync** heeft twee para meters:

* Een *teken reeks* die een URI bevat van een [Azure Storage](../storage/index.yml) BLOB-container die moet worden gebruikt als *invoer* voor de taak. Deze URI moet een SAS-token bevatten dat lees toegang verleent aan de container. Deze container moet een BLOB bevatten met de naam **devices.txt** die de geserialiseerde apparaatgegevens bevat die moeten worden geïmporteerd in uw identiteits register. De import gegevens moeten informatie over het apparaat bevatten in dezelfde JSON-indeling als de **ExportImportDevice** -taak die wordt gebruikt bij het maken van een **devices.txt** blob. Het SAS-token moet deze machtigingen bevatten:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```

* Een *teken reeks* die een URI bevat van een [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) BLOB-container die moet worden gebruikt als *uitvoer* van de taak. Met de taak wordt een blok-Blob in deze container gemaakt voor het opslaan van de fout gegevens van de voltooide import **taak**. Het SAS-token moet deze machtigingen bevatten:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> De twee para meters kunnen verwijzen naar dezelfde BLOB-container. Met de afzonderlijke para meters hoeft u meer controle over uw gegevens in te scha kelen, omdat de uitvoer container extra machtigingen nodig heeft.

In het volgende code fragment van C# ziet u hoe u een import taak start:

```csharp
JobProperties importJob = 
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Deze methode kan ook worden gebruikt voor het importeren van de gegevens voor het apparaat dubbele. De notatie voor de gegevens invoer is hetzelfde als de indeling die wordt weer gegeven in de sectie **ExportDevicesAsync** . Op deze manier kunt u de geëxporteerde gegevens opnieuw importeren. De **$metadata** is optioneel.

## <a name="import-behavior"></a>Gedrag bij importeren

U kunt de methode **ImportDevicesAsync** gebruiken om de volgende bulk bewerkingen uit te voeren in uw identiteits register:

* Bulk registratie van nieuwe apparaten
* Bulk verwijderingen van bestaande apparaten
* Bulk status wijzigingen (apparaten in-of uitschakelen)
* Bulk toewijzing van nieuwe authenticatie sleutels voor apparaten
* Automatisch opnieuw genereren van sleutels voor verificatie van apparaten
* Bulk update van dubbele gegevens

U kunt een combi natie van de voor gaande bewerkingen uitvoeren binnen één **ImportDevicesAsync** -aanroep. U kunt bijvoorbeeld nieuwe apparaten registreren en bestaande apparaten verwijderen of bijwerken. Bij gebruik in combi natie met de methode **ExportDevicesAsync** kunt u al uw apparaten volledig migreren van een IOT-hub naar een andere.

Als het import bestand dubbele meta gegevens bevat, worden met deze meta gegevens de bestaande dubbele meta gegevens overschreven. Als het import bestand geen dubbele meta gegevens bevat, worden alleen de `lastUpdateTime` meta gegevens bijgewerkt met de huidige tijd.

Gebruik de optionele eigenschap **importMode** in de import-serialisatie-gegevens voor elk apparaat om het import proces per apparaat te beheren. De eigenschap **importMode** heeft de volgende opties:

| importMode | Description |
| --- | --- |
| **createOrUpdate** |Als er geen apparaat met de opgegeven **id** bestaat, wordt het pas geregistreerd. <br/>Als het apparaat al bestaat, wordt de bestaande informatie overschreven met de opgegeven invoer gegevens zonder rekening te houden met de **ETAG** -waarde. <br> De gebruiker kan eventueel dubbele gegevens en de apparaatgegevens opgeven. De dubbele ETAG, indien opgegeven, wordt onafhankelijk van de ETAG van het apparaat verwerkt. Als er niet overeenkomt met de bestaande ' ETAG ', wordt er een fout naar het logboek bestand geschreven. |
| **creëren** |Als er geen apparaat met de opgegeven **id** bestaat, wordt het pas geregistreerd. <br/>Als het apparaat al bestaat, wordt er een fout naar het logboek bestand geschreven. <br> De gebruiker kan eventueel dubbele gegevens en de apparaatgegevens opgeven. De dubbele ETAG, indien opgegeven, wordt onafhankelijk van de ETAG van het apparaat verwerkt. Als er niet overeenkomt met de bestaande ' ETAG ', wordt er een fout naar het logboek bestand geschreven. |
| **update** |Als er al een apparaat met de opgegeven **id** bestaat, wordt de bestaande informatie overschreven met de opgegeven invoer gegevens zonder rekening te houden met de **ETAG** -waarde. <br/>Als het apparaat niet bestaat, wordt er een fout naar het logboek bestand geschreven. |
| **updateIfMatchETag** |Als er al een apparaat met de opgegeven **id** bestaat, wordt de bestaande informatie overschreven met de opgegeven invoer gegevens alleen als er sprake is van een **ETAG** -overeenkomst. <br/>Als het apparaat niet bestaat, wordt er een fout naar het logboek bestand geschreven. <br/>Als er een **ETAG** niet overeenkomt, wordt er een fout naar het logboek bestand geschreven. |
| **createOrUpdateIfMatchETag** |Als er geen apparaat met de opgegeven **id** bestaat, wordt het pas geregistreerd. <br/>Als het apparaat al bestaat, wordt de bestaande informatie overschreven met de opgegeven invoer gegevens alleen als er sprake is van een **ETAG** -overeenkomst. <br/>Als er een **ETAG** niet overeenkomt, wordt er een fout naar het logboek bestand geschreven. <br> De gebruiker kan eventueel dubbele gegevens en de apparaatgegevens opgeven. De dubbele ETAG, indien opgegeven, wordt onafhankelijk van de ETAG van het apparaat verwerkt. Als er niet overeenkomt met de bestaande ' ETAG ', wordt er een fout naar het logboek bestand geschreven. |
| **verwijderen** |Als er al een apparaat met de opgegeven **id** bestaat, wordt dit verwijderd zonder rekening te houden met de **ETAG** -waarde. <br/>Als het apparaat niet bestaat, wordt er een fout naar het logboek bestand geschreven. |
| **deleteIfMatchETag** |Als er al een apparaat met de opgegeven **id** bestaat, wordt dit alleen verwijderd als er sprake is van een **ETAG** -overeenkomst. Als het apparaat niet bestaat, wordt er een fout naar het logboek bestand geschreven. <br/>Als er een ETag niet overeenkomt, wordt er een fout naar het logboek bestand geschreven. |

> [!NOTE]
> Als de serialisatie-gegevens niet expliciet een **importMode** -vlag voor een apparaat definiëren, wordt standaard de **createOrUpdate** tijdens de import bewerking.

## <a name="import-devices-example--bulk-device-provisioning"></a>Voor beeld van apparaten importeren-Bulksgewijs inrichten van apparaten

In het volgende voor beeld van C#-code ziet u hoe u meerdere apparaat-id's kunt genereren die:

* Verificatie sleutels bevatten.
* Schrijf die apparaatgegevens naar een blok-blob.
* Importeer de apparaten in het identiteits register.

```csharp
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
  // Create a new ExportImportDevice
  // CryptoKeyGenerator is in the Microsoft.Azure.Devices.Common namespace
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to the list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write the list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the blob to add new devices
// Log information related to the job is written to the same container
// This normally takes 1 minute per 100 devices
JobProperties importJob =
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Voor beeld van apparaten importeren-bulksgewijs verwijderen

In het volgende code voorbeeld ziet u hoe u de apparaten verwijdert die u hebt toegevoegd met behulp van het vorige code voorbeeld:

```csharp
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="get-the-container-sas-uri"></a>De SAS-URI van de container ophalen

In het volgende code voorbeeld ziet u hoe u een [SAS-URI](../storage/common/storage-sas-overview.md) kunt genereren met de machtigingen lezen, schrijven en verwijderen voor een BLOB-container:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u bulk bewerkingen kunt uitvoeren op het id-REGI ster in een IoT-hub. Veel van deze bewerkingen, met inbegrip van het verplaatsen van apparaten van de ene hub naar een andere, worden gebruikt in de [sectie apparaten beheren die zijn geregistreerd bij de IOT-hub van het klonen van een IOT hub](iot-hub-how-to-clone.md#managing-the-devices-registered-to-the-iot-hub). 

Er is een werk voorbeeld aan het kloon artikel gekoppeld. Dit bevindt zich in de IoT C#-voor beelden op deze pagina: [Azure IOT-voor beelden voor C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/), waarbij het project wordt ImportExportDevicesSample. U kunt het voor beeld downloaden en het uitproberen. Er zijn instructies in het artikel [een IOT hub klonen](iot-hub-how-to-clone.md) .

Raadpleeg de volgende artikelen voor meer informatie over het beheren van Azure IoT Hub:

* [IoT Hub bewaken](monitor-iot-hub.md)

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [Ontwikkelaars handleiding IoT Hub](iot-hub-devguide.md)
* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/quickstart-linux.md)

Zie voor meer informatie over het gebruik van de IoT Hub Device Provisioning Service om Zero-Touch, just-in-time inrichten in te scha kelen: 

* [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml)