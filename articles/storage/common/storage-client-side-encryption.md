---
title: Versleuteling Client-Side met .NET voor Microsoft Azure Storage | Microsoft Docs
description: De Azure Storage-client bibliotheek voor .NET ondersteunt versleuteling en integratie van client zijde met Azure Key Vault voor een maximale beveiliging van uw Azure Storage toepassingen.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/16/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 9d00b6aa09ef19b1e6892e0e90536e45dd3bce79
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101718519"
---
# <a name="client-side-encryption-and-azure-key-vault-for-microsoft-azure-storage"></a>Client-Side versleuteling en Azure Key Vault voor Microsoft Azure Storage

[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Overzicht

De [Azure Storage-client bibliotheek voor .net](/dotnet/api/overview/azure/storage) biedt ondersteuning voor het versleutelen van gegevens in client toepassingen voordat u ze uploadt naar Azure Storage en gegevens ontsleutelt tijdens het downloaden van de client. De bibliotheek ondersteunt ook integratie met [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) voor sleutel beheer van het opslag account.

Zie [Microsoft Azure Storage blobs versleutelen en ontsleutelen met behulp van Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)voor een stapsgewijze zelf studie die u helpt bij het versleutelen van blobs met versleuteling aan client zijde en Azure Key Vault.

Zie [versleuteling aan client zijde met Java voor Microsoft Azure Storage](storage-client-side-encryption-java.md)voor versleuteling aan de client zijde met Java.

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Versleuteling en ontsleuteling via de envelop techniek

De versleutelings processen en ontsleuteling volgen de envelop techniek.

### <a name="encryption-via-the-envelope-technique"></a>Versleuteling via de envelop techniek

Versleuteling via de envelop techniek werkt op de volgende manier:

1. De Azure Storage-client bibliotheek genereert een coderings sleutel voor inhoud (CEK). Dit is een eenmalige symmetrische sleutel.
2. Gebruikers gegevens worden versleuteld met behulp van deze CEK.
3. De CEK wordt vervolgens verpakt (versleuteld) met behulp van de versleutelingssleutel voor sleutel (KEK). De KEK wordt geïdentificeerd aan de hand van een sleutel-id en kan een asymmetrisch sleutel paar of een symmetrische sleutel zijn en kan lokaal worden beheerd of opgeslagen in azure-sleutel kluizen.

    De Storage-client bibliotheek zelf heeft nooit toegang tot KEK. De bibliotheek roept de sleutel terugloop algoritme aan die wordt verschaft door Key Vault. Gebruikers kunnen ervoor kiezen om aangepaste providers te gebruiken voor sleutel terugloop/uitpakken, indien gewenst.

4. De versleutelde gegevens worden vervolgens geüpload naar de Azure Storage-service. De ingepakte sleutel en enkele extra versleutelings meta gegevens worden opgeslagen als meta gegevens (op een blob) of worden geïnterpoleerd met de versleutelde gegevens (wachtrij berichten en tabel entiteiten).

### <a name="decryption-via-the-envelope-technique"></a>Ontsleuteling via de envelop techniek

Ontsleuteling via de envelop techniek werkt op de volgende manier:

1. De client bibliotheek gaat ervan uit dat de gebruiker de Key Encryption Key (KEK) lokaal of in azure-sleutel kluizen beheert. De gebruiker hoeft geen kennis te hebben van de specifieke sleutel die voor versleuteling is gebruikt. In plaats daarvan kan een Key resolver waarmee verschillende sleutel-id's worden omgezet in sleutels, worden ingesteld en gebruikt.
2. De versleutelde gegevens worden door de client bibliotheek gedownload, samen met het versleutelings materiaal dat is opgeslagen in de service.
3. De versleutelings sleutel van de verpakte inhoud (CEK) wordt vervolgens met behulp van de sleutel versleutelings sleutel (KEK) gepakt (gedecodeerd). Hier is de client bibliotheek geen toegang tot KEK. Hiermee wordt het algoritme voor het uitpakken van de aangepaste of Key Vault Provider aangeroepen.
4. De versleutelings sleutel voor inhoud (CEK) wordt vervolgens gebruikt om de versleutelde gebruikers gegevens te ontsleutelen.

## <a name="encryption-mechanism"></a>Versleutelings mechanisme

De Storage-client bibliotheek gebruikt [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) om gebruikers gegevens te versleutelen. Met name de [CBC-modus (Cipher Block Chaining)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) met AES. Elke service werkt enigszins anders, dus we bespreken deze hier.

### <a name="blobs"></a>Blobs

De client bibliotheek ondersteunt momenteel alleen versleuteling van hele blobs. Voor down loads worden zowel volledige als bereik downloads ondersteund.

Tijdens het versleutelen genereert de client bibliotheek een wille keurige initialisatie vector (IV) van 16 bytes, samen met een wille keurige inhouds versleutelings sleutel (CEK) van 32 bytes en voert u een envelop versleuteling van de BLOB-gegevens uit met deze gegevens. De verpakte CEK en andere versleutelings-meta gegevens worden vervolgens opgeslagen als blob-meta gegevens, samen met de versleutelde Blob in de service.

> [!WARNING]
> Als u uw eigen meta gegevens voor de BLOB bewerkt of uploadt, moet u ervoor zorgen dat deze meta gegevens behouden blijven. Als u nieuwe meta gegevens zonder deze meta gegevens uploadt, gaan de verpakte CEK, IV en andere meta gegevens verloren en kan de inhoud van de BLOB niet meer worden opgehaald.

Bij het downloaden van een volledige blob is de ingepakte CEK gewrappt en gebruikt in combi natie met de IV (opgeslagen als blob-meta gegevens in dit geval) om de ontsleutelde gegevens te retour neren aan de gebruikers.

Als u een wille keurig bereik in de versleutelde BLOB downloadt, moet u het bereik dat door de gebruikers wordt verstrekt, aanpassen om een klein aantal extra gegevens te krijgen dat kan worden gebruikt om het aangevraagde bereik te ontsleutelen.

Alle BLOB-typen (blok-blobs, pagina-blobs en toevoeg-blobs) kunnen worden versleuteld/ontsleuteld met dit schema.

### <a name="queues"></a>Wachtrijen

Omdat wachtrij berichten een wille keurige indeling hebben, definieert de client bibliotheek een aangepaste indeling die de initialisatie vector (IV) en de versleutelde inhouds versleutelings sleutel (CEK) bevat in de bericht tekst.

Tijdens het versleutelen genereert de client bibliotheek een wille keurige IV van 16 bytes samen met een wille keurige CEK van 32 bytes en wordt een envelop versleuteling van de bericht tekst van de wachtrij uitgevoerd met behulp van deze gegevens. De verpakte CEK en andere versleutelings-meta gegevens worden vervolgens toegevoegd aan het versleutelde bericht in de wachtrij. Dit gewijzigde bericht (hieronder weer gegeven) wordt opgeslagen op de service.

```xml
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

Tijdens de ontsleuteling wordt de ingepakte sleutel geëxtraheerd uit het wachtrij bericht en genest. De IV wordt ook geëxtraheerd uit het wachtrij bericht en gebruikt samen met de niet-ingepakte sleutel voor het ontsleutelen van de wachtrij bericht gegevens. Houd er rekening mee dat de meta gegevens van de versleutelings functie klein zijn (minder dan 500 bytes), dus wanneer het telt voor de limiet van 64 kB voor een wachtrij bericht, moet de impact kunnen worden beheerd. Houd er rekening mee dat het versleutelde bericht base64-gecodeerd is, zoals wordt weer gegeven in het bovenstaande code fragment, waarmee de grootte van het bericht dat wordt verzonden, ook kan worden uitgebreid.

### <a name="tables"></a>Tabellen

> [!NOTE]
> De Table service wordt alleen ondersteund in de Azure Storage-client bibliotheek via versie 9. x.

De client bibliotheek ondersteunt versleuteling van entiteits eigenschappen voor INSERT-en Replace-bewerkingen.

> [!NOTE]
> Samen voegen wordt momenteel niet ondersteund. Omdat een subset van eigenschappen mogelijk eerder is versleuteld met een andere sleutel, worden de nieuwe eigenschappen gebundeld en worden de meta gegevens bijgewerkt als gevolg van gegevens verlies. Samen voegen vereist het maken van extra service aanroepen om de vooraf bestaande entiteit van de service te lezen of een nieuwe sleutel per eigenschap te gebruiken, die beide niet geschikt zijn om prestatie redenen.

Tabel gegevens versleuteling werkt als volgt:  

1. Gebruikers geven de eigenschappen op die moeten worden versleuteld.
2. De client bibliotheek genereert een wille keurige initialisatie vector (IV) van 16 bytes samen met een wille keurige inhouds versleutelings sleutel (CEK) van 32 bytes voor elke entiteit en voert een envelop versleuteling uit voor de afzonderlijke eigenschappen die moeten worden versleuteld door een nieuwe IV per eigenschap af te leiden. De versleutelde eigenschap wordt opgeslagen als binaire gegevens.
3. De verpakte CEK en andere versleutelings meta gegevens worden vervolgens opgeslagen als twee extra gereserveerde eigenschappen. De eerste gereserveerde eigenschap (_ClientEncryptionMetadata1) is een teken reeks eigenschap die de informatie over IV, versie en ingepakte sleutel bevat. De tweede gereserveerde eigenschap (_ClientEncryptionMetadata2) is een binaire eigenschap die de informatie bevat over de versleutelde eigenschappen. De informatie in deze tweede eigenschap (_ClientEncryptionMetadata2) is zelf versleuteld.
4. Als gevolg van deze aanvullende gereserveerde eigenschappen die vereist zijn voor versleuteling, kunnen gebruikers nu slechts 250 aangepaste eigenschappen hebben in plaats van 252. De totale grootte van de entiteit moet kleiner zijn dan 1 MB.

Houd er rekening mee dat alleen teken reeks eigenschappen kunnen worden versleuteld. Als andere typen eigenschappen moeten worden versleuteld, moeten ze worden geconverteerd naar teken reeksen. De versleutelde teken reeksen worden als binaire eigenschappen opgeslagen op de service en worden teruggeconverteerd naar teken reeksen na ontsleuteling.

Voor tabellen, naast het versleutelings beleid, moeten gebruikers de eigenschappen opgeven die moeten worden versleuteld. U kunt dit doen door ofwel een [EncryptProperty]-kenmerk op te geven (voor POCO-entiteiten die zijn afgeleid van TableEntity) of een Encryption resolver in de aanvraag opties. Een Encryption resolver is een gemachtigde die een partitie sleutel, een rij-en een eigenschaps naam gebruikt en een Booleaanse waarde retourneert die aangeeft of die eigenschap moet worden versleuteld. Tijdens de versleuteling gebruikt de client bibliotheek deze gegevens om te bepalen of een eigenschap moet worden versleuteld tijdens het schrijven naar de kabel. De gemachtigde biedt ook de mogelijkheid van logica om te bepalen hoe eigenschappen worden versleuteld. (Bijvoorbeeld als X, vervolgens versleutelings eigenschap A; anders eigenschappen A en B versleutelen.) Houd er rekening mee dat deze informatie niet hoeft te worden verstrekt tijdens het lezen of doorzoeken van entiteiten.

### <a name="batch-operations"></a>Batch bewerkingen

In batch-bewerkingen wordt dezelfde KEK gebruikt voor alle rijen in die batch bewerking omdat de client bibliotheek slechts één Options-object (en dus één beleid/KEK) per batch-bewerking toestaat. In de-client bibliotheek wordt echter intern een nieuwe wille keurige steek proef en wille keurige CEK per rij in de batch gegenereerd. Gebruikers kunnen er ook voor kiezen om verschillende eigenschappen te versleutelen voor elke bewerking in de batch door dit gedrag te definiëren in de versleutelings conflict Oplosser.

### <a name="queries"></a>Query's

> [!NOTE]
> Omdat de entiteiten zijn versleuteld, kunt u geen query's uitvoeren die filteren op een versleutelde eigenschap.  Als u probeert, zijn de resultaten onjuist, omdat de service probeert versleutelde gegevens te vergelijken met niet-versleutelde gegevens.
>
> Als u query bewerkingen wilt uitvoeren, moet u een sleutel conflict Oplosser opgeven waarmee alle sleutels in de resultatenset kunnen worden omgezet. Als een entiteit in het query resultaat niet kan worden omgezet naar een provider, wordt een fout gegenereerd door de client bibliotheek. Voor elke query die projecties aan de server zijde uitvoert, voegt de client bibliotheek de speciale eigenschappen van de meta gegevens voor versleuteling (_ClientEncryptionMetadata1 en _ClientEncryptionMetadata2) standaard toe aan de geselecteerde kolommen.

## <a name="azure-key-vault"></a>Azure Key Vault

Met Azure Sleutelkluis kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Met behulp van Azure Key Vault kunnen gebruikers sleutels en geheimen versleutelen (zoals verificatie sleutels, sleutels voor opslag accounts, sleutels voor gegevens versleuteling,. PFX-bestanden en wacht woorden) met behulp van sleutels die worden beveiligd door Hardware Security modules (Hsm's). Zie [Wat is Azure Key Vault?](../../key-vault/general/overview.md) voor meer informatie.

De Storage-client bibliotheek gebruikt de Key Vault interfaces in de kern bibliotheek om een gemeen schappelijk Framework te bieden in azure voor het beheren van sleutels. Gebruikers kunnen gebruikmaken van Key Vault bibliotheken voor alle extra voor delen die ze bieden, zoals handige functionaliteit rond eenvoudige en naadloze symmetrische/RSA-lokale en Cloud sleutel providers, evenals hulp bij aggregatie en caching.

### <a name="interface-and-dependencies"></a>Interface en afhankelijkheden

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Er zijn twee vereiste pakketten voor de integratie van Key Vault:

* Azure. Core bevat de `IKeyEncryptionKey` en- `IKeyEncryptionKeyResolver` interfaces. De Storage-client bibliotheek voor .NET definieert deze al als een afhankelijkheid.
* Azure. Security. keys (v4. x) bevat de Key Vault REST-client, evenals cryptografische clients die worden gebruikt met versleuteling aan de client zijde.

Key Vault is ontworpen voor hoogwaardige hoofd sleutels en beperkings limieten per Key Vault zijn met dit doel ontworpen. Vanaf Azure. Security. Keys 4.1.0 is er geen `IKeyEncryptionKeyResolver` implementatie die ondersteuning biedt voor sleutel opslag. Als u de cache nodig hebt om te beperken, kan [dit voor beeld](https://docs.microsoft.com/samples/azure/azure-sdk-for-net/azure-key-vault-proxy/) worden gevolgd om een cache-laag in een-exemplaar te injecteren `Azure.Security.KeyVault.Keys.Cryptography.KeyResolver` .

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Er zijn drie Key Vault pakketten:

* Micro soft. Azure. de sleutel kluis. Core bevat de IKey en IKeyResolver. Het is een klein pakket zonder afhankelijkheden. De Storage-client bibliotheek voor .NET definieert deze als een afhankelijkheid.
* Micro soft. Azure. de sleutel kluis (v3. x) bevat de Key Vault REST-client.
* Micro soft. Azure. de sleutel kluis. Extensions (v3. x) bevat extensie code die implementaties van cryptografische algoritmen en een RSAKey en een SymmetricKey bevat. Dit is afhankelijk van de core-en de naam ruimte van de hoofd kluis en biedt functionaliteit voor het definiëren van een aggregatie resolver (wanneer gebruikers meerdere sleutel providers willen gebruiken) en een cache sleutel conflict Oplosser. Hoewel de opslag-client bibliotheek niet rechtstreeks afhankelijk is van dit pakket, moet u dit pakket hebben als gebruikers Azure Key Vault willen gebruiken om hun sleutels op te slaan of de Key Vault extensies te gebruiken voor het gebruik van de lokale en Cloud cryptografie providers.

Key Vault is ontworpen voor hoogwaardige hoofd sleutels en beperkings limieten per Key Vault zijn met dit doel ontworpen. Bij het uitvoeren van versleuteling aan de client zijde met Key Vault moet het voorkeurs model gebruikmaken van symmetrische hoofd sleutels die zijn opgeslagen als geheimen in Key Vault en lokaal in de cache worden geplaatst. Gebruikers moeten het volgende doen:

1. Maak een geheim offline en upload het naar Key Vault.
2. Gebruik de basis-id van het geheim als para meter om de huidige versie van het geheim voor versleuteling op te lossen en deze gegevens lokaal op te slaan in de cache. CachingKeyResolver gebruiken voor caching; gebruikers worden niet verwacht hun eigen cache logica te implementeren.
3. Gebruik de caching-resolver als invoer bij het maken van het versleutelings beleid.

Meer informatie over Key Vault gebruik in V11 vindt u in de voor [beelden van V11](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples)-code ring.

---

## <a name="best-practices"></a>Aanbevolen procedures

Ondersteuning voor versleuteling is alleen beschikbaar in de Storage-client bibliotheek voor .NET. Windows Phone en Windows Runtime ondersteunen momenteel geen versleuteling.

> [!IMPORTANT]
> Houd rekening met de volgende belang rijke punten wanneer versleuteling aan de client zijde wordt gebruikt:
>
> * Bij het lezen van of schrijven naar een versleutelde BLOB gebruikt u hele BLOB-upload opdrachten en bereik/hele BLOB-Download opdrachten. Vermijd het schrijven naar een versleutelde blob met behulp van protocol bewerkingen zoals put blok keren, blokkerings lijst plaatsen, schrijf pagina's, pagina's wissen of blok toevoegen. anders is het mogelijk dat u de versleutelde BLOB beschadigd en onleesbaar maakt.
> * Voor tabellen bestaat een soort gelijke beperking. Zorg ervoor dat u geen versleutelde eigenschappen bijwerkt zonder de versleutelings meta gegevens bij te werken.
> * Als u meta gegevens op de versleutelde BLOB instelt, kunt u de meta gegevens voor versleuteling overschrijven die vereist zijn voor ontsleuteling, omdat het instellen van meta gegevens geen additief is. Dit geldt ook voor moment opnamen. Vermijd het opgeven van meta gegevens tijdens het maken van een moment opname van een versleutelde blob. Als meta gegevens moeten worden ingesteld, moet u eerst de **FetchAttributes** -methode aanroepen om de huidige versleutelings-meta gegevens op te halen en te voor komen dat er gelijktijdige schrijf bewerkingen worden uitgevoerd terwijl de meta gegevens worden ingesteld.
> * Schakel de eigenschap **RequireEncryption** in de standaard opties voor aanvragen in voor gebruikers die alleen moeten werken met versleutelde gegevens. Zie hieronder voor meer informatie.

## <a name="client-api--interface"></a>Client-API/-interface

Gebruikers kunnen alleen een sleutel, alleen een resolver of beide opgeven. Sleutels worden geïdentificeerd met behulp van een sleutel-id en bieden de logica voor het verpakken/uitpakken. Resolvers worden gebruikt voor het omzetten van een sleutel tijdens het ontsleutelen. Hiermee wordt een methode voor het oplossen van conflicten gedefinieerd die een sleutel retourneert met een sleutel-id. Dit biedt gebruikers de mogelijkheid om te kiezen tussen meerdere sleutels die worden beheerd op meerdere locaties.

* Voor versleuteling wordt de sleutel altijd gebruikt en wordt er een fout veroorzaakt door het ontbreken van een sleutel.
* Voor ontsleuteling:
  * Als de sleutel is opgegeven en de id overeenkomt met de vereiste sleutel-id, wordt die sleutel gebruikt voor ontsleuteling. Anders wordt de resolver geprobeerd. Als er geen resolver voor deze poging is, wordt er een fout gegenereerd.
  * De Key resolver wordt aangeroepen als deze is opgegeven om de sleutel op te halen. Als de resolver is opgegeven maar geen toewijzing voor de sleutel-id heeft, wordt er een fout gegenereerd.

### <a name="requireencryption-mode-v11-only"></a>RequireEncryption-modus (alleen V11)

Gebruikers kunnen eventueel een bewerkings modus inschakelen, waarbij alle uploads en down loads moeten worden versleuteld. In deze modus wordt geprobeerd gegevens te uploaden zonder versleutelings beleid of gegevens te downloaden die niet zijn versleuteld op de service, mislukt de client. De eigenschap **RequireEncryption** van het object aanvraag opties bepaalt dit gedrag. Als uw toepassing alle objecten versleutelt die zijn opgeslagen in Azure Storage, kunt u de eigenschap **RequireEncryption** instellen voor de standaard aanvraag opties voor het object service-client. Stel bijvoorbeeld **CloudBlobClient. DefaultRequestOptions. RequireEncryption** in op **True** om versleuteling te vereisen voor alle BLOB-bewerkingen die via dat client object worden uitgevoerd.

### <a name="blob-service-encryption"></a>Versleuteling Blob service

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Maak een **ClientSideEncryptionOptions** -object en stel dit in op client maken met **SpecializedBlobClientOptions**. U kunt geen versleutelings opties per API instellen. Alle overige worden intern door de client bibliotheek verwerkt.

```csharp
// Your key and key resolver instances, either through KeyVault SDK or an external implementation
IKeyEncryptionKey key;
IKeyEncryptionKeyResolver keyResolver;

// Create the encryption options to be used for upload and download.
ClientSideEncryptionOptions encryptionOptions = new ClientSideEncryptionOptions(ClientSideEncryptionVersion.V1_0)
{
   KeyEncryptionKey = key,
   KeyResolver = keyResolver,
   // string the storage client will use when calling IKeyEncryptionKey.WrapKey()
   KeyWrapAlgorithm = "some algorithm name"
};

// Set the encryption options on the client options
BlobClientOptions options = new SpecializedBlobClientOptions() { ClientSideEncryption = encryptionOptions };

// Get your blob client with client-side encryption enabled.
// Client-side encryption options are passed from service to container clients, and container to blob clients.
// Attempting to construct a BlockBlobClient, PageBlobClient, or AppendBlobClient from a BlobContainerClient
// with client-side encryption options present will throw, as this functionality is only supported with BlobClient.
BlobClient blob = new BlobServiceClient(connectionString, options).GetBlobContainerClient("my-container").GetBlobClient("myBlob");

// Upload the encrypted contents to the blob.
blob.Upload(stream);

// Download and decrypt the encrypted contents from the blob.
MemoryStream outputStream = new MemoryStream();
blob.DownloadTo(outputStream);
```

Een **BlobServiceClient** is niet nodig om versleutelings opties toe te passen. Ze kunnen ook worden door gegeven aan **BlobContainerClient** / **BlobClient** -constructors die **BlobClientOptions** -objecten accepteren.

Als een gewenst **BlobClient** -object al bestaat, maar zonder versleutelings opties aan de client zijde, bestaat een uitbreidings methode voor het maken van een kopie van dat object met de opgegeven **ClientSideEncryptionOptions**. Deze extensie methode vermijdt de overhead van een volledig nieuwe **BlobClient** -object.

```csharp
using Azure.Storage.Blobs.Specialized;

// Your existing BlobClient instance and encryption options
BlobClient plaintextBlob;
ClientSideEncryptionOptions encryptionOptions;

// Get a copy of plaintextBlob that uses client-side encryption
BlobClient clientSideEncryptionBlob = plaintextBlob.WithClientSideEncryptionOptions(encryptionOptions);
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Maak een **BlobEncryptionPolicy** -object en stel dit in de aanvraag opties in (per API of op client niveau met behulp van **DefaultRequestOptions**). Alle overige worden intern door de client bibliotheek verwerkt.

```csharp
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

// Set the encryption policy on the request options.
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Upload the encrypted contents to the blob.
blob.UploadFromStream(stream, size, null, options, null);

// Download and decrypt the encrypted contents from the blob.
MemoryStream outputStream = new MemoryStream();
blob.DownloadToStream(outputStream, null, options, null);
```

---

### <a name="queue-service-encryption"></a>Versleuteling Queue-service

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Maak een **ClientSideEncryptionOptions** -object en stel dit in op client maken met **SpecializedQueueClientOptions**. U kunt geen versleutelings opties per API instellen. Alle overige worden intern door de client bibliotheek verwerkt.

```csharp
// Your key and key resolver instances, either through KeyVault SDK or an external implementation
IKeyEncryptionKey key;
IKeyEncryptionKeyResolver keyResolver;

// Create the encryption options to be used for upload and download.
ClientSideEncryptionOptions encryptionOptions = new ClientSideEncryptionOptions(ClientSideEncryptionVersion.V1_0)
{
   KeyEncryptionKey = key,
   KeyResolver = keyResolver,
   // string the storage client will use when calling IKeyEncryptionKey.WrapKey()
   KeyWrapAlgorithm = "some algorithm name"
};

// Set the encryption options on the client options
QueueClientOptions options = new SpecializedQueueClientOptions() { ClientSideEncryption = encryptionOptions };

// Get your queue client with client-side encryption enabled.
// Client-side encryption options are passed from service to queue clients.
QueueClient queue = new QueueServiceClient(connectionString, options).GetQueueClient("myQueue");

// Send an encrypted queue message.
queue.SendMessage("Hello, World!");

// Download queue messages, decrypting ones that are detected to be encrypted
QueueMessage[] queue.ReceiveMessages(); 
```

Een **QueueServiceClient** is niet nodig om versleutelings opties toe te passen. Ze kunnen ook worden door gegeven aan **QueueClient** -constructors die **QueueClientOptions** -objecten accepteren.

Als een gewenst **QueueClient** -object al bestaat, maar zonder versleutelings opties aan de client zijde, bestaat een uitbreidings methode voor het maken van een kopie van dat object met de opgegeven **ClientSideEncryptionOptions**. Deze extensie methode vermijdt de overhead van een volledig nieuwe **QueueClient** -object.

```csharp
using Azure.Storage.Queues.Specialized;

// Your existing QueueClient instance and encryption options
QueueClient plaintextQueue;
ClientSideEncryptionOptions encryptionOptions;

// Get a copy of plaintextQueue that uses client-side encryption
QueueClient clientSideEncryptionQueue = plaintextQueue.WithClientSideEncryptionOptions(encryptionOptions);
```

Sommige gebruikers hebben mogelijk wacht rijen waarin niet alle ontvangen berichten kunnen worden ontsleuteld en de sleutel of resolver moet worden gegenereerd. In dit geval wordt de laatste regel van het bovenstaande voor beeld gegenereerd en zijn geen van de ontvangen berichten toegankelijk. In deze scenario's kan de subklasse **QueueClientSideEncryptionOptions** worden gebruikt om versleutelings opties aan clients te bieden. Er wordt een gebeurtenis **DecryptionFailed** beschikbaar gemaakt die wordt geactiveerd wanneer een wachtrij bericht niet kan worden ontsleuteld, zolang er ten minste één aanroep is toegevoegd aan de gebeurtenis. Afzonderlijk mislukte berichten kunnen op deze manier worden verwerkt en ze worden gefilterd van de uiteindelijke **QueueMessage []** geretourneerd door **ReceiveMessages**.

```csharp
// Create your encryption options using the sub-class.
QueueClientSideEncryptionOptions encryptionOptions = new QueueClientSideEncryptionOptions(ClientSideEncryptionVersion.V1_0)
{
   KeyEncryptionKey = key,
   KeyResolver = keyResolver,
   // string the storage client will use when calling IKeyEncryptionKey.WrapKey()
   KeyWrapAlgorithm = "some algorithm name"
};

// Add a handler to the DecryptionFailed event.
encryptionOptions.DecryptionFailed += (source, args) => {
   QueueMessage failedMessage = (QueueMessage)source;
   Exception exceptionThrown = args.Exception;
   // do something
};

// Use these options with your client objects.
QueueClient queue = new QueueClient(connectionString, queueName, new SpecializedQueueClientOptions()
{
   ClientSideEncryption = encryptionOptions
});

// Retrieve 5 messages from the queue.
// Assume 5 messages come back and one throws during decryption.
QueueMessage[] messages = queue.ReceiveMessages(maxMessages: 5).Value;
Debug.Assert(messages.Length == 4)
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Maak een **QueueEncryptionPolicy** -object en stel dit in de aanvraag opties in (per API of op client niveau met behulp van **DefaultRequestOptions**). Alle overige worden intern door de client bibliotheek verwerkt.

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

 // Add message
 QueueRequestOptions options = new QueueRequestOptions() { EncryptionPolicy = policy };
 queue.AddMessage(message, null, null, options, null);

 // Retrieve message
 CloudQueueMessage retrMessage = queue.GetMessage(null, options, null);
```

---

### <a name="table-service-encryption-v11-only"></a>Versleuteling Table service (alleen V11)

Naast het maken van een versleutelings beleid en het instellen ervan op aanvraag opties, moet u een **EncryptionResolver** opgeven in **TableRequestOptions** of het kenmerk [EncryptProperty] instellen voor de entiteit.

#### <a name="using-the-resolver"></a>De resolver gebruiken

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

 TableRequestOptions options = new TableRequestOptions()
 {
    EncryptionResolver = (pk, rk, propName) =>
     {
        if (propName == "foo")
         {
            return true;
         }
         return false;
     },
     EncryptionPolicy = policy
 };

 // Insert Entity
 currentTable.Execute(TableOperation.Insert(ent), options, null);

 // Retrieve Entity
 // No need to specify an encryption resolver for retrieve
 TableRequestOptions retrieveOptions = new TableRequestOptions()
 {
    EncryptionPolicy = policy
 };

 TableOperation operation = TableOperation.Retrieve(ent.PartitionKey, ent.RowKey);
 TableResult result = currentTable.Execute(operation, retrieveOptions, null);
```

#### <a name="using-attributes"></a>Kenmerken gebruiken

Zoals hierboven vermeld, als de entiteit TableEntity implementeert, kunnen de eigenschappen worden gedecoreerd met het kenmerk [EncryptProperty] in plaats van de **EncryptionResolver** op te geven.

```csharp
[EncryptProperty]
 public string EncryptedProperty1 { get; set; }
```

## <a name="encryption-and-performance"></a>Versleuteling en prestaties

Houd er rekening mee dat het versleutelen van uw opslag gegevens resulteert in extra prestatie overhead. De inhouds sleutel en IV moeten worden gegenereerd, de inhoud zelf moet worden versleuteld en aanvullende meta gegevens moeten worden geformatteerd en geüpload. Deze overhead is afhankelijk van de hoeveelheid gegevens die wordt versleuteld. Klanten wordt aangeraden hun toepassingen altijd te testen tijdens de ontwikkeling van de prestaties.

## <a name="next-steps"></a>Volgende stappen

* [Zelf studie: blobs in Microsoft Azure Storage versleutelen en ontsleutelen met behulp van Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)
* De [Azure Storage-client bibliotheek voor .net NuGet-pakket](https://www.nuget.org/packages/WindowsAzure.Storage) downloaden
* Down load de NuGet [core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/)-, [client](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)-en [Extensions](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/) -pakketten van Azure Key Vault  
* Ga naar de [Azure Key Vault documentatie](../../key-vault/general/overview.md)
