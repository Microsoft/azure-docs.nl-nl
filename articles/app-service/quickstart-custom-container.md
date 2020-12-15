---
title: 'Quickstart: Een aangepaste container uitvoeren op App Service'
description: Ga aan de slag met containers op Azure App Service door uw eerste aangepaste container te implementeren.
author: msangapu-msft
ms.author: msangapu
ms.date: 10/21/2019
ms.topic: quickstart
ms.custom: devx-track-csharp
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 360da015f012822593dbb6390cb7df0017ba85b1
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745074"
---
# <a name="run-a-custom-container-in-azure"></a>Een aangepaste container uitvoeren in Azure

::: zone pivot="container-windows"
[Azure App Service](overview.md) biedt vooraf gedefinieerde toepassingsstacks in Windows, zoals ASP.NET of Node.js, die worden uitgevoerd in IIS. Met de vooraf geconfigureerde Windows-container wordt het besturingssysteem vergrendeld voor beheerderstoegang, software-installaties, wijzigingen aan de Global Assembly Cache, enzovoort. Zie [Functionaliteit van besturingssystemen in Azure App Service](operating-system-functionality.md) voor meer informatie. Als voor uw toepassing meer toegang is vereist dan is toegestaan in de vooraf geconfigureerde omgeving, kunt u in plaats hiervan een aangepaste Windows-container implementeren.

