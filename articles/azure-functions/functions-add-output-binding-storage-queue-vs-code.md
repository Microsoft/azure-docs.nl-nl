---
title: Azure Functions verbinden met Azure Storage met behulp van Visual Studio Code
description: Informatie over het verbinden van Azure Functions met een Azure Storage-wachtrij door een uitvoerbinding toe te voegen aan uw Visual Studio Code-project.
ms.date: 02/07/2020
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-js
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: b709981e199d63c32426381ba48665402de820ce
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422702"
---
# <a name="connect-azure-functions-to-azure-storage-using-visual-studio-code"></a>Azure Functions verbinden met Azure Storage met behulp van Visual Studio Code

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

In dit artikel leest u hoe u Visual Studio Code gebruikt om Azure Storage te verbinden met de functie die u hebt gemaakt in het vorige quickstart-artikel. De uitvoerbinding die u aan deze functie toevoegt, schrijft gegevens van een HTTP-aanvraag naar een bericht in een Azure Queue Storage-wachtrij. 

Voor de meeste bindingen is een opgeslagen verbindingsreeks vereist die Functions gebruikt om toegang te krijgen tot de gebonden service. Om het eenvoudiger te maken, gebruikt u het Storage-account dat u hebt gemaakt met uw functie-app. De verbinding met dit account is al opgeslagen in een app-instelling met de naam `AzureWebJobsStorage`.  

## <a name="configure-your-local-environment"></a>Uw lokale omgeving configureren

Voordat u aan dit artikel begint, moet aan de volgende vereisten worden voldaan:

* De [Azure Storage-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) installeren.

