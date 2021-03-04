---
title: Versleuteling aan client zijde met python
titleSuffix: Azure Storage
description: De Azure Storage-client bibliotheek voor python ondersteunt versleuteling aan de client zijde voor maximale beveiliging van uw Azure Storage toepassingen.
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: ffdfd4dc8a81587d757e3f9853f1bb34e0b93c0d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043742"
---
# <a name="client-side-encryption-with-python"></a>Versleuteling aan client zijde met python

[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Overzicht
De [Azure Storage-client bibliotheek voor python](https://pypi.python.org/pypi/azure-storage) ondersteunt het versleutelen van gegevens in client toepassingen voordat ze naar Azure Storage worden geüpload en gegevens worden ontsleuteld tijdens het downloaden naar de client.

> [!NOTE]
> De Azure Storage python-bibliotheek is beschikbaar als preview-versie.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Versleuteling en ontsleuteling via de envelop techniek
De versleutelings processen en ontsleuteling volgen de envelop techniek.

### <a name="encryption-via-the-envelope-technique"></a>Versleuteling via de envelop techniek
Versleuteling via de envelop techniek werkt op de volgende manier:

1. De Azure Storage-client bibliotheek genereert een coderings sleutel voor inhoud (CEK). Dit is een eenmalige symmetrische sleutel.
2. Gebruikers gegevens worden versleuteld met behulp van deze CEK.
3. De CEK wordt vervolgens verpakt (versleuteld) met behulp van de versleutelingssleutel voor sleutel (KEK). De KEK wordt geïdentificeerd aan de hand van een sleutel-id en kan een asymmetrisch sleutel paar zijn of een symmetrische sleutel, die lokaal wordt beheerd.
   De Storage-client bibliotheek zelf heeft nooit toegang tot KEK. De bibliotheek roept het sleutel terugloop algoritme aan dat wordt gegeven door de KEK. Gebruikers kunnen ervoor kiezen om aangepaste providers te gebruiken voor sleutel terugloop/uitpakken, indien gewenst.
4. De versleutelde gegevens worden vervolgens geüpload naar de Azure Storage-service. De ingepakte sleutel en enkele extra versleutelings meta gegevens worden opgeslagen als meta gegevens (op een blob) of worden geïnterpoleerd met de versleutelde gegevens (wachtrij berichten en tabel entiteiten).

### <a name="decryption-via-the-envelope-technique"></a>Ontsleuteling via de envelop techniek
Ontsleuteling via de envelop techniek werkt op de volgende manier:

1. De client bibliotheek gaat ervan uit dat de gebruiker de sleutel versleutelings sleutel lokaal beheert (KEK). De gebruiker hoeft geen kennis te hebben van de specifieke sleutel die voor versleuteling is gebruikt. In plaats daarvan kan een Key resolver, waarmee verschillende sleutel-id's worden omgezet in sleutels, worden ingesteld en gebruikt.
2. De versleutelde gegevens worden door de client bibliotheek gedownload, samen met het versleutelings materiaal dat is opgeslagen in de service.
3. De versleutelings sleutel van de verpakte inhoud (CEK) wordt vervolgens met behulp van de sleutel versleutelings sleutel (KEK) gepakt (gedecodeerd). Hier is de client bibliotheek geen toegang tot KEK. Hiermee wordt de algoritme voor het uitpakken van de aangepaste provider aangeroepen.
4. De versleutelings sleutel voor inhoud (CEK) wordt vervolgens gebruikt om de versleutelde gebruikers gegevens te ontsleutelen.

## <a name="encryption-mechanism"></a>Versleutelings mechanisme
De Storage-client bibliotheek gebruikt [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) om gebruikers gegevens te versleutelen. Met name de [CBC-modus (Cipher Block Chaining)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) met AES. Elke service werkt enigszins anders, dus we bespreken deze hier.

### <a name="blobs"></a>Blobs
De client bibliotheek ondersteunt momenteel alleen versleuteling van hele blobs. Versleuteling wordt met name ondersteund wanneer gebruikers de methoden **Create*** gebruiken. Voor down loads worden zowel volledige als bereik downloads ondersteund en parallel Lise ring van uploaden en downloaden beschikbaar.

Tijdens het versleutelen genereert de client bibliotheek een wille keurige initialisatie vector (IV) van 16 bytes, samen met een wille keurige inhouds versleutelings sleutel (CEK) van 32 bytes en voert u een envelop versleuteling van de BLOB-gegevens uit met deze gegevens. De verpakte CEK en andere versleutelings-meta gegevens worden vervolgens opgeslagen als blob-meta gegevens, samen met de versleutelde Blob in de service.

> [!WARNING]
> Als u uw eigen meta gegevens voor de BLOB bewerkt of uploadt, moet u ervoor zorgen dat deze meta gegevens behouden blijven. Als u nieuwe meta gegevens zonder deze meta gegevens uploadt, gaan de verpakte CEK, IV en andere meta gegevens verloren en kan de blob-inhoud niet meer worden opgehaald.
> 
> 

Het downloaden van een versleutelde BLOB bestaat uit het ophalen van de inhoud van de volledige blob met behulp van de methoden **Get***. De ingepakte CEK is gewrappt en gebruikt samen met de IV (opgeslagen als blob-meta gegevens in dit geval) om de ontsleutelde gegevens te retour neren aan de gebruikers.

Bij het downloaden van een wille keurig bereik (**Get***-methoden met de para meters van het bereik dat is door gegeven in) in de versleutelde blob, moet u het bereik dat door de gebruikers wordt verstrekt aanpassen om een klein aantal extra gegevens te krijgen dat kan worden gebruikt om het gevraagde bereik te ontsleutelen

Blok-blobs en pagina-blobs kunnen alleen worden versleuteld/ontsleuteld met dit schema. Er is momenteel geen ondersteuning voor het versleutelen van toevoeg-blobs.

### <a name="queues"></a>Wachtrijen
Omdat wachtrij berichten een wille keurige indeling hebben, definieert de client bibliotheek een aangepaste indeling die de initialisatie vector (IV) en de versleutelde inhouds versleutelings sleutel (CEK) bevat in de bericht tekst.

Tijdens het versleutelen genereert de client bibliotheek een wille keurige IV van 16 bytes samen met een wille keurige CEK van 32 bytes en wordt een envelop versleuteling van de bericht tekst van de wachtrij uitgevoerd met behulp van deze gegevens. De verpakte CEK en andere versleutelings-meta gegevens worden vervolgens toegevoegd aan het versleutelde bericht in de wachtrij. Dit gewijzigde bericht (hieronder weer gegeven) wordt opgeslagen op de service.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

Tijdens de ontsleuteling wordt de ingepakte sleutel geëxtraheerd uit het wachtrij bericht en genest. De IV wordt ook geëxtraheerd uit het wachtrij bericht en gebruikt samen met de niet-ingepakte sleutel voor het ontsleutelen van de wachtrij bericht gegevens. Houd er rekening mee dat de meta gegevens van de versleutelings functie klein zijn (minder dan 500 bytes), dus wanneer het telt voor de limiet van 64 kB voor een wachtrij bericht, moet de impact kunnen worden beheerd.

### <a name="tables"></a>Tabellen
De client bibliotheek ondersteunt versleuteling van entiteits eigenschappen voor INSERT-en Replace-bewerkingen.

> [!NOTE]
> Samen voegen wordt momenteel niet ondersteund. Omdat een subset van eigenschappen mogelijk eerder is versleuteld met een andere sleutel, worden de nieuwe eigenschappen gebundeld en worden de meta gegevens bijgewerkt als gevolg van gegevens verlies. Samen voegen vereist het maken van extra service aanroepen om de vooraf bestaande entiteit van de service te lezen of een nieuwe sleutel per eigenschap te gebruiken, die beide niet geschikt zijn om prestatie redenen.
> 
> 

Tabel gegevens versleuteling werkt als volgt:

1. Gebruikers geven de eigenschappen op die moeten worden versleuteld.
2. De client bibliotheek genereert een wille keurige initialisatie vector (IV) van 16 bytes samen met een wille keurige inhouds versleutelings sleutel (CEK) van 32 bytes voor elke entiteit en voert een envelop versleuteling uit voor de afzonderlijke eigenschappen die moeten worden versleuteld door een nieuwe IV per eigenschap af te leiden. De versleutelde eigenschap wordt opgeslagen als binaire gegevens.
3. De verpakte CEK en andere versleutelings meta gegevens worden vervolgens opgeslagen als twee extra gereserveerde eigenschappen. De eerste gereserveerde eigenschap ( \_ ClientEncryptionMetadata1) is een teken reeks eigenschap die de informatie over IV, versie en ingepakte sleutel bevat. De tweede gereserveerde eigenschap ( \_ ClientEncryptionMetadata2) is een binaire eigenschap die de informatie bevat over de versleutelde eigenschappen. De informatie in deze tweede eigenschap ( \_ ClientEncryptionMetadata2) is zelf versleuteld.
4. Als gevolg van deze aanvullende gereserveerde eigenschappen die vereist zijn voor versleuteling, kunnen gebruikers nu slechts 250 aangepaste eigenschappen hebben in plaats van 252. De totale grootte van de entiteit moet kleiner zijn dan 1 MB.

   Houd er rekening mee dat alleen teken reeks eigenschappen kunnen worden versleuteld. Als andere typen eigenschappen moeten worden versleuteld, moeten ze worden geconverteerd naar teken reeksen. De versleutelde teken reeksen worden op de service opgeslagen als binaire eigenschappen, en ze worden teruggeconverteerd naar teken reeksen (onbewerkte teken reeksen, niet EntityProperties met het type EdmType. STRING) na ontsleuteling.

   Voor tabellen, naast het versleutelings beleid, moeten gebruikers de eigenschappen opgeven die moeten worden versleuteld. U kunt dit doen door deze eigenschappen op te slaan in TableEntity-objecten met het type ingesteld op EdmType. STRING en encrypte ingesteld op True of door de encryption_resolver_function in te stellen op het tableservice-object. Een coderings conflict is een functie die een partitie sleutel, een rijdefinitie en een eigenschaps naam gebruikt en een Booleaanse waarde retourneert die aangeeft of die eigenschap moet worden versleuteld. Tijdens de versleuteling gebruikt de client bibliotheek deze gegevens om te bepalen of een eigenschap moet worden versleuteld tijdens het schrijven naar de kabel. De gemachtigde biedt ook de mogelijkheid van logica om te bepalen hoe eigenschappen worden versleuteld. (Bijvoorbeeld als X, vervolgens versleutelings eigenschap A; anders eigenschappen A en B versleutelen.) Houd er rekening mee dat deze informatie niet hoeft te worden verstrekt tijdens het lezen of doorzoeken van entiteiten.

### <a name="batch-operations"></a>Batch bewerkingen
Eén versleutelings beleid is van toepassing op alle rijen in de batch. In de client bibliotheek wordt intern een nieuwe wille keurige IV en wille keurige CEK per rij in de batch gegenereerd. Gebruikers kunnen er ook voor kiezen om verschillende eigenschappen te versleutelen voor elke bewerking in de batch door dit gedrag te definiëren in de versleutelings conflict Oplosser.
Als een batch wordt gemaakt als context beheerder via de methode tableservice batch (), wordt het versleutelings beleid van de tableservice automatisch toegepast op de batch. Als een batch expliciet wordt gemaakt door het aanroepen van de constructor, moet het versleutelings beleid worden door gegeven als een para meter en blijft ongewijzigd voor de levens duur van de batch.
Houd er rekening mee dat entiteiten worden versleuteld wanneer ze worden ingevoegd in de batch met behulp van het versleutelings beleid van de batch (entiteiten worden niet versleuteld op het moment van het door voeren van de batch met het versleutelings beleid van de tableservice).

### <a name="queries"></a>Query's
> [!NOTE]
> Omdat de entiteiten zijn versleuteld, kunt u geen query's uitvoeren die filteren op een versleutelde eigenschap.  Als u probeert, zijn de resultaten onjuist, omdat de service probeert versleutelde gegevens te vergelijken met niet-versleutelde gegevens.
> 
> 
> Als u query bewerkingen wilt uitvoeren, moet u een sleutel conflict Oplosser opgeven waarmee alle sleutels in de resultatenset kunnen worden omgezet. Als een entiteit in het query resultaat niet kan worden omgezet naar een provider, wordt een fout gegenereerd door de client bibliotheek. Voor elke query die aan server zijde-projecties uitvoert, voegt de client bibliotheek de speciale eigenschappen van de meta gegevens voor versleuteling ( \_ ClientEncryptionMetadata1 en \_ ClientEncryptionMetadata2) standaard toe aan de geselecteerde kolommen.
> 
> [!IMPORTANT]
> Houd rekening met de volgende belang rijke punten wanneer versleuteling aan de client zijde wordt gebruikt:
> 
> * Bij het lezen van of schrijven naar een versleutelde BLOB gebruikt u hele BLOB-upload opdrachten en bereik/hele BLOB-Download opdrachten. Vermijd het schrijven naar een versleutelde blob met behulp van protocol bewerkingen zoals put blok keren, blokkerings lijst plaatsen, schrijf pagina's of pagina's wissen; anders is het mogelijk dat u de versleutelde BLOB beschadigd en onleesbaar maakt.
> * Voor tabellen bestaat een soort gelijke beperking. Zorg ervoor dat u geen versleutelde eigenschappen bijwerkt zonder de versleutelings meta gegevens bij te werken.
> * Als u meta gegevens op de versleutelde BLOB instelt, kunt u de meta gegevens voor versleuteling overschrijven die vereist zijn voor ontsleuteling, omdat het instellen van meta gegevens geen additief is. Dit geldt ook voor moment opnamen. Vermijd het opgeven van meta gegevens tijdens het maken van een moment opname van een versleutelde blob. Als meta gegevens moeten worden ingesteld, moet u eerst de **get_blob_metadata** methode aanroepen om de huidige versleutelings-meta gegevens op te halen en voor komen dat er gelijktijdige schrijf bewerkingen worden uitgevoerd terwijl de meta gegevens worden ingesteld.
> * Schakel de vlag **require_encryption** in op het Service object voor gebruikers die alleen moeten werken met versleutelde gegevens. Zie hieronder voor meer informatie.

De opslag-client bibliotheek verwacht de door de KEK en de sleutel conflict Oplosser voor het implementeren van de volgende interface. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ondersteuning voor python Kek-beheer is in behandeling en wordt in deze bibliotheek geïntegreerd wanneer dit is voltooid.

## <a name="client-api--interface"></a>Client-API/-interface
Nadat een Storage Service-object (blockblobservice) is gemaakt, kan de gebruiker waarden toewijzen aan de velden die een versleutelings beleid vormen: key_encryption_key, key_resolver_function en require_encryption. Gebruikers kunnen alleen een KEK, een resolver of beide opgeven. key_encryption_key is het basis sleutel type dat wordt geïdentificeerd met behulp van een sleutel-id en die de logica biedt voor het in-of uitpakken. key_resolver_function wordt gebruikt om een sleutel tijdens het ontsleutelen op te lossen. Het retourneert een geldige KEK op basis van een sleutel-id. Dit biedt gebruikers de mogelijkheid om te kiezen tussen meerdere sleutels die worden beheerd op meerdere locaties.

De KEK moet de volgende methoden implementeren om gegevens te versleutelen:

* wrap_key (CEK): rondt de opgegeven CEK (bytes) aan met behulp van een algoritme van de keuze van de gebruiker. Retourneert de ingepakte sleutel.
* get_key_wrap_algorithm (): retourneert de algoritme die wordt gebruikt om sleutels te terugloop.
* get_kid (): retourneert de teken reeks sleutel-id voor deze KEK.
  De KEK moet de volgende methoden implementeren om gegevens te ontsleutelen:
* unwrap_key (CEK, algoritme): retourneert het niet-gepakte formulier van de opgegeven CEK met behulp van het algoritme dat door de teken reeks is opgegeven.
* get_kid (): retourneert een teken reeks sleutel-id voor deze KEK.

De Key resolver moet ten minste een methode implementeren die, op basis van een sleutel-id, de overeenkomende KEK retourneert die de bovenstaande interface implementeert. Alleen deze methode moet worden toegewezen aan de eigenschap key_resolver_function van het Service object.

* Voor versleuteling wordt de sleutel altijd gebruikt en wordt er een fout veroorzaakt door het ontbreken van een sleutel.
* Voor ontsleuteling:

  * De Key resolver wordt aangeroepen als deze is opgegeven om de sleutel op te halen. Als de resolver is opgegeven maar geen toewijzing voor de sleutel-id heeft, wordt er een fout gegenereerd.
  * Als er geen resolver is opgegeven, maar er is een sleutel opgegeven, wordt de sleutel gebruikt als de id overeenkomt met de vereiste sleutel-id. Als de id niet overeenkomt, wordt er een fout gegenereerd.

    De versleutelings voorbeelden in Azure. storage. samples demonstreren een meer gedetailleerd end-to-end-scenario voor blobs, wacht rijen en tabellen.
      Voor beelden van implementaties van de KEK en Key resolver worden in de voorbeeld bestanden vermeld als respectievelijk de sleutel-wrapper en de Keyinfo.

### <a name="requireencryption-mode"></a>RequireEncryption-modus
Gebruikers kunnen eventueel een bewerkings modus inschakelen, waarbij alle uploads en down loads moeten worden versleuteld. In deze modus wordt geprobeerd gegevens te uploaden zonder versleutelings beleid of gegevens te downloaden die niet zijn versleuteld op de service, mislukt de client. Met de vlag **require_encryption** van het Service object wordt dit gedrag bepaald.

### <a name="blob-service-encryption"></a>Versleuteling Blob service
Stel de velden voor het versleutelings beleid in op het object blockblobservice. Alle overige worden intern door de client bibliotheek verwerkt.

# <a name="python-v12"></a>[Python-V12](#tab/python)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_block_blob_service.key_encryption_key = kek
my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

# Upload the encrypted contents to the blob.
my_block_blob_service.create_blob_from_stream(
    container_name, blob_name, stream)

# Download and decrypt the encrypted contents from the blob.
blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)
```
---

### <a name="queue-service-encryption"></a>Versleuteling Queue-service
Stel de velden voor het versleutelings beleid in op het object queueservice. Alle overige worden intern door de client bibliotheek verwerkt.

# <a name="python-v12"></a>[Python-V12](#tab/python)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_queue_service.key_encryption_key = kek
my_queue_service.key_resolver_funcion = key_resolver.resolve_key

# Add message
my_queue_service.put_message(queue_name, content)

# Retrieve message
retrieved_message_list = my_queue_service.get_messages(queue_name)
```
---

### <a name="table-service-encryption"></a>Versleuteling Table service
Naast het maken van een versleutelings beleid en het instellen ervan op aanvraag opties, moet u een **encryption_resolver_function** op de **tableservice** opgeven of het kenmerk Encryption op de EntityProperty instellen.

### <a name="using-the-resolver"></a>De resolver gebruiken

# <a name="python-v12"></a>[Python-V12](#tab/python)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Define the encryption resolver_function.


def my_encryption_resolver(pk, rk, property_name):
    if property_name == 'foo':
        return True
    return False


# Set the KEK and key resolver on the service object.
my_table_service.key_encryption_key = kek
my_table_service.key_resolver_funcion = key_resolver.resolve_key
my_table_service.encryption_resolver_function = my_encryption_resolver

# Insert Entity
my_table_service.insert_entity(table_name, entity)

# Retrieve Entity
# Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
my_table_service.get_entity(
    table_name, entity['PartitionKey'], entity['RowKey'])
```
---

### <a name="using-attributes"></a>Kenmerken gebruiken
Zoals hierboven vermeld, kan een eigenschap worden gemarkeerd voor versleuteling door deze op te slaan in een EntityProperty-object en het versleutelings veld in te stellen.

# <a name="python-v12"></a>[Python-V12](#tab/python)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```
---

## <a name="encryption-and-performance"></a>Versleuteling en prestaties
Houd er rekening mee dat het versleutelen van uw opslag gegevens resulteert in extra prestatie overhead. De inhouds sleutel en IV moeten worden gegenereerd, de inhoud zelf moet worden versleuteld en aanvullende meta gegevens moeten worden opgemaakt en geüpload. Deze overhead is afhankelijk van de hoeveelheid gegevens die wordt versleuteld. Klanten wordt aangeraden hun toepassingen altijd te testen tijdens de ontwikkeling van de prestaties.

## <a name="next-steps"></a>Volgende stappen
* De [Azure Storage-client bibliotheek voor Java PyPi-pakket](https://pypi.python.org/pypi/azure-storage) downloaden
* De [Azure Storage-client bibliotheek voor python-bron code downloaden van github](https://github.com/Azure/azure-storage-python)
