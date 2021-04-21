---
title: Een App Service-app maken met behulp van een Azure Resource Manager-sjabloon
description: Maak binnen enkele seconden uw eerste app voor Azure App Service met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon). Dit is een van de vele manieren om in App Service te implementeren.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 10/16/2020
ms.custom: subject-armqs, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: bce6bfb61eb59d1fa66c550a133ac8b6f8d7f2c5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768998"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>Quickstart: App Service-app maken met behulp van een ARM-sjabloon

Aan de slag [Azure App Service](overview.md) door een app in de cloud te implementeren met behulp van een <abbr title="Een JSON-bestand dat declaratief een of meer Azure-resources en afhankelijkheden tussen de geïmplementeerde resources definieert. De sjabloon kan worden gebruikt om de resources consistent en herhaaldelijk te implementeren.">ARM-sjabloon</abbr> en [Azure CLI](/cli/azure/get-started-with-azure-cli) in Cloud Shell. Omdat u een gratis servicelaag van App Service gebruikt, zijn er geen kosten verbonden aan het voltooien van deze quickstart. <abbr title="In declaratieve syntaxis beschrijft u de beoogde implementatie zonder dat u de reeks programmeeropdrachten voor het maken van de implementatie hoeft te schrijven.">Voor de sjabloon is declaratieve syntaxis vereist.</abbr>

 Als uw omgeving voldoet aan de vereisten en u bekend bent met het gebruik van [ARM-sjablonen,](../azure-resource-manager/templates/overview.md)selecteert u **de knop Implementeren in Azure.** De sjabloon wordt in Azure Portal geopend.

::: zone pivot="platform-windows"
[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-windows%2Fazuredeploy.json)
::: zone-end

::: zone pivot="platform-linux"
[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-linux%2Fazuredeploy.json)
::: zone-end

<hr/>

## <a name="1-prepare-your-environment"></a>1. Uw omgeving voorbereiden

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<hr/>

## <a name="2-review-the-template"></a>2. De sjabloon controleren

::: zone pivot="platform-windows"
De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-app-service-docs-windows). Hiermee wordt een App Service-plan en een App Service-app in Windows geïmplementeerd.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json":::

<details>
<summary>Welke resources en parameters zijn gedefinieerd in de sjabloon?</summary>

Er worden twee Azure-resources gedefinieerd in de sjabloon:

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): een App Service-plan maken.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): een App Service-app maken.

In de volgende tabel worden de standaardparameters en de bijbehorende beschrijvingen beschreven:

| Parameters | Type     | Standaardwaarde                | Beschrijving |
|------------|---------|------------------------------|-------------|
| webAppName | tekenreeks  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Naam van app |
| location   | tekenreeks  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | App-regio |
| sku        | tekenreeks  | "F1"                         | Exemplaargrootte (F1 = gratis laag) |
| language   | tekenreeks  | ".net"                       | Stack met programmeertalen (.net, php, node, html) |
| helloWorld | booleaans | False                        | True = Hello World-app implementeren |
| repoUrl    | tekenreeks  | " "                          | Externe Git-opslagplaats (optioneel) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-app-service-docs-linux). Hiermee wordt een App Service-plan en een App Service-app in Windows geïmplementeerd.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json":::

Deze sjabloon bevat Azure-resources en -parameters die voor uw gemak zijn gedefinieerd.

<details>
<summary>Welke resources en parameters zijn gedefinieerd in de sjabloon?</summary>

Er worden twee Azure-resources gedefinieerd in de sjabloon:

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): een App Service-plan maken.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): een App Service-app maken.

In de volgende tabel worden de standaardparameters en de bijbehorende beschrijvingen beschreven:

| Parameters | Type     | Standaardwaarde                | Beschrijving |
|------------|---------|------------------------------|-------------|
| webAppName | tekenreeks  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Naam van app |
| location   | tekenreeks  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | App-regio |
| sku        | tekenreeks  | "F1"                         | Exemplaargrootte (F1 = gratis laag) |
| linuxFxVersion   | tekenreeks  | DOTNETCORE&#124;3.0        | Stack met programmeertalen &#124; versie |
| repoUrl    | tekenreeks  | " "                          | Externe Git-opslagplaats (optioneel) |

---

</details>

::: zone-end

<hr/>

