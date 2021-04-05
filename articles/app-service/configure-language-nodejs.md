---
title: Node.js-apps configureren
description: Meer informatie over het configureren van een Node.js-app in de systeem eigen Windows-exemplaren of in een vooraf ontwikkelde Linux-container in Azure App Service. In dit artikel worden de meest algemene configuratietaken beschreven.
ms.custom: devx-track-js, devx-track-azurecli
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 6a6f782768db12c2ce75f5cf1e66100222f24446
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101095215"
---
# <a name="configure-a-nodejs-app-for-azure-app-service"></a>Een Node.js-app configureren voor Azure App Service

Node.js-apps moeten worden geïmplementeerd met alle vereiste NPM-afhankelijkheden. De App Service-implementatie-engine wordt automatisch uitgevoerd `npm install --production` Wanneer u een [Git-opslag plaats](deploy-local-git.md)implementeert of een [zip-pakket](deploy-zip.md) met Build Automation is ingeschakeld. Als u uw bestanden implementeert met [FTP/S](deploy-ftp.md), moet u de vereiste pakketten echter hand matig uploaden.

Deze hand leiding bevat belang rijke concepten en instructies voor Node.js ontwikkel aars die op App Service zijn geïmplementeerd. Als u Azure App Service nog nooit hebt gebruikt, volgt u eerst de [Node.js Snelstartgids](quickstart-nodejs.md) en [Node.js met MongoDb zelf studie](tutorial-nodejs-mongodb-app.md) .

## <a name="show-nodejs-version"></a>Node.js versie weer geven

::: zone pivot="platform-windows"  

Als u de huidige versie van Node.js wilt weer geven, voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings list --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

Als u alle ondersteunde versies van Node.js wilt weer geven, voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep node
```

::: zone-end

::: zone pivot="platform-linux"

Als u de huidige versie van Node.js wilt weer geven, voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Als u alle ondersteunde versies van Node.js wilt weer geven, voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

::: zone-end

## <a name="set-nodejs-version"></a>Node.js versie instellen

::: zone pivot="platform-windows"  

Als u uw app wilt instellen op een [ondersteunde Node.js versie](#show-nodejs-version), voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com) om in `WEBSITE_NODE_DEFAULT_VERSION` te stellen op een ondersteunde versie:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

Met deze instelling geeft u op welke Node.js versie moet worden gebruikt, zowel tijdens runtime als tijdens het automatisch herstellen van pakketten tijdens het App Service bouwen van Automation.

> [!NOTE]
> Stel de Node.js versie in het project in `package.json` . De implementatie-engine wordt uitgevoerd in een afzonderlijk proces dat alle ondersteunde Node.js versies bevat.

::: zone-end

::: zone pivot="platform-linux"

Als u uw app wilt instellen op een [ondersteunde Node.js versie](#show-nodejs-version), voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Met deze instelling geeft u op welke Node.js versie moet worden gebruikt, zowel tijdens runtime als tijdens het automatisch herstellen van pakketten in kudu.

> [!NOTE]
> Stel de Node.js versie in het project in `package.json` . De implementatie-engine wordt uitgevoerd in een afzonderlijke container die alle ondersteunde Node.js versies bevat.

::: zone-end

## <a name="get-port-number"></a>Poort nummer ophalen

U Node.js app moet Luis teren naar de juiste poort voor het ontvangen van inkomende aanvragen.

::: zone pivot="platform-windows"  

In App Service op Windows worden Node.js apps gehost met [IISNode](https://github.com/Azure/iisnode)en wordt de Node.js-app geluisterd naar de poort die is opgegeven in de `process.env.PORT` variabele. In het volgende voor beeld ziet u hoe u dit doet in een eenvoudige Express-app:

::: zone-end

::: zone pivot="platform-linux"  

App Service stelt de omgevings variabele `PORT` in de Node.js-container en stuurt de inkomende aanvragen door naar uw container op dat poort nummer. Als u de aanvragen wilt ontvangen, moet uw app met worden geluisterd naar die poort `process.env.PORT` . In het volgende voor beeld ziet u hoe u dit doet in een eenvoudige Express-app:

::: zone-end

```javascript
const express = require('express')
const app = express()
const port = process.env.PORT || 3000

app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`)
})
```

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>De automatisering van bouwbewerkingen aanpassen

Als u uw app wilt implementeren met behulp van Git of zip-pakketten waarbij bouwautomatisering is ingeschakeld, moet u de volgende stappen voor de App Service-bouwautomatisering in deze volgorde uitvoeren:

1. Voer aangepast script uit als dit door `PRE_BUILD_SCRIPT_PATH` is opgegeven.
1. `npm install`Zonder vlaggen worden uitgevoerd, waaronder NPM `preinstall` en `postinstall` scripts en wordt ook geïnstalleerd `devDependencies` .
1. Voer uit `npm run build` Als u een build-script hebt opgegeven in uw *package.jsop*.
1. Uitvoeren `npm run build:azure` als een build: Azure script is opgegeven in uw *package.jsop*.
1. Voer aangepast script uit als dit is opgegeven door `POST_BUILD_SCRIPT_PATH`.

