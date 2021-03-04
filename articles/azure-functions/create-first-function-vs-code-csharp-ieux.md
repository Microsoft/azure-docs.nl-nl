---
title: Een C#-functie maken met behulp van Visual Studio Code - Azure Functions
description: Leer hoe u een C#-functie maakt en publiceer vervolgens het lokale project naar serverloze hosting in Azure Functions met behulp van de Azure Functions-extensie in Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom: devx-track-csharp
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2848ce6214d59ba2732dcfc148ccaf9936497f17
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121028"
---
# <a name="quickstart-create-a-c-function-in-azure-using-visual-studio-code"></a>Quickstart: Een C#-functie maken in Azure met behulp van Visual Studio Code

> [!div class="op_single_selector" title1="Uw functietaal selecteren: "]
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [Python](create-first-function-vs-code-python.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Anders (Go/Rust)](create-first-function-vs-code-other.md)

In dit artikel gebruikt u Visual Studio Code om een op een C#-klassenbibliotheek gebaseerde functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de <abbr title="Een runtime Computing Environment waarin alle details van de server transparant zijn voor toepassings ontwikkelaars, waardoor het proces van het implementeren en beheren van code wordt vereenvoudigd.">serverloos</abbr> omgeving van <abbr title="De Azure-service die een goedkope computer omgeving biedt voor toepassingen.">Azure Functions</abbr>.

Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account.

Er is ook een [op een CLI gebaseerde versie](create-first-function-cli-csharp.md) van dit artikel.
    
## <a name="1-configure-your-environment"></a>1. Configureer uw omgeving

Voordat u aan de slag kunt gaan, moet u beschikken over de volgende vereisten:

+ Een Azure <abbr title="Het profiel waarmee facturerings gegevens voor Azure-gebruik worden bijgehouden.">account</abbr> met een actieve <abbr title="De basis organisatie structuur waarin u resources in azure beheert, meestal gekoppeld aan een individu of afdeling binnen een organisatie.">abonnement</abbr>. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ De [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools), versie 3.x.

