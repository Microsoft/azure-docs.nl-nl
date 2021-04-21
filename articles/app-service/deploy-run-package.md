---
title: Uw app uitvoeren vanuit een ZIP-pakket
description: Implementeer het ZIP-pakket van uw app met atomiciteit. Verbeter de voorspelbaarheid en betrouwbaarheid van het gedrag van uw app tijdens het ZIP-implementatieproces.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: d3315370342f54091598aa3f77f70f03bda4ad33
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772735"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Uw app rechtstreeks vanuit Azure App Service ZIP-pakket uitvoeren

In [Azure App Service](overview.md)kunt u uw apps rechtstreeks uitvoeren vanuit een ZIP-pakketbestand voor de implementatie. In dit artikel wordt beschreven hoe u deze functionaliteit in uw app inschakelen.

Alle andere implementatiemethoden in App Service hebben iets gemeenschappelijks: uw bestanden worden geïmplementeerd naar *D:\home\site\wwwroot* in uw app (of */home/site/wwwroot* voor Linux-apps). Aangezien dezelfde map tijdens runtime door uw app wordt gebruikt, is het mogelijk dat de implementatie mislukt vanwege bestandsvergrendelingsconflicten en dat de app zich onvoorspelbaar gedraagt omdat sommige bestanden nog niet zijn bijgewerkt.

Wanneer u daarentegen rechtstreeks vanuit een pakket wordt uitgevoerd, worden de bestanden in het pakket niet gekopieerd naar de *map wwwroot.* In plaats daarvan wordt het ZIP-pakket zelf rechtstreeks als de alleen-lezen *wwwroot-map* bevestigd. Het rechtstreeks uitvoeren vanuit een pakket heeft verschillende voordelen:

- Voorkomt bestandsvergrendelingsconflicten tussen implementatie en runtime.
- Zorgt ervoor dat alleen volledig geïmplementeerde apps op elk moment worden uitgevoerd.
- Kan worden geïmplementeerd in een productie-app (met opnieuw opstarten).
- Verbetert de prestaties van Azure Resource Manager implementaties.
- Kan koude starttijden verminderen, met name voor JavaScript-functies met grote npm-pakketboom.

> [!NOTE]
> Momenteel worden alleen ZIP-pakketbestanden ondersteund.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Uitvoeren vanuit pakket inschakelen

Met `WEBSITE_RUN_FROM_PACKAGE` de app-instelling kunt u uitvoeren vanuit een pakket. Voer de volgende opdracht uit met Azure CLI om deze in te stellen.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"` met kunt u uw app uitvoeren vanuit een lokaal pakket naar uw app. U kunt ook [uitvoeren vanaf een extern pakket](#run-from-external-url-instead).

## <a name="run-the-package"></a>Het pakket uitvoeren

De eenvoudigste manier om een pakket uit te voeren in uw App Service is met de Azure CLI-opdracht [az webapp deployment source config-zip.](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_zip) Bijvoorbeeld:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Omdat de app-instelling is ingesteld, wordt met deze opdracht de pakketinhoud niet uitgepakt naar de `WEBSITE_RUN_FROM_PACKAGE` *map D:\home\site\wwwroot* van uw app. In plaats daarvan wordt het ZIP-bestand naar *D:\home\data\SitePackages* geüpload en wordt een *packagename.txt* gemaakt in dezelfde map, die de naam bevat van het ZIP-pakket dat tijdens runtime moet worden geladen. Als u uw ZIP-pakket op een andere manier uploadt (zoals [FTP),](deploy-ftp.md)moet u de map *D:\home\data\SitePackages* en het *packagename.txt* handmatig maken.

Met de opdracht wordt de app ook opnieuw gestart. Omdat is ingesteld, App Service het geüploade pakket als de `WEBSITE_RUN_FROM_PACKAGE` alleen-lezen *map wwwroot* en wordt de app rechtstreeks uitgevoerd vanuit die map.

## <a name="run-from-external-url-instead"></a>Voer in plaats daarvan uit vanaf een externe URL

U kunt ook een pakket uitvoeren vanaf een externe URL, zoals Azure Blob Storage. U kunt de [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) om pakketbestanden te uploaden naar uw Blob Storage-account. Gebruik een privéopslagcontainer met een [Shared Access Signature (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) om de runtime van de App Service veilig toegang te geven tot het pakket. 

Nadat u het bestand hebt geüpload naar Blob Storage en een SAS-URL voor het bestand hebt, stelt u de `WEBSITE_RUN_FROM_PACKAGE` app-instelling in op de URL. In het volgende voorbeeld wordt dit uitgevoerd met behulp van Azure CLI:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Als u een bijgewerkt pakket met dezelfde naam publiceert naar Blob Storage, moet u uw app opnieuw opstarten zodat het bijgewerkte pakket in de App Service.

## <a name="troubleshooting"></a>Problemen oplossen

- Als u rechtstreeks vanuit een pakket wordt uitgevoerd, `wwwroot` wordt alleen-lezen gebruikt. Uw app ontvangt een foutmelding als wordt geprobeerd bestanden naar deze map te schrijven.
- TAR- en GZIP-indelingen worden niet ondersteund.
- Het ZIP-bestand mag niet meer dan 1 GB zijn
- Deze functie is niet compatibel met [lokale cache.](overview-local-cache.md)
- Gebruik de lokale zip-optie (=1) voor verbeterde koude `WEBSITE_RUN_FROM_PACKAGE` startprestaties.

## <a name="more-resources"></a>Meer bronnen

- [Continue implementatie voor Azure App Service](deploy-continuous-deployment.md)
- [Code implementeren met een ZIP- of WAR-bestand](deploy-zip.md)
