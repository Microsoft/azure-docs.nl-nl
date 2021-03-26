---
title: Overzicht van Azure page-blobs | Microsoft Docs
description: Een overzicht van Azure-pagina-blobs en hun voor delen, met inbegrip van gebruiks voorbeelden met voorbeeld scripts.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/15/2020
ms.author: tamram
ms.reviewer: wielriac
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 88c0d88a1d3119ef2fa00eb49da447749fde3221
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543836"
---
# <a name="overview-of-azure-page-blobs"></a>Overzicht van Azure-pagina-blobs

Azure Storage biedt drie typen Blob Storage: blok-blobs, toevoeg-blobs en pagina-blobs. Blok-blobs bestaan uit blokken en zijn ideaal voor het opslaan van tekst of binaire bestanden, en voor het efficiënt uploaden van grote bestanden. Toevoeg-blobs bestaan ook uit blokken, maar ze zijn geoptimaliseerd voor toevoeg bewerkingen, waardoor ze ideaal zijn voor logboek scenario's. Pagina-blobs bestaan uit 512-byte pagina's tot 8 TB in totale grootte en zijn ontworpen voor frequente, wille keurige Lees-en schrijf bewerkingen. Pagina-blobs zijn de basis van Azure IaaS-schijven. Dit artikel richt zich op de uitleg van de functies en voor delen van pagina-blobs.

Pagina-blobs zijn een verzameling van 512-byte pagina's, die de mogelijkheid bieden om wille keurig bereik van bytes te lezen/schrijven. Pagina-blobs zijn daarom ideaal voor het opslaan van op index gebaseerde en sparse gegevens structuren zoals besturings systeem-en gegevens schijven voor Virtual Machines en data bases. Azure SQL DB maakt bijvoorbeeld gebruik van pagina-blobs als de onderliggende permanente opslag voor de data bases. Daarnaast worden pagina-blobs ook vaak gebruikt voor bestanden met Range-Based-updates.  

De belangrijkste functies van Azure page-blobs zijn de REST interface, de duurzaamheid van de onderliggende opslag en de naadloze migratie mogelijkheden naar Azure. Deze functies worden gedetailleerd beschreven in de volgende sectie. Daarnaast worden Azure page-blobs momenteel ondersteund op twee soorten opslag: Premium Storage en standaard opslag. Premium Storage is speciaal ontworpen voor werk belastingen die consistente hoge prestaties en lage latentie nodig hebben om Premium-pagina-blobs te maken die ideaal zijn voor opslag scenario's met hoge prestaties. Standaard opslag accounts zijn rendabeler voor het uitvoeren van latentie gevoelige werk belastingen.

## <a name="restrictions"></a>Beperkingen

Pagina-blobs kunnen alleen de laag **Hot** Access gebruiken, ze kunnen geen **koele** of **Archief** lagen gebruiken. Zie [toegangs lagen voor Azure Blob Storage-hot, cool en Archive](storage-blob-storage-tiers.md)voor meer informatie over toegangs lagen.

## <a name="sample-use-cases"></a>Voorbeelden van toepassingsscenario’s

We bespreken enkele gebruiks voorbeelden voor pagina-blobs die beginnen met Azure IaaS-schijven. Azure-pagina-blobs zijn de ruggen graat van het platform voor virtuele schijven voor Azure IaaS. Zowel Azure-besturings systemen als-gegevens schijven worden geïmplementeerd als virtuele schijven waar gegevens blijvend worden opgeslagen in het Azure Storage-platform en vervolgens worden geleverd aan de virtuele machines voor maximale prestaties. Azure-schijven worden bewaard in de Hyper-V [VHD-indeling](/previous-versions/windows/it-pro/windows-7/dd979539(v=ws.10)) en opgeslagen als een pagina- [BLOB](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) in azure Storage. Naast het gebruik van virtuele schijven voor Azure IaaS Vm's, kunnen pagina-blobs ook PaaS-en DBaaS-scenario's, zoals Azure SQL DB-Service, die momenteel gebruikmaken van pagina-blobs voor het opslaan van SQL-gegevens en snelle, wille keurige Lees bewerkingen voor de data base inschakelen. Een ander voor beeld is dat als u een PaaS-service voor gedeelde media toegang hebt voor toepassingen voor gezamenlijke video bewerking, de pagina-blobs snelle toegang tot wille keurige locaties in de media bieden. Het biedt ook snelle en efficiënte bewerking en samen voeging van hetzelfde medium door meerdere gebruikers. 

