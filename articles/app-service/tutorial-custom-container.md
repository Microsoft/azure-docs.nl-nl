---
title: 'Zelfstudie: Een aangepaste installatiekopie bouwen en uitvoeren in Azure App Service'
description: Een stapsgewijze handleiding voor het bouwen van een aangepaste Linux- of Windows-installatiekopie, het pushen van de installatiekopie naar Azure Container Registry en het implementeren van de installatiekopie in Azure App Service. Meer informatie over hoe u aangepaste implementatiesoftware naar App Service migreert in een aangepaste container.
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: msangapu
keywords: azure-app-service, web-app, linux, windows, docker, container
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-python, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 5d3a714230f0279bd68b39cd02624866b9b3bacf
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180510"
---
# <a name="migrate-custom-software-to-azure-app-service-using-a-custom-container"></a>Aangepaste software naar Azure App Service migreren met een aangepaste container

::: zone pivot="container-windows"  

[Azure App Service](overview.md) biedt vooraf gedefinieerde toepassingsstacks in Windows, zoals ASP.NET of Node.js, die worden uitgevoerd in IIS. Met de vooraf geconfigureerde Windows-omgeving wordt het besturingssysteem vergrendeld voor beheerderstoegang, software-installaties, wijzigingen aan de Global Assembly Cache, enzovoort. (Zie [Functionaliteit van besturingssystemen in Azure App Service](operating-system-functionality.md).) Als u echter een aangepaste Windows-container in App Service gebruikt, kunt u besturingssysteemwijzigingen aanbrengen die uw app nodig heeft, zodat het eenvoudig is om een on-premises app te migreren waarvoor aangepaste besturingssysteem- en softwareconfiguraties nodig zijn. Dit zelfstudie laat zien hoe u een ASP.NET-app naar App Service migreert die aangepaste lettertypen gebruikt die zijn geïnstalleerd in de Windows-lettertypenbibliotheek. U implementeert een aangepaste geconfigureerde Windows-installatiekopie van Visual Studio naar [Azure Container Registry](../container-registry/index.yml) en voert deze vervolgens uit in de App-service.

![Toont de web-app die wordt uitgevoerd in een Windows-container.](media/tutorial-custom-container/app-running.png)

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

- <a href="https://hub.docker.com/" target="_blank">Registreren voor een Docker Hub-account</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Docker voor Windows installeren</a>.
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Docker instellen voor het uitvoeren van Windows-containers</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 installeren</a> met de workloads **ASP.NET- en webontwikkeling** en **Azure-ontwikkeling**. Als u Visual Studio 2019 al hebt geïnstalleerd:
    - Installeer de nieuwste updates in Visual Studio door te klikken op **Help** > **Check for Updates**.
    - Voeg de werkbelastingen toe in Visual Studio door te klikken op **Tools** > **Get Tools and Features**.

## <a name="set-up-the-app-locally"></a>De app lokaal instellen

### <a name="download-the-sample"></a>Het voorbeeld downloaden

In deze stap stelt u het lokale .NET-project in.

