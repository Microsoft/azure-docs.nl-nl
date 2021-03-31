---
title: Uw Java-functie verbinden met Azure Storage
description: Meer informatie over het verbinden van een door HTTP geactiveerde Java-functie met Azure Storage met behulp van een Queue Storage-uitvoerbinding.
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: b4381c4acbea8a3b7d86d9c32aaf9cea24cf15fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422651"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Uw Java-functie verbinden met Azure Storage

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

In dit artikel leest u hoe u de functie die u in het [vorige quickstart-artikel](./create-first-function-cli-java.md?tabs=bash,browser) hebt gemaakt, met een Azure Storage-wachtrij kunt integreren. De uitvoerbinding die u aan deze functie toevoegt, schrijft gegevens van een HTTP-aanvraag naar een bericht in de wachtrij.

Voor de meeste bindingen is een opgeslagen verbindingsreeks vereist die Functions gebruikt om toegang te krijgen tot de gebonden service. Om de verbinding makkelijker te kunnen maken, gebruikt u het Storage-account dat u voor uw functie-app hebt gemaakt. De verbinding met dit account is al opgeslagen in een app-instelling met de naam `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Vereisten

Voordat u aan dit artikel begint, voert u de stappen in [deel 1 van de Java-quickstart](./create-first-function-cli-java.md?tabs=bash,browser) uit.

## <a name="download-the-function-app-settings"></a>De instellingen voor functie-apps downloaden

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Extensiebundels inschakelen

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Nu kunt u de Storage-uitvoerbinding aan uw project toevoegen.

## <a name="add-an-output-binding"></a>Een uitvoerbinding toevoegen

[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Code toevoegen die gebruikmaakt van de uitvoerbinding

[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]

U bent nu klaar om de nieuwe uitvoerbinding lokaal uit te proberen.

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Gebruik net als voorheen de volgende opdracht om het project te bouwen en de Functions-runtime lokaal te starten:

# <a name="maven"></a>[Maven](#tab/maven)
```bash
mvn clean package 
mvn azure-functions:run
```
# <a name="gradle"></a>[Gradle](#tab/gradle) 
```bash
gradle jar --info
gradle azureFunctionsRun
```
---

> [!NOTE]  
> Omdat u extensiebundels hebt ingeschakeld in de host.json, is de [Opslagbindingsextensie](functions-bindings-storage-blob.md#add-to-your-functions-app) tijdens het opstarten gedownload en geïnstalleerd, samen met de andere Microsoft-bindingsextensies.

Activeer net als voorheen de functie in een nieuw terminalvenster vanaf de opdrachtregel met de opdracht cURL:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

De uitvoerbinding maakt deze keer ook een wachtrij met de naam `outqueue` in uw Storage-account en voegt een bericht toe met dezelfde tekenreeks.

Vervolgens gebruikt u de Azure CLI om de nieuwe wachtrij te bekijken en te controleren of er een bericht is toegevoegd. U kunt uw wachtrij ook weergeven met behulp van [Microsoft Azure Storage Explorer][Azure Storage Explorer] of in [Azure Portal](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Het project opnieuw implementeren 

Voer de volgende opdracht opnieuw uit om de gepubliceerde app bij te werken:  

# <a name="maven"></a>[Maven](#tab/maven)  
```bash
mvn azure-functions:deploy
```
# <a name="gradle"></a>[Gradle](#tab/gradle)  
```bash
gradle azureFunctionsDeploy
```
---

U kunt ook cURL gebruiken om de geïmplementeerde functie te testen. Geef net als voorheen de waarde `AzureFunctions` in de hoofdtekst van de POST-aanvraag naar de URL door, zoals in dit voorbeeld:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

[Bekijk het bericht in Storage Queue](#query-the-storage-queue) opnieuw om te controleren of de uitvoerbinding zoals verwacht een nieuw bericht in de wachtrij genereert.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt uw HTTP-geactiveerde functie bijgewerkt om gegevens naar een Storage-wachtrij te schrijven. Zie de [Azure Functions Java-ontwikkelaarshandleiding](functions-reference-java.md) en [Azure Functions-triggers en -bindingen](functions-triggers-bindings.md) voor meer informatie over het ontwikkelen van Azure Functions met Java. Zie de [voorbeelden van Java-functies](/samples/browse/?products=azure-functions&languages=Java) voor voorbeelden van complete functieprojecten in Java. 

Vervolgens moet u bewaking met Application Insights inschakelen voor uw functie-app:

> [!div class="nextstepaction"]
> [Application Insights-integratie inschakelen](configure-monitoring.md#add-to-an-existing-function-app)


[Azure Storage Explorer]: https://storageexplorer.com/
