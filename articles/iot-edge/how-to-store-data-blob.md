---
title: Blok-blobs opslaan op apparaten-Azure IoT Edge | Microsoft Docs
description: Zie ondersteunde bewerkingen voor Blob-opslag en maak verbinding met uw Blob Storage-account voor meer informatie over lagen en time-to-Live-functies.
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6cefb60d794defcce54766b9c7f71e7fbf40fe5c
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539430"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>Gegevens opslaan aan de rand met Azure Blob Storage op IoT Edge

Azure Blob Storage op IoT Edge biedt een [blok-BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) en [voegt een BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) -opslag oplossing toe aan de rand. Een Blob Storage-module op uw IoT Edge-apparaat gedraagt zich als een Azure Blob-service, behalve dat de blobs lokaal op uw IoT Edge apparaat worden opgeslagen. U kunt toegang krijgen tot uw blobs met dezelfde Azure Storage SDK-methoden of BLOB API-aanroepen die u al gebruikt. In dit artikel worden de concepten beschreven die betrekking hebben op Azure Blob Storage op IoT Edge-container waarop een BLOB-service op uw IoT Edge-apparaat wordt uitgevoerd.

Deze module is handig in scenario's:

* waar gegevens lokaal moeten worden opgeslagen totdat deze kunnen worden verwerkt of overgedragen naar de Cloud. Deze gegevens kunnen Video's, afbeeldingen, financiële gegevens, ziekenhuis gegevens of andere ongestructureerde gegevens zijn.
* Wanneer apparaten zich op een locatie bevinden met beperkte connectiviteit.
* Wanneer u de gegevens lokaal wilt verwerken om toegang te krijgen tot de gegevens met lage latentie, zodat u zo snel mogelijk op eventuele nood gevallen kunt reageren.
* Wanneer u de bandbreedte kosten wilt verlagen en wilt voor komen dat terabytes aan gegevens naar de cloud worden overgebracht. U kunt de gegevens lokaal verwerken en alleen de verwerkte gegevens verzenden naar de Cloud.

Bekijk de video voor snelle inleiding
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

Deze module wordt geleverd met **deviceToCloudUpload** -en **deviceAutoDelete** -functies.

**deviceToCloudUpload** is een Configureer bare functionaliteit. Deze functie uploadt automatisch de gegevens van uw lokale Blob-opslag naar Azure met een tijdelijke ondersteuning voor de Internet verbinding. U kunt het volgende doen:

* De functie deviceToCloudUpload in-of uitschakelen.
* Kies de volg orde waarin de gegevens naar Azure worden gekopieerd, zoals NewestFirst of OldestFirst.
* Geef het Azure Storage account op waarnaar u uw gegevens wilt uploaden.
* Geef de containers op die u wilt uploaden naar Azure. Met deze module kunt u zowel de bron-als de doel container naam opgeven.
* Kies de mogelijkheid om de blobs onmiddellijk te verwijderen nadat het uploaden naar de Cloud opslag is voltooid
* Upload de volledige BLOB (met behulp van `Put Blob` bewerking) en uploads op blok niveau (met behulp van `Put Block` `Put Block List` en `Append Block` bewerkingen).

In deze module wordt upload op blok niveau gebruikt als uw Blob uit blokken bestaat. Hier volgen enkele van de algemene scenario's:

* Uw toepassing werkt sommige blokken van een eerder geüploade blok-BLOB bij of voegt nieuwe blokken toe aan een toevoeg-blob, maar deze module uploadt alleen de bijgewerkte blokken en niet de hele blob.
* De module uploadt Blob en Internet verbinding verdwijnt, wanneer de verbinding weer wordt hersteld, worden alleen de resterende blokken geüpload en niet de hele blob.

Als er een onverwacht proces wordt beëindigd (zoals een stroom storing) tijdens het uploaden van een blob, worden alle blokken die het uploaden verloopt opnieuw geüpload zodra de module weer online is.

**deviceAutoDelete** is een Configureer bare functionaliteit. Met deze functie worden uw blobs automatisch uit de lokale opslag verwijderd wanneer de opgegeven duur (gemeten in minuten) verloopt. U kunt het volgende doen:

* De functie deviceAutoDelete in-of uitschakelen.
* Geef de tijd in minuten (deleteAfterMinutes) op waarna de blobs automatisch worden verwijderd.
* Kies de mogelijkheid om de BLOB te bewaren tijdens het uploaden als de deleteAfterMinutes-waarde verloopt.

