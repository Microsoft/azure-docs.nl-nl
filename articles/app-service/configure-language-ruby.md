---
title: Ruby-apps configureren - Azure App Service
description: Lees hoe u een vooraf gebouwde Ruby-container voor uw app configureert. In dit artikel worden de meest algemene configuratietaken beschreven.
ms.topic: quickstart
ms.date: 06/18/2020
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 038d62573b491325adc60647debf17fa87e06cfe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92743683"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Een Linux Ruby-app voor Azure App Service configureren

In dit artikel wordt beschreven hoe Ruby-apps in een Linux-container worden uitgevoerd in [Azure App Service](overview.md) en hoe u het gedrag van Azure App Service zo nodig kunt aanpassen. Ruby-apps moet worden geïmplementeerd met alle vereiste [gems](https://rubygems.org/gems).

In deze handleiding vindt u belangrijke concepten en instructies voor Ruby-ontwikkelaars die een ingebouwde Linux-container in App Service gebruiken. Als u Azure App Service nog nooit hebt gebruikt, moet u eerst de [Ruby-quickstart](quickstart-ruby.md) en de [zelfstudie Ruby met PostgreSQL](tutorial-ruby-postgres-app.md) volgen.

## <a name="show-ruby-version"></a>Ruby-versie weergeven

Als u de huidige versie van Ruby wilt weergeven, voert u de volgende opdracht uit in de [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Als u alle ondersteunde versies van Ruby wilt weergeven, voert u de volgende opdracht uit in de [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

U kunt een niet-ondersteunde versie van Ruby uitvoeren door in plaats daarvan uw eigen containerinstallatiekopie te bouwen. Voor meer informatie raadpleegt u [Een aangepaste Docker-installatiekopie gebruiken](tutorial-custom-container.md?pivots=container-linux).

## <a name="set-ruby-version"></a>Ruby-versie instellen

Voer de volgende opdracht uit in de [Cloud Shell](https://shell.azure.com) om de versie van Ruby in te stellen op 2.3:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Worden er tijdens de implementatie fouten weergegeven die lijken op de volgende?
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> of
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Dit betekent dat de geconfigureerde Ruby-versie in uw project afwijkt van de geïnstalleerde versie in de actieve container (`2.3.3` in het bovenstaande voorbeeld). Schakel in het bovenstaande voorbeeld zowel *Gemfile* als *.ruby-version* in en controleer of de Ruby-versie niet is ingesteld of is ingesteld op de versie die is geïnstalleerd in de actieve container (`2.3.3` in het bovenstaande voorbeeld).

## <a name="access-environment-variables"></a>Toegang tot omgevingsvariabelen

In App Service kunt u [app-instellingen configureren](configure-common.md#configure-app-settings) buiten uw app-code. Vervolgens kunt u ze openen met behulp van het standaardpatroon [ENV['\<path-name>']](https://ruby-doc.org/core-2.3.3/ENV.html). Voor toegang tot bijvoorbeeld de app-instelling `WEBSITE_SITE_NAME` gebruikt u de volgende code:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Implementatie aanpassen

Wanneer u een [Git-opslagplaats](deploy-local-git.md)implementeert, of een [zip-pakket](deploy-zip.md) waarbij buildprocessen zijn ingeschakeld, voert de implementatie-engine (Kudu) automatisch de volgende stappen na de implementatie uit:

1. Controleer of er een *Gemfile* bestaat.
1. Voer `bundle clean` uit. 
1. Voer `bundle install --path "vendor/bundle"` uit.
1. Voer `bundle package` uit om gems te verpakken in de map leverancier/cache.

### <a name="use---without-flag"></a>De vlag --without gebruiken

Als u `bundle install` wilt uitvoeren met de vlag [--without](https://bundler.io/man/bundle-install.1.html), stelt u de [app-instelling](configure-common.md#configure-app-settings) `BUNDLE_WITHOUT` in op een lijst met door komma's gescheiden groepen. Met de volgende opdracht wordt deze bijvoorbeeld ingesteld op `development,test`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Als deze instelling is gedefinieerd, wordt in de implementatie-engine `bundle install` uitgevoerd met `--without $BUNDLE_WITHOUT`.

### <a name="precompile-assets"></a>Assets vooraf compileren

Tijdens de stappen na de implementatie worden assets niet standaard vooraf gecompileerd. Als u de functie voor de compilatie van assets vooraf wilt inschakelen, stelt u de [app-instelling](configure-common.md#configure-app-settings) `ASSETS_PRECOMPILE` in op `true`. Vervolgens wordt de opdracht `bundle exec rake --trace assets:precompile` uitgevoerd aan het einde van de stappen na de implementatie. Bijvoorbeeld:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Zie [Statische assets](#serve-static-assets) leveren voor meer informatie.

## <a name="customize-start-up"></a>Opstarten aanpassen

Standaard start de Ruby-container de Rails-server in de volgende volgorde (Zie voor meer informatie het [opstartscript](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. Genereer de waarde [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security), als er nog geen bestaat. Deze waarde is vereist voor het uitvoeren van de app in productiemodus.
1. Stel de omgevingsvariabele `RAILS_ENV` in op `production`.
1. Verwijder het *.PID*-bestand in de map *tmp/pids* dat daar is geplaatst door een eerder uitgevoerde Rails-server.
1. Controleer of alle afhankelijkheden zijn geïnstalleerd. Als dat niet het geval is, installeert u gems vanuit de lokale map *vendor/cache*.
1. Voer `rails server -e $RAILS_ENV` uit.

U kunt het opstartproces op de volgende manieren aanpassen:

- [Statische assets leveren](#serve-static-assets)
- [Uitvoeren in niet-productiemodus](#run-in-non-production-mode)
- [Secret_key_base handmatig instellen](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Statische assets leveren

De Rails-server in de Ruby-container wordt standaard uitgevoerd in productiemodus en [er wordt van uitgegaan dat assets vooraf zijn gecompileerd en worden geleverd door de webserver](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Als u statische assets van de Rails-server wilt leveren, moet u twee acties uitvoeren:

- **Compileer de assets vooraf** - [Compileer de statische assets eerst lokaal](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) en implementeer ze handmatig. U kunt dit ook laten uitvoeren door de implementatie-engine (zie [Assets vooraf compileren](#precompile-assets)).
- **Schakel het leveren van statische bestanden in**: voor het leveren van statische assets vanuit de Ruby-container [stelt u de app-instelling `RAILS_SERVE_STATIC_FILES`](configure-common.md#configure-app-settings) in op `true`. Bijvoorbeeld:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Uitvoeren in niet-productiemodus

De Rails-server wordt standaard uitgevoerd in de productiemodus. Als u de ontwikkelmodus wilt gebruiken, stelt u bijvoorbeeld de [app-instelling](configure-common.md#configure-app-settings) `RAILS_ENV` in op `development`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Met deze instelling wordt de Rails-server alleen gestart in de ontwikkelmodus, waarin alleen localhost-aanvragen worden geaccepteerd en die niet toegankelijk is buiten de container. Als u externe clientaanvragen wilt accepteren, stelt u de [app-instelling](configure-common.md#configure-app-settings) `APP_COMMAND_LINE` in op `rails server -b 0.0.0.0`. Met deze app-instelling kunt u een aangepaste opdracht uitvoeren in de Ruby-container. Bijvoorbeeld:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a><a name="set-secret_key_base-manually"></a> Secret_key_base handmatig instellen

Als u uw eigen `secret_key_base`-waarde wilt gebruiken in plaats van dat deze door de App Service wordt gegenereerd, stelt u de [app-instelling](configure-common.md#configure-app-settings) `SECRET_KEY_BASE` in op de gewenste waarde. Bijvoorbeeld:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH-sessie in de browser openen

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Rails-app met PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Veelgestelde vragen over App Service Linux](faq-app-service-linux.md)
