---
title: Zip-push implementatie voor Azure Functions
description: Gebruik de implementatie faciliteiten van het zip-bestand van de kudu-implementatie service om uw Azure Functions te publiceren.
ms.topic: conceptual
ms.date: 08/12/2018
ms.openlocfilehash: e104661dcdf1f6c6fd6dd5eb1024748980e7931f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018421"
---
# <a name="zip-deployment-for-azure-functions"></a>Zip-implementatie voor Azure Functions

In dit artikel wordt beschreven hoe u Project bestanden van uw functie-app implementeert in azure vanuit een zip-bestand (gecomprimeerd). U leert hoe u een push-implementatie kunt uitvoeren met behulp van Azure CLI en met behulp van de REST-Api's. [Azure functions core tools](functions-run-local.md) gebruikt deze implementatie-api's ook bij het publiceren van een lokaal project naar Azure.

Azure Functions heeft het volledige aanbod van continue implementatie-en integratie opties die worden verstrekt door Azure App Service. Zie [continue implementatie voor Azure functions](functions-continuous-deployment.md)voor meer informatie.

Om de ontwikkeling te versnellen, is het wellicht eenvoudiger om uw functie-app-project bestanden rechtstreeks vanuit een zip-bestand te implementeren. De API. zip-implementatie neemt de inhoud van een zip-bestand en extraheert de inhoud naar de `wwwroot` map van uw functie-app. Deze zip-bestands implementatie maakt gebruik van dezelfde kudu-service die voorziet in doorlopende implementaties op basis van integratie, waaronder:

+ Verwijderen van bestanden die overgebleven waren in eerdere implementaties.
+ Aanpassing van de implementatie, waaronder het uitvoeren van implementatie scripts.
+ Implementatie Logboeken.
+ Functie Triggers worden gesynchroniseerd in een functie-app met een [verbruiks plan](functions-scale.md) .

Zie voor meer informatie de [referentie. zip-implementatie](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="deployment-zip-file-requirements"></a>Vereisten voor implementatie. zip-bestand

Het zip-bestand dat u voor push-implementatie gebruikt, moet alle bestanden bevatten die nodig zijn om de functie uit te voeren.

>[!IMPORTANT]
> Wanneer u. zip-implementatie gebruikt, worden alle bestanden uit een bestaande implementatie die niet in het zip-bestand zijn gevonden, verwijderd uit de functie-app.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Een functie-app bevat alle bestanden en mappen in de `wwwroot` map. De implementatie van een zip-bestand bevat de inhoud van de `wwwroot` map, maar niet de map zelf. Wanneer u een C# Class Library-project implementeert, moet u de gecompileerde bibliotheek bestanden en afhankelijkheden in een `bin` submap van uw zip-pakket toevoegen.

## <a name="download-your-function-app-files"></a>Down load de bestanden van de functie-app

Wanneer u op een lokale computer ontwikkelt, is het eenvoudig om een zip-bestand te maken in de map van de functie-app-project op uw ontwikkel computer.

U hebt uw functies mogelijk wel gemaakt met behulp van de editor in de Azure Portal. U kunt een bestaand functie-app-project op een van de volgende manieren downloaden:

+ **Van de Azure Portal:**

  1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en ga vervolgens naar uw functie-app.

  2. Selecteer **app-inhoud downloaden** op het tabblad **overzicht** . Selecteer de download opties en selecteer vervolgens **downloaden**.

      ![Down load het project van de functie-app](./media/deployment-zip-push/download-project.png)

     Het gedownloade zip-bestand heeft de juiste indeling zodat deze opnieuw kan worden gepubliceerd naar uw functie-app met behulp van. zip push-implementatie. Het downloaden van de portal kan ook de bestanden toevoegen die nodig zijn om de functie-app rechtstreeks in Visual Studio te openen.

+ **REST-Api's gebruiken:**

    Gebruik de volgende implementatie-API om de bestanden van uw project te downloaden `<function_app>` : 

    ```http
    https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/
    ```

    Met inbegrip van `/site/wwwroot/` kunt u ervoor zorgen dat uw zip-bestand alleen de project bestanden van de functie-app en niet de hele site bevat. Als u nog niet bent aangemeld bij Azure, wordt u gevraagd dit te doen.  

U kunt ook een zip-bestand downloaden uit een GitHub-opslag plaats. Wanneer u een GitHub-opslag plaats als zip-bestand downloadt, voegt GitHub een extra mapniveau voor de vertakking toe. Dit extra mapniveau betekent dat u het zip-bestand niet rechtstreeks kunt implementeren tijdens het downloaden van GitHub. Als u een GitHub-opslag plaats gebruikt voor het onderhouden van uw functie-app, moet u [continue integratie](functions-continuous-deployment.md) gebruiken om uw app te implementeren.  

## <a name="deploy-by-using-azure-cli"></a><a name="cli"></a>Implementeren met behulp van Azure CLI

U kunt Azure CLI gebruiken om een push-implementatie te activeren. Push implementeren van een zip-bestand naar uw functie-app met behulp van de opdracht [AZ functionapp Deployment source config-zip](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip) . Als u deze opdracht wilt gebruiken, moet u Azure CLI versie 2.0.21 of hoger gebruiken. Als u wilt zien welke Azure CLI-versie u gebruikt, gebruikt u de `az --version` opdracht.

Vervang in de volgende opdracht de `<zip_file_path>` tijdelijke aanduiding door het pad naar de locatie van uw zip-bestand. Vervang ook door `<app_name>` de unieke naam van de functie-app en vervang door `<resource_group>` de naam van uw resource groep.

```azurecli-interactive
az functionapp deployment source config-zip -g <resource_group> -n \
<app_name> --src <zip_file_path>
```

Met deze opdracht worden project bestanden van het gedownloade zip-bestand geïmplementeerd in uw functie-app in Azure. Vervolgens wordt de app opnieuw gestart. Als u de lijst met implementaties voor deze functie-app wilt weer geven, moet u de REST-Api's gebruiken.

Wanneer u Azure CLI op uw lokale computer gebruikt, `<zip_file_path>` is het pad naar het zip-bestand op uw computer. U kunt ook Azure CLI uitvoeren in [Azure Cloud shell](../cloud-shell/overview.md). Wanneer u Cloud Shell gebruikt, moet u eerst uw implementatie. zip-bestand uploaden naar het Azure Files-account dat is gekoppeld aan uw Cloud Shell. In dat geval `<zip_file_path>` is de opslag locatie die uw Cloud shell-account gebruikt. Zie [bestanden in azure Cloud shell persistent](../cloud-shell/persisting-shell-storage.md)maken voor meer informatie.

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Functies uitvoeren vanuit het implementatie pakket

U kunt er ook voor kiezen om uw functies rechtstreeks vanuit het implementatie pakket bestand uit te voeren. Deze methode slaat de implementatie stap over van het kopiëren van bestanden uit het pakket naar de `wwwroot` map van uw functie-app. In plaats daarvan wordt het pakket bestand gekoppeld door de runtime van functions en wordt de inhoud van de `wwwroot` Directory alleen-lezen.  

Een zip-implementatie kan worden geïntegreerd met deze functie, die u kunt inschakelen door de instelling van de functie-app `WEBSITE_RUN_FROM_PACKAGE` in te stellen op een waarde van `1` . Zie [uw functies uitvoeren vanuit een implementatie pakket bestand](run-functions-from-deployment-package.md)voor meer informatie.

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Doorlopende implementatie voor Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
