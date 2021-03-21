---
title: Verbinding maken met REST-eind punten van Azure Logic Apps
description: REST-eind punten in geautomatiseerde taken, processen en werk stromen bewaken met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: 810aaae9634a7de8d07b6d49edd0c6c2eda96754
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730878"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>REST-eind punten aanroepen met behulp van Azure Logic Apps

Met [Azure Logic apps](../logic-apps/logic-apps-overview.md) en de ingebouwde http + Swagger-connector kunt u werk stromen automatiseren die regel matig een wille keurig rest-eind punt aanroepen via een [Swagger-bestand](https://swagger.io) door Logic apps te bouwen. De trigger en actie voor HTTP + Swagger werken hetzelfde als de [http-trigger en actie](connectors-native-http.md) , maar bieden een betere ervaring in de ontwerp functie voor de logische app door de API-structuur en-uitvoer weer te geven die worden beschreven door het Swagger-bestand. Als u een polling trigger wilt implementeren, volgt u het polling-patroon dat wordt beschreven in [aangepaste Api's maken voor het aanroepen van andere api's, services en systemen vanuit Logic apps](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De URL voor het Swagger-bestand (niet OpenAPI) dat het doel REST-eind punt beschrijft

  Normaal gesp roken moet het REST-eind punt voldoen aan deze criteria om de connector te laten werken:

  * Het Swagger-bestand moet worden gehost op een HTTPS-URL die openbaar toegankelijk is.
  
  * Het Swagger-bestand moet een `operationID` voor elke bewerking in de definitie bevatten. Als dat niet het geval is, wordt in de connector alleen de laatste bewerking in het Swagger-bestand weer gegeven. 

  * Voor het Swagger-bestand moet [Cross-Origin Resource Sharing (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) zijn ingeschakeld.

  Als u wilt verwijzen naar een Swagger-bestand dat niet wordt gehost of niet voldoet aan de beveiligings-en cross-Origin-vereisten, kunt u [het Swagger-bestand uploaden naar een BLOB-container in een Azure-opslag account](#host-swagger)en CORS inschakelen voor dat opslag account, zodat u naar het bestand kunt verwijzen.

  In de voor beelden in dit onderwerp wordt gebruikgemaakt van de [Cognitive Services Face-API](../cognitive-services/face/overview.md), waarvoor een [Cognitive Services account en toegangs sleutel](../cognitive-services/cognitive-services-apis-create-account.md)is vereist.

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

* De logische app waarvan u het doel eindpunt wilt aanroepen. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om te beginnen met de http + Swagger-trigger. Als u de actie HTTP + Swagger wilt gebruiken, start u de logische app met een wille keurige trigger. In dit voor beeld wordt de HTTP + Swagger-trigger als de eerste stap gebruikt.

## <a name="add-an-http--swagger-trigger"></a>Een HTTP + Swagger-trigger toevoegen

Deze ingebouwde trigger verzendt een HTTP-aanvraag naar een URL voor een Swagger-bestand waarin een REST API wordt beschreven en een antwoord wordt geretourneerd dat de inhoud van het bestand bevat.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Open uw lege logische app in de ontwerp functie voor logische apps.

1. Voer op de ontwerp functie in het zoekvak ' Swagger ' in als uw filter. Selecteer in de lijst **Triggers** de **http + Swagger-** trigger.

   ![HTTP + Swagger-trigger selecteren](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. Voer in het vak **URL van Swagger-eind punt** de URL voor het SWAGGER-bestand in en selecteer **volgende**.

   In dit voor beeld wordt de Swagger-URL gebruikt die zich bevindt in de regio vs-West voor het [Cognitive Services Face-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Scherm opname van de logica van de logische app met de trigger ' H T/m P + Swagger ' en de eigenschap ' Swagger endpoint U R L ' ingesteld op een U R L-waarde.](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Wanneer de ontwerp functie de bewerkingen weergeeft die worden beschreven door het Swagger-bestand, selecteert u de bewerking die u wilt gebruiken.

   ![Scherm opname van de Logic app Designer met de trigger ' H T/m + Swagger ' en een lijst met Swagger-bewerkingen.](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Geef de waarden op voor de trigger parameters, die variëren op basis van de geselecteerde bewerking, die u wilt opnemen in de eindpunt aanroep. Stel het terugkeer patroon in voor de frequentie waarmee u wilt dat de trigger het eind punt aanroept.

   In dit voor beeld wordt de naam van de trigger gewijzigd in ' HTTP + Swagger trigger: Face-detect ' zodat de stap een meer beschrijvende naam heeft.

   ![Scherm opname van de Logic app Designer met de trigger ' H T/m + Swagger ' die de bewerking ' face-detect ' weergeeft.](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Als u andere beschik bare para meters wilt toevoegen, opent u de lijst **nieuwe para meter toevoegen** en selecteert u de gewenste para meters.

   Zie [verificatie toevoegen aan uitgaande oproepen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)voor meer informatie over de beschik bare verificatie typen voor http + Swagger.

1. Ga door met het bouwen van de werk stroom van uw logische app met acties die worden uitgevoerd wanneer de trigger wordt geactiveerd.

1. Wanneer u klaar bent, moet u uw logische app opslaan. Selecteer **Opslaan** op de werkbalk van de ontwerper.

## <a name="add-an-http--swagger-action"></a>Een HTTP + Swagger-actie toevoegen

Deze ingebouwde actie maakt een HTTP-aanvraag voor de URL voor het Swagger-bestand waarin een REST API wordt beschreven en retourneert een antwoord met daarin de inhoud van dat bestand.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Open uw logische app in de ontwerp functie voor logische apps.

1. Selecteer **nieuwe stap** onder de stap waar u de http + Swagger-actie wilt toevoegen.

   Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. Selecteer het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Voer op de ontwerp functie in het zoekvak ' Swagger ' in als uw filter. Selecteer in de lijst **acties** de actie **http + Swagger** .

    ![HTTP + Swagger-actie selecteren](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. Voer in het vak **URL van Swagger-eind punt** de URL voor het SWAGGER-bestand in en selecteer **volgende**.

   In dit voor beeld wordt de Swagger-URL gebruikt die zich bevindt in de regio vs-West voor het [Cognitive Services Face-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![URL voor Swagger-eind punt invoeren](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Wanneer de ontwerp functie de bewerkingen weergeeft die worden beschreven door het Swagger-bestand, selecteert u de bewerking die u wilt gebruiken.

   ![Bewerkingen in het Swagger-bestand](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Geef de waarden op voor de actie parameters, die variëren op basis van de geselecteerde bewerking, die u wilt opnemen in de eindpunt aanroep.

   Dit voor beeld heeft geen para meters, maar de naam van de actie wordt gewijzigd in ' HTTP + Swagger-actie: Face-identify ' zodat de stap een meer beschrijvende naam heeft.

   ![Bewerkingsdetails](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Als u andere beschik bare para meters wilt toevoegen, opent u de lijst **nieuwe para meter toevoegen** en selecteert u de gewenste para meters.

   Zie [verificatie toevoegen aan uitgaande oproepen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)voor meer informatie over de beschik bare verificatie typen voor http + Swagger.

1. Wanneer u klaar bent, moet u uw logische app opslaan. Selecteer **Opslaan** op de werkbalk van de ontwerper.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Swagger in Azure Storage hosten

U kunt verwijzen naar een Swagger-bestand dat niet wordt gehost of niet voldoet aan de beveiligings-en cross-Origin-vereisten door dat bestand te uploaden naar BLOB-container in een Azure-opslag account en CORS in te scha kelen voor dat opslag account. Voer de volgende stappen uit om Swagger-bestanden te maken, in te stellen en op te slaan in Azure Storage:

1. [Maak een Azure Storage-account](../storage/common/storage-account-create.md).

1. Schakel nu CORS in voor de blob. Selecteer **CORS** in het menu van uw opslag account. Geef op het tabblad **BLOB service** deze waarden op en selecteer vervolgens **Opslaan**.

   | Eigenschap | Waarde |
   |----------|-------|
   | **Toegestane oorsprongen** | `*` |
   | **Toegestane methoden** | `GET`, `HEAD`, `PUT` |
   | **Toegestane headers** | `*` |
   | **Weer gegeven kopteksten** | `*` |
   | **Max. duur** (in seconden) | `200` |
   |||

   Hoewel in dit voor beeld de [Azure Portal](https://portal.azure.com)wordt gebruikt, kunt u een hulp programma zoals [Azure Storage Explorer](https://storageexplorer.com/)gebruiken of deze instelling automatisch configureren met behulp van dit [Power shell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)-voorbeeld script.

1. [Maak een BLOB-container](../storage/blobs/storage-quickstart-blobs-portal.md). Selecteer **toegangs niveau wijzigen** in het deel venster **overzicht** van de container. Selecteer in de lijst **openbaar toegangs niveau** de optie **BLOB (anonieme lees toegang alleen voor blobs)** en selecteer **OK**.

1. [Upload het Swagger-bestand naar de BLOB-container](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), hetzij via de [Azure Portal](https://portal.azure.com) of [Azure Storage Explorer](https://storageexplorer.com/).

1. Als u wilt verwijzen naar het bestand in de BLOB-container, haalt u de HTTPS-URL op die volgt op de notatie (hoofdletter gevoelig) van Azure Storage Explorer:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<complete-swagger-file-name>?<query-parameters>`

## <a name="connector-reference"></a>Connector-verwijzing

Hier vindt u meer informatie over de uitvoer van een HTTP + Swagger-trigger of-actie. De HTTP + Swagger-aanroep retourneert deze informatie:

| Naam van eigenschap | Type | Beschrijving |
|---------------|------|-------------|
| koppen | object | De headers van de aanvraag |
| body | object | JSON-object | Het object met de inhoud van de hoofd tekst van de aanvraag |
| statuscode | int | De status code van de aanvraag |
|||

| Statuscode | Description |
|-------------|-------------|
| 200 | OK |
| 202 | Geaccepteerd |
| 400 | Ongeldige aanvraag |
| 401 | Niet geautoriseerd |
| 403 | Verboden |
| 404 | Niet gevonden |
| 500 | Interne serverfout. Er is een onbekende fout opgetreden. |
|||

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)
