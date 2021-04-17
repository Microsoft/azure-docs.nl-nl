---
title: Verzoeken indienen met Postman
titleSuffix: Azure Digital Twins
description: Meer informatie over het configureren en gebruiken van Postman om de api'Azure Digital Twins testen.
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: a528e224511fda363afb80a7749a018e07b5fa26
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588000"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Postman gebruiken om aanvragen te verzenden naar de Azure Digital Twins API's

[Postman](https://www.getpostman.com/) is een REST-testprogramma dat belangrijke HTTP-aanvraagfunctionaliteiten biedt in een bureaublad- en invoegtoepassingsgebaseerde GUI. U kunt deze gebruiken om HTTP-aanvragen te maken en deze te verzenden [naar de Azure Digital Twins REST API's](how-to-use-apis-sdks.md).

In dit artikel wordt beschreven hoe u de [Postman REST-client](https://www.getpostman.com/) configureert voor interactie met de Azure Digital Twins API's door de volgende stappen uit te voeren:

1. Gebruik de Azure CLI om [**een Bearer-token op te halen**](#get-bearer-token) dat u gaat gebruiken om API-aanvragen te maken in Postman.
1. Stel een [**Postman-verzameling in en**](#about-postman-collections) configureer de Postman REST-client om uw Bearer-token te gebruiken voor verificatie. Bij het instellen van de verzameling kunt u een van de volgende opties kiezen:
    1. [**Importeer**](#import-collection-of-azure-digital-twins-apis) een vooraf gebouwde verzameling Azure Digital Twins API-aanvragen.
    1. [**Maak**](#create-your-own-collection) uw eigen verzameling vanaf nul.
1. [**Voeg aanvragen toe**](#add-an-individual-request) aan de geconfigureerde verzameling en verzend deze naar de Azure Digital Twins API's.

Azure Digital Twins bevat twee sets API's die u kunt gebruiken: **gegevensvlak** en **besturingsvlak.** Zie How-to: Use the Azure Digital Twins [*APIs and SDKs*](how-to-use-apis-sdks.md)(De api's en SDK's gebruiken) voor meer informatie over het verschil tussen deze API-sets. Dit artikel bevat informatie voor beide API-sets.

## <a name="prerequisites"></a>Vereisten

Als u Postman wilt gebruiken voor toegang tot de Azure Digital Twins API's, moet u een Azure Digital Twins instellen en Postman downloaden. In de rest van deze sectie krijgt u stapsgewijze instructies.

### <a name="set-up-azure-digital-twins-instance"></a>Een Azure Digital Twins-exemplaar instellen

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Postman downloaden

Download vervolgens de desktopversie van de Postman-client. [*Navigeer www.getpostman.com/apps*](https://www.getpostman.com/apps) en volg de aanwijzingen om de app te downloaden.

## <a name="get-bearer-token"></a>Bearer-token op halen

Nu u Postman en uw Azure Digital Twins-exemplaar hebt ingesteld, moet u een bearer-token krijgen dat Postman-aanvragen kunnen gebruiken om te autoreren voor de Azure Digital Twins API's.

Er zijn verschillende manieren om dit token te verkrijgen. In dit artikel wordt de [Azure CLI gebruikt](/cli/azure/install-azure-cli) om u aan te melden bij uw Azure-account en op die manier een token te verkrijgen.

Als u een Azure CLI lokaal [hebt geïnstalleerd,](/cli/azure/install-azure-cli)kunt u een opdrachtprompt op uw computer starten om de volgende opdrachten uit te voeren.
Anders kunt u een venster [Azure Cloud Shell](https://shell.azure.com) in uw browser openen en daar de opdrachten uitvoeren.

1. Zorg er eerst voor dat u met de juiste referenties bent aangemeld bij Azure door deze opdracht uit te voeren:

    ```azurecli-interactive
    az login
    ```

2. Gebruik vervolgens de [opdracht az account get-access-token](/cli/azure/account#az_account_get_access_token) om een bearer-token op te halen met toegang tot de Azure Digital Twins service. In deze opdracht geeft u de resource-id voor het Azure Digital Twins-service-eindpunt door om een toegangsken op te halen dat toegang heeft tot Azure Digital Twins resources. 

    De vereiste context voor het token is afhankelijk van welke set API's u [](how-to-use-apis-sdks.md#overview-data-plane-apis) gebruikt. Gebruik daarom de onderstaande tabbladen om te selecteren tussen de API's voor het gegevensvlak en [het besturingsvlak.](how-to-use-apis-sdks.md#overview-control-plane-apis)

    # <a name="data-plane"></a>[Gegevenslaag](#tab/data-plane)
    
    Gebruik de volgende statische  waarde voor de tokencontext om een token op te halen voor gebruik met de gegevensvlak-API's: `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Dit is de resource-id voor Azure Digital Twins service-eindpunt.
    
    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```
    
    # <a name="control-plane"></a>[Besturingsvlak](#tab/control-plane)
    
    Als u een token  wilt gebruiken met de API's van het besturingsvlak, gebruikt u de volgende waarde voor de tokencontext: `https://management.azure.com/` .
    
    ```azurecli-interactive
    az account get-access-token --resource https://management.azure.com/
    ```
    ---

    >[!NOTE]
    > Als u toegang nodig hebt tot uw Azure Digital Twins-exemplaar met behulp van een service-principal of gebruikersaccount dat bij een andere Azure Active Directory-tenant van het exemplaar hoort, moet u een **token** aanvragen bij de tenant 'home' van het Azure Digital Twins-exemplaar. Zie [*Procedure: app-verificatiecode schrijven*](how-to-authenticate-client.md#authenticate-across-tenants)voor meer informatie over dit proces.

3. Kopieer de waarde van `accessToken` in het resultaat en sla deze op voor gebruik in de volgende sectie. Dit is de **waarde van uw token** die u aan Postman geeft om uw aanvragen te autor toestemming te geven.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Schermopname van de console met het resultaat van de opdracht az account get-access-token. Het veld accessToken en de voorbeeldwaarde zijn gemarkeerd.":::

>[!TIP]
>Dit token is ten minste vijf minuten en maximaal 60 minuten geldig. Als er geen tijd meer is toegewezen voor het huidige token, kunt u de stappen in deze sectie herhalen om een nieuw token op te halen.

Vervolgens stelt u Postman in om dit token te gebruiken om API-aanvragen te maken voor Azure Digital Twins.

## <a name="about-postman-collections"></a>Over Postman-verzamelingen

Aanvragen in Postman worden opgeslagen in **verzamelingen** (groepen aanvragen). Wanneer u een verzameling maakt om uw aanvragen te groeperen, kunt u algemene instellingen op veel aanvragen tegelijk toepassen. Dit kan de autorisatie sterk vereenvoudigen als u van plan bent om meer dan één aanvraag te maken voor de Azure Digital Twins-API's, omdat u deze gegevens slechts één keer hoeft te configureren voor de hele verzameling.

Wanneer u met Azure Digital Twins werkt, kunt u aan de slag door een vooraf gebouwde verzameling van alle Azure Digital Twins [importeren.](#import-collection-of-azure-digital-twins-apis) U kunt dit doen als u de API's verkent en snel een project wilt instellen met aanvraagvoorbeelden.

U kunt er ook voor kiezen om [](#create-your-own-collection) helemaal opnieuw te beginnen door uw eigen lege verzameling te maken en deze te vullen met afzonderlijke aanvragen die alleen de API's aanroepen die u nodig hebt. 

In de volgende secties worden beide processen beschreven. De rest van het artikel vindt plaats in uw lokale Postman-toepassing, dus open nu de Postman-toepassing op uw computer.

## <a name="import-collection-of-azure-digital-twins-apis"></a>Verzameling van Azure Digital Twins-API's importeren

Een snelle manier om aan de slag te Azure Digital Twins in Postman is het importeren van een vooraf gebouwde verzameling aanvragen voor de Azure Digital Twins API's.

### <a name="download-the-collection-file"></a>Het verzamelingsbestand downloaden

De eerste stap bij het importeren van de API-set is het downloaden van een verzameling. Kies het tabblad hieronder voor uw keuze van gegevensvlak of besturingsvlak om de vooraf gebouwde verzamelingsopties te bekijken.

# <a name="data-plane"></a>[Gegevenslaag](#tab/data-plane)

Er zijn momenteel twee Azure Digital Twins gegevensvlakverzamelingen beschikbaar waar u uit kunt kiezen:
* [**Azure Digital Twins Postman Collection:**](https://github.com/microsoft/azure-digital-twins-postman-samples)deze verzameling biedt een eenvoudige aan de slag-ervaring voor Azure Digital Twins in Postman. De aanvragen bevatten voorbeeldgegevens, zodat u ze met minimale bewerkingen kunt uitvoeren. Kies deze verzameling als u een samenvattingsset met belangrijke API-aanvragen met voorbeeldgegevens wilt.
    - Als u de verzameling wilt zoeken, gaat u naar de koppeling naar de repo en opent u het *bestandpostman_collection.jsop*.
* Azure Digital Twins gegevensvlak **[Swagger:](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)** deze repo bevat het volledige Swagger-bestand voor de Azure Digital Twins API-set, dat als verzameling kan worden gedownload en geïmporteerd in Postman. Dit biedt een uitgebreide set van elke API-aanvraag, maar met lege gegevens in plaats van voorbeeldgegevens. Kies deze verzameling als u toegang wilt hebben tot elke API-aanroep en alle gegevens zelf wilt invullen.
    - Als u de verzameling wilt zoeken, gaat u naar de koppeling naar de repo en kiest u de map voor de meest recente specificatieversie. Open hier het bestand met de *naamdigitaltwins.jsop*.

# <a name="control-plane"></a>[Besturingsvlak](#tab/control-plane)

De verzameling die momenteel beschikbaar is voor het besturingsvlak is [**Azure Digital Twins besturingsvlak Swagger**](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Deze repo bevat het volledige Swagger-bestand voor de Azure Digital Twins API-set, die als een verzameling kan worden gedownload en geïmporteerd in Postman. Dit biedt een uitgebreide set van elke API-aanvraag.

Als u de verzameling wilt zoeken, gaat u naar de koppeling naar de repo en kiest u de map voor de meest recente specificatieversie. Open hier het bestand met de *naamdigitaltwins.jsop*.

---

Hier ziet u hoe u de gekozen verzameling downloadt naar uw computer, zodat u deze kunt importeren in Postman.
1. Gebruik de bovenstaande koppelingen om het verzamelingsbestand in GitHub in uw browser te openen.
1. Selecteer de **knop Onbewerkt** om de onbewerkte tekst van het bestand te openen.
    :::image type="content" source="media/how-to-use-postman/swagger-raw.png" alt-text="Schermopname van het gegevensvlak digitaltwins.jsbestand in GitHub. Er is een markering rond de knop Onbewerkt." lightbox="media/how-to-use-postman/swagger-raw.png":::
1. Kopieer de tekst uit het venster en plak deze in een nieuw bestand op uw computer.
1. Sla het bestand op met *de extensie .json* (de bestandsnaam mag zijn wat u wilt, zolang u het maar kunt onthouden om het bestand later te vinden).

### <a name="import-the-collection"></a>De verzameling importeren

Importeer vervolgens de verzameling in Postman.

1. Selecteer in het hoofdvenster van Postman de **knop** Importeren.
    :::image type="content" source="media/how-to-use-postman/postman-import-collection.png" alt-text="Schermopname van een nieuw geopend Postman-venster. De knop Importeren is gemarkeerd." lightbox="media/how-to-use-postman/postman-import-collection.png":::

1. Selecteer in het volgende venster Importeren de optie **Bestanden uploaden** en navigeer naar het verzamelingsbestand op de computer die u eerder hebt gemaakt. Selecteer Openen.
1. Selecteer de **knop Importeren** om te bevestigen.

    :::image type="content" source="media/how-to-use-postman/postman-import-collection-2.png" alt-text="Schermopname van het venster 'Importeren' van Postman, met het bestand dat als een verzameling moet worden geïmporteerd en de knop Importeren.":::

De zojuist geïmporteerde verzameling is nu te zien in de hoofdweergave van Postman, op het tabblad Verzamelingen.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-imported.png" alt-text="Schermopname van het hoofdvenster van Postman. De zojuist geïmporteerde verzameling is gemarkeerd op het tabblad Verzamelingen." lightbox="media/how-to-use-postman/postman-post-collection-imported.png":::

Ga vervolgens verder met de volgende sectie om een Bearer-token toe te voegen aan de verzameling voor autorisatie en verbind dit met uw Azure Digital Twins-exemplaar.

### <a name="configure-authorization"></a>Autorisatie configureren

Bewerk vervolgens de verzameling die u hebt gemaakt om enkele toegangsgegevens te configureren. Markeer de verzameling die u hebt gemaakt en selecteer het **pictogram Meer acties** weergeven om een menu op te halen. Selecteer **Bewerken**.

:::image type="content" source="media/how-to-use-postman/postman-edit-collection.png" alt-text="Schermopname van Postman. Het pictogram Meer acties weergeven voor de geïmporteerde verzameling is gemarkeerd en 'Bewerken' is gemarkeerd in de opties." lightbox="media/how-to-use-postman/postman-edit-collection.png":::

Volg deze stappen om een Bearer-token toe te voegen aan de verzameling voor autorisatie. Hier gebruikt u de **tokenwaarde** die u hebt verzameld in de sectie [Bearer-token](#get-bearer-token) ophalen om deze te gebruiken voor alle API-aanvragen in uw verzameling.

1. Zorg ervoor dat u zich op het tabblad Autorisatie in het dialoogvenster voor bewerken **voor uw** verzameling. 

    :::image type="content" source="media/how-to-use-postman/postman-authorization-imported.png" alt-text="Schermopname van het dialoogvenster voor het bewerken van de geïmporteerde verzameling in Postman, met het tabblad Autorisatie." lightbox="media/how-to-use-postman/postman-authorization-imported.png":::

1. Stel type in op **OAuth 2.0,** plak uw toegangsteken in het vak Toegangs token en selecteer **Opslaan.**

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-imported.png" alt-text="Schermopname van het dialoogvenster Postman bewerken voor de geïmporteerde verzameling op het tabblad Autorisatie. Type is OAuth 2.0 en het vak Toegangsteken is gemarkeerd." lightbox="media/how-to-use-postman/postman-paste-token-imported.png":::

### <a name="additional-configuration"></a>Aanvullende configuratie

# <a name="data-plane"></a>[Gegevenslaag](#tab/data-plane)

Als u een [](how-to-use-apis-sdks.md#overview-data-plane-apis) gegevensvlakverzameling maakt, helpt u de verzameling om eenvoudig  verbinding te maken met uw Azure Digital Twins resources door enkele variabelen in te stellen die bij de verzamelingen worden geleverd. Wanneer voor veel aanvragen in een verzameling dezelfde waarde is vereist (zoals de hostnaam van uw Azure Digital Twins-exemplaar), kunt u de waarde opslaan in een variabele die van toepassing is op elke aanvraag in de verzameling. Beide downloadbare verzamelingen voor Azure Digital Twins worden met vooraf gemaakte variabelen die u op verzamelingsniveau kunt instellen.

1. Ga nog steeds in het bewerkingsdialoogvenster voor uw verzameling naar **het tabblad** Variabelen.

1. Gebruik de **hostnaam** van uw exemplaar uit de sectie [*Vereisten*](#prerequisites) om het veld CURRENT VALUE van de relevante variabele in te stellen. Selecteer **Opslaan**.

    :::image type="content" source="media/how-to-use-postman/postman-variables-imported.png" alt-text="Schermopname van het dialoogvenster voor het bewerken van de geïmporteerde verzameling in Postman, met het tabblad Variabelen. Het veld HUIDIGE WAARDE is gemarkeerd." lightbox="media/how-to-use-postman/postman-variables-imported.png":::

1. Als uw verzameling aanvullende variabelen heeft, vult u deze waarden in en sla u deze op.

Wanneer u klaar bent met de bovenstaande stappen, bent u klaar met het configureren van de verzameling. U kunt indien nodig het tabblad Bewerken voor de verzameling sluiten.

# <a name="control-plane"></a>[Besturingsvlak](#tab/control-plane)

Als u een [besturingsvlakverzameling](how-to-use-apis-sdks.md#overview-control-plane-apis) maakt, hebt u alles gedaan wat u nodig hebt om de verzameling te configureren. U kunt het tabblad Bewerken voor de verzameling sluiten als u wilt en doorgaan naar de volgende sectie.

--- 

### <a name="explore-requests"></a>Aanvragen verkennen

Verken vervolgens de aanvragen in de Azure Digital Twins API-verzameling. U kunt de verzameling uitbreiden om de vooraf gemaakte aanvragen weer te geven (gesorteerd op bewerkingscategorie). 

Voor verschillende aanvragen is andere informatie over uw exemplaar en de gegevens ervan vereist. Als u alle informatie wilt zien die nodig is om een bepaalde aanvraag te maken, bekijkt u de aanvraagdetails in Azure Digital Twins REST API [referentiedocumentatie](/rest/api/azure-digitaltwins/).

U kunt de details van een aanvraag in de Postman-verzameling bewerken met behulp van deze stappen:

1. Selecteer deze in de lijst om de bewerkbare details op te halen. 

1. Vul waarden in voor de variabelen die worden vermeld op het **tabblad Params** onder **Padvariabelen.**

    :::image type="content" source="media/how-to-use-postman/postman-request-details-imported.png" alt-text="Schermopname van Postman. De verzameling wordt uit uitgebreid om een aanvraag weer te geven. De sectie Padvariabelen is gemarkeerd in de aanvraagdetails." lightbox="media/how-to-use-postman/postman-request-details-imported.png":::

1. Geef alle **benodigde headers of** **hoofdtekstdetails** op op de desbetreffende tabbladen.

Zodra alle vereiste gegevens zijn opgegeven, kunt u de aanvraag uitvoeren met de **knop** Verzenden.

U kunt ook uw eigen aanvragen toevoegen aan de verzameling met behulp van het proces dat wordt beschreven in de sectie [*Een afzonderlijke aanvraag*](#add-an-individual-request) toevoegen hieronder.

## <a name="create-your-own-collection"></a>Uw eigen verzameling maken

In plaats van de bestaande verzameling van alle Azure Digital Twins API's te importeren, kunt u ook uw eigen verzameling helemaal zelf maken. U kunt deze vervolgens vullen met afzonderlijke aanvragen met behulp van de [Azure Digital Twins REST API referentiedocumentatie](/rest/api/azure-digitaltwins/).

### <a name="create-a-postman-collection"></a>Een Postman-verzameling maken

1. Als u een verzameling wilt maken, selecteert **u de** knop Nieuw in het hoofdvenster van Postman.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Schermopname van het hoofdvenster van Postman. De knop Nieuw is gemarkeerd." lightbox="media/how-to-use-postman/postman-new.png":::

    Kies een type **verzameling**.

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-2.png" alt-text="Schermopname van het dialoogvenster Nieuwe maken in Postman. De optie Verzameling is gemarkeerd.":::

1. Hiermee opent u een tabblad voor het invullen van de details van de nieuwe verzameling. Selecteer het pictogram Bewerken naast de standaardnaam van de verzameling **(Nieuwe** verzameling) om deze te vervangen door uw eigen naam. 

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-3.png" alt-text="Schermopname van het bewerkingsdialoogvenster van de nieuwe verzameling in Postman. Het pictogram Bewerken naast de naam Nieuwe verzameling is gemarkeerd." lightbox="media/how-to-use-postman/postman-new-collection-3.png":::

Ga vervolgens verder met de volgende sectie om een bearer-token toe te voegen aan de verzameling voor autorisatie.

### <a name="configure-authorization"></a>Autorisatie configureren

Volg deze stappen om een Bearer-token toe te voegen aan de verzameling voor autorisatie. Hier gebruikt u de **tokenwaarde** die u hebt verzameld in de sectie [Bearer-token](#get-bearer-token) ophalen om deze te gebruiken voor alle API-aanvragen in uw verzameling.

1. Ga nog steeds in het bewerkingsdialoogvenster voor uw nieuwe verzameling naar het **tabblad Autorisatie.**

    :::image type="content" source="media/how-to-use-postman/postman-authorization-custom.png" alt-text="Schermopname van het dialoogvenster voor het bewerken van de nieuwe verzameling in Postman, met het tabblad Autorisatie." lightbox="media/how-to-use-postman/postman-authorization-custom.png":::

1. Stel type in op **OAuth 2.0,** plak uw toegangsteken in het vak Toegangs token en selecteer **Opslaan.**

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-custom.png" alt-text="Schermopname van het dialoogvenster Postman bewerken voor de nieuwe verzameling op het tabblad Autorisatie. Type is OAuth 2.0 en het vak Toegangsteken is gemarkeerd." lightbox="media/how-to-use-postman/postman-paste-token-custom.png":::

Wanneer u klaar bent met de bovenstaande stappen, bent u klaar met het configureren van de verzameling. U kunt indien nodig het tabblad Bewerken voor de nieuwe verzameling sluiten.

De nieuwe verzameling is te zien in de hoofdweergave van Postman, op het tabblad Verzamelingen.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-custom.png" alt-text="Schermopname van het hoofdvenster van Postman. De zojuist gemaakte aangepaste verzameling wordt gemarkeerd op het tabblad Verzamelingen." lightbox="media/how-to-use-postman/postman-post-collection-custom.png":::

## <a name="add-an-individual-request"></a>Een afzonderlijke aanvraag toevoegen

Nu uw verzameling is ingesteld, kunt u uw eigen aanvragen toevoegen aan de Azure Digital Twin-API's.

1. Als u een aanvraag wilt maken, gebruikt u **opnieuw de** knop Nieuw.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Schermopname van het hoofdvenster van Postman. De knop Nieuw is gemarkeerd." lightbox="media/how-to-use-postman/postman-new.png":::

    Kies een type **aanvraag**.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Schermopname van het dialoogvenster Nieuwe maken in Postman. De optie 'Aanvraag' is gemarkeerd.":::

1. Met deze actie wordt het venster SAVE REQUEST geopend, waarin u een naam voor uw aanvraag kunt invoeren, deze een optionele beschrijving kunt geven en de verzameling kunt kiezen waar deze deel van uitmaakt. Vul de gegevens in en sla de aanvraag op in de verzameling die u eerder hebt gemaakt.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Schermopname van het venster Aanvraag opslaan in Postman met de beschreven velden. De knop Opslaan Azure Digital Twins verzameling is gemarkeerd.":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

U kunt uw aanvraag nu weergeven onder de verzameling en deze selecteren om de bewerkbare details op te halen.

:::image type="content" source="media/how-to-use-postman/postman-request-details-custom.png" alt-text="Schermopname van Postman. De Azure Digital Twins verzameling wordt uitgebreid om de details van de aanvraag weer te geven." lightbox="media/how-to-use-postman/postman-request-details-custom.png":::

### <a name="set-request-details"></a>Aanvraagdetails instellen

Als u een Postman-aanvraag wilt indienen bij een van de Azure Digital Twins API's, hebt u de URL van de API en informatie nodig over de details die nodig zijn. U vindt deze informatie in de Azure Digital Twins REST API [referentiedocumentatie](/rest/api/azure-digitaltwins/).

Als u wilt doorgaan met een voorbeeldquery, gebruikt dit artikel de Query-API (en de referentiedocumentatie [)](/rest/api/digital-twins/dataplane/query/querytwins)om een query uit te voeren op alle digitale tweelingen in een exemplaar.

1. Haal de aanvraag-URL op en typ uit de referentiedocumentatie. Voor de Query-API is dit momenteel *POST. `https://digitaltwins-hostname/query?api-version=2020-10-31`*
1. Stel in Postman het type voor de aanvraag in en voer de aanvraag-URL in en vul waar nodig tijdelijke aanduidingen in de URL in. Hier gebruikt u de **hostnaam** van uw exemplaar uit de [*sectie*](#prerequisites) Vereisten.
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Schermopname van de details van de nieuwe aanvraag in Postman. De query-URL uit de referentiedocumentatie is ingevuld in het vak aanvraag-URL." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Controleer of de parameters voor de aanvraag op het **tabblad Parameters** overeenkomen met de parameters die worden beschreven in de referentiedocumentatie. Voor deze aanvraag in Postman werd de `api-version` parameter automatisch ingevuld toen de aanvraag-URL in de vorige stap werd ingevoerd. Voor de Query-API is dit de enige vereiste parameter, dus deze stap wordt uitgevoerd.
1. Stel op **het tabblad** Autorisatie het type in op Verificatie overnemen **van bovenliggende**. Dit geeft aan dat deze aanvraag de autorisatie gebruikt die u eerder hebt ingesteld voor de hele verzameling.
1. Controleer of de headers voor de aanvraag op het tabblad Headers overeenkomen met de **headers** die worden beschreven in de referentiedocumentatie. Voor deze aanvraag zijn er automatisch verschillende headers ingevuld. Voor de Query-API zijn geen van de headeropties vereist, dus deze stap is uitgevoerd.
1. Controleer of de hoofd body die wordt weergegeven voor de aanvraag op **het tabblad Hoofdwerk,** overeenkomt met de behoeften die worden beschreven in de referentiedocumentatie. Voor de Query-API is een JSON-body vereist om de querytekst op te geven. Hier is een voorbeeld van een body voor deze aanvraag die een query op alle digitale tweelingen in het exemplaar opvraagt:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="Schermopname van de details van de nieuwe aanvraag in Postman, op het tabblad Hoofd hoofd. Deze bevat een onbewerkte JSON-body met de query 'SELECT * FROM DIGITALTWINS'." lightbox="media/how-to-use-postman/postman-request-body.png":::

   Zie [*How-to: Query*](how-to-query-graph.md)the twin graph (Een query uitvoeren op de tweelinggrafiek) voor meer informatie over het maken van Azure Digital Twins query's.

1. Raadpleeg de referentiedocumentatie voor andere velden die mogelijk vereist zijn voor uw type aanvraag. Voor de Query-API is nu aan alle vereisten voldaan in de Postman-aanvraag, dus deze stap is uitgevoerd.
1. Gebruik de **knop Verzenden** om uw voltooide aanvraag te verzenden.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Schermopname van Postman met de details van de nieuwe aanvraag. De knop Verzenden is gemarkeerd." lightbox="media/how-to-use-postman/postman-request-send.png":::

Na het verzenden van de aanvraag worden de antwoorddetails weergegeven in het Postman-venster onder de aanvraag. U kunt de statuscode van het antwoord en elke tekst in de tekst bekijken.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Schermopname van de verzonden aanvraag in Postman. Onder de details van de aanvraag wordt het antwoord weergegeven. De status is 200 OK en de body geeft queryresultaten weer." lightbox="media/how-to-use-postman/postman-request-response.png":::

U kunt ook het antwoord vergelijken met de verwachte antwoordgegevens in de referentiedocumentatie om het resultaat te controleren of meer te weten te komen over eventuele fouten die zich voordoen.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over de Digital Twins-API's leest u [*How-to: Use the Azure Digital Twins APIs and SDKs*](how-to-use-apis-sdks.md)(De Azure Digital Twins-API's en SDK's gebruiken) of bekijkt u de referentiedocumentatie voor de [REST API's.](/rest/api/azure-digitaltwins/)