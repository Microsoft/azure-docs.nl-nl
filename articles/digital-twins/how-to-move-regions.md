---
title: Exemplaar verplaatsen naar een andere Azure-regio
titleSuffix: Azure Digital Twins
description: Zie hoe u een Azure Digital Twins verplaatst van de ene Azure-regio naar de andere.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: 62db56ac9791cea7d6f1a40f794241ed68fa90fa
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483569"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Een Azure Digital Twins verplaatsen naar een andere Azure-regio

Als u uw Azure Digital Twins-exemplaar van de ene regio naar de andere wilt verplaatsen, bestaat het huidige proces uit het opnieuw maken van uw resources in de nieuwe regio en vervolgens de oorspronkelijke resources te verwijderen. Aan het einde van dit proces werkt u met een nieuwe Azure Digital Twins-instantie die identiek is aan de eerste, met uitzondering van de bijgewerkte locatie.

Dit artikel bevat richtlijnen voor het maken van een volledige overstap en het kopiëren van alles wat u nodig hebt om het nieuwe exemplaar te laten overeenkomen met het origineel.

Dit proces omvat de volgende stappen:

1. Voorbereiden: Download uw oorspronkelijke modellen, tweelingen en grafiek.
1. Verplaatsen: Maak een nieuw Azure Digital Twins in een nieuwe regio.
1. Verplaatsen: het nieuwe exemplaar van Azure Digital Twins opnieuw.
    - Upload de oorspronkelijke modellen, tweelingen en grafiek.
    - Maak eindpunten en routes opnieuw.
    - Verbonden resources opnieuw koppelen.
1. Bronbronnen ops schonen: Verwijder het oorspronkelijke exemplaar.

## <a name="prerequisites"></a>Vereisten

Voordat u uw Azure Digital Twins-exemplaar opnieuw probeert te maken, gaat u naar de onderdelen van uw oorspronkelijke exemplaar om een duidelijk beeld te krijgen van alle onderdelen die opnieuw moeten worden gemaakt.

Dit zijn enkele vragen die hier van belang zijn:

* Welke modellen worden *geüpload* naar mijn exemplaar? Hoeveel zijn er?
* Wat zijn de *tweelingen* in mijn exemplaar? Hoeveel zijn er?
* Wat is de algemene vorm van de *grafiek* in mijn exemplaar? Hoeveel relaties zijn er?
* Welke *eindpunten* heb ik in mijn exemplaar?
* Welke *routes* heb ik in mijn exemplaar? Hebben ze filters?
* Waar maakt mijn exemplaar *verbinding met andere Azure-services?* Enkele veelvoorkomende integratiepunten zijn:

    - Azure Event Grid, Azure Event Hubs of Azure Service Bus
    - Azure Functions
    - Azure Logic Apps
    - Azure Time Series Insights
    - Azure Maps
    - Azure IoT Hub Device Provisioning Service
* Welke andere *persoonlijke apps of bedrijfsapps* heb ik die verbinding maken met mijn exemplaar?

