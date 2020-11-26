---
title: 'Zelfstudie: blobs versleutelen en ontsleutelen met Azure Key Vault'
titleSuffix: Azure Storage
description: Meer informatie over het versleutelen en ontsleutelen van een blob met versleuteling aan de clientzijde met Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: ddc9dbf77c04ea95e5b873c45de4c0df109514c7
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544442"
---
# <a name="tutorial---encrypt-and-decrypt-blobs-using-azure-key-vault"></a>Zelfstudie: blobs versleutelen en ontsleutelen met Azure Key Vault

In deze zelfstudie wordt beschreven hoe u gebruik kunt maken van opslagversleuteling aan de clientzijde met Azure Key Vault. U wordt begeleid bij het versleutelen en ontsleutelen van een blob in een consoletoepassing met behulp van deze technologieën.

**Geschatte tijdsduur:** 20 minuten

Zie [Wat is Azure Key Vault?](../../key-vault/general/overview.md) voor algemene informatie over Azure Key Vault.

Zie [Client-Side Encryption and Azure Key Vault for Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Versleuteling aan de clientzijde en Azure Key Vault voor Microsoft Azure Storage) voor algemene informatie over versleuteling aan de clientzijde voor Azure Storage.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze zelfstudie te voltooien:

* Een Azure Storage-account
* Visual Studio 2013 of later
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Overzicht van versleuteling aan de clientzijde

Zie [Client-Side Encryption and Azure Key Vault for Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Versleuteling aan de clientzijde en Azure Key Vault voor Microsoft Azure Storage) voor een overzicht van versleuteling aan de clientzijde voor Azure Storage

Hier volgt een korte beschrijving van hoe versleuteling aan de clientzijde werkt:

1. De Azure Storage Client SDK genereert een versleutelingssleutel voor inhoud (CEK). Dit is een symmetrische sleutel voor eenmalig gebruik.
2. Klantgegevens worden versleuteld met behulp van deze CEK.
3. De CEK wordt vervolgens verpakt (versleuteld) met behulp van de versleutelingssleutel voor sleutel (KEK). De KEK wordt geïdentificeerd aan de hand van een sleutel-id en kan een asymmetrisch sleutelpaar of een symmetrische sleutel zijn en kan lokaal worden beheerd of opgeslagen in Azure Key Vault. De Storage-client zelf heeft nooit toegang tot de KEK. Deze roept alleen het algoritme voor sleutelverpakking op die wordt geleverd door Key Vault. Klanten kunnen ervoor kiezen om aangepaste providers te gebruiken voor de verpakken/uitpakken van sleutels.
4. De versleutelde gegevens worden vervolgens geüpload naar de Azure Storage-service.

## <a name="set-up-your-azure-key-vault"></a>Uw Azure Key Vault instellen

Als u wilt doorgaan met deze zelfstudie, moet u de volgende stappen uitvoeren, zoals beschreven in de zelfstudie [Quickstart: een geheim uit Azure Key Vault instellen en ophalen met behulp van een .NET-web-app](../../key-vault/secrets/quick-create-net.md):

* Een sleutelkluis maken.
* Voeg een sleutel of geheim toe aan de sleutelkluis.
* Registreer een toepassing met Azure Active Directory.
* Geef de toepassing toestemming om de sleutel of het geheim te gebruiken.

Noteer de ClientID en ClientSecret die zijn gegenereerd bij het registreren van een toepassing met Azure Active Directory.

Maak beide sleutels in de sleutelkluis. Voor de rest van de zelfstudie wordt ervan uitgegaan dat u de volgende namen hebt gebruikt: ContosoKeyVault en TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Een consoletoepassing maken met pakketten en AppSettings

Maak in Visual Studio een nieuwe consoletoepassing.

Voeg de benodigde nuget-pakketten toe in de Package Manager Console.

```powershell
Install-Package Microsoft.Azure.ConfigurationManager
Install-Package Microsoft.Azure.Storage.Common
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Voeg AppSettings toe aan de App.Config.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Voeg de volgende `using`-instructies toe en zorg ervoor dat u een verwijzing naar System.Configuration aan het project toevoegt.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.KeyVault;
using System.Threading;
using System.IO;
```

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Een methode toevoegen voor het ophalen van een token in uw consoletoepassing

De volgende methode wordt gebruikt door Key Vault-klassen die moeten worden geverifieerd om toegang te krijgen tot uw sleutelkluis.

```csharp
private async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(
        CloudConfigurationManager.GetSetting("clientId"),
        CloudConfigurationManager.GetSetting("clientSecret"));
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

## <a name="access-azure-storage-and-key-vault-in-your-program"></a>Toegang tot Azure Storage en Key Vault in uw programma

Voeg de volgende code toe in de methode Main().

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    CloudConfigurationManager.GetSetting("accountName"),
    CloudConfigurationManager.GetSetting("accountKey")
);
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(CloudConfigurationManager.GetSetting("container"));
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```

