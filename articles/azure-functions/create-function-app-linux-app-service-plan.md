---
title: Een functie-app in Linux maken op basis van de Azure Portal
description: Meer informatie over het maken van uw eerste Azure-functie in Linux met behulp van de Azure Portal.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 34a93795b5e041ccef8e9576f97092e16c429444
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937175"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Een functie-app in Linux maken in een Azure App Service plan

Met Azure Functions kunt u uw functies voor Linux hosten in een standaardcontainer van Azure App Service. In dit artikel wordt uitgelegd hoe u de [Azure Portal](https://portal.azure.com) gebruikt voor het maken van een door Linux gehoste functie-app die wordt uitgevoerd in een [app service plan](dedicated-plan.md). U kunt ook [uw eigen aangepaste container](functions-create-function-linux-custom-image.md) gebruiken.

![Functie-app maken in Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u met uw Azure-account aan bij Azure Portal op <https://portal.azure.com>.

## <a name="create-a-function-app"></a>Een functie-app maken

U moet beschikken over een functie-app om de uitvoering van uw functies in Linux te hosten. De functie-app biedt een omgeving waarin uw functiecode kan worden uitgevoerd. Hiermee kunt u functies groeperen als logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren, schalen en delen. In dit artikel maakt u een App Service plan wanneer u de functie-app maakt.

1. Selecteer vanuit het menu van Azure Portal of op de **startpagina** de optie **Een resource maken**.

1. Selecteer op de pagina **Nieuw** **Reken** > **functie-app**.

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-flow.png" alt-text="Een functie-app maken in Azure Portal":::

1. Op de pagina **Basics** gebruikt u de instellingen voor de functie-app zoals in de volgende tabel wordt vermeld.

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe functie-app is gemaakt. |
    | **[Resourcegroep](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Naam voor de nieuwe resourcegroep waarin uw functie-app moet worden gemaakt. |
    | **Naam van de functie-app** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app wordt aangeduid. Geldige tekens zijn `a-z` (hoofdlettergevoelig), `0-9` en `-`.  |
    |**Publiceren**| **Code** (standaard) | Optie voor het publiceren van codebestanden of een Docker-container. |
    | **Runtimestack** | Voorkeurstaal | Kies een runtime die uw favoriete functieprogrammeertaal ondersteunt. Kies **.NET Core** voor C#- en F#-functies. |
    |**Versie**| Versienummer | Kies de versie van uw geïnstalleerde runtime.  |
    |**Regio**| Voorkeursregio | Kies een [regio](https://azure.microsoft.com/regions/) in de buurt of in de buurt van andere services die door uw functie worden gebruikt. |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-basics-linux.png" alt-text="Pagina Basisinformatie":::

1. Selecteer **Volgende : Hosting**. Voer op de pagina **Hosting** de volgende instellingen in.

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **[Opslagaccount](../storage/common/storage-account-create.md)** |  Wereldwijd unieke naam |  Maak een opslagaccount die wordt gebruikt door uw functie-app. Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en kunnen alleen cijfers en kleine letters bevatten. U kunt ook een bestaand account gebruiken dat voldoet aan de [vereisten voor een opslagaccount](../azure-functions/storage-considerations.md#storage-account-requirements). |
    |**Besturingssysteem**| **Linux** | Er wordt vooraf een besturingssysteem geselecteerd voor u op basis van de selectie van de runtimestack, maar u kunt de instelling wijzigen, indien nodig. |
    | **[Plannen](../azure-functions/functions-scale.md)** | **Verbruik (serverloos)** | Hostingabonnement dat definieert hoe resources worden toegewezen aan uw functie-app. In het standaard **Verbruiksabonnement** worden resources dynamisch toegevoegd zoals door uw functies wordt vereist. Met hosten [zonder server](https://azure.microsoft.com/overview/serverless-computing/) betaalt u alleen voor de tijd dat uw functies actief zijn. Wanneer u in een App Service-plan uitvoert, moet u het [Schalen van uw functie-app](../azure-functions/functions-scale.md) beheren.  |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-hosting-linux.png" alt-text="Hostingpagina":::

1. Selecteer **Volgende : Bewaking**. Voer op de pagina **Bewaking** de volgende instellingen in.

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | **Ja** (standaard instelling) | Hiermee maakt u een Application Insights-resource van dezelfde *app-naam* in de dichtstbijzijnde ondersteunde regio. Door deze instelling uit te vouwen of **Nieuwe maken** te selecteren, kunt u de naam van Application Insights wijzigen of een andere regio kiezen in een [Azure-geografie](https://azure.microsoft.com/global-infrastructure/geographies/) waar u uw gegevens wilt opslaan. |

   :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-monitoring-linux.png" alt-text="Pagina Bewaking":::

1. Selecteer **Beoordelen + maken** om de selecties van appconfiguratie te controleren.

1. Controleer uw instellingen op de pagina **Beoordelen en maken** en selecteer vervolgens **Maken** om de functie-app in te richten en te implementeren.

1. Selecteer het **Meldingspictogram** in de rechterbovenhoek van de portal en zoek het bericht **Implementatie voltooid**.

1. Selecteer **Naar de resource gaan** om uw nieuwe functie-app te bekijken. U kunt ook **Vastmaken aan dashboard** selecteren. Vastmaken maakt het gemakkelijker om terug te gaan naar deze functie-app-resource vanuit uw dashboard.

    ![Implementatiemelding](./media/create-function-app-linux-app-service-plan/function-app-create-notification2.png)

    Zelfs nadat de functie-app beschikbaar is, kan het enkele minuten duren voordat deze volledig is geïnitialiseerd.

Vervolgens maakt u een functie in de nieuwe functie-app.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Een HTTP-triggerfunctie maken

In deze sectie wordt beschreven hoe u een functie maakt in de nieuwe functie-app in de portal.

> [!NOTE]
> De portal-ontwikkelings ervaring kan handig zijn om Azure Functions uit te proberen. Voor de meeste scenario's kunt u uw functies lokaal ontwikkelen en het project publiceren naar uw functie-app met behulp van [Visual Studio code](./create-first-function-vs-code-csharp.md#create-an-azure-functions-project) of de [Azure functions core tools](functions-run-local.md#create-a-local-functions-project).  

1. Selecteer in het menu links van het venster **Functies** de optie **Functies** en selecteer vervolgens **Toevoegen** in het bovenste menu. 
 
1. Selecteer **http-trigger** in het venster **nieuwe functie** .

    ![HTTP-triggerfunctie kiezen](./media/create-function-app-linux-app-service-plan/function-app-select-http-trigger.png)

1. Accepteer in het venster **nieuwe functie** de standaard naam voor de **nieuwe functie** of voer een nieuwe naam in. 

1. Kies **anoniem** in de vervolg keuzelijst **autorisatie niveau** en selecteer vervolgens **functie maken**.

    Azure maakt de HTTP-activerings functie. U kunt de nieuwe functie nu uitvoeren door een HTTP-aanvraag te verzenden.

## <a name="test-the-function"></a>De functie testen

1. Selecteer in de nieuwe functie HTTP-trigger **code + test** in het menu links en selecteer **functie-URL ophalen** in het bovenste menu.

    ![Functie-URL ophalen selecteren](./media/create-function-app-linux-app-service-plan/function-app-select-get-function-url.png)

1. Selecteer in het dialoog venster **functie-URL ophalen** de optie **standaard** in de vervolg keuzelijst en selecteer vervolgens het pictogram **kopiëren naar klem bord** . 

    ![De functie-URL vanuit Azure Portal kopiëren](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

1. Plak de URL van de functie in de adresbalk van uw browser. Voeg de query reeks waarde `?name=<your_name>` toe aan het einde van deze URL en druk op ENTER om de aanvraag uit te voeren. 

    Het volgende voorbeeld toont het antwoord in de browser:

    ![Het antwoord van de functie in de browser.](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    De aanvraag-URL bevat een sleutel die standaard is vereist, en waarmee u via HTTP toegang hebt tot de functie.

1. Wanneer uw functie wordt uitgevoerd, wordt traceringsinformatie naar de logboeken geschreven. Als u de uitvoer van de tracering wilt zien, gaat u terug naar de pagina **code en test** in de portal en vouwt u de pijl **Logboeken** onder aan de pagina uit.

   ![De viewer voor functielogboeken in Azure Portal.](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt een functie-app met een eenvoudige HTTP-activerings functie gemaakt.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Zie [Azure Functions HTTP bindings](functions-bindings-http-webhook.md) (Azure Functions-HTTP-bindingen) voor meer informatie.