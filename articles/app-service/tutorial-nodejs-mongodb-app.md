---
title: 'Zelfstudie: Node.js-app met MongoDB'
description: Leer hoe u een Node.js-app kunt laten werken in Azure, met een verbinding met een MongoDB-database in Azure (Cosmos DB). In de zelfstudie wordt MEAN.js gebruikt.
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/16/2020
ms.custom: mvc, cli-validate, seodec18, devx-track-js, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: b1dcd413f301f25460cb29f1bb20e67a37ac6ebb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767313"
---
# <a name="tutorial-build-a-nodejs-and-mongodb-app-in-azure"></a>Zelfstudie: Een app voor Node.js en MongoDB bouwen in Azure

::: zone pivot="platform-windows"  

[Azure App Service](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie. In deze zelfstudie ziet u hoe u een Node.js-app maakt in App Service op Windows en deze verbindt met een MongoDB-database. Als u klaar bent, beschikt u over een MEAN-toepassing (MongoDB, Express, AngularJS en Node.js) die wordt uitgevoerd in [Azure App Service](overview.md). In het voorbeeld wordt ter vereenvoudiging gebruikgemaakt van het [MEAN.js-webframework](https://meanjs.org/).

::: zone-end

::: zone pivot="platform-linux"


[Azure App Service](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie via het Linux-besturingssysteem. In deze zelfstudie wordt beschreven hoe u een Node.js-app in App Service op Linux maakt, deze lokaal verbindt met een MongoDB-database en vervolgens in een database in de API van Azure Cosmos DB voor MongoDB implementeert. Als u klaar bent, beschikt u over een MEAN-toepassing (MongoDB, Express, AngularJS en Node.js) die wordt uitgevoerd in App Service onder Linux. In het voorbeeld wordt ter vereenvoudiging gebruikgemaakt van het [MEAN.js-webframework](https://meanjs.org/).

::: zone-end

![MEAN.js-app uitgevoerd in Azure App Service](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Wat u leert:

> [!div class="checklist"]
> * Een MongoDB-database maken in Azure
> * Een Node.js-app verbinden met MongoDB
> * De app implementeren in Azure
> * Het gegevensmodel bijwerken en de app opnieuw implementeren
> * Logboeken met diagnostische gegevens vanaf Azure streamen
> * De app in Azure Portal beheren

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

- [Git installeren](https://git-scm.com/)
- [Node.js en NPM installeren](https://nodejs.org/)
- [Bower installeren ](https://bower.io/) (vereist voor [MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started))
- [Gulp.js installeren ](https://gulpjs.com/) (vereist voor [MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started))
- [MongoDB Community Edition installeren en uitvoeren](https://docs.mongodb.com/manual/administration/install-community/)
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)] 

## <a name="test-local-mongodb"></a>MongoDB lokaal testen

Open het terminalvenster en `cd` naar de `bin`-map van uw MongoDB-installatie. U kunt dit terminalvenster gebruiken om alle opdrachten in deze zelfstudie uit te voeren.

Voer `mongo` uit in de terminal om verbinding te maken met de lokale MongoDB-server.

```bash
mongo
```

Als er verbinding wordt gemaakt, wordt de MongoDB-database al uitgevoerd. Zo niet, controleer dan of de lokale MongoDB-database kan worden gestart via de stappen in [Install MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/) (MongoDB Community Edition installeren). Vaak is MongoDB al geïnstalleerd, maar u moet de database nog wel starten door `mongod` uit te voeren. 

Nadat u de MongoDB-database hebt getest, typt u `Ctrl+C` in de terminal. 

## <a name="create-local-nodejs-app"></a>Lokale Node.js-app maken

In deze stap stelt u het lokale Node.js-project in.

### <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Voer in het terminalvenster de opdracht `cd` naar een werkmap uit.  

Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Dit voorbeeld van een opslagplaats bevat een kopie van de [MEAN.js-opslagplaats](https://github.com/meanjs/mean). Deze is gewijzigd om op App Service te kunnen worden uitgevoerd (zie het [README-bestand](https://github.com/Azure-Samples/meanjs/blob/master/README.md) bij de MEAN.js-opslagplaats voor meer informatie).

### <a name="run-the-application"></a>De toepassing uitvoeren

Voer de volgende opdrachten uit om de vereiste pakketten te installeren en de toepassing te starten.

```bash
cd meanjs
npm install
npm start
```

Negeer de waarschuwing over config.domain. Wanneer de app volledig is geladen, ziet u een bericht zoals dit:

<pre>
--
MEAN.JS - Ontwikkelomgeving

Omgeving:     ontwikkelingsserver:          http://0.0.0.0:3000 Database:        mongodb://localhost/mean-dev App version:     0.5.0 MEAN.JS-versie: 0.5.0 --
</pre>

Ga naar `http://localhost:3000` in een browser. Klik op **Registreren** in het bovenste menu en maak een testgebruiker. 

De MEAN.js-voorbeeldtoepassing slaat gebruikersgegevens op in de database. Als de gebruiker is gemaakt en u bent aangemeld, schrijft de app gegevens naar de lokale MongoDB-database.

![MEAN.js maakt een geslaagde verbinding met MongoDB](./media/tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Selecteer **Admin > Manage Articles** om enkele artikelen toe te voegen.

Als u Node.js wilt stoppen, drukt u in de terminal op `Ctrl+C`. 

## <a name="create-production-mongodb"></a>Productie-MongoDB maken

In deze stap maakt u een MongoDB-database in Azure. Als de app is geïmplementeerd in Azure, wordt deze cloud-database gebruikt.

Voor MongoDB wordt in deze zelfstudie gebruikgemaakt van [Azure Cosmos DB](/azure/documentdb/). Cosmos DB ondersteunt MongoDB-clientverbindingen.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-cosmos-db-account"></a>Cosmos DB-account maken

> [!NOTE]
> Er zijn kosten voor het maken van de Azure Cosmos DB-databases in deze zelfstudie in uw eigen Azure-abonnement. Als u gedurende zeven dagen een gratis Azure Cosmos DB-account wilt gebruiken, kunt u de ervaring [Probeer Azure Cosmos DB gratis uit](https://azure.microsoft.com/try/cosmosdb/) gebruiken. Klik gewoon op de knop **Maken** in de tegel MongoDB om een gratis MongoDB-database te maken in Azure. Wanneer de database is gemaakt, gaat u naar **Verbindingsreeks** in de portal en haalt u de verbindingsreeks voor Azure Cosmos DB op die u verderop in de zelfstudie gaat gebruiken.
>

Maak in Cloud Shell een Cosmos DB-account met de opdracht [`az cosmosdb create`](/cli/azure/cosmosdb#az_cosmosdb_create).

Vervang in de volgende opdracht de tijdelijke aanduiding *\<cosmosdb-name>* door een unieke Cosmos DB-naam. Deze naam wordt gebruikt als onderdeel van het Cosmos DB-eindpunt, `https://<cosmosdb-name>.documents.azure.com/`. De naam moet dus uniek zijn voor alle Cosmos DB-accounts in Azure. De naam mag alleen kleine letters, cijfers en het afbreekstreepje (-) bevatten, en moet tussen de 3 en 50 tekens lang zijn.

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

De parameter *--kind MongoDB* maakt MongoDB-clientverbindingen mogelijk.

Wanneer het Cosmos DB-account wordt gemaakt toont de Azure CLI informatie die lijkt op het volgende voorbeeld:

<pre>
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://&lt;cosmosdb-name&gt;.documents.azure.com:443/",
  "failoverPolicies": 
  ...
  &lt; Output truncated for readability &gt;
}
</pre>

## <a name="connect-app-to-production-mongodb"></a>App verbinden met productie-MongoDB

In deze stap verbindt u uw MEAN.js-voorbeeldtoepassing met de Cosmos DB-database die u zojuist hebt gemaakt, met behulp van een MongoDB-verbindingsreeks. 

### <a name="retrieve-the-database-key"></a>De databasesleutel ophalen

U hebt de databasesleutel nodig om verbinding te kunnen maken met de Cosmos DB-database. Gebruik in Cloud Shell de opdracht [`az cosmosdb list-keys`](/cli/azure/cosmosdb#az_cosmosdb_list_keys) om de primaire sleutel op te halen.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb-name> --resource-group myResourceGroup
```

De Azure CLI toont informatie die lijkt op het volgende voorbeeld:

<pre>
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
</pre>

Kopieer de waarde van `primaryMasterKey`. U hebt deze informatie nodig voor de volgende stap.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Configureer de verbindingsreeks in uw Node.js-toepassing

Maak in de lokale MEAN.js-opslagplaats, in de map _config/env/_ , een bestand met de naam _lokale-productie.js_. _.gitignore_ is al geconfigureerd om dit bestand buiten de opslagplaats te houden. 

Kopieer er de volgende code naartoe. Vervang de twee tijdelijke aanduidingen *\<cosmosdb-name>* door de naam van de Cosmos DB-database en vervang de tijdelijke aanduiding *\<primary-master-key>* door de sleutel die u in de vorige stap hebt gekopieerd.

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary-master-key>@<cosmosdb-name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

De optie `ssl=true` is vereist omdat [Cosmos DB SSL vereist](../cosmos-db/connect-mongodb-account.md#connection-string-requirements). 

Sla uw wijzigingen op.

### <a name="test-the-application-in-production-mode"></a>De toepassing in productiemodus testen 

Voer in het lokale terminalvenster de volgende opdracht uit om scripts voor de productieomgeving te minimaliseren en bundelen. In dit proces worden de bestanden gemaakt die nodig zijn in de productieomgeving.

```bash
gulp prod
```

Voer in het lokale terminalvenster de volgende opdracht uit om de verbindingsreeks te gebruiken die u in _config/env/local-production.js_ hebt geconfigureerd. Negeer de certificaatfout en de waarschuwing over config.domain.

```bash
# Bash
NODE_ENV=production node server.js

# Windows PowerShell
$env:NODE_ENV = "production" 
node server.js
```

`NODE_ENV=production` stelt de omgevingsvariabele in die aan Node.js meldt om in de productieomgeving te worden uitgevoerd.  `node server.js` start de Node.js-server met `server.js` in de hoofdmap van de opslagplaats. Op deze manier wordt de Node.js-toepassing in Azure geladen. 

Nadat de app is geladen, controleert u of de app wordt uitgevoerd in de productieomgeving:

<pre>
--
MEAN.JS

Omgeving:     productieserver:          http://0.0.0.0:8443 Database:        mongodb://&lt; cosmosdb-name&gt;:&lt; primary-master-key&gt;@&lt; cosmosdb-name&gt;.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false App version:     0.5.0 MEAN.JS-versie: 0.5.0
</pre>

Ga naar `http://localhost:8443` in een browser. Klik op **Registreren** in het bovenste menu en maak een testgebruiker. Als de gebruiker is gemaakt en u bent aangemeld, schrijft de app gegevens naar de Cosmos DB-database in Azure. 

Stop Node.js door in het terminalvenster `Ctrl+C` te typen. 

## <a name="deploy-app-to-azure"></a>App in Azure implementeren

In deze step implementeert u uw met MongoDB verbonden Node.js-toepassing in Azure App Service.

### <a name="configure-a-deployment-user"></a>Een implementatiegebruiker configureren

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Een App Service-plan maken

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

<a name="create"></a>
### <a name="create-a-web-app"></a>Een webtoepassing maken

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-no-h.md)] 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-linux-no-h.md)] 

::: zone-end

### <a name="configure-an-environment-variable"></a>Een omgevingsvariabele configureren

Standaard houdt het MEAN.js-project _config/env/local-production.js_ buiten de Git-opslagplaats. Voor uw Azure-app gebruikt u dus app-instellingen om de MongoDB-verbindingsreeks te definiëren.

Gebruik de opdracht [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) in Cloud Shell om de app-instellingen te definiëren. 

In het volgende voorbeeld wordt de app-instelling `MONGODB_URI` in de Azure-app geconfigureerd. Vervang de tijdelijke aanduidingen *\<app-name>* , *\<cosmosdb-name>* en *\<primary-master-key>* .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb-name>:<primary-master-key>@<cosmosdb-name>.documents.azure.com:10250/mean?ssl=true"
```

In Node.js-code [opent u deze app-instelling](configure-language-nodejs.md#access-environment-variables) met `process.env.MONGODB_URI`, net zoals u een andere omgevingsvariabele zou openen. 

In uw lokale MEAN.js-opslagplaats opent u _config/env/production.js_ (niet _config/env/local-production.js_), waarvoor een configuratie geldt die specifiek is voor de productieomgeving. De standaard-MEAN.js-app is al geconfigureerd om gebruik te maken van de omgevingsvariabele `MONGODB_URI` die u hebt gemaakt.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="push-to-azure-from-git"></a>Pushen naar Azure vanaf Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      main -> main
</pre>

Het implementatieproces voert [Gulp](https://gulpjs.com/) na `npm install` uit. In App Service worden tijdens de implementatie geen Gulp- of Grunt-taken uitgevoerd, dus deze voorbeeldopslagplaats bevat twee extra bestanden in de hoofdmap om deze in te schakelen: 

- _.deployment_: dit bestand meldt App Service om `bash deploy.sh` uit te voeren als het aangepaste implementatiescript.
- _deploy.sh_: het aangepaste implementatiescript. Als u het bestand bekijkt, ziet u dat `gulp prod` wordt uitgevoerd na `npm install` en `bower install`. 

U kunt deze aanpak gebruiken om een stap toe te voegen aan de op Git gebaseerde implementatie. Als u de Azure-app op enig moment opnieuw start, worden deze automatiseringstaken niet opnieuw uitgevoerd in App Service. Zie [Grunt/Bower/Gulp uitvoeren](configure-language-nodejs.md#run-gruntbowergulp) voor meer informatie.

### <a name="browse-to-the-azure-app"></a>Naar de Azure-app bladeren 

Blader in de webbrowser naar de geïmplementeerde app. 

```bash 
http://<app-name>.azurewebsites.net 
``` 

Klik op **Registreren** in het bovenste menu en maak een dummygebruiker aan. 

Als de app automatisch voor de aangemaakte gebruiker wordt aangemeld, dan heeft de MEAN.js-app in Azure verbinding met de MongoDB (Cosmos DB)-database. 

![MEAN.js-app uitgevoerd in Azure App Service](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Selecteer **Admin > Manage Articles** om enkele artikelen toe te voegen. 

**Gefeliciteerd!** U voert een gegevensgestuurde Node.js-app uit in Azure App Service.

## <a name="update-data-model-and-redeploy"></a>Gegevensmodel bijwerken en opnieuw implementeren

In deze stap wijzigt u het `article`-gegevensmodel en publiceert u de wijziging in Azure.

### <a name="update-the-data-model"></a>Gegevensmodel bijwerken

Open _modules/articles/server/models/article.server.model.js_ in de lokale MEAN.js-opslagplaats.

Voeg een `String`-type met de naam `comment` toe in `ArticleSchema`. Als u klaar bent, ziet het schema er als volgt uit:

```javascript
const ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

### <a name="update-the-articles-code"></a>Artikelencode bijwerken

Werk de rest van de `articles`-code bij om `comment` te gebruiken.

U moet vijf bestanden wijzigen: de servercontroller en de vier clientweergaven. 

Open _modules/articles/server/controllers/articles.server.controller.js_.

Voeg aan de `update`-functie een toewijzing voor `article.comment` toe. De volgende code toont de voltooide `update`-functie:

```javascript
exports.update = function (req, res) {
  let article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

Open _modules/articles/client/views/view-article.client.view.html_.

Voeg vlak boven de afsluitende `</section>`-tag de volgende regel toe om `comment` samen met de rest van de artikelgegevens weer te geven:

```html
<p class="lead" ng-bind="vm.article.comment"></p>
```

Open _modules/articles/client/views/list-articles.client.view.html_.

Voeg vlak boven de afsluitende `</a>`-tag de volgende regel toe om `comment` samen met de rest van de artikelgegevens weer te geven:

```html
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Open _modules/articles/client/views/admin/list-articles.client.view.html_.

Voeg binnen het `<div class="list-group">`-element en vlak boven de afsluitende `</a>`-tag de volgende regel toe om `comment` samen met de rest van de artikelgegevens weer te geven:

```html
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Open _modules/articles/client/views/admin/form-article.client.view.html_.

Zoek het `<div class="form-group">`-element met de verzendknop, dat er als volgt uitziet:

```html
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Voeg vlak boven deze tag een ander `<div class="form-group">`-element toe waarmee een gebruiker het `comment`-veld kan bewerken. Het nieuwe element ziet er als volgt uit:

```html
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>De wijzigingen lokaal testen

Sla al uw wijzigingen op.

Test in het lokale terminalvenster opnieuw uw wijzigingen in productiemodus.

```bash
# Bash
gulp prod
NODE_ENV=production node server.js

# Windows PowerShell
gulp prod
$env:NODE_ENV = "production" 
node server.js
```

Ga in een browser naar `http://localhost:8443` en zorg dat u bent aangemeld.

Selecteer **Admin > Manage Articles** en voeg een artikel toe door de **+** -knop te selecteren.

U ziet nu het nieuwe tekstvak `Comment`.

![Aan Artikelen toegevoegd veld voor o0pmerkingen](./media/tutorial-nodejs-mongodb-app/added-comment-field.png)

Stop Node.js door in het terminalvenster `Ctrl+C` te typen. 

### <a name="publish-changes-to-azure"></a>Wijzigingen publiceren in Azure

Leg in het lokale terminalvenster uw wijzigingen vast in Git en push de codewijzigingen naar Azure.

```bash
git commit -am "added article comment"
git push azure main
```

Zodra `git push` is voltooid, gaat u naar de Azure-app en probeert u de nieuwe functionaliteit uit.

![In Azure gepubliceerde model- en databasewijzigingen](media/tutorial-nodejs-mongodb-app/added-comment-field-published.png)

Als u eerder artikelen hebt toegevoegd, kunt u deze nog steeds zien. Bestaande gegevens in Cosmos DB zijn nog aanwezig. En uw updates aan het gegevensschema zijn nog intact.

## <a name="stream-diagnostic-logs"></a>Diagnostische logboeken streamen 

::: zone pivot="platform-windows"  

Terwijl uw Node.js-toepassing in Azure App Service wordt uitgevoerd, kunt u de consolelogboeken doorgesluisd krijgen naar uw terminal. Op die manier krijgt u de dezelfde diagnostische berichten om toepassingsfouten op te sporen.

Gebruik voor het starten van logboekstreaming de opdracht [`az webapp log tail`](/cli/azure/webapp/log#az_webapp_log_tail) in de Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
``` 

Nadat logboekstreaming is gestart, vernieuwt u de Azure-app in de browser om wat webverkeer te genereren. U ziet dat consolelogboeken nu worden doorgegeven aan uw terminal.

U kunt logboekstreaming op elk gewenst moment stoppen door te drukken op `Ctrl+C`. 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

## <a name="manage-your-azure-app"></a>Uw Azure-app beheren

Ga naar de [Azure-portal](https://portal.azure.com) om de app te zien die u hebt gemaakt.

Klik in het linkermenu op **App Services** en klik op de naam van uw Azure-app.

![Navigatie naar Azure-app in de portal](./media/tutorial-nodejs-mongodb-app/access-portal.png)

In de portal wordt standaard de pagina **Overzicht** van de app weergegeven. Deze pagina geeft u een overzicht van hoe uw app presteert. Hier kunt u ook algemene beheertaken uitvoeren, zoals bladeren, stoppen, starten, opnieuw opstarten en verwijderen. De tabbladen aan de linkerkant van de pagina tonen de verschillende configuratiepagina's die u kunt openen.

![App Service-pagina in Azure Portal](./media/tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Volgende stappen

Wat u hebt geleerd:

> [!div class="checklist"]
> * Een MongoDB-database maken in Azure
> * Een Node.js-app verbinden met MongoDB
> * De app implementeren in Azure
> * Het gegevensmodel bijwerken en de app opnieuw implementeren
> * Logboeken vanaf Azure naar uw terminal streamen
> * De app in Azure Portal beheren

Ga door naar de volgende zelfstudie om te leren hoe u een aangepaste DNS-naam aan een web-app kunt toewijzen.

> [!div class="nextstepaction"] 
> [Een bestaande aangepaste DNS-naam toewijzen aan Azure App Service](app-service-web-tutorial-custom-domain.md)

U kunt ook andere resources bekijken:

> [!div class="nextstepaction"]
> [Node.js-app configureren](configure-language-nodejs.md)
