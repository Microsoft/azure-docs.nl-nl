---
title: Versleuteling Client-Side met Java voor Microsoft Azure Storage | Microsoft Docs
description: De Azure Storage-client bibliotheek voor Java ondersteunt versleuteling van de client en integratie met Azure Key Vault voor een maximale beveiliging van uw Azure Storage toepassingen.
services: storage
author: tamram
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-java
ms.openlocfilehash: 78baaa3f794bed870b40fb3975f6b80ff37e90f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043725"
---
# <a name="client-side-encryption-and-azure-key-vault-with-java-for-microsoft-azure-storage"></a>Client-Side versleuteling en Azure Key Vault met Java voor Microsoft Azure Storage
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Overzicht
De [Azure Storage-client bibliotheek voor Java](https://search.maven.org/artifact/com.azure/azure-storage-blob-cryptography) biedt ondersteuning voor het versleutelen van gegevens in client toepassingen voordat u deze uploadt naar Azure Storage en gegevens ontsleutelt tijdens het downloaden van de client. De bibliotheek ondersteunt ook integratie met [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) voor sleutel beheer van het opslag account.

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
De client bibliotheek ondersteunt momenteel alleen versleuteling van hele blobs. Versleuteling wordt met name ondersteund wanneer gebruikers de methoden **Upload** _ of de methode _ *openOutputStream** gebruiken. Voor down loads worden zowel volledige als bereik downloads ondersteund.  

Tijdens het versleutelen genereert de client bibliotheek een wille keurige initialisatie vector (IV) van 16 bytes, samen met een wille keurige inhouds versleutelings sleutel (CEK) van 32 bytes en voert u een envelop versleuteling van de BLOB-gegevens uit met deze gegevens. De verpakte CEK en andere versleutelings-meta gegevens worden vervolgens opgeslagen als blob-meta gegevens, samen met de versleutelde Blob in de service.

> [!WARNING]
> Als u uw eigen meta gegevens voor de BLOB bewerkt of uploadt, moet u ervoor zorgen dat deze meta gegevens behouden blijven. Als u nieuwe meta gegevens zonder deze meta gegevens uploadt, gaan de verpakte CEK, IV en andere meta gegevens verloren en kan de blob-inhoud niet meer worden opgehaald.
> 
> 

Voor het downloaden van een versleutelde BLOB moet u de inhoud van de hele BLOB ophalen met behulp van de methoden voor het **downloaden** van / **openInputStream** . De ingepakte CEK is gewrappt en gebruikt samen met de IV (opgeslagen als blob-meta gegevens in dit geval) om de ontsleutelde gegevens te retour neren aan de gebruikers.

Als u een wille keurig bereik (**downloadRange** -methoden) in de versleutelde BLOB downloadt, moet u het bereik dat door de gebruikers wordt verstrekt, aanpassen om een klein aantal extra gegevens te krijgen dat kan worden gebruikt om het aangevraagde bereik te ontsleutelen.  

Alle BLOB-typen (blok-blobs, pagina-blobs en toevoeg-blobs) kunnen worden versleuteld/ontsleuteld met dit schema.

### <a name="queues"></a>Wachtrijen
Omdat wachtrij berichten een wille keurige indeling hebben, definieert de client bibliotheek een aangepaste indeling die de initialisatie vector (IV) en de versleutelde inhouds versleutelings sleutel (CEK) bevat in de bericht tekst.  

Tijdens het versleutelen genereert de client bibliotheek een wille keurige IV van 16 bytes samen met een wille keurige CEK van 32 bytes en wordt een envelop versleuteling van de bericht tekst van de wachtrij uitgevoerd met behulp van deze gegevens. De verpakte CEK en andere versleutelings-meta gegevens worden vervolgens toegevoegd aan het versleutelde bericht in de wachtrij. Dit gewijzigde bericht (hieronder weer gegeven) wordt opgeslagen op de service.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

Tijdens de ontsleuteling wordt de ingepakte sleutel geëxtraheerd uit het wachtrij bericht en genest. De IV wordt ook geëxtraheerd uit het wachtrij bericht en gebruikt samen met de niet-ingepakte sleutel voor het ontsleutelen van de wachtrij bericht gegevens. Houd er rekening mee dat de meta gegevens van de versleutelings functie klein zijn (minder dan 500 bytes), dus wanneer het telt voor de limiet van 64 kB voor een wachtrij bericht, moet de impact kunnen worden beheerd.

### <a name="tables"></a>Tables
De client bibliotheek ondersteunt versleuteling van entiteits eigenschappen voor INSERT-en Replace-bewerkingen.

> [!NOTE]
> Samen voegen wordt momenteel niet ondersteund. Omdat een subset van eigenschappen mogelijk eerder is versleuteld met een andere sleutel, worden de nieuwe eigenschappen gebundeld en worden de meta gegevens bijgewerkt als gevolg van gegevens verlies. Samen voegen vereist het maken van extra service aanroepen om de vooraf bestaande entiteit van de service te lezen of een nieuwe sleutel per eigenschap te gebruiken, die beide niet geschikt zijn om prestatie redenen.
> 
> 

Tabel gegevens versleuteling werkt als volgt:  

1. Gebruikers geven de eigenschappen op die moeten worden versleuteld.  
2. De client bibliotheek genereert een wille keurige initialisatie vector (IV) van 16 bytes samen met een wille keurige inhouds versleutelings sleutel (CEK) van 32 bytes voor elke entiteit en voert een envelop versleuteling uit voor de afzonderlijke eigenschappen die moeten worden versleuteld door een nieuwe IV per eigenschap af te leiden. De versleutelde eigenschap wordt opgeslagen als binaire gegevens.  
3. De verpakte CEK en andere versleutelings meta gegevens worden vervolgens opgeslagen als twee extra gereserveerde eigenschappen. De eerste gereserveerde eigenschap (_ClientEncryptionMetadata1) is een teken reeks eigenschap die de informatie over IV, versie en ingepakte sleutel bevat. De tweede gereserveerde eigenschap (_ClientEncryptionMetadata2) is een binaire eigenschap die de informatie bevat over de versleutelde eigenschappen. De informatie in deze tweede eigenschap (_ClientEncryptionMetadata2) is zelf versleuteld.  
4. Als gevolg van deze aanvullende gereserveerde eigenschappen die vereist zijn voor versleuteling, kunnen gebruikers nu slechts 250 aangepaste eigenschappen hebben in plaats van 252. De totale grootte van de entiteit moet kleiner zijn dan 1 MB.  
   
   Houd er rekening mee dat alleen teken reeks eigenschappen kunnen worden versleuteld. Als andere typen eigenschappen moeten worden versleuteld, moeten ze worden geconverteerd naar teken reeksen. De versleutelde teken reeksen worden als binaire eigenschappen opgeslagen op de service en worden teruggeconverteerd naar teken reeksen na ontsleuteling.
   
   Voor tabellen, naast het versleutelings beleid, moeten gebruikers de eigenschappen opgeven die moeten worden versleuteld. U kunt dit doen door ofwel een kenmerk [encryption] op te geven (voor POCO-entiteiten die zijn afgeleid van TableEntity) of een Encryption resolver in de aanvraag opties. Een Encryption resolver is een gemachtigde die een partitie sleutel, een rij-en een eigenschaps naam gebruikt en een Booleaanse waarde retourneert die aangeeft of die eigenschap moet worden versleuteld. Tijdens de versleuteling gebruikt de client bibliotheek deze gegevens om te bepalen of een eigenschap moet worden versleuteld tijdens het schrijven naar de kabel. De gemachtigde biedt ook de mogelijkheid van logica om te bepalen hoe eigenschappen worden versleuteld. (Bijvoorbeeld als X, vervolgens versleutelings eigenschap A; anders eigenschappen A en B versleutelen.) Houd er rekening mee dat deze informatie niet hoeft te worden verstrekt tijdens het lezen of doorzoeken van entiteiten.

### <a name="batch-operations"></a>Batch bewerkingen
In batch-bewerkingen wordt dezelfde KEK gebruikt voor alle rijen in die batch bewerking omdat de client bibliotheek slechts één Options-object (en dus één beleid/KEK) per batch-bewerking toestaat. In de-client bibliotheek wordt echter intern een nieuwe wille keurige steek proef en wille keurige CEK per rij in de batch gegenereerd. Gebruikers kunnen er ook voor kiezen om verschillende eigenschappen te versleutelen voor elke bewerking in de batch door dit gedrag te definiëren in de versleutelings conflict Oplosser.

### <a name="queries"></a>Query's
> [!NOTE]
> Omdat de entiteiten zijn versleuteld, kunt u geen query's uitvoeren die filteren op een versleutelde eigenschap.  Als u probeert, zijn de resultaten onjuist, omdat de service probeert versleutelde gegevens te vergelijken met niet-versleutelde gegevens.
> 
> 
> Als u query bewerkingen wilt uitvoeren, moet u een sleutel conflict Oplosser opgeven waarmee alle sleutels in de resultatenset kunnen worden omgezet. Als een entiteit in het query resultaat niet kan worden omgezet naar een provider, wordt een fout gegenereerd door de client bibliotheek. Voor elke query die aan server zijde-projecties uitvoert, voegt de client bibliotheek de speciale eigenschappen van de meta gegevens voor versleuteling (_ClientEncryptionMetadata1 en _ClientEncryptionMetadata2) standaard toe aan de geselecteerde kolommen.

## <a name="azure-key-vault"></a>Azure Key Vault
Met Azure Sleutelkluis kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Met behulp van Azure Key Vault kunnen gebruikers sleutels en geheimen versleutelen (zoals verificatie sleutels, sleutels voor opslag accounts, sleutels voor gegevens versleuteling,. PFX-bestanden en wacht woorden) met behulp van sleutels die worden beveiligd door Hardware Security modules (Hsm's). Zie [Wat is Azure Key Vault?](../../key-vault/general/overview.md) voor meer informatie.

De Storage-client bibliotheek gebruikt de Key Vault-kern bibliotheek om een gemeen schappelijk Framework te bieden in azure voor het beheren van sleutels. Gebruikers krijgen ook het extra voor deel van het gebruik van de Key Vault extensies-bibliotheek. De uitbrei dingen bibliotheek biedt nuttige functionaliteit rond eenvoudige en naadloze symmetrische/RSA-lokale en Cloud sleutel providers en aggregatie en caching.

### <a name="interface-and-dependencies"></a>Interface en afhankelijkheden
Er zijn drie Key Vault pakketten:  

* Azure-sleutel kluis-Core bevat de IKey en IKeyResolver. Het is een klein pakket zonder afhankelijkheden. De Storage-client bibliotheek voor Java definieert deze als een afhankelijkheid.
* Azure-sleutel kluis bevat de Key Vault REST-client.  
* Azure-sleutel kluis-uitbrei dingen bevatten extensie code die implementaties van cryptografische algoritmen en een RSAKey en een SymmetricKey bevat. Dit is afhankelijk van de core-en de naam ruimte van de hoofd kluis en biedt functionaliteit voor het definiëren van een aggregatie resolver (wanneer gebruikers meerdere sleutel providers willen gebruiken) en een cache sleutel conflict Oplosser. Hoewel de opslag-client bibliotheek niet rechtstreeks afhankelijk is van dit pakket, moet u dit pakket hebben als gebruikers Azure Key Vault willen gebruiken om hun sleutels op te slaan of de Key Vault extensies te gebruiken voor het gebruik van de lokale en Cloud cryptografie providers.  
  
  Key Vault is ontworpen voor hoogwaardige hoofd sleutels en beperkings limieten per Key Vault zijn met dit doel ontworpen. Bij het uitvoeren van versleuteling aan de client zijde met Key Vault moet het voorkeurs model gebruikmaken van symmetrische hoofd sleutels die zijn opgeslagen als geheimen in Key Vault en lokaal in de cache worden geplaatst. Gebruikers moeten het volgende doen:  

1. Maak een geheim offline en upload het naar Key Vault.  
2. Gebruik de basis-id van het geheim als para meter om de huidige versie van het geheim voor versleuteling op te lossen en deze gegevens lokaal op te slaan in de cache. CachingKeyResolver gebruiken voor caching; gebruikers worden niet verwacht hun eigen cache logica te implementeren.  
3. Gebruik de caching-resolver als invoer bij het maken van het versleutelings beleid.
   Meer informatie over het gebruik van Key Vault vindt u in de voor beelden van code ring.

## <a name="best-practices"></a>Aanbevolen procedures
Ondersteuning voor versleuteling is alleen beschikbaar in de Storage-client bibliotheek voor Java.

> [!IMPORTANT]
> Houd rekening met de volgende belang rijke punten wanneer versleuteling aan de client zijde wordt gebruikt:
> 
> * Bij het lezen van of schrijven naar een versleutelde BLOB gebruikt u hele BLOB-upload opdrachten en bereik/hele BLOB-Download opdrachten. Vermijd het schrijven naar een versleutelde blob met behulp van protocol bewerkingen zoals put blok keren, blokkerings lijst plaatsen, schrijf pagina's, pagina's wissen of blok toevoegen. anders is het mogelijk dat u de versleutelde BLOB beschadigd en onleesbaar maakt.
> * Voor tabellen bestaat een soort gelijke beperking. Zorg ervoor dat u geen versleutelde eigenschappen bijwerkt zonder de versleutelings meta gegevens bij te werken.  
> * Als u meta gegevens op de versleutelde BLOB instelt, kunt u de meta gegevens voor versleuteling overschrijven die vereist zijn voor ontsleuteling, omdat het instellen van meta gegevens geen additief is. Dit geldt ook voor moment opnamen. Vermijd het opgeven van meta gegevens tijdens het maken van een moment opname van een versleutelde blob. Als meta gegevens moeten worden ingesteld, moet u eerst de **downloadAttributes** -methode aanroepen om de huidige versleutelings-meta gegevens op te halen en te voor komen dat er gelijktijdige schrijf bewerkingen worden uitgevoerd terwijl de meta gegevens worden ingesteld.  
> * Schakel de vlag **requireEncryption** in de standaard opties voor aanvragen in voor gebruikers die alleen moeten werken met versleutelde gegevens. Zie hieronder voor meer informatie.  
> 
> 

## <a name="client-api--interface"></a>Client-API/-interface
Bij het maken van een EncryptionPolicy-object kunnen gebruikers alleen een sleutel opgeven (IKey implementeren), alleen een resolver (Implementing IKeyResolver) of beide. IKey is het basis type van de sleutel die wordt geïdentificeerd met behulp van een sleutel-id en die de logica biedt voor het in-of uitpakken. IKeyResolver wordt gebruikt om een sleutel tijdens het ontsleutelen op te lossen. Hiermee wordt een ResolveKey-methode gedefinieerd die een IKey retourneert met een sleutel-id. Dit biedt gebruikers de mogelijkheid om te kiezen tussen meerdere sleutels die worden beheerd op meerdere locaties.

* Voor versleuteling wordt de sleutel altijd gebruikt en wordt er een fout veroorzaakt door het ontbreken van een sleutel.  
* Voor ontsleuteling:  
  
  * De Key resolver wordt aangeroepen als deze is opgegeven om de sleutel op te halen. Als de resolver is opgegeven maar geen toewijzing voor de sleutel-id heeft, wordt er een fout gegenereerd.  
  * Als er geen resolver is opgegeven, maar er is een sleutel opgegeven, wordt de sleutel gebruikt als de id overeenkomt met de vereiste sleutel-id. Als de id niet overeenkomt, wordt er een fout gegenereerd.  
    
    De [versleutelings voorbeelden](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) illustreren een gedetailleerder end-to-end-scenario voor blobs, wacht rijen en tabellen, samen met Key Vault-integratie.

### <a name="requireencryption-mode"></a>RequireEncryption-modus
Gebruikers kunnen eventueel een bewerkings modus inschakelen, waarbij alle uploads en down loads moeten worden versleuteld. In deze modus wordt geprobeerd gegevens te uploaden zonder versleutelings beleid of gegevens te downloaden die niet zijn versleuteld op de service, mislukt de client. Met de vlag **requireEncryption** van het object aanvraag opties wordt dit gedrag bepaald. Als uw toepassing alle objecten versleutelt die zijn opgeslagen in Azure Storage, kunt u de eigenschap **requireEncryption** instellen voor de standaard aanvraag opties voor het object service-client.   

Gebruik bijvoorbeeld **CloudBlobClient. getDefaultRequestOptions (). setRequireEncryption (true)** om versleuteling te vereisen voor alle BLOB-bewerkingen die via dat client object worden uitgevoerd.

### <a name="blob-service-encryption"></a>Versleuteling Blob service
Maak een **BlobEncryptionPolicy** -object en stel dit in de aanvraag opties in (per API of op client niveau met behulp van **DefaultRequestOptions**). Alle overige worden intern door de client bibliotheek verwerkt.

# <a name="java-v12"></a>[Java-V12](#tab/java)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="java-v8"></a>[Java-V8](#tab/java8)

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

// Set the encryption policy on the request options.
BlobRequestOptions options = new BlobRequestOptions();
options.setEncryptionPolicy(policy);

// Upload the encrypted contents to the blob.
blob.upload(stream, size, null, options, null);

// Download and decrypt the encrypted contents from the blob.
ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
blob.download(outputStream, null, options, null);
```
---

### <a name="queue-service-encryption"></a>Versleuteling Queue-service
Maak een **QueueEncryptionPolicy** -object en stel dit in de aanvraag opties in (per API of op client niveau met behulp van **DefaultRequestOptions**). Alle overige worden intern door de client bibliotheek verwerkt.

# <a name="java-v12"></a>[Java-V12](#tab/java)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="java-v8"></a>[Java-V8](#tab/java8)

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

// Add message
QueueRequestOptions options = new QueueRequestOptions();
options.setEncryptionPolicy(policy);

queue.addMessage(message, 0, 0, options, null);

// Retrieve message
CloudQueueMessage retrMessage = queue.retrieveMessage(30, options, null);
```
---

### <a name="table-service-encryption"></a>Versleuteling Table service
Naast het maken van een versleutelings beleid en het instellen ervan op aanvraag opties, moet u een **EncryptionResolver** opgeven in **TableRequestOptions** of het kenmerk [encryption] instellen op de getter en setter van de entiteit.

### <a name="using-the-resolver"></a>De resolver gebruiken

# <a name="java-v12"></a>[Java-V12](#tab/java)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="java-v8"></a>[Java-V8](#tab/java8)

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

TableRequestOptions options = new TableRequestOptions()
options.setEncryptionPolicy(policy);
options.setEncryptionResolver(new EncryptionResolver() {
    public boolean encryptionResolver(String pk, String rk, String key) {
        if (key == "foo")
        {
            return true;
        }
        return false;
    }
});

// Insert Entity
currentTable.execute(TableOperation.insert(ent), options, null);

// Retrieve Entity
// No need to specify an encryption resolver for retrieve
TableRequestOptions retrieveOptions = new TableRequestOptions()
retrieveOptions.setEncryptionPolicy(policy);

TableOperation operation = TableOperation.retrieve(ent.PartitionKey, ent.RowKey, DynamicTableEntity.class);
TableResult result = currentTable.execute(operation, retrieveOptions, null);
```
---

### <a name="using-attributes"></a>Kenmerken gebruiken
Zoals hierboven vermeld, als de entiteit TableEntity implementeert, kan de eigenschappen getter en setter worden gedecoreerd met het kenmerk [encryption] in plaats van de **EncryptionResolver** op te geven.

# <a name="java-v12"></a>[Java-V12](#tab/java)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="java-v8"></a>[Java-V8](#tab/java8)

```java
private string encryptedProperty1;

@Encrypt
public String getEncryptedProperty1 () {
    return this.encryptedProperty1;
}

@Encrypt
public void setEncryptedProperty1(final String encryptedProperty1) {
    this.encryptedProperty1 = encryptedProperty1;
}
```
---

## <a name="encryption-and-performance"></a>Versleuteling en prestaties

Houd er rekening mee dat het versleutelen van uw opslag gegevens resulteert in extra prestatie overhead. De inhouds sleutel en IV moeten worden gegenereerd, de inhoud zelf moet worden versleuteld en aanvullende meta gegevens moeten worden geformatteerd en geüpload. Deze overhead is afhankelijk van de hoeveelheid gegevens die wordt versleuteld. Klanten wordt aangeraden hun toepassingen altijd te testen tijdens de ontwikkeling van de prestaties.

## <a name="next-steps"></a>Volgende stappen

* De [Azure Storage-client bibliotheek voor Java maven-pakket](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) downloaden  
* De [Azure Storage-client bibliotheek voor Java-bron code downloaden van github](https://github.com/Azure/azure-storage-java)
* Down load de Azure Key Vault maven-bibliotheek voor Java maven-pakketten:
  * [Kern](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault-core) pakket
  * [Client](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault) pakket
* Ga naar de [Azure Key Vault documentatie](../../key-vault/general/overview.md)
