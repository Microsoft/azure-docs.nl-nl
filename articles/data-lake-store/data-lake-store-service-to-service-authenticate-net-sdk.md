---
title: .NET-service-naar-service-verificatie-Data Lake Storage Gen1
description: Meer informatie over het uitvoeren van service-to-service-verificatie met Azure Data Lake Storage Gen1 met behulp Azure Active Directory met behulp van .NET SDK
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 19b4ac619ec3e72c787efc8e9f043f42dbd8b09b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010296"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Service-naar-service verificatie met Azure Data Lake Storage Gen1 met behulp van .NET SDK
> [!div class="op_single_selector"]
> * [Java gebruiken](data-lake-store-service-to-service-authenticate-java.md)
> * [.NET SDK gebruiken](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Python gebruiken](data-lake-store-service-to-service-authenticate-python.md)
> * [REST-API gebruiken](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

In dit artikel leert u hoe u .NET SDK kunt gebruiken om service-naar-service-verificatie met Azure Data Lake Storage Gen1 uit te voeren. Voor verificatie door eind gebruikers met Data Lake Storage Gen1 met behulp van .NET SDK raadpleegt u [verificatie door eind gebruikers met data Lake Storage gen1 met behulp van .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Vereisten
* **Visual Studio 2013 of hoger**. In de onderstaande instructies wordt gebruikgemaakt van Visual Studio 2019.

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Een Azure Active Directory Web-toepassing maken**. U moet de stappen in [service-to-service-verificatie met data Lake Storage gen1 met behulp van Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md)hebben voltooid.

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

## <a name="service-to-service-authentication-with-client-secret"></a>Service-naar-service verificatie met client geheim
Voeg dit fragment toe aan uw .NET-client toepassing. Vervang de waarden van de tijdelijke aanduiding door de waarden die zijn opgehaald uit een Azure AD-webtoepassing (vermeld als een vereiste). Met dit code fragment kunt u uw toepassing **niet-interactief** verifiëren met data Lake Storage gen1 met behulp van het client geheim/-sleutel voor de Azure AD-webtoepassing.

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
    var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
    var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
}
```

In het voor gaande fragment wordt een hulp functie gebruikt `GetCreds_SPI_SecretKey` . De code voor deze Help functie is [hier beschikbaar op github](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey).

## <a name="service-to-service-authentication-with-certificate"></a>Service-naar-service verificatie met certificaat

Voeg dit fragment toe aan uw .NET-client toepassing. Vervang de waarden van de tijdelijke aanduiding door de waarden die zijn opgehaald uit een Azure AD-webtoepassing (vermeld als een vereiste). Met dit code fragment kunt u uw toepassing **niet-interactief** verifiëren met data Lake Storage gen1 met behulp van het certificaat voor een Azure AD-webtoepassing. Zie [Service-Principal maken met certificaten](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)voor instructies over het maken van een Azure AD-toepassing.

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    var cert = new X509Certificate2(@"d:\cert.pfx", "<certpassword>");
    var armCreds = GetCreds_SPI_Cert(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, cert);
    var adlCreds = GetCreds_SPI_Cert(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, cert);
}
```

In het voor gaande fragment wordt een hulp functie gebruikt `GetCreds_SPI_Cert` . De code voor deze Help functie is [hier beschikbaar op github](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert).

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u service-naar-service-verificatie kunt gebruiken om te verifiëren met Data Lake Storage Gen1 met behulp van .NET SDK. U kunt nu de volgende artikelen bekijken over het gebruik van de .NET SDK om met Data Lake Storage Gen1 te werken.

* [Account beheer bewerkingen op Data Lake Storage Gen1 met behulp van .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Gegevens bewerkingen op Data Lake Storage Gen1 met behulp van .NET SDK](data-lake-store-data-operations-net-sdk.md)
