---
title: Instantie verplaatsen naar een andere Azure-regio
titleSuffix: Azure Digital Twins
description: Zie een Azure Digital Apparaatdubbels-exemplaar verplaatsen van de ene Azure-regio naar een andere.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: e268cca87479625af023b5970bb27c56721f6d39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102049845"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Een Azure Digital Apparaatdubbels-exemplaar verplaatsen naar een andere Azure-regio

Als u uw Azure Digital Apparaatdubbels-exemplaar moet verplaatsen van de ene regio naar een andere, is het huidige proces om uw resources opnieuw te maken in de nieuwe regio en vervolgens de oorspronkelijke resources te verwijderen. Aan het einde van dit proces werkt u met een nieuw exemplaar van Azure Digital Apparaatdubbels dat identiek is aan de eerste, met uitzonde ring van de bijgewerkte locatie.

Dit artikel bevat richt lijnen voor het uitvoeren van een volledig verplaatsen en kopiëren van alles wat u moet doen om ervoor te zorgen dat het nieuwe exemplaar overeenkomt met het origineel.

Dit proces bestaat uit de volgende stappen:

1. Voorbereiden: down load uw oorspronkelijke modellen, apparaatdubbels en Graph.
1. Verplaatsen: Maak een nieuw Azure Digital Apparaatdubbels-exemplaar in een nieuwe regio.
1. Verplaatsen: Vul het nieuwe Azure Digital Apparaatdubbels-exemplaar opnieuw in.
    - Upload de oorspronkelijke modellen, apparaatdubbels en grafiek.
    - Nieuwe eind punten en routes maken.
    - Koppel verbonden resources opnieuw.
1. Bron resources opschonen: Verwijder het oorspronkelijke exemplaar.

## <a name="prerequisites"></a>Vereisten

Voordat u probeert uw Azure Digital Apparaatdubbels-exemplaar opnieuw te maken, gaat u naar de onderdelen van uw oorspronkelijke exemplaar om een duidelijk beeld te krijgen van alle onderdelen die opnieuw moeten worden gemaakt.

Dit zijn enkele vragen die hier van belang zijn:

* Wat zijn de *modellen* die zijn geüpload naar mijn exemplaar? Hoeveel er zijn er?
* Wat zijn de *apparaatdubbels* in mijn exemplaar? Hoeveel er zijn er?
* Wat is de algemene vorm van de *grafiek* in mijn exemplaar? Hoeveel relaties zijn er?
* Welke *eind punten* heb ik in mijn exemplaar?
* Welke *routes* heb ik in mijn exemplaar? Hebben ze filters?
* Waar maakt mijn exemplaar *verbinding met andere Azure-Services*? Enkele algemene integratie punten zijn:

    - Azure Event Grid, Azure Event Hubs of Azure Service Bus
    - Azure Functions
    - Azure Logic Apps
    - Azure Time Series Insights
    - Azure Maps
    - Azure IoT Hub Device Provisioning Service
* Wat andere *persoonlijke apps of bedrijfs toepassingen* heb ik die verbinding maken met mijn exemplaar?

