---
title: Een functie in azure instellen voor het verwerken van gegevens
titleSuffix: Azure Digital Twins
description: Zie een functie maken in azure die kan worden geopend en geactiveerd door Digital apparaatdubbels.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 6f74f973abc33d809624bd8abd5a514a52ccfe70
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602705"
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

Selecteer in Visual Studio 2019 _bestand > nieuw > project_ en zoek naar de _Azure functions_ sjabloon en selecteer _volgende_.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio: dialoog venster Nieuw project":::

Geef een naam op voor de functie-app en selecteer _maken_.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio: nieuw project configureren":::

Selecteer het type functie-app *Event grid trigger* en selecteer _maken_.

:::image type="content" source="media/how-to-create-azure-function/eventgridtrigger-function.png" alt-text="Visual Studio: dialoog venster Azure Functions project activeren":::

Als uw functie-app is gemaakt, heeft uw Visual Studio automatisch een code voorbeeld in het **Function.cs** -bestand in de projectmap. Deze korte functie wordt gebruikt om gebeurtenissen te registreren.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Visual Studio: Project venster met voorbeeld code":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>Een functie schrijven met een Event Grid trigger

U kunt een functie schrijven door de SDK toe te voegen aan uw functie-app. De functie-app communiceert met Azure Digital Apparaatdubbels met behulp van de [Azure Digital APPARAATDUBBELS SDK voor .net (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 

Als u de SDK wilt gebruiken, moet u de volgende pakketten in uw project toevoegen. U kunt de pakketten installeren met behulp van Visual Studio NuGet package manager of de pakketten toevoegen met behulp van het `dotnet` opdracht regel programma. Kies een van de volgende methoden: 

**Optie 1. Pakketten toevoegen met behulp van Visual Studio Package Manager:**
    
U kunt dit doen door met de rechter muisknop op het project te klikken en _NuGet-pakketten beheren_ te selecteren in de lijst. In het venster dat wordt geopend, selecteert u op het tabblad _Bladeren_ en zoekt u naar de volgende pakketten. Selecteer _installeren_ en _Accepteer_ de gebruiksrecht overeenkomst om de pakketten te installeren.

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

Open vervolgens in uw Visual Studio-Solution Explorer het _Function.cs_ -bestand met voorbeeld code en voeg _de volgende instructies_ toe aan uw functie. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Verificatie code toevoegen aan de functie

U declareert nu variabelen op klasseniveau en voegt een verificatie code toe waarmee de functie toegang kan krijgen tot Azure Digital Apparaatdubbels. U voegt het volgende toe aan uw functie in het bestand {uw functie naam}. cs.

* Lees de URL van de ADT-service als een omgevings variabele. Het is een goed idee om de service-URL te lezen uit een omgevings variabele in plaats van deze op te harden in de functie.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* Een statische variabele die een httpclient maakt-exemplaar kan bevatten. Httpclient maakt is relatief kostbaar en we willen voor komen dat u dit hoeft te doen voor elke functie aanroep.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* U kunt de referenties van de beheerde identiteit gebruiken in Azure Functions.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* Voeg in uw functie een lokale variabele _DigitalTwinsClient_ toe om uw Azure Digital apparaatdubbels-client exemplaar te bewaren voor het functie project. Maak deze variabele *niet* statisch in uw klasse.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* Voeg een null-controle toe voor _adtInstanceUrl_ en verpakken de functie logica in een try catch-blok om uitzonde ringen te ondervangen.

Na deze wijzigingen wordt de functie code op de volgende manier weer gegeven:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

## <a name="publish-the-function-app-to-azure"></a>De functie-app publiceren naar Azure

Als u het project wilt publiceren naar een functie-app in azure, klikt u met de rechter muisknop op het functie project (niet de oplossing) in Solution Explorer en kiest u **publiceren**.

> [!IMPORTANT] 
> Wanneer u publiceert naar een functie-app in azure, worden extra kosten in rekening gebracht voor uw abonnement, onafhankelijk van Azure Digital Apparaatdubbels.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function.png" alt-text="Visual Studio: functie publiceren naar Azure":::

Selecteer **Azure** als publicatie doel en selecteer **volgende**.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-1.png" alt-text="Visual Studio: Azure Functions dialoog venster publiceren, selecteer Azure ":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-2.png" alt-text="Visual Studio: functie dialoog venster publiceren, selecteer Azure functie-app (Windows) of (Linux) op basis van uw computer":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-3.png" alt-text="Visual Studio: functie dialoog venster publiceren, een nieuwe Azure-functie maken":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-4.png" alt-text="Visual Studio: functie dialoog venster publiceren, vult u de velden in en selecteert u maken":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-5.png" alt-text="Visual Studio: functie dialoog venster publiceren, de functie-app in de lijst selecteren en volt ooien":::

Voer op de volgende pagina de gewenste naam in voor de nieuwe functie-app, een resource groep en andere gegevens.
De functie-app kan alleen toegang krijgen tot Azure Digital Apparaatdubbels als het een door een systeem beheerde identiteit heeft en toegang heeft tot uw Azure Digital Apparaatdubbels-exemplaar.

Vervolgens kunt u beveiligings toegang voor de functie instellen met behulp van CLI of Azure Portal. Kies een van de volgende methoden:

## <a name="set-up-security-access-for-the-function-app"></a>Beveiligings toegang instellen voor de functie-app
U kunt de beveiligings toegang voor de functie-app instellen met een van de volgende opties:

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

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-functionapp.png" alt-text="Azure Portal: zoek functie-app":::

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

U hebt ADT_INSTANCE_URL nodig om een toepassings instelling te maken.

U kunt ADT_INSTANCE_URL ophalen door **_https://_** toe te voegen aan de hostnaam van uw exemplaar. In de Azure Portal kunt u de hostnaam van uw digitale apparaatdubbels-exemplaar vinden door te zoeken naar uw instantie in de zoek balk. Selecteer vervolgens _overzicht_ op de linkernavigatiebalk om de _hostnaam_ weer te geven. Kopieer deze waarde om een toepassings instelling te maken.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Azure Portal: overzicht-> de hostnaam kopiëren voor gebruik in het veld _Value_.":::

U kunt nu een toepassings instelling maken aan de hand van de volgende stappen:

* Zoek naar uw app met behulp van de naam van de functie-app in de zoek balk en selecteer de functie-app in de lijst
* Selecteer _configuratie_ op de navigatie balk aan de linkerkant om een nieuwe toepassings instelling te maken
* Selecteer op het tabblad _Toepassings instellingen_ _+ nieuwe toepassings instelling_

:::image type="content" source="media/how-to-create-azure-function/search-for-azure-function.png" alt-text="Azure Portal: zoeken naar een bestaande functie-app":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Azure Portal: toepassings instellingen configureren":::

In het venster dat wordt geopend, gebruikt u de waarde die u eerder hebt gekopieerd om een toepassings instelling te maken. \
_Naam_  : ADT_SERVICE_URL \
_Waarde_ : https://{Your-Azure-Digital-apparaatdubbels-hostname}

Selecteer _OK_ om een toepassings instelling te maken.

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Azure Portal: toepassings instellingen toevoegen.":::

U kunt de toepassings instellingen weer geven onder het veld _naam_ in de naam van de toepassing. Sla de toepassings instellingen vervolgens op door de knop _Opslaan_ te selecteren.

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Azure Portal: de toepassing weer geven die is gemaakt en de toepassing opnieuw starten":::

Voor wijzigingen in de toepassings instellingen moet de toepassing opnieuw worden gestart. Selecteer _door gaan_ om de toepassing opnieuw op te starten.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Azure Portal: toepassings instellingen opslaan":::

U kunt zien dat de toepassings instellingen worden bijgewerkt door het pictogram _meldingen_ te selecteren. Als uw toepassings instelling niet is gemaakt, kunt u opnieuw proberen een toepassings instelling toe te voegen door het bovenstaande proces te volgen.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Azure Portal: meldingen voor het bijwerken van toepassings instellingen":::

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u de stappen voor het instellen van een functie-app in azure gevolgd voor gebruik met Azure Digital Apparaatdubbels. Vervolgens kunt u uw functie abonneren op Event Grid om te Luis teren naar een eind punt. Dit eind punt kan het volgende zijn:
* Een Event Grid-eind punt dat is gekoppeld aan Azure Digital Apparaatdubbels om berichten te verwerken die afkomstig zijn van Azure Digital Apparaatdubbels zelf (zoals wijzigings berichten van eigenschappen, telemetrie-berichten die worden gegenereerd door [digitale apparaatdubbels](concepts-twins-graph.md) in het dubbele diagram of levenscyclus berichten)
* De IoT-systeem onderwerpen die door IoT Hub worden gebruikt voor het verzenden van telemetrie en andere gebeurtenissen voor apparaten
* Een Event Grid-eind punt dat berichten van andere services ontvangt

Bekijk vervolgens hoe u de basis functie bouwt om IoT Hub gegevens op te nemen in azure Digital Apparaatdubbels:
* [*Instructies: telemetrie opnemen van IoT Hub*](how-to-ingest-iot-hub-data.md)
