---
title: Een Azure Data Lake Storage Gen1-account beheren met .NET
description: Meer informatie over het gebruik van de .NET SDK voor Azure Data Lake Storage Gen1-account beheer bewerkingen.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c4ed092604e9511023a7025a69dc7a78652ea56
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430476"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Account beheer bewerkingen op Azure Data Lake Storage Gen1 met behulp van .NET SDK
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

In dit artikel leert u hoe u account beheer bewerkingen kunt uitvoeren op Azure Data Lake Storage Gen1 met behulp van .NET SDK. Account beheer bewerkingen zijn onder andere het maken van een Data Lake Storage Gen1-account, het weer geven van de accounts in een Azure-abonnement, het verwijderen van de accounts, enzovoort.

Zie [bestandssysteem bewerkingen op Data Lake Storage gen1 met behulp van .NET SDK](data-lake-store-data-operations-net-sdk.md)voor instructies over het uitvoeren van gegevens beheer bewerkingen op Data Lake Storage gen1 met behulp van .NET SDK.

## <a name="prerequisites"></a>Vereisten
* **Visual Studio 2013 of hoger**. In de onderstaande instructies wordt gebruikgemaakt van Visual Studio 2019.

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-net-application"></a>Een .NET-toepassing maken
1. Selecteer in Visual Studio het menu **bestand** , **Nieuw** en vervolgens **project**.
2. Kies **console-app (.NET Framework)** en selecteer **volgende**.
3. Voer in **project naam**, Enter `CreateADLApplication` en selecteer vervolgens **maken**.

4. Voeg de NuGet-pakketten toe aan het project.

   1. Klik in Solution Explorer met de rechtermuisknop op de projectnaam en klik op **Manage NuGet Packages**.
   2. Controleer op het tabblad **NuGet package manager** of **pakket bron** is ingesteld op **nuget.org** en of het selectie vakje **include Prerelease** is ingeschakeld.
   3. Zoek en installeer de volgende NuGet-pakketten:

      * `Microsoft.Azure.Management.DataLake.Store`: in deze zelfstudie wordt gebruikgemaakt van v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication`: in deze zelfstudie wordt gebruikgemaakt van v2.2.12.

        ![Een NuGet-bron toevoegen](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Een nieuw Azure Data Lake-account maken")
   4. Sluit **NuGet package manager**.
5. Open **Program.cs**, verwijder de bestaande code en neem de volgende instructies op om verwijzingen naar naamruimten toe te voegen.

    ```csharp
    using System;
    using System.IO;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Collections.Generic;
    using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

6. Declareer de variabelen en vervang de tijdelijke aanduidingen door waarden. Zorg er ook voor dat het lokale pad en de bestandsnaam die u opgeeft al bestaan op de computer.

    ```csharp
    namespace SdkSample
    {
        class Program
        {
            private static DataLakeStoreAccountManagementClient _adlsClient;
                
            private static string _adlsAccountName;
            private static string _resourceGroupName;
            private static string _location;
            private static string _subId;

            private static void Main(string[] args)
            {
                _adlsAccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net"; 
                _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                _location = "East US 2";
                _subId = "<SUBSCRIPTION-ID>";                    
            }
        }
    }
    ```

In de rest van het artikel ziet u het gebruik van de beschikbare .NET-methoden voor het uitvoeren van bewerkingen, zoals verificatie, het uploaden van bestanden enzovoort.

## <a name="authentication"></a>Verificatie

* Voor verificatie door eind gebruikers voor uw toepassing raadpleegt [u verificatie door eind gebruikers met data Lake Storage gen1 met behulp van .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Zie [service-to-service-verificatie met data Lake Storage gen1 met behulp van .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)voor service-to-service verificatie voor uw toepassing.

## <a name="create-client-object"></a>Clientobject maken
Met het volgende code fragment wordt het client object van het Data Lake Storage Gen1-account gemaakt. dit wordt gebruikt om account beheer aanvragen voor de service te verlenen, zoals account maken, account verwijderen, enzovoort.

```csharp
// Create client objects and set the subscription ID
_adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
```
    
## <a name="create-a-data-lake-storage-gen1-account"></a>Een Data Lake Storage Gen1-account maken
Met het volgende code fragment maakt u een Data Lake Storage Gen1-account in het Azure-abonnement dat u hebt gegeven tijdens het maken van het client object van het Data Lake Storage Gen1-account.

```csharp
// Create Data Lake Storage Gen1 account
var adlsParameters = new DataLakeStoreAccount(location: _location);
_adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
```

## <a name="list-all-data-lake-storage-gen1-accounts-within-a-subscription"></a>Alle Data Lake Storage Gen1 accounts in een abonnement weer geven
Voeg de volgende methode toe aan uw klassedefinitie. In het volgende code fragment wordt een lijst weer gegeven met alle Data Lake Storage Gen1 accounts binnen een bepaald Azure-abonnement.

```csharp
// List all Data Lake Storage Gen1 accounts within the subscription
public static List<DataLakeStoreAccountBasic> ListAdlStoreAccounts()
{
    var response = _adlsClient.Account.List(_adlsAccountName);
    var accounts = new List<DataLakeStoreAccountBasic>(response);

    while (response.NextPageLink != null)
    {
        response = _adlsClient.Account.ListNext(response.NextPageLink);
        accounts.AddRange(response);
    }

    return accounts;
}
```

## <a name="delete-a-data-lake-storage-gen1-account"></a>Een Data Lake Storage Gen1 account verwijderen
Met het volgende code fragment verwijdert u het Data Lake Storage Gen1 account dat u eerder hebt gemaakt.

```csharp
// Delete Data Lake Storage Gen1 account
_adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
```

## <a name="see-also"></a>Zie ook
* [Bestandssysteem bewerkingen op Data Lake Storage Gen1 met behulp van .NET SDK](data-lake-store-data-operations-net-sdk.md)
* [Naslag informatie over Data Lake Storage Gen1 .NET SDK](/dotnet/api/overview/azure/data-lake-store)

## <a name="next-steps"></a>Volgende stappen
* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)