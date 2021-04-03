---
title: Een opgeslagen toegangs beleid maken met .NET
titleSuffix: Azure Storage
description: Gebruik Azure Storage en .NET om een opgeslagen toegangs beleid te maken. Extra controle niveaus over gedeelde toegangs handtekeningen op service niveau op de server uitoefenen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 6cd6147fbe38710bcefd580e71be1d6f5d446a21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89010746"
---
# <a name="create-a-stored-access-policy-with-net"></a>Een opgeslagen toegangs beleid maken met .NET

Een opgeslagen toegangs beleid biedt een extra controle niveau voor Shared Access signatures (SAS) op service niveau aan de server zijde. Als u een opgeslagen toegangs beleid definieert, worden de hand tekeningen voor gedeelde toegang gegroepeerd en worden er aanvullende beperkingen geboden voor de hand tekeningen voor gedeelde toegang die door het beleid zijn gebonden. U kunt een opgeslagen toegangs beleid gebruiken om de start tijd, de verloop tijd of de machtigingen voor een SAS te wijzigen, of om deze in te trekken nadat deze is uitgegeven.
  
De volgende Azure Storage resources ondersteunen opgeslagen toegangs beleid:  
  
- Blobcontainers  
- Bestandsshares  
- Wachtrijen  
- Tabellen  
  
> [!NOTE]
> Een opgeslagen toegangs beleid voor een container kan worden gekoppeld aan een gedeelde toegangs handtekening die machtigingen verleent aan de container zelf of aan de blobs die deze bevat. Op dezelfde manier kan een opgeslagen toegangs beleid voor een bestands share worden gekoppeld aan een gedeelde toegangs handtekening waarmee machtigingen worden verleend aan de share zelf of aan de bestanden die deze bevat.  
>
> Opgeslagen toegangs beleid wordt alleen ondersteund voor een service-SAS. Opgeslagen toegangs beleid wordt niet ondersteund voor account-SAS of SAS voor gebruikers overdracht.  

Zie [een opgeslagen toegangs beleid definiëren](/rest/api/storageservices/define-stored-access-policy)voor meer informatie over opgeslagen toegangs beleid.

## <a name="create-a-stored-access-policy"></a>Een opgeslagen toegangs beleid maken

De onderliggende REST-bewerking voor het maken van een opgeslagen toegangs beleid is [ingesteld als container-ACL](/rest/api/storageservices/set-container-acl). U moet de bewerking autoriseren om een opgeslagen toegangs beleid te maken via een gedeelde sleutel met behulp van de toegangs sleutels voor het account in een connection string. Het autoriseren van de **set-container-ACL** -bewerking met Azure AD-referenties wordt niet ondersteund. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens bewerkingen](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie.

De volgende code voorbeelden maken een opgeslagen toegangs beleid voor een container. U kunt het toegangs beleid gebruiken om beperkingen voor een service-SA'S op te geven voor de container of de blobs.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Als u een opgeslagen toegangs beleid wilt maken in een container met versie 12 van de .NET-client bibliotheek voor Azure Storage, roept u een van de volgende methoden aan:

- [BlobContainerClient.SetAccessPolicy](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy)
- [BlobContainerClient.SetAccessPolicyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicyasync)

In het volgende voor beeld wordt een opgeslagen toegangs beleid gemaakt dat geldt voor één dag en die lees-/schrijfmachtigingen verleent:

```csharp
async static Task CreateStoredAccessPolicyAsync(string containerName)
{
    string connectionString = "";

    // Use the connection string to authorize the operation to create the access policy.
    // Azure AD does not support the Set Container ACL operation that creates the policy.
    BlobContainerClient containerClient = new BlobContainerClient(connectionString, containerName);

    try
    {
        await containerClient.CreateIfNotExistsAsync();

        // Create one or more stored access policies.
        List<BlobSignedIdentifier> signedIdentifiers = new List<BlobSignedIdentifier>
        {
            new BlobSignedIdentifier
            {
                Id = "mysignedidentifier",
                AccessPolicy = new BlobAccessPolicy
                {
                    StartsOn = DateTimeOffset.UtcNow.AddHours(-1),
                    ExpiresOn = DateTimeOffset.UtcNow.AddDays(1),
                    Permissions = "rw"
                }
            }
        };
        // Set the container's access policy.
        await containerClient.SetAccessPolicyAsync(permissions: signedIdentifiers);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.ErrorCode);
        Console.WriteLine(e.Message);
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Als u een opgeslagen toegangs beleid wilt maken in een container met versie 12 van de .NET-client bibliotheek voor Azure Storage, roept u een van de volgende methoden aan:

- [CloudBlobContainer. SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [CloudBlobContainer. SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

In het volgende voor beeld wordt een opgeslagen toegangs beleid gemaakt dat van toepassing is op één dag en die lees-, schrijf-en lijst machtigingen verleent:

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

---

## <a name="see-also"></a>Zie ook

- [Beperkte toegang verlenen tot Azure Storage-resources met behulp van Shared Access signatures (SAS)](storage-sas-overview.md)
- [Opgeslagen toegangsbeleid definiëren](/rest/api/storageservices/define-stored-access-policy)
- [Azure Storage-verbindingsreeksen configureren](storage-configure-connection-string.md)
