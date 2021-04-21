---
title: Zip-pushimplementatie voor Azure Functions
description: Gebruik de faciliteiten voor zip-bestandsimplementatie van de Kudu-implementatieservice om uw gegevens te Azure Functions.
ms.topic: conceptual
ms.date: 08/12/2018
ms.openlocfilehash: fb6867d7719f9650acb00f80ac3a933713ce0e23
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777647"
---
# <a name="zip-deployment-for-azure-functions"></a>Zip-implementatie voor Azure Functions

In dit artikel wordt beschreven hoe u projectbestanden van uw functie-app implementeert in Azure vanuit een ZIP-bestand (gecomprimeerd). U leert hoe u een push-implementatie kunt uitvoeren, zowel met behulp van Azure CLI als met behulp van de REST API's. [Azure Functions Core Tools](functions-run-local.md) maakt ook gebruik van deze implementatie-API's bij het publiceren van een lokaal project naar Azure.

Azure Functions beschikt over het volledige scala aan opties voor continue implementatie en integratie die door de Azure App Service. Zie Continue implementatie [voor](functions-continuous-deployment.md)Azure Functions.

Als u de ontwikkeling wilt versnellen, is het wellicht eenvoudiger om de projectbestanden van uw functie-app rechtstreeks vanuit een ZIP-bestand te implementeren. De .zip-implementatie-API neemt de inhoud van een ZIP-bestand en extraheert de inhoud in de `wwwroot` map van uw functie-app. Deze ZIP-bestandsimplementatie maakt gebruik van dezelfde Kudu-service die wordt gebruikt voor continue implementaties op basis van integratie, waaronder:

+ Verwijderen van bestanden die zijn overgelaten van eerdere implementaties.
+ Implementatieaanpassing, inclusief het uitvoeren van implementatiescripts.
+ Implementatielogboeken.
+ Synchronisatiefunctietriggers in een [verbruiksplanfunctie-app.](functions-scale.md)

Zie de naslaginformatie voor [ZIP-implementatie voor meer informatie.](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)

## <a name="deployment-zip-file-requirements"></a>Vereisten voor ZIP-bestand voor implementatie

Het ZIP-bestand dat u voor de push-implementatie gebruikt, moet alle bestanden bevatten die nodig zijn om uw functie uit te voeren.

>[!IMPORTANT]
> Wanneer u .zip-implementatie gebruikt, worden bestanden van een bestaande implementatie die niet in het ZIP-bestand zijn gevonden, verwijderd uit uw functie-app.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Een functie-app bevat alle bestanden en mappen in de `wwwroot` map . De implementatie van een ZIP-bestand bevat de inhoud van de `wwwroot` map, maar niet de map zelf. Wanneer u een C#-klassebibliotheekproject implementeert, moet u de gecompileerde bibliotheekbestanden en afhankelijkheden opnemen in een `bin` submap in uw ZIP-pakket.

## <a name="download-your-function-app-files"></a>Uw functie-app-bestanden downloaden

Wanneer u op een lokale computer ontwikkelt, kunt u eenvoudig een ZIP-bestand maken van de projectmap van de functie-app op uw ontwikkelcomputer.

Mogelijk hebt u uw functies echter gemaakt met behulp van de editor in de Azure Portal. U kunt een bestaand functie-app-project op een van de volgende manieren downloaden:

