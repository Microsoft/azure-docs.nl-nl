---
title: Blobs kopiëren van een opslag account naar een Azure Media Services-Asset | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u een bestaande BLOB kopieert naar een Media Services-activum. In het voor beeld worden Azure Media Services .NET SDK-extensies gebruikt.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 6ee6af8726dbd0a7b564b442242572afec670f4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016743"
---
# <a name="copying-existing-blobs-into-a-media-services-asset"></a>Bestaande blobs kopiëren naar een Media Services-Asset

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

In dit artikel wordt beschreven hoe u blobs van een opslag account kopieert naar een nieuwe Azure Media Services-asset (AMS) met behulp van [Azure Media Services .NET SDK-extensies](https://github.com/Azure/azure-sdk-for-media-services-extensions/).

U moet niet proberen om de inhoud te wijzigen van BLOB-containers die zijn gegenereerd door Media Services zonder gebruik te maken van media service-Api's.

De extensie methoden werken met:

- Normale activa.
- Live-Archief assets (FragBlob-indeling).
- Bron-en doel assets die deel uitmaken van verschillende Media Services accounts (zelfs via verschillende data centers). Er kunnen echter kosten in rekening worden gebracht. Zie [gegevens overdracht](https://azure.microsoft.com/pricing/#header-11)voor meer informatie over prijzen.

Het artikel bevat twee code voorbeelden:

1. Kopieer blobs van een asset in één AMS-account naar een nieuwe asset in een ander AMS-account.
2. Kopieer blobs van een opslag account naar een nieuwe asset in een AMS-account.

## <a name="copy-blobs-between-two-ams-accounts"></a>Blobs kopiëren tussen twee AMS-accounts  

### <a name="prerequisites"></a>Vereisten

Twee Media Services accounts. Zie het artikel [een Media Services-account maken voor meer informatie](media-services-portal-create-account.md).

### <a name="download-sample"></a>Voorbeeld downloaden
U kunt de stappen in dit artikel volgen of u kunt een voor beeld downloaden dat de code bevat [die in dit](https://azure.microsoft.com/documentation/samples/media-services-dotnet-copy-blob-into-asset/)artikel wordt beschreven.

### <a name="set-up-your-project"></a>Uw project instellen

1. Stel uw ontwikkel omgeving in zoals beschreven in [Media Services Development met .net](media-services-dotnet-how-to-use.md). 
2. Voeg de sectie appSettings toe aan het. config-bestand en werk de waarden bij op basis van uw Media Services accounts, het doel-opslag account en de ID van de bron Asset.  

```xml
<appSettings>
    <add key="AMSSourceAADTenantDomain" value="tenant"/>
    <add key="AMSSourceRESTAPIEndpoint" value="endpoint"/>

    <add key="SourceAMSClientId" value="clientID"/>
    <add key="SourceAMSClientSecret" value="clientSecret"/>

    <add key="SourceAssetID" value="nb:cid:UUID:<id>"/>

    <add key="AMSDestAADTenantDomain" value="tenant"/>
    <add key="AMSDestRESTAPIEndpoint" value="endpoint"/>

    <add key="DestAMSClientId" value="clientID"/>
    <add key="DestAMSClientSecret" value="clientSecret"/>

    <add key="DestStorageAccountName" value="name"/>
    <add key="DestStorageAccountKey" value="key"/>

</appSettings>
```

### <a name="copy-blobs-from-an-asset-in-one-ams-account-into-an-asset-in-another-ams-account"></a>Blobs kopiëren van een asset in één AMS-account naar een asset in een ander AMS-account

De volgende code maakt gebruik van de methode extension **IAsset. Copy** om alle bestanden in de bron-Asset naar het doel activum te kopiëren met één extensie.

```csharp
using System;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Linq;
using System.Configuration;
using Microsoft.WindowsAzure.Storage.Auth;

namespace CopyExistingBlobsIntoAsset
{
    class Program
    {
        static string _sourceAADTenantDomain = 
            ConfigurationManager.AppSettings["AMSSourceAADTenantDomain"];
        static string _sourceRESTAPIEndpoint = 
            ConfigurationManager.AppSettings["AMSSourceRESTAPIEndpoint"];
        static string _sourceClientId = 
            ConfigurationManager.AppSettings["SourceAMSClientId"];
        static string _sourceClientSecret = 
            ConfigurationManager.AppSettings["SourceAMSClientSecret"];

        static string _destAADTenantDomain = 
            ConfigurationManager.AppSettings["AMSDestAADTenantDomain"];
        static string _destRESTAPIEndpoint = 
            ConfigurationManager.AppSettings["AMSDestRESTAPIEndpoint"];
        static string _destClientId = 
            ConfigurationManager.AppSettings["DestAMSClientId"];
        static string _destClientSecret = 
            ConfigurationManager.AppSettings["DestAMSClientSecret"];

        static string _destStorageAccountName = 
            ConfigurationManager.AppSettings["DestStorageAccountName"];
        static string _destStorageAccountKey = 
            ConfigurationManager.AppSettings["DestStorageAccountKey"];
        static string _sourceAssetID = 
            ConfigurationManager.AppSettings["SourceAssetID"];

        private static CloudMediaContext _sourceContext = null;
        private static CloudMediaContext _destContext = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials1 = new AzureAdTokenCredentials(_sourceAADTenantDomain,
                   new AzureAdClientSymmetricKey(_sourceClientId, _sourceClientSecret),
                   AzureEnvironments.AzureCloudEnvironment);

            AzureAdTokenCredentials tokenCredentials2 = new AzureAdTokenCredentials(_destAADTenantDomain,
                   new AzureAdClientSymmetricKey(_destClientId, _destClientSecret),
                   AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider1 = new AzureAdTokenProvider(tokenCredentials1);
            var tokenProvider2 = new AzureAdTokenProvider(tokenCredentials2);

            // Create the context for your source Media Services account.
            _sourceContext = new CloudMediaContext(new Uri(_sourceRESTAPIEndpoint), tokenProvider1);

            // Create the context for your destination Media Services account.
            _destContext = new CloudMediaContext(new Uri(_destRESTAPIEndpoint), tokenProvider2);

            // Get the credentials of the default Storage account bound to your destination Media Services account.
            StorageCredentials destinationStorageCredentials =
                new StorageCredentials(_destStorageAccountName, _destStorageAccountKey);

            // Get a reference to the source asset in the source context.
            IAsset sourceAsset = _sourceContext.Assets.Where(asset => asset.Id == _sourceAssetID).First();

            // Create an empty destination asset in the destination context.
            IAsset destinationAsset = _destContext.Assets.Create(sourceAsset.Name, AssetCreationOptions.None);

            // Copy the files in the source asset instance into the destination asset instance.
            // There is an additional overload with async support.
            sourceAsset.Copy(destinationAsset, destinationStorageCredentials);

            Console.WriteLine("Done");
        }
    }
}
```

## <a name="copy-blobs-from-a-storage-account-into-an-ams-account"></a>Blobs kopiëren van een opslag account naar een AMS-account 

### <a name="prerequisites"></a>Vereisten

- Een opslag account waarvan u de blobs wilt kopiëren.
- Een AMS-account waarnaar u blobs wilt kopiëren.

### <a name="set-up-your-project"></a>Uw project instellen

1. Stel uw ontwikkel omgeving in zoals beschreven in [Media Services Development met .net](media-services-dotnet-how-to-use.md). 
2. Voeg de sectie appSettings toe aan het. config-bestand en werk de waarden bij op basis van de bron-en doel-AMS accounts.

```xml
<appSettings>
    <add key="SourceStorageAccountName" value="name" />
    <add key="SourceStorageAccountKey" value="key" />
    <add key="NameOfBlobContainerYouWantToCopy" value="BlobContainerName"/>
    
    <add key="AMSAADTenantDomain" value="tenant"/>
    <add key="AMSRESTAPIEndpoint" value="endpoint"/>
    <add key="AMSClientId" value="clientID"/>
    <add key="AMSClientSecret" value="clientSecret"/>
    <add key="AMSStorageAccountName" value="name"/>
    <add key="AMSStorageAccountKey" value="key"/>
</appSettings>
```

### <a name="copy-blobs-from-some-storage-account-into-a-new-asset-in-an-ams-account"></a>Blobs kopiëren van een opslag account naar een nieuwe asset in een AMS-account

Met de volgende code worden blobs van een opslag account naar een Media Services-Asset gekopieerd. 

>[!NOTE]
>Er geldt een limiet van 1.000.000 beleidsregels voor verschillende AMS-beleidsitems (bijvoorbeeld voor Locator-beleid of ContentKeyAuthorizationPolicy). U moet dezelfde beleids-id gebruiken als u altijd dezelfde dagen/toegangsmachtigingen gebruikt, bijvoorbeeld beleidsregels voor locators die zijn bedoeld om gedurende een lange periode gehandhaafd te blijven (niet-upload-beleidsregels). Zie [Dit](media-services-dotnet-manage-entities.md#limit-access-policies) artikel voor meer informatie.

```csharp
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
    
namespace CopyExistingBlobsIntoAsset
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _sourceStorageAccountName =
            ConfigurationManager.AppSettings["SourceStorageAccountName"];
        private static readonly string _sourceStorageAccountKey =
            ConfigurationManager.AppSettings["SourceStorageAccountKey"];
        private static readonly string _NameOfBlobContainerYouWantToCopy =
            ConfigurationManager.AppSettings["NameOfBlobContainerYouWantToCopy"];

        private static readonly string _AMSAADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _AMSRESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];
        private static readonly string _AMSStorageAccountName =
            ConfigurationManager.AppSettings["AMSStorageAccountName"];
        private static readonly string _AMSStorageAccountKey =
            ConfigurationManager.AppSettings["AMSStorageAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static CloudStorageAccount _sourceStorageAccount = null;
        private static CloudStorageAccount _destinationStorageAccount = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AMSAADTenantDomain,
               new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
               AzureEnvironments.AzureCloudEnvironment);
            
            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            // Create the context for your source Media Services account.
            _context = new CloudMediaContext(new Uri(_AMSRESTAPIEndpoint), tokenProvider);

            _sourceStorageAccount =
                new CloudStorageAccount(new StorageCredentials(_sourceStorageAccountName,
                    _sourceStorageAccountKey), true);

            _destinationStorageAccount =
                new CloudStorageAccount(new StorageCredentials(_AMSStorageAccountName,
                    _AMSStorageAccountKey), true);

            CloudBlobClient sourceCloudBlobClient =
                _sourceStorageAccount.CreateCloudBlobClient();
            CloudBlobContainer sourceContainer =
                sourceCloudBlobClient.GetContainerReference(_NameOfBlobContainerYouWantToCopy);

            CreateAssetFromExistingBlobs(sourceContainer);

            Console.WriteLine("Done");
        }

        static private IAsset CreateAssetFromExistingBlobs(CloudBlobContainer sourceBlobContainer)
        {
            CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

            // Create a new asset. 
            IAsset asset = _context.Assets.Create("NewAsset_" + Guid.NewGuid(), AssetCreationOptions.None);

            IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
                TimeSpan.FromHours(24), AccessPermissions.Write);

            ILocator destinationLocator =
                _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);

            // Get the asset container URI and Blob copy from mediaContainer to assetContainer. 
            CloudBlobContainer destAssetContainer =
                destBlobStorage.GetContainerReference((new Uri(destinationLocator.Path)).Segments[1]);

            if (destAssetContainer.CreateIfNotExists())
            {
                destAssetContainer.SetPermissions(new BlobContainerPermissions
                {
                    PublicAccess = BlobContainerPublicAccessType.Blob
                });
            }

            var blobList = sourceBlobContainer.ListBlobs();

            foreach (CloudBlockBlob sourceBlob in blobList)
            {
                var assetFile = asset.AssetFiles.Create((sourceBlob as ICloudBlob).Name);

                ICloudBlob destinationBlob = destAssetContainer.GetBlockBlobReference(assetFile.Name);

                CopyBlob(sourceBlob, destAssetContainer);
                
                sourceBlob.FetchAttributes();
                assetFile.ContentFileSize = (sourceBlob as ICloudBlob).Properties.Length;
                assetFile.Update();
                Console.WriteLine("File {0} is of {1} size", assetFile.Name, assetFile.ContentFileSize);
            }

            asset.Update();

            destinationLocator.Delete();
            writePolicy.Delete();

            // Set the primary asset file.
            // If, for example, we copied a set of Smooth Streaming files, 
            // set the .ism file to be the primary file. 
            // If we, for example, copied an .mp4, then the mp4 would be the primary file. 
            var ismAssetFile = asset.AssetFiles.ToList().
                Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).FirstOrDefault();

            // The following code assigns the first .ism file as the primary file in the asset.
            // An asset should have one .ism file.  
            if (ismAssetFile != null)
            {
                ismAssetFile.IsPrimary = true;
                ismAssetFile.Update();
            }

            return asset;
        }

        /// <summary>
        /// Copies the specified blob into the specified container.
        /// </summary>
        /// <param name="sourceBlob">The source container.</param>
        /// <param name="destinationContainer">The destination container.</param>
        static private void CopyBlob(ICloudBlob sourceBlob, CloudBlobContainer destinationContainer)
        {
            var signature = sourceBlob.GetSharedAccessSignature(new SharedAccessBlobPolicy
            {
                Permissions = SharedAccessBlobPermissions.Read,
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
            });

            var destinationBlob = destinationContainer.GetBlockBlobReference(sourceBlob.Name);

            if (destinationBlob.Exists())
            {
                Console.WriteLine(string.Format("Destination blob '{0}' already exists. Skipping.", destinationBlob.Uri));
            }
            else
            {

                // Display the size of the source blob.
                Console.WriteLine(sourceBlob.Properties.Length);

                Console.WriteLine(string.Format("Copy blob '{0}' to '{1}'", sourceBlob.Uri, destinationBlob.Uri));
                destinationBlob.StartCopy(new Uri(sourceBlob.Uri.AbsoluteUri + signature));

                while (true)
                {
                    // The StartCopyFromBlob is an async operation, 
                    // so we want to check if the copy operation is completed before proceeding. 
                    // To do that, we call FetchAttributes on the blob and check the CopyStatus. 
                    destinationBlob.FetchAttributes();
                    if (destinationBlob.CopyState.Status != CopyStatus.Pending)
                    {
                        break;
                    }
                    //It's still not completed. So wait for some time.
                    System.Threading.Thread.Sleep(1000);
                }

                // Display the size of the destination blob.
                Console.WriteLine(destinationBlob.Properties.Length);

            }
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

U kunt nu de geüploade assets coderen. Zie [Assets coderen](media-services-portal-encode.md) voor meer informatie.

U kunt ook Azure Functions gebruiken om een coderingstaak te activeren op basis van een bestand dat binnenkomt in de geconfigureerde container. Zie [dit voorbeeld](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ) voor meer informatie.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
