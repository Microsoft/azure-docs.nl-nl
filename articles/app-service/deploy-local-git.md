---
title: Implementeren vanuit lokale Git-repo
description: Meer informatie over het inschakelen van lokale Git-implementatie Azure App Service. Een van de eenvoudigste manieren om code te implementeren vanaf uw lokale computer.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 02/16/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: e0dc9093503cab92a71517a21a8788814d16cbbe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772861"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Lokale Git-implementatie in Azure App Service

In deze handleiding ziet u hoe u uw app implementeert naar [Azure App Service](overview.md) vanuit een Git-opslagplaats op uw lokale computer.

## <a name="prerequisites"></a>Vereisten

Volg de stappen in deze handleiding:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Installeer Git](https://www.git-scm.com/downloads).

- U hebt een lokale Git-opslagplaats met code die u wilt implementeren. Voer de volgende opdracht uit in het lokale terminalvenster om een voorbeeldopslagplaats te downloaden:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-a-deployment-user"></a>Een implementatiegebruiker configureren

Zie [Implementatiereferenties configureren voor Azure App Service.](deploy-configure-credentials.md) U kunt referenties voor het gebruikersbereik of referenties voor het toepassingsbereik gebruiken.

## <a name="create-a-git-enabled-app"></a>Een git-app maken

Zie Een bestaande app configureren als u App Service app hebt en er lokale Git-implementatie [voor wilt](#configure-an-existing-app) configureren.

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Voer [`az webapp create`](/cli/azure/webapp#az_webapp_create) uit met de optie `--deployment-local-git` . Bijvoorbeeld:

```azurecli-interactive
az webapp create --resource-group <group-name> --plan <plan-name> --name <app-name> --runtime "<runtime-flag>" --deployment-local-git
```

De uitvoer bevat een URL zoals: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Gebruik deze URL om uw app in de volgende stap te implementeren.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Voer [New-AzWebApp uit](/powershell/module/az.websites/new-azwebapp) vanuit de hoofdmap van uw Git-opslagplaats. Bijvoorbeeld:

```azurepowershell-interactive
New-AzWebApp -Name <app-name>
```

Wanneer u deze cmdlet vanuit een map die een Git-opslagplaats is, wordt automatisch een externe Git gemaakt voor uw App Service-app met de naam `azure` .

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

In de portal moet u eerst een app maken en vervolgens de implementatie voor de app configureren. Zie [Een bestaande app configureren.](#configure-an-existing-app)

-----

## <a name="configure-an-existing-app"></a>Een bestaande app configureren

Als u nog geen app hebt gemaakt, bekijkt u In plaats [daarvan Een git-app maken.](#create-a-git-enabled-app)

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Voer [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_local_git) uit. Bijvoorbeeld:

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

De uitvoer bevat een URL zoals: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Gebruik deze URL om uw app in de volgende stap te implementeren.

> [!TIP]
> Deze URL bevat de gebruikersnaam voor de implementatie binnen het gebruikersbereik. Als u wilt, kunt u in plaats daarvan de referenties [voor het toepassingsbereik](deploy-configure-credentials.md#appscope) gebruiken. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Stel de `scmType` van uw app in door de cmdlet [Set-AzResource](/powershell/module/az.resources/set-azresource) uit te stellen.

```powershell-interactive
$PropertiesObject = @{
    scmType = "LocalGit";
}

Set-AzResource -PropertyObject $PropertiesObject -ResourceGroupName <group-name> `
-ResourceType Microsoft.Web/sites/config -ResourceName <app-name>/web `
-ApiVersion 2015-08-01 -Force
```

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

1. Ga in [Azure Portal](https://portal.azure.com)naar de beheerpagina van uw app.

1. Selecteer in het menu links **Instellingen voor**  >  **implementatiecentrum.** Selecteer **Lokale Git** in **Bron** en klik vervolgens op **Opslaan.**

    ![Laat zien hoe u lokale Git-implementatie voor App Service in de Azure Portal](./media/deploy-local-git/enable-portal.png)

1. Kopieer in de sectie Lokale Git de **Git Clone-URI voor** later gebruik. Deze URI bevat geen referenties.

-----

## <a name="deploy-the-web-app"></a>De web-app implementeren

1. Wijzig in een lokaal terminalvenster de map in de hoofdmap van uw Git-opslagplaats en voeg een externe Git-opslagplaats toe met behulp van de URL die u hebt van uw app. Als de gekozen methode u geen URL geeft, gebruikt u `https://<app-name>.scm.azurewebsites.net/<app-name>.git` met de naam van uw app in `<app-name>` .
   
   ```bash
   git remote add azure <url>
   ```

    > [!NOTE]
    > Als u [een app met Git-ondersteuning hebt gemaakt in PowerShell met behulp van New-AzWebApp,](#create-a-git-enabled-app)is de externe app al voor u gemaakt.
   
1. Push naar de externe Azure-verbinding met `git push azure master` . 
   
1. Voer in **Aanmeldingsgegevensbeheer** Git-account uw referenties voor gebruikersbereik of [toepassingsbereik](#configure-a-deployment-user)in, niet uw Azure-aanmeldingsreferenties.

    Als uw externe Git-URL al de gebruikersnaam en het wachtwoord bevat, wordt u hier niet om gevraagd. 
   
1. Controleer de uitvoer. Mogelijk ziet u runtimespecifieke automatisering, zoals MSBuild voor ASP.NET, Node.js `npm install` en `pip install` voor Python. 
   
1. Blader naar uw app in Azure Portal om te controleren of de inhoud is geïmplementeerd.

## <a name="troubleshoot-deployment"></a>Problemen met de implementatie oplossen

Mogelijk ziet u de volgende veelvoorkomende foutberichten wanneer u Git gebruikt om te publiceren naar een App Service-app in Azure:

|Bericht|Oorzaak|Oplossing
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|De app is niet actief.|Start de app in de Azure Portal. Git-implementatie is niet beschikbaar wanneer de web-app wordt gestopt.|
|`Couldn't resolve host 'hostname'`|De adresgegevens voor de externe azure-locatie zijn onjuist.|Gebruik de `git remote -v` opdracht om alle remotes weer te geven, samen met de bijbehorende URL. Controleer of de URL voor de externe Azure-locatie juist is. Verwijder deze externe locatie zo nodig en maak deze opnieuw met behulp van de juiste URL.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|U hebt geen vertakking opgegeven tijdens `git push` of u hebt de waarde niet ingesteld in `push.default` `.gitconfig` .|Voer `git push` opnieuw uit en geef de volgende main branch: `git push azure main` .|
|`Error - Changes committed to remote repository but deployment to website failed.`|U hebt een lokale vertakking naar 'azure' ge pusht die niet overeen komt met de app-implementatietak.|Controleer of current branch `master` is. Als u de standaard branch wilt wijzigen, gebruikt u `DEPLOYMENT_BRANCH` de toepassingsinstelling.|
|`src refspec [branchname] does not match any.`|U hebt geprobeerd te pushen naar een andere vertakking dan main op de externe azure-locatie.|Voer `git push` opnieuw uit en geef de volgende main branch: `git push azure main` .|
|`RPC failed; result=22, HTTP code = 5xx.`|Deze fout kan zich voor doen als u probeert een grote Git-opslagplaats via HTTPS te pushen.|Wijzig de git-configuratie op de lokale computer om de grotere `postBuffer` te maken. Bijvoorbeeld: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|U hebt een Node.js geïmplementeerd met eenpackage.js _in_ een bestand dat aanvullende vereiste modules specificeert.|Bekijk de `npm ERR!` foutberichten vóór deze fout voor meer context over de fout. Hier volgen de bekende oorzaken van deze fout en de bijbehorende `npm ERR!` berichten:<br /><br />**Misvormde package.jsin het bestand**: `npm ERR! Couldn't read dependencies.`<br /><br />**Systeemeigen module heeft geen binaire distributie voor Windows:**<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />of <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Aanvullende bronnen

- [App Service buildserver (Documentatie voor Project Kudu)](https://github.com/projectkudu/kudu/wiki)
- [Continue implementatie naar Azure App Service](deploy-continuous-deployment.md)
- [Voorbeeld: Een web-app maken en code implementeren vanuit een lokale Git-opslagplaats (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Voorbeeld: Een web-app maken en code implementeren vanuit een lokale Git-opslagplaats (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