## <a name="prerequisites"></a>Vereisten

Een Azure IoT Edge-apparaat:

* U kunt uw ontwikkel computer of een virtuele machine als IoT Edge apparaat gebruiken door de stappen in de Quick start voor [Linux](quickstart-linux.md) -of [Windows-apparaten](quickstart.md)te volgen.

* Raadpleeg [Azure IOT Edge ondersteunde systemen](support.md#operating-systems) voor een lijst met ondersteunde besturings systemen en architecturen. De Azure Blob Storage op IoT Edge-module biedt ondersteuning voor de volgende architecturen:
  * Windows AMD64
  * Linux AMD64
  * Linux ARM32
  * Linux-ARM64 (preview-versie)

Cloudresources:

Een standaard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>eigenschappen deviceToCloudUpload en deviceAutoDelete

Gebruik de gewenste eigenschappen van de module om **deviceToCloudUploadProperties** en **deviceAutoDeleteProperties** in te stellen. Gewenste eigenschappen kunnen tijdens de implementatie worden ingesteld of later worden gewijzigd door de module te bewerken, maar niet opnieuw te implementeren. We raden u aan om de ' module dubbele ' voor `reported configuration` te controleren en `configurationValidation` om ervoor te zorgen dat waarden correct worden door gegeven.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

De naam van deze instelling is `deviceToCloudUploadProperties` . Als u de IoT Edge Simulator gebruikt, stelt u de waarden in op de verwante omgevings variabelen voor deze eigenschappen, die u in de sectie uitleg kunt vinden.

| Eigenschap | Mogelijke waarden | Uitleg |
| ----- | ----- | ---- |
| uploadOn | de waarde True, false | Standaard ingesteld op `false` . Als u de functie wilt inschakelen, stelt u dit veld in op `true` . <br><br> Omgevings variabele: `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Hiermee kunt u de volg orde kiezen waarin de gegevens naar Azure worden gekopieerd. Standaard ingesteld op `OldestFirst` . De volg orde wordt bepaald door het tijdstip van de laatste wijziging van de blob. <br><br> Omgevings variabele: `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` is een connection string waarmee u het opslag account kunt opgeven waarnaar u uw gegevens wilt uploaden. Opgeven `Azure Storage Account Name` , `Azure Storage Account Key` , `End point suffix` . Voeg de juiste EndpointSuffix van Azure toe, waarbij gegevens worden geüpload, wat de wereld wijde Azure, overheids Azure en Microsoft Azure Stack is. <br><br> U kunt ervoor kiezen om Azure Storage SAS-connection string hier op te geven. Maar u moet deze eigenschap bijwerken wanneer deze verloopt. SAS-machtigingen kunnen onder andere het maken van toegang voor containers omvatten en het maken, schrijven en toevoegen van toegang voor blobs.  <br><br> Omgevings variabele: `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Hiermee kunt u de namen van de containers opgeven die u wilt uploaden naar Azure. Met deze module kunt u zowel de bron-als de doel container naam opgeven. Als u de naam van de doel container niet opgeeft, wordt de naam van de container automatisch toegewezen als `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>` . U kunt sjabloon teken reeksen maken voor de naam van de doel container, de kolom mogelijke waarden bekijken. <br>*% h-> IoT Hub naam (3-50 tekens). <br>*% d-> IoT Edge apparaat-ID (1 tot 129 tekens). <br>*% m-> module naam (1 tot 64 tekens). <br>*% c-> bron container naam (3 tot 63 tekens). <br><br>De maximum grootte van de container naam is 63 tekens, terwijl de naam van de doel container automatisch wordt toegewezen als de grootte van de container groter is dan 63 tekens, wordt elke sectie (IoTHubName, IotEdgeDeviceID, module naam, SourceContainerName) in 15 tekens geknipt. <br><br> Omgevings variabele: `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| deleteAfterUpload | de waarde True, false | Standaard ingesteld op `false` . Als deze is ingesteld op `true` , worden de gegevens automatisch verwijderd wanneer het uploaden naar de Cloud opslag is voltooid. <br><br> **Let** op: als u toevoeg-blobs gebruikt, wordt met deze instelling toevoeg-blobs uit de lokale opslag verwijderd nadat het uploaden is geslaagd, en eventuele toekomstige toevoeg bewerkingen voor het blok keren van deze blobs mislukken. Gebruik deze instelling voorzichtig, schakel dit niet in als uw toepassing incidenteel toevoeg bewerkingen uitvoert of geen ondersteuning biedt voor doorlopende toevoeg bewerkingen<br><br> Omgevings variabele: `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` . |

### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

De naam van deze instelling is `deviceAutoDeleteProperties` . Als u de IoT Edge Simulator gebruikt, stelt u de waarden in op de verwante omgevings variabelen voor deze eigenschappen, die u in de sectie uitleg kunt vinden.

| Eigenschap | Mogelijke waarden | Uitleg |
| ----- | ----- | ---- |
| deleteOn | de waarde True, false | Standaard ingesteld op `false` . Als u de functie wilt inschakelen, stelt u dit veld in op `true` . <br><br> Omgevings variabele: `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Geef de tijd in minuten op. De module verwijdert automatisch uw blobs uit de lokale opslag wanneer deze waarde verloopt. Het huidige maximum aantal minuten dat is toegestaan, is 35791. <br><br> Omgevings variabele: `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | de waarde True, false | De standaard instelling is ingesteld op `true` , en de BLOB wordt bewaard tijdens het uploaden naar de Cloud opslag als deleteAfterMinutes verloopt. U kunt deze instellen op `false` en de gegevens worden verwijderd zodra deleteAfterMinutes verloopt. Opmerking: voor deze eigenschap werkt uploadOn moet worden ingesteld op True.  <br><br> **Let** op: als u toevoeg-blobs gebruikt, wordt met deze instelling toevoeg-blobs uit de lokale opslag verwijderd wanneer de waarde verloopt en eventuele toekomstige toevoeg bewerkingen voor het blok keren van deze blobs mislukken. U kunt ervoor zorgen dat de verloop waarde groot genoeg is voor de verwachte frequentie van toevoeg bewerkingen die door uw toepassing worden uitgevoerd.<br><br> Omgevings variabele: `deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>SMB-share gebruiken als lokale opslag

Als u een Windows-container van deze module op Windows host implementeert, kunt u SMB-share als uw lokale opslagpad opgeven.

Zorg ervoor dat de SMB-share en het IoT-apparaat zich in wederzijds vertrouwde domeinen bevinden.

U kunt `New-SmbGlobalMapping` de Power shell-opdracht uitvoeren om de SMB-share lokaal toe te wijzen op het IOT-apparaat waarop Windows wordt uitgevoerd.

Hieronder vindt u de configuratie stappen:

```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```

Bijvoorbeeld:

```powershell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G:
```

Met deze opdracht worden de referenties gebruikt voor verificatie bij de externe SMB-server. Wijs vervolgens het externe sharepad toe aan G: stationsletter (kan elk ander beschik bare stationsletter zijn). Het IoT-apparaat heeft nu het gegevens volume toegewezen aan een pad op het station G:.

Zorg ervoor dat de gebruiker in IoT-apparaat een lees-of schrijf bewerking naar de externe SMB-share kan uitvoeren.

Voor uw implementatie moet u de waarde `<storage mount>` **G:/ContainerData: C:/BlobRoot**.

## <a name="granting-directory-access-to-container-user-on-linux"></a>Directory toegang verlenen aan de container gebruiker op Linux

Als u [volume koppeling](https://docs.docker.com/storage/volumes/) voor opslag hebt gebruikt in uw Create-opties voor Linux-containers, hoeft u geen extra stappen uit te voeren, maar als u [BIND Mount](https://docs.docker.com/storage/bind-mounts/) hebt gebruikt, zijn deze stappen vereist om de service correct uit te voeren.

Na het beginsel van de minimale bevoegdheid om de toegangs rechten voor gebruikers te beperken tot bare minimale machtigingen die ze nodig hebben om hun werk uit te voeren, bevat deze module een gebruiker (naam: absie, ID: 11000) en een gebruikers groep (naam: absie, ID: 11000). Als de container is gestart als **root** (standaard gebruiker is **hoofdmap**), wordt onze service gestart als de **absie** -gebruiker met lage bevoegdheden.

Dit gedrag maakt het configureren van de machtigingen op het hostvolume van cruciaal belang voor een goede werking van de service, anders wordt de service vastlopen met geweigerde toegang. Het pad dat wordt gebruikt in Directory binding moet toegankelijk zijn voor de container gebruiker (voor beeld: absie 11000). U kunt de container gebruiker toegang verlenen tot de Directory door de onderstaande opdrachten op de host uit te voeren:

```terminal
sudo chown -R 11000:11000 <blob-dir>
sudo chmod -R 700 <blob-dir>
```

Bijvoorbeeld:

```terminal
sudo chown -R 11000:11000 /srv/containerdata
sudo chmod -R 700 /srv/containerdata
```

Als u de service als een andere gebruiker dan **absie** moet uitvoeren, kunt u uw aangepaste gebruikers-id opgeven in CreateOptions onder gebruiker-eigenschap in het implementatie manifest. In dat geval moet u de standaard-of basis groep-ID gebruiken `0` .

```json
"createOptions": {
  "User": "<custom user ID>:0"
}
```

Verleen de container gebruiker nu toegang tot de Directory

```terminal
sudo chown -R <user ID>:<group ID> <blob-dir>
sudo chmod -R 700 <blob-dir>
```

## <a name="configure-log-files"></a>Logboek bestanden configureren

Zie voor meer informatie over het configureren van logboek bestanden voor uw module deze [Aanbevolen procedures voor productie](./production-checklist.md#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Verbinding maken met de Blob Storage-module

U kunt de account naam en de account sleutel die u hebt geconfigureerd voor uw module gebruiken om toegang te krijgen tot de Blob-opslag op uw IoT Edge-apparaat.

Geef uw IoT Edge apparaat op als het BLOB-eind punt voor opslag aanvragen die u aanbrengt. U kunt [een Connection String maken voor een expliciet opslag eindpunt](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) met behulp van de IOT Edge apparaatgegevens en de account naam die u hebt geconfigureerd.

* Voor modules die op hetzelfde apparaat worden geïmplementeerd als waar de Azure Blob Storage op IoT Edge module wordt uitgevoerd, is het BLOB-eind punt: `http://<module name>:11002/<account name>` .
* Voor modules of toepassingen die op een ander apparaat worden uitgevoerd, moet u het juiste eind punt voor uw netwerk kiezen. Afhankelijk van uw netwerk instellingen, kiest u een eindpunt indeling, zodat het gegevens verkeer van uw externe module of toepassing het apparaat kan bereiken waarop de Azure-Blob Storage op IoT Edge module wordt uitgevoerd. Het BLOB-eind punt voor dit scenario is een van:
  * `http://<device IP >:11002/<account name>`
  * `http://<IoT Edge device hostname>:11002/<account name>`
  * `http://<fully qualified domain name>:11002/<account name>`
 
 > [!IMPORTANT]
 > Azure IoT Edge is hoofdletter gevoelig wanneer u aanroepen naar modules uitvoert en de opslag-SDK is standaard ingesteld op kleine letters. Hoewel de naam van de module in [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) **AzureBlobStorageonIoTEdge** is, is het wijzigen van de naam in kleine letters handig om ervoor te zorgen dat uw verbindingen met de Azure Blob Storage op IOT Edge module niet worden onderbroken.
 
## <a name="azure-blob-storage-quickstart-samples"></a>Voor beelden van Azure Blob Storage Quick Start

De documentatie voor Azure Blob Storage bevat voorbeeld code van Quick Start in verschillende talen. U kunt deze voor beelden uitvoeren om Azure Blob Storage te testen op IoT Edge door het BLOB-eind punt te wijzigen om verbinding te maken met de lokale Blob Storage-module.

De volgende Quick start-voor beelden gebruiken talen die ook door IoT Edge worden ondersteund, zodat u deze kunt implementeren als IoT Edge modules naast de module Blob Storage:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
  * De Azure Blob Storage op IOT Edge module v 1.4.0 en eerdere versies zijn compatibel met WindowsAzure. Storage 9.3.3 SDK en v 1.4.1 ondersteunt ook Azure. storage. blobs 12.8.0 SDK.
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * Versies vóór V 2.1 van de python-SDK hebben een bekend probleem waarbij de module de aanmaak tijd van de BLOB niet retourneert. Vanwege dat probleem werkt sommige methoden als lijst-blobs niet. Als tijdelijke oplossing stelt u de API-versie op de BLOB-client expliciet in op ' 2017-04-17 '. Hierbij  `block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [Voor beeld van BLOB toevoegen](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-legacy.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-legacy.md)
* [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [Go](../storage/blobs/storage-quickstart-blobs-go.md)
* [PHP](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Verbinding maken met uw lokale opslag met Azure Storage Explorer

U kunt [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gebruiken om verbinding te maken met uw lokale opslag account.

1. Azure Storage Explorer downloaden en installeren

1. Verbinding maken met Azure Storage met behulp van een connection string

1. Geef connection string op: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Door loop de stappen om verbinding te maken.

1. Container maken in uw lokale opslag account

1. Begin met het uploaden van bestanden als blok-blobs of toevoeg-blobs.
   > [!NOTE]
   > Deze module biedt geen ondersteuning voor pagina-blobs.

1. U kunt er ook voor kiezen om verbinding te maken met uw Azure Storage-accounts in Storage Explorer. Deze configuratie geeft u één weer gave voor uw lokale opslag account en een Azure-opslag account

## <a name="supported-storage-operations"></a>Ondersteunde opslag bewerkingen

Blob-opslag modules op IoT Edge de Azure Storage Sdk's gebruiken en zijn consistent met de 2017-04-17-versie van de Azure Storage API voor blok-BLOB-eind punten.

Omdat niet alle Azure Blob Storage bewerkingen worden ondersteund door Azure Blob Storage op IoT Edge, wordt in deze sectie de status van elk weer gegeven.

### <a name="account"></a>Account

Geboden

* Containers weergeven

Niet-ondersteunde

* Eigenschappen van de BLOB-service ophalen en instellen
* Preflight-BLOB-aanvraag
* Statistieken van blob-service ophalen
* Account gegevens ophalen

### <a name="containers"></a>Containers

Geboden

* Container maken en verwijderen
* Container eigenschappen en meta gegevens ophalen
* Blobs vermelden
* Container-ACL ophalen en instellen
* Metagegevens van container instellen

Niet-ondersteunde

* Lease-container

### <a name="blobs"></a>Blobs

Geboden

* BLOB plaatsen, ophalen en verwijderen
* Eigenschappen van BLOB ophalen en instellen
* BLOB-meta gegevens ophalen en instellen

Niet-ondersteunde

* Lease-BLOB
* Moment opname-BLOB
* Kopieer-BLOB kopiëren en afbreken
* Blob verwijderen ongedaan maken
* BLOB-laag instellen

### <a name="block-blobs"></a>Blok-blobs

Geboden

* Blok keren
* De blokkerings lijst plaatsen en ophalen

Niet-ondersteunde

* Blok van URL plaatsen

### <a name="append-blobs"></a>Toevoeg-blobs

Geboden

* Blok toevoegen

Niet-ondersteunde

* Blok van URL toevoegen

## <a name="event-grid-on-iot-edge-integration"></a>Event Grid op IoT Edge-integratie

> [!CAUTION]
> De integratie met Event Grid op IoT Edge is in de preview-versie

Deze Azure Blob Storage op IoT Edge module biedt nu integratie met Event Grid op IoT Edge. Voor gedetailleerde informatie over deze integratie raadpleegt [u de zelf studie voor het implementeren van modules, het publiceren van gebeurtenissen en het controleren van de levering van gebeurtenissen](../event-grid/edge/react-blob-storage-events-locally.md).

## <a name="release-notes"></a>Releaseopmerkingen

Dit zijn de [release opmerkingen in docker hub](https://hub.docker.com/_/microsoft-azure-blob-storage) voor deze module. Mogelijk kunt u meer informatie vinden met betrekking tot fout oplossingen en herstel in de release opmerkingen van een specifieke versie.

## <a name="suggestions"></a>Suggesties

Uw feedback is belang rijk voor ons om deze module en de bijbehorende functies nuttig en gebruiks vriendelijk te maken. Deel uw feedback en laat ons weten hoe we kunnen verbeteren.

U kunt ons bereiken op absiotfeedback@microsoft.com

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [implementeren van Azure Blob Storage op IOT Edge](how-to-deploy-blob.md)

Blijf up-to-date met recente updates en aankondigingen in de [Azure Blob Storage op IOT Edge Blog](https://aka.ms/abs-iot-blogpost)
