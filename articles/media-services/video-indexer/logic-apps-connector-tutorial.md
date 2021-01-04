---
title: De zelfstudie over Video Indexer-connectoren met Logic App en Power Automate.
description: In deze zelfstudie leert u hoe u nieuwe ervaringen en mogelijkheden om geld te verdienen kunt ontgrendelen met Video Indexer-connectors met Logic App en Power Automate.
author: anzaman
manager: johndeu
ms.author: alzam
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 5c545fa7d58d925dfcb9fa98b301c3bb0a20833e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358568"
---
# <a name="tutorial-use-video-indexer-with-logic-app-and-power-automate"></a>Zelfstudie: Video Indexer met Logic App en Power Automate gebruiken

De [REST-API van Video Indexer v2](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Delete-Video?) van Azure Media Services biedt ondersteuning voor zowel communicatie tussen servers onderling als tussen clients en servers, en stelt Video Indexer-gebruikers in staat om eenvoudig video- en audio-inzichten te integreren in hun toepassingslogica, wat nieuwe ervaringen en mogelijkheden om geld te verdienen oplevert.

Om de integratie nog gemakkelijker te maken, bieden we ondersteuning voor [Logic Apps](https://azure.microsoft.com/services/logic-apps/) en [Power Automate](https://preview.flow.microsoft.com/connectors/shared_videoindexer-v2/video-indexer-v2/) -connectoren die compatibel zijn met onze API. U kunt de connectoren gebruiken om aangepaste werkstromen in te stellen waarmee u inzichten doeltreffend kunt indexeren en extraheren uit een grote hoeveelheid video- en audio-bestanden zonder één regel code te schrijven. Daarnaast geeft het gebruik van connectoren voor uw integratie u meer inzicht in de status van uw werkstroom, en een gemakkelijke manier om fouten op te sporen.  

Om u snel op weg te helpen met de Video Indexer-connectoren, overlopen we snel een voorbeeld van een Logic App- en Power Automate-oplossing die u kunt instellen. Deze zelfstudie laat zien hoe u stromen kunt instellen met behulp van Logic Apps. De editors en mogelijkheden in beide oplossingen zijn echter bijna identiek, zodat de diagrammen en uitleg van toepassing zijn op zowel Logic Apps als Power Automate.

Het scenario voor het automatisch uploaden en indexeren van uw video dat in deze zelfstudie wordt behandeld, bestaat uit twee verschillende stromen die met elkaar samenwerken. 
* De eerste stroom wordt geactiveerd wanneer een blob wordt toegevoegd aan of gewijzigd in een Azure Storage-account. Het uploadt het nieuwe bestand naar Video Indexer met een callback-URL om een melding te verzenden zodra de indexering voltooid is. 
* De tweede stroom wordt geactiveerd op basis van de callback-URL en slaat de geëxtraheerde inzichten terug naar een JSON-bestand in Azure Storage. Deze aanpak met twee stromen wordt gebruikt om het asynchrone uploaden en indexeren van grotere bestanden effectief te laten verlopen. 

Deze zelfstudie maakt gebruik van Logic Apps en omvat het volgende:

> [!div class="checklist"]
> * De uploadstroom voor uw bestand instellen
> * De JSON-extractiestroom instellen

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Om te beginnen heeft u een Video Indexer-account nodig, alsook [toegang tot de API's via API-sleutel](video-indexer-use-apis.md). 
* U hebt ook een Azure Storage-account nodig. Noteer de toegangssleutel voor uw opslagaccount. Maak twee containers: één om video's op te slaan en één om inzichten van de Video Indexer op te slaan.  
* Vervolgens moet u twee afzonderlijke stromen openen in Logic Apps of Power Automate (afhankelijk van welke u gebruikt). 

## <a name="set-up-the-first-flow---file-upload"></a>De eerste stroom instellen: de uploadstroom voor uw bestand   

De eerste stroom wordt geactiveerd wanneer een blob wordt toegevoegd aan de Azure Storage-container. Zodra deze geactiveerd is, maakt het een SAS-URI die u kunt gebruiken om de video te uploaden en te indexeren in Video Indexer. In deze sectie gaat u de volgende stroom maken. 

![Uploadstroom bestand](./media/logic-apps-connector-tutorial/file-upload-flow.png)

Om de eerste stroom in te stellen, moet u uw Video Indexer-API-sleutel en Azure Storage-referenties opgeven. 

![Azure Blob Storage](./media/logic-apps-connector-tutorial/azure-blob-storage.png)

![Verbindingsnaam en API-sleutel](./media/logic-apps-connector-tutorial/connection-name-api-key.png)

> [!TIP]
> Als u eerder een Azure Storage-account of Video Indexer-account hebt verbonden met een logische app, zijn uw verbindingsgegevens opgeslagen en wordt u automatisch verbonden. <br/>U kunt de verbinding bewerken door te klikken op **Verbinding wijzigen** onder aan een Azure Storage (het opslagvenster) of Video Indexer (het spelervenster).

Als u eenmaal verbinding kunt maken met uw Azure Storage- en Video Indexer-accounts, zoekt u kiest u de trigger 'Wanneer een blob wordt toegevoegd of gewijzigd' in de **Logic Apps-ontwerpfunctie**.

Selecteer de container waarin u uw videobestanden wilt plaatsen. 

![Schermopname van het dialoogvenster 'Wanneer een blob wordt toegevoegd of gewijzigd' waarin u een container kunt selecteren.](./media/logic-apps-connector-tutorial/container.png)

Zoek en selecteer vervolgens de actie 'SAS URI op pad maken'. Selecteer in het dialoogvenster voor de actie de optie voor de lijst met bestandspaden in de opties voor dynamische inhoud.  

Voeg ook een nieuwe parameter toe met de naam 'Protocol voor gedeelde toegang'. Kies HttpsOnly (Alleen HTTPS) als waarde voor de parameter.

![SAS-uri op pad](./media/logic-apps-connector-tutorial/sas-uri-by-path.jpg)

Vul [uw accountlocatie](regions.md) en [account-id](./video-indexer-use-apis.md#account-id)  in om het Video Indexer-accounttoken op te halen.

![Toegangstoken van account ophalen](./media/logic-apps-connector-tutorial/account-access-token.png)

Vul voor 'Video en index uploaden' de vereiste parameters en video-URL in. Selecteer 'Nieuwe parameter toevoegen' en selecteer Callback-URL. 

![Uploaden en indexeren](./media/logic-apps-connector-tutorial/upload-and-index.png)

Laat de callback-URL voorlopig leeg. U voegt deze pas toe nadat u de tweede stroom voor het maken van de callback-URL hebt voltooid. 

U kunt voor de andere parameters de standaardwaarde gebruiken of deze instellen zoals u dat wenst. 

Klik op **Opslaan** om de tweede stroom te configureren en de inzichten op te halen zodra het uploaden en indexeren is voltooid. 

## <a name="set-up-the-second-flow---json-extraction"></a>De tweede stroom instellen: JSON-extractie  

Zodra de eerste stroom geüpload en geïndexeerd is, wordt een HTTP-aanvraag met de juiste callback-URL verzonden om de tweede stroom te activeren. Vervolgens haalt het inzichten op die door Video Indexer gegenereerd zijn. In dit voorbeeld wordt de uitvoer van uw indexeringstaak opgeslagen in uw Azure Storage.  U kiest echter zelf wat u met de uitvoert wilt doen.  

Maak de tweede stroom los van de eerste. 

![JSON-extractiestroom](./media/logic-apps-connector-tutorial/json-extraction-flow.png)

Om deze stroom in te stellen, moet u uw Video Indexer-API-sleutel en Azure Storage-referenties opnieuw opgeven. U moet dezelfde parameters bijwerken als voor de eerste stroom. 

Voor uw trigger ziet u een HTTP POST-URL-veld. De URL wordt pas gegenereerd nadat u de stroom hebt opgeslagen. Uiteindelijk heeft u de URL echter wel nodig. We komen hierop terug. 

Vul [uw accountlocatie](regions.md) en [account-id](./video-indexer-use-apis.md#account-id)  in om het Video Indexer-accounttoken op te halen.  

Ga naar de actie 'Video-index ophalen' en vul de vereiste parameters in. Geef de volgende expressie op voor Video-id: triggerOutputs()['queries']['id'] 

![video indexer action info](./media/logic-apps-connector-tutorial/video-indexer-action-info.jpg)

Deze expressie vertelt de connecter om de Video-id op te halen uit de uitvoer van uw trigger. In dit geval wordt de uitvoer van de trigger de uitvoer van 'Video en index uploaden' in uw eerste trigger. 

Ga naar de actie 'Blob maken' en selecteer het pad naar de map waarin u de inzichten wilt opslaan. Stel de naam van de blob die u maakt in. Voor Blob-inhoud geeft u de volgende expressie op: body(‘Get_Video_Index’) 

![Actie 'Blob maken'](./media/logic-apps-connector-tutorial/create-blob-action.jpg)

Deze expressie neemt de uitvoer van de actie 'Video-index ophalen' van deze stroom. 

Klik op **Stroom opslaan**. 

Zodra de stroom is opgeslagen, wordt er een HTTP POST-URL gemaakt in de trigger. Kopieer de URL van de trigger. 

![URL-trigger opslaan](./media/logic-apps-connector-tutorial/save-url-trigger.png)

Ga nu terug naar de eerste stroom en plak de URL in de actie 'Video en index uploaden' voor de parameter Callback-URL. 

Zorg ervoor dat beide stromen zijn opgeslagen en u bent klaar. 

Probeer uw nieuwe Logic App- of Power Automate-oplossing uit door een video toe te voegen aan uw Azure-blobcontainer, en ga een paar minuten later terug om te controleren of de inzichten verschijnen in de doelmap. 

## <a name="generate-captions"></a>Ondertiteling genereren

Zie het volgende blog voor de stappen die laten zien [hoe u ondertiteling kunt genereren met Video Indexer en Logic Apps](https://techcommunity.microsoft.com/t5/azure-media-services/generating-captions-with-video-indexer-and-logic-apps/ba-p/1672198). 

In dit artikel wordt ook beschreven hoe u een video automatisch kunt indexeren door deze te kopiëren naar OneDrive en hoe u de ondertitels opslaat die door Video Indexer worden gegenereerd in OneDrive.
 
## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze zelfstudie, kunt u deze Logic App- of Power Automate-oplossing actief houden indien nodig. Wenst u deze niet actief te houden en niet gefactureerd te worden, schakel dan beide stromen uit als u Power Automate gebruikt. Schakel beide stromen uit als u Logic Apps gebruikt. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebben we slechts één voorbeeld van Video Indexer-connectoren laten zien. U kunt de Video Indexer-connectoren gebruiken voor elke API-aanroep van Video Indexer. Bijvoorbeeld: inzichten uploaden en ophalen, de resultaten vertalen, insluitbare widgets gebruiken en zelfs uw modellen aanpassen. Daarnaast kunt u ervoor kiezen om deze acties te activeren op basis van verschillende bronnen, zoals updates voor bestandsopslagplaatsen of verzonden e-mails. Vervolgens kunt u ervoor kiezen om de resultaten bij te werken naar onze relevante infrastructuur of toepassing, of een aantal actie-items te genereren.  

> [!div class="nextstepaction"]
> [De Video Indexer-API gebruiken](video-indexer-use-apis.md)

Raadpleeg dit document over [Video Indexer](https://docs.microsoft.com/connectors/videoindexer-v2/) voor extra resources.
