---
title: Een functie instellen in Azure om gegevens te verwerken
titleSuffix: Azure Digital Twins
description: Bekijk hoe u een functie in Azure maakt die toegang heeft tot en kan worden geactiveerd door digitale tweelingen.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5dddaabf47a261f741b3b1cb8d3319d589c4e474
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480727"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Functie-apps verbinden in Azure voor het verwerken van gegevens

Digitale tweelingen kunnen worden bijgewerkt op basis van gegevens met behulp van [gebeurtenisroutes](concepts-route-events.md) via rekenbronnen. Een functie die wordt gemaakt met behulp van Azure Functions [kan](../azure-functions/functions-overview.md) bijvoorbeeld een digitale tweeling bijwerken als reactie op:
* Telemetriegegevens van apparaten van Azure IoT Hub.
* Een eigenschapswijziging of andere gegevens van een andere digitale tweeling in de tweelinggrafiek.

In dit artikel wordt beschreven hoe u een functie maakt in Azure voor gebruik met Azure Digital Twins. Als u een functie wilt maken, volgt u deze basisstappen:

1. Maak een Azure Functions-project in Visual Studio.
2. Schrijf een functie met een [Azure Event Grid](../event-grid/overview.md) trigger.
3. Voeg verificatiecode toe aan de functie, zodat u toegang hebt tot Azure Digital Twins.
4. Publiceer de functie-app naar Azure.
5. Stel beveiliging [in](concepts-security.md) voor de functie-app.

## <a name="prerequisite-set-up-azure-digital-twins"></a>Vereiste: Een Azure Digital Twins

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Een functie-app maken in Visual Studio

Selecteer Visual Studio 2019 Bestand   >  **Nieuw**  >  **project.** Zoek naar de **Azure Functions** sjabloon. Selecteer **Next**.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Schermopname van Visual Studio met het dialoogvenster nieuw project. De Azure Functions is gemarkeerd.":::

Geef een naam op voor de functie-app en selecteer __vervolgens Maken.__

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Schermopname van Visual Studio met het dialoogvenster voor het configureren van een nieuw project. Instellingen zijn onder andere projectnaam, op te slaan locatie, keuze voor het maken van een nieuwe oplossing en naam van de oplossing.":::

Selecteer het type functie-app **Event Grid trigger** en selecteer vervolgens __Maken.__

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="Schermopname van Visual Studio met het dialoogvenster voor het maken van een nieuwe Azure Functions toepassing. De Event Grid triggeroptie is gemarkeerd.":::

Nadat uw functie-app is gemaakt, Visual Studio een codevoorbeeld gegenereerd in een *Function1.cs-bestand* in de projectmap. Deze korte functie wordt gebruikt voor het logboeken van gebeurtenissen.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Schermopname van Visual Studio. Het projectvenster voor het nieuwe project wordt weergegeven. Code voor een voorbeeldfunctie wordt weergegeven in een bestand met de naam Function1." lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-that-has-an-event-grid-trigger"></a>Een functie schrijven met een Event Grid trigger

