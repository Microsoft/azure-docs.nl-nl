---
title: Azure Functions runtime-versies instellen
description: Azure Functions ondersteunt meerdere versies van de runtime. Meer informatie over het opgeven van de runtime versie van een functie-app die wordt gehost in Azure.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 46bf7849888033b2bbb7e9b9669ee3eae4de10e9
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916521"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Azure Functions runtime-versies instellen

Een functie-app wordt uitgevoerd op een specifieke versie van de Azure Functions runtime. Er zijn drie hoofd versies: [1. x, 2. x en 3. x](functions-versions.md). Functie-apps worden standaard gemaakt in versie 3. x van de runtime. In dit artikel wordt uitgelegd hoe u een functie-app in azure kunt configureren om uit te voeren op de versie die u kiest. Zie voor meer informatie over het configureren van een lokale ontwikkel omgeving voor een specifieke versie [code en testen Azure functions lokaal](functions-run-local.md).

De manier waarop u een specifieke versie hand matig wilt richten, is afhankelijk van of u Windows of Linux uitvoert.

## <a name="automatic-and-manual-version-updates"></a>Automatische en hand matige versie-updates

_Deze sectie is niet van toepassing wanneer u uw functie-app [in Linux](#manual-version-updates-on-linux)uitvoert._

Met Azure Functions kunt u een specifieke versie van de runtime op Windows richten met behulp van de `FUNCTIONS_EXTENSION_VERSION` toepassings instelling in een functie-app. De functie-app wordt op de opgegeven primaire versie bewaard totdat u expliciet naar een nieuwe versie gaat verplaatsen. Als u alleen de primaire versie opgeeft, wordt de functie-app automatisch bijgewerkt naar nieuwe secundaire versies van de runtime wanneer deze beschikbaar komen. Nieuwe secundaire versies mogen geen belang rijke wijzigingen introduceren. 

Als u een secundaire versie opgeeft (bijvoorbeeld ' 2.0.12345 '), wordt de functie-app vastgemaakt aan die specifieke versie totdat u deze expliciet wijzigt. Oudere secundaire versies worden regel matig verwijderd uit de productie omgeving. Als dit gebeurt, wordt de functie-app uitgevoerd op de nieuwste versie in plaats van de versie die is ingesteld in `FUNCTIONS_EXTENSION_VERSION` . Daarom moet u snel problemen met uw functie-app oplossen waarvoor een specifieke secundaire versie is vereist, zodat u in plaats daarvan de primaire versie kunt instellen. Verwijderingen van de secundaire versie worden aangekondigd in [app service aankondigingen](https://github.com/Azure/app-service-announcements/issues).

> [!NOTE]
> Als u vastmaakt aan een specifieke primaire versie van Azure Functions en vervolgens probeert te publiceren naar Azure met behulp van Visual Studio, wordt er een dialoog venster weer gegeven waarin u wordt gevraagd om bij te werken naar de meest recente versie of om de publicatie te annuleren. U kunt dit voor komen door de `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` eigenschap in het bestand toe te voegen `.csproj` .

Wanneer een nieuwe versie openbaar beschikbaar is, kunt u met een prompt in de portal naar de gewenste versie gaan. Nadat u naar een nieuwe versie hebt verplaatst, kunt u de `FUNCTIONS_EXTENSION_VERSION` toepassings instelling altijd gebruiken om terug te gaan naar een vorige versie.

In de volgende tabel ziet `FUNCTIONS_EXTENSION_VERSION` u de waarden voor elke primaire versie om automatische updates in te scha kelen:

| Primaire versie | `FUNCTIONS_EXTENSION_VERSION` Value |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

Een wijziging in de runtime versie leidt ertoe dat een functie-app opnieuw wordt gestart.

## <a name="view-and-update-the-current-runtime-version"></a>De huidige runtime versie weer geven en bijwerken

_Deze sectie is niet van toepassing wanneer u uw functie-app [in Linux](#manual-version-updates-on-linux)uitvoert._

U kunt de runtime versie die wordt gebruikt door uw functie-app wijzigen. U kunt de runtime-versie alleen wijzigen voordat u functies in uw functie-app hebt gemaakt. 

> [!IMPORTANT]
> Hoewel de runtime versie wordt bepaald door de `FUNCTIONS_EXTENSION_VERSION` instelling, moet u deze wijziging aanbrengen in de Azure Portal en niet door de instelling rechtstreeks te wijzigen. Dit komt doordat de portal uw wijzigingen valideert en andere gerelateerde wijzigingen indien nodig maakt.

# <a name="portal"></a>[Portal](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Met behulp van de Azure Portal kunt u de runtime versie niet wijzigen voor een functie-app die al functies bevat.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azurecli)

U kunt ook de `FUNCTIONS_EXTENSION_VERSION` van de Azure cli weer geven en instellen.  

Bekijk met behulp van de Azure CLI de huidige runtime versie met de opdracht [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) .

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

Vervang in deze code door `<function_app>` de naam van uw functie-app. Vervang ook door `<my_resource_group>` de naam van de resource groep voor uw functie-app. 

U ziet de `FUNCTIONS_EXTENSION_VERSION` in de volgende uitvoer, die is afgekapt voor duidelijkheid:

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

U kunt de `FUNCTIONS_EXTENSION_VERSION` instelling in de functie-app bijwerken met de opdracht [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) .

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings FUNCTIONS_EXTENSION_VERSION=<VERSION>
```

Vervang door `<FUNCTION_APP>` de naam van uw functie-app. Vervang ook door `<RESOURCE_GROUP>` de naam van de resource groep voor uw functie-app. Vervang ook door `<VERSION>` een specifieke versie, of `~3` , `~2` , of `~1` .

U kunt deze opdracht uitvoeren vanuit de [Azure Cloud shell](../cloud-shell/overview.md) door deze in het voor gaande code voorbeeld **te kiezen.** U kunt de [Azure cli ook lokaal](/cli/azure/install-azure-cli) gebruiken om deze opdracht uit te voeren nadat u [AZ login](/cli/azure/reference-index#az-login) hebt uitgevoerd om u aan te melden.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u de runtime van Azure Functions wilt controleren, gebruikt u de volgende cmdlet: 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```

Vervang door `<FUNCTION_APP>` de naam van uw functie-app en `<RESOURCE_GROUP>` . De huidige waarde van de `FUNCTIONS_EXTENSION_VERSION` instelling wordt geretourneerd in de hash-tabel.

Gebruik het volgende script om de functions-runtime te wijzigen:

```powershell
Update-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>" -AppSetting @{"FUNCTIONS_EXTENSION_VERSION" = "<VERSION>"} -Force
```

Vervang door `<FUNCTION_APP>` de naam van uw functie-app en `<RESOURCE_GROUP>` met de naam van de resource groep. Vervang ook door `<VERSION>` de specifieke versie of primaire versie, zoals `~2` of `~3` . U kunt de bijgewerkte waarde van de `FUNCTIONS_EXTENSION_VERSION` instelling in de geretourneerde hash-tabel controleren. 

---

De functie-app wordt opnieuw opgestart nadat de wijziging is aangebracht in de toepassings instelling.

## <a name="manual-version-updates-on-linux"></a>Hand matige versie-updates op Linux

Als u een Linux-functie-app wilt vastmaken aan een specifieke versie van de host, geeft u de afbeeldings-URL op in het veld ' LinuxFxVersion ' in site configuratie. Bijvoorbeeld: als we een functie-app met een knoop punt 10 willen vastmaken om 3.0.13142 te zeggen

Voor **Linux app service/elastisch Premium-apps** : Stel deze `LinuxFxVersion` in op `DOCKER|mcr.microsoft.com/azure-functions/node:3.0.13142-node10-appservice` .

Voor **Linux-verbruiks-apps** : ingesteld `LinuxFxVersion` op `DOCKER|mcr.microsoft.com/azure-functions/mesh:3.0.13142-node10` .


# <a name="azure-cli"></a>[Azure-CLI](#tab/azurecli-linux)

U kunt de `LinuxFxVersion` van de Azure cli weer geven en instellen.  

Gebruik de Azure CLI om de huidige runtime versie weer te geven met de opdracht [AZ functionapp config show](/cli/azure/functionapp/config) .

```azurecli-interactive
az functionapp config show --name <function_app> \
--resource-group <my_resource_group>
```

Vervang in deze code door `<function_app>` de naam van uw functie-app. Vervang ook door `<my_resource_group>` de naam van de resource groep voor uw functie-app. 

U ziet de `linuxFxVersion` in de volgende uitvoer, die is afgekapt voor duidelijkheid:

```output
{
  ...

  "kind": null,
  "limits": null,
  "linuxFxVersion": <LINUX_FX_VERSION>,
  "loadBalancing": "LeastRequests",
  "localMySqlEnabled": false,
  "location": "West US",
  "logsDirectorySizeLimit": 35,
   ...
}
```

U kunt de `linuxFxVersion` instelling in de functie-app bijwerken met de opdracht [AZ functionapp config set](/cli/azure/functionapp/config) .

```azurecli-interactive
az functionapp config set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--linux-fx-version <LINUX_FX_VERSION>
```

Vervang door `<FUNCTION_APP>` de naam van uw functie-app. Vervang ook door `<RESOURCE_GROUP>` de naam van de resource groep voor uw functie-app. Vervang ook door `<LINUX_FX_VERSION>` de waarden die hierboven worden uitgelegd.

U kunt deze opdracht uitvoeren vanuit de [Azure Cloud shell](../cloud-shell/overview.md) door deze in het voor gaande code voorbeeld **te kiezen.** U kunt de [Azure cli ook lokaal](/cli/azure/install-azure-cli) gebruiken om deze opdracht uit te voeren nadat u [AZ login](/cli/azure/reference-index#az-login) hebt uitgevoerd om u aan te melden.


Op dezelfde manier wordt de functie-app opnieuw opgestart nadat de wijziging is aangebracht in de site configuratie.

> [!NOTE]
> Houd er rekening mee dat `LinuxFxVersion` bij het rechtstreeks instellen van de afbeeldings-URL voor verbruiks-apps de tijdelijke aanduidingen en andere koude start optimalisaties worden afgemeld.

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [De 2,0-runtime in uw lokale ontwikkel omgeving richten](functions-run-local.md)

> [!div class="nextstepaction"]
> [Zie de release opmerkingen voor runtime versies](https://github.com/Azure/azure-webjobs-sdk-script/releases)