U kunt deze informatie verzamelen met behulp van de [Azure Portal](https://portal.azure.com), [Azure Digital Apparaatdubbels api's en Sdk's](how-to-use-apis-sdks.md), [Azure Digital apparaatdubbels cli-opdrachten](how-to-use-cli.md)of het [Azure Digital apparaatdubbels Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) -voor beeld.

## <a name="prepare"></a>Voorbereiden

In deze sectie gaat u voorbereiden om uw exemplaar opnieuw te maken door uw oorspronkelijke modellen, apparaatdubbels en grafiek te downloaden van uw oorspronkelijke exemplaar. In dit artikel wordt het voor beeld van [Azure Digital Apparaatdubbels Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) gebruikt voor deze taak.

>[!NOTE]
>Mogelijk hebt u al bestanden die de modellen of de grafiek in uw exemplaar bevatten. Als dat het geval is, hoeft u niet alles opnieuw te downloaden: alleen de onderdelen die u mist of de items die mogelijk zijn gewijzigd sinds u deze bestanden oorspronkelijk hebt geüpload. Mogelijk hebt u bijvoorbeeld apparaatdubbels die zijn bijgewerkt met nieuwe gegevens.

### <a name="limitations-of-azure-digital-twins-explorer"></a>Beperkingen van Azure Digital Apparaatdubbels Explorer

Het [Azure Digital Apparaatdubbels Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) -voor beeld is een client-app-voor beeld dat een visuele weer gave van uw grafiek ondersteunt en een visuele interactie met uw exemplaar biedt. In dit artikel wordt uitgelegd hoe u dit kunt gebruiken om uw modellen, apparaatdubbels en grafieken te downloaden en later opnieuw te uploaden.

Dit voor beeld is geen volledig hulp programma. Het is niet zwaar getest en is niet gebouwd om grafieken van een grote grootte te verwerken. Houd daarom de volgende beperkingen met betrekking tot de voor beelden uit:

* Het voor beeld is momenteel alleen getest op de grootte van grafieken tot 1.000 knoop punten en 2.000 relaties.
* Het voor beeld biedt geen ondersteuning voor nieuwe pogingen in het geval van onregelmatige storingen.
* In het voor beeld wordt de gebruiker niet noodzakelijkerwijs gewaarschuwd als de geüploade gegevens onvolledig zijn.
* Het voor beeld behandelt geen fouten die voortkomen uit zeer grote grafieken die groter zijn dan de beschik bare bronnen, zoals het geheugen.

Als het voor beeld de grootte van uw grafiek niet kan verwerken, kunt u de grafiek exporteren en importeren met behulp van andere hulpprogram ma's voor ontwikkel aars van Azure Digital Apparaatdubbels:

* [Azure Digital Apparaatdubbels CLI-opdrachten](how-to-use-cli.md)
* [Azure Digital Apparaatdubbels Api's en Sdk's](how-to-use-apis-sdks.md)

### <a name="set-up-the-azure-digital-twins-explorer-application"></a>De Azure Digital Apparaatdubbels Explorer-toepassing instellen

Als u wilt door gaan met Azure Digital Apparaatdubbels Explorer, downloadt u eerst de code van de voorbeeld toepassing en stelt u deze in op de computer.

Zie [Azure Digital Apparaatdubbels Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)om het voor beeld te verkrijgen. Selecteer de knop **zip downloaden** om een zip-bestand van deze voorbeeld code naar uw computer te downloaden als **Azure_Digital_Twins__ADT__explorer.zip**. Pak het bestand uit.

Vervolgens stelt u de machtigingen in en configureert u deze voor Azure Digital Apparaatdubbels Explorer. Volg de instructies in de sectie [Azure Digital apparaatdubbels en Azure Digital Apparaatdubbels Explorer instellen](quickstart-adt-explorer.md#set-up-azure-digital-twins-and-azure-digital-twins-explorer) van de Snelstartgids voor Azure Digital apparaatdubbels. In deze sectie wordt u begeleid bij de volgende stappen:

1. Stel een Azure Digital Apparaatdubbels-exemplaar in. U kunt dit onderdeel overs Laan omdat u al een exemplaar hebt.
1. Stel lokale Azure-referenties in om toegang te bieden tot uw exemplaar.
1. Voer Azure Digital Apparaatdubbels Explorer uit en configureer het om verbinding te maken met uw exemplaar. U gebruikt de *hostnaam* van uw oorspronkelijke Azure Digital apparaatdubbels-exemplaar dat u wilt verplaatsen.

Nu moet u de voor beeld-app van Azure Digital Apparaatdubbels Explorer in een browser op uw computer uitvoeren. Het voor beeld moet worden verbonden met uw oorspronkelijke Azure Digital Apparaatdubbels-exemplaar.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Browser venster met een app die wordt uitgevoerd op localhost: 3000. De app heet Azure Digital Apparaatdubbels Explorer en bevat vakken voor query Explorer, model weergave, grafiek weergave en eigenschappen Verkenner. Er zijn nog geen gegevens op het scherm." lightbox="media/how-to-move-regions/explorer-blank.png":::

Als u de verbinding wilt controleren, selecteert u de knop **query uitvoeren** om de standaard query uit te voeren waarin alle apparaatdubbels en relaties in de grafiek worden weer gegeven in het vak **Explorer Verkenner** .

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Een query die wordt uitgevoerd in de rechter bovenhoek van het venster wordt gemarkeerd." lightbox="media/how-to-move-regions/run-query.png":::

U kunt de uitvoering van Azure Digital Apparaatdubbels Explorer verlaten omdat u deze later in dit artikel opnieuw gaat gebruiken om deze items opnieuw te uploaden naar uw nieuwe exemplaar in de doel regio.

### <a name="download-models-twins-and-graph"></a>Modellen, apparaatdubbels en grafieken downloaden

Down load vervolgens de modellen, apparaatdubbels en Graph in uw oplossing naar uw computer.

Als u al deze items tegelijk wilt downloaden, moet u eerst controleren of de volledige grafiek wordt weer gegeven in het vak **grafiek weergave** . Als de volledige grafiek nog niet wordt weer gegeven, voert u de standaard query uit `SELECT * FROM digitaltwins` in het vak **query Verkenner** .
 
Selecteer vervolgens het pictogram **grafiek exporteren** in het vak **grafiek weergave** .

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="In het vak grafiek weergave wordt een pictogram gemarkeerd. Er wordt een pijl naar beneden in een Cloud weer gegeven." lightbox="media/how-to-move-regions/export-graph.png":::

Met deze actie wordt een **Download** koppeling in het vak **grafiek weergave** ingeschakeld. Selecteer deze optie om een op JSON gebaseerde weer gave van het query resultaat te downloaden, waaronder uw modellen, apparaatdubbels en relaties. Met deze actie moet u een. JSON-bestand downloaden naar uw computer.

>[!NOTE]
>Als het gedownloade bestand een andere bestands extensie heeft, probeert u de extensie rechtstreeks te bewerken en te wijzigen in. json.

## <a name="move"></a>Verplaatsen

Daarna voltooit u de ' verplaatsen ' van uw exemplaar door een nieuw exemplaar te maken in de doel regio. Vervolgens vult u de gegevens en onderdelen van uw oorspronkelijke exemplaar.

### <a name="create-a-new-instance"></a>Een nieuw exemplaar maken

Maak eerst een nieuw exemplaar van Azure Digital Apparaatdubbels in de doel regio. Volg de stappen in [How-to: een instantie en authenticatie instellen](how-to-set-up-instance-portal.md). Houd de volgende aanwijzers in acht:

* U kunt de naam *van het nieuwe exemplaar in* een andere resource groep blijven gebruiken. Als u dezelfde resource groep wilt gebruiken als de oorspronkelijke instantie, heeft de nieuwe instantie een eigen afzonderlijke naam nodig.
* Voer de nieuwe doel regio in wanneer u wordt gevraagd om een locatie.

Nadat deze stap is voltooid, hebt u de hostnaam van het nieuwe exemplaar nodig om deze te blijven instellen met uw gegevens. Als u tijdens de installatie geen notitie van de hostnaam hebt gemaakt, volgt u [deze instructies](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) om het apparaat nu te downloaden van de Azure Portal.

### <a name="repopulate-the-old-instance"></a>De oude instantie opnieuw vullen

Vervolgens stelt u het nieuwe exemplaar in, zodat het een kopie is van het origineel.

#### <a name="upload-the-original-models-twins-and-graph-by-using-azure-digital-twins-explorer"></a>De oorspronkelijke modellen, apparaatdubbels en Graph uploaden met behulp van Azure Digital Apparaatdubbels Explorer

In deze sectie kunt u uw modellen, apparaatdubbels en Graph opnieuw uploaden naar het nieuwe exemplaar. Als u geen modellen, apparaatdubbels of grafieken in uw oorspronkelijke exemplaar hebt of als u deze niet wilt verplaatsen naar het nieuwe exemplaar, kunt u door gaan naar de [volgende sectie](#re-create-endpoints-and-routes).

Als dat niet het geval is, gaat u terug naar het browser venster met Azure Digital Apparaatdubbels Explorer en voert u de volgende stappen uit.

##### <a name="connect-to-the-new-instance"></a>Verbinding maken met het nieuwe exemplaar

Momenteel is Azure Digital Apparaatdubbels Explorer verbonden met uw oorspronkelijke Azure Digital Apparaatdubbels-exemplaar. Schakel de verbinding zo in dat deze naar uw nieuwe instantie verwijst door de knop **Aanmelden** in de rechter bovenhoek van het venster te selecteren.

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="Azure Digital Apparaatdubbels Explorer markeert het pictogram aanmelden in de rechter bovenhoek van het venster. Het pictogram toont een eenvoudig silhouet van een persoon die overlapt met een silhouet van een sleutel." lightbox="media/how-to-move-regions/sign-in.png":::

Vervang de **ADT-URL** zodat deze overeenkomt met uw nieuwe instantie. Wijzig deze waarde zodat de *https://{New Instance host name}* wordt gelezen.

Selecteer **Verbinding maken**. U wordt mogelijk gevraagd om u opnieuw aan te melden met uw Azure-referenties of u kunt deze toepassing toestemming geven voor uw exemplaar.

##### <a name="upload-models-twins-and-graph"></a>Modellen, apparaatdubbels en grafieken uploaden

Upload vervolgens de oplossings onderdelen die u eerder hebt gedownload naar uw nieuwe exemplaar.

Als u uw modellen, apparaatdubbels en grafiek wilt uploaden, selecteert u het pictogram **grafiek importeren** in het vak **grafiek weergave** . Met deze optie worden alle drie deze onderdelen tegelijk geüpload. Er worden zelfs modellen geüpload die momenteel niet worden gebruikt in de grafiek.

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="In het vak Graph View (Grafiekweergave) is een pictogram gemarkeerd. Het laat een pijl zien die naar een wolk (de cloud) wijst." lightbox="media/how-to-move-regions/import-graph.png":::

Ga in het vak bestands kiezer naar het gedownloade diagram. Selecteer het bestand Graph **. json** en selecteer **openen**.

Na een paar seconden opent Azure Digital Apparaatdubbels Explorer een **import** weergave waarin een voor beeld van de grafiek wordt weer gegeven die moet worden geladen.

Als u het uploaden van de graaf wilt bevestigen, selecteert u het pictogram **Save** (Opslaan) in de rechterbovenhoek van het vak **GRAPH VIEW** (GRAAFWEERGAVE).

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Het pictogram Save (Opslaan) gemarkeerd in het deelvenster Graph Preview (Graafvoorbeeld)." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Azure Digital Apparaatdubbels Explorer uploadt uw modellen en grafiek (inclusief de apparaatdubbels en relaties) nu naar uw nieuwe Azure Digital Apparaatdubbels-exemplaar. Er wordt een bericht weer gegeven met de melding dat het aantal modellen, apparaatdubbels en relaties is geüpload.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="Dialoog venster waarin wordt aangegeven dat het importeren van de grafiek is voltooid. Er wordt ' importeren geslaagd ' gelezen. 2 modellen geïmporteerd. vier apparaatdubbels geïmporteerd. twee geïmporteerde relaties. '" lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Als u wilt controleren of alles is geüpload, selecteert u de knop **query uitvoeren** in het vak **grafiek Verkenner** om de standaard query uit te voeren waarin alle apparaatdubbels en relaties in de grafiek worden weer gegeven. Met deze actie wordt ook de lijst met modellen vernieuwd in het vak **model weergave** .

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Markeer de knop Query uitvoeren in de rechter bovenhoek van het venster." lightbox="media/how-to-move-regions/run-query.png":::

U ziet uw grafiek met alle apparaatdubbels en relaties die worden weer gegeven in het vak **EXPLORER Verkenner** . U ziet ook de modellen die worden vermeld in het vak **model weergave** .

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Een weer gave van Azure Digital Apparaatdubbels Explorer met twee modellen, gemarkeerd in het vak model weergave en een grafiek die is gemarkeerd in het vak Graph Explorer." lightbox="media/how-to-move-regions/post-upload.png":::

Deze weer gaven bevestigen dat uw modellen, apparaatdubbels en grafiek opnieuw zijn geüpload naar het nieuwe exemplaar in de doel regio.

#### <a name="re-create-endpoints-and-routes"></a>Eind punten en routes opnieuw maken

Als u eind punten of routes in uw oorspronkelijke exemplaar hebt, moet u ze opnieuw maken in uw nieuwe exemplaar. Als u geen eind punten of routes in uw oorspronkelijke exemplaar hebt of als u deze niet naar het nieuwe exemplaar wilt verplaatsen, kunt u door gaan naar de [volgende sectie](#relink-connected-resources).

Als dat niet het geval is, volgt u de stappen in [How-to: Manage eind punten en routes](how-to-manage-routes-portal.md) met behulp van het nieuwe exemplaar. Houd de volgende aanwijzers in acht:

* U hoeft de Event Grid, Event Hubs of Service Bus resource die u voor het eind punt gebruikt, *niet* opnieuw te maken. Zie de sectie ' vereisten ' in de instructies van het eind punt voor meer informatie. U hoeft het eind punt alleen opnieuw te maken op het Azure Digital Apparaatdubbels-exemplaar.
* U kunt eind punten en route namen opnieuw gebruiken omdat ze zijn afgestemd op een ander exemplaar.
* Vergeet niet om de vereiste filters toe te voegen aan de routes die u maakt.

#### <a name="relink-connected-resources"></a>Verbonden resources opnieuw koppelen

Als u andere apps of Azure-resources hebt die zijn verbonden met uw oorspronkelijke Azure Digital Apparaatdubbels-exemplaar, moet u de verbinding bewerken zodat deze in plaats daarvan het nieuwe exemplaar bereiken. Deze resources omvatten mogelijk andere Azure-Services of persoonlijke of bedrijfs-apps die u hebt ingesteld voor gebruik met Azure Digital Apparaatdubbels.

Als u geen andere resources hebt die zijn verbonden met uw oorspronkelijke exemplaar of als u deze niet wilt verplaatsen naar het nieuwe exemplaar, kunt u door gaan naar de [volgende sectie](#verify).

Als dat niet het geval is, moet u rekening houden met de verbonden resources in uw scenario. U hoeft geen verbonden resources te verwijderen en opnieuw te maken. In plaats daarvan hoeft u alleen de punten te bewerken waarmee ze verbinding maken met een Azure Digital Apparaatdubbels-exemplaar via de hostnaam. Vervolgens werkt u deze punten bij om de hostnaam van het nieuwe exemplaar in plaats van de oorspronkelijke te gebruiken.

De exacte resources die u nodig hebt om te bewerken, zijn afhankelijk van uw scenario, maar hier volgen enkele algemene integratie punten:

* Azure Functions. Als u een Azure-functie hebt waarvan de code de hostnaam van het oorspronkelijke exemplaar bevat, moet u deze waarde bijwerken naar de hostnaam van het nieuwe exemplaar en de functie opnieuw publiceren.
* Event Grid, Event Hubs of Service Bus.
* Logic Apps.
* Time Series Insights.
* Azure Maps.
* IoT Hub Device Provisioning Service.
* Persoonlijke of bedrijfs-apps buiten Azure, zoals de client-app die in de [zelf studie is gemaakt: Codeer een client-app](tutorial-code.md)die verbinding maakt met het exemplaar en Azure Digital Apparaatdubbels-api's aanroept.
* Azure AD-App-registraties hoeven *niet* opnieuw te worden gemaakt. Als u een app- [registratie](how-to-create-app-registration.md) gebruikt om verbinding te maken met de Azure Digital apparaatdubbels api's, kunt u dezelfde app-registratie met uw nieuwe exemplaar gebruiken.

Nadat u deze stap hebt voltooid, moet het nieuwe exemplaar in de doel regio een kopie van het oorspronkelijke exemplaar zijn.

## <a name="verify"></a>Verifiëren

Gebruik de volgende hulpprogram ma's om te controleren of het nieuwe exemplaar correct is ingesteld:

* [Azure-portal](https://portal.azure.com). De portal is goed om te controleren of het nieuwe exemplaar bestaat en zich in de juiste doel regio bevindt. Het is ook geschikt voor het controleren van eind punten en routes en verbindingen met andere Azure-Services.
* [Azure Digital APPARAATDUBBELS cli-opdrachten](how-to-use-cli.md). Deze opdrachten zijn handig om te controleren of het nieuwe exemplaar bestaat en zich in de juiste doel regio bevindt. Ze kunnen ook worden gebruikt om exemplaar gegevens te controleren.
* [Azure Digital Apparaatdubbels Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Azure Digital Apparaatdubbels Explorer is geschikt voor het controleren van exemplaar gegevens, zoals modellen, apparaatdubbels en grafieken.
* [Azure Digital Apparaatdubbels api's en sdk's](how-to-use-apis-sdks.md). Deze resources zijn geschikt voor het controleren van exemplaar gegevens, zoals modellen, apparaatdubbels en grafieken. Ze zijn ook geschikt voor het controleren van eind punten en routes.

U kunt ook proberen aangepaste apps of end-to-end-stromen uit te voeren die u met uw oorspronkelijke exemplaar hebt uitgevoerd, zodat u kunt controleren of ze correct werken met het nieuwe exemplaar.

## <a name="clean-up-source-resources"></a>Bron resources opschonen

Nu het nieuwe exemplaar is ingesteld in de doel regio met een kopie van de gegevens en verbindingen van het oorspronkelijke exemplaar, kunt u het oorspronkelijke exemplaar verwijderen.

U kunt de [Azure Portal](https://portal.azure.com), de [Azure cli](how-to-use-cli.md)of de control- [api's](how-to-use-apis-sdks.md#overview-control-plane-apis)gebruiken.

Als u het exemplaar wilt verwijderen met behulp van de Azure Portal, [opent u de portal](https://portal.azure.com) in een browser venster en gaat u naar uw oorspronkelijke Azure Digital apparaatdubbels-exemplaar door te zoeken naar de naam in de zoek balk van de portal.

Selecteer de knop **verwijderen** en volg de aanwijzingen om het verwijderen te volt ooien.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Bekijk de details van het Azure Digital Apparaatdubbels-exemplaar in het Azure Portal op het tabblad Overzicht. De knop verwijderen is gemarkeerd.":::