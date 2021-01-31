---
title: 'Zelfstudie: Facebook-inhoud controleren met Content Moderator'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie leert u hoe u met behulp van machine learning en Content Moderator berichten en opmerkingen voor Facebook kunt controleren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: pafarley
ms.openlocfilehash: f3b43ed6a86276b308599f9091d581423b0f363c
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220987"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Zelfstudie: Facebook-berichten en -opmerkingen controleren met Azure Content Moderator

In deze zelfstudie leert u hoe u met behulp van Azure Content Moderator berichten en opmerkingen op een Facebook-pagina kunt controleren. Inhoud die door bezoekers wordt geplaatst, wordt door Facebook naar de Content Moderator-service verstuurd. De werkstromen van uw Content Moderator zorgen ervoor dat de inhoud wordt gepubliceerd of dat er beoordelingen worden gemaakt binnen het beoordelingsprogramma, afhankelijk van scores voor inhoud en drempelwaarden. Zie de [demonstratievideo Build 2017](https://channel9.msdn.com/Events/Build/2017/T6033) voor een praktijkvoorbeeld van dit scenario.

> [!IMPORTANT]
> In 2018 heeft Facebook een strenger doorlichtingsbeleid geïmplementeerd voor Facebook-apps. U kunt de stappen van deze zelfstudie niet voltooien als uw app niet is beoordeeld en goedgekeurd door het Facebook-beoordelingsteam.

In deze zelfstudie ontdekt u hoe u:

> [!div class="checklist"]
> * Een Content Moderator-team samenstellen.
> * Azure-functies maken die luisteren naar HTTP-gebeurtenissen van Content Moderator en Facebook.
> * Koppel een Facebook-pagina aan Content Moderator met een Facebook-toepassing.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint.

Dit diagram toont elk component van dit scenario:

![Diagram van Content Moderator die informatie ontvangt van Facebook door 'FBListener' en informatie verzend door 'CMListener'](images/tutorial-facebook-moderation.png)

## <a name="prerequisites"></a>Vereisten

- Een abonnementssleutel voor Content Moderator. Volg de instructies in [Een Cognitive Services-account maken](../cognitive-services-apis-create-account.md) om u te abonneren op Content Moderator en een sleutel op te halen.
- Een [Facebook-account](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Een beoordelingsteam maken

Zie de quickstart [Content Moderator proberen op internet](quick-start.md) om te lezen hoe u zich kunt aanmelden voor het beoordelingsprogramma [Content Moderator](https://contentmoderator.cognitive.microsoft.com/) en een beoordelingsteam kunt samenstellen. Noteer de waarde voor **Team Id** op de pagina **Create review team**.

## <a name="configure-image-moderation-workflow"></a>Werkstroom voor controle van afbeeldingen configureren

Raadpleeg de gids [Werkstromen definiëren, testen en gebruiken](review-tool-user-guide/workflows.md) om een aangepaste afbeeldingenwerkstroom te maken. In Content Moderator wordt deze werkstroom gebruikt om automatisch de afbeeldingen op Facebook te controleren en een aantal naar het beoordelingsprogramma te zenden. Noteer de **naam** van de werkstroom.

## <a name="configure-text-moderation-workflow"></a>Werkstroom voor controle van tekst configureren

Raadpleeg opnieuw de gids [Werkstromen definiëren, testen en gebruiken](review-tool-user-guide/workflows.md), dit keer om een aangepaste tekstwerkstroom te maken. In Content Moderator wordt deze werkstroom gebruikt om automatisch tekstinhoud te controleren. Noteer de **naam** van de werkstroom.

![Tekstwerkstroom configureren](images/text-workflow-configure.PNG)

Test uw werkstroom met behulp van de knop **Werkstroom uitvoeren**.

![Tekstwerkstroom testen](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Azure-functies maken

Meld u aan bij de [Azure-portal](https://portal.azure.com/) en voer de volgende stappen uit:

1. Maak een Azure-functie-app zoals wordt weergegeven op de pagina [Azure Functions](../../azure-functions/functions-create-function-app-portal.md).
1. Open de zojuist gemaakte functie-app.
1. Ga in de app naar het tabblad **Platformfuncties** en selecteer **Configuratie**. Selecteer in het gedeelte **Toepassingsinstellingen** op de volgende pagina **Nieuwe toepassingsinstelling** om de volgende sleutel-/waardeparen toe te voegen:
    
    | Naam app-instelling | waarde   | 
    | -------------------- |-------------|
    | `cm:TeamId`   | De id van het Content Moderator-team  | 
    | `cm:SubscriptionKey` | Uw abonnementssleutel voor Content Moderator: zie [Referenties](./review-tool-user-guide/configure.md#credentials) |
    | `cm:Region` | De naam van uw Content Moderator-regio, zonder de spaties. U kunt deze naam vinden in het veld **Locatie** van het tabblad **Overzicht** van uw Azure-resource.|
    | `cm:ImageWorkflow` | De naam van de werkstroom om uit te voeren voor afbeeldingen |
    | `cm:TextWorkflow` | De naam van de werkstroom om uit te voeren voor tekst |
    | `cm:CallbackEndpoint` | URL voor de CMListener-functie-app die u verderop in deze handleiding maakt |
    | `fb:VerificationToken` | Een geheim token dat u maakt, wordt gebruikt om u te abonneren op de feed met Facebook-gebeurtenissen |
    | `fb:PageAccessToken` | Het toegangstoken voor de Facebook Graph-API verloopt niet en maakt het mogelijk de functie voor het verbergen/verwijderen van berichten namens u uit te voeren. U ontvangt dit token in een latere stap. |

    Klik op de knop **Opslaan** bovenaan de pagina.

1. Ga terug naar het tabblad **Platformfuncties**. Gebruik de knop **+** in het linkerdeelvenster om het deelvenster **Nieuwe functie** weer te geven. De functie die u gaat maken, ontvangt gebeurtenissen van Facebook.

    ![Deelvenster Azure Functions met de knop Functie toevoegen gemarkeerd.](images/new-function.png)

    1. Klik op de tegel met de naam **HttpTrigger**.
    1. Voer de naam **FBListener** in. Stel **Autorisatieniveau** in op **Functie**.
    1. Klik op **Create**.
    1. Vervang de inhoud van **run.csx** door de inhoud van **FbListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. Maak een nieuwe **Http-tigger** functie met de naam **CMListener**. Deze functie ontvangt gebeurtenissen van Content Moderator. Vervang de inhoud van **run.csx** door de inhoud van **CMListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>De Facebook-pagina en -app configureren

1. Maak een Facebook-app.

    ![Facebook-ontwikkelaarspagina](images/facebook-developer-app.png)

    1. Navigeer naar de [site voor Facebook-ontwikkelaars](https://developers.facebook.com/)
    1. Ga naar **mijn apps**.
    1. Voeg een nieuw app toe.
    1. Geef een naam op
    1. Selecteer **Webhooks -> Instellen**
    1. Selecteer **Pagina** in het vervolgkeuzemenu en selecteer **Abonneren op dit object**
    1. Geef **FBListener Url** op als de Callback URL en het **Verify Token** dat u hebt geconfigureerd onder **Function App Settings**
    1. Als u zich hebt geabonneerd, bladert u omlaag naar de feed en selecteert u **subscribe**.
    1. Selecteer de knop **testen** van de rij **invoer** om een test bericht naar de Azure-functie FBListener te verzenden en klik vervolgens op de knop **verzenden naar mijn server** . U zou moeten zien dat de aanvraag wordt ontvangen op uw FBListener.

1. Maak een Facebook-pagina.

    > [!IMPORTANT]
    > In 2018 heeft Facebook een strengere doorlichting geïmplementeerd voor Facebook-apps. U kunt de gedeelten 2, 3 en 4 niet voltooien als uw app niet is beoordeeld en goedgekeurd door het Facebook-beoordelingsteam.

    1. Navigeer naar [Facebook](https://www.facebook.com/bookmarks/pages) en maak een **nieuwe Facebook-pagina**.
    1. Volg deze stappen om toe te staan dat de Facebook-app toegang heeft tot deze pagina:
        1. Navigeer naar de [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
        1. Selecteer **Application**.
        1. Selecteer **Page Access Token**, verstuur een **Get**-aanvraag.
        1. Selecteer de **pagina-id** in het antwoord.
        1. Voeg de **/subscribed_apps** toe aan de URL en verstuur een **Get**-aanvraag (leeg antwoord).
        1. Verstuur een **Post**-aanvraag. U krijgt het antwoord als **success: true**.

3. Maak een Graph API-toegangstoken dat niet verloopt.

    1. Navigeer naar de [Graph API Explorer](https://developers.facebook.com/tools/explorer/).
    2. Selecteer de optie **Application**.
    3. Selecteer de optie **Get User Access Token**.
    4. Selecteer onder **Select Permissions** de opties **manage_pages** en **publish_pages**.
    5. We gebruiken het **toegangstoken** (token met korte levensduur) in de volgende stap.

4. We gebruiken Postman voor de volgende stappen.

    1. Open **Postman** (of downloadt het programma [hier](https://www.getpostman.com/)).
    2. Importeer deze twee bestanden:
        1. [Postman Collection](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Postman Environment](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. Werk deze omgevingsvariabelen bij:
    
        | Sleutel | Waarde   | 
        | -------------------- |-------------|
        | appId   | Voeg hier de id van uw Facebook-app in  | 
        | appSecret | Voeg hier het geheim van uw Facebook-app in | 
        | token_met_korte_levensduur | Voeg hier het toegangstoken met korte levensduur van de gebruiker in dat u in de vorige stap hebt gegenereerd |
    4. Voer nu de drie API's uit die in de verzameling worden vermeld: 
        1. Selecteer **Generate Long-Lived Access Token** en klik op **Send**.
        2. Selecteer **Get User ID** en klik op **Send**.
        3. Selecteer **Get Permanent Page Access Token** en klik op **Send**.
    5. Kopieer de waarde van **access_token** in het antwoord en wijs deze toe aan de app-instelling, **fb:PageAccessToken**.

De oplossing verzendt alle afbeeldingen en tekst die op uw Facebook-pagina worden geplaatst naar Content Moderator. De werkstromen die u eerder hebt geconfigureerd, worden dan aangeroepen. De inhoud die niet voldoet aan de criteria die zijn gedefinieerd in de werkstromen, wordt doorgegeven ter beoordeling binnen het beoordelingsprogramma. De rest van de inhoud wordt automatisch gepubliceerd.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een programma gemaakt voor het analyseren van productafbeeldingen, deze te taggen op producttype, en een beoordelingsteam te laten beslissen of de afbeeldingen al dan niet geschikt zijn. In de volgende zelfstudie wordt meer aandacht besteed aan het beoordelen van gecontroleerde afbeeldingen.

> [!div class="nextstepaction"]
> [Beheer van afbeeldingen](./image-moderation-api.md)