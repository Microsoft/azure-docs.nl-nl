---
title: "Zelfstudie: API's importeren en beheren - Azure API Management en Visual Studio Code | Microsoft Docs"
description: In deze zelfstudie leert u hoe u de Azure API Management-extensie voor Visual Studio Code kunt gebruiken om API's te importeren, te testen en te beheren.
ms.service: api-management
author: dlepow
ms.author: apimpm
ms.topic: tutorial
ms.date: 12/10/2020
ms.openlocfilehash: 0090d981e93cee12f2feaaf7d2c12f341564f6ec
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482327"
---
# <a name="tutorial-use-the-api-management-extension-for-visual-studio-code-to-import-and-manage-apis"></a>Zelfstudie: Gebruik de API Management-extensie voor Visual Studio Code om API's te importeren en te beheren

In deze zelfstudie leert u hoe u de extensie API Management voor Visual Studio Code kunt gebruiken voor algemene bewerkingen in API Management. Gebruik de vertrouwde Visual Studio Code-omgeving om API's te importeren, te testen en te beheren.

In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een API importeren in API Management
> * De API bewerken
> * API Management-beleid toepassen
> * De API testen


:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-result.png" alt-text="API in API Management-extensie":::

Raadpleeg de zelfstudies voor API Management met behulp van de [Azure-portal](import-and-publish.md) voor een inleiding tot aanvullende API Management-functies.

