---
title: Wijzigingen die zijn aangebracht in een MVC-project wanneer u verbinding maakt met Azure AD
description: Beschrijft wat er gebeurt met uw MVC-project wanneer u verbinding maakt met Azure AD met behulp van Visual Studio Connected Services
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.openlocfilehash: ab72512b2e5d915dbd4b8a3d7d8446a5932eef9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88165444"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Wat is er gebeurd met mijn MVC-project (Visual Studio Azure Active Directory Connected service)?

> [!div class="op_single_selector"]
> - [Aan de slag](vs-active-directory-dotnet-getting-started.md)
> - [Wat is er gebeurd](vs-active-directory-dotnet-what-happened.md)

In dit artikel worden de exacte wijzigingen voor een ASP.NET MVC-project geïdentificeerd wanneer u de [Azure Active Directory verbonden service toevoegt met behulp van Visual Studio](vs-active-directory-add-connected-service.md).

Zie [aan](vs-active-directory-dotnet-getting-started.md)de slag voor meer informatie over het werken met de verbonden service.

## <a name="added-references"></a>Verwijzingen toegevoegd

Is van invloed op het project bestand (*. NET references) en `packages.config` (NuGet references).

| Type | Referentie |
| --- | --- |
| NET NuGet | Microsoft.IdentityModel.Protocol.Extensions |
| NET NuGet | Micro soft. Owin |
| NET NuGet | Microsoft.Owin.Host.SystemWeb |
| NET NuGet | Micro soft. Owin. Security |
| NET NuGet | Microsoft.Owin.Security.Cookies |
| NET NuGet | Microsoft.Owin.Security.OpenIdConnect |
| NET NuGet | Owin |
| .NET        | System. Identity model |
| NET NuGet | System. Identity model. tokens. JWT |
| .NET        | System.Runtime.Serialization |

Aanvullende verwijzingen als u de optie **Directory gegevens lezen** hebt geselecteerd:

| Type | Referentie |
| --- | --- |
| NET NuGet | EntityFramework |
| .NET        | EntityFramework. SqlServer (alleen Visual Studio 2015) |
| NET NuGet | Micro soft. Azure. ActiveDirectory. GraphClient |
| NET NuGet | Micro soft. data. EDM |
| NET NuGet | Micro soft. data. OData |
| NET NuGet | Micro soft. data. Services. client |
| NET NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Micro soft. Identity model. clients. ActiveDirectory. WindowsForms (alleen Visual Studio 2015) |
| NET NuGet | Systeem. ruimtelijk |

De volgende verwijzingen worden verwijderd (ASP.NET 4 projecten, net als in Visual Studio 2015):

| Type | Referentie |
| --- | --- |
| NET NuGet | Micro soft. AspNet. Identity. core |
| NET NuGet | Micro soft. AspNet. Identity. EntityFramework |
| NET NuGet | Micro soft. AspNet. Identity. Owin |

## <a name="project-file-changes"></a>Wijzigingen in het project bestand

- Stel de eigenschap `IISExpressSSLPort` in op een specifiek getal.
- Stel de eigenschap `WebProject_DirectoryAccessLevelKey` in op 0 of 1 als u de optie **Directory gegevens lezen** hebt geselecteerd.
- Stel de eigenschap `IISUrl` in op `https://localhost:<port>/` waar `<port>` de `IISExpressSSLPort` waarde overeenkomt.

## <a name="webconfig-or-appconfig-changes"></a>Wijzigingen web.config of app.config

- De volgende configuratie vermeldingen zijn toegevoegd:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- `<dependentAssembly>`Elementen toegevoegd onder het `<runtime><assemblyBinding>` knoop punt voor `System.IdentityModel.Tokens.Jwt` en `Microsoft.IdentityModel.Protocol.Extensions` .

Aanvullende wijzigingen als u de optie **Directory gegevens lezen** hebt geselecteerd:

