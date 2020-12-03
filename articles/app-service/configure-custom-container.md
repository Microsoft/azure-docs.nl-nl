---
title: Een aangepaste container configureren
description: Meer informatie over het configureren van een aangepaste container in Azure App Service. In dit artikel worden de meest algemene configuratietaken beschreven.
ms.topic: article
ms.date: 09/22/2020
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 2aece0550d7b78ac4312e71b2671de4a64e4b86b
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96557923"
---
# <a name="configure-a-custom-container-for-azure-app-service"></a>Een aangepaste container configureren voor Azure App Service

In dit artikel leest u hoe u een aangepaste container kunt configureren om te worden uitgevoerd op Azure App Service.

::: zone pivot="container-windows"

Deze hand leiding bevat belang rijke concepten en instructies voor container opslag van Windows-apps in App Service. Als u Azure App Service nog nooit hebt gebruikt, volgt u eerst de Snelstartgids en [zelf studie](tutorial-custom-container.md) van de [aangepaste container](quickstart-custom-container.md) .

::: zone-end

::: zone pivot="container-linux"

Deze hand leiding bevat belang rijke concepten en instructies voor container opslag Linux-apps in App Service. Als u Azure App Service nog nooit hebt gebruikt, volgt u eerst de Snelstartgids en [zelf studie](tutorial-custom-container.md) van de [aangepaste container](quickstart-custom-container.md) . Er is ook een Snelstartgids en [zelf studie](tutorial-multi-container-app.md)voor [apps met meerdere containers](quickstart-multi-container.md) .

::: zone-end

::: zone pivot="container-windows"

## <a name="supported-parent-images"></a>Ondersteunde bovenliggende installatie kopieën

