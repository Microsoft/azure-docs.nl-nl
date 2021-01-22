---
title: Een functie in azure instellen voor het verwerken van gegevens
titleSuffix: Azure Digital Twins
description: Zie een functie maken in azure die kan worden geopend en geactiveerd door Digital apparaatdubbels.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 04ca8d515dbc5a28a7d3a30369d97877928c9dc1
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683861"
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

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio: dialoog venster Nieuw project":::

Geef een naam op voor de functie-app en selecteer _maken_.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio: nieuw project configureren":::

Selecteer het type functie-app van *Event grid trigger* en selecteer _maken_.

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="Visual Studio: dialoog venster Azure Functions project activeren":::

Als uw functie-app is gemaakt, wordt in Visual Studio een code voorbeeld gegenereerd in een **Function1.cs** -bestand in de projectmap. Deze korte functie wordt gebruikt om gebeurtenissen te registreren.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Visual Studio: Project venster met voorbeeld code":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>Een functie schrijven met een Event Grid trigger

U kunt een functie schrijven door de SDK toe te voegen aan uw functie-app. De functie-app communiceert met Azure Digital Apparaatdubbels met behulp van de [Azure Digital APPARAATDUBBELS SDK voor .net (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 

Als u de SDK wilt gebruiken, moet u de volgende pakketten in uw project toevoegen. U kunt de pakketten installeren met behulp van de NuGet package manager van Visual Studio of de pakketten toevoegen met behulp `dotnet` van een opdracht regel programma. Volg de onderstaande stappen voor uw voorkeurs methode.

**Optie 1. Pakketten toevoegen met behulp van Visual Studio Package Manager:**
    
Klik met de rechter muisknop op uw project en selecteer _NuGet-pakketten beheren_ in de lijst. In het venster dat wordt geopend, selecteert u het tabblad _Bladeren_ en zoekt u naar de volgende pakketten. Selecteer _installeren_ en _Accepteer_ de gebruiksrecht overeenkomst om de pakketten te installeren.

* `Azure.DigitalTwins.Core`
* `Azure.Identity`
* `System.Net.Http`
* `Azure.Core`

**Optie 2. Pakketten toevoegen met behulp van `dotnet` het opdracht regel programma:**

U kunt ook de volgende `dotnet add` opdrachten gebruiken in een opdracht regel programma:

```cmd/sh
dotnet add package Azure.DigitalTwins.Core
dotnet add package Azure.Identity
dotnet add package System.Net.Http
dotnet add package Azure.Core
```

Open vervolgens in uw Visual Studio Solution Explorer het _Function1.cs_ -bestand met voorbeeld code en voeg de volgende `using` instructies toe aan uw functie. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Verificatie code toevoegen aan de functie

U declareert nu variabelen op klasseniveau en voegt een verificatie code toe waarmee de functie toegang kan krijgen tot Azure Digital Apparaatdubbels. U voegt het volgende toe aan de functie in het _Function1.cs_ -bestand.

* Code voor het lezen van de URL van de Azure Digital Apparaatdubbels-service als een omgevings variabele. Het is een goed idee om de service-URL te lezen uit een omgevings variabele in plaats van deze op te harden in de functie.

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

## <a name="set-up-security-access-for-the-function-app"></a>Beveiligings toegang instellen voor de functie-app

U kunt de beveiligings toegang voor de functie-app instellen met behulp van de Azure CLI of de Azure Portal. Volg de onderstaande stappen voor uw voorkeurs optie.

### <a name="option-1-set-up-security-access-for-the-function-app-using-cli"></a>Optie 1: beveiligings toegang instellen voor de functie-app met behulp van CLI

De functie skelet van eerdere voor beelden vereist dat er een Bearer-token wordt door gegeven, zodat het kan worden geverifieerd met Azure Digital Apparaatdubbels. Als u er zeker van wilt zijn dat dit Bearer-token is door gegeven, moet u [Managed Service Identity (MSI)](../active-directory/managed-identities-azure-resources/overview.md) instellen voor de functie-app. U hoeft dit slechts één keer te doen voor elke functie-app.

U kunt door het systeem beheerde identiteit maken en de identiteit van de functie-app toewijzen aan de rol _**Azure Digital Apparaatdubbels data owner**_ voor uw Azure Digital apparaatdubbels-instantie. Hiermee geeft u de functie-app toestemming in het exemplaar voor het uitvoeren van activiteiten voor gegevens vlak. Vervolgens stelt u de URL van het Azure Digital Apparaatdubbels-exemplaar toegankelijk te maken voor uw functie door een omgevings variabele in te stellen.

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

Gebruik [Azure Cloud shell](https://shell.azure.com) om de opdrachten uit te voeren.

Gebruik de volgende opdracht om de door het systeem beheerde identiteit te maken. Noteer het veld _principalId_ in de uitvoer.

```azurecli-interactive 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
Gebruik de waarde _principalId_ in de volgende opdracht om de identiteit van de functie-app toe te wijzen aan de rol _Gegevenseigenaar van Azure Digital Twins_ voor uw Azure Digital Twins-exemplaar.

```azurecli-interactive 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```
Ten slotte kunt u de URL van uw Azure Digital Apparaatdubbels-exemplaar toegankelijk maken voor uw functie door een omgevings variabele in te stellen. Zie [*omgevings variabelen*](/sandbox/functions-recipes/environment-variables)voor meer informatie over het instellen van omgevings variabelen. 

> [!TIP]
> De URL van het Azure Digital Apparaatdubbels-exemplaar wordt gemaakt door *https://* toe te voegen aan het begin van de *hostnaam* van uw Azure Digital apparaatdubbels-exemplaar. Als u de hostnaam, samen met alle eigenschappen van uw exemplaar, wilt zien, kunt u uitvoeren `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-function-app-using-azure-portal"></a>Optie 2: beveiligings toegang instellen voor de functie-app met behulp van Azure Portal

Met een door het systeem toegewezen beheerde identiteit kunnen Azure-bronnen worden geverifieerd bij Cloud Services (bijvoorbeeld Azure Key Vault) zonder dat referenties in code worden opgeslagen. Wanneer deze functie is ingeschakeld, kunnen alle benodigde machtigingen worden verleend via Azure Role-based-Access-Control. De levens cyclus van dit type beheerde identiteit is gekoppeld aan de levens cyclus van deze resource. Daarnaast kan elke resource (bijvoorbeeld virtuele machine) slechts één door het systeem toegewezen beheerde identiteit hebben.

Zoek in de [Azure Portal](https://portal.azure.com/)op de zoek balk naar _functie-app_ met de naam van de functie-app die u eerder hebt gemaakt. Selecteer de *functie-app* in de lijst. 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Azure Portal: zoek functie-app":::

Selecteer in het venster functie-app _identiteit_ in de navigatie balk aan de linkerkant om beheerde identiteit in te scha kelen.
Schakel onder _systeem toegewezen_ tabblad de _status_ in op aan en _Sla_ deze op. Er wordt een pop-up weer gegeven om door het _systeem toegewezen beheerde identiteit in te scha kelen_.
Selecteer de knop _Ja_ . 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Azure Portal: door systeem beheerde identiteit inschakelen":::

U kunt in de meldingen controleren dat de functie is geregistreerd bij Azure Active Directory.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Azure Portal: meldingen":::

Noteer ook de **object-id** die wordt weer gegeven op de pagina _identiteit_ , zoals deze wordt gebruikt in de volgende sectie.

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="De object-ID kopiëren die u in de toekomst wilt gebruiken":::

### <a name="assign-access-roles-using-azure-portal"></a>Toegangs rollen toewijzen met behulp van Azure Portal

Selecteer de knop _Azure Role Assignments_ , waarmee de pagina met *Azure-roltoewijzingen* wordt geopend. Selecteer vervolgens _+ roltoewijzing toevoegen (preview)_.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Azure Portal: roltoewijzing toevoegen":::

Selecteer op de pagina _roltoewijzing toevoegen (preview)_ die wordt geopend:

* _Bereik_: resourcegroep
* _Abonnement_: Selecteer uw Azure-abonnement
* _Resource groep_: Selecteer de resource groep in de vervolg keuzelijst
* _Rol_: Selecteer de _Azure Digital Apparaatdubbels-gegevens eigenaar_ uit de vervolg keuzelijst

Sla uw gegevens vervolgens op door te klikken op de knop _Opslaan_ .

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Azure Portal: roltoewijzing toevoegen (preview) ":::

### <a name="configure-application-settings-using-azure-portal"></a>Toepassings instellingen configureren met behulp van Azure Portal

U kunt de URL van uw Azure Digital Apparaatdubbels-exemplaar toegankelijk maken voor uw functie door een omgevings variabele in te stellen. Zie [*omgevings variabelen*](/sandbox/functions-recipes/environment-variables)voor meer informatie. Toepassings instellingen worden weer gegeven als omgevings variabelen voor toegang tot het digitale apparaatdubbels-exemplaar. 

Als u een omgevings variabele met de URL van uw exemplaar wilt instellen, moet u eerst de URL ophalen door de hostnaam van het Azure Digital Apparaatdubbels-exemplaar te vinden. Zoek in de zoek balk van [Azure Portal](https://portal.azure.com) naar uw instantie. Selecteer vervolgens _overzicht_ op de linkernavigatiebalk om de _hostnaam_ weer te geven. Kopieer deze waarde.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Azure Portal: overzicht-> de hostnaam kopiëren voor gebruik in het veld _Value_.":::

U kunt nu een toepassings instelling maken aan de hand van de volgende stappen:

1. Zoek naar uw app met behulp van de naam van de functie-app in de zoek balk en selecteer de functie-app in de lijst
1. Selecteer _configuratie_ op de navigatie balk aan de linkerkant om een nieuwe toepassings instelling te maken
1. Selecteer op het tabblad _Toepassings instellingen_ _+ nieuwe toepassings instelling_

:::image type="content" source="media/how-to-create-azure-function/search-for-azure-function.png" alt-text="Azure Portal: zoeken naar een bestaande functie-app" lightbox="media/how-to-create-azure-function/search-for-azure-function.png":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Azure Portal: toepassings instellingen configureren":::

In het venster dat wordt geopend, gebruikt u de waarde voor de hostnaam gekopieerd hierboven om een toepassings instelling te maken.
* _Naam_ : ADT_SERVICE_URL
* _Waarde_: https://{Your-Azure-Digital-apparaatdubbels-host-name}

Selecteer _OK_ om een toepassings instelling te maken.

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Azure Portal: toepassings instellingen toevoegen.":::

U kunt de toepassings instellingen weer geven onder het veld _naam_ in de naam van de toepassing. Sla de toepassings instellingen vervolgens op door de knop _Opslaan_ te selecteren.

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Azure Portal: de toepassing weer geven die is gemaakt en de toepassing opnieuw starten":::

Voor wijzigingen in de toepassings instellingen moet de toepassing opnieuw worden gestart. Selecteer _door gaan_ om de toepassing opnieuw op te starten.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Azure Portal: toepassings instellingen opslaan":::

U kunt zien dat de toepassings instellingen worden bijgewerkt door het pictogram _meldingen_ te selecteren. Als uw toepassings instelling niet is gemaakt, kunt u opnieuw proberen een toepassings instelling toe te voegen door het bovenstaande proces te volgen.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Azure Portal: meldingen voor het bijwerken van toepassings instellingen":::

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u de stappen voor het instellen van een functie-app in azure gevolgd voor gebruik met Azure Digital Apparaatdubbels.

Bekijk vervolgens hoe u de basis functie bouwt om IoT Hub gegevens op te nemen in azure Digital Apparaatdubbels:
* [*Instructies: telemetrie opnemen van IoT Hub*](how-to-ingest-iot-hub-data.md)
