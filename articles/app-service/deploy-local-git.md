---
title: Implementeren vanuit lokale Git-opslag plaats
description: Meer informatie over het inschakelen van lokale Git-implementatie naar Azure App Service. Een van de eenvoudigste manieren om code te implementeren vanaf uw lokale computer.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 02/16/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 5dd6183bf88c167adb2f084c319cd90b94351dfb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100560488"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Lokale Git-implementatie in Azure App Service

In deze hand leiding wordt uitgelegd hoe u uw app kunt implementeren in [Azure app service](overview.md) vanuit een Git-opslag plaats op uw lokale computer.

## <a name="prerequisites"></a>Vereisten

Volg de stappen in deze hand leiding:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Installeer Git](https://www.git-scm.com/downloads).

- Een lokale Git-opslag plaats hebben met code die u wilt implementeren. Als u een voor beeld van een opslag plaats wilt downloaden, voert u de volgende opdracht uit in het lokale terminal venster:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-a-deployment-user"></a>Een implementatiegebruiker configureren

Zie [implementatie referenties configureren voor Azure app service](deploy-configure-credentials.md). U kunt referenties voor een gebruikers bereik of referenties voor toepassings bereik gebruiken.

## <a name="create-a-git-enabled-app"></a>Een app waarvoor een Git is ingeschakeld maken

Als u al een App Service-app hebt en lokale Git-implementatie wilt configureren, raadpleegt u in plaats daarvan [een bestaande app configureren](#configure-an-existing-app) .

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Voer uit [`az webapp create`](/cli/azure/webapp#az_webapp_create) met de `--deployment-local-git` optie. Bijvoorbeeld:

```azurecli-interactive
az webapp create --resource-group <group-name> --plan <plan-name> --name <app-name> --runtime "<runtime-flag>" --deployment-local-git
```

De uitvoer bevat een URL zoals: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Gebruik deze URL om uw app te implementeren in de volgende stap.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Voer [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) uit vanuit de hoofdmap van uw Git-opslag plaats. Bijvoorbeeld:

```azurepowershell-interactive
New-AzWebApp -Name <app-name>
```

Wanneer u deze cmdlet uitvoert vanuit een map die een Git-opslag plaats is, wordt automatisch een Git-extern naar uw App Service-app gemaakt, met de naam `azure` .

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

In de portal moet u eerst een app maken en vervolgens de implementatie configureren. Zie [een bestaande app configureren](#configure-an-existing-app).

-----

## <a name="configure-an-existing-app"></a>Een bestaande app configureren

Als u nog geen app hebt gemaakt, raadpleegt u in plaats daarvan [een app met git-functionaliteit maken](#create-a-git-enabled-app) .

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Run [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-local-git) . Bijvoorbeeld:

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

De uitvoer bevat een URL zoals: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Gebruik deze URL om uw app te implementeren in de volgende stap.

> [!TIP]
> Deze URL bevat de gebruikers naam voor de implementatie van het bereik. Als u wilt, kunt u in plaats daarvan [de referenties voor het toepassings bereik gebruiken](deploy-configure-credentials.md#appscope) . 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Stel `scmType` uw app in door de cmdlet [set-AzResource](/powershell/module/az.resources/set-azresource) uit te voeren.

```powershell-interactive
$PropertiesObject = @{
    scmType = "LocalGit";
}

Set-AzResource -PropertyObject $PropertiesObject -ResourceGroupName <group-name> `
-ResourceType Microsoft.Web/sites/config -ResourceName <app-name>/web `
-ApiVersion 2015-08-01 -Force
```

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

1. Ga in het [Azure Portal](https://portal.azure.com)naar de beheer pagina van uw app.

1. Selecteer in het menu links de instellingen van het **implementatie centrum**  >  . Selecteer **lokale Git** in **bron** en klik vervolgens op **Opslaan**.

    ![Laat zien hoe u lokale Git-implementatie kunt inschakelen voor App Service in het Azure Portal](./media/deploy-local-git/enable-portal.png)

1. Kopieer in de sectie Local Git de **git clone-URI** voor later. Deze URI bevat geen referenties.

-----

## <a name="deploy-the-web-app"></a>De web-app implementeren

1. In een lokaal Terminal venster, wijzigt u de map in de hoofdmap van uw Git-opslag plaats en voegt u een Git-extern toe met behulp van de URL die u hebt ontvangen van uw app. Als uw gekozen methode geen URL geeft, gebruikt u `https://<app-name>.scm.azurewebsites.net/<app-name>.git` de naam van uw app in `<app-name>` .
   
   ```bash
   git remote add azure <url>
   ```

    > [!NOTE]
    > Als u [een app met git hebt gemaakt in Power shell met New-AzWebApp](#create-a-git-enabled-app), is de externe voor u al gemaakt.
   
1. Pushen naar de externe Azure met `git push azure master` . 
   
1. Voer in het venster **Git-referentie beheer** uw [referenties voor gebruikers bereik of toepassings bereik](#configure-a-deployment-user)in, niet uw aanmeldings referenties voor Azure.

    Als uw Git externe URL al de gebruikers naam en het wacht woord bevat, wordt u niet gevraagd. 
   
1. Controleer de uitvoer. U kunt runtime-specifieke automatisering zien, zoals MSBuild voor ASP.NET, `npm install` voor Node.js en `pip install` voor python. 
   
1. Blader naar uw app in de Azure Portal om te controleren of de inhoud is geïmplementeerd.

## <a name="troubleshoot-deployment"></a>Problemen met implementatie oplossen

Mogelijk worden de volgende veelvoorkomende fout berichten weer geven wanneer u Git gebruikt om te publiceren naar een App Service-app in Azure:

|Bericht|Oorzaak|Oplossing
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|De app is niet actief.|Start de app in het Azure Portal. Git-implementatie is niet beschikbaar wanneer de web-app is gestopt.|
|`Couldn't resolve host 'hostname'`|De adres gegevens voor de externe Azure-computer zijn onjuist.|Gebruik de `git remote -v` opdracht om alle externe-en de bijbehorende URL weer te geven. Controleer of de URL voor de externe Azure juist is. Als dat nodig is, kunt u deze extern verwijderen en opnieuw maken met de juiste URL.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|U hebt geen vertakking opgegeven tijdens `git push` of u hebt geen waarde ingesteld `push.default` in `.gitconfig` .|Voer `git push` opnieuw uit en geef de hoofd vertakking op: `git push azure main` .|
|`src refspec [branchname] does not match any.`|U probeert te pushen naar een andere vertakking dan Main op de externe Azure.|Voer `git push` opnieuw uit en geef de hoofd vertakking op: `git push azure main` .|
|`RPC failed; result=22, HTTP code = 5xx.`|Deze fout kan optreden als u probeert een grote Git-opslag plaats via HTTPS te pushen.|Wijzig de Git-configuratie op de lokale computer zodat deze `postBuffer` groter wordt. Bijvoorbeeld: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|U hebt een Node.js-app geïmplementeerd met een _package.jsin_ een bestand dat aanvullende vereiste modules bevat.|Bekijk de `npm ERR!` fout berichten vóór deze fout voor meer context over de fout. Hieronder vindt u de bekende oorzaken van deze fout en de bijbehorende `npm ERR!` berichten:<br /><br />**Onjuist gevormd package.jsbestand**: `npm ERR! Couldn't read dependencies.`<br /><br />**Systeem eigen module heeft geen binaire distributie voor Windows**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />of <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Aanvullende bronnen

- [App Service build server (project kudu-documentatie)](https://github.com/projectkudu/kudu/wiki)
- [Continue implementatie naar Azure App Service](deploy-continuous-deployment.md)
- [Voor beeld: een web-app maken en code implementeren vanuit een lokale Git-opslag plaats (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Voor beeld: een web-app maken en code implementeren vanuit een lokale Git-opslag plaats (Power shell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
