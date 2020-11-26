---
title: Hulp bij het ontwikkelen van Azure Functions
description: Meer informatie over de Azure Functions-concepten en-technieken die u nodig hebt voor het ontwikkelen van functies in azure, in alle programmeer talen en bindingen.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: 54bfd770fba9a1766396d66c0c263111c233c9c2
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96167876"
---
# <a name="azure-functions-developer-guide"></a>Ontwikkelaarshandleiding voor Azure Functions
In Azure Functions delen specifieke functies enkele kern technische concepten en onderdelen, ongeacht de taal of binding die u gebruikt. Lees de informatie in dit overzicht die van toepassing is op alle voor waarden, voordat u naar een bepaalde taal of binding gaat gaan.

In dit artikel wordt ervan uitgegaan dat u het [Azure functions overzicht](functions-overview.md)al hebt gelezen.

## <a name="function-code"></a>Functie code
Een *functie* is het primaire concept in azure functions. Een functie bevat twee belang rijke onderdelen: uw code, die in verschillende talen kan worden geschreven, en sommige configuratie, het function.jsbestand. Voor gecompileerde talen wordt dit configuratie bestand automatisch gegenereerd vanuit aantekeningen in uw code. Voor script talen moet u zelf het configuratie bestand opgeven.

De function.jsin het bestand definieert de trigger, bindingen en andere configuratie-instellingen van de functie. Elke functie kan slechts één trigger hebben. De runtime gebruikt dit configuratie bestand om te bepalen welke gebeurtenissen moeten worden bewaakt en hoe gegevens moeten worden door gegeven en hoe gegevens kunnen worden opgehaald uit een functie-uitvoering. Hier volgt een voor beeld function.jsvan een bestand.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Zie [Azure functions triggers en bindingen concepten](functions-triggers-bindings.md)voor meer informatie.

De `bindings` eigenschap is waar u zowel triggers als bindingen configureert. Elke binding deelt enkele algemene instellingen en enkele instellingen die specifiek zijn voor een bepaald type binding. Voor elke binding zijn de volgende instellingen vereist:

| Eigenschap | Waarden/typen | Opmerkingen |
| --- | --- | --- |
| `type` |tekenreeks |Bindings type. Bijvoorbeeld `queueTrigger`. |
| `direction` |' in ', ' out ' |Geeft aan of de binding is voor het ontvangen van gegevens van de functie of het verzenden van gegevens van de functie. |
| `name` |tekenreeks |De naam die wordt gebruikt voor de afhankelijke gegevens in de functie. Voor C# is dit een argument naam; voor Java script is het de sleutel in een lijst met sleutel/waarden. |

## <a name="function-app"></a>Function App
Een functie-app biedt een uitvoerings context in azure waarin uw functies worden uitgevoerd. Zo is het de implementatie-en beheer eenheid voor uw functies. Een functie-app bestaat uit een of meer afzonderlijke functies die met elkaar worden beheerd, geïmplementeerd en geschaald. Alle functies in een functie-app delen hetzelfde prijs plan, dezelfde implementatie methode en dezelfde runtime versie. U kunt een functie-app beschouwen als een manier om uw functies te organiseren en gezamenlijk te beheren. Zie [een functie-app beheren](functions-how-to-use-azure-function-app-settings.md)voor meer informatie. 

> [!NOTE]
> Alle functies in een functie-app moeten in dezelfde taal zijn geschreven. In [eerdere versies](functions-versions.md) van de Azure functions runtime is dit niet vereist.

## <a name="folder-structure"></a>Mapstructuur
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Hierboven vindt u de standaard (en aanbevolen) mapstructuur voor een functie-app. Als u de bestands locatie van een functie code wilt wijzigen, wijzigt u de `scriptFile` sectie van de _function.jsin_ het bestand. We raden u ook aan om [pakket implementatie](deployment-zip-push.md) te gebruiken voor het implementeren van uw project in uw functie-app in Azure. U kunt ook bestaande hulpprogram ma's zoals [continue integratie en implementatie](functions-continuous-deployment.md) en Azure DevOps gebruiken.

