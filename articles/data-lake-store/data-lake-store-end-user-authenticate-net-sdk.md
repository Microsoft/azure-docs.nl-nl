---
title: Verificatie van de eind gebruiker-.NET met Data Lake Storage Gen1-Azure
description: Meer informatie over het bezorgen van de verificatie van eind gebruikers met Azure Data Lake Storage Gen1 met behulp van Azure Active Directory met .NET SDK
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-csharp
ms.openlocfilehash: 67ba4f12aec9e987d79109b7197d03301bf40650
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89004779"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Verificatie door eind gebruikers met Azure Data Lake Storage Gen1 met behulp van .NET SDK
> [!div class="op_single_selector"]
> * [Java gebruiken](data-lake-store-end-user-authenticate-java-sdk.md)
> * [.NET SDK gebruiken](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Python gebruiken](data-lake-store-end-user-authenticate-python.md)
> * [REST-API gebruiken](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

In dit artikel leert u hoe u de .NET SDK kunt gebruiken om verificatie door eind gebruikers uit te voeren met Azure Data Lake Storage Gen1. Zie [service-to-service-verificatie met data Lake Storage gen1 met behulp van .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)voor service-naar-service verificatie met data Lake Storage gen1 met behulp van .NET SDK.

## <a name="prerequisites"></a>Vereisten
* **Visual Studio 2013 of hoger**. In de onderstaande instructies wordt gebruikgemaakt van Visual Studio 2019.

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Maak een Azure Active Directory systeem eigen toepassing**. U moet de stappen in de [verificatie van eind gebruikers hebben voltooid met data Lake Storage gen1 met behulp van Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

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

5. **Programma openen. cs**
6. Vervang de instructies using door de volgende regels:

    ```csharp
    using System;
    using System.IO;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Collections.Generic;
            
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```     

## <a name="end-user-authentication"></a>Verificatie van de eindgebruiker
Voeg dit fragment toe aan uw .NET-client toepassing. Vervang de waarden van de tijdelijke aanduiding door de waarden die zijn opgehaald uit een systeem eigen Azure AD-toepassing (vermeld als vereiste). Met dit code fragment kunt u uw toepassing **interactief** verifiëren met data Lake Storage gen1, wat betekent dat u wordt gevraagd uw Azure-referenties in te voeren.

Voor gebruiks gemak gebruikt het volgende fragment standaard waarden voor client-ID en omleidings-URI die geldig zijn voor een Azure-abonnement. In het volgende code fragment hoeft u alleen de waarde voor uw Tenant-ID op te geven. U kunt de Tenant-ID ophalen met behulp van de instructies in [de Tenant-id ophalen](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
    
- Vervang de functie main () door de volgende code:

    ```csharp
    private static void Main(string[] args)
    {
        //User login via interactive popup
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string MY_DOCUMENTS = System.Environment.GetFolderPath(System.Environment.SpecialFolder.MyDocuments);
        string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");
        var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
        var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
        var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
    }
    ```

Een aantal dingen die u moet weten over het voor gaande fragment:

* Het voor gaande fragment maakt gebruik van een hulp functie `GetTokenCache` en `GetCreds_User_Popup` . De code voor deze Help-functies is [hier beschikbaar op github](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* Om u te helpen de zelf studie sneller te volt ooien, gebruikt het fragment een systeem eigen toepassings-client-ID die standaard beschikbaar is voor alle Azure-abonnementen. U kunt **dit fragment dus in zijn huidige vorm in uw toepassing gebruiken**.
* Als u echter uw eigen Azure AD-domein- en toepassingsclient-id wilt gebruiken, moet u een systeemeigen Azure AD-toepassing maken en vervolgens het Azure AD-tenant-ID, de client-ID en omleidings-URI gebruiken voor de toepassing die u hebt gemaakt. Zie [een Active Directory-toepassing maken voor verificatie door eind gebruikers met data Lake Storage gen1](data-lake-store-end-user-authenticate-using-active-directory.md) voor instructies.

  
## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u verificatie van eind gebruikers kunt gebruiken om te verifiëren met Azure Data Lake Storage Gen1 met behulp van .NET SDK. U kunt nu de volgende artikelen bekijken over het gebruik van de .NET SDK om met Azure Data Lake Storage Gen1 te werken.

* [Account beheer bewerkingen op Data Lake Storage Gen1 met behulp van .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Gegevens bewerkingen op Data Lake Storage Gen1 met behulp van .NET SDK](data-lake-store-data-operations-net-sdk.md)

