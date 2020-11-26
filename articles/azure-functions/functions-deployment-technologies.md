---
title: Implementatie technologieën in Azure Functions
description: Meer informatie over de verschillende manieren waarop u code kunt implementeren voor Azure Functions.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 7a75408008a90a2c40553b1f6c5c196775a48e61
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168097"
---
# <a name="deployment-technologies-in-azure-functions"></a>Implementatie technologieën in Azure Functions

U kunt een aantal verschillende technologieën gebruiken om uw Azure Functions project code te implementeren in Azure. Dit artikel bevat een overzicht van de implementatie methoden die voor u beschikbaar zijn en aanbevelingen voor de beste manier om te gebruiken in verschillende scenario's. Het bevat ook een uitgebreide lijst met en belang rijke details over de onderliggende implementatie technologieën. 

## <a name="deployment-methods"></a>Implementatie methoden

De implementatie technologie die u gebruikt om code naar Azure te publiceren, wordt doorgaans bepaald door de manier waarop u uw app publiceert. De juiste implementatie methode wordt bepaald aan de hand van specifieke behoeften en het punt in de ontwikkelings cyclus. Tijdens het ontwikkelen en testen kunt u bijvoorbeeld rechtstreeks vanuit uw ontwikkel programma implementeren, zoals Visual Studio code. Wanneer uw app in productie is, is het waarschijnlijker dat u continu publiceert vanuit broncode beheer of met behulp van een geautomatiseerde publicatie pijplijn, die aanvullende validatie en tests bevat.  

In de volgende tabel worden de beschik bare implementatie methoden voor uw functie project beschreven.

