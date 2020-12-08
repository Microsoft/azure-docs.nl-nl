---
title: 'Zelfstudie: Azure Key Vault gebruiken met een Azure-web-app in .NET'
description: In deze zelfstudie gaat u een Azure-web-app configureren in een ASP.NET Core-toepassing voor het lezen van een geheim in uw sleutelkluis.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 473ed1f14d77470e31c2f14665a12542a70a2a98
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512295"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-in-net"></a>Zelfstudie: Een beheerde identiteit gebruiken om Key Vault te verbinden met een Azure-web-app in .NET

[Azure Key Vault](./overview.md) biedt een manier om referenties en andere geheimen op te slaan met verbeterde beveiliging. Maar uw code moet worden geverifieerd bij Key Vault om deze op te halen. [Beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md) helpen bij het oplossen van dit probleem door Azure-services een automatisch beheerde identiteit in Azure Active Directory (Azure AD) te geven. U kunt deze identiteit gebruiken voor verificatie bij alle services die ondersteuning bieden voor Azure AD-verificatie, inclusief Key Vault, zonder dat u referenties in uw code hoeft weer te geven.

In deze zelfstudie wordt een beheerde identiteit gebruikt voor het verifiëren van een Azure-web-app met een Azure Key Vault. U gebruikt de [Azure Key Vault-clientbibliotheek voor geheimen voor .NET](/dotnet/api/overview/azure/key-vault) en de [Azure CLI](/cli/azure/get-started-with-azure-cli). Dezelfde basisprincipes zijn van toepassing wanneer u de ontwikkeltaal van uw keuze, Azure PowerShell en/of Azure Portal gebruikt.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze quickstart te voltooien:

* Een Azure-abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* De [.NET Core 3.1 SDK of hoger](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Een [Git](https://www.git-scm.com/downloads)-installatie.
* De [Azure CLI](/cli/azure/install-azure-cli) of [Azure PowerShell](/powershell/azure/).
* [Azure Key Vault.](./overview.md) U kunt een sleutelkluis maken met [Azure Portal](quick-create-portal.md), de [Azure CLI](quick-create-cli.md) of [Azure PowerShell](quick-create-powershell.md).
* Een Key Vault-[geheim](../secrets/about-secrets.md). U kunt een geheim maken met behulp van [Azure Portal](../secrets/quick-create-portal.md), [PowerShell](../secrets/quick-create-powershell.md) of de [Azure CLI](../secrets/quick-create-cli.md).

## <a name="create-a-net-core-app"></a>Een .NET Core-app maken
In deze stap stelt u het lokale .NET Core-project in.

Maak in een terminalvenster op uw computer een map met de naam `akvwebapp` en maak hiervan de huidige map:

```bash
mkdir akvwebapp
cd akvwebapp
```

Maak een .NET Core-app met behulp van de opdracht [dotnet new web](/dotnet/core/tools/dotnet-new):

```bash
dotnet new web
```

Voer de toepassing lokaal uit zodat u weet hoe deze eruit ziet wanneer u de toepassing implementeert naar Azure:

```bash
dotnet run
```

Ga in een webbrowser naar de app in `http://localhost:5000`.

Het bericht 'Hallo wereld!' uit de voorbeeld-app wordt weergegeven op de pagina.

## <a name="deploy-the-app-to-azure"></a>De app implementeren in Azure

In deze stap implementeert u de .NET Core-app met Azure App Service met behulp van lokale Git. Zie [Een ASP.NET Core-web-app maken in Azure](../../app-service/quickstart-dotnetcore.md) voor meer informatie over het maken en implementeren van toepassingen.

### <a name="configure-the-local-git-deployment"></a>De lokale Git-implementatie configureren

Selecteer in het terminalvenster **CTRL + C** om de webserver te sluiten.  Initialiseer een Git-opslagplaats voor het .NET Core-project:

```bash
git init
git add .
git commit -m "first commit"
```

U kunt FTP en lokale Git gebruiken om een Azure-web-app te implementeren met behulp van een *implementatiegebruiker*. Nadat u deze implementatiegebruiker hebt gemaakt, kunt u deze voor al uw Azure-implementaties gebruiken. Uw gebruikersnaam en wachtwoord voor implementatie op accountniveau verschillen van de referenties voor uw Azure-abonnement. 

Als u de implementatiegebruiker wilt configureren, voert u de opdracht [az webapp deployment user set](/cli/azure/webapp/deployment/user?#az-webapp-deployment-user-set) uit. Kies een gebruikersnaam en wachtwoord die voldoen aan de volgende richtlijnen: 

- De gebruikersnaam moet binnen Azure uniek zijn. Voor lokale Git-pushes mag deze geen apenstaartje (@) bevatten. 
- Het wachtwoord moet ten minste acht tekens lang zijn en minimaal twee van de volgende drie typen elementen bevatten: letters, cijfers en symbolen. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

De JSON-uitvoer toont het wachtwoord als `null`. Als er een `'Conflict'. Details: 409`-fout optreedt, wijzigt u de gebruikersnaam. Als er een `'Bad Request'. Details: 400`-fout optreedt, kiest u een sterker wachtwoord. 

Noteer uw gebruikersnaam en wachtwoord zodat u deze kunt gebruiken bij het implementeren van uw web-apps.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een logische container waarin u uw Azure-resources implementeert en beheert. Maak een resourcegroep om zowel uw sleutelkluis als uw web-app in te bewaren met behulp van de opdracht [az group create](/cli/azure/group?#az-group-create):

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

### <a name="create-an-app-service-plan"></a>Een App Service-plan maken

Maak een [App Service-plan](../../app-service/overview-hosting-plans.md) met behulp van de Azure CLI-opdracht [az appservice plan create](/cli/azure/appservice/plan). In het volgende voorbeeld wordt een App Service-plan gemaakt met de naam `myAppServicePlan` en de prijscategorie `FREE`:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Wanneer het App Service-plan wordt gemaakt, toont de Azure CLI informatie die lijkt op wat u hier ziet:

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>

Zie [Een App Service-plan beheren in Azure](../../app-service/app-service-plan-manage.md) voor meer informatie.

### <a name="create-a-web-app"></a>Een webtoepassing maken

Maak een [Azure-web-app](../../app-service/overview.md) in het App Service-plan `myAppServicePlan`. 

> [!Important]
> Net als een sleutelkluis moet een Azure-web-app een unieke naam hebben. Vervang in de volgende voorbeelden `<your-webapp-name>` door de naam van uw web-app.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

Wanneer de web-app is gemaakt, toont de Azure CLI soortgelijke uitvoer als wat u hier ziet:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>


De URL van de externe Git wordt weergegeven in de eigenschap `deploymentLocalGitUrl`, in de indeling `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git`. Sla deze URL op. U hebt deze later nodig.

Ga naar uw nieuwe app met behulp van de volgende opdracht. Vervang `<your-webapp-name>` door de naam van uw app.

```bash
https://<your-webapp-name>.azurewebsites.net
```

De standaardwebpagina voor een nieuwe Azure-web-app wordt weergeven.

### <a name="deploy-your-local-app"></a>Uw lokale app implementeren

Voeg, eenmaal terug in het lokale terminalvenster, een externe Azure-instantie toe aan uw lokale Git-opslagplaats. Vervang in de volgende opdracht `<deploymentLocalGitUrl-from-create-step>` door de URL van de externe Git die u hebt opgeslagen in de sectie [Een webtoepassing maken](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Push naar de externe Azure-instantie om uw app te implementeren met behulp van de volgende opdracht. Wanneer Git Credential Manager u om referenties vraagt, gebruikt u de referenties die u hebt gemaakt in de sectie [De lokale Git-implementatie configureren](#configure-the-local-git-deployment).

```bash
git push azure master
```

Het uitvoeren van deze opdracht kan enkele minuten duren. Terwijl deze wordt uitgevoerd, wordt er informatie weergegeven die vergelijkbaar is met wat u hier ziet:
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

Ga naar (of vernieuw) de geïmplementeerde toepassing via uw webbrowser:

```bash
http://<your-webapp-name>.azurewebsites.net
```

Het bericht 'Hallo wereld!' dat u eerder hebt gezien toen u `http://localhost:5000` hebt bezocht, wordt weergegeven.
 
## <a name="configure-the-web-app-to-connect-to-key-vault"></a>De web-app configureren om verbinding te maken met Key Vault

In deze sectie configureert u de webtoegang tot de sleutelkluis en werkt u uw toepassingscode bij om een geheim uit de sleutelkluis op te halen.

### <a name="create-and-assign-a-managed-identity"></a>Een beheerde identiteit maken en toewijzen

In deze zelfstudie gebruiken we een [beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) voor het verifiëren van Key Vault. Met een beheerde identiteit worden toepassingsreferenties automatisch beheerd.

Voer in Azure CLI de opdracht [az webapp-identity assign](/cli/azure/webapp/identity?#az-webapp-identity-assign) uit om de identiteit voor de toepassing te maken:

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

Met deze opdracht wordt dit JSON-fragment geretourneerd:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Als u uw web-app toestemming wilt geven om bewerkingen voor **ophalen** en **weergeven** uit te voeren op uw sleutelkluis, geeft u de `principalId` door aan de Azure CLI-opdracht [az keyvault set-policy](/cli/azure/keyvault?#az-keyvault-set-policy):

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```

U kunt ook een toegangsbeleid toewijzen met behulp van [Azure Portal](./assign-access-policy-portal.md) of [PowerShell](./assign-access-policy-powershell.md).

### <a name="modify-the-app-to-access-your-key-vault"></a>De app wijzigen om toegang te krijgen tot uw sleutelkluis

In deze zelfstudie gebruikt u de [Azure Key Vault-clientbibliotheek voor geheimen](https://docs.microsoft.com/dotnet/api/overview/azure/security.keyvault.secrets-readme) voor demonstratiedoeleinden. U kunt ook de [Azure Key Vault-clientbibliotheek voor certificaten](https://docs.microsoft.com/dotnet/api/overview/azure/security.keyvault.certificates-readme) of de [Azure Key Vault-bibliotheek voor sleutels](https://docs.microsoft.com/dotnet/api/overview/azure/security.keyvault.keys-readme) gebruiken.

#### <a name="install-the-packages"></a>De pakketten installeren

Installeer vanuit het terminalvenster de pakketten voor de Azure Key Vault-clientbibliotheek voor geheimen en de Azure Identity-clientbibliotheek:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

#### <a name="update-the-code"></a>De code bijwerken

Zoek en open het bestand Startup.cs in uw akvwebapp-project. 

Voeg deze regels toe aan de koptekst:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Voeg de volgende regels toe vóór de `app.UseEndpoints`-aanroep, waarbij u de URI bijwerkt volgens de `vaultUri` van uw sleutelkluis. Deze code maakt gebruik van [DefaultAzureCredential ()](/dotnet/api/azure.identity.defaultazurecredential) om te verifiëren bij Key Vault, waarbij een token van de beheerde identiteit wordt gebruikt voor verificatie. Zie de [Gids voor ontwikkelaars](./developers-guide.md#authenticate-to-key-vault-in-code) voor meer informatie over het verifiëren bij Key Vault. De code gebruikt ook exponentieel uitstel voor nieuwe pogingen wanneer Key Vault wordt vertraagd. Zie [Azure Key Vault-beperkingsrichtlijnen](./overview-throttling.md) voor meer informatie over transactiebeperkingen van sleutelkluizen.

```csharp
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("<mySecret>");

string secretValue = secret.Value;
```

Werk de regel `await context.Response.WriteAsync("Hello World!");` bij zodat deze op deze regel lijkt:

```csharp
await context.Response.WriteAsync(secretValue);
```

Zorg ervoor dat u uw wijzigingen opslaat voordat u verdergaat met de volgende stap.

#### <a name="redeploy-your-web-app"></a>Uw web-app opnieuw implementeren

Nu u uw code hebt bijgewerkt, kunt u deze opnieuw implementeren in Azure met behulp van deze Git-opdrachten:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure master
```

## <a name="go-to-your-completed-web-app"></a>Ga naar uw voltooide web-app

```bash
http://<your-webapp-name>.azurewebsites.net
```

Waar eerder 'Hallo wereld' stond, zou nu de waarde van uw geheim moeten staan.

## <a name="next-steps"></a>Volgende stappen

- [Azure Key Vault gebruiken met toepassingen geïmplementeerd op een virtuele machine in .NET](./tutorial-net-virtual-machine.md)
- Meer informatie over [beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)
- Bekijk de [Gids voor ontwikkelaars](./developers-guide.md)
- [Veilige toegang tot een sleutelkluis](./secure-your-key-vault.md)