- De volgende configuratie vermelding is toegevoegd onder `<appSettings>` :

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- De volgende elementen zijn toegevoegd onder `<configuration>` ; waarden voor het project-MDF-bestand en project-catalogus-ID variëren:

    ```xml
    <configSections>
      <!-- For more information on Entity Framework configuration, visit https://go.microsoft.com/fwlink/?LinkID=237468 -->
      <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>

    <connectionStrings>
      <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\<project-mdf-file>.mdf;Initial Catalog=<project-catalog-id>;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>

    <entityFramework>
      <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
        <parameters>
          <parameter value="mssqllocaldb" />
        </parameters>
      </defaultConnectionFactory>
      <providers>
        <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
      </providers>
    </entityFramework>
    ```

- `<dependentAssembly>`Elementen toegevoegd onder het `<runtime><assemblyBinding>` knoop punt voor `Microsoft.Data.Services.Client` , `Microsoft.Data.Edm` en `Microsoft.Data.OData` .

## <a name="code-changes-and-additions"></a>Code wijzigingen en toevoegingen

- Het kenmerk is toegevoegd `[Authorize]` aan `Controllers/HomeController.cs` en andere bestaande controllers.

- Er is een opstart klasse voor verificatie toegevoegd, `App_Start/Startup.Auth.cs` met een opstart logica voor Azure AD-verificatie. Als u de optie **Directory gegevens lezen** hebt geselecteerd, bevat dit bestand ook code voor het ontvangen van een OAuth-code en het uitwisselen van een toegangs token.

- Er is een controller klasse, `Controllers/AccountController.cs` , met en-methoden, toegevoegd `SignIn` `SignOut` .

- Er is een gedeeltelijke weer gave toegevoegd, `Views/Shared/_LoginPartial.cshtml` die een actie koppeling voor `SignIn` en bevat `SignOut` .

- Er is een gedeeltelijke weer gave toegevoegd, `Views/Account/SignoutCallback.cshtml` met HTML voor de gebruikers interface voor afmelden.

- De `Startup.Configuration` methode is bijgewerkt om een aanroep op te nemen `ConfigureAuth(app)` als de klasse al bestaat; anders is een `Startup` klasse toegevoegd die de methode aanroept.

- Toegevoegd `Connected Services/AzureAD/ConnectedService.json` (Visual studio 2017) of `Service References/Azure AD/ConnectedService.json` (visual studio 2015) met informatie die Visual Studio gebruikt om de toevoeging van de verbonden service bij te houden.

- Als u de optie **Directory gegevens lezen** hebt geselecteerd, toegevoegd `Models/ADALTokenCache.cs` en voor het ondersteunen van het opslaan van `Models/ApplicationDbContext.cs` tokens. Daarnaast hebt u een extra controller en weer gave toegevoegd om toegang te krijgen tot gebruikers profiel gegevens met behulp van Azure Graph Api's: `Controllers/UserProfileController.cs` , `Views/UserProfile/Index.cshtml` en `Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Back-up van bestanden (Visual Studio 2015)

Wanneer u de verbonden service toevoegt, maakt Visual Studio 2015 een back-up van gewijzigde en verwijderde bestanden. Alle betrokken bestanden worden opgeslagen in de map `Backup/AzureAD` . Visual Studio 2017 en hoger maakt geen back-ups.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Wijzigingen in azure

- Er is een Azure AD-toepassing gemaakt in het domein dat u hebt geselecteerd bij het toevoegen van de verbonden service.
- De app is bijgewerkt met de machtiging voor het **lezen van Directory gegevens** als deze optie is geselecteerd.

Meer [informatie over Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Volgende stappen

- [Verificatie scenario's voor Azure Active Directory](./authentication-vs-authorization.md)
- [Aanmelding met Microsoft toevoegen aan een ASP.NET-web-app](quickstart-v2-aspnet-webapp.md)