| Implementatie &nbsp; type | Methoden | Beste voor... |
| -- | -- | -- |
| Op basis van hulpprogram ma's | &bull;&nbsp;[Visual &nbsp; Studio &nbsp; code &nbsp; Publish](functions-develop-vs-code.md#publish-to-azure)<br/>&bull;&nbsp;[Visual Studio publiceren](functions-develop-vs.md#publish-to-azure)<br/>&bull;&nbsp;[Basis Hulpprogramma's publiceren](functions-run-local.md#publish) | Implementaties tijdens de ontwikkeling en andere implementaties van AD-Hock. Implementaties worden lokaal beheerd door het hulp programma. | 
| App Service beheerd| &bull;&nbsp;[Implementatie &nbsp; centrum &nbsp; (CI/cd)](functions-continuous-deployment.md)<br/>&bull;&nbsp;[Container &nbsp; implementaties](functions-create-function-linux-custom-image.md#enable-continuous-deployment-to-azure) |  Continue implementatie (CI/CD) van broncode beheer of vanuit een container register. Implementaties worden beheerd door het App Service platform (kudu).|
| Externe pijp lijnen|&bull;&nbsp;[DevOps-pijp lijnen](functions-how-to-azure-devops.md)<br/>&bull;&nbsp;[GitHub-acties](functions-how-to-github-actions.md) | Productie-en DevOps-pijp lijnen die extra validatie, testen en andere acties omvatten, worden uitgevoerd als onderdeel van een geautomatiseerde implementatie. Implementaties worden beheerd door de pijp lijn. |

Hoewel de implementaties van specifieke functies gebruikmaken van de beste technologie op basis van hun context, zijn de meeste implementatie methoden gebaseerd op de [zip-implementatie](#zip-deploy).

## <a name="deployment-technology-availability"></a>Beschik baarheid implementatie technologie

Azure Functions ondersteunt lokale ontwikkeling en hosting in Windows en Linux op meerdere platforms. Momenteel zijn er drie hosting plannen beschikbaar:

+ [Meerverbruik](functions-scale.md#consumption-plan)
+ [Ultieme](functions-scale.md#premium-plan)
+ [Toegewezen (App Service)](functions-scale.md#app-service-plan)

Elk plan heeft verschillende gedragingen. Niet alle implementatie technologieën zijn beschikbaar voor elk van de Azure Functions. In het volgende diagram ziet u welke implementatie technologieën worden ondersteund voor elke combi natie van besturings systeem en hosting plan:

| Implementatie technologie | Windows-verbruik | Windows Premium | Windows toegewezen  | Linux-verbruik | Linux Premium | Speciaal voor Linux |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| Externe pakket-URL<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| Zip-implementatie |✔|✔|✔|✔|✔|✔|
| Docker-container | | | | |✔|✔|
| Web implementeren |✔|✔|✔| | | |
| Broncodebeheer |✔|✔|✔| |✔|✔|
| Lokale Git<sup>1</sup> |✔|✔|✔| |✔|✔|
| Cloud synchronisatie<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Portal bewerken |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup> implementatie technologie waarvoor [hand matige synchronisatie](#trigger-syncing)van de trigger is vereist.
<sup>2</sup> het bewerken van de portal is alleen ingeschakeld voor http-en timer triggers voor functies op Linux met behulp van Premium-en speciale abonnementen.

## <a name="key-concepts"></a>Belangrijkste concepten

Enkele belang rijke concepten zijn essentieel om te weten hoe implementaties werken in Azure Functions.

### <a name="trigger-syncing"></a>Synchronisatie activeren

Wanneer u een van de triggers wijzigt, moeten de functies de infra structuur op de hoogte zijn van de wijzigingen. Synchronisatie gebeurt automatisch voor veel implementatie technologieën. In sommige gevallen moet u de triggers echter hand matig synchroniseren. Wanneer u uw updates implementeert door te verwijzen naar een URL voor een extern pakket, lokale Git, Cloud synchronisatie of FTP, moet u de triggers hand matig synchroniseren. U kunt triggers op een van de volgende drie manieren synchroniseren:

* Start uw functie-app in de Azure Portal
* Verzend een HTTP POST-aanvraag naar `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` het gebruik van de [hoofd sleutel](functions-bindings-http-webhook-trigger.md#authorization-keys).
* Verzend een HTTP POST-aanvraag naar `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01` . Vervang de tijdelijke aanduidingen door uw abonnements-ID, naam van de resource groep en de naam van uw functie-app.

### <a name="remote-build"></a>Externe build

Azure Functions kunt automatisch builds uitvoeren op de code die na de implementaties van de post wordt ontvangen. Deze builds werken enigszins anders, afhankelijk van of uw app op Windows of Linux wordt uitgevoerd. Externe builds worden niet uitgevoerd wanneer een app eerder is ingesteld om te worden uitgevoerd in de modus [uitvoeren vanuit pakket](run-functions-from-deployment-package.md) . Ga naar [zip-implementatie](#zip-deploy)voor meer informatie over het gebruik van extern bouwen.

> [!NOTE]
> Als u problemen ondervindt met het maken van een externe build, kan het zijn dat de app is gemaakt voordat de functie beschikbaar werd gemaakt (1 augustus 2019). Maak een nieuwe functie-app of voer uit `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` om uw functie-app bij te werken. Het kan twee pogingen om deze opdracht uit te voeren.

#### <a name="remote-build-on-windows"></a>Externe Build op Windows

Alle functie-apps die worden uitgevoerd op Windows hebben een kleine beheer-app, de SCM-(of [kudu](https://github.com/projectkudu/kudu))-site. Deze site handelt veel van de implementatie en bouw logica voor Azure Functions.

Wanneer een app wordt geïmplementeerd in Windows, worden taalspecifieke opdrachten (zoals `dotnet restore` C#) of `npm install` (Java script) uitgevoerd.

#### <a name="remote-build-on-linux"></a>Externe Build op Linux

Om externe Build op Linux in te scha kelen, moet u de volgende [Toepassings instellingen](functions-how-to-use-azure-function-app-settings.md#settings) instellen:

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Standaard worden zowel [Azure functions core tools](functions-run-local.md) als de [Azure functions-extensie voor Visual Studio code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) externe builds uitgevoerd bij het implementeren naar Linux. Daarom maken beide hulpprogram ma's deze instellingen automatisch voor u in Azure.

Wanneer apps op afstand zijn gebouwd op Linux, worden ze [uitgevoerd vanuit het implementatie pakket](run-functions-from-deployment-package.md).

##### <a name="consumption-plan"></a>Verbruiksabonnement

Linux-functie-apps die worden uitgevoerd in het verbruiks abonnement, hebben geen SCM/kudu-site, waarmee de implementatie opties worden beperkt. Functie-apps in Linux die worden uitgevoerd in het verbruiks abonnement, bieden echter ondersteuning voor externe builds.

##### <a name="dedicated-and-premium-plans"></a>Speciale en Premium-abonnementen

Functie-apps die worden uitgevoerd op Linux in het [speciale (app service)-abonnement](functions-scale.md#app-service-plan) en het [Premium-abonnement](functions-scale.md#premium-plan) hebben ook een beperkte SCM/kudu-site.

## <a name="deployment-technology-details"></a>Details implementatie technologie

De volgende implementatie methoden zijn beschikbaar in Azure Functions.

### <a name="external-package-url"></a>URL van extern pakket

U kunt een externe pakket-URL gebruiken om te verwijzen naar een extern pakket bestand (. zip) dat uw functie-app bevat. Het bestand wordt gedownload van de beschik bare URL en de app wordt uitgevoerd in de pakket modus in [uitvoering](run-functions-from-deployment-package.md) .

>__Hoe gebruikt u dit:__ Toevoegen `WEBSITE_RUN_FROM_PACKAGE` aan de toepassings instellingen. De waarde van deze instelling moet een URL zijn (de locatie van het specifieke pakket bestand dat u wilt uitvoeren). U kunt instellingen toevoegen [in de portal](functions-how-to-use-azure-function-app-settings.md#settings) of met [behulp van de Azure cli](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).
>
>Als u Azure Blob Storage gebruikt, gebruikt u een persoonlijke container met een [Shared Access Signature (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) om functies toegang te geven tot het pakket. Telkens wanneer de toepassing opnieuw wordt gestart, wordt een kopie van de inhoud opgehaald. Uw verwijzing moet geldig zijn voor de levens duur van de toepassing.

>__Wanneer u deze gebruikt:__ De URL van het externe pakket is de enige ondersteunde implementatie methode voor Azure Functions die in Linux in het verbruiks abonnement wordt uitgevoerd, als de gebruiker niet wilt dat er een [externe build wordt gemaakt](#remote-build) . Wanneer u het pakket bestand bijwerkt waarnaar wordt verwezen door een functie-app, moet u de [Triggers hand matig synchroniseren](#trigger-syncing) om Azure te laten weten dat uw toepassing is gewijzigd.

### <a name="zip-deploy"></a>Zip-implementatie

Gebruik Zip-implementatie om een zip-bestand met uw functie-app naar Azure te pushen. Desgewenst kunt u uw app zo instellen dat deze [vanuit het pakket wordt uitgevoerd](run-functions-from-deployment-package.md), of opgeven dat er een [externe build wordt gemaakt](#remote-build) .

>__Hoe gebruikt u dit:__ Implementeren met behulp van uw favoriete client hulpprogramma: [Visual Studio code](functions-develop-vs-code.md#publish-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure)of vanaf de opdracht regel met behulp van de [Azure functions core tools](functions-run-local.md#project-file-deployment). Deze hulpprogram ma's gebruiken standaard de zip-implementatie en [worden uitgevoerd vanuit het pakket](run-functions-from-deployment-package.md). De kern Hulpprogramma's en de Visual Studio code-extensie maken beide gebruik van [externe build](#remote-build) bij het implementeren naar Linux. Als u een zip-bestand hand matig wilt implementeren in uw functie-app, volgt u de instructies in [Deploy vanuit een zip-bestand of-URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

>Wanneer u implementeert met behulp van zip-implementatie, kunt u instellen dat uw app [vanuit het pakket wordt uitgevoerd](run-functions-from-deployment-package.md). Als u uit het pakket wilt uitvoeren, stelt `WEBSITE_RUN_FROM_PACKAGE` u de waarde voor de toepassings instelling in op `1` . U kunt het beste een zip-implementatie uitvoeren. Het levert snellere laad tijden voor uw toepassingen en is de standaard waarde voor VS code, Visual Studio en de Azure CLI.

>__Wanneer u deze gebruikt:__ Zip-implementatie is de aanbevolen implementatie technologie voor Azure Functions.

### <a name="docker-container"></a>Docker-container

U kunt een Linux-container installatie kopie implementeren die uw functie-app bevat.

>__Hoe gebruikt u dit:__ Maak een Linux-functie-app in het Premium-of dedicated-abonnement en geef op van welke container installatie kopie moet worden uitgevoerd. U kunt dit op twee manieren doen:
>
>* Maak een Linux-functie-app in een Azure App Service plan in het Azure Portal. Voor **publiceren** selecteert u **docker-installatie kopie** en configureert u vervolgens de container. Geef de locatie op waar de installatie kopie wordt gehost.
>* Een Linux-functie-app maken in een App Service-abonnement met behulp van de Azure CLI. Zie [een functie maken in Linux met behulp van een aangepaste installatie kopie](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function)voor meer informatie.
>
>Als u wilt implementeren in een bestaande app met behulp van een aangepaste container, gebruikt u de opdracht in [Azure functions core tools](functions-run-local.md) [`func deploy`](functions-run-local.md#publish) .

>__Wanneer u deze gebruikt:__ Gebruik de optie docker-container wanneer u meer controle nodig hebt over de Linux-omgeving waarin uw functie-app wordt uitgevoerd. Dit implementatie mechanisme is alleen beschikbaar voor functies die worden uitgevoerd op Linux.

### <a name="web-deploy-msdeploy"></a>Web Deploy (MSDeploy)

Web Deploy-pakketten en implementeert uw Windows-toepassingen op elke IIS-server, met inbegrip van uw functie-apps die worden uitgevoerd in Windows in Azure.

>__Hoe gebruikt u dit:__ Gebruik [Visual Studio-hulpprogram ma's voor Azure functions](functions-create-your-first-function-visual-studio.md). Schakel het selectie vakje **uitvoeren vanaf pakket bestand (aanbevolen)** uit.
>
>U kunt ook [Web Deploy 3,6](https://www.iis.net/downloads/microsoft/web-deploy) downloaden en `MSDeploy.exe` direct bellen.

>__Wanneer u deze gebruikt:__ Web Deploy wordt ondersteund en heeft geen problemen, maar het voorkeurs mechanisme is [zip implementeren met uitvoeren vanuit pakket ingeschakeld](#zip-deploy). Raadpleeg voor meer informatie de [Visual Studio Development Guide](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Broncodebeheer

Gebruik broncode beheer om uw functie-app te verbinden met een Git-opslag plaats. Een update van de code in die opslag plaats activeert de implementatie. Zie de [kudu-wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)voor meer informatie.

>__Hoe gebruikt u dit:__ Gebruik het implementatie centrum in het gedeelte functies van de portal om de publicatie vanuit broncode beheer in te stellen. Zie [continue implementatie voor Azure functions](functions-continuous-deployment.md)voor meer informatie.

>__Wanneer u deze gebruikt:__ Het gebruik van broncode beheer is de best practice voor teams die samen werken aan hun functie-apps. Broncode beheer is een goede implementatie optie die complexere implementatie pijplijnen mogelijk maakt.

### <a name="local-git"></a>Lokale Git

U kunt lokale Git gebruiken om code te pushen van uw lokale computer naar Azure Functions met behulp van Git.

>__Hoe gebruikt u dit:__ Volg de instructies in [lokale Git-implementatie om te Azure app service](../app-service/deploy-local-git.md).

>__Wanneer u deze gebruikt:__ Over het algemeen is het raadzaam een andere implementatie methode te gebruiken. Wanneer u publiceert vanuit lokale Git, moet u [Triggers hand matig synchroniseren](#trigger-syncing).

### <a name="cloud-sync"></a>Cloud synchronisatie

Gebruik Cloud synchronisatie om uw inhoud te synchroniseren vanuit Dropbox en OneDrive naar Azure Functions.

>__Hoe gebruikt u dit:__ Volg de instructies in [inhoud synchroniseren vanuit een map](../app-service/deploy-content-sync.md)in de Cloud.

>__Wanneer u deze gebruikt:__ Over het algemeen raden wij andere implementatie methoden aan. Wanneer u publiceert met behulp van Cloud synchronisatie, moet u [Triggers hand matig synchroniseren](#trigger-syncing).

### <a name="ftp"></a>FTP

U kunt FTP gebruiken om bestanden rechtstreeks naar Azure Functions te verzenden.

>__Hoe gebruikt u dit:__ Volg de instructies in [inhoud implementeren met behulp van FTP/s](../app-service/deploy-ftp.md).

>__Wanneer u deze gebruikt:__ Over het algemeen raden wij andere implementatie methoden aan. Wanneer u publiceert met FTP, moet u [Triggers hand matig synchroniseren](#trigger-syncing).

### <a name="portal-editing"></a>Portal bewerken

In de portal-editor kunt u de bestanden in uw functie-app rechtstreeks bewerken (in principe elke keer dat u uw wijzigingen opslaat).

>__Hoe gebruikt u dit:__ Als u uw functies in het Azure Portal wilt kunnen bewerken, moet u [uw functies in de portal hebben gemaakt](functions-create-first-azure-function.md). Als u één bron van waarheid wilt behouden, is het gebruik van een andere implementatie methode de functie alleen-lezen en voor komt u dat de portal verder kan worden bewerkt. Als u wilt terugkeren naar een status waarin u uw bestanden kunt bewerken in de Azure Portal, kunt u de bewerkings modus hand matig weer inschakelen `Read/Write` en alle implementatie-gerelateerde toepassings instellingen verwijderen (zoals `WEBSITE_RUN_FROM_PACKAGE` ).

>__Wanneer u deze gebruikt:__ De portal is een goede manier om aan de slag te gaan met Azure Functions. Voor een intensere ontwikkel werkzaamheden wordt u aangeraden een van de volgende client hulpprogramma's te gebruiken:
>
>* [Visual Studio Code](./create-first-function-vs-code-csharp.md)
>* [Azure Functions Core Tools (opdracht regel)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

In de volgende tabel ziet u de besturings systemen en talen die ondersteuning bieden voor het bewerken van portals:

| Taal | Windows-verbruik | Windows Premium | Windows toegewezen | Linux-verbruik | Linux Premium | Speciaal voor Linux |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| C# Script |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (Preview) | | | | | | |
| PowerShell (Preview) |✔|✔|✔| | | |
| Type script (Node.js) | | | | | | |

<sup>*</sup> Het bewerken van de portal is alleen ingeschakeld voor HTTP-en timer triggers voor functies op Linux met behulp van Premium-en speciale abonnementen.

## <a name="deployment-behaviors"></a>Implementatie gedrag

Wanneer u een implementatie uitvoert, zijn alle bestaande uitvoeringen toegestaan of zijn er een time-out, waarna de nieuwe code wordt geladen om te beginnen met het verwerken van aanvragen.

Als u meer controle over deze overgang wilt, moet u implementatie sleuven gebruiken.

## <a name="deployment-slots"></a>Implementatiesites

Wanneer u de functie-app in azure implementeert, kunt u implementeren in een afzonderlijke implementatie site in plaats van rechtstreeks naar productie. Zie de documentatie over [Azure functions implementatie sleuven](functions-deployment-slots.md) voor meer informatie over implementatie sites.

## <a name="next-steps"></a>Volgende stappen

Lees deze artikelen voor meer informatie over het implementeren van uw functie-apps:

+ [Doorlopende implementatie voor Azure Functions](functions-continuous-deployment.md)
+ [Continue levering met behulp van Azure DevOps](functions-how-to-azure-devops.md)
+ [Zip-implementaties voor Azure Functions](deployment-zip-push.md)
+ [Uw Azure Functions uitvoeren vanuit een pakket bestand](run-functions-from-deployment-package.md)
+ [De implementatie van resources voor uw functie-app in Azure Functions automatiseren](functions-infrastructure-as-code.md)