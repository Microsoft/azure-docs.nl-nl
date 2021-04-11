---
title: Hulp bij het ontwikkelen van Azure Functions
description: Meer informatie over de Azure Functions-concepten en-technieken die u nodig hebt voor het ontwikkelen van functies in azure, in alle programmeer talen en bindingen.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: 7030ca1c1950f7c06580ce7417a4429fbe330c4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102614816"
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

| Eigenschap    | Waarden | Type | Opmerkingen|
|---|---|---|---|
| type  | Naam van de binding.<br><br>Bijvoorbeeld `queueTrigger`. | tekenreeks | |
| richting | `in`, `out`  | tekenreeks | Geeft aan of de binding is voor het ontvangen van gegevens van de functie of het verzenden van gegevens van de functie. |
| naam | Functie-id.<br><br>Bijvoorbeeld `myQueue`. | tekenreeks | De naam die wordt gebruikt voor de afhankelijke gegevens in de functie. Voor C# is dit een argument naam; voor Java script is het de sleutel in een lijst met sleutel/waarden. |

## <a name="function-app"></a>Functie-app
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
Als er meerdere activerings gebeurtenissen sneller optreden dan een functie-runtime met één thread, kan deze de functie meerdere keren tegelijk aanroepen.  Als een functie-app het [verbruiks hosting plan](event-driven-scaling.md)gebruikt, kan de functie-app automatisch worden uitgeschaald.  Elk exemplaar van de functie-app, of de app wordt uitgevoerd op het verbruiks-hosting plan of een regulier [app service hosting abonnement](../app-service/overview-hosting-plans.md), kan gelijktijdig gelijktijdige functie aanroepen verwerken met behulp van meerdere threads.  Het maximum aantal gelijktijdige functie aanroepen in elk functie-app-exemplaar is afhankelijk van het type trigger dat wordt gebruikt, evenals de resources die worden gebruikt door andere functies in de functie-app.

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


## <a name="connections"></a>Verbindingen

Uw functie project verwijst naar de verbindings gegevens op naam van de configuratie provider. De verbindings gegevens worden niet rechtstreeks geaccepteerd, zodat deze kunnen worden gewijzigd tussen omgevingen. Een trigger definitie kan bijvoorbeeld een `connection` eigenschap bevatten. Dit kan verwijzen naar een connection string, maar u kunt de connection string niet rechtstreeks instellen in een `function.json` . In plaats daarvan stelt u `connection` de naam in van een omgevings variabele die de Connection String bevat.

