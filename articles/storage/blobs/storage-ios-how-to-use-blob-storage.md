---
title: Object (BLOB)-opslag gebruiken vanuit iOS-Azure | Microsoft Docs
description: Sla niet-gestructureerde gegevens op in de cloud met Azure Blob Storage (objectopslag).
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/20/2018
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 7fc1b2638c2ab17c4cd58ca8d4508d2e6d244cfa
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95996802"
---
# <a name="how-to-use-blob-storage-from-ios"></a>Blob Storage gebruiken vanuit iOS

In dit artikel wordt beschreven hoe u algemene scenario's uitvoert met behulp van Microsoft Azure Blob-opslag. De voor beelden zijn geschreven in doel-C en gebruiken de [Azure Storage-client bibliotheek voor IOS](https://github.com/Azure/azure-storage-ios). De besproken scenario's zijn onder andere het uploaden, weer geven, downloaden en verwijderen van blobs. Zie de sectie [volgende stappen](#next-steps) voor meer informatie over blobs. U kunt ook de voor [beeld-app](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) downloaden om snel het gebruik van Azure Storage in een IOS-toepassing weer te geven.

Zie [Inleiding tot Azure Blob-opslag](storage-blobs-introduction.md)voor meer informatie over Blob Storage.

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importeer de Azure Storage iOS-bibliotheek in uw toepassing

U kunt de Azure Storage iOS-bibliotheek importeren in uw toepassing met behulp van de [Azure Storage CocoaPod](https://cocoapods.org/pods/AZSClient) of door het **Framework** -bestand te importeren. CocoaPod is de aanbevolen manier om de bibliotheek eenvoudiger te integreren, maar het importeren van het Framework bestand is minder opvallend voor uw bestaande project.

Als u deze bibliotheek wilt gebruiken, hebt u het volgende nodig:

- iOS 8 +
- Xcode 7 +

## <a name="cocoapod"></a>CocoaPod

1. Als u dit nog niet hebt gedaan, [installeert u CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) op uw computer door een Terminal venster te openen en de volgende opdracht uit te voeren.

    ```shell
    sudo gem install cocoapods
    ```

2. Maak vervolgens in de projectmap (de map met uw. xcodeproj-bestand) een nieuw bestand met de naam _Podfile_(geen bestands extensie). Voeg het volgende toe aan _Podfile_ en sla het op.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. In het Terminal venster gaat u naar de projectmap en voert u de volgende opdracht uit:

    ```shell
    pod install
    ```

4. Als uw. xcodeproj is geopend in Xcode, sluit u het. Open in de projectmap het zojuist gemaakte project bestand dat de extensie. xcworkspace heeft. Dit is het bestand waarmee u nu kunt werken.

## <a name="framework"></a>Framework

De andere manier om de bibliotheek te gebruiken is het maken van het Framework hand matig:

1. Down load of kloon eerst de [Azure-opslag-IOS-opslag plaats](https://github.com/azure/azure-storage-ios).
2. Ga naar *Azure-Storage-IOS*  ->  *lib*  ->  *Azure Storage-client bibliotheek* en open `AZSClient.xcodeproj` in Xcode.
3. Wijzig in de linkerbovenhoek van Xcode het actieve schema van "Azure Storage-client bibliotheek" in "Framework".
4. Bouw het project (⌘ + B). Hiermee maakt u een `AZSClient.framework` bestand op uw bureau blad.

U kunt het Framework-bestand vervolgens importeren in uw toepassing door het volgende te doen:

1. Maak een nieuw project of open uw bestaande project in Xcode.
2. Sleep en zet het neer `AZSClient.framework` in uw Xcode-project Navigator.
3. Selecteer *items kopiëren indien nodig* en klik op *volt ooien*.
4. Klik in het navigatie venster aan de linkerkant op het project en klik op het tabblad *Algemeen* boven aan de project-editor.
5. Klik in de sectie *gekoppelde frameworks en bibliotheken* op de knop toevoegen (+).
6. Zoek in de lijst met bibliotheken die al worden aangeboden `libxml2.2.tbd` en voeg deze toe aan uw project.

## <a name="import-the-library"></a>De bibliotheek importeren

```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Als u Swift gebruikt, moet u een bridging-header maken en daar een importeren \<AZSClient/AZSClient.h> :

1. Maak een header `Bridging-Header.h` -bestand en voeg de bovenstaande instructie import toe.
2. Ga naar het tabblad *Build Settings* en zoek naar *doel-C bridging-header*.
3. Dubbel klik op het veld van de *overbruggings header van de objectief-C* en voeg het pad toe aan het header-bestand: `ProjectName/Bridging-Header.h`
4. Bouw het project (⌘ + B) om te controleren of de bridging-header is opgehaald door Xcode.
5. Als u de bibliotheek rechtstreeks in een Swift-bestand wilt gebruiken, hoeft u geen instructies te importeren.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Asynchrone bewerkingen

> [!NOTE]
> Alle methoden voor het uitvoeren van een aanvraag voor de service zijn asynchrone bewerkingen. In de code voorbeelden vindt u het volgende: deze methoden hebben een voltooiings-handler. De code in de voltooiings-handler wordt uitgevoerd **nadat** de aanvraag is voltooid. Code nadat de voltooiings-handler wordt uitgevoerd **terwijl** de aanvraag wordt ingediend.

## <a name="create-a-container"></a>Een container maken

Elke Blob in Azure Storage moet zich in een container bevinden. In het volgende voor beeld ziet u hoe u een container met de naam *newcontainer* maakt in uw opslag account als deze nog niet bestaat. Wanneer u een naam kiest voor uw container, moet u mindful van de hierboven genoemde naamgevings regels.

```objc
-(void)createContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

U kunt controleren of dit werkt door naar de [Microsoft Azure Storage Explorer](https://storageexplorer.com) te kijken en te controleren of *newcontainer* in de lijst met containers voor uw opslag account staat.

## <a name="set-container-permissions"></a>Container machtigingen instellen

De machtigingen van een container zijn standaard geconfigureerd voor **persoonlijke** toegang. Containers bieden echter enkele verschillende opties voor toegang tot de container:

- **Persoonlijk**: container-en BLOB-gegevens kunnen alleen worden gelezen door de eigenaar van het account.
- **BLOB**: BLOB-gegevens binnen deze container kunnen worden gelezen via anonieme aanvragen, maar er zijn geen container gegevens beschikbaar. Clients kunnen geen blobs in de container opsommen via anonieme aanvragen.
- **Container**: container-en BLOB-gegevens kunnen worden gelezen via anonieme aanvragen. Clients kunnen blobs in de container opsommen via een anonieme aanvraag, maar kunnen containers in het opslag account niet inventariseren.

In het volgende voor beeld ziet u hoe u een container maakt met toegangs machtigingen voor de **container** , zodat open bare, alleen-lezen toegang wordt toegestaan voor alle gebruikers op het Internet:

```objc
-(void)createContainerWithPublicAccess{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

## <a name="upload-a-blob-into-a-container"></a>Een blob uploaden naar een container

Zoals vermeld in de sectie Blob service concepten, biedt Blob Storage drie verschillende typen blobs: blok-blobs, toevoeg-blobs en pagina-blobs. De Azure Storage iOS-bibliotheek ondersteunt alle drie typen blobs. In de meeste gevallen is een blok-blob het aangewezen type om te gebruiken.

In het volgende voor beeld ziet u hoe u een blok-BLOB uploadt vanuit een NSString. Als er al een blob met dezelfde naam bestaat in deze container, wordt de inhoud van deze BLOB overschreven.

```objc
-(void)uploadBlobToContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
        {
            if (error){
                NSLog(@"Error in creating container.");
            }
            else{
                // Create a local blob object
                AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                // Upload blob to Storage
                [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                    if (error){
                        NSLog(@"Error in creating blob.");
                    }
                }];
            }
        }];
}
```

U kunt controleren of dit werkt door naar de [Microsoft Azure Storage Explorer](https://storageexplorer.com) te kijken en te controleren of de container, *containerpublic*, de blob, *sampleblob* bevat. In dit voor beeld hebben we een open bare container gebruikt, zodat u ook kunt controleren of deze toepassing heeft gewerkt door naar de BLOB-URI te gaan:

```http
https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob
```

Naast het uploaden van een blok-Blob uit een NSString, bestaan er soort gelijke methoden voor NSData, NSInputStream of een lokaal bestand.

## <a name="list-the-blobs-in-a-container"></a>Blobs in een container vermelden

In het volgende voor beeld ziet u hoe alle blobs in een container worden weer gegeven. Als u deze bewerking wilt uitvoeren, moet u mindful van de volgende para meters:

- **continuationToken** -het vervolg token geeft aan waar de vermelding moet worden gestart. Als er geen token is, worden de blobs vanaf het begin weer gegeven. Een wille keurig aantal blobs kan worden weer gegeven, van nul tot een ingesteld maximum. Zelfs als deze methode nul resultaten retourneert, als `results.continuationToken` niet Nil is, zijn er mogelijk meer blobs op de service die niet zijn vermeld.
- **voor voegsel** : u kunt het voor voegsel opgeven dat moet worden gebruikt voor de BLOB-vermelding. Alleen blobs die met dit voor voegsel beginnen, worden weer gegeven.
- **useFlatBlobListing** : zoals vermeld in de sectie [Naamgeving en verwijzende containers en blobs](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) , hoewel de BLOB service een plat opslag schema is, kunt u een virtuele hiërarchie maken door blobs aan te geven met behulp van padgegevens. Niet-platte vermelding wordt momenteel niet ondersteund. Deze functie is binnenkort beschikbaar. Voor dit moment moet deze waarde **Ja** zijn.
- **blobListingDetails** -u kunt opgeven welke items moeten worden meegenomen bij het weer geven van blobs
  - _AZSBlobListingDetailsNone_: Geef alleen doorgevoerde blobs weer en retour neer geen blob-meta gegevens.
  - _AZSBlobListingDetailsSnapshots_: vermeld doorgevoerde blobs en BLOB-moment opnamen.
  - _AZSBlobListingDetailsMetadata_: ophalen van BLOB-meta gegevens voor elke blob die in de vermelding is geretourneerd.
  - _AZSBlobListingDetailsUncommittedBlobs_: een lijst met doorgevoerde en niet-doorgevoerde blobs.
  - _AZSBlobListingDetailsCopy_: Kopieer eigenschappen opnemen in de vermelding.
  - _AZSBlobListingDetailsAll_: alle beschik bare doorgevoerde blobs, niet-doorgevoerde blobs en moment opnamen weer geven en alle meta gegevens en kopieer status voor deze blobs retour neren.
- **maxResults** : het maximum aantal resultaten dat moet worden geretourneerd voor deze bewerking. Gebruik-1 om geen limiet in te stellen.
- **completionHandler** : het code blok dat moet worden uitgevoerd met de resultaten van de vermelding.

In dit voor beeld wordt een hulp methode gebruikt voor het recursief aanroepen van de lijst blobs-methode wanneer een vervolg token wordt geretourneerd.

```objc
-(void)listBlobsInContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    //List all blobs in container
    [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
        if (error != nil){
            NSLog(@"Error in creating container.");
        }
    }];
}

