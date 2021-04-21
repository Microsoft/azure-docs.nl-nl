---
title: 'Zelfstudie: Web-app krijgt toegang tot Microsoft Graph als de app | Azure'
description: In deze zelfstudie leert u hoe u toegang tot gegevens in Microsoft Graph krijgt met beheerde identiteiten.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 01/28/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1, devx-track-azurepowershell
ms.openlocfilehash: 5bb52799836b1975de9d936e04fb53987effb300
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832622"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-app"></a>Zelfstudie: Toegang tot Microsoft Graph krijgen vanuit een beveiligde app als de app

Ontdek hoe u toegang tot Microsoft Graph krijgt vanuit een web-app die wordt uitgevoerd in Azure App Service.

:::image type="content" alt-text="Diagram waarin de toegang tot Microsoft Graph wordt weergegeven." source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

U wilt Microsoft Graph aanroepen voor de web-app. Een veilige manier om uw web-app toegang tot gegevens te geven, is een [door het systeem toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) te gebruiken. Met een beheerde identiteit van Azure Active Directory kan App Services toegang tot resources krijgen via RBAC (op rollen gebaseerd toegangsbeheer), zonder dat daar app-referenties voor nodig zijn. Nadat een beheerde identiteit aan uw web-app is toegewezen, wordt er in Azure een certificaat gemaakt en gedistribueerd. U hoeft zich geen zorgen te maken over het beheren van geheimen of app-referenties.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een door het systeem toegewezen beheerde identiteit maken in een web-app
> * Microsoft Graph API-machtigingen toevoegen aan een beheerde identiteit
> * Microsoft Graph aanroepen vanuit een web-app met beheerde identiteiten

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Een webtoepassing die wordt uitgevoerd in Azure App Service waarvoor de [module App Service-verificatie/-autorisatie is ingeschakeld](scenario-secure-app-authentication-app-service.md).

## <a name="enable-managed-identity-on-app"></a>Een beheerde identiteit inschakelen voor een app

Als u uw web-app via Visual Studio maakt en publiceert, was de beheerde identiteit al voor u ingeschakeld in uw app. In uw app-service selecteert u **Identiteit** in het linkerdeelvenster en selecteert u vervolgens **Door het systeem toegewezen**. Verifieer dat **Status** is ingesteld op **Aan**. Als dat niet het geval is, selecteert u **Opslaan** en vervolgens **Ja** om de door het systeem toegewezen beheerde identiteit in te schakelen. Wanneer de beheerde identiteit is ingeschakeld, is de status ingesteld op **Aan** en is de object-id beschikbaar.

Noteer de **Object-id**-waarde. U hebt deze in de volgende stap nodig.

:::image type="content" alt-text="Schermopname van door het systeem toegewezen identiteit." source="./media/scenario-secure-app-access-microsoft-graph/create-system-assigned-identity.png":::

## <a name="grant-access-to-microsoft-graph"></a>Toegang tot Microsoft Graph verlenen

Voor toegang tot Microsoft Graph moet de beheerde identiteit de juiste machtigingen hebben voor de bewerking die deze wil uitvoeren. Er is momenteel geen optie om dergelijke machtigingen toe te wijzen via Azure Portal. Met het volgende script worden de aangevraagde Microsoft Graph API-machtigingen toegevoegd aan het service-principalobject van de beheerde identiteit.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD.

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$TenantID="<tenant-id>"
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp-20201102125811"

# Get the ID of the managed identity for the web app.
$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid

# Check the Microsoft Graph documentation for the permission you need for the operation.
$PermissionName = "User.Read.All"

Connect-AzureAD -TenantId $TenantID

# Get the service principal for Microsoft Graph.
# First result should be AppId 00000003-0000-0000-c000-000000000000
$GraphServicePrincipal = Get-AzureADServicePrincipal -SearchString "Microsoft Graph" | Select-Object -first 1

# Assign permissions to the managed identity service principal.
$AppRole = $GraphServicePrincipal.AppRoles | `
Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}

New-AzureAdServiceAppRoleAssignment -ObjectId $spID -PrincipalId $spID `
-ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az login

webAppName="SecureWebApp-20201106120003"

spId=$(az resource list -n $webAppName --query [*].identity.principalId --out tsv)

graphResourceId=$(az ad sp list --display-name "Microsoft Graph" --query [0].objectId --out tsv)

appRoleId=$(az ad sp list --display-name "Microsoft Graph" --query "[0].appRoles[?value=='User.Read.All' && contains(allowedMemberTypes, 'Application')].id" --output tsv)

uri=https://graph.microsoft.com/v1.0/servicePrincipals/$spId/appRoleAssignments

body="{'principalId':'$spId','resourceId':'$graphResourceId','appRoleId':'$appRoleId'}"

