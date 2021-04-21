---
title: Veelgestelde vragen over ingebouwde containers uitvoeren
description: Vind antwoorden op de veelgestelde vragen over de ingebouwde Linux-containers in Azure App Service.
keywords: azure app service, web-app, faq, linux, oss, web app for containers, multi-container, multicontainer
author: msangapu-msft
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 82fc5707800e06e3221754bfa29d8e981ccdbd2d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782581"
---
# <a name="azure-app-service-on-linux-faq"></a>Veelgestelde vragen over Azure App Service on Linux

Met de release van App Service op Linux, werken we aan het toevoegen van functies en het verbeteren van ons platform. Dit artikel bevat antwoorden op vragen die onze klanten ons onlangs hebben gesteld.

Als u een vraag hebt, kunt u een opmerking plaatsen bij dit artikel.

## <a name="built-in-images"></a>Ingebouwde afbeeldingen

**Ik wil een fork maken van de ingebouwde Docker-containers die het platform biedt. Waar vind ik die bestanden?**

U vindt alle Docker-bestanden op [GitHub.](https://github.com/azure-app-service) U vindt alle Docker-containers op [Docker Hub](https://hub.docker.com/u/appsvc/).

<a id="#startup-file"></a>

**Wat zijn de verwachte waarden voor de sectie Opstartbestand wanneer ik de runtimestack configureer?**

| Stack           | Verwachte waarde                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | de opdracht om uw JAR-app te starten (bijvoorbeeld `java -jar /home/site/wwwroot/app.jar --server.port=80` ) |
| Tomcat          | de locatie van een script om de benodigde configuraties uit te voeren (bijvoorbeeld `/home/site/deployments/tools/startup_script.sh` )          |
| Node.js         | het PM2-configuratiebestand of uw scriptbestand                                |
| .NET Core       | de gecompileerde DLL-naam als `dotnet <myapp>.dll`                                 |
| Ruby            | het Ruby-script waarmee u uw app wilt initialiseren                     |

Deze opdrachten of scripts worden uitgevoerd nadat de ingebouwde Docker-container is gestart, maar voordat uw toepassingscode wordt gestart.

## <a name="management"></a>Beheer

**Wat gebeurt er wanneer ik op de knop Opnieuw opstarten in de Azure Portal?**

Deze actie is hetzelfde als het opnieuw opstarten van Docker.

**Kan ik Secure Shell (SSH) gebruiken om verbinding te maken met de virtuele machine (VM) van de app-container?**

Ja, u kunt dit doen via de site voor broncodebeheer (SCM).

> [!NOTE]
> U kunt ook rechtstreeks vanaf uw lokale ontwikkelcomputer verbinding maken met de app-container via SSH, SFTP of Visual Studio Code (voor live foutopsporing gebruikt u Node.js-apps). Zie [Foutopsporing op afstand en SSH in App Service in Linux](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html) voor meer informatie.
>

**Hoe kan ik een Linux-App Service maken via een SDK of een Azure Resource Manager sjabloon?**

Stel het **gereserveerde** veld van de app-service in op *true.*

## <a name="continuous-integration-and-deployment"></a>Continue integratie en implementatie

**Mijn web-app gebruikt nog steeds een oude Docker-containerafbeelding nadat ik de afbeelding op de Docker Hub. Ondersteunt u continue integratie en implementatie van aangepaste containers?**

Ja, als u continue integratie/implementatie wilt instellen voor Azure Container Registry of DockerHub, door Continue implementatie [te volgen met Web App for Containers](./deploy-ci-cd-custom-container.md). Voor privéregisters kunt u de container vernieuwen door uw web-app te stoppen en vervolgens te starten. U kunt ook een dummytoepassingsinstelling wijzigen of toevoegen om het vernieuwen van uw container af te dwingen.

**Ondersteunt u faseringsomgevingen?**

Ja.

**Kan ik *WebDeploy/MSDeploy gebruiken om* mijn web-app te implementeren?**

Ja, u moet een app-instelling met de naam instellen `WEBSITE_WEBDEPLOY_USE_SCM` op *false*.

**De Git-implementatie van mijn toepassing mislukt wanneer ik een Linux-web-app gebruik. Hoe kan ik het probleem oplossen?**

Als de Git-implementatie in uw Linux-web-app mislukt, kiest u een van de volgende opties om uw toepassingscode te implementeren:

- De functie Continue levering (preview) gebruiken: u kunt de broncode van uw app opslaan in een Azure DevOps Git-opslagplaats of GitHub-opslagplaats om Continue levering van Azure te gebruiken. Zie Continue levering voor [Linux-web-app configureren voor meer informatie.](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/)

- De [ZIP-implementatie-API gebruiken:](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)als u deze API wilt gebruiken, gaat u met SSH naar uw [web-app](configure-linux-open-ssh-session.md) en gaat u naar de map waarin u uw code wilt implementeren. Voer de volgende code uit:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Als er een foutbericht wordt weergegeven dat de opdracht niet is gevonden, moet u curl installeren met behulp `curl` van voordat u de vorige opdracht hebt `apt-get install curl` `curl` uitgevoerd.

## <a name="language-support"></a>Taalondersteuning

**Ik wil websockets gebruiken in Node.js toepassing, speciale instellingen of configuraties om in te stellen?**

Ja, schakel `perMessageDeflate` in de code aan de serverzijde Node.js uit. Als u bijvoorbeeld een socket.io, gebruikt u de volgende code:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Ondersteunt u niet-gecompileerde .NET Core-apps?**

Ja.

**Ondersteunt u Composer als afhankelijkheidsbeheerder voor PHP-apps?**

Ja, tijdens een Git-implementatie moet Kudu detecteren dat u een PHP-toepassing implementeert (dankzij de aanwezigheid van een composer.lock-bestand), waarna Kudu een composer-installatie activeert.

## <a name="custom-containers"></a>Aangepaste containers

**Ik gebruik mijn eigen aangepaste container. Ik wil dat het platform een SMB-share aan de map `/home/` kan toevoegen.**

Als de instelling niet is gespecificeerd of is ingesteld op false , wordt de map niet gedeeld tussen schaal-exemplaren en blijven geschreven bestanden niet persistent bij `WEBSITES_ENABLE_APP_SERVICE_STORAGE` opnieuw   `/home/` opstarten.   Als u expliciet `WEBSITES_ENABLE_APP_SERVICE_STORAGE` *instelt op true,* wordt de mount ingeschakeld.

**Het duurt lang voordat mijn aangepaste container is gestart en het platform start de container opnieuw op voordat deze is gestart.**

U kunt configureren hoe lang het platform wacht voordat de container opnieuw wordt gestart. Als u dit wilt doen, stelt u `WEBSITES_CONTAINER_START_TIME_LIMIT` de app-instelling in op de wantwaarde. De standaardwaarde is 230 seconden en de maximumwaarde is 1800 seconden.

**Wat is de indeling voor de URL van de privéregisterserver?**

Geef de volledige register-URL op, inclusief `http://` of `https://` .

**Wat is de indeling voor de naam van de afbeelding in de optie Privéregister?**

Voeg de volledige naam van de afbeelding toe, inclusief de URL van het privéregister (bijvoorbeeld myacr.azurecr.io/dotnet:latest). Namen van afbeeldingen die gebruikmaken van een aangepaste poort [kunnen niet worden ingevoerd via de portal](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Als u `docker-custom-image-name` wilt instellen, gebruikt [ `az` u het opdrachtregelprogramma](/cli/azure/webapp/config/container#az_webapp_config_container_set).

**Kan ik meer dan één poort op mijn aangepaste containerafbeelding gebruiken?**

We bieden geen ondersteuning voor het blootstellen van meer dan één poort.

**Kan ik mijn eigen opslag gebruiken?**

Ja, [Bring Your Own Storage](./configure-connect-to-azure-storage.md) is in preview.

**Waarom kan ik niet door het bestandssysteem van mijn aangepaste container bladeren of door processen uitvoeren vanaf de SCM-site?**

De SCM-site wordt uitgevoerd in een afzonderlijke container. U kunt het bestandssysteem of de lopende processen van de app-container niet controleren.

**Mijn aangepaste container luistert naar een andere poort dan poort 80. Hoe kan ik mijn app configureren om aanvragen naar die poort te routeer?**

We hebben automatische poortdetectie. U kunt ook een app-instelling met de *naam WEBSITES_PORT* geef deze de waarde van het verwachte poortnummer. Voorheen gebruikte het platform de *app-instelling PORT.* We zijn van plan deze app-instelling af te bouwen en *uitsluitend* WEBSITES_PORT gebruiken.

**Moet ik HTTPS implementeren in mijn aangepaste container?**

Nee, het platform verwerkt HTTPS-beëindiging aan de gedeelde front-ends.

**Moet ik de variabele PORT gebruiken in code voor ingebouwde containers?**

Nee, poortvariabele is niet nodig vanwege automatische poortdetectie. Als er geen poort wordt gedetecteerd, wordt deze standaard ingesteld op 80.
Als u handmatig een aangepaste poort wilt configureren, gebruikt u de instructie EXPOSE in de Dockerfile en de app-instelling, WEBSITES_PORT, met een poortwaarde die u voor de container wilt binden.

**Moet ik een WEBSITES_PORT voor aangepaste containers?**

Ja, dit is vereist voor aangepaste containers. Als u handmatig een aangepaste poort wilt configureren, gebruikt u de instructie EXPOSE in de Dockerfile en de app-instelling, WEBSITES_PORT, met een poortwaarde die u voor de container wilt binden.

**Kan ik ASPNETCORE_URLS gebruiken in de Docker-afbeelding?**

Ja, overschrijf de omgevingsvariabele voordat de .NET Core-app wordt gestart.
Bijvoorbeeld In het init.sh script: export ASPNETCORE_URLS={Your value}

## <a name="multi-container-with-docker-compose"></a>Meerdere containers met Docker Compose

**Hoe kan ik ACR (Azure Container Registry) configureren voor gebruik met meerdere containers?**

Als u ACR wilt gebruiken met meerdere containers, moeten alle **container-afbeeldingen** worden gehost op dezelfde ACR-registerserver. Zodra ze zich op dezelfde registerserver, moet u toepassingsinstellingen maken en vervolgens het configuratiebestand van Docker Compose bijwerken om de naam van de ACR-installatier op te nemen.

Maak de volgende toepassingsinstellingen:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (volledige URL, bijvoorbeeld: `https://<server-name>.azurecr.io` )
- DOCKER_REGISTRY_SERVER_PASSWORD (beheerderstoegang inschakelen in ACR-instellingen)

In het configuratiebestand verwijst u naar uw ACR-installatiebestand, zoals in het volgende voorbeeld:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Hoe kan ik welke container toegankelijk is via internet?**

- Er kan slechts één container zijn geopend voor toegang
- Alleen poort 80 en 8080 zijn toegankelijk (beschikbaar gemaakt poorten)

Hier volgen de regels om te bepalen welke container toegankelijk is in volgorde van prioriteit:

- `WEBSITES_WEB_CONTAINER_NAME`Toepassingsinstelling ingesteld op de containernaam
- De eerste container voor het definiëren van poort 80 of 8080
- Als geen van de bovenstaande is waar, is de eerste container die is gedefinieerd in het bestand toegankelijk (beschikbaar)


## <a name="web-sockets"></a>websockets

Websockockers worden ondersteund in Linux-apps.

> [!IMPORTANT]
> Web sockets worden momenteel niet ondersteund voor Linux-apps in gratis App Service-abonnementen. We werken aan het verwijderen van deze beperking en zijn van plan maximaal 5 websockockeverbindingen te ondersteunen voor gratis App Service abonnementen.

## <a name="pricing-and-sla"></a>Prijzen en SLA

**Wat zijn de prijzen, nu de service algemeen beschikbaar is?**

De prijzen variëren per SKU en regio, maar u kunt meer informatie bekijken op onze pagina met prijzen: [App Service Prijzen.](https://azure.microsoft.com/pricing/details/app-service/linux/)

## <a name="other-questions"></a>Overige vragen

**Wat betekent 'Aangevraagde functie is niet beschikbaar in resourcegroep'?**

Mogelijk ziet u dit bericht wanneer u een web-app maakt met Azure Resource Manager (ARM). Op basis van een huidige beperking kunt u voor dezelfde resourcegroep geen Windows- en Linux-apps in dezelfde regio combineren.

**Wat zijn de ondersteunde tekens in namen van toepassingsinstellingen?**

U kunt alleen letters (A-Z, a-z), cijfers (0-9) en het onderstrepingsteken (_) gebruiken voor toepassingsinstellingen.

**Waar kan ik nieuwe functies aanvragen?**

U kunt uw idee indienen op het [Web Apps feedbackforum.](https://aka.ms/webapps-uservoice) Voeg '[Linux]' toe aan de titel van uw idee.

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure App Service op Linux?](overview.md#app-service-on-linux)
- [Faseringsomgevingen in Azure App Service instellen](deploy-staging-slots.md)
- [Continue implementatie met Web App for Containers](./deploy-ci-cd-custom-container.md)
- [Dingen die u moet weten: Web Apps en Linux](https://techcommunity.microsoft.com/t5/apps-on-azure/things-you-should-know-web-apps-and-linux/ba-p/392472)
