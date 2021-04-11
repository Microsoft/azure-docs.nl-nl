---
title: 'Quickstart: Azure Blob Storage-clientbibliotheek - Ruby'
description: Maak een opslagaccount en een container in Azure Blob Storage. Gebruik de opslagclientbibliotheek voor Ruby om een blob te maken, een blob te downloaden en de blobs in een container te vermelden.
author: twooley
ms.author: twooley
ms.date: 12/04/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 96b47afb11a0105e8f6d6b58e8862994493389f4
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277973"
---
# <a name="quickstart-azure-blob-storage-client-library-for-ruby"></a>Quickstart: Azure Blob Storage-clientbibliotheek voor Ruby

Meer informatie over het gebruik van Ruby voor het maken, downloaden en vermelden van blobs in een container in Microsoft Azure Blob Storage.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Zorg dat u de volgende aanvullende vereisten hebt geïnstalleerd:

- [Ruby](https://www.ruby-lang.org/en/downloads/)
- [Azure Storage-bibliotheek voor Ruby](https://github.com/azure/azure-storage-ruby) met het [RubyGem-pakket](https://rubygems.org/gems/azure-storage-blob):

    ```console
    gem install azure-storage-blob
    ```

## <a name="download-the-sample-application"></a>De voorbeeldtoepassing downloaden

De [voorbeeldtoepassing](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git) die in deze snelstartgids wordt gebruikt, is een Ruby-basistoepassing.

Gebruik [Git](https://git-scm.com/) om een kopie van de toepassing te downloaden naar de ontwikkelomgeving. Met deze opdracht wordt de opslagplaats naar uw lokale computer gekloond:

```console
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git
```

Navigeer naar de map *storage-blobs-ruby-quickstart* en open het bestand *example.rb* in uw code-editor.

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>De opslagverbindingsreeks configureren

Geef uw opslagaccountnaam en accountsleutel op om een [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService)-exemplaar voor uw toepassing te maken.

Met de volgende code in het bestand *example.rb* wordt een nieuw [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService)-object geïnstantieerd. Vervang de waarden *accountname* en *accountkey* met uw accountnaam en de -sleutel.

```ruby
# Create a BlobService object
account_name = "accountname"
account_key = "accountkey"

blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
)
```

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

In het voorbeeld wordt een container gemaakt in Blob Storage, wordt er een nieuwe blob in de container gemaakt, worden de blobs in de container vermeld en wordt de blob gedownload naar een lokaal bestand.

Voet het voorbeeld uit. Hier volgt een voorbeeld van de uitvoer van het uitvoeren van de toepassing:

```console
C:\azure-samples\storage-blobs-ruby-quickstart> ruby example.rb

Creating a container: quickstartblobs18cd9ec0-f4ac-4688-a979-75c31a70503e

Creating blob: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

List blobs in the container following continuation token
        Blob name: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Downloading blob to C:/Users/azureuser/Documents/QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Paused, press the Enter key to delete resources created by the sample and exit the application
```

Wanneer u op Enter drukt om door te gaan, wordt de opslagcontainer en het lokale bestand door het voorbeeldprogramma verwijderd. Voordat u doorgaat, controleert u uw map *Documenten* voor het gedownloade bestand.

U kunt ook [Azure Storage Explorer](https://storageexplorer.com) gebruiken om de bestanden in uw opslagaccount weer te geven. Azure Storage Explorer is een gratis hulpprogramma voor meerdere platforms waarmee u toegang hebt tot de gegevens van uw opslagaccount.

Nadat u de bestanden hebt gecontroleerd, drukt u op Enter om de testbestanden te verwijderen en het voorbeeld af te sluiten. Open het bestand *example.rb* om de code te bekijken.

## <a name="understand-the-sample-code"></a>De voorbeeldcode begrijpen

We gaan nu de voorbeeldcode bespreken, zodat u begrijpt hoe deze werkt.

### <a name="get-references-to-the-storage-objects"></a>Verwijzingen naar de opslagobjecten ophalen

Als eerste moeten exemplaren worden gemaakt van de objecten die worden gebruikt voor het verkrijgen van toegang tot de Blob-opslag en voor het beheren ervan. Deze objecten bouwen voort op elkaar. Elk object wordt gebruikt door het volgende object in de lijst.

- Maak een exemplaar van het [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService)-object voor Azure-opslag om verbindingsreferenties in te stellen.
- Maak het object [Container](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container/Container) dat de container vertegenwoordigt die u wilt openen. Containers worden gebruikt om uw blobs te organiseren op een wijze die lijkt op het gebruik van mappen op uw computer om uw bestanden te organiseren.

Zodra u het containerobject hebt, kunt u een [Blok](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block)-blob-object maken dat verwijst naar een specifieke blob waarin u geïnteresseerd bent. Gebruik het [Blok](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block)-object om blobs te maken, te downloaden en te kopiëren.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Containers, blobs en metagegevens een naam geven en hiernaar verwijzen) voor meer informatie over de namen van containers en blobs.

De volgende voorbeeldcode:

- Maakt een nieuwe container
- Stelt machtigingen in op de container, zodat de blobs openbaar zijn. De container heeft de naam *quickstartblobs* met een unieke id toegevoegd.

```ruby
# Create a container
container_name = "quickstartblobs" + SecureRandom.uuid
puts "\nCreating a container: " + container_name
container = blob_client.create_container(container_name)

# Set the permission so the blobs are public
blob_client.set_container_acl(container_name, "container")
```

### <a name="create-a-blob-in-the-container"></a>Maak een blob in de container

Blob Storage ondersteunt blok-blobs, toevoeg-blobs en pagina-blobs. Als u een blob wilt maken, roept u de [create_block_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#create_block_blob-instance_method)-methode aan die wordt doorgegeven in de gegevens voor de blob.

In het volgende voorbeeld wordt een blob gemaakt met de naam *QuickStart_* met een unieke id en een *.txt*-bestandsextensie in de container die u eerder hebt gemaakt.

```ruby
# Create a new block blob containing 'Hello, World!'
blob_name = "QuickStart_" + SecureRandom.uuid + ".txt"
blob_data = "Hello, World!"
puts "\nCreating blob: " + blob_name
blob_client.create_block_blob(container.name, blob_name, blob_data)
```

Blok-blobs kunnen tot 4,7 TB groot zijn en kunnen van alles zijn: van spreadsheets tot grote videobestanden. Pagina-blobs worden hoofdzakelijk gebruikt voor de VHD-bestanden die worden gebruikt als back-up voor IaaS-virtuele machines. Toevoeg-blobs worden meestal gebruikt voor logboekregistratie, bijvoorbeeld wanneer u gegevens wilt wegschrijven naar een bestand en vervolgens gegevens wilt blijven toevoegen.

### <a name="list-the-blobs-in-a-container"></a>Blobs in een container vermelden

Haal een lijst met bestanden op in de container met behulp van de methode [list_blobs](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#list_blobs-instance_method). Met de volgende code wordt de lijst met blobs opgehaald, waarna de namen worden weergegeven.

```ruby
# List the blobs in the container
puts "\nList blobs in the container following continuation token"
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name: #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-a-blob"></a>Een blob downloaden

Download een blob naar uw lokale schijf met de methode [get_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#get_blob-instance_method). Met de volgende code wordt een blob gedownload die in een vorige sectie is gemaakt.

```ruby
# Download the blob

# Set the path to the local folder for downloading
if(is_windows)
    local_path = File.expand_path("~/Documents")
else 
    local_path = File.expand_path("~/")
end

# Create the full path to the downloaded file
full_path_to_file = File.join(local_path, blob_name)

puts "\nDownloading blob to " + full_path_to_file
blob, content = blob_client.get_blob(container_name, blob_name)
File.open(full_path_to_file,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>Resources opschonen

Als een blob niet meer nodig is, gebruikt u [delete_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#delete_blob-instance_method) om deze te verwijderen. Verwijder een volledige container met behulp van de methode [delete_container](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#delete_container-instance_method). Als u een container verwijdert, worden ook alle blobs verwijderd die in de container zijn opgeslagen.

```ruby
# Clean up resources, including the container and the downloaded file
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
```

## <a name="resources-for-developing-ruby-applications-with-blobs"></a>Resources voor het ontwikkelen van Ruby-toepassingen met blobs

Zie de volgende aanvullende resources voor Ruby-ontwikkeling:

- Bekijk en installeer de [broncode voor de Ruby-clientbibliotheek](https://github.com/Azure/azure-storage-ruby) voor Azure Storage op GitHub.
- Verken met behulp van de Ruby-clientbibliotheek geschreven [Azure-voorbeelden](/samples/browse/?products=azure&languages=ruby).
- [Voorbeeld: Aan de slag met Azure Storage in Ruby](https://github.com/Azure-Samples/storage-blob-ruby-getting-started)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u bestanden overbrengt tussen een Azure Blob Storage en een lokale schijf met behulp van Ruby. Ga naar het overzicht van het opslagaccount voor meer informatie over het werken met Blob Storage.

> [!div class="nextstepaction"]
> [Overzicht van opslagaccounts](../common/storage-account-overview.md)

Zie [Azure Blob Storage-resources beheren met Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) voor meer informatie over Storage Explorer en blobs.
