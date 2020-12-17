---
title: Grote berichten afhandelen met behulp van Chunking
description: Meer informatie over het verwerken van grote bericht grootten met behulp van chunks in geautomatiseerde taken en werk stromen die u maakt met Azure Logic Apps
services: logic-apps
ms.suite: integration
author: DavidCBerry13
ms.author: daberry
ms.topic: article
ms.date: 12/03/2019
ms.openlocfilehash: 1b23c92ec70b80a6cd08fc42a05ffec1e5b43b31
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656764"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>Grote berichten verwerken met Chunking in Azure Logic Apps

Bij het afhandelen van berichten, Logic Apps de bericht inhoud beperkt tot een maximum grootte. Deze limiet vermindert de overhead die is gemaakt door grote berichten op te slaan en te verwerken. Voor het afhandelen van berichten die groter zijn dan deze limiet *, kan Logic apps* een groot bericht in kleinere berichten afsplitsen. Op die manier kunt u nog steeds grote bestanden overdragen met behulp van Logic Apps onder specifieke voor waarden. Wanneer u communiceert met andere services via connectors of HTTP, kan Logic Apps grote berichten gebruiken, maar *alleen* in segmenten. Dit betekent dat connectors ook segmenting ondersteunen, of de onderliggende HTTP-bericht uitwisseling tussen Logic Apps en dat deze services segmenting moeten gebruiken.

In dit artikel wordt beschreven hoe u Chunking kunt instellen voor acties die groter zijn dan de limiet. Logic app-Triggers bieden geen ondersteuning voor het delen van segmenten omdat er meer berichten worden uitgewisseld. 

## <a name="what-makes-messages-large"></a>Wat betekent ' grote ' berichten?

Berichten zijn ' groot ' op basis van de service die deze berichten verwerkt. De exacte maximum grootte voor grote berichten verschilt tussen Logic Apps en connectors. Zowel Logic Apps als Connectors kunnen niet rechtstreeks grote berichten gebruiken, die moeten worden gesegmenteerd. Zie [Logic apps limieten en configuratie](../logic-apps/logic-apps-limits-and-config.md)voor de limiet voor de grootte van het Logic apps bericht.
Zie de [specifieke technische details](../connectors/apis-list.md)van de connector voor de maximale bericht grootte voor elke connector.

### <a name="chunked-message-handling-for-logic-apps"></a>Verwerking van gesegmenteerde berichten voor Logic Apps

Logic Apps kan geen uitvoer rechtstreeks gebruiken van gesegmenteerde berichten die groter zijn dan de maximale bericht grootte. Alleen acties die ondersteuning bieden voor Chunking hebben toegang tot de bericht inhoud in deze uitvoer. Een actie die grote berichten afhandelt, moet *dus voldoen aan de volgende* criteria:

* Het systeem eigen ondersteuning voor segmentering wanneer die actie tot een connector behoort. 
* Ondersteuning van segmentering ingeschakeld in de runtime configuratie van die actie. 

