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
ms.openlocfilehash: d99ec80308152ce9e4870da809acaa25c663d98d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715697"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Postman gebruiken om aanvragen te verzenden naar de Azure Digital Apparaatdubbels-Api's

[Postman](https://www.getpostman.com/) is een hulp programma voor rest testen dat belang rijke HTTP-aanvraag functionaliteit biedt in een op een bureau blad gebaseerde GUI U kunt deze gebruiken om HTTP-aanvragen te bevragen en deze te verzenden naar de [rest-api's van Azure Digital apparaatdubbels](how-to-use-apis-sdks.md).

In dit artikel wordt beschreven hoe u de [postman rest-client](https://www.getpostman.com/) kunt configureren om te communiceren met de Azure Digital apparaatdubbels api's door de volgende stappen uit te voeren:

1. Gebruik de [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) om een Bearer-token op te halen die u gaat gebruiken om API-aanvragen in te voeren in de Postman.
1. Stel een postman-verzameling in en configureer de Postman REST-client om uw Bearer-token te gebruiken voor verificatie.
1. Gebruik de geconfigureerde postman voor het maken en verzenden van een aanvraag naar de Azure Digital Apparaatdubbels-Api's.

## <a name="prerequisites"></a>Vereisten

Als u wilt door gaan met het gebruik van Postman om toegang te krijgen tot de Azure Digital Apparaatdubbels Api's, moet u een Azure Digital Apparaatdubbels-exemplaar instellen en postman downloaden. In de rest van deze sectie krijgt u stapsgewijze instructies.

### <a name="set-up-azure-digital-twins-instance"></a>Een Azure Digital Twins-exemplaar instellen

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Berichtman downloaden

Down load vervolgens de desktop versie van de Postman-client. Navigeer naar [*www.getpostman.com/apps*](https://www.getpostman.com/apps) en volg de aanwijzingen om de app te downloaden.

## <a name="get-bearer-token"></a>Bearer-Token ophalen

Nu u postman en uw Azure Digital Apparaatdubbels-exemplaar hebt ingesteld, moet u een Bearer-token ontvangen dat is ingesteld op het gebruik van de digitale Apparaatdubbels-Api's van Azure.

Er zijn verschillende manieren om dit token op te halen. In dit artikel wordt de [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) gebruikt om u aan te melden bij uw Azure-account en een token op te halen.

Als u een Azure CLI [lokaal hebt geïnstalleerd](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), kunt u een opdracht prompt op uw computer starten om de volgende opdrachten uit te voeren.
Als dat niet het geval is, kunt u een [Azure Cloud shell](https://shell.azure.com) -venster openen in uw browser en de opdrachten daar uitvoeren.

1. Zorg er eerst voor dat u bent aangemeld bij Azure met de juiste referenties door deze opdracht uit te voeren:

    ```azurecli-interactive
    az login
    ```

1. Gebruik vervolgens de opdracht [AZ account Get-access-token](/cli/azure/account?preserve-view=true&view=azure-cli-latest#az_account_get_access_token) om een Bearer-token te verkrijgen met toegang tot de Azure Digital apparaatdubbels-service.

    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

1. Kopieer de waarde van `accessToken` in het resultaat en sla deze op voor gebruik in de volgende sectie. Dit is de **waarde** van uw token die u moet plaatsen om de aanvragen te verifiëren.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Weer gave van een lokale console venster met het resultaat van de opdracht AZ account Get-access-token. Een van de velden in het resultaat wordt accessToken en de bijbehorende voorbeeld waarde genoemd, beginnend met EY--is gemarkeerd.":::

>[!TIP]
>Dit token is geldig gedurende ten minste vijf minuten en Maxi maal 60 minuten. Als u geen tijd meer hebt toegewezen aan het huidige token, kunt u de stappen in deze sectie herhalen om een nieuwe te verkrijgen.

## <a name="set-up-postman-collection-and-authorization"></a>Postman-verzameling en-autorisatie instellen

Stel vervolgens postman in om API-aanvragen te maken.
Deze stappen worden uitgevoerd in uw lokale postman-toepassing. Ga dus verder met het openen van de toepassing postman op uw computer.

### <a name="create-a-postman-collection"></a>Een Postman-verzameling maken

Aanvragen in postman worden opgeslagen in **verzamelingen** (groepen aanvragen). Wanneer u een verzameling maakt om uw aanvragen te groeperen, kunt u algemene instellingen Toep assen op veel aanvragen tegelijk. Dit kan de autorisatie aanzienlijk vereenvoudigen als u van plan bent om meer dan één aanvraag te maken voor de Azure Digital Apparaatdubbels-Api's, omdat u slechts één keer verificatie hoeft te configureren voor de hele verzameling.

1. Als u een verzameling wilt maken, klikt u op de knop *+ nieuwe verzameling* .

    :::image type="content" source="media/how-to-use-postman/postman-new-collection.png" alt-text="Weer gave van een onlangs geopend venster van postman. De knop nieuwe verzameling is gemarkeerd":::

1. Geef in het venster *een nieuwe verzameling maken* dat volgt een **naam** en een optionele **Beschrijving** op voor uw verzameling.

Ga vervolgens verder met de volgende sectie om een Bearer-token toe te voegen aan de verzameling voor autorisatie.

### <a name="add-authorization-token-and-finish-collection"></a>Autorisatie token toevoegen en verzameling volt ooien

1. Ga in het dialoog venster *een nieuwe verzameling maken* naar het tabblad *autorisatie* . Hier gaat u de **token waarde** die u hebt verzameld, in de sectie [token ophalen](#get-bearer-token) plaatsen om deze te gebruiken voor alle API-aanvragen in uw verzameling.

    :::image type="content" source="media/how-to-use-postman/postman-authorization.png" alt-text="Het tabblad autorisatie wordt weer gegeven in het venster een nieuwe verzameling maken.":::

1. Stel het *type* in op _**OAuth 2,0**_ en plak uw toegangs token in het vak *toegangs token* .

    :::image type="content" source="media/how-to-use-postman/postman-paste-token.png" alt-text="Het tabblad autorisatie wordt weer gegeven in het venster een nieuwe verzameling maken. Een type ' OAuth 2,0 ' is geselecteerd en het toegangs token vak waarin de waarde van het toegangs token kan worden geplakt, is gemarkeerd.":::

1. Nadat u uw Bearer-token hebt geplakt, klikt u op *maken* om het maken van de verzameling te volt ooien.

Uw nieuwe verzameling kan nu worden weer gegeven in de hoofd weergave van Postman onder *verzamelingen*.

:::image type="content" source="media/how-to-use-postman/postman-post-collection.png" alt-text="Weer gave van het hoofd venster van postman. De zojuist gemaakte verzameling wordt op het tabblad verzamelingen gemarkeerd.":::

## <a name="create-a-request"></a>Een aanvraag maken

Nadat u de voor gaande stappen hebt voltooid, kunt u aanvragen maken voor de digitale twee Api's van Azure.

1. Als u een aanvraag wilt maken, klikt u op de knop *+ Nieuw* .

    :::image type="content" source="media/how-to-use-postman/postman-new-request.png" alt-text="Weer gave van het hoofd venster van postman. De knop Nieuw is gemarkeerd":::

1. Kies een *aanvraag*.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Weer gave van de opties die u kunt selecteren om iets nieuw te maken. De optie ' aanvraag ' is gemarkeerd":::

1. Met deze actie wordt het venster *aanvraag opslaan* geopend, waarin u een naam kunt invoeren voor uw aanvraag, een optionele beschrijving geven en de verzameling kiest waarvan het deel uitmaakt. Vul de details in en sla de aanvraag op in de verzameling die u eerder hebt gemaakt.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Weer gave van het venster aanvraag opslaan, waarin u de velden kunt invullen die worden beschreven. De knop Opslaan in azure Digital Apparaatdubbels-verzameling is gemarkeerd":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

U kunt nu uw aanvraag bekijken in de verzameling en deze selecteren om de bewerkte details op te halen.

:::image type="content" source="media/how-to-use-postman/postman-request-details.png" alt-text="Weer gave van het hoofd venster van postman. De verzameling Azure Digital Apparaatdubbels is uitgebreid en de aanvraag ' query apparaatdubbels ' is gemarkeerd. Details van de aanvraag worden weer gegeven in het midden van de pagina." lightbox="media/how-to-use-postman/postman-request-details.png":::

### <a name="set-request-details"></a>Details van aanvraag instellen

Als u een postman-aanvraag naar een van de Azure Digital Apparaatdubbels-Api's wilt maken, hebt u de URL van de API nodig en kunt u informatie over de benodigde details. U kunt deze informatie vinden in de [referentie documentatie voor Azure Digital apparaatdubbels rest API](/rest/api/azure-digitaltwins/).

Om door te gaan met een voorbeeld query, wordt in dit artikel de query-API (en de bijbehorende [referentie documentatie](/rest/api/digital-twins/dataplane/query/querytwins)) gebruikt om een query uit te voeren voor alle digitale apparaatdubbels in een exemplaar.

1. Haal de aanvraag-URL en het type van de referentie documentatie op. Voor de query-API is dit momenteel *een `https://digitaltwins-hostname/query?api-version=2020-10-31` bericht*.
1. Stel in postman het type voor de aanvraag in en voer de aanvraag-URL in, waarbij tijdelijke aanduidingen in de URL worden ingevuld zoals vereist. Hier gebruikt u de **hostnaam** van uw exemplaar in het gedeelte [*vereisten*](#prerequisites) .
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="In de details van de nieuwe aanvraag is de query-URL uit de referentie documentatie ingevuld in het vak aanvraag-URL." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Controleer of de para meters die voor de aanvraag worden weer gegeven op het tabblad *params* overeenkomen met die in de referentie documentatie. Voor deze aanvraag in Postman is de `api-version` para meter automatisch ingevuld wanneer de aanvraag-URL in de vorige stap is ingevoerd. Voor de query-API is dit de enige vereiste para meter, dus deze stap is voltooid.
1. Stel op het tabblad *autorisatie* het *type* in op *overnemen van auth van bovenliggend element*. Dit geeft aan dat met deze aanvraag de verificatie wordt gebruikt die u eerder hebt ingesteld voor de hele verzameling.
1. Controleer of de kopteksten die voor de aanvraag op het tabblad *headers* worden weer gegeven, overeenkomen met de headers die in de referentie documentatie zijn beschreven. Voor deze aanvraag zijn verschillende headers automatisch gevuld. Voor de query-API zijn geen van de opties voor de header vereist, dus deze stap is voltooid.
1. Controleer of de hoofd tekst die voor de aanvraag in het tabblad *hoofd tekst* wordt weer gegeven, overeenkomt met de vereisten die in de referentie documentatie zijn beschreven. Voor de query-API is een JSON-hoofd tekst vereist voor het opgeven van de query tekst. Hier volgt een voor beeld van een hoofd tekst voor deze aanvraag die een query uitvoert voor alle digitale apparaatdubbels in het exemplaar:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="In de details van de nieuwe aanvraag wordt het tabblad hoofd tekst weer gegeven. Het bevat een onbewerkte JSON-hoofd tekst met een query van ' SELECT * FROM DIGITALTWINS '." lightbox="media/how-to-use-postman/postman-request-body.png":::

   Zie [*How-to: query the dubbele Graph*](how-to-query-graph.md)(Engelstalig) voor meer informatie over het opstellen van Azure Digital apparaatdubbels-query's.

1. Raadpleeg de referentie documentatie voor andere velden die mogelijk vereist zijn voor uw type aanvraag. Voor de query-API zijn nu aan alle vereisten voldaan in de Postman-aanvraag, dus deze stap is voltooid.
1. Gebruik de knop *verzenden* om uw voltooide aanvraag te verzenden.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Bij de details van de nieuwe aanvraag wordt de verzend knop gemarkeerd." lightbox="media/how-to-use-postman/postman-request-send.png":::

Nadat de aanvraag is verzonden, worden de antwoord gegevens weer gegeven in het venster postman onder de aanvraag. U kunt de status code en eventuele hoofd tekst van de reactie bekijken.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Onder de details van de verzonden aanvraag worden de details van het antwoord gemarkeerd. Er is een status van 200 OK en hoofd tekst met een beschrijving van de digitale apparaatdubbels die door de query zijn geretourneerd." lightbox="media/how-to-use-postman/postman-request-response.png":::

U kunt ook het antwoord vergelijken met de verwachte reactie gegevens die zijn opgegeven in de referentie documentatie, om het resultaat te controleren of meer te weten te komen over fouten die zich voordoen.

## <a name="next-steps"></a>Volgende stappen

Lees voor meer informatie over de Digital Apparaatdubbels-Api's [*-instructies: gebruik de Azure Digital apparaatdubbels-api's en sdk's*](how-to-use-apis-sdks.md)of Bekijk de [referentie documentatie voor de rest api's](/rest/api/azure-digitaltwins/).