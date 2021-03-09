---
title: Azure Cosmos DB bijlagen
description: Dit artikel bevat een overzicht van Azure Cosmos DB bijlagen.
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/07/2020
ms.reviewer: sngun
ms.openlocfilehash: 455fdc7615e0909189f311755571d02a9acca24e
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501988"
---
# <a name="azure-cosmos-db-attachments"></a>Azure Cosmos DB bijlagen
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB bijlagen zijn speciale items die verwijzingen bevatten naar een gekoppelde meta gegevens met een externe BLOB of een media bestand.

Azure Cosmos DB ondersteunt twee typen bijlagen:

* **Onbeheerde bijlagen** zijn een wrapper rond een URI-verwijzing naar een blob die is opgeslagen in een externe service (bijvoorbeeld Azure Storage, OneDrive, enz.). Deze benadering is vergelijkbaar met het opslaan van een URI-eigenschap in een standaard Azure Cosmos DB item.
* **Beheerde bijlagen** zijn blobs die intern worden beheerd en opgeslagen door Azure Cosmos DB en worden weer gegeven via een door het systeem gegenereerde media link.


> [!NOTE]
> Bijlage is een verouderde functie. Hun ondersteuning ligt binnen het bereik om voortdurende functionaliteit te bieden als u deze functie al gebruikt.
> 
> In plaats van bijlagen te gebruiken, raden we u aan Azure Blob Storage te gebruiken als een voor het doel gemaakte Blob Storage-service voor het opslaan van BLOB-gegevens. U kunt meta gegevens met betrekking tot blobs, samen met referentie-URI-koppelingen, opslaan in Azure Cosmos DB als item eigenschappen. Het opslaan van deze gegevens in Azure Cosmos DB biedt de mogelijkheid om meta gegevens en koppelingen op te vragen naar blobs die zijn opgeslagen in Azure Blob Storage.
> 
> Micro soft streeft ernaar een minimum 36-maand te bieden vóór het volledig terugvallen van bijlagen, die op een verdere datum worden aangekondigd.

## <a name="known-limitations"></a>Bekende beperkingen

De beheerde bijlagen van Azure Cosmos DB zijn niet hetzelfde als de ondersteuning voor standaard items: waarvoor u onbeperkte schaal baarheid, wereld wijde distributie en integratie met andere Azure-Services biedt.

- Bijlagen worden niet ondersteund in alle versies van de Sdk's van de Azure Cosmos DB.
- Beheerde bijlagen zijn beperkt tot 2 GB aan opslag per database account.
- Beheerde bijlagen zijn niet compatibel met de globale distributie van Azure Cosmos DB en ze worden niet gerepliceerd tussen regio's.

> [!NOTE]
> Azure Cosmos DB-API voor MongoDB versie 3,2 maakt gebruik van beheerde bijlagen voor GridFS, die daarom gelden voor de beperkingen van de beheerde bijlagen van Azure Cosmos DB.
>
> U wordt aangeraden ontwikkel aars die de MongoDB GridFS-functie hebt ingesteld, te upgraden naar Azure Cosmos DB-API voor MongoDB versie 3,6 of hoger, die is losgekoppeld van bijlagen en een sterker en robuustere ervaring biedt. Ontwikkel aars die gebruikmaken van de MongoDB GridFS-functieset, moeten ook overwegen Azure Blob Storage te gebruiken. Dit is gebaseerd op de opslag van blob-inhoud en biedt uitgebreide functionaliteit tegen lagere kosten vergeleken met GridFS.

## <a name="migrating-attachments-to-azure-blob-storage"></a>Bijlagen migreren naar Azure Blob Storage

U wordt aangeraden Azure Cosmos DB bijlagen te migreren naar Azure Blob Storage door de volgende stappen uit te voeren:

1. Kopieer bijlagen gegevens van de bron Azure Cosmos DB container naar uw doel-Azure Blob Storage-container.
2. Valideer de geüploade BLOB-gegevens in de Azure Blob Storage-doel container.
3. Als dit van toepassing is, kunt u URI-verwijzingen toevoegen aan de blobs in Azure Blob Storage als teken reeks eigenschappen binnen uw Azure Cosmos DB DataSet.
4. De code van uw toepassing refactorie om blobs te lezen en te schrijven vanuit de nieuwe Azure Blob Storage-container.

In het volgende DotNet-code voorbeeld ziet u hoe u bijlagen kopieert van Azure Cosmos DB naar Azure Blob-opslag als onderdeel van een migratie stroom met behulp van Azure Cosmos DB .NET SDK v2 en Azure Blob Storage .NET SDK V12. Zorg ervoor dat u de `<placeholder values>` voor het bron Azure Cosmos DB-account en het doel Azure Blob-opslag container vervangt.

