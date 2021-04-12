---
title: Een functie in azure instellen voor het verwerken van gegevens
titleSuffix: Azure Digital Twins
description: Zie een functie maken in azure die kan worden geopend en geactiveerd door Digital apparaatdubbels.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f1ed4b9beda9848bba8fb12783f49dcf8016d3dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104590616"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Functie-apps in azure verbinden voor het verwerken van gegevens

Het bijwerken van digitale apparaatdubbels op basis van gegevens wordt verwerkt met behulp van [**gebeurtenis routes**](concepts-route-events.md) via Compute-resources, zoals een functie die is gemaakt met behulp van [Azure functions](../azure-functions/functions-overview.md). Functies kunnen worden gebruikt voor het bijwerken van een digitale dubbele in reactie op:
* telemetrie-gegevens van apparaten die afkomstig zijn van IoT Hub
* eigenschaps wijziging of andere gegevens die afkomstig zijn van een andere digitale twee in het dubbele diagram

Dit artikel helpt u bij het maken van een functie in azure voor gebruik met Azure Digital Apparaatdubbels. 

Hier volgt een overzicht van de stappen die het bevat:

1. Een Azure Functions-project in Visual Studio maken
2. Een functie schrijven met een [Event grid](../event-grid/overview.md) trigger
3. Voeg verificatie code toe aan de functie (om toegang te krijgen tot Azure Digital Apparaatdubbels)
4. De functie-app publiceren naar Azure
5. [Beveiligings](concepts-security.md) toegang instellen voor de functie-app

## <a name="prerequisite-set-up-azure-digital-twins-instance"></a>Voor waarde: Stel het Azure Digital Apparaatdubbels-exemplaar in

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Een functie-app maken in Visual Studio

Selecteer in Visual Studio 2019 _bestand > nieuw > project_ en zoek naar de _Azure functions_ sjabloon. Selecteer _Next_.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Scherm opname van Visual Studio waarin het dialoog venster Nieuw project wordt weer gegeven. De Azure Functions project sjabloon is gemarkeerd.":::

Geef een naam op voor de functie-app en selecteer _maken_.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Scherm opname van Visual Studio waarin het dialoog venster wordt weer gegeven voor het configureren van een nieuw project, met inbegrip van de project naam, de locatie van de opslag, het maken van een nieuwe oplossing en de naam van de oplossing.":::

Selecteer het type functie-app van *Event grid trigger* en selecteer _maken_.

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="Scherm opname van Visual Studio waarin het dialoog venster wordt weer gegeven voor het maken van een nieuwe Azure Functions-toepassing. De optie Trigger Event Grid is gemarkeerd.":::

Als uw functie-app is gemaakt, wordt in Visual Studio een code voorbeeld gegenereerd in een **Function1. cs** -bestand in de projectmap. Deze korte functie wordt gebruikt om gebeurtenissen te registreren.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Scherm opname van Visual Studio in het project venster voor het nieuwe project dat is gemaakt. Er is code voor een voorbeeld functie met de naam Function1." lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>Een functie schrijven met een Event Grid trigger

