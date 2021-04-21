---
title: Runtimeversies Azure Functions doel
description: Azure Functions ondersteunt meerdere versies van de runtime. Meer informatie over het opgeven van de runtime-versie van een functie-app die wordt gehost in Azure.
ms.topic: conceptual
ms.date: 07/22/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ca107ec2f0ce04bf7b1eae3a98087217c267d33d
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830912"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Runtime-Azure Functions gebruiken

Een functie-app wordt uitgevoerd op een specifieke versie van de Azure Functions runtime. Er zijn drie belangrijke versies: [3.x, 2.x en 1.x.](functions-versions.md) Functie-apps worden standaard gemaakt in versie 3.x van de runtime. In dit artikel wordt uitgelegd hoe u een functie-app configureert in Azure om uit te voeren op de versie die u kiest. Zie Code and test Azure Functions local (Code en testomgeving lokaal) voor meer informatie over het configureren [van een lokale ontwikkelomgeving Azure Functions een specifieke versie.](functions-run-local.md)

De manier waarop u zich handmatig op een specifieke versie richt, is afhankelijk van het feit of u Windows of Linux gebruikt.

## <a name="automatic-and-manual-version-updates"></a>Automatische en handmatige versie-updates

_Deze sectie is niet van toepassing bij het uitvoeren van uw functie-app [op Linux.](#manual-version-updates-on-linux)_

Azure Functions kunt u zich richten op een specifieke versie van de runtime in Windows met behulp van `FUNCTIONS_EXTENSION_VERSION` de toepassingsinstelling in een functie-app. De functie-app blijft op de opgegeven hoofdversie totdat u expliciet kiest om over te gaan naar een nieuwe versie. Als u alleen de belangrijkste versie opgeeft, wordt de functie-app automatisch bijgewerkt naar nieuwe secundaire versies van de runtime wanneer deze beschikbaar komen. Nieuwe secundaire versies mogen geen belangrijke wijzigingen introduceren. 

Als u een secundaire versie opgeeft (bijvoorbeeld '2.0.12345'), wordt de functie-app vastgemaakt aan die specifieke versie totdat u deze expliciet wijzigt. Oudere secundaire versies worden regelmatig verwijderd uit de productieomgeving. Als uw secundaire versie wordt verwijderd, wordt uw functie-app weer uitgevoerd op de nieuwste versie in plaats van de versie die is ingesteld in `FUNCTIONS_EXTENSION_VERSION` . Als zodanig moet u snel eventuele problemen met uw functie-app oplossen waarvoor een specifieke secundaire versie is vereist. Vervolgens kunt u terugkeren naar de belangrijkste versie. Secundaire versieverwijderingen worden aangekondigd in [App Service aankondigingen](https://github.com/Azure/app-service-announcements/issues).

> [!NOTE]
> Als u vastmaken aan een specifieke belangrijke versie van Azure Functions en vervolgens probeert te publiceren naar Azure met behulp van Visual Studio, wordt er een dialoogvenster weergegeven waarin u wordt gevraagd om bij te werken naar de nieuwste versie of de publicatie te annuleren. Voeg de eigenschap toe aan `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` het bestand om dit te `.csproj` voorkomen.

Wanneer een nieuwe versie openbaar beschikbaar is, kunt u met een prompt in de portal naar die versie gaan. Nadat u naar een nieuwe versie bent verplaatst, kunt u altijd de toepassingsinstelling gebruiken om `FUNCTIONS_EXTENSION_VERSION` terug te gaan naar een eerdere versie.

De volgende tabel bevat de waarden `FUNCTIONS_EXTENSION_VERSION` voor elke belangrijke versie voor het inschakelen van automatische updates:

| Primaire versie | `FUNCTIONS_EXTENSION_VERSION` Waarde |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

Een wijziging in de runtime-versie zorgt ervoor dat een functie-app opnieuw wordt gestart.

>[!NOTE]
>.NET Function-apps die zijn `~2.0` vastgemaakt om af te zien van de automatische upgrade naar .NET Core 3.1. Zie Overwegingen voor [Functions v2.x voor meer informatie.](functions-dotnet-class-library.md#functions-v2x-considerations)  

## <a name="view-and-update-the-current-runtime-version"></a>De huidige runtimeversie weergeven en bijwerken

_Deze sectie is niet van toepassing bij het uitvoeren van uw functie-app [op Linux.](#manual-version-updates-on-linux)_

U kunt de runtimeversie wijzigen die wordt gebruikt door uw functie-app. Vanwege de mogelijke wijzigingen die kunnen worden gewijzigd, kunt u alleen de runtimeversie wijzigen voordat u functies in uw functie-app hebt gemaakt. 

> [!IMPORTANT]
> Hoewel de runtimeversie wordt bepaald door de instelling, moet u deze wijziging alleen in de Azure Portal en niet door de `FUNCTIONS_EXTENSION_VERSION` instelling rechtstreeks te wijzigen. Dit komt doordat de portal uw wijzigingen valideert en, naar behoefte, andere gerelateerde wijzigingen aan te brengen.

# <a name="portal"></a>[Portal](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Met de Azure Portal kunt u de runtimeversie niet wijzigen voor een functie-app die al functies bevat.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azurecli)

U kunt de ook weergeven en instellen `FUNCTIONS_EXTENSION_VERSION` vanuit de Azure CLI.  

Gebruik de Azure CLI om de huidige runtimeversie weer te geven met [de opdracht az functionapp config appsettings set.](/cli/azure/functionapp/config/appsettings)

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

Vervang in deze code `<function_app>` door de naam van uw functie-app. Vervang ook `<my_resource_group>` door de naam van de resourcegroep voor uw functie-app. 

U ziet de in de volgende uitvoer, die `FUNCTIONS_EXTENSION_VERSION` voor de duidelijkheid is afgekapt:

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

U kunt de instelling `FUNCTIONS_EXTENSION_VERSION` in de functie-app bijwerken met [de opdracht az functionapp config appsettings set.](/cli/azure/functionapp/config/appsettings)

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings FUNCTIONS_EXTENSION_VERSION=<VERSION>
```

Vervang `<FUNCTION_APP>` door de naam van uw functie-app. Vervang ook `<RESOURCE_GROUP>` door de naam van de resourcegroep voor uw functie-app. Vervang ook `<VERSION>` door een specifieke versie, of , of `~3` `~2` `~1` .

Kies **Proberen in het** vorige codevoorbeeld om de opdracht uit te voeren in [Azure Cloud Shell](../cloud-shell/overview.md). U kunt de [Azure CLI ook lokaal uitvoeren om](/cli/azure/install-azure-cli) deze opdracht uit te voeren. Wanneer u lokaal wordt uitgevoerd, moet u eerst [az login uitvoeren om](/cli/azure/reference-index#az_login) u aan te melden.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u de Azure Functions runtime wilt controleren, gebruikt u de volgende cmdlet: 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```

Vervang `<FUNCTION_APP>` door de naam van uw functie-app en `<RESOURCE_GROUP>` . De huidige waarde van de `FUNCTIONS_EXTENSION_VERSION` instelling wordt geretourneerd in de hash-tabel.

Gebruik het volgende script om de Functions-runtime te wijzigen:

```powershell
Update-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>" -AppSetting @{"FUNCTIONS_EXTENSION_VERSION" = "<VERSION>"} -Force
```

Vervang net als `<FUNCTION_APP>` voorheen door de naam van uw functie-app en `<RESOURCE_GROUP>` door de naam van de resourcegroep. Vervang ook `<VERSION>` door de specifieke versie of de hoofdversie, zoals of `~2` `~3` . U kunt de bijgewerkte waarde van de instelling `FUNCTIONS_EXTENSION_VERSION` in de geretourneerde hashtabel controleren. 

---

De functie-app wordt opnieuw opgestart nadat de toepassingsinstelling is gewijzigd.

## <a name="manual-version-updates-on-linux"></a>Handmatige versie-updates in Linux

Als u een Linux-functie-app wilt vastmaken aan een specifieke hostversie, geeft u de afbeeldings-URL op in het veld LinuxFxVersion in de site-configuratie. Bijvoorbeeld: als we een functie-app van knooppunt 10 willen vastmaken met de hostversie 3.0.13142 -

Voor **Linux App Service-/elastische Premium-apps:** stel in `LinuxFxVersion` op `DOCKER|mcr.microsoft.com/azure-functions/node:3.0.13142-node10-appservice` .

Voor **linux-verbruiksapps:** stel `LinuxFxVersion` in op `DOCKER|mcr.microsoft.com/azure-functions/mesh:3.0.13142-node10` .

# <a name="portal"></a>[Portal](#tab/portal)

Het weergeven en wijzigen van site-configuratie-instellingen voor functie-apps wordt niet ondersteund in de Azure Portal. Gebruik in plaats daarvan de Azure CLI.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azurecli)

U kunt de weergeven en instellen `LinuxFxVersion` met behulp van de Azure CLI.  

Als u de huidige runtimeversie wilt weergeven, gebruikt u met [de opdracht az functionapp config show.](/cli/azure/functionapp/config)

```azurecli-interactive
az functionapp config show --name <function_app> \
--resource-group <my_resource_group> --query 'linuxFxVersion' -o tsv
```

Vervang in deze code door `<function_app>` de naam van uw functie-app. Vervang ook `<my_resource_group>` door de naam van de resourcegroep voor uw functie-app. De huidige waarde van `linuxFxVersion` wordt geretourneerd.

Als u de instelling `linuxFxVersion` in de functie-app wilt bijwerken, gebruikt [u de opdracht az functionapp config set.](/cli/azure/functionapp/config)

```azurecli-interactive
az functionapp config set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--linux-fx-version <LINUX_FX_VERSION>
```

Vervang `<FUNCTION_APP>` door de naam van uw functie-app. Vervang ook `<RESOURCE_GROUP>` door de naam van de resourcegroep voor uw functie-app. Vervang ook door `<LINUX_FX_VERSION>` de waarde van een specifieke afbeelding zoals hierboven wordt beschreven.

U kunt deze opdracht uitvoeren vanaf de  [Azure Cloud Shell](../cloud-shell/overview.md) door In het voorgaande codevoorbeeld Proberen te kiezen. U kunt de [Azure CLI](/cli/azure/install-azure-cli) ook lokaal gebruiken om deze opdracht uit te voeren nadat u az login hebt uitgevoerd [om](/cli/azure/reference-index#az_login) u aan te melden.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Azure PowerShell kan op dit moment niet worden gebruikt om de `linuxFxVersion` in te stellen. Gebruik in plaats daarvan de Azure CLI.

---

De functie-app wordt opnieuw opgestart nadat de wijziging is aangebracht in de site-configuratie.

> [!NOTE]
> Voor apps die worden uitgevoerd in een verbruiksplan, kan het `LinuxFxVersion` instellen op een specifieke afbeelding de koude starttijden verhogen. Dit komt doordat het vastmaken aan een specifieke afbeelding voorkomt dat Functions enkele optimalisaties voor koude start gebruikt. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Richt u op de 2.0-runtime in uw lokale ontwikkelomgeving](functions-run-local.md)

> [!div class="nextstepaction"]
> [Zie Release notes for runtime versions (Release-opmerkingen voor runtimeversies)](https://github.com/Azure/azure-webjobs-sdk-script/releases)
