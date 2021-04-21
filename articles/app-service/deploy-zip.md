---
title: Code implementeren met een ZIP- of WAR-bestand
description: Meer informatie over het implementeren van uw app Azure App Service met een ZIP-bestand (of een WAR-bestand voor Java-ontwikkelaars).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 9f59576ea66b72a492e1e6c665a51258861842dd
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833012"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Uw app implementeren in Azure App Service met een ZIP- of WAR-bestand

In dit artikel wordt beschreven hoe u een ZIP-bestand of WAR-bestand gebruikt om uw web-app te implementeren in [Azure App Service](overview.md). 

Deze zip-bestandsimplementatie maakt gebruik van dezelfde Kudu-service die continue implementaties op basis van integratie mogelijk maakt. Kudu ondersteunt de volgende functionaliteit voor de implementatie van ZIP-bestanden: 

- Het verwijderen van bestanden die zijn overgenomen van een eerdere implementatie.
- Optie om het standaard buildproces in te stellen, waaronder pakketherstel.
- Implementatieaanpassing, inclusief het uitvoeren van implementatiescripts.  
- Implementatielogboeken. 
- Een bestandsgroottelimiet van 2048 MB.

Zie onze [Kudu-documentatie](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file) voor meer informatie.

De war-bestandsimplementatie implementeert uw [WAR-bestand](https://wikipedia.org/wiki/WAR_(file_format)) in App Service om uw Java-web-app uit te voeren. Zie [WAR-bestand implementeren.](#deploy-war-file)

> [!NOTE]
> Wanneer u gebruikt, worden bestanden alleen gekopieerd als de tijdstempels niet overeenkomen `ZipDeploy` met wat al is geïmplementeerd. Het genereren van een zip-bestand met behulp van een buildproces dat uitvoer in de cache opgeslagen, kan leiden tot snellere implementaties. Zie [Implementeren vanuit een ZIP-bestand of URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel wilt voltooien, maakt u [App Service app](./index.yml)of gebruikt u een app die u hebt gemaakt voor een andere zelfstudie.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
Het bovenstaande eindpunt werkt op dit moment niet App Services Linux-systemen. Overweeg in plaats daarvan FTP of de [ZIP-implementatie-API](faq-app-service-linux.md#continuous-integration-and-deployment) te gebruiken.

## <a name="deploy-zip-file-with-azure-cli"></a>ZIP-bestand implementeren met Azure CLI

Implementeer het geüploade ZIP-bestand naar uw web-app met behulp van [de opdracht az webapp deployment source config-zip.](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_zip)  

In het volgende voorbeeld wordt het ZIP-bestand geïmplementeerd dat u hebt geüpload. Wanneer u een lokale installatie van Azure CLI gebruikt, geeft u het pad op naar uw lokale ZIP-bestand voor `--src` .

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

Met deze opdracht worden de bestanden en mappen uit het ZIP-bestand geïmplementeerd in de standaardmap voor de App Service-toepassing (`\home\site\wwwroot`) en wordt de app opnieuw opgestart.

Standaard gaat de implementatie-engine ervan uit dat een ZIP-bestand gereed is om te worden uitgevoerd zoals het is en dat er geen buildautomatisering wordt uitgevoerd. Als u dezelfde buildautomatisering wilt inschakelen als in een [Git-implementatie,](deploy-local-git.md)stelt u de app-instelling in door de volgende opdracht uit te voeren in `SCM_DO_BUILD_DURING_DEPLOYMENT` [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Zie onze [Kudu-documentatie](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url) voor meer informatie.

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>WAR-bestand implementeren

Als u een WAR-bestand wilt implementeren App Service, verzendt u een POST-aanvraag naar `https://<app-name>.scm.azurewebsites.net/api/wardeploy` . Het WAR-bestand moet zijn opgenomen in de hoofdtekst van de POST-aanvraag. De implementatiereferenties voor uw app moet u opgegeven in de aanvraag met behulp van HTTP-basisverificatie.

Gebruik altijd `/api/wardeploy` bij het implementeren van WAR-bestanden. Met deze API wordt uw WAR-bestand uitgebreid en op het gedeelde bestandsstation opgeslagen. het gebruik van andere implementatie-API's kan leiden tot inconsistent gedrag. 

Voor de HTTP BASIC-verificatie hebt u uw App Service implementatiereferenties nodig. Zie Referenties op gebruikersniveau instellen en opnieuw instellen voor informatie over het instellen van [uw implementatiereferenties.](deploy-configure-credentials.md#userscope)

### <a name="with-curl"></a>Met cURL

In het volgende voorbeeld wordt het hulpprogramma cURL gebruikt om een WAR-bestand te implementeren. Vervang de tijdelijke aanduidingen `<username>` `<war-file-path>` , en `<app-name>` . Wanneer u hier om wordt gevraagd door cURL, typt u het wachtwoord.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Met PowerShell

In het volgende voorbeeld wordt [Publish-AzWebapp gebruikt om het](/powershell/module/az.websites/publish-azwebapp) WAR-bestand te uploaden. Vervang de tijdelijke aanduidingen `<group-name>` `<app-name>` , en `<war-file-path>` .

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Volgende stappen

Voor meer geavanceerde implementatiescenario's kunt [u proberen te implementeren in Azure met Git](deploy-local-git.md). Implementatie op basis van Git in Azure maakt versiebeheer, pakketherstel, MSBuild en meer mogelijk.

## <a name="more-resources"></a>Meer bronnen

* [Kudu: Implementeren vanuit een ZIP-bestand](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Azure App Service implementatiereferenties](deploy-ftp.md)
