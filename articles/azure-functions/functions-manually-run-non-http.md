---
title: Handmatig niet door HTTP geactiveerde Azure-functies uitvoeren
description: Een HTTP-aanvraag gebruiken om niet door HTTP geactiveerde Azure-functies uit te voeren
author: craigshoemaker
ms.topic: article
ms.date: 04/23/2020
ms.author: cshoe
ms.openlocfilehash: 79aebf7ed80fea370ff7a5d5cc40911da4144414
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91537698"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Handmatig een niet door HTTP geactiveerde functie uitvoeren

In dit artikel wordt gedemonstreerd hoe u een niet door HTTP geactiveerde functie handmatig kunt uitvoeren via een speciaal opgemaakte HTTP-aanvraag.

In sommige scenario's moet u wellicht een Azure-functie 'op aanvraag' uitvoeren die indirect wordt geactiveerd.  Voorbeelden van indirecte activeringen zijn [geplande functies](./functions-create-scheduled-function.md) of functies die worden uitgevoerd als gevolg van [de actie van een andere resource](./functions-create-storage-blob-triggered-function.md). 

In het volgende voorbeeld wordt [Postman](https://www.getpostman.com/) gebruikt, maar u kunt ook [cURL](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler) of een soortgelijk hulpprogramma gebruiken HTTP-aanvragen te verzenden.

## <a name="define-the-request-location"></a>Aanvraaglocatie definiëren

Als u een niet met HTTP geactiveerde functie wilt uitvoeren, moet u een manier om een aanvraag naar Azure te verzenden om de functie uit te voeren. De URL om deze aanvraag te doen, heeft een specifieke vorm.

![De aanvraaglocatie definiëren: hostnaam + mappad + functienaam](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Hostnaam:** De open bare locatie van de functie-app die is gemaakt op basis van de naam van de functie-app plus *azurewebsites.net* of uw aangepaste domein.
- Mappad **:** Als u via een HTTP-aanvraag toegang wilt krijgen tot niet-HTTP-geactiveerde functies, moet u de aanvraag verzenden via de mappen *admin/functions*.
- **Functie naam:** De naam van de functie die u wilt uitvoeren.

Gebruik de aanvraaglocatie in Postman in combinatie met de hoofdsleutel van de functie in de aanvraag voor Azure om de functie uit te voeren.

> [!NOTE]
> Als de functie lokaal wordt uitgevoerd, is de hoofdsleutel van de functie niet vereist. U kunt de [functie rechtstreeks aanroepen](#call-the-function) en de `x-functions-key`-header weglaten.

## <a name="get-the-functions-master-key"></a>De hoofdsleutel van de functie ophalen

1. Navigeer naar uw functie-app in het [Azure Portal](https://portal.azure.com), selecteer **app-sleutels** en vervolgens de `_master` sleutel. 

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key.png" alt-text="Zoek de hoofd sleutel die u wilt kopiëren." border="true":::

1. Kopieer de sleutel waarde in het gedeelte **sleutel bewerken** naar het klem bord en selecteer **OK**.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-copy.png" alt-text="Kopieer de hoofd sleutel naar het klem bord." border="true":::

1. Nadat u de *_master* sleutel hebt gekopieerd, selecteert u **code + test** en selecteert u vervolgens **Logboeken**. Hier zijn de berichten van de functie gelogd als u de functie handmatig in Postman uitvoert.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-function-log.png" alt-text="Scherm opname van de pagina ' code + test ' met een bericht uit de logboeken die worden weer gegeven." border="true":::

> [!CAUTION]  
> Vanwege verhoogde machtigingen in de functie-app die door de hoofdsleutel zijn verleend, dient u deze sleutel niet te delen met derden of in een toepassing te distribueren. De sleutel mag alleen naar een HTTPS-eind punt worden verzonden.

## <a name="call-the-function"></a>Functie aanroepen

Open Postman en volg deze stappen:

1. Voer de **aanvraaglocatie in het URL-tekstvak in**.
1. Zorg ervoor dat de HTTP-methode is ingesteld op **POST**.
1. Selecteer het tabblad **kopteksten** .
1. Typ **x-functions-Key** als de eerste sleutel en plak de hoofd sleutel (van het klem bord) als waarde.
1. Typ **Content-type** als tweede sleutel en typ **Application/JSON** als waarde.

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png" alt-text="Instellingen van Postman-headers." border="true":::

1. Selecteer het tabblad **Hoofdtekst**.
1. Typ **{"input": "test"}** als de hoofd tekst van de aanvraag.

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png" alt-text="Instellingen van de Postman-hoofd tekst." border="true":::

1. Selecteer **Verzenden**.
        
    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png" alt-text="Verzend een aanvraag met een bericht." border="true":::

    Postman rapporteert vervolgens de status **202 - Geaccepteerd**.

1. Ga vervolgens terug naar uw functie in de Azure-portal. Bekijk de logboeken en er worden berichten weer geven die afkomstig zijn van de hand matige aanroep van de functie.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-logs.png" alt-text="Bekijk de logboeken om de resultaten van de hoofd sleutel test weer te geven." border="true":::

## <a name="next-steps"></a>Volgende stappen

- [Strategieën voor het testen van uw code in Azure Functions](./functions-test-a-function.md)
- [Lokaal opsporen van fouten in Azure-functies voor Event Grid Trigger](./functions-debug-event-grid-trigger-local.md)
