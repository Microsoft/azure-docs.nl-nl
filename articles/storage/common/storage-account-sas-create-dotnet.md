---
title: Een account-SAS maken met .NET
titleSuffix: Azure Storage
description: Meer informatie over het maken van een account voor Shared Access Signature (SAS) met behulp van de .NET-client bibliotheek.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/12/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 2918b845430a6fc6dc59eca7041c114fc9d06515
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97092207"
---
# <a name="create-an-account-sas-with-net"></a>Een account-SAS maken met .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

In dit artikel wordt beschreven hoe u de sleutel van het opslag account gebruikt om een account-SAS te maken met de [Azure Storage-client bibliotheek voor .net](/dotnet/api/overview/azure/storage).

## <a name="create-an-account-sas"></a>Een account-SAS maken

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Een account-SAS is ondertekend met de toegangs sleutel voor het account. Gebruik de [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) -klasse om de referentie te maken die wordt gebruikt om de sa's te ondertekenen. Maak vervolgens een nieuw [AccountSasBuilder](/dotnet/api/azure.storage.sas.accountsasbuilder) -object en roep de [ToSasQueryParameters](/dotnet/api/azure.storage.sas.accountsasbuilder.tosasqueryparameters) aan om de SAS-token teken reeks op te halen.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetAccountSASToken":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Als u een account-SAS voor een container wilt maken, roept u de methode [Cloud Storage account. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.getsharedaccesssignature) aan.

In het volgende code voorbeeld worden een account-SA'S gemaakt die geldig zijn voor de BLOB-en bestands Services, en worden de machtigingen lezen, schrijven en weer geven voor de client machtigingen voor toegang tot Api's op service niveau. De account-SAS beperkt het Protocol tot HTTPS, zodat de aanvraag moet worden gemaakt met HTTPS. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use Shared Key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<storage-account>;AccountKey=<account-key>";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

---

## <a name="use-an-account-sas-from-a-client"></a>Een account-SAS van een client gebruiken

Als u de account-SA'S wilt gebruiken om toegang te krijgen tot serviceniveau-Api's voor de Blob service, moet u een Blob service-client object maken met behulp van de SAS en het Blob Storage-eind punt voor uw opslag account.

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_UseAccountSAS":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Vervang in dit code fragment de `<storage-account>` tijdelijke aanduiding door de naam van uw opslag account.

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

---

## <a name="next-steps"></a>Volgende stappen

- [Beperkte toegang verlenen tot Azure Storage-resources met behulp van Shared Access signatures (SAS)](storage-sas-overview.md)
- [Een account-SAS maken](/rest/api/storageservices/create-account-sas)
