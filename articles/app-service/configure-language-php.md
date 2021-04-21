---
title: PHP-apps configureren
description: Meer informatie over het configureren van een PHP-app in de systeemeigen Windows-exemplaren of in een vooraf gebouwde PHP-container in Azure App Service. In dit artikel worden de meest algemene configuratietaken beschreven.
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 94cbe0fa6669546cee8e989a6db2fcbb428cb9d0
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829436"
---
# <a name="configure-a-php-app-for-azure-app-service"></a>Een PHP-app configureren voor Azure App Service

Deze handleiding laat zien hoe u uw PHP-web-apps, mobiele back-ends en API-apps configureert in Azure App Service.

Deze handleiding bevat belangrijke concepten en instructies voor PHP-ontwikkelaars die apps implementeren in App Service. Als u deze zelfstudie nog Azure App Service, volgt u eerst de [php-quickstart](quickstart-php.md) en [PHP met MySQL-zelfstudie.](tutorial-php-mysql-app.md)

## <a name="show-php-version"></a>PHP-versie tonen

::: zone pivot="platform-windows"  

Als u de huidige PHP-versie wilt zien, moet u de volgende opdracht uitvoeren in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

> [!NOTE]
> Als u een ontwikkelingssleuf wilt aanpakken, moet u de parameter `--slot` opnemen, gevolgd door de naam van de sleuf.

Als u alle ondersteunde PHP-versies wilt zien, moet u de volgende opdracht uitvoeren in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep php
```

::: zone-end

::: zone pivot="platform-linux"

Als u de huidige PHP-versie wilt zien, moet u de volgende opdracht uitvoeren in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

> [!NOTE]
> Als u een ontwikkelingssleuf wilt aanpakken, moet u de parameter `--slot` opnemen, gevolgd door de naam van de sleuf.

Als u alle ondersteunde PHP-versies wilt zien, moet u de volgende opdracht uitvoeren in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

::: zone-end

## <a name="set-php-version"></a>PHP-versie instellen

::: zone pivot="platform-windows"  

Voer de volgende opdracht uit in [Cloud Shell](https://shell.azure.com) php-versie in te stellen op 7.4:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --php-version 7.4
```

::: zone-end

::: zone pivot="platform-linux"

