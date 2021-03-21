---
title: Uw app uitvoeren vanuit een ZIP-pakket
description: Implementeer het ZIP-pakket van uw app met behulp van atomisch. Verbeter de voorspel baarheid en betrouw baarheid van het gedrag van uw app tijdens het ZIP-implementatie proces.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 036dd8c86251af2ed5553939e74a0d85fc303ecb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577543"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Uw app rechtstreeks vanuit een ZIP-pakket uitvoeren in Azure App Service

In [Azure app service](overview.md)kunt u uw apps rechtstreeks vanuit een zip-pakket bestand voor implementatie uitvoeren. In dit artikel wordt beschreven hoe u deze functionaliteit in uw app inschakelt.

Alle andere implementatie methoden in App Service hebben een gemeen schappelijke waarde: uw bestanden worden geïmplementeerd in *D:\home\site\wwwroot* in uw app (of */Home/site/wwwroot* voor Linux-apps). Aangezien dezelfde map tijdens runtime wordt gebruikt door uw app, is het mogelijk dat de implementatie mislukt omdat er conflicten zijn opgetreden in de bestands vergrendeling, waardoor de app onvoorspelbaar werkt omdat sommige bestanden nog niet zijn bijgewerkt.

Als u daarentegen rechtstreeks vanuit een pakket uitvoert, worden de bestanden in het pakket niet gekopieerd naar de map *wwwroot* . In plaats daarvan wordt het ZIP-pakket zelf direct gekoppeld als de alleen-lezen *wwwroot* -Directory. Er zijn verschillende voor delen die rechtstreeks vanuit een pakket kunnen worden uitgevoerd:

- Elimineert conflicten tussen bestands vergrendelingen tussen implementatie en runtime.
- Hiermee wordt gegarandeerd dat er op elk gewenst moment alleen apps met volledige implementatie worden uitgevoerd.
- Kan worden geïmplementeerd in een productie-app (met opnieuw opstarten).
- Verbetert de prestaties van Azure Resource Manager implementaties.
- Kan koude begin tijden verminderen, met name voor Java script-functies met grote NPM-pakket structuren.

> [!NOTE]
> Op dit moment worden alleen ZIP-pakket bestanden ondersteund.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Uitvoeren vanaf pakket inschakelen

`WEBSITE_RUN_FROM_PACKAGE`Met de app-instelling kan vanuit een pakket worden uitgevoerd. Als u deze wilt instellen, voert u de volgende opdracht uit met Azure CLI.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"` met kunt u uw app uitvoeren vanuit een pakket dat lokaal is voor uw app. U kunt ook [uitvoeren vanuit een extern pakket](#run-from-external-url-instead).

## <a name="run-the-package"></a>Het pakket uitvoeren

De eenvoudigste manier om een pakket in uw App Service uit te voeren, is met de Azure CLI [AZ webapp Deployment source config-](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-zip) opdracht. Bijvoorbeeld:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Omdat de `WEBSITE_RUN_FROM_PACKAGE` app-instelling is ingesteld, wordt met deze opdracht de pakket inhoud niet uitgepakt naar de map *D:\home\site\wwwroot* van uw app. In plaats daarvan uploadt het ZIP-bestand naar *D:\home\data\SitePackages* en maakt het een *packagename.txt* in dezelfde directory, die de naam bevat van het zip-pakket dat tijdens runtime wordt geladen. Als u uw ZIP-pakket op een andere manier uploadt (zoals [FTP](deploy-ftp.md)), moet u de *D:\home\data\SitePackages* -map en het *packagename.txt* bestand hand matig maken.

Met deze opdracht wordt ook de app opnieuw gestart. Omdat `WEBSITE_RUN_FROM_PACKAGE` is ingesteld, app service het geüploade pakket koppelen als de alleen-lezen *wwwroot* -map en wordt de app rechtstreeks vanuit die gekoppelde map uitgevoerd.

## <a name="run-from-external-url-instead"></a>Uitvoeren vanaf externe URL

U kunt ook een pakket uitvoeren vanuit een externe URL, zoals Azure Blob Storage. U kunt de [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) gebruiken om pakket bestanden te uploaden naar uw Blob Storage-account. U moet een persoonlijke opslag container met een [Shared Access Signature (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) gebruiken om de app service runtime in staat te stellen om veilig toegang te krijgen tot het pakket. 

Wanneer u uw bestand uploadt naar Blob-opslag en een SAS-URL voor het bestand hebt, stelt u de `WEBSITE_RUN_FROM_PACKAGE` app-instelling in op de URL. In het volgende voor beeld wordt dit gedaan met behulp van Azure CLI:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Als u een bijgewerkt pakket met dezelfde naam naar de Blob-opslag publiceert, moet u de app opnieuw opstarten zodat het bijgewerkte pakket in App Service wordt geladen.

## <a name="troubleshooting"></a>Problemen oplossen

- Direct vanuit een pakket wordt uitgevoerd `wwwroot` , heeft het kenmerk alleen-lezen. Uw app krijgt een fout melding als wordt geprobeerd om bestanden naar deze map te schrijven.
- TAR-en GZIP-indelingen worden niet ondersteund.
- Het ZIP-bestand kan Maxi maal 1 GB groot zijn
- Deze functie is niet compatibel met de [lokale cache](overview-local-cache.md).
- Gebruik de lokale zip-optie (= 1) voor verbeterde prestaties van koud start `WEBSITE_RUN_FROM_PACKAGE` .

## <a name="more-resources"></a>Meer bronnen

- [Continue implementatie voor Azure App Service](deploy-continuous-deployment.md)
- [Code implementeren met een ZIP-of WAR-bestand](deploy-zip.md)
