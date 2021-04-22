---
title: Azure Functions ontwikkelen met Visual Studio Code
description: Meer informatie over het ontwikkelen en testen Azure Functions met behulp van de Azure Functions-extensie voor Visual Studio Code.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/21/2019
ms.openlocfilehash: c2869b2b30722495523a9f0dfb2d70a17a205854
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871269"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Azure Functions ontwikkelen met Visual Studio Code

Met [Azure Functions-extensie voor Visual Studio Code] kunt u lokaal functies ontwikkelen en deze implementeren in Azure. Als deze ervaring uw eerste ervaring is met Azure Functions, kunt u meer informatie krijgen in [Een inleiding tot Azure Functions.](functions-overview.md)

De Azure Functions biedt de volgende voordelen:

* Bewerk, bouw en voer functies uit op uw lokale ontwikkelcomputer.
* Publiceer uw Azure Functions project rechtstreeks naar Azure.
* Schrijf uw functies in verschillende talen en profiteer van de voordelen van Visual Studio Code.

De extensie kan worden gebruikt met de volgende talen, die worden ondersteund door de Azure Functions runtime vanaf versie 2.x:

* [C# gecompileerd](functions-dotnet-class-library.md)
* [C#-script](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Hiervoor moet u [het C#-script instellen als de standaardprojecttaal](#c-script-projects).

In dit artikel zijn voorbeelden momenteel alleen beschikbaar voor JavaScript-functies (Node.js) en C#-klassebibliotheekfuncties.  

In dit artikel vindt u meer informatie over het gebruik van de Azure Functions-extensie om functies te ontwikkelen en deze te publiceren naar Azure. Voordat u dit artikel leest, moet u [uw eerste functie maken met behulp van Visual Studio Code](./create-first-function-vs-code-csharp.md).

> [!IMPORTANT]
> Combineer geen lokale ontwikkeling en portalontwikkeling voor één functie-app. Wanneer u vanuit een lokaal project publiceert naar een functie-app, overschrijft het implementatieproces alle functies die u in de portal hebt ontwikkeld.

## <a name="prerequisites"></a>Vereisten

Voordat u de extensie Azure Functions [voor]Azure Functions code installeert en[Visual Studio,]moet u aan de volgende vereisten voldoen:

* [Visual Studio Code geïnstalleerd](https://code.visualstudio.com/) op een van de [ondersteunde platforms.](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

* Een actief Azure-abonnement.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Andere resources die u nodig hebt, zoals een Azure-opslagaccount, worden in uw abonnement gemaakt wanneer u publiceert met behulp [van Visual Studio Code](#publish-to-azure). 

### <a name="run-local-requirements"></a>Lokale vereisten uitvoeren

Deze vereisten zijn alleen vereist om uw functies lokaal uit te voeren [en fouten op te sporen.](#run-functions-locally) Ze zijn niet vereist om projecten te maken of te publiceren naar Azure Functions.

# <a name="c"></a>[C\#](#tab/csharp)

+ De [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) versie 2.x of hoger. Het pakket Core Tools wordt gedownload en automatisch geïnstalleerd wanneer u het project lokaal start. Core Tools bevat de volledige Azure Functions runtime, dus downloaden en installeren kan enige tijd duren.

+ De [C#-extensie](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) voor Visual Studio Code. 

+ [.NET Core SLI hulpprogramma's](/dotnet/core/tools/?tabs=netcore2x).  

# <a name="java"></a>[Java](#tab/java)

+ De [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) versie 2.x of hoger. Het pakket Core Tools wordt gedownload en automatisch geïnstalleerd wanneer u het project lokaal start. Core Tools bevat de volledige Azure Functions runtime, dus downloaden en installeren kan enige tijd duren.

+ [Debugger voor Java-extensie](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug).

+ [Java 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) wordt aanbevolen. Zie Java-versies voor andere [ondersteunde versies.](functions-reference-java.md#java-versions)

+ [Maven 3 of hoger](https://maven.apache.org/)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ De [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) versie 2.x of hoger. Het pakket Core Tools wordt gedownload en automatisch geïnstalleerd wanneer u het project lokaal start. Core Tools bevat de volledige Azure Functions runtime, dus downloaden en installeren kan enige tijd duren.

+ [Node.js](https://nodejs.org/), Active LTS en Maintenance LTS-versies (10.14.1 aanbevolen). Gebruik de opdracht `node --version` om uw versie te controleren. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

+ De [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) versie 2.x of hoger. Het pakket Core Tools wordt gedownload en automatisch geïnstalleerd wanneer u het project lokaal start. Core Tools bevat de volledige Azure Functions runtime, dus downloaden en installeren kan enige tijd duren.

+ [PowerShell 7](/powershell/scripting/install/installing-powershell-core-on-windows) aanbevolen. Zie [PowerShell-versies voor versie-informatie.](functions-reference-powershell.md#powershell-versions)

+ Zowel [.NET Core 3.1-runtime](https://dotnet.microsoft.com/download) als [.NET Core 2.1-runtime](https://dotnet.microsoft.com/download/dotnet/2.1)  

+ De [PowerShell-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).  

# <a name="python"></a>[Python](#tab/python)

+ De [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) versie 2.x of hoger. Het pakket Core Tools wordt gedownload en automatisch geïnstalleerd wanneer u het project lokaal start. Core Tools bevat de volledige Azure Functions runtime, dus downloaden en installeren kan enige tijd duren.

+ [Python 3.x](https://www.python.org/downloads/). Zie Python-versies [per](functions-reference-python.md#python-version) Azure Functions runtime voor versie-informatie.

+ [Python-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python) (Engelstalig).

---

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Een Azure Functions-project maken

Met de Functions-extensie kunt u een functie-app-project maken, samen met uw eerste functie. De volgende stappen laten zien hoe u een door HTTP geactiveerde functie maakt in een nieuw Functions-project. [HTTP-trigger](functions-bindings-http-webhook.md) is de eenvoudigste functietriggersjabloon om te demonstreren.

1. Selecteer **vanuit Azure: Functies** het pictogram **Functie** maken:

    ![Een functie maken](./media/functions-develop-vs-code/create-function.png)

1. Selecteer de map voor uw functie-app-project en selecteer **vervolgens een taal voor uw functieproject.**

1. Selecteer de **functiesjabloon HTTP-trigger** of u kunt Nu overslaan **selecteren** om een project zonder functie te maken. U kunt later [altijd een functie toevoegen aan uw project.](#add-a-function-to-your-project)

    ![De sjabloon voor de HTTP-trigger kiezen](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Typ **HttpExample** als functienaam, selecteer Enter en selecteer vervolgens **Functieautorisatie.** Voor dit autorisatieniveau moet u een [functiesleutel verstrekken wanneer](functions-bindings-http-webhook-trigger.md#authorization-keys) u het functie-eindpunt aanroept.

    ![Functieautorisatie selecteren](./media/functions-develop-vs-code/create-function-auth.png)

    Er wordt een functie gemaakt in de taal die u hebt gekozen en in de sjabloon voor een door HTTP geactiveerde functie.

    ![Door HTTP geactiveerde functiesjabloon in Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>Gegenereerde projectbestanden

De projectsjabloon maakt een project in de taal van uw keuze en installeert de vereiste afhankelijkheden. Voor elke taal heeft het nieuwe project de volgende bestanden:

* **host.js:** hiermee kunt u de Functions-host configureren. Deze instellingen zijn van toepassing wanneer u functies lokaal en wanneer u ze in Azure gebruikt. Zie voor meer informatiehost.js[naslaginformatie.](functions-host-json.md)

* **local.settings.js:** onderhoudt de instellingen die worden gebruikt wanneer u functies lokaal gebruikt. Deze instellingen worden alleen gebruikt wanneer u functies lokaal gebruikt. Zie Local [settings file (Bestand met lokale instellingen) voor meer informatie.](#local-settings-file)

    >[!IMPORTANT]
    >Omdat de local.settings.jsbestand geheimen kan bevatten, moet u deze uitsluiten van het broncodebeheer van uw project.

Afhankelijk van uw taal worden deze andere bestanden gemaakt:

# <a name="c"></a>[C\#](#tab/csharp)

* [HttpExample.cs-klassebibliotheekbestand](functions-dotnet-class-library.md#functions-class-library-project) dat de functie implementeert.

# <a name="java"></a>[Java](#tab/java)

+ Een pom.xml in de hoofdmap die de project- en implementatieparameters definieert, inclusief projectafhankelijkheden en de [Java-versie](functions-reference-java.md#java-versions). De pom.xml bevat ook informatie over de Azure-resources die tijdens een implementatie worden gemaakt.   

+ Een [Functions.java-bestand](functions-reference-java.md#triggers-and-annotations) in uw src-pad dat de functie implementeert.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

* Een package.jsin het bestand in de hoofdmap.

* Een HttpExample-map [](functions-reference-node.md#folder-structure) die defunction.jsbevat voor het definitiebestand en hetindex.js-bestand , een [Node.js-bestand](functions-reference-node.md#exporting-a-function)dat de functiecode bevat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

* Een HttpExample-map die defunction.js[ bevat voor](functions-reference-powershell.md#folder-structure) het definitiebestand en het run.ps1-bestand, dat de functiecode bevat.
 
# <a name="python"></a>[Python](#tab/python)
    
* Een bestand op projectniveau requirements.txt een lijst met pakketten die door Functions zijn vereist.
    
* Een HttpExample-map met de [function.jshet](functions-reference-python.md#folder-structure) definitiebestand en het \_ \_ init \_ \_ .py-bestand, dat de functiecode bevat.

---

Op dit moment kunt u invoer- en [uitvoerbindingen toevoegen](#add-input-and-output-bindings) aan uw functie. U kunt ook [een nieuwe functie toevoegen aan uw project](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Binding-extensies installeren

Met uitzondering van HTTP- en timertriggers worden bindingen geïmplementeerd in extensiepakketten. U moet de extensiepakketten installeren voor de triggers en bindingen die ze nodig hebben. Het proces voor het installeren van bindingsextensies is afhankelijk van de taal van uw project.

# <a name="c"></a>[C\#](#tab/csharp)

Voer de [opdracht dotnet add package](/dotnet/core/tools/dotnet-add-package) uit in het terminalvenster om de extensiepakketten te installeren die u nodig hebt in uw project. Met de volgende opdracht wordt de Azure Storage-extensie geïnstalleerd, waarmee bindingen voor blob-, wachtrij- en tabelopslag worden geïmplementeerd.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="java"></a>[Java](#tab/java)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="powershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Een functie toevoegen aan uw project

U kunt een nieuwe functie toevoegen aan een bestaand project met behulp van een van de vooraf gedefinieerde Functions-triggersjablonen. Als u een nieuwe functietrigger wilt toevoegen, selecteert u F1 om het opdrachtenpalet te openen. Zoek en voer vervolgens de opdracht **uit Azure Functions: Functie maken.** Volg de aanwijzingen om uw triggertype te kiezen en de vereiste kenmerken van de trigger te definiëren. Als uw trigger een toegangssleutel of een connection string verbinding met een service vereist, moet u deze voorbereiden voordat u de functietrigger maakt.

De resultaten van deze actie zijn afhankelijk van de taal van uw project:

# <a name="c"></a>[C\#](#tab/csharp)

Er wordt een nieuw C#-klassebibliotheekbestand (.cs) toegevoegd aan uw project.

# <a name="java"></a>[Java](#tab/java)

Er wordt een nieuw Java-bestand (.java) toegevoegd aan uw project.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

Er wordt een nieuwe map gemaakt in het project. De map bevat een nieuwe function.jsbestand en het nieuwe JavaScript-codebestand.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Er wordt een nieuwe map gemaakt in het project. De map bevat een nieuwe function.jsin het bestand en het nieuwe PowerShell-codebestand.

# <a name="python"></a>[Python](#tab/python)

Er wordt een nieuwe map gemaakt in het project. De map bevat een nieuwe function.jsin het bestand en het nieuwe Python-codebestand.

---

## <a name="connect-to-services"></a><a name="add-input-and-output-bindings"></a>Verbinding maken met services

U kunt uw functie verbinden met andere Azure-services door invoer- en uitvoerbindingen toe te voegen. Bindingen verbinden uw functie met andere services zonder dat u de verbindingscode moet schrijven. Het proces voor het toevoegen van bindingen is afhankelijk van de taal van uw project. Zie [Concepten van Azure Functions-triggers en -bindingen](functions-triggers-bindings.md) voor meer informatie over bindingen.

De volgende voorbeelden maken verbinding met een opslagwachtrij met de naam , waarbij de connection string voor het opslagaccount is ingesteld in de `outqueue` `MyStorageConnection` toepassingsinstelling in local.settings.jsop.

# <a name="c"></a>[C\#](#tab/csharp)

Werk de functiemethode bij om de volgende parameter toe te voegen aan de `Run` methodedefinitie:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

De parameter `msg` is een type `ICollector<T>` die een verzameling berichten vertegenwoordigt die worden geschreven naar een uitvoerbinding wanneer de functie is voltooid. Met de volgende code wordt een bericht toegevoegd aan de verzameling:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="30-31":::

 Berichten worden naar de wachtrij verzonden wanneer de functie is voltooid.

Zie de naslagdocumentatie voor [Queue Storage-uitvoerbinding voor meer](functions-bindings-storage-queue-output.md?tabs=csharp) informatie. Zie Bindingen toevoegen aan een bestaande functie in Azure Functions voor meer informatie over welke bindingen aan een [functie kunnen worden Azure Functions.](add-bindings-existing-function.md?tabs=csharp) 

# <a name="java"></a>[Java](#tab/java)

Werk de functiemethode bij om de volgende parameter toe te voegen aan de `Run` methodedefinitie:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

De `msg` parameter is een `OutputBinding<T>` type, waarbij een tekenreeks is die naar een `T` uitvoerbinding wordt geschreven wanneer de functie is voltooid. Met de volgende code stelt u het bericht in de uitvoerbinding in:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="33-34":::

Dit bericht wordt naar de wachtrij verzonden wanneer de functie is voltooid.

Zie de naslagdocumentatie voor [Queue Storage-uitvoerbinding voor meer](functions-bindings-storage-queue-output.md?tabs=java) informatie. Zie Bindingen toevoegen aan een bestaande functie in Azure Functions voor meer informatie over welke bindingen aan een [functie kunnen worden Azure Functions.](add-bindings-existing-function.md?tabs=java) 

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

In uw functiecode wordt de `msg` binding gebruikt vanuit de , zoals in dit `context` voorbeeld:

:::code language="javascript" range="5-7" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::

Dit bericht wordt naar de wachtrij verzonden wanneer de functie is voltooid.

Zie de naslagdocumentatie voor [Queue Storage-uitvoerbinding voor meer](functions-bindings-storage-queue-output.md?tabs=javascript) informatie. Zie Bindingen toevoegen aan een bestaande functie in Azure Functions voor meer algemene informatie over welke bindingen aan een [functie kunnen Azure Functions.](add-bindings-existing-function.md?tabs=javascript) 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

:::code language="powershell" range="18-19" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::

Dit bericht wordt naar de wachtrij verzonden wanneer de functie is voltooid.

Zie de naslagdocumentatie voor [Queue Storage-uitvoerbinding voor meer](functions-bindings-storage-queue-output.md?tabs=powershell) informatie. Zie Bindingen toevoegen aan een bestaande functie in Azure Functions voor meer algemene informatie over welke bindingen aan een [functie kunnen Azure Functions.](add-bindings-existing-function.md?tabs=powershell) 

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

Werk de `Main` definitie bij om een uitvoerparameter toe te `msg: func.Out[func.QueueMessage]` voegen, zodat de definitie eruitziet als in het volgende voorbeeld:

:::code language="python" range="6" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Met de volgende code worden tekenreeksgegevens uit de aanvraag toegevoegd aan de uitvoerwachtrij:

:::code language="python" range="18" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Dit bericht wordt naar de wachtrij verzonden wanneer de functie is voltooid.

Zie de naslagdocumentatie voor [Queue Storage-uitvoerbinding voor meer](functions-bindings-storage-queue-output.md?tabs=python) informatie. Zie Bindingen toevoegen aan een bestaande functie in Azure Functions voor meer algemene informatie over welke bindingen aan een [functie kunnen Azure Functions.](add-bindings-existing-function.md?tabs=python) 

---

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Publiceren naar Azure

Visual Studio Code kunt u uw Functions-project rechtstreeks naar Azure publiceren. In dit proces maakt u een functie-app en de bijbehorende resources in uw Azure-abonnement. De functie-app biedt een context waar u uw functies kunt uitvoeren. Het project wordt in uw Azure-abonnement verpakt en geïmplementeerd in de nieuwe functie-app.

Wanneer u vanuit Visual Studio Code publiceert naar een nieuwe functie-app in Azure, kunt u een pad kiezen voor het maken van een snelle functie-app met standaardwaarden of een geavanceerd pad, waar u meer controle hebt over de externe resources die zijn gemaakt. 

Wanneer u vanuit Visual Studio Code publiceert, profiteert u van de [ZIP-implementatietechnologie.](functions-deployment-technologies.md#zip-deploy) 

### <a name="quick-function-app-create"></a>Snelle functie-app maken

Wanneer u kiest **voor + Nieuwe functie-app maken in Azure...**, genereert de extensie automatisch waarden voor de Azure-resources die nodig zijn voor uw functie-app. Deze waarden zijn gebaseerd op de naam van de functie-app die u kiest. Zie het snelstartartikel Visual Studio Code voor een voorbeeld van het gebruik van standaardinstellingen om uw project te publiceren naar een nieuwe [functie-app](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure)in Azure.

Als u expliciete namen wilt verstrekken voor de gemaakte resources, moet u het pad voor geavanceerd maken kiezen.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Een project publiceren naar een nieuwe functie-app in Azure met behulp van geavanceerde opties

Met de volgende stappen publiceert u uw project naar een nieuwe functie-app die is gemaakt met geavanceerde opties voor maken:

1. Voer in het opdrachtpalet de **volgende Azure Functions: Implementeren naar functie-app in.**

1. Als u niet bent aangemeld, wordt u gevraagd om u **aan te melden bij Azure**. U kunt ook **een gratis Azure-account maken.** Nadat u zich vanuit de browser aanmeldt, gaat u terug naar Visual Studio Code.

1. Als u meerdere abonnementen hebt, **selecteert u een abonnement** voor de functie-app en selecteert u **vervolgens + Nieuwe functie-app maken in Azure... _Geavanceerd._** Met _deze_ optie Geavanceerd hebt u meer controle over de resources die u in Azure maakt. 

1. Geef de volgende informatie op nadat u hier om wordt gevraagd:

    | Prompt | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Functie-app selecteren in Azure | Nieuwe functie-app maken in Azure | Typ bij de volgende prompt een globaal unieke naam die uw nieuwe functie-app identificeert en selecteer vervolgens Enter. Geldige tekens voor de naam van en functie-app zijn `a-z`, `0-9` en `-`. |
    | Een besturingssysteem selecteren | Windows | De functie-app wordt uitgevoerd in Windows. |
    | Een hostingplan selecteren | Verbruiksabonnement | Er wordt een [serverloze hosting van een verbruiksplan](consumption-plan.md) gebruikt. |
    | Een runtime voor uw nieuwe app selecteren | Uw projecttaal | De runtime moet overeenkomen met het project dat u publiceert. |
    | Selecteer een resourcegroep voor nieuwe resources | Nieuwe resourcegroep maken | Typ bij de volgende prompt de naam van een resourcegroep, bijvoorbeeld `myResourceGroup` , en selecteer vervolgens Enter. U kunt ook een bestaande resourcegroep selecteren. |
    | Selecteer een opslagaccount | Nieuw opslagaccount maken | Typ bij de volgende prompt een globaal unieke naam voor het nieuwe opslagaccount dat wordt gebruikt door uw functie-app en selecteer vervolgens Enter. Namen van opslagaccounts moeten tussen de 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. U kunt ook een bestaand account selecteren. |
    | Een locatie voor nieuwe resources selecteren | regio | Selecteer een locatie in een regio [bij](https://azure.microsoft.com/regions/) u in de buurt of in de buurt van andere services die door uw functies worden gebruikt. |

    Er wordt een melding weergegeven nadat uw functie-app is gemaakt en het implementatiepakket is toegepast. Selecteer in deze melding de optie **Uitvoer weergeven** om de resultaten van het maken en implementeren te bekijken, inclusief de Azure-resources die u hebt gemaakt.

### <a name="get-the-url-of-an-http-triggered-function-in-azure"></a><a name="get-the-url-of-the-deployed-function"></a>De URL van een door HTTP geactiveerde functie in Azure downloaden

Als u een door HTTP geactiveerde functie wilt aanroepen vanuit een client, hebt u de URL van de functie nodig wanneer deze wordt geïmplementeerd in uw functie-app. Deze URL bevat alle vereiste functiesleutels. U kunt de extensie gebruiken om deze URL's voor uw geïmplementeerde functies op te halen. Als u alleen de externe functie in Azure wilt uitvoeren, gebruikt [u de](#run-functions-in-azure) functie Nu uitvoeren van de extensie.

1. Selecteer F1 om het opdrachtenpalet te openen en zoek en voer de opdracht **uit Azure Functions: Functie-URL kopiëren.**

1. Volg de aanwijzingen om uw functie-app te selecteren in Azure en vervolgens de specifieke HTTP-trigger die u wilt aanroepen.

De functie-URL wordt gekopieerd naar het klembord, samen met de vereiste sleutels die worden doorgegeven door de `code` queryparameter. Gebruik een HTTP-hulpprogramma om POST-aanvragen of een browser voor GET-aanvragen naar de externe functie te verzenden.  

Bij het ophalen van de URL van functies in Azure gebruikt de extensie uw Azure-account om automatisch de sleutels op te halen die nodig zijn om de functie te starten. [Meer informatie over functietoegangssleutels](security-concepts.md#function-access-keys). Voor het starten van niet door HTTP geactiveerde functies is het gebruik van de beheersleutel vereist.

## <a name="republish-project-files"></a>Projectbestanden opnieuw publiceren

Wanneer u continue implementatie [in stelt,](functions-continuous-deployment.md)wordt uw functie-app in Azure bijgewerkt wanneer u bronbestanden bij werkt op de verbonden bronlocatie. We raden continue implementatie aan, maar u kunt de updates van uw projectbestand ook opnieuw publiceren vanuit Visual Studio Code.

> [!IMPORTANT]
> Als u in een bestaande functie-app publiceert, wordt de inhoud van die app in Azure overschreven.

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

## <a name="run-functions"></a>Functies uitvoeren

Met Azure Functions-extensie kunt u afzonderlijke functies uitvoeren, in uw project op uw lokale ontwikkelcomputer of in uw Azure-abonnement. 

Voor HTTP-triggerfuncties roept de extensie het HTTP-eindpunt aan. Voor andere soorten triggers worden beheerders-API's aanroepen om de functie te starten. De bericht body van de aanvraag die naar de functie wordt verzonden, is afhankelijk van het type trigger. Wanneer een trigger testgegevens vereist, wordt u gevraagd om gegevens in een specifieke JSON-indeling in te voeren.

### <a name="run-functions-in-azure"></a>Functies uitvoeren in Azure

Een functie uitvoeren in Azure vanuit Visual Studio Code. 

1. Voer in het opdrachtpalet de **volgende Azure Functions: Functie nu uitvoeren in** en kies uw Azure-abonnement. 

1. Kies uw functie-app in Azure in de lijst. Als u uw functie-app niet ziet, moet u ervoor zorgen dat u bent aangemeld bij het juiste abonnement. 

1. Kies de functie die u wilt uitvoeren in de lijst en typ de bericht-body van de aanvraag in **Aanvraag body invoeren.** Druk op Enter om dit aanvraagbericht naar uw functie te verzenden. De standaardtekst in **Aanvraagtekst invoeren** moet de indeling van de body aangeven. Als uw functie-app geen functies heeft, wordt er een meldingsfout weergegeven met deze fout. 

1. Wanneer de functie wordt uitgevoerd in Azure en een antwoord retourneert, wordt er een melding in Visual Studio Code.
 
U kunt uw functie ook uitvoeren vanuit het gebied **Azure: Functions** door met de rechtermuisknop te klikken (Ctrl+klikken op Mac) op de functie die u wilt uitvoeren vanuit uw functie-app in uw Azure-abonnement en Nu functie uitvoeren **te kiezen...**.

Bij het uitvoeren van functies in Azure gebruikt de extensie uw Azure-account om automatisch de sleutels op te halen die nodig zijn om de functie te starten. [Meer informatie over functietoegangssleutels](security-concepts.md#function-access-keys). Voor het starten van niet door HTTP geactiveerde functies is het gebruik van de beheersleutel vereist.

### <a name="run-functions-locally"></a>Functies lokaal uitvoeren

De lokale runtime is dezelfde runtime die als host voor uw functie-app in Azure wordt gebruikt. Lokale instellingen worden gelezen uit delocal.settings.js[ in het bestand](#local-settings-file). Als u uw Functions-project lokaal wilt uitvoeren, moet u voldoen aan [aanvullende vereisten.](#run-local-requirements)

#### <a name="configure-the-project-to-run-locally"></a>Het project zo configureren dat het lokaal wordt uitgevoerd

De Functions-runtime maakt intern Azure Storage account voor alle triggertypen dan HTTP en webhooks. Daarom moet u de sleutel **Values.AzureWebJobsStorage** instellen op een geldig Azure Storage account connection string.

In deze sectie wordt de [Azure Storage-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) met [Azure Storage Explorer](https://storageexplorer.com/) gebruikt om verbinding te maken met de opslag en de opslag op te connection string.

De opslagaccount instellen op connection string:

1. Open Visual Studio **Cloud Explorer,** vouw **Opslagaccount** uw opslagaccount uit en selecteer vervolgens Eigenschappen en  >  kopieer de waarde **primaire verbindingsreeks.** 

2. Open in uw project de local.settings.jsin het bestand en stel de waarde van de **sleutel AzureWebJobsStorage** in op de connection string u hebt gekopieerd.

3. Herhaal de vorige stap om unieke sleutels toe te voegen aan de **matrix Waarden** voor andere verbindingen die vereist zijn voor uw functies.

Zie Local [settings file (Bestand met lokale instellingen) voor meer informatie.](#local-settings-file)

#### <a name="debug-functions-locally"></a><a name="debugging-functions-locally"></a>Lokaal fouten opsporen in functies  

Als u fouten in uw functies wilt opsporen, selecteert u F5. Als u Core [Tools]nog niet hebt gedownload[Azure Functions Core Tools], wordt u gevraagd dit te doen. Wanneer Core Tools is geïnstalleerd en wordt uitgevoerd, wordt uitvoer weergegeven in de terminal. Dit is hetzelfde als het uitvoeren van de Core Tools-opdracht vanuit de terminal, maar met `func host start` extra build-taken en een gekoppeld debugger.  

Wanneer het project wordt uitgevoerd, kunt u de functie Nu **uitvoeren...** van de extensie gebruiken om uw functies te activeren zoals u zou doen wanneer het project in Azure wordt geïmplementeerd. Nu het project wordt uitgevoerd in de foutopsporingsmodus, worden onderbrekingspunten in Visual Studio Code zoals verwacht. 

1. Voer in het opdrachtpalet de **Azure Functions: Functie nu uitvoeren in** en kies Lokaal **project.** 

1. Kies de functie die u wilt uitvoeren in uw project en typ de bericht body van de aanvraag in **Aanvraag body invoeren.** Druk op Enter om dit aanvraagbericht naar uw functie te verzenden. De standaardtekst in **Aanvraagtekst invoeren** moet de indeling van de body aangeven. Als uw functie-app geen functies heeft, wordt er een meldingsfout weergegeven met deze fout. 

1. Wanneer de functie lokaal wordt uitgevoerd en nadat het antwoord is ontvangen, wordt er een melding in Visual Studio code. Informatie over de uitvoering van de functie wordt weergegeven in **het deelvenster** Terminal.

Voor het lokaal uitvoeren van functies is het niet nodig om sleutels te gebruiken. 

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Standaard worden deze instellingen niet automatisch gemigreerd wanneer het project naar Azure wordt gepubliceerd. Nadat het publiceren is uitgevoerd, krijgt u de optie om instellingen van local.settings.jsnaar uw functie-app in Azure te publiceren. Zie Toepassingsinstellingen publiceren [voor meer informatie.](#publish-application-settings)

Waarden in **ConnectionStrings worden** nooit gepubliceerd.

De waarden voor de instellingen van de functietoepassing kunnen ook in uw code worden gelezen als omgevingsvariabelen. Zie de secties Omgevingsvariabelen van deze taalspecifieke referentieartikelen voor meer informatie:

* [C# vooraf gecompileerd](functions-dotnet-class-library.md#environment-variables)
* [C#-script (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

## <a name="application-settings-in-azure"></a>Toepassingsinstellingen in Azure

De instellingen in het local.settings.jsbestand in uw project moeten hetzelfde zijn als de toepassingsinstellingen in de functie-app in Azure. Alle instellingen die u aan de local.settings.jstoevoegen, moeten ook toevoegen aan de functie-app in Azure. Deze instellingen worden niet automatisch geüpload wanneer u het project publiceert. Op dezelfde manier moeten alle instellingen die u in uw functie-app in de [portal](functions-how-to-use-azure-function-app-settings.md#settings) maakt, worden gedownload naar uw lokale project.

### <a name="publish-application-settings"></a>Toepassingsinstellingen publiceren

De eenvoudigste manier om de vereiste instellingen naar uw functie-app in Azure te publiceren, is door de koppeling Instellingen uploaden te gebruiken die wordt weergegeven nadat u uw project hebt gepubliceerd: 

![Toepassingsinstellingen uploaden](./media/functions-develop-vs-code/upload-app-settings.png)

U kunt instellingen ook publiceren met behulp van de **Azure Functions: De opdracht Lokale** instelling uploaden in het opdrachtenpalet. U kunt afzonderlijke instellingen toevoegen aan toepassingsinstellingen in Azure met behulp van **de opdracht Azure Functions: Nieuwe instelling** toevoegen.

> [!TIP]
> Zorg ervoor dat u uw local.settings.jsin het bestand opgeslagen voordat u het publiceert.

Als het lokale bestand is versleuteld, wordt het opnieuw ontsleuteld, gepubliceerd en versleuteld. Als er instellingen zijn met conflicterende waarden op de twee locaties, wordt u gevraagd om te kiezen hoe u wilt doorgaan.

Bekijk bestaande app-instellingen in het gebied **Azure: Functions** door uw abonnement, uw functie-app en Toepassingsinstellingen uit **te breiden.**

![Instellingen voor functie-apps weergeven in Visual Studio Code](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Instellingen downloaden van Azure

Als u toepassingsinstellingen in Azure hebt gemaakt, kunt u deze downloaden naar uw local.settings.json-file met behulp van **de opdracht Azure Functions: Externe instellingen** downloaden.

Net als bij het uploaden, wordt het lokale bestand, als het is versleuteld, opnieuw ontsleuteld, bijgewerkt en versleuteld. Als er instellingen zijn met conflicterende waarden op de twee locaties, wordt u gevraagd om te kiezen hoe u wilt doorgaan.

## <a name="monitoring-functions"></a>Functies bewaken

Wanneer u [functies lokaal hebt uitgevoerd,](#run-functions-locally)worden logboekgegevens gestreamd naar de Terminal-console. U kunt ook logboekgegevens krijgen wanneer uw Functions-project wordt uitgevoerd in een functie-app in Azure. U kunt verbinding maken met streaminglogboeken in Azure om bijna realtime logboekgegevens te bekijken, of u kunt Application Insights inschakelen voor een vollediger begrip van de werking van uw functie-app.

### <a name="streaming-logs"></a>Streaminglogboeken

Wanneer u een toepassing ontwikkelt, is het vaak handig om logboekregistratiegegevens bijna in realtime te bekijken. U kunt een stroom logboekbestanden weergeven die door uw functies worden gegenereerd. Deze uitvoer is een voorbeeld van streaminglogboeken voor een aanvraag naar een door HTTP geactiveerde functie:

![Uitvoer van streaminglogboeken voor HTTP-trigger](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

Zie Streaminglogboeken [voor meer informatie.](functions-monitoring.md#streaming-logs)

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Streaminglogboeken ondersteunen slechts één exemplaar van de Functions-host. Wanneer uw functie wordt geschaald naar meerdere exemplaren, worden gegevens van andere exemplaren niet weergegeven in de logboekstroom. [Live Metrics Stream](../azure-monitor/app/live-stream.md) in Application Insights ondersteunt meerdere exemplaren. Ook in bijna realtime is streaminganalyse gebaseerd op [voorbeeldgegevens.](configure-monitoring.md#configure-sampling)

### <a name="application-insights"></a>Application Insights

U wordt aangeraden de uitvoering van uw functies te controleren door uw functie-app te integreren met Application Insights. Wanneer u een functie-app maakt in Azure Portal, vindt deze integratie standaard plaats. Wanneer u uw functie-app maakt tijdens Visual Studio publicatie, moet u deze Application Insights integreren. Zie Enable [Application Insights integration (Integratie van Application Insights inschakelen) voor meer informatie.](configure-monitoring.md#enable-application-insights-integration)

Zie Monitor Application Insights (Bewaking van [Azure Functions) voor meer informatie over bewaking met behulp Azure Functions.](functions-monitoring.md)

## <a name="c-script-projects"></a>\#C-scriptprojecten

Standaard worden alle C#-projecten gemaakt als [C#-gecompileerde klassebibliotheekprojecten.](functions-dotnet-class-library.md) Als u liever met C#-scriptprojecten werkt, moet u C#-script als standaardtaal selecteren in de instellingen Azure Functions extensie:

1. Selecteer **Instellingen voor**  >    >  **bestandsvoorkeuren.**

1. Ga naar **Extensies voor**  >    >  **gebruikersinstellingen Azure Functions**.

1. Selecteer **C#Script** in **Azure Function: Project Language.**

Nadat u deze stappen hebt voltooid, bevatten aanroepen naar de onderliggende Core Tools de optie , waarmee `--csx` C#-script -projectbestanden (.csx) worden gegenereerd en gepubliceerd. Wanneer u deze standaardtaal hebt opgegeven, worden alle projecten die u maakt standaard ingesteld op C#-scriptprojecten. U wordt niet gevraagd om een projecttaal te kiezen wanneer een standaardinstelling is ingesteld. Als u projecten in andere talen wilt maken, moet u deze instelling wijzigen of verwijderen uit de settings.jsin het bestand. Nadat u deze instelling hebt verwijderd, wordt u opnieuw gevraagd uw taal te kiezen wanneer u een project maakt.

## <a name="command-palette-reference"></a>Naslag voor opdrachtenpalet

De Azure Functions-extensie biedt een handige grafische interface op het gebied voor interactie met uw functie-apps in Azure. Dezelfde functionaliteit is ook beschikbaar als opdrachten in het opdrachtenpalet (F1). Deze Azure Functions zijn beschikbaar:

|Azure Functions opdracht  | Description  |
|---------|---------|
|**Nieuwe instellingen toevoegen**  |  Hiermee maakt u een nieuwe toepassingsinstelling in Azure. Zie Toepassingsinstellingen publiceren [voor meer informatie.](#publish-application-settings) Mogelijk moet u deze [instelling ook downloaden naar uw lokale instellingen.](#download-settings-from-azure) |
| **Implementatiebron configureren** | Verbindt uw functie-app in Azure met een lokale Git-opslagplaats. Zie Continue implementatie [voor](functions-continuous-deployment.md)Azure Functions. |
| **Verbinding maken met GitHub-opslagplaats** | Verbindt uw functie-app met een GitHub-opslagplaats. |
| **Functie-URL kopiëren** | Haalt de externe URL op van een http-geactiveerde functie die wordt uitgevoerd in Azure. Zie Get [the URL of the deployed function (De URL van de geïmplementeerde functie op halen) voor meer informatie.](#get-the-url-of-the-deployed-function) |
| **Functie-app maken in Azure** | Hiermee maakt u een nieuwe functie-app in uw abonnement in Azure. Zie de sectie over het publiceren naar een nieuwe [functie-app in Azure voor meer informatie.](#publish-to-azure)        |
| **Instellingen ontsleutelen** | Ontsleutelt [lokale instellingen](#local-settings-file) die zijn versleuteld door **Azure Functions: Instellingen versleutelen.**  |
| **Functie-app verwijderen** | Hiermee verwijdert u een functie-app uit uw abonnement in Azure. Wanneer er geen andere apps in het App Service zijn, krijgt u de mogelijkheid om die ook te verwijderen. Andere resources, zoals opslagaccounts en resourcegroepen, worden niet verwijderd. Als u alle resources wilt verwijderen, moet [u in plaats daarvan de resourcegroep verwijderen.](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources) Uw lokale project wordt niet beïnvloed. |
|**Functie verwijderen**  | Hiermee verwijdert u een bestaande functie uit een functie-app in Azure. Omdat deze verwijdering geen invloed heeft op uw lokale project, kunt u overwegen om de functie lokaal te verwijderen en vervolgens uw [project opnieuw te publiceren.](#republish-project-files) |
| **Proxy verwijderen** | Hiermee verwijdert u Azure Functions proxy uit uw functie-app in Azure. Zie Werken met Azure Functions-proxy's voor meer [informatie over Azure Functions-proxy's.](functions-proxies.md) |
| **Instelling verwijderen** | Hiermee verwijdert u een functie-app-instelling in Azure. Deze verwijdering heeft geen invloed op de instellingen in uw local.settings.jsin het bestand. |
| **Verbinding met de repo verbreken**  | Hiermee verwijdert u de [continue implementatieverbinding](functions-continuous-deployment.md) tussen een functie-app in Azure en een opslagplaats voor broncodebeheer. |
| **Externe instellingen downloaden** | Hiermee downloadt u instellingen van de gekozen functie-app in Azure naar local.settings.json-file. Als het lokale bestand is versleuteld, wordt het opnieuw ontsleuteld, bijgewerkt en versleuteld. Als er instellingen zijn met conflicterende waarden op de twee locaties, wordt u gevraagd om te kiezen hoe u wilt doorgaan. Zorg ervoor dat u wijzigingen in uw local.settings.jsin het bestand opgeslagen voordat u deze opdracht uit te voeren. |
| **Instellingen bewerken** | Wijzigt de waarde van een bestaande functie-app-instelling in Azure. Deze opdracht heeft geen invloed op de instellingen in uw local.settings.jsbestand.  |
| **Instellingen versleutelen** | Versleutelt afzonderlijke items in de `Values` matrix in de lokale [instellingen.](#local-settings-file) In dit bestand is ook ingesteld op , waarmee wordt aangegeven dat de lokale runtime de instellingen `IsEncrypted` ontsleutelt voordat deze worden `true` gebruikt. Versleutel lokale instellingen om het risico op het lekken van waardevolle informatie te verminderen. In Azure worden toepassingsinstellingen altijd versleuteld opgeslagen. |
| **Functie nu uitvoeren** | Start handmatig een functie met behulp van beheer-API's. Deze opdracht wordt gebruikt voor het lokaal testen tijdens debuggen en voor functies die worden uitgevoerd in Azure. Bij het activeren van een functie in Azure verkrijgt de extensie eerst automatisch een beheersleutel, die wordt gebruikt voor het aanroepen van de externe beheer-API's die functies starten in Azure. De body van het bericht dat naar de API wordt verzonden, is afhankelijk van het type trigger. Voor timertriggers hoeft u geen gegevens door te geven. |
| **Project initialiseren voor gebruik met VS Code** | Hiermee voegt u de Visual Studio Code-projectbestanden toe aan een bestaand Functions-project. Gebruik deze opdracht om te werken met een project dat u hebt gemaakt met behulp van Core Tools. |
| **Installatie of update Azure Functions Core Tools** | Installeert of [werkt]Azure Functions Core Tools , die wordt gebruikt om functies lokaal uit te voeren. |
| **Opnieuw implementeren**  | Hiermee kunt u projectbestanden opnieuw implementeren vanuit een verbonden Git-opslagplaats naar een specifieke implementatie in Azure. Als u lokale updates opnieuw wilt publiceren vanuit Visual Studio Code, [moet u uw project opnieuw publiceren.](#republish-project-files) |
| **Naam van instellingen wijzigen** | Wijzigt de sleutelnaam van een bestaande functie-app-instelling in Azure. Deze opdracht heeft geen invloed op de instellingen in uw local.settings.jsop bestand. Nadat u de naam van de instellingen in Azure hebt gewijzigd, [moet u deze wijzigingen downloaden naar het lokale project](#download-settings-from-azure). |
| **Opnieuw starten** | Start de functie-app opnieuw op in Azure. Als u updates implementeert, wordt ook de functie-app opnieuw gestart. |
| **AzureWebJobsStorage instellen**| Hiermee stelt u de waarde van de `AzureWebJobsStorage` toepassingsinstelling in. Deze instelling is vereist voor Azure Functions. Deze wordt ingesteld wanneer een functie-app wordt gemaakt in Azure. |
| **Begin** | Start een gestopte functie-app in Azure. |
| **Streaminglogboeken starten** | Start de streaminglogboeken voor de functie-app in Azure. Gebruik streaminglogboeken tijdens het oplossen van externe problemen in Azure als u logboekregistratiegegevens bijna in realtime wilt zien. Zie Streaminglogboeken [voor meer informatie.](#streaming-logs) |
| **Stoppen** | Stopt een functie-app die wordt uitgevoerd in Azure. |
| **Streaminglogboeken stoppen** | Stopt de streaminglogboeken voor de functie-app in Azure. |
| **In-/uitschakelen als sleufinstelling** | Wanneer deze optie is ingeschakeld, zorgt ervoor dat een toepassingsinstelling voor een bepaalde implementatiesleuf blijft bestaan. |
| **Uninstall Azure Functions Core Tools** | Hiermee verwijdert Azure Functions Core Tools, die vereist is voor de extensie. |
| **Lokale instellingen uploaden** | Uploadt instellingen van uw local.settings.jsnaar de gekozen functie-app in Azure. Als het lokale bestand is versleuteld, wordt het opnieuw ontsleuteld, geüpload en versleuteld. Als er instellingen zijn met conflicterende waarden op de twee locaties, wordt u gevraagd om te kiezen hoe u wilt doorgaan. Zorg ervoor dat u wijzigingen in uw local.settings.jsin het bestand opgeslagen voordat u deze opdracht uit te voeren. |
| **Commit weergeven in GitHub** | Toont u de meest recente doorzet in een specifieke implementatie wanneer uw functie-app is verbonden met een opslagplaats. |
| **Implementatielogboeken weergeven** | Toont de logboeken voor een specifieke implementatie naar de functie-app in Azure. |

## <a name="next-steps"></a>Volgende stappen

Zie Werken met Azure Functions Core Tools voor meer informatie [over Azure Functions Core Tools.](functions-run-local.md)

Zie C#-referentiemateriaal voor ontwikkelaars voor meer informatie over het ontwikkelen van functies [Azure Functions .NET-klassebibliotheken.](functions-dotnet-class-library.md) Dit artikel bevat ook koppelingen naar voorbeelden van het gebruik van kenmerken om de verschillende typen bindingen te declaren die worden ondersteund door Azure Functions.

[Azure Functions-extensie voor Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md