* [Azure Storage Explorer](https://storageexplorer.com/) installeren. Storage Explorer is een hulpprogramma dat u gebruikt om wachtrijberichten te onderzoeken die worden gegenereerd door uw uitvoerbinding. Storage Explorer wordt ondersteund op macOS-, Windows- en Linux-besturingssystemen.

::: zone pivot="programming-language-csharp"
* [.NET Core CLI-hulpprogramma's](/dotnet/core/tools/?tabs=netcore2x) installeren.
::: zone-end

::: zone pivot="programming-language-csharp"  
* Voer de stappen in [deel 1 van de Visual Studio Code-snelstartgids](create-first-function-vs-code-csharp.md). 
::: zone-end  
::: zone pivot="programming-language-javascript"  
* Voer de stappen in [deel 1 van de Visual Studio Code-snelstartgids](create-first-function-vs-code-node.md). 
::: zone-end   
::: zone pivot="programming-language-java"  
* Voer de stappen in [deel 1 van de Visual Studio Code-snelstartgids](create-first-function-vs-code-java.md). 
::: zone-end   
::: zone pivot="programming-language-typescript"  
* Voer de stappen in [deel 1 van de Visual Studio Code-snelstartgids](create-first-function-vs-code-typescript.md). 
::: zone-end   
::: zone pivot="programming-language-python"  
* Voer de stappen in [deel 1 van de Visual Studio Code-snelstartgids](create-first-function-vs-code-python.md). 
::: zone-end   
::: zone pivot="programming-language-powershell"  
* Voer de stappen in [deel 1 van de Visual Studio Code-snelstartgids](create-first-function-vs-code-powershell.md). 
::: zone-end   

In dit artikel wordt ervan uitgegaan dat u al bent aangemeld bij uw Azure-abonnement vanuit Visual Studio Code. U kunt zich aanmelden door `Azure: Sign In` vanuit het opdrachtpalet uit te voeren. 

## <a name="download-the-function-app-settings"></a>De instellingen voor functie-apps downloaden

In het [vorige snelstartgids-artikel](functions-create-first-function-vs-code.md)hebt u een functie-app gemaakt in Azure, met het vereiste Storage-account. De verbindingsreeks voor dit account wordt veilig opgeslagen in de app-instellingen in Azure. In dit artikel schrijft u berichten naar een opslagwachtrij in hetzelfde account. Als u verbinding wilt maken met uw Storage-account wanneer u de functie lokaal uitvoert, moet u de app-instellingen downloaden naar het bestand local.settings.json. 

1. Druk op de toets F1 om het opdrachtpalet te openen, zoek naar de opdracht `Azure Functions: Download Remote Settings....` en voer deze uit. 

1. Kies de functie-app die u in het vorige artikel hebt gemaakt. Selecteer **Ja op alle** om de bestaande lokale instellingen te overschrijven. 

    > [!IMPORTANT]  
    > Omdat deze geheimen bevat, wordt het bestand local.settings.json nooit gepubliceerd en wordt deze uitgesloten van het broncodebeheer.

1. Kopieer de waarde `AzureWebJobsStorage`. Dit is de sleutel voor het verbindingsreekswaarde van de Storage-account. U gebruikt deze verbinding om te controleren of de uitvoerbinding werkt zoals verwacht.

## <a name="register-binding-extensions"></a>Binding-extensies registreren

Omdat u een Queue Storage-uitvoerbinding gebruikt, moet u de extensie voor Storage-bindingen hebben geïnstalleerd voordat u het project uitvoert. 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

Uw project is geconfigureerd voor het gebruik van [uitbreidingsbundels](functions-bindings-register.md#extension-bundles), waarmee automatisch een vooraf gedefinieerde set uitbreidingspakketten wordt geïnstalleerd. 

Het gebruik van uitbreidingsbundels wordt ingeschakeld in het bestand host.json in de hoofdmap van het project, dat er als volgt uitziet:

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

::: zone pivot="programming-language-csharp"

Met uitzondering van HTTP- en timertriggers worden bindingen geïmplementeerd als uitbreidingspakketten. Voer de volgende [dotnet add package](/dotnet/core/tools/dotnet-add-package)-opdracht in het terminalvenster uit om het Storage-extensiepakket toe te voegen aan uw project.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage
```

::: zone-end

Nu kunt u de Storage-uitvoerbinding toevoegen aan uw project.

## <a name="add-an-output-binding"></a>Een uitvoerbinding toevoegen

In functies moet voor elk type binding een `direction`, `type`en een unieke `name` worden gedefinieerd in het bestand function.json. De manier waarop u deze kenmerken definieert, is afhankelijk van de taal van uw functie-app.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [functions-add-output-binding-java](../../includes/functions-add-output-binding-java.md)]

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Code toevoegen die gebruikmaakt van de uitvoerbinding

Nadat de binding is gedefinieerd, kunt u de `name` van de binding gebruiken om deze te openen als een kenmerk in de functie handtekening. Als u een uitvoerbinding gebruikt, hoeft u niet de Azure Storage SDK-code te gebruiken voor verificatie, het ophalen van een wachtrijverwijzing of het schrijven van gegevens. Deze taken worden voor u verwerkt via Functions-runtime en Queue Storage-uitvoerbinding.

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

::: zone-end

::: zone pivot="programming-language-csharp"  

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

::: zone-end  

::: zone pivot="programming-language-java"  

[!INCLUDE [functions-add-storage-binding-java-code](../../includes/functions-add-storage-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end  

<!--- Local testing section --->

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Wanneer de uitvoerbinding voor het eerst wordt gebruikt, wordt er door de runtime van Functions een nieuwe wachtrij met de naam **outqueue** gemaakt in uw opslagaccount. U gebruikt Storage Explorer om te controleren of de wachtrij is gemaakt met het nieuwe bericht.

::: zone pivot="programming-language-java"  

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end

### <a name="connect-storage-explorer-to-your-account"></a>Storage Explorer verbinden met uw account

Sla deze sectie over als u Azure Storage Explorer al hebt geïnstalleerd en met uw Azure-account hebt verbonden.

1. Voer het hulpprogramma [Azure Storage Explorer] uit, selecteer aan de linkerkant het pictogram Verbinding maken en selecteer **Een account toevoegen**.

    ![Een Azure-account toevoegen aan Microsoft Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. Kies in het dialoogvenster **Verbinding maken** **Een Azure-account toevoegen**, kies uw **Azure-omgeving** en selecteer **Aanmelden...** . 

    ![Aanmelden bij uw Azure-account](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Nadat u zich hebt aangemeld bij uw account, ziet u alle Azure-abonnementen die zijn gekoppeld aan uw account.

### <a name="examine-the-output-queue"></a>De uitvoerwachtrij controleren

1. Druk in Visual Studio Code op de F1-toets om het opdrachtpalet te openen, zoek de opdracht en voer deze uit `Azure Storage: Open in Storage Explorer` en kies de naam van uw Storage-accountaccount. Uw opslagaccount wordt geopend in Azure Storage Explorer.  

1. Vouw het knooppunt **Wachtrijen** uit en selecteer vervolgens de wachtrij met de naam **outqueue**. 

   De wachtrij bevat het bericht dat met de Queue Storage-uitvoerbinding is gemaakt toen u de met HTTP geactiveerde functie hebt uitgevoerd. Als u de functie hebt aangeroepen met de standaardwaarde voor `name`, namelijk *Azure*, is het wachtrijbericht *Naam is doorgegeven aan de functie: Azure*.

    ![Wachtrijbericht weergegeven in Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Voer de functie opnieuw uit en stuur een nieuwe aanvraag. Er wordt nu een nieuw bericht weergegeven in de wachtrij.  

Nu is het tijd om de bijgewerkte functie-app opnieuw te publiceren naar Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>De bijgewerkte app opnieuw implementeren en verifiëren

1. Druk in Visual Studio Code op F1 om het opdrachtenpalet te openen. In het opdrachtenpalet zoekt en selecteert u `Azure Functions: Deploy to function app...`.

1. Kies de functie-app die u in het eerste artikel hebt gemaakt. Omdat u uw project opnieuw implementeert voor dezelfde app, selecteert u **Implementeren** om de waarschuwing over het overschrijven van bestanden te negeren.

1. Nadat de implementatie is voltooid, kunt u de opnieuw geïmplementeerde functie weer testen door cURL of een browser te gebruiken. Voeg, net als voorheen, de queryreeks `&name=<yourname>` toe aan de URL, zoals in het volgende voorbeeld:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. [Bekijk het bericht in de opslagwachtrij](#examine-the-output-queue) om te controleren of de uitvoerbinding opnieuw een nieuw bericht in de wachtrij genereert.

## <a name="clean-up-resources"></a>Resources opschonen

In Azure verwijzen *Resources* naar functie-apps, functies, opslagaccounts enzovoort. Deze zijn gegroepeerd in *resourcegroepen*. U kunt alle resources in een groep verwijderen door de groep zelf te verwijderen.

U hebt resources gemaakt om deze snelstartgidsen te voltooien. Deze resources kunnen bij u in rekening worden gebracht, afhankelijk van de [accountstatus](https://azure.microsoft.com/account/) en [serviceprijzen](https://azure.microsoft.com/pricing/). Als u de resources niet meer nodig hebt, kunt u ze als volgt verwijderen:

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt uw HTTP-geactiveerde functie bijgewerkt om gegevens naar een opslagwachtrij te schrijven. Nu kunt u meer informatie vinden over het ontwikkelen van functies met Visual Studio Code:

+ [Azure Functions ontwikkelen met Visual Studio Code](functions-develop-vs-code.md)

+ [Azure Functions-triggers en -bindingen](functions-triggers-bindings.md).
::: zone pivot="programming-language-csharp"  
+ [Voorbeelden van complete Function-projecten in C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Naslaginformatie over Azure Functions C# voor ontwikkelaars](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Voorbeelden van complete Function-projecten in Javascript](/samples/browse/?products=azure-functions&languages=javascript).

+ [Ontwikkelaarshandleiding voor Azure Functions Javascript](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-java"  
+ [Voorbeelden van complete Function-projecten in Java](/samples/browse/?products=azure-functions&languages=java).

+ [Java-ontwikkelaarshandleiding voor Azure Functions](functions-reference-java.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Voorbeelden van complete Function-projecten in TypeScript](/samples/browse/?products=azure-functions&languages=typescript).

+ [Ontwikkelaarshandleiding voor Azure Functions TypeScript](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Voorbeelden van complete Function-projecten in Python](/samples/browse/?products=azure-functions&languages=python).

+ [Ontwikkelaarshandleiding voor Azure Functions Python](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Voorbeelden van complete Function-projecten in PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Ontwikkelaarshandleiding voor Azure Functions PowerShell](functions-reference-powershell.md) 
::: zone-end
