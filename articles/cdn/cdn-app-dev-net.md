---
title: Aan de slag met de Azure CDN-bibliotheek voor .NET | Microsoft Docs
description: Meer informatie over het schrijven van .NET-toepassingen voor het beheren van Azure CDN met behulp van Visual Studio.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 63cf4101-92e7-49dd-a155-a90e54a792ca
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: a812704c42a4da5ddf89fe6c5ba0c9a684047f75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88919485"
---
# <a name="get-started-with-the-azure-cdn-library-for-net"></a>Aan de slag met de Azure CDN-bibliotheek voor .NET
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
>
>

U kunt de [Azure CDN-bibliotheek voor .net](/dotnet/api/overview/azure/cdn) gebruiken om het maken en beheren van CDN-profielen en-eind punten te automatiseren.  In deze zelf studie wordt uitgelegd hoe u een eenvoudige .NET-console toepassing maakt die verschillende van de beschik bare bewerkingen toont.  Deze zelf studie is niet bedoeld om alle aspecten van de Azure CDN-bibliotheek voor .NET in detail te beschrijven.

U hebt Visual Studio 2015 nodig om deze zelf studie te volt ooien.  [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) kan gratis worden gedownload.

> [!TIP]
> Het [voltooide project van deze zelf studie](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) is beschikbaar voor downloaden op MSDN.
>
>

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Uw project maken en Nuget-pakketten toevoegen
Nu we een resource groep voor onze CDN-profielen hebben gemaakt en u de machtiging van de Azure AD-toepassing hebt gegeven voor het beheren van CDN-profielen en eind punten binnen die groep, kunnen we de toepassing gaan maken.

Klik in Visual Studio 2015 op **bestand**, **Nieuw**, **project...** om het dialoog venster Nieuw project te openen.  Vouw **Visual C#** uit en selecteer vervolgens **Windows** in het deel venster aan de linkerkant.  Klik op **console toepassing** in het middelste deel venster.  Geef uw project een naam en klik vervolgens op **OK**.

![Nieuw project](./media/cdn-app-dev-net/cdn-new-project.png)

Ons project gaat gebruikmaken van enkele Azure-bibliotheken die zijn opgenomen in Nuget-pakketten.  Laten we deze aan het project toevoegen.

