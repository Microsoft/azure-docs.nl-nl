---
title: Functies uitschakelen in Azure Functions
description: Meer informatie over het uitschakelen en inschakelen van functies in Azure Functions.
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 03803abfda010c81fa8286a478d626ef39db59fb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777577"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Functies uitschakelen in Azure Functions

In dit artikel wordt uitgelegd hoe u een functie in Azure Functions. Als *u een* functie wilt uitschakelen, betekent dit dat de runtime de automatische trigger negeert die voor de functie is gedefinieerd. Hiermee kunt u voorkomen dat een specifieke functie wordt uitgevoerd zonder de hele functie-app te stoppen.

De aanbevolen manier om een functie uit te schakelen is met een app-instelling in de indeling `AzureWebJobs.<FUNCTION_NAME>.Disabled` ingesteld op `true` . U kunt deze toepassingsinstelling op een aantal manieren maken en wijzigen, waaronder  met behulp van [de Azure CLI](/cli/azure/) en via het tabblad Overzicht van uw functie in [Azure Portal](https://portal.azure.com). 

> [!NOTE]  
> Wanneer u een door HTTP geactiveerde functie uit schakelen met behulp van de methoden die in dit artikel worden beschreven, kan het eindpunt nog steeds toegankelijk zijn wanneer het wordt uitgevoerd op uw lokale computer.  

## <a name="disable-a-function"></a>Een functie uitschakelen

# <a name="portal"></a>[Portal](#tab/portal)

Gebruik de **knoppen Inschakelen** **en** Uitschakelen op de pagina Overzicht **van de** functie. Deze knoppen werken door de waarde van de `AzureWebJobs.<FUNCTION_NAME>.Disabled` app-instelling te wijzigen. Deze functiespecifieke instelling wordt gemaakt bij de eerste keer dat deze wordt uitgeschakeld. 

![Functietoestandsschakelaar](media/disable-function/function-state-switch.png)

Zelfs wanneer u vanuit een lokaal project naar uw functie-app publiceert, kunt u de portal nog steeds gebruiken om functies in de functie-app uit te schakelen. 

> [!NOTE]  
> De in de portal geïntegreerde testfunctionaliteit negeert de `Disabled` instelling. Dit betekent dat een uitgeschakelde functie nog steeds wordt uitgevoerd wanneer deze wordt gestart vanuit het **testvenster** in de portal. 

# <a name="azure-cli"></a>[Azure-CLI](#tab/azurecli)

In de Azure CLI gebruikt u de opdracht [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) om de app-instelling te maken en te wijzigen. Met de volgende opdracht wordt een functie met de naam uitgeschakeld door een app-instelling met de naam `QueueTrigger` te maken en deze in te stellen op `AzureWebJobs.QueueTrigger.Disabled` `true` . 

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Als u de functie opnieuw wilt inschakelen, moet u dezelfde opdracht opnieuw uitvoeren met de waarde `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Met [`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting) de opdracht wordt een toepassingsinstelling toegevoegd of bijgewerkt. Met de volgende opdracht wordt een functie met de naam uitgeschakeld door een app-instelling met de naam te `QueueTrigger` maken en deze in te stellen op `AzureWebJobs.QueueTrigger.Disabled` `true` . 

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "true"}
```

Als u de functie opnieuw wilt inschakelen, moet u dezelfde opdracht opnieuw uitvoeren met de waarde `false` .

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "false"}
```
---

## <a name="functions-in-a-slot"></a>Functies in een sleuf

App-instellingen zijn standaard ook van toepassing op apps die worden uitgevoerd in implementatiesleuven. U kunt echter de app-instelling die door de sleuf wordt gebruikt, overschrijven door een specifieke app-instelling voor de sleuf in te stellen. U wilt bijvoorbeeld dat een functie actief is in productie, maar niet tijdens het testen van de implementatie, zoals een door een timer geactiveerde functie. 

Een functie alleen uitschakelen in de staging-sleuf:

# <a name="portal"></a>[Portal](#tab/portal)

