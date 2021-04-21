---
title: Een aangepaste container configureren
description: Meer informatie over het configureren van een aangepaste container in Azure App Service. In dit artikel worden de meest algemene configuratietaken beschreven.
ms.topic: article
ms.date: 02/23/2021
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 7bfebe318d93a544c964d70ea0a28144a7f0e43b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764239"
---
# <a name="configure-a-custom-container-for-azure-app-service"></a>Een aangepaste container configureren voor Azure App Service

In dit artikel wordt beschreven hoe u een aangepaste container configureert om te worden uitgevoerd op Azure App Service.

::: zone pivot="container-windows"

Deze handleiding bevat belangrijke concepten en instructies voor containerisatie van Windows-apps in App Service. Als u deze zelfstudie nog nooit Azure App Service, volgt u eerst de [quickstart](quickstart-custom-container.md) en [zelfstudie](tutorial-custom-container.md) voor aangepaste containers.

::: zone-end

::: zone pivot="container-linux"

Deze handleiding bevat belangrijke concepten en instructies voor containerisatie van Linux-apps in App Service. Als u deze zelfstudie nog nooit Azure App Service, volgt u eerst de [quickstart](quickstart-custom-container.md) en [zelfstudie](tutorial-custom-container.md) voor aangepaste containers. Er is ook een [quickstart en](quickstart-multi-container.md) zelfstudie voor een app met meerdere [containers.](tutorial-multi-container-app.md)

::: zone-end

::: zone pivot="container-windows"

## <a name="supported-parent-images"></a>Ondersteunde bovenliggende afbeeldingen

