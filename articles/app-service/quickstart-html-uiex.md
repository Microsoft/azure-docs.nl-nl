---
title: 'Snelstartgids: Een statische HTML-web-app maken'
description: Implementeer in enkele minuten uw eerste HTML Hallo wereld in Azure App Service. U implementeert met Git, een van de vele manieren waarop u kunt implementeren naar App Service.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: be555039375e2d71d00bcf8ee58898f54cc3645a
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379567"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Een statische HTML-web-app maken in Azure

In deze quickstart ziet u hoe u een eenvoudige HTML+CSS-site implementeert in <abbr title="Een OP HTTP gebaseerde service voor het hosten van webtoepassingen, REST API's en mobiele back-endtoepassingen.">Azure App Service</abbr>. U gaat deze Quickstart in [Cloud Shell](../cloud-shell/overview.md) doen, maar u kunt deze opdrachten ook lokaal uitvoeren met [Azure CLI](/cli/azure/install-azure-cli).

## <a name="1-prepare-your-environment"></a>1. Uw omgeving voorbereiden

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Maak [Cloud Shell](../cloud-shell/overview.md)snelstartmap en wijzig deze in .

```bash
mkdir quickstart

cd $HOME/quickstart
```

Voer vervolgens de volgende opdracht uit om de voorbeeld-app-opslagplaats te klonen naar de map 'snelstart'.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```
<hr/>

## <a name="2-create-a-web-app"></a>2. Een web-app maken

Ga naar de map die de voorbeeldcode bevat en voer de opdracht `az webapp up` uit. **Vervangen** `<app-name>` met een wereldwijd unieke naam.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

<details>
<summary>Problemen oplossen</summary>
<ul>
<li>Als de opdracht niet wordt herkend, moet u ervoor zorgen dat de Azure CLI is geïnstalleerd zoals <code>az</code> beschreven in <a href="#1-prepare-your-environment">Uw omgeving voorbereiden.</a></li>
<li>Vervang <code>&lt;app-name&gt;</code> door een naam die uniek is in Azure ( geldige tekens zijn , en <em> <code>a-z</code> <code>0-9</code> <code>-</code> </em> ). Het is handig om een combinatie van uw bedrijfsnaam en een app-id te gebruiken.</li>
<li>Met het argument <code>--sku F1</code> maakt u de web-app in de prijscategorie Gratis. Laat dit argument weg om een snellere Premium-laag te gebruiken, waarmee u kosten per uur in rekening worden gebracht.</li>
<li>Het <code>--html</code> argument geeft aan dat alle mapinhoud moet worden behandeld als statische inhoud en dat buildautomatisering moet worden uitgeschakeld.</li>
<li>U kunt eventueel het argument <code>--location &lt;location-name&gt;</code> toevoegen, waarbij <code>&lt;location-name&gt;</code> een beschikbare Azure-regio is. U kunt een lijst met toegestane regio's voor uw Azure-account ophalen door de opdracht uit te <a href="/cli/azure/appservice#az-appservice-list-locations"> <code>az account list-locations</code> </a> voeren.</li>
</ul>
</details>

Het volledig uitvoeren van de opdracht kan even duren. 

<details>
<summary>Wat doet <code>az webapp up</code> u?</summary>
<p>Met de opdracht <code>az webapp up</code> worden de volgende acties uitgevoerd:</p>
<ul>
<li>Er wordt een standaardresourcegroep gemaakt.</li>
<li>Een standaardplan App Service maken.</li>
<li><a href="/cli/azure/webapp#az-webapp-create">Maak een App Service app</a> met de opgegeven naam.</li>
<li>Er worden via zip bestanden van de huidige werkmap naar de app <a href="/azure/app-service/deploy-zip">geïmplementeerd</a>.</li>
<li>Tijdens het uitvoeren worden berichten over het maken van resources, logboekregistratie en ZIP-implementaties verstrekt.</li>
</ul>

Wanneer deze is klaar, wordt informatie weergegeven die lijkt op het volgende voorbeeld:

```output
{
  "app_url": "https://&lt;app_name&gt;.azurewebsites.net",
  "location": "westeurope",
  "name": "&lt;app_name&gt;",
  "os": "Windows",
  "resourcegroup": "appsvc_rg_Windows_westeurope",
  "serverfarm": "appsvc_asp_Windows_westeurope",
  "sku": "FREE",
  "src_path": "/home/&lt;username&gt;/quickstart/html-docs-hello-world ",
  &lt; JSON data removed for brevity. &gt;
}
```

</details>

U hebt de waarde later `resourceGroup` nodig om resources op [te](#6-clean-up-resources) schonen.

<hr/>

## <a name="3-browse-to-the-app"></a>3. Bladeren naar de app

Ga in een browser naar de URL van de app: `http://<app_name>.azurewebsites.net`.

De pagina wordt als een web-app uitgevoerd in Azure App Service.

![Startpagina van voorbeeld-app](media/quickstart-html/hello-world-in-browser-az.png)

<hr/>

## <a name="4-update-and-redeploy-the-app"></a>4. De app bijwerken en opnieuw installeren

Typ in Cloud Shell om **de** `nano index.html` nanoteksteditor te openen. 

Wijzig in `<h1>` de koptag 'Azure App Service - Sample Static HTML Site' in 'Azure App Service'.

![Nano index.html](media/quickstart-html/nano-index-html.png)

**Sla** uw wijzigingen op met behulp van de opdracht `^O` .

**Sluit** nano af met behulp van de opdracht `^X` .

Ployer de app opnieuw met de `az webapp up` opdracht .

```bash
az webapp up --html
```

Ga terug naar het browservenster dat is geopend in de **stap Bladeren naar de app.**

**Vernieuw** de pagina.

![Bijgewerkte startpagina van voorbeeld-app](media/quickstart-html/hello-azure-in-browser-az.png)

<hr/>

## <a name="5-manage-your-new-azure-app"></a>5. Uw nieuwe Azure-app beheren

**Navigeer** [naar Azure Portal](https://portal.azure.com). 

**Zoek** en **selecteer** **App Services**.

![App Services selecteren in de Azure-portal](./media/quickstart-html/portal0.png)

**Selecteer** de naam van uw Azure-app.

![Navigatie naar Azure-app in de portal](./media/quickstart-html/portal1.png)

De pagina Overzicht van uw web-app wordt weergegeven. Hier kunt u algemene beheertaken uitvoeren, zoals bladeren, stoppen, starten, opnieuw opstarten en verwijderen.

![App Service-blade in Azure Portal](./media/quickstart-html/portal2.png)

Het linkermenu bevat een aantal pagina's voor het configureren van uw app.

<hr/>

## <a name="6-clean-up-resources"></a>6. Resources ops schonen

In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. Als u deze resources niet meer nodig denkt te hebben, verwijdert u de resourcegroep door de volgende opdracht in Cloud Shell uit te voeren. De naam van de resourcegroep is automatisch gegenereerd in de stap [Een web-app maken](#2-create-a-web-app).

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

Het kan een minuut duren voordat deze opdracht is uitgevoerd.

<hr/>

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aangepast domein toewijzen](app-service-web-tutorial-custom-domain-uiex.md)
