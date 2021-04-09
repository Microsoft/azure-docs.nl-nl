---
title: 'Quickstart: Uw eerste functie maken in Azure met behulp van Visual Studio'
description: In deze quickstart leert u hoe u voor een Azure-functie een HTTP-trigger kunt maken en publiceren met behulp van Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 09/30/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./functions-create-your-first-function-visual-studio-uiex
ms.openlocfilehash: d691ee60f624f75c89e44e905e5343bf9c71fd4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701388"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Quickstart: Uw eerste functie maken in Azure met behulp van Visual Studio

In dit artikel gebruikt u Visual Studio om een op een C#-klassenbibliotheek gebaseerde functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions.  

Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account.

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, installeert u eerst [Visual Studio 2019](https://azure.microsoft.com/downloads/). Zorg ervoor dat u tijdens de installatie de **Azure-ontwikkeling**-werkbelasting selecteert. Als u in plaats daarvan een Azure Functions-project wilt maken met behulp van Visual Studio 2017, moet u eerst de [nieuwste hulpprogramma's van Azure Functions](functions-develop-vs.md#check-your-tools-version) installeren.

![Visual Studio installeren met de Azure-ontwikkelworkload](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Als u geen [Azure-abonnement](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) hebt, maakt u een [gratis account](https://azure.microsoft.com/free/dotnet/) voordat u begint.

## <a name="create-a-function-app-project"></a>Een functie-appproject maken

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio maakt een project en klasse die standaardcode voor het type HTTP-triggerfunctie bevat. De standaardcode verzendt een HTTP-reactie met een waarde uit de hoofdtekst van de aanvraag of uit de query-tekenreeks. Met het kenmerk `HttpTrigger` wordt aangegeven dat de functie wordt geactiveerd door een HTTP-aanvraag. 

## <a name="rename-the-function"></a>De naam van de functie wijzigen

Met het kenmerk van de `FunctionName`-methode wordt de naam van de functie ingesteld. Deze wordt standaard gegenereerd als `Function1`. Aangezien het hulpprogramma u niet toestaat de standaardnaam van de functie te wijzigen wanneer u het project maakt, raden we u aan even de tijd te nemen om een betere naam te maken voor de functieklasse, het bestand en de metagegevens.

1. Klik in **Verkenner** met de rechtermuisknop op het bestand Function1.cs, en wijzig de naam in `HttpExample.cs`.

1. Wijzig in de code de naam van de klasse Function1 in HttpExample.

1. Wijzig in de `HttpTrigger`-methode met de naam `Run` de naam van het kenmerk van de `FunctionName`-methode, in `HttpExample`.

Nu u de naam van de functie hebt gewijzigd, kunt u deze testen op de lokale computer.

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Visual Studio kan worden geïntegreerd met Azure Functions Core Tools zodat u uw functies lokaal kunt testen met behulp van de volledige Azure Functions-runtime.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project te publiceren in Azure.

## <a name="publish-the-project-to-azure"></a>Het project naar Azure publiceren

Voordat u uw project kunt publiceren, moet u een functie-app in uw Azure-abonnement hebben. Visual Studio Publishing maakt een functie-app wanneer u de eerste keer uw project publiceert.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Uw functie testen in Azure

1. In Cloud Explorer moet de nieuwe functie-app zijn geselecteerd. Als dit niet het geval is, vouwt u uw abonnement uit > **App Services**, en selecteert u de nieuwe functie-app.

1. Klik met de rechtermuisknop op de functie-app en kies **Openen in browser**. Hiermee opent u de hoofdmap van de functie-app in uw standaardwebbrowser, en wordt de pagina geopend waarop staat aangegeven dat de functie-app actief is. 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="Functie-app actief":::

1. Voeg in de adresbalk van de browser de tekenreeks `/api/HttpExample?name=Functions` toe aan de basis-URL, en voer de aanvraag uit.

    De URL die uw HTTP-triggerfunctie aanroept, heeft de volgende indeling:

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

2. Ga naar deze URL. U ziet nu in de browser een antwoord op de externe GET-aanvraag dat is geretourneerd met de functie. Dit ziet er ongeveer als volgt uit:

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="Het antwoord van de functie in de browser":::

## <a name="clean-up-resources"></a>Resources opschonen

Andere Quick Starts in deze verzameling zijn op deze Quick Start gebaseerd. Verwijder de resources niet als u van plan bent om te werken met volgende quickstarts, zelfstudies of een van de services die u in deze quickstart hebt gemaakt.

*Resources* verwijst in Azure naar functie-apps, functies, opslagaccounts,enzovoort. Deze zijn gegroepeerd in *resourcegroepen*. U kunt alle resources in een groep verwijderen door de groep zelf te verwijderen. 

U hebt resources gemaakt om deze snelstartgidsen te voltooien. Deze resources kunnen bij u in rekening worden gebracht, afhankelijk van de [accountstatus](https://azure.microsoft.com/account/) en [serviceprijzen](https://azure.microsoft.com/pricing/). Als u de resources niet meer nodig hebt, kunt u ze als volgt verwijderen:

1. Vouw in Cloud Explorer uw abonnement uit > **App Services**, klik met de rechtermuisknop op de functie-app en kies **Openen in portal**. 

1. Selecteer op de pagina Functie-app het tabblad **Overzicht**, en selecteer vervolgens de koppeling onder **Resourcegroep**.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Selecteer op de pagina Functie-app de resourcegroep die u wilt verwijderen":::

2. Bekijk op de pagina **Resourcegroep** de lijst met opgenomen resources, en controleer of dit de resources zijn die u wilt verwijderen.
 
3. Selecteer **Resourcegroep verwijderen** en volg de instructies.

   Verwijderen kan enkele minuten duren. Wanneer dit is voltooid, verschijnt een aantal seconden een melding in beeld. U kunt ook het belpictogram boven aan de pagina selecteren om de melding te bekijken.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u Visual Studio gebruikt om in Azure een C#-functie-app met een eenvoudige HTTP-triggerfunctie te maken en te publiceren. 

Ga naar het volgende artikel voor informatie over het toevoegen van een Azure Storage-wachtrijbinding aan uw functie:
> [!div class="nextstepaction"]
> [Een Azure Storage-wachtrijbinding aan uw functie toevoegen](functions-add-output-binding-storage-queue-vs.md)