> [!NOTE]
> Objectmodellen voor Key Vault
> 
> Het is belangrijk om te weten dat er eigenlijk twee Key Vault-objectmodellen zijn waarmee u rekening moet houden: een is gebaseerd op de REST API (KeyVault-naamruimte) en de andere is een extensie voor versleuteling aan de clientzijde.
> 
> De Key Vault Client communiceert met de REST API en begrijpt JSON-websleutels en -geheimen voor de twee soorten zaken die zijn opgenomen in Key Vault.
> 
> De Key Vault-extensies zijn klassen die specifiek zijn gemaakt voor versleuteling aan de clientzijde in Azure Storage. Ze bevatten een interface voor sleutels (IKey) en klassen gebaseerd op het concept van een Key Resolver. Er zijn twee implementaties van IKey die u moet kennen: RSAKey en SymmetricKey. Nu vallen ze toevallig samen met de zaken die zijn opgenomen in een Key Vault, maar ze zijn op dit moment onafhankelijke klassen (de sleutel die en het geheim dat door Key Vault Client wordt opgehaald, implementeren IKey niet).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Blob versleutelen en uploaden

Voeg de volgende code toe om een blob te versleutelen en deze te uploaden naar uw Azure-opslagaccount. De methode **ResolveKeyAsync** die wordt gebruikt, retourneert een IKey.

```csharp
// Retrieve the key that you created previously.
// The IKey that is returned here is an RsaKey.
var rsa = cloudResolver.ResolveKeyAsync(
            "https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", 
            CancellationToken.None).GetAwaiter().GetResult();

// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Reference a block blob.
CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

// Upload using the UploadFromStream method.
using (var stream = System.IO.File.OpenRead(@"C:\Temp\MyFile.txt"))
    blob.UploadFromStream(stream, stream.Length, null, options, null);
```

> [!NOTE]
> Als u de constructor BlobEncryptionPolicy bekijkt, ziet u dat deze een sleutel en/of een resolver kan accepteren. Houd er rekening mee dat u momenteel geen resolver kunt gebruiken voor versleuteling omdat deze momenteel geen standaardsleutel ondersteunt.

## <a name="decrypt-blob-and-download"></a>Blob ontsleutelen en downloaden

Bij ontsleuteling heeft het gebruik van de resolver-klassen echt zin. De id van de sleutel die wordt gebruikt voor versleuteling, is gekoppeld aan de blob in de metagegevens, zodat u de sleutel niet hoeft op te halen en de koppeling tussen de sleutel en de blob niet hoeft te onthouden. U hoeft alleen maar te controleren of de sleutel in Key Vault blijft.   

De persoonlijke sleutel van een RSA-sleutel blijft in Key Vault. Voor ontsleuteling wordt de versleutelde sleutel uit de blob-metagegevens die de CEK bevat, naar Key Vault gestuurd voor ontsleuteling.

Voeg het volgende toe om de blob die u zojuist hebt geüpload te ontsleutelen.

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

> [!NOTE]
> Er zijn een aantal andere soorten resolvers voor eenvoudiger sleutelbeheer, waaronder: AggregateKeyResolver en CachingKeyResolver.

## <a name="use-key-vault-secrets"></a>Key Vault-geheimen gebruiken

De beste manier om een geheim te gebruiken met versleuteling aan de clientzijde is via de SymmetricKey-klasse, omdat een geheim in feite een symmetrische sleutel is. Maar zoals hierboven vermeld, komt een geheim in Key Vault niet precies overeen met een SymmetricKey. Er zijn enkele zaken die u moet weten:

* De sleutel in een SymmetricKey moet een vaste lengte hebben: 128, 192, 256, 384 of 512 bits.
* De sleutel in een SymmetricKey moet Base64-gecodeerd zijn.
* Een Key Vault-geheim dat wordt gebruikt als een SymmetricKey moet het inhoudstype 'application/octet-stream' hebben in Key Vault.

Hier volgt een voorbeeld in PowerShell van het maken van een geheim in Key Vault dat kan worden gebruikt als een SymmetricKey.
Houd er rekening mee dat de in code vastgelegde waarde, $key, alleen bestemd is voor demonstratiedoeleinden. In uw eigen code kunt u deze sleutel het beste genereren.

```powershell
// Here we are making a 128-bit key so we have 16 characters.
//     The characters are in the ASCII range of UTF8 so they are
//    each 1 byte. 16 x 8 = 128.
$key = "qwertyuiopasdfgh"
$b = [System.Text.Encoding]::UTF8.GetBytes($key)
$enc = [System.Convert]::ToBase64String($b)
$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

// Substitute the VaultName and Name in this command.
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"
```

In uw consoletoepassing kunt u dezelfde aanroep als eerder gebruiken om dit geheim op te halen als een SymmetricKey.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```

Dat is alles. Veel plezier!

## <a name="next-steps"></a>Volgende stappen

Zie [Microsoft Azure Storage-clientbibliotheek voor .NET](/previous-versions/azure/dn261237(v=azure.100)) voor meer informatie over het gebruik van Microsoft Azure Storage met C#.

Zie [Blob Service REST API](/rest/api/storageservices/Blob-Service-REST-API) voor meer informatie over de Blob REST API.

Ga voor de meest recente informatie over Microsoft Azure Storage naar het [teamblog van Microsoft Azure Storage](/archive/blogs/windowsazurestorage/).