Micro soft-services van derden, zoals Azure Site Recovery, Azure Backup, en veel ontwikkel aars hebben toonaangevende innovaties geïmplementeerd met behulp van de REST-interface van de pagina-blob. Hier volgen enkele van de unieke scenario's die in azure zijn geïmplementeerd: 

* Toepassings gerichte incrementele momentopname beheer: toepassingen kunnen gebruikmaken van pagina-BLOB-moment opnamen en REST Api's voor het opslaan van de controle punten van de toepassing zonder kost bare duplicatie van gegevens. Azure Storage ondersteunt lokale moment opnamen voor pagina-blobs, waarvoor het kopiëren van de hele BLOB niet is vereist. Deze open bare momentopname-Api's bieden ook toegang tot en het kopiëren van verschillen tussen moment opnamen.
* Livemigratie van toepassingen en gegevens van on-premises naar de Cloud: Kopieer de on-premises gegevens en gebruik REST-Api's om rechtstreeks naar een Azure-pagina-BLOB te schrijven terwijl de on-premises VM actief blijft. Zodra het doel actief is, kunt u snel een failover uitvoeren naar Azure VM met behulp van die gegevens. Op deze manier kunt u uw Vm's en virtuele schijven van on-premises naar de Cloud migreren met minimale downtime omdat de gegevens migratie op de achtergrond wordt uitgevoerd terwijl u de virtuele machine blijft gebruiken en de downtime die nodig is voor de failover kort is (in minuten).
* Gedeelde toegang [op basis van SAS](../common/storage-sas-overview.md) , waarmee scenario's zoals meerdere lezers en single Writer met ondersteuning voor gelijktijdigheids beheer worden ingeschakeld.

## <a name="pricing"></a>Prijzen

