---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: d944d1d3e9c72471fab2435430a7d13e1770e807
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010454"
---
## <a name="local-settings-file"></a>Lokale instellingsbestand

In het bestand local.settings.json worden app-instellingen, verbindingsreeksen en instellingen opgeslagen die door lokale ontwikkelprogramma's worden gebruikt. Instellingen in het bestand local.settings.json worden alleen gebruikt als u projecten lokaal uitvoert. Het bestand met lokale instelling heeft de volgende structuur:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

Deze instellingen worden ondersteund wanneer u projecten lokaal uitvoert:

| Instelling      | Beschrijving                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Wanneer deze instelling is ingesteld op `true`, worden alle waarden versleuteld met een lokale machinesleutel. Gebruikt met `func settings`-opdrachten. De standaardwaarde is `false`. Mogelijk wilt u het bestand local.settings.json versleutelen op uw lokale computer wanneer het geheimen bevat, zoals serviceverbindingsreeksen. De host ontsleutelt de instellingen automatisch wanneer deze wordt uitgevoerd. Gebruik de opdracht `func settings decrypt` voordat u lokaal versleutelde instellingen probeert te lezen. |
| **`Values`** | Matrix van toepassingsinstellingen en verbindingsreeksen die worden gebruikt wanneer een project lokaal wordt uitgevoerd. De sleutelwaardeparen (tekenreeks-tekenreeks) corresponderen met toepassingsinstellingen in uw functie-app in Azure, zoals [`AzureWebJobsStorage`]. Veel triggers en bindingen hebben een eigenschap die verwijst naar een app-instelling voor verbindingsreeksen, bijvoorbeeld `Connection` voor [Blob-opslagtrigger](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). Voor deze eigenschappen hebt u een toepassingsinstelling nodig die is gedefinieerd in de matrix `Values`. Bekijk de onderliggende tabel voor een lijst met veelgebruikte instellingen. <br/>Waarden moeten tekenreeksen zijn en geen JSON-objecten of matrices. Instellingsnamen kunnen geen dubbele punt (`:`) bevatten of een dubbel onderstrepingsteken (`__`). Dubbele onderstrepingstekens worden gereserveerd tijdens de runtime en de dubbele punt is gereserveerd voor [afhankelijkheidsinjectie](../articles/azure-functions/functions-dotnet-dependency-injection.md#working-with-options-and-settings). |
| **`Host`** | Met de instellingen in dit gedeelte wordt het Functions-hostproces aangepast wanneer u projecten lokaal uitvoert. Deze instellingen staan los van de host.json-instellingen, die ook van toepassing zijn wanneer u projecten uitvoert in Azure. |
| **`LocalHttpPort`** | Hiermee stelt u de standaardpoort in die wordt gebruikt wanneer de lokale Functions-host wordt uitgevoerd (`func host start` en `func run`). De opdrachtregeloptie `--port` heeft voorrang op deze instelling. |
| **`CORS`** | Definieert de oorsprong die is toegestaan voor [CORS (Cross-origin-resource delen)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Oorsprongen worden aangeleverd als een door komma's gescheiden lijst zonder spaties. De jokertekenwaarde (\*) wordt ondersteund, waarmee verzoeken van elke oorsprong toegestaan zijn. |
| **`CORSCredentials`** |  Als deze instelling is ingesteld op `true`, zijn `withCredentials`-verzoeken toegestaan. |
| **`ConnectionStrings`** | Een verzameling. Gebruik deze verzameling niet voor de verbindingsreeksen die worden gebruikt door uw functiebindingen. Deze verzameling wordt alleen gebruikt door frameworks die doorgaans een verbindingsreeks krijgen van het gedeelte `ConnectionStrings` van een configuratiebestand, bijvoorbeeld [Entity Framework](/ef/ef6/). Verbindingsreeksen in dit object zijn toegevoegd aan de omgeving met het providertype van [System.Data.SqlClient](/dotnet/api/system.data.sqlclient). Items in deze verzameling worden niet gepubliceerd naar Azure met andere app-instellingen. U moet deze waarden expliciet toevoegen aan de verzameling `Connection strings` van de instellingen van uw functie-app. Als u een [`SqlConnection`](/dotnet/api/system.data.sqlclient.sqlconnection) maakt in uw functiecode, moet u de verbindingsreekswaarde bewaren met uw andere verbindingen in **Toepassingsinstellingen** in de portal. |

De volgende toepassingsinstellingen kunnen worden opgenomen in de matrix **`Values`** wanneer deze lokaal wordt uitgevoerd:

| Instelling | Waarden | Beschrijving |
|-----|-----|-----|
|**`AzureWebJobsStorage`**| Verbindingsreeks van opslagaccount of<br/>`UseDevelopmentStorage=true`| Bevat de verbindingsreeks voor een Azure-opslagaccount. Vereist wanneer andere triggers dan HTTP worden gebruikt. Raadpleeg de referentie [`AzureWebJobsStorage`] voor meer informatie.<br/>Wanneer u de [Azure Storage-emulator](../articles/storage/common/storage-use-emulator.md) lokaal hebt geïnstalleerd en u [`AzureWebJobsStorage`] hebt ingesteld op `UseDevelopmentStorage=true`, gebruikt Core Tools de emulator. De emulator is nuttig tijdens ontwikkeling, maar u moet testen met een echte opslagverbinding voor de implementatie.| 
|**`AzureWebJobs.<FUNCTION_NAME>.Disabled`**| `true`\|`false` | Als u een functie wilt uitschakelen wanneer u lokaal iets uitvoert, voegt u `"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"` toe aan de verzameling, waar `<FUNCTION_NAME>` de naam van de functie is. Raadpleeg voor meer informatie [Functies uitschakelen in Azure Functions](../articles/azure-functions/disable-function.md#localsettingsjson) |
|**`FUNCTIONS_WORKER_RUNTIME`** | `dotnet`<br/>`node`<br/>`java`<br/>`powershell`<br/>`python`| Geeft de doeltaal van de Functions-runtime aan. Vereist voor versie 2.x en hoger van de Functions-runtime. Deze instelling wordt gegenereerd voor uw project door Core Tools. Raadpleeg de referentie [`FUNCTIONS_WORKER_RUNTIME`](../articles/azure-functions/functions-app-settings.md#functions_worker_runtime) voor meer informatie.|
| **`FUNCTIONS_WORKER_RUNTIME_VERSION`** | `~7` |Geeft aan dat PowerShell 7 moet worden gebruikt bij lokaal uitvoeren. Zo niet, dan wordt PowerShell Core 6 gebruikt. Deze instelling wordt alleen gebruikt bij lokaal uitvoeren. Wanneer u in Azure uitvoert, wordt de PowerShell-runtimeversie bepaald door de siteconfiguratie-instelling `powerShellVersion`, die [in de portal kan worden ingesteld](../articles/azure-functions/functions-reference-powershell.md#changing-the-powershell-version). | 

['AzureWebJobsStorage']: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage