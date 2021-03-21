---
title: 'Zelfstudie: Django-app implementeren met App Service en Azure Database for PostgreSQL - Flexible Server (Preview) in een virtueel netwerk'
description: Django-app implementeren met App Service en Azure Database for PostgreSQL - Flexible Server (Preview) in een virtueel netwerk
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: bcc9ca0175e0e03c62c2ce2b91d8ec337756a3cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92490098"
---
# <a name="tutorial-deploy-django-app-with-app-service-and-azure-database-for-postgresql---flexible-server-preview"></a>Zelfstudie: Django-app implementeren met App Service en Azure Database for PostgreSQL - Flexible Server (Preview)

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

In deze zelfstudie leert u hoe u een Django-toepassing kunt implementeren in Azure met behulp van App Services en Azure Database for PostgreSQL - Flexible Server in een virtueel netwerk.

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

In dit artikel moet u Azure CLI-versie 2.0 of later lokaal uitvoeren. Voer de opdracht `az --version` uit om de geïnstalleerde versie te zien. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

U moet zich aanmelden bij uw account met behulp van de opdracht [az login](/cli/azure/authenticate-azure-cli). Let op de **id**-eigenschap van de opdrachtuitvoer voor de naam van het desbetreffende abonnement.

```azurecli
az login
```

Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer de specifieke abonnements-id in uw account met de opdracht [az account set](/cli/azure/account). Gebruik de eigenschap **abonnement-id** uit de **az login**-uitvoer voor uw abonnement in de tijdelijke aanduiding voor de abonnement-id.

```azurecli
az account set --subscription <subscription id>
```
## <a name="clone-or-download-the-sample-app"></a>De voorbeeld-app klonen of downloaden

# <a name="git-clone"></a>[Git-kloon](#tab/clone)

Kloon de voorbeeldopslagplaats:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Ga vervolgens naar die map:

```terminal
cd djangoapp
```

# <a name="download"></a>[Downloaden](#tab/download)

Ga naar [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp), selecteer **Kloon** en vervolgens **ZIP-bestand downloaden**.

Pak het ZIP-bestand uit in een map met de naam *djangoapp*.

Open vervolgens een terminalvenster in die map *djangoapp*.

---

Het voorbeeld van de djangoapp bevat de gegevensgestuurde Django-polls-app die u kunt verkrijgen door [Uw eerste Django-app schrijven](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) in de Django-documentatie te volgen. De voltooide app wordt hier voor het gemak aangeboden.

Het voorbeeld is ook aangepast om te worden uitgevoerd in een productie-omgeving, zoals App Service:

- Productie-instellingen bevinden zich in het bestand *azuresite/production.py*. De ontwikkelingsgegevens bevinden zich in *azuresite/settings.py*.
- De app gebruikt productie-instellingen wanneer de `DJANGO_ENV`-omgevingsvariabele is ingesteld op 'productie'. Verderop in de zelfstudie maakt u deze omgevingsvariabele samen met andere die worden gebruikt voor de configuratie van de PostgreSQL-database.

Deze wijzigingen dienen specifiek om Django uit te voeren in een productieomgeving en zijn niet uniek voor App Service. Zie de sectie [Controlelijst voor Django-implementatie](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/) voor meer informatie.

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Een flexibele PostgreSQL-server maken in een virtueel netwerk

Maak een flexibele privéserver en een database in een virtueel netwerk (VNET) met behulp van de volgende opdracht:
```azurecli
# Create Flexible server in a VNET

az postgres flexible-server create --resource-group myresourcegroup --location westus2

```

Deze opdracht voert de volgende acties uit, dit kan enkele minuten duren:

- Maak de resourcegroep als deze nog niet bestaat.
- Hiermee wordt een servernaam gegenereerd als deze niet wordt opgegeven.
- Maak een nieuw virtueel netwerk voor uw nieuwe PostgreSQL-server. **Noteer de naam van het virtuele netwerk en het subnet** dat voor uw server is gemaakt. U moet de web-app namelijk aan hetzelfde virtuele netwerk toevoegen.
- Hiermee maakt u de gebruikersnaam van de beheerder en het wachtwoord voor uw server indien niet opgegeven. **Noteer de gebruikersnaam en het wachtwoord** om te gebruiken in de volgende stap.
- Maak een database ```postgres``` die kan worden gebruikt voor ontwikkeling. U kunt [**psql** uitvoeren om verbinding te maken met de database](quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql) om een andere database te maken.

> [!NOTE]
> Noteer het wachtwoord dat voor u wordt gegenereerd als het niet is opgegeven. Als u het wachtwoord vergeet, kunt u het wachtwoord opnieuw instellen met behulp van de opdracht ``` az postgres flexible-server update```


## <a name="deploy-the-code-to-azure-app-service"></a>De code implementeren naar Azure App Service

In deze sectie maakt u een app-host in de App Service-app, koppelt u deze app aan de Postgres-database en implementeert u vervolgens uw code naar die host.


### <a name="create-the-app-service-web-app-in-a-virtual-network"></a>De App Service-web-app maken in een virtueel netwerk

Controleer in de terminal of u zich in de hoofdmap van de opslagplaats (`djangoapp`) die de code van de app bevat bevindt.

