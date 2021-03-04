---
title: 'Quickstart: Uw eerste functie maken in Azure met behulp van Visual Studio'
description: In deze quickstart leert u hoe u voor een Azure-functie een HTTP-trigger kunt maken en publiceren met behulp van Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 09/30/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9e3144738bd259ab9be75059af00f125581bb37c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050117"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Quickstart: Uw eerste functie maken in Azure met behulp van Visual Studio

In dit artikel gebruikt u Visual Studio om een op een C#-klassenbibliotheek gebaseerde functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de <abbr title="Een runtime Computing Environment waarin alle details van de server transparant zijn voor toepassings ontwikkelaars, waardoor het proces van het implementeren en beheren van code wordt vereenvoudigd.">serverloos</abbr> omgeving van <abbr title="Een Azure-service die een voordelige serverloze computer omgeving biedt voor toepassingen.">Azure Functions</abbr>.

Het volt ooien van deze Quick Start brengt een kleine prijs van een paar USD cent of minder in uw <abbr title="Het profiel waarmee facturerings gegevens voor Azure-gebruik worden bijgehouden.">Azure-account</abbr>.

## <a name="1-prepare-your-environment"></a>1. uw omgeving voorbereiden

+ Een Azure maken <abbr title="Het profiel waarmee facturerings gegevens voor Azure-gebruik worden bijgehouden.">account</abbr> met een actieve <abbr title="De basis organisatie structuur waarin u resources in azure beheert, meestal gekoppeld aan een individu of afdeling binnen een organisatie.">abonnement</abbr>. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ Installeer [Visual Studio 2019](https://azure.microsoft.com/downloads/) en selecteer de werk belasting van **Azure Development** tijdens de installatie. 

![Visual Studio installeren met de Azure-ontwikkelworkload](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

<br/>
<details>
<summary><strong>In plaats daarvan een Azure Functions project gebruiken</strong></summary>
Als u een wilt maken <abbr title="Een logische container voor een of meer afzonderlijke functies die samen kunnen worden geïmplementeerd en beheerd.">Azure Functions project</abbr> in plaats daarvan moet u eerst de [nieuwste Azure functions-hulpprogram ma's](functions-develop-vs.md#check-your-tools-version)installeren met behulp van Visual Studio 2017.
</details>

## <a name="2-create-a-function-app-project"></a>2. een functie-app-project maken

1. Selecteer in Visual Studio-menu **Bestand** > **Nieuw** > **Project**.

1. Voer in **Een nieuw project maken** *functies* in het zoekvenster in, kies de sjabloon **Azure Functions** en selecteer vervolgens **Volgende**.

1. In **uw nieuwe project configureren** voert u **<abbr title=" de naam van de functie-app in als C#-naam ruimte. u moet dus geen onderstrepings tekens, afbreek streepjes of andere niet-alfanumerieke karakters gebruiken. "> Project naam </abbr>** voor uw project en selecteer vervolgens **maken**. 

1. Geef de volgende informatie op voor de instellingen voor het **maken van een nieuwe Azure functions-toepassing** :

    + Selecteer **<abbr title=" deze waarde maakt een functie project dat gebruikmaakt van versie 3. x runtime van Azure functions, dat .net Core 3. x ondersteunt. Azure functions 1. x ondersteunt de .NET Framework. "> Azure Functions v3 (.NET core) </abbr>** uit de vervolg keuzelijst functions runtime. (Zie [Azure functions runtime versies Overview](functions-versions.md)voor meer informatie.)
    
    + Selecteer **<abbr title=" deze waarde maakt een functie die wordt geactiveerd door een HTTP-aanvraag. "> HTTP- </abbr> trigger** als de functie sjabloon.
    
    + Selecteer **<abbr title=" omdat een Azure-functie een opslag account vereist, er een wordt toegewezen of gemaakt wanneer u uw project naar Azure publiceert. Een HTTP-trigger gebruikt geen Azure Storage-account connection string; voor alle andere trigger typen is een geldig Azure Storage account vereist connection string. "> Opslag emulator </abbr>** uit de vervolg keuzelijst voor het opslag account.
        
    + Selecteer **anoniem** in het <abbr title="De gemaakte functie kan door iedere client worden geactiveerd zonder een sleutel op te geven. Met deze autorisatie-instelling kunt u eenvoudig uw nieuwe functie testen.">Autorisatieniveau</abbr> vervolg. (Zie [autorisatie sleutels](functions-bindings-http-webhook-trigger.md#authorization-keys) en [bindingen voor http en webhook](functions-bindings-http-webhook.md)voor meer informatie over sleutels en autorisatie.)

    + Selecteer **Maken**
        
## <a name="3-rename-the-function"></a>3. Wijzig de naam van de functie

Met het kenmerk van de `FunctionName`-methode wordt de naam van de functie ingesteld. Deze wordt standaard gegenereerd als `Function1`. Omdat het hulp programma niet in staat is om de standaard functie naam te overschrijven wanneer u het project maakt, moet u een aantal minuten duren om een betere naam te maken voor de functie klasse, het bestand en de meta gegevens.

1. Klik in **Verkenner** met de rechter muisknop op het bestand Function1.cs en wijzig de naam in *HttpExample.cs*.

1. Wijzig in de code de naam van de klasse Function1 in HttpExample.

1. Wijzig in de `HttpTrigger`-methode met de naam `Run` de naam van het kenmerk van de `FunctionName`-methode, in `HttpExample`.


## <a name="4-run-the-function-locally"></a>4. Voer de functie lokaal uit

1. Als u de functie wilt uitvoeren, drukt u op <kbd>F5</kbd> in Visual Studio.

1. Kopieer de URL van uw functie vanuit de uitvoer van de Azure Functions-runtime.

    ![Lokale Azure-runtime](../../includes/media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

1. Plak de URL van de HTTP-aanvraag in de adresbalk van uw browser. De query teken reeks toevoegen **? name =<YOUR_NAME>** aan deze URL en voer de aanvraag uit. 

    ![De reactie van de lokale host van de functie in de browser](../../includes/media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

1. Als u het fout opsporingsprogramma wilt stoppen, drukt u op <kbd>SHIFT</kbd> + <kbd>F5</kbd> in Visual Studio.

<br/>
<details>
<summary><strong>Problemen oplossen</strong></summary>
 Mogelijk moet u ook een firewall-uitzondering maken, zodat de hulpprogramma's HTTP-aanvragen kunnen afhandelen. Er worden geen autorisatieniveaus afgedwongen wanneer u een functie lokaal uitvoert.
</details>

## <a name="5-publish-the-project-to-azure"></a>5. Publiceer het project naar Azure

1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Publiceren**.

1. Selecteer in **doel** **Azure**

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-1.png" alt-text="Azure-doel selecteren":::

1. In **Specifiek doel** selecteert u **Azure Functie-app (Windows)**

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-2.png" alt-text="Selecteer Azure Functie-app":::

1. Selecteer in **functie**-exemplaar **een nieuwe Azure-functie maken...** en gebruik vervolgens de waarden die zijn opgegeven in de volgende:

    + Voor **naam** bieden een <abbr title="Gebruik een unieke naam voor uw nieuwe functie-app. Accepteer deze naam of voer een nieuwe in. Geldige tekens zijn: `a-z` , `0-9` en `-` ..">Wereldwijd unieke naam</abbr>
    
    + **Selecteer** een abonnement in de vervolg keuzelijst.
    
    + **Selecteer** een bestaande <abbr title="Een logische container voor gerelateerde Azure-resources die u kunt beheren als een eenheid.">resourcegroep</abbr> in de vervolg keuzelijst of kies **Nieuw** om een nieuwe resource groep te maken.
    
    + **Selecteren** <abbr title="Wanneer u uw project publiceert in een functie-app die wordt uitgevoerd in een verbruiksabonnement, betaalt u alleen voor uitvoeringen van uw functie-app. Andere hostingabonnement kosten meer.">Verbruik</abbr> in de vervolg keuzelijst Play-type. (Zie [verbruiks abonnement](consumption-plan.md)voor meer informatie.)
    
    + **Selecteer** een  <abbr title="Een geografische verwijzing naar een specifiek Azure-Data Center waarin resources worden toegewezen. Zie [regio's](https://azure.microsoft.com/regions/) voor een lijst met beschik bare regio's.">location</abbr> in de vervolg keuzelijst.
    
    + **Selecteer** een <abbr = "er is een Azure Storage account vereist voor de runtime van functions. Selecteer Nieuw om een algemeen opslagaccount te configureren. U kunt ook een bestaand account kiezen dat voldoet aan de vereisten voor het opslag account. >Azure Storage</abbr> account in de vervolg keuzelijst

    ![Het dialoogvenster Create App Service](../../includes/media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Selecteer **Maken** 

1. Controleer in **Functie-exemplaar** of **Uitvoeren vanuit pakketbestand** is aangevinkt. 

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="Profiel maken afronden":::

    <br/>
    <details>
    <summary><strong>Wat doet deze instelling?</strong></summary>
    Wanneer u **uitvoeren vanaf pakket bestand** gebruikt, wordt de functie-app geïmplementeerd met behulp van [zip-implementatie](functions-deployment-technologies.md#zip-deploy) met de modus [uitvoeren vanaf pakket](run-functions-from-deployment-package.md) ingeschakeld. Dit is de aanbevolen implementatiemethode voor uw functieproject, aangezien deze leidt tot betere prestaties.    
    </details>   

1. Selecteer **Finish**.

1. Selecteer op de pagina Publiceren de optie **Publiceren**.

1. Controleer op de pagina publiceren de basis-URL van de functie-app.

1. Kies in het tabblad publiceren de optie **beheren in <abbr title=" Cloud Explorer om de inhoud van de site weer te geven, de functie-app te starten en te stoppen en rechtstreeks naar app-resources in Azure en in de Azure portal te bladeren. "> Cloud Explorer </abbr>**.
    
    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Succesbericht publiceren":::
    

## <a name="6-test-your-function-in-azure"></a>6. test uw functie in azure

1. In Cloud Explorer moet de nieuwe functie-app zijn geselecteerd. Als dat niet het geval is, vouwt u uw abonnement uit, vouwt u **app Services** uit en selecteert u de nieuwe functie-app.

1. Klik met de rechtermuisknop op de functie-app en kies **Openen in browser**. Hiermee opent u de hoofdmap van de functie-app in uw standaardwebbrowser, en wordt de pagina geopend waarop staat aangegeven dat de functie-app actief is. 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="Functie-app actief":::

1. Voeg in de adres balk van de browser de teken reeks **/API/HttpExample? name = functions** toe aan de basis-URL en voer de aanvraag uit.

    De URL die uw HTTP-triggerfunctie aanroept, heeft de volgende indeling:

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

2. Ga naar deze URL. U ziet nu in de browser een antwoord op de externe GET-aanvraag dat is geretourneerd met de functie. Dit ziet er ongeveer als volgt uit:

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="Het antwoord van de functie in de browser":::

## <a name="7-clean-up-resources"></a>7. resources opschonen

Verwijder de functie-app en de bijbehorende resources om te voor komen dat er verdere kosten in rekening worden gebracht.

1. Vouw in Cloud Explorer uw abonnement uit, vouw **app Services** uit, klik met de rechter muisknop op de functie-app en kies **openen in portal**. 

1. Selecteer op de pagina Functie-app het tabblad **Overzicht**, en selecteer vervolgens de koppeling onder **Resourcegroep**.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Selecteer op de pagina Functie-app de resourcegroep die u wilt verwijderen":::

1. Bekijk op de pagina **Resourcegroep** de lijst met opgenomen resources, en controleer of dit de resources zijn die u wilt verwijderen.
 
1. Selecteer **Resourcegroep verwijderen** en volg de instructies.

    Verwijderen kan enkele minuten duren. Wanneer dit is voltooid, verschijnt een aantal seconden een melding in beeld. U kunt ook het belpictogram boven aan de pagina selecteren om de melding te bekijken.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het toevoegen van een <abbr title="Een manier om een functie te koppelen aan een opslag wachtrij, zodat deze berichten kan maken in de wachtrij. Bindingen zijn declaratieve verbindingen tussen een functie en andere resources. Een invoer binding biedt gegevens voor de functie. een uitvoer binding biedt gegevens van de functie aan andere resources.">Uitvoer binding van Azure Storage wachtrij</abbr> naar uw functie:

> [!div class="nextstepaction"]
> [Een Azure Storage-wachtrijbinding aan uw functie toevoegen](functions-add-output-binding-storage-queue-vs.md)