## <a name="prerequisites"></a>Vereisten
- Kennis van de [terminologie van Azure API Management](api-management-terminology.md)
- Zorg ervoor dat u Visual Studio [Code en](https://code.visualstudio.com/) de nieuwste [Azure API Management-extensie voor Visual Studio Code hebt geïnstalleerd](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)
- [Een API Management-exemplaar maken](vscode-create-service-instance.md)

## <a name="import-an-api"></a>Een API importeren

In het volgende voorbeeld wordt een OpenAPI-specificatie in de JSON-indeling geïmporteerd in API Management. Microsoft biedt de back-end-API die in dit voorbeeld wordt gebruikt en host deze op Azure op `https://conferenceapi.azurewebsites.net?format=json`.

1. Selecteer in Visual Studio Code het Azure-pictogram op de Activiteitenbalk.
1. Vouw in het deelvenster Verkenner het API Management-exemplaar uit dat u hebt gemaakt.
1. Klik met de rechtermuisknop op **API's** en selecteer **Importeren vanuit OpenAPI-koppeling**. 
1. Voer de volgende waarden in als u hierom wordt gevraagd:
    1. Een **OpenAPI-koppeling** voor inhoud in de JSON-indeling. Voor dit voorbeeld: *https://conferenceapi.azurewebsites.net?format=json* .
    Deze URL is de service waarmee de voorbeeld-API wordt geïmplementeerd. API Management stuurt aanvragen door naar dit adres.
    1. Een **API-naam**, zoals *demo-conference-api*, die uniek is in het API Management-exemplaar. Deze naam mag alleen letters, cijfers en afbreekstreepjes bevatten. Het eerste en laatste teken moeten alfanumeriek zijn. Deze naam wordt gebruikt in het pad om de API aan te roepen.

Nadat de API is geïmporteerd, wordt deze weergegeven in het deelvenster Verkenner, en beschikbare API-bewerkingen worden weergegeven onder het knooppunt **Bewerkingen**.

:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-operations.png" alt-text="API geïmporteerd in het deelvenster Verkenner":::

## <a name="edit-the-api"></a>De API bewerken

U kunt de API bewerken in Visual Studio Code. Bewerk bijvoorbeeld de Resource Manager JSON-beschrijving van de API in het editorvenster om het **HTTP**-protocol te verwijderen dat wordt gebruikt voor toegang tot de API. Selecteer vervolgens **Bestand** > **Opslaan**.

:::image type="content" source="media/visual-studio-code-tutorial/import-demo-api.png" alt-text="JSON-beschrijving bewerken":::

Klik met de rechtermuisknop op de API-naam in het deelvenster Verkenner, en selecteer **OpenAPI bewerken** om de OpenAPI-indeling te bewerken. Breng uw wijzigingen aan en selecteer vervolgens **Bestand** > **Opslaan**.

## <a name="apply-policies-to-the-api"></a>Beleid toepassen op de API 

API Management biedt [beleidsregels](api-management-policies.md) die u kunt configureren voor uw API's. Beleidsregels zijn een verzameling instructies die sequentieel worden uitgevoerd op de aanvraag of het antwoord van een API. Beleidsregels kunnen globaal zijn. Ze zijn dan van toepassing op alle API's in uw API Management-exemplaar. Beleidsregels kunnen ook worden toegewezen aan een specifieke API of API-bewerking.

In deze sectie ziet u hoe u bepaalde uitgaande beleidsregels kunt toepassen op een API waardoor de API-antwoord wordt getransformeerd. Op basis van de beleidsregels in dit voorbeeld wordt de actiekoptekst gewijzigd, en worden oorspronkelijke back-end-URL's in de antwoordkoptekst verborgen.

1. Selecteer in het deelvenster Verkenner de optie **Beleid** onder de *demo-conference-api* die u hebt geïmporteerd. Het beleidsbestand wordt geopend in het editorvenster. Met dit bestand worden beleidsregels geconfigureerd voor alle bewerkingen in de API. 

1. Werk het bestand bij met de volgende inhoud in het element `<outbound>`:
    ```html
    [...]
    <outbound>
        <set-header name="Custom" exists-action="override">
            <value>"My custom value"</value>
        </set-header>
        <set-header name="X-Powered-By" exists-action="delete" />
        <redirect-content-urls />
        <base />
    </outbound>
    [...]
    ```

    * Op basis van het eerste `set-header`-beleid wordt een aangepaste antwoordkoptekst toegevoegd voor demonstratiedoeleinden.
    * Op basis van het tweede `set-header`-beleid wordt de koptekst **X-Powered-By** verwijderd, als deze bestaat. Met deze koptekst kan het toepassingsframework worden onthuld dat is gebruikt in de API-back-end. Uitgevers verwijderen deze vaak.
    * Op basis van `redirect-content-urls`-beleid worden koppelingen (maskers) opnieuw geschreven, zodat ze verwijzen naar de equivalente koppelingen via de API Management-gateway.
    
1. Sla het bestand op. Selecteer **Uploaden**, als u hierom wordt gevraagd, om het bestand te uploaden naar de cloud.

## <a name="test-the-api"></a>De API testen

### <a name="get-the-subscription-key"></a>De abonnementssleutel ophalen

Als u de geïmporteerde API en de toegepaste beleidsregels wilt testen, hebt u een abonnementssleutel nodig voor uw API Management-exemplaar.

1. Klik in het deelvenster Verkenner met de rechtermuisknop op de naam van uw API Management-exemplaar.
1. Selecteer **Abonnementssleutel kopiëren**.

    :::image type="content" source="media/visual-studio-code-tutorial/copy-subscription-key.png" alt-text="Abonnementssleutel kopiëren":::

### <a name="test-an-api-operation"></a>Een API-bewerking testen

1. Vouw in het deelvenster Verkenner het knooppunt **Bewerkingen** uit onder de *demo-conference-api* die u hebt geïmporteerd.
1. Selecteer een bewerking zoals *GetSpeakers* en klik vervolgens met de rechtermuisknop op de bewerking en **selecteer Testbewerking.**
1. Vervang in het editorvenster, naast **Ocp-Apim-Subscription-Key**, `{{SubscriptionKey}}` door de abonnementssleutel die u hebt gekopieerd.
1. Selecteer **Verzoek verzenden**. 

:::image type="content" source="media/visual-studio-code-tutorial/test-api.png" alt-text="Een API-aanvraag verzenden vanuit Visual Studio Code":::

Wanneer de aanvraag is geslaagd, reageert de back-end met **200 OK** en enkele gegevens.

:::image type="content" source="media/visual-studio-code-tutorial/test-api-policies.png" alt-text="API-testbewerking":::

U ziet de volgende details in het antwoord:
* De koptekst **Aangepast** wordt toegevoegd aan het antwoord.
* De koptekst **X-Powered-By** wordt niet weergegeven in het antwoord.
* URL's naar de API-back-end worden omgeleid naar de API Management-gateway, in dit geval `https://apim-hello-world.azure-api.net/demo-conference-api`.

### <a name="trace-the-api-operation"></a>De API-bewerking traceren

Voor gedetailleerde traceringsgegevens om u te helpen fouten in de API-bewerking op te sporen, selecteert u de koppeling die wordt weergegeven naast **Ocp-APIM-Trace-Location**. 

Het JSON-bestand op deze locatie bevat traceringsgegevens voor Inkomend, Back-end en Uitgaand, zodat u kunt bepalen waar zich eventueel problemen voordoen nadat de aanvraag is gedaan.

> [!TIP]
> Wanneer u de API-bewerkingen test, is in de API Management-extensie optionele [foutopsporing voor beleid](api-management-debug-policies.md) toegestaan (beschikbaar in de servicelaag voor ontwikkelaars).

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder het API Management-exemplaar als u dit niet meer nodig hebt, door met de rechtermuisknop te klikken en **Openen in portal** te selecteren om de [API Management-service](get-started-create-service-instance.md#clean-up-resources) en de bijbehorende resourcegroep te verwijderen.

U kunt ook **API Management verwijderen** selecteren om alleen het API Management-exemplaar te verwijderen (met deze bewerking wordt de resourcegroep niet verwijderd).

:::image type="content" source="media/visual-studio-code-tutorial/vscode-apim-delete.png" alt-text="API Management-exemplaar uit VS Code verwijderen":::

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie zijn verschillende functies van de API Management-extensie voor Visual Studio Code geïntroduceerd die u kunt gebruiken om API's te importeren en te beheren. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een API importeren in API Management
> * De API bewerken
> * API Management-beleid toepassen
> * De API testen

De API Management-extensie biedt extra functies om te werken met uw API's. Bijvoorbeeld [foutopsporing voor beleid](api-management-debug-policies.md) (beschikbaar in de servicelaag voor ontwikkelaars), of het maken en beheren van [benoemde waarden](api-management-howto-properties.md).