Navigeer naar het site-exemplaar  van uw functie-app door Implementatiesleuven te selecteren onder **Implementatie,** uw site te kiezen en Functies te **selecteren** in het site-exemplaar.  Kies uw functie en gebruik vervolgens de knoppen **Inschakelen** **en** Uitschakelen op de pagina **Overzicht van de** functie. Deze knoppen werken door de waarde van de `AzureWebJobs.<FUNCTION_NAME>.Disabled` app-instelling te wijzigen. Deze functiespecifieke instelling wordt gemaakt bij de eerste keer dat deze wordt uitgeschakeld. 

U kunt ook de app-instelling met de `AzureWebJobs.<FUNCTION_NAME>.Disabled` naam met de waarde van rechtstreeks toevoegen in de `true` **configuratie** voor het sleuf-exemplaar. Wanneer u een specifieke app-instelling voor een sleuf toevoegt, moet u het selectievakje **Implementatiesleufinstelling in-** of uit te checken. Hiermee behoudt u de instellingswaarde met de sleuf tijdens het wisselen.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azurecli)

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=true
```
Als u de functie opnieuw wilt inschakelen, moet u dezelfde opdracht opnieuw uitvoeren met de waarde `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell biedt momenteel geen ondersteuning voor deze functionaliteit.

---

Zie Implementatiesleuven voor [Azure Functions meer informatie.](functions-deployment-slots.md)

## <a name="localsettingsjson"></a>local.settings.json

Functies kunnen op dezelfde manier worden uitgeschakeld wanneer ze lokaal worden uitgevoerd. Als u een functie met de naam wilt uitschakelen, voegt u als volgt een vermelding toe aan de verzameling `HttpExample` Waarden in local.settings.jsbestand:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "UseDevelopmentStorage=true", 
    "AzureWebJobs.HttpExample.Disabled": "true"
  }
}
``` 

## <a name="other-methods"></a>Andere methoden

Hoewel de methode voor toepassingsinstelling wordt aanbevolen voor alle talen en alle runtimeversies, zijn er verschillende andere manieren om functies uit te schakelen. Deze methoden, die variëren per taal en runtimeversie, worden onderhouden voor compatibiliteit met eerdere versies. 

### <a name="c-class-libraries"></a>C#-klassebibliotheken

In een klassebibliotheekfunctie kunt u ook het kenmerk gebruiken om `Disable` te voorkomen dat de functie wordt geactiveerd. Met dit kenmerk kunt u de naam aanpassen van de instelling die wordt gebruikt om de functie uit te schakelen. Gebruik de versie van het kenmerk waarmee u een constructorparameter kunt definiëren die verwijst naar een booleaanse app-instelling, zoals wordt weergegeven in het volgende voorbeeld:

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Met deze methode kunt u de functie in- en uitschakelen door de app-instelling te wijzigen, zonder opnieuw te moeten worden gecompileerd of opnieuw moet worden gecompileerd. Als u een app-instelling wijzigt, wordt de functie-app opnieuw opgestart, waardoor de statuswijziging uitgeschakeld onmiddellijk wordt herkend.

Er is ook een constructor voor de parameter die geen tekenreeks accepteert voor de naam van de instelling. Deze versie van het kenmerk wordt niet aanbevolen. Als u deze versie gebruikt, moet u het project opnieuwcompileren en opnieuwployeren om de uitgeschakelde status van de functie te wijzigen.

### <a name="functions-1x---scripting-languages"></a>Functions 1.x : scripttalen

In versie 1.x kunt u ook de eigenschap van defunction.jsin het bestand gebruiken om aan te geven dat de runtime geen `disabled` functie moet activeren.  Deze methode werkt alleen voor scripttalen zoals C#-script en JavaScript. De `disabled` eigenschap kan worden ingesteld op of op de naam van een `true` app-instelling:

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
of 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

In het tweede voorbeeld wordt de functie uitgeschakeld wanneer er een app-instelling met de naam IS_DISABLED is ingesteld `true` op of 1.

>[!IMPORTANT]  
>De portal gebruikt toepassingsinstellingen om v1.x-functies uit te schakelen. Wanneer een toepassingsinstelling een conflict veroorzaakt met function.jsbestand, kan er een fout optreden. Verwijder de eigenschap uit `disabled` het bestand function.jsom fouten te voorkomen. 


## <a name="next-steps"></a>Volgende stappen

Dit artikel gaat over het uitschakelen van automatische triggers. Zie Triggers en bindingen voor meer informatie [over triggers.](functions-triggers-bindings.md)
