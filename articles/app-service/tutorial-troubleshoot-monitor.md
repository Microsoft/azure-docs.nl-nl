---
title: 'Zelfstudie: Problemen oplossen met Azure Monitor'
description: Meer informatie over hoe Azure Monitor en Log Analytics uw App Service-web-app helpen bewaken. Azure Monitor maximaliseert de beschikbaarheid door een uitgebreide oplossing te leveren voor het bewaken van uw omgevingen.
author: msangapu-msft
ms.author: msangapu
ms.topic: tutorial
ms.date: 06/20/2020
ms.openlocfilehash: af2711a3d219bb472334ad61bad0b87f6c691dab
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183197"
---
# <a name="tutorial-troubleshoot-an-app-service-app-with-azure-monitor"></a>Zelfstudie: Problemen met een App Service-app oplossen met behulp van Azure Monitor

> [!NOTE]
> Azure Monitor-integratie met App Service bevindt zich in de [preview-fase](https://aka.ms/appsvcblog-azmon).
>

In deze zelfstudie ziet u hoe u problemen met een [App Service](overview.md) oplost met behulp van [Azure Monitor](../azure-monitor/overview.md). De voorbeeld-app bevat code die is bedoeld om het geheugen uit te putten en HTTP 500-fouten te veroorzaken, zodat u het probleem kunt vaststellen en oplossen met behulp van Azure Monitor. Wanneer u klaar bent, hebt u een voorbeeld-app in App Service in Linux die is geïntegreerd met [Azure Monitor](../azure-monitor/overview.md).

Met [Azure Monitor](../azure-monitor/overview.md) worden de beschikbaarheid en prestaties van uw toepassing en services gemaximaliseerd door een uitgebreide oplossing te bieden waarmee u telemetriegegevens kunt verzamelen en analyseren, en op basis hiervan kunt handelen, zowel in de cloud als on-premises.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een web-app configureren met Azure Monitor
> * Console-logboeken verzenden naar Log Analytics
> * Logboekquery's gebruiken om web-app-fouten op te sporen en op te lossen

U kunt de stappen in deze zelfstudie volgen voor macOS, Linux en Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

- [Azure-abonnement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Azure-CLI](/cli/azure/install-azure-cli)
- [Git](https://git-scm.com/)

## <a name="create-azure-resources"></a>Azure-resources maken

Voer eerst lokaal verschillende opdrachten uit om een voorbeeld-app in te stellen voor gebruik met deze zelfstudie. Met de opdrachten gaat u een voorbeeld-app klonen, Azure-resources maken, een implementatiegebruiker maken, en de app implementeren in Azure. U wordt gevraagd naar het wachtwoord dat is opgegeven als onderdeel van het maken van de implementatiegebruiker. 

```bash
git clone https://github.com/Azure-Samples/App-Service-Troubleshoot-Azure-Monitor
az group create --name myResourceGroup --location "South Central US"
az webapp deployment user set --user-name <username> --password <password>
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
git remote add azure <url_from_previous_step>
git push azure main
```

## <a name="configure-azure-monitor-preview"></a>Azure Monitor configureren (preview-versie)

### <a name="create-a-log-analytics-workspace"></a>Een Log Analytics-werkruimte maken

Nu u de voorbeeld-app hebt geïmplementeerd in Azure App Service, configureert u de bewakingsmogelijkheden, om problemen met de app op te lossen wanneer deze zich voordoen. In Azure Monitor worden logboekgegevens opgeslagen in a Log Analytics-werkruimte. Een werkruimte is een container die gegevens en configuratie-informatie bevat.

In deze stap maakt u een Log Analytics-werkruimte om Azure Monitor te configureren met uw app.

```bash
az monitor log-analytics workspace create --resource-group myResourceGroup --workspace-name myMonitorWorkspace
```

> [!NOTE]
> [Voor Azure Monitor Log Analytics betaalt u voor gegevensopname en gegevensretentie.](https://azure.microsoft.com/pricing/details/monitor/)
>

### <a name="create-a-diagnostic-setting"></a>Een diagnostische instelling maken

Diagnostische instellingen kunnen worden gebruikt om metrische gegevens voor bepaalde Azure-Services te verzamelen in Azure Monitor-logboeken, voor analyse met andere bewakingsinformatie met behulp van logboekquery's. Voor deze zelfstudie schakelt u de webserverlogboeken en standaardlogboeken voor uitvoer/fouten in. Zie [Ondersteunde logboektypen](./troubleshoot-diagnostic-logs.md#supported-log-types) voor een volledige lijst met logboektypen en beschrijvingen.

U voert de volgende opdrachten uit om diagnostische instellingen te maken voor AppServiceConsoleLogs (standaardlogboeken voor uitvoer/fouten) en AppServiceHTTPLogs (webserverlogboeken). Vervang _\<app-name>_ en _\<workspace-name>_ door uw eigen waarden. 

> [!NOTE]
> De eerste twee opdrachten, `resourceID` en `workspaceID`, zijn variabelen die moeten worden gebruikt in de opdracht `az monitor diagnostic-settings create`. Zie [Diagnostische instellingen maken met behulp van Azure CLI](../azure-monitor/platform/diagnostic-settings.md#create-using-azure-cli) voor meer informatie over deze opdracht.
>

```bash
resourceID=$(az webapp show -g myResourceGroup -n <app-name> --query id --output tsv)

workspaceID=$(az monitor log-analytics workspace show -g myResourceGroup --workspace-name <workspace-name> --query id --output tsv)

az monitor diagnostic-settings create --resource $resourceID \
 --workspace $workspaceID \
 -n myMonitorLogs \
 --logs '[{"category": "AppServiceConsoleLogs", "enabled": true},
  {"category": "AppServiceHTTPLogs", "enabled": true}]'
```

## <a name="troubleshoot-the-app"></a>Problemen met de app oplossen

Blader naar `http://<app-name>.azurewebsites.net`.

Met de voorbeeld-app, ImageConverter, worden opgenomen afbeeldingen geconverteerd van `JPG` naar `PNG`. Voor deze zelfstudie is opzettelijk een bug in de code geplaatst. Als u voldoende afbeeldingen selecteert, produceert de app een HTTP 500-fout tijdens de conversie van afbeeldingen. Stel dat dit scenario niet is overwogen tijdens de ontwikkelingsfase. U gebruikt Azure Monitor om de fout op te lossen.

### <a name="verify-the-app-is-works"></a>Controleer of de app werkt

Als u afbeeldingen wilt converteren, klikt u op `Tools` en selecteert u `Convert to PNG`.

![Klik op Hulpprogramma’s en selecteer Converteren naar PNG](./media/tutorial-azure-monitor/sample-monitor-app-tools-menu.png)

Selecteer de eerste twee afbeeldingen en klik op `convert`. Hiermee wordt de conversie voltooid.

![Selecteer de eerste twee afbeeldingen](./media/tutorial-azure-monitor/sample-monitor-app-convert-two-images.png)

### <a name="break-the-app"></a>Onderbreek de app

Nu u de app hebt gecontroleerd door twee afbeeldingen te converteren, gaan we proberen de eerste vijf afbeeldingen te converteren.

![Converteer de eerste vijf afbeeldingen](./media/tutorial-azure-monitor/sample-monitor-app-convert-five-images.png)

Deze actie mislukt en resulteert in een `HTTP 500`-fout die niet is getest tijdens de ontwikkeling.

![De conversie resulteert in een HTTP 500-fout](./media/tutorial-azure-monitor/sample-monitor-app-http-500.png)

## <a name="use-log-query-to-view-azure-monitor-logs"></a>Logboekquery gebruiken om Azure Monitor-logboeken weer te geven

Laten we kijken welke logboeken beschikbaar zijn in de Log Analytics-werkruimte. 

Klik op deze [koppeling voor Log Analytics-werkruimte](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) om toegang te krijgen tot uw werkruimte in Azure Portal.

Selecteer in Azure Portal uw Log Analytics-werkruimte.

### <a name="log-queries"></a>Logboekquery's

Met logboekquery's kunt u de waarde van de gegevens die in Azure Monitor-logboeken worden verzameld, volledig benutten. U gebruikt logboekquery's om de logboeken te identificeren in zowel AppServiceHTTPLogs als AppServiceConsoleLogs. Bekijk het [overzicht voor logboekquery’s](../azure-monitor/log-query/log-query-overview.md) voor meer informatie over logboekquery's.

### <a name="view-appservicehttplogs-with-log-query"></a>AppServiceHTTPLogs weergeven met logboekquery

Nu we toegang hebben tot de app, gaan we de gegevens bekijken die zijn gekoppeld aan HTTP-aanvragen in de `AppServiceHTTPLogs`.

1. Klik op `Logs` in de navigatiebalk aan de linkerkant.

![Logboeken voor Log Anlytics-werkruimte](./media/tutorial-azure-monitor/log-analytics-workspace-logs.png)

2. Ga naar `appservice` en dubbelklik op `AppServiceHTTPLogs`.

![Tabellen voor Log Analytics-werkruimte](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-tables.png)

3. Klik op `Run`.

![AppServiceHTTPLogs voor Log Analytics-werkruimte](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-http-logs.png)

Met de query `AppServiceHTTPLogs` worden alle aanvragen van de afgelopen 24 uur geretourneerd. De kolom `ScStatus` bevat de HTTP-status. Als u de `HTTP 500`-fouten wilt vaststellen, beperkt u de `ScStatus` tot 500 en voert u de query uit, zoals hieronder wordt weergegeven:

```kusto
AppServiceHTTPLogs
| where ScStatus == 500
```

### <a name="view-appserviceconsolelogs-with-log-query"></a>AppServiceConsoleLogs weergeven met logboekquery

Nu u de HTTP 500-fouten hebt bevestigd, gaan we kijken naar de standaardlogboeken voor uitvoer/fouten van de app. Deze logboeken vindt u in AppServiceConsoleLogs.

(1) Klik op `+` om een nieuwe query te maken. 

(2) Dubbelklik op de tabel `AppServiceConsoleLogs` en klik op `Run`. 

Omdat het converteren van vijf afbeeldingen resulteert in serverfouten, kunt u kijken of de app ook fouten schrijft door `ResultDescription` te filteren op fouten, zoals hieronder wordt weergegeven:

```kusto
AppServiceConsoleLogs |
where ResultDescription  contains "error" 
```

In de kolom `ResultDescription` ziet u de volgende fout:

```output
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

### <a name="join-appservicehttplogs-and-appserviceconsolelogs"></a>AppServiceHTTPLogs en AppServiceConsoleLogs koppelen

Nu u zowel HTTP 500-fouten als standaardfouten hebt geïdentificeerd, moet u controleren of er een correlatie bestaat tussen deze berichten. Vervolgens voegt u de tabellen samen op basis van het tijdstempel, `TimeGenerated`.

> [!NOTE]
> Er is een query voorbereid die het volgende doet:
>
> - HTTPLogs filteren op 500-fouten
> - Query’s uitvoeren op console-logboeken
> - De tabellen samenvoegen in `TimeGenerated`
>

Voer de volgende query uit:

```kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;
```

In de kolom `ResultDescription` ziet u de volgende fout op hetzelfde moment als webserverfouten:

```output
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

In het bericht staat dat het geheugen is uitgeput op regel 20 van `process.php`. U hebt nu bevestigd dat de toepassing een fout heeft veroorzaakt tijdens de HTTP 500-fout. We gaan nu kijken naar de code om het probleem te identificeren.

## <a name="identify-the-error"></a>De fout identificeren

Open `process.php` in de lokale map en kijk naar regel 20. 

```php
imagepng($imgArray[$x], $filename);
```

Het eerste argument, `$imgArray[$x]`, is een variabele met alle JPG’s (in het geheugen) die moeten worden geconverteerd. Voor `imagepng` hoeft echter alleen deze afbeelding te worden geconverteerd, en niet alle afbeeldingen. Het is niet nodig om afbeeldingen vooraf te laden en dit is mogelijk de oorzaak van de geheugenuitputting, wat leidt tot HTTP 500-fouten. We gaan de code bijwerken om afbeeldingen op aanvraag te laden om te zien of het probleem hierdoor wordt opgelost. Vervolgens gaat u de code verbeteren om het geheugenprobleem op te lossen.

## <a name="fix-the-app"></a>De app herstellen

### <a name="update-locally-and-redeploy-the-code"></a>De code lokaal bijwerken en opnieuw implementeren

U brengt de volgende wijzigingen aan in `process.php` om de geheugenuitputting af te handelen:

```php
<?php

//Retrieve query parameters
$maxImages = $_GET['images'];
$imgNames  = explode(",",$_GET['imgNames']);

//Load JPEGs into an array (in memory)
for ($x=0; $x<$maxImages; $x++){
    $filename = './images/converted_' . substr($imgNames[$x],0,-4) . '.png';
    imagepng(imagecreatefromjpeg("./images/" . $imgNames[$x]), $filename);
}
```

Leg uw wijzigingen vast in Git en push de codewijzigingen vervolgens naar Azure.

```bash
git commit -am "Load images on-demand in process.php"
git push azure main
```

### <a name="browse-to-the-azure-app"></a>Naar de Azure-app bladeren

Blader naar `http://<app-name>.azurewebsites.net`. 

Als het goed is, levert het converteren van afbeeldingen nu geen HTTP 500-fouten meer op.

![PHP-app uitgevoerd in Azure App Service](./media/tutorial-azure-monitor/sample-monitor-app-working.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

Verwijder de diagnostische instelling met de volgende opdracht:

```bash
az monitor diagnostic-settings delete --resource $resourceID -n myMonitorLogs
```
Wat u hebt geleerd:

> [!div class="checklist"]
> * Een web-app configureren met Azure Monitor
> * Logboeken verzenden naar Log Analytics
> * Logboekquery's gebruiken om web-app-fouten op te sporen en op te lossen

## <a name="next-steps"></a><a name="nextsteps"></a> Volgende stappen
* [Logboeken doorzoeken met Azure Monitor](../azure-monitor/log-query/log-query-overview.md)
* [Problemen met Azure App Service oplossen in Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [App-logboeken in HDInsight analyseren](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)