Deze quickstart laat zien hoe u een ASP.NET-app in een Windows-installatiekopie implementeert in [Docker Hub](https://hub.docker.com/) vanuit Visual Studio. U voert de app uit in een aangepaste container in Azure App Service.

> [!NOTE]
> Windows-containers is beperkt tot Azure Files en biedt momenteel geen ondersteuning voor Azure Blob.


## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

- <a href="https://hub.docker.com/" target="_blank">Registreren voor een Docker Hub-account</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Docker voor Windows installeren</a>.
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Docker instellen voor het uitvoeren van Windows-containers</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 installeren</a> met de workloads **ASP.NET- en webontwikkeling** en **Azure-ontwikkeling**. Als u Visual Studio 2019 al hebt geïnstalleerd:

    - Installeer de nieuwste updates in Visual Studio door **Help** > **Controleren op updates** te selecteren.
    - Voeg de workloads toe in Visual Studio door **Extra** > **Hulpprogramma’s en functies ophalen** te selecteren.

## <a name="create-an-aspnet-web-app"></a>Een ASP.NET-web-app maken

Maak een ASP.NET-web-app door de volgende stappen uit te voeren:

1. Open Visual Studio en selecteer **Een nieuw project maken**.

1. Kies in **Een nieuw project maken** de optie **ASP.NET-webtoepassing (.NET Framework)** voor #C. Selecteer vervolgens **Volgende**.

1. Geef in **Uw nieuwe project configureren** de toepassing de naam _myfirstazurewebapp_ en selecteer **Maken**.

   ![Uw web-app-project configureren](./media/quickstart-custom-container/configure-web-app-project-container.png)

1. U kunt elk type ASP.NET-web-app implementeren in Azure. Kies voor deze quickstart de sjabloon **MVC**.

1. Selecteer **Docker-ondersteuning** en zorg dat verificatie is ingesteld op **Geen verificatie**. Selecteer **Maken**.

   ![ASP.NET-webtoepassing maken](./media/quickstart-custom-container/select-mvc-template-for-container.png)

1. Als het bestand _Dockerfile_ niet automatisch wordt geopend, opent u het vanuit **Solution Explorer**.

1. U hebt een [ondersteunde bovenliggende installatiekopie](configure-custom-container.md#supported-parent-images) nodig. Wijzig de bovenliggende installatiekopie door de regel `FROM` ​​te vervangen door de volgende code en het bestand op te slaan:

   ```dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. Selecteer in het menu van Visual Studio de optie **Foutopsporing** > **Starten zonder foutopsporing** om de web-app lokaal uit te voeren.

   ![De app lokaal uitvoeren](./media/quickstart-custom-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Publiceren naar Docker Hub

1. Klik in **Solution Explorer** met de rechtermuisknop op het project **myfirstazurewebapp** en selecteer **Publiceren**.

1. Kies **App Service** en selecteer vervolgens **Publiceren**.

1. Selecteer in **Een publicatiedoel kiezen** de opties **Container Registry** en **Docker Hub**. Selecteer vervolgens **Publiceren**.

   ![Publiceren vanaf de projectoverzichtspagina](./media/quickstart-custom-container/publish-to-docker-vs2019.png)

1. Geef de referenties voor uw Docker Hub-account op en selecteer **Opslaan**.

   Wacht totdat de installatie is voltooid. Op de pagina **Publiceren** ziet u nu de naam van de opslagplaats die u later gaat gebruiken.

   ![Schermopname met de knop naam van de opslagplaats gemarkeerd.](./media/quickstart-custom-container/published-docker-repository-vs2019.png)

1. Kopieer deze naam van de opslagplaats later gebruik.

## <a name="create-a-windows-container-app"></a>Een Windows-container-app maken

1. Meld u aan bij de [Azure-portal]( https://portal.azure.com).

1. Kies in de linkerbovenhoek van Azure Portal **Een resource maken**.

1. Ga in het zoekvak boven de lijst met Azure Marketplace-resources naar **Web App for Containers** en selecteer **Maken**.

1. Kies in **Web-app maken** uw abonnement en een **Resourcegroep**. U kunt een nieuwe resourcegroep maken, indien nodig.

1. Geef een naam op voor de app, zoals *win-container-demo* en kies **Windows** als **Besturingssysteem**. Selecteer **Volgende: Docker** om door te gaan.

   ![Een web-app voor containers maken](media/quickstart-custom-container/create-web-app-continer.png)

1. Kies **Docker Hub** als **Broninstallatiekopie**, en voer voor **Installatiekopie en tag**, de opslagplaatsnaam in die u hebt gekopieerd in [Publiceren in Docker Hub](#publish-to-docker-hub).

   ![Een web-app voor containers configureren](media/quickstart-custom-container/configure-web-app-continer.png)

    Als u elders een aangepaste installatiekopie voor de webtoepassing hebt, bijvoorbeeld in [Azure Container Registry](../container-registry/index.yml) of in een andere privéopslagplaats, kunt u deze hier configureren.

1. Selecteer **Controleren en maken** en vervolgens **Maken**, en wacht tot de vereiste resources zijn gemaakt in Azure.

## <a name="browse-to-the-container-app"></a>Naar de container-app bladeren

Als de bewerking in Azure is voltooid, wordt er een melding weergegeven.

![Implementatie geslaagd](media/quickstart-custom-container/portal-create-finished.png)

1. Klik op **Ga naar resource**.

1. Klik in het overzicht van deze resource op de koppeling naast **URL**.

Er wordt een nieuwe browserpagina geopend met de volgende pagina:

![Windows-container-app wordt gestart](media/quickstart-custom-container/app-starting.png)

Wacht enkele minuten en probeer het opnieuw totdat u de startpagina van ASP.NET ziet:

![Windows-container-app wordt uitgevoerd](media/quickstart-custom-container/app-running-vs.png)

**Gefeliciteerd!** U voert nu uw eerste aangepaste Windows-container uit in Azure App Service.

## <a name="see-container-start-up-logs"></a>Logboeken voor opstarten van containers bekijken

Het kan enige tijd duren voordat de Windows-container is geladen. Als u de voortgang wilt bekijken, gaat u naar de volgende URL en vervangt u *\<app_name>* door de naam van de app.
```
https://<app_name>.scm.azurewebsites.net/api/logstream
```

De gestreamde logboeken zien er ongeveer als volgt uit:

```
2018-07-27T12:03:11  Welcome, you are now connected to log-streaming service.
27/07/2018 12:04:10.978 INFO - Site: win-container-demo - Start container succeeded. Container: facbf6cb214de86e58557a6d073396f640bbe2fdec88f8368695c8d1331fc94b
27/07/2018 12:04:16.767 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.017 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.020 INFO - Site: win-container-demo - Container started successfully
```

## <a name="update-locally-and-redeploy"></a>Lokaal bijwerken en opnieuw implementeren

1. Open in Visual Studio, in **Solution Explorer**, achtereenvolgens **Weergaven** > **Start** > **Index.cshtml**.

1. Zoek ergens bovenaan de HTML-tag `<div class="jumbotron">` en vervang het volledige element door de volgende code:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Als u opnieuw wilt implementeren in Azure, klikt u in **Solution Explorer** met de rechtermuisknop op het project **myfirstazurewebapp**, en kiest u **Publiceren**.

1. Selecteer op de pagina Publiceren de knop **Publiceren** en wacht tot het publiceren is voltooit.

1. Als u wilt dat App Service de nieuwe installatiekopie ophaalt uit Docker Hub, moet u de app opnieuw starten. Klik als u terugbent in de app-pagina in de portal op **Opnieuw starten** > **Ja**.

   ![Web-app opnieuw starten in Azure](./media/quickstart-custom-container/portal-restart-app.png)

[Blader opnieuw naar de container-app](#browse-to-the-container-app). Nadat de webpagina is vernieuwd, moet de app eerst teruggaan naar de pagina 'Starting up' en dan na een paar minuten opnieuw de bijgewerkte webpagina weergeven.

![Bijgewerkte web-app in Azure](./media/quickstart-custom-container/azure-web-app-updated.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Migreren naar Windows-container in Azure](tutorial-custom-container.md)

U kunt ook andere resources bekijken:

> [!div class="nextstepaction"]
> [Een aangepaste container configureren](configure-custom-container.md)

::: zone-end  

::: zone pivot="container-linux"
App Service op Linux biedt vooraf gedefinieerde toepassingsstacks op Linux met ondersteuning voor talen zoals .NET, PHP, Node.js en meer. U kunt een aangepaste Docker-installatiekopie ook gebruiken om uw web-app uit te voeren op een toepassingsstack die nog niet in Azure is gedefinieerd. In deze quickstart ziet u hoe u een installatiekopie van een [Azure Container Registry](../container-registry/index.yml) (ACR) implementeert in App Service.

## <a name="prerequisites"></a>Vereisten

* Een [Azure-account](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* De [Azure App Service-extensie voor VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). U kunt deze extensie gebruiken voor het maken, beheren en implementeren van Linux-web-apps in het Platform-as-a-Service van Azure (PaaS).
* De [Docker-extensie voor VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker). U kunt deze extensie gebruiken om het beheer te vereenvoudigen van lokale Docker-installatiekopieën en -opdrachten en om installatiekopieën van gebouwde apps te implementeren in Azure.

## <a name="create-an-image"></a>Een installatiekopie maken

Voor het voltooien van deze quickstart hebt u een geschikte installatiekopie nodig van een web-app die is opgeslagen in een [Azure Container Registry](../container-registry/index.yml). Volg de instructies in [Quickstart: Een privécontainerregister maken met behulp van Azure Portal](../container-registry/container-registry-get-started-portal.md), maar gebruik de installatiekopie van `mcr.microsoft.com/azuredocs/go` in plaats van de installatiekopie van `hello-world`. Ter referentie: de [-voorbeeld-Dockerfile vindt u in de opslagplaats met Azure-voorbeelden](https://github.com/Azure-Samples/go-docs-hello-world).

> [!IMPORTANT]
> Zorg ervoor dat u de optie **Gebruiker met beheerdersrechten** instelt op **Inschakelen** wanneer u het containerregister maakt. U kunt deze optie ook instellen in de sectie **Toegangssleutels** van de registerpagina in Azure Portal. Deze instelling is vereist voor toegang tot App Service.

## <a name="sign-in"></a>Aanmelden

Vervolgens start u VS code en meldt u zich aan bij uw Azure-account met behulp van de extensie App Service. Hiertoe selecteert u het Azure-logo op de balk Activiteit, gaat u naar de verkenner van **APP SERVICE** en selecteert u **Aanmelden bij Azure** en volgt u de instructies.

![aanmelden bij Azure](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>Vereisten controleren

Nu kunt u controleren of alle vereiste onderdelen correct zijn geïnstalleerd en geconfigureerd.

In VS Code ziet u uw Azure-e-mailadres in de statusbalk, en uw abonnement in de verkenner van **APP SERVICE**.

Controleer vervolgens of Docker is geïnstalleerd en wordt uitgevoerd. Met de volgende opdracht wordt de Docker-versie weergegeven als Docker wordt uitgevoerd.

```bash
docker --version
```

Controleer tot slot of uw Azure Container Registry is verbonden. Hiertoe selecteert u het Docker-logo in de balk Activiteit en gaat u vervolgens naar **REGISTERS**.

![Schermopname van de waarde Registers, met Azure uitgevouwen en een bestand met de bestandsextensie punt i o.](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>De installatiekopie implementeren naar Azure App Service

Nu alles is geconfigureerd, kunt u uw installatiekopie rechtstreeks vanuit de Docker-extensieverkenner implementeren in [Azure App Service](https://azure.microsoft.com/services/app-service/).

Zoek de installatiekopie onder het knooppunt **Registers** in de verkenner van **DOCKER** en vouw deze uit om de tags ervan weer te geven. Klik met de rechtermuisknop op een tag en selecteer **Installatiekopie implementeren naar Azure App Service**.

Volg hier de aanwijzingen voor het kiezen van een abonnement, een globaal unieke app-naam, een resourcegroep en een App Service plan. Kies **B1 Basic** als prijscategorie, en kies een regio.

Na de implementatie is uw app beschikbaar op `http://<app name>.azurewebsites.net`.

Een **resourcegroep** is een benoemde verzameling van alle resources van uw toepassing in Azure. Een resourcegroep kan bijvoorbeeld een verwijzing naar een website, een database en een Azure-functie bevatten.

Een **App Service-plan** definieert de fysieke resources die worden gebruikt voor het hosten van uw website. In deze quickstart wordt gebruikgemaakt van een **Basic**-hostingsabonnement op **Linux**-infrastructuur, wat betekent dat de site wordt gehost op een Linux-computer naast andere websites. Als u begint met het **Basic**-abonnement, kunt u omhoog schalen met behulp van Azure Portal zodat u de enige site bent die op een computer wordt uitgevoerd.

## <a name="browse-the-website"></a>Door de website bladeren

Het venster **Uitvoer** wordt geopend tijdens de implementatie om de status van de bewerking aan te geven. Wanneer de bewerking is voltooid, zoekt u de app die u hebt gemaakt in de verkenner van **APP SERVICE**, klikt u erop met de rechtermuisknop en selecteert u **Door website bladeren** om de site in uw browser te openen.

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd, u hebt deze quickstart voltooid.

Bekijk hierna de andere Azure-extensies.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Azure CLI-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager-hulpprogramma’s](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

U kunt ze ook allemaal downloaden door het extensiepakket [Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) te installeren.

Bekijk andere resources:

> [!div class="nextstepaction"]
> [Een aangepaste container configureren](configure-custom-container.md)

::: zone-end