az rest --method post --uri $uri --body $body --headers "Content-Type=application/json"
```

---

Nadat u het script hebt uitgevoerd, kunt u in de [Azure-portal](https://portal.azure.com) verifiëren dat de aangevraagde API-machtigingen zijn toegewezen aan de beheerde identiteit.

Ga naar **Azure Active Directory** en selecteer **Bedrijfstoepassingen**. Op dit venster worden alle service-principals in uw tenant weergegeven. Selecteer in **Alle toepassingen** de service-principal voor de beheerde identiteit. 

Als u deze zelfstudie volgt, zijn er twee service-principals met dezelfde weergavenaam (bijvoorbeeld SecureWebApp2020094113531). De service-principal met een **Startpagina-URL** vertegenwoordigt de web-app in uw tenant. De service-principal zonder een **Startpagina-URL** vertegenwoordigt de door het systeem toegewezen beheerde identiteit voor uw web-app. De **Object-id** voor de beheerde identiteit komt overeen met de object-id van de beheerde identiteit die u eerder hebt gemaakt.

Selecteer de service-principal voor de beheerde identiteit.

:::image type="content" alt-text="Schermopname van de optie Alle toepassingen." source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-all-applications.png":::

In **Overzicht** selecteert u **Machtigingen**. U ziet dan de toegevoegde machtigingen voor Microsoft Graph.

:::image type="content" alt-text="Schermopname van het venster Machtigingen." source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-permissions.png":::

## <a name="call-microsoft-graph-net"></a>Microsoft Graph (.NET) aanroepen

De klasse [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) wordt gebruikt om een tokenreferentie voor uw code op te halen om aanvragen voor Microsoft Graph te autoriseren. Maak een exemplaar van de klasse [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential), die gebruikmaakt van de beheerde identiteit om tokens op te halen en aan de serviceclient te koppelen. Met het volgende codevoorbeeld wordt de geverifieerde tokenreferentie opgehaald en gebruikt om een serviceclientobject te maken, waarmee de gebruikers in de groep worden opgehaald.

Zie het [voorbeeld op GitHub](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/3-WebApp-graphapi-managed-identity) als u deze code wilt bekijken als onderdeel van een voorbeeldtoepassing.

### <a name="install-the-microsoftidentitywebmicrosoftgraph-client-library-package"></a>Installeer het clientbibliotheekpakket Microsoft.Identity.Web.MicrosoftGraph

Installeer het [Microsoft.Identity.Web.MicrosoftGraph NuGet-pakket](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) in uw project met behulp van de .NET Core-opdrachtregelinterface of de Pakketbeheer-console in Visual Studio.

# <a name="command-line"></a>[Opdrachtregel](#tab/command-line)

Open een opdrachtregel en ga naar de map die uw projectbestand bevat.

Voer de installatieopdrachten uit.

```dotnetcli
dotnet add package Microsoft.Identity.Web.MicrosoftGraph
```

# <a name="package-manager"></a>[Package Manager](#tab/package-manager)

Open het project/de oplossing in Visual Studio en open de console met de opdracht **Hulpprogramma's** > **NuGet Package Manager** > **Package Manager Console**.

Voer de installatieopdrachten uit.
```powershell
Install-Package Microsoft.Identity.Web.MicrosoftGraph
```

---

### <a name="example"></a>Voorbeeld

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Azure.Identity;
using Microsoft.Graph.Core;
using System.Net.Http.Headers;

...

public IList<MSGraphUser> Users { get; set; }

public async Task OnGetAsync()
{
    // Create the Microsoft Graph service client with a DefaultAzureCredential class, which gets an access token by using the available Managed Identity.
    var credential = new DefaultAzureCredential();
    var token = credential.GetToken(
        new Azure.Core.TokenRequestContext(
            new[] { "https://graph.microsoft.com/.default" }));

    var accessToken = token.Token;
    var graphServiceClient = new GraphServiceClient(
        new DelegateAuthenticationProvider((requestMessage) =>
        {
            requestMessage
            .Headers
            .Authorization = new AuthenticationHeaderValue("bearer", accessToken);

            return Task.CompletedTask;
        }));

    List<MSGraphUser> msGraphUsers = new List<MSGraphUser>();
    try
    {
        var users =await graphServiceClient.Users.Request().GetAsync();
        foreach(var u in users)
        {
            MSGraphUser user = new MSGraphUser();
            user.userPrincipalName = u.UserPrincipalName;
            user.displayName = u.DisplayName;
            user.mail = u.Mail;
            user.jobTitle = u.JobTitle;

            msGraphUsers.Add(user);
        }
    }
    catch(Exception ex)
    {
        string msg = ex.Message;
    }

    Users = msGraphUsers;
}
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met deze zelfstudie en de web-app of bijbehorende resources niet meer nodig hebt, kunt u [de gemaakte resources opschonen](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
>
> * Een door het systeem toegewezen beheerde identiteit maken in een web-app
> * Microsoft Graph API-machtigingen toevoegen aan een beheerde identiteit
> * Microsoft Graph aanroepen vanuit een web-app met beheerde identiteiten

Ontdek hoe u een [.NET Core-app](tutorial-dotnetcore-sqldb-app.md), [Python-app](tutorial-python-postgresql-app.md), [Java-app](tutorial-java-spring-cosmosdb.md) of [Node.js-app](tutorial-nodejs-mongodb-app.md) verbindt met een database.