Voor uw aangepaste Windows-afbeelding moet u de juiste [bovenliggende afbeelding (basisafbeelding)](https://docs.docker.com/develop/develop-images/baseimages/) kiezen voor het framework dat u wilt:

- Als u .NET Framework apps wilt implementeren, gebruikt u een bovenliggende afbeelding op basis van de release van Windows Server Core [Long-Term Servicing Channel (LTSC).](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) 
- Als u .NET Core-apps wilt implementeren, gebruikt u een bovenliggende installatie afbeelding op basis van de release Windows Server Nano [Semi-Annual Servicing Channel (SAC).](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) 

Het duurt enige tijd om een bovenliggende installatiekopie te downloaden tijdens het opstarten van de app. U kunt deze opstarttijd echter verminderen door een van de volgende bovenliggende installatiekopieën te gebruiken die al in cache zijn opgeslagen in Azure App Service:

- [](https://hub.docker.com/_/microsoft-windows-servercore)mcr.microsoft.com/windows/servercore:2004
- [mcr.microsoft.com/windows/servercore](https://hub.docker.com/_/microsoft-windows-servercore):ltsc2019
- [](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/)mcr.microsoft.com/dotnet/framework/aspnet:4.8-windowsservercore-2004
- [](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/)mcr.microsoft.com/dotnet/framework/aspnet:4.8-windowsservercore-ltsc2019
- [](https://hub.docker.com/_/microsoft-dotnet-core-runtime/)mcr.microsoft.com/dotnet/core/runtime:3.1-nanoserver-2004
- [](https://hub.docker.com/_/microsoft-dotnet-core-runtime/)mcr.microsoft.com/dotnet/core/runtime:3.1-nanoserver-1909
- [](https://hub.docker.com/_/microsoft-dotnet-core-runtime/)mcr.microsoft.com/dotnet/core/runtime:3.1-nanoserver-1903
- [](https://hub.docker.com/_/microsoft-dotnet-core-runtime/)mcr.microsoft.com/dotnet/core/runtime:3.1-nanoserver-1809
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-2004
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-1909
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-1903
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-1809

::: zone-end

## <a name="change-the-docker-image-of-a-custom-container"></a>De Docker-afbeelding van een aangepaste container wijzigen

Gebruik de volgende opdracht om een bestaande aangepaste container-app te wijzigen van de huidige Docker-afbeelding in een nieuwe afbeelding:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>Een afbeelding uit een privéregister gebruiken

Als u een afbeelding uit een persoonlijk register wilt gebruiken, zoals Azure Container Registry, moet u de volgende opdracht uitvoeren:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

Voor *\<username>* en levert u de *\<password>* aanmeldingsreferenties voor uw privéregisteraccount.

## <a name="i-dont-see-the-updated-container"></a>Ik zie de bijgewerkte container niet

Als u de instellingen van uw Docker-container wijzigt om naar een nieuwe container te wijzen, kan het enkele minuten duren voordat de app HTTP-aanvragen van de nieuwe container afwerkt. Terwijl de nieuwe container wordt aangetrokken en gestart, App Service aanvragen van de oude container blijven verwerken. Alleen wanneer de nieuwe container is gestart en gereed is om aanvragen te ontvangen, App Service aanvragen naar de container verzenden.

## <a name="how-container-images-are-stored"></a>Hoe containerafbeeldingen worden opgeslagen

De eerste keer dat u een aangepaste Docker-afbeelding in een App Service, App Service een en worden alle lagen van `docker pull` de afbeelding pullt. Deze lagen worden op schijf opgeslagen, bijvoorbeeld als u On-premises Docker gebruikt. Telkens als de app opnieuw wordt opgestart, App Service een , maar alleen lagen die `docker pull` zijn gewijzigd. Als er geen wijzigingen zijn aangebracht, App Service bestaande lagen op de lokale schijf gebruikt.

Als de app om welke reden dan ook reken-exemplaren wijzigt, zoals het omhoog en omlaag schalen van de prijslagen, moeten App Service alle lagen weer omlaag halen. Hetzelfde geldt als u uitschaalt om extra exemplaren toe te voegen. Er zijn ook zeldzame gevallen waarin de app-exemplaren zonder schaalbewerking kunnen worden gewijzigd.

## <a name="configure-port-number"></a>Poortnummer configureren

Standaard wordt App Service dat uw aangepaste container luistert op poort 80. Als uw container naar een andere poort luistert, stelt u de `WEBSITES_PORT` app-instelling in uw App Service app in. U kunt deze instellen via de [Cloud Shell](https://shell.azure.com). In Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_PORT"="8000"}
```

App Service kan uw container momenteel slechts één poort beschikbaar maken voor HTTP-aanvragen. 

## <a name="configure-environment-variables"></a>Omgevingsvariabelen configureren

Uw aangepaste container kan omgevingsvariabelen gebruiken die extern moeten worden opgegeven. U kunt deze doorgeven via de [Cloud Shell.](https://shell.azure.com) In Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings DB_HOST="myownserver.mysql.database.azure.com"
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"DB_HOST"="myownserver.mysql.database.azure.com"}
```

Wanneer uw app wordt uitgevoerd, worden App Service app-instellingen automatisch als omgevingsvariabelen in het proces geïnjecteerd. U kunt containeromgevingsvariabelen controleren met de URL `https://<app-name>.scm.azurewebsites.net/Env)` .

Als uw app gebruikmaakt van afbeeldingen uit een persoonlijk register of van Docker Hub, worden referenties voor toegang tot de opslagplaats opgeslagen in omgevingsvariabelen: `DOCKER_REGISTRY_SERVER_URL` , `DOCKER_REGISTRY_SERVER_USERNAME` en `DOCKER_REGISTRY_SERVER_PASSWORD` . Vanwege beveiligingsrisico's wordt geen van deze gereserveerde variabelenamen zichtbaar voor de toepassing.

::: zone pivot="container-windows"
Voor containers op basis van IIS of .NET Framework (4.0 of hoger) worden ze automatisch als .NET-app-instellingen en verbindingsreeksen `System.ConfigurationManager` App Service. Voor alle andere taal of frameworks worden ze geleverd als omgevingsvariabelen voor het proces, met een van de volgende bijbehorende voorvoegsels:

- `APPSETTING_`
- `SQLCONTR_`
- `MYSQLCONTR_`
- `SQLAZURECOSTR_`
- `POSTGRESQLCONTR_`
- `CUSTOMCONNSTR_`

::: zone-end

::: zone pivot="container-linux"

Deze methode werkt zowel voor apps met één container als voor apps met meerdere containers, waarbij de omgevingsvariabelen zijn opgegeven in het bestand *docker-compose.yml.*

::: zone-end

## <a name="use-persistent-shared-storage"></a>Permanente gedeelde opslag gebruiken

::: zone pivot="container-windows"

U kunt de *map C:\home* in het bestandssysteem van uw app gebruiken om bestanden bij het opnieuw opstarten persistent te maken en deze te delen tussen exemplaren. De `C:\home` in uw app wordt geleverd om uw container-app toegang te geven tot permanente opslag.

Wanneer permanente opslag is uitgeschakeld, worden schrijf schrijffuncties naar de `C:\home` map niet persistent gemaakt. [Docker-hostlogboeken en containerlogboeken](#access-diagnostic-logs) worden opgeslagen in een standaard permanente gedeelde opslag die niet is gekoppeld aan de container. Wanneer permanente opslag is ingeschakeld, blijven alle schrijfgegevens naar de map persistent en zijn ze toegankelijk voor alle exemplaren van een uitschaalbare app en zijn `C:\home` logboeken toegankelijk op `C:\home\LogFiles` .

::: zone-end

::: zone pivot="container-linux"

U kunt de *map /home* in het bestandssysteem van uw app gebruiken om bestanden bij het opnieuw opstarten persistent te maken en deze te delen tussen exemplaren. De `/home` in uw app wordt geleverd om uw container-app toegang te geven tot permanente opslag.

Wanneer permanente opslag is uitgeschakeld, worden schrijf schrijffuncties naar de map niet persistent gemaakt tijdens het opnieuw opstarten van apps of `/home` tussen meerdere exemplaren. De enige uitzondering hierop is `/home/LogFiles` de map , die wordt gebruikt voor het opslaan van de Docker- en containerlogboeken. Wanneer permanente opslag is ingeschakeld, blijven alle schrijf schrijfingen naar de map persistent en zijn ze toegankelijk voor alle exemplaren van een `/home` geschaalde app.

::: zone-end

Permanente opslag is standaard uitgeschakeld en de instelling wordt niet weergegeven in de app-instellingen. Als u deze wilt inschakelen, stelt u `WEBSITES_ENABLE_APP_SERVICE_STORAGE` de app-instelling in via [Cloud Shell](https://shell.azure.com). In Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=true}
```

> [!NOTE]
> U kunt ook [uw eigen permanente opslag configureren.](configure-connect-to-azure-storage.md)

## <a name="detect-https-session"></a>HTTPS-sessie detecteren

App Service beëindigt TLS/SSL aan de front-ends. Dit betekent dat TLS/SSL-aanvragen nooit bij uw app komen. U hoeft dit niet te doen en mag geen ondersteuning voor TLS/SSL implementeren in uw app. 

De front-ends bevinden zich in Azure-datacenters. Als u TLS/SSL gebruikt voor uw app, wordt uw verkeer via internet altijd veilig versleuteld.

::: zone pivot="container-windows"

## <a name="customize-aspnet-machine-key-injection"></a>Een ASP.NET machinesleutelinjectie aanpassen

 Tijdens het starten van de container worden automatisch gegenereerde sleutels in de container geïnjecteerd als de machinesleutels voor ASP.NET cryptografische routines. U vindt [deze sleutels in uw container door](#connect-to-the-container) te zoeken naar de volgende omgevingsvariabelen: , , , `MACHINEKEY_Decryption` `MACHINEKEY_DecryptionKey` `MACHINEKEY_ValidationKey` `MACHINEKEY_Validation` . 

De nieuwe sleutels bij elke herstart kunnen opnieuw worden ASP.NET formulierverificatie en weergavetoestand, als uw app er afhankelijk van is. Als u wilt voorkomen dat sleutels automatisch opnieuw worden gebruikt, stelt u deze handmatig [in App Service app-instellingen.](#configure-environment-variables) 

## <a name="connect-to-the-container"></a>Verbinding maken met de container

U kunt rechtstreeks verbinding maken met uw Windows-container voor diagnostische taken door naar te `https://<app-name>.scm.azurewebsites.net/DebugConsole` navigeren. Het werkt als volgt:

- Met de console voor foutopsporing kunt u interactieve opdrachten uitvoeren, zoals het starten van PowerShell-sessies, het inspecteren van registersleutels en het navigeren door het hele containerbestandssysteem.
- Het werkt afzonderlijk van de grafische browser erboven, waarin alleen de bestanden in uw gedeelde [opslag worden weergegeven.](#use-persistent-shared-storage)
- In een uitschaalde app is de console voor foutopsporing verbonden met een van de container-exemplaren. U kunt een ander exemplaar selecteren in de **vervolgkeuzelijst Instantie** in het bovenste menu.
- Wijzigingen die u in de container  aan de console aan brengen, blijven niet behouden wanneer uw app opnieuw wordt gestart (met uitzondering van wijzigingen in de gedeelde opslag), omdat deze geen deel uitmaakt van de Docker-afbeelding. Als u uw wijzigingen wilt blijven aanbrengen, zoals registerinstellingen en software-installatie, maakt u ze deel uit van het Dockerfile.

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

App Service registreert acties van de Docker-host en activiteiten vanuit de container. Logboeken van de Docker-host (platformlogboeken) worden standaard verzonden, maar toepassingslogboeken of webserverlogboeken vanuit de container moeten handmatig worden ingeschakeld. Zie Logboekregistratie van toepassingen [inschakelen](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) en Logboekregistratie [van webserver inschakelen voor meer informatie.](troubleshoot-diagnostic-logs.md#enable-web-server-logging) 

Er zijn verschillende manieren om toegang te krijgen tot Docker-logboeken:

- [In Azure Portal](#in-azure-portal)
- [Vanuit de Kudu-console](#from-the-kudu-console)
- [Met de Kudu-API](#with-the-kudu-api)
- [Logboeken verzenden naar Azure Monitor](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)

### <a name="in-azure-portal"></a>In Azure Portal

Docker-logboeken worden weergegeven in de portal op de **pagina Containerinstellingen** van uw app. De logboeken worden afgekapt, maar u kunt alle logboeken downloaden door op **Downloaden te klikken.** 

### <a name="from-the-kudu-console"></a>Vanuit de Kudu-console

Navigeer `https://<app-name>.scm.azurewebsites.net/DebugConsole` naar en klik op de map **LogFiles** om de afzonderlijke logboekbestanden te bekijken. Als u de volledige **Map LogFiles wilt** downloaden, klikt u op **het pictogram Downloaden** links van de mapnaam. U kunt deze map ook openen met behulp van een FTP-client.

In de consoleterminal hebt u standaard geen toegang tot de map `C:\home\LogFiles` omdat permanente gedeelde opslag niet is ingeschakeld. Als u dit gedrag wilt inschakelen in de consoleterminal, [moet u permanente gedeelde opslag inschakelen.](#use-persistent-shared-storage)

Als u het Docker-logboek probeert te downloaden dat momenteel wordt gebruikt met behulp van een FTP-client, kan er een foutmelding worden weergegeven vanwege een bestandsvergrendeling.

### <a name="with-the-kudu-api"></a>Met de Kudu-API

Navigeer rechtstreeks `https://<app-name>.scm.azurewebsites.net/api/logs/docker` naar om de metagegevens voor de Docker-logboeken te bekijken. Er wordt mogelijk meer dan één logboekbestand weergegeven en met de eigenschap `href` kunt u het logboekbestand rechtstreeks downloaden. 

Als u alle logboeken samen in één ZIP-bestand wilt downloaden, gaat u naar `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip` .

## <a name="customize-container-memory"></a>Containergeheugen aanpassen

Standaard zijn alle Windows-containers die zijn geïmplementeerd in Azure App Service beperkt tot 1 GB RAM-geheugen. U kunt deze waarde wijzigen door de `WEBSITE_MEMORY_LIMIT_MB` app-instelling op te geven via [de Cloud Shell](https://shell.azure.com). In Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_MEMORY_LIMIT_MB=2000
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_MEMORY_LIMIT_MB"=2000}
```

De waarde is gedefinieerd in MB en moet kleiner en gelijk zijn aan het totale fysieke geheugen van de host. In een abonnement met App Service 8 GB RAM mag het cumulatieve totaal van voor alle apps bijvoorbeeld niet groter `WEBSITE_MEMORY_LIMIT_MB` zijn dan 8 GB. Informatie over hoeveel geheugen beschikbaar is voor elke prijscategorie vindt u [in](https://azure.microsoft.com/pricing/details/app-service/windows/)App Service prijsinformatie in de sectie Premium **Container (Windows)-abonnement.**

## <a name="customize-the-number-of-compute-cores"></a>Het aantal rekenkernen aanpassen

Standaard wordt een Windows-container uitgevoerd met alle beschikbare kernen voor de gekozen prijscategorie. U kunt bijvoorbeeld het aantal kernen verminderen dat door uw staging-sleuf wordt gebruikt. Als u het aantal kernen wilt verminderen dat door een container wordt gebruikt, stelt u de `WEBSITE_CPU_CORES_LIMIT` app-instelling in op het gewenste aantal kernen. U kunt deze instellen via de [Cloud Shell](https://shell.azure.com). In Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --slot staging --settings WEBSITE_CPU_CORES_LIMIT=1
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_CPU_CORES_LIMIT"=1}
```

> [!NOTE]
> Het bijwerken van de app-instelling activeert automatisch opnieuw opstarten, wat minimale downtime tot gevolg heeft. Voor een productie-app kunt u overwegen deze om te wisselen naar een staging-slot, de app-instelling in de staging-sleuf te wijzigen en deze vervolgens weer in productie te wisselen.

Controleer uw aangepaste nummer door naar de Kudu-console ( ) te gaan `https://<app-name>.scm.azurewebsites.net` en de volgende opdrachten te typen met behulp van PowerShell. Met elke opdracht wordt een getal uitgevoerd.

```PowerShell
Get-ComputerInfo | ft CsNumberOfLogicalProcessors # Total number of enabled logical processors. Disabled processors are excluded.
Get-ComputerInfo | ft CsNumberOfProcessors # Number of physical processors.
```

De processors kunnen multicore- of hyperthreading-processors zijn. Informatie over het aantal kernen dat beschikbaar is voor elke prijscategorie vindt u in [App Service](https://azure.microsoft.com/pricing/details/app-service/windows/)prijzen in de sectie **Premium Container (Windows)-abonnement.**

## <a name="customize-health-ping-behavior"></a>Gedrag van status ping aanpassen

App Service beschouwt een container als een goed gestarte container wanneer de container wordt gestart en reageert op een HTTP-ping. De status ping aanvraag bevat de header `User-Agent= "App Service Hyper-V Container Availability Check"` . Als de container wordt gestart, maar na een bepaalde tijd niet reageert op een ping, registreert App Service een gebeurtenis in het Docker-logboek, waarin wordt gezegd dat de container niet is gestart. 

Als uw toepassing resource-intensief is, reageert de container mogelijk niet op tijd op de HTTP-ping. Als u de acties wilt bepalen wanneer HTTP-pings mislukken, stelt u de `CONTAINER_AVAILABILITY_CHECK_MODE` app-instelling in. U kunt deze instellen via de [Cloud Shell](https://shell.azure.com). In Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings CONTAINER_AVAILABILITY_CHECK_MODE="ReportOnly"
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"CONTAINER_AVAILABILITY_CHECK_MODE"="ReportOnly"}
```

In de volgende tabel ziet u de mogelijke waarden:

| Waarde | Beschrijvingen |
| - | - |
| **Reparatie** | Start de container opnieuw op na drie opeenvolgende beschikbaarheidscontroles |
| **ReportOnly** | De standaardwaarde. Start de container niet opnieuw op, maar rapporteer na drie opeenvolgende beschikbaarheidscontroles in de Docker-logboeken voor de container. |
| **Uit** | Controleer niet op beschikbaarheid. |

## <a name="support-for-group-managed-service-accounts"></a>Ondersteuning voor door groepen beheerde serviceaccounts

Door groepen beheerde serviceaccounts (gMSA's) worden momenteel niet ondersteund in Windows-containers in App Service.

::: zone-end

::: zone pivot="container-linux"

## <a name="enable-ssh"></a>SSH inschakelen

SSH maakt veilige communicatie tussen een container en een client mogelijk. Als u wilt dat een aangepaste container SSH ondersteunt, moet u deze toevoegen aan uw Docker-afbeelding zelf.

> [!TIP]
> Alle ingebouwde Linux-containers in App Service hebben de SSH-instructies toegevoegd in hun opslagplaatsen voor afbeeldingen. U kunt de volgende instructies volgen met de [Node.js 10.14-opslagplaats](https://github.com/Azure-App-Service/node/blob/master/10.14) om te zien hoe deze daar is ingeschakeld. De configuratie in Node.js ingebouwde installatie afbeelding is iets anders, maar in principe hetzelfde.

- Voeg [een sshd_config toe](https://man.openbsd.org/sshd_config) aan uw opslagplaats, zoals in het volgende voorbeeld.

    ```
    Port            2222
    ListenAddress       0.0.0.0
    LoginGraceTime      180
    X11Forwarding       yes
    Ciphers aes128-cbc,3des-cbc,aes256-cbc,aes128-ctr,aes192-ctr,aes256-ctr
    MACs hmac-sha1,hmac-sha1-96
    StrictModes         yes
    SyslogFacility      DAEMON
    PasswordAuthentication  yes
    PermitEmptyPasswords    no
    PermitRootLogin     yes
    Subsystem sftp internal-sftp
    ```

    > [!NOTE]
    > Dit bestand configureert OpenSSH en moet de volgende items bevatten:
    > - `Port` moet worden ingesteld op 2222.
    > - `Ciphers`moet ten minste één item in deze lijst bevatten: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs`moet ten minste één item in deze lijst bevatten: `hmac-sha1,hmac-sha1-96`.

- Voeg in uw Dockerfile de volgende opdrachten toe:

    ```Dockerfile
    # Install OpenSSH and set the password for root to "Docker!". In this example, "apk add" is the install instruction for an Alpine Linux-based image.
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 

    # Copy the sshd_config file to the /etc/ssh/ directory
    COPY sshd_config /etc/ssh/

    # Open port 2222 for SSH access
    EXPOSE 80 2222
    ```

    Deze configuratie staat geen externe verbindingen naar de container toe. Poort 2222 van de container is alleen toegankelijk binnen het brugnetwerk van een particulier virtueel netwerk en is niet toegankelijk voor een aanvaller op internet.

- Start de SSH-server in het opstartscript voor uw container.

    ```bash
    /usr/sbin/sshd
    ```

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>Apps met meerdere containers configureren

- [Permanente opslag gebruiken in Docker Compose](#use-persistent-storage-in-docker-compose)
- [Preview-beperkingen](#preview-limitations)
- [Opties voor Docker Compose](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Permanente opslag gebruiken in Docker Compose

Apps met meerdere containers, zoals WordPress, hebben permanente opslag nodig om goed te kunnen werken. Als u dit wilt inschakelen, moet de configuratie van Docker Compose naar een opslaglocatie buiten *uw* container wijzen. De opslaglocaties in uw container blijven niet opgeslagen na het opnieuw opstarten van de app.

U kunt permanente opslag inschakelen door de app-instelling in te stellen met behulp van de opdracht `WEBSITES_ENABLE_APP_SERVICE_STORAGE` [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) in [Cloud Shell](https://shell.azure.com).

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Wijs *in het bestand docker-compose.yml* de optie toe aan `volumes` `${WEBAPP_STORAGE_HOME}` . 

`WEBAPP_STORAGE_HOME` is een omgevingsvariabele in App Service die is toegewezen aan de permanente opslag voor uw app. Bijvoorbeeld:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Preview-beperkingen

Meerdere containers zijn momenteel beschikbaar als preview-versie. De volgende App Service platformfuncties worden niet ondersteund:

- Verificatie / autorisatie
- Beheerde identiteiten
- CORS

### <a name="docker-compose-options"></a>Opties voor Docker Compose

In de volgende lijsten worden ondersteunde en niet-ondersteunde configuratieopties voor Docker Compose vermeld:

#### <a name="supported-options"></a>Ondersteunde opties

- command
- entrypoint
- omgeving
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Niet-ondersteunde opties

- build (niet toegestaan)
- depends_on (genegeerd)
- networks (genegeerd)
- secrets (genegeerd)
- andere poorten dan 80 en 8080 (genegeerd)

> [!NOTE]
> Andere opties die niet expliciet worden genoemd, worden genegeerd in openbare preview.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Aangepaste software migreren naar Azure App Service met behulp van een aangepaste container](tutorial-custom-container.md)

::: zone pivot="container-linux"

> [!div class="nextstepaction"]
> [Zelfstudie: WordPress-app met meerdere containers](tutorial-multi-container-app.md)

::: zone-end

U kunt ook aanvullende resources bekijken:

[Certificaat laden in Windows-/Linux-containers](configure-ssl-certificate-in-code.md#load-certificate-in-linuxwindows-containers)
