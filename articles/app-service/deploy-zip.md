---
title: Code implementeren met een ZIP-of WAR-bestand
description: Meer informatie over het implementeren van uw app naar Azure App Service met een ZIP-bestand (of een WAR-bestand voor Java-Ontwikkel aars).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 150f6b57f1dec0b6d925ef53b4a7001ae9f23607
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97007905"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Uw app implementeren voor Azure App Service met een ZIP-of WAR-bestand

In dit artikel leest u hoe u een ZIP-bestand of een WAR-bestand gebruikt om uw web-app te implementeren op [Azure app service](overview.md). 

Bij deze ZIP-bestands implementatie wordt gebruikgemaakt van dezelfde kudu-service die doorlopende implementaties op basis van integratie aanstuurt. Kudu ondersteunt de volgende functionaliteit voor de implementatie van ZIP-bestanden: 

- Het verwijderen van bestanden van een vorige implementatie is overgebleven.
- Optie voor het inschakelen van het standaard constructie proces, dat package Restore bevat.
- Aanpassing van de implementatie, waaronder het uitvoeren van implementatie scripts.  
- Implementatie Logboeken. 
- De maximale bestands grootte van 2048 MB.

Zie onze [Kudu-documentatie](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file) voor meer informatie.

De WAR-bestands implementatie implementeert uw [War](https://wikipedia.org/wiki/WAR_(file_format)) -bestand op app service om uw Java-Web-app uit te voeren. Zie [war-bestand implementeren](#deploy-war-file).

> [!NOTE]
> Wanneer u gebruikt `ZipDeploy` , worden alleen bestanden gekopieerd als de tijds tempels niet overeenkomen met wat er al is geïmplementeerd. Het genereren van een zip met een bouw proces dat uitvoer in de cache opslaat, kan leiden tot snellere implementaties. Zie [implementeren vanuit een zip-bestand of-URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel wilt uitvoeren, [maakt u een app service-app](./index.yml)of gebruikt u een app die u hebt gemaakt voor een andere zelf studie.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
Het bovenstaande eind punt werkt op dit moment niet voor Linux-App Services. Overweeg in plaats daarvan FTP of de [API voor zip-implementatie](faq-app-service-linux.md#continuous-integration-and-deployment) te gebruiken.

## <a name="deploy-zip-file-with-azure-cli"></a>ZIP-bestand implementeren met Azure CLI

Implementeer het geüploade ZIP-bestand naar uw web-app met behulp van de opdracht [AZ webapp Deployment source config-zip](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-zip) .  

In het volgende voor beeld wordt het ZIP-bestand dat u hebt geüpload, geïmplementeerd. Wanneer u een lokale installatie van Azure CLI gebruikt, geeft u het pad op naar het lokale ZIP-bestand voor `--src` .

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

Met deze opdracht worden de bestanden en mappen uit het ZIP-bestand geïmplementeerd in de standaardmap voor de App Service-toepassing (`\home\site\wwwroot`) en wordt de app opnieuw opgestart.

De implementatie-engine veronderstelt standaard dat een ZIP-bestand gereed is om te worden uitgevoerd, en voert geen automatisering op basis van builds uit. Als u dezelfde build-automatisering wilt inschakelen als in een [Git-implementatie](deploy-local-git.md), stelt u de `SCM_DO_BUILD_DURING_DEPLOYMENT` app-instelling in door de volgende opdracht uit te voeren in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Zie onze [Kudu-documentatie](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url) voor meer informatie.

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>WAR-bestand implementeren

Als u een WAR-bestand wilt implementeren op App Service, stuurt u een POST-aanvraag naar `https://<app-name>.scm.azurewebsites.net/api/wardeploy` . Het WAR-bestand moet zijn opgenomen in de hoofdtekst van de POST-aanvraag. De implementatiereferenties voor uw app moet u opgegeven in de aanvraag met behulp van HTTP-basisverificatie.

Altijd gebruiken `/api/wardeploy` bij het implementeren van War-bestanden. Met deze API wordt uw WAR-bestand uitgebreid en op het gedeelde bestands station geplaatst. het gebruik van andere implementatie-Api's kan leiden tot inconsistent gedrag. 

Voor de HTTP-basis verificatie hebt u uw App Service implementatie referenties nodig. Zie [referenties voor gebruikers niveau instellen en opnieuw](deploy-configure-credentials.md#userscope)instellen om te zien hoe u uw implementatie referenties kunt instellen.

### <a name="with-curl"></a>Met krul

In het volgende voor beeld wordt het gereedschap krul gebruikt om een WAR-bestand te implementeren. Vervang de tijdelijke aanduidingen `<username>` , `<war-file-path>` en `<app-name>` . Wanneer u wordt gevraagd door krul, typt u het wacht woord.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Met PowerShell

In het volgende voor beeld wordt het WAR-bestand geüpload [-AzWebapp](/powershell/module/az.websites/publish-azwebapp) . Vervang de tijdelijke aanduidingen `<group-name>` , `<app-name>` en `<war-file-path>` .

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Volgende stappen

Probeer [te implementeren naar Azure met git](deploy-local-git.md)voor meer geavanceerde implementatie scenario's. Met op Git gebaseerde implementatie naar Azure kunt u versie beheer, pakket herstel, MSBuild en meer.

## <a name="more-resources"></a>Meer bronnen

* [Kudu: implementeren vanuit een zip-bestand](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Implementatie referenties Azure App Service](deploy-ftp.md)
