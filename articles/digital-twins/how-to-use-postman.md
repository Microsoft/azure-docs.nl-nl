---
title: Verzoeken indienen met Postman
titleSuffix: Azure Digital Twins
description: Meer informatie over het configureren en gebruiken van Postman voor het testen van de Azure Digital Apparaatdubbels-Api's.
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: d4a6e25578cd26b10b34f74a9f859d4957cc553b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783809"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Postman gebruiken om aanvragen te verzenden naar de Azure Digital Apparaatdubbels-Api's

[Postman](https://www.getpostman.com/) is een hulp programma voor rest testen dat belang rijke HTTP-aanvraag functionaliteit biedt in een op een bureau blad gebaseerde GUI U kunt deze gebruiken om HTTP-aanvragen te bevragen en deze te verzenden naar de [rest-api's van Azure Digital apparaatdubbels](how-to-use-apis-sdks.md).

In dit artikel wordt beschreven hoe u de [postman rest-client](https://www.getpostman.com/) kunt configureren om te communiceren met de Azure Digital apparaatdubbels api's door de volgende stappen uit te voeren:

1. Gebruik de Azure CLI om [**een Bearer-token**](#get-bearer-token) op te halen die u gaat gebruiken om API-aanvragen in te voeren in de Postman.
1. Stel een [**postman-verzameling**](#about-postman-collections) in en configureer de POSTman rest-client om uw Bearer-token te gebruiken voor verificatie. Bij het instellen van de verzameling kunt u een van de volgende opties kiezen:
    1. Een vooraf gemaakte verzameling van Azure Digital Apparaatdubbels API-aanvragen [**importeren**](#import-collection-of-azure-digital-twins-apis) .
    1. [**Maak**](#create-your-own-collection) een volledig nieuwe verzameling.
1. [**Voeg aanvragen**](#add-an-individual-request) toe aan uw geconfigureerde verzameling en stuur deze naar de Azure Digital Apparaatdubbels-api's.

Azure Digital Apparaatdubbels heeft twee sets Api's waarmee u kunt werken: **gegevens vlak** en **besturings vlak**. Voor meer informatie over het verschil tussen deze API-sets, Zie [*How to: de Azure Digital apparaatdubbels-api's en Sdk's gebruiken*](how-to-use-apis-sdks.md). Dit artikel bevat informatie voor beide API-sets.

## <a name="prerequisites"></a>Vereisten

Als u wilt door gaan met het gebruik van Postman om toegang te krijgen tot de Azure Digital Apparaatdubbels Api's, moet u een Azure Digital Apparaatdubbels-exemplaar instellen en postman downloaden. In de rest van deze sectie krijgt u stapsgewijze instructies.

### <a name="set-up-azure-digital-twins-instance"></a>Een Azure Digital Twins-exemplaar instellen

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Berichtman downloaden

Down load vervolgens de desktop versie van de Postman-client. Navigeer naar [*www.getpostman.com/apps*](https://www.getpostman.com/apps) en volg de aanwijzingen om de app te downloaden.

## <a name="get-bearer-token"></a>Bearer-Token ophalen

Nu u postman en uw Azure Digital Apparaatdubbels-exemplaar hebt ingesteld, moet u een Bearer-token ontvangen dat is ingesteld op het gebruik van de digitale Apparaatdubbels-Api's van Azure.

Er zijn verschillende manieren om dit token op te halen. In dit artikel wordt de [Azure cli](/cli/azure/install-azure-cli) gebruikt om u aan te melden bij uw Azure-account en een token op te halen.

Als u een Azure CLI [lokaal hebt geïnstalleerd](/cli/azure/install-azure-cli), kunt u een opdracht prompt op uw computer starten om de volgende opdrachten uit te voeren.
Als dat niet het geval is, kunt u een [Azure Cloud shell](https://shell.azure.com) -venster openen in uw browser en de opdrachten daar uitvoeren.

1. Zorg er eerst voor dat u bent aangemeld bij Azure met de juiste referenties door deze opdracht uit te voeren:

    ```azurecli-interactive
    az login
    ```

2. Gebruik vervolgens de opdracht [AZ account Get-access-token](/cli/azure/account#az_account_get_access_token) om een Bearer-token te verkrijgen met toegang tot de Azure Digital apparaatdubbels-service. In deze opdracht geeft u de resource-ID voor het Azure Digital Apparaatdubbels service-eind punt door om een toegangs token te verkrijgen waarmee toegang kan worden verkregen tot Azure Digital Apparaatdubbels-resources. 

    De vereiste context voor het token is afhankelijk van welke set Api's u gebruikt. Gebruik daarom de tabbladen hieronder om te selecteren tussen het [gegevens vlak](how-to-use-apis-sdks.md#overview-data-plane-apis) en de [besturings element](how-to-use-apis-sdks.md#overview-control-plane-apis) -api's.

    # <a name="data-plane"></a>[Gegevenslaag](#tab/data-plane)
    
    Als u een token wilt ophalen voor gebruik met de gegevenslaag **-api's, gebruikt u de volgende** statische waarde voor de token context: `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Dit is de resource-ID voor het Azure Digital Apparaatdubbels service-eind punt.
    
    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```
    
    # <a name="control-plane"></a>[Besturingsvlak](#tab/control-plane)
    
    Gebruik de volgende waarde voor de token context om een token op te halen dat moet worden gebruikt met de **Control** -api's: `https://management.azure.com/` .
    
    ```azurecli-interactive
    az account get-access-token --resource https://management.azure.com/
    ```
    ---


3. Kopieer de waarde van `accessToken` in het resultaat en sla deze op voor gebruik in de volgende sectie. Dit is de **waarde** van uw token die u opgeeft om uw aanvragen te autoriseren.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Scherm afbeelding van de console met het resultaat van de opdracht AZ account Get-access-token. Het veld accessToken en de voorbeeld waarde zijn gemarkeerd.":::

>[!TIP]
>Dit token is geldig gedurende ten minste vijf minuten en Maxi maal 60 minuten. Als u geen tijd meer hebt toegewezen aan het huidige token, kunt u de stappen in deze sectie herhalen om een nieuwe te verkrijgen.

Vervolgens stelt u postman in om het token te gebruiken voor het maken van API-aanvragen voor Azure Digital Apparaatdubbels.

## <a name="about-postman-collections"></a>Over postman-verzamelingen

Aanvragen in postman worden opgeslagen in **verzamelingen** (groepen aanvragen). Wanneer u een verzameling maakt om uw aanvragen te groeperen, kunt u algemene instellingen Toep assen op veel aanvragen tegelijk. Dit kan de autorisatie aanzienlijk vereenvoudigen als u van plan bent om meer dan één aanvraag te maken voor de Azure Digital Apparaatdubbels-Api's, omdat u deze gegevens slechts één keer hoeft te configureren voor de volledige verzameling.

Wanneer u met Azure Digital Apparaatdubbels werkt, kunt u aan de slag met [het importeren van een vooraf gemaakte verzameling van alle Azure Digital apparaatdubbels-aanvragen](#import-collection-of-azure-digital-twins-apis). U kunt dit doen als u de Api's wilt verkennen en snel een project wilt instellen met aanvraag voorbeelden.

U kunt er ook voor kiezen om helemaal vanaf het begin te beginnen, door [uw eigen lege verzameling te maken](#create-your-own-collection) en deze te vullen met afzonderlijke aanvragen die alleen de api's aanroepen die u nodig hebt. 

In de volgende secties worden beide processen beschreven. De rest van het artikel vindt plaats in uw lokale postman-toepassing. Ga dus verder met het openen van de toepassing postman op uw computer.

## <a name="import-collection-of-azure-digital-twins-apis"></a>Verzameling van Azure Digital Apparaatdubbels-Api's importeren

Een snelle manier om aan de slag te gaan met Azure Digital Apparaatdubbels in Postman is het importeren van een vooraf gemaakte verzameling aanvragen voor de Azure Digital Apparaatdubbels-Api's.

### <a name="download-the-collection-file"></a>Het verzamelings bestand downloaden

De eerste stap bij het importeren van de API-set is het downloaden van een verzameling. Kies het onderstaande tabblad voor een gegevens vlak of besturings vlak om de vooraf gemaakte verzamelings opties weer te geven.

# <a name="data-plane"></a>[Gegevenslaag](#tab/data-plane)

Er zijn momenteel twee verzamelingen van Azure Digital Apparaatdubbels-gegevens opslag beschikbaar waaruit u kunt kiezen:
* [**Azure Digital Apparaatdubbels postman-verzameling**](https://github.com/microsoft/azure-digital-twins-postman-samples): deze verzameling biedt een eenvoudige aan de slag-ervaring voor Azure Digital Apparaatdubbels in postman. De aanvragen bevatten voorbeeld gegevens, zodat u ze kunt uitvoeren met minimale bewerkingen vereist. Kies deze verzameling als u een digestible-set van Key API-aanvragen met voorbeeld gegevens wilt.
    - Als u de verzameling wilt vinden, gaat u naar de koppeling opslag plaats en opent u het bestand met de naam *postman_collection.jsop*.
* **[Gegevens vlak Swagger van Azure Digital apparaatdubbels](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)**: deze opslag plaats bevat het volledige Swagger-bestand voor de Azure Digital apparaatdubbels API set, die kan worden gedownload en geïmporteerd als een verzameling. Dit geeft een uitgebreide set van elke API-aanvraag, maar met lege gegevens instanties in plaats van voorbeeld gegevens. Kies deze verzameling als u toegang wilt hebben tot elke API-aanroep en alle gegevens zelf wilt invullen.
    - Als u de verzameling wilt vinden, gaat u naar de koppeling opslag plaats en kiest u de map voor de meest recente versie van de specificatie. Open het bestand met de naam *digitaltwins.jsop*.

# <a name="control-plane"></a>[Besturingsvlak](#tab/control-plane)

De verzameling die momenteel beschikbaar is voor besturings vlak is het [**Apparaatdubbels Swagger van het Azure Digital-besturings element**](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Deze opslag plaats bevat het volledige Swagger-bestand voor de Azure Digital Apparaatdubbels API set, die kan worden gedownload en geïmporteerd als een verzameling. Dit geeft een uitgebreide set van elke API-aanvraag.

Als u de verzameling wilt vinden, gaat u naar de koppeling opslag plaats en kiest u de map voor de meest recente versie van de specificatie. Open het bestand met de naam *digitaltwins.jsop*.

---

U kunt als volgt uw gekozen verzameling naar uw computer downloaden, zodat u deze in een postman-bestand kan importeren.
1. Gebruik de koppelingen hierboven om het verzamel bestand in GitHub in uw browser te openen.
1. Selecteer de knop **RAW** om de onbewerkte tekst van het bestand te openen.
    :::image type="content" source="media/how-to-use-postman/swagger-raw.png" alt-text="Scherm afbeelding van het gegevenslaag digitaltwins.jsin het bestand GitHub. Er is een markering om de onbewerkte knop." lightbox="media/how-to-use-postman/swagger-raw.png":::
1. Kopieer de tekst in het venster en plak deze in een nieuw bestand op uw computer.
1. Sla het bestand op met de extensie *. json* (de bestands naam kan wille keurig zijn, op voor waarde dat u het bestand later kunt vinden).

### <a name="import-the-collection"></a>De verzameling importeren

Importeer vervolgens de verzameling in het bericht.

1. Selecteer in het hoofd venster van Postman de knop **importeren** .
    :::image type="content" source="media/how-to-use-postman/postman-import-collection.png" alt-text="Scherm afbeelding van een onlangs geopend postman-venster. De knop importeren is gemarkeerd." lightbox="media/how-to-use-postman/postman-import-collection.png":::

1. Selecteer in het venster importeren de optie **bestanden uploaden** en navigeer naar het verzamelings bestand op uw computer dat u eerder hebt gemaakt. Selecteer Openen.
1. Selecteer de knop **importeren** om te bevestigen.

    :::image type="content" source="media/how-to-use-postman/postman-import-collection-2.png" alt-text="Scherm afbeelding van het venster importeren van Postman, met daarin het bestand dat u wilt importeren als een verzameling en de knop importeren.":::

De zojuist geïmporteerde verzameling kan nu worden weer gegeven in de hoofd weergave van Postman op het tabblad verzamelingen.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-imported.png" alt-text="Scherm afbeelding van het hoofd venster van postman. De zojuist geïmporteerde verzameling wordt op het tabblad verzamelingen gemarkeerd." lightbox="media/how-to-use-postman/postman-post-collection-imported.png":::

Ga vervolgens verder met de volgende sectie om een Bearer-token toe te voegen aan de verzameling voor autorisatie en verbinding te maken met uw Azure Digital apparaatdubbels-exemplaar.

### <a name="configure-authorization"></a>Autorisatie configureren

Bewerk vervolgens de verzameling die u hebt gemaakt om bepaalde toegangs gegevens te configureren. Markeer de verzameling die u hebt gemaakt en selecteer het pictogram **meer acties weer geven** om een menu op te halen. Selecteer **Bewerken**.

:::image type="content" source="media/how-to-use-postman/postman-edit-collection.png" alt-text="Scherm opname van postman. Het pictogram ' meer acties weer geven ' voor de geïmporteerde verzameling is gemarkeerd en ' bewerken ' is gemarkeerd in de opties." lightbox="media/how-to-use-postman/postman-edit-collection.png":::

Volg deze stappen om een Bearer-token toe te voegen aan de verzameling voor autorisatie. Hier gaat u de **token waarde** gebruiken die u hebt verzameld in de sectie [token ophalen](#get-bearer-token) om deze te gebruiken voor alle API-aanvragen in uw verzameling.

1. Controleer of u zich op het tabblad **autorisatie** bevindt in het dialoog venster voor het bewerken van uw verzameling. 

    :::image type="content" source="media/how-to-use-postman/postman-authorization-imported.png" alt-text="Scherm afbeelding van het bewerkings dialoogvenster van de geïmporteerde verzameling in de Postman, waarin het tabblad autorisatie wordt weer gegeven." lightbox="media/how-to-use-postman/postman-authorization-imported.png":::

1. Stel het type in op **OAuth 2,0**, plak uw toegangs token in het vak toegangs token en selecteer **Opslaan**.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-imported.png" alt-text="Scherm afbeelding van het dialoog venster bericht bewerken voor de geïmporteerde verzameling op het tabblad autorisatie. Type is ' OAuth 2,0 ' en het vak toegangs token is gemarkeerd." lightbox="media/how-to-use-postman/postman-paste-token-imported.png":::

### <a name="additional-configuration"></a>Aanvullende configuratie

# <a name="data-plane"></a>[Gegevenslaag](#tab/data-plane)

Als u een gegevenslaag verzameling maakt, kunt u [de verzameling helpen](how-to-use-apis-sdks.md#overview-data-plane-apis) om eenvoudig verbinding te maken met uw Azure Digital apparaatdubbels-resources door sommige **variabelen** in te stellen die zijn opgenomen in de verzamelingen. Wanneer veel aanvragen in een verzameling dezelfde waarde vereisen (zoals de hostnaam van uw Azure Digital Apparaatdubbels-exemplaar), kunt u de waarde opslaan in een variabele die van toepassing is op elke aanvraag in de verzameling. Beide Download bare verzamelingen voor Azure Digital Apparaatdubbels worden geleverd met vooraf gemaakte variabelen die u op verzamelings niveau kunt instellen.

1. Ga in het bewerkings venster voor uw verzameling nog naar het tabblad **variabelen** .

1. Gebruik de **hostnaam** van uw exemplaar in de sectie [*vereisten*](#prerequisites) om het veld huidige waarde van de relevante variabele in te stellen. Selecteer **Opslaan**.

    :::image type="content" source="media/how-to-use-postman/postman-variables-imported.png" alt-text="Scherm afbeelding van het bewerkings dialoogvenster van de geïmporteerde verzameling in de Postman, met het tabblad ' variabelen '. Het veld huidige waarde is gemarkeerd." lightbox="media/how-to-use-postman/postman-variables-imported.png":::

1. Als uw verzameling extra variabelen heeft, vult u deze waarden ook in.

Wanneer u klaar bent met de bovenstaande stappen, bent u klaar met het configureren van de verzameling. U kunt het tabblad bewerken voor de verzameling sluiten als u dat wilt.

# <a name="control-plane"></a>[Besturingsvlak](#tab/control-plane)

Als u een verzameling [besturings elementen](how-to-use-apis-sdks.md#overview-control-plane-apis) maakt, hebt u alles gedaan wat u nodig hebt om de verzameling te configureren. U kunt het tabblad bewerken voor de verzameling sluiten als u wilt en door gaan naar de volgende sectie.

--- 

### <a name="explore-requests"></a>Verzoeken verkennen

Daarna kunt u de aanvragen in de Azure Digital Apparaatdubbels API-verzameling verkennen. U kunt de verzameling uitvouwen om de vooraf gemaakte aanvragen weer te geven (gesorteerd op categorie van de bewerking). 

Voor verschillende aanvragen is andere informatie over uw exemplaar en de gegevens vereist. Als u alle vereiste gegevens voor het maken van een bepaalde aanvraag wilt weer geven, zoekt u de details van de aanvraag op in de [referentie documentatie van Azure Digital apparaatdubbels rest API](/rest/api/azure-digitaltwins/).

Met de volgende stappen kunt u de details van een aanvraag in de Postman-verzameling bewerken:

1. Selecteer deze optie in de lijst om de bewerkte details op te halen. 

1. Vul **waarden in voor** de variabelen die worden weer gegeven op het tabblad **params** onder padvariabelen.

    :::image type="content" source="media/how-to-use-postman/postman-request-details-imported.png" alt-text="Scherm opname van postman. De verzameling wordt uitgevouwen om een aanvraag weer te geven. De sectie ' Path Varia bles ' is gemarkeerd in de details van de aanvraag." lightbox="media/how-to-use-postman/postman-request-details-imported.png":::

1. Geef de benodigde **kopteksten** of informatie over de **hoofd tekst** op in de desbetreffende tabbladen.

Zodra u alle vereiste gegevens hebt opgegeven, kunt u de aanvraag uitvoeren met de knop **verzenden** .

U kunt ook uw eigen aanvragen toevoegen aan de verzameling met behulp van het proces dat wordt beschreven in de sectie [*een afzonderlijke aanvraag toevoegen*](#add-an-individual-request) hieronder.

## <a name="create-your-own-collection"></a>Uw eigen verzameling maken

In plaats van de bestaande verzameling van alle Azure Digital Apparaatdubbels-Api's te importeren, kunt u ook zelf een volledig nieuwe verzameling maken. U kunt deze vervolgens vullen met afzonderlijke aanvragen met behulp van de [referentie documentatie van Azure Digital apparaatdubbels rest API](/rest/api/azure-digitaltwins/).

### <a name="create-a-postman-collection"></a>Een Postman-verzameling maken

1. Als u een verzameling wilt maken, selecteert u de knop **Nieuw** in het hoofd venster van postman.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Scherm afbeelding van het hoofd venster van postman. De knop Nieuw is gemarkeerd." lightbox="media/how-to-use-postman/postman-new.png":::

    Kies een type **verzameling**.

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-2.png" alt-text="Scherm opname van het dialoog venster ' nieuw maken ' in het bericht. De optie verzameling is gemarkeerd.":::

1. Hiermee opent u een tabblad voor het invullen van de details van de nieuwe verzameling. Selecteer het bewerkings pictogram naast de standaard naam van de verzameling (**nieuwe verzameling**) om deze te vervangen door de naam van uw eigen keuze. 

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-3.png" alt-text="Scherm afbeelding van het dialoog venster bewerken van de nieuwe verzameling in het bericht. Het bewerkings pictogram naast de naam ' nieuwe verzameling ' wordt gemarkeerd." lightbox="media/how-to-use-postman/postman-new-collection-3.png":::

Ga vervolgens verder met de volgende sectie om een Bearer-token toe te voegen aan de verzameling voor autorisatie.

### <a name="configure-authorization"></a>Autorisatie configureren

Volg deze stappen om een Bearer-token toe te voegen aan de verzameling voor autorisatie. Hier gaat u de **token waarde** gebruiken die u hebt verzameld in de sectie [token ophalen](#get-bearer-token) om deze te gebruiken voor alle API-aanvragen in uw verzameling.

1. Ga in het dialoog venster bewerken voor uw nieuwe verzameling naar het tabblad **autorisatie** .

    :::image type="content" source="media/how-to-use-postman/postman-authorization-custom.png" alt-text="Scherm afbeelding van het dialoog venster bewerken van de nieuwe verzameling in de Postman, waarin het tabblad autorisatie wordt weer gegeven." lightbox="media/how-to-use-postman/postman-authorization-custom.png":::

1. Stel het type in op **OAuth 2,0**, plak uw toegangs token in het vak toegangs token en selecteer **Opslaan**.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-custom.png" alt-text="Scherm afbeelding van het dialoog venster postman bewerken voor de nieuwe verzameling, op het tabblad autorisatie. Type is ' OAuth 2,0 ' en het vak toegangs token is gemarkeerd." lightbox="media/how-to-use-postman/postman-paste-token-custom.png":::

Wanneer u klaar bent met de bovenstaande stappen, bent u klaar met het configureren van de verzameling. Als u wilt, kunt u het tabblad bewerken voor de nieuwe verzameling sluiten.

De nieuwe verzameling kan worden weer gegeven in de hoofd weergave van Postman op het tabblad verzamelingen.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-custom.png" alt-text="Scherm afbeelding van het hoofd venster van postman. De zojuist gemaakte aangepaste verzameling wordt op het tabblad verzamelingen gemarkeerd." lightbox="media/how-to-use-postman/postman-post-collection-custom.png":::

## <a name="add-an-individual-request"></a>Een afzonderlijke aanvraag toevoegen

Nu uw verzameling is ingesteld, kunt u uw eigen aanvragen toevoegen aan de digitale twee Api's van Azure.

1. Als u een aanvraag wilt maken, gebruikt u opnieuw de knop **Nieuw** .

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Scherm afbeelding van het hoofd venster van postman. De knop Nieuw is gemarkeerd." lightbox="media/how-to-use-postman/postman-new.png":::

    Kies een type **aanvraag**.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Scherm opname van het dialoog venster ' nieuw maken ' in het bericht. De optie ' aanvraag ' is gemarkeerd.":::

1. Met deze actie wordt het venster aanvraag opslaan geopend, waarin u een naam kunt invoeren voor uw aanvraag, een optionele beschrijving geven en de verzameling kiest waarvan het deel uitmaakt. Vul de details in en sla de aanvraag op in de verzameling die u eerder hebt gemaakt.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Scherm opname van het venster ' aanvraag opslaan ' in het bericht dat wordt weer gegeven in de velden die worden beschreven. De knop Opslaan in azure Digital Apparaatdubbels Collection is gemarkeerd.":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

U kunt nu uw aanvraag bekijken in de verzameling en deze selecteren om de bewerkte details op te halen.

:::image type="content" source="media/how-to-use-postman/postman-request-details-custom.png" alt-text="Scherm opname van postman. De Azure Digital Apparaatdubbels-verzameling is uitgevouwen om de details van de aanvraag weer te geven." lightbox="media/how-to-use-postman/postman-request-details-custom.png":::

### <a name="set-request-details"></a>Details van aanvraag instellen

Als u een postman-aanvraag naar een van de Azure Digital Apparaatdubbels-Api's wilt maken, hebt u de URL van de API nodig en kunt u informatie over de benodigde details. U kunt deze informatie vinden in de [referentie documentatie voor Azure Digital apparaatdubbels rest API](/rest/api/azure-digitaltwins/).

Om door te gaan met een voorbeeld query, wordt in dit artikel de query-API (en de bijbehorende [referentie documentatie](/rest/api/digital-twins/dataplane/query/querytwins)) gebruikt om een query uit te voeren voor alle digitale apparaatdubbels in een exemplaar.

1. Haal de aanvraag-URL en het type van de referentie documentatie op. Voor de query-API is dit momenteel *een `https://digitaltwins-hostname/query?api-version=2020-10-31` bericht*.
1. Stel in postman het type voor de aanvraag in en voer de aanvraag-URL in, waarbij tijdelijke aanduidingen in de URL worden ingevuld zoals vereist. Hier gebruikt u de **hostnaam** van uw exemplaar in het gedeelte [*vereisten*](#prerequisites) .
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Scherm afbeelding van de details van de nieuwe aanvraag in postman. De query-URL uit de referentie documentatie is ingevuld in het vak aanvraag-URL." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Controleer of de para meters die voor de aanvraag worden weer gegeven op het tabblad **params** overeenkomen met die in de referentie documentatie. Voor deze aanvraag in Postman is de `api-version` para meter automatisch ingevuld wanneer de aanvraag-URL in de vorige stap is ingevoerd. Voor de query-API is dit de enige vereiste para meter, dus deze stap is voltooid.
1. Stel op het tabblad **autorisatie** het type in op **overnemen van auth van bovenliggend element**. Dit geeft aan dat met deze aanvraag de autorisatie wordt gebruikt die u eerder hebt ingesteld voor de hele verzameling.
1. Controleer of de kopteksten die voor de aanvraag op het tabblad **headers** worden weer gegeven, overeenkomen met de headers die in de referentie documentatie zijn beschreven. Voor deze aanvraag zijn verschillende headers automatisch gevuld. Voor de query-API zijn geen van de opties voor de header vereist, dus deze stap is voltooid.
1. Controleer of de hoofd tekst die voor de aanvraag in het tabblad **hoofd tekst** wordt weer gegeven, overeenkomt met de vereisten die in de referentie documentatie zijn beschreven. Voor de query-API is een JSON-hoofd tekst vereist voor het opgeven van de query tekst. Hier volgt een voor beeld van een hoofd tekst voor deze aanvraag die een query uitvoert voor alle digitale apparaatdubbels in het exemplaar:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="Scherm afbeelding van de details van de nieuwe aanvraag in Postman, op het tabblad hoofd tekst. Het bevat een onbewerkte JSON-hoofd tekst met een query van ' SELECT * FROM DIGITALTWINS '." lightbox="media/how-to-use-postman/postman-request-body.png":::

   Zie [*How-to: query the dubbele Graph*](how-to-query-graph.md)(Engelstalig) voor meer informatie over het opstellen van Azure Digital apparaatdubbels-query's.

1. Raadpleeg de referentie documentatie voor andere velden die mogelijk vereist zijn voor uw type aanvraag. Voor de query-API zijn nu aan alle vereisten voldaan in de Postman-aanvraag, dus deze stap is voltooid.
1. Gebruik de knop **verzenden** om uw voltooide aanvraag te verzenden.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Scherm afbeelding van de Postman met de details van de nieuwe aanvraag. De knop verzenden is gemarkeerd." lightbox="media/how-to-use-postman/postman-request-send.png":::

Nadat de aanvraag is verzonden, worden de antwoord gegevens weer gegeven in het venster postman onder de aanvraag. U kunt de status code en eventuele hoofd tekst van de reactie bekijken.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Scherm opname van de verzonden aanvraag in het bericht. Onder de details van de aanvraag wordt het antwoord weer gegeven. De status is 200 OK en de hoofd tekst van de query wordt weer gegeven." lightbox="media/how-to-use-postman/postman-request-response.png":::

U kunt ook het antwoord vergelijken met de verwachte reactie gegevens die zijn opgegeven in de referentie documentatie, om het resultaat te controleren of meer te weten te komen over fouten die zich voordoen.

## <a name="next-steps"></a>Volgende stappen

Lees voor meer informatie over de Digital Apparaatdubbels-Api's [*-instructies: gebruik de Azure Digital apparaatdubbels-api's en sdk's*](how-to-use-apis-sdks.md)of Bekijk de [referentie documentatie voor de rest api's](/rest/api/azure-digitaltwins/).