U kunt een functie schrijven door een SDK toe te voegen aan uw functie-app. De functie-app communiceert met Azure Digital Twins met behulp van [de Azure Digital Twins SDK voor .NET (C#).](/dotnet/api/overview/azure/digitaltwins/client) 

Als u de SDK wilt gebruiken, moet u de volgende pakketten in uw project opnemen. Installeer de pakketten met behulp van de Visual Studio NuGet-pakketbeheer. Of voeg de pakketten toe met behulp `dotnet` van in een opdrachtregelprogramma.

* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System.Net.Http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure.Core](https://www.nuget.org/packages/Azure.Core/)

Open vervolgens in Visual Studio Solution Explorer het bestand _Function1.cs_ dat uw voorbeeldcode bevat. Voeg de volgende `using` -instructies toe voor de pakketten.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Verificatiecode toevoegen aan de functie

Declareer nu variabelen op klasseniveau en voeg verificatiecode toe waarmee de functie toegang heeft tot Azure Digital Twins. Voeg de variabelen en code toe aan uw functie in het _bestand Function1.cs._

* **Code voor het lezen van Azure Digital Twins service-URL als een omgevingsvariabele.** Het is een goed idee om de service-URL te lezen uit een omgevingsvariabele in plaats van deze in de functie hard te coderen. Later in dit artikel stelt u de waarde van deze [omgevingsvariabele in.](#set-up-security-access-for-the-function-app) Zie Uw functie-app beheren voor meer informatie [over omgevingsvariabelen.](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* **Een statische variabele voor het gebruik van een HttpClient-exemplaar.** HttpClient is relatief duur om te maken, dus we willen voorkomen dat deze voor elke functieaanroep wordt gemaakt.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* **Referenties voor beheerde identiteit.**
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* **Een lokale variabele _DigitalTwinsClient._** Voeg de variabele toe in uw functie om uw client-Azure Digital Twins te houden. Maak *deze* variabele niet statisch binnen uw klasse.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* **Een null-controle _voor adtInstanceUrl_.** Voeg de null-controle toe en verpak uw functielogica in een try/catch-blok om eventuele uitzonderingen te ondervangen.

Na deze wijzigingen ziet uw functiecode eruit als in het volgende voorbeeld.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Nu uw toepassing is geschreven, kunt u deze publiceren naar Azure.

## <a name="publish-the-function-app-to-azure"></a>De functie-app publiceren naar Azure

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

### <a name="verify-the-publication-of-your-function"></a>De publicatie van uw functie controleren

1. Meld u aan met uw referenties in [de Azure Portal](https://portal.azure.com/).
2. Zoek in het zoekvak bovenaan het venster naar de naam van uw functie-app en selecteer deze.

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="Schermopname van de Azure Portal. Voer in het zoekveld de naam van de functie-app in." lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. Kies functies op de pagina **Functie-app** die wordt geopend in het menu aan de **linkerkant.** Als uw functie is gepubliceerd, wordt de naam ervan weergegeven in de lijst.

    > [!Note] 
    > Mogelijk moet u enkele minuten wachten of de pagina enkele keren vernieuwen voordat uw functie wordt weergegeven in de lijst met gepubliceerde functies.

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="Gepubliceerde functies weergeven in de Azure Portal." lightbox="media/how-to-create-azure-function/view-published-functions.png":::

Voor toegang Azure Digital Twins heeft uw functie-app een door het systeem beheerde identiteit nodig met machtigingen voor toegang tot uw Azure Digital Twins-exemplaar. U stelt dit vervolgens in.

## <a name="set-up-security-access-for-the-function-app"></a>Beveiligingstoegang instellen voor de functie-app

U kunt beveiligingstoegang instellen voor de functie-app met behulp van de Azure CLI of de Azure Portal. Volg de stappen voor de gewenste optie.

# <a name="cli"></a>[CLI](#tab/cli)

Voer deze opdrachten uit in [Azure Cloud Shell](https://shell.azure.com) of een [lokale Azure CLI-installatie.](/cli/azure/install-azure-cli)
U kunt de door het systeem beheerde identiteit van de functie-app gebruiken om deze de rol Azure Digital Twins **gegevenseigenaar te** geven voor uw Azure Digital Twins exemplaar. De rol geeft de functie-app in het exemplaar toestemming om gegevensvlakactiviteiten uit te voeren. Maak vervolgens de URL van het exemplaar toegankelijk voor uw functie door een omgevingsvariabele in te stellen.

### <a name="assign-an-access-role"></a>Een toegangsrol toewijzen

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Het geraamte van de functie in eerdere voorbeelden vereist dat er een Bearer-token aan wordt doorgegeven. Als het Bearer-token niet wordt doorgegeven, kan de functie-app niet worden geverifieerd met Azure Digital Twins. 

Om ervoor te zorgen dat het Bearer-token wordt doorgegeven, stelt u machtigingen voor beheerde identiteiten in, zodat de functie-app toegang heeft tot Azure Digital Twins. [](../active-directory/managed-identities-azure-resources/overview.md) U stelt deze machtigingen slechts één keer in voor elke functie-app.


1. Gebruik de volgende opdracht om de details van de door het systeem beheerde identiteit voor de functie te bekijken. Noteer het `principalId` veld in de uitvoer.

    ```azurecli-interactive 
    az functionapp identity show -g <your-resource-group> -n <your-App-Service-(function-app)-name> 
    ```

    >[!NOTE]
    > Als het resultaat leeg is in plaats van identiteitsdetails weer te geven, maakt u een nieuwe door het systeem beheerde identiteit voor de functie met behulp van deze opdracht:
    > 
    >```azurecli-interactive    
    >az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>  
    >```
    >
    > In de uitvoer worden details van de identiteit weergegeven, inclusief de `principalId` waarde die is vereist voor de volgende stap. 

1. Gebruik de waarde in de volgende opdracht om de identiteit van de functie-app toe te wijzen aan de rol Azure Digital Twins gegevenseigenaar voor uw `principalId` Azure Digital Twins exemplaar. 

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

### <a name="configure-application-settings"></a>Toepassingsinstellingen configureren

Maak de URL van uw exemplaar toegankelijk voor uw functie door er een omgevingsvariabele voor in te stellen. Zie Uw functie-app beheren voor meer informatie [over omgevingsvariabelen.](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal) 

> [!TIP]
> De AZURE DIGITAL TWINS van het exemplaar wordt gemaakt door https:// aan *het* begin van de hostnaam van uw exemplaar toe te voegen. Voer uit om de hostnaam en alle eigenschappen van uw exemplaar te `az dt show --dt-name <your-Azure-Digital-Twins-instance>` bekijken.

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Voltooi de volgende stappen in [Azure Portal](https://portal.azure.com/).

### <a name="assign-an-access-role"></a>Een toegangsrol toewijzen

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Met een door het systeem toegewezen beheerde identiteit kunnen Azure-resources worden geverifieerd bij cloudservices (bijvoorbeeld Azure Key Vault) zonder referenties in code op te slaan. Nadat u door het systeem toegewezen beheerde identiteit hebt ingeschakeld, kunnen alle benodigde machtigingen worden verleend via op rollen gebaseerd toegangsbeheer van Azure. 

De levenscyclus van dit type beheerde identiteit is gekoppeld aan de levenscyclus van deze resource. Daarnaast kan elke resource slechts één door het systeem toegewezen beheerde identiteit hebben.

1. Zoek in [Azure Portal](https://portal.azure.com/)functie-app door de naam ervan in het zoekvak te typen. Selecteer uw app in de resultaten. 

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Schermopname van de Azure Portal. De naam van de functie-app staat in de zoekbalk van de portal en het zoekresultaat is gemarkeerd.":::

1. Selecteer op de pagina functie-app in het menu aan de linkerkant __identiteit__ om te werken met een beheerde identiteit voor de functie. Controleer op __de pagina Systeem__ toegewezen of de __Status__ is ingesteld op **Aan.** Als dat niet het beste is, stelt u deze nu in en vervolgens **de wijziging** opslaan.

    :::image type="content" source="media/how-to-create-azure-function/verify-system-managed-identity.png" alt-text="Schermopname van de Azure Portal. Op de pagina Identiteit voor de functie-app is de optie Status ingesteld op Aan." lightbox="media/how-to-create-azure-function/verify-system-managed-identity.png":::

1. Selecteer __Azure-roltoewijzingen.__

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-1.png" alt-text="Schermopname van de Azure Portal. Op de pagina Identiteit van de Azure-functie wordt onder Machtigingen de knop Azure-roltoewijzingen gemarkeerd." lightbox="media/how-to-create-azure-function/add-role-assignment-1.png":::

    Selecteer __+ Roltoewijzing toevoegen (preview)__.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-2.png" alt-text="Schermopname van de Azure Portal. Op de pagina Azure-roltoewijzingen is de knop Roltoewijzing toevoegen (preview) gemarkeerd." lightbox="media/how-to-create-azure-function/add-role-assignment-2.png":::

1. Selecteer op __de pagina Roltoewijzing toevoegen (preview)__ de volgende waarden:

    * **Bereik:** _Resourcegroep_
    * **Abonnement**: Selecteer uw Azure-abonnement.
    * **Resourcegroep:** selecteer uw resourcegroep.
    * **Rol:** _Azure Digital Twins eigenaar van gegevens_

    Sla de details op door Opslaan __te selecteren.__

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-3.png" alt-text="Schermopname van de Azure Portal, waarin wordt weergegeven hoe u een nieuwe roltoewijzing toevoegt. Het dialoogvenster bevat velden voor Bereik, Abonnement, Resourcegroep en Rol.":::

### <a name="configure-application-settings"></a>Toepassingsinstellingen configureren

Als u de URL van uw Azure Digital Twins toegankelijk wilt maken voor uw functie, kunt u een omgevingsvariabele instellen. Toepassingsinstellingen worden als omgevingsvariabelen blootgesteld om toegang tot het Azure Digital Twins verlenen. Zie Uw functie-app beheren voor meer informatie [over omgevingsvariabelen.](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal) 

Als u een omgevingsvariabele wilt instellen met de URL van uw exemplaar, moet u eerst de hostnaam van uw exemplaar zoeken: 

1. Zoek uw exemplaar in de [Azure Portal](https://portal.azure.com). 
1. Selecteer Overzicht in het menu aan de __linkerkant.__ 
1. Kopieer de __waarde van Hostnaam.__

    :::image type="content" source="media/how-to-create-azure-function/instance-host-name.png" alt-text="Schermopname van de Azure Portal. Op de pagina Overzicht van het exemplaar is de waarde van de hostnaam gemarkeerd.":::

U kunt nu een toepassingsinstelling maken:

1. Zoek in de zoekbalk van de portal naar uw functie-app en selecteer deze in de resultaten.

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Schermopname van de Azure Portal. De naam van de functie-app wordt doorzocht in de zoekbalk van de portal. Het zoekresultaat is gemarkeerd.":::

1. Selecteer aan de linkerkant __Configuratie__. Selecteer vervolgens op __het tabblad Toepassingsinstellingen__ __de optie + Nieuwe toepassingsinstelling.__

    :::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Schermopname van de Azure Portal. Op het tabblad Configuratie voor de functie-app is de knop voor het maken van een nieuwe toepassingsinstelling gemarkeerd.":::

1. Gebruik in het venster dat wordt geopend de hostnaamwaarde die u hebt gekopieerd om een toepassingsinstelling te maken.
    * **Naam:** ADT_SERVICE_URL
    * **Waarde:** https://{your-azure-digital-twins-host-name}
    
    Selecteer __OK om__ een toepassingsinstelling te maken.
    
    :::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Schermopname van de Azure Portal. Op de pagina Toepassingsinstelling toevoegen/bewerken worden de velden Naam en Waarde ingevuld. De knop O K is gemarkeerd.":::

1. Nadat u de instelling hebt ingesteld, wordt deze weergegeven op het __tabblad Toepassingsinstellingen.__ Controleer of **ADT_SERVICE_URL** wordt weergegeven in de lijst. Sla vervolgens de nieuwe toepassingsinstelling op door __Opslaan te selecteren.__

    :::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Schermopname van de Azure Portal. Op het tabblad Toepassingsinstellingen is de nieuwe A D T SERVICE U R L-instelling gemarkeerd. De knop Opslaan is ook gemarkeerd.":::

1. Voor wijzigingen in de toepassingsinstellingen moet de toepassing opnieuw worden opgestart. Selecteer __daarom Doorgaan om__ de toepassing opnieuw op te starten wanneer u daarom wordt gevraagd.

    :::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Schermopname van de Azure Portal. Een opmerking geeft aan dat eventuele wijzigingen in de toepassingsinstellingen uw toepassing opnieuw starten. De knop Doorgaan is gemarkeerd.":::

---

## <a name="next-steps"></a>Volgende stappen

In dit artikel stelt u een functie-app in Azure in voor gebruik met Azure Digital Twins. Bekijk vervolgens hoe u verder kunt bouwen op uw basisfunctie om [uw IoT Hub op te nemen in Azure Digital Twins](how-to-ingest-iot-hub-data.md).