```csharp

using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;

namespace attachments
{
    class Program
    {
        private static string cosmosAccount = "<Your_Azure_Cosmos_account_URI>";
        private static string cosmosKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>";
        private static string cosmosDatabaseName = "<Your_Azure_Cosmos_database>";
        private static string cosmosCollectionName = "<Your_Azure_Cosmos_collection>";
        private static string storageConnectionString = "<Your_Azure_Storage_connection_string>";
        private static string storageContainerName = "<Your_Azure_Storage_container_name>";
        private static DocumentClient cosmosClient = new DocumentClient(new Uri(cosmosAccount), cosmosKey);
        private static BlobServiceClient storageClient = new BlobServiceClient(storageConnectionString);
        private static BlobContainerClient storageContainerClient = storageClient.GetBlobContainerClient(storageContainerName);

        static void Main(string[] args)
        {
            CopyAttachmentsToBlobsAsync().Wait();
        }

        private async static Task CopyAttachmentsToBlobsAsync()
        {
            Console.WriteLine("Copying Azure Cosmos DB Attachments to Azure Blob Storage ...");

            int totalCount = 0;
            string docContinuation = null;

            // Iterate through each item (document in v2) in the Azure Cosmos DB container (collection in v2) to look for attachments.
            do
            {
                FeedResponse<dynamic> response = await cosmosClient.ReadDocumentFeedAsync(
                    UriFactory.CreateDocumentCollectionUri(cosmosDatabaseName, cosmosCollectionName),
                    new FeedOptions
                    {
                        MaxItemCount = -1,
                        RequestContinuation = docContinuation
                    });
                docContinuation = response.ResponseContinuation;

                foreach (Document document in response)
                {
                    string attachmentContinuation = null;
                    PartitionKey docPartitionKey = new PartitionKey(document.Id);

                    // Iterate through each attachment within the item (if any).
                    do
                    {
                        FeedResponse<Attachment> attachments = await cosmosClient.ReadAttachmentFeedAsync(
                            document.SelfLink,
                            new FeedOptions
                            {
                                PartitionKey = docPartitionKey,
                                RequestContinuation = attachmentContinuation
                            }
                        );
                        attachmentContinuation = attachments.ResponseContinuation;

                        foreach (var attachment in attachments)
                        {
                            // Download the attachment in to local memory.
                            MediaResponse content = await cosmosClient.ReadMediaAsync(attachment.MediaLink);

                            byte[] buffer = new byte[content.ContentLength];
                            await content.Media.ReadAsync(buffer, 0, buffer.Length);

                            // Upload the locally buffered attachment to blob storage
                            string blobId = String.Concat(document.Id, "-", attachment.Id);

                            Azure.Response<BlobContentInfo> uploadedBob = await storageContainerClient.GetBlobClient(blobId).UploadAsync(
                                new MemoryStream(buffer, writable: false),
                                true
                            );

                            Console.WriteLine("Copied attachment ... Item Id: {0} , Attachment Id: {1}, Blob Id: {2}", document.Id, attachment.Id, blobId);
                            totalCount++;

                            // Clean up attachment from Azure Cosmos DB.
                            // Warning: please verify you've succesfully migrated attachments to blog storage prior to cleaning up Azure Cosmos DB.
                            // await cosmosClient.DeleteAttachmentAsync(
                            //     attachment.SelfLink,
                            //     new RequestOptions { PartitionKey = docPartitionKey }
                            // );

                            // Console.WriteLine("Cleaned up attachment ... Document Id: {0} , Attachment Id: {1}", document.Id, attachment.Id);
                        }

                    } while (!string.IsNullOrEmpty(attachmentContinuation));
                }
            }
            while (!string.IsNullOrEmpty(docContinuation));

            Console.WriteLine("Finished copying {0} attachments to blob storage", totalCount);
        }
    }
}

```

## <a name="next-steps"></a>Volgende stappen

- Aan de slag met [Azure Blob Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- Referenties ophalen voor het gebruik van bijlagen via [de .NET SDK v2 van Azure Cosmos DB](/dotnet/api/microsoft.azure.documents.attachment)
- Referenties ophalen voor het gebruik van bijlagen via [de Java SDK v2 van Azure Cosmos DB](/java/api/com.microsoft.azure.documentdb.attachment)
- Referenties ophalen voor het gebruik van bijlagen via [de rest API van Azure Cosmos DB](/rest/api/cosmos-db/attachments)