> [!NOTE]
> Als u een pakket hand matig implementeert, moet u ervoor zorgen dat u de _host.jsop_ bestands-en functie mappen rechtstreeks naar de map implementeert `wwwroot` . Neem de map niet `wwwroot` op in uw implementaties. Anders eindigt u met `wwwroot\wwwroot` mappen.

#### <a name="use-local-tools-and-publishing"></a>Lokale hulpprogram ma's en publicatie gebruiken
Functie-apps kunnen worden gemaakt en gepubliceerd met behulp van verschillende hulpprogram ma's, waaronder [Visual Studio](./functions-develop-vs.md), [Visual Studio code](./create-first-function-vs-code-csharp.md), [IntelliJ](./functions-create-maven-intellij.md), [eclips](./functions-create-maven-eclipse.md)en de [Azure functions core tools](./functions-develop-local.md). Zie [code en test Azure functions lokaal](./functions-develop-local.md)voor meer informatie.

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a> Functies bewerken in de Azure Portal
Met de functions-editor die in de Azure Portal is ingebouwd, kunt u uw code en uw *function.jsop* bestand direct inline bijwerken. Dit wordt alleen aanbevolen voor kleine wijzigingen of de controle van concepten best practice het gebruik van een lokaal ontwikkelings programma zoals VS code.

## <a name="parallel-execution"></a>Parallelle uitvoering
Als er meerdere activerings gebeurtenissen sneller optreden dan een functie-runtime met één thread, kan deze de functie meerdere keren tegelijk aanroepen.  Als een functie-app het [verbruiks hosting plan](functions-scale.md#how-the-consumption-and-premium-plans-work)gebruikt, kan de functie-app automatisch worden uitgeschaald.  Elk exemplaar van de functie-app, of de app wordt uitgevoerd op het verbruiks-hosting plan of een regulier [app service hosting abonnement](../app-service/overview-hosting-plans.md), kan gelijktijdig gelijktijdige functie aanroepen verwerken met behulp van meerdere threads.  Het maximum aantal gelijktijdige functie aanroepen in elk functie-app-exemplaar is afhankelijk van het type trigger dat wordt gebruikt, evenals de resources die worden gebruikt door andere functies in de functie-app.

## <a name="functions-runtime-versioning"></a>Functions runtime versie beheer

U kunt de versie van de functions-runtime configureren met de `FUNCTIONS_EXTENSION_VERSION` app-instelling. De waarde "~ 3" geeft bijvoorbeeld aan dat de functie-app 3. x als primaire versie zal gebruiken. Functie-apps worden bijgewerkt naar elke nieuwe secundaire versie zodra deze worden vrijgegeven. Zie [runtime-versies van Azure functions instellen](set-runtime-version.md)voor meer informatie over het weer geven van de exacte versie van uw functie-app.

## <a name="repositories"></a>Opslagplaatsen
De code voor Azure Functions is open source en opgeslagen in GitHub-opslag plaatsen:

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Azure Functions host](https://github.com/Azure/azure-functions-host/)
* [Azure Functions Portal](https://github.com/azure/azure-functions-ux)
* [Azure Functions sjablonen](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK-extensies](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Bindingen
Hier volgt een tabel met alle ondersteunde bindingen.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Ondervindt u problemen met de fouten die afkomstig zijn van de bindingen? Raadpleeg de documentatie over de [Azure functions bindings fout codes](functions-bindings-error-pages.md) .

## <a name="reporting-issues"></a>Rapportage problemen
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Volgende stappen
Zie de volgende resources voor meer informatie:

* [Azure Functions-triggers en -bindingen](functions-triggers-bindings.md)
* [Code-en test Azure Functions lokaal](./functions-develop-local.md)
* [Aanbevolen procedures voor Azure Functions](functions-best-practices.md)
* [Naslaginformatie over Azure Functions C# voor ontwikkelaars](functions-dotnet-class-library.md)
* [Azure Functions Node.js Naslag informatie voor ontwikkel aars](functions-reference-node.md)