+ [Visual Studio Code](https://code.visualstudio.com/) op een van de [ondersteunde platforms](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ De [C#-extensie](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) voor Visual Studio Code.  

+ De [Azure Functions-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voor Visual Studio Code.

## <a name="2-create-your-local-project"></a><a name="create-an-azure-functions-project"></a>2. uw lokale project maken

In deze sectie gebruikt u Visual Studio code voor het maken van een lokale <abbr title="Een logische container voor een of meer afzonderlijke functies die samen kunnen worden geïmplementeerd en beheerd.">Azure Functions project</abbr> in C#. Verderop in dit artikel publiceert u de functiecode in Azure.

1. Kies het pictogram van Azure in de <abbr title="De verticale groep met pictogrammen aan de linkerkant van het venster Visual Studio code.">Activiteiten balk</abbr>en selecteer in het gebied **Azure: functions** het pictogram **Nieuw project maken...** .

    ![Een nieuw project maken kiezen](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Kies een maplocatie voor de werkruimte van uw project en kies **Selecteren**.

    > [!NOTE]
    > Deze stappen waren bedoeld om buiten een werkruimte te worden voltooid. Selecteer in dit geval geen projectmap die deel uitmaakt van een werkruimte.

1. Geef de volgende informatie op bij de prompts:

    + **Selecteer een taal voor uw functieproject**: Kies `C#`.

    + **Selecteer een sjabloon voor de eerste functie van uw project**: Kies `HTTP trigger`.

    + **Geef een functienaam op**: Typ `HttpExample`.

    + **Geef een naamruimte op**: Typ `My.Functions`.

    + **Autorisatieniveau**: Kies `Anonymous`, waarmee iedereen uw functie-eindpunt kan aanroepen. Zie [autorisatie sleutels](functions-bindings-http-webhook-trigger.md#authorization-keys)voor meer informatie over autorisatie niveaus.

    + **Selecteer hoe u uw project wilt openen**: Kies `Add to workspace`.

1. Met deze informatie wordt met Visual Studio code een Azure Functions project gegenereerd met een HTTP- <abbr title="Het type gebeurtenis waarmee de code van de functie wordt aangeroepen, zoals een HTTP-aanvraag, een wachtrij bericht of een specifieke tijd.">activeren</abbr>. U kunt de lokale projectbestanden weergeven in de Explorer. Zie [Gegenereerde projectbestanden](functions-develop-vs-code.md#generated-project-files) voor meer informatie over bestanden die worden gemaakt.

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project te publiceren in Azure met behulp van Visual Studio Code.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="5-publish-the-project-to-azure"></a>5. Publiceer het project naar Azure

In deze sectie maakt u een functie-app en de bijbehorende resources in uw Azure-abonnement en implementeert u vervolgens uw code. 

> [!IMPORTANT]
> Als u in een bestaande functie-app publiceert, wordt de inhoud van die app in Azure overschreven. 

1. Kies het Azure-pictogram in de activiteitenbalk en selecteer vervolgens in het gedeelte **Azure: In het gebied** Functies kiest u de knop **Implementeren in functie-app ...** .

    

1. Geef de volgende informatie op bij de prompts:

    + **Selecteer map**: Kies een map in uw werkruimte of blader naar een map die de functie-app bevat. Dit wordt niet weergegeven als u al een geldige functie-app hebt geopend.

    + **Selecteer abonnement**: Kies het abonnement dat u wilt gebruiken. Dit ziet u niet als u maar één abonnement hebt.

    + **Selecteer functie-app in Azure**: Kies `+ Create new Function App`. (Kies niet de optie `Advanced` die niet in dit artikel wordt behandeld.)

    + **Voer een globaal unieke naam in voor de functie-app**: Typ een naam die geldig is in een URL-pad. De naam die u typt, wordt gevalideerd om er zeker van te zijn dat deze <abbr title="De naam moet uniek zijn voor alle functies projecten die worden gebruikt door alle Azure-klanten. Normaal gesp roken gebruikt u een combi natie van uw persoonlijke of bedrijfs naam, toepassings naam en een numerieke id, zoals in contoso-bizapp-func-20">uniek in Azure Functions</abbr>. 

    + **Selecteer een locatie voor nieuwe resources**:  Kies voor betere prestaties een [regio](https://azure.microsoft.com/regions/) bij u in de buurt.

    De uitbrei ding toont de status van afzonderlijke resources, aangezien deze worden gemaakt in Azure in het systeemvak.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Melding van het maken van Azure-resources":::

1. Wanneer dit is voltooid, worden de volgende Azure-resources in uw abonnement gemaakt met behulp van namen op basis van de naam van uw functie-app:

    + Een **resource groep**, een logische container voor gerelateerde resources.
    + Een standaard **Azure Storage account**, waarmee de status en andere informatie over uw projecten worden bijgehouden.
    + Een **verbruiks plan** dat de onderliggende host definieert voor uw serverloze functie-app. 
    + Een **functie-app**, waarmee u de omgeving voor het uitvoeren van uw functie code kunt gebruiken. Met een functie-app kunt u functies groeperen in een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren en delen binnen hetzelfde hostingabonnement.
    + Een **Application Insights exemplaar** dat is gekoppeld aan de functie-app, waarmee het gebruik van uw serverloze functie wordt bijgehouden.

    Nadat de functie-app is gemaakt en het implementatiepakket is toegepast, wordt er een melding weergegeven. 

    > [!TIP]
    > De Azure-resources die vereist zijn voor uw functie-app, worden standaard gemaakt op basis van de naam van de functie-app die u opgeeft. Standaard worden ze ook in dezelfde nieuwe resource groep gemaakt als de functie-app. Als u de namen van deze resources wilt aanpassen of bestaande resources opnieuw wilt gebruiken, moet u in plaats daarvan [het project publiceren met geavanceerde opties voor maken](functions-develop-vs-code.md#enable-publishing-with-advanced-create-options).


1. Selecteer in deze melding de optie **Uitvoer weergeven** om de resultaten van het maken en implementeren te bekijken, inclusief de Azure-resources die u hebt gemaakt. Als u de melding mist, selecteert u het belpictogram in de rechterbenedenhoek om deze opnieuw weer te geven.

    ![Volledige melding maken](./media/functions-create-first-function-vs-code/function-create-notifications.png)

## <a name="6-run-the-function-in-azure"></a>6. Voer de functie uit in azure

1. Ga terug naar het gebied **Azure: functions** in de zijbalk, vouw uw abonnement uit, uw nieuwe functie-app en **functions**. Klik met de rechter muisknop (Windows) of <kbd>CTRL</kbd> (macOS) op de `HttpExample` functie en kies **functie nu uitvoeren...**.

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="De functie nu uitvoeren in azure vanuit Visual Studio code":::

1. In de **hoofd tekst** van de aanvraag ziet u de waarde van de aanvraag bericht hoofdtekst van `{ "name": "Azure" }` .

    Druk op ENTER om dit aanvraag bericht naar uw functie te verzenden.  

1. Wanneer de functie wordt uitgevoerd in Azure en een antwoord retourneert, wordt er een melding gegenereerd in Visual Studio code.

## <a name="5-clean-up-resources"></a>5. opschonen van resources

Wanneer u doorgaat met de [volgende stap](#next-steps) en een <abbr title="Een manier om een functie te koppelen aan een opslag wachtrij, zodat deze berichten kan maken in de wachtrij.">Uitvoer binding van Azure Storage wachtrij</abbr> Als u uw functie wilt gebruiken, moet u ervoor zorgen dat al uw resources op de juiste plaats staan om te kunnen bouwen op wat u al hebt gedaan.

Als dat niet het geval is, kunt u de volgende stappen gebruiken om de functie-app en de bijbehorende resources te verwijderen om te voorkomen dat er verdere kosten in rekening worden gebracht.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

Zie [Kosten voor verbruiksplannen inschatten](functions-consumption-costs.md) voor meer informatie over de kosten voor Functions.

## <a name="next-steps"></a>Volgende stappen

U hebt een functie-app met een eenvoudige HTTP-geactiveerde functie gemaakt in Visual Studio Code. In het volgende artikel vouwt u die functie uit door een uitvoer toe te voegen <abbr title="Een declaratieve verbinding tussen een functie en andere resources. Een invoer binding biedt gegevens voor de functie. een uitvoer binding biedt gegevens van de functie aan andere resources.">dwingen</abbr>. Met deze binding wordt de tekenreeks van de HTTP-aanvraag naar een bericht in een Azure Queue Storage-wachtrij geschreven. 

> [!div class="nextstepaction"]
> [Verbinding maken met een Azure Storage-wachtrij](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