+ **Vanuit de Azure Portal:**

  1. Meld u aan bij [Azure Portal](https://portal.azure.com)en ga vervolgens naar uw functie-app.

  2. Selecteer op **het tabblad** Overzicht de optie **App-inhoud downloaden.** Selecteer uw downloadopties en selecteer vervolgens **Downloaden.**

      ![Het functie-app-project downloaden](./media/deployment-zip-push/download-project.png)

     Het gedownloade ZIP-bestand heeft de juiste indeling om opnieuw te worden gepubliceerd naar uw functie-app met behulp van .zip-pushimplementatie. Met het downloaden van de portal kunt u ook de bestanden toevoegen die nodig zijn om uw functie-app rechtstreeks in de Visual Studio.

+ **REST API's gebruiken:**

    Gebruik de volgende GET API voor implementatie om de bestanden van uw project te `<function_app>` downloaden: 

    ```http
    https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/
    ```

    Inclusief `/site/wwwroot/` zorgt ervoor dat uw zip-bestand alleen de projectbestanden van de functie-app bevat en niet de hele site. Als u nog niet bent aangemeld bij Azure, wordt u gevraagd dit te doen.  

U kunt ook een ZIP-bestand downloaden uit een GitHub-opslagplaats. Wanneer u een GitHub-opslagplaats downloadt als zip-bestand, voegt GitHub een extra mapniveau toe voor de vertakking. Dit extra mapniveau betekent dat u het ZIP-bestand niet rechtstreeks kunt implementeren terwijl u het hebt gedownload van GitHub. Als u een GitHub-opslagplaats gebruikt om uw functie-app te onderhouden, moet u continue [integratie gebruiken](functions-continuous-deployment.md) om uw app te implementeren.  

## <a name="deploy-by-using-azure-cli"></a><a name="cli"></a>Implementeren met behulp van Azure CLI

U kunt Azure CLI gebruiken om een push-implementatie te activeren. Push implementeer een ZIP-bestand naar uw functie-app met behulp van de [opdracht az functionapp deployment source config-zip.](/cli/azure/functionapp/deployment/source#az_functionapp_deployment_source_config_zip) Als u deze opdracht wilt gebruiken, moet u Azure CLI versie 2.0.21 of hoger gebruiken. Gebruik de opdracht om te zien welke Azure CLI-versie u `az --version` gebruikt.

Vervang in de volgende opdracht de tijdelijke aanduiding `<zip_file_path>` door het pad naar de locatie van uw ZIP-bestand. Vervang ook `<app_name>` door de unieke naam van uw functie-app en vervang door de naam van uw `<resource_group>` resourcegroep.

```azurecli-interactive
az functionapp deployment source config-zip -g <resource_group> -n \
<app_name> --src <zip_file_path>
```

Met deze opdracht worden projectbestanden van het gedownloade ZIP-bestand geïmplementeerd in uw functie-app in Azure. Vervolgens wordt de app opnieuw gestart. Als u de lijst met implementaties voor deze functie-app wilt weergeven, moet u de REST API's gebruiken.

Wanneer u Azure CLI op uw lokale computer gebruikt, `<zip_file_path>` is het pad naar het ZIP-bestand op uw computer. U kunt Azure CLI ook uitvoeren in [Azure Cloud Shell](../cloud-shell/overview.md). Wanneer u Cloud Shell gebruikt, moet u eerst het ZIP-bestand van uw implementatie uploaden naar het Azure Files-account dat is gekoppeld aan uw Cloud Shell. In dat geval `<zip_file_path>` is de opslaglocatie die uw Cloud Shell account gebruikt. Zie Persist [files in Azure Cloud Shell (Bestanden in](../cloud-shell/persisting-shell-storage.md)de Azure Cloud Shell) voor meer informatie.

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Functies uitvoeren vanuit het implementatiepakket

U kunt er ook voor kiezen om uw functies rechtstreeks vanuit het implementatiepakketbestand uit te voeren. Met deze methode wordt de implementatiestap overgeslagen van het kopiëren van bestanden van het pakket naar de `wwwroot` map van uw functie-app. In plaats daarvan wordt het pakketbestand door de Functions-runtime en wordt de inhoud van de `wwwroot` map alleen-lezen.  

Zip-implementatie kan worden geïntegreerd met deze functie, die u kunt inschakelen door de instelling voor de functie-app in `WEBSITE_RUN_FROM_PACKAGE` te stellen op de waarde `1` . Zie Uw functies uitvoeren vanuit [een implementatiepakketbestand voor meer informatie.](run-functions-from-deployment-package.md)

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Continue implementatie voor Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