U kunt deze informatie verzamelen met behulp van [de Azure Portal](https://portal.azure.com), Azure Digital Twins-API's en [SDK's,](how-to-use-apis-sdks.md)Azure Digital Twins [CLI-opdrachten](how-to-use-cli.md)of het [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) voorbeeld.

## <a name="prepare"></a>Voorbereiden

In deze sectie bereidt u het opnieuw maken van uw exemplaar voor door uw oorspronkelijke modellen, tweelingen en grafiek te downloaden van uw oorspronkelijke exemplaar. In dit artikel wordt [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) voorbeeld voor deze taak gebruikt.

>[!NOTE]
>Mogelijk hebt u al bestanden die de modellen of de grafiek in uw exemplaar bevatten. Als dat het zo is, hoeft u niet alles opnieuw te downloaden, alleen de onderdelen die u mist of dingen die mogelijk zijn gewijzigd sinds u deze bestanden oorspronkelijk hebt geüpload. U kunt bijvoorbeeld tweelingen hebben die zijn bijgewerkt met nieuwe gegevens.

### <a name="limitations-of-azure-digital-twins-explorer"></a>Beperkingen van Azure Digital Twins Explorer

Het [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) voorbeeld is een voorbeeld van een client-app dat ondersteuning biedt voor een visuele weergave van uw grafiek en visuele interactie met uw exemplaar biedt. In dit artikel wordt beschreven hoe u het kunt gebruiken om uw modellen, tweelingen en grafieken te downloaden en later opnieuw te uploaden.

Dit voorbeeld is geen volledig hulpprogramma. Het is niet getest met stress en is niet gebouwd voor het verwerken van grafen van een groot formaat. Houd daarom rekening met de volgende out-of-the-box-voorbeeldbeperkingen:

* Het voorbeeld is momenteel alleen getest op grafiekgrootten van maximaal 1000 knooppunten en 2000 relaties.
* Het voorbeeld biedt geen ondersteuning voor opnieuw proberen in het geval van onregelmatige fouten.
* In het voorbeeld wordt de gebruiker niet per se op de hoogte gesteld als de geüploade gegevens onvolledig zijn.
* Het voorbeeld verwerkt geen fouten die het gevolg zijn van zeer grote grafieken die de beschikbare resources overschrijden, zoals geheugen.

Als het voorbeeld de grootte van uw grafiek niet kan verwerken, kunt u de grafiek exporteren en importeren met behulp van Azure Digital Twins ontwikkelhulpprogramma's:

* [Azure Digital Twins CLI-opdrachten](how-to-use-cli.md)
* [Azure Digital Twins API's en SDK's](how-to-use-apis-sdks.md)

### <a name="set-up-the-azure-digital-twins-explorer-application"></a>De toepassing Azure Digital Twins Explorer instellen

Als u wilt doorgaan Azure Digital Twins Explorer, downloadt u eerst de voorbeeldtoepassingscode en stelt u deze in om te worden uitgevoerd op uw computer.

Als u het voorbeeld wilt op halen, gaat u [naar Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Selecteer de **knop Bladeren in code** onder de titel, waarmee u naar de GitHub-opslagplaats voor het voorbeeld gaat. Selecteer de **knop Code** en zip **downloaden om** het voorbeeld te downloaden als een *. ZIP-bestand* naar uw computer.

:::image type="content" source="media/how-to-move-regions/download-repo-zip.png" alt-text="Schermopname van de opslagplaats digital-twins-explorer op GitHub. De knop Code is geselecteerd. Er wordt een klein dialoogvenster weergegeven waarin de knop ZIP downloaden is gemarkeerd." lightbox="media/how-to-move-regions/download-repo-zip.png":::

Pak het bestand uit.

Vervolgens stelt u machtigingen voor de Azure Digital Twins Explorer. Volg de instructies in de [sectie Azure Digital Twins en Azure Digital Twins Explorer](quickstart-azure-digital-twins-explorer.md#set-up-azure-digital-twins-and-azure-digital-twins-explorer) van de Azure Digital Twins quickstart. In deze sectie doorloopt u de volgende stappen:

1. Stel een Azure Digital Twins in. U kunt dit onderdeel overslaan omdat u al een exemplaar hebt.
1. Lokale Azure-referenties instellen om toegang te bieden tot uw exemplaar.
1. Voer Azure Digital Twins Explorer en configureer deze om verbinding te maken met uw exemplaar. U gebruikt de *hostnaam van* uw oorspronkelijke Azure Digital Twins exemplaar dat u verplaatst.

Nu moet de voorbeeld-app Azure Digital Twins Explorer uitgevoerd in een browser op uw computer. Het voorbeeld moet zijn verbonden met uw oorspronkelijke Azure Digital Twins exemplaar.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Browservenster met een app die wordt uitgevoerd op localhost:3000. De app heet Azure Digital Twins Explorer bevat vakken voor QueryVerkenner, Modelweergave, Grafiekweergave en Property Explorer. Er zijn nog geen gegevens op het scherm." lightbox="media/how-to-move-regions/explorer-blank.png":::

Als u de verbinding wilt controleren, selecteert u de knop **Query** uitvoeren om de standaardquery uit te voeren waarmee alle tweelingen en relaties in de grafiek worden weergegeven in het **vak GRAPH EXPLORER.**

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Een knop query uitvoeren in de rechterbovenhoek van het venster is gemarkeerd." lightbox="media/how-to-move-regions/run-query.png":::

U kunt deze Azure Digital Twins Explorer omdat u deze later in dit artikel opnieuw gebruikt om deze items opnieuw te uploaden naar uw nieuwe exemplaar in de doelregio.

### <a name="download-models-twins-and-graph"></a>Modellen, tweelingen en grafieken downloaden

Download vervolgens de modellen, tweelingen en grafiek in uw oplossing naar uw computer.

Als u al deze items in één keer wilt downloaden, moet u eerst ervoor zorgen dat de volledige grafiek wordt weergegeven in het **vak GRAPH VIEW.** Als de volledige grafiek nog niet wordt weergegeven, moet u de standaardquery van opnieuw uitvoeren `SELECT * FROM digitaltwins` in het **vak QUERYVERKENNER.**
 
Selecteer vervolgens het **pictogram Grafiek exporteren** in het vak GRAPH **VIEW.**

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="In het vak Grafiekweergave is een pictogram gemarkeerd. Er wordt een pijl naar beneden uit een cloud laten wijzen." lightbox="media/how-to-move-regions/export-graph.png":::

Met deze actie schakelt u een **koppeling Downloaden** in het vak **GRAPH VIEW** in. Selecteer deze om een JSON-weergave van het queryresultaat te downloaden, inclusief uw modellen, tweelingen en relaties. Met deze actie moet een JSON-bestand naar uw computer worden gedownload.

>[!NOTE]
>Als het gedownloade bestand een andere bestandsextensie lijkt te hebben, probeert u de extensie rechtstreeks te bewerken en te wijzigen in .json.

## <a name="move"></a>Verplaatsen

Vervolgens voltooit u de 'move' van uw exemplaar door een nieuw exemplaar te maken in de doelregio. Vervolgens vult u deze met de gegevens en onderdelen van uw oorspronkelijke exemplaar.

### <a name="create-a-new-instance"></a>Een nieuw exemplaar maken

Maak eerst een nieuw exemplaar van Azure Digital Twins in uw doelregio. Volg de stappen in [Instructies: Een exemplaar en verificatie instellen.](how-to-set-up-instance-portal.md) Houd rekening met de volgende aanwijzers:

* U kunt dezelfde naam voor het nieuwe exemplaar behouden *als* deze zich in een andere resourcegroep. Als u dezelfde resourcegroep moet gebruiken die uw oorspronkelijke exemplaar bevat, heeft uw nieuwe exemplaar een eigen unieke naam nodig.
* Voer de nieuwe doelregio in wanneer u om een locatie wordt gevraagd.

Nadat deze stap is voltooid, hebt u de hostnaam van het nieuwe exemplaar nodig om door te gaan met het instellen met uw gegevens. Als u de hostnaam niet hebt noteren [](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) tijdens de installatie, volgt u deze instructies om deze nu op te halen uit de Azure Portal.

### <a name="repopulate-the-old-instance"></a>Het oude exemplaar opnieuw inpopuleren

Vervolgens stelt u het nieuwe exemplaar zo in dat het een kopie van het origineel is.

#### <a name="upload-the-original-models-twins-and-graph-by-using-azure-digital-twins-explorer"></a>Upload de oorspronkelijke modellen, tweelingen en grafiek met behulp van Azure Digital Twins Explorer

In deze sectie kunt u uw modellen, tweelingen en grafiek opnieuw uploaden naar het nieuwe exemplaar. Als u geen modellen, tweelingen of grafieken in uw oorspronkelijke exemplaar hebt of als u ze niet naar het nieuwe exemplaar wilt verplaatsen, kunt u naar de volgende sectie [gaan.](#re-create-endpoints-and-routes)

Ga anders terug naar het browservenster met Azure Digital Twins Explorer en volg deze stappen.

##### <a name="connect-to-the-new-instance"></a>Verbinding maken met het nieuwe exemplaar

Momenteel is Azure Digital Twins Explorer verbonden met uw oorspronkelijke Azure Digital Twins-exemplaar. Schakel de verbinding om naar uw nieuwe exemplaar te wijzen door de knop **Aanmelden** in de rechterbovenhoek van het venster te selecteren.

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="Azure Digital Twins Explorer het pictogram Aanmelden in de rechterbovenhoek van het venster. Het pictogram toont een eenvoudige show van een persoon die wordt overlad met een sleutel." lightbox="media/how-to-move-regions/sign-in.png":::

Vervang de **ADT-URL om** uw nieuwe exemplaar weer te geven. Wijzig deze waarde zodat deze *https://{new instance host name} leest.*

Selecteer **Verbinding maken**. U wordt mogelijk gevraagd om u opnieuw aan te melden met uw Azure-referenties of om deze toepassing toestemming te geven voor uw exemplaar.

##### <a name="upload-models-twins-and-graph"></a>Modellen, tweelingen en grafieken uploaden

Upload vervolgens de oplossingsonderdelen die u eerder hebt gedownload naar uw nieuwe exemplaar.

Als u uw modellen, tweelingen en grafiek wilt uploaden, selecteert u het **pictogram Grafiek** importeren in het vak **GRAPH VIEW.** Met deze optie worden alle drie deze onderdelen tegelijk geüpload. Er worden zelfs modellen geüpload die momenteel niet in de grafiek worden gebruikt.

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="In het vak Graph View (Grafiekweergave) is een pictogram gemarkeerd. Het laat een pijl zien die naar een wolk (de cloud) wijst." lightbox="media/how-to-move-regions/import-graph.png":::

Ga in het vak bestands selector naar uw gedownloade grafiek. Selecteer het **JSON-grafiekbestand** en selecteer **Openen.**

Na een paar seconden wordt Azure Digital Twins Explorer **importweergave** geopend met een voorbeeld van de grafiek die moet worden geladen.

Als u het uploaden van de graaf wilt bevestigen, selecteert u het pictogram **Save** (Opslaan) in de rechterbovenhoek van het vak **GRAPH VIEW** (GRAAFWEERGAVE).

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Het pictogram Save (Opslaan) gemarkeerd in het deelvenster Graph Preview (Graafvoorbeeld)." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Azure Digital Twins Explorer uploadt nu uw modellen en grafiek (inclusief de tweelingen en relaties) naar uw nieuwe Azure Digital Twins exemplaar. U ziet nu een succesbericht met de melding hoeveel modellen, tweelingen en relaties zijn geüpload.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="Dialoogvenster waarin wordt aangegeven dat het importeren van de grafiek is geslaagd. De tekst 'Importeren is geslaagd. 2 modellen geïmporteerd. 4 tweelingen geïmporteerd. 2 relaties geïmporteerd.'" lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Als u wilt controleren of alles is geüpload, selecteert u de knop **Query** uitvoeren in het vak **GRAPH EXPLORER** om de standaardquery uit te voeren waarmee alle tweelingen en relaties in de grafiek worden weergegeven. Met deze actie wordt ook de lijst met modellen in het **vak MODELWEERGAVE** vernieuwd.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Markeer de knop Query uitvoeren in de rechterbovenhoek van het venster." lightbox="media/how-to-move-regions/run-query.png":::

Als het goed is, ziet u dat uw grafiek met alle tweelingen en relaties wordt weergegeven in het **vak GRAPH EXPLORER.** Als het goed is, worden uw modellen ook weergegeven in het **vak MODELWEERGAVE.**

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Een weergave van Azure Digital Twins Explorer met twee modellen die zijn gemarkeerd in het vak Modelweergave en een grafiek die is gemarkeerd in het vak Graph Explorer." lightbox="media/how-to-move-regions/post-upload.png":::

Deze weergaven bevestigen dat uw modellen, tweelingen en grafiek opnieuw zijn geüpload naar het nieuwe exemplaar in de doelregio.

#### <a name="re-create-endpoints-and-routes"></a>Eindpunten en routes opnieuw maken

Als u eindpunten of routes in uw oorspronkelijke exemplaar hebt, moet u deze opnieuw maken in uw nieuwe exemplaar. Als u geen eindpunten of routes in uw oorspronkelijke exemplaar hebt of als u ze niet naar het nieuwe exemplaar wilt verplaatsen, kunt u naar de volgende sectie [gaan.](#relink-connected-resources)

Volg anders de stappen in [Instructies: Eindpunten](how-to-manage-routes-portal.md) en routes beheren met behulp van het nieuwe exemplaar. Houd rekening met de volgende aanwijzers:

* U hoeft *de* resource die u voor het eindpunt Event Grid, Event Hubs of Service Bus te maken. Zie de sectie Vereisten in de eindpuntinstructies voor meer informatie. U hoeft alleen het eindpunt opnieuw te maken op het Azure Digital Twins exemplaar.
* U kunt eindpunt- en routenamen opnieuw gebruiken omdat ze zijn beperkt tot een ander exemplaar.
* Vergeet niet om vereiste filters toe te voegen aan de routes die u maakt.

#### <a name="relink-connected-resources"></a>Verbonden resources opnieuw koppelen

Als u andere apps of Azure-resources hebt die zijn verbonden met uw oorspronkelijke Azure Digital Twins-exemplaar, moet u de verbinding bewerken zodat ze in plaats daarvan uw nieuwe exemplaar bereiken. Deze resources omvatten mogelijk andere Azure-services of persoonlijke of bedrijfsapps die u hebt ingesteld om met uw Azure Digital Twins.

Als u geen andere resources hebt die zijn verbonden met uw oorspronkelijke exemplaar of als u ze niet wilt verplaatsen naar het nieuwe exemplaar, kunt u naar de [volgende sectie gaan.](#verify)

Anders kunt u de verbonden resources in uw scenario overwegen. U hoeft geen verbonden resources te verwijderen en opnieuw te maken. In plaats daarvan hoeft u alleen de punten te bewerken waar ze verbinding maken met een Azure Digital Twins-exemplaar via de hostnaam. Vervolgens kunt u deze punten bijwerken om de hostnaam van het nieuwe exemplaar te gebruiken in plaats van het oorspronkelijke exemplaar.

De exacte resources die u moet bewerken, zijn afhankelijk van uw scenario, maar hier zijn enkele algemene integratiepunten:

* Azure Functions. Als u een Azure-functie hebt waarvan de code de hostnaam van het oorspronkelijke exemplaar bevat, moet u deze waarde bijwerken naar de hostnaam van het nieuwe exemplaar en de functie opnieuw publiceren.
* Event Grid, Event Hubs of Service Bus.
* Logic Apps.
* Time Series Insights.
* Azure Maps.
* IoT Hub Device Provisioning Service.
* Persoonlijke of bedrijfsapps buiten Azure, zoals de client-app die is gemaakt in Zelfstudie: Codeer een [client-app](tutorial-code.md)die verbinding maakt met het exemplaar en roep Azure Digital Twins API's aan.
* Azure *AD-app-registraties* hoeven niet opnieuw te worden gemaakt. Als u een [app-registratie](how-to-create-app-registration.md) gebruikt om verbinding te maken met de Azure Digital Twins API's, kunt u dezelfde app-registratie opnieuw gebruiken bij uw nieuwe exemplaar.

Nadat u deze stap hebt voltooien, moet uw nieuwe exemplaar in de doelregio een kopie van het oorspronkelijke exemplaar zijn.

## <a name="verify"></a>Verifiëren

Gebruik de volgende hulpprogramma's om te controleren of uw nieuwe exemplaar juist is ingesteld:

* [Azure-portal](https://portal.azure.com). De portal is goed om te controleren of uw nieuwe exemplaar bestaat en zich in de juiste doelregio bevindt. Het is ook goed voor het verifiëren van eindpunten en routes en verbindingen met andere Azure-services.
* [Azure Digital Twins CLI-opdrachten](how-to-use-cli.md). Deze opdrachten zijn goed om te controleren of uw nieuwe exemplaar bestaat en zich in de juiste doelregio bevindt. Ze kunnen ook worden gebruikt om exemplaargegevens te verifiëren.
* [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Azure Digital Twins Explorer is goed voor het verifiëren van exemplaargegevens, zoals modellen, tweelingen en grafieken.
* [Azure Digital Twins API's en SDK's .](how-to-use-apis-sdks.md) Deze resources zijn goed voor het verifiëren van exemplaargegevens, zoals modellen, tweelingen en grafieken. Ze zijn ook goed voor het verifiëren van eindpunten en routes.

U kunt ook alle aangepaste apps of end-to-end-stromen uitvoeren die u met uw oorspronkelijke exemplaar hebt uitgevoerd, zodat u kunt controleren of ze correct werken met het nieuwe exemplaar.

## <a name="clean-up-source-resources"></a>Bronbronnen ops schonen

Nu uw nieuwe exemplaar is ingesteld in de doelregio met een kopie van de gegevens en verbindingen van het oorspronkelijke exemplaar, kunt u het oorspronkelijke exemplaar verwijderen.

U kunt de [Azure Portal,](https://portal.azure.com)de [Azure CLI](how-to-use-cli.md)of de API's van [het besturingsvlak gebruiken.](how-to-use-apis-sdks.md#overview-control-plane-apis)

Als u het exemplaar wilt verwijderen met behulp van de Azure Portal, opent u de [portal](https://portal.azure.com) in een browservenster en gaat u naar uw oorspronkelijke Azure Digital Twins-exemplaar door te zoeken naar de naam in de zoekbalk van de portal.

Selecteer de **knop** Verwijderen en volg de aanwijzingen om het verwijderen te voltooien.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Weergave van de Azure Digital Twins instantiedetails in de Azure Portal, op het tabblad Overzicht. De knop Verwijderen is gemarkeerd.":::