Een App Service-app maken (het hostproces) met de opdracht [`az webapp up`](/cli/azure/webapp#az-webapp-up):

```azurecli

# Create a web app
az webapp up --resource-group myresourcegroup --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>

# Enable VNET integration for web app.
# Replace <vnet-name> and <subnet-name> with the virtual network and subnet name that the flexible server is using.

az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>

# Configure database information as environment variables
# Use the postgres server name , database name , username , password for the database created in the previous steps

az webapp config appsettings set --settings DJANGO_ENV="production" DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```
- Gebruik voor het argument `--location` dezelfde locatie als voor de database in het vorige onderdeel.
- Vervang *\<app-name>* door een naam die overal in Azure uniek is (het servereindpunt is `https://\<app-name>.azurewebsites.net`). De tekens die voor *\<app-name>* zijn toegestaan, zijn `A`-`Z`, `0`-`9` en `-`. Het is handig om een een combinatie van uw bedrijfsnaam en een app-id te gebruiken.
- Maak het [App Service-abonnement](../../app-service/overview-hosting-plans.md) *DjangoPostgres-tutorial-plan* in de Basic-prijscategorie (B1), als deze nog niet bestaat. `--plan` en `--sku` zijn optioneel.
- Maak de App Service-app als deze nog niet bestaat.
- Schakel standaardlogboeken voor de app in, als die nog niet zijn ingeschakeld.
- Upload de opslagplaats met behulp van ZIP-implementatie, met ingeschakelde bouwautomatisering.
- Met de opdracht **az webapp vnet-integration** wordt de web-app toegevoegd in hetzelfde netwerk als de postgres-server.
- De code van de app verwacht om database-informatie te vinden in een aantal omgevingsvariabelen. Om omgevingsvariabelen in te stellen in App Service, maakt u 'app-instellingen' met de opdracht [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set).

> [!TIP]
> Veel Azure CLI-opdrachten cachen dezelfde parameters, zoals de naam van de resourcegroep en het App Service-abonnement, in het bestand *.azure/config*. Bijgevolg hoeft u niet al diezelfde parameters op te geven met latere opdrachten. Om bijvoorbeeld de app opnieuw te implementeren na wijzigingen, kunt u `az webapp up` gewoon opnieuw uitvoeren zonder parameters.

### <a name="run-django-database-migrations"></a>Django-databasemigraties uitvoeren

Django-databasemigraties zorgen ervoor dat het schema in de PostgreSQL van de Azure-database overeenkomt met de schema's die in uw code beschreven worden.

1. Open een SSH-sessie in de browser door te navigeren naar *https://\<app-name>.scm.azurewebsites.net/webssh/host* en meld u aan met de referenties van uw Azure-account (niet de referenties van de databaseserver).

1. Voer in de SSH-sessie de volgende opdrachten uit (u kunt opdrachten plakken met **CTRL**+**Shift**+**V**):

    ```bash
    cd site/wwwroot

    # Activate default virtual environment in App Service container
    source /antenv/bin/activate
    # Install packages
    pip install -r requirements.txt
    # Run database migrations
    python manage.py migrate
    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

1. De `createsuperuser`-opdracht vraagt u om de referenties van de super gebruiker op te geven. Gebruik voor deze zelfstudie de standaard gebruikersnaam `root`, druk op **Enter** voor het e-mailadres om het leeg te laten en voer `postgres1` in bij het wachtwoord.

### <a name="create-a-poll-question-in-the-app"></a>Een poll-vraag maken in de app

1. Open in een browser de URL *http:\//\<app-name>.azurewebsites.net*. De app zou het bericht 'Er zijn geen polls beschikbaar' moeten weergeven, omdat er nog geen specifieke polls in de database zitten.

1. Blader naar *http:\//\<app-name>.azurewebsites.net/admin*. Meld u aan met de beheerdersreferenties uit het vorige onderdeel (`root` en `postgres1`). Selecteer onder **Polls** **Toevoegen** naast **Vragen** en maak een poll-vraag met enkele antwoordmogelijkheden.

1. Ga opnieuw naar *http:\//\<app-name>. azurewebsites.net/* om te controleren of de gebruiker nu de vragen te zien krijgt. Beantwoord vragen zoals u wilt om wat gegevens de genereren in de database.

**Gefeliciteerd!** U voert een Python (Django) web-app uit in Azure App Service voor Linux met een actieve Postgres-database.

> [!NOTE]
> In App Service wordt een Django-project gedetecteerd door in elke submap naar een *wsgi.py*-bestand te zoeken dat standaard wordt gemaakt met `manage.py startproject`. Wanneer App Service dat bestand heeft gevonden, wordt de Django web-app geladen. Zie [Ingebouwde Python-installatiekopie configureren](../../app-service/configure-language-python.md) voor meer informatie.

## <a name="make-code-changes-and-redeploy"></a>Wijzigingen aanbrengen in code en opnieuw implementeren

In dit onderdeel maakt u lokale wijzigingen in de app en implementeert u de code opnieuw naar App Service. Tijdens dat proces stelt u een virtuele Python-omgeving in die ondersteuning biedt bij doorlopend werk.

### <a name="run-the-app-locally"></a>De app lokaal uitvoeren

Voer in een terminalvenster de volgende opdrachten uit. Volg de aanwijzingen wanneer u de supergebruiker aanmaakt:

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```
Zodra de web-app volledig geladen is, voorziet de ontwikkelingsserver voor Django de URL van de lokale app in het bericht 'Ontwikkelingsserver starten op http://127.0.0.1:8000/. Verlaat de server met CTRL-BREAK'.

:::image type="content" source="./media/tutorial-django-app-service-postgres/django-dev-server-output.png" alt-text="Voorbeelduitvoer van Django-ontwikkelingsserver":::

Test de app lokaal met de volgende stappen:

1. Ga in een browser naar *http:\//localhost:8000*, waar het bericht 'Geen polls beschikbaar' zou moeten worden weergegeven.

1. Ga naar *http:\//localhost:8000/admin* en meld u aan als de gebruiker met beheerdersrechten die u voorheen hebt gemaakt. Selecteer onder **Polls** opnieuw **Toevoegen** naast **Vragen** en maak een poll-vraag met enkele antwoordmogelijkheden.

1. Ga weer naar *http:\//localhost:8000* en beantwoord de vraag om de app te testen.

1. Stop de Django-server door op **CTRL**+**C** te drukken.

Wanneer de app lokaal wordt uitgevoerd, wordt er een lokale Sqlite3-database gebruikt en wordt uw productiedatabase niet verstoord. U kunt, indien gewenst, ook een lokale PostgreSQL-database gebruiken om uw productieomgeving beter te simuleren.



### <a name="update-the-app"></a>De app bijwerken

Zoek in `polls/models.py` naar de regel die begint met `choice_text` en verander de parameter `max_length` naar 100:

```python
# Find this lie of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

Omdat u het gegevensmodel heeft gewijzigd, moet u een nieuwe Django-migratie maken en de database migreren:

```
python manage.py makemigrations
python manage.py migrate
```

Voer de ontwikkelingsserver opnieuw uit met `python manage.py runserver` en test de app op *http:\//localhost: 8000/admin*:

Stop de Django-webserver opnieuw met **CTRL**+**C**.


### <a name="redeploy-the-code-to-azure"></a>De code opnieuw implementeren naar Azure

Voer vanuit de hoofdmap van de opslagplaats de volgende opdrachten uit:

```azurecli
az webapp up
```

Deze opdracht maakt gebruik van de parameters in de cache van het bestand *.azure/config*. Omdat App Service detecteert dat de app al bestaat, wordt de code gewoon opnieuw geïmplementeerd.



### <a name="rerun-migrations-in-azure"></a>Migraties opnieuw uitvoeren in Azure

Omdat u wijzigingen hebt aangebracht in het gegevensmodel, moet u databasemigraties opnieuw uitvoeren in App Service.

Open opnieuw een SSH-sessie in de browser door naar *https://\<app-name>.scm.azurewebsites.net/webssh/host* te navigeren. Voer vervolgens de volgende opdrachten uit:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Apps in productie controleren

Ga naar *http:\//\<app-name>.azurewebsites.net* en test de app opnieuw in productie. (Aangezien u enkel de lengte van een databaseveld heeft gewijzigd, wordt de wijziging pas zichtbaar wanneer u een langer antwoord invoert tijdens het maken van een vraag.)

> [!TIP]
> U kunt [django-storages](https://django-storages.readthedocs.io/en/latest/backends/azure.html) gebruiken om statische en media-assets op te slaan in Azure-opslag. U kunt Azure CDN gebruiken voor gzipping van statische bestanden.


## <a name="manage-your-app-in-the-azure-portal"></a>De app beheren in Azure Portal

Zoek in het [Azure-portal](https://portal.azure.com) naar de appnaam en selecteer de app in de resultaten.

:::image type="content" source="./media/tutorial-django-app-service-postgres/navigate-to-django-app-in-app-services-in-the-azure-portal.png" alt-text="Naar uw Python Django-app navigeren in Azure Portal":::

Het portal laat standaard de pagina **Overzicht** van uw app zien; hier vindt u een algemeen overzicht van de prestaties. Hier kunt u ook algemene beheertaken uitvoeren zoals bladeren, stoppen, opnieuw starten en verwijderen. De tabbladen aan de linkerkant van de pagina tonen de verschillende configuratiepagina's die u kunt openen.

:::image type="content" source="./media/tutorial-django-app-service-postgres/manage-django-app-in-app-services-in-the-azure-portal.png" alt-text="Uw Python Django-app beheren op de overzichtspagina in Azure Portal":::


## <a name="clean-up-resources"></a>Resources opschonen

Als u de app wilt behouden of wilt doorgaan naar de volgende zelfstudie, sla dit dan over en ga naar [Volgende stappen](#next-steps). Zo niet, dan kunt u de resourcegroep die u gemaakt hebt voor deze zelfstudie verwijderen om doorlopende kosten te vermijden:

```azurecli
az group delete -g myresourcegroup
```

Voor de opdracht wordt de resourcegroepnaam gebruikt die in het bestand *.azure/config* in de cache is opgeslagen. Door de resourcegroep te verwijderen, kunt u ook de toewijzing van alle resources erin ongedaan maken en deze verwijderen.

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe u een aangepaste DNS-naam kunt toewijzen aan uw app:

> [!div class="nextstepaction"]
> [Zelfstudie: Een aangepaste DNS-naam aan uw app toewijzen](../../app-service/app-service-web-tutorial-custom-domain.md)

Ontdek hoe App Service een Python-app uitvoert:

> [!div class="nextstepaction"]
> [Python-app configureren](../../app-service/configure-language-python.md)
