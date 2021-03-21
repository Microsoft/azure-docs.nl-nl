---
title: Azure AD-verificatie gebruiken om toegang te krijgen tot Azure Media Services-API met .NET | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u Azure Active Directory (Azure AD)-verificatie gebruikt voor toegang tot Azure Media Services-API (AMS) met .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: fd19bce05d18eb8f9b8fd3806082caeebe98bdad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013989"
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Azure AD-verificatie gebruiken om toegang te krijgen tot Azure Media Services-API met .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Vanaf windowsazure. Media Services 4.0.0.4 ondersteunt Azure Media Services verificatie op basis van Azure Active Directory (Azure AD). In dit onderwerp wordt beschreven hoe u Azure AD-verificatie gebruikt voor toegang tot Azure Media Services-API met Microsoft .NET.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account. Zie [gratis proef versie van Azure](https://azure.microsoft.com/pricing/free-trial/)voor meer informatie.
- Een Media Services-account. Zie [een Azure Media Services-account maken met behulp van de Azure Portal](media-services-portal-create-account.md)voor meer informatie.
- Het meest recente [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) -pakket.
- Vertrouwd zijn met het onderwerp [toegang tot Azure Media Services-API met Azure AD-verificatie overzicht](media-services-use-aad-auth-to-access-ams-api.md).

Wanneer u Azure AD-verificatie gebruikt met Azure Media Services, kunt u op een van de volgende twee manieren verifiëren:

- **Gebruikers verificatie** verifieert een persoon die de app gebruikt om te communiceren met Azure Media Services-resources. De interactieve toepassing moet eerst de gebruiker vragen om referenties. Een voor beeld is een beheer console-app die wordt gebruikt door geautoriseerde gebruikers om de versleutelings taken of live streamen te controleren.
- **Service-Principal-verificatie** verifieert een service. Toepassingen die meestal gebruikmaken van deze verificatie methode zijn apps die daemon services, middelste laag Services of geplande taken uitvoeren, zoals web apps, functie-apps, Logic apps, Api's of micro Services.

>[!IMPORTANT]
>Azure media service ondersteunt momenteel een Azure Access Control Service-verificatie model. Access Control autorisatie zal echter worden afgeschaft op 22 juni 2018. U wordt aangeraden zo snel mogelijk naar een Azure Active Directory verificatie model te migreren.

## <a name="get-an-azure-ad-access-token"></a>Een Azure AD-toegangstoken ophalen

Om verbinding te maken met de Azure Media Services-API met Azure AD-verificatie, moet de client-app een Azure AD-toegangs token aanvragen. Wanneer u de Media Services .NET-client-SDK gebruikt, worden veel van de details over het verkrijgen van een Azure AD-toegangs token in de [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) -en [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs) -klassen genest en vereenvoudigd.

U hoeft bijvoorbeeld geen Azure AD-instantie, Media Services resource-URI of systeem eigen Azure AD-toepassings gegevens op te geven. Dit zijn bekende waarden die al zijn geconfigureerd door de klasse van de Azure AD-toegangs token provider.

Als u geen Azure media service .NET SDK gebruikt, raden we u aan de [Azure AD-verificatie bibliotheek](../../active-directory/azuread-dev/active-directory-authentication-libraries.md)te gebruiken. Zie [de Azure Portal gebruiken om toegang te krijgen tot Azure AD-verificatie-instellingen](media-services-portal-get-started-with-aad.md)voor de waarden van de para meters die u moet gebruiken met de Azure AD-verificatie bibliotheek.

U kunt ook de standaard implementatie van de **AzureAdTokenProvider** vervangen door uw eigen implementatie.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Azure Media Services .NET SDK installeren en configureren

>[!NOTE]
>Als u Azure AD-verificatie wilt gebruiken met de Media Services .NET SDK, hebt u het meest recente [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) -pakket nodig. Voeg ook een verwijzing naar de assembly **micro soft. Identity model. clients. ActiveDirectory** toe. Als u een bestaande app gebruikt, neemt u de **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll** -assembly op.

1. Maak in Visual Studio een nieuwe C#-consoletoepassing.
2. Gebruik het [windowsazure. Media Services](https://www.nuget.org/packages/windowsazure.mediaservices) NuGet-pakket om **Azure Media Services .NET SDK** te installeren.

    Als u referenties wilt toevoegen met behulp van NuGet, voert u de volgende stappen uit: Klik in **Solution Explorer** met de rechter muisknop op de project naam en selecteer vervolgens **NuGet-pakketten beheren**. Zoek vervolgens naar **windowsazure. Media Services** en selecteer **installeren**.

    -of-

    Voer de volgende opdracht uit in de **Package Manager-console** in Visual Studio.

    ```console
    Install-Package windowsazure.mediaservices -Version 4.0.0.4
    ```

3. Voeg **deze toe aan** de bron code.

    ```csharp
    using Microsoft.WindowsAzure.MediaServices.Client;
    ```

## <a name="use-user-authentication"></a>Gebruikers verificatie gebruiken

Als u verbinding wilt maken met de Azure media service-API met de optie voor gebruikers verificatie, moet de client-app een Azure AD-token aanvragen met behulp van de volgende para meters:

- Azure AD-Tenant eindpunt. De gegevens van de Tenant kunnen worden opgehaald uit de Azure Portal. Beweeg de muis aanwijzer over de aangemelde gebruiker in de rechter bovenhoek.
- Media Services resource-URI.
- Media Services (systeem eigen) client-ID van toepassing.
- Media Services (systeem eigen) URI voor omleiding van toepassing.

De waarden voor deze para meters vindt u in **AzureEnvironments. AzureCloudEnvironment**. De constante **AzureEnvironments. AzureCloudEnvironment** is een helper in de .NET SDK voor het verkrijgen van de juiste omgevings variabele-instellingen voor een openbaar Azure-Data Center.

Het bevat vooraf gedefinieerde omgevings instellingen voor het openen van Media Services in de open bare data centers. Voor soevereine of overheids Cloud regio's kunt u respectievelijk **AzureChinaCloudEnvironment**, **AzureUsGovernmentEnvironment** of **AzureGermanCloudEnvironment** gebruiken.

In het volgende code voorbeeld wordt een token gemaakt:

```csharp
var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
```

Als u wilt beginnen met het Program meren op Media Services, moet u een **cloudmediacontext maakt** -exemplaar maken dat de server context vertegenwoordigt. De **cloudmediacontext maakt** bevat verwijzingen naar belang rijke verzamelingen, waaronder taken, assets, bestanden, toegangs beleid en Locators.

U moet ook de resource- **URI voor media rest-Services** door geven aan de **cloudmediacontext maakt** -constructor. Als u de resource-URI voor media REST-services wilt ophalen, meldt u zich aan bij de Azure Portal, selecteert u uw Azure Media Services account, selecteert u **API-toegang** en selecteert **u verbinding maken met Azure Media Services met gebruikers verificatie**.

In het volgende code voorbeeld wordt een **cloudmediacontext maakt** -exemplaar gemaakt:

```csharp
CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
```

In het volgende voor beeld ziet u hoe u het Azure AD-token en de context maakt:

```csharp
namespace AzureADAuthSample
{
    class Program
    {
        static void Main(string[] args)
        {
            // Specify your Azure AD tenant domain, for example "microsoft.onmicrosoft.com".
            var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
            CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

            var assets = context.Assets;
            foreach (var a in assets)
            {
                Console.WriteLine(a.Name);
            }
        }
    }
}
```

>[!NOTE]
>Als u een uitzonde ring krijgt met de tekst "de externe server heeft een fout geretourneerd: (401) niet gemachtigd," Zie de sectie [toegangs beheer](media-services-use-aad-auth-to-access-ams-api.md#access-control) van toegang tot Azure Media Services-API met Azure AD-verificatie overzicht.

## <a name="use-service-principal-authentication"></a>Service-Principal-verificatie gebruiken

Als u verbinding wilt maken met de Azure Media Services-API met de optie Service-Principal, moet uw middelste app (Web API of Web Application) een Azure AD-token aanvragen met de volgende para meters:

- Azure AD-Tenant eindpunt. De gegevens van de Tenant kunnen worden opgehaald uit de Azure Portal. Beweeg de muis aanwijzer over de aangemelde gebruiker in de rechter bovenhoek.
- Media Services resource-URI.
- Azure AD-toepassings waarden: de **client-id** en het **client geheim**.

De waarden voor de **client-id** en **client Secret** -para meters vindt u in de Azure Portal. Zie [aan de slag met Azure AD-verificatie met behulp van de Azure Portal](media-services-portal-get-started-with-aad.md)voor meer informatie.

In het volgende code voorbeeld wordt een token gemaakt met behulp van de **AzureAdTokenCredentials** -constructor die **AzureAdClientSymmetricKey** als para meter neemt:

```csharp
var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                        new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"),
                        AzureEnvironments.AzureCloudEnvironment);

var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
```

U kunt ook de **AzureAdTokenCredentials** -constructor opgeven die **AzureAdClientCertificate** als para meter gebruikt.

Voor instructies over het maken en configureren van een certificaat in een formulier dat door Azure AD kan worden gebruikt, raadpleegt [u verificatie bij Azure AD in daemon-apps met certificaten-hand matige configuratie stappen](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2).

```csharp
var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                        new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"),
                        AzureEnvironments.AzureCloudEnvironment);
```

Als u wilt beginnen met het Program meren op Media Services, moet u een **cloudmediacontext maakt** -exemplaar maken dat de server context vertegenwoordigt. U moet ook de resource- **URI voor media rest-Services** door geven aan de **cloudmediacontext maakt** -constructor. U kunt de **resource-URI voor de media rest Services** -waarde ook ophalen uit het Azure Portal.

In het volgende code voorbeeld wordt een **cloudmediacontext maakt** -exemplaar gemaakt:

```csharp
CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
```

In het volgende voor beeld ziet u hoe u het Azure AD-token en de context maakt:

```csharp
namespace AzureADAuthSample
{
    class Program
    {
        static void Main(string[] args)
        {
            var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                                        new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"),
                                        AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
            CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

            var assets = context.Assets;
            foreach (var a in assets)
            {
                Console.WriteLine(a.Name);
            }

            Console.ReadLine();
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met [het uploaden van bestanden naar uw account](media-services-dotnet-upload-files.md).