//List blobs helper method
-(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
{
    [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
        if (error)
        {
            completionHandler(error);
        }
        else
        {
            for (int i = 0; i < results.blobs.count; i++) {
                NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
            }
            if (results.continuationToken)
            {
                [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
            }
            else
            {
                completionHandler(nil);
            }
        }
    }];
}
```

## <a name="download-a-blob"></a>Een blob downloaden

In het volgende voor beeld ziet u hoe u een BLOB kunt downloaden naar een NSString-object.

```objc
-(void)downloadBlobToString{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

    // Download blob
    [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
        if (error) {
            NSLog(@"Error in downloading blob");
        }
        else{
            NSLog(@"%@",text);
        }
    }];
}
```

## <a name="delete-a-blob"></a>Een blob verwijderen

In het volgende voor beeld ziet u hoe u een BLOB verwijdert.

```objc
-(void)deleteBlob{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

    // Delete blob
    [blockBlob deleteWithCompletionHandler:^(NSError *error) {
        if (error) {
            NSLog(@"Error in deleting blob.");
        }
    }];
}
```

## <a name="delete-a-blob-container"></a>Een BLOB-container verwijderen

In het volgende voor beeld ziet u hoe u een container verwijdert.

```objc
-(void)deleteContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Delete container
    [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
        if(error){
            NSLog(@"Error in deleting container");
        }
    }];
}
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u Blob Storage van iOS kunt gebruiken, volgt u deze koppelingen voor meer informatie over de iOS-bibliotheek en de opslag service.

- [Azure Storage-client bibliotheek voor iOS](https://github.com/azure/azure-storage-ios)
- [Azure Storage iOS-referentie documentatie](https://azure.github.io/azure-storage-ios/)
- [REST-API voor Azure Storage-services](/rest/api/storageservices/)
- [Blog van het Azure Storage-team](/archive/blogs/windowsazurestorage/)

Als u vragen hebt over deze bibliotheek, kunt u terecht op onze [micro soft Q&een vraag pagina](/answers/topics/azure-blob-storage.html) of [stack overflow](https://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Als u functie suggesties hebt voor Azure Storage, kunt u een bericht plaatsen op [Azure Storage feedback](https://feedback.azure.com/forums/217298-storage/).