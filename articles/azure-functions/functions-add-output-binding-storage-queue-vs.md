---
title: Functies verbinden met Azure Storage met behulp van Visual Studio
description: Lees hoe u een uitvoerbinding toevoegt om functies uit uw C#-klassebibliotheek met behulp van Visual Studio te verbinden met een Azure Storage-wachtrij.
ms.date: 07/22/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 635392212027c73e5aa954eb671be31228796a0d
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185127"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio"></a>Functies verbinden met Azure Storage met behulp van Visual Studio

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

In dit artikel leest u hoe u Visual Studio gebruikt om verbinding te maken met Azure Storage, met behulp van de functie die u hebt gemaakt in het [Vorig quickstart-artikel]. De uitvoerbinding die u aan deze functie toevoegt, schrijft gegevens van een HTTP-aanvraag naar een bericht in een Azure Queue Storage-wachtrij. 

Voor de meeste bindingen is een opgeslagen verbindingsreeks vereist die Functions gebruikt om toegang te krijgen tot de gebonden service. Om het eenvoudiger te maken, gebruikt u het Storage-account dat u hebt gemaakt met uw functie-app. De verbinding met dit account is al opgeslagen in een app-instelling met de naam `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Vereisten

Voordat u aan dit artikel begint, moet u het volgende doen: 

 - [Deel 1 van de quickstart over Visual Studio](./functions-create-your-first-function-visual-studio.md) voltooien. 

- Aanmelden bij uw Azure-abonnement vanuit Visual Studio.

## <a name="download-the-function-app-settings"></a>De instellingen voor functie-apps downloaden

In het [vorige snelstartgids-artikel](./create-first-function-vs-code-csharp.md)hebt u een functie-app gemaakt in Azure, met het vereiste Storage-account. De verbindingsreeks voor dit account wordt veilig opgeslagen in de app-instellingen in Azure. In dit artikel schrijft u berichten naar een opslagwachtrij in hetzelfde account. Als u verbinding wilt maken met uw Storage-account wanneer u de functie lokaal uitvoert, moet u de app-instellingen downloaden naar het bestand *local.settings.json*. 

1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Publiceren**. 

1. Selecteer onder **Acties** **Instellingen Azure App Service bewerken**. 

    ![De toepassingsinstellingen bewerken](media/functions-add-output-binding-storage-queue-vs/edit-app-settings.png)

1. Kopieer onder **AzureWebJobsStorage** de tekenreekswaarde bij **Extern** naar **Lokaal** en selecteer vervolgens **OK**. 

De opslagbinding, die gebruikmaakt van de instelling `AzureWebJobsStorage` voor de verbinding, kan nu verbinding maken met uw Queue Storage-wachtrij wanneer deze lokaal wordt uitgevoerd.

## <a name="register-binding-extensions"></a>Binding-extensies registreren

Omdat u een Queue Storage-uitvoerbinding gebruikt, moet u de extensie voor Storage-bindingen hebben geïnstalleerd voordat u het project uitvoert. Met uitzondering van HTTP- en timertriggers worden bindingen geïmplementeerd als uitbreidingspakketten. 

1. Selecteer in het menu **Hulpprogramma’s** de optie **NuGet Package Manager** > **Package Manager Console**. 

1. Voer in de-console de volgende [Install-Package](/nuget/tools/ps-ref-install-package)-opdracht uit om de Storage-extensies te installeren:

    ```Command
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.6
    ````

Nu kunt u de Storage-uitvoerbinding toevoegen aan uw project.

## <a name="add-an-output-binding"></a>Een uitvoerbinding toevoegen

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Code toevoegen die gebruikmaakt van de uitvoerbinding

Nadat de binding is gedefinieerd, kunt u de `name` van de binding gebruiken om deze te openen als een kenmerk in de functie handtekening. Als u een uitvoerbinding gebruikt, hoeft u niet de Azure Storage SDK-code te gebruiken voor verificatie, het ophalen van een wachtrijverwijzing of het schrijven van gegevens. Deze taken worden voor u verwerkt via Functions-runtime en Queue Storage-uitvoerbinding.

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Wanneer de uitvoerbinding voor het eerst wordt gebruikt, wordt er door de runtime van Functions een nieuwe wachtrij met de naam `outqueue` gemaakt in uw opslagaccount. U gebruikt Cloud Explorer om te controleren of de wachtrij is gemaakt met het nieuwe bericht.

## <a name="examine-the-output-queue"></a>De uitvoerwachtrij controleren

1. Selecteer in Visual Studio in het menu **Weergave** de optie **Cloud Explorer**.

1. Vouw in **Cloud Explorer** uw Azure-abonnement uit en **Opslagaccounts**, en vouw vervolgens het opslagaccount uit dat door uw functie wordt gebruikt. Als u de naam van het opslagaccount niet meer weet, controleert u de instelling van de verbindingsreeks `AzureWebJobsStorage` in het bestand *local.settings.json*.  

1. Vouw het knooppunt **Wachtrijen** uit en dubbelklik vervolgens op de wachtrij met de naam **outqueue** om de inhoud van de wachtrij in Visual Studio weer te geven. 

   De wachtrij bevat het bericht dat met de Queue Storage-uitvoerbinding is gemaakt toen u de met HTTP geactiveerde functie hebt uitgevoerd. Als u de functie hebt aangeroepen met de standaardwaarde voor `name`, namelijk *Azure*, is het wachtrijbericht *Naam is doorgegeven aan de functie: Azure*.

    ![Wachtrijbericht weergegeven in Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Voer de functie opnieuw uit en stuur een nieuwe aanvraag. Er wordt nu een nieuw bericht weergegeven in de wachtrij.  

Nu is het tijd om de bijgewerkte functie-app opnieuw te publiceren naar Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>De bijgewerkte app opnieuw implementeren en verifiëren

1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Publiceren**. Kies vervolgens **Publiceren** om het project opnieuw te publiceren naar Azure.

1. Als de implementatie is voltooid, kunt u de browser nogmaals gebruiken om de opnieuw geïmplementeerde functie te testen. Zoals eerder, moet u de queryreeks `&name=<yourname>` toevoegen aan de URL.

1. [Bekijk het bericht in de opslagwachtrij](#examine-the-output-queue) om te controleren of de uitvoerbinding opnieuw een nieuw bericht in de wachtrij genereert.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt uw HTTP-geactiveerde functie bijgewerkt om gegevens naar een opslagwachtrij te schrijven. Als u meer wilt lezen over het ontwikkelen van functies, raadpleegt u [Azure Functions ontwikkelen met Visual Studio](functions-develop-vs.md).

Vervolgens moet u bewaking met Application Insights inschakelen voor uw functie-app:

> [!div class="nextstepaction"]
> [Application Insights-integratie inschakelen](configure-monitoring.md#add-to-an-existing-function-app)

[Azure Storage Explorer]: https://storageexplorer.com/
[Vorig quickstart-artikel]: functions-create-your-first-function-visual-studio.md