Beide typen opslag die worden aangeboden met pagina-blobs hebben hun eigen prijs model. Premium-pagina-blobs volgen het prijs model Managed disks, terwijl standaard pagina-blobs worden gefactureerd op basis van gebruikte grootte en met elke trans actie. Zie de pagina met prijzen voor [Azure-pagina-blobs](https://azure.microsoft.com/pricing/details/storage/page-blobs/)voor meer informatie.

## <a name="page-blob-features"></a>Functies voor pagina-blobs

### <a name="rest-api"></a>REST-API

Raadpleeg het volgende document om aan de slag te gaan met [ontwikkelen met behulp van pagina-blobs](./storage-quickstart-blobs-dotnet.md). Bekijk bijvoorbeeld hoe u met de Storage-client bibliotheek voor .NET toegang hebt tot pagina-blobs. 

In het volgende diagram worden de algemene relaties tussen accounts, containers en pagina-blobs beschreven.

![Scherm opname van relaties tussen accounts, containers en pagina-blobs](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Een lege pagina-Blob maken van een opgegeven grootte

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Eerst moet u een verwijzing naar een container ophalen. Als u een pagina-BLOB wilt maken, roept u de methode GetPageBlobClient aan en roept u de methode [PageBlobClient. Create](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.create) aan. Geef de maximale grootte door om de BLOB te maken. De grootte moet een meervoud van 512 bytes zijn.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_CreatePageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Als u een pagina-BLOB wilt maken, maakt u eerst een **CloudBlobClient** -object met de basis-URI voor toegang tot de Blob-opslag voor uw opslag account (*pbaccount* in afbeelding 1) samen met het object **StorageCredentialsAccountAndKey** , zoals wordt weer gegeven in het volgende voor beeld. In het voor beeld wordt vervolgens een verwijzing naar een **CloudBlobContainer** -object gemaakt en vervolgens de container (*testvhds*) gemaakt als deze nog niet bestaat. Maak vervolgens met behulp van het object **CloudBlobContainer** een verwijzing naar een **CloudPageBlob** -object door de naam van de pagina-BLOB (OS4. VHD) op te geven die u wilt openen. Als u de pagina-BLOB wilt maken, roept u [CloudPageBlob. Create](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.create)aan en geeft u de maximale grootte voor de BLOB op die moet worden gemaakt. De *blobSize* moet een meervoud van 512 bytes zijn.

```csharp
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

long OneGigabyteAsBytes = 1024 * 1024 * 1024;
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("testvhds");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();

CloudPageBlob pageBlob = container.GetPageBlobReference("os4.vhd");
pageBlob.Create(16 * OneGigabyteAsBytes);
```

---

#### <a name="resizing-a-page-blob"></a>Het formaat van een pagina-BLOB wijzigen

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Als u na het maken het formaat van een pagina-BLOB wilt wijzigen, gebruikt u de methode [formaat wijzigen](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.resize) . De aangevraagde grootte moet een meervoud van 512 bytes zijn.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ResizePageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Als u na het maken het formaat van een pagina-BLOB wilt wijzigen, gebruikt u de methode [formaat wijzigen](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.resize) . De aangevraagde grootte moet een meervoud van 512 bytes zijn.

```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes);
```

---

#### <a name="writing-pages-to-a-page-blob"></a>Pagina's naar een pagina-BLOB schrijven

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Als u pagina's wilt schrijven, gebruikt u de methode [PageBlobClient. UploadPages](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.uploadpages) .  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_WriteToPageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Als u pagina's wilt schrijven, gebruikt u de methode [CloudPageBlob. WritePages](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.beginwritepages) .  

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

---

Zo kunt u een reeks pagina's naar 4MBs schrijven. De offset waarnaar wordt geschreven, moet beginnen met een 512-byte-grens (startingOffset %512 = = 0) en eindigen op een 512 grens-1. 

Zodra een schrijf aanvraag voor een opeenvolgende set pagina's slaagt in de BLOB-service en wordt gerepliceerd voor duurzaamheid en tolerantie, wordt de schrijf bewerking doorgevoerd en wordt het resultaat teruggestuurd naar de client.  

In het onderstaande diagram ziet u twee afzonderlijke schrijf bewerkingen:

![Een diagram waarin de twee afzonderlijke schrijf opties worden weer gegeven.](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Een schrijf bewerking beginnend bij een offset van 0 tot en met een lengte van 1024 bytes 
2.  Een schrijf bewerking vanaf een offset van 4096 met een lengte van 1024 

#### <a name="reading-pages-from-a-page-blob"></a>Pagina's lezen van een pagina-BLOB

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Als u pagina's wilt lezen, gebruikt u de methode [PageBlobClient. down load](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadto) om een bereik aan bytes te lezen van de pagina-blob. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ReadFromPageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Als u pagina's wilt lezen, gebruikt u de methode [CloudPageBlob. DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.icloudblob.downloadrangetobytearray) om een bereik aan bytes te lezen van de pagina-blob. 

```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```

---

Zo kunt u de volledige BLOB of het bereik van de bytes downloaden, te beginnen bij een offset in de blob. Bij het lezen hoeft de offset niet te beginnen op een meervoud van 512. Bij het lezen van bytes van een NUL-pagina retourneert de service nul bytes.

In de volgende afbeelding ziet u een lees bewerking met een offset van 256 en een bereik grootte van 4352. Gegevens die worden geretourneerd, worden oranje gemarkeerd. Nullen worden geretourneerd voor NUL pagina's.

![Een diagram met een lees bewerking met een offset van 256 en een bereik grootte van 4352](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Als u een met sparse gevulde BLOB hebt, wilt u misschien alleen de geldige pagina regio's downloaden om te voor komen dat u betaalt voor egressing van nul bytes en om de latentie van de down load te verminderen.  

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Gebruik [PageBlobClient. GetPageRanges](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.getpageranges)om te bepalen voor welke pagina's een back-up van gegevens wordt gemaakt. U kunt vervolgens de geretourneerde bereiken opsommen en de gegevens in elk bereik downloaden. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ReadValidPageRegionsFromPageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Gebruik [CloudPageBlob. GetPageRanges](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.getpageranges)om te bepalen voor welke pagina's een back-up van gegevens wordt gemaakt. U kunt vervolgens de geretourneerde bereiken opsommen en de gegevens in elk bereik downloaden. 

```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the range size
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and
    // place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // Then use the buffer for the page range just read
}
```

---

#### <a name="leasing-a-page-blob"></a>Een pagina-BLOB leasen

Met de bewerking lease BLOB wordt een vergren deling voor een BLOB tot stand gebracht en beheerd voor schrijf-en verwijder bewerkingen. Deze bewerking is handig in scenario's waarbij een pagina-BLOB wordt geopend vanaf meerdere clients om ervoor te zorgen dat er slechts één client tegelijk naar de Blob kan schrijven. Azure disks maakt bijvoorbeeld gebruik van dit lease mechanisme om ervoor te zorgen dat de schijf alleen wordt beheerd door één virtuele machine. De vergrendelings duur kan 15 tot 60 seconden zijn, of kan oneindig zijn. Raadpleeg [de documentatie voor](/rest/api/storageservices/lease-blob) meer informatie.

Naast Rich REST-Api's bieden pagina-blobs ook gedeelde toegang, duurzaamheid en verbeterde beveiliging. Deze voor delen worden in de volgende alinea's uitvoeriger besproken. 

### <a name="concurrent-access"></a>Gelijktijdige toegang

Met de pagina-blobs REST API en het lease mechanisme kunnen toepassingen vanaf meerdere clients toegang krijgen tot de pagina-blob. Stel bijvoorbeeld dat u een gedistribueerde Cloud service moet maken die opslag objecten deelt met meerdere gebruikers. Dit kan een webtoepassing zijn die een grote verzameling installatie kopieën aan verschillende gebruikers levert. Een optie voor het implementeren hiervan is het gebruik van een virtuele machine met gekoppelde schijven. Dit nadeel is onder andere, (i) de beperking die een schijf alleen kan worden gekoppeld aan één virtuele machine, waardoor de schaal baarheid, flexibiliteit en toename van Risico's worden beperkt. Als er een probleem is met de virtuele machine of de service die op de virtuele machine wordt uitgevoerd, is de installatie kopie vanwege de lease ontoegankelijk totdat de lease verloopt of verbroken is. en (II) extra kosten voor het gebruik van een IaaS-VM. 

Een alternatieve optie is het gebruik van de pagina-blobs rechtstreeks via Azure Storage REST Api's. Deze optie elimineert de nood zaak van dure IaaS Vm's, biedt volledige flexibiliteit van rechtstreekse toegang vanaf meerdere clients, vereenvoudigt het klassieke implementatie model door de nood zaak om schijven te koppelen/loskoppelen te elimineren en elimineert het risico van problemen op de virtuele machine. En biedt het hetzelfde prestatie niveau voor wille keurige Lees-en schrijf bewerkingen als een schijf

### <a name="durability-and-high-availability"></a>Duurzaamheid en hoge Beschik baarheid

De standaard-en Premium-opslag zijn duurzame opslag waar de gegevens van de pagina-BLOB altijd worden gerepliceerd om duurzaamheid en hoge Beschik baarheid te garanderen. Raadpleeg deze [documentatie](../common/storage-redundancy.md)voor meer informatie over Azure Storage redundantie. Azure heeft consistente duurzaamheid op ondernemings niveau geboden voor IaaS-schijven en pagina-blobs, met een toonaangevende uitval percentage van 0 procent [per jaar](https://en.wikipedia.org/wiki/Annualized_failure_rate).

### <a name="seamless-migration-to-azure"></a>Naadloze migratie naar Azure

Voor klanten en ontwikkel aars die geïnteresseerd zijn in het implementeren van hun eigen aangepaste back-upoplossing, biedt Azure ook incrementele moment opnamen die alleen de verschillen bevatten. Deze functie voor komt de kosten van de eerste volledige kopie, waardoor de back-upkosten aanzienlijk lager worden. Naast de mogelijkheid om op efficiënte wijze differentiële gegevens te lezen en te kopiëren, is dit een krachtige mogelijkheid waarmee ontwikkel aars nog meer innovaties kunnen maken, waardoor er een beste ervaring met back-ups en herstel na nood gevallen (DR) in azure mogelijk is. U kunt uw eigen back-up-of DR-oplossing voor uw virtuele machines in azure instellen met behulp van [BLOB-moment opname](/rest/api/storageservices/snapshot-blob) samen met de API [Get Page Ranges](/rest/api/storageservices/get-page-ranges) en de BLOB-API voor [incrementele kopieën](/rest/api/storageservices/incremental-copy-blob) , die u kunt gebruiken voor het eenvoudig kopiëren van de incrementele gegevens voor Dr. 

Daarnaast hebben veel bedrijven essentiële werk belastingen die al worden uitgevoerd in on-premises data centers. Voor de migratie van de werk belasting naar de Cloud is een van de belangrijkste problemen de uitval tijd die nodig is voor het kopiëren van de gegevens en het risico dat er onverwachte problemen zijn na het overschakelen. In veel gevallen kan de downtime een showstopper zijn voor migratie naar de Cloud. Met behulp van de pagina-blobs REST API wordt dit probleem door Azure opgelost door Cloud migratie in te scha kelen met minimale onderbreking van kritieke werk belastingen. 

Voor voor beelden van hoe u een moment opname maakt en hoe u een pagina-BLOB herstelt vanuit een moment opname, raadpleegt u het artikel [een back-upproces instellen met incrementele moment opnamen](../../virtual-machines/windows/incremental-snapshots.md) .