## <a name="3-deploy-the-template"></a>3. De sjabloon implementeren

::: zone pivot="platform-windows"
Voer de onderstaande code uit om een .NET Framework-app in Windows te implementeren met behulp van Azure CLI. 

Vervangen <abbr title="Geldige tekens zijn `a-z` , `0-9` en `-` .">`<app-name>`</abbr> met een wereldwijd unieke app-naam. Meer informatie over andere <abbr title="U kunt ook Azure Portal, Azure PowerShell, de REST API gebruiken.">implementatiemethoden</abbr>, zie [Sjablonen implementeren.](../azure-resource-manager/templates/deploy-powershell.md) U vindt [hier](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites) meer App Service-sjabloonvoorbeelden.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
```
::: zone-end
::: zone pivot="platform-linux"
Voer de onderstaande code uit om een Python-app te maken in Linux. 

Vervangen <abbr title="Geldige tekens zijn `a-z` , `0-9` en `-` .">`<app-name>`</abbr> door een wereldwijd unieke app-naam.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```
::: zone-end

<details>
<summary>Wat doet de code?</summary>
<p>De opdrachten voeren de volgende acties uit:</p>
<ul>
<li>Een standaardinstelling maken <abbr title="Een logische container voor gerelateerde Azure-resources die u als eenheid kunt beheren.">resourcegroep</abbr>.</li>
<li>Een standaardinstelling maken <abbr title="Het plan waarmee de locatie, grootte en functies van de webserverfarm worden opgegeven die als host voor uw app wordt gebruikt.">App Service-plan</abbr>.</li>
<li><a href="/cli/azure/webapp#az_webapp_create">Een maken <abbr title="De weergave van uw web-app, die uw app-code, DNS-hostnamen, certificaten en gerelateerde resources bevat. "> App Service app</abbr></a> met de opgegeven naam.</li>
</ul>
</details>

::: zone pivot="platform-windows"
<details>
<summary>Hoe kan ik een andere taalstack implementeren?</summary>
Als u een andere taalstack wilt implementeren, moet u bijwerken <abbr title="Deze sjabloon is compatibel met .NET Core-, .NET Framework-, PHPNode.js- en statische HTML-apps. "> taalparameter</abbr> met de juiste waarden. Raadpleeg <a href="/azure/app-service/quickstart-java-uiex">Java-app maken</a> voor Java.

| Parameters | Type     | Standaardwaarde                | Beschrijving |
|------------|---------|------------------------------|-------------|
| language   | tekenreeks  | ".net"                       | Stack met programmeertalen (.net, php, node, html) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
<details>
<summary>Hoe kan ik een andere taalstack implementeren?</summary>
Als u een andere taalstack wilt implementeren, werkt u `linuxFxVersion` bij met de juiste waarden. Voorbeelden worden hieronder weergegeven. Als u huidige versies wilt weergeven, voert u de volgende opdracht in de Cloud Shell uit: `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Taal    | Voorbeeld:                                              |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion="DOTNETCORE&#124;3.0"                 |
| **PHP**     | linuxFxVersion="PHP&#124;7.4"                        |
| **Node.js** | linuxFxVersion="NODE&#124;10.15"                     |
| **Java**    | linuxFxVersion="JAVA&#124;1.8 &#124;TOMCAT&#124;9.0" |
| **Python**  | linuxFxVersion="PYTHON&#124;3.7"                     |
| **Ruby**    | linuxFxVersion="RUBY&#124;2.6"                       |

---

</details>
::: zone-end

<hr/>

## <a name="4-validate-the-deployment"></a>4. De implementatie valideren

Blader naar `http://<app_name>.azurewebsites.net/` en controleer of deze is gemaakt.

<hr/>

## <a name="5-clean-up-resources"></a>5. Resources ops schonen

[Verwijder de resourcegroep](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group) als u deze niet meer nodig hebt.

<hr/>

## <a name="next-steps"></a>Volgende stappen

- Implementeren vanuit lokale Git
- [ASP.NET Core met SQL Database](tutorial-dotnetcore-sqldb-app.md)
- Python met Postgres
- [PHP met MySQL](tutorial-php-mysql-app.md)
- [Verbinding maken met Azure SQL-database met Java](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)
- [Aangepast domein toewijzen](app-service-web-tutorial-custom-domain-uiex.md)
