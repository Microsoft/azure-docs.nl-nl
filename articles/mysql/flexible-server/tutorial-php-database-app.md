---
title: 'Zelfstudie: Een PHP-app (Laravel) bouwen met Azure Database for MySQL Flexible Server'
description: In deze zelfstudie wordt uitgelegd hoe u een PHP-app bouwt met een flexibele server.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: tutorial
ms.devlang: php
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 426cf59c9fb9d88039231ed441b2ffc7246716c7
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844434"
---
# <a name="tutorial-build-a-php-laravel-and-mysql-flexible-server-preview-app-in-azure-app-service"></a>Zelfstudie: Een PHP-app (Laravel) en een MySQL Flexible Server-app (preview) bouwen in Azure App Service


:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="PHP-web-app in Azure met Flexible Server":::

[Azure App Service](../../app-service/overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie via het Linux-besturingssysteem. In deze zelfstudie wordt getoond hoe u een PHP-app in Azure maakt en hoe u deze verbinding laat maken met een MySQL-database. Wanneer u klaar bent, hebt u een [Laravel](https://laravel.com/)-app die in Azure App Service op Linux wordt uitgevoerd.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een PHP-app (Laravel) instellen met lokale MySQL
> * Een MySQL Flexible Server maken (preview)
> * Een PHP-app verbinden met MySQL Flexible Server (preview)
> * De app implementeren in Azure App Service
> * Het gegevensmodel bijwerken en de app opnieuw implementeren
> * De app in Azure Portal beheren

Als u geen [Azure-abonnement](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

1. [Git installeren](https://git-scm.com/)
2. [Installeer PHP 5.6.4 of hoger](https://php.net/downloads.php)
3. [Composer installeren](https://getcomposer.org/doc/00-intro.md)
4. Schakel de volgende PHP-extensies in die nodig zijn voor Laravel: OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML
5. [MySQL installeren en starten](https://dev.mysql.com/doc/refman/5.7/en/installing.html)

## <a name="prepare-local-mysql"></a>Lokale MySQL voorbereiden

In deze stap maakt u een database in uw lokale MySQL-server voor gebruik in deze zelfstudie.

### <a name="connect-to-local-mysql-server"></a>Verbinding maken met lokale MySQL-server

Maak in een terminalvenster verbinding met uw lokale MySQL-server. U kunt dit terminalvenster gebruiken om alle opdrachten in deze zelfstudie uit te voeren.

```bash
mysql -u root -p
```

Als u wordt gevraagd om een wachtwoord, voert u het wachtwoord in voor het `root`-account. Als u het wachtwoord van uw hoofdaccount niet meer weet, raadpleegt u [MySQL: Het hoofdwachtwoord opnieuw instellen](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Als uw opdracht succesvol is uitgevoerd, wordt uw MySQL-server uitgevoerd. Als dit niet het geval is, zorgt u ervoor dat de lokale MySQL-server is gestart door de [Stappen na installatie van MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html) te volgen.

### <a name="create-a-database-locally"></a>Lokaal een database maken

Maak een database bij de prompt `mysql`.

```sql
CREATE DATABASE sampledb;
```

Sluit uw serververbinding door `quit` te typen.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>Lokaal een PHP-app maken
In deze stap krijgt u een Laravel-voorbeeld-app, configureert u de databaseverbinding en voert u deze lokaal uit.

### <a name="clone-the-sample"></a>Het voorbeeld klonen

Navigeer in het terminalvenster naar een lege map waar u de voorbeeldtoepassing kunt klonen.  Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen.

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

`cd` naar de gekloonde map.
Installeer de vereiste pakketten.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>MySQL-verbinding configureren

Maak een bestand met de naam *.env* in de hoofdmap van de opslagplaats. Kopieer de volgende variabelen in het bestand *.env*. Vervang de tijdelijke aanduiding _&lt;root_password>_ met het wachtwoord van de MySQL-hoofdgebruiker.

```txt
APP_ENV=local
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

Zie [Laravel-omgeving configureren](https://laravel.com/docs/5.4/configuration#environment-configuration) voor informatie over hoe Laravel het _.env_-bestand gebruikt.

### <a name="run-the-sample-locally"></a>Het voorbeeld lokaal uitvoeren

Voer [Laravel-databasemigraties](https://laravel.com/docs/5.4/migrations) uit om de tabellen te maken die de toepassing nodig heeft. Als u wilt zien welke tabellen worden gemaakt in de migraties, zoek dan in de map _database/migrations_ in de Git-opslagplaats.

```bash
php artisan migrate
```

Genereer een nieuwe sleutel voor de Laravel-toepassing.

```bash
php artisan key:generate
```

Voer de toepassing uit.

```bash
php artisan serve
```

Ga naar `http://localhost:8000` in een browser. Voeg een paar taken op de pagina toe.

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="PHP maakt verbinding met MySQL":::

Typ `Ctrl + C` in de terminal om PHP te stoppen.

## <a name="create-a-mysql-flexible-server-preview"></a>Een MySQL Flexible Server maken (preview)
In deze stap maakt u een MySQL-database in [Azure Database for MySQL Flexible Server](../index.yml) die in openbare preview is. Later configureert u de PHP-toepassing om verbinding te maken met deze database. Maak vanuit de [Azure Cloud Shell](../../cloud-shell/overview.md) een server met behulp van de opdracht [`az flexible-server create`](/cli/azure/mysql/server#az-mysql-flexible-server-create).

```azurecli-interactive
az mysql flexible-server create  --resource-group myResourceGroup --public-access <IP-Address>
```

> [!IMPORTANT]
> - Noteer de **servernaam** en **verbindingsreeks** om deze te gebruiken in de volgende stap om verbinding te maken en Laravel-gegevensmigratie uit te voeren.
> - Geef het IP-adres van uw clientcomputer op voor het argument **IP-Address**. De server is vergrendeld wanneer deze is gemaakt en u moet toegang tot uw clientcomputer toestaan om de server lokaal te beheren.

### <a name="configure-server-firewall-to-allow-web-app-to-connect-to-the-server"></a>Serverfirewall configureren zodat web-apps verbinding kunnen maken met de server

Maak in de Cloud Shell een firewallregel voor uw MySQL-server om clientverbindingen toe te staan met behulp van de opdracht az mysql server firewall-rule create. Als zowel het IP-beginadres als het IP-eindadres zijn ingesteld op ```0.0.0.0```, wordt de firewall alleen geopend voor andere Azure-Services die geen statisch IP-adres hebben om verbinding te maken met de server.

```azurecli
az mysql flexible-server firewall-rule create --name allanyAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="connect-to-production-mysql-server-locally"></a>Lokaal verbinding maken met MySQL-server in productie

Maak in het lokale terminalvenster verbinding met de MySQL-server in Azure. Gebruik de waarde die u eerder hebt opgegeven voor ```<admin-user>``` en ```<mysql-server-name>```. Wanneer u wordt gevraagd om een wachtwoord, gebruikt u het wachtwoord dat u hebt opgegeven bij het maken van de database in Azure.

```bash
mysql -u <admin-user> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>Een productiedatabase maken

Maak een database bij de prompt `mysql`.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Een gebruiker met machtigingen maken

Maak een databasegebruiker met de naam _phpappuser_ en geef deze alle bevoegdheden in de `sampledb`-database. Gebruik ten behoeve van de eenvoud van de zelfstudie _MySQLAzure2020_ als het wachtwoord.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2020';
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Sluit uw serververbinding door `quit` te typen.

```sql
quit
```

## <a name="connect-app-to-mysql-flexible-server"></a>De app verbinden met MySQL Flexible Server

In deze stap verbindt u de PHP-toepassing met de MySQL-database die u in Azure Database for MySQL hebt gemaakt.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Verbinding met de database configureren

Maak in de hoofdmap van de opslagplaats een _.env.production_-bestand en kopieer de volgende variabelen ernaartoe. Vervang de tijdelijke aanduiding _&lt;mysql-server-name>_ in zowel *DB_HOST* als *DB_USERNAME*.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Sla de wijzigingen op.

> [!TIP]
> Dit bestand is voor het beveiligen van uw MySQL-verbindingsgegevens al uitgesloten van de Git-opslagplaats (zie _.gitignore_ in de hoofdmap van de opslagplaats). Later leert u hoe u omgevingsvariabelen in App Service configureert om verbinding te maken met uw database in Azure Database for MySQL. Met omgevingsvariabelen heeft u het *.env*-bestand in App Service niet nodig.
>

### <a name="configure-tlsssl-certificate"></a>TLS-/SSL-certificaat configureren

MySQL Flexible Server dwingt standaard TLS-verbindingen van clients af. Voor verbinding met uw MySQL-database in Azure moet u het [ _.pem_-certificaat gebruiken dat is opgegeven door Azure Database for MySQL Flexible Server](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem). Download [dit certificaat](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) en plaats het in de map **SSL** in het lokale exemplaar van de opslagplaats van de voorbeeld-app.

Open _config/database.php_ en voeg de parameters `sslmode` en `options` toe aan `connections.mysql`, zoals wordt weergegeven in de volgende code.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL') && extension_loaded('pdo_mysql')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/DigiCertGlobalRootCA.crt.pem',
    ] : []
],
```

### <a name="test-the-application-locally"></a>De toepassing lokaal testen

Voer Laravel-databasemigraties uit met _.env.production_ als het omgevingsbestand om de tabellen te maken in uw MySQL-database in Azure Database for MySQL. Vergeet niet dat _. env.production_ de verbindingsgegevens heeft voor uw MySQL-database in Azure.

```bash
php artisan migrate --env=production --force
```

_. env.production_ heeft nog geen geldige toepassingssleutel. Genereer hiervoor een nieuwe in de terminal.

```bash
php artisan key:generate --env=production --force
```

Voer de voorbeeldtoepassing uit met _. env.production_ als het omgevingsbestand.

```bash
php artisan serve --env=production
```

Navigeer naar `http://localhost:8000`. Als de pagina zonder fouten wordt geladen, maakt de PHP-toepassing verbinding met de MySQL-database in Azure.

Voeg een paar taken op de pagina toe.

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="PHP maakt verbinding met Azure Database for MySQL":::

Typ `Ctrl + C` in de terminal om PHP te stoppen.

### <a name="commit-your-changes"></a>Uw wijzigingen doorvoeren

Voer de volgende Git-opdrachten uit om uw wijzigingen door te voeren:

```bash
git add .
git commit -m "database.php updates"
```

Uw app kan worden geïmplementeerd.

## <a name="deploy-to-azure"></a>Implementeren in Azure

In deze stap implementeert u de met MySQL verbonden PHP-toepassing naar Azure App Service.

### <a name="configure-a-deployment-user"></a>Een implementatiegebruiker configureren

FTP en lokale Git kunnen worden geïmplementeerd in een Azure-web-app met behulp van een implementatiegebruikers. Zodra u deze implementatiegebruiker hebt gemaakt, kunt u deze voor al uw Azure-implementaties gebruiken. Uw gebruikersnaam en wachtwoord voor implementatie op accountniveau verschillen van de referenties voor uw Azure-abonnement.

Als u de implementatiegebruiker wilt configureren, voert u de opdracht [az webapp deployment user set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) uit in Azure Cloud Shell. Vervang _&lt;gebruikersnaam >_ en _&lt;wachtwoord >_ door de gebruikersnaam en het wachtwoord van uw implementatiegebruiker.

De gebruikersnaam moet uniek zijn binnen Azure en voor lokale Git-pushes en mag het symbool @ niet bevatten.
Het wachtwoord moet ten minste acht tekens lang zijn en minimaal twee van de volgende drie typen elementen bevatten: letters, cijfers en symbolen.

```bash
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux
```

De JSON-uitvoer toont het wachtwoord als null. Als u de fout 'Conflict'. Details: 409 krijgt, wijzigt u de gebruikersnaam. Als u de fout 'Bad Request'. Details: 400 krijgt, kiest u een sterker wachtwoord. **Noteer uw gebruikersnaam en wachtwoord om te gebruiken bij het implementeren van uw web-apps.**

### <a name="create-an-app-service-plan"></a>Een App Service-plan maken

Maak in de Cloud Shell een App Service-plan in de resourcegroep met de opdracht [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create). In het volgende voorbeeld wordt een App Service-plan gemaakt met de naam myAppServicePlan in de prijscategorie Gratis (--sku F1) en in een Linux-container (--is-linux).

az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux

<a name="create"></a>

### <a name="create-a-web-app"></a>Een webtoepassing maken

Maak een [web-app](../../app-service/overview.md#app-service-on-linux) in het App Service-plan myAppServicePlan.

In de Cloud Shell kunt u de opdracht [az webapp create](/cli/azure/webapp#az-webapp-create) gebruiken. Vervang in het volgende voorbeeld _&lt;app-name>_ door een unieke naam (geldige tekens zijn `a-z`, `0-9` en `-`). De runtime is ingesteld op `PHP|7.0`. Voer [az webapp list-runtimes --linux](/cli/azure/webapp#az-webapp-list-runtimes) uit om alle ondersteunde runtimes te bekijken.

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
```

Wanneer de web-app is gemaakt, toont de Azure CLI soortgelijke uitvoer als in het volgende voorbeeld:

```
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

U hebt een nieuwe lege web-app gemaakt, met Git-implementatie ingeschakeld.

> [!NOTE]
> De URL van de externe Git wordt weergegeven in de eigenschap deploymentLocalGitUrl, in de indeling https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git. Sla deze URL op, want u hebt deze later nodig.

### <a name="configure-database-settings"></a>Database-instellingen configureren

In App Service stelt u de omgevingsvariabelen in op _app-instellingen_ met behulp van de opdracht [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set).

De volgende opdracht configureert de app-instellingen `DB_HOST`, `DB_DATABASE`, `DB_USERNAME`, en `DB_PASSWORD`. Vervang de tijdelijke aanduidingen _&lt;app-name>_ en _&lt;mysql-server-name>_ .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

U kunt de PHP-methode [getenv](https://www.php.net/manual/en/function.getenv.php) gebruiken voor toegang tot de instellingen. De Laravel-code gebruikt een [env](https://laravel.com/docs/5.4/helpers#method-env)-wrapper boven de PHP `getenv`. Zo is de MySQL-configuratie in _config/database.php_ bijvoorbeeld vergelijkbaar met de volgende code:

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Laravel-omgevingsvariabelen configureren en instellen

Laravel heeft een toepassingssleutel nodig in App Service. U kunt deze configureren met app-instellingen.

In het lokale terminalvenster gebruikt u `php artisan` voor het genereren van een nieuwe toepassingssleutel zonder deze op te slaan in _.env_.

```bash
php artisan key:generate --show
```

In de Cloud Shell stelt u de toepassingssleutel in de App Service-app in met behulp van de opdracht [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set). Vervang de tijdelijke aanduidingen _&lt;app-name>_ en _&lt;outputofphpartisankey:generate>_ .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` geeft Laravel de opdracht foutopsporingsinformatie te retourneren wanneer de geïmplementeerde app fouten tegenkomt. Als een productietoepassing wordt uitgevoerd, stelt u deze in op `false`, wat een veiliger optie is.

### <a name="set-the-virtual-application-path"></a>Het pad van de virtuele toepassing instellen

[Levenscyclus van de Laravel-toepassing](https://laravel.com/docs/5.4/lifecycle) begint in de map _public_ en niet in de hoofdmap van de toepassing. De standaardinstallatiekopie van PHP Docker voor App Service maakt gebruik van Apache, dit laat u de `DocumentRoot` niet aanpassen voor Laravel. U kunt echter `.htaccess` gebruiken om alle aanvragen te herschrijven, zodat zij verwijzen naar _/openbaar_ in plaats van naar de hoofdmap. In de hoofdmap van de opslagplaats is voor dit doel al een `.htaccess` toegevoegd. Uw Laravel-toepassing is hiermee gereed om te worden geïmplementeerd.

Zie [Sitehoofdmap wijzigen](../../app-service/configure-language-php.md?pivots=platform-linux#change-site-root) voor meer informatie.

### <a name="push-to-azure-from-git"></a>Pushen naar Azure vanaf Git

Voeg, eenmaal terug in het lokale terminalvenster, een externe Azure-instantie toe aan uw lokale Git-opslagplaats. Vervang _&lt;deploymentLocalGitUrl-from-create-step>_ door de URL van de externe Git-instantie die u hebt opgeslagen in [Een web-app maken](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Push naar de externe Azure-instantie om uw app te implementeren met de volgende opdracht. Wanneer Git Credential Manager u om referenties vraagt, geeft u de referenties op die u hebt gemaakt in **Een implementatiegebruiker configureren**, en niet de referenties die u gebruikt om u aan te melden bij de Azure-portal.

```bash
git push azure master
```

Het kan enkele minuten duren voor deze opdracht is uitgevoerd. De opdracht geeft informatie weer die lijkt op het volgende voorbeeld:

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

### <a name="browse-to-the-azure-app"></a>Naar de Azure-app bladeren

Blader naar `http://<app-name>.azurewebsites.net` en voeg een paar taken toe aan de lijst.

:::image type="content" source="media/tutorial-php-database-app/php-mysql-in-azure.png" alt-text="PHP-web-app in Azure":::

U voert nu een gegevensgestuurde PHP-app uit in Azure App Service.

## <a name="update-model-locally-and-redeploy"></a>Het model lokaal bijwerken en opnieuw implementeren

In deze stap maakt u een eenvoudige wijziging in het `task`-gegevensmodel en de web-app en publiceert u de update vervolgens naar Azure.

Voor het takenscenario wijzigt u de toepassing, zodat u een taak als voltooid kunt markeren.

### <a name="add-a-column"></a>Een kolom toevoegen

Ga in het lokale terminalvenster naar de hoofdmap van de Git-opslagplaats.

Genereer een nieuwe databasemigratie voor de `tasks`-tabel:

```bash
php artisan make:migration add_complete_column --table=tasks
```

Deze opdracht laat u de naam zien van het migratiebestand dat wordt gegenereerd. Zoek dit bestand in _database/migrations_ en open het.

Vervang de `up`-methode door de volgende code:

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

De bovenstaande code voegt een Booleaanse kolom toe aan de tabel `tasks` met de naam `complete`.

Vervang de methode `down` door de volgende code voor de terugdraaiactie:

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

Voer in het lokale terminalvenster Laravel-database-migraties uit om de wijziging in de lokale database te maken.

```bash
php artisan migrate
```

Op basis van de [Laravel-naamgevingsconventie](https://laravel.com/docs/5.4/eloquent#defining-models) wordt het model `Task` (zie _app/Task.php_) standaard toegewezen aan de `tasks`-tabel.

### <a name="update-application-logic"></a>Toepassingslogica bijwerken

Open het bestand *routes/web.php*. De toepassing definieert hier de routes en bedrijfslogica.

Voeg aan het einde van het bestand een route toe met de volgende code:

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

De bovenstaande code voert een eenvoudige update uit op het gegevensmodel door het omschakelen van de waarde van `complete`.

### <a name="update-the-view"></a>De weergave bijwerken

Open het bestand *resources/views/tasks.blade.php*. Zoek de `<tr>`-openingstag en vervang deze door:

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

De bovenstaande code verandert de rijkleur, afhankelijk van of de taak voltooid is.

U hebt de volgende code in deze regel:

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

Vervang de gehele regel door de volgende code:

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

De bovenstaande code voegt de verzendknop toe die verwijst naar de route die u eerder hebt gedefinieerd.

### <a name="test-the-changes-locally"></a>De wijzigingen lokaal testen

Voer in het lokale terminalvenster de ontwikkelaarsserver uit vanuit de hoofdmap van de Git-opslagplaats.

```bash
php artisan serve
```

Om de status van de taak te zien wijzigen, gaat u naar `http://localhost:8000` en selecteert u het selectievakje.

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox.png" alt-text="Selectievakje toegevoegd aan de taak":::

Typ `Ctrl + C` in de terminal om PHP te stoppen.

### <a name="publish-changes-to-azure"></a>Wijzigingen publiceren in Azure

Voer in het lokale terminalvenster Laravel-databasemigraties uit met de productieverbindingsreeks om de wijziging te maken in de Azure-database.

```bash
php artisan migrate --env=production --force
```

Voer uw wijzigingen door in Git en push de codewijzigingen vervolgens naar Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Zodra `git push` is voltooid, gaat u naar de Azure-app en probeert u de nieuwe functionaliteit uit.

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="In Azure gepubliceerde model- en databasewijzigingen":::

Als u taken hebt toegevoegd, worden deze in de database bewaard. Updates van het gegevensschema laten bestaande gegevens intact.

## <a name="clean-up-resources"></a>Resources opschonen
In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. Als u deze resources niet meer nodig denkt te hebben, verwijdert u de resourcegroep door de volgende opdracht in Cloud Shell uit te voeren:

```bash
az group delete --name myResourceGroup
```

<a name="next"></a>

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uw resources beheren in Azure Portal](../../azure-resource-manager/management/manage-resources-portal.md) <br/>
> [!div class="nextstepaction"]
> [Uw server beheren](how-to-manage-server-cli.md)