U kunt een functie schrijven door de SDK toe te voegen aan uw functie-app. De functie-app communiceert met Azure Digital Apparaatdubbels met behulp van de [Azure Digital APPARAATDUBBELS SDK voor .net (C#)](/dotnet/api/overview/azure/digitaltwins/client). 

Als u de SDK wilt gebruiken, moet u de volgende pakketten in uw project toevoegen. U kunt de pakketten installeren met behulp van de NuGet package manager van Visual Studio of de pakketten toevoegen met behulp `dotnet` van een opdracht regel programma.

* [Azure. DigitalTwins. core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure. Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System .net. http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure. core](https://www.nuget.org/packages/Azure.Core/)

Open vervolgens in uw Visual Studio Solution Explorer het bestand _Function1. cs_ waarin u voorbeeld code hebt en voeg de volgende `using` instructies voor deze pakketten toe aan uw functie.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Verificatie code toevoegen aan de functie

U declareert nu variabelen op klasseniveau en voegt een verificatie code toe waarmee de functie toegang kan krijgen tot Azure Digital Apparaatdubbels. U voegt het volgende toe aan uw functie in het bestand _Function1. cs_ .

* Code voor het lezen van de URL van de Azure Digital Apparaatdubbels-service als een **omgevings variabele**. Het is een goed idee om de service-URL te lezen uit een omgevings variabele in plaats van deze op te harden in de functie. [Verderop in dit artikel](#set-up-security-access-for-the-function-app)moet u de waarde van deze omgevings variabele instellen. Zie [*uw functie-app beheren*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)voor meer informatie over omgevings variabelen.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* Een statische variabele die een httpclient maakt-exemplaar kan bevatten. Httpclient maakt is relatief kostbaar en we willen voor komen dat u dit hoeft te doen voor elke functie aanroep.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* U kunt de referenties van de beheerde identiteit gebruiken in Azure Functions.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* Voeg in uw functie een lokale variabele _DigitalTwinsClient_ toe om uw Azure Digital apparaatdubbels-client exemplaar te bewaren. Maak deze variabele *niet* statisch in uw klasse.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* Voeg een null-controle toe voor _adtInstanceUrl_ en verpakken de functie logica in een try/catch-blok om eventuele uitzonde ringen te ondervangen.

Na deze wijzigingen wordt de functie code op de volgende manier weer gegeven:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Nu uw toepassing is geschreven, kunt u deze publiceren naar Azure met behulp van de stappen in de volgende sectie.

## <a name="publish-the-function-app-to-azure"></a>De functie-app publiceren naar Azure

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

### <a name="verify-function-publish"></a>Functie publiceren controleren

1. Meld u aan met uw referenties in de [Azure Portal](https://portal.azure.com/).
2. Zoek in de zoek balk boven in het venster naar de naam van de **functie-app**.

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="Zoek naar de functie-app met de naam in de Azure Portal." lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. Op de pagina *functie-app* die wordt geopend, kiest u *functies* in de menu opties aan de linkerkant. Als de functie is gepubliceerd, ziet u de naam van de functie in de lijst.
Mogelijk moet u enkele minuten wachten of de pagina paar keren vernieuwen voordat u de functie ziet die wordt weer gegeven in de lijst gepubliceerde functies.

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="Bekijk gepubliceerde functies in de Azure Portal." lightbox="media/how-to-create-azure-function/view-published-functions.png":::

Als uw functie-app toegang moet hebben tot Azure Digital Apparaatdubbels, moet er een door het systeem beheerde identiteit zijn met machtigingen voor toegang tot uw Azure Digital Apparaatdubbels-instantie. Daarna stelt u dat in.

## <a name="set-up-security-access-for-the-function-app"></a>Beveiligings toegang instellen voor de functie-app

U kunt de beveiligings toegang voor de functie-app instellen met behulp van de Azure CLI of de Azure Portal. Volg de onderstaande stappen voor uw voorkeurs optie.

# <a name="cli"></a>[CLI](#tab/cli)

U kunt deze opdrachten uitvoeren in [Azure Cloud shell](https://shell.azure.com) of een [lokale Azure cli-installatie](/cli/azure/install-azure-cli).
U kunt de door het systeem beheerde identiteit van de functie-app gebruiken om aan te geven dat de rol _**Azure Digital Apparaatdubbels data owner**_ is voor uw Azure Digital apparaatdubbels-exemplaar. Hiermee geeft u de functie-app toestemming in het exemplaar voor het uitvoeren van activiteiten voor gegevens vlak. Vervolgens stelt u de URL van het Azure Digital Apparaatdubbels-exemplaar toegankelijk te maken voor uw functie door een omgevings variabele in te stellen.

### <a name="assign-access-role"></a>Access-rol toewijzen

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

De functie skelet van eerdere voor beelden vereist dat er een Bearer-token wordt door gegeven, zodat het kan worden geverifieerd met Azure Digital Apparaatdubbels. Om ervoor te zorgen dat dit Bearer-token wordt door gegeven, moet u [Managed Service Identity (MSI)-](../active-directory/managed-identities-azure-resources/overview.md) machtigingen instellen voor de functie-app voor toegang tot Azure Digital apparaatdubbels. U hoeft dit slechts één keer te doen voor elke functie-app.


1. Gebruik de volgende opdracht om de details van de door het systeem beheerde identiteit voor de functie weer te geven. Noteer het veld _principalId_ in de uitvoer.

    ```azurecli-interactive 
    az functionapp identity show -g <your-resource-group> -n <your-App-Service-(function-app)-name> 
    ```

    >[!NOTE]
    > Als het resultaat leeg is in plaats van de details van een identiteit weer te geven, maakt u met behulp van deze opdracht een nieuwe door het systeem beheerde identiteit voor de functie:
    > 
    >```azurecli-interactive    
    >az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>  
    >```
    >
    > In de uitvoer worden vervolgens de details van de identiteit weer gegeven, met inbegrip van de _principalId_ -waarde die is vereist voor de volgende stap. 

1. Gebruik de waarde _principalId_ in de volgende opdracht om de identiteit van de functie-app toe te wijzen aan de rol _Gegevenseigenaar van Azure Digital Twins_ voor uw Azure Digital Twins-exemplaar.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

### <a name="configure-application-settings"></a>Toepassingsinstellingen configureren

Maak ten slotte de URL van uw Azure Digital Apparaatdubbels-exemplaar toegankelijk voor uw functie door een **omgevings variabele** voor de instantie in te stellen. Zie [*uw functie-app beheren*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)voor meer informatie over omgevings variabelen. 

> [!TIP]
> De URL van het Azure Digital Apparaatdubbels-exemplaar wordt gemaakt door *https://* toe te voegen aan het begin van de *hostnaam* van uw Azure Digital apparaatdubbels-exemplaar. Als u de hostnaam, samen met alle eigenschappen van uw exemplaar, wilt weer geven, kunt u uitvoeren `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Voer de volgende stappen uit in de [Azure Portal](https://portal.azure.com/).

### <a name="assign-access-role"></a>Access-rol toewijzen

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Met een door het systeem toegewezen beheerde identiteit kunnen Azure-bronnen worden geverifieerd bij Cloud Services (bijvoorbeeld Azure Key Vault) zonder dat referenties in code worden opgeslagen. Wanneer deze functie is ingeschakeld, kunnen alle benodigde machtigingen worden verleend via toegangs beheer op basis van rollen. De levens cyclus van dit type beheerde identiteit is gekoppeld aan de levens cyclus van deze resource. Daarnaast kan elke resource slechts één door het systeem toegewezen beheerde identiteit hebben.

1. Zoek in het [Azure Portal](https://portal.azure.com/)naar uw functie-app door de naam ervan in de zoek balk te typen. Selecteer uw app in de resultaten. 

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Scherm afbeelding van de Azure Portal: de naam van de functie-app wordt doorzocht in de zoek balk van de portal en het Zoek resultaat is gemarkeerd.":::

1. Selecteer op de pagina functie-app de optie _identiteit_ in de navigatie balk aan de linkerkant om te werken met een beheerde identiteit voor de functie. Controleer op de pagina _systeem toegewezen_ of de _status_ is ingesteld op aan (indien dit niet het geval is, nu instellen en *Sla* de wijziging **op** ).

    :::image type="content" source="media/how-to-create-azure-function/verify-system-managed-identity.png" alt-text="Scherm opname van de Azure Portal: op de pagina identiteit voor de functie-app, is de optie status ingesteld op aan." lightbox="media/how-to-create-azure-function/verify-system-managed-identity.png":::

1. Selecteer de knop _Azure Role Assignments_ , waarmee de pagina met *Azure-roltoewijzingen* wordt geopend.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-1.png" alt-text="Scherm afbeelding van de Azure Portal: een markering voor de knop Azure-functie toewijzingen onder machtigingen op de pagina identiteit van Azure function." lightbox="media/how-to-create-azure-function/add-role-assignment-1.png":::

    Selecteer _+ roltoewijzing toevoegen (preview)_.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-2.png" alt-text="Scherm afbeelding van de Azure Portal: een hooglicht rond + roltoewijzing toevoegen (preview) op de pagina met Azure-functies." lightbox="media/how-to-create-azure-function/add-role-assignment-2.png":::

1. Selecteer op de pagina _roltoewijzing toevoegen (preview)_ die wordt geopend de volgende waarden:

    * **Bereik**: resourcegroep
    * **Abonnement**: Selecteer uw Azure-abonnement
    * **Resource groep**: Selecteer de resource groep in de vervolg keuzelijst
    * **Rol**: Selecteer de _Azure Digital Apparaatdubbels-gegevens eigenaar_ uit de vervolg keuzelijst

    Sla uw gegevens vervolgens op door te klikken op de knop _Opslaan_ .

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-3.png" alt-text="Scherm afbeelding van het dialoog venster Azure Portal: om een nieuwe roltoewijzing toe te voegen (preview). Er zijn velden voor het bereik, het abonnement, de resource groep en de rol.":::

### <a name="configure-application-settings"></a>Toepassingsinstellingen configureren

Als u de URL van uw Azure Digital Apparaatdubbels-exemplaar toegankelijk wilt maken voor uw functie, kunt u een **omgevings variabele** voor de instantie instellen. Zie [*uw functie-app beheren*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)voor meer informatie over omgevings variabelen. Toepassings instellingen worden weer gegeven als omgevings variabelen voor toegang tot het Azure Digital Apparaatdubbels-exemplaar. 

Als u een omgevings variabele met de URL van uw exemplaar wilt instellen, moet u eerst de URL ophalen door de hostnaam van het Azure Digital Apparaatdubbels-exemplaar te vinden. Zoek in de zoek balk van [Azure Portal](https://portal.azure.com) naar uw instantie. Selecteer vervolgens _overzicht_ op de linkernavigatiebalk om de _hostnaam_ weer te geven. Kopieer deze waarde.

:::image type="content" source="media/how-to-create-azure-function/instance-host-name.png" alt-text="Scherm afbeelding van de Azure Portal: op de pagina overzicht voor het Azure Digital Apparaatdubbels-exemplaar wordt de waarde voor de hostnaam gemarkeerd.":::

U kunt nu een toepassings instelling maken met behulp van de volgende stappen:

1. Zoek naar uw functie-app in de zoek balk van de portal en selecteer deze in de resultaten.

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Scherm afbeelding van de Azure Portal: de naam van de functie-app wordt doorzocht in de zoek balk van de portal en het Zoek resultaat is gemarkeerd.":::

1. Selecteer _configuratie_ op de navigatie balk aan de linkerkant. Klik op het tabblad _Toepassings instellingen_ en selecteer _+ nieuwe toepassings instelling_.

    :::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Scherm afbeelding van de Azure Portal: op de pagina configuratie voor de functie-app wordt de knop voor het maken van een nieuwe toepassings instelling gemarkeerd.":::

1. In het venster dat wordt geopend, gebruikt u de waarde voor de hostnaam gekopieerd hierboven om een toepassings instelling te maken.
    * **Naam**: ADT_SERVICE_URL
    * **Waarde**: https://{Your-Azure-Digital-apparaatdubbels-host-name}
    
    Selecteer _OK_ om een toepassings instelling te maken.
    
    :::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Scherm afbeelding van de Azure Portal: de knop OK wordt gemarkeerd na het invullen van de velden naam en waarde op de pagina Toepassings instelling toevoegen/bewerken.":::

1. Nadat u de instelling hebt gemaakt, wordt deze weer gegeven op het tabblad _Toepassings instellingen_ . Controleer of *ADT_SERVICE_URL* wordt weer gegeven in de lijst en sla de nieuwe toepassings instelling op door de knop _Opslaan_ te selecteren.

    :::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Scherm afbeelding van de Azure Portal: de pagina Toepassings instellingen, met de instelling nieuwe ADT_SERVICE_URL gemarkeerd. De knop Opslaan is ook gemarkeerd.":::

1. Voor wijzigingen in de toepassings instellingen moet de toepassing opnieuw worden opgestart, dus Selecteer _door gaan_ om de toepassing opnieuw op te starten wanneer u hierom wordt gevraagd.

    :::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Scherm afbeelding van de Azure Portal: er is een melding dat er wijzigingen in de toepassings instellingen zijn aangebracht met het opnieuw opstarten van de toepassing. De knop door gaan is gemarkeerd.":::

---

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u de stappen voor het instellen van een functie-app in azure gevolgd voor gebruik met Azure Digital Apparaatdubbels.

Bekijk vervolgens hoe u de basis functie bouwt om IoT Hub gegevens op te nemen in azure Digital Apparaatdubbels:
* [*Instructies: telemetrie opnemen van IoT Hub*](how-to-ingest-iot-hub-data.md)
