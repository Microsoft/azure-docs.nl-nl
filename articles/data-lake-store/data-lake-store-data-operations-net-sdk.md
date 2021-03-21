---
title: '.NET SDK: bestandssysteem bewerkingen op Data Lake Storage Gen1-Azure'
description: Gebruik de Azure Data Lake Storage Gen1 .NET SDK voor bestandssysteem bewerkingen op Data Lake Storage Gen1 zoals mappen maken, enzovoort.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/03/2020
ms.author: twooley
ms.custom: devx-track-dotnet
ms.openlocfilehash: faa539119bbb8d73e66788b8ba472cf0db26a2e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102431734"
---
# <a name="filesystem-operations-on-data-lake-storage-gen1-using-the-net-sdk"></a>Bestandssysteem bewerkingen op Data Lake Storage Gen1 met behulp van de .NET SDK

> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

In dit artikel vindt u informatie over het uitvoeren van bestandssysteem bewerkingen op Data Lake Storage Gen1 met behulp van de .NET SDK. Bestandssysteem bewerkingen omvatten het maken van mappen in een Data Lake Storage Gen1-account, het uploaden van bestanden, het downloaden van bestanden, enzovoort.

Zie voor instructies over het uitvoeren van account beheer bewerkingen op Data Lake Storage Gen1 met behulp van de .NET SDK [account beheer bewerkingen op Data Lake Storage gen1 met behulp van .NET SDK](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Vereisten

* **Visual Studio 2013 of hoger**. De instructies in dit artikel gebruiken Visual Studio 2019.

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Azure data Lake Storage gen1-account**. Zie [aan de slag met Azure data Lake Storage gen1](data-lake-store-get-started-portal.md)voor instructies over het maken van een account.

## <a name="create-a-net-application"></a>Een .NET-toepassing maken

Dit codevoorbeeld beschikbaar [in GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) doorloopt het proces waarin bestanden in het archief worden gemaakt, bestanden worden samengevoegd, een bestand wordt gedownload en een aantal bestanden uit het archief wordt verwijderd. In dit gedeelte van het artikel komen de belangrijkste onderdelen van de code aan bod.

1. Selecteer in Visual Studio het menu **bestand** , **Nieuw** en vervolgens **project**.
1. Kies **console-app (.NET Framework)** en selecteer **volgende**.
1. Voer in **project naam**, Enter `CreateADLApplication` en selecteer vervolgens **maken**.
1. Voeg de NuGet-pakketten toe aan het project.

   1. Klik in Solution Explorer met de rechtermuisknop op de projectnaam en klik op **Manage NuGet Packages**.
   1. Controleer op het tabblad **NuGet package manager** of de **pakket bron** is ingesteld op **nuget.org**. Zorg er ook voor dat het selectie vakje **include Prerelease** is ingeschakeld.
   1. Zoek en installeer de volgende NuGet-pakketten:

      * `Microsoft.Azure.DataLake.Store` -In dit artikel wordt gebruikgemaakt van v 1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` -In dit artikel wordt v 2.3.1 gebruikt.

      Sluit **NuGet package manager**.

1. Open **Program.cs**, verwijder de bestaande code en neem de volgende instructies op om verwijzingen naar naamruimten toe te voegen.

    ```
    using System;
    using System.IO;using System.Threading;
    using System.Linq;
    using System.Text;
    using System.Collections.Generic;
    using System.Security.Cryptography.X509Certificates; // Required only if you're using an Azure AD application created with certificates
                
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.DataLake.Store;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Declareer de variabelen zoals hieronder wordt weergegeven en vervang de tijdelijke aanduidingen door waarden. Zorg er ook voor dat het lokale pad en de bestandsnaam die u hier opgeeft al bestaan op de computer.

    ```
    namespace SdkSample
    {
        class Program
        {
            private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";
        }
    }
    ```

In de overige secties van het artikel ziet u hoe u de beschik bare .NET-methoden kunt gebruiken om bewerkingen uit te voeren, zoals verificatie, het uploaden van bestanden, enzovoort.

## <a name="authentication"></a>Verificatie

* Voor verificatie door eind gebruikers voor uw toepassing raadpleegt [u verificatie door eind gebruikers met data Lake Storage gen1 met behulp van .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Zie [service-to-service-verificatie met data Lake Storage gen1 met behulp van .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)voor service-to-service verificatie voor uw toepassing.

## <a name="create-client-object"></a>Clientobject maken

Met het volgende code fragment wordt het client object van het Data Lake Storage Gen1-bestands systeem gemaakt, dat wordt gebruikt om aanvragen voor de service te verlenen.

```
// Create client objects
AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);
```

## <a name="create-a-file-and-directory"></a>Een bestand en map maken

Voeg het volgende codefragment toe aan uw toepassing. Met dit code fragment wordt een bestand en eventuele bovenliggende mappen toegevoegd die niet bestaan.

```
// Create a file - automatically creates any parent directories that don't exist
// The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store

using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);

    textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="append-to-a-file"></a>Toevoegen aan een bestand

Met het volgende code fragment worden gegevens toegevoegd aan een bestaand bestand in Data Lake Storage Gen1-account.

```
// Append to existing file

using (var stream = client.GetAppendStream(fileName))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="read-a-file"></a>Een bestand lezen

In het volgende code fragment wordt de inhoud van een bestand in Data Lake Storage Gen1 gelezen.

```
//Read file contents

using (var readStream = new StreamReader(client.GetReadStream(fileName)))
{
    string line;
    while ((line = readStream.ReadLine()) != null)
    {
        Console.WriteLine(line);
    }
}
```

## <a name="get-file-properties"></a>Bestandseigenschappen opvragen

Het volgende codefragment retourneert de eigenschappen die zijn gekoppeld aan een bestand of map.

```
// Get file properties
var directoryEntry = client.GetDirectoryEntry(fileName);
PrintDirectoryEntry(directoryEntry);
```

De definitie van de `PrintDirectoryEntry` methode is beschikbaar als onderdeel van het [voor beeld op github](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="rename-a-file"></a>De naam van een bestand wijzigen

In het volgende code fragment wordt de naam van een bestaand bestand in een Data Lake Storage Gen1-account gewijzigd.

```
// Rename a file
string destFilePath = "/Test/testRenameDest3.txt";
client.Rename(fileName, destFilePath, true);
```

## <a name="enumerate-a-directory"></a>Een map opsommen

In het volgende code fragment worden de mappen in een Data Lake Storage Gen1-account opgesomd.

```
// Enumerate directory
foreach (var entry in client.EnumerateDirectory("/Test"))
{
    PrintDirectoryEntry(entry);
}
```

De definitie van de `PrintDirectoryEntry` methode is beschikbaar als onderdeel van het [voor beeld op github](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Mappen recursief verwijderen

Het volgende code fragment verwijdert een map en alle submappen, recursief.

```
// Delete a directory and all its subdirectories and files
client.DeleteRecursive("/Test");
```

## <a name="samples"></a>Voorbeelden

Hier volgen enkele voor beelden die laten zien hoe u de Data Lake Storage Gen1 bestandssysteem-SDK gebruikt.

* [Basis voorbeeld op GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Geavanceerd voor beeld op GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Zie ook

* [Account beheer bewerkingen op Data Lake Storage Gen1 met behulp van .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Naslag informatie over Data Lake Storage Gen1 .NET SDK](/dotnet/api/overview/azure/data-lake-store)

## <a name="next-steps"></a>Volgende stappen

* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)