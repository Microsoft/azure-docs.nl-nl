---
title: Azure Functions verbinden met Azure Storage met behulp van opdrachtregelhulpprogramma's
description: Informatie over het verbinden van Azure Functions met een Azure Storage-wachtrij door een uitvoerbinding toe te voegen aan uw opdrachtregelproject.
ms.date: 02/07/2020
ms.topic: quickstart
ms.custom: devx-track-python
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: c7d41d889692856a9818aacd265e67b0c2d3d1ad
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422860"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Azure Functions verbinden met Azure Storage met behulp van opdrachtregelhulpprogramma's

In dit artikel integreert u een Azure Storage-wachtrij met de functie die en het opslagaccount dat u in het vorige quickstart-artikel hebt gemaakt. De integratie wordt mogelijk gemaakt door *uitvoerbinding* die gegevens schrijft van een HTTP-aanvraag naar een bericht in de wachtrij. Het voltooien van dit artikel brengt geen extra kosten met zich mee, behalve de paar dollarcent die tijdens de vorige quickstart zijn uitgegeven. Zie [Concepten van Azure Functions-triggers en -bindingen](functions-triggers-bindings.md) voor meer informatie over bindingen.

## <a name="configure-your-local-environment"></a>Uw lokale omgeving configureren

Voordat u begint, moet u het volgende artikel voltooien: [Quickstart: een Azure Functions-project maken via de opdrachtregel][previous-quickstart]. Als u de resources na voltooiing van dat artikel al had opgeruimd, doorloopt u de stappen voor het maken van de functie-app en de bijbehorende resources opnieuw in Azure.

::: zone pivot="programming-language-csharp"  
Voordat u begint, moet u het volgende artikel voltooien: [Quickstart: een Azure Functions-project maken via de opdrachtregel](create-first-function-cli-csharp.md). Als u de resources na voltooiing van dat artikel al had opgeruimd, doorloopt u de stappen voor het maken van de functie-app en de bijbehorende resources opnieuw in Azure.  
::: zone-end  
::: zone pivot="programming-language-javascript"  
Voordat u begint, moet u het volgende artikel voltooien: [Quickstart: een Azure Functions-project maken via de opdrachtregel](create-first-function-cli-node.md). Als u de resources na voltooiing van dat artikel al had opgeruimd, doorloopt u de stappen voor het maken van de functie-app en de bijbehorende resources opnieuw in Azure.  
::: zone-end   
::: zone pivot="programming-language-java"  
Voordat u begint, moet u het volgende artikel voltooien: [Quickstart: een Azure Functions-project maken via de opdrachtregel](create-first-function-cli-java.md). Als u de resources na voltooiing van dat artikel al had opgeruimd, doorloopt u de stappen voor het maken van de functie-app en de bijbehorende resources opnieuw in Azure.  
::: zone-end   
::: zone pivot="programming-language-typescript"  
Voordat u begint, moet u het volgende artikel voltooien: [Quickstart: een Azure Functions-project maken via de opdrachtregel](create-first-function-cli-typescript.md). Als u de resources na voltooiing van dat artikel al had opgeruimd, doorloopt u de stappen voor het maken van de functie-app en de bijbehorende resources opnieuw in Azure.  
::: zone-end   
::: zone pivot="programming-language-python"  
Voordat u begint, moet u het volgende artikel voltooien: [Quickstart: een Azure Functions-project maken via de opdrachtregel](create-first-function-cli-python.md). Als u de resources na voltooiing van dat artikel al had opgeruimd, doorloopt u de stappen voor het maken van de functie-app en de bijbehorende resources opnieuw in Azure.  
::: zone-end   
::: zone pivot="programming-language-powershell"  
Voordat u begint, moet u het volgende artikel voltooien: [Quickstart: een Azure Functions-project maken via de opdrachtregel](create-first-function-cli-powershell.md). Als u de resources na voltooiing van dat artikel al had opgeruimd, doorloopt u de stappen voor het maken van de functie-app en de bijbehorende resources opnieuw in Azure.  
::: zone-end   

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end   