Voer de volgende opdracht uit in [Cloud Shell](https://shell.azure.com) PHP-versie in te stellen op 7.2:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PHP|7.2"
```

::: zone-end

::: zone pivot="platform-windows"  

## <a name="run-composer"></a>Composer uitvoeren

Als u wilt App Service [Composer](https://getcomposer.org/) tijdens de implementatie uit te voeren, is de eenvoudigste manier om Composer op te nemen in uw opslagplaats.

Wijzig vanuit een lokaal terminalvenster de map in de hoofdmap van uw opslagplaats en volg de instructies in [Composer](https://getcomposer.org/download/) downloaden om *composer.phar* te downloaden naar de hoofdmap van de map.

Voer de volgende opdrachten uit (u [moet npm installeren):](https://www.npmjs.com/get-npm)

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

De hoofdmap van uw opslagplaats heeft nu twee extra bestanden: *.deployment* *en deploy.sh*.

Open *deploy.sh* en zoek de `Deployment` sectie die er als de volgende uitziet:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Voeg de codesectie toe die u nodig hebt om het vereiste hulpprogramma aan *het einde van de* sectie uit te `Deployment` voeren:

```bash
# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
```

Commit al uw wijzigingen en implementeer uw code met behulp van Git of Zip deploy met buildautomatisering ingeschakeld. Composer moet nu worden uitgevoerd als onderdeel van implementatieautomatisering.

## <a name="run-gruntbowergulp"></a>RunCret/Bower/Gulp

Als u App Service automatiseringshulpprogramma's wilt uitvoeren tijdens de implementatie, zoals Bowt, Bower of Gulp, moet u een aangepast implementatiescript [leveren.](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) App Service script wordt uitgevoerd wanneer u implementeert met Git of als [zip-implementatie](deploy-zip.md) met buildautomatisering is ingeschakeld. 

Als u wilt dat uw opslagplaats deze hulpprogramma's kan uitvoeren, moet u ze toevoegen aan de afhankelijkheden in *package.jsingeschakeld.* Bijvoorbeeld:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Ga in een lokaal terminalvenster naar de hoofdmap van uw opslagplaats en voer de volgende opdrachten uit [(u moet npm installeren):](https://www.npmjs.com/get-npm)

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

De hoofdmap van uw opslagplaats heeft nu twee extra bestanden: *.deployment* *en deploy.sh*.

Open *deploy.sh* en zoek de `Deployment` sectie die er als de volgende uitziet:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Deze sectie eindigt met het uitvoeren van `npm install --production` . Voeg de codesectie toe die u nodig hebt om het vereiste hulpprogramma aan *het einde van de* sectie uit te `Deployment` voeren:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Zie een [voorbeeld in het voorbeeld MEAN.js voorbeeld](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), waarin met het implementatiescript ook een aangepaste opdracht wordt `npm install` uitgevoerd.

### <a name="bower"></a>Bower

Met dit fragment wordt `bower install` uitgevoerd.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Met dit fragment wordt `gulp imagemin` uitgevoerd.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Met dit fragment wordt `grunt` uitgevoerd.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>De automatisering van bouwbewerkingen aanpassen

Als u uw app wilt implementeren met behulp van Git of zip-pakketten waarbij bouwautomatisering is ingeschakeld, moet u de volgende stappen voor de App Service-bouwautomatisering in deze volgorde uitvoeren:

1. Voer aangepast script uit als dit door `PRE_BUILD_SCRIPT_PATH` is opgegeven.
1. Voer `php composer.phar install` uit.
1. Voer aangepast script uit als dit is opgegeven door `POST_BUILD_SCRIPT_PATH`.

`PRE_BUILD_COMMAND` en `POST_BUILD_COMMAND` zijn omgevingsvariabelen die standaard leeg zijn. Als u vooraf gebouwde opdrachten wilt uitvoeren, definieert u `PRE_BUILD_COMMAND`. Als u achteraf gebouwde opdrachten wilt uitvoeren, definieert u `POST_BUILD_COMMAND`.

In het volgende voorbeeld worden de twee variabelen voor een reeks opdrachten opgegeven, gescheiden door komma's.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Zie [Oryx-configuratie](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md) voor aanvullende omgevingsvariabelen om bouwautomatisering aan te passen.

Zie App Service [Oryx-documentatie: Hoe PHP-apps](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md)worden gedetecteerd en gebouwd voor meer informatie over hoe php-apps worden uitgevoerd en gebouwd in Linux.

## <a name="customize-start-up"></a>Opstarten aanpassen

Met de ingebouwde PHP-container wordt standaard de Apache-server uitgevoerd. Bij het starten wordt `apache2ctl -D FOREGROUND"` uitgevoerd. Als u wilt, kunt u bij het starten een andere opdracht uitvoeren door de volgende opdracht uit te voeren in [de Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

::: zone-end

## <a name="access-environment-variables"></a>Toegang tot omgevingsvariabelen

In App Service kunt u [app-instellingen configureren](configure-common.md#configure-app-settings) buiten uw app-code. Vervolgens kunt u ze openen met behulp van het [standaard getenv()-patroon.](https://secure.php.net/manual/function.getenv.php) Voor toegang tot bijvoorbeeld de app-instelling `DB_HOST` gebruikt u de volgende code:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Site-hoofdmap wijzigen

::: zone pivot="platform-windows"  

Het web-framework van uw keuze kan een submap gebruiken als de hoofdmap van de site. Laravel gebruikt [bijvoorbeeld](https://laravel.com/)de submap *public/als* de hoofdmap van de site.

Als u de hoofdmap van de site wilt aanpassen, stelt u het pad voor de virtuele toepassing voor de app in met behulp van de [`az resource update`](/cli/azure/resource#az_resource_update) opdracht . In het volgende voorbeeld wordt de hoofdmap van de site in de *openbare/submap* in uw opslagplaats geplaatst. 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Azure App Service wijst standaard het hoofdpad voor de virtuele toepassing ( _/_ ) toe aan de hoofdmap van de geïmplementeerde toepassingsbestanden (_sites\wwwroot_).

::: zone-end

::: zone pivot="platform-linux"

Het web-framework van uw keuze kan een submap gebruiken als de hoofdmap van de site. Laravel gebruikt [bijvoorbeeld](https://laravel.com/)de `public/` submap als de hoofdmap van de site.

De standaard PHP-afbeelding voor App Service maakt gebruik van Apache en hiermee kunt u de hoofdmap van de site voor uw app niet aanpassen. U kunt deze beperking omzeilen door een *.htaccess-bestand* toe te voegen aan de hoofdmap van uw opslagplaats met de volgende inhoud:

```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} ^(.*)
    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

Als u echter liever niet *.htaccess* herschrijft, kunt u uw Laravel-toepassing in plaats daarvan met een [aangepaste Docker-installatiekopie](quickstart-custom-container.md) implementeren.

::: zone-end

## <a name="detect-https-session"></a>HTTPS-sessie detecteren

In App Service vindt [SSL-beëindiging](https://wikipedia.org/wiki/TLS_termination_proxy) plaats in de load balancers voor het netwerk, zodat alle HTTPS-aanvragen uw app bereiken als niet-versleutelde HTTP-aanvragen. Inspecteer de header `X-Forwarded-Proto` als de app-logica moet controleren of de aanvragen van gebruikers al dan niet zijn versleuteld.

```php
if (isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https') {
// Do something when HTTPS is used
}
```

Populaire webframeworks bieden toegang tot de `X-Forwarded-*`-informatie in het patroon van de standaard-app. In [CodeIgniter](https://codeigniter.com/) wordt met [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) standaard de waarde van `X_FORWARDED_PROTO` gecontroleerd.

## <a name="customize-phpini-settings"></a>Instellingen voor php.ini aanpassen

Als u wijzigingen moet aanbrengen in uw PHP-installatie, kunt u een van dephp.ini [ wijzigen](https://www.php.net/manual/ini.list.php) door deze stappen te volgen.

> [!NOTE]
> De beste manier om de PHP-versie en de huidigephp.inizien, is [door phpinfo() aan te](https://php.net/manual/function.phpinfo.php) roepen in uw app. 
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Instructies voor niet-PHP_INI_SYSTEM aanpassen

::: zone pivot="platform-windows"  

Als u PHP_INI_USER-, PHP_INI_PERDIR- en PHP_INI_ALL-instructies wilt aanpassen (zie [php.ini-instructies),](https://www.php.net/manual/ini.list.php)voegt u een bestand toe aan de hoofdmap `.user.ini` van uw app.

Voeg configuratie-instellingen toe aan `.user.ini` het bestand met dezelfde syntaxis die u in een bestand zou `php.ini` gebruiken. Als u bijvoorbeeld de instelling wilt in- en instellen op `display_errors` 10 miljoen, bevat uw bestand `upload_max_filesize` `.user.ini` de volgende tekst:

```
 ; Example Settings
 display_errors=On
 upload_max_filesize=10M

 ; Write errors to d:\home\LogFiles\php_errors.log
 ; log_errors=On
```

Ployer uw app opnieuw met de wijzigingen en start deze opnieuw op.

Als alternatief voor het gebruik van een bestand kunt u ini_set() in uw app gebruiken om deze `.user.ini` niet-PHP_INI_SYSTEM [](https://www.php.net/manual/function.ini-set.php) aanpassen.

::: zone-end

::: zone pivot="platform-linux"

Als u PHP_INI_USER-, PHP_INI_PERDIR- en PHP_INI_ALL-instructies wilt aanpassen (zie [php.ini-instructies](https://www.php.net/manual/ini.list.php)), voegt u een *HTACCESS-bestand* toe aan de hoofdmap van uw app.

Voeg in *het .htaccess-bestand* de -instructies toe met behulp van de `php_value <directive-name> <value>` syntaxis. Bijvoorbeeld:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Ployer uw app opnieuw met de wijzigingen en start deze opnieuw op. Als u deze implementeert met Kudu (bijvoorbeeld met behulp van [Git](deploy-local-git.md)), wordt deze na de implementatie automatisch opnieuw gestart.

Als alternatief voor het gebruik *van .htaccess* kunt u [ini_set()](https://www.php.net/manual/function.ini-set.php) in uw app gebruiken om deze niet-PHP_INI_SYSTEM aanpassen.

::: zone-end

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Instructies voor PHP_INI_SYSTEM aanpassen

::: zone pivot="platform-windows"  

Als u PHP_INI_SYSTEM wilt aanpassen [ (ziephp.ini-instructies](https://www.php.net/manual/ini.list.php)), kunt u de *HTACCESS-methode niet* gebruiken. App Service biedt een afzonderlijk mechanisme met behulp van de `PHP_INI_SCAN_DIR` app-instelling.

Voer eerst de volgende opdracht uit in [de](https://shell.azure.com) Cloud Shell om een app-instelling met de naam toe te `PHP_INI_SCAN_DIR` voegen:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="d:\home\site\ini"
```

Navigeer naar de Kudu-console ( `https://<app-name>.scm.azurewebsites.net/DebugConsole` ) en navigeer naar `d:\home\site` .

Maak een map in met de naam en maak vervolgens een `d:\home\site` `ini` *INI-bestand* in de `d:\home\site\ini` map (bijvoorbeeld *settings.ini)* met de instructies die u wilt aanpassen. Gebruik dezelfde syntaxis die u in eenphp.ini *gebruiken.* 

Voer bijvoorbeeld de volgende opdrachten uit om de waarde [van expose_php](https://php.net/manual/ini.core.php#ini.expose-php) te wijzigen:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Start de app opnieuw om de wijzigingen door te voeren.

::: zone-end

::: zone pivot="platform-linux"

Als u PHP_INI_SYSTEM wilt aanpassen [ (ziephp.ini-instructies](https://www.php.net/manual/ini.list.php)), kunt u de *HTACCESS-methode niet* gebruiken. App Service biedt een afzonderlijk mechanisme met behulp van de `PHP_INI_SCAN_DIR` app-instelling.

Voer eerst de volgende opdracht uit in de [Cloud Shell](https://shell.azure.com) om een app-instelling met de naam toe te `PHP_INI_SCAN_DIR` voegen:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` is de standaardmap *waarphp.ini* bestaat. `/home/site/ini`is de aangepaste map waarin u een aangepast *.ini-bestand toevoegt.* U scheidt de waarden met een `:` .

Navigeer naar de web-SSH-sessie met uw Linux-container ( `https://<app-name>.scm.azurewebsites.net/webssh/host` ).

Maak een map in met de naam en maak vervolgens een `/home/site` `ini` *INI-bestand* in de `/home/site/ini` map (bijvoorbeeld *settings.ini)* met de instructies die u wilt aanpassen. Gebruik dezelfde syntaxis die u in eenphp.ini *gebruiken.* 

> [!TIP]
> In de ingebouwde Linux-containers in App Service *wordt /home* gebruikt als persistente gedeelde opslag. 
>

Voer bijvoorbeeld de volgende opdrachten uit om de waarde [van expose_php](https://php.net/manual/ini.core.php#ini.expose-php) te wijzigen:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Start de app opnieuw om de wijzigingen door te voeren.

::: zone-end

## <a name="enable-php-extensions"></a>PHP-extensies inschakelen

::: zone pivot="platform-windows"  

De ingebouwde PHP-installaties bevatten de meest gebruikte extensies. U kunt aanvullende extensies op dezelfde manier inschakelen als u [de instructies php.ini aanpassen.](#customize-php_ini_system-directives)

> [!NOTE]
> De beste manier om de PHP-versie en de huidigephp.inizien, is door [phpinfo() aan te](https://php.net/manual/function.phpinfo.php) roepen in uw app. 
>

Als u aanvullende extensies wilt inschakelen, volgt u deze stappen:

Voeg een map toe aan de hoofdmap van uw app en plaats de extensiebestanden in de map `bin` `.dll` *(bijvoorbeeldmongodb.dll*). Zorg ervoor dat de extensies compatibel zijn met de PHP-versie in Azure en compatibel zijn met VC9 en niet thread-safe (nts).

Implementeer uw wijzigingen.

Volg de stappen in [De instructies PHP_INI_SYSTEM](#customize-php_ini_system-directives)aanpassen, voeg de extensies [](https://www.php.net/manual/ini.core.php#ini.extension) toe aan het aangepaste *.ini-bestand* met de extensie- of [zend_extension-instructies.](https://www.php.net/manual/ini.core.php#ini.zend-extension)

```
extension=d:\home\site\wwwroot\bin\mongodb.dll
zend_extension=d:\home\site\wwwroot\bin\xdebug.dll
```

Start de app opnieuw om de wijzigingen door te voeren.

::: zone-end

::: zone pivot="platform-linux"

De ingebouwde PHP-installaties bevatten de meest gebruikte extensies. U kunt aanvullende extensies op dezelfde manier inschakelen als u [de instructies php.ini aanpassen.](#customize-php_ini_system-directives)

> [!NOTE]
> De beste manier om de PHP-versie en de huidigephp.inizien, is [door phpinfo() aan te](https://php.net/manual/function.phpinfo.php) roepen in uw app. 
>

Als u aanvullende extensies wilt inschakelen, volgt u deze stappen:

Voeg een map toe aan de hoofdmap van uw app en plaats de extensiebestanden in de map `bin` `.so` *(bijvoorbeeld mongodb.so*). Zorg ervoor dat de extensies compatibel zijn met de PHP-versie in Azure en compatibel zijn met VC9 en niet-thread-safe (nts).

Implementeer uw wijzigingen.

Volg de stappen in [Customize PHP_INI_SYSTEM directives](#customize-php_ini_system-directives)(Instructies voor PHP_INI_SYSTEM aanpassen) [](https://www.php.net/manual/ini.core.php#ini.extension) en voeg de extensies toe aan het aangepaste *.ini-bestand* met de extensie- of [zend_extension-instructies.](https://www.php.net/manual/ini.core.php#ini.zend-extension)

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Start de app opnieuw om de wijzigingen door te voeren.

::: zone-end

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

::: zone pivot="platform-windows"  

Gebruik het hulpprogramma [standard error_log()](https://php.net/manual/function.error-log.php) om uw diagnostische logboeken weer te geven in Azure App Service.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

## <a name="troubleshooting"></a>Problemen oplossen

Wanneer een werkende PHP-app zich anders gedraagt in App Service of fouten heeft, probeert u het volgende:

- [Open de logboekstream](#access-diagnostic-logs).
- Test de app lokaal in de productiemodus. App Service app in de productiemodus wordt uitgevoerd, moet u ervoor zorgen dat uw project lokaal werkt zoals verwacht in de productiemodus. Bijvoorbeeld:
    - Afhankelijk van uw *composer.jsop*, kunnen verschillende pakketten worden geïnstalleerd voor de productiemodus ( `require` vs. `require-dev` ).
    - Bepaalde web-frameworks kunnen statische bestanden op een andere manier implementeren in de productiemodus.
    - Bepaalde web-frameworks kunnen aangepaste opstartscripts gebruiken bij het uitvoeren in de productiemodus.
- Voer uw app uit in App Service in de foutopsporingsmodus. In Laravel kunt u uw app [bijvoorbeeld configureren](https://meanjs.org/)om foutopsporingsberichten in productie uit te geven door [de `APP_DEBUG` app-instelling in te stellen op `true` ](configure-common.md#configure-app-settings).

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: PHP-app met MySQL](tutorial-php-mysql-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [Veelgestelde vragen over App Service Linux](faq-app-service-linux.md)

::: zone-end
