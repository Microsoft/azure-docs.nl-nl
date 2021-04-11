---
title: Een functie maken met behulp van Azure for students starter
description: Meer informatie over het maken van een Azure-functie vanuit een Azure for students starter-abonnement
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 3ca91999182e717500eacfa73dda834e9cf89562
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067126"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Een functie maken met behulp van Azure for students starter

In deze zelf studie maakt u een ' Hallo wereld ' HTTP-functie in een Azure for students starter-abonnement. Er wordt ook uitgelegd wat er beschikbaar is in Azure Functions in dit type abonnement.

Met micro soft *Azure for students starter* kunt u aan de slag met de Azure-producten die u nodig hebt om gratis te ontwikkelen in de Cloud. [Meer informatie over deze aanbieding vindt u hier.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Met Azure Functions kunt u uw code in een [serverloze](https://azure.microsoft.com/solutions/serverless/) omgeving uitvoeren zonder dat u eerst een virtuele machine moet maken of een webtoepassing moet publiceren. [Meer informatie over functies vindt u hier.](./functions-overview.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function"></a>Een functie maken

 In dit artikel vindt u informatie over het gebruik van Azure Functions voor het maken van een ' Hallo wereld ' HTTP-trigger functie in de Azure Portal.

![Functie-app maken in Azure Portal](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u met uw Azure-account aan bij [Azure Portal](https://portal.azure.com).

## <a name="create-a-function-app"></a>Een functie-app maken

U moet een functie-app hebben die als host fungeert voor de uitvoering van uw functies. Met een functie-app kunt u functies groeperen in een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren, schalen en delen.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Vervolgens maakt u een functie in de nieuwe functie-app.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Een HTTP-triggerfunctie maken

1. Selecteer in het menu links van het venster **Functies** de optie **Functies** en selecteer vervolgens **Toevoegen** in het bovenste menu. 
 
1. Selecteer **http-trigger** in het venster **nieuwe functie** .

    ![HTTP-triggerfunctie kiezen](./media/functions-create-student-starter/function-app-select-http-trigger.png)

1. Accepteer in het venster **nieuwe functie** de standaard naam voor de **nieuwe functie** of voer een nieuwe naam in. 

1. Kies **anoniem** in de vervolg keuzelijst **autorisatie niveau** en selecteer vervolgens **functie maken**.

    Azure maakt de HTTP-activerings functie. U kunt de nieuwe functie nu uitvoeren door een HTTP-aanvraag te verzenden.

## <a name="test-the-function"></a>De functie testen

1. Selecteer in de nieuwe functie HTTP-trigger **code + test** in het menu links en selecteer **functie-URL ophalen** in het bovenste menu.

    ![Functie-URL ophalen selecteren](./media/functions-create-student-starter/function-app-select-get-function-url.png)

1. Selecteer in het dialoog venster **functie-URL ophalen** de optie **standaard** in de vervolg keuzelijst en selecteer vervolgens het pictogram **kopiëren naar klem bord** . 

    ![De functie-URL vanuit Azure Portal kopiëren](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

1. Plak de URL van de functie in de adresbalk van uw browser. Voeg de query reeks waarde `?name=<your_name>` toe aan het einde van deze URL en druk op ENTER om de aanvraag uit te voeren. 

    Het volgende voorbeeld toont het antwoord in de browser:

    ![Het antwoord van de functie in de browser.](./media/functions-create-student-starter/function-app-browser-testing.png)

    De aanvraag-URL bevat een sleutel die standaard is vereist, en waarmee u via HTTP toegang hebt tot de functie.

1. Wanneer uw functie wordt uitgevoerd, wordt traceringsinformatie naar de logboeken geschreven. Als u de uitvoer van de tracering wilt zien, gaat u terug naar de pagina **code en test** in de portal en vouwt u de pijl **Logboeken** onder aan de pagina uit.

   ![De viewer voor functielogboeken in Azure Portal.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Ondersteunde functies in azure for students starter

In azure for students starter hebt u toegang tot de meeste functies van de Azure Functions runtime, met de volgende sleutel beperkingen:

* De HTTP-trigger is het enige type trigger dat wordt ondersteund.
    * Alle invoer-en uitvoer bindingen worden ondersteund. [Bekijk hier de volledige lijst.](functions-triggers-bindings.md)
* Ondersteunde talen: 
    * C# (.NET Core 2)
    * Java script (Node.js 8 & 10)
    * F # (.NET Core 2)
    * [Bekijk hier de talen die in hogere plannen worden ondersteund](supported-languages.md)
* Windows is het enige besturings systeem dat wordt ondersteund.
* De schaal is beperkt tot [één exemplaar van een gratis laag](https://azure.microsoft.com/pricing/details/app-service/windows/) dat elke dag maxi maal 60 minuten wordt uitgevoerd. U kunt serverlessly schalen van 0 naar 1 automatisch als HTTP-verkeer wordt ontvangen, maar niet verder.
* Alleen [versie 2. x en hoger](functions-versions.md) van de functions-runtime wordt ondersteund.
* Alle hulpprogram ma's voor ontwikkel aars worden ondersteund voor het bewerken en publiceren van functies. Dit omvat VS code, Visual Studio, de Azure CLI en de Azure Portal. Als u andere items dan de portal wilt gebruiken, moet u eerst een app in de portal maken en vervolgens die app als een implementatie doel in uw favoriete hulp programma kiezen.

## <a name="next-steps"></a>Volgende stappen

U bent nu klaar met het maken van een functie-app met een eenvoudige HTTP-activerings functie. Daarna kunt u het lokale hulp programma, meer talen, bewaking en integraties verkennen.

 * [Uw eerste functie maken met Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Uw eerste functie maken met Visual Studio Code](./create-first-function-vs-code-csharp.md)
 * [Ontwikkelaarshandleiding voor Azure Functions Javascript](./functions-reference-node.md)
 * [Azure Functions gebruiken om verbinding te maken met een Azure SQL Database](./functions-scenario-database-table-cleanup.md)
 * [Meer informatie over Azure functions HTTP-bindingen](./functions-bindings-http-webhook.md).
 * [Uw Azure Functions bewaken](./functions-monitoring.md)