1. Klik in het menu **extra** op **Nuget package manager** en vervolgens op **Package Manager console**.

    ![Nuget-pakketten beheren](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. Voer in de Package Manager-console de volgende opdracht uit om de **Active Directory Authentication Library (ADAL)** te installeren:

    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. Voer de volgende handelingen uit om de **Azure CDN-beheer bibliotheek** te installeren:

    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Instructies, constanten, hoofd methode en helper-methoden
Laten we de basis structuur van ons programma ontvangen.

1. Op het tabblad Program. CS vervangt u de `using` richt lijnen bovenaan met het volgende:

    ```csharp
    using System;
    using System.Collections.Generic;
    using Microsoft.Azure.Management.Cdn;
    using Microsoft.Azure.Management.Cdn.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
2. We moeten enkele constanten definiëren die door de methoden worden gebruikt.  Voeg in de `Program` -klasse, maar vóór de `Main` -methode, het volgende toe.  Zorg ervoor dat u de tijdelijke aanduidingen, inclusief de **&lt; punt haken &gt;**, vervangt door uw eigen waarden, indien nodig.

    ```csharp
    //Tenant app constants
    private const string clientID = "<YOUR CLIENT ID>";
    private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";

    //Application constants
    private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
    private const string profileName = "CdnConsoleApp";
    private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
    private const string resourceGroupName = "CdnConsoleTutorial";
    private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Definieer ook op class niveau deze twee variabelen.  We gebruiken deze later om te bepalen of ons profiel en eind punt al bestaan.

    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```
4. Vervang de- `Main` methode als volgt:

   ```csharp
   static void Main(string[] args)
   {
       //Get a token
       AuthenticationResult authResult = GetAccessToken();

       // Create CDN client
       CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
           { SubscriptionId = subscriptionId };

       ListProfilesAndEndpoints(cdn);

       // Create CDN Profile
       CreateCdnProfile(cdn);

       // Create CDN Endpoint
       CreateCdnEndpoint(cdn);

       Console.WriteLine();

       // Purge CDN Endpoint
       PromptPurgeCdnEndpoint(cdn);

       // Delete CDN Endpoint
       PromptDeleteCdnEndpoint(cdn);

       // Delete CDN Profile
       PromptDeleteCdnProfile(cdn);

       Console.WriteLine("Press Enter to end program.");
       Console.ReadLine();
   }
   ```
5. Een aantal van onze andere methoden om de gebruiker te vragen met ' Ja/nee ' vragen.  Voeg de volgende methode toe om het een beetje gemakkelijker te maken:

    ```csharp
    private static bool PromptUser(string Question)
    {
        Console.Write(Question + " (Y/N): ");
        var response = Console.ReadKey();
        Console.WriteLine();
        if (response.Key == ConsoleKey.Y)
        {
            return true;
        }
        else if (response.Key == ConsoleKey.N)
        {
            return false;
        }
        else
        {
            // They pressed something other than Y or N.  Let's ask them again.
            return PromptUser(Question);
        }
    }
    ```

Nu de basis structuur van ons programma is geschreven, moeten we de methoden maken die worden aangeroepen door de- `Main` methode.

## <a name="authentication"></a>Verificatie
Voordat we de Azure CDN-beheer bibliotheek kunnen gebruiken, moeten we onze service-principal verifiëren en een verificatie token verkrijgen.  Deze methode maakt gebruik van ADAL om het token op te halen.

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    ClientCredential credential = new ClientCredential(clientID, clientSecret);
    AuthenticationResult authResult =
        authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

    return authResult;
}
```

Als u afzonderlijke gebruikers verificatie gebruikt, ziet de `GetAccessToken` methode er iets anders uit.

> [!IMPORTANT]
> Gebruik dit code voorbeeld alleen als u ervoor kiest om afzonderlijke gebruikers verificatie te hebben in plaats van een service-principal.
>
>

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
        clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

    return authResult;
}
```

Zorg ervoor dat u vervangt door `<redirect URI>` de omleidings-URI die u hebt ingevoerd bij het registreren van de toepassing in azure AD.

## <a name="list-cdn-profiles-and-endpoints"></a>CDN-profielen en eind punten weer geven
U bent nu klaar om CDN-bewerkingen uit te voeren.  Het eerste dat onze methode heeft, is een lijst van alle profielen en eind punten in de resource groep, en als er een overeenkomst wordt gevonden voor het profiel en de namen van eind punten die zijn opgegeven in onze constanten, maakt u er later een notitie van.

```csharp
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
    // List all the CDN profiles in this resource group
    var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
    foreach (Profile p in profileList)
    {
        Console.WriteLine("CDN profile {0}", p.Name);
        if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
        {
            // Hey, that's the name of the CDN profile we want to create!
            profileAlreadyExists = true;
        }

        //List all the CDN endpoints on this CDN profile
        Console.WriteLine("Endpoints:");
        var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
        foreach (Endpoint e in endpointList)
        {
            Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
            if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
            {
                // The unique endpoint name already exists.
                endpointAlreadyExists = true;
            }
        }
        Console.WriteLine();
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>CDN-profielen en-eind punten maken
Vervolgens maken we een profiel.

```csharp
private static void CreateCdnProfile(CdnManagementClient cdn)
{
    if (profileAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating profile {0}.", profileName);
        ProfileCreateParameters profileParms =
            new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
        cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
    }
}
```

Zodra het profiel is gemaakt, maken we een eind punt.

```csharp
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
    if (endpointAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
        EndpointCreateParameters endpointParms =
            new EndpointCreateParameters()
            {
                Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
                IsHttpAllowed = true,
                IsHttpsAllowed = true,
                Location = resourceLocation
            };
        cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
    }
}
```

> [!NOTE]
> In het bovenstaande voor beeld wordt het eind punt een oorsprong met de naam *Contoso* met een hostnaam toegewezen `www.contoso.com` .  U moet dit wijzigen zodat deze verwijst naar de hostnaam van uw eigen oorsprong.
>
>

## <a name="purge-an-endpoint"></a>Een eind punt leegmaken
Ervan uitgaande dat het eind punt is gemaakt, kan een veelvoorkomende taak die we in ons programma willen uitvoeren, de inhoud in het eind punt verwijderen.

```csharp
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
    if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
    {
        Console.WriteLine("Purging endpoint. Please wait...");
        cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

> [!NOTE]
> In het bovenstaande voor beeld wordt met de teken reeks `/*` aangegeven dat ik alles wil verwijderen uit de hoofdmap van het pad van het eind punt.  Dit komt overeen met het controleren van **Alles opschonen** in het dialoog venster ' opschonen ' van de Azure Portal. In de `CreateCdnProfile` -methode maak ik ons profiel als **Azure CDN van een Verizon** -profiel met behulp van de code `Sku = new Sku(SkuName.StandardVerizon)` . Dit is dus geslaagd.  **Azure CDN van Akamai** profielen bieden echter geen ondersteuning voor het **opschonen van alles**, dus als ik een Akamai-profiel voor deze zelf studie gebruik, moet ik dan specifieke paden voor het leegmaken van de toepassing toevoegen.
>
>

## <a name="delete-cdn-profiles-and-endpoints"></a>CDN-profielen en eind punten verwijderen
Met de laatste methoden worden ons eind punt en profiel verwijderd.

```csharp
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
    {
        Console.WriteLine("Deleting endpoint. Please wait...");
        cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
    {
        Console.WriteLine("Deleting profile. Please wait...");
        cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

## <a name="running-the-program"></a>Het programma uitvoeren
We kunnen het programma nu compileren en uitvoeren door te klikken op de knop **starten** in Visual Studio.

![Programma wordt uitgevoerd](./media/cdn-app-dev-net/cdn-program-running-1.png)

Wanneer het programma de bovenstaande prompt bereikt, kunt u terugkeren naar de resource groep in de Azure Portal en zien dat het profiel is gemaakt.

![Gelukt!](./media/cdn-app-dev-net/cdn-success.png)

We kunnen vervolgens de prompts bevestigen om de rest van het programma uit te voeren.

![Programma volt ooien](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Volgende stappen
[Down load het voor beeld](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c)om het voltooide project in dit overzicht te bekijken.

Raadpleeg de [Naslag informatie op MSDN](/dotnet/api/overview/azure/cdn)voor aanvullende documentatie over de Azure CDN-beheer bibliotheek voor .net.

Uw CDN-resources beheren met [Power shell](cdn-manage-powershell.md).
