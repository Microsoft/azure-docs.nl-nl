---
title: Implementatietechnologieën in Azure Functions
description: Meer informatie over de verschillende manieren waarop u code kunt implementeren Azure Functions.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: ca81067fa60836d77c4d8af121ebf415c772a1d7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789209"
---
# <a name="deployment-technologies-in-azure-functions"></a>Implementatietechnologieën in Azure Functions

U kunt een aantal verschillende technologieën gebruiken om uw projectcode Azure Functions azure te implementeren. Dit artikel bevat een overzicht van de implementatiemethoden die voor u beschikbaar zijn en aanbevelingen voor de beste methode voor gebruik in verschillende scenario's. Het bevat ook een uitgebreide lijst met en belangrijke details over de onderliggende implementatietechnologieën. 

## <a name="deployment-methods"></a>Implementatiemethoden

De implementatietechnologie die u gebruikt om code naar Azure te publiceren, wordt meestal bepaald door de manier waarop u uw app publiceert. De juiste implementatiemethode wordt bepaald door specifieke behoeften en het punt in de ontwikkelingscyclus. Tijdens het ontwikkelen en testen kunt u bijvoorbeeld rechtstreeks implementeren vanuit uw ontwikkelprogramma, zoals Visual Studio Code. Wanneer uw app in productie is, is het waarschijnlijker dat u continu publiceert vanuit broncodebeheer of met behulp van een geautomatiseerde publicatiepijplijn, die aanvullende validatie en tests omvat.  

In de volgende tabel worden de beschikbare implementatiemethoden voor uw function-project beschreven.

| &nbsp;Implementatietype | Methoden | Het beste voor... |
| -- | -- | -- |
| Op basis van hulpprogramma's | &bull;&nbsp;[Visual &nbsp; Studio &nbsp; Code &nbsp; publiceren](functions-develop-vs-code.md#publish-to-azure)<br/>&bull;&nbsp;[Visual Studio publiceren](functions-develop-vs.md#publish-to-azure)<br/>&bull;&nbsp;[Core Tools publiceren](functions-run-local.md#publish) | Implementaties tijdens ontwikkeling en andere ad-hoc implementaties. Implementaties worden lokaal beheerd door de hulpprogramma's. | 
| App Service beheerd| &bull;&nbsp;[Implementatiecentrum &nbsp; &nbsp; (CI/CD)](functions-continuous-deployment.md)<br/>&bull;&nbsp;[&nbsp;Containerimplementaties](functions-create-function-linux-custom-image.md#enable-continuous-deployment-to-azure) |  Continue implementatie (CI/CD) vanuit broncodebeheer of vanuit een containerregister. Implementaties worden beheerd door het App Service platform (Kudu).|
| Externe pijplijnen|&bull;&nbsp;[Azure Pipelines](functions-how-to-azure-devops.md)<br/>&bull;&nbsp;[GitHub-acties](functions-how-to-github-actions.md) | Productie- en DevOps-pijplijnen die aanvullende validatie, tests en andere acties bevatten, worden uitgevoerd als onderdeel van een geautomatiseerde implementatie. Implementaties worden beheerd door de pijplijn. |

Hoewel voor specifieke Functions-implementaties de beste technologie wordt gebruikt op basis van hun context, zijn de meeste implementatiemethoden gebaseerd op [zip-implementatie.](#zip-deploy)

## <a name="deployment-technology-availability"></a>Beschikbaarheid van implementatietechnologie

Azure Functions ondersteunt platformoverschrijdende lokale ontwikkeling en hosting in Windows en Linux. Er zijn momenteel drie hostingplannen beschikbaar:

+ [Verbruik](consumption-plan.md)
+ [Premium](functions-premium-plan.md)
+ [Toegewezen (App Service)](dedicated-plan.md)

Elk plan heeft ander gedrag. Niet alle implementatietechnologieën zijn beschikbaar voor elke smaak Azure Functions. In de volgende grafiek ziet u welke implementatietechnologieën worden ondersteund voor elke combinatie van besturingssysteem en hostingplan:

| Implementatietechnologie | Windows-verbruik | Windows Premium | Windows Dedicated  | Linux-verbruik | Linux Premium | Linux Dedicated |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| URL van extern<sup>pakket 1</sup> |✔|✔|✔|✔|✔|✔|
| Zip-implementatie |✔|✔|✔|✔|✔|✔|
| Docker-container | | | | |✔|✔|
| Web implementeren |✔|✔|✔| | | |
| Broncodebeheer |✔|✔|✔| |✔|✔|
| Lokale Git<sup>1</sup> |✔|✔|✔| |✔|✔|
| Cloudsynchronisatie<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Portal bewerken |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup> Implementatietechnologie die handmatige [triggersynchronisatie vereist.](#trigger-syncing)
<sup>2</sup> Portalbewerking is alleen ingeschakeld voor HTTP- en timertriggers voor functies in Linux met behulp van Premium- en Dedicated-abonnementen.

## <a name="key-concepts"></a>Belangrijkste concepten

Enkele belangrijke concepten zijn essentieel om te begrijpen hoe implementaties werken in Azure Functions.

### <a name="trigger-syncing"></a>Synchronisatie activeren

Wanneer u een van uw triggers wijzigt, moet de Functions-infrastructuur op de hoogte zijn van de wijzigingen. Synchronisatie vindt automatisch plaats voor veel implementatietechnologieën. In sommige gevallen moet u uw triggers echter handmatig synchroniseren. Wanneer u uw updates implementeert door te verwijzen naar een externe pakket-URL, lokale Git, cloudsynchronisatie of FTP, moet u uw triggers handmatig synchroniseren. U kunt triggers op een van de volgende drie manieren synchroniseren:

* Start de functie-app opnieuw op in Azure Portal
* Verzend een HTTP POST-aanvraag naar met `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` behulp van [de hoofdsleutel](functions-bindings-http-webhook-trigger.md#authorization-keys).
* Verzend een HTTP POST-aanvraag naar `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01` . Vervang de tijdelijke aanduidingen door uw abonnements-id, resourcegroepnaam en de naam van uw functie-app.

### <a name="remote-build"></a>Externe build

Azure Functions kunnen automatisch builds uitvoeren op de code die wordt ontvangen na zip-implementaties. Deze builds gedragen zich iets anders, afhankelijk van of uw app wordt uitgevoerd in Windows of Linux. Externe builds worden niet uitgevoerd wanneer eerder is ingesteld dat een app wordt uitgevoerd in [de modus Uitvoeren vanuit](run-functions-from-deployment-package.md) pakket. Als u wilt weten hoe u een externe build gebruikt, gaat u naar [zip-implementatie.](#zip-deploy)

> [!NOTE]
> Als u problemen hebt met extern bouwen, kan dit zijn omdat uw app is gemaakt voordat de functie beschikbaar werd gesteld (1 augustus 2019). Maak een nieuwe functie-app of gebruik deze om `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` uw functie-app bij te werken. Het kan twee pogingen duren om deze opdracht uit te voeren.

#### <a name="remote-build-on-windows"></a>Extern bouwen in Windows

Alle functie-apps die in Windows worden uitgevoerd, hebben een kleine beheer-app, de site SCM (of [Kudu).](https://github.com/projectkudu/kudu) Deze site verwerkt een groot deel van de implementatie- en buildlogica voor Azure Functions.

Wanneer een app wordt geïmplementeerd in Windows, worden taalspecifieke opdrachten zoals `dotnet restore` (C#) of `npm install` (JavaScript) uitgevoerd.

#### <a name="remote-build-on-linux"></a>Extern bouwen op Linux

Als u extern bouwen op Linux wilt inschakelen, moeten de volgende [toepassingsinstellingen](functions-how-to-use-azure-function-app-settings.md#settings) worden ingesteld:

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Standaard voeren zowel [Azure Functions Core Tools](functions-run-local.md) als [Azure Functions-extensie](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) voor Visual Studio Code externe builds uit bij de implementatie in Linux. Als gevolg daarvan maken beide hulpprogramma's deze instellingen automatisch voor u in Azure.

Wanneer apps extern zijn gebouwd op Linux, worden [ze uitgevoerd vanuit het implementatiepakket](run-functions-from-deployment-package.md).

##### <a name="consumption-plan"></a>Verbruiksabonnement

Linux-functie-apps die worden uitgevoerd in het verbruiksplan hebben geen SCM/Kudu-site, waardoor de implementatieopties worden beperkt. Functie-apps op Linux die worden uitgevoerd in het verbruiksplan bieden echter wel ondersteuning voor externe builds.

##### <a name="dedicated-and-premium-plans"></a>Dedicated- en Premium-abonnementen

Functie-apps die worden uitgevoerd op Linux in het [Dedicated-abonnement (App Service)](dedicated-plan.md) en het [Premium-abonnement](functions-premium-plan.md) hebben ook een beperkte SCM/Kudu-site.

## <a name="deployment-technology-details"></a>Details van implementatietechnologie

De volgende implementatiemethoden zijn beschikbaar in Azure Functions.

### <a name="external-package-url"></a>URL van extern pakket

U kunt een externe pakket-URL gebruiken om te verwijzen naar een extern pakketbestand (.zip) dat uw functie-app bevat. Het bestand wordt gedownload via de opgegeven URL en de app wordt uitgevoerd in [de modus Uitvoeren vanuit](run-functions-from-deployment-package.md) pakket.

>__Het volgende gebruiken:__ Voeg [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) toe aan uw toepassingsinstellingen. De waarde van deze instelling moet een URL zijn (de locatie van het specifieke pakketbestand dat u wilt uitvoeren). U kunt instellingen toevoegen [in de portal](functions-how-to-use-azure-function-app-settings.md#settings) of met behulp van de Azure [CLI.](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set)
>
>Als u Azure Blob Storage gebruikt, gebruikt u een persoonlijke container met een [Shared Access Signature (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) om Functions toegang te geven tot het pakket. Elke keer dat de toepassing opnieuw wordt gestart, wordt er een kopie van de inhoud opgehaald. Uw verwijzing moet geldig zijn voor de levensduur van de toepassing.

>__Wanneer u deze gebruikt:__ De URL van het externe pakket is de enige ondersteunde implementatiemethode voor Azure Functions die wordt uitgevoerd op Linux in het verbruiksplan, als de gebruiker niet wil dat er een externe [build](#remote-build) wordt uitgevoerd. Wanneer u het pakketbestand bijwerkt waarnaar een functie-app verwijst, moet u [triggers](#trigger-syncing) handmatig synchroniseren om Azure te laten weten dat uw toepassing is gewijzigd.

### <a name="zip-deploy"></a>Zip-implementatie

Gebruik zip deploy om een ZIP-bestand met uw functie-app naar Azure te pushen. U kunt uw app desgewenst zo instellen dat deze wordt uitgevoerd [vanuit een pakket](run-functions-from-deployment-package.md)of opgeven dat er een externe build [plaatsvindt.](#remote-build)

>__Het volgende gebruiken:__ Implementeer met behulp van uw favoriete clienthulpprogramma: [Visual Studio Code](functions-develop-vs-code.md#publish-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure)of vanaf de opdrachtregel met behulp van [Azure Functions Core Tools](functions-run-local.md#project-file-deployment). Deze hulpprogramma's maken standaard gebruik van zip-implementatie en [worden uitgevoerd vanuit pakket](run-functions-from-deployment-package.md). Core Tools en de Visual Studio Code-extensie maken beide [extern bouwen](#remote-build) mogelijk bij het implementeren in Linux. Als u handmatig een ZIP-bestand wilt implementeren in uw functie-app, volgt u de instructies in Implementeren vanuit een [ZIP-bestand of URL.](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)

>Wanneer u implementeert met zip-implementatie, kunt u instellen dat uw app [wordt uitgevoerd vanuit pakket](run-functions-from-deployment-package.md). Als u wilt uitvoeren vanuit een pakket, stelt u de waarde [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) van de toepassingsinstelling in op `1` . U wordt aangeraden zip-implementatie uit te gaan. Het levert snellere laadtijden voor uw toepassingen op. Dit is de standaardinstelling voor VS Code, Visual Studio en de Azure CLI.

>__Wanneer u deze gebruikt:__ Zip-implementatie is de aanbevolen implementatietechnologie voor Azure Functions.

### <a name="docker-container"></a>Docker-container

U kunt een Linux-containerafbeelding implementeren die uw functie-app bevat.

>__Het volgende gebruiken:__ Maak een Linux-functie-app in het Premium- of Dedicated-abonnement en geef op vanaf welke containerafbeelding moet worden uitgevoerd. U kunt dit op twee manieren doen:
>
>* Maak een Linux-functie-app op een Azure App Service-abonnement in Azure Portal. Bij **Publiceren** selecteert **u Docker-afbeelding** en configureert u vervolgens de container. Voer de locatie in waar de afbeelding wordt gehost.
>* Maak een Linux-functie-app op een App Service-abonnement met behulp van de Azure CLI. Zie Een functie maken [in Linux met behulp van een aangepaste afbeelding voor meer informatie.](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function)
>
>Als u wilt implementeren in een bestaande app met behulp van een aangepaste container, gebruikt u in [Azure Functions Core Tools](functions-run-local.md)de [`func deploy`](functions-run-local.md#publish) opdracht .

>__Wanneer u deze gebruikt:__ Gebruik de optie Docker-container wanneer u meer controle nodig hebt over de Linux-omgeving waarin uw functie-app wordt uitgevoerd. Dit implementatiemechanisme is alleen beschikbaar voor functies die worden uitgevoerd op Linux.

### <a name="web-deploy-msdeploy"></a>Web Deploy (MSDeploy)

Web Deploy verpakt en implementeert uw Windows-toepassingen op elke IIS-server, met inbegrip van uw functie-apps die worden uitgevoerd in Windows in Azure.

>__Het volgende gebruiken:__ Gebruik [Visual Studio hulpprogramma's voor Azure Functions](functions-create-your-first-function-visual-studio.md). Vink het **selectievakje Uitvoeren vanuit pakketbestand (aanbevolen)** uit.
>
>U kunt Web [Deploy 3.6](https://www.iis.net/downloads/microsoft/web-deploy) ook downloaden en rechtstreeks `MSDeploy.exe` aanroepen.

>__Wanneer u deze gebruikt:__ Web Deploy wordt ondersteund en heeft geen problemen, maar het voorkeursmechanisme is [zip deploy met Uitvoeren vanuit pakket ingeschakeld.](#zip-deploy) Zie de handleiding voor Visual Studio [meer informatie.](functions-develop-vs.md#publish-to-azure)

### <a name="source-control"></a>Broncodebeheer

Gebruik broncodebeheer om uw functie-app te verbinden met een Git-opslagplaats. Een update voor code in die opslagplaats activeert de implementatie. Zie de [Kudu-wiki voor meer informatie.](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)

>__Het volgende gebruiken:__ Gebruik Deployment Center in het gebied Functions van de portal om publicatie vanuit broncodebeheer in te stellen. Zie Continue implementatie [voor](functions-continuous-deployment.md)Azure Functions.

>__Wanneer u deze gebruikt:__ Het gebruik van broncodebeheer is best practice voor teams die samenwerken aan hun functie-apps. Broncodebeheer is een goede implementatieoptie die geavanceerdere implementatiepijplijnen mogelijk maakt.

### <a name="local-git"></a>Lokale Git

U kunt lokale Git gebruiken om code te pushen van uw lokale computer naar Azure Functions met behulp van Git.

>__Het volgende gebruiken:__ Volg de instructies in [Local Git deployment to Azure App Service](../app-service/deploy-local-git.md).

>__Wanneer u deze gebruikt:__ Over het algemeen wordt u aangeraden een andere implementatiemethode te gebruiken. Wanneer u publiceert vanuit lokale Git, moet u [triggers handmatig synchroniseren.](#trigger-syncing)

### <a name="cloud-sync"></a>Cloudsynchronisatie

Gebruik cloudsynchronisatie om uw inhoud vanuit Dropbox en OneDrive te synchroniseren met Azure Functions.

>__Het volgende gebruiken:__ Volg de instructies in [Inhoud synchroniseren vanuit een cloudmap](../app-service/deploy-content-sync.md).

>__Wanneer u deze gebruikt:__ Over het algemeen raden we andere implementatiemethoden aan. Wanneer u publiceert met behulp van cloudsynchronisatie, moet u [triggers handmatig synchroniseren.](#trigger-syncing)

### <a name="ftp"></a>FTP

U kunt FTP gebruiken om bestanden rechtstreeks over te dragen naar Azure Functions.

>__Het volgende gebruiken:__ Volg de instructies in [Inhoud implementeren met FTP/s.](../app-service/deploy-ftp.md)

>__Wanneer u deze gebruikt:__ Over het algemeen raden we andere implementatiemethoden aan. Wanneer u publiceert met FTP, moet u [triggers handmatig synchroniseren.](#trigger-syncing)

### <a name="portal-editing"></a>Portal bewerken

In de portaleditor kunt u de bestanden in uw functie-app rechtstreeks bewerken (in feite steeds wanneer u uw wijzigingen opgeslagen implementeert).

>__Het volgende gebruiken:__ Als u uw functies in de portal wilt Azure Portal, moet u [uw functies in de portal hebben gemaakt.](./functions-get-started.md) Om één bron van waarheid te behouden, zorgt het gebruik van een andere implementatiemethode ervoor dat uw functie alleen-lezen is en wordt voorkomen dat de portal verder wordt bewerkt. Als u wilt terugkeren naar een status waarin u uw bestanden in de Azure Portal kunt bewerken, kunt u de bewerkingsmodus handmatig terugschakelen naar en alle implementatiegerelateerde toepassingsinstellingen `Read/Write` verwijderen (zoals [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) .

>__Wanneer u deze gebruikt:__ De portal is een goede manier om aan de slag te gaan met Azure Functions. Voor intensievere ontwikkelwerkzaamheden raden we u aan een van de volgende clienthulpprogramma's te gebruiken:
>
>* [Visual Studio Code](./create-first-function-vs-code-csharp.md)
>* [Azure Functions Core Tools (opdrachtregel)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

In de volgende tabel ziet u de besturingssystemen en talen die ondersteuning bieden voor het bewerken van de portal:

| Taal | Windows-verbruik | Windows Premium | Windows Dedicated | Linux-verbruik | Linux Premium | Linux Dedicated |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| C# Script |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (Preview) | | | | | | |
| PowerShell (Preview) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup> Het bewerken van de portal is alleen ingeschakeld voor HTTP- en timertriggers voor functies in Linux met behulp van Premium- en Dedicated-abonnementen.

## <a name="deployment-behaviors"></a>Implementatiegedrag

Wanneer u een implementatie hebt uitgevoerd, mogen alle bestaande uitvoeringen worden voltooid of is er een time-out, waarna de nieuwe code wordt geladen om te beginnen met het verwerken van aanvragen.

Als u meer controle nodig hebt over deze overgang, moet u implementatiesleuven gebruiken.

## <a name="deployment-slots"></a>Implementatiesites

Wanneer u uw functie-app implementeert in Azure, kunt u implementeren naar een afzonderlijke implementatiesleuf in plaats van rechtstreeks naar productie. Zie voor meer informatie over implementatiesleuven de [Azure Functions Deployment Slots](functions-deployment-slots.md) (Implementatiesleuven) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Lees deze artikelen voor meer informatie over het implementeren van uw functie-apps:

+ [Continue implementatie voor Azure Functions](functions-continuous-deployment.md)
+ [Continue levering met behulp van Azure DevOps](functions-how-to-azure-devops.md)
+ [Zip-implementaties voor Azure Functions](deployment-zip-push.md)
+ [Voer uw Azure Functions uit vanuit een pakketbestand](run-functions-from-deployment-package.md)
+ [Resource-implementatie automatiseren voor uw functie-app in Azure Functions](functions-infrastructure-as-code.md)