De standaard configuratie provider maakt gebruik van omgevings variabelen. Deze kunnen worden ingesteld door [Toepassings instellingen](./functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) wanneer ze worden uitgevoerd in de Azure functions-service, of vanuit het [lokale instellingen bestand](functions-run-local.md#local-settings-file) bij het ontwikkelen van lokaal.

### <a name="connection-values"></a>Verbindings waarden

Wanneer de naam van de verbinding wordt omgezet in een enkele exacte waarde, identificeert de runtime de waarde als een _Connection String_, die meestal een geheim bevat. De details van een connection string worden gedefinieerd door de service waarmee u verbinding wilt maken.

Een verbindings naam kan echter ook verwijzen naar een verzameling van meerdere configuratie-items. Omgevings variabelen kunnen worden behandeld als een verzameling met behulp van een gedeeld voor voegsel dat eindigt op dubbele onderstrepings tekens `__` . U kunt vervolgens naar de groep verwijzen door de naam van de verbinding in te stellen op dit voor voegsel.

De `connection` eigenschap voor de definitie van een Azure Blob-trigger kan bijvoorbeeld zijn `Storage1` . Zolang er geen teken reeks waarde is geconfigureerd met `Storage1` de naam, `Storage1__serviceUri` zou worden gebruikt voor de `serviceUri` eigenschap van de verbinding. De verbindings eigenschappen verschillen voor elke service. Raadpleeg de documentatie voor de uitbrei ding die gebruikmaakt van de verbinding.

### <a name="configure-an-identity-based-connection"></a>Een verbinding op basis van een identiteit configureren

Sommige verbindingen in Azure Functions zijn geconfigureerd voor het gebruik van een identiteit in plaats van een geheim. Ondersteuning is afhankelijk van de uitbrei ding via de verbinding. In sommige gevallen is er nog steeds een connection string vereist in functions, zelfs als de service waarmee u verbinding maakt, verbindingen op basis van identiteiten ondersteunt.

> [!IMPORTANT]
> Zelfs als een bindings extensie verbindingen op basis van een identiteit ondersteunt, wordt die configuratie mogelijk nog niet ondersteund in het verbruiks abonnement. Zie de onderstaande ondersteunings tabel.

Verbindingen op basis van een identiteit worden ondersteund door de volgende trigger-en bindings uitbreidingen:

| Extensie naam | Versie van de extensie                                                                                     | Ondersteund in het verbruiks abonnement |
|----------------|-------------------------------------------------------------------------------------------------------|---------------------------------------|
| Azure Blob     | [Versie 5.0.0-beta1 of hoger](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)  | No                                    |
| Azure Queue    | [Versie 5.0.0-beta1 of hoger](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) | No                                    |
| Azure Event Hubs    | [Versie 5.0.0-beta1 of hoger](./functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher) | No                                    |

> [!NOTE]
> Ondersteuning voor verbindingen op basis van een identiteit is nog niet beschikbaar voor opslag verbindingen die worden gebruikt door de functions-runtime voor kern gedrag. Dit betekent dat de `AzureWebJobsStorage` instelling een Connection String moet zijn.

#### <a name="connection-properties"></a>Verbindingseigenschappen

Een verbinding op basis van een identiteit voor een Azure-service accepteert de volgende eigenschappen:

| Eigenschap    | Vereist voor uitbrei dingen | Omgevingsvariabele | Description |
|---|---|---|---|
| Service-URI | Azure-Blob, Azure-wachtrij | `<CONNECTION_NAME_PREFIX>__serviceUri` |  De gegevensfeed-URI van de service waarmee u verbinding maakt. |
| Volledig gekwalificeerde naam ruimte | Event Hubs | `<CONNECTION_NAME_PREFIX>__fullyQualifiedNamespace` | De volledig gekwalificeerde Event hub-naam ruimte. |

Er kunnen extra opties worden ondersteund voor een bepaald verbindings type. Raadpleeg de documentatie voor het onderdeel dat de verbinding maakt.

Bij het hosten van de Azure Functions-service gebruiken verbindingen op basis van een identiteit een [beheerde identiteit](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json). De door het systeem toegewezen identiteit wordt standaard gebruikt. Bij het uitvoeren in andere contexten, zoals lokale ontwikkeling, wordt de identiteit van uw ontwikkelaar gebruikt, hoewel dit kan worden aangepast met alternatieve verbindings parameters.

##### <a name="local-development"></a>Lokale ontwikkeling

Wanneer lokaal wordt uitgevoerd, geeft de bovenstaande configuratie aan dat de runtime uw lokale identiteit van de ontwikkelaar moet gebruiken. Met de verbinding wordt geprobeerd een token op te halen uit de volgende locaties, in volg orde:

- Een lokale cache die wordt gedeeld tussen micro soft-toepassingen
- De huidige gebruikers context in Visual Studio
- De huidige gebruikers context in Visual Studio code
- De huidige gebruikers context in de Azure CLI

Als geen van deze opties is geslaagd, treedt er een fout op.

In sommige gevallen wilt u het gebruik van een andere identiteit opgeven. U kunt configuratie-eigenschappen toevoegen voor de verbinding die verwijst naar de alternatieve identiteit.

> [!NOTE]
> De volgende configuratie opties worden niet ondersteund wanneer deze worden gehost in de Azure Functions-service.

Als u verbinding wilt maken met behulp van een Azure Active Directory Service-Principal met een client-ID en een geheim, definieert u de verbinding met de volgende vereiste eigenschappen naast de bovenstaande [verbindings eigenschappen](#connection-properties) :

| Eigenschap    | Omgevingsvariabele | Description |
|---|---|---|
| Tenant-id | `<CONNECTION_NAME_PREFIX>__tenantId` | De ID van de Azure Active Directory Tenant (map). |
| Client-id | `<CONNECTION_NAME_PREFIX>__clientId` |  De client-ID (toepassing) van een app-registratie in de Tenant. |
| Clientgeheim | `<CONNECTION_NAME_PREFIX>__clientSecret` | Een client geheim dat is gegenereerd voor de app-registratie. |

Voor beeld van `local.settings.json` eigenschappen die zijn vereist voor identiteits verbinding met Azure Blob: 
```json
{
  "IsEncrypted": false,
  "Values": {
    "<CONNECTION_NAME_PREFIX>__serviceUri": "<serviceUri>",
    "<CONNECTION_NAME_PREFIX>__tenantId": "<tenantId>",
    "<CONNECTION_NAME_PREFIX>__clientId": "<clientId>",
    "<CONNECTION_NAME_PREFIX>__clientSecret": "<clientSecret>"
  }
}
```

#### <a name="grant-permission-to-the-identity"></a>Toestemming geven voor de identiteit

Welke identiteit wordt gebruikt, moet machtigingen hebben om de bedoelde acties uit te voeren. Dit gebeurt doorgaans door een rol toe te wijzen in azure RBAC of de identiteit op te geven in een toegangs beleid, afhankelijk van de service waarmee u verbinding maakt. Raadpleeg de documentatie voor elke service op welke machtigingen nodig zijn en hoe ze kunnen worden ingesteld.

> [!IMPORTANT]
> Sommige machtigingen kunnen worden weer gegeven door de service die niet nodig zijn voor alle contexten. Indien mogelijk moet u zich houden aan het **principe van minimale bevoegdheden**, waarbij u de identiteit alleen vereiste bevoegdheden verleent. Als de app bijvoorbeeld alleen uit een BLOB moet lezen, gebruikt u de rol [Storage BLOB data Reader](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) als de eigenaar van de [opslag-BLOB-gegevens](../role-based-access-control/built-in-roles.md#storage-blob-data-owner) overmatige machtigingen voor een lees bewerking bevat.


## <a name="reporting-issues"></a>Rapportage problemen
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Volgende stappen
Zie de volgende resources voor meer informatie:

* [Azure Functions-triggers en -bindingen](functions-triggers-bindings.md)
* [Code-en test Azure Functions lokaal](./functions-develop-local.md)
* [Aanbevolen procedures voor Azure Functions](functions-best-practices.md)
* [Naslaginformatie over Azure Functions C# voor ontwikkelaars](functions-dotnet-class-library.md)
* [Azure Functions Node.js Naslag informatie voor ontwikkel aars](functions-reference-node.md)