Anders krijgt u een runtime fout wanneer u toegang probeert te krijgen tot grote inhouds uitvoer. Zie voor het inschakelen van Chunking de [ondersteuning voor Chunking instellen](#set-up-chunking).

### <a name="chunked-message-handling-for-connectors"></a>Verwerking van gesegmenteerde berichten voor connectors

Services die communiceren met Logic Apps kunnen hun eigen bericht grootte limieten hebben. Deze limieten zijn vaak kleiner dan de Logic Apps limiet. Als er bijvoorbeeld wordt aangenomen dat een connector Chunking ondersteunt, kan een connector een bericht van 30 MB als groot beschouwen, terwijl Logic Apps dat niet doet. Om te voldoen aan de limiet van deze connector, Logic Apps een bericht dat groter is dan 30 MB in kleinere segmenten gesplitst.

Voor connectors die ondersteuning bieden voor Chunking, is het onderliggende Chunking-protocol onzichtbaar voor eind gebruikers. Niet alle connectors ondersteunen echter Chunking, dus genereren deze connectors fouten wanneer binnenkomende berichten de limieten voor de grootte van de connector overschrijden.

> [!NOTE]
> Voor acties die gebruikmaken van Chunking, kunt u de hoofd tekst van de trigger niet door geven of expressies gebruiken zoals `@triggerBody()?['Content']` in die acties. In plaats daarvan kunt u voor inhoud van tekst of JSON-bestand proberen de [actie **opstellen**](../logic-apps/logic-apps-perform-data-operations.md#compose-action) te gebruiken of [een variabele te maken](../logic-apps/logic-apps-create-variables-store-values.md) om die inhoud af te handelen. Als de hoofd tekst van de trigger andere inhouds typen bevat, zoals media bestanden, moet u andere stappen uitvoeren om die inhoud af te handelen.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Segmentering via HTTP instellen

In algemene HTTP-scenario's kunt u grote inhoud downloaden en uploads via HTTP opsplitsen, zodat uw logische app en een eind punt grote berichten kunnen uitwisselen. U moet echter berichten afsegmenten op de manier die Logic Apps verwacht. 

Als een eind punt Chunking heeft ingeschakeld voor down loads of uploads, worden met de HTTP-acties in uw logische app automatisch grote berichten gesegmenteerd. Anders moet u de ondersteuning voor segmentering instellen voor het eind punt. Als u het eind punt of de connector niet bezit of beheert, hebt u mogelijk niet de mogelijkheid om Chunking in te stellen.

Als een HTTP-actie nog niet is ingeschakeld, moet u ook Chunking instellen in de eigenschap van de actie `runTimeConfiguration` . U kunt deze eigenschap in de actie instellen, hetzij rechtstreeks in de code weergave-editor, zoals later beschreven, of in de Logic Apps Designer, zoals hier wordt beschreven:

1. Klik in de rechter bovenhoek van de http-actie op de knop met het weglatings teken (**...**) en kies vervolgens **instellingen**.

   ![Open het menu instellingen op de actie](./media/logic-apps-handle-large-messages/http-settings.png)

2. Stel onder **inhouds overdracht** **toestaan dat Chunking** is **ingeschakeld in op**.

   ![Segmentering inschakelen](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Ga door met de volgende secties als u wilt door gaan met het instellen van Chunking voor down loads of uploads.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Inhoud in segmenten downloaden

Veel eind punten verzenden automatisch grote berichten in segmenten wanneer ze worden gedownload via een HTTP GET-aanvraag. Als u gesegmenteerde berichten van een eind punt over HTTP wilt downloaden, moet het eind punt gedeeltelijke inhouds aanvragen ondersteunen of *gesegmenteerde down loads*. Wanneer uw logische app een HTTP GET-aanvraag naar een eind punt verzendt voor het downloaden van inhoud, en het eind punt reageert met de status code ' 206 ', bevat het antwoord gesegmenteerde inhoud. Logic Apps kan niet bepalen of een eind punt gedeeltelijke aanvragen ondersteunt. Als uw logische app echter het eerste ' 206 ' antwoord krijgt, verzendt uw logische app automatisch meerdere aanvragen om alle inhoud te downloaden.

Als u wilt controleren of een eind punt gedeeltelijke inhoud kan ondersteunen, verzendt u een HEAD-aanvraag. Met deze aanvraag kunt u bepalen of het antwoord de `Accept-Ranges` header bevat. Op die manier kunt u deze optie *aanbevelen* door de `Range` header in uw HTTP GET-aanvraag in te stellen als het eind punt gesegmenteerde down loads ondersteunt, maar geen gesegmenteerde inhoud verzendt. 

In deze stappen wordt het gedetailleerde proces beschreven Logic Apps gebruikt voor het downloaden van gesegmenteerde inhoud van een eind punt naar uw logische app:

1. Uw logische app verzendt een HTTP GET-aanvraag naar het eind punt.

   De aanvraag header kan optioneel een veld bevatten `Range` waarin een byte bereik voor het aanvragen van inhouds segmenten wordt beschreven.

2. Het eind punt reageert met de status code ' 206 ' en een HTTP-bericht tekst.

    Details over de inhoud van dit segment worden weer gegeven in de koptekst van het antwoord `Content-Range` , met inbegrip van informatie die helpt Logic apps het begin en het einde van het segment te bepalen, plus de totale grootte van de volledige inhoud voordat deze wordt gesegmenteerd.

3. Uw logische app verzendt automatisch opvolgings aanvragen voor HTTP GET.

    Uw logische app verzendt aanvragen voor opvolging totdat de volledige inhoud is opgehaald.

Deze actie definitie toont bijvoorbeeld een HTTP GET-aanvraag waarmee de header wordt ingesteld `Range` . De header *geeft* aan dat het eind punt moet reageren met gesegmenteerde inhoud:

```json
"getAction": {
    "inputs": {
        "headers": {
            "Range": "bytes=0-1023"
        },
       "method": "GET",
       "uri": "http://myAPIendpoint/api/downloadContent"
    },
    "runAfter": {},
    "type": "Http"
}
```

De GET-aanvraag stelt de ' Range '-header in op ' bytes = 0-1023 ', wat het bereik van bytes is. Als het eind punt aanvragen voor gedeeltelijke inhoud ondersteunt, reageert het eind punt met een inhouds segment van het aangevraagde bereik. Op basis van het eind punt kunnen de exacte notatie voor het koptekst veld bereik verschillen.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Inhoud uploaden in segmenten

Voor het uploaden van gesegmenteerde inhoud van een HTTP-actie moet de actie Chunking-ondersteuning hebben ingeschakeld via de eigenschap van de actie `runtimeConfiguration` . Met deze instelling kan de actie het Chunking-protocol starten. Uw logische app kan vervolgens een eerste POST of een bericht naar het doel eindpunt verzenden. Nadat het eind punt reageert met een voorgestelde segment grootte, wordt de logische app gevolgd door HTTP-PATCH aanvragen te verzenden die de inhouds segmenten bevatten.

In deze stappen wordt het gedetailleerde proces beschreven Logic Apps gebruikt voor het uploaden van gesegmenteerde inhoud van uw logische app naar een eind punt:

1. Uw logische app verzendt een eerste HTTP POST-of PUT-aanvraag met een lege bericht tekst. De aanvraag header bevat de volgende informatie over de inhoud die uw logische app wil uploaden in segmenten:

   | Veld aanvraag header Logic Apps | Waarde | Type | Beschrijving |
   |---------------------------------|-------|------|-------------|
   | **x-MS-overdracht-modus** | gesegmenteerde | Tekenreeks | Geeft aan dat de inhoud is geüpload in segmenten |
   | **x-MS-content-length** | <*content-length*> | Geheel getal | De volledige inhouds grootte in bytes voordat deze wordt gesegmenteerd |
   ||||

2. Het eind punt reageert met de status code ' 200 ' en de volgende optionele informatie:

   | Veld koptekst eindpunt reactie | Type | Vereist | Beschrijving |
   |--------------------------------|------|----------|-------------|
   | **x-MS-chunk-grootte** | Geheel getal | Nee | De voorgestelde segment grootte in bytes |
   | **Locatie** | Tekenreeks | Ja | De URL-locatie waarnaar de HTTP-PATCH berichten moeten worden verzonden |
   ||||

3. In uw logische app worden opvolge HTTP-PATCH berichten gemaakt en verzonden, met de volgende gegevens:

   * Een inhouds segment op basis van **x-MS-chunk-grootte** of sommige intern berekende grootte totdat alle inhoud die de totale **x-MS-content-length** bevat, opeenvolgend is geüpload

   * Deze header gegevens over het inhouds segment dat is verzonden in elk PATCH bericht:

     | Veld aanvraag header Logic Apps | Waarde | Type | Beschrijving |
     |---------------------------------|-------|------|-------------|
     | **Content-Range** | <*bereik*> | Tekenreeks | Het bereik van de byte voor het huidige inhouds segment, met inbegrip van de begin waarde, de eind waarde en de totale inhouds grootte, bijvoorbeeld: ' bytes = 0-1023/10100 ' |
     | **Content-Type** | <*inhouds type*> | Tekenreeks | Het type van de gesegmenteerde inhoud |
     | **Content-Length** | <*content-length*> | Tekenreeks | De lengte van de grootte in bytes van het huidige segment |
     |||||

4. Na elke PATCH-aanvraag bevestigt het eind punt de ontvangst voor elk segment door te reageren met de status code ' 200 ' en de volgende antwoord headers:

   | Veld koptekst eindpunt reactie | Type | Vereist | Beschrijving |
   |--------------------------------|------|----------|-------------|
   | **Bereik** | Tekenreeks | Ja | Het bereik van de bytes voor inhoud die door het eind punt is ontvangen, bijvoorbeeld: "bytes = 0-1023" |   
   | **x-MS-chunk-grootte** | Geheel getal | Nee | De voorgestelde segment grootte in bytes |
   ||||

Deze actie definitie toont bijvoorbeeld een HTTP POST-aanvraag voor het uploaden van gesegmenteerde inhoud naar een eind punt. In de eigenschap van de actie `runTimeConfiguration` `contentTransfer` wordt de eigenschap `transferMode` ingesteld op `chunked` :

```json
"postAction": {
    "runtimeConfiguration": {
        "contentTransfer": {
            "transferMode": "chunked"
        }
    },
    "inputs": {
        "method": "POST",
        "uri": "http://myAPIendpoint/api/action",
        "body": "@body('getAction')"
    },
    "runAfter": {
    "getAction": ["Succeeded"]
    },
    "type": "Http"
}
```
