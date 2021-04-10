---
title: 'Quickstart: Een Python-app maken'
description: Ga aan de slag met Azure App Service door uw eerste Python-app te implementeren in een Linux-container in App Service.
ms.topic: quickstart
ms.date: 11/10/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python, devx-track-azurecli
zone_pivot_groups: python-frameworks-01
adobe-target: true
adobe-target-activity: DocsExp–393165–A/B–Docs/PythonQuickstart–CLIvsPortal–FY21Q4
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-python-portal
ms.openlocfilehash: e0542d47ff2c687f5f9470edb0cbb34ebc79554c
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107010861"
---
# <a name="quickstart-create-a-python-app-using-azure-app-service-on-linux"></a>Quickstart: Een Python-app maken met behulp van Azure App Service in Linux

In deze quickstart implementeert u een Python-web-app op [App Service op Linux](overview.md#app-service-on-linux), een uiterst schaalbare webhostingservice van Azure. Gebruik de lokale [Azure-opdrachtregelinterface (CLI)](/cli/azure/install-azure-cli) op een Mac-, Linux- of Windows-computer om een voorbeeld te implementeren met behulp van deFlask- of Django-frameworks. De web-app die u configureert, maakt gebruik van een basis App Service-laag die een kleine kosten in uw Azure-abonnement omkeert.

## <a name="set-up-your-initial-environment"></a>Uw eerste omgeving instellen

1. U moet beschikken over een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
1. Installeer <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 of hoger</a>.
1. Installeer de <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 of hoger, waarmee u opdrachten kunt uitvoeren in elke willekeurige shell voor het inrichten en configureren van Azure-resources.

Open een terminalvenster en controleer of uw Python-versie 3.6 of hoger is:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Controleer of uw Azure CLI-versie 2.0.80 of hoger is:

```azurecli
az --version
```

Meld u vervolgens aan bij Azure via de CLI:

```azurecli
az login
```

Met deze opdracht wordt een browser geopend om uw referenties te verzamelen. Wanneer de opdracht is voltooid, wordt JSON-uitvoer weergegeven met informatie over uw abonnementen.

Zodra u bent aangemeld, kunt u Azure-opdrachten uitvoeren met de Azure CLI om te werken met resources in uw abonnement.

Ondervindt u problemen? [Laat het ons weten](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="clone-the-sample"></a>Het voorbeeld klonen

Kloon de opslagplaats met voorbeelden met de volgende opdracht en ga dan naar de map met voorbeelden. ([Installeer git](https://git-scm.com/downloads) als u nog geen git hebt.)

::: zone pivot="python-framework-flask"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```
::: zone-end

::: zone pivot="python-framework-django"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-django
```
::: zone-end

Het voorbeeld bevat framework-specifieke code die door Azure App Service wordt herkend bij het starten van de app. Zie [Opstartprocessen van container](configure-language-python.md#container-startup-process) voor meer informatie.

Ondervindt u problemen? [Laat het ons weten](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

::: zone pivot="python-framework-flask"
1. Open de map *python-docs-hello-world*:

    ```terminal
    cd python-docs-hello-world
    ```

1. Maak een virtuele omgeving en installeer afhankelijkheden:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    Als u het foutbericht '[Errno 2] Bestand of map bestaat niet: 'requirements.txt'' krijgt, controleert u of u zich in de map *python-docs-hello-world* bevindt.

1. Voer de ontwikkelaarsserver uit.

    ```terminal  
    flask run
    ```
    
    De server gaat er standaard vanuit dat de invoermodule van de app zich in *app.py* bevindt, zoals in het voorbeeld wordt gebruikt.

    Als u een andere modulenaam gebruikt, stelt u omgevingsvariabele `FLASK_APP` in op die naam.

    Als de fout 'Kan geen Flask-toepassing vinden. U hebt niet de omgevingsvariabele 'FLASK_APP' opgegeven en er is geen 'wsgi.py'- of 'app.py'-module gevonden in de huidige map.' optreedt, controleert u of u zich in de map `python-docs-hello-world` bevindt waarin het voorbeeld is opgenomen.

1. Open een webbrowser en ga naar de voorbeeld-app op `http://localhost:5000/`. In de app wordt het bericht **Hallo wereld** weergegeven.

    ![Een Python-voorbeeld-app uitvoeren](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. Druk in het terminalvenster op **Ctrl**+**C** om de ontwikkelingsserver af te sluiten.
::: zone-end

::: zone pivot="python-framework-django"
1. Open de map *python-docs-hello-django*:

    ```terminal
    cd python-docs-hello-django
    ```

1. Maak een virtuele omgeving en installeer afhankelijkheden:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    Als u het foutbericht '[Errno 2] Bestand of map bestaat niet: 'requirements.txt'' krijgt, controleert u of u zich in de map *python-docs-hello-django* bevindt.
    
1. Voer de ontwikkelaarsserver uit.

    ```terminal
    python manage.py runserver
    ```

1. Open een webbrowser en ga naar de voorbeeld-app op `http://localhost:8000/`. In de app wordt het bericht **Hallo wereld** weergegeven.

    ![Een Python-voorbeeld-app uitvoeren](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. Druk in het terminalvenster op **Ctrl**+**C** om de ontwikkelingsserver af te sluiten.
::: zone-end

Ondervindt u problemen? [Laat het ons weten](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="deploy-the-sample"></a>Het voorbeeld implementeren

Implementeer de code in uw lokale map (*python-docs-hello-world*) met behulp van de `az webapp up`-opdracht:

```azurecli
az webapp up --sku B1 --name <app-name>
```

- Als de `az`-opdracht niet wordt herkend, controleert u of de Azure CLI is geïnstalleerd volgens de beschrijving in [Uw initiële omgeving instellen](#set-up-your-initial-environment).
- Als de `webapp`-opdracht niet wordt herkend, komt dat omdat uw Azure CLI-versie 2.0.80 of hoger is. Als dat niet zo is, [installeert u de nieuwste versie](/cli/azure/install-azure-cli).
- Vervang `<app_name>` door een naam die in de volledige Azure-omgeving uniek is (*geldige tekens zijn `a-z`, `0-9` en `-`* ). Het is handig om een combinatie van uw bedrijfsnaam en een app-id te gebruiken.
- Met het argument `--sku B1` maakt u de web-app voor de prijscategorie Basic, waarvoor een kleine vergoeding per uur wordt gevraagd. Laat dit argument weg om een snellere Premium-laag te gebruiken.
- U kunt eventueel het argument `--location <location-name>` toevoegen, waarbij `<location_name>` een beschikbare Azure-regio is. U kunt een lijst met toegestane regio's voor uw Azure-account ophalen door de [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations)-opdracht uit te voeren.
- Als de fout 'Kan de runtimestack van uw app niet automatisch detecteren' wordt weergegeven, moet u controleren of u de opdracht uitvoert in de map *python-docs-hello-world* (Flask) of de map *python-docs-hello-django* (Django), die het bestand *requirements.txt* bevat. (Zie [Problemen met automatische detectie oplossen met az webapp up](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md) (GitHub) voor meer informatie.)

Het volledig uitvoeren van de opdracht kan even duren. Terwijl de opdracht wordt uitgevoerd, krijgt u berichten over het maken van de resourcegroep, het App Service-plan en de hosting-app, het configureren van de logboekfunctie en het vervolgens uitvoeren van de ZIP-implementatie. Vervolgens krijgt u het volgende bericht: 'U kunt de app starten op http://&lt;app-name&gt;.azurewebsites.net'. Dit is de URL van de app op Azure.

![Voorbeelduitvoer van de opdracht az webapp up](./media/quickstart-python/az-webapp-up-output.png)

Ondervindt u problemen? Raadpleeg eerst de [Handleiding voor het oplossen van problemen](configure-language-python.md#troubleshooting). Als u er niet uitkomt, [laat het ons weten](https://aka.ms/FlaskCLIQuickstartHelp).

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Bladeren naar de app

Blader naar de geïmplementeerde toepassing in uw webbrowser op de URL `http://<app-name>.azurewebsites.net`. Het kan een paar minuten duren voordat de app wordt gestart. Wacht dus een minuut en vernieuw de browser als u de pagina van een standaard-app ziet.

Met de Python-voorbeeldcode wordt een Linux-container uitgevoerd in App Service via een ingebouwde installatiekopie.

![Een Python-voorbeeld-app uitvoeren in Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Gefeliciteerd!** U hebt uw Python-app geïmplementeerd in App Service.

Ondervindt u problemen? Raadpleeg eerst de [Handleiding voor het oplossen van problemen](configure-language-python.md#troubleshooting). Als u er niet uitkomt, [laat het ons weten](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="redeploy-updates"></a>Updates opnieuw implementeren

In deze sectie brengt u een kleine codewijziging aan en implementeert u de code opnieuw in Azure. Met deze codewijziging wordt een `print`-instructie opgenomen waardoor logboekuitvoer wordt gegenereerd waarmee u in de volgende sectie gaat werken.

::: zone pivot="python-framework-flask"
Open *app.py* in een editor en werk de functie `hello` bij zodat deze overeenkomt met de volgende code. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello, Azure!"
```
::: zone-end
::: zone pivot="python-framework-django"
Open *hello/views.py* in een editor en werk de functie `hello` bij zodat deze overeenkomt met de volgende code.

```python
def hello(request):
    print("Handling request to home page.")
    return HttpResponse("Hello, Azure!")
```
::: zone-end
    
Sla de wijzigingen op en implementeer de app opnieuw met de opdracht `az webapp up`:

```azurecli
az webapp up
```

Met deze opdracht worden waarden gebruikt die lokaal in de cache worden opgeslagen in het bestand *.azure/config*, met inbegrip van de app-naam, de resourcegroep en het App Service-plan.

Zodra de implementatie is voltooid, schakelt u terug naar het browservenster om `http://<app-name>.azurewebsites.net` te openen. Vernieuw de pagina. Als het goed is, wordt hier het volgende aangepast bericht weergegeven:

![Een bijgewerkte Python-voorbeeld-app uitvoeren in Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

Ondervindt u problemen? Raadpleeg eerst de [Handleiding voor het oplossen van problemen](configure-language-python.md#troubleshooting). Als u er niet uitkomt, [laat het ons weten](https://aka.ms/FlaskCLIQuickstartHelp).

> [!TIP]
> Visual Studio Code biedt krachtige extensies voor Python en Azure App Service, waarmee het implementatieproces van Python-web-apps in App Service wordt vereenvoudigd. Zie [Python-apps implementeren in App Service vanuit Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01) voor meer informatie.

## <a name="stream-logs"></a>Logboeken streamen

U hebt toegang tot de consolelogboeken die zijn gegenereerd vanuit de app en de container waarin deze wordt uitgevoerd. Logboeken bevatten uitvoer die wordt gegenereerd met behulp van `print`-instructies.

Als u logboeken wilt streamen, voert u de opdracht [az webapp log tail](/cli/azure/webapp/log#az_webapp_log_tail) uit:

```azurecli
az webapp log tail
```

U kunt ook parameter `--logs` opnemen door middel van opdracht `az webapp up`, zodat tijdens de implementatie de logboekstroom automatisch wordt geopend.

Vernieuw de app in de browser om consolelogboeken te genereren. Deze omvatten berichten waarin HTTP-aanvragen voor de app worden beschreven. Als er niet direct uitvoer wordt weergegeven, probeert u het over 30 seconden nog eens.

U kunt ook de logboekbestanden van de browser inspecteren op `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Als u het streamen van logboeken op een bepaald moment wilt stoppen, drukt u in de terminal op **Ctrl**+**C**.

Ondervindt u problemen? Raadpleeg eerst de [Handleiding voor het oplossen van problemen](configure-language-python.md#troubleshooting). Als u er niet uitkomt, [laat het ons weten](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="manage-the-azure-app"></a>De Azure-app beheren

Ga naar <a href="https://portal.azure.com" target="_blank">Azure Portal</a> om de app te beheren die u hebt gemaakt. Zoek en selecteer **App Services**.

![Naar App Services navigeren in Azure Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Selecteer de naam van uw Azure-app.

![Naar uw Python-app in App Services navigeren in Azure Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Wanneer u de app selecteert, wordt de pagina **Overzicht** van deze app weergegeven. Hier kunt u algemene beheertaken uitvoeren, zoals bladeren, stoppen, starten, opnieuw starten en verwijderen.

![Uw Python-app beheren op de pagina Overzicht in Azure Portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

Het App Service-menu biedt verschillende pagina's voor het configureren van uw app.

Ondervindt u problemen? Raadpleeg eerst de [Handleiding voor het oplossen van problemen](configure-language-python.md#troubleshooting). Als u er niet uitkomt, [laat het ons weten](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. De naam van de resourcegroep lijkt op 'appsvc_rg_Linux_CentralUS', afhankelijk van uw locatie. Als u de web-app blijvend uitvoert, worden er enige kosten in rekening gebracht (zie [Prijzen voor App Service](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Als u denkt dat u deze resources niet meer gaat gebruiken, verwijdert u de resourcegroep door de volgende opdracht uit te voeren:

```azurecli
az group delete --no-wait
```

Voor de opdracht wordt de resourcegroepnaam gebruikt die in het bestand *.azure/config* in de cache is opgeslagen.

Met argument `--no-wait` kan de opdracht worden geretourneerd voordat de bewerking wordt voltooid.

Ondervindt u problemen? [Laat het ons weten](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Python-web-app (Django) met PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Python-app configureren](configure-language-python.md)

> [!div class="nextstepaction"]
> [Gebruikersaanmelding toevoegen aan een Python-web-app](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Zelfstudie: Een Python-app in een aangepaste container uitvoeren](tutorial-custom-container.md)