Voor uw aangepaste Windows-installatie kopie moet u de juiste [bovenliggende installatie kopie (basis installatie kopie)](https://docs.docker.com/develop/develop-images/baseimages/) kiezen voor het gewenste Framework:

- Als u .NET Framework-Apps wilt implementeren, gebruikt u een bovenliggende installatie kopie op basis van de Windows Server Core [LTSC-versie (Long-term Servicing Channel)](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) . 
- Als u .NET Core-Apps wilt implementeren, gebruikt u een bovenliggende installatie kopie op basis van de Windows Server nano [Semi-Annual-onderhouds kanaal (SAC)](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) -versie. 

Het duurt enige tijd om een bovenliggende installatiekopie te downloaden tijdens het opstarten van de app. U kunt deze opstarttijd echter verminderen door een van de volgende bovenliggende installatiekopieën te gebruiken die al in cache zijn opgeslagen in Azure App Service:

- [MCR.Microsoft.com/Windows/ServerCore](https://hub.docker.com/_/microsoft-windows-servercore): 2004
- [MCR.Microsoft.com/Windows/ServerCore](https://hub.docker.com/_/microsoft-windows-servercore): ltsc2019
- [MCR.Microsoft.com/DOTNET/Framework/ASPNET](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/): 4,8-windowsservercore-2004
- [MCR.Microsoft.com/DOTNET/Framework/ASPNET](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/): 4,8-windowsservercore-ltsc2019
- [MCR.Microsoft.com/DotNet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nano server-2004
- [MCR.Microsoft.com/DotNet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nano server-1909
- [MCR.Microsoft.com/DotNet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nano server-1903
- [MCR.Microsoft.com/DotNet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nano server-1809
- [MCR.Microsoft.com/DotNet/core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nano server-2004
- [MCR.Microsoft.com/DotNet/core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nano server-1909
- [MCR.Microsoft.com/DotNet/core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nano server-1903
- [MCR.Microsoft.com/DotNet/core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nano server-1809

::: zone-end

## <a name="change-the-docker-image-of-a-custom-container"></a>De docker-installatie kopie van een aangepaste container wijzigen

Als u een bestaande aangepaste container-app wilt wijzigen van de huidige docker-installatie kopie naar een nieuwe installatie kopie, gebruikt u de volgende opdracht:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>Een installatie kopie van een persoonlijk REGI ster gebruiken

Als u een installatie kopie uit een persoonlijk REGI ster wilt gebruiken, zoals Azure Container Registry, voert u de volgende opdracht uit:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

*\<username>* Geef voor en *\<password>* de aanmeldings referenties voor uw persoonlijke register account op.

## <a name="i-dont-see-the-updated-container"></a>Ik zie de bijgewerkte container niet

Als u de instellingen van de docker-container wijzigt zodat deze naar een nieuwe container verwijzen, kan het enkele minuten duren voordat de app HTTP-aanvragen van de nieuwe container verzendt. Terwijl de nieuwe container wordt opgehaald en gestart, blijft App Service aanvragen van de oude container blijven verwerken. Alleen wanneer de nieuwe container wordt gestart en er aanvragen kunnen worden ontvangen, worden App Service aanvragen verzonden.

## <a name="how-container-images-are-stored"></a>Hoe container installatie kopieën worden opgeslagen

De eerste keer dat u een aangepaste docker-installatie kopie uitvoert in App Service, bevat App Service een `docker pull` en worden alle afbeeldings lagen opgehaald. Deze lagen worden op schijf opgeslagen, zoals als u docker on-premises gebruikt. Telkens wanneer de app opnieuw wordt gestart, App Service een `docker pull` , maar worden alleen lagen opgehaald die zijn gewijzigd. Als er geen wijzigingen zijn, gebruikt App Service bestaande lagen op de lokale schijf.

Als de app de reken instanties om een of andere reden wijzigt, zoals het omhoog en omlaag schalen van de prijs categorieën, moet App Service alle lagen opnieuw ophalen. Hetzelfde geldt als u uitbreidt om extra instanties toe te voegen. Er zijn ook zeldzame gevallen waarin de app-exemplaren kunnen veranderen zonder een schaal bewerking.

## <a name="configure-port-number"></a>Poort nummer configureren

App Service gaat er standaard van uit dat uw aangepaste container luistert op poort 80. Als uw container naar een andere poort luistert, stelt u de `WEBSITES_PORT` app-instelling in uw app service-app in. U kunt deze instellen via de [Cloud shell](https://shell.azure.com). In bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_PORT"="8000"}
```

App Service staat momenteel toe dat uw container slechts één poort beschikbaar maakt voor HTTP-aanvragen. 

## <a name="configure-environment-variables"></a>Omgevingsvariabelen configureren

Uw aangepaste container kan gebruikmaken van omgevings variabelen die extern moeten worden opgegeven. U kunt deze door geven via de [Cloud shell](https://shell.azure.com). In bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings DB_HOST="myownserver.mysql.database.azure.com"
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"DB_HOST"="myownserver.mysql.database.azure.com"}
```

Wanneer uw app wordt uitgevoerd, worden de App Service app-instellingen automatisch in het proces geïnjecteerd als omgevings variabelen. 

::: zone pivot="container-windows"
Voor IIS-of .NET Framework (4,0 of hoger)-containers worden ze `System.ConfigurationManager` door app service als .net-app-instellingen en verbindings reeksen automatisch ingevoegd. Voor alle andere talen of Framework worden ze als omgevings variabelen voor het proces geboden, met een van de volgende corresponderende voor voegsels:

- `APPSETTING_`
- `SQLCONTR_`
- `MYSQLCONTR_`
- `SQLAZURECOSTR_`
- `POSTGRESQLCONTR_`
- `CUSTOMCONNSTR_`

::: zone-end

::: zone pivot="container-linux"

Deze methode werkt zowel voor apps met één container als apps met meerdere containers, waarbij de omgevings variabelen worden opgegeven in het bestand *docker-Compose. yml* .

::: zone-end

## <a name="use-persistent-shared-storage"></a>Permanente gedeelde opslag gebruiken

::: zone pivot="container-windows"

U kunt de *C:\home* -map in het bestands systeem van uw app gebruiken om bestanden op te slaan tijdens het opnieuw opstarten en ze te delen met alle instanties. De `C:\home` in uw app is beschikbaar om uw container-app toegang te geven tot permanente opslag.

Wanneer permanente opslag is uitgeschakeld, worden er geen schrijf bewerkingen naar de `C:\home` Directory bewaard. [Docker-host-logboeken en container logboeken](#access-diagnostic-logs) worden opgeslagen in een standaard permanente gedeelde opslag die niet aan de container is gekoppeld. Wanneer permanente opslag is ingeschakeld, worden alle schrijf bewerkingen naar de `C:\home` Directory persistent gemaakt en kunnen alle exemplaren van een uitgeschaalde app worden geopend. logboeken zijn toegankelijk op `C:\home\LogFiles` .

::: zone-end

::: zone pivot="container-linux"

U kunt de */Home* -map in het bestands systeem van uw app gebruiken om bestanden op te slaan tijdens het opnieuw opstarten en ze te delen met alle instanties. De `/home` in uw app is beschikbaar om uw container-app toegang te geven tot permanente opslag.

Wanneer permanente opslag is uitgeschakeld, worden de schrijf bewerkingen naar de `/home` Directory niet opgeslagen in een app die opnieuw wordt gestart of over meerdere exemplaren. De enige uitzonde ring hierop is de `/home/LogFiles` map die wordt gebruikt voor het opslaan van de docker-en container Logboeken. Wanneer permanente opslag is ingeschakeld, worden alle schrijf bewerkingen naar de `/home` Directory persistent gemaakt en kunnen alle exemplaren van een uitgeschaalde app worden geopend.

::: zone-end

Permanente opslag is standaard uitgeschakeld en de instelling wordt niet weer gegeven in de app-instellingen. Als u deze wilt inschakelen, stelt u de `WEBSITES_ENABLE_APP_SERVICE_STORAGE` app-instelling in via de [Cloud shell](https://shell.azure.com). In bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=true}
```

> [!NOTE]
> U kunt ook [uw eigen permanente opslag configureren](configure-connect-to-azure-storage.md).

## <a name="detect-https-session"></a>HTTPS-sessie detecteren

App Service stopt TLS/SSL bij de front-ends. Dit betekent dat TLS/SSL-aanvragen nooit naar uw app gaan. U hoeft geen ondersteuning voor TLS/SSL in uw app te implementeren. 

De front-ends bevinden zich in azure data centers. Als u gebruikmaakt van TLS/SSL met uw app, wordt uw verkeer via internet altijd veilig versleuteld.

::: zone pivot="container-windows"

## <a name="customize-aspnet-machine-key-injection"></a>Injectie van ASP.NET-machine sleutel aanpassen

 Tijdens het starten van de container worden automatisch gegenereerde sleutels in de container geïnjecteerd als de computer sleutels voor ASP.NET-cryptografische routines. U kunt [deze sleutels in de container vinden](#connect-to-the-container) door te zoeken naar de volgende omgevings variabelen: `MACHINEKEY_Decryption` , `MACHINEKEY_DecryptionKey` , `MACHINEKEY_ValidationKey` , `MACHINEKEY_Validation` . 

De nieuwe sleutels bij elke herstart kunnen de ASP.NET formulier verificatie en de weergave status opnieuw instellen als uw app hiervan afhankelijk is. Als u wilt voor komen dat sleutels automatisch opnieuw worden gegenereerd, [stelt u ze hand matig in als app service-app-instellingen](#configure-environment-variables). 

## <a name="connect-to-the-container"></a>Verbinding maken met de container

U kunt rechtstreeks verbinding maken met uw Windows-container voor diagnostische taken door te navigeren naar `https://<app-name>.scm.azurewebsites.net/DebugConsole` . Het werkt als volgt:

- Met de console fout opsporing kunt u interactieve opdrachten uitvoeren, zoals het starten van Power shell-sessies, het inspecteren van register sleutels en het navigeren door het hele container bestands systeem.
- Het werkt onafhankelijk van de grafische browser hierboven, waarin alleen de bestanden in uw [gedeelde opslag](#use-persistent-shared-storage)worden weer gegeven.
- In een uitgeschaalde app is de console fout opsporing verbonden met een van de container exemplaren. U kunt een ander exemplaar selecteren in de vervolg keuzelijst **exemplaar** in het bovenste menu.
- Wijzigingen die u aanbrengt in de container vanuit de-console blijven *niet* behouden wanneer uw app opnieuw wordt gestart (met uitzonde ring van wijzigingen in de gedeelde opslag), omdat deze geen deel uitmaakt van de docker-installatie kopie. Als u uw wijzigingen wilt behouden, zoals register instellingen en software-installatie, maakt u deze deel uit van de Dockerfile.

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

App Service registreert acties van de docker-host en activiteiten vanuit de container. Logboeken van de docker-host (platform-Logboeken) worden standaard verzonden, maar toepassings Logboeken of webserver logboeken in de container moeten hand matig worden ingeschakeld. Zie [toepassings logboeken inschakelen](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) en [logboek registratie van webserver inschakelen](troubleshoot-diagnostic-logs.md#enable-web-server-logging)voor meer informatie. 

Er zijn verschillende manieren om toegang te krijgen tot docker-logboeken:

- [In Azure Portal](#in-azure-portal)
- [Vanuit de kudu-console](#from-the-kudu-console)
- [Met de kudu-API](#with-the-kudu-api)
- [Logboeken verzenden naar Azure Monitor](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)

### <a name="in-azure-portal"></a>In Azure Portal

Docker-logboeken worden weer gegeven in de portal, op de pagina **container instellingen** van uw app. De logboeken worden afgekapt, maar u kunt alle logboeken downloaden door te klikken op **downloaden**. 

### <a name="from-the-kudu-console"></a>Vanuit de kudu-console

Navigeer naar `https://<app-name>.scm.azurewebsites.net/DebugConsole` en klik op de map **logfiles** om de afzonderlijke logboek bestanden weer te geven. Als u de volledige map met **logboek bestanden** wilt downloaden, klikt u op het **Download** pictogram links van de mapnaam. U kunt deze map ook openen met een FTP-client.

In de-console terminal kunt u de `C:\home\LogFiles` map niet standaard openen omdat permanente gedeelde opslag niet is ingeschakeld. U kunt dit gedrag inschakelen in de-console terminal door [permanente gedeelde opslag in te scha kelen](#use-persistent-shared-storage).

Als u probeert het docker-logboek te downloaden dat momenteel in gebruik is met een FTP-client, wordt er mogelijk een fout bericht weer geven als gevolg van een bestands vergrendeling.

### <a name="with-the-kudu-api"></a>Met de kudu-API

Ga rechtstreeks naar `https://<app-name>.scm.azurewebsites.net/api/logs/docker` om meta gegevens voor de docker-logboeken weer te geven. Mogelijk worden er meerdere logboek bestanden weer gegeven, en `href` kunt u het logboek bestand rechtstreeks downloaden met de eigenschap. 

Als u alle logboeken Samen in één ZIP-bestand wilt downloaden, gaat u naar Access `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip` .

## <a name="customize-container-memory"></a>Container geheugen aanpassen

Standaard zijn alle Windows-containers die in Azure App Service zijn geïmplementeerd, beperkt tot 1 GB RAM-geheugen. U kunt deze waarde wijzigen door de `WEBSITE_MEMORY_LIMIT_MB` app-instelling via de [Cloud shell](https://shell.azure.com)op te geven. In bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_MEMORY_LIMIT_MB=2000
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_MEMORY_LIMIT_MB"=2000}
```

De waarde wordt gedefinieerd in MB en moet kleiner zijn dan of gelijk zijn aan het totale fysieke geheugen van de host. In een App Service-abonnement met 8 GB RAM-geheugen, mag het cumulatieve totaal van `WEBSITE_MEMORY_LIMIT_MB` voor alle apps bijvoorbeeld 8 GB niet overschrijden. Informatie over hoeveel geheugen beschikbaar is voor elke prijs categorie kunt u vinden in [app service prijzen](https://azure.microsoft.com/pricing/details/app-service/windows/)in de sectie **Premium container (Windows)-abonnement** .

## <a name="customize-the-number-of-compute-cores"></a>Het aantal reken kernen aanpassen

Standaard wordt een Windows-container uitgevoerd met alle beschik bare kernen voor uw gekozen prijs categorie. U kunt bijvoorbeeld het aantal kernen verminderen dat door uw staging-sleuf wordt gebruikt. Als u het aantal kernen wilt beperken dat door een container wordt gebruikt, stelt `WEBSITE_CPU_CORES_LIMIT` u de app-instelling in op het gewenste aantal kernen. U kunt deze instellen via de [Cloud shell](https://shell.azure.com). In bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --slot staging --settings WEBSITE_CPU_CORES_LIMIT=1
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_CPU_CORES_LIMIT"=1}
```

> [!NOTE]
> Bij het bijwerken van de app-instelling wordt automatisch opnieuw opstarten geactiveerd, waardoor minimale downtime wordt veroorzaakt. Voor een productie-app kunt u overwegen om deze naar een staging-sleuf te wisselen, de app-instelling in de staging-sleuf te wijzigen en deze vervolgens terug te wisselen naar productie.

Controleer het aangepaste nummer door naar de kudu-console ( `https://<app-name>.scm.azurewebsites.net` ) te gaan en de volgende opdrachten te typen met behulp van Power shell. Elke opdracht voert een getal uit.

```PowerShell
Get-ComputerInfo | ft CsNumberOfLogicalProcessors # Total number of enabled logical processors. Disabled processors are excluded.
Get-ComputerInfo | ft CsNumberOfProcessors # Number of physical processors.
```

De processoren kunnen multicore-of HyperThreading-processors zijn. Informatie over hoeveel kernen beschikbaar zijn voor elke prijs categorie kunt u vinden in [app service prijzen](https://azure.microsoft.com/pricing/details/app-service/windows/)in de sectie **Premium container (Windows)-abonnement** .

## <a name="customize-health-ping-behavior"></a>Gedrag van de status ping aanpassen

App Service houdt in dat een container met succes wordt gestart wanneer de container wordt gestart en reageert op een HTTP-ping. De ping-aanvraag voor de status bevat de header `User-Agent= "App Service Hyper-V Container Availability Check"` . Als de container wordt gestart, maar niet reageert op een ping na een bepaalde tijd, App Service een gebeurtenis geregistreerd in het docker-logboek, wat aangeeft dat de container niet is gestart. 

Als uw toepassing resource intensief is, reageert de container mogelijk niet op de HTTP-ping in de tijd. Als u de acties wilt beheren wanneer HTTP-pings mislukken, stelt u de `CONTAINER_AVAILABILITY_CHECK_MODE` app-instelling in. U kunt deze instellen via de [Cloud shell](https://shell.azure.com). In bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings CONTAINER_AVAILABILITY_CHECK_MODE="ReportOnly"
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"CONTAINER_AVAILABILITY_CHECK_MODE"="ReportOnly"}
```

De volgende tabel bevat de mogelijke waarden:

| Waarde | Beschreven |
| - | - |
| **Steller** | De container opnieuw opstarten na drie opeenvolgende beschikbaarheids controles |
| **ReportOnly** | De standaard waarde. Start de container niet opnieuw op, maar rapport in de docker-logboeken voor de container na drie opeenvolgende beschikbaarheids controles. |
| **Uit** | Controleer niet op Beschik baarheid. |

## <a name="support-for-group-managed-service-accounts"></a>Ondersteuning voor door groepen beheerde service accounts

Beheerde service accounts voor groepen (Gmsa's) worden momenteel niet ondersteund in Windows-containers in App Service.

::: zone-end

::: zone pivot="container-linux"

## <a name="enable-ssh"></a>SSH inschakelen

SSH maakt veilige communicatie tussen een container en een client mogelijk. Als u een aangepaste container SSH wilt ondersteunen, moet u deze toevoegen aan de Dockerfile zelf.

> [!TIP]
> Alle ingebouwde Linux-containers hebben de SSH-instructies toegevoegd aan hun afbeeldings opslagplaatsen. U kunt de volgende instructies door lopen met de [ opslag plaatsNode.js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14) om te zien hoe deze er wordt ingeschakeld.

- Gebruik de instructie [Run](https://docs.docker.com/engine/reference/builder/#run) om de SSH-server te installeren en stel het wacht woord voor het hoofd account in op `"Docker!"` . Voor een installatie kopie op basis van [Alpine Linux](https://hub.docker.com/_/alpine)hebt u bijvoorbeeld de volgende opdrachten nodig:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Deze configuratie staat geen externe verbindingen naar de container toe. SSH is alleen beschikbaar via `https://<app-name>.scm.azurewebsites.net` en geverifieerd met de publicatie referenties.

- Voeg [dit sshd_config bestand](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) toe aan uw opslag plaats voor installatie kopieën en gebruik de [Kopieer](https://docs.docker.com/engine/reference/builder/#copy) instructie om het bestand te kopiëren naar de */etc/ssh/* -map. Zie [OpenBSD-documentatie](https://man.openbsd.org/sshd_config)voor meer informatie over *sshd_config* -bestanden.

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Het *sshd_config*-bestand moet de volgende items bevatten:
    > - `Ciphers`moet ten minste één item in deze lijst bevatten: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs`moet ten minste één item in deze lijst bevatten: `hmac-sha1,hmac-sha1-96`.

- Gebruik de instructie [beschikbaar](https://docs.docker.com/engine/reference/builder/#expose) maken om poort 2222 in de container te openen. Hoewel het Hoofdwacht woord bekend is, is poort 2222 niet toegankelijk via internet. Het is alleen toegankelijk voor containers in het brug netwerk van een particulier virtueel netwerk.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- Start de SSH-server in het opstart script voor uw container.

    ```bash
    /usr/sbin/sshd
    ```

    Voor een voor beeld ziet u hoe de standaard [ containerNode.js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) de SSH-server start.

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>Apps voor meerdere containers configureren

- [Permanente opslag gebruiken in docker opstellen](#use-persistent-storage-in-docker-compose)
- [Preview-beperkingen](#preview-limitations)
- [Opties voor docker opstellen](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Permanente opslag gebruiken in docker opstellen

Multi-container-apps zoals WordPress hebben permanente opslag nodig om goed te kunnen functioneren. Als u deze functie wilt inschakelen, moet de configuratie van de docker-samen stellen naar een opslag locatie *buiten* uw container verwijzen. Opslag locaties in uw container behouden geen wijzigingen na het opnieuw opstarten van de app.

Schakel permanente opslag in door de `WEBSITES_ENABLE_APP_SERVICE_STORAGE` app-instelling in te stellen met behulp van de opdracht [AZ webapp config appSettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in [Cloud shell](https://shell.azure.com).

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Wijs in uw *docker-Compose. yml* -bestand de `volumes` optie toe aan `${WEBAPP_STORAGE_HOME}` . 

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

Meerdere containers zijn momenteel beschikbaar als preview-versie. De volgende App Service platform functies worden niet ondersteund:

- Verificatie / autorisatie
- Beheerde identiteiten
- CORS

### <a name="docker-compose-options"></a>Opties voor docker opstellen

In de volgende lijsten worden ondersteunde en niet-ondersteunde docker-configuratie opties weer gegeven:

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
> Alle andere opties die niet expliciet worden aangeroepen, worden genegeerd in de open bare preview-versie.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: aangepaste software migreren naar Azure App Service met behulp van een aangepaste container](tutorial-custom-container.md)

::: zone pivot="container-linux"

> [!div class="nextstepaction"]
> [Zelfstudie: WordPress-app met meerdere containers](tutorial-multi-container-app.md)

::: zone-end

Of Zie aanvullende bronnen:

[Certificaat in Windows/Linux-containers laden](configure-ssl-certificate-in-code.md#load-certificate-in-linuxwindows-containers)