Zie [Concepten van Azure Functions-triggers en -bindingen](functions-triggers-bindings.md) en [Configuratie van de wachtrijuitvoer](functions-bindings-storage-queue-output.md#configuration) voor meer informatie over bindingen.

## <a name="add-code-to-use-the-output-binding"></a>Code toevoegen die gebruikmaakt van de uitvoerbinding

Als de wachtrijbinding is gedefinieerd, kunt u de functie bijwerken om de uitvoerparameter `msg` te ontvangen en berichten te schrijven naar de wachtrij.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

Houd er rekening mee dat u *geen* code hoeft te schrijven voor verificatie, het ophalen van een wachtrijverwijzing of het schrijven van gegevens. Al deze integratietaken kunnen worden verwerkt in de Azure Functions-runtime en in de wachtrijuitvoerbinding.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Het bericht weergeven in de wachtrij van Azure Storage

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

## <a name="redeploy-the-project-to-azure"></a>Het project opnieuw implementeren in Azure

Nu u lokaal hebt gecontroleerd of de functie een bericht naar de Azure Storage-wachtrij heeft geschreven, kunt u uw project opnieuw implementeren om het eindpunt in Azure bij te werken.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
Gebruik in de map *LocalFunctionsProj* de opdracht [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) om het project opnieuw te implementeren, waarbij u `<APP_NAME>` vervangt door de naam van uw app.

```
func azure functionapp publish <APP_NAME>
```
::: zone-end  

::: zone pivot="programming-language-java" 

Gebruik in de lokale projectmap de volgende Maven-opdracht om het project opnieuw te publiceren:
```
mvn azure-functions:deploy
```
::: zone-end

## <a name="verify-in-azure"></a>Verifiëren in Azure

1. Net als in de vorige quickstart gebruikt u een browser of CURL om de opnieuw geïmplementeerde functie te testen.

    # <a name="browser"></a>[Browser](#tab/browser)
    
    Kopieer de volledige **Aanroep-URL** die wordt weergegeven in de uitvoer van de publicatieopdracht naar de adresbalk van een browser en voeg de queryparameter `&name=Functions` toe. De browser moet vergelijkbare uitvoer weergeven als u de functie lokaal hebt uitgevoerd.

    ![De uitvoer van de functie die wordt uitgevoerd in Azure, in een browser](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[curl](#tab/curl)
    
    Voer [`curl`](https://curl.haxx.se/) uit met de **aanroep-URL** en voeg de parameter `&name=Functions` toe. De uitvoer van de opdracht moet de tekst ‘Hallo Functions’ zijn.
    
    ![De uitvoer van de functie wordt uitgevoerd in Azure met behulp van CURL](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. Controleer de opslagwachtrij opnieuw, zoals beschreven in de vorige sectie, om te controleren of deze het nieuwe bericht bevat dat naar de wachtrij is geschreven.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent, gebruikt u de volgende opdracht om de resourcegroep en alle bijbehorende resources te verwijderen om te voorkomen dat er verdere kosten in rekening worden gebracht.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Volgende stappen

U hebt uw HTTP-geactiveerde functie bijgewerkt om gegevens naar een opslagwachtrij te schrijven. U kunt nu meer te weten komen over het ontwikkelen van functies via de opdrachtregel door gebruik te maken van Core Tools en de Azure CLI:

+ [Werken met Azure Functions Core Tools](functions-run-local.md)  

+ [Azure Functions-triggers en -bindingen](functions-triggers-bindings.md)

::: zone pivot="programming-language-csharp"  
+ [Voorbeelden van complete Function-projecten in C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Naslaginformatie over Azure Functions C# voor ontwikkelaars](functions-dotnet-class-library.md)  

[previous-quickstart]: create-first-function-cli-csharp.md

::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Voorbeelden van complete Function-projecten in Javascript](/samples/browse/?products=azure-functions&languages=javascript).

+ [Ontwikkelaarshandleiding voor Azure Functions Javascript](functions-reference-node.md)  

[previous-quickstart]: create-first-function-cli-javascript.md
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Voorbeelden van complete Function-projecten in TypeScript](/samples/browse/?products=azure-functions&languages=typescript).

+ [Ontwikkelaarshandleiding voor Azure Functions TypeScript](functions-reference-node.md#typescript)  

[previous-quickstart]: create-first-function-cli-typescript.md
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Voorbeelden van complete Function-projecten in Python](/samples/browse/?products=azure-functions&languages=python).

+ [Ontwikkelaarshandleiding voor Azure Functions Python](functions-reference-python.md)  

[previous-quickstart]: create-first-function-cli-python.md
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Voorbeelden van complete Function-projecten in PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Ontwikkelaarshandleiding voor Azure Functions PowerShell](functions-reference-powershell.md) 

[previous-quickstart]: create-first-function-cli-powershell.md
::: zone-end