- [Download het voorbeeldproject](https://github.com/Azure-Samples/custom-font-win-container/archive/master.zip).
- Pak het bestand *custom-font-win-container.zip* uit (unzip).

Het voorbeeldproject bevat een eenvoudige ASP.NET toepassing die een aangepast lettertype gebruikt dat in de Windows-lettertypebibliotheek is geïnstalleerd. Het is niet nodig om lettertypen te installeren, maar het is een voorbeeld van een app die is geïntegreerd met het onderliggende besturingssysteem. Om een ​​dergelijke app naar App Service te migreren, wijzigt u uw code om de integratie te verwijderen of migreert u de code zoals ze is in een aangepaste Windows-container.

### <a name="install-the-font"></a>Het lettertype installeren

Navigeer in Windows Verkenner naar _custom-font-win-container-master/CustomFontSample_, klik met de rechtermuisknop op _FrederickatheGreat-Regular.ttf_ en selecteer **Installeren**.

Dit lettertype is openbaar beschikbaar via [Google Fonts](https://fonts.google.com/specimen/Fredericka+the+Great).

### <a name="run-the-app"></a>De app uitvoeren

Open het bestand *custom-font-win-container/CustomFontSample.sln* in Visual Studio. 

Typ `Ctrl+F5` voor het uitvoeren van de app zonder foutopsporing. De app wordt weergegeven in de standaardbrowser. 

:::image type="content" source="media/tutorial-custom-container/local-app-in-browser.png" alt-text="Schermopname waarin de app wordt weergegeven in de standaardbrowser.":::

Omdat de app een geïnstalleerd lettertype gebruikt, kan deze niet worden uitgevoerd in de sandbox van App Service. U kunt de app echter in plaats daarvan implementeren met behulp van een Windows-container, omdat u het lettertype in de Windows-container kunt installeren.

### <a name="configure-windows-container"></a>Windows-container configureren

Klik in Solution Explorer met de rechtermuisknop op het project **CustomFontSample** en selecteer **Add** > **Container Orchestration Support**.

:::image type="content" source="media/tutorial-custom-container/enable-container-orchestration.png" alt-text="Schermopname van het Solution Explorer-venster waarin het project CustomFontSample en de menu-items Add en Container Orchestrator Support zijn geselecteerd.":::

Selecteer **Docker Compose** > **OK**.

Uw project is nu ingesteld om te worden uitgevoerd in een Windows-container. Er wordt een _Dockerfile_ toegevoegd aan het project **CustomFontSample**, en er wordt een **docker-compose**-project toegevoegd aan de oplossing. 

Open **Dockerfile** vanuit Solution Explorer.

U moet een [ondersteunde bovenliggende installatiekopie](configure-custom-container.md#supported-parent-images) gebruiken. Wijzig de bovenliggende installatiekopie door de regel `FROM` ​​te vervangen door de volgende code:

```dockerfile
FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
```

Voeg aan het einde van het bestand de volgende regel toe en sla het bestand op:

```dockerfile
RUN ${source:-obj/Docker/publish/InstallFont.ps1}
```

U kunt _InstallFont.ps1_ vinden in het project **CustomFontSample**. Het is een eenvoudig script waarmee het lettertype wordt geïnstalleerd. U vindt een complexere versie van het script in het [Script Center](https://gallery.technet.microsoft.com/scriptcenter/fb742f92-e594-4d0c-8b79-27564c575133).

> [!NOTE]
> Als u de Windows-container lokaal wilt testen, controleert u of Docker is gestart op de lokale computer.
>

## <a name="publish-to-azure-container-registry"></a>Publiceren naar Azure Container Registry

[Azure Container Registry](../container-registry/index.yml) kan uw afbeeldingen opslaan voor containerimplementaties. U kunt App Service configureren voor het gebruik van installatiekopieën die worden gehost in Azure Container Registry.

### <a name="open-publish-wizard"></a>Publicatie-wizard openen

Klik in Solution Explorer met de rechtermuisknop op het project **CustomFontSample** en selecteer **Publish** (publiceren).

:::image type="content" source="media/tutorial-custom-container/open-publish-wizard.png" alt-text="Schermopname van het Solution Explorer-venster waarin het project CustomFontSample en de optie Publish zijn geselecteerd.":::

### <a name="create-registry-and-publish"></a>Register maken en publiceren

Selecteer in de publicatiewizard **Container Registry** (containerregister) > **Create New Azure Container Registry** (nieuw Azure-containerregister maken) > **Publish** (publiceren).

:::image type="content" source="media/tutorial-custom-container/create-registry.png" alt-text="Schermopname van de wizard voor publiceren waarin de opties Container Registry, Create New Azure Container Registry en de knop Publish zijn geselecteerd.":::

### <a name="sign-in-with-azure-account"></a>Aanmelden met Azure-account

Selecteer **Add an account** (een account toevoegen) in het dialoogvenster **Create a new Azure Container Registry** (nieuw Azure-containerregister maken) en meld u vervolgens aan bij uw Azure-abonnement. Als u al bent aangemeld, selecteert u het account met het gewenste abonnement uit de vervolgkeuzelijst.

![Aanmelden bij Azure](./media/tutorial-custom-container/add-an-account.png)

### <a name="configure-the-registry"></a>Het register configureren

Configureer het nieuwe containerregister op basis van de voorgestelde waarden in de volgende tabel. Klik op **Create** als u klaar bent.

| Instelling  | Voorgestelde waarde | Voor meer informatie |
| ----------------- | ------------ | ----|
|**DNS-voorvoegsel**| Behoud de gegenereerde registernaam of wijzig deze in een andere unieke naam. |  |
|**Resourcegroep**| Klik op **New** (nieuw), typ **myResourceGroup** en klik op **OK**. |  |
|**SKU**| Basic | [Prijscategorieën](https://azure.microsoft.com/pricing/details/container-registry/)|
|**Registerlocatie**| Europa -west | |

![Azure Container Registry configureren](./media/tutorial-custom-container/configure-registry.png)

Er wordt een terminalvenster geopend waarin de voortgang van het implementeren van de installatiekopie wordt getoond. Wacht totdat de installatie is voltooid.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-web-app"></a>Een webtoepassing maken

Selecteer in het menu links **Create a resource** (een resource maken) > **Web** > **Web App for Containers**.

### <a name="configure-app-basics"></a>Basisprincipes van app configureren

Configureer op het tabblad **Basisprincipes** de instellingen volgens de volgende tabel en klik op **Volgende: Docker**.

| Instelling  | Voorgestelde waarde | Voor meer informatie |
| ----------------- | ------------ | ----|
|**Abonnement**| Zorg ervoor dat het correcte abonnement is geselecteerd. |  |
|**Resourcegroep**| Selecteer **Nieuwe maken**, typ **myResourceGroup** en klik op **OK**. |  |
|**Naam**| Typ een unieke naam. | De URL van de web-app is `http://<app-name>.azurewebsites.net`, waarbij `<app-name>` de naam van uw app is. |
|**Publiceren**| Docker-container | |
|**Besturingssysteem**| Windows | |
|**Regio**| Europa -west | |
|**Windows Plan**| Selecteer **Nieuwe** maken, typ **myAppServicePlan** en klik op **OK**. | |

Uw tabblad **Basisprincipes** moet er zo uitzien:

![Toont het tabblad Basics dat wordt gebruikt om de web-app te configureren.](media/tutorial-custom-container/configure-app-basics.png)

### <a name="configure-windows-container"></a>Windows-container configureren

Configureer op het tabblad **Docker** uw aangepaste Windows-container, zoals wordt weergegeven in de volgende tabel, en selecteer **Beoordelen + maken**.

| Instelling  | Voorgestelde waarde |
| ----------------- | ------------ |
|**Bron van installatiekopie**| Azure Container Register |
|**Register**| Selecteer [het register dat u eerder hebt gemaakt](#publish-to-azure-container-registry). |
|**Installatiekopie**| customfontsample |
|**Tag**| meest recente |

### <a name="complete-app-creation"></a>De app voltooien

Klik op **Maken** en wacht tot de vereiste resources zijn gemaakt in Azure.

## <a name="browse-to-the-web-app"></a>Bladeren naar de web-app

Als de bewerking in Azure is voltooid, wordt er een melding weergegeven.

![Toont dat de Azure-bewerking is voltooid.](media/tutorial-custom-container/portal-create-finished.png)

1. Klik op **Ga naar resource**.

2. Klik op de app-pagina onder **URL** op de koppeling.

Er wordt een nieuwe browserpagina geopend met de volgende pagina:

![Toont de nieuwe browserpagina voor de web-app.](media/tutorial-custom-container/app-starting.png)

Wacht een paar minuten en probeer het opnieuw, totdat u de startpagina krijgt met het mooie lettertype dat u verwacht:

![Hiermee wordt de startpagina weergegeven met het lettertype dat u hebt geconfigureerd.](media/tutorial-custom-container/app-running.png)

**Gefeliciteerd!** U hebt een ASP.NET toepassing gemigreerd naar Azure App Service in een Windows-container.

## <a name="see-container-start-up-logs"></a>Logboeken voor opstarten van containers bekijken

Het kan enige tijd duren voordat de Windows-container is geladen. Als u de voortgang wilt bekijken, gaat u naar de volgende URL en vervangt u *\<app-name>* door de naam van de app.
```
https://<app-name>.scm.azurewebsites.net/api/logstream
```

De gestreamde logboeken zien er ongeveer als volgt uit:

```
14/09/2018 23:16:19.889 INFO - Site: fonts-win-container - Creating container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest.
14/09/2018 23:16:19.928 INFO - Site: fonts-win-container - Create container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest succeeded. Container Id 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:23.405 INFO - Site: fonts-win-container - Start container succeeded. Container: 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Configuring container
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container start-up and configuration completed successfully
```

::: zone-end

::: zone pivot="container-linux"

Azure App Service gebruikt de Docker-containertechnologie voor het hosten van zowel ingebouwde installatiekopieën als aangepaste installatiekopieën. Voer de Azure CLI-opdracht [az webapp list-runtimes --linux](/cli/azure/webapp#az-webapp-list-runtimes) uit om een lijst met ingebouwde installatiekopieën te zien. Als deze installatiekopieën niet voldoen aan uw behoeften, kunt u een aangepaste installatiekopie bouwen en implementeren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een aangepaste installatiekopie bouwen als ingebouwde installatiekopieën niet voldoen aan uw behoeften
> * De aangepaste installatiekopie naar een privécontainerregister pushen in Azure
> * De aangepaste installatiekopie uitvoeren in App Service
> * Omgevingsvariabelen configureren
> * De installatiekopie bijwerken en opnieuw implementeren
> * Toegang tot diagnostische logboeken
> * Verbinding maken met de container via SSH

Voor het voltooien van deze zelfstudie kan een klein bedrag in rekening worden gebracht voor het containerregister, en er kunnen extra kosten in rekening worden gebracht als u de container langer dan een maand host.

## <a name="set-up-your-initial-environment"></a>Uw eerste omgeving instellen

- U moet beschikken over een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Installeer [Docker](https://docs.docker.com/get-started/#setup). Dit gebruikt u om Docker-installatiekopieën te bouwen. Voor het installeren van Docker moet de computer mogelijk opnieuw worden opgestart.
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
- Voor deze zelfstudie is versie 2.0.80 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

Nadat u Docker hebt geïnstalleerd of Azure Cloud Shell hebt uitgevoerd, opent u een terminalvenster en controleert u of Docker is geïnstalleerd:

```bash
docker --version
```

## <a name="clone-or-download-the-sample-app"></a>De voorbeeld-app klonen of downloaden

U kunt het voorbeeld voor deze zelfstudie verkrijgen via een git-kloon of download.

### <a name="clone-with-git"></a>Klonen met git

Kloon de voorbeeldopslagplaats:

```terminal
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
```

Zorg ervoor dat u het argument `--config core.autocrlf=input` opneemt om te zorgen voor de juiste regeleinden in bestanden die worden gebruikt binnen de Linux-container:

Ga vervolgens naar die map:

```terminal
cd docker-django-webapp-linux
```

### <a name="download-from-github"></a>Downloaden van GitHub

In plaats van de git-kloon te gebruiken kunt u naar [https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux) gaan, **Klonen** selecteren, en vervolgens **ZIP downloaden** selecteren. 

Pak het ZIP-bestand uit in een map met de naam *docker-django-webapp-linux*. 

Open vervolgens een terminalvenster in deze map *docker-django-webapp-linux*.

## <a name="optional-examine-the-docker-file"></a>Het Docker-bestand bekijken (optioneel)

Het bestand in het voorbeeld met de naam _Dockerfile_ dat de Docker-installatiekopie beschrijft en configuratie-instructies bevat:

```Dockerfile
FROM tiangolo/uwsgi-nginx-flask:python3.6

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt --no-cache-dir
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222

#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

* Met de eerste groep opdrachten worden de vereisten van de app geïnstalleerd in de omgeving.
* Met de tweede groep opdrachten wordt een [SSH](https://www.ssh.com/ssh/protocol/)-server gemaakt voor veilige communicatie tussen de container en de host.
* Met de laatste regel, `ENTRYPOINT ["init.sh"]`, wordt `init.sh` aangeroepen om de SSH-service en Python-server te starten.

## <a name="build-and-test-the-image-locally"></a>De installatiekopie lokaal bouwen en testen

> [!NOTE]
> Docker Hub heeft [quota voor het aantal anonieme pulls per IP-adres en het aantal geverifieerde pulls per gratis gebruiker (zie **Gegevensoverdracht**)](https://www.docker.com/pricing). Als u merkt dat er beperkingen gelden voor uw pulls uit Docker Hub, probeer dan `docker login` als u nog niet bent aangemeld.
> 

1. Voer de volgende opdracht uit om de installatiekopie te bouwen:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```
    
1. Test of de build werkt door de Docker-container lokaal uit te voeren:

    ```bash
    docker run -p 8000:8000 appsvc-tutorial-custom-image
    ```
    
    Met deze opdracht [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) wordt de poort opgegeven met het argument `-p`, gevolgd door de naam van de installatiekopie. 
    
    > [!TIP]
    > Als u uitvoert in Windows en deze fout ziet: *standard_init_linux.go:211: exec user process caused ‘bestand of map bestaat niet’* , bevat het *init.sh*-bestand CR-LF-regeleinden in plaats van de verwachte LF-einden. Deze fout treedt op als u de git hebt gebruikt om de voorbeeldopslagplaats te klonen, maar de parameter `--config core.autocrlf=input` hebt weggelaten. In dit geval moet u de opslagplaats opnieuw klonen met het argument --config. U ziet de fout mogelijk ook als u *init.sh* hebt bewerkt en vervolgens hebt opgeslagen met CRLF-einden. In dit geval slaat u het bestand opnieuw op met alleen LF-einden.

1. Blader naar `http://localhost:8000` om te controleren of de web-app en de container goed werken.

    ![Web-app lokaal testen](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="create-a-resource-group"></a>Een resourcegroep maken

In deze en de volgende secties richt u resources in Azure in waarnaar u de installatiekopie pusht. Vervolgens implementeert u een container in Azure App Service. U begint met het maken van een resourcegroep waarin u al deze resources kunt verzamelen.

Voer de opdracht [az group create](/cli/azure/group#az-group-create) uit om een resourcegroep te maken:

```azurecli-interactive
az group create --name AppSvc-DockerTutorial-rg --location westus2
```

U kunt de waarde `--location` wijzigen om een regio bij u in de buurt op te geven.

## <a name="push-the-image-to-azure-container-registry"></a>De installatiekopie naar Azure Container Registry pushen

In deze sectie pusht u de installatiekopie naar Azure Container Registry, waar deze kan worden geïmplementeerd via App Service.

1. Voer de opdracht [`az acr create`](/cli/azure/acr#az-acr-create) uit om een Azure Container Registry te maken:

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group AppSvc-DockerTutorial-rg --sku Basic --admin-enabled true
    ```
    
    Vervang `<registry-name>` door een geschikte naam voor het register. De naam moet uniek zijn in Azure en mag alleen letters en cijfers bevatten.

1. Voer de opdracht [`az acr show`](/cli/azure/acr#az-acr-show) uit om referenties voor het register op te halen:

    ```azurecli-interactive
    az acr credential show --resource-group AppSvc-DockerTutorial-rg --name <registry-name>
    ```
    
    De JSON-uitvoer van deze opdracht biedt twee wachtwoorden samen met de gebruikersnaam van het register.
    
1. Gebruik de opdracht `docker login` om u aan te melden bij het containerregister:

    ```bash
    docker login <registry-name>.azurecr.io --username <registry-username>
    ```
    
    Vervang `<registry-name>` en `<registry-username>` door waarden uit de vorige stappen. Wanneer u hierom wordt gevraagd, voert u een van de wachtwoorden uit de vorige stap in.

    U gebruikt dezelfde registernaam in alle resterende stappen van deze sectie.

1. Als de aanmelding is geslaagd, labelt u de lokale Docker-installatiekopie voor het register:

    ```bash
   docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```    

1. Gebruik de opdracht `docker push` om de installatiekopie naar het register te pushen:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    De eerste keer dat u de installatiekopie uploadt, kan dit enkele minuten duren, omdat deze upload de basisinstallatiekopie bevat. De volgende uploads zijn doorgaans sneller.

    Terwijl u wacht, kunt u de stappen in de volgende sectie voltooien om App Service te configureren voor implementatie vanuit het register.

1. Gebruik de opdracht `az acr repository list` om te controleren of de push is geslaagd:

    ```azurecli-interactive
    az acr repository list -n <registry-name>
    ```
    
    In de uitvoer moet de naam van de installatiekopie te zien zijn.


## <a name="configure-app-service-to-deploy-the-image-from-the-registry"></a>App Service configureren voor implementatie van de installatiekopie vanuit het register

Als u een container wilt implementeren in Azure App Service, maakt u eerst een web-app in App Service. Vervolgens verbindt u de web-app met het containerregister. Wanneer de web-app start, wordt de installatiekopie met App Service automatisch opgehaald uit het register.

1. Maak een App Service-abonnement met behulp van de opdracht [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create):

    ```azurecli-interactive
    az appservice plan create --name AppSvc-DockerTutorial-plan --resource-group AppSvc-DockerTutorial-rg --is-linux
    ```

    Een App Service-abonnement komt overeen met de virtuele machine die als host fungeert voor de web-app. De vorige opdracht maakt standaard gebruik van een goedkope [B1-prijscategorie](https://azure.microsoft.com/pricing/details/app-service/linux/) die de eerste maand gratis is. U kunt de categorie beheren met de parameter `--sku`.

1. Maak de web-app met de opdracht [`az webpp create`](/cli/azure/webapp#az-webapp-create):

    ```azurecli-interactive
    az webapp create --resource-group AppSvc-DockerTutorial-rg --plan AppSvc-DockerTutorial-plan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    Vervang `<app-name>` door een naam voor de web-app. Deze moet uniek zijn in Azure. Vervang ook `<registry-name>` door de naam van uw register uit de vorige sectie.

1. Gebruik [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) om de omgevingsvariabele `WEBSITES_PORT` in te stellen zoals verwacht voor de app-code: 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group AppSvc-DockerTutorial-rg --name <app-name> --settings WEBSITES_PORT=8000
    ```

    Vervang `<app-name>` door de naam die u in de vorige stap hebt gebruikt.
    
    Raadpleeg de [Leesmij in de voorbeeldopslagplaats van GitHub](https://github.com/Azure-Samples/docker-django-webapp-linux) voor meer informatie over deze omgevingsvariabele.

1. Schakel [beheerde identiteit](./overview-managed-identity.md) in voor de web-app door de opdracht [`az webapp identity assign`](/cli/azure/webapp/identity#az-webapp-identity-assign) te gebruiken:

    ```azurecli-interactive
    az webapp identity assign --resource-group AppSvc-DockerTutorial-rg --name <app-name> --query principalId --output tsv
    ```

    Vervang `<app-name>` door de naam die u in de vorige stap hebt gebruikt. De uitvoer van de opdracht (gefilterd op de argumenten `--query` en `--output`) is de service-principal van de toegewezen identiteit. Deze gebruikt u zo meteen.

    Met beheerde identiteit kunt u de web-app toegang verlenen tot andere Azure-resources zonder dat hiervoor specifieke referenties nodig zijn.

1. Haal uw abonnements-id op met de opdracht [`az account show`](/cli/azure/account#az-account-show), die u in de volgende stap nodig hebt:

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. Verleen de web-app toegang tot het containerregister:

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/AppSvc-DockerTutorial-rg/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    Vervang de volgende waarden:
    - `<principal-id>` door de service-principal-id uit de opdracht `az webapp identity assign`
    - `<registry-name>` door de naam van het containerregister
    - `<subscription-id>` door de abonnements-id die is opgehaald uit de opdracht `az account show`

Zie voor meer informatie over deze machtigingen [Op rollen gebaseerd toegangsbeheer in Azure](../role-based-access-control/overview.md) en 

## <a name="deploy-the-image-and-test-the-app"></a>De installatiekopie implementeren en de app testen

U kunt deze stappen voltooien zodra de installatiekopie naar het containerregister is gepusht en de App Service volledig is ingericht.

1. Gebruik de opdracht [`az webapp config container set`](/cli/azure/webapp/config/container#az-webapp-config-container-set) om het containerregister en de installatiekopie op te geven die voor de web-app moeten worden geïmplementeerd:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-custom-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest --docker-registry-server-url https://<registry-name>.azurecr.io
    ```
    
    Vervang `<app_name>` door de naam van de web-app en vervang `<registry-name>` op twee plekken door de naam van het register. 

    - Wanneer u een ander register dan Docker Hub gebruikt (zoals weergegeven in dit voorbeeld), moet `--docker-registry-server-url` worden opgemaakt als `https://`, gevolgd door de Fully Qualified Domain Name van het register.
    - Het bericht 'Geen referentie opgegeven om toegang te krijgen tot Azure Container Registry. Proberen op te zoeken...' geeft aan dat in Azure de beheerde identiteit van de app wordt gebruikt voor verificatie bij het containerregister, in plaats van dat om een gebruikersnaam en wachtwoord wordt gevraagd.
    - Als u deze fout ziet: ‘AttributeError: Object "NoneType" heeft geen kenmerk "gereserveerd".’ Controleert u of uw `<app-name>` juist is.

    > [!TIP]
    > U kunt de containerinstellingen van de web-app op elk gewenst moment ophalen met de opdracht `az webapp config container show --name <app-name> --resource-group AppSvc-DockerTutorial-rg`. De installatiekopie wordt opgegeven in de eigenschap `DOCKER_CUSTOM_IMAGE_NAME`. Wanneer de web-app is geïmplementeerd via Azure DevOps- of Azure Resource Manager-sjablonen, kan de installatiekopie ook worden weergegeven in een eigenschap met de naam `LinuxFxVersion`. Beide eigenschappen hebben hetzelfde doel. Als beide aanwezig zijn in de configuratie van de web-app, heeft `LinuxFxVersion` prioriteit.

1. Zodra de opdracht `az webapp config container set` is voltooid, moet de web-app worden uitgevoerd in de container in App Service.

    Als u de app wilt testen, bladert u naar `http://<app-name>.azurewebsites.net`, waarbij u `<app-name>` vervangt door de naam van de web-app. Als u de app de eerste keer opent, kan het enige tijd duren voordat de app reageert, omdat App Service de hele installatiekopie moet ophalen uit het register. Als er een time-out optreedt voor de browser, vernieuwt u gewoon de pagina. Zodra de eerste installatiekopie is opgehaald, verlopen volgende tests veel sneller.

    ![Geslaagde test van de web-app in Azure](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="modify-the-app-code-and-redeploy"></a>De app-code wijzigen en opnieuw implementeren

In deze sectie brengt u een wijziging aan in de code van de web-app, bouwt u de container opnieuw, en pusht u de container naar het register. In App Service wordt vervolgens automatisch de bijgewerkte installatiekopie opgehaald uit het register om de actieve web-app bij te werken.

1. Open in uw lokale map *docker-django-webapp-linux* het bestand *app/templates/app/index.html*.

1. Wijzig eerst het HTML-element om overeen te komen met de volgende code.

    ```html
    <nav class="navbar navbar-inverse navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
        </div>
      </div>
    </nav>
    ```
    
1. Sla uw wijzigingen op.

1. Ga naar de map *docker-django-webapp-linux* en bouw de installatiekopie opnieuw:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```

1. Werk het versienummer van de installatiekopietag bij naar v1.0.1:

    ```bash
    docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Vervang `<registry-name>` door de naam van uw register.

1. Push de installatiekopie naar het register:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

1. Start de web-app opnieuw:

    ```azurecli-interactive
    az webapp restart --name <app_name> --resource-group AppSvc-DockerTutorial-rg
    ```

    Vervang `<app_name>` door de naam van de web-app. Bij het opnieuw opstarten wordt met App Service de bijgewerkte installatiekopie opgehaald uit het containerregister.

1. Controleer of de update is geïmplementeerd, door naar `http://<app-name>.azurewebsites.net` te bladeren.

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

1. Schakel logboekregistratie voor container in:

    ```azurecli-interactive
    az webapp log config --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-container-logging filesystem
    ```
    
1. Schakel de logboekstream in:

    ```azurecli-interactive
    az webapp log tail --name <app-name> --resource-group AppSvc-DockerTutorial-rg
    ```
    
    Als u de consolelogboeken niet meteen ziet, probeert u het opnieuw na 30 seconden.

    U kunt ook de logboekbestanden van de browser inspecteren op `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

1. U kunt op elk gewenst moment stoppen met logboekstreaming door **Ctrl**+**C** te typen.

## <a name="connect-to-the-container-using-ssh"></a>Verbinding maken met de container via SSH

SSH maakt veilige communicatie tussen een container en een client mogelijk. Als u een SSH-verbinding met uw container wilt inschakelen, moet uw aangepaste installatiekopie hiervoor worden geconfigureerd. Als de container eenmaal wordt uitgevoerd, kunt u een SSH-verbinding openen.

### <a name="configure-the-container-for-ssh"></a>De container configureren voor SSH

De voorbeeld-app die in deze zelfstudie wordt gebruikt, beschikt al over de benodigde configuratie in het *Dockerfile*, waarmee de SSH-server wordt geïnstalleerd en de aanmeldingsreferenties worden ingesteld. Deze sectie is alleen ter informatie. Ga naar de volgende sectie om verbinding te maken met de container

```Dockerfile
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
  && apt-get install -y --no-install-recommends openssh-server \
  && echo "$SSH_PASSWD" | chpasswd 
```

> [!NOTE]
> Deze configuratie staat geen externe verbindingen naar de container toe. SSH is alleen beschikbaar via de Kudu/SCM-Site. De Kudu/SCM-site wordt geverifieerd met uw Azure-account.

Met het *Dockerfile* wordt ook het bestand *sshd_config* gekopieerd naar de map */etc/ssh/* , en wordt poort 2222 zichtbaar in de container:

```Dockerfile
COPY sshd_config /etc/ssh/

# ...

EXPOSE 8000 2222
```

Poort 2222 is interne poort die alleen toegankelijk is voor containers in het brugnetwerk van een virtueel particulier netwerk. 

Ten slotte wordt de SSH-server gestart met het invoerscript *init.sh*.

```bash
#!/bin/bash
service ssh start
```

### <a name="open-ssh-connection-to-container"></a>SSH-verbinding met de container openen

1. Blader naar `https://<app-name>.scm.azurewebsites.net/webssh/host` en meld u aan met uw Azure-account. Vervang `<app-name>` door de naam van de web-app.

1. Wanneer u bent aangemeld, wordt u omgeleid naar een informatiepagina voor de web-app. Selecteer bovenaan de pagina **SSH** om de shell te openen en opdrachten te gebruiken.

    U kunt bijvoorbeeld de processen die in de shell worden uitgevoerd, controleren met behulp van de opdracht `top`.
    
## <a name="clean-up-resources"></a>Resources opschonen

Voor de resources die u in dit artikel hebt gemaakt, kunnen lopende kosten in rekening worden gebracht. Als u de resources wilt opschonen, hoeft u alleen de resourcegroep te verwijderen die ze bevat:

```azurecli
az group delete --name AppSvc-DockerTutorial-rg
```

::: zone-end

## <a name="next-steps"></a>Volgende stappen

Wat u hebt geleerd:

> [!div class="checklist"]
> * Een aangepaste installatiekopie in een privécontainerregister implementeren
> * De aangepaste installatiekopie implementeren in App Service
::: zone pivot="container-linux"
> * De installatiekopie bijwerken en opnieuw implementeren
::: zone-end
> * Toegang tot diagnostische logboeken
::: zone pivot="container-linux"
> * Verbinding maken met de container via SSH
::: zone-end

In de volgende zelfstudie leer u hoe u een aangepaste DNS-naam kunt toewijzen aan uw app.

> [!div class="nextstepaction"]
> [Zelfstudie: Een aangepaste DNS-naam aan uw app toewijzen](app-service-web-tutorial-custom-domain.md)

U kunt ook andere resources bekijken:

> [!div class="nextstepaction"]
> [Een aangepaste container configureren](configure-custom-container.md)

::: zone pivot="container-linux"
> [!div class="nextstepaction"]
> [Zelfstudie: WordPress-app met meerdere containers](tutorial-multi-container-app.md)
::: zone-end