> [!NOTE]
> Zoals beschreven in [NPM-documenten](https://docs.npmjs.com/misc/scripts), scripts `prebuild` met de namen en `postbuild` uitvoeren vóór en na `build` , respectievelijk, indien opgegeven. `preinstall` en `postinstall` vóór en na `install` , respectievelijk worden uitgevoerd.

`PRE_BUILD_COMMAND` en `POST_BUILD_COMMAND` zijn omgevings variabelen die standaard leeg zijn. Als u vooraf gebouwde opdrachten wilt uitvoeren, definieert u `PRE_BUILD_COMMAND`. Als u achteraf gebouwde opdrachten wilt uitvoeren, definieert u `POST_BUILD_COMMAND`.

In het volgende voorbeeld worden de twee variabelen voor een reeks opdrachten opgegeven, gescheiden door komma's.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Zie [Oryx-configuratie](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md) voor aanvullende omgevingsvariabelen om bouwautomatisering aan te passen.

Zie Oryx-documentatie voor meer informatie over hoe App Service worden uitgevoerd en gebouwd Node.js apps in Linux [: hoe Node.js-apps worden gedetecteerd en gebouwd](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md).

## <a name="configure-nodejs-server"></a>Node.js server configureren

De Node.js-containers worden geleverd met [PM2](https://pm2.keymetrics.io/), een productie proces Manager. U kunt uw app configureren om te beginnen met PM2, of met NPM, of met een aangepaste opdracht.

- [Aangepaste opdracht uitvoeren](#run-custom-command)
- [NPM starten](#run-npm-start)
- [Uitvoeren met PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Aangepaste opdracht uitvoeren

App Service kunt uw app starten met een aangepaste opdracht, zoals een uitvoerbaar bestand zoals *Run.sh*. Als u bijvoorbeeld wilt uitvoeren `npm run start:prod` , voert u de volgende opdracht uit in het [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>NPM starten

Als u uw app wilt starten met `npm start` , moet u ervoor zorgen dat een `start` script in de *package.js* in het bestand staat. Bijvoorbeeld:

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

Als u een aangepaste *package.js* wilt gebruiken in uw project, voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Uitvoeren met PM2

De container start automatisch uw app met PM2 wanneer een van de gemeen schappelijke Node.js bestanden in uw project is gevonden:

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Een van de volgende [PM2-bestanden](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.jsop* en *ecosystem.config.js*

U kunt ook een aangepast start bestand met de volgende extensies configureren:

- Een *js* -bestand
- Een [PM2-bestand](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) met de *extensie. json*, *.config.js*, *. yaml* of *. yml*

Als u een aangepast start bestand wilt toevoegen, voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Foutopsporing op afstand

> [!NOTE]
> Externe fout opsporing is momenteel beschikbaar als preview-versie.

U kunt fouten opsporen in uw Node.js-app op afstand in [Visual Studio code](https://code.visualstudio.com/) als u deze configureert voor [uitvoering met PM2](#run-with-pm2), behalve wanneer u deze uitvoert met een * .config.js, *. yml of *. yaml*.

In de meeste gevallen is er geen extra configuratie vereist voor uw app. Als uw app wordt uitgevoerd met een *process.jsin* het bestand (standaard of aangepast), moet deze een `script` eigenschap hebben in het JSON-basis programma. Bijvoorbeeld:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Als u Visual Studio code voor externe fout opsporing wilt instellen, installeert u de [app service extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Volg de instructies op de pagina extensie en meld u aan bij Azure in Visual Studio code.

Zoek in de Azure Verkenner de app waarvoor u fouten wilt opsporen, klik er met de rechter muisknop op en selecteer **externe fout opsporing starten**. Klik op **Ja** om het voor uw app in te scha kelen. App Service start een tunnel proxy voor u en koppelt de debugger. U kunt vervolgens aanvragen indienen bij de app en zien hoe het fout opsporingsprogramma op onderbrekings punten pauzeert.

Als u klaar bent met het oplossen van fouten, stopt u het fout opsporingsprogramma door de **verbinding te verbreken** Wanneer u hierom wordt gevraagd, klikt u op **Ja** om externe fout opsporing uit te scha kelen. Als u dit later wilt uitschakelen, klikt u opnieuw met de rechter muisknop op uw app in azure Verkenner en selecteert u **externe fout opsporing uitschakelen**.

::: zone-end

## <a name="access-environment-variables"></a>Toegang tot omgevingsvariabelen

In App Service kunt u [app-instellingen configureren](configure-common.md) buiten uw app-code. Vervolgens kunt u ze openen met behulp van het standaard Node.js patroon. Voor toegang tot bijvoorbeeld de app-instelling `NODE_ENV` gebruikt u de volgende code:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Voer grunt/Bower/Gulp uit

App Service build Automation wordt standaard uitgevoerd `npm install --production` Wanneer wordt gedetecteerd dat een Node.js-app wordt geïmplementeerd via git of een zip-implementatie waarbij build Automation is ingeschakeld. Als uw app een van de populaire automatiserings hulpprogramma's vereist, zoals grunt, Bower of Gulp, moet u een [aangepast implementatie script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) opgeven om het uit te voeren.

Als u de opslag plaats wilt inschakelen om deze hulpprogram ma's uit te voeren, moet u deze toevoegen aan de afhankelijkheden in *package.jsop.* Bijvoorbeeld:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Wijzig vanuit een lokaal Terminal venster de map in de hoofdmap van de opslag plaats en voer de volgende opdrachten uit:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

De hoofdmap van uw opslag plaats heeft nu twee extra bestanden: *. Deployment* en *Deploy.sh*.

Open *Deploy.sh* en zoek de `Deployment` sectie, die er als volgt uitziet:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Deze sectie eindigt met het uitvoeren van `npm install --production` . Voeg de sectie code toe die u nodig hebt om het vereiste hulp programma uit te voeren *aan het einde* van de `Deployment` sectie:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Bekijk een [voor beeld in het MEAN.js-voor beeld](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), waarbij het implementatie script ook een aangepaste `npm install` opdracht uitvoert.

### <a name="bower"></a>Bower

Dit code fragment wordt uitgevoerd `bower install` .

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Dit code fragment wordt uitgevoerd `gulp imagemin` .

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Dit code fragment wordt uitgevoerd `grunt` .

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>HTTPS-sessie detecteren

In App Service vindt [SSL-beëindiging](https://wikipedia.org/wiki/TLS_termination_proxy) plaats in de load balancers voor het netwerk, zodat alle HTTPS-aanvragen uw app bereiken als niet-versleutelde HTTP-aanvragen. Inspecteer de header `X-Forwarded-Proto` als de app-logica moet controleren of de aanvragen van gebruikers al dan niet zijn versleuteld.

Populaire webframeworks bieden toegang tot de `X-Forwarded-*`-informatie in het patroon van de standaard-app. In [Express](https://expressjs.com/)kunt u [vertrouwens proxy's](https://expressjs.com/guide/behind-proxies.html)gebruiken. Bijvoorbeeld:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

::: zone pivot="platform-windows"  

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end


::: zone pivot="platform-linux"

## <a name="monitor-with-application-insights"></a>Controleren met behulp van Application Insights

Met Application Insights kunt u de prestaties, uitzonde ringen en het gebruik van uw toepassing bewaken zonder dat er code wijzigingen worden aangebracht. Als u de app Insights-agent wilt koppelen, gaat u naar uw web-app in de portal en selecteert u **Application Insights** onder **instellingen** en selecteert u vervolgens **Application Insights inschakelen**. Selecteer vervolgens een bestaande app Insights-resource of maak een nieuwe. Ten slotte selecteert u onder **Toep assen** . Als u uw web-app wilt instrumenteren met Power shell, raadpleegt u [deze instructies](../azure-monitor/app/azure-web-apps.md?tabs=netcore#enabling-through-powershell)

Deze agent bewaakt uw Node.js-toepassing aan de server zijde. Als u uw Java script aan de client zijde wilt bewaken, [voegt u de Java script-SDK toe aan uw project](../azure-monitor/app/javascript.md). 

Zie de release opmerkingen voor de [Application Insights-extensie](../azure-monitor/app/web-app-extension-release-notes.md)voor meer informatie.

::: zone-end

## <a name="troubleshooting"></a>Problemen oplossen

Wanneer een werk Node.js-app anders werkt in App Service of fouten bevat, kunt u het volgende proberen:

- [Open de logboekstream](#access-diagnostic-logs).
- Test de app lokaal in de productie modus. App Service worden uw Node.js-apps uitgevoerd in de productie modus. u moet er dus voor zorgen dat uw project in de productie modus lokaal werkt zoals verwacht. Bijvoorbeeld:
    - Afhankelijk van uw *package.jsop* kunnen verschillende pakketten worden geïnstalleerd voor de productie modus ( `dependencies` VS. `devDependencies` ).
    - Bepaalde web Frameworks kunnen statische bestanden in de productie modus anders implementeren.
    - Bepaalde web Frameworks kunnen aangepaste opstart scripts gebruiken wanneer ze in de productie modus worden uitgevoerd.
- Voer uw app uit in de App Service in de ontwikkelings modus. In [MEAN.js](https://meanjs.org/)kunt u bijvoorbeeld uw app in runtime instellen op de ontwikkelings modus door [de app- `NODE_ENV` instelling](configure-common.md)in te stellen.

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Node.js-app met MongoDB](tutorial-nodejs-mongodb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [Veelgestelde vragen over App Service Linux](faq-app-service-linux.md)

::: zone-end
